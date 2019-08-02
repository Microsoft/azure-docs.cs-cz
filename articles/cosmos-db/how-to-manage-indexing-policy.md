---
title: Správa zásad indexování v Azure Cosmos DB
description: Naučte se spravovat zásady indexování v Azure Cosmos DB
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: sample
ms.date: 06/27/2019
ms.author: thweiss
ms.openlocfilehash: 0100be7eeacdcda5b123356e95e2510a365d0f22
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2019
ms.locfileid: "68356442"
---
# <a name="manage-indexing-policies-in-azure-cosmos-db"></a>Správa zásad indexování v Azure Cosmos DB

V Azure Cosmos DB jsou data indexována po [indexování zásad](index-policy.md) , které jsou definovány pro každý kontejner. Výchozí zásada indexování pro nově vytvořené kontejnery vynutila indexy rozsahu pro libovolný řetězec nebo číslo a prostorové indexy pro libovolný objekt typu Point. JSON. Tyto zásady se dají přepsat:

- Z Azure Portal
- Použití rozhraní příkazového řádku Azure
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

Chcete-li sledovat průběh transformace indexu, předejte `RequestOptions` objekt, který `PopulateQuotaInfo` nastaví vlastnost na `true`hodnotu.

```csharp
// retrieve the container's details
ResourceResponse<DocumentCollection> container = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"), new RequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = container.IndexTransformationProgress;
```

## <a name="use-the-java-sdk"></a>Použití sady Java SDK

`getIndexingPolicy()` `setIndexingPolicy()` [](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) [](create-sql-api-java.md) Objekt z SDK Java (viz tento rychlý Start týkající se jeho použití) zpřístupňuje a metody. `DocumentCollection` Objekt `IndexingPolicy` , ke kterému se manipuluje, umožňuje změnit režim indexování a přidat nebo odebrat zahrnuté a vyloučené cesty.

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

```python
containerPath = 'dbs/database/colls/collection'
# retrieve the container's details
container = client.ReadContainer(containerPath)
# set the indexing mode to Consistent
container['indexingPolicy']['indexingMode'] = 'consistent'
# add an excluded path
container['indexingPolicy']['excludedPaths'] = [
    {"path": "/headquarters/employees/?"}]
# update the container with our changes
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
