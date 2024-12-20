# Nested-PPE-Detection
This repo contains a nested object detection pipeline intended to detect protective gear worn on work sites.

NESTED-PPE DETECTION

Person Detection Training 

In order to train the ppe detection model and the person detection model the Yolov8 model by Ultralytics was used.

The training for the person class was done in the following manner:

Got the pre-trained model “yolov8n.pt” from Ultralytics
Converted the PascalVOC Annotations into the Yolov8 annotations
Extracted only the person class for the person detection task
Setup the YAML file accordingly for training
Performed batched training by setting the device to utilize cuda
A lot of hyperparameters were tinkered with to finally settle with the following important ones - 
         
         Imgsz = 640
         Epochs = 20
         Conf = 0.15
         IoU = 0.6
         LR = 0.002, Momentum = 0.9, Decay = 0.0

The losses were tracked using TensorBoard and graphs of F1 score, Precision and Recall as a function of epochs.

After training was done for 20 epochs, the following mean average precision(mAP) scores were observed : 

mAP 50 = 0.987
mAP 50-95 = 0.892
Precision = 0.907
Recall = 0.99637

The model weights were saved and later loaded for inference

PPE Detection Training

For PPE detection training, a script was implemented to get “valid” ppe targets.
“Valid” ppe targets are ppe targets whose bounding boxes lie completely within the person class bounding boxes

After these targets were filtered a count was taken which revealed the following numbers:



Class 0 is the hard-hat class.In fact all classes’ labels were reduced by 1 since the person class wasn't trained with PPE classes.

Because of class imbalance only the classes: hard-hat, gloves, boots, vest, ppe-suit were used for ppe detection training 

After filtering out the imbalanced classes, the ppe detection model was trained on these classes.A similar approach was used for the training of the ppe detection model.


Validation/ Eval metrics:

For person detection

In the final model used for inference of person classes at a confidence threshold of 0.15 and IoU threshold of 0.6, the following metrics were observed:

map50-95 =  0.8694543622777229
map50 =  0.9787202133980607
map75 =  0.9373248679511978

The following inference speeds were observed on a cuda device for every image:

 0.2ms pre process, 5.4ms inference, 0.0ms loss, 3.3ms post process 

For ppe detection

The eval metrics for the ppe detection model at a confidence threshold of 0.25 and IoU of 0.5 is as follows:

The following inference speeds were observed on a cuda device for every image:

Speed: 0.2ms preprocess, 4.3ms inference, 0.0ms loss, 1.6ms post process per image

Detection sample results:

A sample of the detection results are shown here.
The inference was done in 2 stages as instructed where the person class was extracted before the ppe detections were made.




The corresponding predicted annotations file for this image has the following content:

0 0 165 344 399 
1 71 166 212 248 
6 0 301 217 397 
0 465 94 581 344 
1 477 94 521 132 
6 486 127 565 218 
5 520 330 553 344 
This indicates that for the first person detected(indicated by the top line), there were 2 detections, one belonging to class 1 and the other belonging to class 6 as indicated by the next 2 lines.

The following line gives bounding boxes for the next person and the last 3 lines indicate the ppe kit detected on that person.

