---
title: Práce s Geoprostorová data v účtu rozhraní SQL API služby Azure Cosmos DB
description: Naučte se vytvářet, indexování a dotazovat Prostorové objekty s Azure Cosmos DB a rozhraní SQL API.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/01/2017
ms.author: sngun
ms.openlocfilehash: 5f096d016b2fa82e3b340a4a6b6c7e1fd6420216
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54037187"
---
# <a name="use-geospatial-and-geojson-location-data-with-azure-cosmos-db-sql-api-account"></a>Použití geoprostorových a GeoJSON umístění dat pomocí účtu rozhraní SQL API služby Azure Cosmos DB

Tento článek je úvodem do geoprostorové funkce ve službě Azure Cosmos DB. Aktuálně účely ukládání a zpřístupnění geoprostorových dat podporuje pouze účty SQL API služby Cosmos DB. Po přečtení tohoto článku, budou moci odpovědět na následující otázky:

* Jak můžu ukládat prostorová data ve službě Azure Cosmos DB?
* Jak lze Geoprostorová data ve službě Azure Cosmos DB v SQL a LINQ dotaz?
* Jak povolit nebo zakázat prostorového indexování ve službě Azure Cosmos DB?

Tento článek popisuje, jak pracovat s prostorovými daty pomocí rozhraní SQL API. Najdete v tomto [projektu z Githubu](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Geospatial/Program.cs) ukázky kódu.

## <a name="introduction-to-spatial-data"></a>Úvod do prostorových dat
Prostorová data popisuje pozice a tvar objektů v prostoru. Ve většině aplikací tyto odpovídá objektům na světě a Geoprostorová data. Prostorová data slouží k reprezentaci umístění osoby, místa, které vás zajímají nebo hranici město nebo jezera. Běžné případy použití často zahrnují blízkých výrazů dotazů, třeba, "najít všechny v kavárnách téměř moji aktuální polohu." 

### <a name="geojson"></a>GeoJSON
Azure Cosmos DB podporuje indexování a dotazování na data bodu geoprostorové, která je reprezentována pomocí [specifikaci GeoJSON](https://tools.ietf.org/html/rfc7946). GeoJSON datové struktury jsou vždycky platné objekty JSON, tak můžete ukládat a dotazovat pomocí služby Azure Cosmos DB bez jakékoli specializované nástroje a knihovny. Azure Cosmos DB SDK poskytuje pomocné třídy a metody, které usnadňují práci s prostorovými daty formátu. 

### <a name="points-linestrings-and-polygons"></a>Body, LineStrings a mnohoúhelníky
A **bodu** označuje jedna pozice v prostoru. V Geoprostorová data představuje bod přesné umístění, které by mohly být adresu blízkým úložiště, jako veřejný terminál, automobilu nebo město.  Bod je vyjádřena v páru GeoJSON (a Azure Cosmos DB) pomocí jeho souřadnice nebo zeměpisné šířky a délky. Tady je příklad JSON pro bod.

**Body ve službě Azure Cosmos DB**

```json
{
    "type":"Point",
    "coordinates":[ 31.9, -4.8 ]
}
```

> [!NOTE]
> Specifikaci GeoJSON určuje zeměpisnou délku prvním šířku a délku druhé. Stejně jako v ostatních aplikacích mapování zeměpisné šířky a délky se jedná o úhly a reprezentovat z hlediska stupňů. Zeměpisná délka hodnoty se měří z poledníku, který a jsou v rozmezí od-180 stupňů a 180.0 stupňů, a hodnoty zeměpisné šířky se měří od rovníku spadají do rozsahu-90.0 stupňů a 90.0 stupňů. 
> 
> Azure Cosmos DB interpretuje souřadnice reprezentovaný za WGS 84 referenčního systému. Níže naleznete další podrobnosti o souřadnic referenčních systémů.
> 
> 

To může být vložen do dokumentu služby Azure Cosmos DB, jak je znázorněno v tomto příkladu profilu uživatele, který obsahuje data o poloze:

**Použít profil s umístěním uložené ve službě Azure Cosmos DB**

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

Kromě body také podporuje GeoJSON LineStrings a mnohoúhelníku. **LineStrings** reprezentaci řady dva nebo víc bodů v prostoru a segmenty čáry, které je propojují. V Geoprostorová data jsou LineStrings běžně používaných ke znázornění dálnice nebo řek. A **mnohoúhelníku** je hranice připojené body, která tvoří uzavřené LineString. Mnohoúhelníky se běžně používá k reprezentování fyzických struktur jako jezera nebo politickou jurisdikce jako měst a států. Tady je příklad mnohoúhelníku ve službě Azure Cosmos DB. 

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
> Specifikaci GeoJSON vyžaduje, že pro platné mnohoúhelníky poslední souřadnic pár poskytuje by měl být stejný jako první, chcete-li vytvořit zavřeného tvaru.
> 
> Body v rámci mnohoúhelníku musí zadat v pořadí proti směru hodinových ručiček. Mnohoúhelník zadat v pořadí po směru hodinových ručiček představuje inverzní oblasti v rámci něj.
> 
> 

Kromě Point, LineString a mnohoúhelník GeoJSON také určuje vyjádření způsob seskupení více míst geoprostorové, jakož i jak přidružit informace o zeměpisné poloze jako libovolné vlastnosti **funkce**. Protože tyto objekty jsou platný kód JSON, se mohou všechny ukládat a zpracovávat ve službě Azure Cosmos DB. Ale služby Azure Cosmos DB podporuje pouze automatického indexování body.

### <a name="coordinate-reference-systems"></a>Souřadnice referenčních systémů
Protože je nestandardní tvar všech koutech světa, souřadnice Geoprostorová data jsou reprezentovány v řadě systémů souřadnic odkaz (CRS), každá má své vlastní referenčním snímků a měrné jednotky. Například "National mřížky z Británie" je referenční systém je přesné pro Spojené království, ale ne mimo něj. 

Nejoblíbenější CRS používá dnes je systém geodetické World [WGS 84](http://earth-info.nga.mil/GandG/wgs84/). GPS zařízení a mnoho služeb mapování, včetně mapy Google a rozhraní API map Bing použijte WGS 84. Azure Cosmos DB podporuje indexování a dotazování prostřednictvím CRS WGS 84 pouze. 

## <a name="creating-documents-with-spatial-data"></a>Vytváření dokumentů s prostorovými daty formátu
Při vytváření dokumentů, které obsahují hodnoty GeoJSON se automaticky indexují se prostorový index podle zásady indexování kontejneru. Pokud pracujete s využitím Azure Cosmos DB SDK v dynamicky psaný jazyk, jako je Python nebo Node.js, je nutné vytvořit platný GeoJSON.

**Vytvoření dokumentu pomocí Geoprostorová data v Node.js**

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

Pokud pracujete s rozhraním SQL API, můžete použít `Point` a `Polygon` třídy v rámci `Microsoft.Azure.Documents.Spatial` obor názvů vložit informace o umístění v rámci vaší aplikace objekty. Tyto třídy pomáhají zjednodušit serializace a deserializace prostorových dat do GeoJSON.

**Vytvoření dokumentu pomocí Geoprostorová data v .NET**

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

Pokud není k dispozici informace o zeměpisné šířce a délce, ale název umístění jako město nebo okres nebo fyzické adresy, můžete vyhledat skutečné souřadnice pomocí geokódování službě, jako je REST služby Bing Maps. Další informace o geografické kódování služby mapy Bing [tady](https://msdn.microsoft.com/library/ff701713.aspx).

## <a name="querying-spatial-types"></a>Prostorové typy dotazování
Teď, když jsme měli podívat, jak vložit Geoprostorová data, Pojďme se podívat, jak zadávat dotazy na tato data pomocí služby Azure Cosmos DB pomocí jazyka SQL a LINQ.

### <a name="spatial-sql-built-in-functions"></a>Prostorové integrované funkce SQL
Azure Cosmos DB podporuje následující předdefinované funkce Otevřít geoprostorové W3c (OGC) pro geoprostorové dotazování. Další informace o kompletní sadu integrovaných funkcí v jazyce SQL najdete v tématu [dotazů Azure Cosmos DB](how-to-sql-query.md).

<table>
<tr>
  <td><strong>Použití</strong></td>
  <td><strong>Popis</strong></td>
</tr>
<tr>
  <td>ST_DISTANCE (spatial_expr, spatial_expr)</td>
  <td>Vrací vzdálenost mezi dvěma GeoJSON bodu mnohoúhelníku či LineString výrazy.</td>
</tr>
<tr>
  <td>ST_WITHIN (spatial_expr, spatial_expr)</td>
  <td>Vrací výraz Boolean určující, zda je první objekt GeoJSON (bodu, mnohoúhelník nebo LineString) v rámci druhého objektu GeoJSON (bodu, mnohoúhelník nebo LineString).</td>
</tr>
<tr>
  <td>ST_INTERSECTS (spatial_expr, spatial_expr)</td>
  <td>Vrátí hodnotu určující, zda dvě zadané GeoJSON objekty (bodu, mnohoúhelník nebo LineString) intersect logický výraz.</td>
</tr>
<tr>
  <td>ST_ISVALID</td>
  <td>Vrátí logickou hodnotu označující, zda je zadaný výraz GeoJSON bodu mnohoúhelníku či LineString platný.</td>
</tr>
<tr>
  <td>ST_ISVALIDDETAILED</td>
  <td>Vrátí hodnotu JSON obsahující logická hodnota, pokud zadaný výraz GeoJSON bodu mnohoúhelníku či LineString je platný a pokud není platný, kromě důvod jako hodnotu řetězce.</td>
</tr>
</table>

Prostorové funkce lze použít k provádění dotazů blízkosti prostorová data. Například tady je dotaz, který vrátí všechny rodiny dokumenty, které jsou v rámci 30 km pomocí integrované funkce ST_DISTANCE zadaného umístění. 

**Dotaz**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**Results**

    [{
      "id": "WakefieldFamily"
    }]

Pokud zahrnete prostorového indexování v zásady indexování, pak "vzdálenost dotazy" bude obsluhovat efektivně pomocí indexu. Další informace o prostorového indexování najdete v následující části. Pokud nemáte prostorový index pro zadané cesty, může stále zlepšovat prostorových dotazů tak, že zadáte `x-ms-documentdb-query-enable-scan` hlavičce žádosti s nastavenou hodnotu "true". V rozhraní .NET, to můžete udělat tak volitelného **FeedOptions** argument dotazů s [EnableScanInQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.enablescaninquery.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.EnableScanInQuery) nastavenou na hodnotu true. 

ST_WITHIN slouží ke kontrole, pokud bod leží uvnitř mnohoúhelníku. Mnohoúhelníky se běžně používá k reprezentování hranice jako PSČ, hranice stavu nebo fyzických struktur. Znovu zadáte-li prostorového indexování v zásady indexování, pak "v" dotazy bude obsluhovat efektivně pomocí indexu. 

Argumenty mnohoúhelníku ve ST_WITHIN může obsahovat jenom jeden okruh, to znamená, mnohoúhelníky nesmí obsahovat otvory v nich. 

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
> Podobně jako jak neodpovídající typy práce ve službě Azure Cosmos DB dotazu, pokud je hodnota umístění zadaná v buď argumentu je chybný nebo není platný, pak je vyhodnocen jako **nedefinované** a vyhodnocené dokumentu přeskočit z výsledků dotazu. Pokud váš dotaz nebyly vráceny žádné výsledky, ST_ISVALIDDETAILED pro spuštění ladění důvod, proč prostorových typ je neplatný.     
> 
> 

Azure Cosmos DB podporuje také provádí inverzní dotazy, to znamená, můžete indexování mnohoúhelníky nebo řádky ve službě Azure Cosmos DB a pak dotazování v oblastech, které obsahují zadaný bod. Tento model se běžně používá v logistiky k identifikaci, třeba při nákladní vozidlo zadá nebo ji opustí určená oblast. 

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

ST_ISVALID a ST_ISVALIDDETAILED umožňuje zkontrolujte, jestli je platný prostorových objektů. Například následující dotaz ověří platnost bod mimo rozsah hodnoty zeměpisné šířky (-132.8). ST_ISVALID vrátí jenom logickou hodnotu a ST_ISVALIDDETAILED vrátí logickou hodnotu a řetězec obsahující důvod, proč bude považován za neplatný.

** Dotazování **

    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })

**Results**

    [{
      "$1": false
    }]

Tyto funkce lze také ověřit mnohoúhelníku. Například tady používáme ST_ISVALIDDETAILED ověření mnohoúhelníku, která není uzavřená. 

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

### <a name="linq-querying-in-the-net-sdk"></a>LINQ dotazování v sadě .NET SDK
SQL SDK pro .NET také poskytovatelé zástupných procedur metody `Distance()` a `Within()` pro použití v rámci LINQ – výrazy. Tato metoda převádí zprostředkovatele SQL LINQ volání ekvivalentní předdefinované funkce volání SQL (ST_DISTANCE a ST_WITHIN v uvedeném pořadí). 

Tady je příklad dotazu LINQ, který najde všechny dokumenty v kolekci Azure Cosmos DB, jehož hodnota "umístění" je v rámci okruhu 30 km zadaného bodu pomocí jazyka LINQ.

**Dotaz LINQ vzdálenosti**

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(UriFactory.CreateDocumentCollectionUri("db", "profiles"))
        .Where(u => u.ProfileType == "Public" && a.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }

Podobně tady je dotaz pro vyhledání všech dokumentů, jejichž "umístění" je v rámci zadaného pole/mnohoúhelníku. 

**Dotazy LINQ pro v rámci**

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


Teď, když jsme měli podívat, jak zadávat dotazy na dokumenty pomocí LINQ a SQL, Pojďme se podívat, jak nakonfigurovat službu Azure Cosmos DB pro prostorového indexování.

## <a name="indexing"></a>Indexování
Jak jsme je popsáno v [schématu nezávislé indexování pomocí služby Azure Cosmos DB](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) papíru, jsme navrhovali databázový stroj služby Azure Cosmos DB bude skutečně nezávislá na schématu a poskytovat prvotřídní podporu pro formát JSON. Databázový stroj optimalizovaný pro zápis služby Azure Cosmos DB nativně rozumí prostorovým datům (body, mnohoúhelníků a čar), které jsou reprezentovány ve standardu GeoJSON.

Řečeno v kostce, je geometrii plánovaných z geodetické souřadnice na plochu 2D pak postupně rozdělit do buňky **quadtree**. Tyto buňky jsou mapovány na 1D, v závislosti na umístění v rámci buňky **Hilbertův místo naplnění křivky**, která zachovává lokality bodů. Kromě toho při indexování umístění dat prochází přes tento proces se označuje jako **teselace**, to znamená všechny buňky, které intersect umístění identifikovaná a uloženy jako klíče v indexu služby Azure Cosmos DB. V době zpracování dotazu argumenty, jako je bodů a mnohoúhelníků jsou také teselace sestavy extrahovat ID oblasti relevantní buněk a potom se používá k načtení dat z indexu.

Pokud zadáte zásady indexování, který obsahuje prostorový index pro / * (všechny cesty), pak všechny body nalezené v rámci kolekce jsou indexována pro efektivní prostorových dotazů (ST_WITHIN a ST_DISTANCE). Prostorové indexy nemáte hodnota přesnosti a vždy použijte výchozí hodnotu přesnosti.

> [!NOTE]
> Azure Cosmos DB podporuje automatické indexování LineStrings, bodů a mnohoúhelníků
> 
> 

Následující fragment kódu JSON ukazuje zásady indexování s prostorového indexování povoleno, to znamená, čárky GeoJSON najdete v dokumentech pro dotazování na prostorový index. Pokud chcete upravit zásady indexování pomocí webu Azure portal, můžete zadat následující kód JSON pro zásady indexování Povolit prostorové indexování do kolekce.

**Kolekce JSON zásady indexování s Spatial povolena pro bodů a mnohoúhelníků**

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

Zde je fragment kódu v .NET, která ukazuje, jak vytvořit kolekci s zapnuté pro všechny cesty obsahující body prostorového indexování. 

**Vytvořte kolekci s prostorového indexování**

    DocumentCollection spatialData = new DocumentCollection()
    spatialData.IndexingPolicy = new IndexingPolicy(new SpatialIndex(DataType.Point)); //override to turn spatial on by default
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), spatialData);

A tady je, jak můžete upravit existující kolekci výhod prostorového indexování přes všechny body, které jsou uloženy v dokumentech.

**Upravit existující kolekci s prostorového indexování**

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
> Pokud umístění hodnota GeoJSON v rámci dokumentu je chybný nebo není platný, pak ji nebude indexování pro prostorová dotazování. Můžete ověřit pomocí ST_ISVALID a ST_ISVALIDDETAILED hodnoty umístění.
> 
> Pokud vaše definice kolekce obsahuje klíč oddílu, není hlášena ve indexování průběh transformace. 
> 
> 

## <a name="next-steps"></a>Další postup
Teď, když jste se naučili, jak začít pracovat s podporuje geoprostorové funkce ve službě Azure Cosmos DB, dále můžete:

* Pusťte se do programování se [geoprostorové .NET ukázky kódu na Githubu](https://github.com/Azure/azure-documentdb-dotnet/blob/fcf23d134fc5019397dcf7ab97d8d6456cd94820/samples/code-samples/Geospatial/Program.cs)
* Vyzkoušejte v praxi s geoprostorové dotazování na [Azure Cosmos DB Query Playground](https://www.documentdb.com/sql/demo#geospatial)
* Další informace o [dotazu služby Azure Cosmos DB](how-to-sql-query.md)
* Další informace o [zásady indexování Azure Cosmos DB](index-policy.md)

