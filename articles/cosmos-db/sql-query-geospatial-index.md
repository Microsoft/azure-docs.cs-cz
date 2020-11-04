---
title: Indexování geoprostorových dat pomocí Azure Cosmos DB
description: Indexace prostorových dat pomocí Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: tisande
ms.openlocfilehash: 47eedf1ddbb155180d364c42ec179b3e01279e44
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93336210"
---
# <a name="index-geospatial-data-with-azure-cosmos-db"></a>Indexování geoprostorových dat pomocí Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Navrhli jsme, aby byl databázový stroj Azure Cosmos DB nezávislá jako skutečně schématu a poskytoval jako první podporu třídy pro JSON. Napsání optimalizovaného databázového stroje Azure Cosmos DB nativně rozumí prostorová data reprezentovaná v rámci standardu injson.

V kostce je geometrie promítnuta z souřadnic Geodetic na 2D rovinu a poté postupně rozdělena do buněk pomocí **quadtree**. Tyto buňky jsou namapovány na 1D na základě umístění buňky v **křivce vyplňování Hilbert prostoru** , která zachovává polohu místa. Kromě toho, když jsou data umístění indexována, projde procesem známým jako **teselaci** , to znamená, že všechny buňky, které protínají umístění, jsou identifikovány a uloženy jako klíče v indexu Azure Cosmos DB. V době dotazu jsou argumenty, jako jsou body a mnohoúhelníky, také teselace k extrakci příslušných rozsahů ID buněk a potom se používají k načtení dat z indexu.

Pokud zadáte zásadu indexování, která zahrnuje prostorový index pro `/*` (všechny cesty), pak jsou všechna data nalezená v rámci kontejneru indexována pro efektivní prostorové dotazy.

> [!NOTE]
> Azure Cosmos DB podporuje indexování bodů, LineStrings, mnohoúhelníků a více mnohoúhelníků. Při indexování některého z těchto typů budeme automaticky indexovat všechny ostatní typy. Jinými slovy, i když indexuje mnohoúhelníky, budeme také indexovat body, LineStrings a více mnohoúhelníků. Indexování nového prostorového typu nemá vliv na poplatek za zápis RU nebo na velikost indexu, pokud nemáte platná data o biojsonu tohoto typu.

## <a name="modifying-geospatial-configuration"></a>Úprava geoprostorové konfigurace

V kontejneru Určuje **geoprostorové nastavení** , jak budou prostorová data indexována. Zadejte jednu **geoprostorové konfiguraci** na kontejner: Geografie nebo geometrie.

V Azure Portal můžete přepínat mezi **zeměpisným** a **geometrickým** typem prostoru. Před přepnutím na prostorový typ je důležité vytvořit [platnou zásadu indexování prostorových geometrie s ohraničujícím polem](#geometry-data-indexing-examples) .

Tady je postup nastavení **geoprostorové konfigurace** v **Průzkumník dat** v rámci Azure Portal:

:::image type="content" source="./media/sql-query-geospatial-index/geospatial-configuration.png" alt-text="Nastavení geoprostorové konfigurace":::

Můžete také upravit v sadě `geospatialConfig` .NET SDK pro úpravu **geoprostorové konfigurace** :

Pokud není zadaný, `geospatialConfig` použije se jako výchozí zeměpisný datový typ. Když upravíte `geospatialConfig` , všechna existující geoprostorové data v kontejneru se Přeindexují.

Tady je příklad pro úpravu geoprostorového datového typu na nastavením `geometry` `geospatialConfig` vlastnosti a přidání **boundingBox** :

```csharp
    //Retrieve the container's details
    ContainerResponse containerResponse = await client.GetContainer("db", "spatial").ReadContainerAsync();
    //Set GeospatialConfig to Geometry
    GeospatialConfig geospatialConfig = new GeospatialConfig(GeospatialType.Geometry);
    containerResponse.Resource.GeospatialConfig = geospatialConfig;
    // Add a spatial index including the required boundingBox
    SpatialPath spatialPath = new SpatialPath
            {  
                Path = "/locations/*",
                BoundingBox = new BoundingBoxProperties(){
                    Xmin = 0,
                    Ymin = 0,
                    Xmax = 10,
                    Ymax = 10
                }
            };
    spatialPath.SpatialTypes.Add(SpatialType.Point);
    spatialPath.SpatialTypes.Add(SpatialType.LineString);
    spatialPath.SpatialTypes.Add(SpatialType.Polygon);
    spatialPath.SpatialTypes.Add(SpatialType.MultiPolygon);

    containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(spatialPath);

    // Update container with changes
    await client.GetContainer("db", "spatial").ReplaceContainerAsync(containerResponse.Resource);
```

## <a name="geography-data-indexing-examples"></a>Příklady indexování geografických dat

Následující fragment kódu JSON ukazuje zásadu indexování s povoleným prostorovým indexováním pro **zeměpisný** datový typ. Je platný pro prostorová data s geografickými datovými typy a v dokumentech pro prostorový dotazování vyhledá všechny body pro geografické JSON, mnohoúhelníky, více mnohoúhelníky nebo LineString nalezené v dokumentech. Pokud měníte zásady indexování pomocí Azure Portal, můžete pro zásady indexování zadat následující JSON a povolit prostorové indexování v kontejneru:

**JSON zásad indexování kontejneru s geografickým indexováním**

```json
{
    "automatic": true,
    "indexingMode": "Consistent",
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "spatialIndexes": [
        {
            "path": "/*",
            "types": [
                "Point",
                "Polygon",
                "MultiPolygon",
                "LineString"
            ]
        }
    ],
    "excludedPaths": []
}
```

> [!NOTE]
> Pokud je hodnota geografického formátu JSON v dokumentu poškozená nebo neplatná, nebude se indexovat pro prostorové dotazování. Hodnoty umístění můžete ověřit pomocí ST_ISVALID a ST_ISVALIDDETAILED.

[Zásady indexování](how-to-manage-indexing-policy.md) můžete také upravit pomocí rozhraní příkazového řádku Azure CLI, PowerShellu nebo jakékoli sady SDK.

## <a name="geometry-data-indexing-examples"></a>Příklady indexování dat geometrie

S datovým typem **geometrie** , podobně jako zeměpisný datový typ, je nutné zadat relevantní cesty a typy k indexování. Kromě toho je nutné zadat také `boundingBox` v rámci zásad indexování, abyste označili požadovanou oblast, která má být pro danou cestu indexována. Každá geoprostorové cesta vyžaduje svoji vlastní `boundingBox` .

Ohraničovací rámeček se skládá z následujících vlastností:

- **XMin** : minimální souřadnice indexovaných x
- **yMin** : minimální souřadnice indexovaného y
- **Xmax** : maximální indexovaná souřadnice x
- **yMax** : maximální souřadnice indexovaných y

Ohraničovací rámeček je povinný, protože geometrická data zabírají rovinu, která může být nekonečná. Prostorové indexy ale vyžadují omezené místo. Pro **zeměpisný** datový typ je zemina hranice a není nutné nastavovat ohraničovací rámeček.

Vytvořte ohraničující rámeček, který obsahuje všechny (nebo většinu) vašich dat. Prostorové indexy budou moci využívat pouze operace vypočítané na objektech, které jsou zcela uvnitř ohraničujícího pole. Zvětšení rámečku, než je potřeba, bude mít negativní dopad na výkon dotazů.

Tady je příklad zásady indexování, **která indexuje** data s **geospatialConfig** nastavenou na `geometry` :

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
            "path": "/\"_etag\"/?"
        }
    ],
    "spatialIndexes": [
        {
            "path": "/locations/*",
            "types": [
                "Point",
                "LineString",
                "Polygon",
                "MultiPolygon"
            ],
            "boundingBox": {
                "xmin": -10,
                "ymin": -20,
                "xmax": 10,
                "ymax": 20
            }
        }
    ]
}
```

Výše uvedené zásady indexování mají **boundingBox** (-10, 10) souřadnic x a (-20, 20) souřadnic y. Kontejner s výše uvedenými zásadami indexování bude indexovat všechny body, mnohoúhelníky, více mnohoúhelníky a LineStrings, které jsou zcela v rámci této oblasti.

> [!NOTE]
> Pokud se pokusíte přidat zásadu indexování s **boundingBox** do kontejneru s `geography` datovým typem, dojde k selhání. Před přidáním BoundingBox byste měli upravit **geospatialConfig** kontejneru `geometry` . **boundingBox** Můžete přidat data a upravit zbývající část zásad indexování (například cesty a typy) před nebo po výběru geoprostorového datového typu pro kontejner.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak začít se geoprostorové podpory v Azure Cosmos DB, můžete následující:

* Další informace o [Azure Cosmos DB dotaz](sql-query-getting-started.md)
* Další informace o [dotazování prostorových dat pomocí Azure Cosmos DB](sql-query-geospatial-query.md)
* Další informace o [umístění geoprostorového a geografického umístění JSON v Azure Cosmos DB](sql-query-geospatial-intro.md)
