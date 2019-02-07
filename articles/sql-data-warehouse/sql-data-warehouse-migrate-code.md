---
title: Migrace kódu SQL do služby SQL Data Warehouse | Dokumentace Microsoftu
description: Tipy pro vývoj řešení migrace kódu SQL do služby Azure SQL Data Warehouse.
services: sql-data-warehouse
author: jrowlandjones
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: jrj
ms.reviewer: igorstan
ms.openlocfilehash: 14b3d62235cfcc8bbc8a929757a16cf99b860753
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55815757"
---
# <a name="migrate-your-sql-code-to-sql-data-warehouse"></a>Migrace kódu SQL do služby SQL Data Warehouse
Tento článek vysvětluje změny kódu, bude pravděpodobně nutné provést při migraci vašeho kódu z jiné databáze do SQL Data Warehouse. Některé funkce SQL Data Warehouse může výrazně zlepšit výkon, jako jsou navrženy pro práci v distribuované způsobem. Ale pokud chcete zachovat výkon a škálování, některé funkce nejsou také k dispozici.

## <a name="common-t-sql-limitations"></a>Běžné omezení jazyka T-SQL
Následující seznam shrnuje nejběžnějším funkcím, které SQL Data Warehouse nepodporuje. Pomocí odkazů můžete přejít do řešení pro nepodporované funkce:

* [Spojení standardu ANSI na aktualizace][ANSI joins on updates]
* [Spojení standardu ANSI na odstranění][ANSI joins on deletes]
* [příkaz Merge][merge statement]
* spojování napříč databázemi
* [Kurzory][cursors]
* [INSERT..EXEC][INSERT..EXEC]
* klauzuli Output
* vložené funkce definované uživatelem
* vícepříkazové funkce
* běžné tabulkové výrazy
* [rekurzivní běžné tabulkové výrazy (CTE)] (#Recursive-common-table-expressions-(CTE)
* Funkce modulu CLR a postupy
* $partition – funkce
* Tabulka proměnných
* parametry s hodnotou tabulky
* Distribuované transakce
* potvrzení / vrácení práce
* Uložit transakce
* kontexty spuštění (EXECUTE AS)
* [Seskupit klauzule se zahrnutím / datové krychle / seskupení sady možností][group by clause with rollup / cube / grouping sets options]
* [úrovní vnoření nad rámec 8][nesting levels beyond 8]
* [Aktualizuje se prostřednictvím zobrazení][updating through views]
* [použití vybrat pro přiřazení proměnné][use of select for variable assignment]
* [žádná maximální datový typ pro dynamické řetězců SQL][no MAX data type for dynamic SQL strings]

Naštěstí je možné pracovat většinu těchto omezení kolem. Vysvětlení najdete v článcích relevantní vývoj výše uvedenými.

## <a name="supported-cte-features"></a>Podporované funkce CTE
Běžné tabulkové výrazy (Cte) jsou podporovány jen částečně ve službě SQL Data Warehouse.  Aktuálně jsou podporovány následující funkce CTE:

* CTE lze zadat v příkazu SELECT.
* CTE lze zadat v příkazu CREATE VIEW.
* CTE lze zadat v příkazu vytvoření TABLE AS SELECT (CTAS).
* CTE lze zadat v příkazu vytvoření vzdálené tabulky jako vyberte (CRTAS).
* CTE lze zadat v příkazu vytvoření externí tabulky jako vyberte (CETAS).
* Vzdálené tabulky můžete odkazovat z CTE.
* Externí tabulky můžete odkazovat z CTE.
* Několik definic CTE dotazu lze definovat v CTE.

## <a name="cte-limitations"></a>Omezení CTE
Běžné tabulkové výrazy mají určitá omezení v SQL Data Warehouse, včetně těchto:

* CTE musí být následován znakem jednom příkazu SELECT. Příkaz INSERT, UPDATE, DELETE a MERGE nejsou podporovány.
* Výraz běžné tabulky, který obsahuje odkazy na sebe sama (rekurzivní výraz běžné tabulky) se nepodporuje (viz níže uvedený oddíl).
* Zadání více než jeden s klauzulí v CTE není povoleno. Například pokud CTE_query_definition obsahuje poddotaz, tento poddotaz nemůže obsahovat vnořený s klauzulí, který definuje další CTE.
* Klauzule ORDER BY nelze použít v CTE_query_definition, s výjimkou, pokud je zadána klauzule TOP.
* Při použití CTE v příkazu, který je součástí dávky příkazu dříve, než se musí následovat za středníkem.
* Při použití v příkazech připravil sp_prepare Cte se bude chovat stejně jako jiné příkazy SELECT v PDW. Ale pokud Cte se používají jako součást CETAS připravil sp_prepare, chování můžete odložit z SQL serveru a jiných příkazů PDW kvůli způsob, jakým vazba je implementována pro sp_prepare. Pokud vyberte CTE používá nesprávném sloupci, který neexistuje v CTE odkazy, sp_prepare předá bez detekovaly chybu, že bude vyvolána chyba během sp_execute místo.

## <a name="recursive-ctes"></a>Rekurzivní Cte
Rekurzivní Cte nejsou podporované ve službě SQL Data Warehouse.  Migrace rekurzivní CTE může být poměrně složité a nejlepší je jeho rozdělení na několik kroků. Můžete obvykle použít smyčku a vyplňte dočasné tabulky, jak iterovat rekurzivní dočasné dotazy. Po naplnění dočasnou tabulku můžete vrátí data jako sadu jeden výsledek. Podobný přístup se použil k řešení `GROUP BY WITH CUBE` v [se zahrnutím klauzule group by nebo datové krychle / seskupení sady možností] [ group by clause with rollup / cube / grouping sets options] článku.

## <a name="unsupported-system-functions"></a>Nepodporované systémové funkce
Existují také některé funkce systému, které nejsou podporovány. Mezi hlavní ty, které je obvykle možné použít v datových skladů, patří:

* NEWSEQUENTIALID()
* @@NESTLEVEL()
* @@IDENTITY()
* @@ROWCOUNT()
* ROWCOUNT_BIG
* ERROR_LINE()

Některé z těchto problémů je možné pracovat kolem.

## <a name="rowcount-workaround"></a>@@ROWCOUNT řešení
Chcete-li chybějící podpora jazyků @@ROWCOUNT, vytvořte uloženou proceduru, která načte poslední počet řádků z sys.dm_pdw_request_steps a následné provádění `EXEC LastRowCount` po příkazu DML.

```sql
CREATE PROCEDURE LastRowCount AS
WITH LastRequest as 
(   SELECT TOP 1    request_id
    FROM            sys.dm_pdw_exec_requests
    WHERE           session_id = SESSION_ID()
    AND             resource_class IS NOT NULL
    ORDER BY end_time DESC
),
LastRequestRowCounts as
(
    SELECT  step_index, row_count
    FROM    sys.dm_pdw_request_steps
    WHERE   row_count >= 0
    AND     request_id IN (SELECT request_id from LastRequest)
)
SELECT TOP 1 row_count FROM LastRequestRowCounts ORDER BY step_index DESC
;
```

## <a name="next-steps"></a>Další postup
Úplný seznam všech podporovaných příkazů T-SQL najdete v tématu [témata příkazů jazyka Transact-SQL][Transact-SQL topics].

<!--Image references-->

<!--Article references-->
[ANSI joins on updates]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[ANSI joins on deletes]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[merge statement]: ./sql-data-warehouse-develop-ctas.md#replace-merge-statements
[INSERT..EXEC]: ./sql-data-warehouse-tables-temporary.md#modularizing-code
[Transact-SQL topics]: ./sql-data-warehouse-reference-tsql-statements.md

[cursors]: ./sql-data-warehouse-develop-loops.md
[group by clause with rollup / cube / grouping sets options]: ./sql-data-warehouse-develop-group-by-options.md
[nesting levels beyond 8]: ./sql-data-warehouse-develop-transactions.md
[updating through views]: ./sql-data-warehouse-develop-views.md
[use of select for variable assignment]: ./sql-data-warehouse-develop-variable-assignment.md
[no MAX data type for dynamic SQL strings]: ./sql-data-warehouse-develop-dynamic-sql.md

<!--MSDN references-->

<!--Other Web references-->
