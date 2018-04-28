---
title: Prozkoumat nové výstrahy prostředí v Azure monitorování | Microsoft Docs
description: Pochopit, jak nové výstrahy jednoduché a škálovatelné prostředí v Azure díky vytváření, zobrazení a Správa výstrah jednodušší
author: manishsm-msft
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2018
ms.author: mamit
ms.custom: ''
ms.openlocfilehash: c3622b4699ef532f204231c76aa3436be3676763
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="the-new-alerts-experience-in-azure-monitor"></a>Nové výstrahy na základě zkušeností uživatelů Azure monitorování

## <a name="overview"></a>Přehled

> [!NOTE]
> Tento článek popisuje novější výstrahy. Starší classic Azure monitorování výstrah jsou popsané v [přehled výstrah classic](monitoring-overview-alerts.md). 
>
>

Výstrahy má nové prostředí. Starší výstrahy prostředí je nyní na kartě výstrahy (klasické). Nové výstrahy prostředí má následující výhody přes prostředí výstrahy (klasické):

-   **Lepší oznámení systému**: použijte všechny novější výstrahy [skupiny akcí]( https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups), které jsou s názvem skupiny oznámení a akce, které lze znovu použít ve více výstrah.  Classic metriky výstrahy a starší výstrahy analýzy protokolů nepoužívejte skupiny akcí. 
- **Jednotné prostředí pro vytváření A** – všechny výstrahy vytvoření metrik, protokoly a aktivity protokolu na Azure monitorů, analýzy protokolů a Application Insights je na jednom místě. 
- **Zobrazení aktivováno analýzy protokolů výstrahy na portálu Azure** – můžete nyní také najdete aktivováno výstrahy analýzy protokolů v rámci vašeho předplatného. Dříve byly tyto samostatné portálu. 
- **Oddělení Fired výstrahy a výstrahu pravidla** – výstraha pravidla (definice podmínku, která aktivuje výstrahu) a jsou rozlišené aktivováno výstrahy (instance pálení pravidlo výstrahy), takže zobrazení provozu a konfigurace jsou oddělené.
- **Lepší pracovního postupu** - nové výstrahy pro tvorbu prostředí příručky uživatele společně nakonfigurovat pravidlo výstrahy, takže je jednodušší zjistit správné skutečnosti, které je na problém upozorněni.
 
Novější metriky výstrahy konkrétně mají následující vylepšení:
-   **Vylepšené latence**: novější metriky výstrahy můžete spustit často každou minutu. Starší metriky výstrahy vždy spustit v intervalu 5 minut. Výstrahy protokolu ještě delší než 1 minuta zpoždění kvůli době je potřeba k ingestování protokoly. 
-   **Podpora pro multidimenzionální metriky**: můžete výstrahy na dimenzí metriky, které umožňuje sledovat segment zajímavé metriky.
-   **Větší kontrolu nad metriky podmínky**: můžete definovat bohatší pravidla výstrah. Výstrahy novější podporují monitorování maximální, minimální, průměrná a celkové hodnoty metrik.
-   **Kombinaci monitorování více metrik**: můžete sledovat více metriky (v současné době až dvě metriky) s jedním pravidlem. Výstraha se spustí, pokud obě metriky narušit jejich příslušné prahové hodnoty pro zadané časové období.
-   **Metriky z protokolů** (verze public preview omezené): některé protokolu dat přejdete do analýzy protokolů můžete nyní extrahována a převést do Azure monitorování metriky a potom zobrazí upozornění na stejně jako jiné metriky. 



Následující části popisují podrobněji, jak funguje na nové prostředí.

## <a name="alert-rules-terminology"></a>Terminologie pravidla výstrah
Nové výstrahy prostředí používá následující koncepty na jednotlivé objekty pravidlo výstrah a aktivováno výstrahu při sjednocujícího prostředí pro vytváření napříč různé typy výstrah.

- **Cíl prostředků** -cílem může být jakýmikoli prostředky Azure. Cílový prostředek definuje obor a signály pro výstrahy. Příklad cíle: virtuální počítač, účet úložiště, škálovací sadu virtuálních počítačů, pracovní prostor analýzy protokolů nebo prostředek Application Insights.

- **Kritéria** – kritéria je kombinací signál a logiku aplikovat na cílový prostředek. Příklady: Procento využití procesoru > 70 %, doba odezvy serveru > 4 ms, počet výsledků protokolu dotaz > 100 atd. 

- **Signál** – signály jsou vysílaných cílový prostředek a může mít několik typů. **Metrika**, **protokol aktivit**, **Application Insights**, a **protokolu** jsou podporované typy signál.

- **Logika** -uživatelem definované logiku zkontrolujte, jestli je signál v rámci očekávaný rozsah nebo hodnotami.  
 
- **Akce** – konkrétní akce při vyvolání výstrahy. Například e-mailem na e-mailovou adresu nebo volání URL webhooku se nenačetla. Více akcí může dojít, když se aktivuje výstrahu. Tyto výstrahy podporovat skupiny akcí.  
 
- **Pravidlo výstrahy** -podmínku, která by aktivaci upozornění. Pravidlo výstrahy zaznamená cíl a kritéria pro výstrahy. Pravidlo výstrahy může být ve povoleno nebo zakázáno.
 
    > [!NOTE]
    > To se liší od výstrahy (klasické) prostředí, kde výstraha představuje pravidlo a aktivní výstrahy a proto může být v některém z stavů upozornění, aktivní nebo zakázáno.
    >

## <a name="single-place-to-view-and-manage-alerts"></a>Jednotné místo pro zobrazení a Správa výstrah
Cílem prostředí výstrahy je možné jednotné místo pro zobrazit a spravovat všechny výstrahy Azure. Následující témata popisují funkce každé jednotlivé obrazovky na nové prostředí.

### <a name="alerts-overview-page"></a>Stránka Přehled výstrah
**Monitorování – výstrahy** přehled stránka zobrazuje agregovaný souhrn všechny aktivní výstrahy, a celkový počet nakonfigurován nebo povolené pravidla výstrah. Také ukazuje seznam všechny aktivní výstrahy. Změna odběry nebo parametry filtrování aktualizací agregace a výstrahy aktivováno seznamu.

> [!NOTE]
> Aktivní výstrahy ukazuje výstrahy jsou omezeny na podporované metrika a aktivity protokolu výstrah; Přehled služby Azure monitorování zobrazuje počet aktivní výstrahy, včetně těch, které ve starší výstrahách Azure

 ![Přehled výstrah](./media/monitoring-overview-unified-alerts/alerts-preview-overview2.png) 

### <a name="alert-rules-management"></a>Pravidla výstrah správy
**Monitorování – výstrahy > pravidla** je jediné stránce Spravovat všechny výstrahy pravidla ve vašich předplatných Azure. Zobrazí seznam všech výstrah pravidel (povolit nebo zakázat) a může být seřazeny podle cílové prostředky, skupiny prostředků, název pravidla nebo stavu. Pravidla výstrah můžete také povolení nebo zakázání nebo upravit z této stránky.  

 ![pravidla výstrah](./media/monitoring-overview-unified-alerts/alerts-preview-rules.png)


## <a name="one-alert-authoring-experience-across-all-monitoring-sources"></a>Jedna výstraha zážitky ze všech zdrojů monitorování
V nové prostředí výstrahy výstrahy konzistentním způsobem bez ohledu na to, služba monitorování se můžou vytvořit nebo signál typu. Aktivováno všechny výstrahy a související podrobnosti jsou dostupné v jediné stránce.  
 
Vytváření výstraha je úloha třech krocích, kde uživatel vybere první cíle pro výstrahy, a potom výběrem správné signál a potom zadáte logiku, která má být použita na signál jako součást pravidla výstrah. Tento proces zjednodušené vytváření už vyžaduje, aby uživatel vědět monitorování zdroje nebo signály podporované před výběrem prostředek služby Azure. Běžné pro tvorbu prostředí automaticky filtruje seznam dostupných signály podle vybrané cílový prostředek a provede vytvoření výstrahy logiky

Další informace o tom, jak vytvořit následující typy výstrah [zde](monitor-alerts-unified-usage.md).
- Upozornění metrik
- Výstrahy protokolu (analýzy protokolů)
- Výstrahy protokolu (aktivity protokoly)
- Výstrahy protokolu (Application Insights)

 
## <a name="alerts-supported-in-new-experience"></a>Výstrahy, které jsou podporovány v nové prostředí
Výstrahy jsou k dispozici v rámci několik Azure monitorování služeb. Informace o tom, a kdy použít tyto služby [najdete v článku](./monitoring-overview.md). Zde je výčet typů výstrah dostupné v Azure a co v současné době podporuje nové výstrahy prostředí. 


| **Typ signálu** | **Monitorování zdroje** | **Popis** | 
|-------------|----------------|-------------|
| Metrika | Azure monitorování | Označuje taky jako [metriky výstrahy téměř v reálném čase](monitoring-near-real-time-metric-alerts.md), budou podporovat vyhodnocení metriky podmínky s četností až 1 minutu a povolit pro více metriky a komplexní metriky pravidla. Seznam typů podporovaných prostředků je k dispozici [zde](monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported). |
| Metrika | Azure monitorování | [Starší classic metriky výstrahy](monitoring-overview-alerts.md) nejsou podporovány v nové výstrahy prostředí. Můžete je vyhledat podle výstrahy (klasické) na portálu Azure. Classic výstrahy podporovat některé typy metriky, které dosud nebyla přesunuta novější výstrah. Úplný seznam najdete v tématu [podporované metriky](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-supported-metrics)
| Logs  | Log Analytics | Přijímat oznámení nebo spustit automatizované akce, pokud vyhledávací dotaz protokolu nad daty metrika nebo událostí splňuje určitá kritéria. Starší výstrahy analýzy protokolů jsou stále k dispozici, ale jsou [kopírovány do nové prostředí](monitoring-alerts-extend.md). Kromě toho [náhled z *analýzy protokolů zaznamená jako metriky* ](monitoring-alerts-extend-tool.md) je k dispozici. Verze preview umožňuje provést některé typy protokolů a jejich převedení na metriky, kde můžete lze pak výstraha na pomocí nové výstrahy prostředí. Ve verzi preview je užitečné, pokud máte protokoly mimo Azure, které chcete získat spolu s nativní metrik Azure monitorování. |
| Protokol aktivit | Protokoly aktivity (Obecné) | Obsahuje záznamy všechny Create, Update a Delete akce prováděné prostřednictvím vybraná cílová (skupiny prostředků nebo prostředků nebo předplatného). |
| Protokol aktivit  | Service Health | Nepodporuje se v nové prostředí pro výstrahy. V tématu [vytvářet aktivity protokolu upozornění na oznámení o službách](monitoring-activity-log-alerts-on-service-notifications.md).  |
| Logs  | Application Insights | Obsahuje protokoly výkonu podrobné informace o této aplikaci. Pomocí analýzy dotazu, můžete definovat podmínky pro akce, jež mají být provedeny – na základě dat aplikace. |
| Metrika | Application Insights | Nepodporuje se v nové prostředí pro výstrahy. V tématu [metriky výstrahy](../application-insights/app-insights-alerts.md) |
| Testy dostupnosti webu | Application Insights | Není podporováno v prostředí výstrahy.  V tématu [webového testu výstrahy](../application-insights/app-insights-monitor-web-app-availability.md). Chcete-li k dispozici na žádné web instrumentovány k odesílání dat do služby Application Insights. Přijímat oznámení v případě dostupnosti nebo odezvy webu je nižší než očekávání. |




## <a name="next-steps"></a>Další postup
- [Další informace o použití nové výstrahy prostředí vytvořit, zobrazit a spravovat výstrahy](monitor-alerts-unified-usage.md)
- [Další informace o protokolu výstrahy v možnosti výstrahy](monitor-alerts-unified-log.md)
- [Další informace o metriky výstrahy v možnosti výstrahy](monitoring-near-real-time-metric-alerts.md)
- [Další informace o protokolu upozornění v možnosti výstrahy](monitoring-activity-log-alerts-new-experience.md)
