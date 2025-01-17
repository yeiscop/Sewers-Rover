# EPM CCTV Sewers Faillure Detection

**About the Project**

App for detection of failuresin sewer systems of the city of Medellin - Colombia. 
Model implemented with YOLO v3. App created with Dash


# App Services
In accordance with epm's security and infrastructure policies, the sewerage system anomaly detection service was mounted in the AZURE and Google cloud and deployed for use at the url:

GCP LINK (App Public) <br>
https://ds4a-team-7-mv5d6qz22q-uc.a.run.app <br>


AZURE LINK (App Private - only login with VPN) <br>
https://wa-epm-prod-ds4.prod-ase01.epm.com.co/ <br>

The following video illustrates how to use the App
[![IMAGE ALT TEXT HERE](https://user-images.githubusercontent.com/26895736/116099983-1db7cf00-a672-11eb-9e36-295cd1448219.png)](https://www.youtube.com/watch?v=zLuhMVZRa9E)

* If you want to test the app with sewers faillures. Extract the folder called: Sample_images.zip o https://drive.google.com/u/1/uc?id=16GTvZwzdWhFhLtqN23PGg7RGA_NibMaT&export=download and test the app with this images. 


**IMPORTANT**
If you want to run the application locally:

1. You must download the data of the model from this link: https://drive.google.com/u/1/uc?export=download&confirm=RvgA&id=10777tL9PnvYKCxK78tSM_xSB_gsUXN-1
2. Then copy and paste both folders inside the project folder (This folder contains all the data requiered for YOLO V3 model)

![image 2](https://user-images.githubusercontent.com/26895736/116101170-3a083b80-a673-11eb-8245-decddd623fa8.png)

3. Install all the requirements. Inside the main folder of the project run the command:

```
pip install -r requirements.txt
```

4. Finally inside the main folder of the project run the command: 

```
python index.py
```

![image](https://user-images.githubusercontent.com/26895736/116101381-6328cc00-a673-11eb-8a87-d95d1b571bf9.png)
![image](https://user-images.githubusercontent.com/26895736/116101447-6e7bf780-a673-11eb-8427-ca706562a367.png)



# Training 

If you want to retrain the model follow the next steps. (Just if you want) 

## Notebook in **COLAB**
To use the `YOLOv3_YOLOv4_colab_training_TL.ipynb` notebook, the user must change the runtime to GPU in *COLAB* and upload the following repository https://drive.google.com/drive/folders/1ege1-3IYXj_xiVQ3IBQEBsbxsYj66ERi?usp=sharing in your personal DRIVER. If you want to increase the number of images in the Transfer Learning process, you must proceed to label them in PASCAL VOC format in the label application that the user sees fit. In addition, if it is required to increase the training periods or the learning rate; As well as, to activate the Transfer Learning and Data Augmentation process, one must resort to the `./yolov3/configs.py` file in the repository in DRIVE and change these parameters.<br>

-- **Note:** In the repository you can see two (2) videos in Spanish of the step by step to follow in *COLAB*.

## TensorFlow-2.x-YOLOv3 and YOLOv4 tutorials
YOLOv3 and YOLOv4 implementation in TensorFlow 2.x, with support for training, transfer training, object tracking mAP and so on... Code was tested with following specs:

* i7-7700k CPU and Nvidia 1080TI GPU
* OS Ubuntu 18.04
* CUDA 10.1
* cuDNN v7.6.5
* TensorRT-6.0.1.5
* Tensorflow-GPU 2.3.1
* Code was tested on Ubuntu and Windows 10 (TensorRT not supported officially)

### Installation
First, download this https://drive.google.com/drive/folders/1ege1-3IYXj_xiVQ3IBQEBsbxsYj66ERi?usp=sharing repository. Install requirements and download pretrained weights:
```
pip install -r ./requirements.txt

# yolov3
wget -P model_data https://pjreddie.com/media/files/yolov3.weights

# yolov4
wget -P model_data https://github.com/AlexeyAB/darknet/releases/download/darknet_yolo_v3_optimal/yolov4.weights

```

### Quick start
Start with using pretrained weights to test predictions on both image and video:
```
python detection_demo.py
```

### Quick training for custom dataset (Sewer's Anomalies) 
`./custom_dataset/` folder contains anomalies images, create training and test data.<br>

**Note:** In the original exercise the data had approximately 8000 tagged images.

### Transfer Learning
* Convert pascal voc format images to YOLO
```
python tools/XML_to_YOLOv3.py
```
*For this case, three (3) labels were used: HOLES, ROOTS and DEPOSITS. The above, due to the fact that according to the descriptive results they are the most frequent and have a high impact on sanitation problems.*

* Training
Before starting the training process, change the Transfer Learning and Data Augmetation parameters to True in the `./yolov3/configs.py` file.

![image](https://user-images.githubusercontent.com/26895736/116101988-ea763f80-a673-11eb-8acf-0f0fad8924de.png)

Now, you can train it and then evaluate your model:
```
python train.py
```

When the training is finished, for this case 12.5 hours, the model is evaluated with the *TensorFlow* tool called *TensorBoard*.

```
tensorboard --logdir=log
Track training progress in Tensorboard and go to http://localhost:6006/:
```
![image](https://user-images.githubusercontent.com/26895736/116102095-01b52d00-a674-11eb-8064-6534ac0c3994.png)
![image](https://user-images.githubusercontent.com/26895736/116102123-0974d180-a674-11eb-9c44-e0de1dff8d2e.png)

* In none of the metrics given by *TensorBoard* is it possible to observe over or under adjustment problems. 
* The total error tended to decrease in the two (2) dates as the epochs increased.
* The convolutional layers (conv2d_74, conv2d_66, conv2d_58) were frozen in order to take advantage of the general characteristics of the pre-trained images in the *COCO* Data set (shadows, background, edges, among others) and to concentrate the Transfer Learning process on those particular characteristics of the anomalies present in the sewer systems.
* Activating the Data Augmetation option ostensibly benefited the final training, since by generating greater variability in the images used to develop the model, when evaluating it, it was observed that the generalization in the prediction covered different contexts.


### Test detection with `detection_custom.py` script:
```
python detection_custom.py
```

The model presented a performance according to what was expected in the detection of anomalies of the sewer system, generalizing in its prediction; as shown in the following samples.

![image](https://user-images.githubusercontent.com/26895736/116102234-27423680-a674-11eb-9c22-2d12b1dc37b7.png)
![image](https://user-images.githubusercontent.com/26895736/116102284-34f7bc00-a674-11eb-9761-e6f8230f5a4d.png)
![image](https://user-images.githubusercontent.com/26895736/116102324-3d4ff700-a674-11eb-954c-b534e2ebd818.png)
![image](https://user-images.githubusercontent.com/26895736/116102438-548ee480-a674-11eb-96d2-6bb906212d54.png)
![image](https://user-images.githubusercontent.com/26895736/116102506-62dd0080-a674-11eb-9357-4dbe547ec2fe.png)
![image](https://user-images.githubusercontent.com/26895736/116102558-6ec8c280-a674-11eb-912b-0b235d4f98c9.png)
![image](https://user-images.githubusercontent.com/26895736/116102613-79835780-a674-11eb-86a4-31c1731c7f61.png)
![image](https://user-images.githubusercontent.com/26895736/116102653-830cbf80-a674-11eb-9e04-4a76971a0be4.png)

The *giou* metric evidenced the power of the algorithm when differentiating images from above, such as roots with deposits.

### Comparison
Different experiments were carried out with the different versions of YOLO: V3, V4 with TF2 and v5, the latter uses the PyTorch algorithm for networking. In the end, due to performance issues in detection rather than speed, it was decided to work with YOLOv3.

| Detection | giou_loss | giou_val_loss | total_loss | total_val_loss | mAP | FPS |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| `YOLOv3 TF2` | 0.53  | 1.26  | 1.19  | 4.25  | 89.36%  | 4.13 |
| `YOLOv4 TF2`| 0.95  |2.68  |4.89  |8.90  | 82.81% | 1.96 |
| `YOLOv5 PyTorch` | 1.13  |3.01  | 6.03 | 10.11 | 79.73%  | 1.82  |

## References
* https://pylessons.com/
* https://github.com/pythonlessons/YOLOv3-object-detection-tutorial
* https://github.com/ultralytics/yolov5

## Thanks
*Thanks for being intersted in this project. Special greetings to EPM, Correlation One and Mintic for this oportunity.*
* \# *MinTic*
* \# *epm*
* \# *DS4*
* \# *TAs*
* \# *TA OSCAR*
* \# *TA ALI*
* \# *NATESH*
* \# *NELSON epm*
* \# *TEAM7*
* \# *ZARCHARY*
* \# *JIMMI JING*
