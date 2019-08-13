---
title: Kurz – Monitorování protokolů a metrik brány Azure Firewall
description: V tomto kurzu se dozvíte, jak povolit a spravovat protokoly a metriky brány Azure Firewall.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 10/24/2018
ms.author: victorh
ms.openlocfilehash: 393441e7ff620f3795e42c2cb376f99f8763f25b
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2019
ms.locfileid: "60193155"
---
# <a name="tutorial-monitor-azure-firewall-logs-and-metrics"></a>Kurz: Monitorovat protokoly Azure Firewall a metriky

Bránu Azure Firewall můžete monitorovat pomocí protokolů brány firewall. K auditu operací na prostředcích brány Azure Firewall můžete také použít protokoly aktivit. Pomocí metrik můžete zobrazit čítače výkonu na portálu. 

Některé z těchto protokolů jsou přístupné z webu Azure Portal. Protokoly je možné odeslat do [Azure monitor protokolů](../azure-monitor/insights/azure-networking-analytics.md), úložiště a Event Hubs a analyzovat v protokolech Azure monitor nebo pomocí různých nástrojů, jako je Excel a Power BI.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Povolit protokolování prostřednictvím webu Azure Portal
> * Povolit protokolování prostřednictvím PowerShellu
> * Zobrazit a analyzovat protokol aktivit
> * Zobrazení a analyzování protokolů pravidel sítě a aplikace
> * Zobrazit metriky


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

Před zahájením tohoto kurzu si v tématu o [protokolech a metrikách brány Firewall Azure](logs-and-metrics.md) přečtěte přehledné informace o diagnostických protokolech a metrikách, které jsou k dispozici pro bránu Azure Firewall.


## <a name="enable-diagnostic-logging-through-the-azure-portal"></a>Povolení diagnostického protokolování prostřednictvím webu Azure Portal

Než se data v protokolech po dokončení tohoto procesu zapnutí protokolování diagnostiky zobrazí, může to trvat několik minut. Pokud nic nevidíte, zkuste to znovu po několika minutách.

1. Na webu Azure Portal otevřete skupinu prostředků brány firewall a klikněte na bránu firewall.
2. V části **monitorování**klikněte na **nastavení diagnostiky**.

   Pro bránu Azure Firewall jsou k dispozici dva protokoly pro konkrétní služby:

   * AzureFirewallApplicationRule
   * AzureFirewallNetworkRule

3. Pokud chcete začít shromažďovat data, klikněte na **Zapnout diagnostiku**.
4. Stránka **Nastavení diagnostiky** obsahuje nastavení diagnostických protokolů. 
5. V tomto příkladu protokoly Azure Monitor ukládá do protokolů, takže jako název zadáte **Log Analytics pro firewall** .
6. Pracovní prostor nakonfigurujete kliknutím na **Poslat do Log Analytics**. Diagnostické protokoly můžete ukládat také pomocí služby center událostí a účtu úložiště.
7. V části **Log Analytics** klikněte na **Konfigurovat**.
8. Na stránce Pracovní prostory Log Analytics klikněte na **Vytvořit nový pracovní prostor**.
9. Na stránce **Pracovní prostor služby Log Analytics** zadejte pro **Pracovní prostor služby Log Analytics** nový název **firewall-oms**.
10. Vyberte předplatné, použijte existující skupinu prostředků brány firewall (**Test-FW-RG**), jako umístění vyberte **USA – východ** a u cenové úrovně zvolte **Free**.
11. Klikněte na **OK**.
   ![Spuštění procesu konfigurace][1] Pracovní prostory OMS se teď označují jako pracovní prostory Log Analytics.  
12. V části **Protokol** začněte shromažďovat protokoly pravidel aplikace a sítě kliknutím na **AzureFirewallApplicationRule** a **AzureFirewallNetworkRule**.
   ![Uložení nastavení diagnostiky][2]
13. Klikněte na **Uložit**.

## <a name="enable-logging-with-powershell"></a>Povolení protokolování prostřednictvím PowerShellu

Protokolování aktivit je u každého prostředku Správce prostředků povolené automaticky. Abyste mohli začít shromažďovat data dostupná prostřednictvím těchto protokolů, musíte zapnout protokolování diagnostiky.

Protokolování diagnostiky zapnete následovně:

1. Poznamenejte si ID prostředku účtu úložiště, kam se data protokolu ukládají. Tato hodnota má formát: */subscriptions/\<ID předplatného\>/resourceGroups/\<název skupiny prostředků\>/providers/Microsoft.Storage/storageAccounts/\<název účtu úložiště\>* .

   Můžete použít libovolný účet úložiště z vašeho předplatného. Tuto informaci najdete pomocí webu Azure Portal na stránce **vlastností** prostředku.

2. Poznamenejte si ID prostředku brány Firewall, který má zapnuté protokolování. Tato hodnota má formát: */subscriptions/\<ID předplatného\>/resourceGroups/\<název skupiny prostředků\>/providers/Microsoft.Network/azureFirewalls/\<název brány firewall\>* .

   Tuto informaci najdete pomocí webu Azure Portal.

3. Protokolování diagnostiky zapnete pomocí následující rutiny PowerShellu:

    ```powershell
    Set-AzDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name> `
   -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> `
   -Enabled $true     
    ```
    
> [!TIP] 
>Diagnostické protokoly vlastí účet úložiště nevyžadují. Za používání úložiště k protokolování přístupu a výkonu se účtují poplatky.

## <a name="view-and-analyze-the-activity-log"></a>Zobrazení a analýza protokolu aktivit

Data protokolu aktivit si můžete zobrazit použitím jedné z následujících metod:

* **Nástroje Azure**: Načtěte informace z protokolu aktivit prostřednictvím Azure PowerShell, Azure CLI, Azure REST API nebo Azure Portal. Podrobné pokyny k jednotlivým metodám najdete v článku o [operacích s protokoly aktivit ve Správci prostředků](../azure-resource-manager/resource-group-audit.md).
* **Power BI**: Pokud ještě nemáte účet [Power BI](https://powerbi.microsoft.com/pricing) , můžete si ho vyzkoušet zdarma. Díky [balíčku obsahu protokoly aktivit Azure pro Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/) můžete svá data analyzovat pomocí předkonfigurovaných řídicích panelů, které můžete použít okamžitě nebo si je upravit.

## <a name="view-and-analyze-the-network-and-application-rule-logs"></a>Zobrazení a analyzování protokolů pravidel sítě a aplikace

[Protokoly Azure monitor](../azure-monitor/insights/azure-networking-analytics.md) shromáždí údaje o čítačích a souborech protokolu událostí. Zahrnuje vizualizace a výkonné vyhledávací funkce k analýze protokolů.

Ukázkové dotazy Azure Firewall Log Analytics najdete v tématu [Azure firewall ukázek Log Analytics](log-analytics-samples.md).

Můžete se také připojit k účtu úložiště a načíst položky protokolu JSON s protokoly přístupu a výkonu. Po stažení souborů JSON je můžete převést do formátu CSV a zobrazit si je v Excelu, Power BI nebo jiném nástroji s vizualizací dat.

> [!TIP]
> Pokud znáte Visual Studio a máte představu, jak u konstant a proměnných v jazyce C# měnit hodnoty, můžete použít [nástroje pro převedení protokolů](https://github.com/Azure-Samples/networking-dotnet-log-converter), které jsou k dispozici na GitHubu.

## <a name="view-metrics"></a>Zobrazit metriky
Přejděte k bráně Azure Firewall a v části **Sledování** klikněte na **Metriky**. Chcete-li zobrazit dostupné hodnoty, vyberte rozevírací seznam **METRIKA**.

## <a name="next-steps"></a>Další postup

Teď, když jste nakonfigurovali bránu firewall pro shromažďování protokolů, můžete prozkoumat protokoly Azure Monitor a zobrazit vaše data.

> [!div class="nextstepaction"]
> [Řešení monitorování sítě v protokolech Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md)

[1]: ./media/tutorial-diagnostics/figure1.png
[2]: ./media/tutorial-diagnostics/figure2.png
