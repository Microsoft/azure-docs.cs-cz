---
title: Povolit Azure Monitor na zařízení s grafickým procesorem Azure Stack Edge pro
description: Popisuje, jak povolit Azure Monitor na zařízení GPU Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: c2f66895fccd14dcffd8c5570f1d5f46933090aa
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102439183"
---
# <a name="enable-azure-monitor-on-your-azure-stack-edge-pro-gpu-device"></a>Povolení Azure Monitor na zařízení GPU pro Azure Stack Edge pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Monitorování kontejnerů na zařízení GPU Azure Stack Edge pro je velmi důležité, speciálně při spuštění více výpočetních aplikací. Azure Monitor umožňuje shromažďovat protokoly kontejnerů a metriky paměti a procesoru z clusteru Kubernetes běžícího na vašem zařízení.

Tento článek popisuje kroky potřebné k povolení Azure Monitor na zařízení a shromáždění protokolů kontejneru v pracovním prostoru Log Analytics. Úložiště metrik Azure Monitor se v současnosti nepodporuje u zařízení s grafickým procesorem Azure Stack Edge pro. 


## <a name="prerequisites"></a>Požadavky

Než začnete, budete potřebovat:

- Zařízení Azure Stack Edge pro. Ujistěte se, že zařízení je aktivované podle kroků v [kurzu: aktivace zařízení](azure-stack-edge-gpu-deploy-activate.md).
- Dokončili jste **konfiguraci výpočetního** kroku podle [kurzu: Konfigurace výpočetních prostředků na zařízení Azure Stack Edge pro](azure-stack-edge-gpu-deploy-configure-compute.md) na vašem zařízení. Vaše zařízení by mělo mít přidružený prostředek IoT Hub, zařízení IoT a IoT Edge zařízení.


## <a name="create-log-analytics-workspace"></a>Vytvoření pracovního prostoru služby Log Analytics

Pokud chcete vytvořit pracovní prostor Log Analytics, proveďte následující kroky. Pracovní prostor Log Analytics je logická jednotka úložiště, kde se shromažďují a ukládají data protokolu.

1. V Azure Portal vyberte **+ vytvořit prostředek** a vyhledejte **Log Analytics pracovní prostor** a pak vyberte **vytvořit**. 
1. V **pracovním prostoru vytvořit Log Analytics** nakonfigurujte následující nastavení. Přijměte zbytek jako výchozí.

    1. Na kartě **základy** zadejte pro pracovní prostor předplatné, skupinu prostředků, název a oblast. 

        ![Karta základy pro pracovní prostor Log Analytics](media/azure-stack-edge-gpu-enable-azure-monitor/create-log-analytics-workspace-basics-1.png)  

    1. Na kartě **cenová úroveň** přijměte výchozí **plán průběžných plateb**.

        ![Karta ceny pro Log Analytics pracovní prostor](media/azure-stack-edge-gpu-enable-azure-monitor/create-log-analytics-workspace-pricing-1.png) 

    1. Na kartě **Revize + vytvořit** zkontrolujte informace o pracovním prostoru a vyberte **vytvořit**.

        ![Kontrola a vytvoření pro Log Analytics pracovní prostor](media/azure-stack-edge-gpu-enable-azure-monitor/create-log-analytics-workspace-review-create-1.png)

Další informace najdete v podrobných krocích v tématu [Vytvoření pracovního prostoru Log Analytics prostřednictvím Azure Portal](../azure-monitor/logs/quick-create-workspace.md).



## <a name="enable-container-insights"></a>Povolit službu Container Insights

Pokud chcete ve svém pracovním prostoru povolit službu Container Insights, proveďte následující kroky. 

1. Postupujte podle podrobných kroků v tématu [Postup přidání řešení Azure monitor Containers](../azure-monitor/containers/container-insights-hybrid-setup.md#how-to-add-the-azure-monitor-containers-solution). Použijte následující soubor šablony `containerSolution.json` :

    ```yml
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceResourceId": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace Resource ID"
            }
        },
        "workspaceRegion": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace region"
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "name": "[Concat('ContainerInsights', '-',  uniqueString(parameters('workspaceResourceId')))]",
            "apiVersion": "2017-05-10",
            "subscriptionId": "[split(parameters('workspaceResourceId'),'/')[2]]",
            "resourceGroup": "[split(parameters('workspaceResourceId'),'/')[4]]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "apiVersion": "2015-11-01-preview",
                            "type": "Microsoft.OperationsManagement/solutions",
                            "location": "[parameters('workspaceRegion')]",
                            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                            "properties": {
                                "workspaceResourceId": "[parameters('workspaceResourceId')]"
                            },
                            "plan": {
                                "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                                "product": "[Concat('OMSGallery/', 'ContainerInsights')]",
                                "promotionCode": "",
                                "publisher": "Microsoft"
                            }
                        }
                    ]
                },
                "parameters": {}
            }
            }
        ]
    }
    ```

1. Získejte ID a umístění prostředku. Přejděte na `Your Log Analytics workspace > General > Properties`. Zkopírujte následující informace:

    - **ID prostředku**, což je plně kvalifikované ID prostředku Azure pracovního prostoru Azure Log Analytics. 
    - **umístění**, což je oblast Azure.

    ![Vlastnosti pracovního prostoru Log Analytics](media/azure-stack-edge-gpu-enable-azure-monitor/log-analytics-workspace-properties-1.png) 

1. Použijte následující soubor parametrů `containerSolutionParams.json` . Nahraďte `workspaceResourceId` ID prostředku a `workspaceRegion` umístěním, které jste zkopírovali v předchozím kroku.

    ```yaml
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        "workspaceResourceId": {
            "value": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourcegroups/myaserg/providers/microsoft.operationalinsights/workspaces/myaseloganalyticsws"
        },
        "workspaceRegion": {
        "value": "westus"
        }
        }
    }
    ```
    
    Tady je ukázkový výstup Log Analytics pracovního prostoru s povolenou službou Container Insights:
    
    ```powershell
    Requesting a Cloud Shell.Succeeded.
    Connecting terminal...
    MOTD: Switch to Bash from PowerShell: bash
    VERBOSE: Authenticating to Azure ...
    VERBOSE: Building your Azure drive ...
    
    PS /home/alpa> az account set -s fa68082f-8ff7-4a25-95c7-ce9da541242f
    PS /home/alpa> ls
    clouddrive  containerSolution.json
    PS /home/alpa> ls
    clouddrive  containerSolution.json  containerSolutionParams.json
    PS /home/alpa> az deployment group create --resource-group myaserg --name Testdeployment1 --template-file containerSolution.json --parameters containerSolutionParams.json
    {- Finished ..
        "id": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourceGroups/myaserg/providers/Microsoft.Resources/deployments/Testdeployment1",
        "location": null,
        "name": "Testdeployment1",
        "properties": {
        "correlationId": "3a9045fe-2de0-428c-b17b-057508a8c575",
        "debugSetting": null,
        "dependencies": [],
        "duration": "PT11.1588316S",
        "error": null,
        "mode": "Incremental",
        "onErrorDeployment": null,
        "outputResources": [
            {
            "id": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourceGroups/myaserg/providers/Microsoft.OperationsManagement/solutions/ContainerInsights(myaseloganalyticsws)",
            "resourceGroup": "myaserg"
            }
        ],
        "outputs": null,
        "parameters": {
            "workspaceRegion": {
            "type": "String",
            "value": "westus"
            },
            "workspaceResourceId": {
            "type": "String",
            "value": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourcegroups/myaserg/providers/microsoft.operationalinsights/workspaces/myaseloganalyticsws"
            }
        },
        "parametersLink": null,
        "providers": [
            {
            "id": null,
            "namespace": "Microsoft.Resources",
            "registrationPolicy": null,
            "registrationState": null,
            "resourceTypes": [
                {
                "aliases": null,
                "apiProfiles": null,
                "apiVersions": null,
                "capabilities": null,
                "defaultApiVersion": null,
                "locations": [
                    null
                ],
                "properties": null,
                "resourceType": "deployments"
                }
            ]
            }
        ],
        "provisioningState": "Succeeded",
        "templateHash": "10500027184662969395",
        "templateLink": null,
        "timestamp": "2020-11-06T22:09:56.908983+00:00",
        "validatedResources": null
        },
        "resourceGroup": "myaserg",
        "tags": null,
        "type": "Microsoft.Resources/deployments"
    }
    PS /home/alpa>
    ```

## <a name="configure-azure-monitor-on-your-device"></a>Konfigurace Azure Monitor na zařízení

1. Přejít na nově vytvořený prostředek Log Analytics a zkopírovat ID a **primární klíč** pracovního **prostoru** (klíč pracovního prostoru).

    ![Správa agentů v pracovním prostoru Log Analytics](media/azure-stack-edge-gpu-enable-azure-monitor/log-analytics-workspace-agents-management-1.png)

    Tyto informace uložte, protože je budete používat v pozdějším kroku.

1. [Připojte se k rozhraní PowerShell zařízení](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface). 
    
1. Pomocí následující rutiny použijte ID pracovního prostoru Log Analytics a klíč pracovního prostoru:

    `Set-HcsKubernetesAzureMonitorConfiguration -WorkspaceId <> -WorkspaceKey <>`

1. Po povolení Azure Monitor by se měly zobrazit protokoly v pracovním prostoru Log Analytics. Pokud chcete zobrazit stav clusteru Kubernetes nasazeného na zařízení, přečtěte si **Azure monitor kontejnerů > Insights >**. Pro možnost prostředí vyberte **vše**. 

    ![Metriky v pracovním prostoru Log Analytics](media/azure-stack-edge-gpu-enable-azure-monitor/log-analytics-workspace-metrics-1.png)

## <a name="next-steps"></a>Další kroky

- Naučte se [monitorovat úlohy Kubernetes pomocí řídicího panelu Kubernetes](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md).
- Naučte se [spravovat oznámení o výstrahách událostí zařízení](azure-stack-edge-gpu-manage-device-event-alert-notifications.md). 