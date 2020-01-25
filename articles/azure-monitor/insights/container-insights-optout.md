---
title: Jak zastavit monitorování vašeho clusteru služby Azure Kubernetes | Dokumentace Microsoftu
description: Tento článek popisuje, jak můžete ukončit monitorování clusteru Azure AKS pomocí Azure monitoru pro kontejnery.
ms.topic: conceptual
ms.date: 08/19/2019
ms.openlocfilehash: fb75379ba6c8109316f78288222039627bcb5902
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715962"
---
# <a name="how-to-stop-monitoring-your-azure-kubernetes-service-aks-with-azure-monitor-for-containers"></a>Jak zastavit monitorování Azure Kubernetes Service (AKS) pomocí Azure monitoru pro kontejnery

Když povolíte monitorování clusteru AKS, můžete zastavit monitorování clusteru, pokud se rozhodnete, že už nechcete monitorovat. Tento článek ukazuje, jak tento úkol dokončit pomocí rozhraní příkazového řádku Azure nebo s dodané šablony Azure Resource Manageru.  


## <a name="azure-cli"></a>Azure CLI

Použití [az aks disable-addons](https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-disable-addons) příkazu zakažte monitorování Azure pro kontejnery. Příkaz odebere agenta z uzlů clusteru, neodebere řešení ani data, která jsou již shromážděna a uložena v prostředku Azure Monitor.  

```azurecli
az aks disable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG
```

Chcete-li znovu povolte sledování pro váš cluster, přečtěte si téma [povolte monitorování pomocí Azure CLI](container-insights-enable-new-cluster.md#enable-using-azure-cli).

## <a name="azure-resource-manager-template"></a>Šablona Azure Resource Manageru

Zadaná jsou dvě šablony Azure Resource Manageru pro podporu odebrání prostředků řešení ve vaší skupině prostředků konzistentně a opakovaně. Jedna je šablona JSON určující konfiguraci pro zastavení monitorování a druhá obsahuje hodnoty parametrů, které nakonfigurujete k určení ID prostředku clusteru AKS a skupiny prostředků, ve které je cluster nasazený.

Pokud nejste obeznámeni s konceptem nasazení prostředků pomocí šablony, naleznete v tématu:
* [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](../../azure-resource-manager/templates/deploy-powershell.md)
* [Nasazení prostředků pomocí šablon Resource Manageru a Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

>[!NOTE]
>Šablona musí být nasazená ve stejné skupině prostředků clusteru. Pokud při použití této šablony vynecháte jiné vlastnosti nebo doplňky, může dojít k jejich odebrání z clusteru. Například *enableRBAC* pro zásady RBAC implementované v clusteru, nebo *aksResourceTagValues* , pokud jsou pro cluster AKS zadány značky.  
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

4. Upravte hodnoty **aksResourceId** a **aksResourceLocation** pomocí hodnot clusteru AKS, které můžete vyhledat na **vlastnosti** stránek pro vybraný cluster .

    ![Stránka vlastnosti kontejneru](media/container-insights-optout/container-properties-page.png)

    Když jste na **vlastnosti** stránky, zkopírujte také **ID prostředku pracovního prostoru**. Tato hodnota je povinná, pokud se rozhodnete, že chcete odstranit pracovní prostor Log Analytics později. Odstraňuje se pracovní prostor Log Analytics se neprovádí jako součást tohoto procesu.

    Upravte hodnoty pro **aksResourceTagValues** tak, aby odpovídaly existujícím hodnotám značek zadaným pro cluster AKS.

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

Pokud pracovní prostor byl vytvořen pouze k podpoře monitorování clusteru a už je nepotřebujete, budete muset odstranit ručně. Pokud nejste obeznámeni s postup odstranění pracovního prostoru, přečtěte si téma [odstranění pracovního prostoru Azure Log Analytics pomocí webu Azure portal](../../log-analytics/log-analytics-manage-del-workspace.md). Nezapomeňte na **ID prostředku pracovního prostoru** zkopírované dříve v kroku 4, který budete potřebovat.
