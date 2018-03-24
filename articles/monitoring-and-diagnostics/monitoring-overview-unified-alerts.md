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
ms.date: 02/02/2018
ms.author: mamit
ms.custom: ''
ms.openlocfilehash: bc9d788367ab14751f9f9158ac88149dc420368a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="explore-the-alerts-experience-in-azure-monitor"></a>Prozkoumejte dojde výstrah v monitorování Azure

## <a name="overview"></a>Přehled
 Výstrahy prostředí v Azure má čerstvou vypadat a aktualizované funkce. Toto nové prostředí má k dispozici **výstrahy** kartě v části monitorování Azure. Tady jsou některé z výhod použití nové výstrahy prostředí, ve srovnání s prostředí výstrahy (klasické):

 - **Oddělení Fired výstrahy a výstrahu pravidla** – v nové prostředí výstrahy výstrah pravidla (definice podmínku, která aktivuje výstrahu) a jsou rozlišené aktivováno výstrahy (instance pálení pravidlo výstrahy), proto provozní a zobrazení konfigurace jsou oddělené.
 - **A sjednocené vytváření prostředí pro metriku, protokol aktivit a protokolu upozornění** - nové výstrahy pro tvorbu prostředí příručky uživatele společně nakonfigurovat pravidlo výstrahy, takže je jednodušší zjistit správné skutečnosti, které je na problém upozorněni.
 - **Zobrazení aktivováno analýzy protokolů výstrahy na portálu Azure** -prostředí v výstrahy, teď můžete také najdete aktivováno výstrahy analýzy protokolů v rámci vašeho předplatného.  
 

Následující části popisují podrobněji, jak funguje na nové prostředí.

## <a name="taxonomy"></a>Taxonomy
Výstrahy prostředí používá následující koncepty na jednotlivé objekty pravidlo výstrah a aktivováno výstrahu při sjednocujícího prostředí pro vytváření napříč různé typy výstrah.

- **Cíl prostředků** -cílem může být jakýmikoli prostředky Azure. Cílový prostředek definuje obor a signály pro výstrahy. Příklad cíle: virtuální počítač, účet úložiště, škálovací sadu virtuálních počítačů, pracovní prostor analýzy protokolů nebo prostředek Application Insights.

- **Kritéria** – kritéria je kombinací signál a logiku aplikovat na cílový prostředek. Příklady: Procento využití procesoru > 70 %, doba odezvy serveru > 4 ms, počet výsledků protokolu dotaz > 100 atd. 

- **Signál** – signály jsou vysílaných cílový prostředek a může mít několik typů. Tato verze preview podporuje **metrika**, **protokol aktivit**, **Application Insights** a **protokolu** jako typy signál.

- **Logika** -uživatelem definované logiku zkontrolujte, jestli je signál v rámci očekávaný rozsah nebo hodnotami.  
 
- **Akce** -konkrétní volání odeslaných k příjemce oznámení (například e-mailem na adresu nebo příspěvků na adresu URL webhooku). Oznámení můžete aktivovat obvykle více akcí. Podporováno v této verzi preview, podporu akce skupiny typů výstrah.  
 
- **Pravidlo výstrahy** -definici podmínku, která by aktivaci upozornění. V této verzi preview pravidlo výstrahy zaznamená cíl a kritéria pro výstrahy. Pravidlo výstrahy může být ve povoleno nebo zakázáno.
 
    > [!NOTE]
    > To se liší od výstrahy (klasické) prostředí, kde výstraha představuje pravidlo a aktivní výstrahy a proto může být v některém z stavů upozornění, aktivní nebo zakázáno.
    >

## <a name="single-place-to-view-and-manage-alerts"></a>Jednotné místo pro zobrazení a Správa výstrah
Cílem prostředí výstrahy (Preview) je možné jednotné místo pro zobrazit a spravovat všechny výstrahy Azure. Následující témata popisují funkce každé jednotlivé obrazovky na nové prostředí.

### <a name="alerts-preview-overview-page"></a>Stránka Přehled výstrah (preview)
**Monitorování – výstrahy (Preview)** přehled stránka zobrazuje agregovaný souhrn všechny aktivní výstrahy, a celkový počet nakonfigurován nebo povolené pravidla výstrah. Také ukazuje seznam všechny aktivní výstrahy. Změna odběry nebo parametry filtrování aktualizací agregace a výstrahy aktivováno seznamu.

> [!NOTE]
> Aktivní výstrahy ukazuje výstrahy jsou omezeny na podporované metrika a activitylog výstrah; Přehled služby Azure monitorování zobrazuje počet výstrah ještě efektivněji, včetně těch, které ve starší výstrahách Azure

 ![alerts-overview](./media/monitoring-overview-unified/alerts-preview-overview.png) 

### <a name="alert-rules-management"></a>Pravidla výstrah správy
**Monitorování – výstrahy (Preview) > pravidla** je jediné stránce Spravovat všechny výstrahy pravidla ve vašich předplatných Azure. Zobrazí seznam všech výstrah pravidel (povolit nebo zakázat) a může být seřazeny podle cílové prostředky, skupiny prostředků, název pravidla nebo stavu. Pravidla výstrah můžete také povolení nebo zakázání nebo upravit z této stránky.  

 ![alerts-rules](./media/monitoring-overview-unified/alerts-preview-rules.png)


## <a name="one-alert-authoring-experience-across-all-monitoring-sources"></a>Jedna výstraha zážitky ze všech zdrojů monitorování
V nové prostředí výstrahy výstrahy konzistentním způsobem bez ohledu na to, služba monitorování se můžou vytvořit nebo signál typu. Aktivováno všechny výstrahy a související podrobnosti jsou dostupné v jediné stránce.  
 
Vytváření výstraha je úloha třech krocích, kde uživatel vybere první cíle pro výstrahy, a potom výběrem správné signál a potom zadáte logiku, která má být použita na signál jako součást pravidla výstrah. Tento proces zjednodušené vytváření už vyžaduje, aby uživatel vědět monitorování zdroje nebo signály podporované před výběrem prostředek služby Azure. Běžné pro tvorbu prostředí automaticky filtruje seznam dostupných signály podle vybrané cílový prostředek a provede vytvoření výstrahy logiky

Další informace o tom, jak vytvořit následující typy výstrah [zde](monitor-alerts-unified-usage.md).
- Metriky výstrahy (nazývané výstrahy metrika téměř v reálném čase v aktuálním prostředí)
- Výstrahy protokolu (analýzy protokolů)
- Výstrahy protokolu (aktivity protokoly)
- Výstrahy protokolu (Application Insights)

 

## <a name="alert-types-supported-in-this-preview"></a>Typy výstrah, které jsou podporovány v této verzi preview


| **Typ signálu** | **Monitorování zdroje** | **Popis** | 
|-------------|----------------|-------------|
| Metrika | Azure monitorování | Volá se [ **téměř v reálném čase metrika výstrahy**](monitoring-near-real-time-metric-alerts.md), tyto metriky výstrahy podporují vyhodnocení metriky podmínky často 1 min a povolit pro více metriky pravidla. Seznam typů podporovaných prostředků je k dispozici [zde](monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported). Starší metrika výstrahy podle definice [sem](monitoring-overview-alerts.md#alerts-in-different-azure-services) nejsou podporovány v nové výstrahy prostředí. Můžete je vyhledat podle výstrahy (klasické)|
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
