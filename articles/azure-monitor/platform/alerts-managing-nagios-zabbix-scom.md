---
title: Správa upozornění z nástrojů System Center Operations Manager, Zabbix a Nagios ve službě Azure Monitor
description: Správa upozornění z nástrojů System Center Operations Manager, Zabbix a Nagios ve službě Azure Monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.subservice: alerts
ms.openlocfilehash: 367a7020e56db7105da7624c7911ae5b59f1365a
ms.sourcegitcommit: d3c3f2ded72bfcf2f552e635dc4eb4010491eb75
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92558867"
---
# <a name="manage-alerts-from-system-center-operations-manager-zabbix-and-nagios-in-azure-monitor"></a>Správa upozornění z nástrojů System Center Operations Manager, Zabbix a Nagios ve službě Azure Monitor

Nyní můžete zobrazit výstrahy z Nagios, Zabbix a System Center Operations Manager v [Azure monitor](./alerts-overview.md). Tyto výstrahy přicházejí z integrace se servery Nagios/Zabbix nebo System Center Operations Manager do Log Analytics. 

## <a name="prerequisites"></a>Požadavky
Všechny záznamy v úložišti Log Analytics s typem výstrahy se naimportují do Azure Monitor, takže musíte provést konfiguraci, která je potřeba ke shromáždění těchto záznamů.
1. U upozornění **Nagios** a **Zabbix** [nakonfigurujte tyto servery](../learn/quick-collect-linux-computer.md) tak, aby [odesílaly výstrahy](./data-sources-custom-logs.md?toc=/azure/azure-monitor/toc.json) Log Analytics.
1. Pro výstrahy **System Center Operations Manager** [Připojte skupinu pro správu Operations Manager k vašemu pracovnímu prostoru Log Analytics](./om-agents.md). V následujícím seznamu nasaďte řešení [Alert Management](./alert-management-solution.md) z webu Azure Solutions Marketplace. Po dokončení se všechny výstrahy vytvořené v System Center Operations Manager importují do Log Analytics.

## <a name="view-your-alert-instances"></a>Zobrazení instancí výstrah
Jakmile nakonfigurujete import do Log Analytics, můžete začít zobrazovat instance výstrah z těchto monitorovacích služeb v [Azure monitor](./alerts-overview.md). Jakmile jsou v Azure Monitor, můžete [Spravovat instance výstrah](./alerts-managing-alert-instances.md?toc=%252fazure%252fazure-monitor%252ftoc.json), [Spravovat pro ně inteligentní skupiny vytvořené pomocí těchto výstrah](./alerts-managing-smart-groups.md?toc=%252fazure%252fazure-monitor%252ftoc.json) a [měnit stav výstrah a inteligentních skupin](./alerts-managing-alert-states.md?toc=%252fazure%252fazure-monitor%252ftoc.json).

> [!NOTE]
>  1. Toto řešení umožňuje zobrazit instance výstrah System Center Operations Manager/Zabbix/Nagios, které se vyvolaly v Azure Monitor. Konfiguraci pravidla výstrahy lze zobrazit nebo upravit pouze v příslušných nástrojích pro monitorování. 
>  1. Všechny instance výstrah aktivovány budou k dispozici v Azure Monitor i v Azure Log Analytics. V současné době neexistuje žádný způsob, jak volit mezi dvěma nebo ingesty jenom specifických výstrah aktivované.
>  1. Všechny výstrahy z System Center Operations Manager, Zabbix a Nagios, mají typ signálu "unknown", protože základní typ telemetrie není dostupný.
>  1. Upozornění Nagios nejsou stavová – například [podmínka monitorování](./alerts-overview.md) výstrahy nebude pokračovat od "vyvoláno" na "Vyřešeno". Místo toho se jako samostatné instance výstrah zobrazují jak "Trigger", tak i vyřešené.