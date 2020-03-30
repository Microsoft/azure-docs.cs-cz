---
title: Správa výstrah z Operations Manager system center, Zabbix a Nagios v Azure Monitoru
description: Správa výstrah z Operations Manager system center, Zabbix a Nagios v Azure Monitoru
ms.topic: conceptual
ms.date: 09/24/2018
ms.subservice: alerts
ms.openlocfilehash: 5ef03f522fb91302bfdec985f1e3c5cf8138ae97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667444"
---
# <a name="manage-alerts-from-system-center-operations-manager-zabbix-and-nagios-in-azure-monitor"></a>Správa výstrah z Operations Manager system center, Zabbix a Nagios v Azure Monitoru

Nyní můžete zobrazit výstrahy z Nagios, Zabbix a System Center Operations Manager v [Azure Monitor](https://aka.ms/azure-alerts-overview). Tyto výstrahy pocházejí z integrace se servery Nagios/Zabbix nebo System Center Operations Manager do Log Analytics. 

## <a name="prerequisites"></a>Požadavky
Všechny záznamy v úložišti Log Analytics s typem výstrahy se importují do Azure Monitoru, takže je nutné provést konfiguraci, která je vyžadována ke shromažďování těchto záznamů.
1. Pro **výstrahy Nagios** a **Zabbix** [nakonfigurujte tyto servery](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) tak, aby [odesílalo výstrahy](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-alerts-nagios-zabbix?toc=%2Fazure%2Fazure-monitor%2Ftoc.json) do log analytics.
1. U výstrah **nástroje Operations Manager systémového centra** připojte [skupinu pro správu nástroje Operations Manager k pracovnímu prostoru Analýzy protokolů](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents). Poté nasaďte řešení [správy výstrah](https://docs.microsoft.com/azure/azure-monitor/platform/alert-management-solution) z trhu řešení Azure. Po dokončení se všechny výstrahy vytvořené v nástroji Operations Manager system center importují do analýzy protokolů.

## <a name="view-your-alert-instances"></a>Zobrazení instancí výstrah
Po nakonfigurování importu do analýzy protokolů můžete začít zobrazovat instance výstrah z těchto monitorovacích služeb v [Azure Monitoru](https://aka.ms/azure-alerts-overview). Jakmile jsou k dispozici ve službě Azure Monitor, můžete [spravovat instance výstrah](https://aka.ms/managing-alert-instances), spravovat inteligentní [skupiny vytvořené na těchto výstrahách](https://aka.ms/managing-smart-groups) a [změnit stav vašich výstrah a inteligentních skupin](https://aka.ms/managing-alert-smart-group-states).

> [!NOTE]
>  1. Toto řešení umožňuje pouze zobrazit System Center Operations Manager/Zabbix/Nagios vystřelil výstrahy instance v Azure Monitor. Konfiguraci pravidla výstrahy lze zobrazit nebo upravit pouze v příslušných monitorovacích nástrojích. 
>  1. Všechny vypalování instancí výstrah bude k dispozici v Azure Monitor a Azure Log Analytics. V současné době neexistuje žádný způsob, jak vybrat mezi dva nebo ingestovat pouze konkrétní aktivována výstrahy.
>  1. Všechny výstrahy z System Center Operations Manager, Zabbix a Nagios mají typ signálu "Neznámý", protože základní typ telemetrie není k dispozici.
>  1. Nagios výstrahy nejsou stavové – například [stav monitorování](https://aka.ms/azure-alerts-overview) výstrahy nepřejde z "Fired" na "Vyřešeno". Místo toho "Fired" a "Vyřešeno" jsou zobrazeny jako samostatné instance výstrahy. 

