---
title: Klauzule ORDER BY v Azure Cosmos DB
description: Přečtěte si o klauzuli ORDER BY jazyka SQL pro Azure Cosmos DB. Použijte SQL jako dotazovací jazyk Azure Cosmos DB JSON.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: tisande
ms.openlocfilehash: b88184be39a41ec42f8fb304a7511073f645f1cb
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2020
ms.locfileid: "77188736"
---
# <a name="order-by-clause-in-azure-cosmos-db"></a>Klauzule ORDER BY v Azure Cosmos DB

Volitelná klauzule ORDER BY určuje pořadí řazení pro výsledky vrácené dotazem.

## <a name="syntax"></a>Syntaxe
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>Argumenty
  
- `<sort_specification>`  
  
   Určuje vlastnost nebo výraz, podle kterého chcete řazení sady výsledků dotazu. Sloupec řazení lze zadat jako název nebo alias vlastnosti.  
  
   Lze zadat více vlastností. Názvy vlastností musí být jedinečné. Pořadí vlastností řazení v klauzuli ORDER BY definuje organizaci seřazené sady výsledků. To znamená sada výsledků je seřazený podle první vlastnost a potom tuto seřazený seznam je seřazen podle druhý vlastnosti a tak dále.  
  
   Názvy vlastností, na které odkazuje klauzule ORDER BY, musí odpovídat buď vlastnosti v seznamu SELECT, nebo vlastnosti definované v kolekci určené v klauzuli FROM bez nejednoznačnosti.  
  
- `<sort_expression>`  
  
   Určuje jednu nebo více vlastností nebo výrazů, u kterých se má seřadit sada výsledků dotazu.  
  
- `<scalar_expression>`  
  
   Podrobnosti najdete v části [skalární výrazy](sql-query-scalar-expressions.md) .  
  
- `ASC | DESC`  
  
   Určuje, zda mají být řazeny hodnot v zadaném sloupci ve vzestupném nebo sestupném pořadí. ASC řadí od nejnižší hodnoty po nejvyšší hodnotu. DESC seřadí od nejvyšší hodnotu na nejnižší hodnotu. ASC se použije výchozí pořadí řazení. Hodnoty Null jsou považovány za nejnižší možné hodnoty.  
  
## <a name="remarks"></a>Poznámky  
  
   Klauzule `ORDER BY` vyžaduje, aby zásady indexování zahrnovaly index pro pole, která se seřadí. Modul runtime dotazu Azure Cosmos DB podporuje řazení proti názvu vlastnosti a nikoli k vypočítaným vlastnostem. Azure Cosmos DB podporuje více vlastností `ORDER BY`. Chcete-li spustit dotaz s více OBJEDNÁVKAmi podle vlastností, měli byste pro pole, která jsou seřazena, definovat [složený index](index-policy.md#composite-indexes) .

> [!Note]
> Pokud jsou seřazené vlastnosti pro některé dokumenty nedefinovány a chcete je načíst v dotazu ORDER BY, je nutné explicitně zahrnout tuto cestu do indexu. Výchozí zásada indexování neumožní načtení dokumentů, kde není definovaná vlastnost Sort. [Přečtěte si příklady dotazů na dokumenty s některými chybějícími poli](#documents-with-missing-fields).

## <a name="examples"></a>Příklady

Tady je příklad dotazu, který načte rodiny ve vzestupném pořadí podle jména rezidentního města:

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
```

Výsledky jsou:

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

Následující dotaz načte rodinu `id`s v pořadí data vytvoření položky. Položka `creationDate` je číslo představující *epocha čas*nebo uplynulý čas od ledna. 1, 1970 v sekundách.

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.creationDate DESC
```

Výsledky jsou:

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

Navíc můžete řadit podle více vlastností. Dotaz, který ORDER by s více vlastnostmi vyžaduje [složený index](index-policy.md#composite-indexes). Vezměte v úvahu následující dotaz:

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

Tento dotaz načte `id` řady ve vzestupném pořadí podle názvu města. Pokud má více položek stejný název města, dotaz bude seřazen podle `creationDate` v sestupném pořadí.

## <a name="documents-with-missing-fields"></a>Dokumenty s chybějícími poli

Dotazy s `ORDER BY`, které se spouštějí u kontejnerů s výchozími zásadami indexování, nebudou vracet dokumenty, u kterých není definovaná vlastnost Sort. Pokud chcete zahrnout dokumenty, kde není definovaná vlastnost Sort, měli byste tuto vlastnost výslovně zahrnout do zásad indexování.

Tady je například kontejner se zásadami indexování, které explicitně nezahrnují žádné cesty kromě `"/*"`:

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

Pokud spustíte dotaz, který obsahuje `lastName` v klauzuli `Order By`, budou výsledky obsahovat jenom dokumenty, které mají definovanou vlastnost `lastName`. Nedefinovali jste explicitní zahrnutou cestu pro `lastName` tak, aby se ve výsledcích dotazu nezobrazovaly žádné dokumenty bez `lastName`.

Tady je dotaz, který se seřadí podle `lastName` ve dvou dokumentech, z nichž jeden není definován `lastName`:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

Výsledky zahrnují pouze dokument, který má definovanou `lastName`:

```json
    [
        {
            "id": "AndersenFamily",
            "lastName": "Andersen"
        }
    ]
```

Pokud aktualizujete zásady indexování kontejneru tak, aby explicitně zahrnovaly cestu pro `lastName`, budeme do výsledků dotazu zahrnout dokumenty s nedefinovanou vlastností řazení. Je nutné explicitně definovat cestu, která má vést k této skalární hodnotě (a ne i mimo ni). V definici cesty v zásadách indexování byste měli použít `?` znak, abyste se ujistili, že explicitně Indexujte vlastnost `lastName` a žádné další vnořené cesty nad ní.

Tady je ukázková zásada indexování, která umožňuje mít ve výsledcích dotazu k dispozici dokumenty s nedefinovaným `lastName`.

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

Pokud znovu spustíte stejný dotaz, ve výsledcích dotazu se zobrazí nejprve dokumenty, které chybí `lastName`:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

Výsledky jsou:

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

Pokud změníte pořadí řazení na `DESC`, zobrazí se v seznamu výsledky dotazu dokumenty, které chybí `lastName`.

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName DESC
```

Výsledky jsou:

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

## <a name="next-steps"></a>Další kroky

- [Začínáme](sql-query-getting-started.md)
- [Zásady indexování ve službě Azure Cosmos DB](index-policy.md)
- [Klauzule LIMIT POSUNu](sql-query-offset-limit.md)
