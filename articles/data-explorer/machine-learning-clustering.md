---
title: Strojové učení funkce v Průzkumníku dat Azure
description: Pomocí clusteringu pro analýzu původní příčiny v Průzkumníku dat Azure machine learning.
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: bc72cc21ab525ec82d9ce4b24e80ce82d92a5d21
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233494"
---
# <a name="machine-learning-capability-in-azure-data-explorer"></a>Strojové učení funkce v Průzkumníku dat Azure

Průzkumník služby Azure Data, platformě pro analýzu velkých objemů dat se používá k monitorování stavu služby, technologie QoS nebo chybně fungující zařízení pro neobvyklé chování pomocí předdefinovaných [detekce anomálií a Prognózování](/azure/data-explorer/anomaly-detection) funkce. Po zjištění neobvyklého vzoru kořenové příčina Analysis (analýza RCA) se provádí na zmírnit dopady nebo vyřešit anomálii.

Proces diagnostiku je složité a dlouhé a provádí odborníky na domény. Proces zahrnuje načítání a připojit se k další data z různých zdrojů pro stejný časový rámec, hledá změny v distribuci hodnot u více dimenzí, vytvoření grafu další proměnné, a další techniky založen na znalosti domény a intuition. Protože tyto diagnózu scénáře jsou běžné v Průzkumníku dat Azure, jsou k dispozici usnadnili diagnostiku fází a zkraťte dobu trvání RCA moduly plug-in machine learning.

Průzkumník služby Azure Data má tři moduly plug-in Machine Learning: [ `autocluster` ](/azure/kusto/query/autoclusterplugin), [ `basket` ](/azure/kusto/query/basketplugin), a [ `diffpatterns` ](/azure/kusto/query/diffpatternsplugin). Všechny moduly plug-in implementovat clusteringu algoritmy. `autocluster` a `basket` moduly plug-in clusteru jedné sady záznamů a `diffpatterns` modulu plug-in clusterů rozdíly mezi dvěma sadami záznamů.

## <a name="clustering-a-single-record-set"></a>Clustering s jedním záznamem sady

Běžný scénář zahrnuje zvolila určitá kritéria, jako je časový interval, který vykazuje neobvyklé chování, odečty zařízení nejvyšší teplota, příkazy dlouhá doba trvání a horní útraty uživatelé datové sady. Rádi bychom rychlý a jednoduchý způsob, jak najít běžné vzory (segmentech) v datech. Vzory jsou podmnožinou datové sady, jejichž záznamy sdílejí stejné hodnoty přes více dimenzí (zařazené do kategorií sloupce). Následující dotaz vytvoří a zobrazí časových řad pro výjimky služby za týden do přihrádek deset minut:

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m
| render timechart with(title="Service exceptions over a week, 10 minutes resolution")
```

![Služba promítnu výjimky](media/machine-learning-clustering/service-exceptions-timechart.png)

Počet výjimek služby koreluje s celkovým provoz služby. Jasně vidíte denní vzor pracovních dní od pondělí do pátku, s nárůstem služby výjimka uprostřed den se počítá a zahodí v počtu v noci. Plochý s nízkou počty jsou viditelné přes víkend pokazil. Špičky výjimky lze zjistit pomocí [time series pro detekci anomálií](/azure/data-explorer/anomaly-detection?#time-series-anomaly-detection) v Průzkumníku dat Azure.

Druhý nárůst dat probíhá v úterý odpoledne. Následující dotaz se používá při další diagnostice tento zásobníku. Použijte dotaz pro překreslení grafu okolo nárůst vyšší rozlišení (8 hodin v minutových přihrádky) Chcete-li ověřit, zda je sharp zásobníku a zobrazte jeho okrajů.

```kusto
let min_t=datetime(2016-08-23 11:00);
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to min_t+8h step 1m
| render timechart with(title="Zoom on the 2nd spike, 1 minute resolution")
```

![Zaměřte se na promítnu zásobníku](media/machine-learning-clustering/focus-spike-timechart.png)

Vidíme úzký zásobníku intervalech od 15:00 do 15:02. V následujícím dotazem počet výjimek v tomto okně dvě minuty:

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| count
```

|Count |
|---------|
|972    |

V následujícím dotazem ukázkový 20 výjimky mimo 972:

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| take 20
```

| PreciseTimeStamp            | Oblast | ScaleUnit | DeploymentId                     | Zarážka s trasováním | ServiceHost                          |
|-----------------------------|--------|-----------|----------------------------------|------------|--------------------------------------|
| 2016-08-23 15:00:08.7302460 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 100005     | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:09.9496584 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 8d257da1-7a1c-44f5-9acd-f9e02ff507fd |
| 2016-08-23 15:00:10.5911748 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 100005     | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:12.2957912 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007007   | f855fcef-ebfe-405d-aaf8-9c5e2e43d862 |
| 2016-08-23 15:00:18.5955357 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 9d390e07-417d-42eb-bebd-793965189a28 |
| 2016-08-23 15:00:20.7444854 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 6e54c1c8-42d3-4e4e-8b79-9bb076ca71f1 |
| 2016-08-23 15:00:23.8694999 | eus2   | su2       | 89e2f62a73bb4efd8f545aeae40d7e51 | 36109      | 19422243-19b9-4d85-9ca6-bc961861d287 |
| 2016-08-23 15:00:26.4271786 | ncus   | su1       | e24ef436e02b4823ac5d5b1465a9401e | 36109      | 3271bae4-1c5b-4f73-98ef-cc117e9be914 |
| 2016-08-23 15:00:27.8958124 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 904498     | 8cf38575-fca9-48ca-bd7c-21196f6d6765 |
| 2016-08-23 15:00:32.9884969 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007007   | d5c7c825-9d46-4ab7-a0c1-8e2ac1d83ddb |
| 2016-08-23 15:00:34.5061623 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 1002110    | 55a71811-5ec4-497a-a058-140fb0d611ad |
| 2016-08-23 15:00:37.4490273 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007006   | f2ee8254-173c-477d-a1de-4902150ea50d |
| 2016-08-23 15:00:41.2431223 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 103200     | 8cf38575-fca9-48ca-bd7c-21196f6d6765 |
| 2016-08-23 15:00:47.2983975 | ncus   | su1       | e24ef436e02b4823ac5d5b1465a9401e | 423690590  | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:50.5932834 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 2a41b552-aa19-4987-8cdd-410a3af016ac |
| 2016-08-23 15:00:50.8259021 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 1002110    | 0d56b8e3-470d-4213-91da-97405f8d005e |
| 2016-08-23 15:00:53.2490731 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 36109      | 55a71811-5ec4-497a-a058-140fb0d611ad |
| 2016-08-23 15:00:57.0000946 | eus2   | su2       | 89e2f62a73bb4efd8f545aeae40d7e51 | 64038      | cb55739e-4afe-46a3-970f-1b49d8ee7564 |
| 2016-08-23 15:00:58.2222707 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007007   | 8215dcf6-2de0-42bd-9c90-181c70486c9c |
| 2016-08-23 15:00:59.9382620 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007006   | 451e3c4c-0808-4566-a64d-84d85cf30978 |

### <a name="use-autocluster-for-single-record-set-clustering"></a>Použít autocluster() pro jeden záznam, nastavte clustering

Přestože jsou nějaké výjimky menší než tisíc, je stále obtížné najít běžné segmenty, protože existuje více hodnot v jednotlivých sloupcích. Můžete použít [ `autocluster()` ](/azure/kusto/query/autoclusterplugin) modul plug-in, který okamžitě extrahovat malé seznam běžných segmentů a najít zajímavé clustery během dvou minut zásobníku, jak je znázorněno v následující dotaz:

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate autocluster()
```

| SegmentId | Count | Procenta | Oblast | ScaleUnit | DeploymentId | ServiceHost |
|-----------|-------|------------------|--------|-----------|----------------------------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1 | 94 | 9.67078189300411 | scus | su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |
| 2 | 82 | 8.43621399176955 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 3 | 68 | 6.99588477366255 | scus | su3 | 90d3d2fc7ecc430c9621ece335651a01 |  |
| 4 | 55 | 5.65843621399177 | ZEU | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |

Se zobrazí ve výsledcích výše uvedené, že nejdominantnějšími segment obsahuje 65.74 % zaznamenává celkový počet výjimek a sdílí čtyř dimenzí. Dalšího segmentu je mnohem méně běžné, obsahuje pouze 9.67 % záznamů a sdílí tři dimenze. Jsou i méně běžné jsou ostatní segmenty. 

Autocluster používá vlastního algoritmu pro více dimenzí dolování a extrahování zajímavé segmenty. "Zajímavé" znamená, že každý segment má významné pokrytí sady záznamů a sadu funkcí. Segmenty jsou také se rozcházela, což znamená, že každý z nich se značně liší od ostatních. Nejméně jednu z těchto segmentů můžou být relevantní pro proces RCA. Chcete-li minimalizovat, analýzu segmentů a hodnocení, extrahuje autocluster pouze malý úsek seznamu.

### <a name="use-basket-for-single-record-set-clustering"></a>Použít basket() pro jeden záznam, nastavte clustering

Můžete také použít [ `basket()` ](/azure/kusto/query/basketplugin) modul plug-in, jak je znázorněno v následující dotaz:

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate basket()
```

| SegmentId | Count | Procenta | Oblast | ScaleUnit | DeploymentId | Zarážka s trasováním | ServiceHost |
|-----------|-------|------------------|--------|-----------|----------------------------------|------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1 | 642 | 66.0493827160494 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  |  |
| 2 | 324 | 33.3333333333333 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 | 0 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 3 | 315 | 32.4074074074074 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 | 16108 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 4 | 328 | 33.7448559670782 |  |  |  | 0 |  |
| 5 | 94 | 9.67078189300411 | scus | su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |  |
| 6 | 82 | 8.43621399176955 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |  |
| 7 | 68 | 6.99588477366255 | scus | su3 | 90d3d2fc7ecc430c9621ece335651a01 |  |  |
| 8 | 167 | 17.1810699588477 | scus |  |  |  |  |
| 9 | 55 | 5.65843621399177 | ZEU | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |  |
| 10 | 92 | 9.46502057613169 |  |  |  | 10007007 |  |
| 11 | 90 | 9.25925925925926 |  |  |  | 10007006 |  |
| 12 | 57 | 5.8641975308642 |  |  |  |  | 00000000-0000-0000-0000-000000000000 |

Nákupní košík implementuje algoritmus Apriori pro položku dolování a extrahuje všechny segmenty, jehož pokrytí sady záznamů je nad prahovou hodnotou (výchozí hodnota % 5). Uvidíte, že další segmenty, které byly extrahovány s podobnosti (například 0, 1 nebo 2,3 segmenty).

Oba moduly plug-in jsou efektivní a snadno použitelná, ale jejich významné omezením je skutečnost, že se cluster bez dohledu způsobem (se žádné popisky) nastavte jeden záznam. Je tedy jasné, zda extrahované vzory charakterizují vybrané sadě záznamů (neobvyklé záznamy) nebo globální sady záznamů.

## <a name="clustering-the-difference-between-two-records-sets"></a>Clustering rozdíl mezi dva záznamy sady

[ `diffpatterns()` ](/azure/kusto/query/diffpatternsplugin) Modulu plug-in překonává omezení `autocluster` a `basket`. `Diffpatterns` přebírá dva sady záznamů a extrahuje hlavní segmenty, které se liší mezi nimi. Jedna sada obvykle obsahuje sadu Zkoumáme neobvyklé záznamů (jeden analyzovaným `autocluster` a `basket`). Druhá sada obsahuje sadu záznamů na odkaz (základní). 

V dotazu níže používáme `diffpatterns` najít zajímavé clustery během dvou minut zásobníku, které se liší od clusterů ve standardních hodnotách. V okně směrného plánu definujeme jako osm minut před 15:00 (při zásobníku spuštění). Musíme také rozšířit binární sloupec (AB) určující, zda konkrétní záznam patří do tohoto směrného plánu nebo sady neobvyklé. `Diffpatterns` implementuje algoritmus učení, ve kterém byly vytvořeny popisky dvě třídy podle neobvyklé oproti standardní hodnoty příznak (AB).

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

| SegmentId | CountA | CountB | PercentA | percentB | PercentDiffAB | Oblast | ScaleUnit | DeploymentId | Zarážka s trasováním |
|-----------|--------|--------|----------|----------|---------------|--------|-----------|----------------------------------|------------|
| 0 | 639 | 21 | 65.74 | 1.7 | 64.04 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  |
| 1 | 167 | 544 | 17.18 | 44.16 | 26.97 | scus |  |  |  |
| 2 | 92 | 356 | 9.47 | 28.9 | 19.43 |  |  |  | 10007007 |
| 3 | 90 | 336 | 9.26 | 27.27 | 18.01 |  |  |  | 10007006 |
| 4 | 82 | 318 | 8.44 | 25.81 | 17.38 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 5 | 55 | 252 | 5.66 | 20.45 | 14.8 | ZEU | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |
| 6 | 57 | 204 | 5.86 | 16.56 | 10.69 |  |  |  |  |

Nejdominantnějšími segmentů je stejný segment, který byl extrahován `autocluster`, jeho pokrytí v okně neobvyklé dvouminutového je také 65.74 %. Ale jeho pokrytí v okně osm minut směrného plánu je pouze % 1.7. Rozdíl je 64.04 %. Tento rozdíl zdá se, že souvisí s neobvyklé špičky. Můžete ověřit tento předpoklad rozdělením původní grafu na záznamy, které patří k segmentu problematické a jsou ostatní segmenty, jak je znázorněno v následujícím dotazu:

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| extend seg = iff(Region == "eau" and ScaleUnit == "su7" and DeploymentId == "b5d1d4df547d4a04ac15885617edba57"
and ServiceHost == "e7f60c5d-4944-42b3-922a-92e98a8e7dec", "Problem", "Normal")
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m by seg
| render timechart
```

![Ověřování promítnu segmentu "diffpattern.](media/machine-learning-clustering/validating-diffpattern-timechart.png)

Tento graf umožňuje podívejte se, že zásobníku v úterý odpoledne z důvodu výjimky z tohoto konkrétního segmentu zjistit pomocí odkazu `diffpatterns` modulu plug-in.

## <a name="summary"></a>Souhrn

Moduly plug-in Azure Data Explorer Machine Learning jsou užitečné pro řadu scénářů. `autocluster` a `basket` implementace algoritmu učení bez dohledu a se snadno používá. `Diffpatterns` implementuje pod dohledem algoritmu učení a i když složitější, je výkonnější při extrakci rozdílů mezi segmenty pro RCA.

Tyto moduly plug-in se interaktivně používají ve scénářích ad-hoc a automaticky téměř v reálném čase služby monitorování. V Průzkumníku dat Azure čas detekce anomálií řady následuje diagnostiku proces, který je vysoce optimalizovaných pro splňují standardy elektronického odesílání nezbytné výkonu.
