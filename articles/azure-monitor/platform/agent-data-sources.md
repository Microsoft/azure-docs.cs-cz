---
title: Konfigurace zdrojů dat agenta v Azure Monitor | Microsoft Docs
description: Zdroje dat definují data protokolu, která Azure Monitor shromažďuje z agentů a dalších připojených zdrojů.  Tento článek popisuje koncept způsobu, jakým Azure Monitor používá zdroje dat, vysvětluje podrobnosti o tom, jak je nakonfigurovat, a poskytuje souhrn různých zdrojů dat, které jsou k dispozici.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: a183589c3e5274cf747164cdc33d46044f95e716
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87073694"
---
# <a name="agent-data-sources-in-azure-monitor"></a>Zdroje dat agenta v Azure Monitor
Data, která Azure Monitor shromažďuje z agentů, jsou definována zdroji dat, které nakonfigurujete.  Data z agentů jsou ukládána jako [data protokolu](data-platform-logs.md) se sadou záznamů.  Každý zdroj dat vytvoří záznamy určitého typu s každým typem s vlastní sadou vlastností.

![Shromažďování dat protokolu](media/agent-data-sources/overview.png)

## <a name="summary-of-data-sources"></a>Shrnutí zdrojů dat
V následující tabulce jsou uvedeny zdroje dat agenta, které jsou aktuálně k dispozici v Azure Monitor.  Každá z nich má odkaz na samostatný článek, který poskytuje podrobnosti pro tento zdroj dat.   Poskytuje také informace o jejich metodě a četnosti shromažďování. 


| Zdroj dat | Platforma | Agent Log Analytics | Agent Operations Manageru | Azure Storage | Operations Manager požadováno? | Data agenta Operations Manager odeslána prostřednictvím skupiny pro správu | Četnost shromažďování dat |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Vlastní protokoly](data-sources-custom-logs.md) | Windows |&#8226; |  | |  |  | při doručení |
| [Vlastní protokoly](data-sources-custom-logs.md) | Linux   |&#8226; |  | |  |  | při doručení |
| [Protokoly IIS](data-sources-iis-logs.md) | Windows |&#8226; |&#8226; |&#8226; |  |  |závisí na nastavení pro výměnu souborů protokolu. |
| [Čítače výkonu](data-sources-performance-counters.md) | Windows |&#8226; |&#8226; |  |  |  |Jak naplánováno, minimálně 10 sekund |
| [Čítače výkonu](data-sources-performance-counters.md) | Linux |&#8226; |  |  |  |  |Jak naplánováno, minimálně 10 sekund |
| [Syslog](data-sources-syslog.md) | Linux |&#8226; |  |  |  |  |z Azure Storage: 10 minut; od agenta: při doručení |
| [Protokoly událostí systému Windows](data-sources-windows-events.md) |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; | při doručení |


## <a name="configuring-data-sources"></a>Konfigurace zdrojů dat
Zdroje dat nakonfigurujete z nabídky **data** v **rozšířeném nastavení** pracovního prostoru.  Všechny konfigurace se doručí do všech připojených zdrojů ve vašem pracovním prostoru.  Z této konfigurace teď nemůžete vyloučit žádné agenty.

![Konfigurace událostí systému Windows](media/agent-data-sources/configure-events.png)

1. V Azure Portal vyberte **Log Analytics pracovní prostory** > > **Rozšířená nastavení**.
2. Vyberte **data**.
3. Klikněte na zdroj dat, který chcete konfigurovat.
4. Podrobnosti o konfiguraci získáte pomocí odkazu na dokumentaci pro každý zdroj dat v tabulce výše.


## <a name="data-collection"></a>Shromažďování dat
Konfigurace zdrojů dat se doručují agentům, které jsou přímo připojené k Azure Monitor během několika minut.  Zadaná data jsou shromažďována z agenta a dodávána přímo do Azure Monitor v intervalech specifických pro jednotlivé zdroje dat.  Prohlédněte si dokumentaci pro každý zdroj dat pro tyto konkrétní.

U agentů System Center Operations Manager v připojené skupině pro správu se konfigurace zdrojů dat přeloží do sad Management Pack a ve výchozím nastavení se doručí do skupiny pro správu každých 5 minut.  Agent stáhne Management Pack jako jiný a shromáždí zadaná data. V závislosti na zdroji dat budou data odeslána na management server, která přepošle data do Azure Monitor, nebo agent odešle data do Azure Monitor bez průchodu management server. Podrobnosti najdete [v tématu podrobnosti o shromažďování dat pro řešení monitorování v Azure](../monitor-reference.md) .  Můžete si přečíst informace o připojení Operations Manager a Azure Monitor a o úpravách četnosti, kterou konfigurace doručí při [konfiguraci integrace s System Center Operations Manager](om-agents.md).

Pokud se Agent nemůže připojit k Azure Monitor nebo Operations Manager, bude pokračovat ve shromažďování dat, která budou doručována při navázání připojení.  Data mohou být ztracena v případě, že množství dat dosáhne maximální velikosti mezipaměti pro klienta nebo pokud Agent není schopen navázat připojení během 24 hodin.

## <a name="log-records"></a>Záznamy protokolu
Všechna data protokolu shromážděná pomocí Azure Monitor jsou ukládána v pracovním prostoru jako záznamy.  Záznamy shromážděné různými zdroji dat budou mít svou vlastní sadu vlastností a budou identifikovány vlastností **typu** .  Podrobnosti o jednotlivých typech záznamů najdete v dokumentaci pro každý zdroj dat a řešení.

## <a name="next-steps"></a>Další kroky
* Přečtěte si o [řešeních monitorování](../insights/solutions.md) , která přidávají funkce pro Azure monitor a také shromažďovat data do pracovního prostoru.
* Přečtěte si o [dotazech protokolů](../log-query/log-query-overview.md) , které analyzují data shromážděná ze zdrojů dat a řešení monitorování.  
* Nakonfigurujte [výstrahy](alerts-overview.md) pro proaktivní upozorňování na důležitá data shromážděná ze zdrojů dat a řešení monitorování.
