# **Deploying A Model In The Cloud**
*Sofia Chevrolat (January 2021)*

> NB: This project is the 8th of a series of 8 projects comprising [the syllabus offered by OpenClassrooms in partnership with Centrale Supélec and sanctioned by the Data Scientist diploma - Master level](https://openclassrooms.com/fr/paths/164-data-scientist).
___

[![forthebadge](https://forthebadge.com/images/badges/made-with-python.svg)](https://forthebadge.com)


The fictional company Fruits! is looking to create a classification model for fruits and vegetables. This classification model needs preprocessed data to be trained on.
The aim of this project is to create a data preprocessing chain that can scale once demand - meaning data volumes - massively increases. 

The goal is thus two-fold:
1- Set up a Big Data architecture that can scale at need
2- Implement and deploy the notebook executing the data preprocessing chain on this Big Data architecture to demonstrate the scalability of the program.
___

## Table of Contents

This notebook is organized as follows:

**0. Setting Up**
- 0.1 Loading of the necessary libraries
- 0.2 Defining the parameters
- 0.3 Defining custom functions

**1. Loading and description of the dataset**
- 1.1 Description of the dataset
- 1.2 Loading & labeling the data

**2. Dimensionality reduction via Transfer Learning**
- 2.1 Preparing the pretrained model for feature extraction 
- 2.2 Feature extraction
- 2.3 Feature visualization

**3. Exporting The Preprocessed Data**
_________

## Requirements

This assumes that you already have an environment allowing you to run Jupyter notebooks. 

The libraries used otherwise are listed in requirements.txt and you should have the Java OpenJDK installed.

_________

## Usage

> **NB 1** : This is a demo project. For this reason, a simple AWS EC2 instance was used.
However, a real project would use a Spark job on an EMR cluster. 

> **NB 2** : The notebook itself can of course be run locally on your computer, if powerful enough.
For this, simply forego the steps referring to the EC2 instance and perform the rest on your own machine. From there, you can of course choose to move your Docker image directly to your EC2 instance afterwards, should you wish to.

1. Set up your demo Big Data architecture:
    - Create a private S3 bucket with name "mltrainingfruitimages"
    - Create an S3 IAM role for reading and writing 
    - Create your EC2 instance (t2large minimum)
    - Set up a key pair to access your EC2 instance
    - Create your security group restreining input access to your sole IP address

2. Download the dataset from [Kaggle](https://www.kaggle.com/moltean/fruits) and place it in your private S3 bucket.

3. Set up your environment: 
    - SSH into your instance using your key pair and following the instructions on the "Connect" tab of your EC2 instance

    - Install Docker on your instance:
    ```bash
    sudo yum update -y
    sudo yum install -y docker
    ```
    - Start Docker on your instance:
    ```bash
    sudo service docker start
    ```
    - Give your user special rights:
    ```bash
    sudo user-mod -a -G docker ec2-user
    ```
    - exit and SSH in again

    - Get and run a ready made Tensorflow jupyter Docker image:
    ```bash
    docker run -v ~/work:/home/jovyan/work -d -p 8888:8888 jupyter/tensorflow-notebook
    ```
    - Allow preserving the notebook:
    ```bash
    sudo chown 1000 ~/work
    ```
    - Enter your container as root:
    ```bash
    docker exec -u 0 -it <name_of_your_container> bash
    ```
    - Install pyspark on your container:
    ```bash
    pip install pyspark
    ```
    - Install OpenJDK:
    ```bash
    sudo add-apt-repository ppa:openjdk-r/ppa
    sudo apt-get update
    sudo apt install openjdk-11-jre
    ```

4. Work on Jupyter in your Docker image on your EC2 instance:
    - Get your container's URL:
    ```bash
    docker logs <name_of_your_container>
    ```
    The URL is the line at the very end introduced by "Copy/paste this URL into your browser when you connect for the first time, to login with a token:"
    - Replace the part before ":8888" with the public DNS of your EC2 instance (see the "Connect" tab of your instance)
    - Copy the whole URL into your browser and press "Enter". 
    
    Jupyter opens. 

5. Transfer the notebook directly into your working directory inside your Docker container on your EC2.

6. Create a folder named ".access/" 

7. Place the login information to your private S3 bucket in a json file named "AWS_access.json" inside .access. 
The json should have the three following keys inside an arborescence named "S3_admin":
    - Access_key_ID
    - Secret_access_key
    - Region

8. Run the notebook.

__________

## Results

For a complete presentation and commentary of the results of this analysis, please see the PowerPoint presentation.

> NB: The presentation is in French.

__________

## Acknowledgments

A big thank you to the following publications that were particularly helpful for this project, providing detailed tutorials:
- [databricks](https://docs.databricks.com/applications/machine-learning/preprocess-data/transfer-learning-tensorflow.html): on how to use transfer learning for featurization using Spark.
- [Guru99](https://www.guru99.com/jupyter-notebook-tutorial.html): on how to get Jupyter up and running inside Docker on an Amazon EC2 instance.

