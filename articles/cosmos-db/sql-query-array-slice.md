---
title: ARRAY_SLICE v jazyce pro dotaz na Azure Cosmos DB
description: Přečtěte si o ARRAY_SLICE funkcí SQL systému v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 1df4177bb8b56bc98977af0f5180e8df5affb257
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348546"
---
# <a name="array_slice-azure-cosmos-db"></a>ARRAY_SLICE (Azure Cosmos DB)
 Vrátí část reprezentující výraz pole.
  
## <a name="syntax"></a>Syntaxe
  
```sql
ARRAY_SLICE (<arr_expr>, <num_expr> [, <num_expr>])  
```  
  
## <a name="arguments"></a>Argumenty
  
*arr_expr*  
   Je libovolný výraz pole.  
  
*num_expr*  
   Založený na nule číselný index, na kterém má být pole. Záporné hodnoty slouží k určení počátečního indexu relativnímu k poslední prvek pole tedy -1 odkazy na poslední prvek v poli.  

*num_expr* Volitelný numerický výraz, který nastaví maximální počet prvků ve výsledném poli.    

## <a name="return-types"></a>Návratové typy
  
  Vrátí výraz pole.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad ukazuje, jak získat různé řezy pole pomocí `ARRAY_SLICE`.  
  
```sql
SELECT
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1) AS s1,  
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1) AS s2,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 1) AS s3,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 2) AS s4,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 0) AS s5,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1000) AS s6,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, -100) AS s7      
  
```  
  
 Tady je sada výsledků.  
  
```json
[{  
           "s1": ["strawberries", "bananas"],   
           "s2": ["strawberries"],
           "s3": ["strawberries"],  
           "s4": ["strawberries", "bananas"], 
           "s5": [],
           "s6": ["strawberries", "bananas"],
           "s7": [] 
}]  
```  

## <a name="next-steps"></a>Další kroky

- [Funkce pole Azure Cosmos DB](sql-query-array-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
