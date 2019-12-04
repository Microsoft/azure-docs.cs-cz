---
title: Funkce strojového učení v Azure Průzkumník dat
description: Analýza základních příčin v Azure Průzkumník dat pomocí clusteringu strojového učení
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: fe72031ef9ade7473dc4d5de7e090e92ef2a6843
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74769927"
---
# <a name="machine-learning-capability-in-azure-data-explorer"></a>Funkce strojového učení v Azure Průzkumník dat

Azure Průzkumník dat, což je platforma pro analýzy velkých objemů dat, se používá k monitorování stavu služeb, QoS nebo nefunkčních zařízení pro chování neobvyklé pomocí integrovaných funkcí [detekce anomálií a předpovědi](/azure/data-explorer/anomaly-detection) . Po zjištění neobvyklé vzoru se provede analýza hlavní příčiny (RCA) pro zmírnění nebo vyřešení anomálií.

Proces diagnostiky je složitý a zdlouhavý a prováděný odborníky na domény. Proces zahrnuje načítání a spojování dalších dat z různých zdrojů pro stejný časový rámec, hledání změn v distribuci hodnot ve více dimenzích, vytváření grafů dalších proměnných a další techniky na základě znalostí v doméně a intuition. Vzhledem k tomu, že jsou tyto scénáře diagnostiky běžné v Azure Průzkumník dat, jsou k dispozici moduly plug-in strojového učení, aby byla fáze diagnostiky jednodušší a zkrácena doba trvání RCA.

Azure Průzkumník dat má tři moduly plug-in Machine Learning: [`autocluster`](/azure/kusto/query/autoclusterplugin), [`basket`](/azure/kusto/query/basketplugin)a [`diffpatterns`](/azure/kusto/query/diffpatternsplugin). Všechny moduly plug-in implementují algoritmy clusteringu. Moduly plug-in `autocluster` a `basket` v clusteru jednu sadu záznamů a modul plug-in pro `diffpatterns` jsou rozdíly mezi dvěma sadami záznamů.

## <a name="clustering-a-single-record-set"></a>Clustering jedné sady záznamů

Běžný scénář zahrnuje datovou sadu vybranou konkrétními kritérii, jako je časové okno, které vykazuje neobvyklé chování, čtení zařízení s vysokou teplotou, příkazy dlouhé doby trvání a uživatele s největším útratou. Rádi bychom v datech našli snadno a rychle způsob, jak najít běžné vzory (segmenty). Vzory jsou podmnožinou sady dat, jejíž záznamy sdílejí stejné hodnoty více dimenzí (kategorií sloupců). Následující dotaz sestaví a znázorňuje časovou řadu výjimek služby za týden v desítkových přihrádkách:

**\[** [**kliknutím spustíte dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5XPsaoCQQyF4d6nCFa7oHCtZd9B0F6G8ajByWTJZHS5+PDOgpVgYRn485EkOAnno9NAriWGFKw7QfQYUy0O43zZ0JNKFQnG/5jrbmeIXHBgwd6DjH2/JVqk2QrTL1aYvlifa4tni29YlzaiUK4yRK3Zu54006dBZ1N5/+X6PqpRI23+pFGGfIKRtz5egzk92K+dsycMyz3szhGEKWJ01lxI760O9ABuq0bMcvV2hqFoqnOz7F9BdSHlSgEAAA==) **\]**

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m
| render timechart with(title="Service exceptions over a week, 10 minutes resolution")
```

![Výjimky služby timechart](media/machine-learning-clustering/service-exceptions-timechart.png)

Počet výjimek služby koreluje s celkovým provozem služby. Po dobu pracovních dnů od pondělí do pátku můžete jasně zobrazit denní vzor, ve kterém se zvyšuje počet výjimek služby v polovině dne a v noci dojde k poklesu počtu. Po víkendu se zobrazí ploché nízké počty. Špičky výjimek je možné zjistit pomocí [detekce anomálií časové řady](/azure/data-explorer/anomaly-detection?#time-series-anomaly-detection) v Azure Průzkumník dat.

Druhá špička v datech probíhá v úterý odpoledne. Následující dotaz slouží k dalšímu diagnostikování tohoto špičky. Použijte dotaz pro překreslení grafu kolem špičky ve vyšším rozlišení (osm hodin v jedné minutové přihrádky), abyste ověřili, jestli se jedná o ostrý špičku a abyste viděli jeho ohraničení.

**\[** [**kliknutím spustíte dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAyXNwQrCMBAE0Hu/YvHUooWkghSl/yDoyUsJyWpCk2xJNnjx403pbeYwbzwyBBdnnoxiZBewHYS89GLshzNIeRWiuzUGA83al8yYXPzI5gdBLdjnWjFDLGHSVCK3HVCEe0LtMj4r9mAVVngnCvsLMO3hOFqo2goyVCxhNJhgu9dWJYavY9uyY4/T4UV1XVm2CEM0kFe34AnkBhXGOs7kCzuKh+4P3/XM5M8AAAA=) **\]**

```kusto
let min_t=datetime(2016-08-23 11:00);
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to min_t+8h step 1m
| render timechart with(title="Zoom on the 2nd spike, 1 minute resolution")
```

![Zaměřte se na timechart špičky](media/machine-learning-clustering/focus-spike-timechart.png)

Zobrazuje se úzká špička od 15:00 do 15:02. V následujícím dotazu spočítáte výjimky v tomto okně o dvou minutách:

**\[** [**kliknutím spustíte dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA8tJLVHIzcyLL0hNzI4vsU1JLEktycxN1TAyMDTTNbDQNTJWMDS1MjDQtObKASlNrCCk1AioNCU1Nz8+Oae0uCS1KDMv3ZCrRqE8I7UoVSGgKDU5szg1BKgvuCQxt0AhKbWkPDU1TwPhBj09hCWaQI3J+aV5JQACnQoRpwAAAA==) **\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| count
```

|Počet |
|---------|
|972    |

V následujícím dotazu je ukázka 20 výjimek z 972:

**\[** [**kliknutím spustíte dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA4XOsQrCMBSF4b1Pccd2aLmJKKL4DoLu4doeNDSJJb1SBx/eOHV0/37OCVCKPrkJMjo9DaJQH1FbNruW963dkNkemJtjFX5U3v+oLXRAfLo+vGZF9uluqg8tD2TQOaP3M66lu6jEiW7QBUj1+qHr1pGmhCojyPIX7QHvzakAAAA=) **\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| take 20
```

| PreciseTimeStamp            | Oblast | ScaleUnit | DeploymentId                     | Zarážka s trasováním | Hostiteli                          |
|-----------------------------|--------|-----------|----------------------------------|------------|--------------------------------------|
| 2016-08-23 15:00:08.7302460 | SCUs   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 100005     | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:09.9496584 | SCUs   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 8d257da1-7a1c-44f5-9acd-f9e02ff507fd |
| 2016-08-23 15:00:10.5911748 | SCUs   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 100005     | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:12.2957912 | SCUs   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007007   | f855fcef-ebfe-405d-aaf8-9c5e2e43d862 |
| 2016-08-23 15:00:18.5955357 | SCUs   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 9d390e07-417d-42eb-bebd-793965189a28 |
| 2016-08-23 15:00:20.7444854 | SCUs   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 6e54c1c8-42d3-4e4e-8b79-9bb076ca71f1 |
| 2016-08-23 15:00:23.8694999 | eus2   | su2       | 89e2f62a73bb4efd8f545aeae40d7e51 | 36109      | 19422243-19b9-4d85-9ca6-bc961861d287 |
| 2016-08-23 15:00:26.4271786 | ncus   | su1       | e24ef436e02b4823ac5d5b1465a9401e | 36109      | 3271bae4-1c5b-4f73-98ef-cc117e9be914 |
| 2016-08-23 15:00:27.8958124 | SCUs   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 904498     | 8cf38575-fca9-48ca-bd7c-21196f6d6765 |
| 2016-08-23 15:00:32.9884969 | SCUs   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007007   | d5c7c825-9d46-4ab7-a0c1-8e2ac1d83ddb |
| 2016-08-23 15:00:34.5061623 | SCUs   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 1002110    | 55a71811-5ec4-497a-a058-140fb0d611ad |
| 2016-08-23 15:00:37.4490273 | SCUs   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007006   | f2ee8254-173c-477d-a1de-4902150ea50d |
| 2016-08-23 15:00:41.2431223 | SCUs   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 103200     | 8cf38575-fca9-48ca-bd7c-21196f6d6765 |
| 2016-08-23 15:00:47.2983975 | ncus   | su1       | e24ef436e02b4823ac5d5b1465a9401e | 423690590  | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:50.5932834 | SCUs   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 2a41b552-aa19-4987-8cdd-410a3af016ac |
| 2016-08-23 15:00:50.8259021 | SCUs   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 1002110    | 0d56b8e3-470d-4213-91da-97405f8d005e |
| 2016-08-23 15:00:53.2490731 | SCUs   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 36109      | 55a71811-5ec4-497a-a058-140fb0d611ad |
| 2016-08-23 15:00:57.0000946 | eus2   | su2       | 89e2f62a73bb4efd8f545aeae40d7e51 | 64038      | cb55739e-4afe-46a3-970f-1b49d8ee7564 |
| 2016-08-23 15:00:58.2222707 | SCUs   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007007   | 8215dcf6-2de0-42bd-9c90-181c70486c9c |
| 2016-08-23 15:00:59.9382620 | SCUs   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007006   | 451e3c4c-0808-4566-a64d-84d85cf30978 |

### <a name="use-autocluster-for-single-record-set-clustering"></a>Použití autocluster () pro clusteringu s jednou sadou záznamů

I když existuje méně než tisíce výjimek, je stále obtížné najít běžné segmenty, protože v každém sloupci je více hodnot. Modul plug-in [`autocluster()`](/azure/kusto/query/autoclusterplugin) můžete použít k okamžitému extrakci malého seznamu běžných segmentů a nalezení zajímavých clusterů v průběhu dvou minut špičky, jak je vidět v následujícím dotazu:

**\[** [**kliknutím spustíte dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA4WOsQrCMBRF937FG5OhJYkoovQfBN1DbC8aTNqSvlgHP94IQkf3c+65AUzRD3aCe1hue8dgHyGM0rta7WuzIb09KCWPVfii7vUPNQXtEUfbhTwzkh9uunrTckcCnRI6P+NSvDO7ONEVvACDWD80zRqRRcTThVxa5DKPv00hP81KL1+4AAAA) **\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate autocluster()
```

| SegmentId | Počet | Procento | Oblast | ScaleUnit | DeploymentId | Hostiteli |
|-----------|-------|------------------|--------|-----------|----------------------------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1\. místo | 94 | 9.67078189300411 | SCUs | su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |
| 2 | 82 | 8.43621399176955 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 3 | 68 | 6.99588477366255 | SCUs | su3 | 90d3d2fc7ecc430c9621ece335651a01 |  |
| 4 | 55 | 5.65843621399177 | ZEU | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |

Můžete vidět z výše uvedených výsledků, které nejvíce dominantní segment obsahuje 65,74% celkových záznamů výjimek a sdílí čtyři dimenze. Další segment je mnohem méně častý, obsahuje jenom 9,67% záznamů a sdílí tři dimenze. Ostatní segmenty jsou dokonce méně běžné. 

Autocluster používá proprietární algoritmus pro dolování více dimenzí a extrakci zajímavých segmentů. "Zajímavá" znamená, že každý segment má významné pokrytí jak sady záznamů, tak sady funkcí. Segmenty se také liší, což znamená, že každá z nich je výrazně odlišná od ostatních. Jeden nebo více těchto segmentů může být relevantní pro proces RCA. Aby se minimalizovala revize a posouzení segmentu, příkaz autocluster extrahuje jenom malý seznam segmentů.

### <a name="use-basket-for-single-record-set-clustering"></a>Pro clustering s jedním záznamem sady záznamů použijte košík ()

Můžete také použít modul plug-in [`basket()`](/azure/kusto/query/basketplugin) , jak je vidět v následujícím dotazu:

**\[** [**kliknutím spustíte dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA4WOsQ6CMBgGd57iH9sB0tZojMZ3MNG9KfBFG1og7Y84+PDWidH9LncBTNGPdoYbLF96x2AfIYzSh1oda7MjvT8pJc9V+KHu/Q81Be0RJ9uFJTOSHx+6+tD6RAJdEzqfcS/ejV2cqQWvwCi2h6bZIrKIeLmwlBa1Lg9gIb9KJv2TswAAAA==) **\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate basket()
```

| SegmentId | Počet | Procento | Oblast | ScaleUnit | DeploymentId | Zarážka s trasováním | Hostiteli |
|-----------|-------|------------------|--------|-----------|----------------------------------|------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1\. místo | 642 | 66.0493827160494 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  |  |
| 2 | 324 | 33.3333333333333 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 | 0 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 3 | 315 | 32.4074074074074 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 | 16108 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 4 | 328 | 33.7448559670782 |  |  |  | 0 |  |
| 5 | 94 | 9.67078189300411 | SCUs | su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |  |
| 6 | 82 | 8.43621399176955 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |  |
| 7 | 68 | 6.99588477366255 | SCUs | su3 | 90d3d2fc7ecc430c9621ece335651a01 |  |  |
| 8 | 167 | 17.1810699588477 | SCUs |  |  |  |  |
| 9 | 55 | 5.65843621399177 | ZEU | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |  |
| 10 | 92 | 9.46502057613169 |  |  |  | 10007007 |  |
| 11 | 90 | 9.25925925925926 |  |  |  | 10007006 |  |
| 12 | 57 | 5.8641975308642 |  |  |  |  | 00000000-0000-0000-0000-000000000000 |

Koš implementuje apriori algoritmus pro dolování sady položek a extrahuje všechny segmenty, jejichž pokrytí sady záznamů je nad prahovou hodnotou (výchozí hodnota je 5%). Vidíte, že další segmenty byly extrahovány podobnými těmi (například segmenty 0, 1 nebo 2, 3).

Oba moduly plug-in jsou výkonné a snadno použitelné, ale jejich významná omezení znamená, že cluster používá jeden záznam, který není pod dohledem (bez popisků). Proto nemažte, zda extrahované vzory charakterizují vybranou sadu záznamů (záznamy neobvyklé) nebo globální sadu záznamů.

## <a name="clustering-the-difference-between-two-records-sets"></a>Rozclusteringu rozdíl mezi dvěma sadami záznamů

Modul plug-in [`diffpatterns()`](/azure/kusto/query/diffpatternsplugin) přechází omezení `autocluster` a `basket`. `Diffpatterns` přebírá dvě sady záznamů a extrahuje hlavní segmenty, které jsou mezi nimi jiné. Jedna sada obvykle obsahuje neobvyklé záznamové sady, která se analyzuje (jedna analyzovaná pomocí `autocluster` a `basket`). Druhá sada obsahuje referenční sadu záznamů (standardní hodnoty). 

V níže uvedeném dotazu používáme `diffpatterns` k vyhledání zajímavých clusterů v průběhu dvou minut špičky, které jsou jiné než clustery v rámci standardních hodnot. Okno směrného plánu jsme definovali jako osm minut před 15:00 (při spuštění špičky). Také je potřeba, abyste rozšířili binární sloupec (AB), který určuje, jestli konkrétní záznam patří do směrného plánu nebo do neobvyklé sady. `Diffpatterns` implementuje algoritmus učení pod dohledem, ve kterém se dva štítky třídy vygenerovaly neobvyklé oproti příznaku standardních hodnot (AB).

**\[** [**kliknutím spustíte dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA42QzU+DQBDF7/wVcwOi5UtrmhJM4OzBRO9kWqbtpssuYacfGv94t0CrxFTd02by5jfvPUkMtVBlQ7gtOauQiUVNXhLFD5NoNknuIJ7Oo8hPHXmS4vEvaXKWWuoCDUmh6Jr8fj79Tv6HfOanEIbwRLgnQFhjAwviA5EC3hCcCYCq6gamEVsC1oB7LfoRt6iMYKEVvGtFQXfeNFKc7mXe2MjNVzl+mARR6lRU63Ipd4apFWodOx9w2FBL4D23tBSGXi3mhbG+OPPGVQTB+ITvg24dGN7vlN5JTxhc+dYAHZls4LzIxGr1k/B4iXcLbq50jfLNtd9i8OB2jD3KnW0dKstokG08Zby8uLbyCfX/tG46AgAA) **\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
let min_baseline_t=datetime(2016-08-23 14:50);
let max_baseline_t=datetime(2016-08-23 14:58); // Leave a gap between the baseline and the spike to avoid the transition zone.
let splitime=(max_baseline_t+min_peak_t)/2.0;
demo_clustering1
| where (PreciseTimeStamp between(min_baseline_t..max_baseline_t)) or
        (PreciseTimeStamp between(min_peak_t..max_peak_t))
| extend AB=iff(PreciseTimeStamp > splitime, 'Anomaly', 'Baseline')
| evaluate diffpatterns(AB, 'Anomaly', 'Baseline')
```

| SegmentId | CountA | CountB | Procenta | PercentB | PercentDiffAB | Oblast | ScaleUnit | DeploymentId | Zarážka s trasováním |
|-----------|--------|--------|----------|----------|---------------|--------|-----------|----------------------------------|------------|
| 0 | 639 | 21 | 65,74 | 1,7 | 64,04 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  |
| 1\. místo | 167 | 544 | 17,18 | 44,16 | 26,97 | SCUs |  |  |  |
| 2 | 92 | 356 | 9,47 | 28,9 | 19,43 |  |  |  | 10007007 |
| 3 | 90 | 336 | 9,26 | 27,27 | 18,01 |  |  |  | 10007006 |
| 4 | 82 | 318 | 8,44 | 25,81 | 17,38 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 5 | 55 | 252 | 5,66 | 20,45 | 14,8 | ZEU | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |
| 6 | 57 | 204 | 5,86 | 16,56 | 10,69 |  |  |  |  |

Nejvíce dominantní segment je stejný segment, který byl extrahován `autocluster`, jeho pokrytí v okně neobvyklé je také 65,74%. Ale jeho pokrytí na osmi minutách základního okna je pouze 1,7%. Rozdíl je 64,04%. Tento rozdíl vypadá jako v souvislosti s špičkou neobvyklé. Tento předpoklad můžete ověřit rozdělením původního grafu do záznamů patřících k tomuto problematickému segmentu a dalším segmentům, jak je vidět v následujícím dotazu:

**\[** [**kliknutím spustíte dotaz**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WRsWrDMBCG9zzF4cmGGuJUjh2Ktw7tUkLTzuEsnRNRnRQkuSQlD185yRTo0EWIO913/J8MRWBttxE6iC5INOhzRey20owhktd2V8EZwsiMXv/Q9Dpfe5I60Idm2kTkQ1E8AczMxMLjf1h4/IN1PzY7Ax0jWQWBdomvhyF/p512FroOMsIxA0zdTdpKn1bHSzmMzbX8TAfjTkw2vqpLp69VpYQaatEogXOBsqrbtl5WDake6yabXWjkv7WkFxeuPGqG5VzWqhQrIUqx6B/L1WKB6aBViy01imT2ANnau94QT9c35xlNVqQAjF9UhpSHAtiRO+lGG/MCUoZ7CTB4x7ePie5mNbk4QDVn6E+ThUT0SQh5iGlM7tHHX4WFgLHOAQAA) **\]**

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| extend seg = iff(Region == "eau" and ScaleUnit == "su7" and DeploymentId == "b5d1d4df547d4a04ac15885617edba57"
and ServiceHost == "e7f60c5d-4944-42b3-922a-92e98a8e7dec", "Problem", "Normal")
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m by seg
| render timechart
```

![Ověřuje se segment ' diffpattern ' timechart](media/machine-learning-clustering/validating-diffpattern-timechart.png)

Tento graf nám umožňuje zjistit, že špička v úterý byla odpoledne z důvodu výjimek z tohoto konkrétního segmentu zjištěná pomocí modulu plug-in `diffpatterns`.

## <a name="summary"></a>Souhrn

Moduly plug-in Machine Learning pro Azure Průzkumník dat jsou užitečné pro řadu scénářů. `autocluster` a `basket` implementovat bezdohledový algoritmus učení a je snadné ho používat. `Diffpatterns` implementuje algoritmus učení pod dohledem a i když je složitější, je výkonnější při extrakci různých segmentů pro RCA.

Tyto moduly plug-in se používají interaktivně ve scénářích ad hoc a v automatických službách monitorování téměř v reálném čase. V Azure Průzkumník dat za detekcí anomálií časové řady následuje proces diagnostiky, který je vysoce optimalizovaný pro splnění nezbytných standardů výkonu.
