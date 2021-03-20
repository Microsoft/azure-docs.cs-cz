---
title: Principy vrstev v Azure Maps Power BI vizuálů | Mapy Microsoft Azure
description: V tomto článku se dozvíte o různých vrstvách, které jsou k dispozici v vizuálu Microsoft Azure Maps pro Power BI.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: a5c7296a0e7b7f6ea33c1f4a669675efd90f9e9a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "86261716"
---
# <a name="understanding-layers-in-the-azure-maps-power-bi-visual"></a>Principy vrstev v Azure Maps Power BI vizuálu

V Azure Mapsovém vizuálu jsou k dispozici dva typy vrstev. První typ se zaměřuje na vykreslování dat, která se předávají do podokna **pole** vizuálu a skládají se z následujících vrstev, pojďme tyto vrstvy vykreslování dat volat.

:::row:::
    :::column span="":::
        **Vrstva bublin**

        Vykresluje body jako kružnice škálované na mapě.

        ![Bublinová vrstva na mapě](media/power-bi-visual/bubble-layer-thumb.png)
    :::column-end:::
    :::column span="":::
        **Vrstva pruhového grafu**

        Vykresluje body jako prostorové pruhy na mapě.
        
        ![Vrstva pruhového grafu na mapě](media/power-bi-visual/bar-chart-layer-thumb.png)
    :::column-end:::
:::row-end:::

Druhý typ vrstvy spojuje přidání externích zdrojů dat k mapování, aby poskytoval více kontextu a sestávají z následujících vrstev.

:::row:::
    :::column span="":::
        **Referenční vrstva**

        Překrytí nahraného souboru. JSON nad mapou.

        ![Referenční vrstva na mapě](media/power-bi-visual/reference-layer-thumb.png)
    :::column-end:::
    :::column span="":::
        **Dlaždicová vrstva**

        Překrytí vlastní vrstvy dlaždice nad mapu.
        
        ![Vrstva dlaždice na mapě](media/power-bi-visual/tile-layer-thumb.png)
    :::column-end:::
    :::column span="":::
        **Vrstva přenosu dat**

        Překrýt informace o přenosech dat v reálném čase na mapě.
        
        ![Přenosová vrstva na mapě](media/power-bi-visual/traffic-layer-thumb.png)
    :::column-end:::
:::row-end:::

Všechny vrstvy vykreslování dat i **vrstva dlaždic** mají možnosti pro minimální a maximální úrovně přiblížení, které se používají k určení rozsahu úrovně přiblížení, které by měly být zobrazeny v. To umožňuje použít jeden typ vrstvy vykreslování na jednu úroveň přiblížení a přechod na jinou vrstvu vykreslování v jiné úrovni přiblížení.

Tyto vrstvy mají také možnost umístění relativní k ostatním vrstvám na mapě. Při použití více vrstev vykreslování dat určuje pořadí, ve kterém jsou přidány na mapu, jejich relativní pořadí vrstev, pokud mají stejnou hodnotu **pozice vrstvy** .

## <a name="general-layer-settings"></a>Obecné nastavení vrstvy

Oddíl obecné vrstvy v podokně **Formát** jsou společná nastavení, která se vztahují na vrstvy, které jsou připojené k Power BI datové sadě v podokně **pole** (bublinová vrstva, pruhový graf).

| Nastavení     | Popis   |
|-------------|---------------|
| Nevybraná průhlednost | Průhlednost obrazců, které nejsou vybrány, je-li vybrán jeden nebo více obrazců.  |
| Zobrazit nuly              | Určuje, zda mají být na mapě zobrazeny body s nulovou hodnotou velikosti pomocí minimálního poloměru. |
| Zobrazit záporné          | Určuje, zda mají být vykresleny absolutní hodnoty záporných hodnot velikosti.   |
| Minimální hodnota dat          | Minimální hodnota vstupních dat pro škálování. Dobrá pro oříznutí.  |
| Maximální hodnota dat          | Maximální hodnota vstupních dat pro škálování. Dobrá pro oříznutí.  |

## <a name="next-steps"></a>Další kroky

Změňte způsob zobrazení dat na mapě:

> [!div class="nextstepaction"]
> [Přidání vrstvy bublin](power-bi-visual-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Přidání vrstvy pruhového grafu](power-bi-visual-add-bar-chart-layer.md)

Přidat k mapě další kontext:

> [!div class="nextstepaction"]
> [Přidání referenční vrstvy](power-bi-visual-add-reference-layer.md)

> [!div class="nextstepaction"]
> [Přidání vrstvy dlaždic](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [Zobrazení provozu v reálném čase](power-bi-visual-show-real-time-traffic.md)
