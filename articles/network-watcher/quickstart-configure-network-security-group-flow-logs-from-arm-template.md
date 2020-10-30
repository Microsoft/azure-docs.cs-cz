---
title: 'Rychlý Start: Konfigurace protokolů toku NSG pomocí šablony Azure Resource Manager'
description: Naučte se, jak povolit protokoly toku NSG programově pomocí šablony Azure Resource Manager (šablony ARM) a Azure PowerShell.
services: network-watcher
author: damendo
Customer intent: I need to enable the NSG flow logs using Azure Resource Manager Template
ms.service: network-watcher
ms.topic: quickstart
ms.date: 07/22/2020
ms.author: damendo
ms.custom: subject-armqs
ms.openlocfilehash: 96f30c05527754cbce3b7593c8d62fb56844d41e
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93042746"
---
# <a name="quickstart-configure-nsg-flow-logs-using-an-arm-template"></a>Rychlý Start: Konfigurace protokolů toku NSG pomocí šablony ARM

V tomto rychlém startu povolíte [protokoly toku NSG](network-watcher-nsg-flow-logging-overview.md) pomocí šablony [Azure Resource Manager](../azure-resource-manager/management/overview.md) (šablona ARM) a Azure PowerShell.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Začneme tak, že poskytneme Přehled vlastností objektu log Flow NSG a potom několik vzorových šablon. Pak šablonu nasadíme pomocí místní instance prostředí PowerShell.

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure** . Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-networkwatcher-flowLogs-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-networkwatcher-flowlogs-create).

:::code language="json" source="~/quickstart-templates/101-networkwatcher-flowlogs-create/azuredeploy.json":::

V šabloně je definováno víc prostředků:

- [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
- [Microsoft. Resources/nasazení](/azure/templates/microsoft.resources/deployments)

## <a name="nsg-flow-logs-object"></a>Objekt protokolů toku NSG

V následujícím seznamu jsou uvedeny objekty protokolů toku NSG se všemi parametry. Úplný přehled vlastností najdete v tématu [Microsoft. Network networkWatchers/flowLogs](/azure/templates/microsoft.network/networkwatchers/flowlogs).

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

Chcete-li vytvořit `Microsoft.Network/networkWatchers/flowLogs` prostředek, přidejte výše uvedený JSON do části Resources (prostředky) vaší šablony.

## <a name="creating-your-template"></a>Vytvoření šablony

Pokud používáte šablony ARM poprvé, můžete o nich získat další informace pomocí níže uvedených odkazů.

- [Nasazení prostředků pomocí šablon ARM a Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md#deploy-local-template)
- [Kurz: vytvoření a nasazení první šablony ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-networkwatcher-flowlogs-create).

Pod příkladem kompletní šablony je nejjednodušší verze s minimálními parametry předanými k nastavení protokolů toku NSG. Další příklady najdete v tomto [Průvodci](network-watcher-nsg-flow-logging-azure-resource-manager.md).

**Příklad** : Níže uvedená šablona povoluje protokol toku NSG na cílovém NSG a ukládá je do daného účtu úložiště.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
    {
      "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
      "type": "Microsoft.Network/networkWatchers/FlowLogs/",
      "location": "centraluseuap",
      "apiVersion": "2019-09-01",
      "properties": {
        "targetResourceId": "/subscriptions/<subscription Id>/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
        "storageId": "/subscriptions/<subscription Id>/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
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
> - Název prostředku má formát _nadřazený Resource_Child prostředku_ . Tady je nadřazený prostředek místní instance Network Watcher (formát: NetworkWatcher_RegionName. Příklad: NetworkWatcher_centraluseuap)
> - `targetResourceId` je ID prostředku cílového NSG.
> - `storageId` je ID prostředku cílového účtu úložiště.

## <a name="deploy-the-template"></a>Nasazení šablony

V tomto kurzu se předpokládá, že máte existující skupinu prostředků a NSG, na které můžete povolit protokolování toku.
Kteroukoli z výše uvedených příkladů šablon můžete uložit lokálně jako `azuredeploy.json` . Aktualizujte hodnoty vlastností tak, aby odkazovaly na platné prostředky v rámci vašeho předplatného.

Pokud chcete nasadit šablonu, spusťte v PowerShellu následující příkaz.

```azurepowershell-interactive
$context = Get-AzSubscription -SubscriptionId <subscription Id>
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> Výše uvedené příkazy nasazují prostředek do skupiny prostředků NetworkWatcherRG a ne jako skupinu prostředků obsahující NSG.

## <a name="validate-the-deployment"></a>Ověření nasazení

Existuje několik způsobů, jak ověřit, zda nasazení proběhlo úspěšně. Konzola PowerShellu by se měla zobrazit `ProvisioningState` jako `Succeeded` . Kromě toho můžete navštívit [stránku portálu NSG Flow logs](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) a potvrdit změny. Pokud došlo k problémům s nasazením, přečtěte si téma [řešení běžných chyb nasazení Azure pomocí Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Azure umožňuje odstraňování prostředků v `Complete` režimu nasazení. Pokud chcete odstranit prostředek toků protokolů, zadejte nasazení v `Complete` režimu bez zahrnutí prostředku, který chcete odstranit. Přečtěte si další informace o [režimu úplného nasazení](../azure-resource-manager/templates/deployment-modes.md#complete-mode).

Alternativně můžete zakázat protokol toku NSG z Azure Portal podle následujících kroků:

1. Přihlášení k webu Azure Portal
1. V levé horním rohu portálu vyberte **Všechny služby** . Do **pole Filtr** zadejte _Network Watcher_ . Jakmile se služba **Network Watcher** zobrazí ve výsledcích hledání, vyberte ji.
1. V části **protokoly** vyberte **protokoly toku NSG** .
1. V seznamu skupin zabezpečení sítě vyberte NSG, pro které chcete zakázat protokoly toku.
1. V části **nastavení protokolů toku** nastavte stav protokolu toků na **vypnuto** .
1. Posuňte se dolů a vyberte **Uložit** .

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste povolili protokoly toku NSG. Nyní se musíte naučit, jak vizualizovat data toku NSG pomocí:

- [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
- [Nástroje pro open source](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
- [Analýza provozu Azure](traffic-analytics.md)
