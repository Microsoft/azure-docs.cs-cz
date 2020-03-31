---
title: Jak zastavit monitorování clusteru služeb Azure Kubernetes | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak můžete ukončit monitorování clusteru Azure AKS pomocí Azure Monitor pro kontejnery.
ms.topic: conceptual
ms.date: 08/19/2019
ms.openlocfilehash: 618a4d7e10212dd2b042724b1ea11c97920dad57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275253"
---
# <a name="how-to-stop-monitoring-your-azure-kubernetes-service-aks-with-azure-monitor-for-containers"></a>Jak zastavit monitorování služby AKS (Azure Kubernetes Service) pomocí služby Azure Monitor pro kontejnery

Po povolení monitorování clusteru AKS můžete zastavit sledování clusteru, pokud se rozhodnete, že již nechcete sledovat. Tento článek ukazuje, jak toho dosáhnout pomocí azure CLI nebo s poskytnutými šablonami Azure Resource Manager.  


## <a name="azure-cli"></a>Azure CLI

Pomocí příkazu [az aks disable-addons](https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-disable-addons) zakažte Azure Monitor pro kontejnery. Příkaz odebere agenta z uzlů clusteru, neodebere řešení ani data, která jsou už shromážděná a uložená ve vašem prostředku Azure Monitoru.  

```azurecli
az aks disable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG
```

Pokud chcete znovu povolit monitorování pro váš cluster, přečtěte si informace o [povolení monitorování pomocí azure cli](container-insights-enable-new-cluster.md#enable-using-azure-cli).

## <a name="azure-resource-manager-template"></a>Šablona Azure Resource Manageru

Za předpokladu, jsou dvě šablony Azure Resource Manager pro podporu odebrání prostředků řešení konzistentně a opakovaně ve skupině prostředků. Jedním z nich je šablona JSON určující konfiguraci pro zastavení monitorování a druhá obsahuje hodnoty parametrů, které nakonfigurujete k určení ID prostředku clusteru AKS a skupiny prostředků, ve které je cluster nasazen.

Pokud nejste obeznámeni s konceptem nasazování prostředků pomocí šablony, přečtěte si:
* [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](../../azure-resource-manager/templates/deploy-powershell.md)
* [Nasazení prostředků pomocí šablon Správce prostředků a nastavení příkazového uživatelského příkazu Azure](../../azure-resource-manager/templates/deploy-cli.md)

>[!NOTE]
>Šablonu je třeba nasadit ve stejné skupině prostředků clusteru. Pokud při použití této šablony vynechete jiné vlastnosti nebo doplňky, může dojít k jejich odebrání z clusteru. Například *enableRBAC* pro zásady RBAC implementované ve vašem clusteru nebo *aksResourceTagValues,* pokud jsou pro cluster AKS zadány značky.  
>

Pokud se rozhodnete použít azure cli, musíte nejprve nainstalovat a použít příkazového příkazového příkazu místně. Musíte spouštět Azure CLI verze 2.0.27 nebo novější. Chcete-li identifikovat `az --version`verzi, spusťte aplikaci . Pokud potřebujete nainstalovat nebo upgradovat vázačitelné příkazy k Webu Azure, přečtěte si informace [o instalaci příkazového příkazového příkazu k azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

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

2. Uložte tento soubor jako **Soubor OptOutTemplate.json** do místní složky.

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

4. Upravte hodnoty pro **aksResourceId** a **aksResourceLocation** pomocí hodnot clusteru AKS, které najdete na stránce **Vlastnosti** vybraného clusteru.

    ![Stránka vlastností kontejneru](media/container-insights-optout/container-properties-page.png)

    Na stránce **Vlastnosti** zkopírujte také **ID prostředku pracovního prostoru**. Tato hodnota je vyžadována, pokud se rozhodnete, že chcete později odstranit pracovní prostor Analýzy protokolů. Odstranění pracovního prostoru Analýzy protokolů se neprovádí jako součást tohoto procesu.

    Upravte hodnoty **aksResourceTagValues** tak, aby odpovídaly existujícím hodnotám značek určeným pro cluster AKS.

5. Uložte tento soubor jako **OptOutParam.json** do místní složky.

6. Jste připraveni k nasazení této šablony.

### <a name="remove-the-solution-using-azure-cli"></a>Odebrání řešení pomocí azure cli

Spusťte následující příkaz pomocí azure cli na Linuxu odebrat řešení a vyčistit konfiguraci v clusteru AKS.

```azurecli
az login   
az account set --subscription "Subscription Name"
az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
```

Změna konfigurace může trvat několik minut. Po dokončení je vrácena zpráva podobná následující, která obsahuje výsledek:

```output
ProvisioningState       : Succeeded
```

### <a name="remove-the-solution-using-powershell"></a>Odebrání řešení pomocí PowerShellu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Spusťte následující příkazy prostředí PowerShell ve složce obsahující šablonu, odeberte řešení a vyčistěte konfiguraci z clusteru AKS.    

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <yourSubscriptionName>
New-AzResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

Změna konfigurace může trvat několik minut. Po dokončení je vrácena zpráva podobná následující, která obsahuje výsledek:

```output
ProvisioningState       : Succeeded
```


## <a name="next-steps"></a>Další kroky

Pokud byl pracovní prostor vytvořen pouze pro podporu monitorování clusteru a už není potřeba, je nutné jej ručně odstranit. Pokud nejste obeznámeni s tím, jak odstranit pracovní prostor, [přečtěte si tématu Odstranění pracovního prostoru Azure Log Analytics pomocí portálu Azure](../../log-analytics/log-analytics-manage-del-workspace.md). Nezapomeňte na **ID prostředku pracovního prostoru** zkopírované dříve v kroku 4, budete to potřebovat.
