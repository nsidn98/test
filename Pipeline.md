# Pipeline
## [Install Tensorflow GPU-version](https://github.com/nsidn98/test/blob/master/Install_tensorflow_GPU.md)

## Download datasets for images:
### Dataset used for training:

The dataset includes driving in Mountain View California and neighboring cities during daylight conditions. It contains over 65,000 labels across 9,423 frames collected from a Point Grey research cameras running at full resolution of 1920x1200 at 2hz. The dataset was annotated by CrowdAI using a combination of machine learning and humans.
* [CrowdAI](http://bit.ly/udacity-annoations-crowdai)
* Please use the corrected annotation csv file from [here](https://github.com/udacity/self-driving-car/blob/master/annotations/labels_crowdai.csv)

#### Labels:
* Car
* Truck
* Pedestrian

#### CSV Format:
* xmin
* ymin
* xmax
* ymax
* frame
* label
* preview url for frame

### Dataset used for testing:
This dataset is similar to dataset 1 but contains additional fields for occlusion and an additional label for traffic lights. The dataset was annotated entirely by humans using Autti and is slightly larger with 15,000 frames.

* [Autti](http://bit.ly/udacity-annotations-autti) dataset

#### Labels:
* Car
* Truck
* Pedestrian
* Street Lights
* Biker

#### CSV Format:
* frame
* xmin
* ymin
* xmax
* ymax
* occluded
* label
* attributes (Only appears on traffic lights)

## Gathering Images with different parameters of shutter speed and gains using an Industrial camera [(uEye IDS)](https://en.ids-imaging.com/usb3-camera-ueye.html)
Go to this [link](https://github.com/nsidn98/test/blob/master/camera/code_explanation.md) for steps

## Label the images with bounding boxes:
Go to this [link](https://github.com/nsidn98/test/blob/master/labelling.md)

## Convert the `xml` files to a csv file
`C:\models\research\object_detection> python xml_to_csv.py`

## Prepare the dataset(the downloaded ones and not the labelled ones) to be converted to a [tfrecord file](https://www.tensorflow.org/programmers_guide/datasets)
Follow the steps in this [notebook](https://github.com/nsidn98/test/blob/master/make%20test%20and%20train%20data%20csv%20file.ipynb)

## Prepare the tf record file:

In `generate_tfrecord.py` check the following:

~~~
# TO-DO replace this with label map
def class_text_to_int(row_label):
    if row_label == 'car':
        print(row_label)
        return 1
    elif row_label=='truck':
        print(row_label)
        return 2
    elif row_label=='pedestrian':
        print(row_label)
        return 3
    
    else:
        print(row_label)
        None
~~~

If it is not there change it.

Run the following commands to create the tfrecord file
~~~
python generate_tfrecord.py --csv_input=images\labels_train.csv --image_dir=images\train --output_path=train.record
python generate_tfrecord.py --csv_input=images\labels_new.csv --image_dir=images\test --output_path=test.record
~~~

## Create a labelmap and configure the training:
Use a text editor to create a new file and save it as labelmap.pbtxt in the C:\models\research\object_detection\training folder. (Make sure the file type is .pbtxt, not .txt !) In the text editor, copy or type in the label map in the format below:
~~~
item {
  id: 1
  name: 'car'
}

item {
  id: 2
  name: 'truck'
}

item {
  id: 3
  name: 'pedestrian'
}
~~~
The label map ID numbers should be the same as what is defined in the generate_tfrecord.py file.















