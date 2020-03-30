---
title: Konfigurace zdrojů dat agenta v Azure Monitoru | Dokumenty společnosti Microsoft
description: Zdroje dat definují data protokolu, která Azure Monitor shromažďuje od agentů a dalších připojených zdrojů.  Tento článek popisuje koncept, jak Azure Monitor používá zdroje dat, vysvětluje podrobnosti o tom, jak je nakonfigurovat a poskytuje souhrn různých dostupných zdrojů dat.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: aec3fe2386ce916c556f6da295a8554fff140259
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249097"
---
# <a name="agent-data-sources-in-azure-monitor"></a>Zdroje dat agenta ve službě Azure Monitor
Data, která Azure Monitor shromažďuje od agentů, jsou definována zdroji dat, které nakonfigurujete.  Data z agentů jsou uložena jako [data protokolu](data-platform-logs.md) se sadou záznamů.  Každý zdroj dat vytvoří záznamy určitého typu s každým typem, který má vlastní sadu vlastností.

![Shromažďování dat protokolu](media/agent-data-sources/overview.png)

## <a name="summary-of-data-sources"></a>Souhrn zdrojů dat
V následující tabulce jsou uvedeny zdroje dat agenta, které jsou aktuálně dostupné ve službě Azure Monitor.  Každý z nich má odkaz na samostatný článek poskytující podrobnosti pro tento zdroj dat.   Poskytuje také informace o jejich způsobu a četnosti sběru. 


| Zdroj dat | Platforma | Agent analýzy protokolů | Agent Operations Manageru | Úložiště Azure | Vyžaduje se provozní manažer? | Data agenta Operations Manager odeslaná prostřednictvím skupiny pro správu | Četnost shromažďování dat |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Vlastní protokoly](data-sources-custom-logs.md) | Windows |&#8226; |  | |  |  | při příjezdu |
| [Vlastní protokoly](data-sources-custom-logs.md) | Linux   |&#8226; |  | |  |  | při příjezdu |
| [Protokoly iis](data-sources-iis-logs.md) | Windows |&#8226; |&#8226; |&#8226; |  |  |závisí na nastavení efektu přechodu souboru protokolu |
| [Čítače výkonu](data-sources-performance-counters.md) | Windows |&#8226; |&#8226; |  |  |  |podle plánu, minimálně 10 sekund |
| [Čítače výkonu](data-sources-performance-counters.md) | Linux |&#8226; |  |  |  |  |podle plánu, minimálně 10 sekund |
| [Syslog](data-sources-syslog.md) | Linux |&#8226; |  |  |  |  |z úložiště Azure: 10 minut; od zástupce: při příjezdu |
| [Protokoly událostí systému Windows](data-sources-windows-events.md) |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; | při příjezdu |


## <a name="configuring-data-sources"></a>Konfigurace zdrojů dat
Zdroje dat nakonfigurujete z nabídky **Data** v **rozšířeném nastavení** pracovního prostoru.  Libovolná konfigurace je dodávána do všech připojených zdrojů ve vašem pracovním prostoru.  V současné době nelze vyloučit žádné agenty z této konfigurace.

![Konfigurace událostí systému Windows](media/agent-data-sources/configure-events.png)

1. Na portálu Azure vyberte **pracovní prostory Analýzy protokolů** > pracovní prostor > **upřesnit nastavení**.
2. Vyberte **možnost Data**.
3. Klikněte na zdroj dat, který chcete konfigurovat.
4. Podrobnosti o jejich konfiguraci naleznete v dokumentaci ke každému zdroji dat ve výše uvedené tabulce.


## <a name="data-collection"></a>Shromažďování dat
Konfigurace zdroje dat se doručují agentům, kteří jsou přímo připojení k Azure Monitorběhem několika minut.  Zadaná data se shromažďují od agenta a doručují přímo do Azure Monitoru v intervalech specifických pro každý zdroj dat.  Viz dokumentace pro každý zdroj dat pro tyto specifika.

U agentů nástroje Operations Manager systémového centra v připojené skupině pro správu jsou konfigurace zdrojů dat převedeny do sad Management Pack a ve výchozím nastavení doručovány do skupiny pro správu každých 5 minut.  Agent stáhne sadu Management Pack jako všechny ostatní a shromáždí zadaná data. V závislosti na zdroji dat budou data odeslána na server pro správu, který předává data do Azure Monitoru, nebo agent odešle data do Služby Azure Monitor, aniž by procházel serverem pro správu. Podrobnosti najdete [v podrobnostech o shromažďování dat pro řešení monitorování v Azure.](../insights/solutions-inventory.md)  Můžete si přečíst podrobnosti o připojení Operations Manager a Azure Monitor a úpravy frekvence, která konfigurace je dodáván na [konfigurovat integraci s System Center Operations Manager](om-agents.md).

Pokud se agent nemůže připojit k Azure Monitoru nebo Operations Manageru, bude nadále shromažďovat data, která bude dodávat, když naváže připojení.  Data mohou být ztracena, pokud množství dat dosáhne maximální velikost mezipaměti pro klienta nebo pokud agent není schopen navázat připojení do 24 hodin.

## <a name="log-records"></a>Protokolovat záznamy
Všechna data protokolu shromážděná službou Azure Monitor se ukládají v pracovním prostoru jako záznamy.  Záznamy shromážděné různými zdroji dat budou mít vlastní sadu vlastností a budou identifikovány jejich **Type** vlastností.  Podrobnosti o jednotlivých typech záznamů naleznete v dokumentaci ke každému zdroji dat a řešení.

## <a name="next-steps"></a>Další kroky
* Přečtěte si o [řešeních monitorování,](../insights/solutions.md) která do Azure Monitoru přidávají funkce a také shromažďují data do pracovního prostoru.
* Další informace o [dotazech protokolu](../log-query/log-query-overview.md) k analýze dat shromážděných ze zdrojů dat a řešení monitorování.  
* Nakonfigurujte [výstrahy](alerts-overview.md) tak, aby vás proaktivně upozorňovaly na důležitá data shromážděná ze zdrojů dat a řešení monitorování.
