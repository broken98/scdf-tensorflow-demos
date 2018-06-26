# SCDF Tensorflow Demos

## Quick Start

* Install Docker: https://www.docker.com/community-edition 

NOTE: Make sure to increase the memory limits to at least 4G:

* Checkout the SCDF environment:
```
git clone https://github.com/tzolov/scdf-tensorflow-demos.git
cd ./scdf-tensorflow-demos
DATAFLOW_VERSION=latest docker-compose up
```
NOTE: Remove any existing `dataflow-server` containers `docker rm dataflow-server`

* Open the preconfigured SCDF UI: http://localhost:9393/dashboard

## SCDF Tensorflow Demos
#### Image recognition
```
inception-stream=in: file --directory='/input/image-recognition' | image-recognition --model-fetch=output --model='http://dl.bintray.com/big-data/generic/tensorflow_inception_graph.pb' --labels='http://dl.bintray.com/big-data/generic/imagenet_comp_graph_label_strings.txt' --response-size=3 --tensorflow.mode=header --draw-labels=true | out: file --directory='/output/image-recognition' --mode=REPLACE --name-expression='headers[file_name]'

labels-metadata=:inception-stream.image-recognition > transform --expression="{#jsonPath(headers[result].toString(),'$.labels')}" | log
```

#### Object Detection

```
object-detection-stream=file --directory='/input/object-detection' | object-detection2 --tensorflow.mode=header --tensorflow.model='http://dl.bintray.com/big-data/generic/faster_rcnn_resnet101_coco_2018_01_28_frozen_inference_graph.pb' --tensorflow.model-fetch='detection_scores,detection_classes,detection_boxes' --tensorflow.object.detection.labels='http://dl.bintray.com/big-data/generic/mscoco_label_map.pbtxt' --draw-bounding-box=true | out: file --mode=REPLACE --directory='/output/object-detection' --binary=true --name-expression='headers[file_name]'
```

#### Pose Estimation (new)

```
poses= in: file --directory='/input/pose-estimation'  | pose-estimation --mode=header --tensorflow.model='file:/apps/model/thin.pb' --tensorflow.model-fetch='Openpose/concat_stage7' |  out: file --mode=REPLACE --directory='/output/pose-estimation' --binary=true --name-expression='headers[file_name]'
```

#### Twitter Sentiment Analysis

```
tweets=twitterstream --access-token-secret=<Add Yours> --access-token=<Add Yours> --consumer-secret=<Add Yours> --consumer-key=<Add Yours> --track=java --stream-type=filter | filter --expression=#jsonPath(payload,'$.lang')=='en' | twitter-sentiment --vocabulary='http://dl.bintray.com/big-data/generic/vocab.csv' --output-name='output/Softmax' --model-fetch='output/Softmax' --model='http://dl.bintray.com/big-data/generic/minimal_graph.proto' | log
```

