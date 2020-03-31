---
title: Přidání vrstvy mapy Otevřené geoprostorové konsorcium (OGC) | Mapy Microsoft Azure
description: Naučte se překrýt vrstvu mapy OGC na mapě a jak používat různé možnosti ve třídě OgcMapLayer.
author: philmea
ms.author: philmea
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: b753ecfc07cfb3806838f8a05dbe33ef0bb92730
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334284"
---
# <a name="add-a-map-layer-from-the-open-geospatial-consortium-ogc"></a>Přidání mapové vrstvy z otevřeného geoprostorového konsorcia (OGC)

Třída `atlas.layer.OgcMapLayer` může překrýt snímky služby WMS (Web Map Services) a snímky služby WMTS (Web Map Tile Services) na mapě. WMS je standardní protokol vyvinutý OGC pro poskytování georeferencovaných mapových obrazů přes internet. Georeferencování obrázku je proces přidružování obrazu k zeměpisné poloze. WMTS je také standardní protokol vyvinutý OGC. Je určen pro zobrazování předem vykreslených a georeferencovaných mapových dlaždic.

V následujících částech jsou popsány funkce `OgcMapLayer` služby webové mapy, které jsou podporovány třídou.

**Webová mapová služba (WMS)**

- Podporované verze: `1.0.0` `1.1.0`, `1.1.1`, a`1.3.0`
- Služba musí podporovat `EPSG:3857` projekční systém nebo zpracovávat reprojekce.
- GetFeatureInfo vyžaduje službu `EPSG:4326` pro podporu nebo zpracování reprojections. 
- Podporované operace:

    | | |
    | :-- | :-- |
    | GetCapabilities | Načte metadata o službě s podporovanými možnostmi. |
    | GetMap | Načte obrázek mapy pro zadanou oblast. |
    | GetFeatureInfo | Načte `feature_info`, který obsahuje podkladová data o funkci |

**Služba dlaždice webové mapy (WMTS)**

- Podporované verze:`1.0.0`
- Dlaždice musí být čtvercové, tak, aby `TileWidth == TileHeight`.
- Crs podporovány: `EPSG:3857` nebo`GoogleMapsCompatible` 
- Identifikátor TileMatrix musí být celá hodnota, která odpovídá úrovni přiblížení na mapě. Na mapě Azure je úroveň přiblížení `"0"` hodnotou mezi a `"22"`. Takže `"0"` je podporován, `"00"` ale není podporován.
- Podporované operace:

    | | |
    | :-- | :-- |
    | GetCapabilities | Načte podporované operace a funkce. |
    | GetTile | Načte snímky pro konkrétní dlaždici. |

## <a name="overlay-an-ogc-map-layer"></a>Překrytí mapové vrstvy OGC

Může `url` se na pokládá být základní adresa URL služby nebo úplná adresa URL s dotazem na získání možností služby. V závislosti na poskytnutých podrobnostech může klient WFS vyzkoušet několik standardních formátů adresy URL, aby zjistil, jak zpočátku přistupovat ke službě.

Následující kód ukazuje, jak překrýt mapovou vrstvu OGC na mapě.

<br/>

<iframe height='700' scrolling='no' title='Příklad vrstvy mapy OGC' src='//codepen.io/azuremaps/embed/xxGLZWB/?height=700&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/xxGLZWB/'>příklad vrstvy Mapy Pera OGC</a> podle Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="ogc-map-layer-options"></a>Volby mapové vrstvy OGC

Níže uvedená ukázka ukazuje různé možnosti mapové vrstvy OGC. Můžete kliknout na tlačítko kód pero v pravém horním rohu upravit kód pero.

<br/>

<iframe height='700' scrolling='no' title='Volby mapové vrstvy OGC' src='//codepen.io/azuremaps/embed/abOyEVQ/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/abOyEVQ/'>možnosti mapové vrstvy</a> Pen OGC podle Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="ogc-web-map-service-explorer"></a>Průzkumník webových mapové služby OGC

Následující nástroj překreslovat snímky ze služby Web Map Services (WMS) a webmap tile services (WMTS) jako vrstvy. Můžete vybrat, které vrstvy ve službě jsou vykresleny na mapě. Můžete také zobrazit přidružené legendy pro tyto hladiny.

<br/>

<iframe height='750' style='width: 100%;' scrolling='no' title='Průzkumník webových mapové služby OGC' src='//codepen.io/azuremaps/embed/YzXxYdX/?height=750&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na <a href='https://codepen.io/azuremaps/pen/YzXxYdX/'>Pero OGC Web Map Service explorer</a> podle Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Můžete také zadat nastavení mapy pro použití služby proxy. Služba proxy umožňuje načíst prostředky, které jsou hostovány v doménách, které nemají povoleno cors.

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách použitých v tomto článku:

> [!div class="nextstepaction"]
> [Vrstva OgcMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.layer.ogcmaplayer)

> [!div class="nextstepaction"]
> [Možnosti OgcMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.ogcmaplayeroptions)

Podívejte se na následující články, které obsahují ukázky kódu, které byste mohli přidat do map:

> [!div class="nextstepaction"]
> [Připojení ke službě WFS](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Využití základních operací](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Podrobnosti o podporovaném formátu dat](spatial-io-supported-data-format-details.md)
