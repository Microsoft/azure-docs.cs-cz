---
title: Konfigurace zdroje dat v Azure Log Analytics | Dokumentace Microsoftu
description: Zdroje dat definují data, že připojení Log Analytics shromažďuje z agentů a dalších zdrojů.  Tento článek popisuje koncept jak Log Analytics používá zdroje dat, vysvětluje podrobnosti o tom, jak je nakonfigurovat a poskytuje přehled různých zdrojů dat. k dispozici.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 67710115-c861-40f8-a377-57c7fa6909b4
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/26/2018
ms.author: bwren
ms.component: ''
ms.openlocfilehash: 8dad4c11ac309d959675d525fbeb48fe385cf4a5
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52336802"
---
# <a name="data-sources-in-log-analytics"></a>Zdroje dat v Log Analytics
Log Analytics shromažďuje data z vašeho připojené zdroje a ukládá ho do pracovního prostoru Log Analytics.  Je definována data, která se shromažďují ze všech zdrojů dat, který nakonfigurujete.  Data ve službě Log Analytics se ukládají jako sady záznamů.  Každý zdroj dat vytvoří záznamy určitého typu s jednotlivými typu s vlastní sadu vlastností.

![Shromažďování dat analýzy protokolů](./media/agent-data-sources/overview.png)

Zdroje dat se liší od [řešení pro správu](../../azure-monitor/insights/solutions.md), což také shromažďovat data z připojené zdroje a vytvářet záznamy ve službě Log Analytics.  Kromě shromažďování dat, obvykle zahrnují řešení prohledávání protokolů a zobrazení, které vám pomůžou analyzovat operace určitá aplikace nebo služby.


## <a name="summary-of-data-sources"></a>Přehled zdrojů dat
Následující tabulka obsahuje seznam zdrojů dat, které jsou aktuálně k dispozici ve službě Log Analytics.  Každý odkaz na věnovaný samostatný článek poskytuje podrobnosti pro tento zdroj dat má.   Poskytuje také informace o jejich metoda a četnost shromažďování dat do Log Analytics.  K identifikaci různých řešení, které jsou k dispozici a lépe porozumět požadavkům datového toku a připojení pro jiné řešení, můžete použít informace v tomto článku. Vysvětlení sloupců, naleznete v tématu [podrobnosti shromažďování dat pro řešení pro správu v Azure](../../azure-monitor/insights/solutions-inventory.md).


| Zdroj dat | Platforma | Agent sledování Microsoft | Agent nástroje Operations Manager | Úložiště Azure | Nástroj Operations Manager vyžaduje? | Dat agenta nástroje Operations Manager odeslaná pomocí skupiny pro správu | Četnost shromažďování dat |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| [Vlastní protokoly](data-sources-custom-logs.md) | Windows |&#8226; |  | |  |  | Při doručení |
| [Vlastní protokoly](data-sources-custom-logs.md) | Linux   |&#8226; |  | |  |  | Při doručení |
| [Protokoly IIS](data-sources-iis-logs.md) | Windows |&#8226; |&#8226; |&#8226; |  |  |závisí na nastavení změna souboru protokolu |
| [Čítače výkonu](data-sources-performance-counters.md) | Windows |&#8226; |&#8226; |  |  |  |podle plánu, minimálně 10 sekund |
| [Čítače výkonu](data-sources-performance-counters.md) | Linux |&#8226; |  |  |  |  |podle plánu, minimálně 10 sekund |
| [Syslog](data-sources-syslog.md) | Linux |&#8226; |  |  |  |  |ze služby Azure storage: 10 minut; z agenta: při doručení |
| [Protokoly událostí Windows](data-sources-windows-events.md) |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; | Při doručení |


## <a name="configuring-data-sources"></a>Konfigurace zdroje dat
Konfigurace zdroje dat z **Data** nabídky ve službě Log Analytics **Upřesnit nastavení**.  Všechny konfigurace se doručí do všech připojených zdrojů ve vašem pracovním prostoru.  Z této konfigurace nelze aktuálně vyloučit všechny agenty.

![Konfigurace událostí Windows](./media/agent-data-sources/configure-events.png)

1. Na webu Azure Portal, vyberte **Log Analytics** > váš pracovní prostor > **Upřesnit nastavení**.
2. Vyberte **Data**.
3. Klikněte na zdroj dat, který chcete konfigurovat.
4. Použijte odkaz na dokumentaci pro jednotlivé zdroje dat v tabulce výše uvedené podrobnosti o jejich konfiguraci.


## <a name="data-collection"></a>Shromažďování dat
Konfigurace zdroje dat jsou doručeny agentům, které jsou přímo připojené ke službě Log Analytics během několika minut.  Je zadaná data shromážděná z agenta a doručované přímo do Log Analytics v intervalech, které jsou specifické pro každý zdroj dat.  Naleznete v dokumentaci pro jednotlivé zdroje dat pro tyto konkrétní.

Pro agenty System Center Operations Manager v připojené skupině pro správu konfigurace zdroje dat jsou přeloženy do sady management Pack a doručit do skupiny pro správu každých 5 minut, ve výchozím nastavení.  Agent soubory ke stažení sady management pack jako u všech ostatních a shromažďuje zadaná data. V závislosti na zdroji dat data budou že buď odeslány na server pro správu, který předává data do Log Analytics nebo bude agent posílat data do Log Analytics bez nutnosti kontaktovat server pro správu. Zobrazit [podrobnosti shromažďování dat pro řešení pro správu v Azure](../../azure-monitor/insights/solutions-inventory.md) podrobnosti.  Informace o najdete podrobnosti o připojení nástroje Operations Manager a Log Analytics a úprava frekvence tuto konfiguraci se doručí na [konfiguraci integrace se sadou System Center Operations Manager](../../log-analytics/log-analytics-om-agents.md).

Pokud je agent nemůže připojit k Log Analytics nebo Operations Manager, bude nadále shromažďovat data, která bude poskytovat navazuje připojení.  Data mohou být ztraceny, pokud objem dat dosáhne maximální velikost mezipaměti klienta, nebo pokud agent není schopen navázat připojení do 24 hodin.

## <a name="log-analytics-records"></a>Záznamy služby Log Analytics
Všechna data shromážděná službou Log Analytics je v pracovním prostoru uloží jako záznamy.  Záznamy shromážděné z různých zdrojů dat budou mít své vlastní sadu vlastností a identifikovat podle jejich **typ** vlastnost.  Najdete v dokumentaci pro jednotlivé zdroje dat a řešení pro podrobnosti pro každý typ záznamu.

## <a name="next-steps"></a>Další postup
* Další informace o [řešení](../../azure-monitor/insights/solutions.md) , které doplňují do Log Analytics a také shromažďovat data do pracovního prostoru.
* Další informace o [prohledávání protokolů](../../log-analytics/log-analytics-queries.md) analyzovat data shromážděná ze zdrojů dat a jejich řešení.  
* Konfigurace [výstrahy](../../monitoring-and-diagnostics/monitoring-overview-alerts.md) můžete zajistit aktivní upozorňování kritických dat shromážděných ze zdroje dat a řešení.
