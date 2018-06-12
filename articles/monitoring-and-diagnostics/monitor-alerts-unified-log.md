---
title: Výstrahy protokolu v nástroji Sledování Azure
description: Aktivační událost e-mailů, oznámení, adresy URL weby volání (webhooky) nebo při splnění zadané podmínky analytické dotazu pro výstrahy Azure automation.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: aab3c843493048291583bea111a52fe3356dc0f0
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35264370"
---
# <a name="log-alerts-in-azure-monitor---alerts"></a>Protokol výstrah v monitorování Azure – výstrahy 
Tento článek obsahuje podrobnosti o výstrahách protokolu se jeden z typů výstrah, které jsou podporovány v rámci nové [Azure výstrahy](monitoring-overview-unified-alerts.md) a povolit uživatelům analytické platformě Azure, je použít jako základ pro zobrazení výstrah... Podrobnosti o výstrahách metrika pomocí protokolů [téměř upozornění v reálném čase metrika](monitoring-near-real-time-metric-alerts.md)


Výstraha protokol se skládá z hledání protokolů pravidel vytvořených pro [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) nebo [Application Insights](../application-insights/app-insights-cloudservices.md#view-azure-diagnostic-events)


## <a name="log-search-alert-rule---definition-and-types"></a>Protokol hledání pravidlo výstrahy - typy a definice

Vyhledat pravidla protokolu jsou vytvořené pomocí Azure výstrahy pro automatické spouštění dotazů zadaného protokolu v pravidelných intervalech.  Pokud výsledky dotazu protokolu konkrétním kritériím, se vytvoří záznam výstrahy. Pravidlo může automaticky spusťte jednu nebo více akcí pomocí [skupiny akcí](monitoring-action-groups.md). 

Vyhledat pravidla protokolu jsou definovány následující podrobnosti:
- **Protokolu dotazu**.  Dotaz, který se spustí pokaždé, když se aktivuje pravidlo výstrahy.  Vrácené tímto dotazem záznamy se používají k určení, jestli se má vytvořit výstrahu. *Azure Application Insights* dotaz může také obsahovat [volání mezi aplikacemi](https://dev.applicationinsights.io/ai/documentation/2-Using-the-API/CrossResourceQuery), pokud uživatel nemá přístupová práva k externím aplikacím. 

    > [!IMPORTANT]
    > Podpora systému [křížové dotazu aplikace pro službu Application Insights](https://dev.applicationinsights.io/ai/documentation/2-Using-the-API/CrossResourceQuery) je ve verzi preview – omezené funkce pro použití s 2 nebo více aplikací a činnost koncového uživatele mohou podléhat změnám. Použití [křížové prostoru dotazu](https://dev.loganalytics.io/oms/documentation/3-Using-the-API/CrossResourceQuery) a [dotazu mezi prostředky pro analýzy protokolů](../log-analytics/log-analytics-cross-workspace-search.md) je aktuálně **nepodporuje** ve výstrahách Azure.

- **Časové období**.  Určuje časový rozsah pro dotaz. Dotaz vrátí pouze záznamy vytvořené v tomto rozsahu před aktuálním časem. Časové období omezuje dat načtených pro dotaz protokolu, aby se zabránilo zneužití a by se obešla libovolný příkaz čas (například před) používaných v dotazu protokolu. <br>*Například pokud časové období nastavena na 60 minut a spuštění dotazu: 15: 00, vrátí se k provedení dotazu protokolu pouze záznamy vytvořené 12:15:00 až 1:15 hodin. Nyní, pokud dotaz protokolu používá čas příkaz jako před (7d), protokolu by spustí dotaz pouze pro data mezi 12:15:00 a času 1:15 - jakoby dat existuje pro pouze za posledních 60 minut. A ne pro sedm dní dat uvedených v protokolu dotazu.*
- **Frekvence**.  Určuje, jak často se má spustit dotaz. Může být libovolná hodnota 5 minut až 24 hodin. Musí být rovna nebo menší než časové období.  Pokud hodnota je větší než časové období, riskujete záznamů je vynechán.<br>*Představte si třeba časové období 30 minut a četnost 60 minut.  Pokud je v 1:00 spustit dotaz, vrátí záznamy 12:30 až 1:00 PM.  Při příštím spuštění dotazu by je 2:00, když měla by vrátit záznamy 1:30 až 2:00.  Všechny záznamy vytvořené 1:00 až 1:30 by nikdy vyhodnotí.*
- **Prahová hodnota**.  Výsledky hledání protokolů se vyhodnocují k určení, zda má být vytvořena výstraha.  Prahová hodnota se liší pro různé typy pravidla výstrah vyhledávání protokolu.

Vyhledat pravidla protokolu je jej pro [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) nebo [Application Insights](../application-insights/app-insights-cloudservices.md#view-azure-diagnostic-events), můžou být dva typy. Každý z těchto typů je podrobně popsány v následujících částech.

- **[Počet výsledků](#number-of-results-alert-rules)**. Vytvoří, když počet záznamů protokolu nalezené překračuje zadaný počet jedna výstraha.
- **[Metriky měření](#metric-measurement-alert-rules)**.  Výstraha byla vytvořena pro každý objekt ve výsledcích hledání protokolu s hodnotami, které překročí zadanou prahovou hodnotu.

Rozdíl mezi typy pravidlo výstrahy se následujícím způsobem.

- *Počet výsledků* pravidla výstrah vždy vytvoří jednu výstrahu, chvíli *metriky měření* pravidlo výstrahy vytvoří výstrahu pro každý objekt, který překračuje prahovou hodnotu.
- *Počet výsledků* pravidla výstrah vytvářejí výstrahu, když je prahová hodnota počtu současně. *Metriky měření* pravidla výstrah může vytvořit upozornění, pokud je překročena prahová hodnota počtu časy v konkrétním časovém intervalu.

### <a name="number-of-results-alert-rules"></a>Počet výsledků pravidla výstrah
**Počet výsledků** pravidla výstrah vytvořit jednu výstrahu, když počet záznamů vrácených dotazem vyhledávání překročí zadanou prahovou hodnotu. Tento typ pravidla výstrah je ideální pro práci s událostmi, jako jsou protokoly událostí systému Windows, Syslog, WebApp odpovědi a vlastní protokoly.  Můžete vytvořit výstrahu, když se vytvoří událost konkrétní chyby, nebo když více chybové události jsou vytvořené v konkrétním časovém období.

**Prahová hodnota**: prahová hodnota pro počet výsledků pravidla výstrah je větší nebo menší než určitou hodnotu.  Pokud počet záznamů protokolu nalezené splňují tato kritéria, se vytvoří výstraha.

Chcete-li výstraha na jednu událost, nastavit počet výsledků na hodnotu větší než 0 a zkontrolujte výskyt jedna událost, která byla vytvořena od posledního spuštění dotazu. Některé aplikace mohou být zaznamenány občasné chyby, který by neměl být nutně vygeneruje výstrahu.  Aplikace může například opakujte procesu, který vytvořil událost chyby a pak úspěšné příště.  V takovém případě nemusí budete chtít vytvořit výstrahu, pokud jsou v konkrétním časovém období vytvořit více událostí.  

V některých případech můžete vytvořit výstrahu při absenci událost.  Tento proces se může například protokolu běžné události indikující, že funguje správně.  Pokud není některá z těchto událostí protokolu v konkrétním časovém období, je třeba vytvořit výstrahu.  V takovém případě by nastavit prahovou hodnotu **menší než 1**.

#### <a name="example"></a>Příklad:
Představte si třeba situaci, kdy budete chtít vědět, když aplikace založené na webu poskytuje odpověď pro uživatele s kódem 500 (tj.) vnitřní chyba serveru. Vytvoříte pravidlo výstrahy s následujícími podrobnostmi:  
- **Dotaz:** požadavky | kde resultCode == "500"<br>
- **Časové období:** 30 minut<br>
- **Frekvence výstrah:** pět minut<br>
- **Prahová hodnota:** skvělé než 0.<br>

Výstraha by spusťte dotaz každých 5 minut, 30 minut dat – má být vyhledán všech záznamů, kde je kód výsledku 500. Pokud se nenajde i jeden takový záznam, aktivuje se výstraha a aktivuje akci nakonfigurovanou.

### <a name="metric-measurement-alert-rules"></a>Pravidla výstrah metriky měření

- **Metriky měření** pravidla výstrah vytvářejí výstrahu pro každý objekt v dotazu s hodnotou, který je delší než zadaná prahová hodnota.  Mají odlišné následující rozdíly proti **počet výsledků** pravidla výstrah.
- **Agregační funkce**: Určuje výpočet, který provádí a potenciálně číselného pole k agregaci.  Například **count()** vrátí počet záznamů v dotazu, **avg(CounterValue)** Vrátí průměrnou hodnotu pole přepočtené průběhu intervalu. Agregační funkci v dotazu musí být s názvem volat: AggregatedValue a zadejte číselnou hodnotu. 
- **Pole Seskupit**: pro každou instanci v tomto poli se vytvoří záznam s hodnotou agregované a výstraha může vygenerovat pro každý.  Například pokud chcete generovat výstrahy pro každý počítač, byste použili **počítačem.** 

    > [!NOTE]
    > Metriky měření výstrah pravidla, které jsou založeny na Application Insights můžete zadat pole pro seskupení dat. Chcete-li to provést, použijte **agregační na** možnost v definici pravidla.   
    
- **Interval**: definuje časový interval, za které agregovaná data.  Například pokud jste zadali **pět minut**, by se vytvoří záznam pro každou instanci pole skupiny agregován v intervalech 5 minut za časové období, zadaný pro výstrahu.

    > [!NOTE]
    > Funkce Koš služby musíte použít v dotazu zadat interval. Jako bin() může mít za následek nerovné časové intervaly – výstraha automaticky převede bin příkaz bin_at příkaz s příslušnou dobu za běhu, zajistit výsledků s pevnou bodu
    
- **Prahová hodnota**: prahová hodnota pro pravidla výstrah metriky měření je definována agregovaná hodnota a celou řadu.  Pokud žádné datového bodu v hledání protokolů překročí tuto hodnotu, považuje za porušení.  Pokud počet porušení v u všech objektů ve výsledcích překročí zadanou hodnotu, se vytvoří výstraha pro tento objekt.

#### <a name="example"></a>Příklad:
Vezměte v úvahu scénář, kde jste chtěli výstrahu překračování libovolného počítače využití procesoru 90 % třikrát více než 30 minut.  Vytvoříte pravidlo výstrahy s následujícími podrobnostmi:  

- **Dotaz:** výkonu | kde ObjectName == "Procesor" a název_čítače == "% času procesoru" | shrnout AggregatedValue = avg(CounterValue) podle bin (TimeGenerated, 5 m), počítač<br>
- **Časové období:** 30 minut<br>
- **Frekvence výstrah:** pět minut<br>
- **Agregace hodnota:** skvělé než 90<br>
- **Aktivační událost upozornění na základě:** celkem poruší větší než 2<br>

Dotaz by vytvořit průměrnou hodnotu pro každý počítač v intervalech 5 minut.  Tento dotaz by spustit každých 5 minut datech shromážděných za předchozí 30 minut.  Ukázková data jsou uvedené dole pro tři počítače.

![Ukázkové výsledky dotazu](./media/monitor-alerts-unified/metrics-measurement-sample-graph.png)

V tomto příkladu by se vytvořily samostatné výstrahy pro srv02 a srv03 vzhledem k tomu, že nedodržení prahovou hodnotu 90 % 3krát za časové období.  Pokud **aktivační událost upozornění na základě:** byly změněny na **za sebou** výstrahu by vytvořen pouze pro srv03, od nichž nebyla dodržena prahová hodnota pro tři po sobě jdoucích vzorků.


## <a name="log-search-alert-rule---creation-and-modification"></a>Protokol hledání pravidlo výstrahy – vytváření a úpravy

Výstraha protokolu, jakož i jeho consisting pravidlo výstrahy vyhledávání protokolu můžou zobrazit, vytvořit nebo změnil z:
- Azure Portal
- REST API (včetně pomocí prostředí PowerShell)
- Šablony Azure Resource Manageru

### <a name="azure-portal"></a>Azure Portal
Od uvedení [nové výstrahy Azure](monitoring-overview-unified-alerts.md), teď můžou uživatelé spravovat všechny typy výstrah na portálu Azure z jednoho umístění a podobným způsobem. Další informace o [pomocí nové výstrahy Azure](monitor-alerts-unified-usage.md).

Navíc můžete uživatelům ideální jejich dotazy v analytické platformě podle výběru v Azure a potom *importovat je pro použití ve výstrahách uložením dotaz*. Postup:
- *Pro službu Application Insights*: Analýza přejděte na portál, ověření dotazu a jeho výsledky. Potom uložte s jedinečným názvem do *sdílené dotazy*.
- *Pro analýzu protokolu*: přejděte na hledání protokolů ověření dotazu a jeho výsledky. Pak použijte uložit s jedinečným názvem do žádné kategorie.

Pak když [vytváření výstrahu protokolu ve výstrahách ](monitor-alerts-unified-usage.md), najdete v části uloženého dotazu uvedené jako typ signálu **protokolu (uložený dotaz)**; jak je znázorněno v následujícím příkladu: ![uložený dotaz importovat do výstrahy](./media/monitor-alerts-unified/AlertsPreviewResourceSelectionLog-new.png)

> [!NOTE]
> Pomocí **protokolu (uložený dotaz)** výsledkem importu výstrah. Proto nebudou všechny změny provést po v Analytics odrážející v pravidla výstrah vyhledávání protokolu a naopak.

### <a name="rest-apis"></a>Rozhraní REST API
Rozhraní API poskytuje pro protokol výstrah se dosáhl standardu RESTful a je přístupný prostřednictvím rozhraní REST API Azure Resource Manager. Proto je přístupná přes prostředí PowerShell, také další možnosti využít rozhraní API.

Podrobnosti a také příklady na pomocí rozhraní REST API, najdete v části:
- [Protokolu analýzy výstraha REST API](../log-analytics/log-analytics-api-alerts.md) – Pokud chcete vytvořit a spravovat pravidla výstrah vyhledávání protokolu pro Azure Log Analytics
- [Azure monitorování naplánované dotazu pravidla REST API](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/) – Pokud chcete vytvořit a spravovat pravidla výstrah vyhledávání protokolu pro službu Azure Application Insights

### <a name="azure-resource-manager-template"></a>Šablona Azure Resource Manageru
Uživatelé můžou použít taky poskytuje flexibilitu [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) k vytváření a aktualizaci prostředků – k vytvoření nebo aktualizaci protokolu výstrahy.

Podrobnosti a také příklady na pomocí šablony Resource Manageru, najdete v části:
- [Uložené hledání a výstrahy správy](monitor-alerts-unified-log-template.md#managing-log-alert-on-log-analytics) protokolu výstrahy založené na Azure Log Analytics
- [Naplánované pravidlo dotazu](monitor-alerts-unified-log-template.md#managing-log-alert-on-application-insights) protokolu výstrahy založené na Azure Application Insights
 

## <a name="next-steps"></a>Další postup
* Pochopení [protokolu výstrahy v Azure](monitor-alerts-unified-log-webhook.md).
* Další informace o nové [Azure výstrahy](monitoring-overview-unified-alerts.md).
* Další informace o [Application Insights](../application-insights/app-insights-analytics.md).
* Další informace o [analýzy protokolů](../log-analytics/log-analytics-overview.md).    
