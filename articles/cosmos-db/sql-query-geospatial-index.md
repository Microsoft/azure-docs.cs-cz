---
title: Indexování geoprostorových dat pomocí Azure Cosmos DB
description: Indexace prostorových dat pomocí Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: 2cf682a404154b9c1bb94680b3adb673892c1c72
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/23/2020
ms.locfileid: "77566371"
---
# <a name="index-geospatial-data-with-azure-cosmos-db"></a>Indexování geoprostorových dat pomocí Azure Cosmos DB

Navrhli jsme, aby byl databázový stroj Azure Cosmos DB nezávislá jako skutečně schématu a poskytoval jako první podporu třídy pro JSON. Napsání optimalizovaného databázového stroje Azure Cosmos DB nativně rozumí prostorová data reprezentovaná v rámci standardu injson.

V kostce je geometrie promítnuta z souřadnic Geodetic na 2D rovinu a poté postupně rozdělena do buněk pomocí **quadtree**. Tyto buňky jsou namapovány na 1D na základě umístění buňky v **křivce vyplňování Hilbert prostoru**, která zachovává polohu místa. Kromě toho, když jsou data umístění indexována, projde procesem známým jako **teselaci**, to znamená, že všechny buňky, které protínají umístění, jsou identifikovány a uloženy jako klíče v indexu Azure Cosmos DB. V době zpracování dotazu argumenty, jako je bodů a mnohoúhelníků jsou také teselace sestavy extrahovat ID oblasti relevantní buněk a potom se používá k načtení dat z indexu.

Pokud zadáte zásadu indexování, která zahrnuje prostorový index pro/* (všechny cesty), pak jsou všechna data nalezená v rámci kontejneru indexována pro efektivní prostorové dotazy.

> [!NOTE]
> Azure Cosmos DB podporuje indexování bodů, LineStrings, mnohoúhelníků a více mnohoúhelníků.
>
>

## <a name="geography-data-indexing-examples"></a>Příklady indexování geografických dat

Následující fragment kódu JSON ukazuje zásadu indexování s povoleným prostorovým indexováním pro **zeměpisný** datový typ. Je platný pro prostorová data s geografickými datovými typy a v dokumentech pro prostorový dotazování vyhledá všechny body pro geografické JSON, mnohoúhelníky, více mnohoúhelníky nebo LineString nalezené v dokumentech. Pokud měníte zásady indexování pomocí Azure Portal, můžete pro zásady indexování zadat následující JSON a povolit prostorové indexování v kontejneru:

**JSON zásad indexování kontejneru s geografickým indexováním**

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
> Pokud umístění hodnota GeoJSON v rámci dokumentu je chybný nebo není platný, pak ji nebude indexování pro prostorová dotazování. Můžete ověřit pomocí ST_ISVALID a ST_ISVALIDDETAILED hodnoty umístění.
>
>
>

[Zásady indexování](how-to-manage-indexing-policy.md) můžete také upravit pomocí rozhraní příkazového řádku Azure CLI, PowerShellu nebo jakékoli sady SDK.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak začít pracovat s podporuje geoprostorové funkce ve službě Azure Cosmos DB, dále můžete:

* Další informace o [Azure Cosmos DB dotaz](sql-query-getting-started.md)
* Další informace o [dotazování prostorových dat pomocí Azure Cosmos DB](sql-query-geospatial-query.md)
* Další informace o [umístění geoprostorového a geografického umístění JSON v Azure Cosmos DB](sql-query-geospatial-intro.md)