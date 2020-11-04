---
title: Klauzule ORDER BY v Azure Cosmos DB
description: Přečtěte si o klauzuli ORDER BY jazyka SQL pro Azure Cosmos DB. Použijte SQL jako dotazovací jazyk Azure Cosmos DB JSON.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/06/2020
ms.author: tisande
ms.openlocfilehash: 887dc13eb5e351688718d2a221e69499557b23e5
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93338289"
---
# <a name="order-by-clause-in-azure-cosmos-db"></a>Klauzule ORDER BY v Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Volitelná `ORDER BY` klauzule určuje pořadí řazení pro výsledky vrácené dotazem.

## <a name="syntax"></a>Syntaxe
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>Argumenty
  
- `<sort_specification>`  
  
   Určuje vlastnost nebo výraz, pro který se má seřadit sada výsledků dotazu. Sloupec řazení lze zadat jako název nebo alias vlastnosti.  
  
   Lze zadat více vlastností. Názvy vlastností musí být jedinečné. Pořadí vlastností řazení v `ORDER BY` klauzuli definuje organizaci seřazené sady výsledků. To znamená, že sada výsledků je seřazená podle první vlastnosti a pak je seřazená podle druhé vlastnosti, a tak dále.  
  
   Názvy vlastností, na které je odkazováno v `ORDER BY` klauzuli, musí odpovídat buď vlastnosti v seznamu SELECT, nebo vlastnosti definované v kolekci určené v `FROM` klauzuli bez nejednoznačnosti.  
  
- `<sort_expression>`  
  
   Určuje jednu nebo více vlastností nebo výrazů, u kterých se má seřadit sada výsledků dotazu.  
  
- `<scalar_expression>`  
  
   Podrobnosti najdete v části [skalární výrazy](sql-query-scalar-expressions.md) .  
  
- `ASC | DESC`  
  
   Určuje, že hodnoty v zadaném sloupci by měly být seřazené ve vzestupném nebo sestupném pořadí. `ASC` Seřadí z nejnižší hodnoty na nejvyšší hodnotu. `DESC` Seřadí z nejvyšší hodnoty na nejnižší hodnotu. `ASC` je výchozí pořadí řazení. Hodnoty null se považují za nejnižší možné hodnoty.  
  
## <a name="remarks"></a>Poznámky  
  
   `ORDER BY`Klauzule vyžaduje, aby zásady indexování zahrnovaly index pro pole, která se seřadí. Modul runtime dotazu Azure Cosmos DB podporuje řazení proti názvu vlastnosti a nikoli k vypočítaným vlastnostem. Azure Cosmos DB podporuje více `ORDER BY` vlastností. Chcete-li spustit dotaz s více OBJEDNÁVKAmi podle vlastností, měli byste pro pole, která jsou seřazena, definovat [složený index](index-policy.md#composite-indexes) .

> [!Note]
> Pokud jsou seřazené vlastnosti pro některé dokumenty nedefinovány a chcete je načíst v dotazu ORDER BY, je nutné explicitně zahrnout tuto cestu do indexu. Výchozí zásada indexování neumožní načtení dokumentů, kde není definovaná vlastnost Sort. [Přečtěte si příklady dotazů na dokumenty s některými chybějícími poli](#documents-with-missing-fields).

## <a name="examples"></a>Příklady

Tady je příklad dotazu, který načte rodiny ve vzestupném pořadí podle jména rezidentního města:

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
```

Výsledky jsou následující:

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

Následující dotaz načte rodinu `id` s v pořadí podle data vytvoření položky. Položka `creationDate` je číslo představující *epocha čas* nebo uplynulý čas od ledna. 1, 1970 v sekundách.

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.creationDate DESC
```

Výsledky jsou následující:

```json
    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472
      }
    ]
```

Navíc můžete řadit podle více vlastností. Dotaz, který ORDER by s více vlastnostmi vyžaduje [složený index](index-policy.md#composite-indexes). Zamyslete se nad následujícím dotazem:

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

Tento dotaz načte rodinu `id` ve vzestupném pořadí podle názvu města. Pokud má více položek stejný název města, dotaz bude seřazen podle `creationDate` sestupného pořadí.

## <a name="documents-with-missing-fields"></a>Dokumenty s chybějícími poli

Dotazy `ORDER BY` , které se spouštějí u kontejnerů s výchozími zásadami indexování, nebudou vracet dokumenty, u kterých není definovaná vlastnost Sort. Pokud chcete zahrnout dokumenty, kde není definovaná vlastnost Sort, měli byste tuto vlastnost výslovně zahrnout do zásad indexování.

Tady je například kontejner se zásadami indexování, které neobsahují explicitně žádné cesty, kromě `"/*"` :

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

Pokud spustíte dotaz, který obsahuje `lastName` `Order By` klauzuli, budou výsledky obsahovat jenom dokumenty, které mají `lastName` definovanou vlastnost. Nedefinovali jste explicitní zahrnutou cestu `lastName` , takže se žádné dokumenty bez a `lastName` nezobrazí ve výsledcích dotazu.

Tady je dotaz, který se seřadí podle `lastName` dvou dokumentů, z nichž jeden nemá `lastName` definované:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

Výsledky zahrnují pouze dokument, který má definované `lastName` :

```json
    [
        {
            "id": "AndersenFamily",
            "lastName": "Andersen"
        }
    ]
```

Pokud aktualizujete zásady indexování kontejneru tak, aby explicitně zahrnovaly cestu pro `lastName` , budeme do výsledků dotazu zahrnout dokumenty s nedefinovanou vlastností řazení. Je nutné explicitně definovat cestu, která má vést k této skalární hodnotě (a ne i mimo ni). `?`V definici cesty v zásadách indexování byste měli použít znak, abyste se ujistili, že budete explicitně indexovat vlastnost `lastName` a žádné další vnořené cesty nad ní. Pokud `Order By` dotaz používá [složený index](index-policy.md#composite-indexes), výsledky budou vždy zahrnovat dokumenty s nedefinovanou vlastností řazení ve výsledcích dotazu.

Tady je ukázková zásada indexování, která vám umožní mít ve výsledcích dotazu k dispozici dokumenty s nedefinovaným `lastName` :

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/lastName/?"
        },
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

Pokud znovu spustíte stejný dotaz, `lastName` ve výsledcích dotazu se zobrazí nejprve dokumenty, které chybí:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

Výsledky jsou následující:

```json
[
    {
        "id": "WakefieldFamily"
    },
    {
        "id": "AndersenFamily",
        "lastName": "Andersen"
    }
]
```

Změníte-li pořadí řazení na `DESC` , `lastName` zobrazí se v seznamu výsledky dotazu tyto dokumenty jako poslední.

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName DESC
```

Výsledky jsou následující:

```json
[
    {
        "id": "AndersenFamily",
        "lastName": "Andersen"
    },
    {
        "id": "WakefieldFamily"
    }
]
```

> [!Note]
> Pouze sada .NET SDK verze 3.4.0 nebo novější podporuje pořadí podle smíšených typů. Proto pokud chcete seřadit podle kombinace nedefinovaných a definovaných hodnot, měli byste použít tuto verzi (nebo novější).

Pořadí, ve kterém se ve výsledcích zobrazují různé typy, nelze ovládat. V předchozím příkladu jsme ukázali, jak byly nedefinované hodnoty řazeny před řetězcovými hodnotami. Pokud místo toho chcete mít větší kontrolu nad pořadím řazení nedefinovaných hodnot, můžete přiřadit libovolné nedefinované vlastnosti hodnotu řetězce "aaaaaaaaa" nebo "zzzzzzzz", abyste se ujistili, že byly buď jako první, nebo jako poslední.

## <a name="next-steps"></a>Další kroky

- [Začínáme](sql-query-getting-started.md)
- [Zásady indexování ve službě Azure Cosmos DB](index-policy.md)
- [Klauzule LIMIT POSUNu](sql-query-offset-limit.md)
