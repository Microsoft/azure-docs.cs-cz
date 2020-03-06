---
title: Přidat vrstvu mapy Open Geospatial Consortium (OGC) | Mapy Microsoft Azure
description: Naučte se překrýt vrstvu mapy OGC na mapě a jak používat různé možnosti ve třídě OgcMapLayer.
author: farah-alyasari
ms.author: v-faalya
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: c41ee293c853572ec9e1f9dd3edf001c805924d3
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78402777"
---
# <a name="add-a-map-layer-from-the-open-geospatial-consortium-ogc"></a>Přidat vrstvu mapy z Open Geospatial Consortium (OGC)

Třída `atlas.layer.OgcMapLayer` může překrývat na mapě webové služby mapování souborů (WMS) a na mapě (WMTS) snímků webu. WMS je standardní protokol vyvinutý společností OGC pro poskytování imagí map s použitím miniatur přes Internet. Geografická odkazování na obrázek je procesy přidružení obrázku k geografickému umístění. WMTS je také standardní protokol vyvinutý pomocí OGC. Je navržena pro obsluhu předem vykreslených a geograficky podrobnějších dlaždic mapy.

Následující části popisují funkce služby webového mapování, které jsou podporovány třídou `OgcMapLayer`.

**Služba Mapování webu (WMS)**

- Podporované verze: `1.0.0`, `1.1.0`, `1.1.1`a `1.3.0`
- Služba musí podporovat systém `EPSG:3857` projekce, jinak musí být služba schopná zpracovat reprojekce.
- GetFeatureInfo vyžaduje, aby služba podporovala `EPSG:4326` nebo zpracování reprojekce. 
- Podporované operace:

    | | |
    | :-- | :-- |
    | GetCapabilities | Načte metadata služby s podporovanými možnostmi. |
    | GetMap | Načte obrázek mapy pro zadanou oblast. |
    | GetFeatureInfo | Načte `feature_info`, který obsahuje základní data o funkci. |

**Služba dlaždice mapování webu (WMTS)**

- Podporované verze: `1.0.0`
- Dlaždice musí být čtvercové, například `TileWidth == TileHeight`.
- Podporovaný počítačový počítač: `EPSG:3857` nebo `GoogleMapsCompatible` 
- Identifikátor TileMatrix musí být celočíselná hodnota, která odpovídá úrovni přiblížení na mapě. V mapě Azure je úroveň přiblížení hodnota mezi `"0"` a `"22"`. Proto je podpora `"0"` podporovaná, ale `"00"` se nepodporuje.
- Podporované operace:

    | | |
    | :-- | :-- |
    | GetCapabilities | Načte podporované operace a funkce. |
    | Getdlaždice | Načte z konkrétní dlaždice obrázek. |

## <a name="overlay-an-ogc-map-layer"></a>Překrytí vrstvy mapy OGC

`url` může být základní adresa URL pro službu nebo úplná adresa URL s dotazem pro získání schopností služby. V závislosti na zadaných podrobnostech může klient WFS vyzkoušet několik standardních formátů adresy URL, které určují, jak zpočátku přistupovat ke službě.

Následující kód ukazuje, jak překrýt vrstvu mapy OGC na mapě.

<br/>

<iframe height='700' scrolling='no' title='Příklad vrstvy mapy OGC' src='//codepen.io/azuremaps/embed/xxGLZWB/?height=700&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na Azure Maps <a href='https://codepen.io/azuremaps/pen/xxGLZWB/'>OGC</a> (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na CodePen (pero) na <a href='https://codepen.io'></a>.
</iframe>

## <a name="ogc-map-layer-options"></a>Možnosti vrstvy mapy OGC

Následující příklad ukazuje různé možnosti vrstvy mapy OGC. Můžete kliknout na tlačítko pero kódu v pravém horním rohu a upravit tak pero kódu.

<br/>

<iframe height='700' scrolling='no' title='Možnosti vrstvy mapy OGC' src='//codepen.io/azuremaps/embed/abOyEVQ/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Prohlédněte si <a href='https://codepen.io/azuremaps/pen/abOyEVQ/'>Možnosti vrstvy mapy OGC</a> pera Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="ogc-web-map-service-explorer"></a>Průzkumník služby Webová mapa OGC

Následující nástroj překrývá v rámci vrstev v podobě obrázků ze služeb Web Map Services (WMS) a mapy webových map (WMTS). V mapě můžete vybrat, které vrstvy ve službě se vykreslují. Můžete si také prohlédnout přidružené legendy pro tyto vrstvy.

<br/>

<iframe height='750' style='width: 100%;' scrolling='no' title='Průzkumník služby Webová mapa OGC' src='//codepen.io/azuremaps/embed/YzXxYdX/?height=750&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>V tématu Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>se podívejte na <a href='https://codepen.io/azuremaps/pen/YzXxYdX/'>Web Service map</a> (pero OGC).
</iframe>

Můžete také zadat nastavení mapování pro použití proxy služby. Služba proxy umožňuje načíst prostředky hostované v doménách, které nemají povolenou CORs.

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [OgcMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.layer.ogcmaplayer)

> [!div class="nextstepaction"]
> [OgcMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.ogcmaplayeroptions)

Podívejte se na následující články, které obsahují ukázky kódu, které byste mohli přidat do svých map:

> [!div class="nextstepaction"]
> [Připojení ke službě WFS](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Využití základních operací](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Podrobnosti o podporovaném formátu dat](spatial-io-supported-data-format-details.md)
