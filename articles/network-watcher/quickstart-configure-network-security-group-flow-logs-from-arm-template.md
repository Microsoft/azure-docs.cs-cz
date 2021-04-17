---
title: 'Rychlý Start: Konfigurace protokolu toku skupiny zabezpečení sítě pomocí šablony Azure Resource Manager (šablona ARM)'
description: Naučte se, jak pomocí šablony Azure Resource Manager (šablona ARM) a Azure PowerShell povolit protokol toků skupin zabezpečení sítě (NSG).
services: network-watcher
author: damendo
ms.author: damendo
ms.date: 01/07/2021
ms.topic: quickstart
ms.service: network-watcher
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: df0ccb5bf5ecd60d80526085983e35abf58e9966
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532433"
---
# <a name="quickstart-configure-network-security-group-flow-logs-by-using-an-arm-template"></a>Rychlý Start: Konfigurace protokolů toku skupiny zabezpečení sítě pomocí šablony ARM

V tomto rychlém startu se dozvíte, jak povolit [protokoly toku NSG (Network Security Group)](network-watcher-nsg-flow-logging-overview.md) pomocí šablony [Azure Resource Manager](../azure-resource-manager/management/overview.md) (šablona ARM) a Azure PowerShell.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Začneme s přehledem vlastností objektu log Flow NSG. Poskytujeme vzorové šablony. Pak použijeme místní instanci Azure PowerShell k nasazení šablony.

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v Azure Portal.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-networkwatcher-flowLogs-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="review-the-template"></a>Kontrola šablony

Šablona, kterou používáme v tomto rychlém startu, je ze [šablon Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/101-networkwatcher-flowlogs-create).

:::code language="json" source="~/quickstart-templates/101-networkwatcher-flowlogs-create/azuredeploy.json":::

Tyto prostředky jsou definované v šabloně:

- [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
- [Microsoft. Resources/nasazení](/azure/templates/microsoft.resources/deployments)

## <a name="nsg-flow-logs-object"></a>Objekt protokolů toku NSG

Následující kód ukazuje objekt log Flow NSG a jeho parametry. Chcete-li vytvořit `Microsoft.Network/networkWatchers/flowLogs` prostředek, přidejte tento kód do části Resources (prostředky) vaší šablony:

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

Úplný přehled vlastností objektu NSG Flow log najdete v tématu [Microsoft. Network networkWatchers/flowLogs](/azure/templates/microsoft.network/networkwatchers/flowlogs).

## <a name="create-your-template"></a>Vytvoření šablony

Pokud používáte šablony ARM poprvé, přečtěte si další informace o šablonách ARM v následujících článcích:

- [Nasazení prostředků pomocí šablon ARM a Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md#deploy-local-template-or-bicep-file)
- [Kurz: vytvoření a nasazení první šablony ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

Následující příklad je kompletní šablona. Je to také nejjednodušší verze šablony. Příklad obsahuje minimální parametry, které jsou předány k nastavení protokolů toku NSG. Další příklady najdete v článku Přehled [Konfigurace protokolů toku NSG ze šablony Azure Resource Manager](network-watcher-nsg-flow-logging-azure-resource-manager.md).

### <a name="example"></a>Příklad

Následující šablona povoluje protokoly toku pro NSG a následně ukládá protokoly do konkrétního účtu úložiště:

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
> - Název prostředku používá _ParentResource_ChildResource_ formátu. V našem příkladu je nadřazeným prostředkem místní instance služby Azure Network Watcher:
>    - **Formát**: NetworkWatcher_RegionName
>    - **Příklad**: NetworkWatcher_centraluseuap
> - `targetResourceId` je ID prostředku cílového NSG.
> - `storageId` je ID prostředku cílového účtu úložiště.

## <a name="deploy-the-template"></a>Nasazení šablony

V tomto kurzu se předpokládá, že máte existující skupinu prostředků a NSG, na které můžete povolit protokolování toku.

Kteroukoli z ukázkových šablon, které jsou uvedeny v tomto článku, můžete uložit místně, jak *azuredeploy.js*. Aktualizujte hodnoty vlastností tak, aby odkazovaly na platné prostředky v předplatném.

Pokud chcete nasadit šablonu, spusťte v Azure PowerShell následující příkaz:

```azurepowershell-interactive
$context = Get-AzSubscription -SubscriptionId <subscription Id>
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> Tyto příkazy nasadí prostředek do ukázkové skupiny prostředků NetworkWatcherRG a ne do skupiny prostředků, která obsahuje NSG.

## <a name="validate-the-deployment"></a>Ověření nasazení

Máte dvě možnosti, jak zjistit, zda bylo nasazení úspěšné:

- Konzola PowerShellu `ProvisioningState` se zobrazí jako `Succeeded` .
- Pro potvrzení změn navštivte [stránku portálu NSG Flow log](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) .

Pokud došlo k problémům s nasazením, přečtěte si téma [řešení běžných chyb nasazení Azure pomocí Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Prostředky Azure můžete odstranit pomocí režimu úplného nasazení. Pokud chcete odstranit prostředek toků protokolů, zadejte nasazení v režimu úplné bez zahrnutí prostředku, který chcete odstranit. Přečtěte si další informace o [režimu úplného nasazení](../azure-resource-manager/templates/deployment-modes.md#complete-mode).

Protokol toku NSG můžete také zakázat v Azure Portal:

1. Přihlaste se k webu Azure Portal.
1. Vyberte **Všechny služby**. Do pole **Filtr** zadejte sledovací proces **sítě**. Ve výsledcích hledání vyberte **Network Watcher**.
1. V části **protokoly** vyberte **protokoly toku NSG**.
1. V seznamu skupin zabezpečení sítě vyberte NSG, pro které chcete zakázat protokoly toku.
1. V části **nastavení protokolů toku** vyberte **vypnuto**.
1. Vyberte **Uložit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak povolit protokoly toku NSG pomocí šablony ARM. V dalším kroku se dozvíte, jak vizualizovat data toku NSG pomocí jedné z těchto možností:

- [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
- [Open Source nástroje](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
- [Analýza provozu Azure](traffic-analytics.md)
