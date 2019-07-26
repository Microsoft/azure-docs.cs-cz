---
title: Používání uložených procedur v Azure SQL Data Warehouse | Microsoft Docs
description: Tipy pro implementaci uložených procedur v Azure SQL Data Warehouse pro vývoj řešení.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/02/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 2c12a679ed5f0a1574deb34df8c0151e737d2d01
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479587"
---
# <a name="using-stored-procedures-in-sql-data-warehouse"></a>Používání uložených procedur v SQL Data Warehouse
Tipy pro implementaci uložených procedur v Azure SQL Data Warehouse pro vývoj řešení.

## <a name="what-to-expect"></a>Co očekávat

SQL Data Warehouse podporuje mnoho funkcí T-SQL, které se používají v SQL Server. Důležitější je, že existují určité funkce škálované na více instancí, které můžete použít k maximalizaci výkonu řešení.

Pokud ale chcete zachovat rozsah a výkon SQL Data Warehouse existují i některé funkce a funkce, které mají rozdíly v chování a jiné, které nejsou podporované.


## <a name="introducing-stored-procedures"></a>Představení uložených procedur
Uložené procedury jsou skvělým způsobem, jak zapouzdření kódu SQL; Uložte je blízko do vašich dat v datovém skladu. Uložené procedury mohou vývojářům naplánovat modularizaci svá řešení zapouzdřením kódu do spravovatelných jednotek. usnadnění větší použitelnosti kódu. Každý uložený postup může také přijímat parametry, aby byly ještě flexibilnější.

SQL Data Warehouse poskytuje zjednodušenou a zjednodušenou implementaci uložených procedur. Největší rozdíl v porovnání s SQL Server je, že uložená procedura není předem zkompilován kód. V datových skladech je čas kompilace malý v porovnání s časem potřebným ke spouštění dotazů na velké objemy dat. Je důležité zajistit, aby byl kód uložené procedury správně optimalizován pro velké dotazy. Cílem je ušetřit hodiny, minuty a sekundy, ne milisekundy. Je proto vhodnější si představit uložené procedury jako kontejnery pro SQL logiku.     

Když SQL Data Warehouse spustí uloženou proceduru, příkazy SQL jsou analyzovány, přeloženy a optimalizovány za běhu. Během tohoto procesu se každý příkaz převede na distribuované dotazy. Kód SQL, který je proveden na základě dat, je jiný než odeslaný dotaz.

## <a name="nesting-stored-procedures"></a>Vnořování uložených procedur
Když uložené procedury volají jiné uložené procedury nebo spustí dynamický příkaz SQL, pak je vnitřní uložená procedura nebo volání kódu označována jako vnořená.

SQL Data Warehouse podporuje maximálně osm úrovní vnoření. To se mírně liší od SQL Server. Úroveň vnoření v SQL Server je 32.

Volání uložené procedury na nejvyšší úrovni je rovno vnořené úrovni 1.

```sql
EXEC prc_nesting
```
Pokud uložená procedura také provede další volání EXEC, úroveň vnořování se zvýší na 2.

```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```
Pokud druhý postup následně provede nějaký dynamický SQL, úroveň vnořování se zvýší na tři.

```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

Všimněte si, SQL Data Warehouse v současné době [nepodporuje@NESTLEVEL@](/sql/t-sql/functions/nestlevel-transact-sql). Je nutné sledovat úroveň vnoření. Je pravděpodobné, že byste překročili osm omezení na úrovni vnoření, ale pokud to uděláte, budete muset znovu pracovat svůj kód, aby odpovídal úrovním vnoření v rámci tohoto limitu.

## <a name="insertexecute"></a>VLOŽIT.. SPUSTIT
SQL Data Warehouse neumožňuje využívat sadu výsledků uložené procedury pomocí příkazu INSERT. Existuje však alternativní přístup, který můžete použít. Příklad najdete v článku o [dočasných tabulkách](sql-data-warehouse-tables-temporary.md). 

## <a name="limitations"></a>Omezení
Existují některé aspekty uložených procedur v jazyce Transact-SQL, které nejsou implementovány v SQL Data Warehouse.

Jsou to tyto:

* dočasné uložené procedury
* číslované uložené procedury
* rozšířené uložené procedury
* Uložené procedury CLR
* možnost šifrování
* možnost replikace
* parametry s hodnotou tabulky
* parametry jen pro čtení
* výchozí parametry
* kontexty spuštění
* Return – příkaz

## <a name="next-steps"></a>Další postup
Další tipy pro vývoj najdete v tématu [Přehled vývoje](sql-data-warehouse-overview-develop.md).

