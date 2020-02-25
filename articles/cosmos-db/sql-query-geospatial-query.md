---
title: Dotazování na geoprostorové údaje pomocí Azure Cosmos DB
description: Dotazování prostorových dat pomocí Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: 08b12bd9d35aaa61c79d35a55068983cdc0f1b83
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/23/2020
ms.locfileid: "77566319"
---
# <a name="querying-geospatial-data-with-azure-cosmos-db"></a>Dotazování na geoprostorové údaje pomocí Azure Cosmos DB

Tento článek popisuje, jak zadávat dotazy na geoprostorové údaje v Azure Cosmos DB pomocí jazyka SQL a LINQ. V současné době jsou ukládání a přístup k geoprostorovému datům podporované jenom pomocí Azure Cosmos DBch jenom účtů rozhraní SQL API. Azure Cosmos DB podporuje následující předdefinované funkce Otevřít geoprostorové W3c (OGC) pro geoprostorové dotazování. Další informace o kompletní sadě integrovaných funkcí v jazyce SQL naleznete v tématu [Query System Functions in Azure Cosmos DB](sql-query-system-functions.md).

## <a name="spatial-sql-built-in-functions"></a>Prostorové integrované funkce SQL

Tady je seznam geoprostorových systémových funkcí užitečných pro dotazování v Azure Cosmos DB:

|**Použití**|**Popis**|
|---|---|
| ST_DISTANCE (spatial_expr, spatial_expr) | Vrací vzdálenost mezi dvěma GeoJSON bodu mnohoúhelníku či LineString výrazy.|
|ST_WITHIN (spatial_expr, spatial_expr) | Vrací výraz Boolean určující, zda je první objekt GeoJSON (bodu, mnohoúhelník nebo LineString) v rámci druhého objektu GeoJSON (bodu, mnohoúhelník nebo LineString).|
|ST_INTERSECTS (spatial_expr, spatial_expr)| Vrátí hodnotu určující, zda dvě zadané GeoJSON objekty (bodu, mnohoúhelník nebo LineString) intersect logický výraz.|
|ST_ISVALID| Vrátí logickou hodnotu označující, zda je zadaný výraz GeoJSON bodu mnohoúhelníku či LineString platný.|
| ST_ISVALIDDETAILED| Vrátí hodnotu JSON obsahující logickou hodnotu, pokud je platný zadaný bodový bod JSON, mnohoúhelník nebo výraz LineString. Pokud je neplatný, vrátí důvod jako řetězcovou hodnotu.|

Prostorové funkce lze použít k provádění dotazů blízkosti prostorová data. Tady je například dotaz, který vrátí všechny dokumenty rodiny, které jsou do 30 km od zadaného umístění pomocí předdefinované funkce `ST_DISTANCE`.

**Dotaz**

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

Pokud zahrnete prostorového indexování v zásady indexování, pak "vzdálenost dotazy" bude obsluhovat efektivně pomocí indexu. Další informace o prostorovém indexování najdete v tématu [geoprostorové indexování](sql-query-geospatial-index.md). Pokud pro zadané cesty nemáte prostorový index, dotaz provede kontrolu kontejneru.

`ST_WITHIN` lze použít ke kontrole, zda bod v mnohoúhelníku leží. Mnohoúhelníky se běžně používá k reprezentování hranice jako PSČ, hranice stavu nebo fyzických struktur. Znovu zadáte-li prostorového indexování v zásady indexování, pak "v" dotazy bude obsluhovat efektivně pomocí indexu.

Argumenty mnohoúhelníku v `ST_WITHIN` můžou obsahovat jenom jeden prstenec, to znamená, že mnohoúhelníky nesmí obsahovat v nich otvory.

**Dotaz**

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
> Podobně jako v případě, že neodpovídající typy fungují v Azure Cosmos DB dotaz, pokud hodnota umístění zadaná v některém z argumentů je poškozená nebo neplatná, vyhodnotí se jako **nedefinované** a vyhodnocený dokument bude přeskočen z výsledků dotazu. Pokud Váš dotaz nevrátí žádné výsledky, spusťte `ST_ISVALIDDETAILED` pro ladění, proč je prostorový typ neplatný.
>
>

Azure Cosmos DB podporuje také provádí inverzní dotazy, to znamená, můžete indexování mnohoúhelníky nebo řádky ve službě Azure Cosmos DB a pak dotazování v oblastech, které obsahují zadaný bod. Tento model se běžně používá v logistiky k identifikaci, třeba při nákladní vozidlo zadá nebo ji opustí určená oblast.

**Dotaz**

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

`ST_ISVALID` a `ST_ISVALIDDETAILED` lze použít ke kontrole, zda je prostorový objekt platný. Například následující dotaz ověří platnost bod mimo rozsah hodnoty zeměpisné šířky (-132.8). `ST_ISVALID` vrátí pouze logickou hodnotu a `ST_ISVALIDDETAILED` vrátí logickou hodnotu a řetězec obsahující důvod, proč se považuje za neplatnou.

**Dotaz**

```sql
    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })
```

**Results**

```json
    [{
      "$1": false
    }]
```

Tyto funkce lze také ověřit mnohoúhelníku. V tomto příkladu používáme `ST_ISVALIDDETAILED` k ověření mnohoúhelníku, který není uzavřený.

**Dotaz**

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

## <a name="linq-querying-in-the-net-sdk"></a>Dotazování LINQ v sadě .NET SDK

Sada SQL .NET SDK také poskytovatele zástupných procedur `Distance()` a `Within()` pro použití ve výrazech LINQ. Tato metoda převádí zprostředkovatele SQL LINQ volání ekvivalentní předdefinované funkce volání SQL (ST_DISTANCE a ST_WITHIN v uvedeném pořadí).

Tady je příklad dotazu LINQ, který vyhledá všechny dokumenty v kontejneru Azure Cosmos, jehož hodnota `location` je v poloměru 30 km od zadaného bodu pomocí LINQ.

**Dotaz LINQ na vzdálenost**

```csharp
    foreach (UserProfile user in container.GetItemLinqQueryable<UserProfile>(allowSynchronousQueryExecution: true)
        .Where(u => u.ProfileType == "Public" && u.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }
```

Podobně tady je dotaz pro vyhledání všech dokumentů, jejichž `location` je v zadaném poli nebo mnohoúhelníku.

**Dotaz LINQ pro v rámci**

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

Teď, když jste se naučili, jak začít pracovat s podporuje geoprostorové funkce ve službě Azure Cosmos DB, dále můžete:

* Další informace o [Azure Cosmos DB dotaz](sql-query-getting-started.md)
* Další informace o [umístění geoprostorového a geografického umístění JSON v Azure Cosmos DB](sql-query-geospatial-intro.md)
* Další informace o [indexování prostorových dat pomocí Azure Cosmos DB](sql-query-geospatial-index.md)
