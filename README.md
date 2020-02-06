# Vehicle detection, tracking and counting
Object detection is a technology related to computer vision and image processing that deals with detecting objects of a certain class in digital images and videos.
Well-researched domains of object detection include face detection and pedestrian detection. Object detection has applications in many areas of computer vision, including image retrieval and video surveillance.
In this article, will explain how I built vehicle counting system. Let's get started!

## How vehicle counting system works?
The vehicle counting system is made up of three main components: a detector, tracker and counter. 
## Detector: 
YOLO (You Only Look Once), is a network for object detectionYolo will take an image and split it into an SxS grid, within each of the grid we take m bounding boxes. For each of the bounding box, the network outputs a class probability and offset values for the bounding box. The bounding boxes having the class probability above a threshold value is selected and used to locate the object within the image.
All of the other object detection algorithms use regions to localize the object within the image. The network does not look at the complete image. Instead, parts of the image which have high probabilities of containing the object. YOLO or You Only Look Once is an object detection algorithm much different from the region based algorithms. In YOLO a single convolutional network predicts the bounding boxes and the class probabilities for those boxes.
The limitation of YOLO algorithm is that it struggles with small objects within the image, for example, it might have difficulties in detecting a flock of birds. This is due to the spatial constraints of the algorithm.

## Tracker
The tracker used is a open source tracking algorithm (Simple Online and Realtime Tracking) It is designed for online tracking applications where only past and current frames are available and the method produces object identities on the fly.

## Counter
The counter counts vehicles in the Region of interest.

## Why do we need Counting?
Here are some reasons:
-	Managing Traffic and planning: If you have a good sense of the volume of traffic moving along a given road or network of roads, you can better understand congestion and then manage and/or make plans to reduce/eliminate it. Vehicle count data is very useful to urban city planners and transport authorities.
-	Traffic control: No one likes to be stuck behind a red light especially when the road is free. Vehicle counting systems can be integrated with traffic light control software to intelligently direct vehicles based on the current traffic situation in real time.
-	Parking management: A VCS can be installed at the entrance of a parking lot to monitor vehicles coming in and going out in order to determine whether there are slots available at any given time. It can also be used to ensure the number of vehicles in a given place (such as a hotel or events center) does not exceed its capacity by controlling Automatic Barrier Gates as opposed to issuing tags.
-	Advertising: Billboard advertisers and their clients are interested in the volume of vehicular traffic along a road where they have ads or where they want to install a billboard because they can make estimates of the number of people who see their ads per time using the data.

## Why video?
There are a handful of ways to count vehicles on the road from manual counts to pneumatic tubes to piezoelectric sensors. Why was video used? Why is it preferred?
-	Sensor data (video footage) can be used to verify the system’s results which makes it easier and faster to evaluate and improve the system.
-	The footage can also be used for other purposes including surveillance, automatic plate number recognition, vehicle type detection and vehicle speed detection to name a few.
-	It is relatively cheaper to implement and scale as a permanent vehicle counting system compared to other systems.
-	It can track and count multiple vehicles moving in different directions across several lanes.

## Method
## Step1:Data Collection
Before we proceed, first thing is to collect all types of vehicles on the road. Since I differentiated vehicles based of the capacity and the number of wheels, I made four classes:
- Light Moving vehicles (Bike, cars, jeep)
-	Medium vehicles (Small trucks)
-	Heavy Moving vehicles (Bus, Truck)
-	Heavy Moving Vehicles Multi axle (Bus truck)

## Step2: Annotations
Once we have all the images ready, place in a folder “image”. Now we need to label the images, so to do that we have many good tools available on internet but, I preferred [LabelImg](https://github.com/tzutalin/labelImg) as it is a graphical image annotation tool. It is written in Python and uses Qt for its graphical interface. Annotations are saved as XML files in PASCAL VOC format, the format used by ImageNet. Besides, it also supports YOLO format. Follow the method mentioned in link
Make sure to save the annotations in the same folder where we have placed the images i.e., “image”.

## Step 3: Build custom object detector model using YOLOv3
Installing Darknet: I have used google colab to install darknet as there was some version problem while installing it on the machine. Here are the steps to setup darknet in [Google colab](https://github.com/k-Shubha/Vehicle-Counting/blob/master/darknet_custom_training.ipynb)

Before runing the train command
### - Split data into test and train
Zip the images and annoations in local machine and load it the colab.Move it to /content/darknet/build/darknet/x64/data/obj/ and run [split_train_test.py]
- Fllow the steps from [AlexeyAB](https://github.com/AlexeyAB/darknet#how-to-train-to-detect-your-custom-objects)

### - Calculate the anchors
!./darknet detector calc_anchors "/content/darknet/build/darknet/x64/data/obj.data" -num_of_clusters 9 -width 416 -height 416

### Update the cfg file which was created before

### Train the model
!./darknet detector train "/content/darknet/build/darknet/x64/data/obj.data" "/content/darknet/build/darknet/x64/yolov3-obj.cfg" "/content/darknet/build/darknet/x64/darknet53.conv.74" -dont_show
- Make sure to take a backup of darknet folder into drive for every 500 itrerations
- To resume training copy the backed up file into the colab and train using above train command

### Test the model 
!./darknet detector demo  "/content/darknet/build/darknet/x64/data/obj.data" "/content/darknet/build/darknet/x64/yolov3-obj.cfg" "/content/darknet/build/darknet/x64/backup/yolov3-obj_4000.weights" -dont_show "/content/drive/My Drive/test3.mp4" -out_filename "/content/result_final.mp4"

# Tracking










