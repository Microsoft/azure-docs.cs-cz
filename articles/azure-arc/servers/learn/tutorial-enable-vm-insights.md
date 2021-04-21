---
title: Kurz – monitorování hybridního počítače pomocí Azure Monitorch přehledů virtuálních počítačů
description: Naučte se shromažďovat a analyzovat data z hybridního počítače v Azure Monitor.
ms.topic: tutorial
ms.date: 04/21/2021
ms.openlocfilehash: f59ad448440110e2c5e6dd1fa1b2858d9cf42e91
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834259"
---
# <a name="tutorial-monitor-a-hybrid-machine-with-vm-insights"></a>Kurz: monitorování hybridního počítače pomocí přehledů virtuálních počítačů

[Azure monitor](../../../azure-monitor/overview.md) může shromažďovat data přímo z hybridních počítačů do pracovního prostoru Log Analytics a získat tak podrobnou analýzu a korelaci. Obvykle by to mělo za následek instalaci [agenta Log Analytics](../../../azure-monitor/agents/agents-overview.md#log-analytics-agent) na počítači pomocí skriptu, ručně nebo automatizované metody podle standardů správy konfigurace. Servery s podporou ARC nedávno zavedly podporu pro instalaci [rozšíření virtuálního počítače](../manage-vm-extensions.md) agenta Log Analytics a závislostí pro systémy Windows a Linux, což umožňuje službě [VM Insights](../../../azure-monitor/vm/vminsights-overview.md) shromažďovat data z virtuálních počítačů mimo Azure.

V tomto kurzu se dozvíte, jak pomocí zjednodušené sady kroků nakonfigurovat a shromažďovat data z počítačů se systémem Linux nebo Windows, a to díky tomu, že se služba VM Insights po zjednodušené sadě kroků zjednodušuje a trvá kratší dobu.  

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

* Funkce rozšíření virtuálních počítačů jsou k dispozici pouze v seznamu [podporovaných oblastí](../overview.md#supported-regions).

* V části [podporované operační systémy](../../../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) se ujistěte, že je operační systém, který chcete povolit, PODPOROVANÝ službou VM Insights.

* Projděte si požadavky na bránu firewall pro agenta Log Analytics poskytovaného v článku [Přehled agenta Log Analytics](../../../azure-monitor/agents/log-analytics-agent.md#network-requirements). Agent závislosti mapy virtuálních počítačů neodesílá žádná data a nevyžaduje žádné změny bran firewall nebo portů.

## <a name="sign-in-to-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se na [Azure Portal](https://portal.azure.com).

## <a name="enable-vm-insights"></a>Povolit přehledy virtuálních počítačů

1. Spusťte službu Azure ARC v Azure Portal kliknutím na **všechny služby** a pak vyhledáte a vyberete **počítače – Azure ARC**.

    :::image type="content" source="./media/quick-enable-hybrid-vm/search-machines.png" alt-text="Hledat servery s podporou ARC ve všech službách" border="false":::

1. Na stránce **počítače – ARC Azure** vyberte připojený počítač, který jste vytvořili v článku [rychlý Start](quick-enable-hybrid-vm.md) .

1. V levém podokně v části **monitorování** vyberte **přehledy** a pak **Povolit**.

    :::image type="content" source="./media/tutorial-enable-vm-insights/insights-option.png" alt-text="Vybrat možnost Insights z nabídky na levé straně" border="false":::

1. Na stránce pro **registraci Azure monitor Insights** se zobrazí výzva k vytvoření pracovního prostoru. Pro tento kurz nedoporučujeme vybírat existující pracovní prostor Log Analytics, pokud ho už máte. Vyberte výchozí, což je pracovní prostor s jedinečným názvem ve stejné oblasti jako registrovaný připojený počítač. Tento pracovní prostor se vytvoří a nakonfiguruje za vás.

    :::image type="content" source="./media/tutorial-enable-vm-insights/enable-vm-insights.png" alt-text="Povolit stránku s přehledy virtuálních počítačů" border="false":::

1. Při provádění konfigurace se zobrazí stavové zprávy. Tento proces trvá několik minut, než se na připojeném počítači nainstalují rozšíření.

    :::image type="content" source="./media/tutorial-enable-vm-insights/onboard-vminsights-vm-portal-status.png" alt-text="Povolit stavovou zprávu průběhu postupu pro službu VM Insights" border="false":::

    Až se dokončí, zobrazí se zpráva, že počítač byl úspěšně připojen a přehled byl úspěšně nasazen.

## <a name="view-data-collected"></a>Zobrazení shromážděných dat

Po dokončení nasazení a konfigurace vyberte **přehledy** a pak vyberte kartu **výkon** . Na kartě výkon se zobrazuje vybraná skupina čítačů výkonu shromážděných z hostovaného operačního systému vašeho počítače. Posuňte se dolů, abyste zobrazili další čítače a přesunuli jste ukazatel myši na graf a zobrazili průměr a percentily od doby, kdy byla na počítač nainstalovaná rozšíření Log Analytics virtuálního počítače.

:::image type="content" source="./media/tutorial-enable-vm-insights/insights-performance-charts.png" alt-text="Grafy výkonu služby VM Insights pro vybraný počítač" border="false":::

Vyberte **Mapa** , chcete-li otevřít funkci Maps, která zobrazuje procesy běžící v počítači a jejich závislosti. Výběrem **vlastnosti** otevřete podokno vlastností, pokud již není otevřeno.

:::image type="content" source="./media/tutorial-enable-vm-insights/insights-map.png" alt-text="Mapa služby VM Insights pro vybraný počítač" border="false":::

Rozbalí procesy pro váš počítač. Výběrem jednoho z procesů zobrazíte jeho podrobnosti a zvýrazníte jeho závislosti.

Vyberte svůj počítač znovu a pak vyberte **události protokolu**. Zobrazí se seznam tabulek, které jsou uložené v pracovním prostoru Log Analytics pro daný počítač. Tento seznam se liší v závislosti na tom, zda používáte počítač se systémem Windows nebo Linux. Vyberte tabulku **událostí** . Tabulka **událostí** obsahuje všechny události z protokolu událostí systému Windows. Log Analytics se otevře s jednoduchým dotazem, který načte shromážděné položky protokolu událostí.

## <a name="next-steps"></a>Další kroky

Další informace o Azure Monitor najdete v následujícím článku:

> [!div class="nextstepaction"]
> [Přehled služby Azure Monitor](../../../azure-monitor/overview.md)