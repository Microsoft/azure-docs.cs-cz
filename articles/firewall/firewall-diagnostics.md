---
title: Monitorování protokolů a metrik Azure Firewallu
description: V tomto článku se dozvíte, jak povolit a spravovat protokoly Azure Firewall a metriky.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 11/04/2020
ms.author: victorh
ms.openlocfilehash: 52c6ef9edfc42bf1ad3b3279e0fa4e19b4cf502c
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98788260"
---
# <a name="monitor-azure-firewall-logs-and-metrics"></a>Monitorování protokolů a metrik Azure Firewallu

Bránu Azure Firewall můžete monitorovat pomocí protokolů brány firewall. K auditu operací na prostředcích brány Azure Firewall můžete také použít protokoly aktivit. Pomocí metrik můžete zobrazit čítače výkonu na portálu.

Některé z těchto protokolů jsou přístupné z webu Azure Portal. Protokoly je možné odeslat do [Azure monitor protokolů](../azure-monitor/insights/azure-networking-analytics.md), úložiště a Event Hubs a analyzovat v protokolech Azure monitor nebo pomocí různých nástrojů, jako je Excel a Power BI.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

Než začnete, měli byste si přečíst [Azure firewall protokoly a metriky](logs-and-metrics.md) pro přehled diagnostických protokolů a metrik, které jsou k dispozici pro Azure firewall.

## <a name="enable-diagnostic-logging-through-the-azure-portal"></a>Povolení diagnostického protokolování prostřednictvím webu Azure Portal

Než se data v protokolech po dokončení tohoto procesu zapnutí protokolování diagnostiky zobrazí, může to trvat několik minut. Pokud nic nevidíte, zkuste to znovu po několika minutách.

1. V Azure Portal otevřete skupinu prostředků brány firewall a vyberte bránu firewall.
2. V části **Monitorování** vyberte **Nastavení diagnostiky**.

   Pro Azure Firewall jsou k dispozici čtyři protokoly pro konkrétní služby:

   * AzureFirewallApplicationRule
   * AzureFirewallNetworkRule
   * AzureFirewallThreatIntelLog
   * AzureFirewallDnsProxy


3. Vyberte **Přidat nastavení diagnostiky**. Stránka **Nastavení diagnostiky** obsahuje nastavení diagnostických protokolů.
5. V tomto příkladu protokoly Azure Monitor ukládá do protokolů, takže jako název zadáte **Log Analytics pro firewall** .
6. V části **protokol** vyberte **AzureFirewallApplicationRule**, **AzureFirewallNetworkRule**, **AzureFirewallThreatIntelLog** a **AzureFirewallDnsProxy** , aby se protokoly shromáždily.
7. Vyberte **Odeslat pro Log Analytics** ke konfiguraci pracovního prostoru.
8. Vyberte své předplatné.
9. Vyberte **Uložit**.

## <a name="enable-diagnostic-logging-by-using-powershell"></a>Povolení protokolování diagnostiky pomocí PowerShellu

Protokolování aktivit je u každého prostředku Správce prostředků povolené automaticky. Abyste mohli začít shromažďovat data dostupná prostřednictvím těchto protokolů, musíte zapnout protokolování diagnostiky.

Chcete-li povolit protokolování diagnostiky pomocí prostředí PowerShell, použijte následující postup:

1. Poznamenejte si ID prostředku pracovního prostoru Log Analytics, kde jsou uložená data protokolu. Tato hodnota je ve formátu: `/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/microsoft.operationalinsights/workspaces/<workspace name>` .

   V rámci svého předplatného můžete použít libovolný pracovní prostor. Tuto informaci najdete pomocí webu Azure Portal Informace najdete na stránce **vlastností** prostředku.

2. Poznamenejte si ID prostředku brány Firewall, který má zapnuté protokolování. Tato hodnota je ve formátu: `/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name>` .

   Tuto informaci najdete pomocí webu Azure Portal.

3. Povolte protokolování diagnostiky pro všechny protokoly a metriky pomocí následující rutiny PowerShellu:

   ```powershell
   $diagSettings = @{
      Name = 'toLogAnalytics'
      ResourceId = '/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name>'
      WorkspaceId = '/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/microsoft.operationalinsights/workspaces/<workspace name>'
      Enabled = $true
   }
   Set-AzDiagnosticSetting  @diagSettings 
   ```

## <a name="enable-diagnostic-logging-by-using-the-azure-cli"></a>Povolení protokolování diagnostiky pomocí Azure CLI

Protokolování aktivit je u každého prostředku Správce prostředků povolené automaticky. Abyste mohli začít shromažďovat data dostupná prostřednictvím těchto protokolů, musíte zapnout protokolování diagnostiky.

Pokud chcete povolit protokolování diagnostiky pomocí Azure CLI, použijte následující postup:

1. Poznamenejte si ID prostředku pracovního prostoru Log Analytics, kde jsou uložená data protokolu. Tato hodnota je ve formátu: `/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name>` .

   V rámci svého předplatného můžete použít libovolný pracovní prostor. Tuto informaci najdete pomocí webu Azure Portal Informace najdete na stránce **vlastností** prostředku.

2. Poznamenejte si ID prostředku brány Firewall, který má zapnuté protokolování. Tato hodnota je ve formátu: `/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name>` .

   Tuto informaci najdete pomocí webu Azure Portal.

3. Povolte protokolování diagnostiky pro všechny protokoly a metriky pomocí následujícího příkazu Azure CLI:

   ```azurecli-interactive
   az monitor diagnostic-settings create -n 'toLogAnalytics'
      --resource '/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name>'
      --workspace '/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/microsoft.operationalinsights/workspaces/<workspace name>'
      --logs '[{\"category\":\"AzureFirewallApplicationRule\",\"Enabled\":true}, {\"category\":\"AzureFirewallNetworkRule\",\"Enabled\":true}, {\"category\":\"AzureFirewallDnsProxy\",\"Enabled\":true}]' 
      --metrics '[{\"category\": \"AllMetrics\",\"enabled\": true}]'
   ```

## <a name="view-and-analyze-the-activity-log"></a>Zobrazení a analýza protokolu aktivit

Data protokolu aktivit si můžete zobrazit použitím jedné z následujících metod:

* **Nástroje Azure**: Načtěte informace z protokolu aktivit prostřednictvím Azure PowerShellu, Azure CLI, rozhraní Azure REST API nebo webu Azure Portal. Podrobné pokyny k jednotlivým metodám najdete v článku o [operacích s protokoly aktivit ve Správci prostředků](../azure-resource-manager/management/view-activity-logs.md).
* **Power BI**: Pokud ještě účet [Power BI](https://powerbi.microsoft.com/pricing) nemáte, můžete ho vyzkoušet zdarma. Díky [balíčku obsahu protokoly aktivit Azure pro Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/) můžete svá data analyzovat pomocí předkonfigurovaných řídicích panelů, které můžete použít okamžitě nebo si je upravit.
* **Azure Sentinel**: můžete připojit protokoly Azure firewall ke službě Azure Sentinel a umožnit vám zobrazovat data protokolu v sešitech, použít je k vytvoření vlastních výstrah a začlenit je do lepšího šetření. Konektor dat Azure Firewall v Azure Sentinel je aktuálně ve verzi Public Preview. Další informace najdete v tématu [připojení dat z Azure firewall](../sentinel/connect-azure-firewall.md).

## <a name="view-and-analyze-the-network-and-application-rule-logs"></a>Zobrazení a analyzování protokolů pravidel sítě a aplikace

[Protokoly Azure monitor](../azure-monitor/insights/azure-networking-analytics.md) shromáždí údaje o čítačích a souborech protokolu událostí. Zahrnuje vizualizace a výkonné vyhledávací funkce k analýze protokolů.

Ukázkové dotazy Azure Firewall Log Analytics najdete v tématu [Azure firewall ukázek Log Analytics](./firewall-workbook.md).

[Azure firewall sešit](firewall-workbook.md) nabízí flexibilní plátno pro Azure firewall analýzu dat. Můžete ho použít k vytvoření bohatých vizuálních sestav v rámci Azure Portal. Můžete klepnout na několik bran firewall nasazených v rámci Azure a kombinovat je do sjednocených interaktivních prostředí.

Můžete se také připojit k účtu úložiště a načíst položky protokolu JSON s protokoly přístupu a výkonu. Po stažení souborů JSON je můžete převést do formátu CSV a zobrazit si je v Excelu, Power BI nebo jiném nástroji s vizualizací dat.

> [!TIP]
> Pokud znáte Visual Studio a máte představu, jak u konstant a proměnných v jazyce C# měnit hodnoty, můžete použít [nástroje pro převedení protokolů](https://github.com/Azure-Samples/networking-dotnet-log-converter), které jsou k dispozici na GitHubu.

## <a name="view-metrics"></a>Zobrazit metriky
Přejděte k Azure Firewall v části **monitorování** vyberte **metriky**. Chcete-li zobrazit dostupné hodnoty, vyberte rozevírací seznam **METRIKA**.

## <a name="next-steps"></a>Další kroky

Teď, když jste nakonfigurovali bránu firewall pro shromažďování protokolů, můžete prozkoumat protokoly Azure Monitor a zobrazit vaše data.

[Monitorování protokolů pomocí Azure Firewallho sešitu](firewall-workbook.md)

[Řešení monitorování sítě v protokolech Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md)