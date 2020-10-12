---
title: Přihlaste se Azure Cosmos DB dotazovací jazyk
description: Přečtěte si o funkci LOG SQL System v Azure Cosmos DB, která vrátí přirozený logaritmus zadaného číselného výrazu.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 74d07076fb322214348d52fe65dbc98bdff3cd44
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88798257"
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

  Tato systémová funkce nebude index využívat.
  
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

## <a name="next-steps"></a>Další kroky

- [Matematické funkce Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do služby Azure Cosmos DB](introduction.md)
