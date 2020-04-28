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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "78302504"
---
# <a name="log-azure-cosmos-db"></a>PROTOKOL (Azure Cosmos DB)
 Vrátí přirozený logaritmus zadaného číselného výrazu.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
LOG (<numeric_expr> [, <base>])  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Je numerický výraz.  
  
*base*  
   Volitelný numerický argument, který nastavuje základ logaritmu.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí číselný výraz.  
  
## <a name="remarks"></a>Poznámky
  
  Ve výchozím nastavení vrátí LOG () přirozený logaritmus. Základ logaritmu na jinou hodnotu můžete změnit pomocí volitelného základního parametru.  
  
  Přirozený logaritmus je logaritmus základu **e**, kde **e** je Irrational konstanta, která je přibližně rovna hodnotě 2,718281828.  
  
  Přirozený logaritmus exponenciálního čísla je číslo samotné: LOG (EXP (n)) = n. A exponenciální hodnota přirozeného logaritmu čísla je samotné číslo: EXP (LOG (n)) = n.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad deklaruje proměnnou a vrátí hodnotu logaritmu zadané proměnné (10).  
  
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
