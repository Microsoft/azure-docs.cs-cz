---
title: Umístění kamery prostorové analýzy
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak nastavit kameru pro použití s prostorovou analýzou.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: aahi
ms.openlocfilehash: 00bca2d0c7e267bef8cd4a917463c5e7db159792
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90935111"
---
# <a name="camera-placement-guide"></a>Průvodce umístěním kamery

Tento článek popisuje doporučení pro umístění kamery pro prostorovou analýzu (Public Preview). Obsahuje obecné pokyny a také specifická doporučení pro výšku, úhel a kameru na místo pro všechny zahrnuté operace. 

> [!NOTE]
> Tato příručka je určená pro kameru M3045-V. Tento fotoaparát bude používat 1080 rozlišení, 106 horizontálního pole v zobrazení, svislé pole se 59m stupně a pevná délka ohniska 2,8 mm. Níže uvedené zásady budou platit pro všechny kamery, ale konkrétní pokyny týkající se výšky kamery a vzdálenosti mezi kamerami a ohniska se budou muset upravit pro použití s ostatními fotoaparáty. 

## <a name="general-guidelines"></a>Obecné pokyny

Při umísťování kamer pro prostorovou analýzu Vezměte v úvahu následující obecné pokyny:

* **Výška osvětlení** Položte kamery pod přípojky osvětlení, aby neblokovali kamery.
* **Překážky.** Aby nedocházelo k překážkám zobrazení kamery, poznamenejte si překážky, jako je POLES, podpis, odložení, zdi a stávající kamery LP.
* **Nenáročný dopad na životní prostředí.** Venkovní osvětlení ovlivňuje kvalitu obrazu kamery. Aby nedocházelo k vážným světelným podmínkám, vyhněte se tomu, aby se kamery směrovaly na externí dveře pro Windows a sklo.
* **Místní pravidla a předpisy pro ochranu osobních údajů.** Místní předpisy můžou omezovat, které kamery můžou zachytit. Ujistěte se, že před umístěním kamer rozumíte místní pravidla a předpisy.
* **Vytváření struktury.** TVK, automatické postřikovače a stávající kabeláž můžou omezovat pevné připojení fotoaparátů.
* **Správa kabelů.** Ujistěte se, že můžete směrovat kabel Ethernet z plánovaných umístění pro připojení kamery na přepínač Power over Internet (PoE).

## <a name="height-focal-point-distance-and-angle"></a>Výška, vzdálenost za kontaktním bodem a úhel

Při rozhodování o tom, jak nainstalovat fotoaparát pro prostorovou analýzu, je třeba zvážit tři věci:
- Výška kamery
- Vzdálenost mezi fotoaparátem a ohniskem
- Úhel kamery vzhledem k rovině podlahy

Je také důležité znát směr, ve vztahu k poli kamera, pokud je to možné, i v závislosti na tom, jestli je to možné. Tento směr je důležitý pro výkon systému.

![Obrázek osoby, která projedná ve směru](./media/spatial-analysis/person-walking-direction.png)

Následující ilustrace znázorňuje zobrazení zvýšení úrovně pro směr procházení osob.

![Zobrazení zvýšení úrovně oprávnění a plánu](./media/spatial-analysis/person-walking-direction-diagram.png)

## <a name="camera-height"></a>Výška kamery

Obvykle by fotoaparáty měly být připojeny 12-14 metrů od země. Při plánování připojení kamery v tomto rozsahu zvažte překážku (například: odložení, odblokování, zavěšení a displeje), které by mohly ovlivnit zobrazení kamery, a pak podle potřeby upravte výšku.

## <a name="camera-to-focal-point-distance"></a>Vzdálenost mezi fotoaparátem a ohniskem

_Vzdálenost mezi fotoaparátem a ohniskem_ je lineární vzdálenost od ohniskového bodu (nebo středového ohniska bodu zájmu) do kamery.

![Kamera na ohnisko – vzdálenost](./media/spatial-analysis/camera-focal-point.png)

Tato vzdálenost se měří na rovině podlahy.

![Způsob měření vzdálenosti mezi kamerami a ohnisky od podlahového bodu](./media/spatial-analysis/camera-focal-point-floor-plane.png)

Z výše uvedeného vypadá takto:

![Způsob měření vzdálenosti mezi kamerami a ohnisky](./media/spatial-analysis/camera-focal-point-above.png)

Pomocí následující tabulky můžete určit vzdálenost kamery od ohniskového bodu na základě konkrétních výšek připojení. Tyto vzdálenosti jsou pro optimální umístění. Všimněte si, že tabulka poskytuje pokyny pod doporučením 12 – 14, protože některé stropy můžou výšku omezit.

| Výška kamery | Vzdálenost mezi fotoaparátem a ohniskem (min/max) |  
| ------------- | ---------------------------------------- |  
| 8            | 10 ' – 13 '                                  |  
| 10pruhový           | 7 – 13                                   |  
| 12,5           | 10 '-17 '                                  |  
| čtrnáct           | 11 ' – 18 '                                  |  
| 16bitovém           | 12 '-22 '                                  |  
| 20o           | 15 – 30                                  |  

Následující obrázek simuluje zobrazení kamery z nejbližších a nejvzdálenějších vzdáleností z kamery do ohniska.

| Nejbližší                                      | Nejvzdálenější                                      |  
| -------------------------------------------- | --------------------------------------------- |  
| ![Nejbližší vzdálenost mezi kamerami a ohnisky](./media/spatial-analysis/focal-point-closest.png) | ![Nejvzdálenější vzdálenost mezi fotoaparáty a kontaktními body](./media/spatial-analysis/focal-point-farthest.png) |  

## <a name="camera-angle-mounting-ranges"></a>Rozsahy připojení úhlu kamery

V této části jsou popsány přijatelné rozsahy připojení úhlu kamery. Tyto rozsahy připojení znázorňují přijatelný rozsah pro optimální umístění.

### <a name="line-configuration"></a>Konfigurace řádku

V následující tabulce jsou uvedena doporučení pro kamery nakonfigurované pro operaci **cognitiveservices Account. Vision. spatialanalysis-personcrossingline** . 

| Výška kamery | Vzdálenost mezi fotoaparátem a ohniskem | Optimální úhel připevnění kamery (min/max) |  
| ------------- | ------------------------------ | ------------------------------------------ |  
| 8            | 9                             | +/-40 °                                     |  
| 10pruhový           | 10pruhový                            | +/-30 °                                     |  
| 12,5           | 13,5                            | +/-20 °                                     |  
| 16bitovém           | let                            | +/-10 °                                     |  
| 20o           | 22                            | +/-10 °                                     |  

Následující obrázek simuluje zobrazení kamery pomocí doporučení levého úhlu (-) a pravého úhlu (+) pro použití **cognitiveservices Account. Vision. spatialanalysis-personcrossingline** k tomu, aby se vypočítána jako vchod do dvířek.

| Zobrazení úplně vlevo                | Zobrazení vpravo                |  
| ---------------------------- | ----------------------------- |  
| ![Úhel kamery vlevo](./media/spatial-analysis/camera-angle-left.png) | ![Úhel kamery vpravo](./media/spatial-analysis/camera-angle-right.png) |  

Následující ilustrace znázorňuje umístění kamery a připojení úhlů ze Zobrazení ptačích očí.

![Zobrazení z ptačí perspektivy](./media/spatial-analysis/camera-angle-top.png)

### <a name="zone-configuration"></a>Konfigurace zóny

Doporučujeme umístit kamery na 10 metrů nebo více nad vozovkou, aby byla zakrytá oblast dostatečně velká. 

Když je zóna vedle překážky, jako je zeď nebo police, připojte kamery v zadané vzdálenosti od cíle do přijatelné škály 120 stupňů, jak je znázorněno na následujícím obrázku.

![Přijatelný úhel kamery](./media/spatial-analysis/camera-angle-acceptable.png)

Následující ilustrace poskytuje simulace pro zobrazení levé a pravé kamery oblasti vedle poli police.

| Levé zobrazení        | Pravé zobrazení        |  
| ---------------- | ----------------- |  
| ![Levé zobrazení](./media/spatial-analysis/end-cap-left.png) | ![Pravé zobrazení](./media/spatial-analysis/end-cap-right.png) |  

#### <a name="queues"></a>Fronty

**Cognitiveservices Account. Vision. spatialanalysis-personcount**, **cognitiveservices Account. Vision. spatialanalysis-persondistance**a **cognitiveservices Account. Vision. spatialanalysis-personcrossingpolygon** se dají použít k monitorování front. Aby se zajistila kvalita dat ve frontě, je vhodnější odstranit rušivé bariéry pásů, aby se minimalizoval překrytí lidí ve frontě a zajistila se konzistence umístění front v průběhu času.

![Odvolatelné fronty pásů](./media/spatial-analysis/retractable-belt-queue.png)

Tento typ bariéry upřednostňuje před neprůhlednými bariérami pro vytvoření fronty, aby se maximalizovala přesnost přehledů ze systému.

Existují dva typy front: lineární a cik-cak.

Následující ilustrace znázorňuje doporučení pro lineární fronty:

![Doporučení lineární fronty](./media/spatial-analysis/camera-angle-linear-queue.png)

Následující ilustrace poskytuje simulace pro zobrazení levé a pravé kamery lineárních front. Všimněte si, že fotoaparát můžete připojit na protější straně fronty.

| Levé zobrazení                          | Pravé zobrazení                          |  
| ---------------------------------- | ----------------------------------- |  
| ![Levý úhel pro lineární frontu](./media/spatial-analysis/camera-angle-linear-left.png) | ![Pravý úhel lineární fronty](./media/spatial-analysis/camera-angle-linear-right.png) |  

Pro fronty cik-cak je nejlepší vyhnout se umístění kamery přímo do směru čáry fronty, jak je znázorněno na následujícím obrázku. Všimněte si, že každá ze čtyř ukázkových pozic kamery na ilustraci poskytuje ideální pohled s přijatelnou odchylkou +/-15 stupňů v každém směru.

Následující obrázky simulují zobrazení z fotoaparátu umístěného v ideálních umístěních pro frontu cik-cak.

| Zobrazit 1        | Zobrazit 2        |  
| ------------- | ------------- |  
| ![Zobrazit 1](./media/spatial-analysis/camera-angle-ideal-location-right.png) | ![Zobrazit 2](./media/spatial-analysis/camera-angle-ideal-location-left.png) |  

| Zobrazení 3 |  Zobrazení 4 |  
| ---- | ----  |
| ![Zobrazení 3](./media/spatial-analysis/camera-angle-ideal-location-back.png) |  ![Zobrazení 4](./media/spatial-analysis/camera-angle-ideal-location-back-left.png) | 

##### <a name="organic-queues"></a>Organické fronty

Ekologické řádky fronty jsou organické. Tento styl fronty je přijatelný, pokud fronty netvoří více než 2-3 lidí a spojnicové formuláře v rámci definice zóny. Je-li délka fronty obvykle více než 2-3 lidí, doporučujeme použít odvolatelné bariéry pásů, které vám pomůžou s směrem fronty a zajištěním spojnicových formulářů v rámci definice zóny.

## <a name="next-steps"></a>Další kroky

* [Nasazení webové aplikace počítající lidi](spatial-analysis-web-app.md)
* [Konfigurace operací prostorových analýz](./spatial-analysis-operations.md)
* [Protokolování a řešení potíží](spatial-analysis-logging.md)
* [Průvodce umístěním zóny a linky](spatial-analysis-zone-line-placement.md)
