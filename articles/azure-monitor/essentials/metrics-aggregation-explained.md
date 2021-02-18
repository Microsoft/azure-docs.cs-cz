---
title: Vysvětlení metrik Azure Monitor metriky metriky a zobrazení
description: Podrobné informace o tom, jak se v Azure Monitor agreguje metriky
author: rboucher
ms.author: robb
services: azure-monitor
ms.topic: conceptual
ms.date: 01/12/2020
ms.subservice: metrics
ms.openlocfilehash: c47c316c82d8c510b4446e4335a219f400d476e4
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100609863"
---
# <a name="azure-monitor-metrics-metrics-aggregation-and-display-explained"></a>Vysvětlení metrik Azure Monitor metriky metriky a zobrazení

Tento článek vysvětluje agregaci metrik v databázi Azure Monitor Time-Series, která se zpětně Azure Monitor [metrikami platformy](../data-platform.md) a [vlastními metrikami](../essentials/metrics-custom-overview.md). Tento článek platí také pro standardní [Application Insights metriky](../app/app-insights-overview.md). 

Toto je složité téma a není nutné porozumět všem informacím v tomto článku, aby bylo možné Azure Monitor metriky používat efektivně.

## <a name="overview-and-terms"></a>Přehled a výrazy

Když přidáte metriku do grafu, Průzkumník metrik automaticky vybere jeho výchozí agregaci. Ve výchozím nastavení je to ve scénářích Basic, ale k získání dalších přehledů o této metrikě můžete použít jiné agregace. Zobrazení různých agregací v grafu vyžaduje, abyste porozuměli tomu, jak je Průzkumník metrik zpracovává.

Řekněme, že nejdřív nadefinujeme několik výrazů:

- **Hodnota metriky** – jediná hodnota měření získaná pro konkrétní prostředek.
- **Časové období** – obecné časové období.
- **Časový interval** – časové období mezi shromážděním dvou hodnot metrik. 
- **Časový rozsah** – časový interval zobrazený v grafu. Typická výchozí hodnota je 24 hodin. K dispozici jsou pouze konkrétní rozsahy. 
- **Časová členitost** nebo **časové intervaly** – časové období použité k agregaci hodnot společně s povoleným zobrazením v grafu. K dispozici jsou pouze konkrétní rozsahy. Aktuální minimum je 1 minuta. Hodnota časové členitosti by měla být menší než vybraný časový rozsah, který je užitečný, jinak se pro celý graf zobrazí jen jedna hodnota. 
- **Typ agregace** – typ statistik vypočítaný z více hodnot metriky.  
- **Aggregate** – proces přebírání více vstupních hodnot a jejich použití k vytvoření jedné výstupní hodnoty prostřednictvím pravidel definovaných typem agregace. Například přebírání průměru více hodnot.  

Metriky představují řadu hodnot metrik zachycených v pravidelném časovém intervalu. Když graf vykreslíte, hodnoty vybrané metriky se samostatně agregují podle časové členitosti (označují se také jako časové intervaly). Velikost časového rozlišení můžete vybrat pomocí [panelu pro výběr Průzkumník metrikho času](../essentials/metrics-getting-started.md#select-a-time-range). Pokud neprovedete explicitní výběr, časové rozlišení se automaticky vybere v závislosti na aktuálně vybraném časovém rozsahu. Po výběru se hodnoty metrik, které byly zachyceny během každého intervalu členitosti, agreguje a umístí do grafu – jedna hodnota DataPoint na interval.

## <a name="aggregation-types"></a>Typy agregace 

V Průzkumníku metrik je dostupných pět základních agregačních typů. Průzkumník metrik skryje agregace, které jsou nepodstatné a nelze je použít pro danou metriku. 

- **Sum** – součet všech hodnot zachycených v intervalu agregace. Někdy se označuje jako celková agregace.
- **Count** – počet měření zachycených v intervalu agregace. Count se nezobrazuje na hodnotě měření, pouze v počtu záznamů. 
- **Average** – průměr hodnot metriky zachycených v intervalu agregace. Pro většinu metrik je tato hodnota součet/počet. 
- **Min** – nejmenší hodnota zachycená v intervalu agregace.
- **Max** – největší hodnota zachycená v intervalu agregace.

Předpokládejme například, že se v grafu zobrazuje celková metrika **sítě** pro virtuální počítač s využitím agregace **Sum** za posledních 24 hodinového intervalu. Časový rozsah a členitost lze změnit v pravém horním rohu grafu, jak je vidět na následujícím snímku obrazovky.

:::image type="content" source="media/metrics-aggregation-explained/time-range-granularity-picker.png" alt-text="Snímek obrazovky znázorňující výběr časového rozsahu a časové rozlišení" border="true":::

Pro časovou členitost = 30 minut a časový rozsah = 24 hodin:

- Graf je vykreslen z 48 datapoints. To je 24 hodin × 2 datapoints za hodinu (60 min/30min) agregovaných na 1 minutu. 
- Spojnicový graf spojuje tečky 48 v oblasti vykreslení grafu. 
- Každý DataPoint představuje součet všech odchozích bajtů odesílaných během každé z příslušných 30-minimálních časových období. 


 :::image type="content" source="media/metrics-aggregation-explained/24-hour-30-min-gran.png" alt-text="Snímek obrazovky zobrazující data na spojnicovém grafu nastavené na 24 hodinový časový rozsah a na 30 minutový interval členitosti" border="true" lightbox="media/metrics-aggregation-explained/24-hour-30-min-gran.png":::

*Pokud chcete zobrazit větší verze, klikněte na obrázky v této části.*

Pokud časové rozlišení přepnete na 15 minut, graf se vykreslí z 96 agregovaných datových bodů. To znamená, že 60 min/15 minut = 4 databody za hodinu × 24 hodin.

:::image type="content" source="media/metrics-aggregation-explained/24-hour-15-min-gran.png" alt-text="Snímek obrazovky zobrazující data na spojnicovém grafu nastavené na 24 hodinový časový rozsah a na 15 minutové časové rozlišení" border="true" lightbox="media/metrics-aggregation-explained/24-hour-15-min-gran.png":::

Pro časové členitosti 5 minut získáte 24 x (60/5) = 288 bodů. 

:::image type="content" source="media/metrics-aggregation-explained/24-hour-5-min-gran.png" alt-text="Snímek obrazovky zobrazující data na spojnicovém grafu nastavené na 24 hodinový časový rozsah a na 5 minutový interval členitosti" border="true" lightbox="media/metrics-aggregation-explained/24-hour-15-min-gran.png":::

Pro časové členitosti 1 minuty (nejmenší možná v grafu) získáte 24 x 60/1 = 1440 bodů. 

:::image type="content" source="media/metrics-aggregation-explained/24-hour-1-min-gran.png" alt-text="Snímek obrazovky zobrazující data na spojnicovém grafu nastavené na 24 hodinový časový rozsah a na 1 minutu členitost času" border="true" lightbox="media/metrics-aggregation-explained/24-hour-1-min-gran.png":::

Grafy pro tyto souhrny vypadají jinak, jak je znázorněno na předchozích snímcích obrazovky.  Všimněte si, že tento virtuální počítač má velký výstup v krátkém časovém období vzhledem ke zbytku časového intervalu.  

Časové rozlišení umožňuje upravit poměr mezi signálem a hlukem v grafu. Vyšší agregace odstraňují šum a hladké špičky.  Všimněte si variací v dolním grafu od 1 minut a jejich vyhlazení při přechodu na vyšší hodnoty členitosti. 

Toto vyhlazení chování je důležité, když odesíláte tato data do jiných systémů – například výstrahy. Obvykle obvykle nechcete být upozorňováni pomocí velmi krátkých Špičk v čase procesoru nad 90%. Pokud ale procesor zůstane v 90% po dobu 5 minut, je to nejspíš důležité. Pokud nastavíte pravidlo výstrahy pro procesor (nebo libovolnou metriku), bude větší časový interval větší, než bude možné snížit počet nepravdivých výstrah. 

Je důležité určit, co je pro vaše zatížení "normální", abyste věděli, jaký časový interval je nejvhodnější. Toto je jedna z výhod [dynamických výstrah](../alerts/alerts-dynamic-thresholds.md), což je jiné téma, které zde není pokryto.  

## <a name="how-the-system-collects-metrics"></a>Způsob, jakým systém shromažďuje metriky

Shromažďování dat se liší podle metriky. Existují dva typy období shromažďování.

### <a name="measurement-collection-frequency"></a>Frekvence shromažďování měření 

- **Regular** – metrika se shromažďuje v konzistentním časovém intervalu, který se neliší.

- **Založené na aktivitách** – metrika se shromažďuje v závislosti na tom, kdy dojde k transakci určitého typu. Každá transakce má položku metriky a časové razítko. Nejsou shromažďovány v pravidelných intervalech, takže v daném časovém období existuje různý počet záznamů. 

### <a name="granularity"></a>Členitost

Minimální časový interval je 1 minuta, ale podkladový systém může data rychleji zachytit v závislosti na metrikě. Například procento využití procesoru se sleduje v pravidelných intervalech každých 15 sekund. Vzhledem k tomu, že chyby HTTP jsou sledovány jako transakce, může snadno překročit více než jednu minutu. Jiné metriky, jako je například úložiště SQL, se zaznamenávají každých 20 minut. Tato volba je až pro jednotlivé poskytovatele a typ prostředků. Většina zkuste zadat nejmenší možný interval.

### <a name="dimensions-splitting-and-filtering"></a>Rozměry, rozdělování a filtrování

Metriky jsou zachyceny pro každý jednotlivý prostředek. Nicméně úroveň, na které se metriky shromažďují, ukládají a můžou být grafy, se může lišit. Tato úroveň je reprezentovaná dalšími metrikami dostupnými v **dimenzích metrik**. Každý poskytovatel prostředků získá informace o tom, jak jsou data shromažďovaná. Azure Monitor definuje pouze to, jak mají být takové podrobnosti prezentovány a uloženy. 

Při vytváření grafu metriky v Průzkumníku metrik máte možnost "rozdělit" graf podle dimenze.  Rozdělení grafu znamená, že hledáte podkladová data pro více podrobností a vidíte tato data v Průzkumníkovi metriky nebo filtrovaná.

Například [Microsoft. ApiManagement/Service](../platform/metrics-supported.md#microsoftapimanagementservice) má jako dimenzi pro mnoho metrik *umístění* . 

- **Kapacita** je jedna taková metrika. Dimenze *umístění* implikuje, že základní systém ukládá záznam metriky pro kapacitu každého umístění, nikoli jenom jednu pro agregovanou částku. Tyto informace pak můžete načíst nebo rozdělit v grafu metriky.  

- V rámci **celkového trvání žádostí o bránu** jsou 2 *umístění* dimenzí a *název hostitele*, které vám umožní znát umístění doby trvání a název hostitele, ze kterého pochází.  

- Jedna z flexibilnější metriky, **požadavky**, má 7 různých dimenzí. 
 
Podrobnosti o jednotlivých metrikách a dostupných dimenzích najdete v článku [podporované metriky](../platform/metrics-supported.md) Azure monitor. Kromě toho může dokumentace pro každého poskytovatele prostředků a typ poskytovat další informace o dimenzích a o tom, co měří.

K dig problému můžete použít rozdělení a filtrování společně. Níže je příklad obrázku ukazujícího *průměrné bajty zápisu na disk* pro skupinu virtuálních počítačů ve skupině prostředků. Máme souhrn všech virtuálních počítačů s touto metrikou, ale můžeme chtít, abyste si DIGI, které jsou vlastně zodpovědné za špičky kolem 6:00. Jsou to stejný počítač? Kolik počítačů je součástí?  

:::image type="content" source="media/metrics-aggregation-explained/total-disk write-bytes-all-VMs.png" alt-text="Snímek obrazovky zobrazující celkový počet bajtů zápisu na disk pro všechny virtuální počítače ve skupině prostředků hotelů společnosti Contoso" border="true" lightbox="media/metrics-aggregation-explained/total-disk write-bytes-all-VMs.png":::

*Pokud chcete zobrazit větší verze, klikněte na obrázky v této části.*

Když použijeme rozdělování, můžeme zobrazit podkladová data, ale jedná se o trochu pracovní. Vypne se 20 virtuálních počítačů, které se agreguje do grafu uvedeného výše. V tomto případě jsme použili naši myš k najetí myší na velkou špičku na 6:00, která nám oznamuje, že je to příčina CH-DCVM11. je ale těžké zobrazit zbývající data přidružená k tomuto virtuálnímu počítači, protože jiné virtuální počítače mají v grafu zbytečný přehled. 

:::image type="content" source="media/metrics-aggregation-explained/split-total-disk write-bytes-all-VMs.png" alt-text="Snímek obrazovky s bajty zápisu na disk u všech virtuálních počítačů ve skupině prostředků v hotelů společnosti Contoso rozdělené podle názvu virtuálního počítače" border="true" lightbox="media/metrics-aggregation-explained/split-total-disk write-bytes-all-VMs.png":::

Použití filtrování vám umožní vyčistit graf, aby se zobrazila informace o tom, co se skutečně děje. Můžete zaškrtnout nebo zrušit kontrolu virtuálních počítačů, které chcete zobrazit. Všimněte si tečkovaná čára. Ty jsou uvedené v pozdější části.

:::image type="content" source="media/metrics-aggregation-explained/split-filter-total-disk write-bytes-all-VMs.png" alt-text="Snímek obrazovky s bajty zápisu na disk pro všechny virtuální počítače ve skupině prostředků v hotelů společnosti Contoso rozdělené a filtrované podle názvu virtuálního počítače" border="true" lightbox="media/metrics-aggregation-explained/split-filter-total-disk write-bytes-all-VMs.png":::

Další informace o tom, jak zobrazit rozdělená data dimenze v grafu Průzkumníka metrik, najdete v tématu [Pokročilé funkce v Průzkumníkovi metrik – filtry a rozdělení](../essentials/metrics-charts.md#filters).

### <a name="null-and-zero-values"></a>Hodnoty NULL a nula

Když systém očekává data metrik z prostředku, ale neobdrží ho, zaznamená hodnotu NULL.  Hodnota NULL se liší od nulové hodnoty, což je důležité při výpočtu agregací a vytváření grafů. Hodnoty NULL se nepočítají jako platná měření. 

Hodnoty NULL se v různých grafech zobrazují odlišně. Bodové zobrazení v grafu přeskočí tečku. Pruhové grafy přeskočí zobrazení pruhu. U spojnicových grafů se hodnota NULL může zobrazit jako [tečkované nebo přerušované čáry](../essentials/metrics-troubleshoot.md#chart-shows-dashed-line) , jako jsou zobrazené na snímku obrazovky v předchozí části. Při výpočtu průměrů, které zahrnují hodnoty NULL, je k dispozici méně datových bodů, z nichž lze použít průměr.  V některých případech může někdy dojít k neočekávanému vynechání hodnot v grafu, ale obvykle méně, než kdyby byla hodnota převedena na nulu a použita jako platná hodnota DataPoint.  

[Vlastní metriky](../essentials/metrics-custom-overview.md) vždy používají hodnoty null, pokud nejsou přijata žádná data. S [metrikami platforem](../data-platform.md)každý poskytovatel prostředků rozhoduje o tom, jestli se mají používat nuly nebo hodnoty null, podle toho, co je pro danou metriku největší smysl.

Výstrahy Azure Monitor používají hodnoty, které poskytovatel prostředků zapisuje do databáze metrik, takže je důležité zjistit, jak poskytovatel prostředků zpracovává hodnoty NULL zobrazením dat jako první.

## <a name="how-aggregation-works"></a>Jak funguje agregace

Grafy metrik v předchozím systému zobrazují různé typy agregovaných dat. Systém předběžně agreguje data tak, aby se požadované grafy zobrazovaly rychleji, aniž by bylo nutné s velkým počtem opakovaných výpočtů.  

V tomto příkladu:

- Shromažďujeme **fiktivní** transakční metriku s názvem **chyby HTTP** . 
- *Server* je dimenze pro metriky **selhání protokolu HTTP** .
- Máme 3 servery – Server A, B a C.

Abychom zjednodušili vysvětlení, zahájíme pouze typ agregace SUM. 

### <a name="sub-minute-to-1-minute-aggregation"></a>Agregace za minutu do 1 minuty

První nezpracovaná data metriky se shromažďují a ukládají v databázi Azure Monitor metrik. V takovém případě má každý server záznamy transakcí uložené s časovým razítkem, protože *Server* je dimenze. Vzhledem k tom, že nejmenší časové období, které si můžete zobrazit jako zákazníka, je 1 minuta, tato časová razítka se nejprve agreguje do 1 minutové hodnoty metriky pro každý jednotlivý Server.  Proces agregace pro server B je zobrazen na obrázku níže. Servery a a C jsou prováděny stejným způsobem a mají různá data.  

:::image type="content" source="media/metrics-aggregation-explained/sub-minute-transaction.png" alt-text="Snímek obrazovky s transakčními záznamy z dílčích minut do agregací o počtu 1 minut. " border="false":::

Výsledné agregované hodnoty na 1 minutu jsou uloženy jako nové položky v databázi metrik, aby je bylo možné shromáždit pro pozdější výpočty. 

:::image type="content" source="media/metrics-aggregation-explained/sub-minute-transaction-dimension-aggregated.png" alt-text="Snímek obrazovky s více než 1 minutou agregovaným záznamem napříč dimenzí serveru. Server A, B a C se zobrazí jednotlivě." border="false":::

### <a name="dimension-aggregation"></a>Agregace dimenzí

1 minutové výpočty se pak sbalí podle dimenze a znovu se uloží jako jednotlivé záznamy.   V tomto případě se všechna data ze všech jednotlivých serverů agreguje do metriky intervalu 1 minuty a ukládají se do databáze metrik pro použití v pozdějších agregacích.

:::image type="content" source="media/metrics-aggregation-explained/1-minute-transaction-dimension-flattened-aggregated.png" alt-text="Snímek obrazovky s více než 1 minutou agregované položky serveru A, B a C agregované do 1 minuty pro celé servery" border="false":::

Pro přehlednost je v následující tabulce uvedena metoda agregace.

| Období   | Server A | Server B | Server C | Sum (A + B + C)|
| -------- | -------- | -------- | -------- | --------   |  
| Minuta 1 | 1        | 1        | 1        | 3          |
| Minuta 2 | 0        | 5        | 1        | 6          |
| Minuta 3 | 0        | 5        | 1        | 6          |
| Minuta 4 | 2        | 3        | 4        | 9          |
| Minuta 5 | 1        | 0        | 3        | 4          |
| Minuta 6 | 1        | 0        | 4        | 5          |
| Minuta 7 | 1        | 2        | 4        | 7          |
| Minuta 8 | 0        | 1        | 0        | 1          |
| Minuty 9 | 1        | 1        | 4        | 6          |
| Minuta 10| 2        | 1        | 0        | 3          |

Výše se zobrazuje jenom jedna dimenze, ale u **všech dimenzí** , které podporuje metrika, se vyskytuje stejný agregační a proces úložiště.

- Shromáždí hodnoty do agregované sady na 1 minutu, kterou tato dimenze používá. Uložte tyto hodnoty. 
- Sbalí dimenzi do agregovaného SOUČTu o 1 minutu. Uložte tyto hodnoty. 

Pojďme předvést další dimenzi selhání HTTP s názvem síťový adaptér. Řekněme, že pro každý server byl narůzný počet adaptérů.

- Server A má 1 adaptér.
- Server B má 2 adaptéry.
- Server C má 3 adaptéry

Data pro následující transakce jsme shromáždili samostatně. Budou označeny:

- Čas
- Hodnota
- Server, ze kterého transakce pochází
- Adaptér, ze kterého transakce pochází

Každý z těchto koncových proudů by se pak měl agregovat do hodnot časových řad na 1 minutu a uloží se do databáze metriky Azure Monitor:

- Server A, adaptér 1
- Server B, adaptér 1
- Server B, adaptér 2
- Server C, adaptér 1
- Server C, adaptér 2
- Server C, adaptér 3

Kromě toho se uloží taky následující sbalená agregace:

- Server A, adaptér 1 (vzhledem k tomu, že není k dispozici žádné sbalení, bude znovu uložen)
- Server B, adaptér 1 + 2
- Server C, adaptér 1 + 2 + 3
- VŠECHNY servery, adaptéry všechny

To ukazuje, že metriky s velkým počtem dimenzí mají větší počet agregací. Není důležité znát všechny permutace, stačí pochopit jejich důvod. Systém chce mít individuální data a agregovaná data uložená pro rychlé načítání pro přístup k libovolnému grafu. Systém vybere buď relevantní uloženou agregaci, nebo základní nezpracovaná data v závislosti na tom, co se rozhodnete zobrazit. 

### <a name="aggregation-with-no-dimensions"></a>Agregace bez rozměrů

Vzhledem k tomu, že tato metrika má *Server* Dimension, můžete získat základní data pro server a, B a C výše prostřednictvím rozdělení a filtrování, jak je vysvětleno výše v tomto článku. Pokud metrika neobsahovala jako dimenzi *Server* , měli byste jako zákazník získat přístup k agregovaným částkám o počtu 1 minut, které jsou na diagramu uvedené černě. To znamená, že hodnoty 3, 6, 6, 9 atd. Systém také neudělá základní práci k agregaci hodnot rozdělení, protože by je nikdy nepoužíval v Průzkumníkovi metrik nebo jejich odeslání prostřednictvím REST API metriky. 

## <a name="viewing-time-granularities-above-1-minute"></a>Zobrazení časových rozlišení nad 1 minutu

Pokud požadujete metriky při větší členitosti, systém použije agregované součty o velikosti 1 minuty pro výpočet součtů pro větší časové rozlišení.  Níže jsou tečkované čáry ukazují metodu sčítání pro členitost na 2 minuty a dobu 5 minut. Znovu Zobrazujeme pouze souhrnný typ agregace pro jednoduchost.

:::image type="content" source="media/metrics-aggregation-explained/1-minute-to-2-min-5-min.png" alt-text="Snímek obrazovky zobrazující více než jednu minutu agregovaných záznamů napříč dimenzí serveru agregovaných do 2 – min a 5 – minimálních časových období." border="false":::

Pro členitost času 2 minuty.

| Období       | Celková       
| -------------|-------------|  
| Minuta 1 & 2 | (3 + 6) = 9 |
| Minuta 3 & 4 | (6 + 9) = 15|
| Minuta 4 & 5 | (4 + 5) = 9 |
| Minuta 6 & 7 | (7 + 1) = 8 |
| Minuta 8 & 9 | (6 + 3) = 9 |

Pro časovou členitost 5 minut.

| Období              |            Celková        |
|---------------------|------------------------|  
| Minuta 1 až 5  | 3 + 6 + 6 + 9 + 4 = 28 |
| Minuty 6 až 10 | 5 + 7 + 1 + 6 + 3 = 22 |

Systém používá uložená agregovaná data, která poskytují nejlepší výkon. 

Níže je větší diagram pro výše uvedený agregační proces 1-minute s některými šipkami pro zlepšení čitelnosti.

:::image type="content" source="media/metrics-aggregation-explained/sum-aggregation-full.png" alt-text="Snímek obrazovky znázorňující konsolidaci předchozích 3 snímků obrazovky Více než jedna minuta agregovaných položek napříč dimenzí serveru agregovaná v intervalu 1 – minutu, 2-minut a 5-minut. Server A, B a C se zobrazí jednotlivě." border="false":::

## <a name="more-complex-example"></a>Složitější příklad

Následuje větší příklad použití hodnot fiktivní metriky nazvané doba odezvy HTTP v milisekundách.  Zde zavádíme další úrovně složitosti.

1. Zobrazujeme agregaci pro součet, počet, minimum a maximum a výpočet pro průměr.
2. Zobrazujeme hodnoty NULL a způsob, jakým ovlivňují výpočty. 

Představte si následující příklad. Pole a šipky ukazují příklady, jak jsou hodnoty agregovány a počítány. 

Stejný proces předagregačního s 1 minutou, jak je popsáno v předchozí části, se vyskytuje pro součty, počet, minimum a maximum.  Average ale není předem agregovaný. Přepočítá se pomocí agregovaných dat, aby se předešlo chybám výpočtů.
 
:::image type="content" source="media/metrics-aggregation-explained/full-aggregation-example-all-types.png" alt-text="Snímek obrazovky s komplexním příkladem agregace a výpočtu součtu, počtu, minimální, maximální a průměrné hodnoty od 1 minuty do 10 minut." border="false" lightbox="media/metrics-aggregation-explained/full-aggregation-example-all-types.png":::

Zvažte minuty 6 pro agregaci o počtu 1 minut, jak je zvýrazněna výše. Tato minuta je bod, ve kterém server B přešel do režimu offline a zastavil data pro vytváření sestav, třeba kvůli restartování. 

Od minuty 6 výše jsou vypočítané 1 minutové typy agregace: 

| Typ agregace | Hodnota        | Poznámky |
|------------------|--------------|-------|
| Sum              | 53 + 20 = 73 | |
| Počet            | 2            | Zobrazuje efekt hodnot NULL.  Hodnota by byla 3, pokud byl server online.  |
| Minimum          | 20           | |
| Maximum          | 53           | |
| Průměr          | 73/2       | Vždy je součet dělený počtem. Nikdy se neukládá a vždy se přepočítá pro každou časovou členitost pomocí agregovaných čísel pro tuto členitost. Všimněte si přepočítání pro prodlevy 5 minut a 10 minut, jak je zvýrazněno výše. |

Barva červeného textu označuje hodnoty, které mohou být považovány za z normálního rozsahu, a ukazuje, jak se šíří (nebo nezpůsobuje) podle časového rozlišení. Všimněte si, jak *minimum* a *Maximum* značí základní anomálie, zatímco *průměr* a *součet* ztratí tyto informace v průběhu časového rozlišení.  

Můžete také zjistit, že hodnoty NULL poskytují lepší výpočet průměru, než kdyby místo toho bylo použito nula.  

> [!NOTE] 
> I když ne v tomto příkladu je *počet* roven *součtu* v případech, kdy je metrika vždy zachycena s hodnotou 1. To je běžné v případě, že metrika sleduje výskyt transakční události – například počet chyb HTTP zmíněných v předchozím příkladu v tomto článku.

## <a name="next-steps"></a>Další kroky

- [Začínáme s Průzkumníkem metrik](../essentials/metrics-getting-started.md)
- [Průzkumník pokročilých metrik](../essentials/metrics-charts.md)
