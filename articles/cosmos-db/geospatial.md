---
title: Práce s daty geoprostorové v Azure Cosmos DB | Microsoft Docs
description: Pochopit, jak vytvářet, index a dotaz prostorových objekty s Azure Cosmos DB a rozhraní SQL API.
services: cosmos-db
documentationcenter: ''
author: arramac
manager: kfile
ms.assetid: 82ce2898-a9f9-4acf-af4d-8ca4ba9c7b8f
ms.service: cosmos-db
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 10/20/2017
ms.author: arramac
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6a78426ed6b54f0227dbd89c150f23af712f1b35
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="working-with-geospatial-and-geojson-location-data-in-azure-cosmos-db"></a>Práce s geoprostorové a GeoJSON umístění dat v Azure Cosmos DB
Tento článek je úvodem k funkci geoprostorové v [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). Po přečtení to, budete moct odpovězte si na následující otázky:

* Jak ukládat prostorových dat v Azure Cosmos DB?
* Jak můžete dotazovat geoprostorové data v Azure DB Cosmos v SQL a LINQ?
* Jak povolit nebo zakázat prostorových indexování v Azure Cosmos DB?

Tento článek ukazuje, jak pracovat s prostorových dat s rozhraním API SQL. Toto [Githubu projektu](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Geospatial/Program.cs) pro ukázky kódu.

## <a name="introduction-to-spatial-data"></a>Úvod do prostorových dat
Prostorová data popisuje pozice a tvar objektů v prostoru. Ve většině aplikací se tyto odpovídají objekty na země a geoprostorové data. Prostorová data můžete používá k reprezentování umístění osoby, místo zájmu nebo hranici města nebo jezero. Běžné případy použití často zahrnují blízkosti dotazy, například, "najít všechny v kavárnách téměř Moje aktuální umístění." 

### <a name="geojson"></a>GeoJSON
Podporuje Azure Cosmos DB indexování a dotazování dat geoprostorové bodu, která je reprezentována pomocí [GeoJSON specifikace](https://tools.ietf.org/html/rfc7946). GeoJSON datové struktury, jsou vždy objekty JSON je platný, a tak, aby se mohou být uloženy a dotazovat pomocí Azure Cosmos DB bez jakýchkoli specializovaných nástrojů nebo knihovny. Sady SDK Azure Cosmos DB zadejte pomocné třídy a metody, které usnadňují pracovat s prostorovými daty formátu. 

### <a name="points-linestrings-and-polygons"></a>Body, LineStrings a mnohoúhelníky
A **bodu** označuje jeden pozice v prostoru. V datech geoprostorové představuje bod přesné umístění, které by mohly být adresu supermarketu úložiště, celoobrazovkovém režimu, automobilu nebo města.  Bod je reprezentována v páru GeoJSON (a Azure Cosmos DB) pomocí jeho souřadnice nebo zeměpisné šířky a délky. Tady je příklad JSON pro bod.

**Body v Azure Cosmos DB**

```json
{
    "type":"Point",
    "coordinates":[ 31.9, -4.8 ]
}
```

> [!NOTE]
> Specifikace GeoJSON určuje zeměpisnou délku první a zeměpisnou šířku druhý. Podobně jako v ostatních aplikacích mapování zeměpisné šířky a délky jsou úhly a uvádí stupňů. Hodnoty zeměpisné délky se měří od základního poledníku a jsou v rozmezí od-180 stupňů a 180.0 stupňů a hodnoty zeměpisné šířky se měří od rovníku a jsou mezi-90.0 stupňů a 90.0 stupňů. 
> 
> Azure Cosmos DB interpretuje souřadnice reprezentovaný za WGS 84 referenčního systému. Níže najdete další podrobnosti o souřadnic referenčních systémů.
> 
> 

To může být vložen do dokument Azure Cosmos DB, jak je znázorněno v tomto příkladu obsahující data o umístění profilu uživatele:

**Použít profil s umístěním, které jsou uloženy v databázi Cosmos Azure**

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

Kromě bodů GeoJSON také podporuje LineStrings a mnohoúhelníky. **LineStrings** představují řadu dva nebo víc bodů v prostoru i segmenty čáry, která se připojují, je. V datech geoprostorové LineStrings běžně se používají k reprezentaci dálnice nebo řeky. A **mnohoúhelníku** je hranice připojené body, která tvoří uzavřené LineString. Mnohoúhelníky se běžně používají k vyjádření přirozené vytváření jako jezera nebo politickou oblasti jurisdikce jako města a stavy. Tady je příklad mnohoúhelníku v Azure Cosmos DB. 

**Mnohoúhelníky v GeoJSON**

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
> Specifikace GeoJSON vyžaduje, aby pro platný mnohoúhelníky, poslední souřadnic pár zadat stejný jako první, chcete-li vytvořit uzavřený obrazec.
> 
> Je třeba zadat body v rámci mnohoúhelníku v pořadí proti směru hodinových ručiček. Mnohoúhelníku zadaný v po směru hodinových ručiček pořadí představuje inverzní oblasti v něm.
> 
> 

Kromě Point, LineString a mnohoúhelníku GeoJSON také určuje vyjádření pro způsob seskupení více Geoprostorové umístění a také jak přidružit informace o zeměpisné poloze jako libovolné vlastnosti **funkce**. Vzhledem k tomu, že tyto objekty jsou platný kód JSON, všechny jde uloženy a zpracovány v Azure Cosmos DB. Ale Azure Cosmos DB podporuje pouze automatické indexování bodů.

### <a name="coordinate-reference-systems"></a>Koordinaci referenčních systémů
Vzhledem k tomu, že tvar zemském povrchu je nestandardní, jsou reprezentovány souřadnice geoprostorové data v mnoha systémy souřadnic odkaz (CRS), každou s vlastní referenční rámce a měrné jednotky. Například "National mřížky z Británie" je referenční systém je velmi přesná pro Velkou Británii, ale ne mimo něj. 

Nejoblíbenější řádku používá dnes je systém geodetické World [WGS 84](http://earth-info.nga.mil/GandG/wgs84/). GPS zařízení a velký počet mapování služby včetně mapy Google a rozhraní API map Bing pomocí WGS 84. Azure Cosmos DB podporuje indexování a dotazování dat geoprostorové rezervační WGS 84 systém pouze. 

## <a name="creating-documents-with-spatial-data"></a>Vytváření dokumentů s prostorovými daty
Když vytvoříte dokumenty, které obsahují GeoJSON hodnoty, budou se automaticky indexované prostorový index podle zásady indexování kolekce. Pokud pracujete se sadou Azure SDK DB Cosmos v jazyce dynamicky zadávaných jako Pythonu nebo Node.js, musíte vytvořit platný GeoJSON.

**Vytvořte dokument s daty geoprostorové v Node.js**

```json
var userProfileDocument = {
    "name":"cosmosdb",
    "location":{
        "type":"Point",
        "coordinates":[ -122.12, 47.66 ]
    }
};

client.createDocument(`dbs/${databaseName}/colls/${collectionName}`, userProfileDocument, (err, created) => {
    // additional code within the callback
});
```

Při práci s rozhraními API SQL, můžete použít `Point` a `Polygon` tříd v rámci `Microsoft.Azure.Documents.Spatial` obor názvů pro vložení informace o umístění v rámci vašich objektů aplikace. Tyto třídy zjednodušit serializace a deserializace prostorových dat do GeoJSON.

**Vytvořte dokument s daty geoprostorové v rozhraní .NET**

```json
using Microsoft.Azure.Documents.Spatial;

public class UserProfile
{
    [JsonProperty("name")]
    public string Name { get; set; }

    [JsonProperty("location")]
    public Point Location { get; set; }

    // More properties
}

await client.CreateDocumentAsync(
    UriFactory.CreateDocumentCollectionUri("db", "profiles"), 
    new UserProfile 
    { 
        Name = "cosmosdb", 
        Location = new Point (-122.12, 47.66) 
    });
```

Pokud nemáte informace o zeměpisné šířky a délky, ale název umístění jako města nebo země nebo fyzické adresy, můžete vyhledat skutečné souřadnice pomocí služeb určování zeměpisných souřadnic jako služby REST Bing Maps. Další informace o určování zeměpisných souřadnic mapy Bing [zde](https://msdn.microsoft.com/library/ff701713.aspx).

## <a name="querying-spatial-types"></a>Dotazování prostorové typy
Teď, když bylo podívejte se na tom, jak vkládání dat geoprostorové, Podívejme se na postup dotazování tato data pomocí Azure DB Cosmos pomocí SQL a LINQ.

### <a name="spatial-sql-built-in-functions"></a>Prostorové integrované funkce SQL
Azure Cosmos DB podporuje následující předdefinované funkce otevřete geoprostorové Consortium (OGC) pro geoprostorové dotazování. Další podrobnosti o kompletní sadu integrovaných funkcí v jazyce SQL najdete v tématu [dotazu Azure Cosmos DB](sql-api-sql-query.md).

<table>
<tr>
  <td><strong>Využití</strong></td>
  <td><strong>Popis</strong></td>
</tr>
<tr>
  <td>ST_DISTANCE (spatial_expr, spatial_expr)</td>
  <td>Vrací vzdálenost mezi dvěma GeoJSON bodu, mnohoúhelníku nebo LineString výrazy.</td>
</tr>
<tr>
  <td>ST_WITHIN (spatial_expr, spatial_expr)</td>
  <td>Vrací výraz logická hodnota určující, zda je první objekt GeoJSON (bod, mnohoúhelníku nebo LineString) je v rámci druhý objekt GeoJSON (bod, mnohoúhelníku nebo LineString).</td>
</tr>
<tr>
  <td>ST_INTERSECTS (spatial_expr, spatial_expr)</td>
  <td>Vrátí hodnotu označující, zda dva zadané GeoJSON objekty (bod, mnohoúhelníku nebo LineString) intersect logický výraz.</td>
</tr>
<tr>
  <td>ST_ISVALID</td>
  <td>Vrátí logickou hodnotu udávající, zda je zadaný výraz GeoJSON bodu, mnohoúhelníku nebo LineString platný.</td>
</tr>
<tr>
  <td>ST_ISVALIDDETAILED</td>
  <td>Vrátí hodnotu hodnotu JSON obsahující logickou hodnotu, pokud zadaný výraz GeoJSON bodu, mnohoúhelníku nebo LineString je platný a pokud neplatný, dále z důvodu jako hodnotu řetězce.</td>
</tr>
</table>

Prostorové funkcí lze provádět dotazy blízkosti proti prostorová data. Tady je příklad dotaz, který vrátí všechny rodiny dokumenty, které jsou v rámci 30 km v zadaném umístění pomocí předdefinované funkci ST_DISTANCE. 

**Dotaz**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**Results**

    [{
      "id": "WakefieldFamily"
    }]

Pokud zahrnete prostorových indexování v zásady indexování, pak "vzdálenost dotazy" se zpracuje efektivně prostřednictvím index. Další informace o prostorových indexování najdete v části níže. Pokud nemáte prostorový index pro zadané cesty, stále můžete provádět prostorových dotazů zadáním `x-ms-documentdb-query-enable-scan` hlavička požadavku s nastavenou hodnotu "true". V rozhraní .NET, to lze provést pomocí předání nepovinný **FeedOptions** argument pro dotazy s [EnableScanInQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.enablescaninquery.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.EnableScanInQuery) nastaven na hodnotu true. 

ST_WITHIN slouží ke kontrole, pokud bod leží uvnitř mnohoúhelníku. Mnohoúhelníky se běžně používají k vyjádření hranice jako PSČ, hranice stavu nebo přírodní vytváření. Znovu Pokud zahrnete prostorových indexování v zásady indexování, pak "v" dotazy se zpracuje efektivně prostřednictvím index. 

Argumenty mnohoúhelníku v ST_WITHIN může obsahovat pouze jedno zazvonění, tedy polygonů nesmí obsahovat mezery v nich. 

**Dotaz**

    SELECT * 
    FROM Families f 
    WHERE ST_WITHIN(f.location, {
        'type':'Polygon', 
        'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })

**Results**

    [{
      "id": "WakefieldFamily",
    }]

> [!NOTE]
> Podobná jak neodpovídající pracovní typy v Azure Cosmos DB dotazu, pokud hodnota umístění v zadána buď argument je chybný nebo není platný, pak je vyhodnocen jako **nedefinované** a vyhodnotí dokumentu, který má být přeskočeno z výsledků dotazu. Pokud dotaz vrátí žádné výsledky, spusťte ST_ISVALIDDETAILED k ladění proč prostorový typ je neplatný.     
> 
> 

Azure Cosmos DB také podporuje provádění inverzní dotazy, tedy můžete indexu mnohoúhelníky nebo řádků v databázi Cosmos Azure a pak dotazu pro oblasti, které obsahují zadaný bod. Tento vzor se obvykle používá v logistiky například identifikovat, kdy vůz zadá nebo opustí určené oblasti. 

**Dotaz**

    SELECT * 
    FROM Areas a 
    WHERE ST_WITHIN({'type': 'Point', 'coordinates':[31.9, -4.8]}, a.location)


**Results**

    [{
      "id": "MyDesignatedLocation",
      "location": {
        "type":"Polygon", 
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
      }
    }]

ST_ISVALID a ST_ISVALIDDETAILED slouží ke kontrole, jestli je objekt prostorových platný. Například následující dotaz kontroluje platnost bod s out hodnoty zeměpisné šířky rozsah (-132.8). ST_ISVALID vrací pouze logickou hodnotu, a ST_ISVALIDDETAILED vrátí řetězec obsahující důvod, proč má se za neplatný a logická hodnota.

** Dotaz **

    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })

**Results**

    [{
      "$1": false
    }]

Tyto funkce lze také ověřit mnohoúhelníky. Například tady používáme ST_ISVALIDDETAILED ověření mnohoúhelníku, který není uzavřený. 

**Dotaz**

    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})

**Results**

    [{
       "$1": { 
            "valid": false, 
            "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a Polygon must have the same start and end points." 
          }
    }]

### <a name="linq-querying-in-the-net-sdk"></a>LINQ dotazování v .NET SDK
.NET SDK služby SQL také poskytovatelé zóny se zakázaným inzerováním metody `Distance()` a `Within()` pro použití v rámci LINQ – výrazy. Zprostředkovatel SQL LINQ překládá těchto volání metod na ekvivalentní integrovaná funkce volání SQL (ST_DISTANCE a ST_WITHIN v uvedeném pořadí). 

Tady je příklad dotazu LINQ, který najde všechny dokumenty v kolekci Azure Cosmos DB, jehož hodnota "umístění" je v rámci okruhu 30 km zadaného bodu pomocí LINQ.

**Dotaz LINQ pro vzdálenost**

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(UriFactory.CreateDocumentCollectionUri("db", "profiles"))
        .Where(u => u.ProfileType == "Public" && a.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }

Podobně je zde dotazu pro vyhledání všech dokumentů, jejichž "umístění" je v rámci zadaného pole nebo mnohoúhelníku. 

**LINQ dotazu v rámci**

    Polygon rectangularArea = new Polygon(
        new[]
        {
            new LinearRing(new [] {
                new Position(31.8, -5),
                new Position(32, -5),
                new Position(32, -4.7),
                new Position(31.8, -4.7),
                new Position(31.8, -5)
            })
        });

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(UriFactory.CreateDocumentCollectionUri("db", "profiles"))
        .Where(a => a.Location.Within(rectangularArea)))
    {
        Console.WriteLine("\t" + user);
    }


Teď, když bylo podívejte se na postup dotazování dokumentů pomocí LINQ a SQL, Podívejme se na tom, jak nakonfigurovat databázi Cosmos Azure pro prostorových indexování.

## <a name="indexing"></a>Indexování
Jsme popsané v [schématu bez ohledu na indexování s Azure Cosmos DB](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) dokumentu, jsme určený databázový stroj databázi Azure Cosmos jako skutečně nezávislá na schéma a poskytovat prvotřídní podporu pro formát JSON. Databázový stroj zápisu optimalizované Azure Cosmos databáze nativně rozumí prostorových dat (body, mnohoúhelníků a čar), které jsou v GeoJSON standard.

Stručně řečeno, je geometrie projektovat z geodetické souřadnice na 2D rovinu pak progresivně rozdělené do buňky pomocí **quadtree**. Tyto buněk jsou namapované na 1D na základě umístění buňky v rámci **Hilbertův místo naplnění křivky**, který zachovává polohu bodů. Dále pokud je indexovaný data o umístění, prochází skrz tento proces se označuje jako **teselace**, to znamená, že se všechny buněk, které intersect umístění identifikovat a uložené jako klíče v Azure Cosmos DB indexu. V době dotazů argumenty jako body a mnohoúhelníky jsou také teselace sestavy k extrakci oblasti relevantní buněk ID a potom použít k načtení dat z indexu.

Pokud zadáte zásady indexování, zahrnující prostorový index pro / * (všechny cesty), pak všechny body nalezené v rámci kolekce jsou indexované pro efektivní prostorových dotazů (ST_WITHIN a ST_DISTANCE). Prostorové indexy nemáte hodnotu přesnost a vždy použít výchozí hodnotu přesnost.

> [!NOTE]
> Azure Cosmos DB podporuje automatické indexování bodů, mnohoúhelníky a LineStrings
> 
> 

Následující fragment kódu JSON zobrazí zásady indexování s prostorových indexování, který je povolen, kdykoli GeoJSON v rámci dokumenty nalezen prostorových dotazování indexu. Chcete-li změnit zásady indexování pomocí portálu Azure, můžete zadat následující JSON pro zásady indexování povolit prostorových indexování do kolekce.

**Kolekce JSON zásady indexování s Spatial povoleno pro body a mnohoúhelníky**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "includedPaths":[
          {
             "path":"/*",
             "indexes":[
                {
                   "kind":"Range",
                   "dataType":"String",
                   "precision":-1
                },
                {
                   "kind":"Range",
                   "dataType":"Number",
                   "precision":-1
                },
                {
                   "kind":"Spatial",
                   "dataType":"Point"
                },
                {
                   "kind":"Spatial",
                   "dataType":"Polygon"
                }                
             ]
          }
       ],
       "excludedPaths":[
       ]
    }

Zde je fragment kódu v rozhraní .NET, který ukazuje, jak můžete vytvořit kolekci s prostorových indexování zapnuta pro všechny cesty obsahující body. 

**Vytvořte kolekci s prostorových indexování**

    DocumentCollection spatialData = new DocumentCollection()
    spatialData.IndexingPolicy = new IndexingPolicy(new SpatialIndex(DataType.Point)); //override to turn spatial on by default
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), spatialData);

A tady je, jak můžete upravit existující kolekci využít prostorových indexování přes všechny body, které jsou uloženy v rámci dokumenty.

**Upravit existující kolekci s prostorových indexování**

    Console.WriteLine("Updating collection with spatial indexing enabled in indexing policy...");
    collection.IndexingPolicy = new IndexingPolicy(new SpatialIndex(DataType.Point));
    await client.ReplaceDocumentCollectionAsync(collection);

    Console.WriteLine("Waiting for indexing to complete...");
    long indexTransformationProgress = 0;
    while (indexTransformationProgress < 100)
    {
        ResourceResponse<DocumentCollection> response = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
        indexTransformationProgress = response.IndexTransformationProgress;

        await Task.Delay(TimeSpan.FromSeconds(1));
    }

> [!NOTE]
> Pokud umístění GeoJSON hodnotu v tomto dokumentu je chybný nebo není platný, pak jej nebude získat indexovaný prostorových dotazování. Můžete ověřit pomocí ST_ISVALID a ST_ISVALIDDETAILED hodnoty umístění.
> 
> Pokud svou definici. kolekce obsahuje klíč oddílu, není hlášena indexování průběh transformace. 
> 
> 

## <a name="next-steps"></a>Další postup
Nolearned, které jste dozvědí o tom, jak začít pracovat s geoprostorové podpory v Azure Cosmos DB, můžete postupovat následovně:

* Psaní s [ukázky kódu .NET geoprostorové na Githubu](https://github.com/Azure/azure-documentdb-dotnet/blob/fcf23d134fc5019397dcf7ab97d8d6456cd94820/samples/code-samples/Geospatial/Program.cs)
* Získat rukou na s geoprostorové dotazování na [Azure Cosmos DB Query Playground](http://www.documentdb.com/sql/demo#geospatial)
* Další informace o [Azure Cosmos DB dotazu](sql-api-sql-query.md)
* Další informace o [Azure Cosmos DB indexování zásady](indexing-policies.md)

