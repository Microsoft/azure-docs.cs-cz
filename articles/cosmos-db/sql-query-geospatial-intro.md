---
title: Data geoprostorového a geografického umístění JSON v Azure Cosmos DB
description: Naučte se vytvářet prostorové objekty pomocí Azure Cosmos DB a rozhraní SQL API.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: 59c8b31dcc8594d2cafb2db7832e290b01026f60
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2020
ms.locfileid: "79367580"
---
# <a name="geospatial-and-geojson-location-data-in-azure-cosmos-db"></a>Data geoprostorového a geografického umístění JSON v Azure Cosmos DB

Tento článek je úvodem do geoprostorové funkce ve službě Azure Cosmos DB. V současné době jsou ukládání a přístup k geoprostorovému datům podporované jenom pomocí Azure Cosmos DBch jenom účtů rozhraní SQL API. Po přečtení naší dokumentace k geoprostorovému indexování budete moci zodpovědět následující otázky:

* Jak můžu ukládat prostorová data ve službě Azure Cosmos DB?
* Jak lze Geoprostorová data ve službě Azure Cosmos DB v SQL a LINQ dotaz?
* Jak povolit nebo zakázat prostorového indexování ve službě Azure Cosmos DB?

## <a name="introduction-to-spatial-data"></a>Úvod do prostorových dat

Prostorová data popisuje pozice a tvar objektů v prostoru. Ve většině aplikací tyto odpovídá objektům na světě a Geoprostorová data. Prostorová data slouží k reprezentaci umístění osoby, místa, které vás zajímají nebo hranici město nebo jezera. Běžné případy použití často zahrnují blízkých výrazů dotazů, třeba, "najít všechny v kavárnách téměř moji aktuální polohu."

Rozhraní SQL API Azure Cosmos DB podporuje dva typy prostorových dat: typ dat **geometrie** a **zeměpisný** datový typ.

- Typ **geometrie** reprezentuje data v Euclidean (plochý) souřadnicový systém.
- **Zeměpisný** typ představuje data v systému souřadnic pro kulatou zemi.

## <a name="supported-data-types"></a>Podporované datové typy

Azure Cosmos DB podporuje indexování a dotazování na data geoprostorového bodu, která se reprezentují pomocí [specifikace pro injson](https://tools.ietf.org/html/rfc7946). GeoJSON datové struktury jsou vždycky platné objekty JSON, tak můžete ukládat a dotazovat pomocí služby Azure Cosmos DB bez jakékoli specializované nástroje a knihovny.

Azure Cosmos DB podporuje následující prostorové datové typy:

- Vyberte
- LineString
- Postupně
- MultiPolygon

### <a name="points"></a>body

**Bod** označuje jednu pozici v prostoru. V Geoprostorová data představuje bod přesné umístění, které by mohly být adresu blízkým úložiště, jako veřejný terminál, automobilu nebo město.  Bod je vyjádřena v páru GeoJSON (a Azure Cosmos DB) pomocí jeho souřadnice nebo zeměpisné šířky a délky.

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

Pro zeměpisný datový typ Určuje specifikace **geografické** JSON délku první a zeměpisnou šířku sekund. Stejně jako v ostatních aplikacích mapování zeměpisné šířky a délky se jedná o úhly a reprezentovat z hlediska stupňů. Zeměpisná délka hodnoty se měří z poledníku, který a jsou v rozmezí od-180 stupňů a 180.0 stupňů, a hodnoty zeměpisné šířky se měří od rovníku spadají do rozsahu-90.0 stupňů a 90.0 stupňů.

Azure Cosmos DB interpretuje souřadnice reprezentovaný za WGS 84 referenčního systému. Níže naleznete další podrobnosti o souřadnic referenčních systémů.

### <a name="linestrings"></a>LineStrings

**LineStrings** představuje řadu dvou nebo více bodů v prostoru a segmentů čáry, které je spojují. V Geoprostorová data jsou LineStrings běžně používaných ke znázornění dálnice nebo řek.

**LineStrings v rámečku JSON**

```json
    "type":"LineString",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 31.8, -4.7 ]
    ] ]
```

### <a name="polygons"></a>Mnohoúhelníky

**Mnohoúhelník** je hranice propojených bodů, které tvoří uzavřenou LineString. Mnohoúhelníky se běžně používá k reprezentování fyzických struktur jako jezera nebo politickou jurisdikce jako měst a států. Tady je příklad mnohoúhelníku v Azure Cosmos DB:

**Mnohoúhelníky v typu injson**

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
> Specifikaci GeoJSON vyžaduje, že pro platné mnohoúhelníky poslední souřadnic pár poskytuje by měl být stejný jako první, chcete-li vytvořit zavřeného tvaru.
>
> Body v rámci mnohoúhelníku musí zadat v pořadí proti směru hodinových ručiček. Mnohoúhelník zadat v pořadí po směru hodinových ručiček představuje inverzní oblasti v rámci něj.
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

Vzhledem k tomu, že tvar země je nepravidelný, jsou souřadnice geograficky geoprostorového data zastoupeny v mnoha referenčních systémech (počítačový systém), každý s vlastními snímky odkazů a jednotkami měření. Například "National mřížky z Británie" je referenční systém je přesné pro Spojené království, ale ne mimo něj.

Nejoblíbenější počítačový systém používaný v současnosti dnes je World Geodetic System [WGS-84](https://earth-info.nga.mil/GandG/update/index.php). GPS zařízení a mnoho služeb mapování, včetně mapy Google a rozhraní API map Bing použijte WGS 84. Azure Cosmos DB podporuje indexování geograficky geoprostorového data a jejich dotazování jenom pomocí POČÍTAČového systému WGS-84.

## <a name="creating-documents-with-spatial-data"></a>Vytváření dokumentů s prostorovými daty formátu
Při vytváření dokumentů, které obsahují hodnoty GeoJSON se automaticky indexují se prostorový index podle zásady indexování kontejneru. Pokud pracujete s využitím Azure Cosmos DB SDK v dynamicky psaný jazyk, jako je Python nebo Node.js, je nutné vytvořit platný GeoJSON.

**Vytvoření dokumentu s Geoprostorovémi daty v Node. js**

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

Pokud pracujete s rozhraními API SQL, můžete použít třídy `Point`, `LineString`, `Polygon`a `MultiPolygon` v rámci oboru názvů `Microsoft.Azure.Cosmos.Spatial` pro vložení informací o poloze v objektech aplikace. Tyto třídy pomáhají zjednodušit serializace a deserializace prostorových dat do GeoJSON.

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

Pokud nemáte informace o zeměpisné šířce a délce, ale máte fyzické adresy nebo název umístění, jako je město nebo země nebo oblast, můžete vyhledat skutečné souřadnice pomocí služby geografické kódování, jako je například služba Bing Maps služby REST. Další informace o geografickém kódování mapy Bing [najdete tady](https://msdn.microsoft.com/library/ff701713.aspx).

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak začít pracovat s podporuje geoprostorové funkce ve službě Azure Cosmos DB, dále můžete:

* Další informace o [Azure Cosmos DB dotaz](sql-query-getting-started.md)
* Další informace o [dotazování prostorových dat pomocí Azure Cosmos DB](sql-query-geospatial-query.md)
* Další informace o [indexování prostorových dat pomocí Azure Cosmos DB](sql-query-geospatial-index.md)