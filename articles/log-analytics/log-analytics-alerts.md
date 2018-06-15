---
title: Vysvětlení výstrah v Azure Log Analytics | Microsoft Docs
description: Výstrahy v Log Analytics identifikovat důležité informace ve svém úložišti OMS a můžete proaktivně upozorňují na problémy nebo vyvolání akce se pokusit o opravte je.  Tento článek popisuje různé druhy pravidla výstrah a jak jsou definovány.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 6cfd2a46-b6a2-4f79-a67b-08ce488f9a91
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/13/2018
ms.author: bwren
ms.openlocfilehash: cf1842c6abbbfd767184d8f480a5f3a5fd654ed0
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32178666"
---
# <a name="understanding-alerts-in-log-analytics"></a>Vysvětlení výstrah v analýzy protokolů

> [!NOTE]
> Výstrahy v analýzy protokolů jsou [rozšiřovanou do Azure](../monitoring-and-diagnostics/monitoring-alerts-extend.md). Informace v tomto článku pořád můžou použít pro definování podrobnosti výstrahy na portálu Azure pomocí vyhledávání analýzy protokolů.

Upozornění v Log Analytics identifikují důležité informace v úložišti Log Analytics. Tento článek popisuje některé z rozhodnutí o návrhu, které musí být provedeny na základě četnosti shromažďování dat je předmětem dotazu, náhodné zpoždění s přijímání dat může být způsobeno latence sítě nebo zpracování kapacitu a potvrzování data do protokolu Pracovní prostor analýzy. Také poskytuje podrobnosti o tom, jak výstrahy pravidlech pracovního analýzy protokolů a popisuje rozdíly mezi různé typy pravidla výstrah.

Proces vytváření pravidla výstrah najdete v následujících článcích:

- Vytvořit pravidla výstrah pomocí [portálu Azure](log-analytics-alerts-creating.md)
- Vytvořit pravidla výstrah pomocí [šablony Resource Manageru](../operations-management-suite/operations-management-suite-solutions-resources-searches-alerts.md)
- Vytvořit pravidla výstrah pomocí [REST API](log-analytics-api-alerts.md)

## <a name="considerations"></a>Požadavky

Podrobnosti o frekvenci shromažďování dat pro různá řešení a typu dat jsou k dispozici v [podrobnosti kolekce dat](log-analytics-add-solutions.md#data-collection-details) článku Přehled řešení. Jak je uvedeno v tomto článku, může být frekvence kolekce jako nepravidelným jako každých 7 dní na *na oznámení*. Je důležité pochopit a vezměte v úvahu interval shromažďování dat před nastavením výstrahu. 

- Frekvence kolekce Určuje, jak často OMS agent odesílá data k analýze protokolů. Například pokud frekvence kolekce je 10 minut a neexistují žádné další zpoždění v systému, potom časová razítka přenášených dat může být kdekoli od 0 do 10 minut před přidávané do úložiště a je vyhledávat v analýzy protokolů.

- Předtím, než může být výstraha, data musí být zapsané do úložiště, aby bylo k dispozici, pokud dotaz. Z důvodu latence popsané výše není frekvence kolekce stejný jako čas, kdy data je k dispozici pro dotazy. Například při data mohou být shromažďována přesněji každých 10 min, data je k dispozici v úložišti dat nepravidelných intervalech. Shromažďovaných dat v počtu nula, 10 a 20 minut intervalech hypoteticky, může být k dispozici pro vyhledávání v 25, 28 a 35 minut v uvedeném pořadí, nebo v některých nestandardní intervalu ovlivněné přijímání latence. Nejhorším případě pro tyto zpoždění dokumentovány v článku [SLA pro analýzy protokolů](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_1), který nezahrnuje zpoždění zaváděné kolekce latence frekvence nebo síti mezi počítači a analýzy protokolů služby.


## <a name="alert-rules"></a>Pravidla upozornění

Upozornění se vytvářejí na základě pravidel upozornění, která automaticky a v pravidelných intervalech spouští prohledávání protokolů. Pokud výsledky hledání protokolů odpovídají konkrétním kritériím se vytvoří záznam výstrahy. Pravidlo pak může automaticky spustit jednu nebo více akcí a proaktivně vás upozornit nebo vyvolat jiný proces. Různé typy pravidla výstrah pomocí různých logiku k provedení této analýze.

![Výstrahy Log Analytics](media/log-analytics-alerts/overview.png)

Vzhledem k tomu, že dojde předpokládaného latence s přijímání dat protokolu, může nepředvídatelné absolutním čase mezi indexování dat a pokud je k dispozici pro vyhledávání. Dostupnost téměř v reálném čase data shromážděná třeba vzít v úvahu při vytváření definice pravidla výstrah.

Je kompromis mezi spolehlivost výstrah a odezvy výstrahy. Můžete nakonfigurovat parametry alert, chcete-li minimalizovat false výstrahy a chybějící výstrahy, nebo můžete výstrahy parametry rychle reagovat na podmínky, které jsou monitorovány, ale někdy generuje false nebo zmeškaných výstrahy.

Pravidla výstrah jsou definovány následující podrobnosti:

- **Hledání protokolů**. Dotaz, který se spustí pokaždé, když se aktivuje pravidlo výstrahy. Vrácené tímto dotazem záznamy se používají k určení, jestli se má vytvořit výstrahu.
- **Časový interval**. Určuje časový rozsah pro dotaz. Dotaz vrátí pouze záznamy vytvořené v tomto rozsahu před aktuálním časem. To může být libovolná hodnota 5 minut až 24 hodin. Rozsah musí být dost široké, aby dokázala pojmout přiměřené zpoždění při přijímání. Časový interval musí být dvakrát délka nejdelší zpoždění, které chcete být schopna zpracovávat.<br> Například pokud chcete generovat výstrahy za spolehlivé pro zpoždění 30 minut, pak rozsahu musí být jedna hodina.

    Existují dvě příznaků, kterým může dojít, pokud časový rozsah je příliš malá.

    - **Chybí výstrahy**. Předpokládejme přijímání zpoždění je 60 minut někdy, ale ve většině případů je 15 minut. Pokud časový interval je nastavené na 30 minut, poté ji nezdařených přístupů k výstrahu při zpoždění je 60 minut, protože data není k dispozici pro vyhledávání, když se spustí výstraha dotazu. 
   
        >[!NOTE]
        >Při pokusu o diagnostikovat, proč je provedena výstrahy není možné. Například v případě výše, budou data zapsána do úložiště 60 minut, jakmile je výstraha dotazu byla spuštěna. Pokud je jeho si všimli další den výstrahu nebyla provedena, a další den bude dotaz proveden prostřednictvím správný časový interval, kritéria hledání protokolu vyhovovat výsledek. Zdá se, že by měl mít byla výstraha. Ve skutečnosti nebyl výstraha vzhledem k tomu, že data nebyla k dispozici při je výstraha dotazu byla spuštěna. 
        >
 
    - **False výstrahy**. Někdy výstrahy dotazy slouží k identifikaci absence událostí. Příkladem toho je zjišťování, pokud je virtuální počítač offline vyhledáním zmeškaných prezenčních signálů. Jako výše, pokud prezenční signál není k dispozici pro vyhledávání v rámci výstrahy časový interval, pak výstrahy je generována, protože data prezenčního signálu dosud nebyl s možností vyhledávání a proto chybí. Toto je stejný výsledek, jako by byl legálně offline virtuálního počítače a se žádná data prezenčního signálu generované ho. Provádění dotazu další den přes správný časový interval ukazuje, že nastaly prezenčních signálů a výstrahy se nezdařilo. Ve skutečnosti má vysílat prezenční signál nebyla ještě dostupná pro hledání kvůli výstrahy časový interval byl nastaven příliš malá.

- **Frekvence**.  Určuje, jak často dotaz by měl být spuštěn a slouží k dosáhnete rychlejší reakce pro případ, normální výstrahy. Hodnota může být v rozmezí 5 minut a 24 hodin a musí být rovna nebo menší než výstrahy časový interval.  Pokud hodnota je větší než časový interval, riskujete záznamů je vynechán.<br>Pokud je cílem za spolehlivé pro zpozdí až 30 minut a normální zpoždění je 10 minut, časový interval by měl být jednu hodinu a hodnota frekvence by měla být 10 minut. To by spustí výstrahu s daty, která má 10 minut přijímání zpoždění mezi 10 a 20 minut od při generování dat výstrah.<br>Vyhněte se vytváření více výstrah pro stejná data, protože časový interval je příliš široké, možnost potlačení výstrahy lze potlačit oznámení pro alespoň stejně dlouho jako časový interval.
  
- **Prahová hodnota**. Výsledky hledání protokolů se vyhodnocují k určení, zda má být vytvořena výstraha. Prahová hodnota se liší pro různé typy pravidla výstrah.

Každé pravidlo výstrahy v analýzy protokolů je jedním ze dvou typů. Každý z těchto typů je podrobně popsány v následujících částech.

- **[Počet výsledků](#number-of-results-alert-rules)**. Vytvoří, když počet záznamů protokolu nalezené překračuje zadaný počet jedna výstraha.
- **[Metriky měření](#metric-measurement-alert-rules)**. Výstraha byla vytvořena pro každý objekt ve výsledcích hledání protokolu s hodnotami, které překročí zadanou prahovou hodnotu.

Rozdíl mezi typy pravidlo výstrahy se následujícím způsobem.

- **Počet výsledků** pravidlo výstrahy vždycky vytvořte jednu výstrahu chvíli **metriky měření** pravidlo výstrahy vytvoří výstrahu pro každý objekt, který překračuje prahovou hodnotu.
- **Počet výsledků** pravidla výstrah vytvářejí výstrahu, když je prahová hodnota počtu současně. **Metriky měření** pravidla výstrah může vytvořit upozornění, pokud je překročena prahová hodnota počtu časy v konkrétním časovém intervalu.

## <a name="number-of-results-alert-rules"></a>Počet výsledků pravidla výstrah
**Počet výsledků** pravidla výstrah vytvořit jednu výstrahu, když počet záznamů vrácených dotazem vyhledávání překročí zadanou prahovou hodnotu.

### <a name="threshold"></a>Mezní hodnota
Prahová hodnota pro **počet výsledků** pravidlo výstrahy je větší nebo menší než určitou hodnotu. Pokud počet záznamů protokolu nalezené splňují tato kritéria, se vytvoří výstraha.

### <a name="scenarios"></a>Scénáře

#### <a name="events"></a>Události
Tento typ pravidla výstrah je ideální pro práci s událostmi, jako je například protokol událostí systému Windows, Syslog, a vlastní protokoly. Můžete vytvořit výstrahu, když se vytvoří událost konkrétní chyby, nebo když jsou v rámci konkrétní časové okno vytvořit více událostí chyby.

Chcete-li výstraha na jednu událost, nastavte počet výsledků na hodnotu větší než 0 a četnost i časové okno na 5 minut. Která se spustí dotaz každých 5 minut a zkontrolujte výskyt jedna událost, která byla vytvořena od posledního spuštění dotazu. Doba mezi událostí shromažďovaných a vytváří výstrahy může zdržet frekvencí delší.

Některé aplikace mohou být zaznamenány občasné chyby, který by neměl být nutně vygeneruje výstrahu. Aplikace může například opakujte procesu, který vytvořil událost chyby a pak úspěšné příště. V takovém případě nemusí budete chtít vytvořit výstrahu, pokud jsou v rámci konkrétní časové okno vytvořit více událostí.

V některých případech můžete vytvořit výstrahu při absenci událost. Tento proces se může například protokolu běžné události indikující, že funguje správně. Pokud není protokolu jednu z těchto událostí v rámci konkrétní časové okno, je třeba vytvořit výstrahu. V takovém případě byste měli nastavit prahovou hodnotu na **menší než 1**.

#### <a name="performance-alerts"></a>Výstrahy výkonu
[Data výkonu](log-analytics-data-sources-performance-counters.md) se ukládají jako záznamy v úložišti OMS podobné události. Pokud chcete upozornit, když čítače výkonu překračuje prahovou hodnotu konkrétní, by měl prahové hodnoty obsažen v dotazu.

Například, pokud chcete upozornit, když procesor běží víc než 90 %, byste použili dotazu jako následující s prahovou hodnotou pro pravidlo výstrahy **větší než 0**.

    Perf | where ObjectName=="Processor" and CounterName=="% Processor Time" and CounterValue>90

Pokud chcete upozornit, když procesor průměrem více než 90 % pro konkrétní časové okno, byste použili dotazu pomocí `measure` příkaz jako na následujícím obrázku se prahová hodnota pro pravidlo výstrahy **větší než 0**.

    Perf | where ObjectName=="Processor" and CounterName=="% Processor Time" | summarize avg(CounterValue) by Computer | where CounterValue>90



## <a name="metric-measurement-alert-rules"></a>Pravidla výstrah metriky měření

>[!NOTE]
> Pravidla výstrah metriky měření jsou aktuálně ve verzi public preview.

**Metriky měření** pravidla výstrah vytvářejí výstrahu pro každý objekt v dotazu s hodnotou, který je delší než zadaná prahová hodnota. Mají odlišné následující rozdíly proti **počet výsledků** pravidla výstrah.

#### <a name="log-search"></a>Prohledávání protokolů
I když můžete používat pro jakýkoli dotaz **počet výsledků** pravidlo výstrahy, existují požadavky na dotaz pro pravidlo výstrahy metriky měření.  Musí obsahovat `measure` příkazu seskupení výsledků podle určitého pole. Tento příkaz musí zahrnovat následující prvky.


- **Agregační funkce**.  Určuje výpočet, který provádí a potenciálně číselného pole k agregaci. Například **count()** vrátí počet záznamů v dotazu, **avg(CounterValue)** Vrátí průměrnou hodnotu pole přepočtené průběhu intervalu.
- **Pole Seskupit**.  Pro každou instanci v tomto poli se vytvoří záznam s hodnotou agregované a výstraha může vygenerovat pro každý. Například pokud chcete generovat výstrahy pro každý počítač, byste použili **počítačem**.   
- **Interval**.  Definuje časový interval, za které agregovaná data.  Například pokud jste zadali **5 minut**, by se vytvoří záznam pro každou instanci pole skupiny v okně čas zadaný pro výstrahu agregován v intervalech 5 minut.

#### <a name="threshold"></a>Mezní hodnota
Prahová hodnota pro pravidla výstrah metriky měření je definována agregovaná hodnota a celou řadu. Pokud žádné datového bodu v hledání protokolů překročí tuto hodnotu, považuje za porušení. Pokud počet porušení v u všech objektů ve výsledcích překročí zadanou hodnotu, se vytvoří výstraha pro tento objekt.

#### <a name="example"></a>Příklad:
Vezměte v úvahu scénář, kde jste chtěli výstrahu překračování libovolného počítače využití procesoru 90 % třikrát více než 30 minut. Vytvoříte pravidlo výstrahy s následujícími podrobnostmi.

**Dotaz:** typ = výkonu ObjectName = název_čítače procesoru = "% času procesoru" | měření avg(CounterValue) počítače interval 5 minut<br>
**Časový interval:** 30 minut<br>
**Frekvence výstrah:** 5 minut<br>
**Agregace hodnota:** větší než 90<br>
**Aktivační událost upozornění na základě:** celkem poruší větší než 5<br>

Dotaz by vytvořit průměrnou hodnotu pro každý počítač v intervalech 5 minut. Tento dotaz by spustit každých 5 minut datech shromážděných za předchozí 30 minut. Ukázková data jsou uvedené dole pro tři počítače.

![Ukázkové výsledky dotazu](media/log-analytics-alerts/metrics-measurement-sample-graph.png)

V tomto příkladu by se vytvořit samostatné výstrahy pro srv02 a srv03, protože jejich nichž nebyla dodržena prahová hodnota 90 % 3krát přes časový interval. Pokud **aktivační událost upozornění na základě:** byly změněny na **za sebou** výstrahu by vytvořen pouze pro srv03, od nichž nebyla dodržena prahová hodnota pro 3 po sobě jdoucích vzorků.

## <a name="alert-records"></a>Výstrahy záznamů
Mít výstrahy záznamy vytvořené pravidla výstrah v analýzy protokolů **typ** z **výstraha** a **SourceSystem** z **OMS**. V následující tabulce mají vlastnosti.

| Vlastnost | Popis |
|:--- |:--- |
| Typ |*Výstrahy* |
| SourceSystem |*OMS* |
| *Objekt*  | [Metriky měření výstrahy](#metric-measurement-alert-rules) má vlastnost pro pole skupiny. Například pokud protokol hledání skupin v počítači, výstrahy záznam s mít počítač pole s názvem počítače jako hodnotu.
| AlertName |Název výstrahy. |
| AlertSeverity |Úroveň závažnosti výstrahy. |
| LinkToSearchResults |Propojit vyhledávání protokolu analýzy protokolů, který vrátí záznamy v dotazu, který vytvořili výstrahu. |
| Dotaz |Text dotazu, který byl spuštěn. |
| QueryExecutionEndTime |Konec časový rozsah pro dotaz. |
| QueryExecutionStartTime |Začátek časový rozsah pro dotaz. |
| ThresholdOperator | Operátor, který byl používán pravidlo výstrahy. |
| ThresholdValue | Hodnota, která byla používána pro pravidlo výstrahy. |
| TimeGenerated |Datum a čas vytvoření výstrahy. |

Existují jiné typy záznamů výstrahy vytvořené [řešení pro správu výstrahu](log-analytics-solution-alert-management.md) a [Power BI exportuje](log-analytics-powerbi.md).  Tyto jsou vybavené **typ** z **výstrahy** , ale jsou rozlišené jejich **SourceSystem**.


## <a name="next-steps"></a>Další postup
* Nainstalujte [řešení pro správu výstrah](log-analytics-solution-alert-management.md) k analýze výstrahy vytvořené v analýzy protokolů společně s výstrahy shromážděných z System Center Operations Manager.
* Další informace o [protokolu hledání](log-analytics-log-searches.md) , mohou generovat výstrahy.
* Dokončete průvodce pro [konfigurace webook](log-analytics-alerts-webhooks.md) s pravidlo výstrahy.
* Další informace o zápisu [sady runbook ve službě Azure Automation](https://azure.microsoft.com/documentation/services/automation) k nápravě problémů identifikovaný výstrahy.
