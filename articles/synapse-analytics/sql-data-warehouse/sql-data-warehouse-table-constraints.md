---
title: Primární, cizí a jedinečné klíče
description: Podpora omezení tabulky ve fondu Synapse SQL ve službě Azure Synapse Analytics
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: f97163d02836442430037e18439bcf0724046332
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80990765"
---
# <a name="primary-key-foreign-key-and-unique-key-in-synapse-sql-pool"></a>Primární klíč, cizí klíč a jedinečný klíč ve fondu SYNAPse SQL

Informace o omezení chodnících tabulky ve fondu Synapse SQL, včetně primárního klíče, cizího klíče a jedinečného klíče.

## <a name="table-constraints"></a>Omezení tabulky

Fond SYNAPSE SQL podporuje tato omezení tabulky: 
- Primární klíč je podporován pouze v případě, že jsou použity neseskupené a nevynucené.    
- Jedinečné omezení je podporováno pouze s není vynuceno se používá.

Syntaxe naleznete v políčku [ZMĚNIT TABULKU](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql) a [VYTVOŘIT TABULKU](https://docs.microsoft.com/sql/t-sql/statements/create-table-azure-sql-data-warehouse). 

Omezení cizího klíče není ve fondu SQL synapse podporováno.  


## <a name="remarks"></a>Poznámky

S primární klíč nebo jedinečný klíč umožňuje synapse SQL fond motoru generovat optimální plán spuštění pro dotaz.  Všechny hodnoty ve sloupci primárního klíče nebo ve sloupci jedinečného omezení by měly být jedinečné.

Po vytvoření tabulky s primárním klíčem nebo jedinečným omezením ve fondu SYNApse SQL musí uživatelé zajistit, aby všechny hodnoty v těchto sloupcích byly jedinečné.  Porušení, které může způsobit dotaz vrátit nepřesný výsledek.  Tento příklad ukazuje, jak může dotaz vrátit nepřesný výsledek, pokud sloupec primárního klíče nebo jedinečného omezení obsahuje duplicitní hodnoty.  

```sql
 -- Create table t1
CREATE TABLE t1 (a1 INT NOT NULL, b1 INT) WITH (DISTRIBUTION = ROUND_ROBIN)

-- Insert values to table t1 with duplicate values in column a1.
INSERT INTO t1 VALUES (1, 100)
INSERT INTO t1 VALUES (1, 1000)
INSERT INTO t1 VALUES (2, 200)
INSERT INTO t1 VALUES (3, 300)
INSERT INTO t1 VALUES (4, 400)

-- Run this query.  No primary key or unique constraint.  4 rows returned. Correct result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
1           2
2           1
3           1
4           1

(4 rows affected)
*/

-- Add unique constraint
ALTER TABLE t1 ADD CONSTRAINT unique_t1_a1 unique (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Incorrect result.
SELECT a1, count(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
4           1
1           1
3           1
1           1

(5 rows affected)
*/

-- Drop unique constraint.
ALTER TABLE t1 DROP CONSTRAINT unique_t1_a1

-- Add primary key constraint
ALTER TABLE t1 add CONSTRAINT PK_t1_a1 PRIMARY KEY NONCLUSTERED (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Incorrect result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
4           1
1           1
3           1
1           1

(5 rows affected)
*/

-- Manually fix the duplicate values in a1
UPDATE t1 SET a1 = 0 WHERE b1 = 1000

-- Verify no duplicate values in column a1 
SELECT * FROM t1

/*
a1          b1
----------- -----------
2           200
3           300
4           400
0           1000
1           100

(5 rows affected)
*/

-- Add unique constraint
ALTER TABLE t1 add CONSTRAINT unique_t1_a1 UNIQUE (a1) NOT ENFORCED  

-- Re-run this query.  5 rows returned.  Correct result.
SELECT a1, COUNT(*) as total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
3           1
4           1
0           1
1           1

(5 rows affected)
*/

-- Drop unique constraint.
ALTER TABLE t1 DROP CONSTRAINT unique_t1_a1

-- Add primary key contraint
ALTER TABLE t1 ADD CONSTRAINT PK_t1_a1 PRIMARY KEY NONCLUSTERED (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Correct result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
3           1
4           1
0           1
1           1

(5 rows affected)
*/

```

## <a name="examples"></a>Příklady

Vytvořte tabulku fondu Synapse SQL s primárním klíčem: 

```sql 
CREATE TABLE mytable (c1 INT PRIMARY KEY NONCLUSTERED NOT ENFORCED, c2 INT);
```
Vytvořte tabulku fondu Synapse SQL s jedinečným omezením:

```sql
CREATE TABLE t6 (c1 INT UNIQUE NOT ENFORCED, c2 INT);
```

## <a name="next-steps"></a>Další kroky

Po vytvoření tabulek pro váš fond Synapse SQL je dalším krokem načtení dat do tabulky. Kurz načítání najdete v [tématu Načítání dat do fondu SQL Synapse](load-data-wideworldimportersdw.md).
