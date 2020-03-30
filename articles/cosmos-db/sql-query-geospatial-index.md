---
title: Indexování geoprostorových dat pomocí Azure Cosmos DB
description: Indexování prostorových dat pomocí Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: eb0a2b2778b3217e185b9883def6eaa54674cc5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137899"
---
# <a name="index-geospatial-data-with-azure-cosmos-db"></a>Indexování geoprostorových dat pomocí Azure Cosmos DB

Databázový stroj Azure Cosmos DB jsme navrhli tak, aby byl skutečně agnostický schématu a poskytoval prvotřídní podporu pro JSON. Databázový stroj optimalizovaný pro zápis služby Azure Cosmos DB nativně rozumí prostorovým datům reprezentovaných ve standardu GeoJSON.

Stručně řečeno, geometrie je promítána z geodetických souřadnic na 2D rovinu a poté postupně rozdělena na buňky pomocí **quadtree**. Tyto buňky jsou mapovány na 1D na základě umístění buňky v **rámci křivky plnění prostoru Hilbert**, která zachovává lokalitu bodů. Navíc při indexování dat umístění prochází procesem **označovaným**jako mozaikování , to znamená, že všechny buňky, které protínají umístění, jsou identifikovány a uloženy jako klíče v indexu Azure Cosmos DB. V době dotazu argumenty jako body a polygony jsou také tessellated extrahovat příslušné oblasti ID buňky, pak se používá k načtení dat z indexu.

Pokud zadáte zásadu indexování, která zahrnuje prostorový index pro /* (všechny cesty), pak všechna data nalezená v kontejneru jsou indexována pro efektivní prostorové dotazy.

> [!NOTE]
> Azure Cosmos DB podporuje indexování bodů, linestringů, mnohonoly a multipolygonů
>
>

## <a name="modifying-geospatial-data-type"></a>Změna typu geoprostorových dat

V kontejneru `geospatialConfig` určuje, jak budou indexována geoprostorová data. Měli byste `geospatialConfig` zadat jeden na kontejner: geografie nebo geometrie. Pokud není zadán, `geospatialConfig` bude výchozí typ zeměpisu data. Při úpravě `geospatialConfig`, budou přeindexována všechna existující geoprostorová data v kontejneru.

> [!NOTE]
> Azure Cosmos DB aktuálně podporuje změny geospatialConfig v .NET SDK pouze ve verzích 3.6 a vyšší.
>

Zde je příklad pro úpravu typu `geometry` geoprostorových `geospatialConfig` dat nastavením vlastnosti a přidáním **opojné pole**:

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

Následující úryvek JSON zobrazuje zásadu indexování s povoleným prostorovým indexováním pro datový typ **zeměpisu.** Je platný pro prostorová data s typem dat geografie a bude indexovat všechny GeoJSON point, Polygon, MultiPolygon nebo LineString nalezené v dokumentech pro prostorové dotazování. Pokud upravujete zásady indexování pomocí portálu Azure, můžete zadat následující JSON pro zásady indexování, abyste povolili prostorové indexování v kontejneru:

**Zásady indexování kontejnerů JSON s prostorovým indexováním zeměpisu**

```json
    {
       "automatic":true,
       "indexingMode":"Consistent",
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
       "excludedPaths":[]
    }
```

> [!NOTE]
> Pokud je hodnota umístění GeoJSON v dokumentu poškozená nebo neplatná, nebude indexována pro prostorové dotazování. Hodnoty umístění můžete ověřit pomocí ST_ISVALID a ST_ISVALIDDETAILED.

Zásady [indexování](how-to-manage-indexing-policy.md) můžete také upravit pomocí azure cli, powershellu nebo libovolné sady SDK.

## <a name="geometry-data-indexing-examples"></a>Příklady indexování geometrií

U datového typu **geometrie,** podobně jako datový typ zeměpisu, je nutné zadat příslušné cesty a typy pro indexování. Kromě toho je nutné `boundingBox` také zadat v rámci zásadindexování k označení požadované oblasti, která má být indexována pro danou konkrétní cestu. Každá geoprostorová cesta`boundingBox`vyžaduje svůj vlastní .

Ohraničovací rámeček se skládá z následujících vlastností:

- **xmin**: minimální indexovaná x souřadnice
- **ymin**: minimální indexovaná souřadnice y
- **xmax**: maximální indexovaná souřadnice x
- **ymax**: maximální indexovaná souřadnice y

Ohraničovací rámeček je vyžadován, protože geometrická data zabírají rovinu, která může být nekonečná. Prostorové indexy však vyžadují omezený prostor. Pro **geografický** datový typ je Hranice Země a není nutné nastavovat ohraničovací rámeček.

Měli byste vytvořit ohraničovací rámeček, který obsahuje všechna (nebo většina) dat. Pouze operace vypočítané na objekty, které jsou zcela uvnitř ohraničovacího rámečku budou moci využít prostorový index. Ohraničovací rámeček by neměl být výrazně větší, než je nutné, protože to negativně ovlivní výkon dotazu.

Zde je příklad zásady indexování, která indexuje data `geometry` **geometrie** s **geospatialConfig** nastavenou na :

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

Výše uvedené zásady indexování má **ohraničujícíBox** (-10, 10) pro x souřadnice a (-20, 20) pro souřadnice y. Kontejner s výše uvedenými zásadami indexování bude indexovat všechny body, mnohonoly, multipolygony a linestrings, které jsou zcela v této oblasti.

> [!NOTE]
> Pokud se pokusíte přidat zásady indexování s **opojnýBox** do kontejneru s `geography` datovým typem, se nezdaří. Měli byste upravit **geospatialConfig** kontejneru `geometry` být před přidáním **opojnýBox**. Můžete přidat data a upravit zbytek zásad indexování (například cesty a typy) před nebo po výběru typu geoprostorových dat pro kontejner.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak začít s geoprostorovou podporou v Azure Cosmos DB, můžete další:

* Další informace o [dotazu Azure Cosmos DB Query](sql-query-getting-started.md)
* Další informace o [dotazování prostorových dat pomocí Azure Cosmos DB](sql-query-geospatial-query.md)
* Další informace o [geografických prostorových datech a datech o poloze GeoJSON v Azure Cosmos DB](sql-query-geospatial-intro.md)