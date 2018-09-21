# IBM Code Model Asset Exchange: Facial Age Estimator

This repository contains code to instantiate and deploy a facial age estimation model. The model detects faces in an image, extracts facial features for each face detected and finally predicts the age of each face. The model uses a coarse-to-fine strategy to perform multi-class classification and regression for age estimation. The input to the model is an image and the output is a list of estimated ages and bounding box coordinates of each face detected in the image. The format of the bounding box coordinates is `[xmin, ymin, width, height]`.

The model is based on the [SSR-Net model](https://github.com/shamangary/SSR-Net). The model files are hosted on [IBM Cloud Object Storage](http://max-assets.s3-api.us-geo.objectstorage.softlayer.net/facial-age-estimator.tar.gz). The code in this repository deploys the model as a web service in a Docker container. This repository was developed as part of the [IBM Code Model Asset Exchange](https://developer.ibm.com/code/exchanges/models/).

## Model Metadata
| Domain | Application | Industry  | Framework | Training Data | Input Data Format |
| ------------- | --------  | -------- | --------- | --------- | -------------- |
| Vision | Age Estimation | General | Keras & TensorFlow | [IMDB-WIKI Dataset](https://data.vision.ee.ethz.ch/cvl/rrothe/imdb-wiki/) | Image(PNG/JPG) |


## Prerequisites:

* You will need Docker installed. Follow the [installation instructions](https://docs.docker.com/install/) for your
system.
* The minimum recommended resources for this model is 2GB Memory and 1 CPU.

## References:

* _T.-Y. Yang, Y.-H. Huang, Y.-Y. Lin, P.-C. Hsiu, and Y.-Y. Chuang._ ["SSR-Net: A Compact Soft Stagewise Regression Network for Age Estimation"](https://www.ijcai.org/proceedings/2018/0150.pdf), IJCAI, 2018.
* [SSR-Net Github Repository](https://github.com/shamangary/SSR-Net)

## Licenses

| Component | License | Link  |
| ------------- | --------  | -------- |
| This repository | [Apache 2.0](https://www.apache.org/licenses/LICENSE-2.0) | [LICENSE](LICENSE) |
| Model Weights | [MIT](https://opensource.org/licenses/MIT) | [LICENSE](https://github.com/shamangary/SSR-Net/blob/master/LICENSE) |
| Model Code (3rd party) | [MIT](https://opensource.org/licenses/MIT) | [LICENSE](https://github.com/shamangary/SSR-Net/blob/master/LICENSE) |
| Test assets | Various | [Asset README](assets/README.md) |

## Pre-requisites:

* `docker`: The [Docker](https://www.docker.com/) command-line interface. Follow the [installation instructions](https://docs.docker.com/install/) for your system.
* The minimum recommended resources for this model is 2GB Memory and 1 CPU.

# Steps

1. [Deploy from Docker Hub](#deploy-from-docker-hub)
2. [Deploy on Kubernetes](#deploy-on-kubernetes)
3. [Run Locally](#run-locally)

## Deploy from Docker Hub

To run the docker image, which automatically starts the model serving API, run:

```
$ docker run -it -p 5000:5000 codait/max-facial-age-estimator
```

This will pull a pre-built image from Docker Hub (or use an existing image if already cached locally) and run it.
If you'd rather checkout and build the model locally you can follow the [run locally](#run-locally) steps below.

## Deploy on Kubernetes

You can also deploy the model on Kubernetes using the latest docker image on Docker Hub.

On your Kubernetes cluster, run the following commands:

```
$ kubectl apply -f https://github.com/IBM/MAX-Facial-Age-Estimator/raw/master/max-facial-age-estimator.yaml
```

The model will be available internally at port `5000`, but can also be accessed externally through the `NodePort`.

## Run Locally

1. [Build the Model](#1-build-the-model)
2. [Deploy the Model](#2-deploy-the-model)
3. [Use the Model](#3-use-the-model)
4. [Development](#4-development)
5. [Clean Up](#5-clean-up)


### 1. Build the Model

Clone this repository locally. In a terminal, run the following command:

```
$ git clone https://github.com/IBM/MAX-Facial-Age-Estimator.git
```

Change directory into the repository base folder:

```
$ cd MAX-Facial-Age-Estimator
```

To build the docker image locally, run:

```
$ docker build -t max-facial-age-estimator .
```

All required model assets will be downloaded during the build process. _Note_ that currently this docker image is CPU only (we will add support for GPU images later).



### 2. Deploy the Model

To run the docker image, which automatically starts the model serving API, run:

```
$ docker run -it -p 5000:5000 max-facial-age-estimator
```

### 3. Use the Model
The API server automatically generates an interactive Swagger documentation page. Go to http://localhost:5000 to load it. From there you can explore the API and also create test requests. Use the model/predict endpoint to load a test image (you can use one of the test images from the assets folder) and get predicted labels for the image from the API

Use the `model/predict` endpoint to load a test image (you can use one of the test images from the `assets` folder) and get predictions for the image from the API.
![Swagger UI Screenshot](docs/swagger-screenshot.png)
You can also test it on the command line, for example:
```
$ curl -F "image=@assets/tom_cruise.jpg" -XPOST http://localhost:5000/model/predict
```
You should see a JSON response like that below:
```
{
    "status": "ok",
    "predictions": [
        {
            "age_estimation": 48,
            "face_box": [
                303,
                174,
                379,
                515
            ]
        }
    ]
}
```

### 4. Development

To run the Flask API app in debug mode, edit `config.py` to set `DEBUG = True` under the application settings. You will then need to rebuild the docker image (see [step 1](#1-build-the-model)).

### 5. Cleanup

To stop the Docker container, type `CTRL` + `C` in your terminal.