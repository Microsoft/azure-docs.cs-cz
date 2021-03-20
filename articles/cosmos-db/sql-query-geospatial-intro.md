---
title: Data geoprostorového a geografického umístění JSON v Azure Cosmos DB
description: Naučte se vytvářet prostorové objekty pomocí Azure Cosmos DB a rozhraní SQL API.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/25/2021
ms.author: tisande
ms.custom: devx-track-js
ms.openlocfilehash: b20c72ae3ed8a8fffa02fc3a2c86f9f73ba2663b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101692126"
---
# <a name="geospatial-and-geojson-location-data-in-azure-cosmos-db"></a>Data geoprostorového a geografického umístění JSON v Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Tento článek představuje úvod do geoprostorové funkce v Azure Cosmos DB. Po přečtení naší dokumentace k geoprostorovému indexování budete moci zodpovědět následující otázky:

* Návody ukládat prostorová data do Azure Cosmos DB?
* Jak se dají dotazovat prostorová data v Azure Cosmos DB v SQL a LINQ?
* Návody povolit nebo zakázat indexování prostorových dat v Azure Cosmos DB?

## <a name="spatial-data-use-cases"></a>Případy použití prostorových dat

Geoprostorové údaje často zahrnují dotazy na Proximity, například "najít všechny kavárny poblíž aktuálního umístění". Běžné případy použití:

* Analýzy geografického umístění, které řídí konkrétní nalezené marketingové iniciativy.
* Individuální nastavení na základě umístění, pro různé obory, jako je maloobchodní a zdravotní péče.
* Vylepšení logistiky pro optimalizaci přenosu.
* Analýza rizik, zejména pro pojišťovací a finanční společnosti.
* Povědomí o situaci, výstrahy a oznámení.

## <a name="introduction-to-spatial-data"></a>Seznámení s prostorovými daty

Prostorová data popisují umístění a tvar objektů v prostoru. Ve většině aplikací odpovídají objekty pro země a geoprostorové údaje. Prostorová data lze použít k reprezentaci umístění osoby, místa zájmu nebo hranice města nebo jezera.

Rozhraní SQL API Azure Cosmos DB podporuje dva typy prostorových dat: typ dat **geometrie** a **zeměpisný** datový typ.

- Typ **geometrie** reprezentuje data v Euclidean (plochý) souřadnicový systém.
- **Zeměpisný** typ představuje data v systému souřadnic pro kulatou zemi.

## <a name="supported-data-types"></a>Podporované datové typy

Azure Cosmos DB podporuje indexování a dotazování na data geoprostorového bodu, která se reprezentují pomocí [specifikace pro injson](https://tools.ietf.org/html/rfc7946). Datové struktury pro injson jsou vždy platné objekty JSON, takže je lze uložit a dotazovat pomocí Azure Cosmos DB bez jakýchkoli specializovaných nástrojů nebo knihoven.

Azure Cosmos DB podporuje následující prostorové datové typy:

- Vyberte
- LineString
- Mnohoúhelník
- MultiPolygon

### <a name="points"></a>Points

**Bod** označuje jednu pozici v prostoru. V geoprostorovém data ukazuje bod přesné umístění, což může být ulice pro nákupní obchod, veřejný terminál, automobil nebo město.  Bod je reprezentován v typu zeměpisná JSON (a Azure Cosmos DB) pomocí páru souřadnic nebo délky a zeměpisné šířky.

Tady je příklad JSON pro bod:

**Body v Azure Cosmos DB**

```json
{
    "type":"Point",
    "coordinates":[ 31.9, -4.8 ]
}
```

Typy prostorových dat lze vložit do dokumentu Azure Cosmos DB, jak je znázorněno v tomto příkladu profilu uživatele obsahujícího údaje o umístění:

**Použít profil s umístěním uloženým v Azure Cosmos DB**

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

### <a name="points-in-a-geometry-coordinate-system"></a>Body v systému souřadnic geometrie

Pro **geometrický** typ dat určuje specifikace injson jako první a svislou osu druhé.

### <a name="points-in-a-geography-coordinate-system"></a>Body v systému geografických souřadnic

Pro zeměpisný datový typ Určuje specifikace **geografické** JSON délku první a zeměpisnou šířku sekund. Podobně jako u jiných mapovacích aplikací, zeměpisná délka a zeměpisná šířka jsou úhly a reprezentované ve stupních. Hodnoty zeměpisných hodnot se měří od primárního poledníku a jsou mezi-180 stupnemi a 180,0 stupni a hodnoty zeměpisné šířky jsou měřeny od přístupnosti a jsou mezi-90,0mi a 90,0 stupni.

Azure Cosmos DB interpretuje souřadnice, jak je znázorněno na referenčním systému WGS-84. Další informace o souřadnicových referenčních systémech najdete níže v části.

### <a name="linestrings"></a>LineStrings

**LineStrings** představuje řadu dvou nebo více bodů v prostoru a segmentů čáry, které je spojují. V geoprostorovém data se LineStrings běžně používají k zastupování dálnic nebo řek.

**LineStrings v rámečku JSON**

```json
    "type":"LineString",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 31.8, -4.7 ]
    ] ]
```

### <a name="polygons"></a>Mnohoúhelníky

**Mnohoúhelník** je hranice propojených bodů, které tvoří uzavřenou LineString. Mnohoúhelníky se běžně používají ke znázornění přirozených Form, jako jsou jezera nebo politické pravomoci, jako jsou města a stavy. Tady je příklad mnohoúhelníku v Azure Cosmos DB:

**Mnohoúhelníky v typu injson**

```json
{
    "type":"Polygon",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 32, -5 ],
        [ 32, -4.7 ],
        [ 31.8, -4.7 ],
        [ 31.8, -5 ]
    ] ]
}
```

> [!NOTE]
> Specifikace typu injson vyžaduje, aby pro platné mnohoúhelníky byl poslední poskytnutý pár souřadnic stejný jako první, aby bylo možné vytvořit uzavřený obrazec.
>
> Body v mnohoúhelníku musí být zadány v pořadí proti směru hodinových ručiček. Mnohoúhelník zadaný v pořadí zleva doprava představuje invertující oblast v oblasti.
>
>

### <a name="multipolygons"></a>Více mnohoúhelníků

Více **mnohoúhelníků** je pole nula nebo více mnohoúhelníků. U více **mnohoúhelníků** nelze překrývat strany nebo mít žádnou běžnou oblast. Můžou se dotýkat v jednom nebo několika bodech.

**Další mnohoúhelníky v typu injson**

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

## <a name="coordinate-reference-systems"></a>Souřadnice referenčních systémů

Vzhledem k tomu, že tvar země je nepravidelný, jsou souřadnice geograficky geoprostorového data zastoupeny v mnoha referenčních systémech (počítačový systém), každý s vlastními snímky odkazů a jednotkami měření. Například "národní mřížka Británie" je referenční systém pro Spojené království, ale ne mimo něj.

Nejoblíbenější počítačový systém používaný v současnosti dnes je World Geodetic System  [WGS-84](https://earth-info.nga.mil/GandG/update/index.php). Zařízení GPS a mnoho služeb mapování, včetně rozhraní API Map Google a Bing Maps, používají WGS-84. Azure Cosmos DB podporuje indexování geograficky geoprostorového data a jejich dotazování jenom pomocí POČÍTAČového systému WGS-84.

## <a name="creating-documents-with-spatial-data"></a>Vytváření dokumentů s prostorovými daty
Když vytváříte dokumenty, které obsahují hodnoty typu injson, jsou automaticky indexovány pomocí prostorového indexu v souladu se zásadami indexování kontejneru. Pokud pracujete se sadou Azure Cosmos DB SDK v dynamicky typovaném jazyce, jako je Python nebo Node.js, je nutné vytvořit platný typ injson.

**Vytvoření dokumentu se Geoprostorovémi daty v Node.js**

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

Pokud pracujete s rozhraními API SQL, můžete použít `Point` `LineString` třídy,, a `Polygon` `MultiPolygon` v rámci `Microsoft.Azure.Cosmos.Spatial` oboru názvů pro vložení informací o poloze v objektech aplikace. Tyto třídy vám pomůžou zjednodušit serializaci a deserializaci prostorových dat do typu injson.

**Vytváření dokumentů s Geoprostorovémi daty v .NET**

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

Pokud nemáte informace o zeměpisné šířce a délce, ale máte fyzické adresy nebo název umístění, jako je město nebo země nebo oblast, můžete vyhledat skutečné souřadnice pomocí služby geografické kódování, jako je například služba Bing Maps služby REST. Další informace o geografickém kódování mapy Bing [najdete tady](/bingmaps/rest-services/).

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak začít se geoprostorové podpory v Azure Cosmos DB, můžete následující:

* Další informace o [Azure Cosmos DB dotaz](sql-query-getting-started.md)
* Další informace o [dotazování prostorových dat pomocí Azure Cosmos DB](sql-query-geospatial-query.md)
* Další informace o [indexování prostorových dat pomocí Azure Cosmos DB](sql-query-geospatial-index.md)