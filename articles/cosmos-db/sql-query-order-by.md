---
title: Klauzule ORDER by ve službě Azure Cosmos DB
description: Další informace o SQL klauzule ORDER by pro službu Azure Cosmos DB. Použití SQL jako dotazovací jazyk Azure Cosmos DB JSON.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: d0a1ed33d5848c3ed8d5f83af8b320d77fe0dc65
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342473"
---
# <a name="order-by-clause"></a>ORDER BY – klauzule

Volitelná klauzule ORDER BY určuje pořadí řazení pro výsledky vrácené dotazem.

## <a name="syntax"></a>Syntaxe
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>Argumenty
  
- `<sort_specification>`  
  
   Určuje vlastnost nebo výraz, podle kterého chcete řazení sady výsledků dotazu. Sloupec pro řazení lze zadat jako alias názvu nebo vlastnosti.  
  
   Je možné zadat víc vlastností. Názvy vlastností musí být jedinečný. Posloupnost vlastnosti řazení v klauzuli ORDER by definuje organizace sady seřazených výsledků. To znamená sada výsledků je seřazený podle první vlastnost a potom tuto seřazený seznam je seřazen podle druhý vlastnosti a tak dále.  
  
   Názvy vlastností, které odkazuje v klauzuli ORDER by musí odpovídat vlastnosti v seznamu select nebo vlastnosti definované v kolekci specifikované v klauzuli FROM bez jakékoli nejednoznačnosti.  
  
- `<sort_expression>`  
  
   Určuje jeden nebo více vlastností nebo výrazy, na kterém se má seřadit sady výsledků dotazu.  
  
- `<scalar_expression>`  
  
   Zobrazit [skalární výrazy](sql-query-scalar-expressions.md) podrobné informace.  
  
- `ASC | DESC`  
  
   Určuje, zda mají být řazeny hodnot v zadaném sloupci ve vzestupném nebo sestupném pořadí. ASC řadí od nejnižší hodnoty po nejvyšší hodnotu. DESC seřadí od nejvyšší hodnotu na nejnižší hodnotu. ASC se použije výchozí pořadí řazení. Hodnoty Null jsou považovány za nejnižší možné hodnoty.  
  
## <a name="remarks"></a>Poznámky  
  
   Klauzule ORDER by vyžaduje zásady indexování index pro pole seřazený. Modul runtime dotazu služby Azure Cosmos DB podporuje řazení proti název vlastnosti a ne vypočítané vlastnosti. Azure Cosmos DB podporuje více vlastností klauzule ORDER BY. Chcete-li spustit dotaz s více vlastnostmi klauzule ORDER BY, byste měli definovat [složeném indexu](index-policy.md#composite-indexes) na seřazený pole.

## <a name="examples"></a>Příklady

Například tady je dotaz, který načte rodiny ve vzestupném pořadí název města rezidenční:

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

Následující dotaz načte řady `id`s v pořadí podle jejich datum vytvoření položky. Položka `creationDate` je představující číslo *unixovém čase*, nebo uplynulý čas od dne do 1, 1970 v řádu sekund.

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

Kromě toho můžete uspořádat podle několika vlastností. Dotaz, který seřadí podle několika vlastností vyžaduje [složeném indexu](index-policy.md#composite-indexes). Vezměte v úvahu následující dotaz:

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

Tento dotaz načte rodině `id` ve vzestupném pořadí název města. Pokud více položek mají stejný název města, bude order dotaz `creationDate` v sestupném pořadí.

## <a name="next-steps"></a>Další postup

- [Začínáme](sql-query-getting-started.md)
- [Klauzule SELECT](sql-query-select.md)
- [OFFSET LIMIT clause](sql-query-offset-limit.md)
