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
ms.openlocfilehash: 356988e8ae743d73c8e2cc7cc106cbc5b0d1a423
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="the-new-alerts-experience-in-azure-monitor"></a>Nové výstrahy na základě zkušeností uživatelů Azure monitorování

## <a name="overview"></a>Přehled
Výstrahy má nové prostředí. Starší výstrahy prostředí je nyní na kartě výstrahy (klasické). Nové výstrahy prostředí má následující výhody přes prostředí výstrahy (klasické):

 - **Oddělení Fired výstrahy a výstrahu pravidla** – výstraha pravidla (definice podmínku, která aktivuje výstrahu) a jsou rozlišené aktivováno výstrahy (instance pálení pravidlo výstrahy), takže zobrazení provozu a konfigurace jsou oddělené.
 - **Jednotné prostředí pro vytváření A** – všechny výstrahy vytvoření metrik, protokoly a aktivity protokolu na Azure monitorů, analýzy protokolů a Application Insights je na jednom místě. 
 - **Zobrazení aktivováno analýzy protokolů výstrahy na portálu Azure** – můžete nyní také najdete aktivováno výstrahy analýzy protokolů v rámci vašeho předplatného. Dříve byly tyto samostatné portálu. 
 - **Lepší pracovního postupu** - nové výstrahy pro tvorbu prostředí příručky uživatele společně nakonfigurovat pravidlo výstrahy, takže je jednodušší zjistit správné skutečnosti, které je na problém upozorněni.
 

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

 ![alerts-overview](./media/monitoring-overview-unified/alerts-preview-overview.png) 

### <a name="alert-rules-management"></a>Pravidla výstrah správy
**Monitorování – výstrahy > pravidla** je jediné stránce Spravovat všechny výstrahy pravidla ve vašich předplatných Azure. Zobrazí seznam všech výstrah pravidel (povolit nebo zakázat) a může být seřazeny podle cílové prostředky, skupiny prostředků, název pravidla nebo stavu. Pravidla výstrah můžete také povolení nebo zakázání nebo upravit z této stránky.  

 ![alerts-rules](./media/monitoring-overview-unified/alerts-preview-rules.png)


## <a name="one-alert-authoring-experience-across-all-monitoring-sources"></a>Jedna výstraha zážitky ze všech zdrojů monitorování
V nové prostředí výstrahy výstrahy konzistentním způsobem bez ohledu na to, služba monitorování se můžou vytvořit nebo signál typu. Aktivováno všechny výstrahy a související podrobnosti jsou dostupné v jediné stránce.  
 
Vytváření výstraha je úloha třech krocích, kde uživatel vybere první cíle pro výstrahy, a potom výběrem správné signál a potom zadáte logiku, která má být použita na signál jako součást pravidla výstrah. Tento proces zjednodušené vytváření už vyžaduje, aby uživatel vědět monitorování zdroje nebo signály podporované před výběrem prostředek služby Azure. Běžné pro tvorbu prostředí automaticky filtruje seznam dostupných signály podle vybrané cílový prostředek a provede vytvoření výstrahy logiky

Další informace o tom, jak vytvořit následující typy výstrah [zde](monitor-alerts-unified-usage.md).
- Upozornění metrik
- Výstrahy protokolu (analýzy protokolů)
- Výstrahy protokolu (aktivity protokoly)
- Výstrahy protokolu (Application Insights)

 

## <a name="alert-types-supported"></a>Podporované typy výstrah


| **Typ signálu** | **Monitorování zdroje** | **Popis** | 
|-------------|----------------|-------------|
| Metrika | Azure monitorování | Označuje taky jako [ **téměř v reálném čase metrika výstrahy**](monitoring-near-real-time-metric-alerts.md), tyto metriky výstrahy podporují vyhodnocení metriky podmínky často 1 min a povolit pro více metriky pravidla. Seznam typů podporovaných prostředků je k dispozici [zde](monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported). Starší metrika výstrahy podle definice [sem](monitoring-overview-alerts.md#alerts-in-different-azure-services) nejsou podporovány v nové výstrahy prostředí. Můžete je vyhledat podle výstrahy (klasické)|
| Logs  | Log Analytics | Přijímat oznámení nebo spustit automatizované akce, pokud vyhledávací dotaz protokolu nad daty metrika nebo událostí splňuje určitá kritéria.|
| Protokol aktivit | Protokoly aktivit | Tato kategorie obsahuje záznamy všechny Create, Update a odstraňte akce prováděné prostřednictvím vybraná cílová (skupiny prostředků nebo prostředků nebo předplatného). |
| Logs  | Protokoly služby stavu | Není podporováno v prostředí výstrahy.   |
| Logs  | Application Insights | Tato kategorie obsahuje protokoly výkonu podrobné informace o této aplikaci. Pomocí analýzy dotazu, můžete definovat podmínky pro akce, jež mají být provedeny – na základě dat aplikace. |
| Metrika | Application Insights | Není podporováno v prostředí výstrahy. Je najdete v části výstrahy (klasické) |
| Testy dostupnosti | Application Insights | Není podporováno v prostředí výstrahy. Je najdete v části výstrahy (klasické) |


## <a name="next-steps"></a>Další postup
- [Další informace o použití nové výstrahy prostředí vytvořit, zobrazit a spravovat výstrahy](monitor-alerts-unified-usage.md)
- [Další informace o protokolu výstrahy v možnosti výstrahy](monitor-alerts-unified-log.md)
- [Další informace o metriky výstrahy v možnosti výstrahy](monitoring-near-real-time-metric-alerts.md)
- [Další informace o protokolu upozornění v možnosti výstrahy](monitoring-activity-log-alerts-new-experience.md)
