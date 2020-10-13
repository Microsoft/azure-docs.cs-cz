---
title: Primární, cizí a jedinečné klíče
description: Podpora omezení tabulky v synapse fondu SQL ve službě Azure synapse Analytics
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 562e2cce317d8774ecf72971d53be4f66f9c3da4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85212764"
---
# <a name="primary-key-foreign-key-and-unique-key-in-synapse-sql-pool"></a>Primární klíč, cizí klíč a jedinečný klíč v synapse fondu SQL

Přečtěte si o omezeních tabulek v synapse fondu SQL, včetně primárního klíče, cizího klíče a jedinečného klíče.

## <a name="table-constraints"></a>Omezení tabulky

Synapse fond SQL podporuje tato omezení tabulky: 
- PRIMÁRNÍ klíč se podporuje jenom v případě, že se používají jenom neclusterované a nevynucované.    
- JEDINEČNÉ omezení se podporuje jenom s nevynucovaném využitím.

Pro syntaxi můžete zaškrtnout možnost [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql) and [Create Table](https://docs.microsoft.com/sql/t-sql/statements/create-table-azure-sql-data-warehouse). 

Omezení CIZÍho klíče není ve fondu SQL synapse podporováno.  


## <a name="remarks"></a>Poznámky

Pokud má primární klíč a/nebo jedinečný klíč, může synapse modul SQL Pool generovat optimální plán spouštění pro dotaz.  Všechny hodnoty ve sloupci primárního klíče nebo ve sloupci jedinečné omezení by měly být jedinečné.

Po vytvoření tabulky s primárním klíčem nebo omezením Unique v synapse fondu SQL musí uživatelé zajistit, aby všechny hodnoty v těchto sloupcích byly jedinečné.  Porušení, které by mohlo způsobit, že dotaz vrátí nepřesný výsledek.  Tento příklad ukazuje, jak může dotaz vracet nepřesný výsledek, pokud sloupec primárního klíče nebo jedinečné omezení obsahuje duplicitní hodnoty.  

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

Vytvořte tabulku synapse fondu SQL s primárním klíčem: 

```sql 
CREATE TABLE mytable (c1 INT PRIMARY KEY NONCLUSTERED NOT ENFORCED, c2 INT);
```
Vytvořte tabulku synapse fondu SQL s jedinečným omezením:

```sql
CREATE TABLE t6 (c1 INT UNIQUE NOT ENFORCED, c2 INT);
```

## <a name="next-steps"></a>Další kroky

Po vytvoření tabulek pro fond SQL synapse je dalším krokem načtení dat do tabulky. Kurz načítání najdete v tématu [načtení dat do synapse fondu SQL](load-data-wideworldimportersdw.md).
