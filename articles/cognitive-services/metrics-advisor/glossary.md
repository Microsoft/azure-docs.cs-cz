---
title: Glosář Poradce pro metriky
titleSuffix: Azure Cognitive Services
description: Klíčové myšlenky a koncepty služby Advisory metriky
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: 6c461983053a145dfda58b9e3d26b39db0c339e5
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92893416"
---
# <a name="metrics-advisor-glossary-of-common-vocabulary-and-concepts"></a>Glosář Poradce pro metriky běžných slovníků a konceptů

Tento dokument popisuje technické výrazy používané v poradci metriky. V tomto článku se dozvíte o běžných konceptech a objektech, se kterými se můžete setkat při používání služby.

## <a name="data-feed"></a>Datový kanál

> [!NOTE]
> Stejný zdroj dat může sdílet několik metrik a dokonce i stejný datový kanál.

Datový kanál je metrika, kterou Poradce pro metriky ingestuje ze zdroje dat, jako je Cosmos DB nebo SQL Server. Datový kanál obsahuje řádky:
* časová razítka
* nula nebo více dimenzí
* jedna nebo více měr. 

## <a name="metric"></a>Metrika

Metrika je kvantifikovaná míra, která se používá k monitorování a vyhodnocení stavu konkrétního obchodního procesu. Může se jednat o kombinaci více hodnot časových řad rozdělených do dimenzí. Například metrika *stavu webu* může obsahovat rozměry *počtu uživatelů* a *trhu en-US* .

## <a name="dimension"></a>Dimenze

Dimenze je jedna nebo více hodnot kategorií. Kombinace těchto hodnot identifikuje určitou univariate časovou řadu, například: Country, Language, tenant atd.

## <a name="multi-dimensional-metric"></a>Multidimenzionální metrika

Co je metrika s více dimenzemi? Pojďme použít dva příklady. 

**Tržby za firmu**

Předpokládejme, že máte data pro tržby vaší firmy. Vaše data časových řad můžou vypadat přibližně takto:

| Timestamp | Kategorie | Uvádět | Výnos |
| ----------|----------|--------|----- |
| 2020-6-1 | Simulant | USA | 1000 |
| 2020-6-1 | Oděvy | USA | 2000 |
| 2020-6-2 | Simulant | UK | 800 | 
| ...      | ...  |... | ... |

V tomto příkladu jsou *kategorie* a *trh* dimenze. *Tržby* jsou klíčový ukazatel výkonu (KPI), který se dá rozdělit do různých kategorií nebo na trhy, a dá se taky agregovat. Například tržby *potravin* pro všechny trhy.
 
**Počty chyb pro komplexní aplikaci**

Předpokládejme, že máte data pro počet chyb zaznamenaných v aplikaci. Vaše data časových řad můžou vypadat přibližně takto:

| Timestamp | Součást aplikace | Oblast | Počet chyb |
| ----------|----------|--------|----- |
| 2020-6-1 | Databáze zaměstnanců | ZÁPADNÍ EU | 9000 |
| 2020-6-1 | Fronta zpráv | VÝCHODNÍ USA | 1000 |
| 2020-6-2 | Fronta zpráv | VÝCHODNÍ USA | 8000| 
| ...      | ...           | ...     |  ...|

V tomto příkladu jsou *komponenty aplikace* a *oblast* dimenze. *Počet chyb* je klíčový ukazatel výkonu, který může být rozdělen do různých kategorií a/nebo na trhy, a může být také agregovaný. Například počet chyb *fronty zpráv* ve všech oblastech.

## <a name="measure"></a>Measure

Míra je základní nebo konkrétní termín jednotky a kvantifikovaná hodnota metriky.

## <a name="time-series"></a>time series (časová řada)

Časová řada je řada datových bodů indexovaných (nebo uvedených v grafu) v chronologickém pořadí. Většinou je časová řada posloupností pořízených po sobě jdoucích místech v čase. Jedná se o sekvenci dat v diskrétním čase.

V poradci metrik se hodnoty jedné metriky v konkrétní kombinaci dimenzí nazývají jedna řada.

## <a name="granularity"></a>Členitost

Členitost indikuje, jak často se budou datové body generovat ve zdroji dat. Například denně, každou hodinu.

## <a name="start-time"></a>Čas spuštění

Čas spuštění je čas, kdy má Poradce pro metriky začít s přijímáním dat ze zdroje dat. Zdroj dat musí mít data v zadaném počátečním čase.

## <a name="confidence-boundaries"></a>Hranice spolehlivosti

> [!NOTE]
> Hranice spolehlivosti nejsou jediným měřením, které se používá k nalezení anomálií. Je možné, aby datové body mimo tuto hranici byly podle modelu detekce označeny jako normální. 

V poradci metriky představují hranice spolehlivosti citlivost používaného algoritmu a používají se k odfiltrování přesných citlivých anomálií. Na webovém portálu se zobrazí hranice spolehlivosti jako transparentní modrý panel. Všechny body v rámci pásma jsou považovány za normální body.

Poradce metrik nabízí nástroje pro úpravu citlivosti použitých algoritmů. Další informace najdete v tématu [How to: Configure metriky and doladění detekce konfigurace](how-tos/configure-metrics.md) .

![Meze spolehlivosti](media/confidence-bounds.png)


## <a name="hook"></a>Háčk

Poradce pro metriky umožňuje vytvářet a odebírat výstrahy v reálném čase. Tyto výstrahy se odesílají přes Internet [pomocí zavěšení](how-tos/alerts.md).

## <a name="anomaly-incident"></a>Incident anomálií

Po použití konfigurace detekce na metriky se incidenty generují vždy, když každá z řad obsahuje anomálii. U velkých datových sad to může být zahlcené, takže Poradce metriky seskupují řady anomálií v rámci metriky na incident. Služba také vyhodnotí závažnost a poskytne nástroje pro [diagnostiku incidentu](how-tos/diagnose-incident.md).

### <a name="incident-tree"></a>Strom incidentů

V poradci metriky můžete pro metriky použít detekci anomálií, a proto Advisor metriky automaticky monitoruje všechny časové řady všech kombinací dimenzí. Pokaždé, když dojde k detekci anomálií, Advisor metrika agreguje anomálie na incidenty.
Jakmile dojde k incidentu, Poradce pro metriky nabídne strom incidentů s hierarchií přispívajících anomálií a zjistí největší dopad. Každý incident má anomálii hlavní příčiny, což je nejvyšší uzel stromu.

### <a name="anomaly-grouping"></a>Seskupení anomálií

Poradce metrik nabízí možnost najít související časovou řadu s podobnými vzory. Může také poskytovat hlubší přehled o dopadu na jiné dimenze a koreluje anomálie.

### <a name="time-series-comparison"></a>Porovnání časových řad

Můžete vybrat několik časových řad a porovnat trendy v jedné vizualizaci. To poskytuje jasný a přehledné způsob, jak zobrazit a porovnat související řady.

## <a name="detection-configuration"></a>Konfigurace detekce

>[!Note]
>Konfigurace detekce se aplikují jenom v rámci jednotlivé metriky.

Na webovém portálu Poradce pro metriky se na levém panelu při zobrazení metriky zobrazí konfigurace detekce (například citlivost, automatické odložení a směr). Parametry se dají vyladit a aplikovat na všechny řady v rámci této metriky.

Pro každou časovou řadu se vyžaduje konfigurace detekce a určuje, jestli je bod v časové řadě anomálií. Poradce pro metriky nastaví výchozí konfiguraci pro celou metriku při první registraci dat. 

Konfiguraci můžete také upřesnit použitím parametrů ladění pro skupinu řad nebo konkrétní. Pro časovou řadu se použije jenom jedna konfigurace:
* Konfigurace použité pro konkrétní řadu přepíše konfigurace pro skupinu.
* Konfigurace pro skupinu přepíše konfigurace použité pro celou metriku.

Poradce metrik nabízí několik [metod detekce](how-tos/configure-metrics.md#anomaly-detection-methods)a můžete je kombinovat pomocí logických operátorů.

### <a name="smart-detection"></a>Inteligentní zjišťování

Detekce anomálií s využitím více algoritmů strojového učení.

**Citlivost** : číselnou hodnotu pro úpravu tolerance detekce anomálií. Vizuálně, čím větší je hodnota, tím užší je horní a dolní hranice kolem časové řady.

### <a name="hard-threshold"></a>Tvrdý práh

Hodnoty mimo horní nebo dolní meze jsou anomálie.

**Minimum** : dolní mez

**Max** : horní mez

### <a name="change-threshold"></a>Změnit prahovou hodnotu

Použijte hodnotu předchozí bod a určete, zda je tento bod anomálií.

**Změna procenta** : v porovnání s předchozím bodem je aktuální bod anomálií, pokud je procentuální hodnota změny větší než tento parametr.

**Změnit přes body** : kolik bodů se má prohledat zpátky.

### <a name="common-parameters"></a>Společné parametry

**Směr** : bod je anomálie jenom v případě, že se odchylka vyskytuje ve směru *nahoru* , *dolů* nebo v *obou* .

Nejedná se o **platnou anomálii, dokud** : datový bod je pouze anomálie v případě, že zadané procento předchozích bodů se také anomálií.

## <a name="alert-settings"></a>Nastavení výstrah

Nastavení výstrah určují, které anomálie by měly aktivovat výstrahu. Můžete nastavit více výstrah s jiným nastavením. Můžete například vytvořit výstrahu pro anomálie s nižším dopadem na chod firmy a jinou pro výstrahy s větší důležitostí.

Můžete také vytvořit výstrahu napříč metrikami. Například výstraha, která se aktivuje jenom v případě, že dvě zadané metriky mají anomálie.

### <a name="alert-scope"></a>Rozsah výstrahy

Rozsah výstrahy odkazuje na obor, na který se výstraha vztahuje. Nabízejí se čtyři možnosti:

**Anomálie všech řad** : výstrahy se aktivují pro anomálie ve všech řadách v rámci metriky.

**Anomálie ve skupině řady** : výstrahy se aktivují jenom pro anomálie v konkrétních dimenzích skupiny řad. Počet určených rozměrů by měl být menší než celkové číslo dimenze.

**Anomálie v oblíbených řadách** : výstrahy se aktivují jenom pro anomálie, které se přidají jako oblíbené položky. Můžete zvolit skupinu řad jako oblíbenou položku pro každou konfiguraci zjišťování.

**Anomálie v horní části N všech řad** : výstrahy se aktivují jenom pro anomálie v horních n řadách. Můžete nastavit parametry pro určení počtu časových razítek, která se mají vzít v úvahu, a kolik anomálií musí být v nich pro odeslání výstrahy.

### <a name="severity"></a>Závažnost

Závažnost je taková, kterou funkce Advisor používá k popisu závažnosti incidentu, včetně *vysokého* , *středního* a *nízkého* výkonu.

V současné době poradce metrik používá následující faktory pro měření závažnosti výstrahy:
1. Poměr hodnot a množství anomálií v metrikě.
1. Spolehlivost anomálií.
1. Vaše oblíbená nastavení také přispívají k závažnosti.

### <a name="auto-snooze"></a>Automaticky připomenout

Některé anomálie jsou přechodnými problémy, zejména pro malé metriky členitosti. Výstrahu *můžete odložit na určitý* počet časových bodů. Pokud se anomálie nacházejí v zadaném počtu bodů, aktivuje se žádná výstraha. Chování automatického odložení lze nastavit na úrovni metrik nebo na úrovni řady.

Chování při odložení lze nastavit na úrovni metrik nebo na úrovni řady.

## <a name="data-feed-settings"></a>Nastavení datového kanálu

### <a name="ingestion-time-offset"></a>Časový posun příjmu

Ve výchozím nastavení se data ingestují podle členitosti (například *denně* ). Pomocí kladného celého čísla můžete zpozdit přijímání dat zadanou hodnotou. Pomocí záporného čísla můžete posunout příjem hodnoty určenou hodnotou.

### <a name="max-ingestion-per-minute"></a>Maximální příjem dat za minutu

Nastavte tento parametr, pokud váš zdroj dat podporuje omezené souběžnosti. V opačném případě ponechte výchozí nastavení.

### <a name="stop-retry-after"></a>Ukončit akci po

Pokud se příjem dat nezdařil, Poradce pro metriky se znovu spustí automaticky po časovém intervalu. Začátek období je čas, kdy došlo k prvnímu příjmu dat. Délka tečky opakování je definována podle členitosti. Použijete-li výchozí hodnotu ( `-1` ), bude perioda opakování určena podle členitosti:

| Členitost       | Ukončit akci po           |
| :------------ | :--------------- |
| Denně, Custom (>= 1 den), týdně, měsíčně, ročně     | 7 dní          |
| Každou hodinu, vlastní (< 1 den)       | 72 hodin |

### <a name="min-retry-interval"></a>Minimální interval opakování

Při pokusu o načtení dat ze zdroje můžete zadat minimální interval. Použijete-li výchozí hodnotu ( `-1` ), bude interval opakování stanoven podle členitosti:

| Členitost       | Minimální interval opakování           |
| :------------ | :--------------- |
| Denně, Custom (>= 1 den), týdně, měsíčně     | 30 minut          |
| Každou hodinu, vlastní (< 1 den)      | 10 minut |
| Ročně | 1 den          |

### <a name="grace-period"></a>Období odkladu

> [!Note]
> Doba odkladu začíná při běžné době přijímání a posune se na časový posun.
    
Doba odkladu je časový úsek, ve kterém bude Poradce pro metriky pokračovat v načítání dat ze zdroje dat, ale neaktivuje žádné výstrahy. Pokud po uplynutí období odkladu nedošlo k ingestování dat, aktivuje se výstraha *datový kanál, který není k dispozici* .

### <a name="snooze-alerts-in"></a>Odložení výstrah v

Pokud je tato možnost nastavena na hodnotu nula, každé časové razítko, *které není k dispozici* , spustí výstrahu. Pokud je hodnota nastavena na jinou hodnotu než nula, zadaný počet výstrah, které *nejsou k dispozici* , bude odložena, pokud nebyla načtena žádná data.

## <a name="data-feed-permissions"></a>Oprávnění datového kanálu

K dispozici jsou dvě role pro správu oprávnění datového kanálu: *správce* a *prohlížeč* . 

* *Správce* má úplnou kontrolu nad datovým kanálem a metrikami, které jsou v něm obsažené. Můžou aktivovat, pozastavit, odstranit datový kanál a dělat aktualizace kanálů a konfigurací. *Správce* je obvykle vlastníkem metrik.

* *Prohlížeč* může zobrazit datový kanál nebo metriky, ale nemůže provádět změny. 

## <a name="next-steps"></a>Další kroky
- [Přehled Poradce pro metriky](overview.md)
- [Použití webového portálu](quickstarts/web-portal.md)