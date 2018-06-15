---
title: Migrace kódu SQL do SQL Data Warehouse | Microsoft Docs
description: Tipy k migraci kódu SQL Azure SQL Data Warehouse na vývoj řešení.
services: sql-data-warehouse
author: jrowlandjones
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: jrj
ms.reviewer: igorstan
ms.openlocfilehash: b17e8e306c01bef4c58658b35f3a67d0e721633c
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
ms.locfileid: "31527449"
---
# <a name="migrate-your-sql-code-to-sql-data-warehouse"></a>Migrace do SQL Data Warehouse kódu SQL
Tento článek vysvětluje změny kódu, které bude pravděpodobně třeba, aby při migraci kódu z jiné databáze SQL Data Warehouse. Některé funkce SQL Data Warehouse může výrazně zlepšit výkon, jako jsou navrženy pro práci v distribuované způsobem. Ale pokud chcete zachovat, výkonu a možností škálování, některé funkce nejsou také k dispozici.

## <a name="common-t-sql-limitations"></a>Běžné omezení T-SQL
Následující seznam shrnuje nejběžnější funkcí, které SQL Data Warehouse nepodporuje. Uvedené odkazy vedou k řešení nepodporované funkce:

* [ANSI spojení na aktualizace][ANSI joins on updates]
* [ANSI spojení na odstranění][ANSI joins on deletes]
* [příkaz Merge][merge statement]
* spojení mezi databáze
* [Kurzory][cursors]
* [PŘÍKAZ INSERT... EXEC –][INSERT..EXEC]
* klauzuli Output
* vložené uživatelem definované funkce
* vícepříkazové funkce
* [běžných výrazech tabulky](#Common-table-expressions)
* [rekurzivní běžných výrazech tabulky (CTE)] (#Recursive-common-table-expressions-(CTE)
* Funkce CLR a procedury
* $partition – funkce
* proměnné tabulky
* parametry s hodnotou tabulky
* distribuované transakce
* potvrzení / vrácení zpět práce
* Uložit transakce
* kontexty provádění (EXECUTE AS)
* [s kumulativní klauzule Group by / datové krychle / nastaví možnosti seskupení][group by clause with rollup / cube / grouping sets options]
* [vnořených úrovní nad rámec 8][nesting levels beyond 8]
* [aktualizace prostřednictvím zobrazení][updating through views]
* [použití vyberte pro přiřazení proměnné][use of select for variable assignment]
* [žádné maximální datový typ pro dynamické řetězce SQL][no MAX data type for dynamic SQL strings]

Naštěstí můžete být kolem fungovala většinu těchto omezení. Vysvětlení najdete v článcích relevantní vývoj výše uvedené.

## <a name="supported-cte-features"></a>Podporované funkce CTE
Běžných výrazech tabulky (odkazu Cte) jsou podporovány jen částečně v SQL Data Warehouse.  Aktuálně jsou podporovány následující funkce CTE:

* CTE lze zadat v příkazu SELECT.
* CTE lze zadat v příkazu CREATE VIEW.
* CTE lze zadat v příkazu vytvořte tabulku AS vyberte funkce CTAS ().
* CTE lze zadat v příkazu vytvořit vzdálené tabulky AS vyberte (CRTAS).
* CTE lze zadat v příkazu vytvořit externí tabulky AS vyberte (CETAS).
* Vzdálenou tabulku se může odkazovat z CTE.
* Externí tabulku se může odkazovat z CTE.
* Několik definic CTE dotazu může být definován v CTE.

## <a name="cte-limitations"></a>Omezení CTE
Běžných výrazech tabulky mají určitá omezení v SQL Data Warehouse, včetně:

* CTE musí následovat jediný příkaz SELECT. Příkaz INSERT, UPDATE, DELETE a MERGE příkazy nejsou podporovány.
* Výraz běžné tabulky, který obsahuje odkazy na sebe sama (rekurzivní výraz běžné tabulky) nepodporuje (viz níže části).
* Určení více než jeden s klauzulí v CTE není povoleno. Například pokud CTE_query_definition obsahuje poddotaz, že poddotazu nesmí obsahovat vnořený s klauzulí, která definuje jiné CTE.
* Klauzuli ORDER BY nelze použít v CTE_query_definition, s výjimkou, když je zadané klauzuli TOP.
* Pokud CTE se používá v příkazu, který je součástí dávky, příkaz dříve, než se musí následovat středníkem.
* Pokud se použije v příkazech připravené sp_prepare, bude odkazu Cte chovají stejným způsobem jako ostatní příkazů SELECT v PDW. Ale pokud odkazu Cte se používají jako součást CETAS připravené sp_prepare, chování můžete odložit ze systému SQL Server a další příkazy PDW kvůli způsob, jakým vazba je implementována pro sp_prepare. Pokud vyberte odkazů CTE je pomocí nesprávného sloupce, který neexistuje v CTE, sp_prepare předá bez zjišťování chyba, že chyba bude vyvolána při proceduře sp_execute místo.

## <a name="recursive-ctes"></a>Rekurzivního odkazu Cte
Rekurzivního odkazu Cte nejsou podporovány v SQL Data Warehouse.  Migrace rekurzivní CTE může být poněkud složité a proces nejlepší je rozdělit na několik kroků. Obvykle můžete použít smyčku a podle iterace v rekurzivní dotazy dočasné naplňte dočasné tabulky. Jakmile se naplní dočasné tabulky můžete vrátí data jako jednu výslednou sadu. Podobný postup se používá ke vyřešit `GROUP BY WITH CUBE` v [s kumulativní klauzule group by / datové krychle / nastaví možnosti seskupení] [ group by clause with rollup / cube / grouping sets options] článku.

## <a name="unsupported-system-functions"></a>Funkce nepodporované systému
Existují také některé funkce systému, které nejsou podporovány. Mezi hlavní ty, které obvykle je možné použít v datových skladů, patří:

* NEWSEQUENTIALID()
* @@NESTLEVEL()
* @@IDENTITY()
* @@ROWCOUNT()
* ROWCOUNT_BIG
* ERROR_LINE()

Některé z těchto problémů můžete fungovala kolem.

## <a name="rowcount-workaround"></a>@@ROWCOUNT alternativní řešení
Chcete-li vyřešit nedostatečná podpora pro @@ROWCOUNT, vytvoření uložené procedury, která bude načítat poslední počet řádků z sys.dm_pdw_request_steps a potom spusťte `EXEC LastRowCount` po příkaz DML.

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
Úplný seznam všech podporovaných příkazů T-SQL najdete v tématu [Transact-SQL témata][Transact-SQL topics].

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
