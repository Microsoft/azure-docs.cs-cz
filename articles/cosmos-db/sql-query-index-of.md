---
title: INDEX_OF v jazyce pro dotaz na Azure Cosmos DB
description: Přečtěte si o INDEX_OF funkcí SQL systému v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 50e489fdf57398d486b07944782ecbb3fd1d6a43
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350984"
---
# <a name="index_of-azure-cosmos-db"></a>INDEX_OF (Azure Cosmos DB)
 Vrátí počáteční pozici prvního výskytu druhý řetězec výrazu v rámci prvního zadaného řetězcového výrazu nebo -1, pokud není nalezen řetězec.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
INDEX_OF(<str_expr1>, <str_expr2> [, <numeric_expr>])  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr1*  
   Je řetězcový výraz, který má být prohledán.  
  
*str_expr2*  
   Je řetězcový výraz, který se má vyhledat.  

*numeric_expr* Volitelný numerický výraz, který nastaví pozici, kterou bude hledání spouštět. První pozice v *str_expr1* je 0. 
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí hodnotu číselného výrazu.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad vrátí index různých podřetězců uvnitř "abc".  
  
```sql
SELECT INDEX_OF("abc", "ab") AS i1, INDEX_OF("abc", "b") AS i2, INDEX_OF("abc", "c") AS i3 
```  
  
 Tady je sada výsledků.  
  
```json
[{"i1": 0, "i2": 1, "i3": -1}]  
```  

## <a name="next-steps"></a>Další kroky

- [Azure Cosmos DB funkce řetězce](sql-query-string-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
