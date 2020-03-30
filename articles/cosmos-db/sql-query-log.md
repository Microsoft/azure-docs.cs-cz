---
title: PROTOKOL v dotazovacím jazyce Azure Cosmos DB
description: Informace o funkci systému LOG SQL v Azure Cosmos DB vrátit přirozený logaritmus zadaného číselného výrazu
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9213ef03f383dec7109652246411fac154b4a7f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302504"
---
# <a name="log-azure-cosmos-db"></a>LOG (Azure Cosmos DB)
 Vrátí přirozený logaritmus zadaného číselného výrazu.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
LOG (<numeric_expr> [, <base>])  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Je číselný výraz.  
  
*base*  
   Volitelný číselný argument, který nastavuje základ logaritmu.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí číselný výraz.  
  
## <a name="remarks"></a>Poznámky
  
  Ve výchozím nastavení vrátí funkce LOG() přirozený logaritmus. Pomocí volitelného základního parametru můžete změnit základnu logaritmu na jinou hodnotu.  
  
  Přirozený logaritmus je logaritmus k základně **e**, kde **e** je iracionální konstanta přibližně rovna 2,718281828.  
  
  Přirozený logaritmus exponenciálního čísla je samotné číslo: LOG( EXP( n ) = n. A exponenciální přirozenélogarity čísla je samotné číslo: EXP( LOG( n ) ) = n.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad deklaruje proměnnou a vrátí logaritmus hodnotu zadané proměnné (10).  
  
```sql
SELECT LOG(10) AS log  
```  
  
 Zde je sada výsledků.  
  
```json
[{log: 2.3025850929940459}]  
```  
  
 Následující příklad vypočítá `LOG` pro exponent čísla.  
  
```sql
SELECT EXP(LOG(10)) AS expLog  
```  
  
 Zde je sada výsledků.  
  
```json
[{expLog: 10.000000000000002}]  
```  

## <a name="remarks"></a>Poznámky

Tato systémová funkce nebude využívat index.

## <a name="next-steps"></a>Další kroky

- [Matematické funkce Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
