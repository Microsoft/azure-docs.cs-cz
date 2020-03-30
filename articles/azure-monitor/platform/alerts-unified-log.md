---
title: Výstrahy protokolu v Azure Monitoru
description: Aktivace e-mailů, oznámení, adres URL volání na weby (webhooky) nebo automatizace, když jsou pro azure alerts splněny zadané podmínky analytického dotazu.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 5/31/2019
ms.subservice: alerts
ms.openlocfilehash: a6abf4665c27771497037da35f85bb540e6e904e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665217"
---
# <a name="log-alerts-in-azure-monitor"></a>Výstrahy protokolu v Azure Monitoru

Tento článek obsahuje podrobnosti o výstrahách protokolu jsou jedním z typů výstrah podporovaných v rámci [výstrah Azure](../../azure-monitor/platform/alerts-overview.md) a umožňují uživatelům používat analytickou platformu Azure jako základ pro výstrahy.

Výstraha protokolu se skládá z pravidel vyhledávání protokolů vytvořených pro [protokoly monitorování Azure](../../azure-monitor/learn/tutorial-viewdata.md) nebo [přehledy aplikací](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events). Další informace o jeho využití najdete [v tématu vytváření upozornění protokolů v Azure.](../../azure-monitor/platform/alerts-log.md)

> [!NOTE]
> Oblíbená data protokolů z [protokolů monitorování Azure](../../azure-monitor/learn/tutorial-viewdata.md) jsou teď dostupná taky na metrické platformě ve Službě Azure Monitor. Zobrazení [podrobností, Upozornění na metriky pro protokoly](../../azure-monitor/platform/alerts-metric-logs.md)


## <a name="log-search-alert-rule---definition-and-types"></a>Pravidlo výstrahy vyhledávání protokolů – definice a typy

Pravidla prohledávání protokolu vytváří služba Azure Alerts pro automatické spouštění zadaných dotazů na protokoly v pravidelných intervalech.  Pokud výsledky dotazu na protokol splňují konkrétní kritéria, vytvoří se záznam upozornění. Pravidlo potom může automaticky spustit jednu nebo více akcí pomocí [skupin akcí](../../azure-monitor/platform/action-groups.md). Může být vyžadována role [přispěvatele monitorování Azure](../../azure-monitor/platform/roles-permissions-security.md) pro vytváření, úpravy a aktualizace výstrah protokolu; spolu s přístup& práva k provádění dotazu pro cíle analýzy v pravidle výstrahy nebo dotazu výstrahy. V případě, že uživatel, který vytváří, nemá přístup ke všem cílům analýzy v pravidle výstrahy nebo dotazu výstrahy – vytvoření pravidla může selhat nebo pravidlo výstrahprotokolu bude spuštěno s částečnými výsledky.

Pravidla vyhledávání protokolu jsou definována následujícími podrobnostmi:

- **Protokolovat dotaz**.  Dotaz, který se spustí pokaždé, když se pravidlo upozornění aktivuje.  Záznamy vrácené tímto dotazem se používají k určení, zda má být výstraha spuštěna. Analytický dotaz může být pro konkrétní pracovní prostor Log Analytics nebo aplikaci Application Insights a dokonce i rozpětí pro [více prostředků Log Analytics a Application Insights](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) za předpokladu, že uživatel má přístup a práva na dotazy ke všem prostředkům. 
    > [!IMPORTANT]
    > Podpora [dotazů mezi prostředky](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) v výstrahách protokolu pro application insights a výstrahy protokolu pro [analýzu protokolů nakonfigurované pouze pomocí rozhraní SCHEDULEDQueryRules API.](../../azure-monitor/platform/alerts-log-api-switch.md)

    Některé analytické příkazy a kombinace jsou nekompatibilní s použitím v výstrahách protokolu; další podrobnosti pro zobrazení [protokolu dotazů upozornění v Azure Monitor](../../azure-monitor/platform/alerts-log-query.md).

- **Časové období**.  Určuje časový rozsah dotazu. Dotaz vrátí pouze záznamy vytvořené v tomto rozsahu před aktuálním časem. Časové období omezuje data načtená pro dotaz protokolu, aby se zabránilo zneužití, a obchází jakýkoli příkaz time (jako před) použitý v dotazu protokolu. <br>*Například Pokud je časové období nastaveno na 60 minut a dotaz je spuštěn v 1:15, jsou vráceny pouze záznamy vytvořené mezi 12:15 pm a 1:15 PM ke spuštění dotazu protokolu. Nyní, pokud dotaz protokolu používá time command like ago (7d), dotaz protokolu by být spuštěn pouze pro data mezi 12:15 PM a 1:15 PM - jako kdyby data existuje pouze za posledních 60 minut. A ne pro sedm dní dat, jak je uvedeno v protokolu dotazu.*

- **Frekvence**.  Určuje, jak často má být dotaz spuštěn. Může být libovolná hodnota mezi 5 minut a 24 hodin. By měla být rovna nebo menší než časové období.  Pokud je hodnota větší než časové období, riskujete, že záznamy budou vynechány.<br>*Zvažte například časové období 30 minut a frekvenci 60 minut.  Pokud je dotaz spuštěn v 1:00, vrátí záznamy mezi 12:30 a 1:00 PM.  Při příštím spuštění dotazu je 2:00, kdy by vrátit záznamy mezi 1:30 a 2:00.  Všechny záznamy vytvořené mezi 1:00 a 1:30 by nikdy být vyhodnoceny.*

- **Prahová hodnota**.  Výsledky hledání protokolu jsou vyhodnoceny k určení, zda má být vytvořena výstraha.  Prahová hodnota se liší pro různé typy pravidel výstrah hledání protokolu.

Pravidla vyhledávání protokolů, ať už pro [protokoly monitorování Azure](../../azure-monitor/learn/tutorial-viewdata.md) nebo [Application Insights](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events), může být dvou typů. Každý z těchto typů je podrobně popsán v následujících částech.

- **[Počet výsledků](#number-of-results-alert-rules)**. Jedna výstraha vytvořená v případě, že počet záznamů vrácených hledáním protokolu překročí zadané číslo.
- **[Metrické měření](#metric-measurement-alert-rules)**.  Výstraha vytvořená pro každý objekt ve výsledcích hledání protokolu s hodnotami, které překračují zadanou prahovou hodnotu.

Rozdíly mezi typy pravidel výstrah y jsou následující.

- *Počet* pravidel upozornění na výsledky vždy vytvoří jednu výstrahu, zatímco pravidlo upozornění *metriky měření* vytvoří výstrahu pro každý objekt, který překročí prahovou hodnotu.
- *Počet* pravidel upozornění na výsledky vytvoří výstrahu při překročení prahové hodnoty jednou. Pravidla upozornění *na měření metriky* mohou vytvořit výstrahu, když je prahová hodnota překročena určitý počet opakování v určitém časovém intervalu.

### <a name="number-of-results-alert-rules"></a>Počet pravidel upozornění na výsledky

**Počet** pravidel upozornění na výsledky vytvoří jednu výstrahu, pokud počet záznamů vrácených vyhledávacím dotazem překročí zadanou prahovou hodnotu. Tento typ pravidla výstrahy je ideální pro práci s událostmi, jako jsou protokoly událostí systému Windows, Syslog, WebApp Response a vlastní protokoly.  Můžete chtít vytvořit výstrahu při vytvoření konkrétní chybové události nebo při vytvoření více chybových událostí v určitém časovém období.

**Prahová hodnota**: Prahová hodnota pro počet pravidel upozornění na výsledky je větší nebo menší než určitá hodnota.  Pokud počet záznamů vrácených hledáním protokolu odpovídá tomuto kritériu, je vytvořena výstraha.

Chcete-li upozornit na jednu událost, nastavte počet výsledků na hodnotu větší než 0 a zkontrolujte výskyt jedné události, která byla vytvořena od posledního spuštění dotazu. Některé aplikace mohou protokolovat příležitostnou chybu, která by neměla nutně vyvolat výstrahu.  Aplikace může například opakovat proces, který vytvořil chybu události a pak úspěšné příště.  V takovém případě pravděpodobně nebudete chtít vytvořit výstrahu, pokud v určitém časovém období není vytvořeno více událostí.  

V některých případech můžete chtít vytvořit výstrahu v nepřítomnosti události.  Proces může například protokolovat pravidelné události označující, že funguje správně.  Pokud nehlásí jednu z těchto událostí v určitém časovém období, měla by být vytvořena výstraha.  V takovém případě byste nastavili prahovou hodnotu na **méně než 1**.

#### <a name="example-of-number-of-records-type-log-alert"></a>Příklad výstrahy protokolu typu Počet záznamů

Zvažte scénář, kde chcete vědět, kdy vaše webová aplikace poskytuje odpověď uživatelům s kódem 500 (to znamená) vnitřní chyba serveru. Vytvořili byste pravidlo výstrahy s následujícími podrobnostmi:  

- **Dotaz:** požadavky | kde resultCode == "500"<br>
- **Časové období:** 30 minut<br>
- **Frekvence výstrah:** pět minut<br>
- **Prahová hodnota:** Větší než 0<br>

Pak by výstraha spustit dotaz každých 5 minut, s 30 minut dat - hledat jakýkoli záznam, kde výsledek kód byl 500. Pokud je nalezen i jeden takový záznam, spustí výstrahu a spustí nakonfigurovanou akci.

### <a name="metric-measurement-alert-rules"></a>Pravidla upozornění na měření metrik

Pravidla upozornění **měření metriky** vytvoří výstrahu pro každý objekt v dotazu s hodnotou, která překračuje zadanou prahovou hodnotu a zadanou podmínku aktivační události. Na rozdíl od počtu pravidel upozornění **na výsledky** fungují pravidla upozornění **metriky měření,** když výsledek analýzy poskytuje časové řady. Mají následující odlišné rozdíly od počet pravidel upozornění **na výsledky.**

- **Agregační funkce**: Určuje výpočet, který se provádí, a potenciálně číselné pole, které má být agregováno.  Například **count()** vrátí počet záznamů v **dotazu, avg(CounterValue)** vrátí průměr pole CounterValue za interval. Agregační funkce v dotazu musí být pojmenována/volána: AggregatedValue a poskytnout číselnou hodnotu. 

- **Skupinové pole**: Pro každou instanci tohoto pole je vytvořen záznam s agregovanou hodnotou a pro každou z nich lze vygenerovat výstrahu.  Pokud byste například chtěli vygenerovat výstrahu pro každý počítač, **použil(a)** byste počítač . V případě, že existuje více skupinových polí určených v dotazu výstrahy, může uživatel určit, které pole má být použito k řazení výsledků pomocí parametru **Aggregate On** (metricColumn).

    > [!NOTE]
    > *Možnost Agregovat on* (metricColumn) je k dispozici pro upozornění protokolu typu metriky měření pro application insights a výstrahy protokolu pro [analýzu protokolů nakonfigurované pouze pomocí rozhraní API scheduledQueryRules.](../../azure-monitor/platform/alerts-log-api-switch.md)

- **Interval**: Definuje časový interval, ve kterém jsou data agregována.  Pokud jste například zadali **pět minut**, bude vytvořen záznam pro každou instanci skupinového pole agregovaného v 5minutových intervalech v časovém období určeném pro výstrahu.

    > [!NOTE]
    > Funkce přihrádky musí být použita v dotazu k určení intervalu. Jako bin() může mít za následek nerovné časové intervaly - Alert automaticky převede příkaz bin na příkaz bin_at s vhodným časem za běhu, aby bylo zajištěno výsledky s pevným bodem. Typ metrického měření výstrahy protokolu je určen pro práci s dotazy, které mají až tři instance příkazu bin().
    
- **Prahová hodnota**: Prahová hodnota pro pravidla upozornění metriky měření je definována agregotou hodnotou a řadou porušení.  Pokud některý datový bod ve vyhledávání protokolu překročí tuto hodnotu, je považován za porušení.  Pokud počet porušení v pro libovolný objekt ve výsledcích překročí zadanou hodnotu, pak je pro tento objekt vytvořena výstraha.

Chybná konfigurace možnosti *Aggregate On* nebo *metricColumn* může způsobit selhání pravidel výstrah. Další informace naleznete v [tématu řešení potíží s nesprávným pravidlem upozornění měření metriky](alert-log-troubleshoot.md#metric-measurement-alert-rule-is-incorrect).

#### <a name="example-of-metric-measurement-type-log-alert"></a>Příklad upozornění protokolu typu metriky měření

Zvažte scénář, ve kterém jste chtěli výstrahu, pokud některý počítač překročil využití procesoru 90 % třikrát během 30 minut.  Vytvořili byste pravidlo výstrahy s následujícími podrobnostmi:  

- **Dotaz:** Perf | kde ObjectName == "Procesor" a CounterName == "% času procesoru" | shrnout AggregatedValue = avg(CounterValue) podle bin(TimeGenerated, 5m), Počítač<br>
- **Časové období:** 30 minut<br>
- **Frekvence výstrah:** pět minut<br>
- **Logika výstrahy – prahová hodnota & podmínky:** Větší než 90<br>
- **Pole skupiny (agregace):** Počítače
- **Aktivační výstraha založená na:** Celková porušení větší než 2<br>

Dotaz by vytvořit průměrnou hodnotu pro každý počítač v intervalech 5 minut.  Tento dotaz by spustit každých 5 minut pro data shromážděná za předchozích 30 minut. Vzhledem k tomu, že vybrané pole skupiny (Agregace) je sloupcové "Počítač" - AggregatedValue je rozdělena pro různé hodnoty "Počítač" a průměrné využití procesoru pro každý počítač je určeno pro časovou přihrádku 5 minut.  Ukázkový výsledek dotazu pro (řekněme) tři počítače, by bylo níže.


|TimeGenerated [UTC] |Počítač  |Agregovaná hodnota  |
|---------|---------|---------|
|20xx-xx-xxT01:00:00Z     |   srv01.contoso.com      |    72     |
|20xx-xx-xxT01:00:00Z     |   srv02.contoso.com      |    91     |
|20xx-xx-xxT01:00:00Z     |   srv03.contoso.com      |    83     |
|...     |   ...      |    ...     |
|20xx-xx-xxT01:30:00Z     |   srv01.contoso.com      |    88     |
|20xx-xx-xxT01:30:00Z     |   srv02.contoso.com      |    84     |
|20xx-xx-xxT01:30:00Z     |   srv03.contoso.com      |    92     |

Pokud by měl být výsledek dotazu vykreslen, bude vypadat jako.

![Ukázkové výsledky dotazu](media/alerts-unified-log/metrics-measurement-sample-graph.png)

V tomto příkladu vidíme v přihrádkách 5 minut pro každý ze tří počítačů - průměrné využití procesoru, jak je vypočítáno po dobu 5 minut. Práh 90 je překročen srv01 pouze jednou v 1:25 bin. Pro srovnání, srv02 překračuje prahovou hodnotu 90 v přihrádkách 1:10, 1:15 a 1:25; zatímco srv03 překračuje prahovou hodnotu 90 v 1:10, 1:15, 1:20 a 1:30.
Vzhledem k tomu, že výstraha je nakonfigurována tak, aby se aktivovala na základě celkových porušení, jsou více než dvě, vidíme, že srv02 a srv03 splňují pouze kritéria. Proto by byly vytvořeny samostatné výstrahy pro srv02 a srv03, protože dvakrát překročily prahovou hodnotu 90% v několika časových přihrádkách.  Pokud *trigger alert based na:* parametr byl místo toho nakonfigurován pro *možnost nepřetržité porušení,* pak výstraha by být aktivována **pouze** pro srv03, protože porušil prahovou hodnotu pro tři po sobě jdoucí časové přihrádky od 1:10 do 1:20. A **ne** pro srv02, protože překročil práh pro dva po sobě jdoucí časové koše od 1:10 do 1:15.

## <a name="log-search-alert-rule---firing-and-state"></a>Pravidlo upozornění vyhledávání protokolů – spouštění a stav

Pravidla výstrah hledání protokolu fungují pouze na logice, kterou do dotazu zapracováváte. Systém výstrah nemá žádný jiný kontext stavu systému, záměr nebo hlavní příčinu vyplývající z dotazu. Jako takové výstrahy protokolu jsou označovány jako stav méně. Podmínky jsou vyhodnoceny jako "PRAVDA" nebo "FALSE" při každém spuštění.  Výstraha se zapálí pokaždé, když je vyhodnocení stavu výstrahy "TRUE", bez ohledu na to, že je aktivována dříve.    

Podívejme se na toto chování v akci s praktickým příkladem. Předpokládejme, že máme pravidlo výstrahy protokolu s názvem *Contoso-Log-Alert*, které je nakonfigurováno tak, jak je znázorněno v [příkladu uvedeném pro výstrahu protokolu typu Počet výsledků](#example-of-number-of-records-type-log-alert). Podmínka je vlastní výstražný dotaz určený k hledání kódu výsledku 500 v protokolech. Pokud další 500 kódy výsledků jsou nalezeny v protokolech, podmínka výstrahy je true. 

V každém níže uvedeném intervalu systém výstrah Azure vyhodnotí podmínku pro *upozornění contoso-log*.


| Time    | Počet záznamů vrácených vyhledávacím dotazem protokolu | Zhodnocení stavu protokolu | Výsledek 
| ------- | ----------| ----------| ------- 
| 13:05 ODPOLEDNE | 0 záznamů | 0 není > 0, takže FALSE |  Výstraha se nestřílí. Nejsou volány žádné akce.
| 13:10 | 2 záznamů | 2 > 0 tak PRAVDA  | Upozornění na požáry a skupiny akcí volané. Stav výstrahy ACTIVE.
| 13:15 | 5 záznamů | 5 > 0 tak PRAVDA  | Upozornění na požáry a skupiny akcí volané. Stav výstrahy ACTIVE.
| 13:20 | 0 záznamů | 0 není > 0, takže FALSE |  Výstraha se nestřílí. Nejsou volány žádné akce. Stav výstrahy vlevo ACTIVE.

Použití předchozího případu jako příklad:

V 13:15 azure výstrahy nelze určit, pokud základní problémy vidět na 1:10 přetrvávají a pokud záznamy jsou čisté nové chyby nebo opakování starších selhání na 1:10 PM. Dotaz poskytnutý uživatelem může nebo nemusí brát v úvahu dřívější záznamy a systém neví. Systém výstrah Azure je vytvořen tak, aby chybovat na straně upozornění a požáry výstrahy a související akce znovu v 1:15 pm. 

V 1:20 pm při nulové záznamy jsou vidět s 500 kód výsledků, Azure výstrahy nemůže být jisti, že příčina 500 kód výsledků vidět na 1:10 pm a 1:15 PM je nyní vyřešen. Neví, zda se problémy s chybami 500 stanou ze stejných důvodů znovu. Proto *Contoso-Log-Alert* nezmění vyřešen **v** Azure Alert řídicího panelu a/nebo oznámení nejsou odesílány o tom, že výstraha je vyřešena. Pouze vy, kdo rozumí přesné podmínce nebo důvodu logiky vložené do analytického dotazu, můžete [výstrahu označit jako uzavřenou](alerts-managing-alert-states.md) podle potřeby.

## <a name="pricing-and-billing-of-log-alerts"></a>Ceny a fakturace výstrah protokolu

Ceny platné pro upozornění protokolů jsou uvedeny na stránce [Ceny monitoru Azure.](https://azure.microsoft.com/pricing/details/monitor/) V azure účty protokolů výstrahy jsou reprezentovány jako typ `microsoft.insights/scheduledqueryrules` s:

- Protokolovat výstrahy na application insights zobrazené s přesným názvem výstrahy spolu se skupinou prostředků a vlastnostmi výstrahy
- Protokolovat výstrahy v log Analytics zobrazené s přesným názvem výstrahy spolu se skupinou prostředků a vlastnostmi výstrah; při vytvoření pomocí [rozhraní SCHEDULEDQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)

[Starší rozhraní API analýzy protokolů](../../azure-monitor/platform/api-alerts.md) obsahuje akce výstrah a plány jako součást uloženého vyhledávání služby Log Analytics a ne správné prostředky [Azure](../../azure-resource-manager/management/overview.md). Proto chcete-li povolit fakturaci pro takové starší výstrahy protokolu vytvořené pro Log Analytics pomocí portálu Azure **bez** [přepnutí na nové rozhraní API](../../azure-monitor/platform/alerts-log-api-switch.md) nebo prostřednictvím [staršího rozhraní API Log Analytics](../../azure-monitor/platform/api-alerts.md) – pro fakturaci v Azure se `microsoft.insights/scheduledqueryrules` vytvářejí skrytá pravidla pseudo výstrah. Skrytá pravidla pseudo výstrah `microsoft.insights/scheduledqueryrules` vytvořených `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` pro fakturaci, jak je znázorněno spolu se skupinou prostředků a vlastnostmi výstrah.

> [!NOTE]
> Pokud neplatné `<, >, %, &, \, ?, /` znaky, jako jsou k `_` dispozici, budou nahrazeny ve skrytém názvu pravidla pseudo výstrahy a tedy také ve vyúčtování Azure.

Chcete-li odebrat skryté plánovačIQueryRules prostředky vytvořené pro fakturaci pravidel výstrah pomocí [starší log Analytics API](api-alerts.md), uživatel může provést některou z následujících akcí:

- Každý uživatel může [přepnout předvolbu rozhraní API pro pravidla výstrah v pracovním prostoru Log Analytics](../../azure-monitor/platform/alerts-log-api-switch.md) a bez ztráty jejich pravidel výstrah nebo monitorování přejít na Azure Resource Manager kompatibilní [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Tím eliminovat potřebu, aby se pseudo skryté upozornění pravidla pro fakturaci.
- Nebo pokud uživatel nechce přepínat předvolbu rozhraní API, uživatel bude muset **odstranit** původní plán a akci výstrahpomocí [staršího rozhraní API Analýzy protokolů](api-alerts.md) nebo odstranit na [webu Azure Portal původní pravidlo upozornění protokolu.](../../azure-monitor/platform/alerts-log.md#view--manage-log-alerts-in-azure-portal)

Navíc pro skryté scheduleQueryRules prostředky vytvořené pro fakturaci pravidel výstrah pomocí [starší log Analytics API](api-alerts.md), všechny operace změny jako PUT se nezdaří. Jako `microsoft.insights/scheduledqueryrules` typ pseudo pravidla jsou pro účely fakturace pravidla výstrah vytvořených pomocí [starší log Analytics API](api-alerts.md). Jakékoli změny pravidla výstrahy by mělo být provedeno pomocí [starší log Analytics API](api-alerts.md) (nebo) uživatel může [přepnout api preference pro pravidla výstrah](../../azure-monitor/platform/alerts-log-api-switch.md) použít [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) místo.

## <a name="next-steps"></a>Další kroky

* Další informace o [vytváření výstrah protokolu v Azure](../../azure-monitor/platform/alerts-log.md).
* Principy [webhooky ve výstrahách protokolu v Azure](alerts-log-webhook.md).
* Další informace o [azure upozornění](../../azure-monitor/platform/alerts-overview.md).
* Další informace o [application insights](../../azure-monitor/app/analytics.md).
* Další informace o [log analytics](../../azure-monitor/log-query/log-query-overview.md).
