---
title: Postup zastavení monitorování clusteru Azure Red Hat OpenShift | Microsoft Docs
description: Tento článek popisuje, jak můžete zastavit monitorování clusteru Azure Red Hat OpenShift pomocí Azure Monitor for Containers.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 11/21/2019
ms.openlocfilehash: f769749532a05260bf3c2c9f99483c5607d985a6
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384328"
---
# <a name="how-to-stop-monitoring-your-azure-red-hat-openshift-cluster-with-azure-monitor-for-containers"></a>Postup zastavení monitorování clusteru Azure Red Hat OpenShift pomocí Azure Monitor for Containers

Po povolení monitorování clusteru Azure Red Hat OpenShift můžete zastavit monitorování clusteru, pokud se rozhodnete, že ho už nechcete monitorovat. Tento článek ukazuje, jak toho dosáhnout pomocí poskytnutých Azure Resource Manager šablon.  

## <a name="azure-resource-manager-template"></a>Šablona Azure Resource Manageru

Zadaná jsou dvě šablony Azure Resource Manageru pro podporu odebrání prostředků řešení ve vaší skupině prostředků konzistentně a opakovaně. Jedna je šablona JSON určující konfiguraci pro zastavení monitorování a druhá obsahuje hodnoty parametrů, které nakonfigurujete k určení ID prostředku clusteru OpenShift a oblasti Azure, ve které je cluster nasazený. 

Pokud nejste obeznámeni s konceptem nasazení prostředků pomocí šablony, naleznete v tématu:
* [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](../../azure-resource-manager/resource-group-template-deploy.md)
* [Nasazení prostředků pomocí šablon Správce prostředků a Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Pokud se rozhodnete používat rozhraní příkazového řádku Azure, musíte nejprve nainstalovat a používat rozhraní příkazového řádku místně. Musíte používat Azure CLI verze 2.0.65 nebo novější. Pro identifikaci vaší verze spusťte `az --version`. Pokud potřebujete nainstalovat nebo upgradovat rozhraní příkazového řádku Azure CLI, přečtěte si téma [instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). 

### <a name="create-template"></a>Vytvoření šablony

1. Zkopírujte a vložte do souboru následující syntaxi JSON:

    ```json
    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "aroResourceId": {
           "type": "string",
           "metadata": {
             "description": "ARO Cluster Resource ID"
          }
        },
        "aroResourceLocation": {
          "type": "string",
          "metadata": {
            "description": "Location of the aro cluster resource e.g. westcentralus"
          }
        }
      },
      "resources": [
        {
           "name": "[split(parameters('aroResourceId'),'/')[8]]",
           "type": "Microsoft.ContainerService/openShiftManagedClusters",
           "location": "[parameters('aroResourceLocation')]",
           "apiVersion": "2019-09-30-preview",
           "properties": {
             "mode": "Incremental",
             "id": "[parameters('aroResourceId')]",
             "monitorProfile": {
               "workspaceResourceID": null,
               "enabled": false
            }
          }
        }
      ]
    }
    ```

2. Uložte tento soubor jako **OptOutTemplate. JSON** do místní složky.

3. Vložte následující syntaxi JSON do souboru:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aroResourceId": {
          "value": "/subscriptions/<subscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.ContainerService/openShiftManagedClusters/<clusterName>"
        },
        "aroResourceLocation": {
          "value": "<azure region of the cluster e.g. westcentralus>"
        }
      }
    }
    ```

4. Upravte hodnoty pro **aroResourceId** a **aroResourceLocation** pomocí hodnot clusteru OpenShift, který najdete na stránce **vlastností** pro vybraný cluster.

    ![Stránka vlastnosti kontejneru](media/container-insights-optout-openshift/cluster-properties-page.png)

5. Uložte tento soubor jako **OptOutParam. JSON** do místní složky.

6. Jste připraveni k nasazení této šablony. 

### <a name="remove-the-solution-using-azure-cli"></a>Odebrat řešení pomocí Azure CLI

Spuštěním následujícího příkazu u Azure CLI v systému Linux odeberte řešení a vyčistěte konfiguraci v clusteru.

```azurecli
az login   
az account set --subscription "Subscription Name" 
az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
```

Změna konfigurace může trvat několik minut. Když se dokončí, je vrácena zpráva podobná následující, který obsahuje výsledek:

```azurecli
ProvisioningState       : Succeeded
```

### <a name="remove-the-solution-using-powershell"></a>Odebrat řešení pomocí Powershellu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Spusťte následující příkazy PowerShellu ve složce obsahující šablonu pro odebrání řešení a vyčištění konfigurace z clusteru.    

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <yourSubscriptionName>
New-AzResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

Změna konfigurace může trvat několik minut. Když se dokončí, je vrácena zpráva podobná následující, který obsahuje výsledek:

```powershell
ProvisioningState       : Succeeded
```

## <a name="next-steps"></a>Další kroky

Pokud pracovní prostor byl vytvořen pouze k podpoře monitorování clusteru a už je nepotřebujete, budete muset odstranit ručně. Pokud nejste obeznámeni s tím, jak pracovní prostor odstranit, přečtěte si téma [odstranění pracovního prostoru Azure Log Analytics](../../log-analytics/log-analytics-manage-del-workspace.md). 
