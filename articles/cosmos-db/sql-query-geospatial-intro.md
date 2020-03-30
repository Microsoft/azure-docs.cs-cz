---
title: Geoprostorová data a data o poloze GeoJSON v Azure Cosmos DB
description: Zjistěte, jak vytvořit prostorové objekty pomocí Azure Cosmos DB a rozhraní SQL API.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: 59c8b31dcc8594d2cafb2db7832e290b01026f60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367580"
---
# <a name="geospatial-and-geojson-location-data-in-azure-cosmos-db"></a>Geoprostorová data a data o poloze GeoJSON v Azure Cosmos DB

Tento článek je úvod do geoprostorové funkce v Azure Cosmos DB. V současné době ukládání a přístup ke geoprostorovým datům je podporována azure cosmos DB SQL API účty jenom. Po přečtení naší dokumentace o geoprostorovéindexingu budete schopni odpovědět na následující otázky:

* Jak můžu ukládat prostorová data v Azure Cosmos DB?
* Jak se můžu dotazovat na geoprostorová data v Azure Cosmos DB v SQL a LINQ?
* Jak povolím nebo zakážu prostorové indexování v Azure Cosmos DB?

## <a name="introduction-to-spatial-data"></a>Úvod do prostorových dat

Prostorová data popisují polohu a tvar objektů v prostoru. Ve většině aplikací odpovídají objektům na zemi a geoprostorovým datům. Prostorová data lze použít k reprezentaci umístění osoby, místa zájmu nebo hranice města nebo jezera. Běžné případy použití často zahrnují dotazy na blízkost, například "najít všechny kavárny v blízkosti mé aktuální polohy."

Rozhraní SQL API služby Azure Cosmos DB podporuje dva typy prostorových dat: datový typ **geometrie** a typ dat **zeměpisu.**

- Typ **geometrie** představuje data v euklidovský (plochý) souřadnicový systém
- Typ **geografie** představuje data v souřadnicovém systému s kulatá země.

## <a name="supported-data-types"></a>Podporované datové typy

Azure Cosmos DB podporuje indexování a dotazování geoprostorových bodových dat, která jsou reprezentována pomocí [specifikace GeoJSON](https://tools.ietf.org/html/rfc7946). Datové struktury GeoJSON jsou vždy platné objekty JSON, takže je můžete ukládat a dotazovat pomocí Azure Cosmos DB bez specializovaných nástrojů nebo knihoven.

Azure Cosmos DB podporuje následující typy prostorových dat:

- Bod
- Linestring
- Mnohoúhelník
- Multipolygon

### <a name="points"></a>Points

A **Point** označuje jednu pozici v prostoru. V geoprostorových datech point představuje přesnou polohu, která může být adresou ulice obchodu s potravinami, kiosku, automobilu nebo města.  Bod je reprezentován v GeoJSON (a Azure Cosmos DB) pomocí jeho souřadnice dvojice nebo zeměpisnou délku a šířku.

Zde je příklad JSON pro bod:

**Body v Db Azure Cosmos**

```json
{
    "type":"Point",
    "coordinates":[ 31.9, -4.8 ]
}
```

Typy prostorových dat lze vložit do dokumentu Azure Cosmos DB, jak je znázorněno v tomto příkladu profilu uživatele obsahujícího data o poloze:

**Použití profilu s umístěním uloženým v Azure Cosmos DB**

```json
{
    "id":"cosmosdb-profile",
    "screen_name":"@CosmosDB",
    "city":"Redmond",
    "topics":[ "global", "distributed" ],
    "location":{
        "type":"Point",
        "coordinates":[ 31.9, -4.8 ]
    }
}
```

### <a name="points-in-a-geometry-coordinate-system"></a>Body v souřadnicovém systému geometrie

Pro datový typ **geometrie** specifikuje specifikace GeoJSON nejprve vodorovnou osu a druhou svislou osu.

### <a name="points-in-a-geography-coordinate-system"></a>Body v souřadnicovém systému zeměpisu

Pro **geografický** datový typ specifikace GeoJSON určuje zeměpisnou délku první a druhou zeměpisnou šířku. Stejně jako v jiných mapovacích aplikacích jsou zeměpisná délka a šířka úhly a reprezentovány z hlediska stupňů. Hodnoty zeměpisné délky se měří od prime poledníku a jsou mezi -180 stupňů a 180,0 stupňů a hodnoty zeměpisné šířky jsou měřeny od rovníku a jsou mezi -90,0 a 90,0 stupňů.

Azure Cosmos DB interpretuje souřadnice tak, jak jsou reprezentovány podle referenčního systému WGS-84. Další podrobnosti o souřadnicových referenčních systémech naleznete níže.

### <a name="linestrings"></a>Řetězce řádků

**LineStrings** představují řadu dvou nebo více bodů v prostoru a segmenty čáry, které je spojují. V geoprostorových datech linestrings se běžně používají k reprezentaci dálnic nebo řek.

**LineStrings v GeoJSON**

```json
    "type":"LineString",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 31.8, -4.7 ]
    ] ]
```

### <a name="polygons"></a>Mnohoúhelníky

**Polygon** je hranice propojených bodů, která tvoří uzavřený LineString. Polygony se běžně používají k reprezentaci přírodních útvarů, jako jsou jezera nebo politické jurisdikce, jako jsou města a státy. Tady je příklad polygonu v Azure Cosmos DB:

**Polygony v GeoJSON**

```json
{
    "type":"Polygon",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 31.8, -4.7 ],
        [ 32, -4.7 ],
        [ 32, -5 ],
        [ 31.8, -5 ]
    ] ]
}
```

> [!NOTE]
> Specifikace GeoJSON vyžaduje, aby pro platné polygony měla být poslední poskytnutá dvojice souřadnic stejná jako první, aby se vytvořil uzavřený tvar.
>
> Body v polygonu musí být zadány v pořadí proti směru hodinových ručiček. Polygon zadaný v pořadí ve směru hodinových ručiček představuje inverzní oblast v něm.
>
>

### <a name="multipolygons"></a>Multipolygony

**MultiPolygon** je pole nula nebo více mnohonoly. **MultiPolygony** se nemohou překrývat se stranami nebo mají žádnou společnou oblast. Mohou se dotýkat na jednom nebo více místech.

**Multipolygony v GeoJSON**

```json
{
    "type":"MultiPolygon",
    "coordinates":[[[
        [52.0, 12.0],
        [53.0, 12.0],
        [53.0, 13.0],
        [52.0, 13.0],
        [52.0, 12.0]
        ]],
        [[
        [50.0, 0.0],
        [51.0, 0.0],
        [51.0, 5.0],
        [50.0, 5.0],
        [50.0, 0.0]
        ]]]
}
```

## <a name="coordinate-reference-systems"></a>Souřadnicové referenční systémy

Vzhledem k tomu, že tvar Země je nepravidelný, jsou souřadnice geoprostorových dat geografie reprezentovány v mnoha souřadnicových referenčních systémech (CRS), z nichž každý má své vlastní referenční rámce a měrné jednotky. Například "National Grid of Britain" je referenční systém je přesný pro Spojené království, ale ne mimo něj.

Nejpopulárnější CRS v provozu dnes je Svět geodetický systém [WGS-84](https://earth-info.nga.mil/GandG/update/index.php). Zařízení GPS a mnoho mapových služeb, včetně rozhraní API Map Google a Map Bing, používá rozhraní WGS-84. Azure Cosmos DB podporuje indexování a dotazování geoprostorových dat zeměpisu pomocí pouze služby CRS WGS-84.

## <a name="creating-documents-with-spatial-data"></a>Vytváření dokumentů s prostorovými daty
Když vytvoříte dokumenty, které obsahují hodnoty GeoJSON, jsou automaticky indexovány prostorovým indexem v souladu se zásadami indexování kontejneru. Pokud pracujete s Azure Cosmos DB SDK v dynamicky zadaný jazyk, jako je Python nebo Node.js, musíte vytvořit platný GeoJSON.

**Vytvořit dokument s geoprostorovými daty v souboru Node.js**

```javascript
var userProfileDocument = {
    "id":"cosmosdb",
    "location":{
        "type":"Point",
        "coordinates":[ -122.12, 47.66 ]
    }
};

client.createDocument(`dbs/${databaseName}/colls/${collectionName}`, userProfileDocument, (err, created) => {
    // additional code within the callback
});
```

Pokud pracujete s sql api, můžete použít `Point` `LineString`, `Polygon`, `MultiPolygon` a `Microsoft.Azure.Cosmos.Spatial` třídy v oboru názvů k vložení informací o umístění v objektech aplikace. Tyto třídy pomáhají zjednodušit serializaci a deserializaci prostorových dat do GeoJSON.

**Vytvořit dokument s geoprostorovými daty v rozhraní .NET**

```csharp
using Microsoft.Azure.Cosmos.Spatial;

public class UserProfile
{
    [JsonProperty("id")]
    public string id { get; set; }

    [JsonProperty("location")]
    public Point Location { get; set; }

    // More properties
}

await container.CreateItemAsync( new UserProfile
    {
        id = "cosmosdb",
        Location = new Point (-122.12, 47.66)
    });
```

Pokud nemáte informace o zeměpisné šířce a zeměpisné šířce, ale máte fyzické adresy nebo název lokality, jako je město nebo země nebo oblast, můžete vyhledat skutečné souřadnice pomocí služby geokódování, jako je služba Bing Maps REST Services. Další informace o geokódování Map Bing [naleznete zde](https://msdn.microsoft.com/library/ff701713.aspx).

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak začít s geoprostorovou podporou v Azure Cosmos DB, můžete další:

* Další informace o [dotazu Azure Cosmos DB Query](sql-query-getting-started.md)
* Další informace o [dotazování prostorových dat pomocí Azure Cosmos DB](sql-query-geospatial-query.md)
* Další informace o [indexování prostorových dat pomocí Azure Cosmos DB](sql-query-geospatial-index.md)