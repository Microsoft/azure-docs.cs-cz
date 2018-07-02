---
title: Konfigurace zdroje dat v Azure Log Analytics | Microsoft Docs
description: Zdroje dat zadat data, analýzy protokolů shromažďuje z agentů a dalších připojené zdroje.  Tento článek popisuje základní informace o tom, jak analýzy protokolů používá zdroje dat, vysvětluje podrobnosti o tom, jak je nakonfigurovat a poskytuje k dispozici různé datové zdroje.
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
ms.component: na
ms.openlocfilehash: 9a43774a5327536ae4fa1346ae933739ea629771
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37130080"
---
# <a name="data-sources-in-log-analytics"></a>Zdroje dat v analýzy protokolů
Analýzy protokolů shromažďuje data z vaší připojené zdroje a ukládá je v pracovní prostor analýzy protokolů.  Data, která se shromažďují z každé je definována zdroje dat, který nakonfigurujete.  Data v analýzy protokolů se ukládají jako sada záznamů.  Všechny zdroje dat, vytvoří záznamy určitého typu pomocí jednotlivých typů má svou vlastní sadu vlastností.

![Přihlaste se shromažďování dat Analytics](./media/log-analytics-data-sources/overview.png)

Zdroje dat se liší od [řešení pro správu](log-analytics-add-solutions.md), který také shromažďovat data z připojené zdroje a vytvářet záznamy v analýzy protokolů.  Kromě shromažďování dat o řešení obvykle zahrnují protokolu vyhledávání a zobrazení, které vám pomůžou analyzovat operaci určitá aplikace nebo služby.


## <a name="summary-of-data-sources"></a>Souhrn datových zdrojů
Následující tabulka uvádí zdroje dat, které jsou aktuálně k dispozici v analýzy protokolů.  Každý má odkaz na samostatný článek poskytuje podrobnosti pro tento zdroj dat.   Také obsahuje informace o jejich metoda a frekvenci shromažďování dat do analýzy protokolů.  K identifikaci různých řešení, které jsou k dispozici a lépe porozumět požadavkům připojení a toku dat pro řešení pro správu jiný, můžete použít informace v tomto článku. Vysvětlení sloupců, najdete v části [podrobnosti kolekce dat pro řešení pro správu v Azure](../monitoring/monitoring-solutions-inventory.md).


| Zdroj dat | Platforma | Agent monitorování Microsoft | Agent nástroje Operations Manager | Úložiště Azure | Nástroj Operations Manager vyžaduje? | Dat agenta nástroje Operations Manager odeslána prostřednictvím skupiny pro správu | Četnost shromažďování dat |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| [Vlastní protokoly](log-analytics-data-sources-custom-logs.md) | Windows |&#8226; |  | |  |  | v případě přijetí |
| [Vlastní protokoly](log-analytics-data-sources-custom-logs.md) | Linux   |&#8226; |  | |  |  | v případě přijetí |
| [Protokoly IIS](log-analytics-data-sources-iis-logs.md) | Windows |&#8226; |&#8226; |&#8226; |  |  |závisí na nastavení změna souboru protokolu |
| [Čítače výkonu](log-analytics-data-sources-performance-counters.md) | Windows |&#8226; |&#8226; |  |  |  |podle plánu, minimálně 10 sekund. |
| [Čítače výkonu](log-analytics-data-sources-performance-counters.md) | Linux |&#8226; |  |  |  |  |podle plánu, minimálně 10 sekund. |
| [Syslog](log-analytics-data-sources-syslog.md) | Linux |&#8226; |  |  |  |  |ze služby Azure storage: 10 minut; z agenta: na přijetí |
| [Protokoly událostí systému Windows](log-analytics-data-sources-windows-events.md) |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; | v případě přijetí |


## <a name="configuring-data-sources"></a>Konfigurace zdroje dat
Konfigurace zdroje dat z **Data** nabídky v analýzy protokolů **Upřesnit nastavení**.  Jakákoli konfigurace se doručí na všech připojených zdrojů v pracovním prostoru.  Veškeré agenty nelze aktuálně vyloučit z této konfigurace.

![Konfigurace události systému Windows](./media/log-analytics-data-sources/configure-events.png)

1. Na portálu Azure vyberte **analýzy protokolů** > pracovního prostoru > **Upřesnit nastavení**.
2. Vyberte **Data**.
3. Klikněte na zdroj dat, který chcete nakonfigurovat.
4. Použijte odkaz na dokumentaci pro každý zdroj dat v tabulce Podrobnosti na jejich konfiguraci.


## <a name="data-collection"></a>Shromažďování dat
Konfigurace zdroje dat se dodávají s agenty, které jsou připojeny přímo k Log Analytics během několika minut.  Zadaná data se shromažďují z agenta a doručeny přímo k Log Analytics v intervalech, které jsou specifické pro každý zdroj dat.  Najdete v dokumentaci pro každý zdroj dat pro tyto konkrétní.

Konfigurace zdroje dat pro agenty nástroje System Center Operations Manager v připojené skupiny pro správu, jsou přeložit na sady management Pack a doručit do skupiny pro správu každých 5 minut, ve výchozím nastavení.  Agent soubory ke stažení sady management pack jako libovolný jiný a shromažďuje zadaná data. V závislosti na zdroji dat budou data, že buď odeslána na server pro správu, který předává data k analýze protokolů nebo bude agent posílat data k analýze protokolů bez průchodu přes server pro správu. Odkazovat na [podrobnosti kolekce dat](log-analytics-add-solutions.md#data-collection-details) podrobnosti.  Další informace o podrobnosti o připojení nástroje Operations Manager a analýzy protokolů a úprava frekvence tato konfigurace je dodána na [konfigurace integrace s nástrojem System Center Operations Manager](log-analytics-om-agents.md).

Pokud agenta nemůže připojit k analýze protokolů nebo Operations Manager, bude pokračovat ke shromažďování dat, která bude poskytovat, když se naváže připojení.  Data mohou být ztracena, pokud objem dat dosáhne maximální velikost mezipaměti klienta, nebo pokud agent není schopen navázat připojení do 24 hodin.

## <a name="log-analytics-records"></a>Záznamy služby Log Analytics
Všechna data shromažďovaná společností analýzy protokolů je uloženo v pracovním prostoru jako záznamy.  Zaznamenává shromážděné z různých zdrojů dat. bude mít vlastní sadu vlastností a identifikovat podle jejich **typ** vlastnost.  Najdete v dokumentaci pro každý zdroj dat a řešení podrobnosti pro každý typ záznamu.

## <a name="next-steps"></a>Další postup
* Další informace o [řešení](log-analytics-add-solutions.md) , přidání funkce do analýzy protokolů a také shromažďovat data do pracovního prostoru.
* Další informace o [protokolu hledání](log-analytics-log-searches.md) analyzovat data shromážděná ze zdrojů dat a řešení.  
* Konfigurace [výstrahy](log-analytics-alerts.md) jako proaktivně upozornění na kritický data shromážděná ze zdrojů dat a řešení.
