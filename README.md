# DTU course: 02456 Deep learning

This repository contains exercises for the DTU course [*02456 Deep Learning*](http://kurser.dtu.dk/course/02456).
The exercises are written in Python programming language and formatted into Jupyter Notebooks.

## Running the exercises:
The exercises are located in the `notebooks` folder.
To run the exercises download the repository and start Jupyter by write the following in a terminal:
``` bash
git clone https://github.com/DeepLearningDTU/02456-deep-learning.git
cd 02456-deep-learning
cd notebooks
jupyter notebook
```

This should start your default browser and you should be up and running.
**Safari** is known to cause issues, so we recommend that you use the newest version of Chrome.

If you are unfamiliar with Jupyter we suggest that you familiarize yourself before beginning with the exercises: [quick introduction](https://www.packtpub.com/books/content/basics-jupyter-notebook-and-python), [thorough introduction](https://www.datacamp.com/community/tutorials/tutorial-jupyter-notebook#gs.a6M6p0Q). 
But very briefly Jupyter Notebooks are interactive Python environments that allow us to combine documentation (images, text, etc.) with code.


## Setup
The primary prerequisites for running the exercises are:
* Python 3.x
    * The official version is `Python 3.5` since it is the only version TensorFlow supports on Windows 
(as of May 2017), but other versions might also work.
* Jupyter Notebook
* TensorFlow 1.x
    * If you have a GPU install the GPU version. This greatly decreases training time.
* Keras 2.x

You will need some other libraries as well, but they should be easy to install using `pip`.

There are three supported setup methods:
1. natively on your own computer
1. on your own computer using Docker
1. on a server, such as Amazon Web Servicess (AWS)

Instructions for each are given below.

___
### Native
Doing a local install is typically easy.
* We recommend installing [Python 3.5 via **Anaconda**](https://www.continuum.io/downloads), as it provides many necessary third party libraries.
    * **Windows**: TensorFlow ONLY works for Python 3.5 on Windows.
* OPTIONAL: If you have a GPU you should install [**CUDA**](https://developer.nvidia.com/cuda-downloads) and [**cuDNN**](https://developer.nvidia.com/cudnn).
* Install [TensorFlow](https://www.tensorflow.org/install/). Note that there are different versions depending on whether you are using GPU or not.


___
### Docker
> **NB!** If you have a GPU read the **Using a GPU** section below before beginning the setup.

In order to make the material as accessible as possible we have also included Dockerfiles.
So you can get the code up and running quickly on any computer.
Now if you are not familiar with Docker we will give you a short introduction here.
However, there is great documentation on their website, and amazing tutorials around the web.
So we will barely touch the surface of what you can do with Docker.


First of all, you will need to have Docker installed on your computer.
The complexity of this task varies tremendously on the operation system of your computer.
But go to [Docker’s homepage](https://www.docker.com/) and follow their instructions.

With Docker installed on your system all you have to do is enter the main folder of this repository.
Here you will find two Dockerfiles: `Dockerfile.cpu` and `Dockerfile.gpu`.
You then rename `Dockerfile.cpu` to `Dockerfile` since this is the filename which Docker looks for.

From here you have to build a docker image using the command:

`docker build -t deeplearning .`

You can just copy the line above, but in order to shortly explain what is going on, you simple create a virtual image specified by the Docker file. 
This image you assign the tag `deeplearning` using the `-t` parameter to make it easier to reference this image later on.
Finally you tell Docker to look for the `Dockerfile` in the current directory.
This is shown graphically below:

![Building image](files/build_docker.png?raw=true)

When the image is built you want to run it. This is done with the command:

`docker run -it -p 8888:8888 -v $(pwd)/notebooks:/notebooks deeplearning`

A lot is going on in the command above. First we tell Docker to run an image. We specify it should be done in an interactive manner (through the `-it` parameter) and to map the local port `8888` to the port `8888` in the image. This allows us to access **Jupyter** from our host machine. We then tell Docker to synchronize the notebooks folder with a corresponding folder inside the image. Finally we tell Docker to use the `deeplearning` image we just created. This is shown visually below:

![Running image](files/run_docker.png?raw=true)

#### Using a GPU
> **NB** Windows is not supported. See issues 
    [#43](https://github.com/NVIDIA/nvidia-docker/issues/43), 
    [#197](https://github.com/NVIDIA/nvidia-docker/issues/197)

If you have a computer with a GPU which can run **CUDA** you will want to use your GPU rather than your CPU. 
This makes things a bit more complicated, but you will surely be up for the task. 
First of all, you have to [check whether there is a driver for your GPU](https://developer.nvidia.com/cuda-gpus). 
Then you will want to follow the instructions on installing **CUDA** and **CUDNN** on your operating system. 
With this in place you need to install  [nvidia-docker](https://github.com/NVIDIA/nvidia-docker)  in order to be able to access the GPU from the virtual machine.

There can be quite a few complications along the way to get to this far. But once here it is very simple. All you have to do is to use the `Dockerfile.gpu` from our repository instead of the `Dockerfile.cpu` and follow the general instructions above replacing the normal `docker` command with `nvidia-docker`.

So building a Docker image becomes:

`nvidia-docker build -t deeplearning .`

and running the image becomes:

`nvidia-docker run -it -p 8888:8888 -v $(pwd)/notebooks:/notebooks deeplearning`

___
### AWS
To run AWS on your own account you must:
1. Create an [AWS account](https://aws.amazon.com/). If you are a student you can apply for [AWS Educate](https://aws.amazon.com/education/awseducate/) and recieve free credits.
1. Request permission to use `p2.xlarge` servers. [Click here](http://aws.amazon.com/contact-us/ec2-request) to request this.
    * **NB** Processing time takes a couple of days.
    * `p2.xlarge` servers are not available at all regions at time of writing (Jan. 2017).
    We suggest the **Ireland region**.
    * The person recieveing your request can give permission for up to 5 instanecs without consulting a superior/technician. Applying for more than 5 makes it take longer and harder to get accepted.
1. Once your request has been accepted create an AWS instance:
    * Go to [this address](https://eu-west-1.console.aws.amazon.com/ec2/v2/home). 
        * Make sure `Ireland` is your region.
    * Click `Launch Instance`.
    * **1. Choose AMI**
        * Click `AWS Marketplace`
        * Search using the AMI ID for the [Deep Learning AMI](https://aws.amazon.com/marketplace/pp/B01M0AXXQB).
        It should look somehting like `ami-d36386aa`.
    * **2. Choose Instance Type**
        * Pick `p2.xlarge`
    * **3. Configure Instance**
        * Leave at default
    * **4. Add Storage**
        * Leave at default
    * **5. Add Tags**
        * Name your instance
    * **6. Configure Security Group**
        * You will need to configure your `Security Group`. The following settings are VERY liberal and aren't safe. But they work:
        ![](files/security_group.png)
        * The `Custom TCP Rules` are necessary for hosting Jupyter Notebooks over the internet (not used currently).
    * Click Launch
    * If you don't already have a `.pem` file create one, and save it. You will need it everytime you SSH into your instance.
* Go to `Instances` and right click your newly launched instance.
Select `Connect`, and follow the instructions.

___

## Contributors
* Ole Winther ([olewinther](https://github.com/olewinther))
* Lars Maaløe ([larsmaaloee](https://github.com/larsmaaloee))
* Casper Sønderby ([casperkaae](https://github.com/casperkaae))
* Søren Kaae Sønderby ([skaae](https://github.com/skaae))
* Alexander R. Johansen ([alrojo](https://github.com/alrojo))
* Jonas Busk ([jonasbusk](https://github.com/jonasbusk))
* Toke Faurby ([faur](https://github.com/Faur))
* Mikkel Vilstrup ([mvilstrup](https://github.com/MVilstrup))
