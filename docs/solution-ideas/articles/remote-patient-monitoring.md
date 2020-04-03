---
title: Remote Patient Monitoring Solutions
titleSuffix: Azure Solution Ideas
author: adamboeglin
ms.date: 12/16/2019
description: Provide a high level of preventative medical care with remote patient monitoring from Azure. Analyze large amounts of medical data in a secure environment.
ms.custom: acom-architecture, ai-ml, healthcare, remote patient monitoring, remote patient monitoring system, medical data analysis, remote patient monitoring solutions, interactive-diagram, 'https://azure.microsoft.com/solutions/architecture/remote-patient-monitoring/'
ms.service: architecture-center
ms.subservice: solution-idea
---

# Remote Patient Monitoring Solutions

[!INCLUDE [header_file](../header.md)]

Predict and prevent future incidents by combining IoT and intelligence to optimize treatments, using Azure to remotely monitor patients and analyze the massive amounts of data generated by medical devices.

## Architecture

![Architecture diagram](../media/remote-patient-monitoring.svg)

## Data Flow

1. Securely ingest medical sensor and device data using Azure IoT Hub.
1. Securely store sensor and device data in Cosmos DB.
1. Analyze sensor and device data using a pre-trained Cognitive Services API or a custom developed Machine Learning model.
1. Store Artificial Intelligence (AI) and Machine Learning results in Cosmos DB.
1. Interact AI and Machine Learning results using PowerBI, while preserving Role-Based Access Control (RBAC).
1. Integrate data insights with backend systems and processes using Logic Apps.

## Components

* [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub): Connect, monitor and manage billions of IoT assets
* [Security Center](https://azure.microsoft.com/services/security-center): Unify security management and enable advanced threat protection across hybrid cloud workloads
* [Cognitive Services](https://azure.microsoft.com/services/cognitive-services): Get started with quickstarts, samples, and tutorials
* [Key Vault](https://azure.microsoft.com/services/key-vault): Safeguard and maintain control of keys and other secrets
* [Logic Apps](https://azure.microsoft.com/services/logic-apps): Automate the access and use of data across clouds without writing code
* [Power BI Embedded](https://azure.microsoft.com/services/power-bi-embedded): Embed fully interactive, stunning data visualizations in your applications
* [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db): Globally distributed, multi-model database for any scale
* Application Insights: Detect, triage, and diagnose issues in your web apps and services
* [Machine Learning Studio](https://azure.microsoft.com/services/machine-learning-studio): Easily build, deploy, and manage predictive analytics solutions
* [Azure Monitor](https://azure.microsoft.com/services/monitor): Full observability into your applications, infrastructure, and network

## Next steps

* [IoT Hub Documentation](https://docs.microsoft.com/azure/iot-hub)
* [Azure Security Center Documentation](https://docs.microsoft.com/azure/security-center)
* [Get Started with Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide)
* [What is Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
* [Azure Logic Apps Documentation](https://docs.microsoft.com/azure/logic-apps)
* [Power BI Embedded Documentation](https://docs.microsoft.com/azure/power-bi-embedded)
* [Azure Cosmos DB Documentation](https://docs.microsoft.com/azure/cosmos-db)
* [Application Insights Documentation](https://docs.microsoft.com/azure/application-insights)
* [Azure Machine Learning Documentation](https://docs.microsoft.com/azure/machine-learning)
* [Azure Monitor Documentation](https://docs.microsoft.com/azure/monitoring-and-diagnostics)