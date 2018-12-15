---
title: Jak zastavit monitorování vašeho clusteru služby Azure Kubernetes | Dokumentace Microsoftu
description: Tento článek popisuje, jak můžete ukončit monitorování clusteru Azure AKS pomocí Azure monitoru pro kontejnery.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/13/2018
ms.author: magoedte
ms.openlocfilehash: f9c2324eb429c82f7e937b4f18311bf204eeb193
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53408749"
---
# <a name="how-to-stop-monitoring-your-azure-kubernetes-service-aks-with-azure-monitor-for-containers"></a>Jak zastavit monitorování Azure Kubernetes Service (AKS) pomocí Azure monitoru pro kontejnery

Když povolíte monitorování clusteru AKS, můžete zastavit monitorování clusteru, pokud se rozhodnete, že už nechcete monitorovat. Tento článek ukazuje, jak tento úkol dokončit pomocí rozhraní příkazového řádku Azure nebo s dodané šablony Azure Resource Manageru.  


## <a name="azure-cli"></a>Azure CLI
Použití [az aks disable-addons](https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-disable-addons) příkazu zakažte monitorování Azure pro kontejnery. Příkaz odebere agenta z uzlů clusteru, neodstraní řešení nebo datového již shromážděná a uložená v prostředku Log Analytics.  

```azurecli
az aks disable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG
```

Chcete-li znovu povolte sledování pro váš cluster, přečtěte si téma [povolte monitorování pomocí Azure CLI](container-insights-onboard.md#enable-monitoring-using-azure-cli).

## <a name="azure-resource-manager-template"></a>Šablona Azure Resource Manageru
Zadaná jsou dvě šablony Azure Resource Manageru pro podporu odebrání prostředků řešení ve vaší skupině prostředků konzistentně a opakovaně. Jedna je určení konfigurace se zastavit monitorování šablony JSON a druhý obsahuje hodnoty parametrů, které můžete nakonfigurovat a určit tak AKS skupinu prostředků clusteru ID a prostředků, která je nasazená clusteru. 

Pokud nejste obeznámeni s konceptem nasazení prostředků pomocí šablony, naleznete v tématu:
* [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](../../azure-resource-manager/resource-group-template-deploy.md)
* [Nasazení prostředků pomocí šablon Resource Manageru a Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

>[!NOTE]
>Šablona musí být nasazený ve stejné skupině prostředků jako cluster. Pokud vynecháte žádné vlastnosti nebo doplňky při použití této šablony, může způsobit v jejich odebrání z clusteru. Například *enableRBAC*.  
>

Pokud se rozhodnete používat rozhraní příkazového řádku Azure, musíte nejprve nainstalovat a používat rozhraní příkazového řádku místně. Musíte používat Azure CLI verze 2.0.27 nebo novější. Zjistěte verzi, spusťte `az --version`. Pokud potřebujete instalaci nebo upgrade rozhraní příkazového řádku Azure, najdete v článku [instalace rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

### <a name="create-template"></a>Vytvoření šablony

1. Zkopírujte a vložte do souboru následující syntaxi JSON:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
           "type": "string",
           "metadata": {
             "description": "AKS Cluster Resource ID"
           }
       },
      "aksResourceLocation": {
        "type": "string",
        "metadata": {
           "description": "Location of the AKS resource e.g. \"East US\""
         }
       }
    },
    "resources": [
      {
        "name": "[split(parameters('aksResourceId'),'/')[8]]",
        "type": "Microsoft.ContainerService/managedClusters",
        "location": "[parameters('aksResourceLocation')]",
        "apiVersion": "2018-03-31",
        "properties": {
          "mode": "Incremental",
          "id": "[parameters('aksResourceId')]",
          "addonProfiles": {
            "omsagent": {
              "enabled": false,
              "config": null
            }
           }
         }
       }
      ]
    }
    ```

2. Uložte soubor jako **OptOutTemplate.json** do místní složky.
3. Vložte následující syntaxi JSON do souboru:

    ```json
    {
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "aksResourceId": {
         "value": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
      },
      "aksResourceLocation": {
        "value": "<aksClusterRegion>"
        }
      }
    }
    ```

4. Upravte hodnoty **aksResourceId** a **aksResourceLocation** pomocí hodnot clusteru AKS, které můžete vyhledat na **vlastnosti** stránek pro vybraný cluster .

    ![Stránka vlastnosti kontejneru](media/container-insights-optout/container-properties-page.png)

    Když jste na **vlastnosti** stránky, zkopírujte také **ID prostředku pracovního prostoru**. Tato hodnota je povinná, pokud se rozhodnete, že chcete odstranit pracovní prostor Log Analytics později. Odstraňuje se pracovní prostor Log Analytics se neprovádí jako součást tohoto procesu. 

5. Uložte soubor jako **OptOutParam.json** do místní složky.
6. Jste připraveni k nasazení této šablony. 

### <a name="remove-the-solution-using-azure-cli"></a>Odebrat řešení pomocí Azure CLI
Spusťte následující příkaz pomocí rozhraní příkazového řádku Azure v Linuxu odebrat řešení a vyčištění konfigurace ve vašem clusteru AKS.

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

Spusťte následující příkazy prostředí PowerShell do složky obsahující šablonu, kterou chcete odebrat řešení a vyčištění konfigurace z clusteru AKS.    

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName <yourSubscriptionName>
New-AzureRmResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

Změna konfigurace může trvat několik minut. Když se dokončí, je vrácena zpráva podobná následující, který obsahuje výsledek:

```powershell
ProvisioningState       : Succeeded
```

Pokud pracovní prostor byl vytvořen pouze k podpoře monitorování clusteru a už je nepotřebujete, budete muset odstranit ručně. Pokud nejste obeznámeni s postup odstranění pracovního prostoru, přečtěte si téma [odstranění pracovního prostoru Azure Log Analytics pomocí webu Azure portal](../../log-analytics/log-analytics-manage-del-workspace.md). Nezapomeňte **ID prostředku pracovního prostoru** jsme si zkopírovali dříve v kroku 4, budete potřebovat, který. 

