---
title: Klauzule ORDER BY v Azure Cosmos DB
description: Přečtěte si o klauzuli ORDER BY jazyka SQL pro Azure Cosmos DB. Použijte SQL jako dotazovací jazyk Azure Cosmos DB JSON.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 5cae2bdd7d1f2f26e626c81ea95d2cee3cc8ae13
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444793"
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
  
   Zobrazit [skalární výrazy](sql-query-scalar-expressions.md) podrobné informace.  
  
- `ASC | DESC`  
  
   Určuje, zda mají být řazeny hodnot v zadaném sloupci ve vzestupném nebo sestupném pořadí. ASC řadí od nejnižší hodnoty po nejvyšší hodnotu. DESC seřadí od nejvyšší hodnotu na nejnižší hodnotu. ASC se použije výchozí pořadí řazení. Hodnoty Null jsou považovány za nejnižší možné hodnoty.  
  
## <a name="remarks"></a>Poznámky  
  
   Klauzule ORDER BY vyžaduje, aby zásady indexování zahrnovaly index pro pole, která se seřadí. Modul runtime dotazu Azure Cosmos DB podporuje řazení proti názvu vlastnosti a nikoli k vypočítaným vlastnostem. Azure Cosmos DB podporuje více pořadí podle vlastností. Chcete-li spustit dotaz s více OBJEDNÁVKAmi podle vlastností, měli byste pro pole, která jsou seřazena, definovat [složený index](index-policy.md#composite-indexes) .
   
> [!Note] 
> Pokud používáte sadu .NET SDK 3.4.0 nebo vyšší, mohou být v případě, že jsou vlastnosti seřazené proti, pro některé dokumenty nedefinovány, nutné pro tyto vlastnosti explicitně vytvořit index. Výchozí zásada indexování neumožní načtení dokumentů, kde není definovaná vlastnost Sort.

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

Následující dotaz načte rodinu `id`s v pořadí data vytvoření položky. Položka `creationDate` je číslo představující *epocha čas*nebo uplynulý čas od ledna. 1, 1970 v sekundách.

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

Tento dotaz načte `id` řady ve vzestupném pořadí podle názvu města. Pokud má více položek stejný název města, dotaz bude seřazen podle `creationDate` v sestupném pořadí.

## <a name="next-steps"></a>Další kroky

- [Začínáme](sql-query-getting-started.md)
- [Klauzule SELECT](sql-query-select.md)
- [Klauzule LIMIT POSUNu](sql-query-offset-limit.md)
