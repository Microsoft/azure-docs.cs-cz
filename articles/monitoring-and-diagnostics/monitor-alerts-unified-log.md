---
title: Upozornění protokolů ve službě Azure Monitor
description: Aktivační událost e-mailů, oznámení, volání adresy URL webů (webhooky), nebo automatizace při splnění zadané podmínky analytického dotazu pro Azure Alerts.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: f36f05789424cfd3213525dd501333f852a0d9c2
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2018
ms.locfileid: "38971716"
---
# <a name="log-alerts-in-azure-monitor---alerts"></a>Upozornění protokolů ve službě Azure Monitor – oznámení 
Tento článek obsahuje podrobnosti o upozornění protokolů jsou jedním z typů výstrah, které jsou podporovány v rámci nové [Azure Alerts](monitoring-overview-unified-alerts.md) a umožnit uživatelům použít analytické platformy Azure jako základ pro generování výstrah... Podrobnosti upozornění metriky protokolů, najdete v [upozornění metriky v téměř reálném čase](monitoring-near-real-time-metric-alerts.md)


Upozornění protokolu se skládá z prohledávání protokolů pravidel vytvořených pro [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) nebo [Application Insights](../application-insights/app-insights-cloudservices.md#view-azure-diagnostic-events)


## <a name="log-search-alert-rule---definition-and-types"></a>Hledání pravidel upozornění protokolů – definice a typy

Pravidla vyhledávání protokolu se vytvoří pomocí výstrah služby Azure pro automatické spouštění dotazů zadaný protokol v pravidelných intervalech.  Pokud výsledky dotazu protokolů splňují konkrétní kritéria, se vytvoří záznam upozornění. Pravidlo pak může automaticky spustit jednu nebo více akcí pomocí [skupiny akcí](monitoring-action-groups.md). 

Pravidla vyhledávání protokolů jsou definovány následující podrobnosti:
- **Protokolování dotazu**.  Dotaz, který se spustí pokaždé, když se pravidlo upozornění aktivuje.  Vrácené tímto dotazem záznamy se používají k určení, zda je vytvořena výstraha. *Azure Application Insights* dotaz může také zahrnovat [volání mezi aplikacemi](https://dev.applicationinsights.io/ai/documentation/2-Using-the-API/CrossResourceQuery), pokud uživatel nemá přístupová práva do externí aplikace. 

    > [!IMPORTANT]
    > Podpora sady [pro různé aplikace dotaz pro Application Insights](https://dev.applicationinsights.io/ai/documentation/2-Using-the-API/CrossResourceQuery) je ve verzi preview – funkce omezena na použití s 2 nebo více aplikací a činnost koncového uživatele se může změnit. Využití [pro různé pracovní prostor dotazu](https://dev.loganalytics.io/oms/documentation/3-Using-the-API/CrossResourceQuery) a [napříč prostředky dotazů ke službě Log Analytics](../log-analytics/log-analytics-cross-workspace-search.md) je aktuálně **nepodporuje** ve výstrahách Azure.

- **Časové období**.  Určuje časový rozsah dotazu. Dotaz vrátí pouze záznamy vytvořené v tomto rozsahu před aktuálním časem. Časové období omezuje data načtena dotaz protokolu, aby se zabránilo zneužití a připojení, vyřešíte nepřetržitou jakýkoli příkaz času (například před) používaných v dotazu protokolu. <br>*Například toto časové období je nastavený na 60 minut a spuštění dotazu v 13:15, se vrátí pouze záznamy vytvořené mezi 12:15 PM a 1:15 PM do spuštění protokolu dotazu. Když teď dotaz protokolu používá čas příkazu, jako jsou před (7d), protokolu by spustí dotaz pouze pro data mezi 12:15 PM a 1:15 PM - jakoby dat existuje pouze posledních 60 minut. A ne po dobu sedmi dní dat, jak je uvedeno v protokolu dotazu.*
- **Frekvence**.  Určuje, jak často se má spustit dotaz. Může být libovolná hodnota mezi 5 minutami a 24 hodinami. By měla být větší nebo menší než toto časové období.  Pokud je hodnota větší než časové období, riskujete záznamy se vynechalo.<br>*Představte si třeba po dobu 30 minut a frekvenci 60 minut.  Pokud je dotaz spuštěn v 1:00, vrátí záznamy 12:30 až 1:00 PM.  Při příštím spuštění dotazu by je 2:00, kdy vracel záznamy 1:30 až 2:00.  Záznamy, které vytvoří až 1:00, 1:30 by nikdy být vyhodnocena.*
- **Prahová hodnota**.  K určení, zda má být vytvořena výstraha se vyhodnocují výsledky prohledávání protokolů.  Prahová hodnota se liší pro různé typy pravidel upozornění na prohledávání protokolu.

Pravidla vyhledávání protokolů už to pro [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) nebo [Application Insights](../application-insights/app-insights-cloudservices.md#view-azure-diagnostic-events), mohou být dvou typů. Každý z těchto typů je podrobně popsány v následující části.

- **[Počet výsledků](#number-of-results-alert-rules)**. Jediná výstraha vytvořena, když počet záznamů vrácených prohledávání protokolů překročí zadaný počet.
- **[Metriky měření](#metric-measurement-alert-rules)**.  Upozornění bylo vytvořeno pro každý objekt ve výsledcích prohledávání protokolů s hodnotami, které překročí zadanou prahovou hodnotu.

Rozdíly mezi typy pravidla upozornění jsou následující.

- *Počet výsledků* pravidla upozornění vždy vytvoří jednu výstrahu, nějakou *měření metriky* pravidlo výstrahy vytvoří upozornění pro každý objekt, který překračuje prahovou hodnotu.
- *Počet výsledků* pravidla upozornění vytvořit upozornění, když je prahová hodnota překročena jednorázově. *Metriky měření* pravidla upozornění může vytvořit upozornění, pokud je překročena prahová hodnota počtu dobu v konkrétním časovém intervalu.

### <a name="number-of-results-alert-rules"></a>Počet pravidel upozornění výsledky
**Počet výsledků** pravidla upozornění vytvoření jedinou výstrahu, když počet záznamů vrácených dotazem vyhledávání překročí zadanou prahovou hodnotu. Tento typ upozornění pravidla je ideální pro práci s událostmi, jako jsou protokoly událostí Windows, Syslog, odpovědi webové aplikace a vlastní protokoly.  Chcete vytvořit upozornění, když se vytvoří událost konkrétní chyby nebo při vytváření více chybové události v konkrétním časovém období.

**Prahová hodnota**: prahová hodnota pro počet pravidel upozornění výsledků je větší nebo menší než jednu konkrétní hodnotu.  Pokud počet záznamů vrácených prohledávání protokolů, které splňují tato kritéria, vytvoří se výstraha.

Výstrahy v rámci jediné události, nastavte počet výsledků na hodnotu větší než 0 a zkontrolujte výskyt jedné události, který byl vytvořen od posledního spuštění dotazu. Některé aplikace může zaznamenat občasné chybu, která by neměla být nutně vygenerovat výstrahu.  Aplikace může například opakujte proces, který vytvoří událost chyby a potom příště se úspěšně provedla.  V takovém případě nemusí chtít vytvořit výstrahu, pokud více událostí jsou vytvořené v konkrétním časovém období.  

V některých případech můžete chtít vytvořit výstrahu chybí událost.  Například může proces protokolu běžné události k označení, že funguje správně.  Pokud není některá z těchto událostí protokolu v konkrétním časovém období, je třeba vytvořit výstrahu.  V takovém případě můžete nastavit mez na **menší než 1**.

#### <a name="example"></a>Příklad:
Představte si třeba situaci, ve kterém chcete vědět, když vaše webové aplikace poskytuje odpověď pro uživatele, kteří kód 500 (to znamená) vnitřní chyba serveru. Vytvoříte pravidlo upozornění s následujícími podrobnostmi:  
- **Dotaz:** požadavky | kde kód výsledku == "500"<br>
- **Časové období:** 30 minut<br>
- **Četnosti upozornění:** pěti minut<br>
- **Prahová hodnota:** skvělé než 0.<br>

Výstraha by spusťte dotaz každých 5 minut, 30 minut dat – vás pod rouškou u jakéhokoli záznamu, kde byl výsledný kód 500. Pokud ještě jeden takový záznam najde, aktivuje se upozornění a aktivuje akci nakonfigurovanou.

### <a name="metric-measurement-alert-rules"></a>Pravidla upozornění metriky měření

- **Metriky měření** pravidla upozornění vytvořit upozornění pro každý objekt v dotazu s hodnotou, která překročí zadanou prahovou hodnotu.  Mají odlišné následující rozdíly proti **počet výsledků** pravidla upozornění.
- **Agregační funkce**: Určuje, která se provede výpočet a potenciálně číselná pole agregovat.  Například **count()** vrátí počet záznamů v dotazu **avg(CounterValue)** vrací průměr pole CounterValue v intervalu. V dotazu je agregační funkce musí být s názvem/volá: AggregatedValue a poskytnout číselnou hodnotu. 
- **Pole Seskupit**: pro každou instanci tohoto pole se vytvoří záznam s agregovanou hodnotu a pro každý může být vygenerována výstraha.  Například pokud chcete generovat výstrahy pro každý počítač, můžete využít **podle počítače** 

    > [!NOTE]
    > Pro měření metriky pravidla výstrah, které jsou založeny na Application Insights můžete zadat pole pro seskupení dat. Chcete-li to provést, použijte **agregační na** možnost v definici pravidla.   
    
- **Interval**: definuje časový interval, po kterou se data agregují.  Například, pokud jste zadali **pět minut**, by se vytvořily záznam pro každou instanci tohoto pole Skupina v 5minutových intervalech agregován v časovém období zadaný pro oznámení.

    > [!NOTE]
    > Funkce Bin musí použít v dotazu k určení intervalu. Jak bin() může vést k nerovnost časové intervaly – výstraha se automaticky převede bin příkaz bin_at příkazu s odpovídajícím časem za běhu, zajistit výsledky s pevnému bodu
    
- **Prahová hodnota**: prahová hodnota pro pravidla upozornění metriky měření je definován tak, že agregované hodnoty a celou řadu.  Pokud libovolný datový bod v prohledávání protokolu překročí tuto hodnotu, se považuje za porušení zabezpečení.  Pokud počet narušení v pro libovolný objekt ve výsledcích překročí zadanou hodnotu, je vytvořit výstrahu pro daný objekt.

#### <a name="example"></a>Příklad:
Představte si třeba situaci, kde chcete výstrahu Pokud jakýkoli počítač překročí 90 % třikrát více než 30 minut využití procesoru.  Vytvoříte pravidlo upozornění s následujícími podrobnostmi:  

- **Dotaz:** výkonu | kde ObjectName == "Procesor pro" a hodnota CounterName == "% Processor Time" | summarize AggregatedValue = avg(CounterValue) podle bin (TimeGenerated, 5 m), počítač<br>
- **Časové období:** 30 minut<br>
- **Četnosti upozornění:** pěti minut<br>
- **Agregovat hodnota:** víc než 90<br>
- **Aktivovat upozornění na základě:** celkem poruší větší než 2<br>

Dotaz by vytvořit průměrnou hodnotu pro každý počítač v intervalech 5 minut.  Tento dotaz se spustí každých 5 minut, než se data shromážděná za předchozí 30 minut.  Ukázková data se zobrazí pod pro tři počítače.

![Ukázkové výsledky dotazu](./media/monitor-alerts-unified/metrics-measurement-sample-graph.png)

V tomto příkladu by se vytvořily samostatné výstrahy pro srv02 a srv03, protože, u nichž nebyla dodržena prahová hodnota 90 % 3krát za časové období.  Pokud **aktivovat upozornění na základě:** byly změněny na **za sebou** výstraha by vytvořeny pouze pro srv03, od nichž nebyla dodržena prahová hodnota pro tři po sobě jdoucích vzorků.


## <a name="log-search-alert-rule---creation-and-modification"></a>Hledání pravidel upozornění protokolů – vytváření a úpravy

Upozornění protokolu, stejně jako jeho consisting pravidlo upozornění vyhledávání protokolu můžete zobrazit, vytvořit nebo změnit:
- Azure Portal
- REST API (včetně přes PowerShell)
- Šablony Azure Resource Manageru

### <a name="azure-portal"></a>Azure Portal
Od uvedení [nové výstrahy Azure](monitoring-overview-unified-alerts.md), teď uživatelé mohou spravovat všechny typy upozornění na webu Azure portal z jednoho místa a podobným způsobem. Další informace o [pomocí nové výstrahy Azure](monitor-alerts-unified-usage.md).

Navíc uživatelé můžou zdokonalujete své dotazy v Analytics platform podle výběru v Azure a pak *naimportovat pro použití ve výstrahách uložením dotaz*. Postup:
- *Pro službu Application Insights*: portál Analytics přejít k ověření dotazů a jeho výsledky. Uložte s jedinečným názvem do *sdílené dotazy*.
- *Ke službě Log Analytics*: přejděte k prohledávání protokolů ověření dotazů a jeho výsledky. Pak použijte uložit s jedinečným názvem do žádné kategorie.

Následně když [vytvoření upozornění protokolu ve výstrahách ](monitor-alerts-unified-usage.md), najdete v článku uloženého dotazu uvedené jako typ signálu **protokolu (uložený dotaz)**, jak je znázorněno v následujícím příkladu: ![uložený dotaz importovat výstrahy](./media/monitor-alerts-unified/AlertsPreviewResourceSelectionLog-new.png)

> [!NOTE]
> Pomocí **protokolu (uložený dotaz)** výsledkem importu na výstrahy. Proto všechny změny provedené po v Analytics se reflektivní pravidel upozornění na prohledávání protokolu a naopak.

### <a name="rest-apis"></a>Rozhraní REST API
Rozhraní API k dispozici pro protokol výstrah jsou RESTful a je přístupný prostřednictvím rozhraní REST API Azure Resource Manageru. Proto je možný přes PowerShell, také další možnosti využít rozhraní API.

Podrobnosti o příklady použití rozhraní REST API, tak prosím najdete:
- [REST API pro analýzu upozornění protokolů](../log-analytics/log-analytics-api-alerts.md) – k vytváření a správě pravidel upozornění na prohledávání protokolu pro Azure Log Analytics
- [Azure Monitor naplánované dotaz pravidla rozhraní REST API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) – k vytváření a správě pravidel upozornění na prohledávání protokolu pro službu Azure Application Insights

### <a name="azure-resource-manager-template"></a>Šablona Azure Resource Manageru
Uživatelé můžou používat také flexibilitu, kterou [Azure Resource Manageru](../azure-resource-manager/resource-group-overview.md) k vytvoření a aktualizaci prostředků – pro vytváření nebo aktualizaci upozornění protokolu.

Podrobnosti o stejně jako příklady pomocí šablon Resource Manageru kdybyste najdete:
- [Uložení hledání a oznámení správy](monitor-alerts-unified-log-template.md#managing-log-alert-on-log-analytics) pro upozornění protokolů, které jsou založené na Azure Log Analytics
- [Naplánované pravidlo dotazu](monitor-alerts-unified-log-template.md#managing-log-alert-on-application-insights) pro upozornění protokolů, které jsou založené na Azure Application Insights
 

## <a name="next-steps"></a>Další postup
* Vysvětlení [upozornění protokolů ve službě Azure](monitor-alerts-unified-log-webhook.md).
* Další informace o novém [Azure Alerts](monitoring-overview-unified-alerts.md).
* Další informace o [Application Insights](../application-insights/app-insights-analytics.md).
* Další informace o [Log Analytics](../log-analytics/log-analytics-overview.md).    
