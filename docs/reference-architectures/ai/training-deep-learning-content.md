<!-- > The H1 title is the same as the title metadata. Don't enter it here, but as the **name** value in the corresponding YAML file.

_Brief introduction goes here (1-3 sentences, one paragraph only)._ [**Deploy this scenario**.](#deploy-this-scenario) -->
An official step-by-step guide of best-practices with techniques and optimizations for running large scale distributed training on AzureML. Includes all aspects of the data science steps to manage enterprise grade MLOps lifecycle from resource setup and data loading to training optimizations, evaluation and optimizations for inference.

A reference implementation of this architecture is availible [here](https://github.com/Azure/azureml-examples/tree/main/best-practices/largescale-deep-learning/Training).

## Architecture

![distributed-training](https://user-images.githubusercontent.com/73311224/227603812-0b989aad-1e27-4c48-9628-fcea46088dd6.png)

>TODO!!! _Download a [Visio file](https://arch-center.azureedge.net/distributed-dl-architecture.vsdx) that contains this architecture diagram. This file must be uploaded to `https://arch-center.azureedge.net/`_

### Workflow

The following workflow (or dataflow) corresponds to the above diagram:

- **Training Files**. These files include the training script, the job submission yaml file, and any other files necessary for training. The job will be submitted with the YAML file that will specify the distribution type, compute, environment, storage, etc.

- **Mounted Blob Storage**. This is where the logs and outputs of the job will be stored. It is important when running large scale distributed training jobs that data is accessed in the most preformant way. [This guide](https://github.com/Azure/azureml-examples/blob/main/best-practices/largescale-deep-learning/Data-loading/data-loading.md#how-can-model-training-performance-be-impacted-by-data-loading) on data loading gives insight on what the best storage method is depending on the specifics of the scenario. 

- **Environment**. This is where the dependencies for training are defined. Curated with large scale distributed training in mind, AzureML provides [Azure Container for PyTorch (ACPT)](https://learn.microsoft.com/en-us/azure/machine-learning/resource-azure-container-for-pytorch) environments that include several cutting edge optimizers.

- **Compute**. Using Azure Machine Learning computes, it is possible to provision and manage clusters of VMs, schedule jobs, gather results, scale resources, and handle failures.

### Components

**[Azure Machine Learning](https://azure.microsoft.com/services/machine-learning)** is an open platform for managing the development and deployment of machine-learning models at scale. The platform supports commonly used open frameworks and offers automated featurization and algorithm selection. You can use Machine Learning to deploy models to various targets, including Azure Container Instances.

**[Standard Blob Storage](/azure/storage/blobs/storage-blobs-introduction)** is used to store the logs and outputs. Standard Blob Storage is recommended for most scenarios, but in certain cases Premium Storage is preferred.

**[Azure Container Registry](/azure/container-registry/container-registry-intro)** is used to store the Docker image that Azure Machine Learning Compute uses to run the training.

## Scenario details

Training large models with large datasets has often led to state-of-the-art accuracies across a range of tasks. However, training these large models is not always easy and comes with challenges that require multi-faceted solutions. These challenges include:
 - GPU memory capacity is limited, making it impossible to fit large models on a single GPU or even on a multi-GPU server.
 - Number of compute operations required to train large models can result in long training times.

By using distributed training with cutting edge optimizations, AzureML is able to provide a solution to these problems that has allowed numerous customers to use Azure Machine Learning for training models with millions/billions of parameters.

### Optimizations
#### Infiniband enabled SKUs
For large scale distributed model training, azureml computes are ideal due to the linear scaling provided by Infiniband. Linear scaling refers to the concept that as the number of VMs training a given model increases, the time to train that model should decrease linearly. AzureML offers optimized supercomputer hardware with high bandwidth interconnects to enable low latency, GPU-to-GPU communication across nodes in a cluster.
The full list of AzureML InfiniBand-enabled machine SKUs can be viewed [here](https://learn.microsoft.com/en-us/azure/virtual-machines/sizes-hpc#rdma-capable-instances).
#### Data Loading
Optimizing data loading is essential for training LLMs due to the large amount of data required. Some things that need to be taken into consideration are storage choice, VM choice, neural network size, file size, data location, and how efficient the client code is at accessing storage. Each of these considerations is expanded in more detail [here](https://github.com/Azure/azureml-examples/blob/main/best-practices/largescale-deep-learning/Data-loading/data-loading.md#how-can-model-training-performance-be-impacted-by-data-loading).
#### DeepSpeed
DeepSpeed is an open-source library developed by Microsoft that optimizes the training of large deep learning models. It aims to reduce the time and memory requirements needed for training large models with trillions of parameters on distributed GPU clusters.

DeepSpeed optimizes training through a combination of several techniques, including gradient accumulation, model parallelism, and automatic mixed precision (AMP). In addition, DeepSpeed also uses a technique known as ZeRO (Zero Redundancy Optimizer). ZeRO partitions a model into small pieces and stores only one copy of each piece in memory, effectively reducing the amount of memory required to train very large models. This can be a huge advantage since running out of memory is a common problem when training large models with limited resources.

#### Onnx Runtime Training (ORT)

ORT, in conjuction with HuggingFace's Optimum library has the goal of getting maximum efficiency out of users' hardware. ORT optimizes in several different ways, including efficient memory planning, kernel optimizations, multi tensor apply for Adam Optimizer, mixed precision training and graph optimizations like node fusions and node eliminations.

The ORTTrainer provided through the HuggingFace Optimum library is also very flexible; it's a one line change to add as an optimizer for training and integrates easily with other optimization tools such as DeepSpeed.

## Considerations

These considerations implement the pillars of the Azure Well-Architected Framework, which is a set of guiding tenets that can be used to improve the quality of a workload. For more information, see [Microsoft Azure Well-Architected Framework](/azure/architecture/framework).

### Reliability

Reliability ensures your application can meet the commitments you make to your customers. For more information, see [Overview of the reliability pillar](/azure/architecture/framework/resiliency/overview).

Providing resiliency and reliability is particularly important for large scale distributed training jobs. When training a model for long periods of time, job failures can be very impactful, wasting time and resources. Fortunately with model checkpointing, the training process can be saved at periodic checkpoints and if the training fails due to hardware faults it can be resumed while losing no progress. AzureML will also automatically resume jobs failed due to hardware faults.

This architecture also uses Nebula checkpointing, which improves on standard model checkpointing by saving models 1000 times faster. For more information on how Nebula checkpointing can improve the reliability of your jobs, see [this overview page](https://github.com/Azure/azureml-examples/blob/main/best-practices/largescale-deep-learning/Training/Nebula-Fast-Checkpointing/nebula.md).

### Cost optimization

Cost optimization is about looking at ways to reduce unnecessary expenses and improve operational efficiencies. For more information, see [Overview of the cost optimization pillar](/azure/architecture/framework/cost/overview).

Training large models often takes several days, which means the cost of using resources for will steadily increase as the training progresses. It is also likely that the hourly cost will be significant do to the quantity of resources needed. This architecture is built with those costs directly considered, though multiple cost saving measures including efficient data loading, linearly scaling VMs, accelerated model saving and training throughput optimizers.

Since total training time is directly correlated with cost because of resources being used and throughput correlates to how well the resources are being utilized in a training job, reducing the training time and throughput is the goal of these optimizations. Applying this architecture with Azure Machine Learning showed a 27.8% improvement in training the BERT model, as shown in [this example](https://github.com/Azure/azureml-examples/blob/main/best-practices/largescale-deep-learning/Training/Bert-Pretrain/README.md) on Github.

For more information on exact pricing for running your deep learning workload, check out the [Azure Pricing Calculator](https://azure.microsoft.com/en-us/pricing/calculator/).

### Operational excellence

Operational excellence covers the operations processes that deploy an application and keep it running in production. For more information, see [Overview of the operational excellence pillar](/azure/architecture/framework/devops/overview).

To achieve operational excellence, this architecture employs several tools to help monitor resource performance and catch problems while they are happening. Aside from the automatically logged stdout and stderr streams of the training script, this architecture also uses Tensorboard, interactive debugging and Pytorch Profiler.

#### Tensorboard
- Tensorboard can be used to monitor the progress of a job by showing metrics recorded in real time. This also includes DeepSpeed recorded metrics if DeepSpeed is enabled.

#### VSCode Interactive Debugging
- Submit a job with a debugger attached and execution paused.

#### Pytorch Profiler
- Shown with a nice UI via Tensorboard, the Pytorch Profiler shows extended detail on resource utilization during training.

More information on interactive debugging can be found on [this introduction page](https://learn.microsoft.com/en-us/azure/machine-learning/how-to-interactive-jobs?tabs=ui#attach-a-debugger-to-a-job).
### Performance efficiency

Performance efficiency is the ability of your workload to scale to meet the demands placed on it by users in an efficient manner. For more information, see [Performance efficiency pillar overview](/azure/architecture/framework/scalability/overview).

Many of the techniques used in this architecture are added specifically for large scale training jobs. If you are running a smaller scale training job, many of the optimizations are not necessary. Distributed training is best suited for:
- Large models that can't be trained by using a reasonable batch size on a single GPU.
- Problems that can't be addressed by distributing the model in a simple, parallel way.

Distributed training isn't recommended for running hyperparameter searches. The scaling efficiency affects performance and makes a distributed approach less efficient than training multiple model configurations separately.

One way to increase scaling efficiency is to increase the batch size. But make this adjustment carefully. Increasing the batch size without adjusting the other parameters can impair the model's final performance.

## Deploy this scenario

A deployment for a reference architecture that implements these recommendations and considerations is available on [GitHub](https://github.com/Azure/azureml-examples/tree/main/best-practices/largescale-deep-learning/Training).


## Contributors

*This article is maintained by Microsoft. It was originally written by the following contributors.*

Principal authors: > Only the primary authors. Listed alphabetically by last name. Use this format: Fname Lname. If the article gets rewritten, keep the original authors and add in the new one(s).

 - [Cassie Esvelt](https://www.linkedin.com/in/cassie-esvelt-b87724202/) | Software Engineer
 - [Ilia Karmanov](https://www.linkedin.com/in/ilia-karmanov-09aa588b) | Senior Applied Scientist
 - [Savita Mittal](https://www.linkedin.com/in/savita-mittal-b8524012/) | Principal Software Engineer
 - [Mathew Salvaris](https://www.linkedin.com/in/drmathewsalvaris) | Principal Data Scientist Lead
 - [Razvan Tanase](https://www.linkedin.com/in/razvantanase/) | Principal Software Engineering Manager

Examples:
* [Azure Machine Learning documentation](/azure/machine-learning)
* [AzureML Large Scale Deep Learning Best Practices](https://github.com/Azure/azureml-examples/tree/main/best-practices/largescale-deep-learning)
