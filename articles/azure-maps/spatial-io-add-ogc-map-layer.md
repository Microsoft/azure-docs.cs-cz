---
title: Přidat vrstvu mapy Open Geospatial Consortium (OGC) | Mapy Microsoft Azure
description: Naučte se překrýt vrstvu mapy OGC na mapě a jak používat různé možnosti ve třídě OgcMapLayer.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 7d94a681fa987a4b23dbcda744c2f8516da4437a
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92891661"
---
# <a name="add-a-map-layer-from-the-open-geospatial-consortium-ogc"></a>Přidat vrstvu mapy z Open Geospatial Consortium (OGC)

`atlas.layer.OgcMapLayer`Třída může překrývat na mapě webové mapy služby (WMS) a WMTS (Web Map Tile Services). WMS je standardní protokol vyvinutý společností OGC pro poskytování imagí map s použitím miniatur přes Internet. Geografická odkazování na obrázek je procesy přidružení obrázku k geografickému umístění. WMTS je také standardní protokol vyvinutý pomocí OGC. Je navržena pro obsluhu předem vykreslených a geograficky podrobnějších dlaždic mapy.

Následující části popisují funkce služby webové mapy, které jsou podporovány `OgcMapLayer` třídou.

**Služba webového mapování (WMS)**

- Podporované verze: `1.0.0` , `1.1.0` , `1.1.1` a `1.3.0`
- Služba musí podporovat `EPSG:3857` systém projekce nebo reprojekce.
- GetFeatureInfo vyžaduje, aby služba podporovala `EPSG:4326` nebo zpracovala reprojekce. 
- Podporované operace:

    | Operace | Popis |
    | :-- | :-- |
    | GetCapabilities | Načte metadata služby s podporovanými možnostmi. |
    | GetMap | Načte obrázek mapy pro zadanou oblast. |
    | GetFeatureInfo | Načte `feature_info` , který obsahuje základní data o funkci. |

**Služba dlaždic webového mapy (WMTS)**

- Podporované verze: `1.0.0`
- Dlaždice musí být čtvercové, například `TileWidth == TileHeight` .
- Podporovaný počítačový počítač: `EPSG:3857` nebo `GoogleMapsCompatible` 
- Identifikátor TileMatrix musí být celočíselná hodnota, která odpovídá úrovni přiblížení na mapě. V mapě Azure je úroveň přiblížení hodnota mezi `"0"` a `"22"` . Takže se `"0"` podporuje, ale nepodporuje se `"00"` .
- Podporované operace:

    | Operace | Popis |
    | :-- | :-- |
    | GetCapabilities | Načte podporované operace a funkce. |
    | Getdlaždice | Načte z konkrétní dlaždice obrázek. |

## <a name="overlay-an-ogc-map-layer"></a>Překrytí vrstvy mapy OGC

`url`Může to být základní adresa URL služby nebo úplná adresa URL s dotazem pro získání schopností služby. V závislosti na zadaných podrobnostech může klient WFS vyzkoušet několik standardních formátů adresy URL, které určují, jak zpočátku přistupovat ke službě.

Následující kód ukazuje, jak překrýt vrstvu mapy OGC na mapě.

<br/>

<iframe height='700' scrolling='no' title='Příklad vrstvy mapy OGC' src='//codepen.io/azuremaps/embed/xxGLZWB/?height=700&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>V OGC () na CodePen se podívejte na <a href='https://codepen.io/azuremaps/pen/xxGLZWB/'>vrstvu mapy</a> pro pero – příklad Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ). <a href='https://codepen.io'>CodePen</a>
</iframe>

## <a name="ogc-map-layer-options"></a>Možnosti vrstvy mapy OGC

Následující příklad ukazuje různé možnosti vrstvy mapy OGC. Můžete kliknout na tlačítko pero kódu v pravém horním rohu a upravit tak pero kódu.

<br/>

<iframe height='700' scrolling='no' title='Možnosti vrstvy mapy OGC' src='//codepen.io/azuremaps/embed/abOyEVQ/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Prohlédněte si <a href='https://codepen.io/azuremaps/pen/abOyEVQ/'>Možnosti vrstvy mapy OGC</a> pera od Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="ogc-web-map-service-explorer"></a>Průzkumník služby Webová mapa OGC

Následující nástroj překrývá v rámci vrstev v podobě obrázků ze služeb Web Map Services (WMS) a mapy webových map (WMTS). V mapě můžete vybrat, které vrstvy ve službě se vykreslují. Můžete si také prohlédnout přidružené legendy pro tyto vrstvy.

<br/>

<iframe height='750' style='width: 100%;' scrolling='no' title='Průzkumník služby Webová mapa OGC' src='//codepen.io/azuremaps/embed/YzXxYdX/?height=750&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>OGC () na CodePen najdete v tématu věnovaném Azure Maps () v <a href='https://codepen.io/azuremaps/pen/YzXxYdX/'>Průzkumníku služby Web Map</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'>CodePen</a>
</iframe>

Můžete také zadat nastavení mapování pro použití proxy služby. Služba proxy umožňuje načíst prostředky hostované v doménách, které nemají povolenou CORS.

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [OgcMapLayer](/javascript/api/azure-maps-spatial-io/atlas.layer.ogcmaplayer)

> [!div class="nextstepaction"]
> [OgcMapLayerOptions](/javascript/api/azure-maps-spatial-io/atlas.ogcmaplayeroptions)

Podívejte se na následující články, které obsahují ukázky kódu, které byste mohli přidat do svých map:

> [!div class="nextstepaction"]
> [Připojení ke službě WFS](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Využití základních operací](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Podrobnosti o podporovaných formátech dat](spatial-io-supported-data-format-details.md)