---
# Related publishing issue: https://github.ibm.com/IBMCode/IBMCodeContent/issues/2696

authors:
  - name: Simon Plovyt
    email: simon@ibm.com
pwg:
  - containers
  - icp
related_content:
  - type: series
    slug: create-model-asset-exchange
  - type: articles
    slug: introduction-to-the-model-asset-exchange-on-ibm-developer
  - type: tutorials
    slug: getting-started-with-the-ibm-code-model-asset-exchange
tags:
  - artificial-intelligence
  - deep-learning
  - machine-learning
  - containers
  - codait
  - open-source
  - cloud
  - devops
abstract: 'Learn how to deploy deep learning models from the Model Asset Exchange to production with IBM Cloud and Kubernetes.'
completed_date: '2019-05-10'
components:		# Required=false For a full list of options see https://github.ibm.com/IBMCode/Definitions/blob/master/components.yml
  - "model-asset-exchange"
  - "cloud-ibm"
  - "kubernetes"
  - "docker"
collections:
  - "codait"
draft: false
excerpt: 'Learn how to deploy deep learning models from the Model Asset Exchange to production with IBM Cloud and Kubernetes.'
last_updated: '2019-05-10'
meta_description: 'Learn how to deploy deep learning models from the Model Asset Exchange to production with IBM Cloud and Kubernetes.'
meta_keywords: 'IBM Cloud, Kubernetes, Model Asset Exchange, MAX, deep learning models, machine learning, deep learning, artificial intelligence, cognitive computing, transfer learning, docker, containers, tutorial'
primary_tag: cloud
pta:
  - "cloud, container, and infrastructure"
title: Deploying MAX models to IBM Cloud with Kubernetes
type: tutorial
meta_title: Deploying MAX models to IBM Cloud with Kubernetes
subtitle: 'Learn how to deploy deep learning models from the Model Asset Exchange to production with IBM Cloud and Kubernetes'

---

Looking to deploy a MAX model to IBM Cloud? Smart!

Fortunately, it's not only easy, but also free. 

## Learning objective

In this tutorial, we will use the Kubernetes system to deploy a cloud instance for your MAX model of choice. The Kubernetes service allows us to easily scale the containerized model from supporting a low rate of queries to production-level queries. The free IBM Cloud Kubernetes Service is limited to 1 worker, but this will do fine for this application. Let's get started!

## Prerequisites

If you are not familiar with the Model Asset Exchange, this [introductory article](https://developer.ibm.com/articles/introduction-to-the-model-asset-exchange-on-ibm-developer/) provides a good overview.

## Estimated time

It should take you approximately 45 minutes to complete this tutorial.

## Steps

This tutorial consists of the following steps:

* [Creating an IBM Cloud account](#1-creating-an-ibm-cloud-account)
* [Setting up a Kubernetes cluster](#2-setting-up-a-kubernetes-cluster)
* [Deploying the MAX model](#3-deploying-the-max-model)

### 1. Creating an IBM Cloud account

If you don't already have one, go to [https://cloud.ibm.com/registration](https://cloud.ibm.com/registration) to sign up for a free IBM Cloud account. The registration process is no different than the usual, so no further explanation is required. Log in to your cloud dashboard when you're done.

### 2. Setting up a Kubernetes cluster

**Create the Kubernetes Service in your IBM Cloud Dashboard**

- Find the [Kubernetes Service](https://cloud.ibm.com/kubernetes/catalog/cluster) in the Cloud Catalog. Click **Create** or **Create Resource** to add the **Kubernetes Service** to your resources. The [Kubernetes Service](https://cloud.ibm.com/kubernetes/catalog/cluster) should look something like this (but is subject to change).

![](images/2a.png)

- Select the **Free** cluster type, and specify the cluster name and physical location. In this tutorial, we will name the cluster '*max-deployment-cluster*'. When done, click **Create Cluster** to confirm.

![](images/2b.png)

- Your Kubernetes Cluster is now being installed on IBM Cloud. You can follow the progress bar at the top of the page (see the red arrow on the picture below). Next, we will go over the instructions detailed on this page to get access to the cluster.

![](images/2c.png)

**Install the IBM Cloud CLI and Kubernetes CLI**

In order to be able to communicate effectively with our newly created cluster, we need to install the [IBM Cloud command-line interface (CLI)](https://cloud.ibm.com/docs/cli/reference/ibmcloud?topic=cloud-cli-ibmcloud-cli#ibmcloud-cli). This will allow us to log in to the cloud from our local machine.

Next, we will install the [Kubernetes command-line interface (CLI)](https://kubernetes.io/docs/tasks/tools/install-kubectl/), to have access to the tools needed to manage and deploy to the Kubernetes cluster after logging in to IBM Cloud.

Fortunately, both of these command-line tools can be installed at the same time with one line of code!

* For **Mac and Linux**, run the following command and follow the instructions:

    `curl -sL https://ibm.biz/idt-installer | bash`

* For **Windows**, run the following command as administrator and follow the instructions:

    `Set-ExecutionPolicy Unrestricted; iex(New-Object Net.WebClient).DownloadString('http://ibm.biz/idt-win-installer')`

*If you are having trouble, or need more information, navigate to the installation instructions [here](https://cloud.ibm.com/docs/cli/reference/ibmcloud?topic=cloud-cli-ibmcloud-cli#ibmcloud-cli) (or search for 'IBM Cloud CLI' on the web).*

**Accessing the Kubernetes Cluster**

Now that we have installed the IBM Cloud communication tools, we are nearly there. However, we still need to log in to IBM Cloud on our local machine and specify what cluster we are targeting in the cloud. All these instructions are also available under the **Access tab** on the online page of the kubernetes cluster we have just created.

![](images/2d.png)

- Log in to the cluster from your local machine

`ibmcloud login -a https://cloud.ibm.com`


- Specify the Kubernetes Service region in which you want to work. Our cluster is in US South.

`ibmcloud ks region-set us-south`


- Get the command to set the environment variable and download the Kubernetes configuration files to the cluster. 
NOTE: Replace the last argument (`max-deployment-cluster`) with the name you gave your cluster earlier.

`ibmcloud ks cluster-config max-deployment-cluster`


- Copy the output from the previous command and paste it in your terminal. The command starts with `export KUBECONFIG=...`. This sets the KUBECONFIG environment variable. 


- Verify that you can connect to your cluster by listing your worker nodes.
kubectl get nodes


We are now ready for the final step of the deployment.

### 3. Deploying the MAX model

The actual deployment process is very short. Now that the connection with the remote Kubernetes cluster has been set up, we merely have to apply the model's configuration file to the Kubernetes cluster to get the API up and running.


Let's download the model's repository, which contains the YAML configuration file.

- Clone the MAX-Model repository from GitHub. This example shows how to do this for the MAX-Object-Detector, but feel free to replace `MAX-Object-Detector` with your MAX Model of choice.

`git clone https://github.com/IBM/MAX-Object-Detector.git`

- Find the YAML file in the downloaded directory.

`cd MAX-Object-Detector`
`ls *.yaml`

- Apply the configuration file to our Kubernetes cluster. Again, replace the `max-object-detector.yaml` with the `.yaml` file for your model. This command deploys the model.

`kubectl apply -f ./max-object-detector.yaml`

- When the model is deployed (give it some time), find the public IP address and port that the API is served on.
    - The public IP
    
    `ibmcloud cs workers mycluster`
    
    - The port
    
    `kubectl describe service max-text-sentiment-classifier | grep NodePort`
 
 - Navigate to the http://<PUBLIC_IP>:<PORT> address in your browser. You should find the API frontend as shown below.
 
![](images/3a.png)
 
The MAX model is now permanently available. Done!

**Some useful commands**

Kubernetes CLI:
 ```
# Show the log of all events
kubectl get events

# Show all pods
kubectl get pods

# Show all services
kubectl get services

# Show all deployments
kubectl get deployments

# Show the details of a service
kubectl describe service SERVICE_NAME

# Show the details of a node
kubectl describe node NODE_NAME

# Delete a service
kubectl delete services SERVICE_NAME

# Delete a deployment
kubectl delete deployment DEPLOYMENT_NAME

# Debugging a pod
kubectl logs POD_NAME
```

IBM Cloud CLI:
```
# Show all clusters
ibmcloud cs clusters

# Show the workers of a specific cluster
ibmcloud cs workers CLUSTER_NAME
```

**Further scaling**

Scaling an application with Kubernetes is nothing more than clicking a button. Adding worker nodes (computation units) or specifying advanced cluster parameters (e.g. more RAM) can easily be done from the Cloud Dashboard. However, you'll have to upgrade to a standard cluster in order to access these. With more worker nodes, a single node's public IP address does not suffice, so services like [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/) and [Loadbalancer](https://kubernetes.io/docs/concepts/services-networking/ingress/) will need to be added as well.

### Troubleshooting

An easy way to detect general issues with your Kubernetes cluster is to visit the Kubernetes Dashboard on IBM Cloud. Any critical or pending worker nodes will be highlighted. In addition, you can run the following commands in the terminal.

- Check Kubernetes events

`kubectl get events`

- Get the logs for a pod

`kubectl logs POD_NAME`

**Memory issues and killed containers**

When trying to deploy a model with above-average memory requirements (for example the MAX-Image-Resolution-Enhancer), you will run into a 'TypeError: Failed to fetch' error. Although the frontend is still responsive, this error often means that the container was killed and restarted due to exceeding the memory limit after posting an input image. In this case, you will need to upgrade to a standard Kubernetes cluster in order to choose the specifications of the worker nodes. Increasing the resources of the node solves this issue.

## Summary

This tutorial illustrates how to deploy a model container from the Model Asset Exchange to IBM Cloud using Kubernetes. The three steps followed are:

* [Creating an IBM Cloud account](#1-creating-an-ibm-cloud-account)
* [Setting up a Kubernetes cluster](#2-setting-up-a-kubernetes-cluster)
* [Deploying the MAX model](#3-deploying-the-max-model)



### Tutorial setup

1. Open a terminal window, start Node-RED `node-red`, and open the Node-RED editor by pointing your browser to the displayed URL, such as `http://127.0.0.1:1880`.

1. From the **&#9776;** menu, select **Manage palette**.

   ![Access the palette](images/hamburger_menu.png)

1. In the _Palette_ tab of the _User Settings_, select **Install**.
1. Search for the `node-red-contrib-browser-utils` module and install it if you don't already have it installed.
1. Close the settings window.

   ![Import prerequisite modules](images/install_prerequisite_modules.png)

1. Verify that the _camera_ and _file inject_ nodes are listed in the _input_ category. You'll use these nodes later in this tutorial.

   ![Lights - camera - action](images/palette_with_browser_utils_nodes.png)

### Import the Model Asset Exchange nodes

The Model Asset Exchange nodes are published on [npm](https://www.npmjs.com/package/node-red-contrib-model-asset-exchange).

1. From the **&#9776;** menu, select **Manage palette**.
1. In the _Palette_ tab of the _User Settings_, select **Install**.
1. Search for the `node-red-contrib-model-asset-exchange` module and install it.
1. Close the settings window.

  Several nodes should be displayed in the _Model Asset eXchange_ category.

   ![Palette with MAX nodes](images/palette_with_max_nodes.png)

   Each Model Asset Exchange node uses the endpoints of a deep learning microservice that you can run in a local environment or in the cloud.

   ![A node exposes the functions of a deep learning microservice](images/node_microservice_association.png)

To keep this tutorial simple, you'll associate your nodes with hosted demo microservice instances. To learn how to run your own microservice instance in a local environment, refer to [this tutorial](https://developer.ibm.com/tutorials/getting-started-with-the-ibm-code-model-asset-exchange/).

### Explore the image caption generator node

For illustrative purposes, you'll be using the image caption generator node, which analyzes an image and generates a caption. If you prefer, you can follow along using a different node. The steps and concepts covered in this tutorial apply to all nodes.

1. Drag the **image caption generator** node onto the workspace and review the displayed information. The node has one input and one output (the microservice response).

1. Double-click the node to edit it. Two node properties are listed: _service_ and _method_.

   ![Image caption generator node default configuration](images/image_caption_node_default_config.png)

1. Edit the _service_ node property to associate the node with an instance of the image caption generator microservice.

1. By default, _host_ is pre-populated with the URL of a hosted demo instance of this microservice, which you can use to explore its capabilities. Click **Add** to associate this demo instance with your node.

   ![Image caption generator node service configuration](images/image_caption_node_service_config.png)

    > Never use hosted demo microservice instances for production workloads. To use your own (local or cloud) microservice instance, enter its URL (for example, `http://127.0.0.1:5000` if you are running the service on your local machine using the pre-built Docker container from Docker Hub).

1. The Model Asset Exchange microservices expose multiple endpoints, which are listed under the _method_ property. By default, the _predict_ method is selected, which analyzes the node's input. From the drop-down list, select **metadata** to retrieve information about the microservice.

   ![Image caption generator node configuration](images/image_caption_node_config.png)

   > Some methods accept optional parameters, such as a threshold value. (There are none for the image caption generator.)

1. Close the node's properties window.

1. Drag an **inject** _input_ node onto the workspace and connect its output to the input of the image _caption generator_ node.

   > The node name automatically changes to _timestamp_. (We are only using this node to run the flow. The _metadata_ method of the microservices does not require any input.)

1. Drag a **debug** output node onto the workspace and connect its input to the output of the _image caption generator_ node.

   Your completed flow should look like the following image.

   ![This flow retrieves microservice metadata](images/image_caption_metadata_flow.png)

1. Deploy the flow.

1. From the **&#9776;** menu, select **View** > **Debug messages** to open the debug output view.

1. Click the _inject_ node's button to inject a message into the flow and inspect the debug output. If your setup is correct, the output should look like:

   ```
    {
     "id":"im2txt-tensorflow",
     "name":"im2txt TensorFlow Model",
     "description":"im2txt TensorFlow model trained on MSCOCO",
     "license":"APACHE V2"
    }
   ```

You've just confirmed that the node can connect to the specified service. Next, you'll modify the flow to generate an image caption.

### Generate an image caption

The image caption generator's `predict` method analyzes the provided input image and generates a brief description of the image's content.

1. Double-click the _image caption generator_ node to edit its properties.
1. Change the selected _method_ from _metadata_ to **predict**.
1. Replace the _inject_ node with the **file inject** node (or the **camera** node - only supported in Chrome or Firefox) and connect its output to the _image caption generator_ node.

    ![Configure file node](images/image_caption_prediction_flow.png)

1. Deploy the flow.
1. Click the **file inject** (or **camera**) node's button to select an image (or take a picture) and inspect the debug output, which should contain a caption describing the image.

    ```
    a man sitting on a bench with a dog .
    ```

   You can access the entire microservice JSON response through the message object.

1. Open the _debug_ node's properties and change the output to **complete msg object**.
1. Redeploy the flow and rerun it.
1. Inspect the output. The message `details` contain a `predictions` array that contains generated captions.

    ```
    {
     "payload": "a man sitting on a bench with a dog .",
     "_msgid": "e8f93edc.7eddd",
     "statusCode": 200,
     ...
     "details": {
        "status": "ok",
        "predictions": [
            {
                "index": "0",
                "caption": "a man sitting on a bench with a dog .",
                "probability": 0.00033009440665662435
            },
            {
                "index": "1",
                "caption": "a teddy bear sitting on a bench in a park",
                "probability": 0.00009953154282018676
            }
        ]
     }
    }
    ```

### Explore other model nodes

The `node-red-contrib-model-asset-exchange` module includes a couple getting started flows that you can import into your workspace.

1. From the **&#9776;** menu, select **Import** > **Examples** > **model asset-exchange** > **getting started**.

    ![Import Model Asset Exchange example flows](images/import_sample_flows.png)

1. Select one of the flows to import it into your workspace.
1. Review the pre-configured flow, deploy, and run it.

Now that you have a basic understanding of how to use the Model Asset Exchange nodes, feel free to try out some of the more complex Raspberry Pi flows that we've published in the [Node-RED library](https://flows.nodered.org/?term=max&type=flow).

## Summary

In this tutorial, you learned how to generate an image caption in a Node-RED flow using the Image Caption Generator microservice from the Model Asset Exchange. The tutorial showed you how to:

* Import the `node-red-contrib-model-asset-exchange` module into your palette
* Create a flow using a node from the _Model Asst eXchange_ category
* Inspect the output of the _metadata_ and _predict_ methods of that node