---
title: Příkazy jazyka T-SQL
description: Obsahuje odkazy na dokumentaci pro příkazy T-SQL podporované v Azure SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: query
ms.date: 05/01/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 861675941693697a20da189edebe33d91918782c
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685520"
---
# <a name="t-sql-statements-supported-in-azure-sql-data-warehouse"></a>Příkazy T-SQL podporované v Azure SQL Data Warehouse
Obsahuje odkazy na dokumentaci pro příkazy T-SQL podporované v Azure SQL Data Warehouse.

## <a name="data-definition-language-ddl-statements"></a>Příkazy jazyka DDL (Data Definition Language)
* [ALTER DATABASE](https://msdn.microsoft.com/library/mt204042.aspx)
* [ZMĚNIT INDEX](https://msdn.microsoft.com/library/ms188388.aspx)
* [Změnit materializované zobrazení](/sql/t-sql/statements/alter-materialized-view-transact-sql?view=azure-sqldw-latest) (Preview) 
* [ZMĚNIT PROCEDURU](https://msdn.microsoft.com/library/ms189762.aspx)
* [ZMĚNIT SCHÉMA](https://msdn.microsoft.com/library/ms173423.aspx)
* [ALTER TABLE](https://msdn.microsoft.com/library/ms190273.aspx)
* [VYTVOŘIT INDEX COLUMNSTORE](https://msdn.microsoft.com/library/gg492153.aspx)
* [CREATE DATABASE](https://msdn.microsoft.com/library/mt204021.aspx)
* [VYTVOŘIT DATABÁZI S ROZSAHEM POVĚŘENÍ](https://msdn.microsoft.com/library/mt270260.aspx)
* [VYTVOŘIT EXTERNÍ ZDROJ DAT](https://msdn.microsoft.com/library/dn935022.aspx)
* [CREATE EXTERNAL FILE FORMAT](https://msdn.microsoft.com/library/dn935026.aspx)
* [VYTVOŘIT EXTERNÍ TABULKU](https://msdn.microsoft.com/library/dn935021.aspx)
* [VYTVOŘIT FUNKCI](https://msdn.microsoft.com/library/mt203952.aspx)
* [VYTVOŘIT INDEX](https://msdn.microsoft.com/library/ms188783.aspx)
* [Vytvořit materializovaná zobrazení jako SELECT](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest) (Preview) 
* [VYTVOŘIT PROCEDURU](https://msdn.microsoft.com/library/ms187926.aspx)
* [VYTVOŘIT SCHÉMA](https://msdn.microsoft.com/library/ms189462.aspx)
* [CREATE STATISTICS](https://msdn.microsoft.com/library/ms188038.aspx)
* [CREATE TABLE](https://msdn.microsoft.com/library/mt203953.aspx)
* [CREATE TABLE AS SELECT](https://msdn.microsoft.com/library/mt204041.aspx)
* [VYTVOŘIT ZOBRAZENÍ](https://msdn.microsoft.com/library/ms187956.aspx)
* [VYTVOŘIT KLASIFIKÁTOR ÚLOH](/sql/t-sql/statements/create-workload-classifier-transact-sql)
* [VYNECHAT EXTERNÍ ZDROJ DAT](https://msdn.microsoft.com/library/mt146367.aspx)
* [VYNECHAT FORMÁT EXTERNÍHO SOUBORU](https://msdn.microsoft.com/library/mt146379.aspx)
* [VYNECHAT EXTERNÍ TABULKU](https://msdn.microsoft.com/library/mt130698.aspx)
* [DROP INDEX](https://msdn.microsoft.com/library/ms176118.aspx)
* [PROCEDURA DROP](https://msdn.microsoft.com/library/ms174969.aspx)
* [VYŘADIT STATISTIKU](https://msdn.microsoft.com/library/ms175075.aspx)
* [ODKLÁDACÍ TABULKA](https://msdn.microsoft.com/library/ms173790.aspx)
* [ZRUŠIT SCHÉMA](https://msdn.microsoft.com/library/ms186751.aspx)
* [ODKLÁDACÍ ZOBRAZENÍ](https://msdn.microsoft.com/library/ms173492.aspx)
* [ZRUŠIT TŘÍDĚNÍ ÚLOH](/sql/t-sql/statements/drop-workload-classifier-transact-sql)
* [Změňte](https://msdn.microsoft.com/library/mt631611.aspx)
* [NASTAVENÍ RESULT_SET_CACHING](/sql/t-sql/statements/set-result-set-caching-transact-sql) 
* [TRUNCATE TABLE](https://msdn.microsoft.com/library/ms177570.aspx)
* [UPDATE STATISTICS](https://msdn.microsoft.com/library/ms187348.aspx)

## <a name="data-manipulation-language-dml-statements"></a>Příkazy jazyka DML (data remanipulace)
* [DELETE](https://msdn.microsoft.com/library/ms189835.aspx)
* [INSERT](https://msdn.microsoft.com/library/ms174335.aspx)
* [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx)

## <a name="database-console-commands"></a>Příkazy konzole databáze
* [DBCC DROPCLEANBUFFERS](https://msdn.microsoft.com/library/ms187762.aspx)
* [DBCC DROPRESULTSETCACHE](/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?view=azure-sqldw-latest) (Preview)
* [DBCC FREEPROCCACHE](https://msdn.microsoft.com/library/mt204018.aspx)
* [DBCC SHRINKLOG](https://msdn.microsoft.com/library/mt204020.aspx)
* [DBCC PDW_SHOWEXECUTIONPLAN](https://msdn.microsoft.com/library/mt204017.aspx)
* [DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD](/sql/t-sql/database-console-commands/dbcc-pdw-showmaterializedviewoverhead-transact-sql?view=azure-sqldw-latest)
* [DBCC SHOWRESULTCACHESPACEUSED](/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql) (Preview)
* [DBCC PDW_SHOWPARTITIONSTATS](https://msdn.microsoft.com/library/mt204013.aspx)
* [DBCC PDW_SHOWSPACEUSED](https://msdn.microsoft.com/library/mt204028.aspx)
* [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/mt204043.aspx)

## <a name="query-statements"></a>Příkazy dotazů
* [VYBRALI](https://msdn.microsoft.com/library/ms189499.aspx)
* [S common_table_expression](https://msdn.microsoft.com/library/ms175972.aspx)
* [EXCEPT a INTERSECT](https://msdn.microsoft.com/library/ms188055.aspx)
* [ČÁSTECH](https://msdn.microsoft.com/library/mt631615.aspx)
* [Výsledkem](https://msdn.microsoft.com/library/ms177634.aspx)
* [Použití PIVOTu a UNPIVOT](https://msdn.microsoft.com/library/ms177410.aspx)
* [SESKUPIT PODLE](https://msdn.microsoft.com/library/ms177673.aspx)
* [MAJÍCÍ](https://msdn.microsoft.com/library/ms180199.aspx)
* [ŘADIT PODLE](https://msdn.microsoft.com/library/ms188385.aspx)
* [OPTION](https://msdn.microsoft.com/library/ms190322.aspx)
* [SJEDNOCOVACÍ](https://msdn.microsoft.com/library/ms180026.aspx)
* [,](https://msdn.microsoft.com/library/ms188047.aspx)
* [VRCHOL](https://msdn.microsoft.com/library/ms189463.aspx)
* [Aliasing](https://msdn.microsoft.com/library/mt631614.aspx)
* [Podmínka vyhledávání](https://msdn.microsoft.com/library/ms173545.aspx)
* [Poddotazy](https://msdn.microsoft.com/library/mt631613.aspx)

## <a name="security-statements"></a>Příkazy zabezpečení
* Oprávnění: [grant](https://msdn.microsoft.com/library/ms187965.aspx), [Deny](https://msdn.microsoft.com/library/ms188338.aspx), [REVOKE](https://msdn.microsoft.com/library/ms187728.aspx)
* [ZMĚNIT AUTORIZACI](https://msdn.microsoft.com/library/ms187359.aspx)
* [ZMĚNIT CERTIFIKÁT](https://msdn.microsoft.com/library/ms189511.aspx)
* [ZMĚNIT ŠIFROVACÍ KLÍČ DATABÁZE](https://msdn.microsoft.com/library/bb630389.aspx)
* [ZMĚNIT PŘIHLAŠOVACÍ ÚDAJE](https://msdn.microsoft.com/library/ms189828.aspx)
* [ZMĚNIT HLAVNÍ KLÍČ](https://msdn.microsoft.com/library/ms186937.aspx)
* [ZMĚNIT ROLI](https://msdn.microsoft.com/library/ms189775.aspx)
* [ZMĚNIT UŽIVATELE](https://msdn.microsoft.com/library/ms176060.aspx)
* [ZÁLOŽNÍ CERTIFIKÁT](https://msdn.microsoft.com/library/ms178578.aspx)
* [ZAVŘÍT HLAVNÍ KLÍČ](https://msdn.microsoft.com/library/ms188387.aspx)
* [VYTVOŘIT CERTIFIKÁT](https://msdn.microsoft.com/library/ms187798.aspx)
* [VYTVOŘIT ŠIFROVACÍ KLÍČ DATABÁZE](https://msdn.microsoft.com/library/bb677241.aspx)
* [VYTVOŘIT PŘIHLAŠOVACÍ ÚDAJE](https://msdn.microsoft.com/library/ms189751.aspx)
* [VYTVOŘIT HLAVNÍ KLÍČ](https://msdn.microsoft.com/library/ms174382.aspx)
* [VYTVOŘIT ROLI](https://msdn.microsoft.com/library/ms187936.aspx)
* [VYTVOŘIT UŽIVATELE](https://msdn.microsoft.com/library/ms173463.aspx)
* [VYŘADIT CERTIFIKÁT](https://msdn.microsoft.com/library/ms179906.aspx)
* [VYŘADIT ŠIFROVACÍ KLÍČ DATABÁZE](https://msdn.microsoft.com/library/bb630256.aspx)
* [ZRUŠIT PŘIHLÁŠENÍ](https://msdn.microsoft.com/library/ms188012.aspx)
* [ZRUŠIT HLAVNÍ KLÍČ](https://msdn.microsoft.com/library/ms180071.aspx)
* [ROLE ZRUŠENÍ](https://msdn.microsoft.com/library/ms174988.aspx)
* [VYŘADIT UŽIVATELE](https://msdn.microsoft.com/library/ms189438.aspx)
* [OTEVŘÍT HLAVNÍ KLÍČ](https://msdn.microsoft.com/library/ms174433.aspx)

## <a name="next-steps"></a>Další kroky
Další referenční informace najdete v tématu [prvky jazyka T-SQL v Azure SQL Data Warehouse](sql-data-warehouse-reference-tsql-language-elements.md)a [systémová zobrazení v Azure SQL Data Warehouse](sql-data-warehouse-reference-tsql-system-views.md).
