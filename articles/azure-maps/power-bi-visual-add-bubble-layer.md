---
title: Přidat bublinovou vrstvu do Azure Maps Power BI vizuálu | Mapy Microsoft Azure
description: V tomto článku se naučíte, jak používat bublinovou vrstvu ve vizuálu Microsoft Azure Maps pro Power BI.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: ff0d2c8e90995222af79d5be63ca3e9da24b3c45
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92896221"
---
# <a name="add-a-bubble-layer"></a>Přidání vrstvy bublin

**Bublinová vrstva** vykresluje data o poloze jako kružnice škálované na mapě.

> [!div class="mx-imgBorder"]
> ![Mapa znázorňující data bodu pomocí bublinové vrstvy](media/power-bi-visual/bubble-layer-with-legend-color.png)

Zpočátku mají všechny bubliny stejnou barvu výplně. Pokud se pole předává do kontejneru **legend** podokna **pole** , bubliny budou vybarvit na základě jejich kategorizace. Obrys bublin je bílý, ale je možné ho změnit na novou barvu nebo povolit možnost obrys vysokého kontrastu. Možnost **obrysu s vysokým kontrastem** dynamicky přiřadí barvu obrysu, která je variací barvy výplně s vysokým kontrastem. To pomáhá zajistit zřetelnou viditelnost bublin bez ohledu na styl mapy. Níže jsou uvedené primární nastavení v podokně **Formát** , které je k dispozici v části **bublinová vrstva** .

| Nastavení               | Popis    |
|-----------------------|----------------|
| Velikost                  | Velikost jednotlivých bublin Tato možnost je skrytá, pokud je pole předáno do kontejneru **Velikost** podokna **pole** . Další možnosti, jak je popsáno v tématu [škálování velikosti bublin](#bubble-size-scaling) , se zobrazí dál v tomto článku. |
| Barva výplně            | Barva jednotlivých bublin Tato možnost je skrytá při předání pole do kontejneru **legendy** podokna **pole** a v podokně **Formát** se zobrazí samostatná část **barvy dat** . |
| Průhlednost výplně     | Průhlednost jednotlivých bublin |
| Světlý obrys s vysokým kontrastem | Nastaví kontrast barvy obrysu barvou výplně pro lepší přístupnost pomocí variace barvy výplně s vysokým kontrastem. |
| Barva obrysu         | Barva, která objednává bublinu Tato možnost je skrytá, pokud je povolená možnost světlosti **vysokého kontrastu** . |
| Průhlednost obrysu  | Průhlednost obrysu |
| Šířka obrysu         | Šířka obrysu v pixelech |
| Efekt                  | Množství rozostření aplikované na obrys Hodnota 1 rozostří bubliny tak, že pouze středový bod nemá žádnou průhlednost. Hodnota 0 aplikuje efekt rozostření. |
| Zarovnání sklonu       | Určuje, jak bubliny vypadají při rozteči mapy. <br/><br/>&nbsp;&nbsp;&nbsp;&nbsp;• Zobrazení – bubliny se zobrazí na okraji na mapě vzhledem k zobrazení. (výchozí)<br/>&nbsp;&nbsp;&nbsp;&nbsp;• Mapa – bubliny jsou vykresleny plochou na povrchu mapy. |
| Měřítko zvětšení            | Velikost bublin by se měla škálovat vzhledem k úrovni přiblížení. Měřítko přiblížení jedna znamená bez škálování. U velkých hodnot se při přiblížení a zvětšení v případě přiblížení a zvětšení změní velikost bublin. To pomáhá snižovat přehlednost na mapě při jejich zmenšování, ale při přiblížení zajišťuje větší množství bodů. Hodnota 1 nepoužívá žádné škálování. |
| Minimální přiblížení              | Minimální dlaždice úrovně přiblížení jsou k dispozici. |
| Maximální přiblížení              | K dispozici jsou tyto dlaždice s maximální úrovní přiblížení. |
| Pozice vrstvy        | Určuje umístění vrstvy vzhledem k ostatním vrstvám mapy. |

## <a name="bubble-size-scaling"></a>Škálování velikosti bublin

Pokud se pole předává do kontejneru **Velikost** v podokně **pole** , bubliny se škálují relativně k hodnotě míry jednotlivých datových bodů. Možnost **Size** v oddílu **bublinová vrstva** v podokně **Formát** zmizí, když se do kontejneru **Size** předává pole, protože bubliny budou škálovat poloměry mezi minimální a maximální hodnotou. Následující možnosti se zobrazí v oddílu **bublinová vrstva** v podokně **Formát** , pokud **Velikost** kontejneru obsahuje zadané pole.

| Nastavení             | Popis  |
|---------------------|--------------|
| Minimální velikost            | Minimální velikost bublin při škálování dat|
| Maximální velikost            | Maximální velikost bublin při škálování dat|
| Size – metoda škálování | Algoritmus škálování používaný k určení relativní velikosti bublin.<br/><br/>&nbsp;&nbsp;&nbsp;&nbsp;• Lineární rozsah vstupních dat lineárně mapovaných na minimum a maximální velikost. (výchozí)<br/>&nbsp;&nbsp;&nbsp;&nbsp;• Log – rozsah vstupních datových logarithmically mapovaných na minimum a maximální velikost.<br/>&nbsp;&nbsp;&nbsp;&nbsp;• Cubic-Bezier – zadáním hodnot x1, Y1, X2, Y2 Cubic-Bezier křivky vytvořte vlastní metodu škálování. |

Když je **Metoda škálování velikosti** nastavená na **protokol** , zpřístupní se následující možnosti.

| Nastavení   | Popis      |
|-----------|------------------|
| Škálování protokolu | Logaritmické měřítko, které má být použito při výpočtu velikosti bublin. |

Pokud je **Metoda škálování velikosti** nastavená na **krychlové** , budou k dispozici následující možnosti pro přizpůsobení křivky škálování.

| Nastavení | Popis                           |
|---------|---------------------------------------|
| X1      | X1 – parametr pro Bézierovu křivku krychle |
| Y1      | Parametr X2 Bézierovy křivky krychle |
| X2      | Parametr Y1 Bézierovy křivky krychle |
| Y2      | Parametr Y2 Bézierovy křivky krychle |

> [!TIP]
> [https://cubic-bezier.com/](https://cubic-bezier.com/) má užitečný nástroj pro vytváření parametrů pro Cubic-Bezier křivky.

## <a name="next-steps"></a>Další kroky

Změňte způsob zobrazení dat na mapě:

> [!div class="nextstepaction"]
> [Přidání vrstvy pruhového grafu](power-bi-visual-add-bar-chart-layer.md)

Přidat k mapě další kontext:

> [!div class="nextstepaction"]
> [Přidání referenční vrstvy](power-bi-visual-add-reference-layer.md)

> [!div class="nextstepaction"]
> [Přidání vrstvy dlaždic](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [Zobrazení provozu v reálném čase](power-bi-visual-show-real-time-traffic.md)

Přizpůsobení vizuálu:

> [!div class="nextstepaction"]
> [Tipy a triky pro formátování barev v Power BI](/power-bi/visuals/service-tips-and-tricks-for-color-formatting)

> [!div class="nextstepaction"]
> [Přizpůsobení názvů, pozadí a legend vizualizací](/power-bi/visuals/power-bi-visualization-customize-title-background-and-legend)