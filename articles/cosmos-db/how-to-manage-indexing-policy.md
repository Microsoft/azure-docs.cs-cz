---
title: Spravovat zásady indexování ve službě Azure Cosmos DB
description: Další informace o správě zásad indexování ve službě Azure Cosmos DB
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/06/2019
ms.author: thweiss
ms.openlocfilehash: 0b47ffd77ee23f997bb7de2ea41f83c2854cba72
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/13/2019
ms.locfileid: "65550891"
---
# <a name="manage-indexing-policies-in-azure-cosmos-db"></a>Spravovat zásady indexování ve službě Azure Cosmos DB

Ve službě Azure Cosmos DB, data zaindexují následující [zásadám indexování](index-policy.md) , která jsou definována pro každý kontejner. Ve výchozím nastavení zásady pro nově vytvořený kontejnery indexování vynucuje rozsah indexů pro libovolný řetězec nebo číslo a prostorové indexy pro libovolný objekt GeoJSON typu bod. Tato zásada se dá přepsat:

- Na webu Azure Portal
- Použití Azure CLI
- pomocí jedné ze sad SDK

[Indexování aktualizace zásad](index-policy.md#modifying-the-indexing-policy) aktivuje transformaci indexu. Průběh této transformace lze sledovat také ze sady SDK.

> [!NOTE]
> Jako součást upgradu sady SDK a portál vyvíjíme zásad index souladu s novým rozložením index, který jste zavedli jsme do nových kontejnerů. Pomocí této nové rozložení jsou všechny primitivní datové typy index rozsahu s úplnou přesností (-1). Proto typy index a přesnost nezveřejňují uživateli zobrazovat. V budoucnu uživatelé budou potřebovat jednoduše přidat do části includedPaths cesty a ignorování indexKinds a přesnosti. Tato změna nemá žádný vliv na výkon a můžete pokračovat v aktualizaci zásady indexování pomocí stejné syntaxe. Můžete nadále používat všechny ukázky ve stávající dokumentaci se aktualizovat zásady index.

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

Kontejnery služby Azure Cosmos ukládat své zásady indexování jako dokument JSON, který na webu Azure portal umožňuje přímo upravovat.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Vytvořit nový účet Azure Cosmos, nebo vyberte existující účet.

1. Otevřít **Průzkumník dat** podokně a vyberte kontejner, který chcete pracovat.

1. Klikněte na **škálování a nastavení**.

1. Upravit indexování dokumentů JSON zásady (podívejte se na příklady [níže](#indexing-policy-examples))

1. Klikněte na tlačítko **Uložit** až budete hotovi.

![Správa indexování pomocí webu Azure portal](./media/how-to-manage-indexing-policy/indexing-policy-portal.png)

## <a name="use-the-azure-cli"></a>Použití Azure CLI

[Aktualizace kolekce az cosmosdb](/cli/azure/cosmosdb/collection#az-cosmosdb-collection-update) příkaz z příkazového řádku Azure umožňuje Nahraďte definici JSON zásady indexování kontejneru:

```azurecli-interactive
az cosmosdb collection update \
    --resource-group-name $resourceGroupName \
    --name $accountName \
    --db-name $databaseName \
    --collection-name $containerName \
    --indexing-policy "{\"indexingMode\": \"consistent\", \"includedPaths\": [{ \"path\": \"/*\", \"indexes\": [{ \"dataType\": \"String\", \"kind\": \"Range\" }] }], \"excludedPaths\": [{ \"path\": \"/headquarters/employees/?\" } ]}"
```

## <a name="use-the-net-sdk-v2"></a>Používat .NET SDK V2

`DocumentCollection` Objektu z [sady .NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) (naleznete v tématu [v tomto rychlém startu](create-sql-api-dotnet.md) související s jeho využitím) poskytuje `IndexingPolicy` vlastnost, která vám umožní změnit `IndexingMode` a přidat nebo odebrat `IncludedPaths` a `ExcludedPaths`.

```csharp
// retrieve the container's details
ResourceResponse<DocumentCollection> containerResponse = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"));
// set the indexing mode to Consistent
containerResponse.Resource.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
// add an excluded path
containerResponse.Resource.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/headquarters/employees/?" });
// update the container with our changes
await client.ReplaceDocumentCollectionAsync(containerResponse.Resource);
```

Chcete-li sledovat průběh transformace index, předejte `RequestOptions` objekt, který nastaví `PopulateQuotaInfo` vlastnost `true`.

```csharp
// retrieve the container's details
ResourceResponse<DocumentCollection> container = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"), new RequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = container.IndexTransformationProgress;
```

## <a name="use-the-java-sdk"></a>Použití sady Java SDK

`DocumentCollection` Objektu z [sady Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) (naleznete v tématu [v tomto rychlém startu](create-sql-api-java.md) související s jeho využitím) poskytuje `getIndexingPolicy()` a `setIndexingPolicy()` metody. `IndexingPolicy` Objektu, manipulovat s umožňuje změnit režim indexování a přidat nebo odebrat zahrnuté a vyloučené cesty.

```java
// retrieve the container's details
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), null);
containerResponse.subscribe(result -> {
    DocumentCollection container = result.getResource();
    IndexingPolicy indexingPolicy = container.getIndexingPolicy();
    // set the indexing mode to Consistent
    indexingPolicy.setIndexingMode(IndexingMode.Consistent);
    Collection<ExcludedPath> excludedPaths = new ArrayList<>();
    ExcludedPath excludedPath = new ExcludedPath();
    excludedPath.setPath("/*");
    // add an excluded path
    excludedPaths.add(excludedPath);
    indexingPolicy.setExcludedPaths(excludedPaths);
    // update the container with our changes
    client.replaceCollection(container, null);
});
```

Chcete-li sledovat průběh transformace indexu v kontejneru, předejte `RequestOptions` objekt, který vyžaduje informace o kvótě vyplní, pak načtení hodnoty z `x-ms-documentdb-collection-index-transformation-progress` hlavička odpovědi.

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

## <a name="use-the-nodejs-sdk"></a>Použití sady SDK pro Node.js

`ContainerDefinition` Rozhraní z [sady Node.js SDK](https://www.npmjs.com/package/@azure/cosmos) (naleznete v tématu [v tomto rychlém startu](create-sql-api-nodejs.md) související s jeho využitím) poskytuje `indexingPolicy` vlastnost, která vám umožní změnit `indexingMode` a přidat nebo odebrat `includedPaths` a `excludedPaths`.

```javascript
// retrieve the container's details
const containerResponse = await client.database('database').container('container').read();
// set the indexing mode to Consistent
containerResponse.body.indexingPolicy.indexingMode = "consistent";
// add an excluded path
containerResponse.body.indexingPolicy.excludedPaths.push({ path: '/headquarters/employees/?' });
// update the container with our changes
const replaceResponse = await client.database('database').container('container').replace(containerResponse.body);
```

Můžete sledovat postup transformace index u kontejneru, předejte `RequestOptions` objekt, který nastaví `populateQuotaInfo` vlastnost `true`, pak načtení hodnoty z `x-ms-documentdb-collection-index-transformation-progress` hlavičky odpovědi.

```javascript
// retrieve the container's details
const containerResponse = await client.database('database').container('container').read({
    populateQuotaInfo: true
});
// retrieve the index transformation progress from the response headers
const indexTransformationProgress = replaceResponse.headers['x-ms-documentdb-collection-index-transformation-progress'];
```

## <a name="use-the-python-sdk"></a>Použít Python SDK

Při použití [Python SDK](https://pypi.org/project/azure-cosmos/) (viz [v tomto rychlém startu](create-sql-api-python.md) související s jeho využitím), konfigurace kontejneru se spravuje jako slovník. Z tohoto slovníku a je možné pracovat s zásady indexování a jeho atributy.

```python
containerPath = 'dbs/database/colls/collection'
# retrieve the container's details
container = client.ReadContainer(containerPath)
# set the indexing mode to Consistent
container['indexingPolicy']['indexingMode'] = 'consistent'
# add an excluded path
container['indexingPolicy']['excludedPaths'] = [{"path" : "/headquarters/employees/?"}]
# update the container with our changes
response = client.ReplaceContainer(containerPath, container)
```

## <a name="indexing-policy-examples"></a>Příklady zásad indexování

Tady je několik příkladů indexování zásady, které jsou uvedené v jejich formát JSON, který je, jak jsou zveřejněné na webu Azure portal. Stejné parametry lze nastavit pomocí rozhraní příkazového řádku Azure, nebo všechny sady SDK.

### <a name="opt-out-policy-to-selectively-exclude-some-property-paths"></a>Zásady odhlásit selektivně vyloučit některé vlastnosti cesty
```
    {
        "indexingPolicy": "consistent",
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

### <a name="opt-in-policy-to-selectively-include-some-property-paths"></a>Zásady vyjádřit výslovný souhlas pro selektivní některé vlastnosti cesty zahrnutí
```
    {
        "indexingPolicy": "consistent",
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

Poznámka: Obecně se doporučuje používat **Odhlásit** zásady služby Azure Cosmos DB umožní aktivně indexování indexovat žádné nové vlastnosti, který může být přidán do modelu.

### <a name="using-a-spatial-index-on-a-specific-property-path-only"></a>Použití prostorový index na cestu určitou vlastnost
```
    {
        "indexingPolicy": "consistent",
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

### <a name="excluding-all-property-paths-but-keeping-indexing-active"></a>Kromě všech cest vlastnost ale je zachováno indexování active

Tyto zásady můžete použít v situacích, kde [Time-to-Live (TTL) funkce](time-to-live.md) je aktivní, ale sekundární index je povinný (pro použití služby Azure Cosmos DB jako čistě úložiště dvojic klíč hodnota).
```
    {
        "indexingMode": "consistent",
        "includedPaths": [],
        "excludedPaths": [{
            "path": "/*"
        }]
    }
```

### <a name="no-indexing"></a>Žádné indexování
```
    {
        "indexingPolicy": "none"
    }
```

## <a name="composite-indexing-policy-examples"></a>Složený indexování příklady zásad

Kromě zahrnutí nebo vyloučení cest pro jednotlivé vlastnosti, můžete také určit složeném indexu. Pokud chcete provést dotaz, který má `ORDER BY` klauzule pro více vlastností [složeném indexu](index-policy.md#composite-indexes) u těchto vlastností je povinný.

### <a name="composite-index-defined-for-name-asc-age-desc"></a>Složeném indexu definované pro (název asc, věk desc):
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

Tato složeném indexu bude moci podporovat následující dva dotazy:

Dotaz #1:
```sql
    SELECT *
    FROM c
    ORDER BY name asc, age desc    
```

Dotaz #2:
```sql
    SELECT *
    FROM c
    ORDER BY name desc, age asc
```

### <a name="composite-index-defined-for-name-asc-age-asc-and-name-asc-age-desc"></a>Složeném indexu definované pro (název asc, věk asc) a (název asc, stáří desc):

Můžete definovat více různých Složené indexy v rámci stejné zásady indexování. 
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
            ]
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

### <a name="composite-index-defined-for-name-asc-age-asc"></a>Složeném indexu definované pro (název asc, věk asc):

Zadání je volitelné pro určení pořadí. Pokud není zadán, je vzestupné pořadí.
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

Další informace o indexování v následujících článcích:

- [Indexování – přehled](index-overview.md)
- [Zásady indexování](index-policy.md)
