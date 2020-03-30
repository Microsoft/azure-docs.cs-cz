---
title: Správa zásad indexování ve službě Azure Cosmos DB
description: Zjistěte, jak spravovat zásady indexování, zahrnout nebo vyloučit vlastnost z indexování, jak definovat indexování pomocí různých sad Azure Cosmos DB SDK
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: thweiss
ms.openlocfilehash: 58a1ee13afa76b152723cb71d4037f9c31cc8d4e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252074"
---
# <a name="manage-indexing-policies-in-azure-cosmos-db"></a>Správa zásad indexování ve službě Azure Cosmos DB

V Azure Cosmos DB data se indexují podle [zásad indexování,](index-policy.md) které jsou definovány pro každý kontejner. Výchozí zásady indexování pro nově vytvořené kontejnery u všech řetězců a čísel vynucují indexy rozsahu. Tyto zásady je možné přepsat vlastními zásadami indexování.

## <a name="indexing-policy-examples"></a>Příklady zásad indexování

Tady jsou některé příklady zásad indexování zobrazených v [jejich formátu JSON](index-policy.md#include-exclude-paths), což je způsob, jakým jsou vystaveny na portálu Azure. Stejné parametry lze nastavit prostřednictvím rozhraní příkazového řádku Azure nebo libovolné sady SDK.

### <a name="opt-out-policy-to-selectively-exclude-some-property-paths"></a>Zásady výslovných účastí pro selektivní vyloučení některých cest vlastností

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*"
            }
        ],
        "excludedPaths": [
            {
                "path": "/path/to/single/excluded/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/excluded/properties/*"
            }
        ]
    }
```

Tato zásada indexování je ekvivalentní zásadě, ```kind``` ```dataType```pod ```precision``` kterou je ručně nastavena , a jejich výchozím hodnotám. Tyto vlastnosti již nejsou nutné explicitně nastavit a můžete vynechat je z zásad indexování úplně (jak je znázorněno výše uvedeného příkladu).

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number",
                        "precision": -1
                    },
                    {
                        "kind": "Range",
                        "dataType": "String",
                        "precision": -1
                    }
                ]
            }
        ],
        "excludedPaths": [
            {
                "path": "/path/to/single/excluded/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/excluded/properties/*"
            }
        ]
    }
```

### <a name="opt-in-policy-to-selectively-include-some-property-paths"></a>Zásady přihlášení k selektivnímu zahrnutí některých cest vlastností

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/path/to/included/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/included/properties/*"
            }
        ],
        "excludedPaths": [
            {
                "path": "/*"
            }
        ]
    }
```

Tato zásada indexování je ekvivalentní zásadě, ```kind``` ```dataType```pod ```precision``` kterou je ručně nastavena , a jejich výchozím hodnotám. Tyto vlastnosti již nejsou nutné explicitně nastavit a můžete vynechat je z zásad indexování úplně (jak je znázorněno výše uvedeného příkladu).

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/path/to/included/property/?",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    },
                    {
                        "kind": "Range",
                        "dataType": "String"
                    }
                ]
            },
            {
                "path": "/path/to/root/of/multiple/included/properties/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    },
                    {
                        "kind": "Range",
                        "dataType": "String"
                    }
                ]
            }
        ],
        "excludedPaths": [
            {
                "path": "/*"
            }
        ]
    }
```

> [!NOTE] 
> Obecně se doporučuje použít zásady **opt-out** indexování, aby Azure Cosmos DB proaktivně indexovat všechny nové vlastnosti, které mohou být přidány do vašeho modelu.

### <a name="using-a-spatial-index-on-a-specific-property-path-only"></a>Použití prostorového indexu pouze na určité cestě vlastnosti

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/_etag/?"
        }
    ],
    "spatialIndexes": [
        {
            "path": "/path/to/geojson/property/?",
            "types": [
                "Point",
                "Polygon",
                "MultiPolygon",
                "LineString"
            ]
        }
    ]
}
```

## <a name="composite-indexing-policy-examples"></a>Příklady zásad kompozitního indexování

Kromě zahrnutí nebo vyloučení cest pro jednotlivé vlastnosti můžete také zadat složený index. Pokud chcete provést dotaz, který `ORDER BY` má klauzuli pro více vlastností, složený [index](index-policy.md#composite-indexes) těchto vlastností je vyžadován. Kromě toho složené indexy budou mít výhodu výkonu pro dotazy, které mají filtr a mají klauzuli ORDER BY na různé vlastnosti.

### <a name="composite-index-defined-for-name-asc-age-desc"></a>Složený index definovaný pro (název asc, věk desc):

```json
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"descending"
                }
            ]
        ]
    }
```

Výše uvedený složený index názvu a stáří je vyžadován pro #1 #2 a dotazu:

#1 dotazu:

```sql
    SELECT *
    FROM c
    ORDER BY c.name ASC, c.age DESC
```

#2 dotazu:

```sql
    SELECT *
    FROM c
    ORDER BY c.name DESC, c.age ASC
```

Tento složený index bude přínosem pro #3 dotazu a #4 dotazu a optimalizovat filtry:

dotaz#3:

```sql
SELECT *
FROM c
WHERE c.name = "Tim"
ORDER BY c.name DESC, c.age ASC
```

Dotaz #4:

```sql
SELECT *
FROM c
WHERE c.name = "Tim" AND c.age > 18
```

### <a name="composite-index-defined-for-name-asc-age-asc-and-name-asc-age-desc"></a>Složený index definovaný pro (název ASC, věk ASC) a (název ASC, věk DESC):

Můžete definovat více různých složených indexů v rámci stejné zásady indexování.

```json
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"ascending"
                }
            ],
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"descending"
                }
            ]
        ]
    }
```

### <a name="composite-index-defined-for-name-asc-age-asc"></a>Složený index definovaný pro (název ASC, věk ASC):

Je volitelné zadat pořadí. Pokud není zadán, pořadí je vzestupně.

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                },
                {  
                    "path":"/age",
                }
            ]
        ]
}
```

### <a name="excluding-all-property-paths-but-keeping-indexing-active"></a>Vyloučení všech cest vlastností, ale zachování aktivního indexování

Tuto zásadu lze použít v situacích, kdy je aktivní [funkce Time-to-Live (TTL),](time-to-live.md) ale není vyžadován žádný sekundární index (k použití Azure Cosmos DB jako úložiště čisté hodnoty klíče).

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [],
        "excludedPaths": [{
            "path": "/*"
        }]
    }
```

### <a name="no-indexing"></a>Bez indexování

Tato zásada vypne indexování. Pokud `indexingMode` je `none`nastavena na , nelze nastavit TTL na kontejneru.

```json
    {
        "indexingMode": "none"
    }
```

## <a name="updating-indexing-policy"></a>Aktualizace zásad indexování

V Azure Cosmos DB lze zásady indexování aktualizovat pomocí některé z následujících metod:

- z portálu Azure
- pomocí příkazového příkazového příkazu Azure
- použití Prostředí PowerShell
- pomocí jedné z sad SDK

[Aktualizace zásad indexování](index-policy.md#modifying-the-indexing-policy) aktivuje transformaci indexu. Průběh této transformace lze také sledovat z sad SDK.

> [!NOTE]
> Při aktualizaci zásad indexování bude zápisy do Azure Cosmos DB nepřerušené. Během přeindexování dotazy mohou vrátit částečné výsledky jako index je aktualizován.

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

Kontejnery Azure Cosmos ukládají své zásady indexování jako dokument JSON, který vám portál Azure umožňuje přímo upravovat.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

1. Vytvořte si nový účet Azure Cosmos nebo vyberte existující účet.

1. Otevřete podokno **Průzkumník dat** a vyberte kontejner, na kterém chcete pracovat.

1. Klikněte na **Měřítko & Nastavení**.

1. Upravte indexovací zásady dokumentu JSON (viz příklady [níže)](#indexing-policy-examples)

1. Po dokončení klikněte na **Uložit**.

![Správa indexování pomocí portálu Azure](./media/how-to-manage-indexing-policy/indexing-policy-portal.png)

## <a name="use-the-azure-cli"></a>Použití Azure CLI

Chcete-li vytvořit kontejner s vlastní zásady indexování viz, [Vytvoření kontejneru s vlastní zásady indexu pomocí CLI](manage-with-cli.md#create-a-container-with-a-custom-index-policy)

## <a name="use-powershell"></a>Použití prostředí PowerShell

Chcete-li vytvořit kontejner s vlastní zásadou indexování, podívejte [se na : Vytvoření kontejneru s vlastní zásadou indexu pomocí prostředí Powershell](manage-with-powershell.md#create-container-custom-index)

## <a name="use-the-net-sdk-v2"></a>Použití sady .NET SDK V2

Objekt `DocumentCollection` z [.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) `IndexingPolicy` zpřístupňuje vlastnost, `IndexingMode` která umožňuje `IncludedPaths` změnit `ExcludedPaths`a přidat nebo odebrat a .

```csharp
// Retrieve the container's details
ResourceResponse<DocumentCollection> containerResponse = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"));
// Set the indexing mode to consistent
containerResponse.Resource.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
// Add an included path
containerResponse.Resource.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
// Add an excluded path
containerResponse.Resource.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/name/*" });
// Add a spatial index
containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(new SpatialSpec() { Path = "/locations/*", SpatialTypes = new Collection<SpatialType>() { SpatialType.Point } } );
// Add a composite index
containerResponse.Resource.IndexingPolicy.CompositeIndexes.Add(new Collection<CompositePath> {new CompositePath() { Path = "/name", Order = CompositePathSortOrder.Ascending }, new CompositePath() { Path = "/age", Order = CompositePathSortOrder.Descending }});
// Update container with changes
await client.ReplaceDocumentCollectionAsync(containerResponse.Resource);
```

Chcete-li sledovat průběh transformace `RequestOptions` indexu, `PopulateQuotaInfo` předajte objekt, který nastaví vlastnost na `true`.

```csharp
// retrieve the container's details
ResourceResponse<DocumentCollection> container = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"), new RequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = container.IndexTransformationProgress;
```

## <a name="use-the-net-sdk-v3"></a>Použití sady .NET SDK V3

Objekt `ContainerProperties` z [.NET SDK v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) (viz [tento rychlý start](create-sql-api-dotnet.md) `IndexingPolicy` týkající se jeho použití) zpřístupňuje vlastnost, která umožňuje změnit `IndexingMode` a přidat nebo odebrat `IncludedPaths` a `ExcludedPaths`.

```csharp
// Retrieve the container's details
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Set the indexing mode to consistent
containerResponse.Resource.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
// Add an included path
containerResponse.Resource.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
// Add an excluded path
containerResponse.Resource.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/name/*" });
// Add a spatial index
SpatialPath spatialPath = new SpatialPath
{
    Path = "/locations/*"
};
spatialPath.SpatialTypes.Add(SpatialType.Point);
containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(spatialPath);
// Add a composite index
containerResponse.Resource.IndexingPolicy.CompositeIndexes.Add(new Collection<CompositePath> { new CompositePath() { Path = "/name", Order = CompositePathSortOrder.Ascending }, new CompositePath() { Path = "/age", Order = CompositePathSortOrder.Descending } });
// Update container with changes
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```

Chcete-li sledovat průběh transformace `RequestOptions` indexu, `PopulateQuotaInfo` předajte objekt, který nastaví vlastnost na `true`, a pak načtěte hodnotu z hlavičky `x-ms-documentdb-collection-index-transformation-progress` odpovědi.

```csharp
// retrieve the container's details
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync(new ContainerRequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = long.Parse(containerResponse.Headers["x-ms-documentdb-collection-index-transformation-progress"]);
```

Při definování zásad vlastního indexování při vytváření nového kontejneru umožňuje rozhraní SDK V3 plynule api napsat tuto definici stručně a efektivně:

```csharp
await client.GetDatabase("database").DefineContainer(name: "container", partitionKeyPath: "/myPartitionKey")
    .WithIndexingPolicy()
        .WithIncludedPaths()
            .Path("/*")
        .Attach()
        .WithExcludedPaths()
            .Path("/name/*")
        .Attach()
        .WithSpatialIndex()
            .Path("/locations/*", SpatialType.Point)
        .Attach()
        .WithCompositeIndex()
            .Path("/name", CompositePathSortOrder.Ascending)
            .Path("/age", CompositePathSortOrder.Descending)
        .Attach()
    .Attach()
    .CreateIfNotExistsAsync();
```

## <a name="use-the-java-sdk"></a>Použití sady Java SDK

Objekt `DocumentCollection` z [java sdk](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) (viz [tento rychlý start](create-sql-api-java.md) `getIndexingPolicy()` týkající `setIndexingPolicy()` se jeho použití) zpřístupňuje a metody. Objekt, `IndexingPolicy` se kterým manipulují, umožňuje změnit režim indexování a přidat nebo odebrat zahrnuté a vyloučené cesty.

```java
// Retrieve the container's details
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), null);
containerResponse.subscribe(result -> {
DocumentCollection container = result.getResource();
IndexingPolicy indexingPolicy = container.getIndexingPolicy();

// Set the indexing mode to consistent
indexingPolicy.setIndexingMode(IndexingMode.Consistent);

// Add an included path

Collection<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.setPath("/*");
includedPaths.add(includedPath);
indexingPolicy.setIncludedPaths(includedPaths);

// Add an excluded path

Collection<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.setPath("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.setExcludedPaths(excludedPaths);

// Add a spatial index

Collection<SpatialSpec> spatialIndexes = new ArrayList<SpatialSpec>();
Collection<SpatialType> collectionOfSpatialTypes = new ArrayList<SpatialType>();

SpatialSpec spec = new SpatialSpec();
spec.setPath("/locations/*");
collectionOfSpatialTypes.add(SpatialType.Point);
spec.setSpatialTypes(collectionOfSpatialTypes);
spatialIndexes.add(spec);

indexingPolicy.setSpatialIndexes(spatialIndexes);

// Add a composite index

Collection<ArrayList<CompositePath>> compositeIndexes = new ArrayList<>();
ArrayList<CompositePath> compositePaths = new ArrayList<>();

CompositePath nameCompositePath = new CompositePath();
nameCompositePath.setPath("/name");
nameCompositePath.setOrder(CompositePathSortOrder.Ascending);

CompositePath ageCompositePath = new CompositePath();
ageCompositePath.setPath("/age");
ageCompositePath.setOrder(CompositePathSortOrder.Descending);

compositePaths.add(ageCompositePath);
compositePaths.add(nameCompositePath);

compositeIndexes.add(compositePaths);
indexingPolicy.setCompositeIndexes(compositeIndexes);

// Update the container with changes

 client.replaceCollection(container, null);
});
```

Chcete-li sledovat průběh transformace indexu `RequestOptions` v kontejneru, předajte objekt, který požaduje informace `x-ms-documentdb-collection-index-transformation-progress` o kvótě, které mají být naplněny, a pak načtěte hodnotu z hlavičky odpovědi.

```java
// set the RequestOptions object
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPopulateQuotaInfo(true);
// retrieve the container's details
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), requestOptions);
containerResponse.subscribe(result -> {
    // retrieve the index transformation progress from the response headers
    String indexTransformationProgress = result.getResponseHeaders().get("x-ms-documentdb-collection-index-transformation-progress");
});
```

## <a name="use-the-nodejs-sdk"></a>Použití sady SDK Node.js

Rozhraní `ContainerDefinition` z [Node.js SDK](https://www.npmjs.com/package/@azure/cosmos) (viz [tento rychlý start](create-sql-api-nodejs.md) `indexingPolicy` týkající se jeho použití) zpřístupňuje vlastnost, která umožňuje změnit `indexingMode` a přidat nebo odebrat `includedPaths` a `excludedPaths`.

Načtení podrobností kontejneru

```javascript
const containerResponse = await client.database('database').container('container').read();
```

Nastavte režim indexování tak, aby byl konzistentní

```javascript
containerResponse.body.indexingPolicy.indexingMode = "consistent";
```

Přidání zahrnuté cesty včetně prostorového indexu

```javascript
containerResponse.body.indexingPolicy.includedPaths.push({
    includedPaths: [
      {
        path: "/age/*",
        indexes: [
          {
            kind: cosmos.DocumentBase.IndexKind.Range,
            dataType: cosmos.DocumentBase.DataType.String
          },
          {
            kind: cosmos.DocumentBase.IndexKind.Range,
            dataType: cosmos.DocumentBase.DataType.Number
          }
        ]
      },
      {
        path: "/locations/*",
        indexes: [
          {
            kind: cosmos.DocumentBase.IndexKind.Spatial,
            dataType: cosmos.DocumentBase.DataType.Point
          }
        ]
      }
    ]
  });
```

Přidat vyloučenou cestu

```javascript
containerResponse.body.indexingPolicy.excludedPaths.push({ path: '/name/*' });
```

Aktualizace kontejneru se změnami

```javascript
const replaceResponse = await client.database('database').container('container').replace(containerResponse.body);
```

Chcete-li sledovat průběh transformace indexu `RequestOptions` v kontejneru, `true`předavěte objekt, `x-ms-documentdb-collection-index-transformation-progress` který nastaví `populateQuotaInfo` vlastnost na , a pak načtěte hodnotu z hlavičky odpovědi.

```javascript
// retrieve the container's details
const containerResponse = await client.database('database').container('container').read({
    populateQuotaInfo: true
});
// retrieve the index transformation progress from the response headers
const indexTransformationProgress = replaceResponse.headers['x-ms-documentdb-collection-index-transformation-progress'];
```

## <a name="use-the-python-sdk-v3"></a>Použití sady Python SDK V3

Při použití [Python SDK V3](https://pypi.org/project/azure-cosmos/) (viz [tento rychlý start](create-sql-api-python.md) týkající se jeho použití), konfigurace kontejneru je spravována jako slovník. Z tohoto slovníku je možné získat přístup k zásadám indexování a všem jeho atributům.

Načtení podrobností kontejneru

```python
containerPath = 'dbs/database/colls/collection'
container = client.ReadContainer(containerPath)
```

Nastavte režim indexování tak, aby byl konzistentní

```python
container['indexingPolicy']['indexingMode'] = 'consistent'
```

Definování zásad indexování s zahrnutou cestou a prostorovým indexem

```python
container["indexingPolicy"] = {

    "indexingMode":"consistent",
    "spatialIndexes":[
                {"path":"/location/*","types":["Point"]}
             ],
    "includedPaths":[{"path":"/age/*","indexes":[]}],
    "excludedPaths":[{"path":"/*"}]
}
```

Definování zásad indexování s vyloučenou cestou

```python
container["indexingPolicy"] = {
    "indexingMode":"consistent",
    "includedPaths":[{"path":"/*","indexes":[]}],
    "excludedPaths":[{"path":"/name/*"}]
}
```

Přidání složeného indexu

```python
container['indexingPolicy']['compositeIndexes'] = [
                [
                    {
                        "path": "/name",
                        "order": "ascending"
                    },
                    {
                        "path": "/age",
                        "order": "descending"
                    }
                ]
                ]
```

Aktualizace kontejneru se změnami

```python
response = client.ReplaceContainer(containerPath, container)
```

## <a name="use-the-python-sdk-v4"></a>Použití sady Python SDK V4

Při použití [Python SDK V4](https://pypi.org/project/azure-cosmos/), konfigurace kontejneru je spravována jako slovník. Z tohoto slovníku je možné získat přístup k zásadám indexování a všem jeho atributům.

Načtení podrobností kontejneru

```python
database_client = cosmos_client.get_database_client('database')
container_client = database_client.get_container_client('container')
container = container_client.read()
```

Nastavte režim indexování tak, aby byl konzistentní

```python
indexingPolicy = {
    'indexingMode': 'consistent'
}
```

Definování zásad indexování s zahrnutou cestou a prostorovým indexem

```python
indexingPolicy = {
    "indexingMode":"consistent",
    "spatialIndexes":[
        {"path":"/location/*","types":["Point"]}
    ],
    "includedPaths":[{"path":"/age/*","indexes":[]}],
    "excludedPaths":[{"path":"/*"}]
}
```

Definování zásad indexování s vyloučenou cestou

```python
indexingPolicy = {
    "indexingMode":"consistent",
    "includedPaths":[{"path":"/*","indexes":[]}],
    "excludedPaths":[{"path":"/name/*"}]
}
```

Přidání složeného indexu

```python
indexingPolicy['compositeIndexes'] = [
    [
        {
            "path": "/name",
            "order": "ascending"
        },
        {
            "path": "/age",
            "order": "descending"
        }
    ]
]
```

Aktualizace kontejneru se změnami

```python
response = database_client.replace_container(container_client, container['partitionKey'], indexingPolicy)
```

## <a name="next-steps"></a>Další kroky

Další informace o indexování naleznete v následujících článcích:

- [Přehled indexování](index-overview.md)
- [Zásady indexování](index-policy.md)
