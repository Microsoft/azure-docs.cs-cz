---
title: Správa zásad indexování v Azure Cosmos DB
description: Naučte se spravovat zásady indexování v Azure Cosmos DB
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: thweiss
ms.openlocfilehash: a6c1ec6d58939336fb8a982e3ab1b9be20d4e0a5
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70172152"
---
# <a name="manage-indexing-policies-in-azure-cosmos-db"></a>Správa zásad indexování v Azure Cosmos DB

V Azure Cosmos DB jsou data indexována po [indexování zásad](index-policy.md) , které jsou definovány pro každý kontejner. Výchozí zásada indexování pro nově vytvořené kontejnery vynutila indexy rozsahu pro libovolný řetězec nebo číslo a prostorové indexy pro libovolný objekt typu Point. JSON. Tyto zásady se dají přepsat:

- z Azure Portal
- použití rozhraní příkazového řádku Azure
- použití jedné ze sad SDK

[Aktualizace zásad indexování](index-policy.md#modifying-the-indexing-policy) spustí transformaci indexu. Průběh této transformace můžete také sledovat ze sad SDK.

> [!NOTE]
> V rámci upgradu sady SDK a portálu jsme vyvíjejí zásady indexů pro zarovnání s novým rozložením indexu, které jsme nastavili na nové kontejnery. S tímto novým rozložením jsou všechny primitivní datové typy indexovány jako rozsah s plnou přesností (-1). Proto nejsou tyto typy indexů a přesnost již pro uživatele vystaveny. V budoucnu budou uživatelé muset jednoduše přidávat cesty do oddílu includedPaths a ignorovat indexKinds a Precision. Tato změna nemá žádný vliv na výkon a můžete pokračovat v aktualizaci zásad indexování pomocí stejné syntaxe. Můžete dál používat všechny ukázky v naší existující dokumentaci k aktualizaci zásad indexu.

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

Kontejnery Azure Cosmos ukládají své zásady indexování jako dokument JSON, který vám Azure Portal umožňuje přímo upravit.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Vytvořte nový účet Azure Cosmos nebo vyberte existující účet.

1. Otevřete podokno **Průzkumník dat** a vyberte kontejner, na kterém chcete pracovat.

1. Klikněte na **Nastavení škálování &** .

1. Úprava dokumentu JSON zásad indexování (viz příklady [níže](#indexing-policy-examples))

1. Po dokončení klikněte na **Uložit** .

![Správa indexování pomocí Azure Portal](./media/how-to-manage-indexing-policy/indexing-policy-portal.png)

## <a name="use-the-azure-cli"></a>Použití Azure CLI

Pomocí příkazu [AZ cosmosdb Collection Update](/cli/azure/cosmosdb/collection#az-cosmosdb-collection-update) z Azure CLI můžete nahradit definici JSON pro zásady indexování kontejneru:

```azurecli-interactive
az cosmosdb collection update \
    --resource-group-name $resourceGroupName \
    --name $accountName \
    --db-name $databaseName \
    --collection-name $containerName \
    --indexing-policy "{\"indexingMode\": \"consistent\", \"includedPaths\": [{ \"path\": \"/*\", \"indexes\": [{ \"dataType\": \"String\", \"kind\": \"Range\" }] }], \"excludedPaths\": [{ \"path\": \"/headquarters/employees/?\" } ]}"
```

## <a name="use-the-net-sdk-v2"></a>Použití sady .NET SDK v2

`IndexingMode` `IndexingPolicy` `ExcludedPaths` `IncludedPaths` [](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) [](create-sql-api-dotnet.md) Objekt ze sady .NET SDK v2 (Další informace najdete v tomto rychlém startu týkající se jeho použití) zpřístupňuje vlastnost, která umožňuje změnit a přidat nebo odebrat a. `DocumentCollection`

Načtení podrobností kontejneru

```csharp
ResourceResponse<DocumentCollection> containerResponse = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"));
```

Nastavit režim indexování jako konzistentní

```csharp
containerResponse.Resource.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
```

Přidat zahrnutou cestu

```csharp
containerResponse.Resource.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/age/*" });
```

Přidat vyloučenou cestu

```csharp
containerResponse.Resource.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/name/*" });
```

Přidání prostorového indexu

```csharp
containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(new SpatialSpec() { Path = "/locations/*", SpatialTypes = new Collection<SpatialType>() { SpatialType.Point } } );
```

Přidat složený index

```csharp
containerResponse.Resource.IndexingPolicy.CompositeIndexes.Add(new Collection<CompositePath> {new CompositePath() { Path = "/name", Order = CompositePathSortOrder.Ascending }, new CompositePath() { Path = "/age", Order = CompositePathSortOrder.Descending }});
```

Aktualizovat kontejner o změny

```csharp
await client.ReplaceDocumentCollectionAsync(containerResponse.Resource);
```

Chcete-li sledovat průběh transformace indexu, předejte `RequestOptions` objekt, který `PopulateQuotaInfo` nastaví vlastnost na `true`hodnotu.

```csharp
// retrieve the container's details
ResourceResponse<DocumentCollection> container = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"), new RequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = container.IndexTransformationProgress;
```

## <a name="use-the-java-sdk"></a>Použití sady Java SDK

`getIndexingPolicy()` `setIndexingPolicy()` [](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) [](create-sql-api-java.md) Objekt z SDK Java (viz tento rychlý Start týkající se jeho použití) zpřístupňuje a metody. `DocumentCollection` Objekt `IndexingPolicy` , ke kterému se manipuluje, umožňuje změnit režim indexování a přidat nebo odebrat zahrnuté a vyloučené cesty.

Načtení podrobností kontejneru

```java
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), null);
containerResponse.subscribe(result -> {
DocumentCollection container = result.getResource();
IndexingPolicy indexingPolicy = container.getIndexingPolicy();
```

Nastavit režim indexování jako konzistentní

```java
indexingPolicy.setIndexingMode(IndexingMode.Consistent);
```

Přidat zahrnutou cestu

```java
Collection<IncludedPath> includedPaths = new ArrayList<>();
ExcludedPath includedPath = new IncludedPath();
includedPath.setPath("/age/*");
includedPaths.add(includedPath);
indexingPolicy.setIncludedPaths(includedPaths);
```

Přidat vyloučenou cestu

```java
Collection<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.setPath("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.setExcludedPaths(excludedPaths);
```

Přidání prostorového indexu

```java
Collection<SpatialSpec> spatialIndexes = new ArrayList<SpatialSpec>();
Collection<SpatialType> collectionOfSpatialTypes = new ArrayList<SpatialType>();

SpatialSpec spec = new SpatialSpec();
spec.setPath("/locations/*");
collectionOfSpatialTypes.add(SpatialType.Point);          
spec.setSpatialTypes(collectionOfSpatialTypes);
spatialIndexes.add(spec);

indexingPolicy.setSpatialIndexes(spatialIndexes);

```

Přidat složený index

```java
Collection<ArrayList<CompositePath>> compositeIndexes = new ArrayList<>();
ArrayList<CompositePath> compositePaths = new ArrayList<>();

CompositePath nameCompositePath = new CompositePath();
nameCompositePath.setPath("/name/*");
nameCompositePath.setOrder(CompositePathSortOrder.Ascending);

CompositePath ageCompositePath = new CompositePath();
ageCompositePath.setPath("/age/*");
ageCompositePath.setOrder(CompositePathSortOrder.Descending);

compositePaths.add(ageCompositePath);
compositePaths.add(nameCompositePath);

compositeIndexes.add(compositePaths);
indexingPolicy.setCompositeIndexes(compositeIndexes);
```

Aktualizace kontejneru změnami

```java
 client.replaceCollection(container, null);
```

Chcete-li sledovat průběh transformace indexu v kontejneru, předejte `RequestOptions` objekt, který požaduje informace o kvótě pro naplnění, a pak hodnotu `x-ms-documentdb-collection-index-transformation-progress` načtěte v hlavičce odpovědi.

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

## <a name="use-the-nodejs-sdk"></a>Použití sady Node. js SDK

`indexingMode` `indexingPolicy` `excludedPaths` `includedPaths` [](https://www.npmjs.com/package/@azure/cosmos) [](create-sql-api-nodejs.md) Rozhraní z Node. js SDK (v tomto rychlém startu najdete informace o jeho použití) zpřístupňuje vlastnost, která umožňuje změnit a přidat nebo odebrat a. `ContainerDefinition`

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

Chcete-li sledovat průběh transformace indexu v kontejneru, předejte `RequestOptions` objekt, na `true`který `populateQuotaInfo` vlastnost nastavuje, a pak hodnotu `x-ms-documentdb-collection-index-transformation-progress` načtěte v hlavičce odpovědi.

```javascript
// retrieve the container's details
const containerResponse = await client.database('database').container('container').read({
    populateQuotaInfo: true
});
// retrieve the index transformation progress from the response headers
const indexTransformationProgress = replaceResponse.headers['x-ms-documentdb-collection-index-transformation-progress'];
```

## <a name="use-the-python-sdk"></a>Použití sady Python SDK

Při použití [sady Python SDK](https://pypi.org/project/azure-cosmos/) (v [tomto rychlém](create-sql-api-python.md) startu najdete informace o jeho použití) je konfigurace kontejneru spravovaná jako slovník. Z tohoto slovníku je možné získat přístup k zásadám indexování a všem jeho atributům.

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

## <a name="indexing-policy-examples"></a>Příklady zásad indexování

Tady je několik příkladů indexování zásad, které jsou uvedené ve formátu JSON, což je způsob jejich zpřístupnění na Azure Portal. Stejné parametry můžete nastavit prostřednictvím rozhraní příkazového řádku Azure nebo libovolné sady SDK.

### <a name="opt-out-policy-to-selectively-exclude-some-property-paths"></a>Zásady výslovných odhlášení pro selektivní vyloučení některých cest k vlastnostem
```
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    },
                    {
                        "kind": "Range",
                        "dataType": "String"
                    },
                    {
                        "kind": "Spatial",
                        "dataType": "Point"
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
```
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/path/to/included/property/?",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    }
                ]
            },
            {
                "path": "/path/to/root/of/multiple/included/properties/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
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

Poznámka: Obecně se doporučuje použít zásadu indexování pro **výslovný souhlas** , která Azure Cosmos DB proaktivní indexování všech nových vlastností, které se dají do modelu přidat.

### <a name="using-a-spatial-index-on-a-specific-property-path-only"></a>Použití prostorového indexu pouze na konkrétní cestu k vlastnosti
```
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*",
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
                "path": "/path/to/geojson/property/?",
                "indexes": [
                    {
                        "kind": "Spatial",
                        "dataType": "Point"
                    }
                ]
            }
        ],
        "excludedPaths": []
    }
```

### <a name="excluding-all-property-paths-but-keeping-indexing-active"></a>Vyloučení všech cest k vlastnostem, ale zachování aktivního indexování

Tato zásada se dá použít v situacích, kdy je aktivní [funkce TTL (Time-to-Live)](time-to-live.md) , ale není nutný žádný sekundární index (pro použití Azure Cosmos DB jako úložiště čistě klíč-hodnota).
```
    {
        "indexingMode": "consistent",
        "includedPaths": [],
        "excludedPaths": [{
            "path": "/*"
        }]
    }
```

### <a name="no-indexing"></a>Bez indexování
```
    {
        "indexingMode": "none"
    }
```

## <a name="composite-indexing-policy-examples"></a>Příklady složených indexovaných zásad

Kromě zahrnutí nebo vyloučení cest pro jednotlivé vlastnosti můžete také zadat složený index. Chcete-li provést dotaz, který má `ORDER BY` klauzuli pro více vlastností, je nutné použít [složený index](index-policy.md#composite-indexes) těchto vlastností.

### <a name="composite-index-defined-for-name-asc-age-desc"></a>Byl definován složený index pro (název ASC, věk DESC):
```
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[  

        ],
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

Tento složený index by mohl podporovat následující dva dotazy:

#1 dotazů:
```sql
    SELECT *
    FROM c
    ORDER BY name asc, age desc    
```

#2 dotazů:
```sql
    SELECT *
    FROM c
    ORDER BY name desc, age asc
```

### <a name="composite-index-defined-for-name-asc-age-asc-and-name-asc-age-desc"></a>Složený index definovaný pro (název ASC, věk ASC) a (název ASC, věkové vydesc):

V rámci stejné zásady indexování můžete definovat několik různých složených indexů. 
```
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[  

        ],
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
```
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[  

        ],
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

## <a name="next-steps"></a>Další postup

Další informace o indexování najdete v následujících článcích:

- [Přehled indexování](index-overview.md)
- [Zásady indexování](index-policy.md)
