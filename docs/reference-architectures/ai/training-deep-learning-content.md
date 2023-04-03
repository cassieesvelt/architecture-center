<!-- > The H1 title is the same as the title metadata. Don't enter it here, but as the **name** value in the corresponding YAML file.

_Brief introduction goes here (1-3 sentences, one paragraph only)._ [**Deploy this scenario**.](#deploy-this-scenario) -->
An official step-by-step guide of best-practices with techniques and optimizations for running large scale distributed training on AzureML. Includes all aspects of the data science steps to manage enterprise grade MLOps lifecycle from resource setup and data loading to training optimizations, evaluation and optimizations for inference.

A reference implementation of this architecture is availible [here](https://github.com/Azure/azureml-examples/tree/main/best-practices/largescale-deep-learning/Training).

## Architecture

![distributed-training](https://user-images.githubusercontent.com/73311224/227603812-0b989aad-1e27-4c48-9628-fcea46088dd6.png)

>TODO!!! _Download a [Visio file](https://arch-center.azureedge.net/distributed-dl-architecture.vsdx) that contains this architecture diagram. This file must be uploaded to `https://arch-center.azureedge.net/`_

### Workflow

> Use a numbered list if there are corresponding numbers in the diagram. If not, use a bulleted list.

The following workflow (or dataflow) corresponds to the above diagram:

- **Training Files**. These files include the training script, the job submission yaml file, and any other files necessary for training. The job will be submitted with the YAML file that will specify the distribution type, compute, environment, storage, etc.

- **Mounted Blob Storage**. This is where the logs and outputs of the job will be stored. It is important when running large scale distributed training jobs that data is accessed in the most preformant way. [This guide](https://github.com/Azure/azureml-examples/blob/main/best-practices/largescale-deep-learning/Data-loading/data-loading.md#how-can-model-training-performance-be-impacted-by-data-loading) on data loading gives insight on what the best storage method is depending on the specifics of a large scale scenario.

- **Environment**. This is where the dependencies for training are defined. Curated with large scale distributed training in mind, AzureML provides [Azure Container for PyTorch (ACPT)](https://learn.microsoft.com/en-us/azure/machine-learning/resource-azure-container-for-pytorch) environments that include several cutting edge optimizers.

- **Compute**. Using Azure Machine Learning computes, it is possible to provision and manage clusters of VMs, schedule jobs, gather results, scale resources, and handle failures. For large scale distributed model training, these computes are especially helpful due to the linear scaling provided by Infiniband enabled SKUs. Linear scaling refers to the concept that as the number of VMs training a given model increases, the time to train that model should decrease linearly. AzureML offers optimized supercomputer hardware with high bandwidth interconnects to enable low latency, GPU-to-GPU communication across nodes in a cluster.
The full list of AzureML InfiniBand-enabled machine SKUs can be viewed [here](https://learn.microsoft.com/en-us/azure/virtual-machines/sizes-hpc#rdma-capable-instances).

### Components

**[Azure Machine Learning](https://azure.microsoft.com/services/machine-learning)** is an open platform for managing the development and deployment of machine-learning models at scale. The platform supports commonly used open frameworks and offers automated featurization and algorithm selection. You can use Machine Learning to deploy models to various targets, including Azure Container Instances.

**[Standard Blob Storage](/azure/storage/blobs/storage-blobs-introduction)** is used to store the logs and outputs. Standard Blob Storage is recommended for most scenarios, but in certain cases Premium Storage is preferred. More details can be found in [this guide](https://github.com/Azure/azureml-examples/blob/main/best-practices/largescale-deep-learning/Data-loading/data-loading.md#how-can-model-training-performance-be-impacted-by-data-loading).

**[Azure Container Registry](/azure/container-registry/container-registry-intro)** is used to store the Docker image that Azure Machine Learning Compute uses to run the training.

## Scenario details

> This should be an introduction of the business problem and why this scenario was built to solve it.
>   What industry is the customer in?
>   What prompted them to solve the problem?
>   What services were used in building out this solution?
>   What does this example scenario show? What are the customer's goals?

> What were the benefits of implementing the solution described below?

Training large models with large datasets has often led to state-of-the-art accuracies across a range of tasks. However, training these large models is not always easy and comes with challenges that require multi-faceted solutions. These challenges include:
 - GPU memory capacity is limited, making it impossible to fit large models on a single GPU or even on a multi-GPU server.
 - Number of compute operations required to train large models can result in long training times.

By using distributed training with cutting edge optimizations, AzureML is able to provide a solution to these problems that has allowed numerous customers to use Azure Machine Learning for training models with millions/billions of parameters.


### Potential use cases

> Are there any other use cases or industries where this would be a fit?
> How similar or different are they to what's in this article?

These other uses cases have similar design patterns:

- List of example use cases

## Recommendations

The following recommendations apply for most scenarios. Follow these recommendations unless you have a specific requirement that overrides them.

_Include considerations for deploying or configuring the elements of this architecture._



## Considerations

> REQUIRED STATEMENT: Include the following statement to introduce this section:

These considerations implement the pillars of the Azure Well-Architected Framework, which is a set of guiding tenets that can be used to improve the quality of a workload. For more information, see [Microsoft Azure Well-Architected Framework](/azure/architecture/framework).

> Are there any lessons learned from running this that would be helpful for new customers?  What went wrong when building it out?  What went right?
> How do I need to think about managing, maintaining, and monitoring this long term?

> REQUIREMENTS: 
>   For a Reference Architecture, you must include all of these H3 sub-sections/WAF pillars: Reliability, Security, Cost optimization, Operational excellence, and Performance efficiency.

- Should deepspeed + ORT go in cost optimization or performance efficiency?

### Reliability

> REQUIRED STATEMENT: Include the following statement to introduce the section:

Reliability ensures your application can meet the commitments you make to your customers. For more information, see [Overview of the reliability pillar](/azure/architecture/framework/resiliency/overview).

> This section includes resiliency and availability considerations. They can also be H4 headers in this section, if you think they should be separated.
> Are there any key resiliency and reliability considerations (past the typical)?

- Providing resiliency and reliability is particularly important for large scale distributed training jobs. When training a model for long periods of time, job failures can be very impactful, wasting time and resources. Fortunately with model checkpointing, the training process can be saved at periodic checkpoints and if the training fails due to hardware faults it can be resumed while losing no progress. AzureML will also automatically resume jobs failed due to hardware faults.
- This architecture also uses Nebula checkpointing, which improves on standard model checkpointing by saving models 1000 times faster. For more information on how Nebula checkpointing can improve the reliability of your jobs, see [this overview page](https://github.com/Azure/azureml-examples/blob/main/best-practices/largescale-deep-learning/Training/Nebula-Fast-Checkpointing/nebula.md).

### Security

> REQUIRED STATEMENT: Include the following statement to introduce the section:

Security provides assurances against deliberate attacks and the abuse of your valuable data and systems. For more information, see [Overview of the security pillar](/azure/architecture/framework/security/overview).

> This section includes identity and data sovereignty considerations.
> Are there any security considerations (past the typical) that I should know about this?
> Because security is important to our business, be sure to include your Azure security baseline assessment recommendations in this section. See https://aka.ms/AzureSecurityBaselines

### Cost optimization

> REQUIRED STATEMENT: Include the following statement to introduce the section:

Cost optimization is about looking at ways to reduce unnecessary expenses and improve operational efficiencies. For more information, see [Overview of the cost optimization pillar](/azure/architecture/framework/cost/overview).

> How much will this cost to run? See if you can answer this without dollar amounts.
> Are there ways I could save cost?
> If it scales linearly, than we should break it down by cost/unit. If it does not, why?
> What are the components that make up the cost?
> How does scale affect the cost?

> Link to the pricing calculator (https://azure.microsoft.com/en-us/pricing/calculator) with all of the components in the architecture included, even if they're a $0 or $1 usage.
> If it makes sense, include small/medium/large configurations. Describe what needs to be changed as you move to larger sizes.
- Training large models often takes several days, which means the cost of using resources for will steadily increase as the training progresses. It is also likely that the hourly cost will be significant do to the quantity of resources needed. This architecture is built with those costs directly considered, though multiple cost saving measures including efficient data loading, linearly scaling VMs, accelerated model saving and training throughput optimizers.
- Since total training time is directly correlated with cost because of resources being used and throughput correlates to how well the resources are being utilized in a training job, reducing the training time and throughput is the goal of these optimizations. Applying this architecture with Azure Machine Learning showed a 27.8% improvement in training the BERT model, as shown in [this example](https://github.com/Azure/azureml-examples/blob/main/best-practices/largescale-deep-learning/Training/Bert-Pretrain/README.md) on Github.
- For more information on exact pricing for running your deep learning workload, check out the [Azure Pricing Calculator](https://azure.microsoft.com/en-us/pricing/calculator/).

### Operational excellence

> REQUIRED STATEMENT: Include the following statement to introduce the section:

Operational excellence covers the operations processes that deploy an application and keep it running in production. For more information, see [Overview of the operational excellence pillar](/azure/architecture/framework/devops/overview).

> This includes DevOps, monitoring, and diagnostics considerations.
> How do I need to think about operating this solution?

- talk about profilers, tensorboard + interactive debugging here ?

### Performance efficiency

> REQUIRED STATEMENT: Include the following statement to introduce the section:

Performance efficiency is the ability of your workload to scale to meet the demands placed on it by users in an efficient manner. For more information, see [Performance efficiency pillar overview](/azure/architecture/framework/scalability/overview).

> This includes scalability considerations.
> Are there any key performance considerations (past the typical)?
> Are there any size considerations around this specific solution? What scale does this work at? At what point do things break or not make sense for this architecture?

- linear scaling with Infiniband ?

## Deploy this scenario

> REQUIRED: Reference Architectures require a deployment. If you cannot provide a deployment, use the Example Workload template instead. 

_Describe a step-by-step process for implementing the reference architecture solution. Best practices are to add the solution to GitHub, provide a link (use boilerplate text below), and explain how to roll out the solution._

A deployment for a reference architecture that implements these recommendations and considerations is available on [GitHub](https://www.github.com/path-to-repo).

1. First step
2. Second step
3. Third step ...

- simple how to start example and/or just a link to bestpractices page or bert training example?

## Contributors

> (Expected, but this section is optional if all the contributors would prefer to not be mentioned.)

> Start with the explanation text (same for every section), in italics. This makes it clear that Microsoft takes responsibility for the article (not the one contributor). Then include the "Principal authors" list and the "Other contributors" list, if there are additional contributors (all in plain text, not italics or bold). Link each contributor's name to the person's LinkedIn profile. After the name, place a pipe symbol ("|") with spaces, and then enter the person's title. We don't include the person's company, MVP status, or links to additional profiles (to minimize edits/updates). (The profiles can be linked to from the person's LinkedIn page, and we hope to automate that on the platform in the future). Implement this format:

*This article is maintained by Microsoft. It was originally written by the following contributors.*

Principal authors: > Only the primary authors. Listed alphabetically by last name. Use this format: Fname Lname. If the article gets rewritten, keep the original authors and add in the new one(s).

 - [Author 1 Name](http://linkedin.com/ProfileURL) | Title, such as "Cloud Solution Architect"
 - [Author 2 Name](http://linkedin.com/ProfileURL) | Title, such as "Cloud Solution Architect"
 - > Continue for each primary author (even if there are 10 of them).

Other contributors: > Include contributing (but not primary) authors, major editors (not minor edits), and technical reviewers. Listed alphabetically by last name. Use this format: Fname Lname. It's okay to add in newer contributors.

 - [Contributor 1 Name](http://linkedin.com/ProfileURL) | Title, such as "Cloud Solution Architect"
 - [Contributor 2 Name](http://linkedin.com/ProfileURL) | Title, such as "Cloud Solution Architect"
 - > Continue for each additional contributor (even if there are 10 of them).

*To see non-public LinkedIn profiles, sign in to LinkedIn.*

## Next steps

> Link to Learn articles. Could also be to appropriate sources outside of Learn, such as GitHub repos, third-party documentation, or an official technical blog post.

Examples:
* [Azure Machine Learning documentation](/azure/machine-learning)
* [What are Azure Cognitive Services?](/azure/cognitive-services/what-are-cognitive-services)

## Related resources

> Use "Related resources" for architecture information that's relevant to the current article. It must be content that the Azure Architecture Center TOC refers to, but may be from a repo other than the AAC repo.
> Links to articles in the AAC repo should be repo-relative, for example (../../solution-ideas/articles/article-name.yml).

> Here is an example section:

Fully deployable architectures:

* [Chatbot for hotel reservations](/azure/architecture/example-scenario/ai/commerce-chatbot)
* [Build an enterprise-grade conversational bot](/azure/architecture/reference-architectures/ai/conversational-bot)
* [Speech-to-text conversion](/azure/architecture/reference-architectures/ai/speech-ai-ingestion)