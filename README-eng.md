# Kaggle-Cots-detection

[Readme - δΈ­ζη](https://github.com/RaphaelCCH-K/Kaggle-Cots-detection)

Using object detection to detect starfish in underwater videos.

Competition link: [Kaggle: TensorFlow - Help Protect the Great Barrier Reef](https://www.kaggle.com/competitions/tensorflow-great-barrier-reef/overview)

## Introduction

### Background

Nowadays, the reef is under threat, in part because of the overpopulation of one particular starfish β the coral-eating crown-of-thorns starfish (or COTS for short). Scientists, tourism operators, and reef managers established a large-scale intervention program to control COTS outbreaks to ecologically sustainable levels.

### Tasks

The goal of this competition is to accurately identify starfish in real-time by building an object detection model trained on underwater videos of coral reefs.

### Evaluation

This competition is evaluated on the $F_2-Score$ at different intersections over union (IoU) thresholds. The metric sweeps over IoU thresholds in the range of 0.3 to 0.8 with a step size of 0.05, calculating an $F_2-Score$ at each threshold. The final $F_2-Score$ is calculated as the mean of the F2 scores at each IoU threshold.

<img src="assets\readme\formula_fscore.png" alt="formula_fscore" style="zoom:80%;" />

## Dataset

The source of the dataset: [The CSIRO Crown-of-Thorn Starfish Detection Dataset](https://arxiv.org/abs/2111.14311)

The training dataset is 3 videos, consisting of 23503 1280 x 720 images, and the train.csv file containing the target boxes in the images. The figure following is the display of the target box in the image.

- `train_images/` - Folder containing training set photos of the form `video_{video_id}/{video_frame}.jpg`.
- `annotations` - The bounding boxes of any starfish detections in a string format that can be evaluated directly with Python. Does not use the same format as the predictions you will submit. A bounding box is described by the pixel coordinate (x_min, y_min) of its upper left corner within the image together with its width and height in pixels.

![cots](G:\project\GitHub\Kaggle-Cots-detection\assets\readme\cots.gif)

<p align="center">cots.gif</p>

<img src="G:\project\GitHub\Kaggle-Cots-detection\assets\readme\image-2.png" alt="image-2" style="zoom:80%;" />

<p align="center">picture-1</p>

<img src="G:\project\GitHub\Kaggle-Cots-detection\assets\readme\traincsv.png" alt="traincsv" style="zoom:80%;" />

<p align="center">train.csv</p>



## Solutions

### 1. Data processing

There are 23501 images in the train data, of which 20.93% have BBoxes. Because the pictures without BBox are backgrounds, they have no positive effect on model training, so we remove the them and use the rest as our training data.

The train data is divided into 3 videos. Due to the strong correlation between the frames before and after, the train data and the valid data cannot be randomly split, so 3-fold cross-validation is used to divide them by different videos.

Rewrite original BBoxes format to YOLO format. 

### 2. Data analysis

Visualizing the data distribution of the BBox in the dataset.

ι¦εε―Ήζ£ζ΅ζ‘ηδΈ­εΏδ½η½?θΏθ‘ε―θ§εοΌε¦ε·¦δΈεΎ (pic.4)οΌε―δ»₯ηε°ζ£ζ΅ζ‘ε¨ y θ½΄εεΈθΎεεοΌε¨ x θ½΄ηδΈ­ι΄εδΈ­ι΄εε·¦ζ―θΎιδΈ­γζ₯δΈζ₯ε―Ήζ£ζ΅ζ‘ηε€§ε°θΏθ‘εζοΌε¦ε³δΈεΎ (pic.5)οΌε―δ»₯θ§ε―ε°ζ£ζ΅ζ‘ηε€§ε°ιδΈ­ε¨ `20 x 20 ~ 60 x 60`ε·¦ε³οΌι¨εε€§ηε―δ»₯θΎΎε°`200 x 200`ηεη΄ οΌζ»δ½ζ₯θ―΄ε°Ίε―ΈθΎε°γ

|         pic.4 Center position         |        pic.5 Length and Width        |
| :-----------------------------------: | :----------------------------------: |
| ![1](./assets/readme/position-xy.png) | ![2](./assets/readme/lenth-bbox.png) |


### 3. θ?­η»η­η₯

- **baseline**οΌθ?­η»δΊ 1280 εθΎ¨ηη yolov5s ζ¨‘εοΌδ½Ώη¨ι»θ?€εζ°οΌεΎε° `F2-score` 0.546γ

- **ι«εθΎ¨η**οΌη±δΊζ£ζ΅η?ζ ε°Ίε―ΈθΎε°οΌδ½Ώη¨ζ΄ε€§ηεθΎ¨ηε―δ»₯ε¨δΈε?η¨εΊ¦δΈζι«εζ°οΌδΊζ―ε°θ―θ?­η»δΊ 1280γ2560γ3000γ3500 εθΎ¨ηη yolov5 ζ¨‘εοΌεζΆδΉδ½Ώη¨δΈεεθΎ¨ηζ₯ζ¨ηοΌζεε¨ 3000 ζ¨ηεθΎ¨ηηζε΅δΈεΎε°δΈδΈηζ΅θ―η»ζοΌηΊΏδΈ `F2-score`οΌοΌ

  | **YOLOv5sεθΎ¨ηιζ©** | **F2-Scores** |
  | :-------------------- | ------------- |
  | 1280                  | 0.573         |
  | 2560                  | 0.583         |
  | **3000**              | **0.588**     |
  | 3500                  | 0.587         |

  εζΆθθηΊΏδΈεηΊΏδΈθ‘¨η°εΎζε΅δΈοΌ3000 εθΎ¨ηθ?­η»η yolov5sοΌ3000 εθΎ¨ηζ¨ηε―δ»₯θΎΎε°δΈδΈͺθΎι«ηεζ°γ

- **ζ°ζ?ε’εΌΊ**οΌδΏ?ζΉζ°ζ?ε’εΌΊηζΉεΌοΌε°θ―ε€η§δΈεηεζ°δΈε’εΌΊζΉζ³οΌε?ιͺεη°οΌε’ε ζ°΄εΉ³η«η΄ηΏ»θ½¬ειζΊζθ½¬ε―Ήεζ°ζζι«οΌεζΆθ?Ύη½? mosaic 0.25οΌmixup 0.5οΌscale 0.5οΌδ½ΏεΎηΊΏδΈεηΊΏδΈηεζ°ζδΊδΈηΉζι«οΌ+0.01οΌγ

- **θ?Ύη½?η½?δΏ‘εΊ¦ιεΌ**οΌι»θ?€ηη½?δΏ‘εΊ¦ιεΌζ― 0.6οΌεΆε?ε―δ»₯ζ΄δ½οΌζ¬ζ¬‘ζ―θ΅ηθ―εζ εζ―`F2-Score`οΌθΏζε³ηθ―ε«εΊζ΄ε€ηζ­£η±»ζ―εε°ζ΄ε€ηιθ――ι’ζ΅ζ΄ιθ¦οΌε¨yolov5δΈ­θ?Ύη½?`F2-Score`ηθ?‘η?οΌεζΆε?ιͺδΊδΈεηη½?δΏ‘εΊ¦ιεΌοΌεΎε°ιεΌδΈΊ 0.1 ηζΆεηΊΏδΈεηΊΏδΈεζ°ι½ηΈε―Ήζ΄ι«γ

- **Tracking**οΌθθε°ι’ζ΅θ§ι’ηεεεΈ§ζ―εΌΊηΈε³ηοΌζδ»₯ε¨ι’ζ΅ζΆε ε₯δΊ tracking ηζΉζ³γζ―θΎεΈΈη¨ηζ Deepsort ζΉζ³οΌζ―ε©η¨εηε©η?ζ³εε‘ε°ζΌζ»€ζ³’ηδΈη§ tracking ζΉζ³οΌε¨ζ¬ζ¬‘ζ―θ΅δΈ­ζη»δ½Ώη¨ηζΆ Norfair ηζΉζ³οΌεηε Deepsortε€§εε°εΌοΌNorfair ε―δ»₯δ½Ώη¨ζ¬§ε ιεΎθ·η¦»ζ₯ζ₯ζΎθ½¨θΏΉγε·δ½ Norfair εΊηθ―΄ζθ―·ηε?ζΉθ―΄ζ [Norfair - GitHub](https://github.com/tryolabs/norfair) 

- **WBF**οΌWBF ε³ε ζζ‘θεη?ζ³οΌε NMS η±»δΌΌζ―δΈη§ε»ι€εδ½ζ‘εΉΆθεζ‘ηζΉζ³οΌδ½ζ Ήζ?η?ζ³ηδΈεθΏδΈ€η§ζΉζ³εΎε°ηζζδΉδΈεοΌWBF η?ζ³θ½ηΆζ΄θζΆοΌδ½ε¨εε€ζ¨‘εεεΉΆι’ζ΅η»ζηζΆεζζ΄ε₯½ηη»ζοΌδΈεΎδΈΊ WBF ε NMS ηδΈηΉεΊε«οΌWBF δ½Ώη¨δΊζζηι’ζ΅ζ‘ζ₯ε±εθ?‘η?εΎε°ζη»ηζ‘οΌNMS ζ―δΈ’εΌδΊδΈδΊι’ζ΅ζ‘γζ΄ε€η»θε―ζ₯ηε?ζΉθ―΄ζ [WBF - GitHub](https://github.com/ZFTurbo/Weighted-Boxes-Fusion)

  <img src="G:\project\GitHub\Kaggle-Cots-detection\assets\readme\WBF.png" alt="WBF" style="zoom:80%;" />

  <p align="center">WBF η?ζ³ηε―θ§ε</p>

- **ζ¨‘εθε**οΌθθε°ε€ε°ΊεΊ¦δ»₯εη¨³ε?ζ§οΌδΊζ―δ½Ώη¨ζ¨‘εθεοΌζη»δ½Ώη¨δΈδΈͺ 1280 εθΎ¨ηεδΈδΈͺ 3000 εθΎ¨ηη yolov5 ζ¨‘εγ

  | **ζ¨‘εεη»ε**                       | **F2-Score** |
  | ------------------------------------ | ------------ |
  | 3000yolov5s                          | 0.601        |
  | 3000yolov5s + Tracking               | **0.625**    |
  | 2560yolov5s + 3000yolov5s + Tracking | 0.633        |
  | 1280yolov5s + 3000yolov5s + Tracking | 0.658        |
  | 1280yolov5l + 3000yolov5s + Tracking | **0.664**    |



## εΆδ»θ―΄ζ

### repoθ―΄ζ

- θ?­η»δ»£η οΌ`notebooks/train.ipynb`
- ζ¨ηδ»£η οΌ`notebooks/infer.ipynb`

### Reference

[https://www.kaggle.com/code/awsaf49/great-barrier-reef-yolov5-train](https://www.kaggle.com/code/awsaf49/great-barrier-reef-yolov5-train)

[https://www.kaggle.com/code/awsaf49/great-barrier-reef-yolov5-infer](https://www.kaggle.com/code/awsaf49/great-barrier-reef-yolov5-infer)

[https://arxiv.org/pdf/1910.13302.pdf](https://arxiv.org/pdf/1910.13302.pdf)

[https://github.com/tryolabs/norfair](https://github.com/tryolabs/norfair)

[https://www.kaggle.com/competitions/tensorflow-great-barrier-reef/discussion/300638](https://www.kaggle.com/competitions/tensorflow-great-barrier-reef/discussion/300638)









