---
title: Příkazy jazyka T-SQL
description: Odkazy na dokumentaci pro příkazy T-SQL podporované v fondu Synapse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 05/01/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 011a850a44948bf5eee28a40d59dcf7672d866e8
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586367"
---
# <a name="t-sql-statements-supported-in-synapse-sql-pool"></a>Příkazy T-SQL podporované v fondu SYNAPSE SQL

Odkazy na dokumentaci pro příkazy T-SQL podporované v fondu Synapse SQL.

## <a name="data-definition-language-ddl-statements"></a>Příkazy jazyka definice dat (DDL)

* [ZMĚNIT DATABÁZI](https://msdn.microsoft.com/library/mt204042.aspx)
* [ALTER INDEX](https://msdn.microsoft.com/library/ms188388.aspx)
* [ZMĚNA ZHMOTNĚNÝ POHLED](/sql/t-sql/statements/alter-materialized-view-transact-sql?view=azure-sqldw-latest) (náhled) 
* [ZMĚNIT POSTUP](https://msdn.microsoft.com/library/ms189762.aspx)
* [ALTER šmejení](https://msdn.microsoft.com/library/ms173423.aspx)
* [ALTER TABLE](https://msdn.microsoft.com/library/ms190273.aspx)
* [VYTVOŘIT INDEX ÚLOŽIŠTĚ SLOUPCŮ](https://msdn.microsoft.com/library/gg492153.aspx)
* [VYTVOŘIT DATABÁZI](https://msdn.microsoft.com/library/mt204021.aspx)
* [Vytvořit pověření s rozsahem databáze](https://msdn.microsoft.com/library/mt270260.aspx)
* [VYTVOŘENÍ EXTERNÍHO ZDROJE DAT](https://msdn.microsoft.com/library/dn935022.aspx)
* [CREATE EXTERNAL FILE FORMAT](https://msdn.microsoft.com/library/dn935026.aspx)
* [VYTVOŘIT EXTERNÍ TABULKU](https://msdn.microsoft.com/library/dn935021.aspx)
* [VYTVOŘIT FUNKCI](https://msdn.microsoft.com/library/mt203952.aspx)
* [CREATE INDEX](https://msdn.microsoft.com/library/ms188783.aspx)
* [VYTVOŘIT ZHMOTněný pohled jako výběr](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest) (náhled) 
* [VYTVOŘIT POSTUP](https://msdn.microsoft.com/library/ms187926.aspx)
* [Vytvořit schéma](https://msdn.microsoft.com/library/ms189462.aspx)
* [VYTVOŘIT STATISTIKU](https://msdn.microsoft.com/library/ms188038.aspx)
* [VYTVOŘIT TABULKU](https://msdn.microsoft.com/library/mt203953.aspx)
* [CREATE TABLE AS SELECT](https://msdn.microsoft.com/library/mt204041.aspx)
* [VYTVOŘIT POHLED](https://msdn.microsoft.com/library/ms187956.aspx)
* [VYTVOŘIT TŘÍDĚNÍ PRACOVNÍHO VYTÍŽENÍ](/sql/t-sql/statements/create-workload-classifier-transact-sql)
* [PŘETAŽENÍ EXTERNÍHO ZDROJE DAT](https://msdn.microsoft.com/library/mt146367.aspx)
* [PŘETAŽENÍ EXTERNÍHO SOUBORU](https://msdn.microsoft.com/library/mt146379.aspx)
* [EXTERNÍ TABULKA PŘETAŽENÍ](https://msdn.microsoft.com/library/mt130698.aspx)
* [DROP INDEX](https://msdn.microsoft.com/library/ms176118.aspx)
* [POSTUP PŘETAŽENÍ](https://msdn.microsoft.com/library/ms174969.aspx)
* [STATISTIKA POKLESU](https://msdn.microsoft.com/library/ms175075.aspx)
* [DROP TABULKA](https://msdn.microsoft.com/library/ms173790.aspx)
* [Schéma DROP](https://msdn.microsoft.com/library/ms186751.aspx)
* [PŘETAŽENÍ POHLEDU](https://msdn.microsoft.com/library/ms173492.aspx)
* [DROP ÚLOHY TŘÍDĚNÍ](/sql/t-sql/statements/drop-workload-classifier-transact-sql)
* [Přejmenovat](https://msdn.microsoft.com/library/mt631611.aspx)
* [SET RESULT_SET_CACHING](/sql/t-sql/statements/set-result-set-caching-transact-sql) 
* [Zkrátit tabulku](https://msdn.microsoft.com/library/ms177570.aspx)
* [UPDATE STATISTICS](https://msdn.microsoft.com/library/ms187348.aspx)

## <a name="data-manipulation-language-dml-statements"></a>Příkazy jazyka pro manipulaci s daty (DML)

* [Odstranit](https://msdn.microsoft.com/library/ms189835.aspx)
* [Vložit](https://msdn.microsoft.com/library/ms174335.aspx)
* [Aktualizace](https://msdn.microsoft.com/library/ms177523.aspx)

## <a name="database-console-commands"></a>Příkazy konzoly databáze

* [DBCC DROPCLEANBUFFERS](https://msdn.microsoft.com/library/ms187762.aspx)
* [DBCC DROPRESULTSETCACHE](/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?view=azure-sqldw-latest) (náhled)
* [DBCC FREEPROCCACHE](https://msdn.microsoft.com/library/mt204018.aspx)
* [DDBCC SMRŠŤOVACÍ PROTOKOL](https://msdn.microsoft.com/library/mt204020.aspx)
* [DBCC PDW_SHOWEXECUTIONPLAN](https://msdn.microsoft.com/library/mt204017.aspx)
* [DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD](/sql/t-sql/database-console-commands/dbcc-pdw-showmaterializedviewoverhead-transact-sql?view=azure-sqldw-latest)
* [DBCC SHOWRESULTCACHESPACEUSED](/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql) (náhled)
* [DBCC PDW_SHOWPARTITIONSTATS](https://msdn.microsoft.com/library/mt204013.aspx)
* [DBCC PDW_SHOWSPACEUSED](https://msdn.microsoft.com/library/mt204028.aspx)
* [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/mt204043.aspx)

## <a name="query-statements"></a>Příkazy dotazu

* [Vyberte](https://msdn.microsoft.com/library/ms189499.aspx)
* [S common_table_expression](https://msdn.microsoft.com/library/ms175972.aspx)
* [Kromě a INTERSECT](https://msdn.microsoft.com/library/ms188055.aspx)
* [Vysvětlit](https://msdn.microsoft.com/library/mt631615.aspx)
* [Z](https://msdn.microsoft.com/library/ms177634.aspx)
* [Použití pivotu a unpivotu](https://msdn.microsoft.com/library/ms177410.aspx)
* [SKUPINA PODLE](https://msdn.microsoft.com/library/ms177673.aspx)
* [HAVING](https://msdn.microsoft.com/library/ms180199.aspx)
* [ORDER BY](https://msdn.microsoft.com/library/ms188385.aspx)
* [Možnost](https://msdn.microsoft.com/library/ms190322.aspx)
* [Unie](https://msdn.microsoft.com/library/ms180026.aspx)
* [Kde](https://msdn.microsoft.com/library/ms188047.aspx)
* [Top](https://msdn.microsoft.com/library/ms189463.aspx)
* [Používání aliasů](https://msdn.microsoft.com/library/mt631614.aspx)
* [Podmínka hledání](https://msdn.microsoft.com/library/ms173545.aspx)
* [Poddotazů](https://msdn.microsoft.com/library/mt631613.aspx)

## <a name="security-statements"></a>Příkazy zabezpečení

* Oprávnění: [GRANT](https://msdn.microsoft.com/library/ms187965.aspx), [DENY](https://msdn.microsoft.com/library/ms188338.aspx), [REVOKE](https://msdn.microsoft.com/library/ms187728.aspx)
* [ZMĚNIT POVOLENÍ](https://msdn.microsoft.com/library/ms187359.aspx)
* [ZMĚNIT CERTIFIKÁT](https://msdn.microsoft.com/library/ms189511.aspx)
* [ZMĚNIT ŠIFROVACÍ KLÍČ DATABÁZE](https://msdn.microsoft.com/library/bb630389.aspx)
* [ZMĚNIT PŘIHLÁŠENÍ](https://msdn.microsoft.com/library/ms189828.aspx)
* [ZMĚNIT HLAVNÍ KLÍČ](https://msdn.microsoft.com/library/ms186937.aspx)
* [ZMĚNIT ROLI](https://msdn.microsoft.com/library/ms189775.aspx)
* [ZMĚNIT UŽIVATELE](https://msdn.microsoft.com/library/ms176060.aspx)
* [ZÁLOŽNÍ CERTIFIKÁT](https://msdn.microsoft.com/library/ms178578.aspx)
* [ZAVŘÍT HLAVNÍ KLÍČ](https://msdn.microsoft.com/library/ms188387.aspx)
* [VYTVOŘIT CERTIFIKÁT](https://msdn.microsoft.com/library/ms187798.aspx)
* [VYTVOŘIT ŠIFROVACÍ KLÍČ DATABÁZE](https://msdn.microsoft.com/library/bb677241.aspx)
* [VYTVOŘIT PŘIHLÁŠENÍ](https://msdn.microsoft.com/library/ms189751.aspx)
* [VYTVOŘIT HLAVNÍ KLÍČ](https://msdn.microsoft.com/library/ms174382.aspx)
* [VYTVOŘIT ROLI](https://msdn.microsoft.com/library/ms187936.aspx)
* [VYTVOŘIT UŽIVATELE](https://msdn.microsoft.com/library/ms173463.aspx)
* [OSVĚDČENÍ O PÁDU](https://msdn.microsoft.com/library/ms179906.aspx)
* [ŠIFROVACÍ KLÍČ DATABÁZE PŘETAŽENÍ](https://msdn.microsoft.com/library/bb630256.aspx)
* [DROP PŘIHLÁŠENÍ](https://msdn.microsoft.com/library/ms188012.aspx)
* [PŘETAŽENÍ HLAVNÍHO KLÍČE](https://msdn.microsoft.com/library/ms180071.aspx)
* [DROP ROLE](https://msdn.microsoft.com/library/ms174988.aspx)
* [DROP UŽIVATEL](https://msdn.microsoft.com/library/ms189438.aspx)
* [OTEVŘÍT HLAVNÍ KLÍČ](https://msdn.microsoft.com/library/ms174433.aspx)

## <a name="next-steps"></a>Další kroky

Další referenční informace naleznete [v tématu Prvky jazyka T-SQL ve fondu Synapse SQL a](sql-data-warehouse-reference-tsql-language-elements.md)Zobrazení [systému ve fondu SYNAPSE SQL](sql-data-warehouse-reference-tsql-system-views.md).
