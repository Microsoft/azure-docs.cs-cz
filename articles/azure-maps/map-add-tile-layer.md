---
title: Přidat Dlaždicovou vrstvu ke službě Azure Maps | Dokumentace Microsoftu
description: Přidání dlaždice vrstvy mapy jazyka Javascript
author: rbrundritt
ms.author: richbrun
ms.date: 12/3/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: a9decacc3b22676d94726e3cf979198b3486d270
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54104637"
---
# <a name="add-a-tile-layer-to-a-map"></a>Přidat Dlaždicovou vrstvu mapy

Tento článek popisuje, jak můžete překryv Dlaždicovou vrstvu na mapě. Vrstvy dlaždic umožňuje také ověření kódování umístěním Image na Azure Maps podkladovou mapu dlaždice. Další informace o Azure Maps dělení do bloků systému najdete v [úrovně přiblížení a mřížka dlaždic](zoom-levels-and-tile-grid.md) dokumentaci.

Zatížení vrstvy dlaždic na dlaždicích ze serveru. Tyto Image můžete buď být předem vykreslen a uložené jako ostatní obrázku na serveru pomocí zásady vytváření názvů, která analyzuje Dlaždicovou vrstvu nebo dynamické služba, která generuje imagí v reálném čase. Existují tři různé dlaždici konvence pojmenování service podporuje Azure Maps [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) třídy; 

* X, Y, zápis přiblížení – podle úrovně přiblížení, x je sloupec a y je pozice řádku dlaždice v mřížce dlaždice.
* Zápis Quadkey – kombinace x, y, informace o zvětšení na jednu řetězcovou hodnotu, která je jedinečný identifikátor pro dlaždici.
* Ohraničující rámeček – pole souřadnice ohraničujícího je možné zadat image ve formátu `{west},{south},{east},{north}` které běžně používá [webové mapování služby (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> A [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) je skvělý způsob, jak vizualizovat velké sady dat na mapě. Pouze Dlaždicovou vrstvu se dá vygenerovat na bitovou kopii, ale vektorová data lze také vykreslit jako Dlaždicovou vrstvu příliš. Vykreslením vektorová data jako Dlaždicovou vrstvu mapový ovládací prvek potřebuje pouze k načtení dlaždic, které mohou být velikosti souboru mnohem menší, než vektorová data, které reprezentují. Tato technika je používána mnoho, kteří potřebují k vykreslení millons řádky dat na mapě.

Adresa URL dlaždice předaná do vrstvy dlaždic musí být adresa URL protokolu http/https do prostředku TileJSON nebo šablonu adresa URL dlaždice, která používá následující parametry: 

* `{x}` -X polohy na dlaždici. Musí také `{y}` a `{z}`.
* `{y}` -Y pozice v dlaždici. Musí také `{x}` a `{z}`.
* `{z}` – Úroveň přiblížení dlaždice. Musí také `{x}` a `{y}`.
* `{quadkey}` – Dlaždice quadkey identifikátor podle konvence názvů systému dlaždici služby mapy Bing.
* `{bbox-epsg-3857}` -Ohraničující pole řetězce ve formátu `{west},{south},{east},{north}` systému EPSG 3857 prostorové Reference.
* `{subdomain}` -Zástupný symbol místo, kam bude přidána subdoménu hodnoty, pokud zadaný.

## <a name="add-a-tile-layer"></a>Přidání vrstvy dlaždic

 Tento příklad ukazuje, jak vytvořit Dlaždicovou vrstvu, která odkazuje na sadu dlaždic, které používají x, y, systém dělení do bloků přiblížení. Zdroj této vrstvy dlaždic je překrytím této možnosti taky přemýšlíte o počasí od [Iowa prostředí Mesonet z Iowa State University](https://mesonet.agron.iastate.edu/ogc/).

<br/>

<iframe height='500' scrolling='no' title='Dlaždici vrstvě pomocí X, Y a Z' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/BGEQjG/'>Dlaždicovou vrstvu pomocí X, Y a Z</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt Map. Můžete zobrazit [Vytvořte mapu](./map-create.md) pokyny.

V druhém bloku kódu [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) je vytvořena dlaždice služby, velikosti dlaždice a krytí k němu poloprůhledného předáním formátovanou adresu URL. Kromě toho při přidávání Dlaždicovou vrstvu na mapě, přidá se níže `labels` vrstvy tak, že popisky jsou stále zřetelně vidět.

## <a name="customize-a-tile-layer"></a>Přizpůsobení Dlaždicovou vrstvu

Dlaždicová vrstva pouze má hodně možností stylu. Zde je nástroj, který je vyzkoušet.

<br/>

<iframe height='700' scrolling='no' title='Možností vrstvy dlaždic' src='//codepen.io/azuremaps/embed/xQeRWX/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/xQeRWX/'>možností vrstvy dlaždic</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Další postup

Další informace o třídy a metody používané v tomto článku:

> [!div class="nextstepaction"]
> [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayeroptions?view=azure-iot-typescript-latest)

Naleznete v následujících článcích pro další ukázky kódu pro přidání do vaše mapy:

> [!div class="nextstepaction"]
> [Přidat vrstvu bitové kopie](./map-add-image-layer.md)