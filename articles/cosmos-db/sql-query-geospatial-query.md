---
title: Dotazování geoprostorových dat pomocí Azure Cosmos DB
description: Dotazování prostorových dat pomocí Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: 08b12bd9d35aaa61c79d35a55068983cdc0f1b83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77566319"
---
# <a name="querying-geospatial-data-with-azure-cosmos-db"></a>Dotazování geoprostorových dat pomocí Azure Cosmos DB

Tento článek se bude zabývat dotazem na geoprostorová data v Azure Cosmos DB pomocí SQL a LINQ. V současné době ukládání a přístup ke geoprostorovým datům je podporována azure cosmos DB SQL API účty jenom. Azure Cosmos DB podporuje následující otevřené geoprostorové konsorcium (OGC) integrované funkce pro geoprostorové dotazování. Další informace o kompletní sadě integrovaných funkcí v jazyce SQL najdete v tématu [Funkce dotazovacího systému v Azure Cosmos DB](sql-query-system-functions.md).

## <a name="spatial-sql-built-in-functions"></a>Vestavěné funkce prostorového SQL

Tady je seznam funkcí geoprostorového systému užitečných pro dotazování v Azure Cosmos DB:

|**Použití**|**Popis**|
|---|---|
| ST_DISTANCE (spatial_expr, spatial_expr) | Vrátí vzdálenost mezi dvěma výrazy GeoJSON Point, Polygon nebo LineString.|
|ST_WITHIN (spatial_expr, spatial_expr) | Vrátí logický výraz označující, zda je první objekt GeoJSON (Point, Polygon nebo LineString) v rámci druhého objektu GeoJSON (Point, Polygon nebo LineString).|
|ST_INTERSECTS (spatial_expr, spatial_expr)| Vrátí logický výraz označující, zda se dva zadané objekty GeoJSON (Point, Polygon nebo LineString) protínají.|
|ST_ISVALID| Vrátí logickou hodnotu označující, zda je zadaný výraz GeoJSON Point, Polygon nebo LineString platný.|
| ST_ISVALIDDETAILED| Vrátí hodnotu JSON obsahující logickou hodnotu, pokud je zadaný výraz GeoJSON Point, Polygon nebo LineString platný. Pokud je neplatný, vrátí důvod jako hodnotu řetězce.|

Prostorové funkce lze použít k provádění bezkontaktních dotazů proti prostorovým datům. Například je zde dotaz, který vrací všechny rodinné dokumenty, které jsou `ST_DISTANCE` do 30 km od zadaného umístění pomocí vestavěné funkce.

**Dotazu**

```sql
    SELECT f.id
    FROM Families f
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000
```

**Results**

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Pokud zahrnete prostorové indexování v zásadách indexování, pak "dotazy na vzdálenost" budou efektivně obsluhovány prostřednictvím indexu. Další informace o prostorovém indexování naleznete v [tématu geoprostorové indexování](sql-query-geospatial-index.md). Pokud nemáte prostorový index pro zadané cesty, dotaz provede prohledání kontejneru.

`ST_WITHIN`lze použít ke kontrole, zda bod leží v polygonu. Běžně polygony se používají k reprezentaci hranice jako PSČ, státní hranice nebo přírodní útvary. Opět platí, že pokud zahrnete prostorové indexování v zásadách indexování, pak "v rámci" dotazy budou obsluhovány efektivně prostřednictvím indexu.

Polygon argumenty `ST_WITHIN` v může obsahovat pouze jeden kroužek, to znamená, že polygony nesmí obsahovat díry v nich.

**Dotazu**

```sql
    SELECT *
    FROM Families f
    WHERE ST_WITHIN(f.location, {
        'type':'Polygon',
        'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })
```

**Results**

```json
    [{
      "id": "WakefieldFamily",
    }]
```

> [!NOTE]
> Podobně jako neodpovídající typy fungují v dotazu Azure Cosmos DB, pokud je hodnota umístění zadaná v argumentu poškozená nebo neplatná, vyhodnotí se na **nedefinovaný** a vyhodnocený dokument, který má být přeskočen z výsledků dotazu. Pokud dotaz vrátí žádné `ST_ISVALIDDETAILED` výsledky, spusťte ladit, proč je prostorový typ neplatný.
>
>

Azure Cosmos DB také podporuje provádění inverzní dotazy, to znamená, že můžete indexovat polygony nebo řádky v Azure Cosmos DB, pak dotaz pro oblasti, které obsahují zadaný bod. Tento vzorec se běžně používá v logistice k identifikaci například při vjezdu nákladního vozidla nebo vyjetého z určeného prostoru.

**Dotazu**

```sql
    SELECT *
    FROM Areas a
    WHERE ST_WITHIN({'type': 'Point', 'coordinates':[31.9, -4.8]}, a.location)
```

**Results**

```json
    [{
      "id": "MyDesignatedLocation",
      "location": {
        "type":"Polygon",
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
      }
    }]
```

`ST_ISVALID`a `ST_ISVALIDDETAILED` lze jej použít ke kontrole, zda je prostorový objekt platný. Například následující dotaz kontroluje platnost bodu s hodnotou šířky mimo rozsah (-132.8). `ST_ISVALID`vrátí pouze logickou hodnotu a `ST_ISVALIDDETAILED` vrátí logickou hodnotu a řetězec obsahující důvod, proč je považován za neplatný.

**Dotazu**

```sql
    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })
```

**Results**

```json
    [{
      "$1": false
    }]
```

Tyto funkce lze také použít k ověření polygonů. Například zde používáme `ST_ISVALIDDETAILED` k ověření polygon, který není uzavřen.

**Dotazu**

```sql
    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})
```

**Results**

```json
    [{
       "$1": { 
            "valid": false, 
            "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a Polygon must have the same start and end points." 
          }
    }]
```

## <a name="linq-querying-in-the-net-sdk"></a>Dotazování LINQ v sdk .NET SDK

Sada SQL .NET SDK také `Distance()` `Within()` poskytovatelí metod se zakázaným inzerováním a pro použití v rámci výrazů LINQ. Zprostředkovatel SQL LINQ překládá volání této metody na ekvivalentní volání vestavěné funkce SQL (ST_DISTANCE a ST_WITHIN).

Tady je příklad dotazu LINQ, který vyhledá všechny dokumenty `location` v kontejneru Azure Cosmos, jehož hodnota je v okruhu 30 km od zadaného bodu pomocí LINQ.

**Dotaz LINQ pro vzdálenost**

```csharp
    foreach (UserProfile user in container.GetItemLinqQueryable<UserProfile>(allowSynchronousQueryExecution: true)
        .Where(u => u.ProfileType == "Public" && u.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }
```

Podobně zde je dotaz pro hledání všech `location` dokumentů, jejichž je v rámci zadaného pole/Polygon.

**LINQ dotaz pro Uvnitř**

```csharp
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

    foreach (UserProfile user in container.GetItemLinqQueryable<UserProfile>(allowSynchronousQueryExecution: true)
         .Where(a => a.Location.Within(rectangularArea)))
    {
        Console.WriteLine("\t" + user);
    }
```

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak začít s geoprostorovou podporou v Azure Cosmos DB, můžete další:

* Další informace o [dotazu Azure Cosmos DB Query](sql-query-getting-started.md)
* Další informace o [geografických prostorových datech a datech o poloze GeoJSON v Azure Cosmos DB](sql-query-geospatial-intro.md)
* Další informace o [indexování prostorových dat pomocí Azure Cosmos DB](sql-query-geospatial-index.md)
