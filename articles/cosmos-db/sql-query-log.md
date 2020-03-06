---
title: Přihlaste se Azure Cosmos DB dotazovací jazyk
description: Přečtěte si o funkci LOG SQL System v Azure Cosmos DB, která vrátí přirozený logaritmus zadaného číselného výrazu.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9213ef03f383dec7109652246411fac154b4a7f9
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302504"
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
  
  Přirozený logaritmus je logaritmus základu **e**, kde **e** je Irrational konstanta, která je přibližně rovna hodnotě 2,718281828.  
  
  Přirozený logaritmus čísla exponenciální je číslo samotné: protokolu (EXP (n).) = n. A exponent přirozený logaritmus čísla je číslo samotné: EXP (protokol (n).) = n.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad deklaruje proměnnou a Vrátí logaritmus o hodnotu zadanou proměnnou (10).  
  
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

Tato systémová funkce nebude index využívat.

## <a name="next-steps"></a>Další kroky

- [Matematické funkce Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
