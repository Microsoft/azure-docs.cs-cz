---
title: Dotazování na geoprostorové údaje pomocí Azure Cosmos DB
description: Dotazování prostorových dat pomocí Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: bb9a0351b6f1de47f3687995c65060a23bdb2874
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93336108"
---
# <a name="querying-geospatial-data-with-azure-cosmos-db"></a>Dotazování na geoprostorové údaje pomocí Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Tento článek popisuje, jak zadávat dotazy na geoprostorové údaje v Azure Cosmos DB pomocí jazyka SQL a LINQ. V současné době jsou ukládání a přístup k geoprostorovému datům podporované jenom pomocí Azure Cosmos DBch jenom účtů rozhraní SQL API. Azure Cosmos DB podporuje následující integrované funkce pro geoprostorové dotazování (OGC) Open Geospatial Consortium (). Další informace o kompletní sadě integrovaných funkcí v jazyce SQL naleznete v tématu [Query System Functions in Azure Cosmos DB](sql-query-system-functions.md).

## <a name="spatial-sql-built-in-functions"></a>Integrované funkce prostorového SQL serveru

Tady je seznam geoprostorových systémových funkcí užitečných pro dotazování v Azure Cosmos DB:

|**Použití**|**Popis**|
|---|---|
| ST_DISTANCE (spatial_expr, spatial_expr) | Vrací vzdálenost mezi dvěma LineStringmi výrazy pro bodový odkaz, mnohoúhelník nebo.|
|ST_WITHIN (spatial_expr, spatial_expr) | Vrátí logický výraz, který označuje, zda se první objekt LineString (Point, mnohoúhelník nebo) nachází v rámci druhého objektu. JSON (Point, mnohoúhelník nebo LineString).|
|ST_INTERSECTS (spatial_expr, spatial_expr)| Vrátí logický výraz, který označuje, zda se protínají dva zadané objekty pro určování hodnoty Boolean (Point, mnohoúhelník nebo LineString).|
|ST_ISVALID| Vrací logickou hodnotu označující, zda je zadaný bodový bod JSON, mnohoúhelník nebo výraz LineString platný.|
| ST_ISVALIDDETAILED| Vrátí hodnotu JSON obsahující logickou hodnotu, pokud je platný zadaný bodový bod JSON, mnohoúhelník nebo výraz LineString. Pokud je neplatný, vrátí důvod jako řetězcovou hodnotu.|

Prostorové funkce lze použít k provádění dotazů na Proximity pro prostorová data. Tady je například dotaz, který vrátí všechny dokumenty rodiny, které jsou do 30 km ze zadaného umístění pomocí `ST_DISTANCE` předdefinované funkce.

**Dotaz**

```sql
    SELECT f.id
    FROM Families f
    WHERE ST_DISTANCE(f.location, {"type": "Point", "coordinates":[31.9, -4.8]}) < 30000
```

**Výsledky**

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Pokud zahrnete prostorové indexování do zásad indexování, pak se v indexu budou efektivně obsluhovat dotazy na Distance. Další informace o prostorovém indexování najdete v tématu [geoprostorové indexování](sql-query-geospatial-index.md). Pokud pro zadané cesty nemáte prostorový index, dotaz provede kontrolu kontejneru.

`ST_WITHIN` dá se použít ke kontrole, jestli v mnohoúhelníku leží bod. Běžně se používají mnohoúhelníky, které představují hranice, jako jsou PSČ, hranice státu nebo přirozené formy. Pokud zahrnete prostorové indexování do zásad indexování, pak budou dotazy v rámci v rámci indexu obsluhovány efektivně.

Argumenty mnohoúhelníku v `ST_WITHIN` můžou obsahovat jenom jeden prstenec, to znamená, že mnohoúhelníky nesmí obsahovat v nich otvory.

**Dotaz**

```sql
    SELECT *
    FROM Families f
    WHERE ST_WITHIN(f.location, {
        "type":"Polygon",
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })
```

**Výsledky**

```json
    [{
      "id": "WakefieldFamily",
    }]
```

> [!NOTE]
> Podobně jako v případě, že neodpovídající typy fungují v Azure Cosmos DB dotaz, pokud hodnota umístění zadaná v některém z argumentů je poškozená nebo neplatná, vyhodnotí se jako **nedefinované** a vyhodnocený dokument bude přeskočen z výsledků dotazu. Pokud Váš dotaz nevrátí žádné výsledky, spusťte příkaz `ST_ISVALIDDETAILED` pro ladění, proč je prostorový typ neplatný.
>
>

Azure Cosmos DB také podporuje provádění inverzních dotazů, to znamená, že je možné indexovat mnohoúhelníky nebo čáry v Azure Cosmos DB a pak se dotazovat na oblasti, které obsahují zadaný bod. Tento model se běžně používá v logistikě k identifikaci, například když nákladní vůz vstoupí nebo opustí určenou oblast.

**Dotaz**

```sql
    SELECT *
    FROM Areas a
    WHERE ST_WITHIN({"type": "Point", "coordinates":[31.9, -4.8]}, a.location)
```

**Výsledky**

```json
    [{
      "id": "MyDesignatedLocation",
      "location": {
        "type":"Polygon",
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
      }
    }]
```

`ST_ISVALID` a `ST_ISVALIDDETAILED` lze ji použít ke kontrole, zda je prostorový objekt platný. Například následující dotaz kontroluje platnost bodu s hodnotou zeměpisné šířky v rozsahu (-132,8). `ST_ISVALID` Vrátí pouze logickou hodnotu a `ST_ISVALIDDETAILED` vrátí logickou hodnotu a řetězec obsahující důvod, proč se považuje za neplatnou.

**Dotaz**

```sql
    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })
```

**Výsledky**

```json
    [{
      "$1": false
    }]
```

Tyto funkce lze také použít k ověření mnohoúhelníků. Například zde používáme `ST_ISVALIDDETAILED` k ověření mnohoúhelníku, který není uzavřený.

**Dotaz**

```sql
    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})
```

**Výsledky**

```json
    [{
       "$1": { 
            "valid": false, 
            "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a Polygon must have the same start and end points." 
          }
    }]
```

## <a name="linq-querying-in-the-net-sdk"></a>Dotazování LINQ v sadě .NET SDK

Sada SQL .NET SDK také poskytovatelé zástupné metody `Distance()` a `Within()` pro použití ve výrazech LINQ. Poskytovatel SQL LINQ překládá tuto metodu na ekvivalentní volání integrovaných funkcí SQL (ST_DISTANCE a ST_WITHIN v uvedeném pořadí).

Tady je příklad dotazu LINQ, který vyhledá všechny dokumenty v kontejneru Azure Cosmos, jehož `location` hodnota je v poloměru 30 km zadaného bodu pomocí LINQ.

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

Teď, když jste se naučili, jak začít se geoprostorové podpory v Azure Cosmos DB, můžete následující:

* Další informace o [Azure Cosmos DB dotaz](sql-query-getting-started.md)
* Další informace o [umístění geoprostorového a geografického umístění JSON v Azure Cosmos DB](sql-query-geospatial-intro.md)
* Další informace o [indexování prostorových dat pomocí Azure Cosmos DB](sql-query-geospatial-index.md)
