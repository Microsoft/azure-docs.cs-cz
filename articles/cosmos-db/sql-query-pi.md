---
title: PI v Azure Cosmos DB dotazovací jazyk
description: Přečtěte si informace o systémové funkci SQL PI v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 27832008e8922e339a648985192a58b111555bc9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "71349651"
---
# <a name="pi-azure-cosmos-db"></a>PI (Azure Cosmos DB)
 Vrátí konstantní hodnotu čísla pí.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
PI ()  
```  
   
## <a name="return-types"></a>Návratové typy
  
  Vrátí číselný výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad vrátí hodnotu `PI`.  
  
```sql
SELECT PI() AS pi 
```  
  
 Zde je sada výsledků.  
  
```json
[{"pi": 3.1415926535897931}]  
```  

## <a name="next-steps"></a>Další kroky

- [Matematické funkce Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
