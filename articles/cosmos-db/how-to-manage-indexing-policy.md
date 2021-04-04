---
title: Správa zásad indexování ve službě Azure Cosmos DB
description: Naučte se spravovat zásady indexování, zahrnout nebo vyloučit vlastnost z indexování, jak definovat indexování pomocí různých sad Azure Cosmos DB SDK.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 11/02/2020
ms.author: tisande
ms.custom: devx-track-python, devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 8d52f8c59e83a4aae8724100770965f756a439fb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98015687"
---
# <a name="manage-indexing-policies-in-azure-cosmos-db"></a>Správa zásad indexování ve službě Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

V Azure Cosmos DB jsou data indexována po [indexování zásad](index-policy.md) , které jsou definovány pro každý kontejner. Výchozí zásady indexování pro nově vytvořené kontejnery u všech řetězců a čísel vynucují indexy rozsahu. Tyto zásady je možné přepsat vlastními zásadami indexování.

> [!NOTE]
> Metoda aktualizace zásad indexování popsaná v tomto článku se týká jenom rozhraní API pro Azure Cosmos DB SQL (Core). Přečtěte si o indexování v [rozhraní Azure Cosmos DB API pro MongoDB](mongodb-indexing.md) a [sekundární indexování v Azure Cosmos DB rozhraní API Cassandra.](cassandra-secondary-index.md)

## <a name="indexing-policy-examples"></a>Příklady zásad indexování

Tady je několik příkladů indexování zásad, které jsou uvedené ve [formátu JSON](index-policy.md#include-exclude-paths), což je způsob jejich zpřístupnění na Azure Portal. Stejné parametry můžete nastavit prostřednictvím rozhraní příkazového řádku Azure nebo libovolné sady SDK.

### <a name="opt-out-policy-to-selectively-exclude-some-property-paths"></a><a id="range-index"></a>Zásady výslovných odhlášení pro selektivní vyloučení některých cest k vlastnostem

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

Tato zásada indexování je ekvivalentní k těm, které jsou nastaveny ručně ```kind``` , ```dataType``` a ```precision``` na jejich výchozí hodnoty. Tyto vlastnosti už nejsou potřebné k explicitnímu nastavení a měli byste je vynechat ze zásad indexování úplně (jak je znázorněno v předchozím příkladu).

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

### <a name="opt-in-policy-to-selectively-include-some-property-paths"></a>Zásada výslovného souhlasu pro selektivní zahrnutí některých cest k vlastnostem

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

Tato zásada indexování je ekvivalentní k těm, které jsou nastaveny ručně ```kind``` , ```dataType``` a ```precision``` na jejich výchozí hodnoty. Tyto vlastnosti už nejsou potřebné k explicitnímu nastavení a měli byste je vynechat ze zásad indexování úplně (jak je znázorněno v předchozím příkladu).

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
> Obecně se doporučuje použít zásadu indexování pro **výslovný souhlas** , která Azure Cosmos DB proaktivně indexovat každou novou vlastnost, která může být přidána do datového modelu.

### <a name="using-a-spatial-index-on-a-specific-property-path-only"></a><a id="spatial-index"></a>Použití prostorového indexu pouze na konkrétní cestu k vlastnosti

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

## <a name="composite-indexing-policy-examples"></a><a id="composite-index"></a>Příklady složených indexovaných zásad

Kromě zahrnutí nebo vyloučení cest pro jednotlivé vlastnosti můžete také zadat složený index. Chcete-li provést dotaz, který má `ORDER BY` klauzuli pro více vlastností, je nutné použít [složený index](index-policy.md#composite-indexes) těchto vlastností. Kromě toho budou mít složené indexy výkonové výhody pro dotazy, které mají více filtrů nebo filtr i klauzule ORDER BY.

> [!NOTE]
> Složené cesty mají implicitní `/?` , protože pouze skalární hodnota na této cestě je indexována. `/*`Zástupný znak není podporován ve složených cestách. Neměli byste zadat `/?` nebo `/*` v složené cestě.

### <a name="composite-index-defined-for-name-asc-age-desc"></a>Byl definován složený index pro (název ASC, věk DESC):

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

Pro dotaz #1 a dotaz #2 se vyžaduje výše uvedený index v názvu a stáří.

#1 dotazů:

```sql
    SELECT *
    FROM c
    ORDER BY c.name ASC, c.age DESC
```

#2 dotazů:

```sql
    SELECT *
    FROM c
    ORDER BY c.name DESC, c.age ASC
```

Tento složený index bude těžit z #3 dotazování a dotazování #4 a optimalizaci filtrů:

#3 dotazů:

```sql
SELECT *
FROM c
WHERE c.name = "Tim"
ORDER BY c.name DESC, c.age ASC
```

#4 dotazů:

```sql
SELECT *
FROM c
WHERE c.name = "Tim" AND c.age > 18
```

### <a name="composite-index-defined-for-name-asc-age-asc-and-name-asc-age-desc"></a>Složený index definovaný pro (název ASC, věk ASC) a (název ASC, věkové vydesc):

V rámci stejné zásady indexování můžete definovat několik různých složených indexů.

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

### <a name="composite-index-defined-for-name-asc-age-asc"></a>Pro (název ASC, věk ASC) byl definován složený index.

Je volitelné zadat objednávku. Pokud tento parametr nezadáte, pořadí je vzestupné.

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

### <a name="excluding-all-property-paths-but-keeping-indexing-active"></a>Vyloučení všech cest k vlastnostem, ale zachování aktivního indexování

Tato zásada se dá použít v situacích, kdy je aktivní [funkce TTL (Time-to-Live)](time-to-live.md) , ale nejsou potřeba žádné další indexy (pro použití Azure Cosmos DB jako úložiště čistě klíč-hodnota).

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

Tato zásada vypne indexování. Pokud `indexingMode` je nastaveno na `none` , nemůžete nastavit hodnotu TTL pro kontejner.

```json
    {
        "indexingMode": "none"
    }
```

## <a name="updating-indexing-policy"></a>Aktualizují se zásady indexování

V Azure Cosmos DB můžete zásady indexování aktualizovat pomocí kterékoli z následujících metod:

- z Azure Portal
- použití rozhraní příkazového řádku Azure
- použití PowerShellu
- použití jedné ze sad SDK

[Aktualizace zásad indexování](index-policy.md#modifying-the-indexing-policy) spustí transformaci indexu. Průběh této transformace můžete také sledovat ze sad SDK.

> [!NOTE]
> Při aktualizaci zásad indexování budou zápisy do Azure Cosmos DB nepřerušeny. Další informace o [transformacích indexování](index-policy.md#modifying-the-indexing-policy)

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

Kontejnery Azure Cosmos ukládají své zásady indexování jako dokument JSON, který vám Azure Portal umožňuje přímo upravit.

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

1. Vytvořte nový účet Azure Cosmos nebo vyberte existující účet.

1. Otevřete podokno **Průzkumník dat** a vyberte kontejner, na kterém chcete pracovat.

1. Klikněte na **Nastavení škálování &**.

1. Úprava dokumentu JSON zásad indexování (viz příklady [níže](#indexing-policy-examples))

1. Po dokončení klikněte na **Uložit**.

:::image type="content" source="./media/how-to-manage-indexing-policy/indexing-policy-portal.png" alt-text="Správa indexování pomocí Azure Portal":::

## <a name="use-the-azure-cli"></a>Použití Azure CLI

Pokud chcete vytvořit kontejner s vlastními zásadami indexování, přečtěte si téma [vytvoření kontejneru s vlastní zásadou indexu pomocí](manage-with-cli.md#create-a-container-with-a-custom-index-policy) rozhraní příkazového řádku (CLI).

## <a name="use-powershell"></a>Použití prostředí PowerShell

Pokud chcete vytvořit kontejner s vlastními zásadami indexování, přečtěte si téma [vytvoření kontejneru s vlastní zásadou indexu pomocí PowerShellu](manage-with-powershell.md#create-container-custom-index) .

## <a name="use-the-net-sdk"></a><a id="dotnet-sdk"></a> Použití sady .NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

`DocumentCollection`Objekt ze [sady .NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) zpřístupňuje `IndexingPolicy` vlastnost, která umožňuje změnit `IndexingMode` a přidat nebo odebrat `IncludedPaths` a `ExcludedPaths` .

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

Chcete-li sledovat průběh transformace indexu, předejte `RequestOptions` objekt, který nastaví `PopulateQuotaInfo` vlastnost na hodnotu `true` .

```csharp
// retrieve the container's details
ResourceResponse<DocumentCollection> container = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"), new RequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = container.IndexTransformationProgress;
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

`ContainerProperties`Objekt ze [sady .NET SDK V3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) (Další informace najdete v [tomto rychlém](create-sql-api-dotnet.md) startu týkající se jeho použití) zpřístupňuje `IndexingPolicy` vlastnost, která umožňuje změnit `IndexingMode` a přidat nebo odebrat `IncludedPaths` a `ExcludedPaths` .

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

Chcete-li sledovat průběh transformace indexu, předejte `RequestOptions` objekt, `PopulateQuotaInfo` na který vlastnost nastavuje, a `true` pak hodnotu načtěte v `x-ms-documentdb-collection-index-transformation-progress` hlavičce odpovědi.

```csharp
// retrieve the container's details
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync(new ContainerRequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = long.Parse(containerResponse.Headers["x-ms-documentdb-collection-index-transformation-progress"]);
```

Při definování vlastních zásad indexování při vytváření nového kontejneru vám rozhraní SDK V3's Fluent umožňuje psát tuto definici stručně a efektivním způsobem:

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
---

## <a name="use-the-java-sdk"></a>Použití sady Java SDK

`DocumentCollection`Objekt z [SDK Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) (viz [Tento rychlý Start](create-sql-api-java.md) týkající se jeho použití) zpřístupňuje `getIndexingPolicy()` a `setIndexingPolicy()` metody. `IndexingPolicy`Objekt, ke kterému se manipuluje, umožňuje změnit režim indexování a přidat nebo odebrat zahrnuté a vyloučené cesty.

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

Chcete-li sledovat průběh transformace indexu v kontejneru, předejte `RequestOptions` objekt, který požaduje informace o kvótě pro naplnění, a pak hodnotu načtěte v `x-ms-documentdb-collection-index-transformation-progress` hlavičce odpovědi.

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

## <a name="use-the-nodejs-sdk"></a>Použití sady Node.js SDK

`ContainerDefinition`Rozhraní z [Node.js SDK](https://www.npmjs.com/package/@azure/cosmos) (v [tomto rychlém](create-sql-api-nodejs.md) startu najdete informace o jeho použití) zpřístupňuje `indexingPolicy` vlastnost, která umožňuje změnit `indexingMode` a přidat nebo odebrat `includedPaths` a `excludedPaths` .

Načtení podrobností kontejneru

```javascript
const containerResponse = await client.database('database').container('container').read();
```

Nastavit režim indexování jako konzistentní

```javascript
containerResponse.body.indexingPolicy.indexingMode = "consistent";
```

Přidat zahrnutou cestu včetně prostorového indexu

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

Aktualizace kontejneru změnami

```javascript
const replaceResponse = await client.database('database').container('container').replace(containerResponse.body);
```

Chcete-li sledovat průběh transformace indexu v kontejneru, předejte `RequestOptions` objekt, na který `populateQuotaInfo` vlastnost nastavuje `true` , a pak hodnotu načtěte v `x-ms-documentdb-collection-index-transformation-progress` hlavičce odpovědi.

```javascript
// retrieve the container's details
const containerResponse = await client.database('database').container('container').read({
    populateQuotaInfo: true
});
// retrieve the index transformation progress from the response headers
const indexTransformationProgress = replaceResponse.headers['x-ms-documentdb-collection-index-transformation-progress'];
```

## <a name="use-the-python-sdk"></a>Použití sady Python SDK

# <a name="python-sdk-v3"></a>[Python SDK V3](#tab/pythonv3)

Při použití sady [Python SDK V3](https://pypi.org/project/azure-cosmos/) (v [tomto rychlém](create-sql-api-python.md) startu najdete informace o jeho použití) je konfigurace kontejneru spravovaná jako slovník. Z tohoto slovníku je možné získat přístup k zásadám indexování a všem jeho atributům.

Načtení podrobností kontejneru

```python
containerPath = 'dbs/database/colls/collection'
container = client.ReadContainer(containerPath)
```

Nastavit režim indexování jako konzistentní

```python
container['indexingPolicy']['indexingMode'] = 'consistent'
```

Definování zásad indexování pomocí zahrnuté cesty a prostorového indexu

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

Přidat složený index

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

Aktualizace kontejneru změnami

```python
response = client.ReplaceContainer(containerPath, container)
```

# <a name="python-sdk-v4"></a>[Python SDK v4](#tab/pythonv4)

Při použití sady [Python SDK v4](https://pypi.org/project/azure-cosmos/)je konfigurace kontejneru spravovaná jako slovník. Z tohoto slovníku je možné získat přístup k zásadám indexování a všem jeho atributům.

Načtení podrobností kontejneru

```python
database_client = cosmos_client.get_database_client('database')
container_client = database_client.get_container_client('container')
container = container_client.read()
```

Nastavit režim indexování jako konzistentní

```python
indexingPolicy = {
    'indexingMode': 'consistent'
}
```

Definování zásad indexování pomocí zahrnuté cesty a prostorového indexu

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

Přidat složený index

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

Aktualizace kontejneru změnami

```python
response = database_client.replace_container(container_client, container['partitionKey'], indexingPolicy)
```

Načíst průběh transformace indexu z hlaviček odpovědi
```python
container_client.read(populate_quota_info = True,
                      response_hook = lambda h,p: print(h['x-ms-documentdb-collection-index-transformation-progress']))
```

---

## <a name="next-steps"></a>Další kroky

Další informace o indexování najdete v následujících článcích:

- [Přehled indexování](index-overview.md)
- [Zásada indexování](index-policy.md)
