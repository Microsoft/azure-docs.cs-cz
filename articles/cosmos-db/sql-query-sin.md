---
title: SIN v Azure Cosmos DB dotazovací jazyk
description: Přečtěte si o funkci systému SQL SIN v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 25e7cf66fdd55a0b641c35443e38b0a67cbe365d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "78303099"
---
# <a name="sin-azure-cosmos-db"></a>SIN (Azure Cosmos DB)
 Vrátí trigonometrický sinus zadaného úhlu v radiánech v zadaném výrazu.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
SIN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Je numerický výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí číselný výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad vypočte `SIN` úhel zadaného úhlu.  
  
```sql
SELECT SIN(45.175643) AS sin  
```  
  
 Zde je sada výsledků.  
  
```json
[{"sin": 0.929607286611012}]  
```  

## <a name="remarks"></a>Poznámky

Tato systémová funkce nebude index využívat.

## <a name="next-steps"></a>Další kroky

- [Matematické funkce Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do služby Azure Cosmos DB](introduction.md)
