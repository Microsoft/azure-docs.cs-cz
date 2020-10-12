---
title: sys.sp_cleanup_data_retention (Transact-SQL) – Azure SQL Edge
description: Informace o použití sys.sp_cleanup_data_retention (Transact-SQL) ve službě Azure SQL Edge
keywords: sys.sp_cleanup_data_retention (Transact-SQL), SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 9c0a6700a476d4f7f875af5373e3c99bc4e25af2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90935757"
---
# <a name="syssp_cleanup_data_retention-transact-sql"></a>sys.sp_cleanup_data_retention (Transact-SQL)

**Platí pro:** Edge Azure SQL

Provede vyčištění zastaralých záznamů z tabulek, které mají povolené zásady uchovávání dat. Další informace najdete v tématu [uchovávání dat](data-retention-overview.md). 

## <a name="syntax"></a>Syntaxe 

```sql
sys.sp_cleanup_data_retention   
    { [@schema_name = ] 'schema_name' },  
    { [@table_name = ] 'table_name' },   
    [ [@rowcount =] rowcount OUTPUT ]    

```

## <a name="arguments"></a>Argumenty  
`[ @schema_name = ] schema_name`    
 Je název vlastnícího schématu pro tabulku, ve které je nutné provést čištění. *schema_name* je povinný parametr typu **musí**.
  
`[ @table_name = ] 'table_name'`    
 Je název tabulky, ve které je nutné provést operaci čištění. *TABLE_NAME* je povinný parametr typu **musí**.

## <a name="output-parameter"></a>Výstupní parametr  

`[ @rowcount = ] rowcount OUTPUT`   
 rowcount je volitelný výstupní parametr, který představuje počet vyčištění záznamů z tabulky. *RowCount* je int.
  
## <a name="permissions"></a>Oprávnění    
 Vyžaduje db_owner oprávnění.

## <a name="next-steps"></a>Další kroky
- [Uchovávání dat a automatické vyprazdňování dat](data-retention-overview.md)
- [Správa historických dat pomocí zásad uchovávání informací](data-retention-cleanup.md) 
- [Povolit a zakázat uchovávání dat](data-retention-enable-disable.md)
