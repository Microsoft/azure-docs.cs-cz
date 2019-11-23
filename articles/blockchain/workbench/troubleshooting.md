---
title: Azure Blockchain Workbench troubleshooting
description: How to troubleshoot an Azure Blockchain Workbench Preview application.
ms.date: 10/14/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: ef4bce4dfba77aafa9b86c6877c153534b54636e
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74324307"
---
# <a name="azure-blockchain-workbench-preview-troubleshooting"></a>Azure Blockchain Workbench Preview troubleshooting

A PowerShell script is available to assist with developer debugging or support. The script generates a summary and collects detailed logs for troubleshooting. Collected logs include:

* Blockchain network, such as Ethereum
* Blockchain Workbench microservices
* Application Insights
* Azure Monitoring (Azure Monitor logs)

You can use the information to determine next steps and determine root cause of issues.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="troubleshooting-script"></a>Troubleshooting script

The PowerShell troubleshooting script is available on GitHub. [Stáhněte soubor .zip](https://github.com/Azure-Samples/blockchain/archive/master.zip) nebo naklonujte ukázku z GitHubu.

```
git clone https://github.com/Azure-Samples/blockchain.git
```

## <a name="run-the-script"></a>Spuštění skriptu
[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

Run the `collectBlockchainWorkbenchTroubleshooting.ps1` script to collect logs and create a ZIP file containing a folder of troubleshooting information. Například:

``` powershell
collectBlockchainWorkbenchTroubleshooting.ps1 -SubscriptionID "<subscription_id>" -ResourceGroupName "workbench-resource-group-name"
```
The script accepts the following parameters:

| Parametr  | Popis | Požaduje se |
|---------|---------|----|
| SubscriptionID | SubscriptionID to create or locate all resources. | Ano |
| ResourceGroupName | Name of the Azure Resource Group where Blockchain Workbench has been deployed. | Ano |
| OutputDirectory | Path to create the output .ZIP file. If not specified, defaults to the current directory. | Ne |
| LookbackHours | Number of hours to use when pulling telemetry. Default value is 24 hours. Maximum value is 90 hours | Ne |
| OmsSubscriptionId | The subscription ID where Azure Monitor logs is deployed. Only pass this parameter if the Azure Monitor logs for the blockchain network is deployed outside of Blockchain Workbench's resource group.| Ne |
| OmsResourceGroup |The resource group where Azure Monitor logs is deployed. Only pass this parameter if the Azure Monitor logs for the blockchain network is deployed outside of Blockchain Workbench's resource group.| Ne |
| OmsWorkspaceName | The Log Analytics workspace name. Only pass this parameter if the Azure Monitor logs for the blockchain network is deployed outside of Blockchain Workbench's resource group | Ne |

## <a name="what-is-collected"></a>What is collected?

The output ZIP file contains the following folder structure:

| Folder or File | Popis  |
|---------|---------|
| \Summary.txt | Summary of the system |
| \Metrics\blockchain | Metrics about the blockchain |
| \Metrics\Workbench | Metrics about the workbench |
| \Details\Blockchain | Detailed logs about the blockchain |
| \Details\Workbench | Detailed logs about the workbench |

The summary file gives you a snapshot of the overall state of the application and health of the application. The summary provides recommended actions, highlights top errors, and metadata about running services.

The **Metrics** folder contains metrics of various system components over time. For example, the output file `\Details\Workbench\apiMetrics.txt` contains a summary of different response codes, and response times throughout the collection period. The **Details** folder contains detailed logs for troubleshooting specific issues with Workbench or the underlying blockchain network. For example, `\Details\Workbench\Exceptions.csv` contains a list of the most recent exceptions that have occurred in the system, which is useful for troubleshooting errors with smart contracts or interactions with the blockchain. 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench Application Insights troubleshooting guide](https://aka.ms/workbenchtroubleshooting)
