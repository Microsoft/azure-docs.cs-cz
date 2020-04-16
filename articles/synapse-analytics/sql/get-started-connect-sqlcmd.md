---
title: Připojení k Synapse SQL pomocí sqlcmd
description: Pomocí nástroje příkazového řádku sqlcmd se můžete připojit k sql on-demand (preview) a fondu SQL a dotazovat se ho na vyžádání.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 8ff9034e6c31c8d95e862570e3962990dfec8442
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423751"
---
# <a name="connect-to-synapse-sql-with-sqlcmd"></a>Připojení k Synapse SQL s sqlcmd

> [!div class="op_single_selector"]
>
> * [Azure Data Studio (preview)](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [Sqlcmd](../sql/get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)

Nástroj příkazového řádku [sqlcmd](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) můžete použít k připojení a dotazování SQL na vyžádání (náhled) a fondu SQL v rámci Synapse SQL.  

## <a name="1-connect"></a>1. Připojte
Chcete-li začít s [sqlcmd](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), otevřete příkazový řádek a zadejte **sqlcmd** následovaný připojovacím řetězcem pro databázi Synapse SQL. Připojovací řetězec bude muset mít následující parametry:

* **Server (-S):** Server v následující podobě: `<`název serveru`>`.database.windows.net
* **Databáze (-d):** Název databáze
* **Povolit uvozovky (-I):** Identifikátory v uvozovkách musí být povoleny pro připojení k instanci Synapse SQL.

Chcete-li použít ověřování serveru SQL Server, je třeba přidat parametry uživatelského jména a hesla:

* **User (-U):** Uživatel serveru v následující podobě: `<`Uživatel`>`
* **Heslo (-P):** Heslo přidružené k uživateli

Připojovací řetězec může vypadat takto:

**SQL na vyžádání**

```sql
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P Enter_Your_Password_Here -I
```

**Fond SQL**

```
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

Chcete-li používat integrované ověřování v Azure Active Directory, je třeba přidat parametry Azure Active Directory:

* **Azure Active Directory Authentication (-G):** Pro ověřování používat Azure Active Directory

Připojovací řetězec může vypadat jako v následujících příkladech:

**SQL na vyžádání**

```
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -G -I
```

**Fond SQL**

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [!NOTE]
> Abyste mohli provádět ověřování pomocí Active Directory, je třeba [povolit ověřování Azure Active Directory](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="2-query"></a>2. Dotaz

### <a name="use-sql-pool"></a>Použití fondu SQL

Po připojení můžete pro instanci zadávat všechny podporované příkazy jazyka Transact-SQL.  V tomto příkladu jsou dotazy odeslány v interaktivním režimu:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

Pro fond SQL následující příklady ukazují, jak spustit dotazy v dávkovém režimu pomocí možnosti -Q nebo potrubí SQL sqlcmd:

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

### <a name="use-sql-on-demand"></a>Použití sql na vyžádání

Po připojení můžete vydat všechny podporované příkazy [Transact-SQL](/sql/t-sql/language-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) (T-SQL) proti instanci.  V následujícím příkladu jsou dotazy odeslány v interaktivním režimu:

```sql
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P Enter_Your_Password_Here -I
1> SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')
2> GO
3> QUIT
```

Pro SQL na vyžádání, příklady, které následují ukazují, jak spustit dotazy v dávkovém režimu pomocí -Q možnost nebo potrubí SQL sqlcmd:

```sql
sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P 'Enter_Your_Password_Here' -I -Q "SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')"
```

```sql
"SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')" | sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P 'Enter_Your_Password_Here' -I > ./tables.out
```

## <a name="next-steps"></a>Další kroky

Další informace o možnostech sqlcmd naleznete v [dokumentaci sqlcmd](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).
