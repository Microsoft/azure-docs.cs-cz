---
title: Sledování sítě – vytvoření protokolů toku skupiny NSG pomocí šablony Správce prostředků Azure
description: Pomocí šablony Azure Resource Manager a Prostředí PowerShell můžete snadno nastavit protokoly toku skupiny NSG.
services: network-watcher
documentationcenter: na
author: damendo
manager: twooley
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/26/2020
ms.author: damendo
ms.openlocfilehash: 104311904b99cadbbc8c0267a98f2709443608ea
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80891454"
---
# <a name="configure-nsg-flow-logs-from-an-azure-resource-manager-template"></a>Konfigurace protokolů toku nsg ze šablony Správce prostředků Azure

> [!div class="op_single_selector"]
> - [Portál Azure](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)
> - [Azure Resource Manager](network-watcher-nsg-flow-logging-azure-resource-manager.md)


[Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) je nativní a výkonný způsob správy [infrastruktury jako kódu.](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code)

Tento článek ukazuje, jak povolit [protokoly toku nsg](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) programově pomocí šablony Azure Resource Manager a Azure PowerShell. Začneme tím, že poskytuje přehled vlastností objektu NSG Flow Log, následovaný několika ukázkovými šablonami. Pak jsme nasadit šablonu pomocí místní instance PowerShell.


## <a name="nsg-flow-logs-object"></a>Objekt toku nsg

Objekt Toku nsg se všemi parametry je uveden níže.
Úplný přehled vlastností si můžete přečíst [odkaz na šablonu protokolů toku nsg](https://docs.microsoft.com/azure/templates/microsoft.network/2019-11-01/networkwatchers/flowlogs#RetentionPolicyParameters).

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
Chcete-li vytvořit prostředek Microsoft.Network/networkWatchers/flowLogs, přidejte výše uvedený json do části prostředků šablony.


## <a name="creating-your-template"></a>Vytvoření šablony

Pokud používáte šablony Azure Resource Manager poprvé, můžete o nich získat další informace pomocí níže uvedených odkazů.

* [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell#deploy-local-template)
* [Kurz: Vytvoření a nasazení první šablony Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-tutorial-create-first-template?tabs=azure-powershell)


Níže jsou uvedeny dva příklady úplných šablon pro nastavení protokolů toku nsg.

**Příklad 1**: Nejjednodušší verze výše uvedeného s minimálními parametry. Níže uvedená šablona umožňuje protokoly toku nsg na cílové skupiny nsg a ukládá je v daném účtu úložiště.

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
> * Název prostředku má formát "Nadřazený prostředek>/podřízený prostředek". Zde je nadřazeným zdrojem místní<RegionName>instance Sledování sítě (Formát: NetworkWatcher_ . Příklad: NetworkWatcher_centraluseuap)
> * targetResourceId je ID prostředku cílové skupiny nsg
> * storageId je ID prostředku cílového účtu úložiště

**Příklad 2**: Následující šablony umožňující protokoly toku nsg (verze 2) s uchováváním po dobu 5 dnů. Povolení traffic analytics s intervalem zpracování 10 minut.

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

Tento kurz předpokládá, že máte existující skupinu prostředků a skupinu nsg můžete povolit přihlášení toku.
Některou z výše uvedených ukázkových `azuredeploy.json`šablon můžete uložit místně jako . Aktualizujte hodnoty vlastností tak, aby ukazovaly na platné prostředky ve vašem předplatném.

Chcete-li šablonu nasadit, spusťte v PowerShellu následující příkaz.
```azurepowershell
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```


## <a name="verifying-your-deployment"></a>Ověření nasazení

Existuje několik způsobů, jak zkontrolovat, zda vaše nasazení proběhlo úspěšně. Konzole PowerShell ubytovny by měl yysvit "ProvisioningState" jako "Succeeded". Kromě toho můžete navštívit [stránku portálu NSG Flow Logs](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) a potvrdit změny. Pokud došlo k problémům s nasazením, podívejte se na [řešení běžných chyb nasazení Azure s Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/common-deployment-errors).

## <a name="deleting-your-resource"></a>Odstranění zdroje
Azure umožňuje odstranění prostředků prostřednictvím režimu nasazení "Complete". Chcete-li odstranit prostředek protokolů toku, zadejte nasazení v režimu Dokončení bez zahrnutí prostředku, který chcete odstranit. Další informace o [režimu úplného nasazení](https://docs.microsoft.com/azure/azure-resource-manager/templates/deployment-modes#complete-mode)

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak vizualizovat data toku nsg pomocí:
* [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
* [Nástroje s otevřeným zdrojovým kódem](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
* [Azure Traffic Analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)
