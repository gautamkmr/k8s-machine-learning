# Inference of squeezenet using MXNet on Amazon EKS

This document explains how to perform inference of [squeezenet](https://en.wikipedia.org/wiki/SqueezeNet) model using MXNet and Keras on Amazon EKS.

## Pre-requisite

1. Create [EKS cluster using GPU](../../eks-gpu.md)
3. Basic understanding of [MXNet Model Server](https://github.com/awslabs/mxnet-model-server)


## 

1. Dockerfile from [MXNet Model Server](https://github.com/awslabs/mxnet-model-server/blob/master/docker/Dockerfile.gpu). You can also use [AWS Deep Learning Containers](https://aws.amazon.com/machine-learning/containers/)


1. We have provided a yaml file which encapsulates launching the deployment and service. So user can make the request for prediction. 

   ```
   kubectl create -f samples/mnist/inference/mxnet/squeezenet.yaml
   ```



1. We have provided a yaml file which encapsulates launching the deployment and service. So user can make the request for prediction.

   ```
   kubectl create -f samples/mnist/inference/mxnet/squeezenet.yaml
   ```

   This should create the deployments and service.


1. There are multiple type of k8s service. We have used the default which is `clusterIP`. We have to do port forwarding for client to access this service. 

   ```
   kubectl port-forward -n ${NAMESPACE} `kubectl get pods -n ${NAMESPACE} --selector=app=squeezenet-service -o jsonpath='{.items[0].metadata.name}'` 8080:8080 & 
   ```

   This will map your local host port (8080) to service port(8080).

1  Lets download an image and run inference..

   ```
   curl -O https://s3.amazonaws.com/model-server/inputs/kitten.jpg
   ```

   ```
   curl -X POST http://127.0.0.1:8080/predictions/squeezenet -T kitten.jpg 
   ```

   The result should be something like below.

   ```
   Handling connection for 8080
   [
     {
       "probability": 0.8582223653793335,
       "class": "n02124075 Egyptian cat"
     },
     {
       "probability": 0.09160047024488449,
       "class": "n02123045 tabby, tabby cat"
     },
     {
       "probability": 0.03748778998851776,
       "class": "n02123159 tiger cat"
     },
     {
       "probability": 0.0061650057323277,
       "class": "n02128385 leopard, Panthera pardus"
     },
     {
       "probability": 0.0031716094817966223,
       "class": "n02127052 lynx, catamount"
     }
   ```
   Feel free to try with random image.
 


