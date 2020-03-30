---
title: Podrobnosti o podporovaném formátu dat | Mapy Microsoft Azure
description: Zjistěte, jak se v modulu prostorových vidníných dat analyzují oddělená prostorová data.
author: philmea
ms.author: philmea
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 3353620f1751e939a04543115fe704555fb3bc21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334085"
---
# <a name="supported-data-format-details"></a>Podrobnosti o podporovaném formátu dat

Tento článek obsahuje podrobnosti o podpoře čtení a zápisu pro všechny značky XML a typy geometrie známého textu. Také podrobně popisuje, jak jsou oddělená prostorová data analyzována v modulu prostorových iO.

## <a name="supported-xml-namespaces"></a>Podporované obory názvů XML

Prostorový modul IO podporuje značky XML z následujících oborů názvů.

| Předpona oboru názvů | Identifikátor URI oboru názvů   | Poznámky                                                                    |
|:------------------|:-----------------|:----------------------------------------|
| `atom`           | `http://www.w3.org/2005/Atom`   |                                         |
| `geo`            | `http://www.w3.org/2003/01/geo/wgs84_pos#`  | Podpora pouze pro čtení v souborech GeoRSS.           |
| `georss`         | `http://www.georss.org/georss`  |                                                |
| `geourl`         | `http://geourl.org/rss/module/` | Podpora pouze pro čtení v souborech GeoRSS.                       |
| `gml`            | `http://www.opengis.net/gml`    |                                                        |
| `gpx`            | `http://www.topografix.com/GPX/1/1` |                                                   |
| `gpxx`           | `http://www.garmin.com/xmlschemas/GpxExtensions/v3` | Podpora pouze pro čtení v souborech GPX. Analyzuje a používá DisplayColor. Všechny ostatní vlastnosti přidané do metadat obrazce. |
| `gpx_style`      | `http://www.topografix.com/GPX/gpx_style/0/2`      | Podporováno v souborech GPX. Použije barvu čáry. |
| `gx`             | `http://www.google.com/kml/ext/2.2` |                                                      |
| `kml`            | `http://www.opengis.net/kml/2.2`    |                                                      |
| `rss`            |                                 | Jen pro čtení. GeoRSS píše pomocí formátu Atom.              |

## <a name="supported-xml-elements"></a>Podporované elementy XML

Prostorový iO modul podporuje následující elementy XML. Všechny značky XML, které nejsou podporovány, budou převedeny na objekt JSON. Poté bude každá značka přidána jako `properties` vlastnost do pole nadřazeného obrazce nebo vrstvy.

### <a name="kml-elements"></a>Prvky KML

Prostorový iO modul podporuje následující prvky KML.

| Název prvku         | Čtení    | Zápis   | Poznámky                                                                                                                      |
|----------------------|---------|---------|----------------------------------------------------------------------------------------------------------------------------|
| `address`            | partial | ano     | Objekt je analyzován, ale nepoužívá se pro umístění tvaru.                                                                    |
| `AddressDetails`     | partial | ne      | Objekt je analyzován, ale nepoužívá se pro umístění tvaru.                                                                    |
| `atom:author`        | ano     | ano     |                                                                                                                            |
| `atom:link`          | ano     | ano     |                                                                                                                            |
| `atom:name`          | ano     | ano     |                                                                                                                            |
| `BalloonStyle`       | partial | partial | `displayMode`není podporována. Převedeno na `PopupTemplate`. Chcete-li psát, přidejte `popupTemplate` vlastnost jako vlastnost funkce, pro kterou ji chcete zapsat. |
| `begin`              | ano     | ano     |                                                                                                                            |
| `color`              | ano     | ano     | Zahrnuje `#AABBGGRR` `#BBGGRR`a . Analyzovat na řetězec barev CSS                                                           |
| `colorMode`          | ano     | ne      |                                                                                                                            |
| `coordinates`        | ano     | ano     |                                                                                                                            |
| `Data`               | ano     | ano     |                                                                                                                            |
| `description`        | ano     | ano     |                                                                                                                            |
| `displayName`        | ano     | ano     |                                                                                                                            |
| `Document`           | ano     | ano     |                                                                                                                            |
| `drawOrder`          | partial | ne      | Přečtěte si pro pozemní překryvy a slouží k jejich třídění. 
| `east`               | ano     | ano     |                                                                                                                            |
| `end`                | ano     | ano     |                                                                                                                            |
| `ExtendedData`       | ano     | ano     | Podporuje `Data`netypové `SimpleData` `Schema`nebo a entity nahrazení `$[dataName]`formuláře .                      |
| `extrude`            | partial | partial | Podporováno pouze pro polygony. MultiGeometry, které mají mnohonoly různých výšek budou rozděleny do jednotlivých funkcí. Styly čar nejsou podporovány. Polygony s výškou 0 budou vykresleny jako plochý polygon. Při čtení se jako výšková vlastnost polyga přidá výška první souřadnice v vnějším kroužku. Poté bude k vykreslení polygonu na mapě použita nadmořská výška první souřadnice. |
| `fill`               | ano     | ano     |                                                                                                                            |
| `Folder`             | ano     | ano     |                                                                                                                            |
| `GroundOverlay`      | ano     | ano     | `color`není podporováno                                                                                                   |
| `heading`            | partial | ne      | Analyzovat, ale nevykresleno `SimpleDataLayer`. Zápisy pouze v případě, že data jsou uložena ve vlastnosti obrazce.                 |
| `hotSpot`            | ano     | partial | Zápisy pouze v případě, že data jsou uložena ve vlastnosti obrazce. Jednotky jsou vydávány pouze jako "pixely".                         |
| `href`               | ano     | ano     |                                                                                                                            |
| `Icon`               | partial | partial | Analyzovat, ale nevykresleno `SimpleDataLayer`. Vlastnost ikony obrazce zapíše pouze v případě, že obsahuje data identifikátoru URI. Je `href` podporována pouze. |
| `IconStyle`          | partial | partial | `icon`, `heading` `colorMode`, `hotspots` a hodnoty jsou analyzovány, ale nejsou vykresleny`SimpleDataLayer`         |
| `innerBoundaryIs`    | ano     | ano     |                                                                                                                            |
| `kml`                | ano     | ano     |                                                                                                                            |
| `LabelStyle`         | ne      | ne      |                                                                                                                            |
| `LatLonBox`          | ano     | ano     |                                                                                                                            |
| `gx:LatLonQuad`      | ano     | ano     |                                                                                                                            |
| `LinearRing`         | ano     | ano     |                                                                                                                            |
| `LineString`         | ano     | ano     |                                                                                                                            |
| `LineStyle`          | ano     | ano     | `colorMode`není podporována.                                                                                         |
| `Link`               | ano     | ne      | Pro `href` síťová propojení je podporována pouze vlastnost.                                                                   |
| `MultiGeometry`      | partial | partial | Při čtení může být rozděleno do jednotlivých funkcí.                                                                     |
| `name`               | ano     | ano     |                                                                                                                            |
| `NetworkLink`        | ano     | ne      | Odkazy musí být ve stejné doméně jako dokument.                                                                  |
| `NetworkLinkControl` | ne      | ne      |                                                                                                                            |
| `north`              | ano     | ano     |                                                                                                                            |
| `open`               | ano     | ano     |                                                                                                                            |
| `outerBoundaryIs`    | ano     | ano     |                                                                                                                            |
| `outline`            | ano     | ano     |                                                                                                                            |
| `overlayXY`          | ne      | ne      |                                                                                                                            |
| `Pair`               | partial | ne      | Podporován `normal` je pouze `StyleMap` styl v a. `highlight`není podporována.                                   |
| `phoneNumber`        | ano     | ano     |                                                                                                                            |
| `PhotoOverlay`       | ne      | ne      |                                                                                                                            |
| `Placemark`          | ano     | ano     |                                                                                                                            |
| `Point`              | ano     | ano     |                                                                                                                            |
| `Polygon`            | ano     | ano     |                                                                                                                            |
| `PolyStyle`          | ano     | ano     |                                                                                                                            |
| `Region`             | partial | partial | `LatLongBox`je podporována na úrovni dokumentu.                                                                      |
| `rotation`           | ne      | ne      |                                                                                                                            |
| `rotationXY`         | ne      | ne      |                                                                                                                            |
| `scale`              | ne      | ne      |                                                                                                                            |
| `Schema`             | ano     | ano     |                                                                                                                            |
| `SchemaData`         | ano     | ano     |                                                                                                                            |
| `schemaUrl`          | partial | ano     | Nepodporuje načítání stylů z externích dokumentů, které nejsou zahrnuty v KMZ.                             |
| `ScreenOverlay`      | ne      | ne      |                                                                                                                            |
| `screenXY`           | ne      | ne      |                                                                                                                            |
| `SimpleData`         | ano     | ano     |                                                                                                                            |
| `SimpleField`        | ano     | ano     |                                                                                                                            |
| `size`               | ne      | ne      |                                                                                                                            |
| `Snippet`            | partial | partial | `maxLines`atribut je ignorován.                                                                                  |
| `south`              | ano     | ano     |                                                                                                                            |
| `Style`              | ano     | ano     |                                                                                                                            |
| `StyleMap`           | partial | ne      | Podporován je pouze `StyleMap` normální styl v a.                                                                        |
| `styleUrl`           | partial | ano     | Adresy URL externího stylu nejsou podporovány.                                                                         |
| `text`               | ano     | ano     | Nahrazení `$[geDirections]` není podporováno.                                                                          |
| `textColor`          | ano     | ano     |                                                                                                                            |
| `TimeSpan`           | ano     | ano     |                                                                                                                            |
| `TimeStamp`          | ano     | ano     |                                                                                                                            |
| `value`              | ano     | ano     |                                                                                                                            |
| `viewRefreshMode`    | partial | ne      |  Pokud ukazuje na službu WMS, pak je podporována pouze `onStop` pro pozemní překrytí. Připojí `BBOX=[bboxWest],[bboxSouth],[bboxEast],[bboxNorth]` se k adrese URL a aktualizuje se podle pohybu mapy.  |
| `visibility`         | ano     | ano     |                                                                                                                            |
| `west`               | ano     | ano     |                                                                                                                            |
| `when`               | ano     | ano     |                                                                                                                            |
| `width`              | ano     | ano     |                                                                                                                            |

### <a name="georss-elements"></a>Prvky GeoRSS

Prostorový iO modul podporuje následující prvky GeoRSS.

| Název prvku             | Čtení    | Zápis | Poznámky                                                                                          |
|--------------------------|---------|-------|------------------------------------------------------------------------------------------------|
| `atom:author`            | ano     | ano   |                                                                                                |
| `atom:category`          | ano     | ano   |                                                                                                |
| `atom:content`           | ano     | ano   |                                                                                                |
| `atom:contributor`       | ano     | ano   |                                                                                                |
| `atom:email`             | ano     | ano   |                                                                                                |
| `atom:entry`             | ano     | ano   |                                                                                                |
| `atom:feed`              | ano     | ano   |                                                                                                |
| `atom:icon`              | ano     | ano   |                                                                                                |
| `atom:id`                | ano     | ano   |                                                                                                |
| `atom:link`              | ano     | ano   |                                                                                                |
| `atom:logo`              | ano     | ano   |                                                                                                |
| `atom:name`              | ano     | ano   |                                                                                                |
| `atom:published`         | ano     | ano   |                                                                                                |
| `atom:rights`            | ano     | ano   |                                                                                                |
| `atom:source`            | ano     | ano   |                                                                                                |
| `atom:subtitle`          | ano     | ano   |                                                                                                |
| `atom:summary`           | ano     | ano   |                                                                                                |
| `atom:title`             | ano     | ano   |                                                                                                |
| `atom:updated`           | ano     | ano   |                                                                                                |
| `atom:uri`               | ano     | ano   |                                                                                                |
| `geo:lat`                | ano     | ne    | Napsáno `georss:point`jako .                                                                   |
| `geo:lon`                | ano     | ne    | Napsáno `georss:point`jako .                                                                   |
| `geo:long`               | ano     | ne    | Napsáno `georss:point`jako .                                                                   |
| `georss:box`             | ano     | ne    | Čtení jako polygon a `subType` dané vlastnost "Obdélník"                                |
| `georss:circle`          | ano     | ano   |                                                                                                |
| `georss:elev`            | ano     | ano   |                                                                                                |
| `georss:featurename`     | ano     | ano   |                                                                                                |
| `georss:featuretypetag`  | ano     | ano   |                                                                                                |
| `georss:floor`           | ano     | ano   |                                                                                                |
| `georss:line`            | ano     | ano   |                                                                                                |
| `georss:point`           | ano     | ano   |                                                                                                |
| `georss:polygon`         | ano     | ano   |                                                                                                |
| `georss:radius`          | ano     | ano   |                                                                                                |
| `georss:relationshiptag` | ano     | ano   |                                                                                                |
| `georss:where`           | ano     | ano   |                                                                                                |
| `geourl:latitude`        | ano     | ne    | Napsáno `georss:point`jako .                                                                   |
| `geourl:longitude`       | ano     | ne    | Napsáno `georss:point`jako .                                                                   |
| `position`               | ano     | ne    | Některé informační kanály XML zabalí GML značkou polohy `georss:where` namísto jeho zabalení značkou. Bude číst tuto značku, ale `georss:where` bude psát pomocí značky. |
| `rss`                    | ano     | ne    | GeoRSS napsané ve formátu ATOM.                                                                 |
| `rss:author`             | ano     | partial | Napsáno `atom:author`jako .                                                                 |
| `rss:category`           | ano     | partial | Napsáno `atom:category`jako .                                                               |
| `rss:channel`            | ano     | ne    |                                                                                                |
| `rss:cloud`              | ano     | ne    |                                                                                                |
| `rss:comments`           | ano     | ne    |                                                                                                |
| `rss:copyright`          | ano     | partial | Napsáno `atom:rights` jako if shape ještě `rights` `properties` nemá vlastnost.       |
| `rss:description`        | ano     | partial | Napsáno `atom:content` jako if shape ještě `content` `properties` nemá vlastnost.      |
| `rss:docs`               | ano     | ne    |                                                                                                |
| `rss:enclosure`          | ano     | ne    |                                                                                                |
| `rss:generator`          | ano     | ne    |                                                                                                |
| `rss:guid`               | ano     | partial | Napsáno `atom:id` jako if shape ještě `id` `properties` nemá vlastnost.         |
| `rss:image`              | ano     | partial | Napsáno `atom:logo` jako if shape ještě `logo` `properties` nemá vlastnost.      |
| `rss:item`               | ano     | partial | Napsáno `atom:entry`jako .                                                                  |
| `rss:language`           | ano     | ne    |                                                                                                |
| `rss:lastBuildDate`      | ano     | partial | Napsáno `atom:updated` jako if shape ještě `updated` `properties` nemá vlastnost.     |
| `rss:link`               | ano     | partial | Napsáno `atom:link`jako .                                                                   |
| `rss:managingEditor`     | ano     | partial | Napsáno `atom:contributor`jako .                                                            |
| `rss:pubDate`            | ano     | partial | Napsáno `atom:published` jako if shape ještě `published` `properties` nemá vlastnost.  |
| `rss:rating`             | ano     | ne    |                                                                                                |
| `rss:skipDays`           | ano     | ne    |                                                                                                |
| `rss:skipHours`          | ano     | ne    |                                                                                                |
| `rss:source`             | ano     | partial | Napsáno `atom:source` jako obsahující `atom:link`.                                       |
| `rss:textInput`          | ano     | ne    |                                                                                                |
| `rss:title`              | ano     | partial | Napsáno `atom:title`jako .                                                                  |
| `rss:ttl`                | ano     | ne    |                                                                                                |
| `rss:webMaster`          | ano     | ne    |                                                                                                |

### <a name="gml-elements"></a>Prvky GML

Prostorový iO modul podporuje následující prvky GML. 

| Název prvku            | Čtení | Zápis | Poznámky                                                                                  |
|-------------------------|------|-------|----------------------------------------------------------------------------------------|
| `gml:coordinates`       | ano  | ne    | Napsáno `gml:posList`jako .                                                              |
| `gml:curveMember`       | ano  | ne    |                                                                                        |
| `gml:curveMembers`      | ano  | ne    |                                                                                        |
| `gml:Box`               | ano  | ne    | Napsáno `gml:Envelope`jako .                                                             |
| `gml:description`       | ano  | ano   |                                                                                        |
| `gml:Envelope`          | ano  | ano   |                                                                                        |
| `gml:exterior`          | ano  | ano   |                                                                                        |
| `gml:Feature`           | ano  | ne    | Napsáno jako tvar.                                                                    |
| `gml:FeatureCollection` | ano  | ne    | Zapsáno jako kolekce geometrie.                                                      |
| `gml:featureMember`     | ano  | ne    | Zapsáno jako kolekce geometrie.                                                      |
| `gml:geometry`          | ano  | ne    | Napsáno jako tvar.                                                                    |
| `gml:geometryMember`    | ano  | ano   |                                                                                        |
| `gml:geometryMembers`   | ano  | ano   |                                                                                        |
| `gml:identifier`        | ano  | ano   |                                                                                        |
| `gml:innerBoundaryIs`   | ano  | ne    | Napsáno `gml.interior`pomocí .                                                          |
| `gml:interior`          | ano  | ano   |                                                                                        |
| `gml:LinearRing`        | ano  | ano   |                                                                                        |
| `gml:LineString`        | ano  | ano   |                                                                                        |
| `gml:lineStringMember`  | ano  | ano   |                                                                                        |
| `gml:lineStringMembers` | ano  | ne    |                                                                                        |
| `gml:MultiCurve`        | ano  | ne    | Čte `gml:LineString` pouze členy. Napsáno jako`gml.MultiLineString`                  |
| `gml:MultiGeometry`     | partial  | partial   | Přečtěte si pouze jako FeatureCollection.                                              |
| `gml:MultiLineString`   | ano  | ano   |                                                                                        |
| `gml:MultiPoint`        | ano  | ano   |                                                                                        |
| `gml:MultiPolygon`      | ano  | ano   |                                                                                        |
| `gml:MultiSurface`      | ano  | ne    | Čte `gml:Polygon` pouze členy. Napsáno jako`gml.MultiPolygon`                        |
| `gml:name`              | ano  | ano   |                                                                                        |
| `gml:outerBoundaryIs`   | ano  | ne    | Napsáno `gml.exterior`pomocí .                                                          |
| `gml:Point`             | ano  | ano   |                                                                                        |
| `gml:pointMember`       | ano  | ano   |                                                                                        |
| `gml:pointMembers`      | ano  | ne    |                                                                                        |
| `gml:Polygon`           | ano  | ano   |                                                                                        |
| `gml:polygonMember`     | ano  | ano   |                                                                                        |
| `gml:polygonMembers`    | ano  | ne    |                                                                                        |
| `gml:pos`               | ano  | ano   |                                                                                        |
| `gml:posList`           | ano  | ano   |                                                                                        |
| `gml:surfaceMember`     | ano  | ano   |                                                                                        |

#### <a name="additional-notes"></a>další poznámky

- Členské prvky budou vyhledány pro geometrii, která může být pohřbena v podřízených prvcích. Tato operace hledání je nezbytná, protože mnoho formátů XML, které se rozprostírají od GML, nemusí umístit geometrii jako přímý podřízený prvek člena.
- `srsName`je částečně podporován pro souřadnice WGS84 a následující kódy:[EPSG:4326](https://epsg.io/4326)) a web Mercator ([EPSG:3857](https://epsg.io/3857) nebo jeden z jeho alternativních kódů. Jakýkoli jiný souřadný systém bude analyzován jako WGS84 tak, jak je.
- Pokud není zadáno při čtení informačního kanálu XML, pořadí os je určeno na základě rad v informačním kanálu XML. Upřednostňují se pořadí osy "zeměpisná šířka, délka".
- Pokud není pro vlastnosti při zápisu do souboru GML určen vlastní obor názvů GML, nebudou přidány další informace o vlastnostech.

### <a name="gpx-elements"></a>Prvky GPX

Prostorový IO modul podporuje následující prvky GPX.

| Název prvku             | Čtení    | Zápis   | Poznámky                                                                                       |
|--------------------------|---------|---------|---------------------------------------------------------------------------------------------|
| `gpx:ageofdgpsdata`      | ano     | ano     |                                                                                             |
| `gpx:author`             | ano     | ano     |                                                                                             |
| `gpx:bounds`             | ano     | ano     | Převedeno na LocationRect při čtení.                                                    |
| `gpx:cmt`                | ano     | ano     |                                                                                             |
| `gpx:copyright`          | ano     | ano     |                                                                                             |
| `gpx:desc`               | ano     | ano     | Zkopírována do vlastnosti popisu při čtení zarovnat s jinými formáty XML.               |
| `gpx:dgpsid`             | ano     | ano     |                                                                                             |
| `gpx:ele`                | ano     | ano     |                                                                                             |
| `gpx:extensions`         | partial | partial | Při čtení se extrahují informace o stylu. Všechna ostatní rozšíření budou sloučí do jednoduchého objektu JSON. Zapsány jsou pouze informace o stylu obrazce. |
| `gpx:geoidheight`        | ano     | ano     |                                                                                             |
| `gpx:gpx`                | ano     | ano     |                                                                                             |
| `gpx:hdop`               | ano     | ano     |                                                                                             |
| `gpx:link`               | ano     | ano     |                                                                                             |
| `gpx:magvar`             | ano     | ano     |                                                                                             |
| `gpx:metadata`           | ano     | ano     |                                                                                             |
| `gpx:name`               | ano     | ano     |                                                                                             |
| `gpx:pdop`               | ano     | ano     |                                                                                             |
| `gpx:rte`                | ano     | ano     |                                                                                             |
| `gpx:rtept`              | ano     | ano     |                                                                                             |
| `gpx:sat`                | ano     | ano     |                                                                                             |
| `gpx:src`                | ano     | ano     |                                                                                             |
| `gpx:sym`                | ano     | ano     | Hodnota je zachycena, ale nepoužívá se ke změně ikony připínáčku.                               |
| `gpx:text`               | ano     | ano     |                                                                                             |
| `gpx:time`               | ano     | ano     |                                                                                             |
| `gpx:trk`                | ano     | ano     |                                                                                             |
| `gpx:trkpt`              | ano     | ano     |                                                                                             |
| `gpx:trkseg`             | ano     | ano     |                                                                                             |
| `gpx:type`               | ano     | ano     |                                                                                             |
| `gpx:vdop`               | ano     | ano     |                                                                                             |
| `gpx:wpt`                | ano     | ano     |                                                                                             |
| `gpx_style:color`        | ano     | ano     |                                                                                             |
| `gpx_style:line`         | partial | partial | `color`, `opacity` `width`, `lineCap` , jsou podporovány.                                           |
| `gpx_style:opacity`      | ano     | ano     |                                                                                             |
| `gpx_style:width`        | ano     | ano     |                                                                                             |
| `gpxx:DisplayColor`      | ano     | ne      | Slouží k určení barvy obrazce. Při psaní `gpx_style:line` se místo toho použije barva.  |
| `gpxx:RouteExtension`    | partial | ne      | Všechny vlastnosti `properties`jsou přečteny do . Používá `DisplayColor` se pouze.                     |
| `gpxx:TrackExtension`    | partial | ne      | Všechny vlastnosti `properties`jsou přečteny do . Používá `DisplayColor` se pouze.                     |
| `gpxx:WaypointExtension` | partial | ne      | Všechny vlastnosti `properties`jsou přečteny do . Používá `DisplayColor` se pouze.                     |
| `gpx:keywords`           | ano     | ano     |                                                                                             |
| `gpx:fix`                | ano     | ano     |                                                                                             |

#### <a name="additional-notes"></a>další poznámky

Při psaní;

- MultiPoints budou rozděleny do jednotlivých trasových bodů.
- Mnoholyana a MultiPolygons budou zapsány jako stopy. 
  
## <a name="supported-well-known-text-geometry-types"></a>Podporované typy geometrie dobře známý text

| Typ geometrie | Čtení | Zápis |
|--------------|:----:|:-----:|
| Bod | x | x |
| BOD Z | x | x | 
| BOD M | x | x<sup>[2]</sup> |
| BOD ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| Linestring | x | x |
| LINESTRING Z | x | x | 
| LINESTRING M | x | x<sup>[2]</sup> |
| LINESTRING ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| Mnohoúhelník | x | x |
| POLYGON Z | x | x |
| POLYGON M | x | x<sup>[2]</sup> |
| POLYGON ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| Multipoint | x | x |
| MULTIPOINT Z | x | x | 
| MULTIPOINT M | x | x<sup>[2]</sup> |
| POMULTIPOINTINT ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| Multilinestring | x | x |
| MULTILINESTRING Z | x | x | 
| MULTILINESTRING M | x | x<sup>[2]</sup> |
| MULTILINESTRING ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| Multipolygon | x | x |
| MULTIPOLYGON Z | x | x | 
| MULTIPOLYGON M | x | x<sup>[2]</sup> |
| MULTIPOLYGON ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| Geometrycollection | x | x |
| GEOMETRYCOLLECTION Z | x | x | 
| GEOMETRYCOLLECTION M | x | x<sup>[2]</sup> | 
| GEOMETRYCOLLECTION ZM | x<sup>[1]</sup><sup>[2]</sup> | x | 

\[1\] Pouze Z parametr je zachycen a přidán jako třetí hodnota v Position hodnotě.

\[Parametr\] 2 M není zachycen.

## <a name="delimited-spatial-data-support"></a>Podpora oddělených prostorových dat

Prostorová data oddělená, například soubory hodnot oddělených čárkami (CSV), mají často sloupce obsahující prostorová data. Mohou například existovat sloupce, které obsahují informace o zeměpisné šířce a zeměpisné délky. Ve formátu Známý text může existovat sloupec, který obsahuje data prostorové geometrie.

### <a name="spatial-data-column-detection"></a>Detekce sloupců prostorových dat

Při čtení odděleného souboru, který obsahuje prostorová data, bude záhlaví analyzováno, aby se zjistilo, které sloupce obsahují pole umístění. Pokud záhlaví obsahuje informace o typu, bude použito k přetypovátí hodnot buněk na příslušný typ. Pokud není zadána žádná hlavička, první řádek bude analyzován a použit ke generování záhlaví. Při analýze prvního řádku je provedena kontrola tak, aby odpovídala názvům sloupců s následujícími názvy způsobem bez rozlišování velkých a malých písmen. Pořadí názvů je prioritou v případě, že v souboru existují dva nebo více názvů.

#### <a name="latitude"></a>Zeměpisná šířka

- `latitude`
- `lat`
- `latdd`
- `lat_dd`
- `latitude83`
- `latdecdeg`
- `y`
- `ycenter`
- `point-y`

#### <a name="longitude"></a>Zeměpisná délka

- `longitude`
- `lon`
- `lng`
- `long`
- `longdd`
- `long_dd`
- `longitude83`
- `longdecdeg`
- `x`
- `xcenter`
- `point-x`

#### <a name="elevation"></a>Zvýšení

- `elevation`
- `elv`
- `altitude`
- `alt`
- `z`

#### <a name="geography"></a>Geography

První řádek dat bude zkontrolován na řetězce, které jsou ve formátu známý text. 

### <a name="delimited-data-column-types"></a>Oddělované typy datových sloupců

Při skenování řádku záhlaví budou extrahovány všechny informace o textu, které jsou v názvu sloupce, a použity k obsazení buněk v tomto sloupci. Zde je příklad názvu sloupce, který má hodnotu typu: "ColumnName (typeName)". Podporovány jsou následující názvy typů bez podomů:

#### <a name="numbers"></a>Čísla

- edm.int64
- int
- long
- edm.double
- float
- double
- číslo

#### <a name="booleans"></a>Booleans

- edm.boolean
- bool
- Boolean

#### <a name="dates"></a>Dates

- edm.datetime
- date
- datetime

#### <a name="geography"></a>Geography

- edm.geography
- Geografie

#### <a name="strings"></a>Řetězce

- edm.string
- varchar
- text
- řetězec

Pokud nelze extrahovat žádné informace o typu ze záhlaví a možnost dynamického psaní je povolena při čtení, bude každá buňka individuálně analyzována, aby se zjistilo, jaký datový typ je nejvhodnější pro přetypování.

## <a name="next-steps"></a>Další kroky

Další ukázky kódu, které chcete přidat do map, naleznete v následujících článcích:

> [!div class="nextstepaction"]
> [Čtení a zápis prostorových dat](spatial-io-read-write-spatial-data.md)
