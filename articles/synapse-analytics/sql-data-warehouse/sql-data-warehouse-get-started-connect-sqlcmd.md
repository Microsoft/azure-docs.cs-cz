---
title: Připojení pomocí sqlcmd
description: Pomocí nástroje příkazového řádku Sqlcmd se můžete připojit k synapse fondu SQL a dotazovat se na něj.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: f61f2932ad9c6342a81ec05df335e9ea4d659fce
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461974"
---
# <a name="connect-to-sql-pool-in-azure-synapse-analytics-with-sqlcmd"></a>Připojení k fondu SQL ve službě Azure synapse Analytics pomocí sqlcmd

> [!div class="op_single_selector"]
>
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md)
> * [SSMS](sql-data-warehouse-query-ssms.md)

K připojení a dotazování fondu SQL použijte nástroj příkazového řádku [Sqlcmd] [Sqlcmd].  

## <a name="1-connect"></a>1. připojení

Pokud chcete začít s [Sqlcmd] [Sqlcmd], otevřete příkazový řádek a zadejte příkaz **Sqlcmd** následovaný připojovacím řetězcem pro váš fond SQL. Připojovací řetězec bude muset mít následující parametry:

* **Server (-S):** Server v následující podobě: `<`název serveru`>`.database.windows.net
* **Databáze (-d):** Název fondu SQL
* **Povolit identifikátory v uvozovkách (-I):** Aby bylo možné připojit se k instanci fondu SQL, musí být povolené identifikátory v uvozovkách.

Chcete-li používat ověřování systému SQL Server, je třeba přidat parametry uživatelského jména a hesla:

* **User (-U):** Uživatel serveru v následující podobě: `<`Uživatel`>`
* **Password (-P):** Heslo přidružené k uživateli

Připojovací řetězec může například vypadat následovně:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

Chcete-li používat integrované ověřování v Azure Active Directory, je třeba přidat parametry Azure Active Directory:

* **Azure Active Directory Authentication (-G):** Pro ověřování používat Azure Active Directory

Připojovací řetězec může například vypadat následovně:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [!NOTE]
> Abyste mohli provádět ověřování pomocí Active Directory, je třeba [povolit ověřování Azure Active Directory](sql-data-warehouse-authentication.md).

## <a name="2-query"></a>2. dotaz

Po připojení můžete pro instanci zadávat všechny podporované příkazy jazyka Transact-SQL.  V tomto příkladu jsou dotazy zadávány v interaktivním režimu.

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

Tyto další příklady ukazují, jak lze vaše dotazy spouštět v dávkovém režimu pomocí parametru -Q nebo vedení serveru SQL k příkazu sqlcmd.

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

## <a name="next-steps"></a>Další kroky

Další informace o možnostech dostupných v Sqlcmd najdete v tématu [dokumentace k nástroji Sqlcmd](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).
