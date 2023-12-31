# Commands

## download gitignore using curl

```bash
curl https://raw.githubusercontent.com/akshaygp18/general_template/main/.gitignore > .gitignore
```
## download init_setup.sh using curl

```bash
curl https://raw.githubusercontent.com/akshaygp18/general_template/main/init_setup.sh > init_setup.sh
```
## tensorflow verification

```bash
python -c "import tensorflow as tf;print(tf.config.list_physical_devices('GPU'))"
```
# Installation of Object Detection API 

## Create TensorFlow directory

```bash
mkdir TensorFlow && cd TensorFlow
```
## Clone the TensorFlow models folder here

```bash
git clone https://github.com/tensorflow/models.git
```
remove .git directory of models repository to avoid git conflicts

add models folder to .gitignore
```bash
echo "TensorFlow/models" >> .gitignore
```
## Protobuff Installation/Compilation

- Visit the link - https://github.com/protocolbuffers/protobuf/releases
    - windows users -
        search for - protoc-3.20.1-win64.zip
    - Mac users - 
        search for - protoc-3.20.1-osx-x86_64.zip
    - Linux users - 
        ```
        sudo apt install -y protobuf-compiler
        ``` 
Unzip into root folder and add '<PATH TO protoc folder>/bin' into sytstem environment variable.

- run the following command:
```bash
cd TensorFlow/models/research/
protoc object_detection/protos/*.proto --python_out=.
```

## Install COCO API
```bash
pip install git+https://github.com/philferriere/cocoapi.git#subdirectory=PythonAPI
```

## Install Object Detection API

```bash
cp object_detection/packages/tf2/setup.py .
python -m pip install .
```

## Test your installation
```bash
python object_detection/builders/model_builder_tf2_test.py
```

# Run examples - 

- Create workspace/example_1 directory in project root
    ```bash
    mkdir -p workspace/example_1
    ```

- cd to workspace/example_1
    ```bash
    cd workspace/example_1
    ```

- Download notebook
    ```bash
    curl https://tensorflow-object-detection-api-tutorial.readthedocs.io/en/latest/_downloads/55b1ed8e083cbc9ca3bfc1c18eb6b860/plot_object_detection_saved_model.ipynb > 
    plot_object_detection_saved_model.ipynb
    ```

# Custom model training
```bash
mkdir workspace/training_demo
cd workspace/training_demo
mkdir -p annotations exported-models models pre-trained-models images/test images/train
```

## Create label map file in training_demo/annotations

and write content as -

```
item {
    id: 1
    name: 'helmet'
}

item {
    id: 2
    name: 'head'
}

item {
    id: 3
    name: 'person'
}
```
## Curl the generate_tfrecord file into root of training_demo directory

```bash
curl https://tensorflow-object-detection-api-tutorial.readthedocs.io/en/latest/_downloads/da4babe668a8afb093cc7776d7e630f3/generate_tfrecord.py > generate_tfrecord.py
```

## generate_tfrecord

### For train data
```bash
python generate_tfrecord.py -x images/train -l annotations/label_map.pbtxt -o annotations/train.record
```

### For test data
```bash
python generate_tfrecord.py -x images/test -l annotations/label_map.pbtxt -o annotations/test.record
```

# Configuring a training Job

## Go to [model zoo](https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/tf2_detection_zoo.md) and download SSD ResNet50 V1 FPN 640x640 (RetinaNet50) model
- extract the downloaded model into training_demo/pre-trained-model directory

## Configure training pipeline
- Create a folder my_ssd_resnet50_v1_fpn in training_demo/models
- Copy pipeline.config here from pre-trained-model directory
- Update it as per the documentation - [link](https://tensorflow-object-detection-api-tutorial.readthedocs.io/en/latest/training.html) 


## Copy training file from TensorFlow/models/research/object_detection/ to the root of training_demo folder

```bash
cp ../../TensorFlow/models/research/object_detection/model_main_tf2.py .
```
## Start training by running the following command
```bash 
python model_main_tf2.py --model_dir=models/my_ssd_resnet50_v1_fpn --pipeline_config_path=models/my_ssd_resnet50_v1_fpn/pipeline.config
```

## Exporting a Trained Model
```bash
cp ../../TensorFlow/models/research/object_detection/exporter_main_v2.py .

python exporter_main_v2.py --input_type image_tensor --pipeline_config_path ./models/my_ssd_resnet50_v1_fpn/pipeline.config --trained_checkpoint_dir ./models/my_ssd_resnet50_v1_fpn/ --output_directory ./exported-models/my_model
```
