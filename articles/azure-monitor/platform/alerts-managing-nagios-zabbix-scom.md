---
title: Správa výstrah z System Center Operations Manager, Zabbix a Nagios v Azure Monitor
description: Správa výstrah z System Center Operations Manager, Zabbix a Nagios v Azure Monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.subservice: alerts
ms.openlocfilehash: 5ef03f522fb91302bfdec985f1e3c5cf8138ae97
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77667444"
---
# <a name="manage-alerts-from-system-center-operations-manager-zabbix-and-nagios-in-azure-monitor"></a>Správa výstrah z System Center Operations Manager, Zabbix a Nagios v Azure Monitor

Nyní můžete zobrazit výstrahy z Nagios, Zabbix a System Center Operations Manager v [Azure monitor](https://aka.ms/azure-alerts-overview). Tyto výstrahy přicházejí z integrace se servery Nagios/Zabbix nebo System Center Operations Manager do Log Analytics. 

## <a name="prerequisites"></a>Požadavky
Všechny záznamy v úložišti Log Analytics s typem výstrahy se naimportují do Azure Monitor, takže musíte provést konfiguraci, která je potřeba ke shromáždění těchto záznamů.
1. U upozornění **Nagios** a **Zabbix** [nakonfigurujte tyto servery](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) tak, aby [odesílaly výstrahy](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-alerts-nagios-zabbix?toc=%2Fazure%2Fazure-monitor%2Ftoc.json) Log Analytics.
1. Pro výstrahy **System Center Operations Manager** [Připojte skupinu pro správu Operations Manager k vašemu pracovnímu prostoru Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents). V následujícím seznamu nasaďte řešení [Alert Management](https://docs.microsoft.com/azure/azure-monitor/platform/alert-management-solution) z webu Azure Solutions Marketplace. Po dokončení se všechny výstrahy vytvořené v System Center Operations Manager importují do Log Analytics.

## <a name="view-your-alert-instances"></a>Zobrazení instancí výstrah
Jakmile nakonfigurujete import do Log Analytics, můžete začít zobrazovat instance výstrah z těchto monitorovacích služeb v [Azure monitor](https://aka.ms/azure-alerts-overview). Jakmile jsou v Azure Monitor, můžete [Spravovat instance výstrah](https://aka.ms/managing-alert-instances), [Spravovat pro ně inteligentní skupiny vytvořené pomocí těchto výstrah](https://aka.ms/managing-smart-groups) a [měnit stav výstrah a inteligentních skupin](https://aka.ms/managing-alert-smart-group-states).

> [!NOTE]
>  1. Toto řešení umožňuje zobrazit instance výstrah System Center Operations Manager/Zabbix/Nagios, které se vyvolaly v Azure Monitor. Konfiguraci pravidla výstrahy lze zobrazit nebo upravit pouze v příslušných nástrojích pro monitorování. 
>  1. Všechny instance výstrah aktivovány budou k dispozici v Azure Monitor i v Azure Log Analytics. V současné době neexistuje žádný způsob, jak volit mezi dvěma nebo ingesty jenom specifických výstrah aktivované.
>  1. Všechny výstrahy z System Center Operations Manager, Zabbix a Nagios, mají typ signálu "unknown", protože základní typ telemetrie není dostupný.
>  1. Upozornění Nagios nejsou stavová – například [podmínka monitorování](https://aka.ms/azure-alerts-overview) výstrahy nebude pokračovat od "vyvoláno" na "Vyřešeno". Místo toho se jako samostatné instance výstrah zobrazují jak "Trigger", tak i vyřešené. 

