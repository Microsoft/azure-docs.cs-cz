---
title: Postup zastavení monitorování clusteru služby Azure Kubernetes | Microsoft Docs
description: Tento článek popisuje, jak můžete zrušit monitorování clusteru Azure AKS pomocí Azure Monitor for Containers.
ms.topic: conceptual
ms.date: 08/19/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 15ec102632258870745f510a98773f70242118be
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "96011638"
---
# <a name="how-to-stop-monitoring-your-azure-kubernetes-service-aks-with-azure-monitor-for-containers"></a>Jak zastavit monitorování služby AKS (Azure Kubernetes Service) pomocí služby Azure Monitor pro kontejnery

Po povolení monitorování clusteru AKS můžete zastavit monitorování clusteru, pokud se rozhodnete, že ho už nechcete monitorovat. V tomto článku se dozvíte, jak toho dosáhnout pomocí Azure CLI nebo pomocí poskytnutých šablon Azure Resource Manager.  


## <a name="azure-cli"></a>Azure CLI

Pomocí příkazu [AZ AKS Disable-addons](/cli/azure/aks?view=azure-cli-latest#az-aks-disable-addons) zakažte Azure monitor pro kontejnery. Příkaz odebere agenta z uzlů clusteru, neodebere řešení ani data, která jsou již shromážděna a uložena v prostředku Azure Monitor.  

```azurecli
az aks disable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG
```

Pokud chcete pro cluster znovu povolit monitorování, přečtěte si téma [povolení monitorování pomocí Azure CLI](container-insights-enable-new-cluster.md#enable-using-azure-cli).

## <a name="azure-resource-manager-template"></a>Šablona Azure Resource Manageru

K dispozici jsou dvě šablony Azure Resource Manager, které podporují odebrání prostředků řešení konzistentně a opakovaně ve vaší skupině prostředků. Jedna je šablona JSON určující konfiguraci pro zastavení monitorování a druhá obsahuje hodnoty parametrů, které nakonfigurujete k určení ID prostředku clusteru AKS a skupiny prostředků, ve které je cluster nasazený.

Pokud nejste obeznámeni s konceptem nasazení prostředků pomocí šablony, přečtěte si téma:
* [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](../../azure-resource-manager/templates/deploy-powershell.md)
* [Nasazení prostředků pomocí šablon Správce prostředků a Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

>[!NOTE]
>Šablona musí být nasazená ve stejné skupině prostředků clusteru. Pokud při použití této šablony vynecháte jiné vlastnosti nebo doplňky, může dojít k jejich odebrání z clusteru. Například *enableRBAC* pro zásady RBAC Kubernetes implementované v clusteru, nebo *aksResourceTagValues* , pokud jsou pro cluster AKS zadány značky.  
>

Pokud se rozhodnete používat rozhraní příkazového řádku Azure, musíte nejdřív nainstalovat a používat rozhraní příkazového řádku (CLI). Musíte používat Azure CLI verze 2.0.27 nebo novější. Pro identifikaci vaší verze spusťte `az --version` . Pokud potřebujete nainstalovat nebo upgradovat rozhraní příkazového řádku Azure CLI, přečtěte si téma [instalace Azure CLI](/cli/azure/install-azure-cli).

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
       },
    "aksResourceTagValues": {
      "type": "object",
      "metadata": {
        "description": "Existing all tags on AKS Cluster Resource"
        }
      }
     },
    "resources": [
      {
        "name": "[split(parameters('aksResourceId'),'/')[8]]",
        "type": "Microsoft.ContainerService/managedClusters",
        "location": "[parameters('aksResourceLocation')]",
        "tags": "[parameters('aksResourceTagValues')]",
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

2. Uložte tento soubor jako **OptOutTemplate.js** do místní složky.

3. Do souboru vložte následující syntaxi JSON:

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

    ![Stránka vlastností kontejneru](media/container-insights-optout/container-properties-page.png)

    Na stránce **vlastnosti** také zkopírujte **ID prostředku pracovního prostoru**. Tato hodnota je povinná, pokud se rozhodnete, že chcete pracovní prostor Log Analytics odstranit později. Odstranění pracovního prostoru Log Analytics se neprovádí jako součást tohoto procesu.

    Upravte hodnoty pro **aksResourceTagValues** tak, aby odpovídaly existujícím hodnotám značek zadaným pro cluster AKS.

5. Uložte tento soubor jako **OptOutParam.js** do místní složky.

6. Jste připraveni k nasazení této šablony.

### <a name="remove-the-solution-using-azure-cli"></a>Odebrání řešení pomocí Azure CLI

Spuštěním následujícího příkazu s Azure CLI v systému Linux odeberte řešení a vyčistěte konfiguraci v clusteru AKS.

```azurecli
az login   
az account set --subscription "Subscription Name"
az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
```

Dokončení změny konfigurace může trvat několik minut. Po dokončení se vrátí zpráva podobná následující, která obsahuje výsledek:

```output
ProvisioningState       : Succeeded
```

### <a name="remove-the-solution-using-powershell"></a>Odebrání řešení pomocí PowerShellu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Spusťte následující příkazy PowerShellu ve složce obsahující šablonu pro odebrání řešení a vyčištění konfigurace z clusteru AKS.    

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <yourSubscriptionName>
New-AzResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

Dokončení změny konfigurace může trvat několik minut. Po dokončení se vrátí zpráva podobná následující, která obsahuje výsledek:

```output
ProvisioningState       : Succeeded
```


## <a name="next-steps"></a>Další kroky

Pokud byl pracovní prostor vytvořen jenom pro podporu monitorování clusteru a už ho nepotřebujete, musíte ho ručně odstranit. Pokud nejste obeznámeni s tím, jak pracovní prostor odstranit, přečtěte si téma [odstranění pracovního prostoru Azure Log Analytics pomocí Azure Portal](../platform/delete-workspace.md). Nezapomeňte na **ID prostředku pracovního prostoru** zkopírované dříve v kroku 4, který budete potřebovat.
