---
title: Klauzule ORDER BY v Azure Cosmos DB
description: Další informace o klauzuli SQL ORDER BY pro Azure Cosmos DB. Použijte SQL jako dotazovací jazyk Azure Cosmos DB JSON.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: tisande
ms.openlocfilehash: 70702ee4a77e8b3c46de4354f3394bca4080d837
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641396"
---
# <a name="order-by-clause-in-azure-cosmos-db"></a>Klauzule ORDER BY v Azure Cosmos DB

Volitelná `ORDER BY` klauzule určuje pořadí řazení výsledků vrácených dotazem.

## <a name="syntax"></a>Syntaxe
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>Argumenty
  
- `<sort_specification>`  
  
   Určuje vlastnost nebo výraz, na kterém se má seřadit sadu výsledků dotazu. Sloupec řazení lze zadat jako název nebo alias vlastnosti.  
  
   Lze zadat více vlastností. Názvy vlastností musí být jedinečné. Posloupnost vlastností řazení `ORDER BY` v klauzuli definuje organizaci seřazené sady výsledků. To znamená, že sada výsledků je seřazena podle první vlastnosti a pak je seřazený seznam podle druhé vlastnosti a tak dále.  
  
   Názvy vlastností odkazované v `ORDER BY` klauzuli musí odpovídat vlastnosti v seznamu select nebo `FROM` vlastnosti definované v kolekci zadané v klauzuli bez jakýchkoli nejasností.  
  
- `<sort_expression>`  
  
   Určuje jednu nebo více vlastností nebo výrazů, na kterých se má seřadit sadu výsledků dotazu.  
  
- `<scalar_expression>`  
  
   Podrobnosti najdete v části [Výrazy Skalární](sql-query-scalar-expressions.md) výrazy.  
  
- `ASC | DESC`  
  
   Určuje, že hodnoty v zadaném sloupci by měly být seřazeny vzestupně nebo sestupně. `ASC`seřadí od nejnižší hodnoty k nejvyšší hodnotě. `DESC`seřadí od nejvyšší hodnoty po nejnižší hodnotu. `ASC`je výchozí pořadí řazení. Hodnoty Null jsou považovány za nejnižší možné hodnoty.  
  
## <a name="remarks"></a>Poznámky  
  
   Klauzule `ORDER BY` vyžaduje, aby zásady indexování obsahovat index pro pole, která jsou seřazena. Runtime dotazu Azure Cosmos DB podporuje řazení podle názvu vlastnosti a ne proti vypočítané vlastnosti. Azure Cosmos DB `ORDER BY` podporuje více vlastností. Chcete-li spustit dotaz s více vlastnostmi ORDER BY, měli byste definovat [složený index](index-policy.md#composite-indexes) v seřazených polích.

> [!Note]
> Pokud vlastnosti, které jsou seřazeny může být nedefinované pro některé dokumenty a chcete načíst v dotazu ORDER BY, je nutné explicitně zahrnout tuto cestu do indexu. Výchozí zásady indexování neumožní načtení dokumentů, kde není definována vlastnost řazení. [Projděte si ukázkové dotazy na dokumenty s některými chybějícími poli](#documents-with-missing-fields).

## <a name="examples"></a>Příklady

Například tady je dotaz, který načítá rodiny ve vzestupném pořadí názvu města bydliště:

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

Následující dotaz načte `id`rodinu s v pořadí podle data vytvoření položky. Položka `creationDate` je číslo představující *čas epochy*nebo uplynulý čas od 1.

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

Kromě toho můžete objednat podle více vlastností. Dotaz, který objednává podle více vlastností, vyžaduje [složený index](index-policy.md#composite-indexes). Zamyslete se nad následujícím dotazem:

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

Tento dotaz načte `id` rodinu ve vzestupném pořadí názvu města. Pokud má více položek stejný název města, `creationDate` bude dotaz sestupněpodle sestupné ho pořadí.

## <a name="documents-with-missing-fields"></a>Dokumenty s chybějícími poli

Dotazy, `ORDER BY` které jsou spuštěny proti kontejnerům s výchozí zásadou indexování, nevrátí dokumenty, kde není definována vlastnost řazení. Pokud chcete zahrnout dokumenty, kde není definována vlastnost řazení, měli byste tuto vlastnost explicitně zahrnout do zásad indexování.

Například zde je kontejner s indexování zásady, která není `"/*"`explicitně obsahovat žádné cesty kromě :

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

Pokud spustíte dotaz, `lastName` který `Order By` obsahuje klauzuli, výsledky budou `lastName` zahrnovat pouze dokumenty, které mají definovanou vlastnost. Nedefinovali jsme explicitně `lastName` zahrnutou cestu, `lastName` takže žádné dokumenty bez a se ve výsledcích dotazu nezobrazí.

Zde je dotaz, `lastName` který seřadí podle dvou dokumentů, `lastName` z nichž jeden nemá definovaný:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

Výsledky zahrnují pouze dokument, který `lastName`má definovaný :

```json
    [
        {
            "id": "AndersenFamily",
            "lastName": "Andersen"
        }
    ]
```

Pokud aktualizujeme zásady indexování kontejneru tak, `lastName`aby explicitně zahrnovaly cestu pro , zahrneme do výsledků dotazu dokumenty s nedefinovanou vlastností řazení. Je nutné explicitně definovat cestu vést k této skalární hodnotu (a ne za ní). Znak v `?` definici cesty v zásadách indexování byste měli použít `lastName` k zajištění explicitního indexování vlastnosti a žádné další vnořené cesty za ní. Pokud `Order By` dotaz používá [složený index](index-policy.md#composite-indexes), výsledky budou vždy obsahovat dokumenty s nedefinovanou vlastností řazení ve výsledcích dotazu.

Zde je ukázka indexování zásady, která umožňuje `lastName` mít dokumenty s nedefinované zobrazí ve výsledcích dotazu:

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

Pokud spusťte stejný dotaz znovu, `lastName` dokumenty, které chybí, se zobrazí jako první ve výsledcích dotazu:

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

Pokud změníte pořadí `DESC`řazení na , `lastName` dokumenty, které chybí, se zobrazí jako poslední ve výsledcích dotazu:

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

## <a name="next-steps"></a>Další kroky

- [Začínáme](sql-query-getting-started.md)
- [Zásady indexování ve službě Azure Cosmos DB](index-policy.md)
- [Klauzule OFFSET LIMIT](sql-query-offset-limit.md)
