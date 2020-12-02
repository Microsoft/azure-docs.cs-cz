---
title: Připojení k synapse SQL pomocí sqlcmd
description: Pomocí nástroje příkazového řádku Sqlcmd se můžete připojit k fondu SQL a vyhrazenému fondu SQL bez serveru a dotazování.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 88ee95789bdc1c6ee9884a021067318caab203d4
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96451645"
---
# <a name="connect-to-synapse-sql-with-sqlcmd"></a>Připojení k synapse SQL pomocí sqlcmd

> [!div class="op_single_selector"]
> * [Azure Data Studio)](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [sqlcmd](../sql/get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)

Nástroj příkazového řádku [Sqlcmd](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) můžete použít k připojení a dotazování fondu SQL bez serveru a vyhrazeného fondu SQL v synapse SQL.  

## <a name="1-connect"></a>1. připojení
Chcete-li začít s nástrojem [Sqlcmd](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), otevřete příkazový řádek a zadejte příkaz **Sqlcmd** následovaný připojovacím řetězcem pro vaši databázi SQL synapse. Připojovací řetězec bude muset mít následující parametry:

* **Server (-S):** Server v následující podobě: `<`název serveru`>`.database.windows.net
* **Databáze (-d):** Název databáze
* **Povolit identifikátory v uvozovkách (-I):** Aby bylo možné připojit se k instanci SQL synapse, musí být povolené identifikátory v uvozovkách.

Chcete-li použít ověřování SQL Server, je nutné přidat parametry uživatelského jména a hesla:

* **User (-U):** Uživatel serveru v následující podobě: `<`Uživatel`>`
* **Heslo (-P):** Heslo přidružené k uživateli

Připojovací řetězec může vypadat jako v následujícím příkladu:

**Bezserverový fond SQL**

```sql
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P Enter_Your_Password_Here -I
```

**Vyhrazený fond SQL**

```
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

Chcete-li používat integrované ověřování v Azure Active Directory, je třeba přidat parametry Azure Active Directory:

* **Azure Active Directory Authentication (-G):** Pro ověřování používat Azure Active Directory

Připojovací řetězec může vypadat jako v následujících příkladech:

**Bezserverový fond SQL**

```
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -G -I
```

**Vyhrazený fond SQL**

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [!NOTE]
> Abyste mohli provádět ověřování pomocí Active Directory, je třeba [povolit ověřování Azure Active Directory](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="2-query"></a>2. dotaz

### <a name="use-dedicated-sql-pool"></a>Použít vyhrazený fond SQL

Po připojení můžete z instance vydat všechny podporované příkazy [jazyka Transact-SQL](/sql/t-sql/language-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) (T-SQL). V tomto příkladu jsou dotazy odesílány v interaktivním režimu:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

V následujících příkladech se pro vyhrazený fond SQL ukáže, jak spouštět dotazy v dávkovém režimu pomocí možnosti-Q nebo rozdávat SQL do Sqlcmd:

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

### <a name="use-serverless-sql-pool"></a>Použití bezserverového fondu SQL

Po připojení můžete z instance vydat všechny podporované příkazy [jazyka Transact-SQL](/sql/t-sql/language-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) (T-SQL).  V následujícím příkladu jsou dotazy odesílány v interaktivním režimu:

```sql
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P Enter_Your_Password_Here -I
1> SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')
2> GO
3> QUIT
```

V níže uvedených příkladech se dozvíte, jak spouštět dotazy v dávkovém režimu, a to pomocí možnosti-Q nebo rozdávat SQL do Sqlcmd:

```sql
sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P 'Enter_Your_Password_Here' -I -Q "SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')"
```

```sql
"SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')" | sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P 'Enter_Your_Password_Here' -I > ./tables.out
```

## <a name="next-steps"></a>Další kroky

Další informace o možnostech nástroje Sqlcmd naleznete v [dokumentaci k nástroji Sqlcmd](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).
