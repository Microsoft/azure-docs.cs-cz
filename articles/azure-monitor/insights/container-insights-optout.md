---
title: Jak zastavit monitorování vašeho clusteru služby Azure Kubernetes | Dokumentace Microsoftu
description: Tento článek popisuje, jak můžete ukončit monitorování clusteru Azure AKS pomocí Azure monitoru pro kontejnery.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 08/19/2019
ms.openlocfilehash: fe0155d6102dac12d5d4c01b78b1ddd45f9bee02
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74382245"
---
# <a name="how-to-stop-monitoring-your-azure-kubernetes-service-aks-with-azure-monitor-for-containers"></a>Jak zastavit monitorování Azure Kubernetes Service (AKS) pomocí Azure monitoru pro kontejnery

Když povolíte monitorování clusteru AKS, můžete zastavit monitorování clusteru, pokud se rozhodnete, že už nechcete monitorovat. Tento článek ukazuje, jak tento úkol dokončit pomocí rozhraní příkazového řádku Azure nebo s dodané šablony Azure Resource Manageru.  


## <a name="azure-cli"></a>Azure CLI

Pomocí příkazu [AZ AKS Disable-addons](https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-disable-addons) zakažte Azure monitor pro kontejnery. Příkaz odebere agenta z uzlů clusteru, neodebere řešení ani data, která jsou již shromážděna a uložena v prostředku Azure Monitor.  

```azurecli
az aks disable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG
```

Pokud chcete pro cluster znovu povolit monitorování, přečtěte si téma [povolení monitorování pomocí Azure CLI](container-insights-enable-new-cluster.md#enable-using-azure-cli).

## <a name="azure-resource-manager-template"></a>Šablona Azure Resource Manageru

Zadaná jsou dvě šablony Azure Resource Manageru pro podporu odebrání prostředků řešení ve vaší skupině prostředků konzistentně a opakovaně. Jedna je šablona JSON určující konfiguraci pro zastavení monitorování a druhá obsahuje hodnoty parametrů, které nakonfigurujete k určení ID prostředku clusteru AKS a skupiny prostředků, ve které je cluster nasazený. 

Pokud nejste obeznámeni s konceptem nasazení prostředků pomocí šablony, naleznete v tématu:
* [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](../../azure-resource-manager/resource-group-template-deploy.md)
* [Nasazení prostředků pomocí šablon Správce prostředků a Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

>[!NOTE]
>Šablona musí být nasazená ve stejné skupině prostředků clusteru. Pokud při použití této šablony vynecháte jiné vlastnosti nebo doplňky, může dojít k jejich odebrání z clusteru. Například *enableRBAC* pro zásady RBAC implementované v clusteru, nebo *aksResourceTagValues* , pokud jsou pro cluster AKS zadány značky.  
>

Pokud se rozhodnete používat rozhraní příkazového řádku Azure, musíte nejprve nainstalovat a používat rozhraní příkazového řádku místně. Musíte používat Azure CLI verze 2.0.27 nebo novější. Pro identifikaci vaší verze spusťte `az --version`. Pokud potřebujete nainstalovat nebo upgradovat rozhraní příkazového řádku Azure CLI, přečtěte si téma [instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). 

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
    "aksResourceTagValues": {
      "type": "object",
      "metadata": {
        "description": "Existing all tags on AKS Cluster Resource"
      }
    },
    "resources": [
      {
        "name": "[split(parameters('aksResourceId'),'/')[8]]",
        "type": "Microsoft.ContainerService/managedClusters",
        "location": "[parameters('aksResourceLocation')]",
        "tags": "[parameters('aksResourceTagValues')]"
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

2. Uložte tento soubor jako **OptOutTemplate. JSON** do místní složky.

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
        },
        "aksResourceTagValues": {
          "value": {
            "<existing-tag-name1>": "<existing-tag-value1>",
            "<existing-tag-name2>": "<existing-tag-value2>",
            "<existing-tag-nameN>": "<existing-tag-valueN>"
          }
        }
      }
    }
    ```

4. Upravte hodnoty pro **aksResourceId** a **aksResourceLocation** pomocí hodnot clusteru AKS, který najdete na stránce **vlastností** pro vybraný cluster.

    ![Stránka vlastnosti kontejneru](media/container-insights-optout/container-properties-page.png)

    Na stránce **vlastnosti** také zkopírujte **ID prostředku pracovního prostoru**. Tato hodnota je povinná, pokud se rozhodnete, že chcete odstranit pracovní prostor Log Analytics později. Odstraňuje se pracovní prostor Log Analytics se neprovádí jako součást tohoto procesu. 

    Upravte hodnoty pro **aksResourceTagValues** tak, aby odpovídaly existujícím hodnotám značek zadaným pro cluster AKS.

5. Uložte tento soubor jako **OptOutParam. JSON** do místní složky.

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

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Spusťte následující příkazy prostředí PowerShell do složky obsahující šablonu, kterou chcete odebrat řešení a vyčištění konfigurace z clusteru AKS.    

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

Pokud pracovní prostor byl vytvořen pouze k podpoře monitorování clusteru a už je nepotřebujete, budete muset odstranit ručně. Pokud nejste obeznámeni s tím, jak pracovní prostor odstranit, přečtěte si téma [odstranění pracovního prostoru Azure Log Analytics pomocí Azure Portal](../../log-analytics/log-analytics-manage-del-workspace.md). Nezapomeňte na **ID prostředku pracovního prostoru** zkopírované dříve v kroku 4, který budete potřebovat. 

