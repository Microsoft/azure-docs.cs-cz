---
title: IS_DEFINED v dotazovacím jazyce Azure Cosmos DB
description: Přečtěte si o funkcích systému SQL IS_DEFINED v Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a5fcaf52d8e9e6b942a95f0b0c43f3f654c5d5d2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "78303847"
---
# <a name="is_defined-azure-cosmos-db"></a>IS_DEFINED (Azure Cosmos DB)
 Vrátí logickou hodnotu, která znamená, zda byla vlastnost přiřazena hodnota.  
  
## <a name="syntax"></a>Syntaxe
  
```sql
IS_DEFINED(<expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*výrazu*  
   Je libovolný výraz.  
  
## <a name="return-types"></a>Návratové typy
  
  Vrátí logický výraz.  
  
## <a name="examples"></a>Příklady
  
  Následující příklad zkontroluje přítomnost vlastnosti v rámci zadaného dokumentu JSON. První vrátí hodnotu true, protože je přítomen znak "a", ale druhý vrátí hodnotu false, protože chybí "b".  
  
```sql
SELECT IS_DEFINED({ "a" : 5 }.a) AS isDefined1, IS_DEFINED({ "a" : 5 }.b) AS isDefined2 
```  
  
 Zde je sada výsledků.  
  
```json
[{"isDefined1":true,"isDefined2":false}]  
```  

## <a name="remarks"></a>Poznámky

Tato systémová funkce bude využívat výhod [indexu rozsahu](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Další kroky

- [Funkce pro kontrolu typu Azure Cosmos DB](sql-query-type-checking-functions.md)
- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do služby Azure Cosmos DB](introduction.md)
