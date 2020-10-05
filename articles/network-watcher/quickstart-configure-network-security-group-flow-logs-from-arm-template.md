---
title: 'Rychlý Start: Konfigurace protokolů toku NSG pomocí šablony Azure Resource Manager'
description: Naučte se, jak povolit protokoly toku NSG programově pomocí šablony Azure Resource Manager a Azure PowerShell.
services: network-watcher
author: damendo
Customer intent: I need to enable the NSG Flow Logs using Azure Resource Manager Template
ms.service: network-watcher
ms.topic: quickstart
ms.date: 07/22/2020
ms.author: damendo
ms.custom: subject-armqs
ms.openlocfilehash: 7d8cb89b1187bb15e7b361e1b6b9505400c612b5
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "87986313"
---
# <a name="quickstart-configure-nsg-flow-logs-from-arm-template"></a>Rychlý Start: Konfigurace protokolů toku NSG ze šablony ARM

V tomto rychlém startu povolíte [protokoly toku NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) programově pomocí šablony [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/overview) (šablony ARM) a Azure PowerShell. 

Začneme tak, že poskytneme Přehled vlastností objektu log Flow NSG a potom několik vzorových šablon. Pak šablonu nasadíme pomocí místní instance prostředí PowerShell.

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="nsg-flow-logs-object"></a>Objekt protokolů toku NSG

V následujícím seznamu jsou uvedeny objekty protokolů toku NSG se všemi parametry.
Úplný přehled vlastností najdete v článku [referenční informace k šabloně NSG Flow log](https://docs.microsoft.com/azure/templates/microsoft.network/networkwatchers/flowlogs).

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

* [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell#deploy-local-template)
* [Kurz: vytvoření a nasazení první šablony Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-tutorial-create-first-template?tabs=azure-powershell)

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-networkwatcher-flowlogs-create).

Pod příkladem kompletní šablony je nejjednodušší verze s minimálními parametry předanými k nastavení protokolů toku NSG. Další příklady najdete na tomto [odkazu](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-azure-resource-manager).

**Příklad**: Níže uvedená šablona povoluje protokol toku NSG na cílovém NSG a ukládá je do daného účtu úložiště.

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


## <a name="deploying-your-azure-resource-manager-template"></a>Nasazení šablony Azure Resource Manageru

V tomto kurzu se předpokládá, že máte existující skupinu prostředků a NSG, na které můžete povolit protokolování toku.
Kteroukoli z výše uvedených příkladů šablon můžete uložit lokálně jako `azuredeploy.json` . Aktualizujte hodnoty vlastností tak, aby odkazovaly na platné prostředky v rámci vašeho předplatného.

Pokud chcete nasadit šablonu, spusťte v PowerShellu následující příkaz.
```azurepowershell-interactive
$context = Get-AzSubscription -SubscriptionId 56acfbd6-vc72-43e9-831f-bcdb6f2c5505
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> Výše uvedené příkazy nasazují prostředek do skupiny prostředků NetworkWatcherRG a ne jako skupinu prostředků obsahující NSG.


## <a name="validate-the-deployment"></a>Ověření nasazení

Existuje několik způsobů, jak ověřit, zda nasazení proběhlo úspěšně. Konzola PowerShellu by měla zobrazit "ProvisioningState" jako "úspěch". Kromě toho můžete navštívit [stránku portálu NSG Flow logs](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) a potvrdit změny. Pokud se s nasazením vyskytly problémy, podívejte se na [řešení běžných chyb při nasazení Azure pomocí Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/common-deployment-errors).

## <a name="deleting-your-resource"></a>Odstraňování prostředku
Azure umožňuje odstraňování prostředků prostřednictvím "kompletního" režimu nasazení. Pokud chcete odstranit prostředek toků protokolů, zadejte nasazení v režimu úplné bez zahrnutí prostředku, který chcete odstranit. Přečtěte si další informace o [režimu úplného nasazení](https://docs.microsoft.com/azure/azure-resource-manager/templates/deployment-modes#complete-mode) .

Alternativně můžete zakázat protokol toku NSG z Azure Portal podle následujících kroků:
1. Přihlášení k webu Azure Portal
2. V levé horním rohu portálu vyberte **Všechny služby**. Do **pole Filtr** zadejte *Network Watcher*. Jakmile se služba**Network Watcher** zobrazí ve výsledcích hledání, vyberte ji.
3. V části **protokoly**vyberte **protokoly toku NSG** .
4. V seznamu skupin zabezpečení sítě vyberte NSG, pro které chcete zakázat protokoly toku.
5. V části **nastavení protokolů toku nastavte možnost**stav protokolu toků na **vypnuto** .
6. Přejděte dolů a vyberte **Uložit** .

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste povolili protokoly toku NSG. Nyní se musíte naučit, jak vizualizovat data toku NSG pomocí: 

* [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
* [Nástroje pro open source](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
* [Analýza provozu Azure](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)
