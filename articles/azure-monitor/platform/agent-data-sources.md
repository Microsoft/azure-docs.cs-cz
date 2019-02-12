---
title: Konfigurace zdroje dat agenta ve službě Azure Monitor | Dokumentace Microsoftu
description: Zdroje dat definují data protokolu, že Azure Monitor shromažďuje z agentů a další připojení zdrojů.  Tento článek popisuje princip toho, jak Azure Monitor používá zdroje dat, vysvětluje podrobnosti o tom, jak je nakonfigurovat a poskytuje přehled různých zdrojů dat. k dispozici.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 67710115-c861-40f8-a377-57c7fa6909b4
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: bwren
ms.openlocfilehash: ad4270700712311d8bea2e2014701a99e44c735f
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55992441"
---
# <a name="agent-data-sources-in-azure-monitor"></a>Agent zdroje dat ve službě Azure Monitor
Data, která shromažďuje agenty Azure Monitor je definována zdroje dat, které nakonfigurujete.  Data z agentů se ukládá jako [můžete vytvářet protokoly dat](data-collection.md) sadu záznamů.  Každý zdroj dat vytvoří záznamy určitého typu s jednotlivými typu s vlastní sadu vlastností.

![Shromažďování dat protokolu](media/agent-data-sources/overview.png)

## <a name="summary-of-data-sources"></a>Přehled zdrojů dat
V následující tabulce jsou uvedeny agenta zdroje dat, které jsou aktuálně k dispozici ve službě Azure Monitor.  Každý odkaz na věnovaný samostatný článek poskytuje podrobnosti pro tento zdroj dat má.   Poskytuje také informace o jejich metoda a frekvenci shromažďování. 


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
Konfigurace zdroje dat z **Data** v nabídce **Upřesnit nastavení** pro pracovní prostor.  Všechny konfigurace se doručí do všech připojených zdrojů ve vašem pracovním prostoru.  Z této konfigurace nelze aktuálně vyloučit všechny agenty.

![Konfigurace událostí Windows](media/agent-data-sources/configure-events.png)

1. Na webu Azure Portal, vyberte **pracovních prostorů Log Analytics** > váš pracovní prostor > **Upřesnit nastavení**.
2. Vyberte **Data**.
3. Klikněte na zdroj dat, který chcete konfigurovat.
4. Použijte odkaz na dokumentaci pro jednotlivé zdroje dat v tabulce výše uvedené podrobnosti o jejich konfiguraci.


## <a name="data-collection"></a>Shromažďování dat
Konfigurace zdroje dat jsou doručeny agentům byli přímo připojení k Azure Monitor během několika minut.  Je zadaná data shromážděná z agenta a doručované přímo do Azure monitoru v intervalech, které jsou specifické pro každý zdroj dat.  Naleznete v dokumentaci pro jednotlivé zdroje dat pro tyto konkrétní.

Pro agenty System Center Operations Manager v připojené skupině pro správu konfigurace zdroje dat jsou přeloženy do sady management Pack a doručit do skupiny pro správu každých 5 minut, ve výchozím nastavení.  Agent soubory ke stažení sady management pack jako u všech ostatních a shromažďuje zadaná data. V závislosti na zdroji dat data budou že buď odeslány na server pro správu, který předává data do Azure monitoru, nebo bude agent posílat data do Azure monitoru bez nutnosti kontaktovat server pro správu. Zobrazit [podrobnosti shromažďování dat pro monitorování řešení v Azure](../insights/solutions-inventory.md) podrobnosti.  Informace o najdete podrobnosti o připojení nástroje Operations Manager a Azure Monitor a úprava frekvence tuto konfiguraci se doručí na [konfiguraci integrace se sadou System Center Operations Manager](om-agents.md).

Pokud je agent nemůže připojit k Azure Monitor nebo Operations Manager, bude nadále shromažďovat data, která bude poskytovat navazuje připojení.  Data mohou být ztraceny, pokud objem dat dosáhne maximální velikost mezipaměti klienta, nebo pokud agent není schopen navázat připojení do 24 hodin.

## <a name="log-records"></a>Záznamy protokolu
Všechna data protokolů shromážděná službou Azure Monitor je v pracovním prostoru uloží jako záznamy.  Záznamy shromážděné z různých zdrojů dat budou mít své vlastní sadu vlastností a identifikovat podle jejich **typ** vlastnost.  Najdete v dokumentaci pro jednotlivé zdroje dat a řešení pro podrobnosti pro každý typ záznamu.

## <a name="next-steps"></a>Další postup
* Další informace o [řešení monitorování](../insights/solutions.md) , které doplňují do Azure monitoru a také shromažďovat data do pracovního prostoru.
* Další informace o [protokolu dotazy](../log-query/log-query-overview.md) analyzovat data shromážděná ze zdrojů dat a monitorování řešení.  
* Konfigurace [výstrahy](alerts-overview.md) můžete zajistit aktivní upozorňování kritických dat shromážděných ze zdroje dat a monitorování řešení.