---
title: Protokolování výstrah v Azure Monitor
description: Aktivovat e-maily, oznámení, volat adresy URL webů (Webhooky) nebo Automation, pokud se pro výstrahy Azure splní podmínky analytického dotazu, které zadáte.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 5/31/2019
ms.subservice: alerts
ms.openlocfilehash: 1d3b3215fe05ef2f57805b5df2b441f360f45df2
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87322342"
---
# <a name="log-alerts-in-azure-monitor"></a>Protokolování výstrah v Azure Monitor

Výstrahy protokolu jsou jedním z typů výstrah, které jsou podporovány v [upozorněních Azure](./alerts-overview.md). Výstrahy protokolu umožňují uživatelům používat platformu Azure Analytics jako základ pro upozorňování.

Výstraha protokolu se skládá z pravidel prohledávání protokolu vytvořených pro [Azure monitor protokolů](../log-query/get-started-portal.md) nebo [Application Insights](../app/cloudservices.md#view-azure-diagnostics-events). Další informace o jeho použití najdete v tématu [vytváření výstrah protokolu v Azure](./alerts-log.md) .

> [!NOTE]
> Oblíbená data protokolu z [protokolů Azure monitor](../log-query/get-started-portal.md) jsou teď dostupná i na platformě metrik v Azure monitor. Pro zobrazení podrobností, [Výstraha metriky pro protokoly](./alerts-metric-logs.md)


## <a name="log-search-alert-rule---definition-and-types"></a>Pravidlo upozornění prohledávání protokolu – definice a typy

Pravidla prohledávání protokolu vytváří služba Azure Alerts pro automatické spouštění zadaných dotazů na protokoly v pravidelných intervalech.  Pokud výsledky dotazu na protokol splňují konkrétní kritéria, vytvoří se záznam upozornění. Pravidlo potom může automaticky spustit jednu nebo více akcí pomocí [skupin akcí](./action-groups.md). Může být vyžadována role [Přispěvatel monitorování Azure](./roles-permissions-security.md) pro vytváření, úpravy a aktualizace výstrah protokolu. společně s přístupem & oprávnění ke spouštění dotazů pro cíl Analytics v pravidle výstrahy nebo dotazu výstrahy. V případě, že uživatel, který vytváří, nemá přístup ke všem cílům Analytics v pravidle výstrahy nebo dotazu výstrahy – vytvoření pravidla může selhat nebo se spustí pravidlo upozornění protokolu s částečnými výsledky.

Pravidla hledání protokolu jsou definována následujícími podrobnostmi:

- **Dotaz protokolu**  Dotaz, který se spustí pokaždé, když se pravidlo upozornění aktivuje.  Záznamy vrácené tímto dotazem slouží k určení, zda má být výstraha aktivována. Analytický dotaz může být určen pro konkrétní Log Analytics pracovní prostor nebo aplikaci Application Insights a dokonce i v rámci [více Log Analytics a Application Insights prostředků](../log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) , k dispozici má uživatel přístup i oprávnění k dotazům pro všechny prostředky. 
    > [!IMPORTANT]
    > podpora [dotazů mezi prostředky](../log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) v protokolových upozorněních pro Application Insights a výstrahy protokolu pro [Log Analytics konfigurovaná pouze pomocí rozhraní scheduledQueryRules API](./alerts-log-api-switch.md) .

    Některé analytické příkazy a kombinace jsou nekompatibilní s použití v protokolových výstrahách. pro další zobrazení podrobností [protokolujte dotazy výstrah v Azure monitor](./alerts-log-query.md).

- **Časové období**.  Určuje časový rozsah dotazu. Dotaz vrátí pouze záznamy vytvořené v tomto rozsahu před aktuálním časem. Časové období omezuje data načítaná pro dotaz protokolu, aby nedocházelo k zneužití a obcházení všech časových příkazů (jako předtím) používaných v dotazu protokolu. <br>*Pokud je například časové období nastavené na 60 minut a dotaz se spustí na 1:15 ODP. k provedení dotazu protokolu se vrátí jenom záznamy vytvořené mezi 12:15 PM a 1:15 PM. Když teď dotaz protokolu používá časový příkaz jako před (7d), dotaz protokolu se spustí jenom pro data mezi 12:15 PM a 1:15 PM, jako kdyby data existovala jenom za posledních 60 minut. A ne sedm dní dat, jak je uvedeno v dotazu protokolu.*

- **Frekvence**.  Určuje, jak často se má dotaz spouštět. Může to být jakákoli hodnota mezi 5 minutami a 24 hodinami. Má být rovno nebo menší než časové období.  Pokud je hodnota větší než časové období, pak dojde k vynechání záznamů rizik.<br>*Představte si třeba dobu 30 minut a frekvenci 60 minut.  Pokud je dotaz spuštěn v 1:00, vrátí záznamy mezi 12:30 a 1:00 PM.  Při příštím spuštění dotazu je 2:00, pokud by vrátil záznamy mezi 1:30 a 2:00.  Žádné záznamy vytvořené mezi 1:00 a 1:30 by nikdy nebyly vyhodnoceny.*

- **Prahová hodnota**.  Výsledky prohledávání protokolu jsou vyhodnocovány, aby bylo možné určit, zda má být vytvořena výstraha.  Prahová hodnota je odlišná u různých typů pravidel upozornění prohledávání protokolu.

Pravidla hledání protokolu pro [Azure monitor protokolů](../log-query/get-started-portal.md) nebo [Application Insights](../app/cloudservices.md#view-azure-diagnostics-events)můžou být dvou typů. Každý z těchto typů je podrobně popsán v následujících oddílech.

- **[Počet výsledků](#number-of-results-alert-rules)** Jediná výstraha vytvořená, když počet záznamů vrácených prohledáváním protokolu překročí zadané číslo.
- **[Měření metriky](#metric-measurement-alert-rules)**.  Výstraha vytvořená pro každý objekt ve výsledcích prohledávání protokolu s hodnotami, které překračují určenou prahovou hodnotu.

Rozdíly mezi typy pravidel výstrah jsou následující.

- *Počet* pravidel upozornění na výsledky vždy vytvoří jedinou výstrahu, zatímco pravidlo upozornění *měření metriky* vytvoří výstrahu pro každý objekt, který překračuje prahovou hodnotu.
- *Počet* pravidel upozornění výsledků vytvoří výstrahu, když je prahová hodnota překročena v jednom okamžiku. Pravidla upozornění *měření metrik* mohou vytvořit výstrahu, pokud je prahová hodnota překročena v určitém časovém intervalu.

### <a name="number-of-results-alert-rules"></a>Počet pravidel upozornění na výsledky

**Počet** pravidel upozornění na výsledky vytvoří jedinou výstrahu, když počet záznamů vrácených vyhledávacím dotazem překročí zadanou prahovou hodnotu. Tento typ pravidla upozornění je ideální pro práci s událostmi, jako jsou protokoly událostí systému Windows, syslog, odpověď WebApp a vlastní protokoly.  Je možné, že budete chtít vytvořit výstrahu, když se vytvoří konkrétní událost chyby nebo když se v určitém časovém období vytvoří více událostí s chybami.

**Prahová**hodnota: prahová hodnota pro počet pravidel upozornění výsledků je větší nebo menší než určitá hodnota.  Pokud počet záznamů vrácených prohledáváním protokolu odpovídá těmto kritériím, vytvoří se výstraha.

Chcete-li upozornit na jednu událost, nastavte počet výsledků na hodnotu větší než 0 a vyhledejte výskyt jedné události, která byla vytvořena od posledního spuštění dotazu. Některé aplikace se mohou zaprotokolovat občasnou chybu, která by neměla nutně vyvolat výstrahu.  Aplikace může například opakovat proces, který vytvořil událost chyby, a potom to úspěšně podruhé.  V takovém případě možná nebudete chtít vytvořit výstrahu, pokud v určitém časovém období nevytvoříte více událostí.  

V některých případech můžete chtít vytvořit výstrahu za absenci události.  Proces může například protokolovat běžné události, aby označoval, že funguje správně.  Pokud se jedna z těchto událostí do konkrétního časového období neprotokoluje, je třeba vytvořit výstrahu.  V takovém případě byste měli nastavit prahovou hodnotu na **menší než 1**.

#### <a name="example-of-number-of-records-type-log-alert"></a>Příklad počtu upozornění protokolu typu záznamů

Vezměte v úvahu scénář, ve kterém se dozvíte, kdy vaše webová aplikace poskytuje reakci na uživatele s vnitřní chybou serveru Code 500 (tj.). Vytvořili byste pravidlo výstrahy s následujícími podrobnostmi:  

- **Dotaz:** žádosti | kde resultCode = = "500"<br>
- **Časové období:** 30 minut<br>
- **Frekvence upozornění:** pět minut<br>
- **Prahová hodnota:** Větší než 0<br>

Výstraha pak spustí dotaz každých 5 minut a 30 minut dat – pro vyhledání libovolného záznamu, kde byl kód výsledku 500. Pokud se najde i jeden takový záznam, aktivuje výstrahu a aktivuje akci nakonfigurovanou.

### <a name="metric-measurement-alert-rules"></a>Pravidla upozornění na měření metrik

Pravidla upozornění **měření metriky** vytvoří výstrahu pro každý objekt v dotazu s hodnotou, která překročí zadanou prahovou hodnotu a zadanou podmínku triggeru. Na rozdíl od počtu pravidel upozornění na **výsledky** fungují pravidla výstrah **měření metriky** , když výsledek analýzy poskytuje časovou řadu. Mají následující odlišné rozdíly od počtu pravidel upozornění **výsledků** .

- **Agregační funkce**: Určuje prováděný výpočet a potenciálně číselné pole, které se má agregovat.  Například funkce **Count ()** vrátí počet záznamů v dotazu, **prům (CounterValue)** vrátí průměr pole CounterValue v průběhu intervalu. Agregační funkce v dotazu musí být pojmenována/volána: AggregatedValue a zadat číselnou hodnotu. 

- **Pole skupiny**: záznam s agregovanou hodnotou se vytvoří pro každou instanci tohoto pole a pro každý z nich je možné vygenerovat výstrahu.  Pokud byste například chtěli vygenerovat výstrahu pro každý počítač, budete ho používat **v počítači**. V případě, že existuje více skupinových polí zadaných v dotazu Alert, může uživatel určit, které pole se má použít k řazení výsledků pomocí parametru **Aggregate on** (metricColumn).

    > [!NOTE]
    > Možnost *Aggregate on* (metricColumn) je k dispozici pro výstrahy protokolu typu měření metrik pro Application Insights a výstrahy protokolu pro [Log Analytics konfigurovaná pouze pomocí rozhraní scheduledQueryRules API](./alerts-log-api-switch.md) .

- **Interval**: definuje časový interval, za který se data agreguje.  Pokud jste například zadali **pět minut**, vytvoří se záznam pro každou instanci pole skupiny agregované v intervalu 5 minut za časové období zadané pro výstrahu.

    > [!NOTE]
    > Funkce bin se musí v dotazu použít k určení intervalu. Když bin () může mít za následek nerovné časové intervaly – výstraha automaticky převede příkaz bin na bin_at příkaz s odpovídajícím časem za běhu, aby se zajistilo, že výsledky budou s pevným bodem. Typ měření metriky výstrahu protokolu je navržený tak, aby fungoval s dotazy, které mají až tři instance příkazu bin ().
    
- **Prahová**hodnota: prahová hodnota pro pravidla upozornění měření metriky je definována agregovanou hodnotou a řadou porušení.  Pokud libovolný datový bod v prohledávání protokolu překročí tuto hodnotu, je považován za porušení.  Pokud počet porušení v nástroji u libovolného objektu ve výsledcích překročí zadanou hodnotu, je pro tento objekt vytvořena výstraha.

*NesmetricColumnná* konfigurace možnosti *agregace on* nebo může způsobit neaktivaci pravidel upozornění. Další informace najdete v tématu [řešení potíží, pokud není správné pravidlo výstrahy měření metriky](./alerts-troubleshoot-log.md#metric-measurement-alert-rule-is-incorrect).

#### <a name="example-of-metric-measurement-type-log-alert"></a>Příklad upozornění protokolu typu měření metriky

Vezměte v úvahu scénář, ve kterém jste chtěli upozornit, pokud některý počítač překročil více než 30 minut o využití procesoru 90% třikrát.  Vytvořili byste pravidlo výstrahy s následujícími podrobnostmi:  

- **Dotaz:** Výkon | kde ObjectName = = "procesor" a CounterName = = "% času procesoru" | sumarizace AggregatedValue = AVG (CounterValue) podle bin (TimeGenerated, 5 min), počítač<br>
- **Časové období:** 30 minut<br>
- **Frekvence upozornění:** pět minut<br>
- **Logika výstrahy & prahová hodnota podmínky:** Větší než 90<br>
- **Pole skupiny (agregace):** Počítač
- **Výstraha aktivační události na základě:** Celkový počet porušení, která jsou větší než 2<br>

Dotaz by vytvořil průměrnou hodnotu pro každý počítač v intervalu 5 minut.  Tento dotaz se spustí každých 5 minut pro data shromážděná během posledních 30 minut. Vzhledem k tomu, že zvolené pole skupiny (agregované) je sloupcem ' Computer ' – AggregatedValue je rozděleno na různé hodnoty ' Computer ' a průměrné využití procesoru pro každý počítač je určeno pro časovou přihrádku 5 minut.  Vzorový výsledek dotazu pro (řekněme) tři počítače, by byl uvedený níže.


|TimeGenerated [UTC] |Počítač  |AggregatedValue  |
|---------|---------|---------|
|20xx-XX-xxT01:00:00Z     |   srv01.contoso.com      |    72     |
|20xx-XX-xxT01:00:00Z     |   srv02.contoso.com      |    91     |
|20xx-XX-xxT01:00:00Z     |   srv03.contoso.com      |    83     |
|...     |   ...      |    ...     |
|20xx-XX-xxT01:30:00Z     |   srv01.contoso.com      |    88     |
|20xx-XX-xxT01:30:00Z     |   srv02.contoso.com      |    84     |
|20xx-XX-xxT01:30:00Z     |   srv03.contoso.com      |    92     |

Pokud se má vykreslit výsledek dotazu, zobrazí se jako.

![Ukázkové výsledky dotazu](media/alerts-unified-log/metrics-measurement-sample-graph.png)

V tomto příkladu vidíme v přihrádkách po dobu 5 minut každého ze tří počítačů – průměrné využití procesoru, které je vypočítáno po dobu 5 minut. Prahová hodnota 90, na kterou se v případě Srv01 nedodržuje jenom jednou v 1:25 přihrádce. V porovnání SRV02 překračuje prahovou hodnotu 90 na 1:10, 1:15 a 1:25 přihrádky; zatímco srv03 překračuje prahovou hodnotu 90 na 1:10, 1:15, 1:20 a 1:30.
Vzhledem k tomu, že výstraha je nakonfigurovaná tak, aby se aktivovala v závislosti na celkovém narušení, je více než dvě, uvidíme, že SRV02 a srv03 odpovídají pouze Proto by se pro SRV02 a srv03 vytvořily samostatné výstrahy, protože v několika časových přihrádkách došlo dvakrát k 90% prahové hodnoty.  Pokud byla *aktivační událost založená na:* parametru nakonfigurované pro možnost *nepřetržitého porušení* , pak se výstraha aktivuje **jenom** pro srv03, protože porušení prahové hodnoty pro tři po sobě jdoucí časová období od 1:10 do 1:20. A **ne** pro SRV02, protože porušila prahovou hodnotu pro dva po sobě jdoucí časové přihrádky od 1:10 do 1:15.

## <a name="log-search-alert-rule---firing-and-state"></a>Pravidlo upozornění prohledávání protokolu – spuštění a stav

Pravidla upozornění na prohledávání protokolu fungují pouze v logice, kterou sestavíte do dotazu. Systém výstrah nemá žádný jiný kontext stavu systému, záměr nebo hlavní příčiny odvozené dotazem. V takovém případě se výstrahy protokolu označují jako stav bez. Podmínky jsou vyhodnoceny jako "TRUE" nebo "FALSE" při každém spuštění.  Výstraha se aktivuje pokaždé, když je vyhodnocení podmínky výstrahy "TRUE", bez ohledu na to, že je aktivována dříve.    

Pojďme se tomuto chování podívat v praxi s praktickým příkladem. Předpokládejme, že máme pravidlo výstrahy protokolu s názvem *Contoso-log-Alert*, které je nakonfigurované tak, jak je uvedeno v příkladu, který je uvedený v poli [počet výsledků výstrah protokolu typu výsledky](#example-of-number-of-records-type-log-alert). Podmínka je vlastní dotaz výstrahy navržený tak, aby v protokolech hledal kód výsledku 500. Pokud se v protokolech najde jeden další kód výsledku pro 500, bude podmínka výstrahy pravdivá. 

V každém intervalu níže systém výstrah Azure vyhodnotí podmínku pro *protokol contoso-log-Alert*.


| Čas    | Počet záznamů vrácených dotazem na hledání protokolu | Protokolovací podmínka Evalution | Výsledek 
| ------- | ----------| ----------| ------- 
| 1:05 ODP. | 0 záznamů | 0 není > 0, takže FALSE |  Výstraha se neaktivuje. Nevolaly se žádné akce.
| 1:10 ODP. | 2 záznamy | 2 > 0, takže TRUE  | Aktivují se výstrahy a volané skupiny akcí. Stav výstrahy aktivní.
| 1:15 ODP. | 5 záznamů | 5 > 0, takže TRUE  | Aktivují se výstrahy a volané skupiny akcí. Stav výstrahy aktivní.
| 1:20 ODP. | 0 záznamů | 0 není > 0, takže FALSE |  Výstraha se neaktivuje. Nevolaly se žádné akce. Stav výstrahy zůstane aktivní.

Předchozí případ použijte jako příklad:

Výstrahy Azure na adrese 1:15 PM nemůžou určit, jestli se podkladové problémy v 1:10 trvají a jestli jsou v záznamech čisté nové chyby nebo se opakují starší chyby v 1:10PM. Dotaz poskytnutý uživatelem může nebo nemusí být přihlédnuto k dřívějším záznamům a systém není známý. Systém upozornění Azure je na straně velmi nejenom na sobě a aktivuje výstrahu a související akce v 1:15./odp.. 

V 1:20. odp. když se v 500 kódu výsledku zobrazí nula záznamů, upozornění Azure nemůže být jisté, že se teď vyřeší kód výsledku 500, který se zobrazuje na 1:10 PM a 1:15 ODP. Neví se, jestli problémy s chybou 500 proběhne ze stejných důvodů znovu. Proto se *Společnost Contoso-log-Alert* nemění na **vyřešených** řídicích panelech Azure Alert a oznámení se neodesílají, protože upozornění je vyřešené. Pouze vy, který zná přesnou podmínku nebo důvod pro logiku vloženou v analytickém dotazu, může [výstrahu označit jako uzavřenou](alerts-managing-alert-states.md) podle potřeby.

## <a name="pricing-and-billing-of-log-alerts"></a>Ceny a fakturace výstrah protokolu

Ceny týkající se výstrah protokolů jsou uvedené na stránce s [cenami Azure monitor](https://azure.microsoft.com/pricing/details/monitor/) . Ve službě Azure Bills se výstrahy protokolu zobrazují jako typ `microsoft.insights/scheduledqueryrules` s:

- Výstrahy protokolu na Application Insights zobrazené s přesným názvem výstrahy spolu se skupinami prostředků a vlastnostmi výstrahy
- Výstrahy protokolu v Log Analytics zobrazeny s přesným názvem výstrahy spolu se skupinami prostředků a vlastnostmi výstrahy; Při vytvoření pomocí [rozhraní scheduledQueryRules API](/rest/api/monitor/scheduledqueryrules)

[Starší verze rozhraní Log Analytics API](./api-alerts.md) mají v rámci Log Analytics uložených hledání a nesprávné [prostředky Azure](../../azure-resource-manager/management/overview.md)nějaké akce a plány výstrah. Pokud tedy chcete povolit účtování pro tyto starší verze protokolů, které jsou vytvořeny pro Log Analytics pomocí Azure Portal **bez** [přepínání do nového rozhraní API](./alerts-log-api-switch.md) nebo prostřednictvím [starší verze Log Analytics API](./api-alerts.md) – `microsoft.insights/scheduledqueryrules` pro účely fakturace v Azure jsou vytvořeny skrytá pravidla pro upozornění na rozhraní. Skrytá pravidla pro upozornění, která byla vytvořena pro fakturaci `microsoft.insights/scheduledqueryrules` , jak je znázorněno `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` společně se skupinou prostředků a vlastnostmi výstrahy.

> [!NOTE]
> Pokud `<, >, %, &, \, ?, /` jsou k dispozici neplatné znaky, jako jsou například, budou nahrazeny `_` názvem pravidla upozornění skryté pseudo, a to i ve službě Azure Bill.

Pokud chcete odebrat skryté prostředky scheduleQueryRules vytvořené pro fakturaci pravidel upozornění pomocí [starších Log Analytics rozhraní API](api-alerts.md), může uživatel provést některou z následujících akcí:

- Uživatel může [přepínat předvolby rozhraní API pro pravidla upozornění v pracovním prostoru Log Analytics](./alerts-log-api-switch.md) , aniž by došlo ke ztrátě svých pravidel nebo monitorování, aby se přesunuly do Azure Resource Manager kompatibilního [rozhraní scheduledQueryRules API](/rest/api/monitor/scheduledqueryrules). Proto Eliminujte nutnost vytvářet pravidla pro vyúčtování pseudo skrytých výstrah.
- Nebo pokud uživatel nechce přepnout na Předvolby rozhraní API, bude muset uživatel **Odstranit** původní plán a akci upozornění pomocí [starší verze Log Analytics API](api-alerts.md) nebo odstranit v [Azure Portal původní pravidlo upozornění protokolu](./alerts-log.md#view--manage-log-alerts-in-azure-portal) .

Kromě toho, že se skryté prostředky scheduleQueryRules vytvořené pro fakturaci pravidel upozornění používají [starší verze rozhraní API Log Analytics](api-alerts.md), všechny operace změny, jako je třeba PUT, selžou. Jako `microsoft.insights/scheduledqueryrules` pravidla typu pseudo jsou pro účely fakturace pravidla upozornění vytvořená pomocí [starší verze rozhraní Log Analytics API](api-alerts.md). Jakákoli změna pravidla výstrahy by se měla provádět pomocí [starší verze rozhraní Log Analytics API](api-alerts.md) (nebo). uživatel může místo toho [Přepnout předvolby rozhraní API pro pravidla upozornění](./alerts-log-api-switch.md) na použití [rozhraní API scheduledQueryRules](/rest/api/monitor/scheduledqueryrules) .

## <a name="next-steps"></a>Další kroky

* Přečtěte si informace o [vytváření v protokolových výstrahách v Azure](./alerts-log.md).
* Pochopení [webhooků v protokolových výstrahách v Azure](alerts-log-webhook.md).
* Přečtěte si o [výstrahách Azure](./alerts-overview.md).
* Přečtěte si další informace o [Application Insights](../log-query/log-query-overview.md).
* Přečtěte si další informace o [Log Analytics](../log-query/log-query-overview.md).

