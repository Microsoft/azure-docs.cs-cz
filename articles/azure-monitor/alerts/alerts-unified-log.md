---
title: Protokolování výstrah v Azure Monitor
description: Aktivovat e-maily, oznámení, volat adresy URL webů (Webhooky) nebo Automation při splnění podmínky dotazu protokolu, který zadáte
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/22/2020
ms.subservice: alerts
ms.openlocfilehash: 88643663c2f14cb7d8883eb1210bdee07b00eece
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100609543"
---
# <a name="log-alerts-in-azure-monitor"></a>Protokolování výstrah v Azure Monitor

## <a name="overview"></a>Přehled

Výstrahy protokolu jsou jedním z typů výstrah, které jsou podporovány v [upozorněních Azure](../platform/alerts-overview.md). Výstrahy protokolu umožňují uživatelům pomocí [log Analyticsho](../log-query/log-analytics-tutorial.md) dotazu vyhodnotit protokoly prostředků každou nastavenou frekvencí a vyvolat výstrahu na základě výsledků. Pravidla mohou aktivovat jednu nebo více akcí pomocí [skupin akcí](../platform/action-groups.md).

> [!NOTE]
> Data protokolu z [Log Analytics pracovního prostoru](../log-query/log-analytics-tutorial.md) lze odeslat do úložiště metrik Azure monitor. Výstrahy metrik mají [různé chování](alerts-metric-overview.md), což může být více žádoucí v závislosti na datech, se kterými pracujete. Informace o tom, co a jak můžete směrovat protokoly do metrik, najdete v tématu [Upozornění na metriky pro protokoly](alerts-metric-logs.md).

> [!NOTE]
> Pro verzi rozhraní API `2020-05-01-preview` a výstrahy protokolu orientované na prostředky se momentálně neúčtují žádné další poplatky.  Ceny pro funkce, které jsou ve verzi Preview, budou v budoucnu ohlášeny a oznámení poskytované před zahájením fakturace. Pokud se rozhodnete dál používat novou verzi rozhraní API a výstrahy protokolu orientované na prostředky po období oznámení, bude se vám účtovat příslušná sazba.

## <a name="prerequisites"></a>Požadavky

Výstrahy protokolu spouštějí dotazy na Log Analytics data. Nejdřív byste měli začít [shromažďovat data protokolu](../platform/resource-logs.md) a dotazovat se na data protokolu. Pomocí [tématu Příklady dotazů na výstrahy](../log-query/example-queries.md) v Log Analytics můžete pochopit, co můžete zjistit nebo začít [psát vlastní dotaz](../log-query/log-analytics-tutorial.md).

[Přispěvatel monitorování Azure](../platform/roles-permissions-security.md) je společná role, která je nutná k vytváření, úpravám a aktualizaci výstrah protokolu. Pro protokoly prostředků je taky potřeba mít přístup & oprávnění k provádění dotazů. Částečný přístup k protokolům prostředků může selhat s dotazy nebo vracet částečné výsledky. [Přečtěte si další informace o konfiguraci upozornění protokolu v Azure](./alerts-log.md).

> [!NOTE]
> Výstrahy protokolu pro Log Analytics používané ke správě pomocí [rozhraní API pro upozornění](../platform/api-alerts.md)na starší verzi Log Analytics. [Přečtěte si další informace o přepnutí na aktuální rozhraní ScheduledQueryRules API](../alerts/alerts-log-api-switch.md).

## <a name="query-evaluation-definition"></a>Definice vyhodnocení dotazu

Definice podmínky pravidel hledání protokolu začíná na:

- Který dotaz se má spustit?
- Jak používat výsledky?

V následujících částech jsou popsány různé parametry, které můžete použít k nastavení výše uvedené logiky.

### <a name="log-query"></a>Dotaz protokolu
[Log Analytics](../log-query/log-analytics-tutorial.md) dotaz použitý k vyhodnocení pravidla Výsledky vrácené tímto dotazem slouží k určení, zda má být výstraha aktivována. Dotaz může být vymezen na:

- Konkrétní prostředek, jako je třeba virtuální počítač.
- Škálování prostředku, jako je například předplatné nebo skupina prostředků.
- Více prostředků s použitím [dotazu mezi prostředky](../log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights). 
 
> [!IMPORTANT]
> Dotazy na výstrahy mají omezení pro zajištění optimálního výkonu a relevance výsledků. [Další informace najdete tady](./alerts-log-query.md).

> [!IMPORTANT]
> Dotaz orientovaný na prostředky a [mezi prostředky](../log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) se podporují jenom pomocí aktuálního rozhraní scheduledQueryRules API. Pokud používáte starší [rozhraní API Log Analytics výstrah](../platform/api-alerts.md), budete muset přepnout. [Další informace o přepínání](./alerts-log-api-switch.md)

#### <a name="query-time-range"></a>Časový rozsah dotazu

V definici podmínky pravidla je nastaven časový rozsah. V pracovních prostorech a Application Insights se říká **perioda**. Ve všech ostatních typech prostředků se nazývá **časový rozsah pro přepis dotazů**.

Podobně jako v Log Analytics časový rozsah omezuje data dotazu na zadaný rozsah. I v případě, že se v dotazu **používá příkaz,** bude platit časový rozsah.

Například dotaz vyhledává 60 minut, pokud je časový rozsah 60 minut, a to i v případě, že text obsahuje hodnotu **před (1d)**. Časový rozsah a filtrování času dotazu se musí shodovat. V ukázkovém případě bude změna   /  **rozsahu času dotazu přepisu** období na jeden den fungovat podle očekávání.

### <a name="measure"></a>Measure

Výstrahy protokolu zapínají protokol na číselné hodnoty, které lze vyhodnotit. Můžete změřit dvě různé věci:

#### <a name="count-of-the-results-table-rows"></a>Počet řádků tabulky výsledků

Počet výsledků je výchozí míra. Ideální pro práci s událostmi, jako jsou protokoly událostí systému Windows, syslog, výjimky aplikací. Aktivuje se v případě, že dojde k záznamu protokolu nebo k němu nedochází v okně vyhodnoceného času.

Výstrahy protokolu fungují nejlépe, když se pokusíte detekovat data v protokolu. Funguje méně dobře, když se pokusíte detekovat chybějící data v protokolech. Například upozornění na prezenční signál virtuálního počítače.

U pracovních prostorů a Application Insights se říká na **základě** výběru **počtu výsledků**. Ve všech ostatních typech prostředků se nazývá **Measure** s **řádky tabulky** výběru.

> [!NOTE]
> Vzhledem k tomu, že protokoly jsou částečně strukturovaná data, jsou ve své podstatě více latentních, než je metrika, při pokusu o detekci chybějících dat v protokolech může docházet k výpadkům a měli byste zvážit použití [výstrah metrik](alerts-metric-overview.md). Data můžete do úložiště metrik odesílat z protokolů pomocí [výstrah metrik pro protokoly](alerts-metric-logs.md).

##### <a name="example-of-results-table-rows-count-use-case"></a>Příklad případu použití počtu řádků tabulky výsledků

Chcete zjistit, kdy vaše aplikace odpověděla s kódem chyby 500 (interní chyba serveru). Vytvořili byste pravidlo výstrahy s následujícími podrobnostmi:

- **Zadávání** 

```Kusto
requests
| where resultCode == "500"
```

- **Časové období/členitost agregace:** 15 minut
- **Frekvence upozornění:** 15 minut
- **Prahová hodnota:** Větší než 0

Pravidla výstrah se pak monitorují pro všechny požadavky končící kódem chyby 500. Dotaz se spouští každých 15 minut za posledních 15 minut. Pokud je nalezen i jeden záznam, aktivuje výstrahu a aktivuje nakonfigurované akce.

#### <a name="calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value"></a>Výpočet míry na základě číselného sloupce (například hodnota čítače CPU)

U pracovních prostorů a Application Insights se říká na **základě** **měření metriky** výběru. Ve všech ostatních typech prostředků se tento název nazývá **míra** s výběrem libovolného číselného sloupce s čísly.

### <a name="aggregation-type"></a>Typ agregace

Výpočet, který je proveden na více záznamech pro agregaci na jednu číselnou hodnotu. Příklad:
- **Count** vrátí počet záznamů v dotazu.
- Funkce **Average** Vrátí průměrnou hodnotu definování [**členitosti**](#aggregation-granularity) sloupce měr.

V pracovních prostorech a Application Insights je podporováno pouze v typu míry **měření metrik** . Výsledek dotazu musí obsahovat sloupec s názvem AggregatedValue, který poskytuje číselnou hodnotu po uživatelsky definované agregaci. Ve všech ostatních typech prostředků je **typ agregace** vybraný z pole s tímto názvem.

### <a name="aggregation-granularity"></a>Členitost agregace

Určuje interval, který se použije k agregaci několika záznamů na jednu číselnou hodnotu. Pokud jste například zadali **5 minut**, záznamy by byly seskupeny o 5 minut pomocí zadaného **typu agregace** .

V pracovních prostorech a Application Insights je podporováno pouze v typu míry **měření metrik** . Výsledek dotazu musí obsahovat [bin ()](/azure/kusto/query/binfunction) , který nastavuje interval ve výsledcích dotazu. U všech ostatních typů prostředků se pole, které řídí toto nastavení, nazývá **členitost agregace**.

> [!NOTE]
> Funkce AS [bin ()](/azure/kusto/query/binfunction) může mít za následek nerovnoměrné časové intervaly, služba Alert automaticky převede funkci [bin ()](/azure/kusto/query/binfunction) na [bin_at ()](/azure/kusto/query/binatfunction) s odpovídajícím časem za běhu, aby se zajistilo, že výsledky budou s pevným bodem.

### <a name="split-by-alert-dimensions"></a>Rozdělit podle dimenzí výstrahy

Oddělte výstrahy podle číselných nebo řetězcových sloupců na samostatné výstrahy seskupením do jedinečných kombinací. Když vytváříte výstrahy orientované na prostředky ve velkém měřítku (předplatné nebo obor skupiny prostředků), můžete rozdělit podle sloupce Azure Resource ID. Rozdělením do sloupce Azure Resource ID se změní cíl výstrahy na zadaný prostředek.

Rozdělení podle sloupce ID prostředku Azure se doporučuje, když chcete monitorovat stejnou podmínku u více prostředků Azure. Například monitorování všech virtuálních počítačů s využitím procesoru nad 80%. Můžete se také rozhodnout, že nebudete rozděleni, pokud chcete podmínku na více prostředků v oboru, jako je například monitorování, že nejméně pět počítačů v oboru skupiny prostředků má využití CPU více než 80%.

V pracovních prostorech a Application Insights je podporováno pouze v typu míry **měření metrik** . Pole se nazývá **agregace**. Je omezeno na tři sloupce. Více než tři skupiny podle sloupců v dotazu by mohly vést k neočekávaným výsledkům. Ve všech ostatních typech prostředků je nakonfigurována v části **rozdělit podle dimenzí** podmínky (omezeno na šest rozdělení).

#### <a name="example-of-splitting-by-alert-dimensions"></a>Příklad rozdělení podle dimenzí výstrah

Například chcete monitorovat chyby pro více virtuálních počítačů, na kterých běží web nebo aplikace v konkrétní skupině prostředků. Pomocí pravidla výstrahy protokolu můžete postupovat takto:

- **Zadávání** 

    ```Kusto
    // Reported errors
    union Event, Syslog // Event table stores Windows event records, Syslog stores Linux records
    | where EventLevelName == "Error" // EventLevelName is used in the Event (Windows) records
    or SeverityLevel== "err" // SeverityLevel is used in Syslog (Linux) records
    ```

    Při použití pracovních prostorů a Application Insights s logikou výstrah **měření metriky** je nutné do textu dotazu přidat tento řádek:

    ```Kusto
    | summarize AggregatedValue = count() by Computer, bin(TimeGenerated, 15m)
    ```

- **Sloupec ID prostředku:** _ResourceId (rozdělení podle sloupce ID prostředku v pravidlech výstrah je k dispozici pouze pro odběry a skupiny prostředků v současnosti)
- **Dimenze/agregované na:**
  - Computer = VM1, VM2 (hodnoty filtrování v definici pravidel výstrah nejsou aktuálně k dispozici pro pracovní prostory a Application Insights. Filtrovat v textu dotazu.)
- **Časové období/členitost agregace:** 15 minut
- **Frekvence upozornění:** 15 minut
- **Prahová hodnota:** Větší než 0

Toto pravidlo monitoruje, zda v posledních 15 minutách nějakého virtuálního počítače obsahovalo chybové události. Každý virtuální počítač se monitoruje samostatně a spustí akce jednotlivě.

> [!NOTE]
> Dimenze rozdělené podle výstrah jsou k dispozici pouze pro aktuální rozhraní scheduledQueryRules API. Pokud používáte starší [rozhraní API Log Analytics výstrah](../platform/api-alerts.md), budete muset přepnout. [Přečtěte si další informace o přepínání](./alerts-log-api-switch.md). Upozorňování na střed prostředků ve velkém měřítku se podporuje jenom ve verzi rozhraní API `2020-05-01-preview` a výše.

## <a name="alert-logic-definition"></a>Definice logiky výstrah

Po definování dotazu, který se má spustit a vyhodnotit výsledky, je nutné definovat logiku upozorňování a případ, kdy se akce aktivují. V následujících částech najdete popis různých parametrů, které můžete použít:

### <a name="threshold-and-operator"></a>Prahová hodnota a operátor

Výsledky dotazu jsou transformovány na číslo, které je porovnáno s prahovou hodnotou a operátorem.

### <a name="frequency"></a>Frekvence

Interval, ve kterém se dotaz spustí. Dá se nastavit z 5 minut na jeden den. Musí být rovno nebo menší než záznamy o [době, kdy je časový rozsah dotazu](#query-time-range) neúspěšný.

Například pokud nastavíte časové období na 30 minut a četnost na 1 hodinu.  Pokud se dotaz spustí v 00:00, vrátí záznamy mezi 23:30 a 00:00. Při příštím spuštění dotazu je 01:00, který by vrátil záznamy mezi 00:30 a 01:00. Žádné záznamy vytvořené mezi 00:00 a 00:30 by nikdy nebyly vyhodnoceny.

### <a name="number-of-violations-to-trigger-alert"></a>Počet porušení pro aktivaci výstrahy

Můžete zadat období vyhodnocování výstrah a počet selhání potřebných ke spuštění výstrahy. Umožňuje vám lépe definovat čas dopadu na aktivaci výstrahy. 

Pokud je například vlastnost [**členitosti**](#aggregation-granularity) pravidla definována jako "5 minut", můžete spustit výstrahu pouze v případě, že došlo k třem selháním (15 minut) poslední hodiny. Toto nastavení je definované vašimi podnikovými zásadami pro aplikace.

## <a name="state-and-resolving-alerts"></a>Stav a řešení výstrah

Výstrahy protokolu jsou bezstavové. Výstrahy se aktivují při každém splnění podmínky, a to i v případě, že jste ji aktivovali dřív. Aktivované výstrahy se nevyřešily. [Výstrahu můžete označit jako uzavřenou](../alerts/alerts-managing-alert-states.md). Můžete také ztlumit akce a zabránit tak tomu, aby se aktivovaly po dobu, kdy se vyvolalo pravidlo výstrahy.

V pracovních prostorech a Application Insights se říká **potlačit výstrahy**. Ve všech ostatních typech prostředků se nazývá **ztlumení akcí**. 

Podívejte se na příklad vyhodnocení výstrahy:

| Čas    | Vyhodnocení stavu protokolu | Výsledek 
| ------- | ----------| ----------| ------- 
| 00:05 | FALSE | Výstraha se neaktivuje. Nevolaly se žádné akce.
| 00:10 | TRUE  | Aktivují se výstrahy a volané skupiny akcí. Nový stav výstrahy aktivní.
| 00:15 | TRUE  | Aktivují se výstrahy a volané skupiny akcí. Nový stav výstrahy aktivní.
| 00:20 | FALSE | Výstraha se neaktivuje. Nevolaly se žádné akce. Stav upozornění zkontrolují zůstane aktivní.

## <a name="pricing-and-billing-of-log-alerts"></a>Ceny a fakturace výstrah protokolu

Informace o cenách najdete na [stránce s cenami Azure monitor](https://azure.microsoft.com/pricing/details/monitor/). Výstrahy protokolu jsou uvedeny v části poskytovatel prostředků `microsoft.insights/scheduledqueryrules` pomocí:

- Výstrahy protokolu v Application Insights zobrazují s přesným názvem prostředku spolu s vlastnostmi skupiny prostředků a výstrahy.
- Výstrahy protokolu na Log Analytics zobrazené s přesným názvem prostředku spolu s vlastnostmi skupiny prostředků a výstrahy; Při vytváření pomocí [rozhraní scheduledQueryRules API](/rest/api/monitor/scheduledqueryrules).
- Výstrahy protokolu vytvořené ze [starších rozhraní Log Analytics API](../platform/api-alerts.md) nejsou sledovány pro [prostředky Azure](../../azure-resource-manager/management/overview.md) a nemají vykonatelné jedinečné názvy prostředků. Tyto výstrahy se pořád vytváří `microsoft.insights/scheduledqueryrules` jako skryté prostředky, které mají tuto strukturu názvů prostředků `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` . Výstrahy protokolu na starší verzi rozhraní API se zobrazují s výše skrytým názvem prostředku společně se skupinami prostředků a vlastnostmi výstrahy.

> [!NOTE]
> Nepodporované znaky prostředku, jako například, se `<, >, %, &, \, ?, /` nahrazují `_` názvy skrytých prostředků, které se projeví také v informacích o fakturaci.

> [!NOTE]
> Výstrahy protokolu pro Log Analytics používané ke správě pomocí starší verze [rozhraní API Log Analytics výstrah](../platform/api-alerts.md) a starších šablon [Log Analytics uložených hledání a upozornění](../insights/solutions.md). [Přečtěte si další informace o přepnutí na aktuální rozhraní ScheduledQueryRules API](../alerts/alerts-log-api-switch.md). Veškerá Správa pravidel výstrah by se měla provádět pomocí [starších Log Analytics rozhraní API](../platform/api-alerts.md) , dokud se nerozhodnete přepnout a nebudete moct skryté prostředky používat.

## <a name="next-steps"></a>Další kroky

* Přečtěte si informace o [vytváření v protokolových výstrahách v Azure](./alerts-log.md).
* Pochopení [webhooků v protokolových výstrahách v Azure](../alerts/alerts-log-webhook.md).
* Přečtěte si o [výstrahách Azure](../platform/alerts-overview.md).
* Přečtěte si další informace o [Log Analytics](../log-query/log-query-overview.md).
