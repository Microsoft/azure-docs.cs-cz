---
title: Podrobnosti o podporovaném formátu dat | Mapy Microsoft Azure
description: Přečtěte si, jak se v modulu pro prostorové vstupně-výstupní operace analyzují oddělená prostorová data.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 7227813f607ca18ee50f503a30b290414f333e21
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91310165"
---
# <a name="supported-data-format-details"></a>Podrobnosti o podporovaných formátech dat

Tento článek poskytuje konkrétní informace o podpoře čtení a zápisu pro všechny značky XML a Well-Known typy geometrie textu. Také podrobně popisuje, jak se v modulu pro prostorové vstupně-výstupní operace analyzují oddělená prostorová data.

## <a name="supported-xml-namespaces"></a>Podporované obory názvů XML

Modul pro prostorové vstupně-výstupní operace podporuje značky XML z následujících oborů názvů.

| Předpona oboru názvů | Identifikátor URI oboru názvů   | Poznámky                                                                    |
|:------------------|:-----------------|:----------------------------------------|
| `atom`           | `http://www.w3.org/2005/Atom`   |                                         |
| `geo`            | `http://www.w3.org/2003/01/geo/wgs84_pos#`  | Podpora jen pro čtení v souborech GeoRSS.           |
| `georss`         | `http://www.georss.org/georss`  |                                                |
| `geourl`         | `http://geourl.org/rss/module/` | Podpora jen pro čtení v souborech GeoRSS.                       |
| `gml`            | `http://www.opengis.net/gml`    |                                                        |
| `gpx`            | `http://www.topografix.com/GPX/1/1` |                                                   |
| `gpxx`           | `http://www.garmin.com/xmlschemas/GpxExtensions/v3` | Podpora jen pro čtení v souborech GPX. Analyzuje a používá DisplayColor. Všechny ostatní vlastnosti přidané k metadatům obrazce |
| `gpx_style`      | `http://www.topografix.com/GPX/gpx_style/0/2`      | Podporováno v souborech GPX. Používá barvu čáry. |
| `gx`             | `http://www.google.com/kml/ext/2.2` |                                                      |
| `kml`            | `http://www.opengis.net/kml/2.2`    |                                                      |
| `rss`            |                                 | Jen pro čtení. GeoRSS zápisy pomocí formátu Atom.              |

## <a name="supported-xml-elements"></a>Podporované elementy XML

Modul pro prostorové vstupně-výstupní operace podporuje následující elementy XML. Všechny značky XML, které nejsou podporovány, budou převedeny na objekt JSON. Následně se každá značka přidá jako vlastnost v `properties` poli nadřazeného obrazce nebo vrstvy.

### <a name="kml-elements"></a>KML elementy

Modul pro prostorové vstupně-výstupní operace podporuje následující elementy KML.

| Název prvku         | Čtení    | Zápis   | Poznámky                                                                                                                      |
|----------------------|---------|---------|----------------------------------------------------------------------------------------------------------------------------|
| `address`            | partial | ano     | Objekt je analyzován, ale není použit pro obrazec umístění.                                                                    |
| `AddressDetails`     | partial | ne      | Objekt je analyzován, ale není použit pro obrazec umístění.                                                                    |
| `atom:author`        | ano     | ano     |                                                                                                                            |
| `atom:link`          | ano     | ano     |                                                                                                                            |
| `atom:name`          | ano     | ano     |                                                                                                                            |
| `BalloonStyle`       | partial | partial | `displayMode` není podporováno. Převedeno na `PopupTemplate` . Chcete-li zapisovat, přidejte `popupTemplate` vlastnost jako vlastnost funkce, pro kterou chcete zapisovat. |
| `begin`              | ano     | ano     |                                                                                                                            |
| `color`              | ano     | ano     | Zahrnuje `#AABBGGRR` a `#BBGGRR` . Analyzováno na řetězec barvy CSS                                                           |
| `colorMode`          | ano     | ne      |                                                                                                                            |
| `coordinates`        | ano     | ano     |                                                                                                                            |
| `Data`               | ano     | ano     |                                                                                                                            |
| `description`        | ano     | ano     |                                                                                                                            |
| `displayName`        | ano     | ano     |                                                                                                                            |
| `Document`           | ano     | ano     |                                                                                                                            |
| `drawOrder`          | partial | ne      | Přečtěte si pro překrytí základní desky a slouží k jejich řazení. 
| `east`               | ano     | ano     |                                                                                                                            |
| `end`                | ano     | ano     |                                                                                                                            |
| `ExtendedData`       | ano     | ano     | Podporuje náhrady netypového typu `Data` `SimpleData` nebo `Schema` entit formuláře `$[dataName]` .                      |
| `extrude`            | partial | partial | Podporováno pouze pro mnohoúhelníky. Více geometrie, které mají mnohoúhelníky s různou výškou, budou rozděleny na jednotlivé funkce. Styly čar nejsou podporovány. Mnohoúhelníky s nadmořskou výškou 0 se vykreslí jako plochý mnohoúhelník. Při čtení se výška první souřadnice v vnějším prstenci přidá jako vlastnost výšky mnohoúhelníku. Pak se výška první souřadnice použije pro vykreslení mnohoúhelníku na mapě. |
| `fill`               | ano     | ano     |                                                                                                                            |
| `Folder`             | ano     | ano     |                                                                                                                            |
| `GroundOverlay`      | ano     | ano     | `color` není podporováno                                                                                                   |
| `heading`            | partial | ne      | Analyzovány, ale nebyly vykreslovány `SimpleDataLayer` . Zapisuje pouze, pokud jsou data uložena ve vlastnosti obrazce.                 |
| `hotSpot`            | ano     | partial | Zapisuje pouze, pokud jsou data uložena ve vlastnosti obrazce. Jednotky jsou výstupy jenom jako pixely.                         |
| `href`               | ano     | ano     |                                                                                                                            |
| `Icon`               | partial | partial | Analyzovány, ale nebyly vykreslovány `SimpleDataLayer` . Zapíše vlastnost Icon obrazce pouze v případě, že obsahuje data identifikátoru URI. `href`Podporuje se jenom. |
| `IconStyle`          | partial | partial | `icon``heading`hodnoty,, `colorMode` a `hotspots` jsou analyzovány, ale nejsou vykreslovány`SimpleDataLayer`         |
| `innerBoundaryIs`    | ano     | ano     |                                                                                                                            |
| `kml`                | ano     | ano     |                                                                                                                            |
| `LabelStyle`         | ne      | ne      |                                                                                                                            |
| `LatLonBox`          | ano     | ano     |                                                                                                                            |
| `gx:LatLonQuad`      | ano     | ano     |                                                                                                                            |
| `LinearRing`         | ano     | ano     |                                                                                                                            |
| `LineString`         | ano     | ano     |                                                                                                                            |
| `LineStyle`          | ano     | ano     | `colorMode` není podporováno.                                                                                         |
| `Link`               | ano     | ne      | `href`Pro síťová propojení je podporována pouze vlastnost.                                                                   |
| `MultiGeometry`      | partial | partial | Můžou být při čtení rozdělené do jednotlivých funkcí.                                                                     |
| `name`               | ano     | ano     |                                                                                                                            |
| `NetworkLink`        | ano     | ne      | Odkazy musí být ve stejné doméně jako dokument.                                                                  |
| `NetworkLinkControl` | ne      | ne      |                                                                                                                            |
| `north`              | ano     | ano     |                                                                                                                            |
| `open`               | ano     | ano     |                                                                                                                            |
| `outerBoundaryIs`    | ano     | ano     |                                                                                                                            |
| `outline`            | ano     | ano     |                                                                                                                            |
| `overlayXY`          | ne      | ne      |                                                                                                                            |
| `Pair`               | partial | ne      | `normal`Podporován je pouze styl ve stylu `StyleMap` . `highlight` není podporováno.                                   |
| `phoneNumber`        | ano     | ano     |                                                                                                                            |
| `PhotoOverlay`       | ne      | ne      |                                                                                                                            |
| `Placemark`          | ano     | ano     |                                                                                                                            |
| `Point`              | ano     | ano     |                                                                                                                            |
| `Polygon`            | ano     | ano     |                                                                                                                            |
| `PolyStyle`          | ano     | ano     |                                                                                                                            |
| `Region`             | partial | partial | `LatLongBox` je podporován na úrovni dokumentu.                                                                      |
| `rotation`           | ne      | ne      |                                                                                                                            |
| `rotationXY`         | ne      | ne      |                                                                                                                            |
| `scale`              | ne      | ne      |                                                                                                                            |
| `Schema`             | ano     | ano     |                                                                                                                            |
| `SchemaData`         | ano     | ano     |                                                                                                                            |
| `schemaUrl`          | partial | ano     | Nepodporuje načítání stylů z externích dokumentů, které nejsou zahrnuté v KMZ.                             |
| `ScreenOverlay`      | ne      | ne      |                                                                                                                            |
| `screenXY`           | ne      | ne      |                                                                                                                            |
| `SimpleData`         | ano     | ano     |                                                                                                                            |
| `SimpleField`        | ano     | ano     |                                                                                                                            |
| `size`               | ne      | ne      |                                                                                                                            |
| `Snippet`            | partial | partial | `maxLines` atribut je ignorován.                                                                                  |
| `south`              | ano     | ano     |                                                                                                                            |
| `Style`              | ano     | ano     |                                                                                                                            |
| `StyleMap`           | partial | ne      | Podporován je pouze normální styl v rámci `StyleMap` .                                                                        |
| `styleUrl`           | partial | ano     | Adresy URL externích stylů se nepodporují.                                                                         |
| `text`               | ano     | ano     | Náhrada `$[geDirections]` není podporovaná.                                                                          |
| `textColor`          | ano     | ano     |                                                                                                                            |
| `TimeSpan`           | ano     | ano     |                                                                                                                            |
| `TimeStamp`          | ano     | ano     |                                                                                                                            |
| `value`              | ano     | ano     |                                                                                                                            |
| `viewRefreshMode`    | partial | ne      |  Pokud odkazujete na službu WMS, `onStop` je podporována pouze pro základní překryvy. Připojí se `BBOX=[bboxWest],[bboxSouth],[bboxEast],[bboxNorth]` k adrese URL a aktualizuje se, jak se přesune mapa.  |
| `visibility`         | ano     | ano     |                                                                                                                            |
| `west`               | ano     | ano     |                                                                                                                            |
| `when`               | ano     | ano     |                                                                                                                            |
| `width`              | ano     | ano     |                                                                                                                            |

### <a name="georss-elements"></a>GeoRSS elementy

Modul pro prostorové vstupně-výstupní operace podporuje následující elementy GeoRSS.

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
| `geo:lat`                | ano     | ne    | Zapsáno jako `georss:point` .                                                                   |
| `geo:lon`                | ano     | ne    | Zapsáno jako `georss:point` .                                                                   |
| `geo:long`               | ano     | ne    | Zapsáno jako `georss:point` .                                                                   |
| `georss:box`             | ano     | ne    | Číst jako mnohoúhelník a mít `subType` vlastnost "Rectangle".                                |
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
| `geourl:latitude`        | ano     | ne    | Zapsáno jako `georss:point` .                                                                   |
| `geourl:longitude`       | ano     | ne    | Zapsáno jako `georss:point` .                                                                   |
| `position`               | ano     | ne    | Některé kanály XML zabalí GML se značkou pozice místo jejího zabalení se `georss:where` značkou. Přečte tuto značku, ale zapíše ji pomocí `georss:where` značky. |
| `rss`                    | ano     | ne    | GeoRSS napsaný ve formátu ATOM.                                                                 |
| `rss:author`             | ano     | partial | Zapsáno jako `atom:author` .                                                                 |
| `rss:category`           | ano     | partial | Zapsáno jako `atom:category` .                                                               |
| `rss:channel`            | ano     | ne    |                                                                                                |
| `rss:cloud`              | ano     | ne    |                                                                                                |
| `rss:comments`           | ano     | ne    |                                                                                                |
| `rss:copyright`          | ano     | partial | Zapsáno jako `atom:rights` obrazec if, který `rights` `properties` již neobsahuje vlastnost.       |
| `rss:description`        | ano     | partial | Zapsáno jako `atom:content` obrazec if, který `content` `properties` již neobsahuje vlastnost.      |
| `rss:docs`               | ano     | ne    |                                                                                                |
| `rss:enclosure`          | ano     | ne    |                                                                                                |
| `rss:generator`          | ano     | ne    |                                                                                                |
| `rss:guid`               | ano     | partial | Zapsáno jako `atom:id` obrazec if, který `id` `properties` již neobsahuje vlastnost.         |
| `rss:image`              | ano     | partial | Zapsáno jako `atom:logo` obrazec if, který `logo` `properties` již neobsahuje vlastnost.      |
| `rss:item`               | ano     | partial | Zapsáno jako `atom:entry` .                                                                  |
| `rss:language`           | ano     | ne    |                                                                                                |
| `rss:lastBuildDate`      | ano     | partial | Zapsáno jako `atom:updated` obrazec if, který `updated` `properties` již neobsahuje vlastnost.     |
| `rss:link`               | ano     | partial | Zapsáno jako `atom:link` .                                                                   |
| `rss:managingEditor`     | ano     | partial | Zapsáno jako `atom:contributor` .                                                            |
| `rss:pubDate`            | ano     | partial | Zapsáno jako `atom:published` obrazec if, který `published` `properties` již neobsahuje vlastnost.  |
| `rss:rating`             | ano     | ne    |                                                                                                |
| `rss:skipDays`           | ano     | ne    |                                                                                                |
| `rss:skipHours`          | ano     | ne    |                                                                                                |
| `rss:source`             | ano     | partial | Zapsáno jako `atom:source` obsahující `atom:link` .                                       |
| `rss:textInput`          | ano     | ne    |                                                                                                |
| `rss:title`              | ano     | partial | Zapsáno jako `atom:title` .                                                                  |
| `rss:ttl`                | ano     | ne    |                                                                                                |
| `rss:webMaster`          | ano     | ne    |                                                                                                |

### <a name="gml-elements"></a>GML elementy

Modul pro prostorové vstupně-výstupní operace podporuje následující elementy GML. 

| Název prvku            | Čtení | Zápis | Poznámky                                                                                  |
|-------------------------|------|-------|----------------------------------------------------------------------------------------|
| `gml:coordinates`       | ano  | ne    | Zapsáno jako `gml:posList` .                                                              |
| `gml:curveMember`       | ano  | ne    |                                                                                        |
| `gml:curveMembers`      | ano  | ne    |                                                                                        |
| `gml:Box`               | ano  | ne    | Zapsáno jako `gml:Envelope` .                                                             |
| `gml:description`       | ano  | ano   |                                                                                        |
| `gml:Envelope`          | ano  | ano   |                                                                                        |
| `gml:exterior`          | ano  | ano   |                                                                                        |
| `gml:Feature`           | ano  | ne    | Zapsáno jako obrazec.                                                                    |
| `gml:FeatureCollection` | ano  | ne    | Zapsáno jako kolekce geometrie.                                                      |
| `gml:featureMember`     | ano  | ne    | Zapsáno jako kolekce geometrie.                                                      |
| `gml:geometry`          | ano  | ne    | Zapsáno jako obrazec.                                                                    |
| `gml:geometryMember`    | ano  | ano   |                                                                                        |
| `gml:geometryMembers`   | ano  | ano   |                                                                                        |
| `gml:identifier`        | ano  | ano   |                                                                                        |
| `gml:innerBoundaryIs`   | ano  | ne    | Napsáno pomocí `gml.interior` .                                                          |
| `gml:interior`          | ano  | ano   |                                                                                        |
| `gml:LinearRing`        | ano  | ano   |                                                                                        |
| `gml:LineString`        | ano  | ano   |                                                                                        |
| `gml:lineStringMember`  | ano  | ano   |                                                                                        |
| `gml:lineStringMembers` | ano  | ne    |                                                                                        |
| `gml:MultiCurve`        | ano  | ne    | Čte pouze `gml:LineString` členy. Zapsáno jako `gml.MultiLineString`                  |
| `gml:MultiGeometry`     | partial  | partial   | Pouze číst jako Součástcollection.                                              |
| `gml:MultiLineString`   | ano  | ano   |                                                                                        |
| `gml:MultiPoint`        | ano  | ano   |                                                                                        |
| `gml:MultiPolygon`      | ano  | ano   |                                                                                        |
| `gml:MultiSurface`      | ano  | ne    | Čte pouze `gml:Polygon` členy. Zapsáno jako `gml.MultiPolygon`                        |
| `gml:name`              | ano  | ano   |                                                                                        |
| `gml:outerBoundaryIs`   | ano  | ne    | Napsáno pomocí `gml.exterior` .                                                          |
| `gml:Point`             | ano  | ano   |                                                                                        |
| `gml:pointMember`       | ano  | ano   |                                                                                        |
| `gml:pointMembers`      | ano  | ne    |                                                                                        |
| `gml:Polygon`           | ano  | ano   |                                                                                        |
| `gml:polygonMember`     | ano  | ano   |                                                                                        |
| `gml:polygonMembers`    | ano  | ne    |                                                                                        |
| `gml:pos`               | ano  | ano   |                                                                                        |
| `gml:posList`           | ano  | ano   |                                                                                        |
| `gml:surfaceMember`     | ano  | ano   |                                                                                        |

#### <a name="additional-notes"></a>Další poznámky

- Prvky členů budou prohledány na geometrii, která může být ukryto v rámci podřízených elementů. Tato operace hledání je nutná, protože mnoho formátů XML, které rozšiřuje z GML, nesmí umístit geometrii jako přímý podřízený prvek člena.
- `srsName` je částečně podporováno pro souřadnice WGS84 a následující kódy:[EPSG: 4326](https://epsg.io/4326)) a web Mercator ([EPSG: 3857](https://epsg.io/3857) nebo jeden z jeho alternativních kódů. Všechny ostatní systémy souřadnic se budou analyzovat jako WGS84 tak, jak jsou.
- Pokud není zadána při čtení datového kanálu XML, je pořadí osy určeno na základě pokynů v informačním kanálu XML. Pro pořadí osy "Zeměpisná délka" Zeměpisná délka je uvedena předvolba.
- Pokud není pro vlastnosti při zápisu do souboru GML zadán vlastní obor názvů GML, další informace o vlastnostech nebudou přidány.

### <a name="gpx-elements"></a>GPX elementy

Modul pro prostorové vstupně-výstupní operace podporuje následující elementy GPX.

| Název prvku             | Čtení    | Zápis   | Poznámky                                                                                       |
|--------------------------|---------|---------|---------------------------------------------------------------------------------------------|
| `gpx:ageofdgpsdata`      | ano     | ano     |                                                                                             |
| `gpx:author`             | ano     | ano     |                                                                                             |
| `gpx:bounds`             | ano     | ano     | Převádí na LocationRect při čtení.                                                    |
| `gpx:cmt`                | ano     | ano     |                                                                                             |
| `gpx:copyright`          | ano     | ano     |                                                                                             |
| `gpx:desc`               | ano     | ano     | Zkopírována do vlastnosti Description při čtení, aby byla zarovnána s jinými formáty XML.               |
| `gpx:dgpsid`             | ano     | ano     |                                                                                             |
| `gpx:ele`                | ano     | ano     |                                                                                             |
| `gpx:extensions`         | partial | partial | Při čtení jsou extrahovány informace o stylu. Všechna ostatní rozšíření budou shrnuta do jednoduchého objektu JSON. Napíší se jenom informace o stylu obrazce. |
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
| `gpx:sym`                | ano     | ano     | Hodnota je zachycena, ale není použita k změně ikony připínáček.                               |
| `gpx:text`               | ano     | ano     |                                                                                             |
| `gpx:time`               | ano     | ano     |                                                                                             |
| `gpx:trk`                | ano     | ano     |                                                                                             |
| `gpx:trkpt`              | ano     | ano     |                                                                                             |
| `gpx:trkseg`             | ano     | ano     |                                                                                             |
| `gpx:type`               | ano     | ano     |                                                                                             |
| `gpx:vdop`               | ano     | ano     |                                                                                             |
| `gpx:wpt`                | ano     | ano     |                                                                                             |
| `gpx_style:color`        | ano     | ano     |                                                                                             |
| `gpx_style:line`         | partial | partial | `color``opacity` `width` `lineCap` jsou podporovány.                                           |
| `gpx_style:opacity`      | ano     | ano     |                                                                                             |
| `gpx_style:width`        | ano     | ano     |                                                                                             |
| `gpxx:DisplayColor`      | ano     | ne      | Slouží k určení barvy tvaru. Při psaní se `gpx_style:line` místo toho použije barva.  |
| `gpxx:RouteExtension`    | partial | ne      | Všechny vlastnosti jsou čteny do `properties` . `DisplayColor`Používá se jenom.                     |
| `gpxx:TrackExtension`    | partial | ne      | Všechny vlastnosti jsou čteny do `properties` . `DisplayColor`Používá se jenom.                     |
| `gpxx:WaypointExtension` | partial | ne      | Všechny vlastnosti jsou čteny do `properties` . `DisplayColor`Používá se jenom.                     |
| `gpx:keywords`           | ano     | ano     |                                                                                             |
| `gpx:fix`                | ano     | ano     |                                                                                             |

#### <a name="additional-notes"></a>Další poznámky

Při psaní;

- Odkazy na jednotlivé Waypoints se budou rozdělit do jednotlivých.
- Mnohoúhelníky a další mnohoúhelníky budou zapsány jako stopy. 
  
## <a name="supported-well-known-text-geometry-types"></a>Podporované typy geometrie textu Well-Known

| Typ geometrie | Čtení | Zápis |
|--------------|:----:|:-----:|
| Vyberte | x | x |
| BOD Z | x | x | 
| BOD M | x | x<sup>[2]</sup> |
| ZM BODU | x<sup>[1]</sup><sup>[2]</sup> | | 
| LINESTRING | x | x |
| LINESTRING Z | x | x | 
| LINESTRING M | x | x<sup>[2]</sup> |
| LINESTRING ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| POSTUPNĚ | x | x |
| MNOHOÚHELNÍK Z | x | x |
| MNOHOÚHELNÍK M | x | x<sup>[2]</sup> |
| ZM MNOHOÚHELNÍKU | x<sup>[1]</sup><sup>[2]</sup> | | 
| MULTIPOINT | x | x |
| SYSTÉM MULTIPOINT Z | x | x | 
| MULTIPOINT M | x | x<sup>[2]</sup> |
| POMULTIPOINTINT ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| MULTILINESTRING | x | x |
| MULTILINESTRING Z | x | x | 
| MULTILINESTRING M | x | x<sup>[2]</sup> |
| MULTILINESTRING ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| MULTIPOLYGON | x | x |
| VÍCE MNOHOÚHELNÍKŮ Z | x | x | 
| VÍCE MNOHOÚHELNÍKŮ M | x | x<sup>[2]</sup> |
| ZM S VÍCE MNOHOÚHELNÍKY | x<sup>[1]</sup><sup>[2]</sup> | | 
| GEOMETRYCOLLECTION | x | x |
| GEOMETRIECOLLECTION Z | x | x | 
| GEOMETRIECOLLECTION M | x | x<sup>[2]</sup> | 
| GEOMETRIECOLLECTION ZM | x<sup>[1]</sup><sup>[2]</sup> | x | 

\[1 \] pouze parametr Z je zachycen a přidán jako třetí hodnota v hodnotě pozice.

\[2 \] M parametr není zachycen.

## <a name="delimited-spatial-data-support"></a>Podpora prostorových dat s oddělovači

Oddělená prostorová data, jako jsou textový soubor s oddělovači (CSV), mají často sloupce, které obsahují prostorová data. Například mohou být sloupce obsahující informace o zeměpisné šířce a délce. V Well-Known formátu textu může být sloupec, který obsahuje data prostorové geometrie.

### <a name="spatial-data-column-detection"></a>Detekce sloupce prostorových dat

Při čtení souboru s oddělovači, který obsahuje prostorová data, se hlavička analyzuje, aby se určilo, které sloupce obsahují pole umístění. Pokud hlavička obsahuje informace o typu, použije se k přetypování hodnot buněk na příslušný typ. Pokud není zadaný žádný nadpis, bude se analyzovat první řádek, který se použije k vygenerování hlavičky. Při analýze prvního řádku se spustí kontroly tak, aby odpovídaly názvům sloupců s následujícími názvy při nerozlišování velkých a malých písmen. V případě, že v souboru existují dva nebo více názvů, pořadí názvů je priorita.

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

#### <a name="elevation"></a>Zvýšení oprávnění

- `elevation`
- `elv`
- `altitude`
- `alt`
- `z`

#### <a name="geography"></a>Geografie

První řádek dat bude prohledáván pro řetězce, které jsou ve formátu Well-Known text. 

### <a name="delimited-data-column-types"></a>Typy datových sloupců s oddělovači

Při kontrole řádku záhlaví budou všechny informace o typu, které jsou v názvu sloupce, extrahovány a použity k přetypování buněk v daném sloupci. Tady je příklad názvu sloupce, který má hodnotu typu: ColumnName (typeName). Podporují se následující názvy typů bez rozlišení velkých a malých písmen:

#### <a name="numbers"></a>Čísla

- EDM. Int64
- int
- long
- EDM. Double
- float
- double
- číslo

#### <a name="booleans"></a>Logické hodnoty

- EDM. Boolean
- bool
- boolean

#### <a name="dates"></a>Kalendářní data

- EDM. DateTime
- date
- datetime

#### <a name="geography"></a>Geografie

- EDM. geografie
- geografické

#### <a name="strings"></a>Řetězce

- EDM. String
- varchar
- text
- řetězec

Pokud nelze extrahovat žádné informace o typu z hlavičky a možnost dynamického zápisu je povolena při čtení, pak se každá buňka jednotlivě analyzuje a určí, jaký datový typ je nejvhodnější k přetypování.

## <a name="next-steps"></a>Další kroky

Další ukázky kódu pro přidání do vašich map najdete v následujících článcích:

[Čtení a zápis prostorových dat](spatial-io-read-write-spatial-data.md)
