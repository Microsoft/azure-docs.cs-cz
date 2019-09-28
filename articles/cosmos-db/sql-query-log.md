---
title: Přihlaste se Azure Cosmos DB dotazovací jazyk
description: Přečtěte si o protokolu funkcí systému SQL v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 16f2321eb48afacbc9636b5c0588b1ea3a01a284
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349731"
---
# <a name="log-azure-cosmos-db"></a>PROTOKOL (Azure Cosmos DB)
 Vrátí přirozený logaritmus zadaného číselný výraz.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
LOG (<numeric_expr> [, <base>])  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Je číselný výraz.  
  
*základ*  
   Volitelné číselný argument, který nastaví základ logaritmu.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí hodnotu číselného výrazu.  
  
## <a name="remarks"></a>Poznámky
  
  Ve výchozím nastavení LOG() vrátí přirozený logaritmus. Základ logaritmu na jinou hodnotu můžete změnit pomocí volitelného parametru základní.  
  
  Přirozený logaritmus se logaritmus o základu **e**, kde **e** rovná nenormální – konstanta přibližně 2.718281828.  
  
  Přirozený logaritmus exponenciálního čísla je číslo samotné: LOG (EXP (n)) = n. A exponenciální hodnota přirozeného logaritmu čísla je samotné číslo: EXP (LOG (n)) = n.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad deklaruje proměnnou a Vrátí logaritmus o hodnotu zadanou proměnnou (10).  
  
```sql
SELECT LOG(10) AS log  
```  
  
 Tady je sada výsledků.  
  
```json
[{log: 2.3025850929940459}]  
```  
  
 Následující příklad vypočítá `LOG` pro exponent čísla.  
  
```sql
SELECT EXP(LOG(10)) AS expLog  
```  
  
 Tady je sada výsledků.  
  
```json
[{expLog: 10.000000000000002}]  
```  

## <a name="next-steps"></a>Další kroky

- [Matematické funkce Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
