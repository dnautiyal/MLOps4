<p align = "center" draggable=”false” ><img src="https://user-images.githubusercontent.com/37101144/161836199-fdb0219d-0361-4988-bf26-48b0fad160a3.png"
     width="200px"
     height="auto"/>
</p>



# <h1 align="center" id="heading">Introduction to Model Serving with PyTorch TorchServe and TensorFlow Serving using AWS EC2</h1>

Deploying machine learning models for inference at scale is not easy. As an MLOps professional, you must collect and package model artifacts, install and configure software libraries for prediction, create and expose API endpoints, generate logs and metrics for monitoring, and manage multiple model versions on potentially multiple servers. Each of these tasks are complex manual operations that can slow down model deployment by weeks or even months. We will utilize AWS EC2 to deploy pre-trained example models utilizing two different serving platforms, TensorFlow Serving and PyTorch's TorchServe. Either of these platforms can run independently.

[TorchServe](https://pytorch.org/serve/) is an open-source model serving framework for PyTorch that makes it easy to deploy trained PyTorch models performantly at scale without having to write custom code. TorchServe delivers lightweight serving with low latency, so you can deploy your models for high performance inference. It provides default handlers for the most common applications such as object detection and text classification, so you don’t have to write custom code to deploy your models. With powerful TorchServe features including multi-model serving, model versioning for A/B testing, metrics for monitoring, and RESTful endpoints for application integration, you can take your models from research to production quickly. TorchServe supports any machine learning environment, including Amazon SageMaker, Kubernetes, Amazon EKS, and Amazon EC2.

[TensorFlow Serving](https://www.tensorflow.org/tfx/guide/serving) is a flexible, high-performance serving system for machine learning models, designed for production environments. TensorFlow Serving makes it easy to deploy new algorithms and experiments, while keeping the same server architecture and APIs. TensorFlow Serving provides out-of-the-box integration with TensorFlow models, but can be easily extended to serve other types of models and data.

Launch an instance using the following specs:
- **AMI:** Deep Learning AMI GPU PyTorch 1.12.1 (Ubuntu 20.04) 20221005

  **Note** This is found in Community AMI
- **Instance Type:** t2.xlarge
- **Enable Public IP**
- **Ports:** SSH, HTTP, HTTPS, 8000-9000
- **Configure storage** 45 GiB gp2

This may take a few minutes to initialize as the image is pulled onto the main drive. This image will have everything necessary for PyTorch TorchServe, but will require some installation for TensorFlow Serving. After you SSH into the instance, continue to the next section.

### PyTorch TorchServe

1. Clone [this](https://github.com/pytorch/serve) repo.
2. Make a directory `model_store`.
3. We'll be using a [DENSENET](https://pytorch.org/vision/main/models/densenet.html) model, specifically [DENSENET161](https://pytorch.org/vision/main/models/generated/torchvision.models.densenet161.html#torchvision.models.densenet161). Download a trained `densenet161` model.
```bash
wget https://download.pytorch.org/models/densenet161-8d451a50.pth
```
4. Archive the model using the [Torch Model Archiver](https://github.com/pytorch/serve/blob/master/model-archiver/README.md).
```bash
torch-model-archiver --model-name densenet161 --version 1.0 --model-file ./serve/examples/image_classifier/densenet_161/model.py --serialized-file densenet161-8d451a50.pth --export-path model_store --extra-files ./serve/examples/image_classifier/index_to_name.json --handler image_classifier
```
5. Start the endpoint using `torchserve`.
```bash
torchserve --start --ncs --model-store model_store --models densenet161.mar
```

6. Open a new SSH connection and navigate to the home directory

7. Let's grab a test image to test our inference
```bash
curl -O https://s3.amazonaws.com/model-server/inputs/kitten.jpg
```

8. Let's acquire an inference from our model using the test image.
``` bash
curl http://127.0.0.1:8080/predictions/densenet161 -T kitten.jpg
```

9. The predict endpoint returns a prediction in JSON similar to the following top five predictions, where the image has a 47% probability of containing an Egyptian cat, followed by a 46% chance it has a tabby cat.
```bash
{
 "tiger_cat": 0.46933576464653015,
 "tabby": 0.463387668132782,
 "Egyptian_cat": 0.0645613968372345,
 "lynx": 0.0012828196631744504,
 "plastic_bag": 0.00023323058849200606
}
```

10. Congrats! Feel free to test out other images and other [models](https://github.com/pytorch/serve/tree/master/examples). Implementing other models will require serving a new model. Stop the sever when done.
```bash
torchserve --stop
```

### TensorFlow serving

For this next part, we'll be implementing TensorFlow Serving using the same instance. Use [this](https://docs.aws.amazon.com/dlami/latest/devguide/tutorial-tfserving.html) as your guide for serving a pretrained model. You will need to install [tensorflow-serving-api](https://pypi.org/project/tensorflow-serving-api/), [grpcio](https://pypi.org/project/grpcio/), and [tensorflow-model-server](https://www.tensorflow.org/tfx/serving/setup) are installed.

When testing using the previous `kitten` image as used in TorchServe, we acquire the following inference. Notice the classes and score values.
```bash
outputs {
  key: "classes"
  value {
    dtype: DT_STRING
    tensor_shape {
      dim {
        size: 1
      }
      dim {
        size: 5
      }
    }
    string_val: "Egyptian cat"
    string_val: "tabby, tabby cat"
    string_val: "tiger cat"
    string_val: "lynx, catamount"
    string_val: "grey fox, gray fox, Urocyon cinereoargenteus"
  }
}
outputs {
  key: "scores"
  value {
    dtype: DT_FLOAT
    tensor_shape {
      dim {
        size: 1
      }
      dim {
        size: 5
      }
    }
    float_val: 8.997885704040527
    float_val: 6.972867965698242
    float_val: 6.395033359527588
    float_val: 6.1467437744140625
    float_val: 5.1913909912109375
  }
}
model_spec {
  name: "INCEPTION"
  version {
    value: 1
  }
  signature_name: "predict_images"
}
```

#### Train and Serve an MNIST model

For this section, you may also need the [tensorflow serving](https://github.com/tensorflow/serving) repository.

Your results should look like this when the parameter is set to `1000`.

```bash
Extracting /tmp/train-images-idx3-ubyte.gz
Extracting /tmp/train-labels-idx1-ubyte.gz
Extracting /tmp/t10k-images-idx3-ubyte.gz
Extracting /tmp/t10k-labels-idx1-ubyte.gz
........................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................
Inference error rate: 10.4%
```

#### TensorFlow Serving using Containers

If you're interested in a challenge, try serving these models using [TensorFlow serving with Docker](https://www.tensorflow.org/tfx/serving/serving_advanced).

## Want more?
Checkout all of the [great examples](https://github.com/pytorch/serve/blob/master/README.md#serve-a-model) PyTorch has to offer, including implementing [HuggingFace Transformers](https://github.com/pytorch/serve/tree/master/examples/Huggingface_Transformers)!

## References
This assignment is inspired by AWS's [TorchServe tutorial](https://docs.aws.amazon.com/dlami/latest/devguide/tutorial-torchserve.html) and [TensorFlow Serving tutorial](https://docs.aws.amazon.com/dlami/latest/devguide/tutorial-tfserving.html).
