---
title: Jak zastavit sledování clusteru Azure Red Hat OpenShift | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak můžete zastavit monitorování clusteru Azure Red Hat OpenShift pomocí Azure Monitor pro kontejnery.
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: eff5203aeedd3c7ad283b55ba12f0e45a556460d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250722"
---
# <a name="how-to-stop-monitoring-your-azure-red-hat-openshift-cluster-with-azure-monitor-for-containers"></a>Jak zastavit monitorování clusteru Azure Red Hat OpenShift pomocí Azure Monitoru pro kontejnery

Po povolení monitorování clusteru Azure Red Hat OpenShift můžete zastavit sledování clusteru, pokud se rozhodnete, že už ho nechcete monitorovat. Tento článek ukazuje, jak toho dosáhnout pomocí zadaných šablon Azure Resource Manager.  

## <a name="azure-resource-manager-template"></a>Šablona Azure Resource Manageru

Za předpokladu, jsou dvě šablony Azure Resource Manager pro podporu odebrání prostředků řešení konzistentně a opakovaně ve skupině prostředků. Jedním z nich je šablona JSON určující konfiguraci pro zastavení monitorování a druhá obsahuje hodnoty parametrů, které nakonfigurujete k určení ID prostředku clusteru OpenShift a oblasti Azure, ve které je cluster nasazen.

Pokud nejste obeznámeni s konceptem nasazování prostředků pomocí šablony, přečtěte si:
* [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](../../azure-resource-manager/templates/deploy-powershell.md)
* [Nasazení prostředků pomocí šablon Správce prostředků a nastavení příkazového uživatelského příkazu Azure](../../azure-resource-manager/templates/deploy-cli.md)

Pokud se rozhodnete použít azure cli, musíte nejprve nainstalovat a použít příkazového příkazového příkazu místně. Musíte spouštět Azure CLI verze 2.0.65 nebo novější. Chcete-li identifikovat `az --version`verzi, spusťte aplikaci . Pokud potřebujete nainstalovat nebo upgradovat vázačitelné příkazy k Webu Azure, přečtěte si informace [o instalaci příkazového příkazového příkazu k azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

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

2. Uložte tento soubor jako **Soubor OptOutTemplate.json** do místní složky.

3. Do souboru vložte následující syntaxi JSON:

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

4. Upravte hodnoty pro **aroResourceId** a **aroResourceLocation** pomocí hodnot clusteru OpenShift, které najdete na stránce **Vlastnosti** vybraného clusteru.

    ![Stránka vlastností kontejneru](media/container-insights-optout-openshift/cluster-properties-page.png)

5. Uložte tento soubor jako **OptOutParam.json** do místní složky.

6. Jste připraveni k nasazení této šablony.

### <a name="remove-the-solution-using-azure-cli"></a>Odebrání řešení pomocí azure cli

Spusťte následující příkaz pomocí azure cli na Linuxu odebrat řešení a vyčistit konfiguraci ve vašem clusteru.

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

Spusťte následující příkazy prostředí PowerShell ve složce obsahující šablonu, odeberte řešení a vyčistěte konfiguraci z clusteru.    

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

Pokud byl pracovní prostor vytvořen pouze pro podporu monitorování clusteru a už není potřeba, je nutné jej ručně odstranit. Pokud nejste obeznámeni s tím, jak odstranit pracovní prostor, přečtěte si informace [o odstranění pracovního prostoru Azure Log Analytics](../../log-analytics/log-analytics-manage-del-workspace.md).
