---
title: Network Watcher – vytváření protokolů toku NSG pomocí šablony Azure Resource Manager
description: Pomocí šablony Azure Resource Manager a PowerShellu můžete snadno nastavit protokoly toku NSG.
services: network-watcher
documentationcenter: na
author: damendo
manager: twooley
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: a7244aebef6adcfbf96884d377592b575e2c3acb
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102519873"
---
# <a name="configure-nsg-flow-logs-from-an-azure-resource-manager-template"></a>Konfigurace protokolů toku NSG ze šablony Azure Resource Manager

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)
> - [Azure Resource Manager](network-watcher-nsg-flow-logging-azure-resource-manager.md)


[Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) je nativní a výkonný způsob správy vaší [infrastruktury jako kódu](/azure/devops/learn/what-is-infrastructure-as-code)v Azure.

V tomto článku se dozvíte, jak povolit [protokoly toku NSG](./network-watcher-nsg-flow-logging-overview.md) programově pomocí šablony Azure Resource Manager a Azure PowerShell. Začneme tak, že poskytneme Přehled vlastností objektu log Flow NSG následovaný několika ukázkovými šablonami. Pak šablonu nasadíme pomocí místní instance prostředí PowerShell.


## <a name="nsg-flow-logs-object"></a>Objekt protokolů toku NSG

V následujícím seznamu jsou uvedeny objekty protokolů toku NSG se všemi parametry.
Úplný přehled vlastností najdete v článku [referenční informace k šabloně NSG Flow log](/azure/templates/microsoft.network/2019-11-01/networkwatchers/flowlogs#RetentionPolicyParameters).

```json
{
  "name": "string",
  "type": "Microsoft.Network/networkWatchers/flowLogs",
  "location": "string",
  "apiVersion": "2019-09-01",
  "properties": {
    "targetResourceId": "string",
    "storageId": "string",
    "enabled": "boolean",
    "flowAnalyticsConfiguration": {
      "networkWatcherFlowAnalyticsConfiguration": {
         "enabled": "boolean",
         "workspaceResourceId": "string",
          "trafficAnalyticsInterval": "integer"
        },
        "retentionPolicy": {
           "days": "integer",
           "enabled": "boolean"
         },
        "format": {
           "type": "string",
           "version": "integer"
         }
      }
    }
  }
```
Pokud chcete vytvořit prostředek Microsoft. Network/networkWatchers/flowLogs, přidejte výše uvedený JSON do části Resources (prostředky) vaší šablony.


## <a name="creating-your-template"></a>Vytvoření šablony

Pokud používáte šablony Azure Resource Manager poprvé, můžete o nich získat další informace pomocí níže uvedených odkazů.

* [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](../azure-resource-manager/templates/deploy-powershell.md#deploy-local-template-or-bicep-file)
* [Kurz: vytvoření a nasazení první šablony Azure Resource Manager](../azure-resource-manager/templates/template-tutorial-create-first-template.md?tabs=azure-powershell)


Níže jsou uvedeny dva příklady kompletních šablon pro nastavení protokolů toku NSG.

**Příklad 1**: předaná nejjednodušší verze výše s minimálními parametry. Níže uvedená šablona povoluje protokoly toku NSG na cílovém NSG a ukládá je do daného účtu úložiště.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
 {
    "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
    "type": "Microsoft.Network/networkWatchers/FlowLogs/",
    "location": "centraluseuap",
    "apiVersion": "2019-09-01",
    "properties": {
      "targetResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
      "storageId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
      "enabled": true,
      "flowAnalyticsConfiguration": {},
      "retentionPolicy": {},
      "format": {}
    }

  }
  ]
}
```

> [!NOTE]
> * Název prostředku má formát "nadřazený Resource_Child prostředek". Tady je nadřazený prostředek místní instance Network Watcher (formát: NetworkWatcher_RegionName. Příklad: NetworkWatcher_centraluseuap)
> * Parametrem targetresourceid je ID prostředku cílového NSG.
> * storageId je ID prostředku cílového účtu úložiště.

**Příklad 2**: následující šablony povolují protokoly NSG flow (verze 2) s uchováváním po dobu 5 dní. Povolení Analýza provozu s intervalem zpracování 10 minut.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
    {
      "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
      "type": "Microsoft.Network/networkWatchers/FlowLogs/",
      "location": "centraluseuap",
      "apiVersion": "2019-09-01",
      "properties": {
        "targetResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
        "storageId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
        "enabled": true,
        "flowAnalyticsConfiguration": {
          "networkWatcherFlowAnalyticsConfiguration": {
            "enabled": true,
            "workspaceResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/defaultresourcegroup-wcus/providers/Microsoft.OperationalInsights/workspaces/1c4f42e5-3a02-4146-ac9b-3051d8501db0",
            "trafficAnalyticsInterval": 10
          }
        },
        "retentionPolicy": {
          "days": 5,
          "enabled": true
        },
        "format": {
          "type": "JSON",
          "version": 2
        }
      }
    }
  ]
}
```

## <a name="deploying-your-azure-resource-manager-template"></a>Nasazení šablony Azure Resource Manageru

V tomto kurzu se předpokládá, že máte existující skupinu prostředků a NSG, na které můžete povolit protokolování toku.
Kteroukoli z výše uvedených příkladů šablon můžete uložit lokálně jako `azuredeploy.json` . Aktualizujte hodnoty vlastností tak, aby odkazovaly na platné prostředky v rámci vašeho předplatného.

Pokud chcete nasadit šablonu, spusťte v PowerShellu následující příkaz.
```azurepowershell
$context = Get-AzSubscription -SubscriptionId 56acfbd6-vc72-43e9-831f-bcdb6f2c5505
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> Výše uvedené příkazy nasazují prostředek do skupiny prostředků NetworkWatcherRG a ne jako skupinu prostředků obsahující NSG.


## <a name="verifying-your-deployment"></a>Ověření nasazení

Existuje několik způsobů, jak ověřit, zda nasazení proběhlo úspěšně. Konzola PowerShellu by měla zobrazit "ProvisioningState" jako "úspěch". Kromě toho můžete navštívit [stránku portálu NSG Flow logs](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) a potvrdit změny. Pokud se s nasazením vyskytly problémy, podívejte se na [řešení běžných chyb při nasazení Azure pomocí Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md).

## <a name="deleting-your-resource"></a>Odstraňování prostředku
Azure umožňuje odstraňování prostředků prostřednictvím "kompletního" režimu nasazení. Pokud chcete odstranit prostředek toků protokolů, zadejte nasazení v režimu úplné bez zahrnutí prostředku, který chcete odstranit. Přečtěte si další informace o [režimu úplného nasazení](../azure-resource-manager/templates/deployment-modes.md#complete-mode) .

## <a name="next-steps"></a>Další kroky

Naučte se vizualizovat data toku NSG pomocí:
* [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
* [Nástroje pro open source](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
* [Analýza provozu Azure](./traffic-analytics.md)