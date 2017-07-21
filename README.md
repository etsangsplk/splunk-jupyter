# Splunk-Jupyter
Analyse your Splunk data from a Jupyter Notebook, as a Pandas Dataframe, into any Machine Learning algorithm.

## Overview
One of the biggest challenges in performing Data Science is the sourcing, cleaning, and transforming of data. What is known as the ETL process takes about 80% of Data Scientists' time, which must focus most of their time in the ETL process rather than on the fun of building and testing algorithms.

Splunk's schema-on-read architecture allows users to create an ephemeral schema on the data each time, with each search. Splunk has created hundreds of data type mappings that extract the fields out of thousands of log files, and automatically recognizing the structure from CSV, JSON, and XML files, greatly simplifying the ETL process.

Jupyter notebooks have been used by the Data Science community for years to develop and test models, and as many of our customers use both, it's only natural to bring two great things together.

## Querying Splunk from a Jupyter Notebook
It's really simple, all you need to do is install Splunk's SDK in your Jupyter instance, and they use it to connect to Splunk.

You can install the Splunk's SDK using pip:

```shell
pip install splunk-sdk
```

## Sample Notebook
The Notebook included in this repo serves as an example on how to install the SDK, query Splunk, and convert the results into a Pandas Dataframe. From there, you're free to use the tools you know and love.
https://github.com/dformoso/splunk-jupyter/blob/master/From%20Splunk%20to%20Jupyter%20and%20into%20a%20Pandas%20Dataframe.ipynb

## Test it in a Docker Environment
If you'd like to play with the idea but are not ready to bring it to your production environment, I've built a docker environment for you to play with.

This has been tested in MacOS running Docker Community Edition.
Version 17.06.1-ce-rc1-mac20 (18682)
Channel: edge

First, we create a User-Defined Network. This network will host our containers, and allow for DNS hostname discovery for the containers within the network, as the default bridge network doesn't allow for it.

```shell
### Create a User-Defined bridge network (needed for DNS hostname discovery)
docker network create \
  --driver=bridge \
  --subnet=172.28.0.0/16 \
  --ip-range=172.28.5.0/24 \
  --gateway=172.28.5.254 \
  jupyter-splunk
```

The 

```shell
### Create a docker container running an unauthenticated Jupyter instance.
docker run -itd \
  --restart always \
  --name jupyter \
  --hostname jupyter \
  --network=jupyter-splunk \
  -p 8888:8888 \
  -v "/Users/danielmartinez/Library/Mobile Documents/com~apple~CloudDocs/Programming/Python/:/home/jovyan/work" \
  jupyter/datascience-notebook:latest \
  start-notebook.sh --NotebookApp.token=''

### Create a docker container running the latest Splunk version.
docker run -itd \
  --restart always \
  --name splunk \
  --hostname splunk \
  --network=jupyter-splunk \
  -p 8000:8000 \
  -e "SPLUNK_START_ARGS=--accept-license" \
  -e "SPLUNK_USER=root" \
  splunk/splunk:latest

### Check the User Defined network has been created and containers are running
docker ps -a
docker network ls

### Delete Environment
docker rm --force jupyter
docker rm --force splunk
docker network rm jupyter-splunk

### Check environment has been deleted
docker ps -a
docker network ls
```



## About Splunk - http://splunk.com
Splunk Enterprise monitors and analyzes machine data from any source to deliver Operational Intelligence to optimize your IT, security and business performance. With intuitive analysis features, machine learning, packaged applications and open APIs, Splunk Enterprise is a flexible platform that scales from focused use cases to an enterprise-wide analytics backbone.

## About Jupyter - http://jupyter.org
The Jupyter Notebook is an open-source web application that allows you to create and share documents that contain live code, equations, visualizations and explanatory text. Uses include: data cleaning and transformation, numerical simulation, statistical modeling, machine learning and much more.
