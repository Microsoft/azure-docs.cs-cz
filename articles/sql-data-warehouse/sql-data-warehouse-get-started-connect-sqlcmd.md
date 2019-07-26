---
title: Připojení ke službě Azure SQL Data Warehouse pomocí sqlcmd | Dokumentace Microsoftu
description: Pomocí nástroje příkazového řádku Sqlcmd se můžete připojit k Azure SQL Data Warehouse a dotazovat se na ně.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: f3b93660fb9f8f3b0bfdddc37105b9e998ed9eee
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479509"
---
# <a name="connect-to-sql-data-warehouse-with-sqlcmd"></a>Připojení k SQL Data Warehouse pomocí sqlcmd
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Pomocí nástroje příkazového řádku [Sqlcmd][sqlcmd] se můžete připojit k Azure SQL Data Warehouse a dotazovat se na ně.  

## <a name="1-connect"></a>1. Připojit
Chcete-li začít s nástrojem [sqlcmd][sqlcmd], otevřete příkazový řádek a zadejte příkaz **sqlcmd** následovaný připojovacím řetězcem pro vaši databázi SQL Data Warehouse. Připojovací řetězec bude muset mít následující parametry:

* **Server (-S):** Server v názvu`>`serveru `<`formuláře. Database.Windows.NET
* **Databáze (-d):** Název databáze.
* **Povolit identifikátory v uvozovkách (-I):** Aby bylo možné se připojit k instanci SQL Data Warehouse, musí být povolené identifikátory v uvozovkách.

Chcete-li používat ověřování systému SQL Server, je třeba přidat parametry uživatelského jména a hesla:

* **Uživatel (-U):** Uživatel serveru ve formuláři `<``>`
* **Heslo (-P):** Heslo přidružené k uživateli

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
> 
> 

## <a name="2-query"></a>2. Dotaz
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

## <a name="next-steps"></a>Další postup
Další informace o možnostech dostupných v Sqlcmd najdete v [dokumentaci k nástroji Sqlcmd][sqlcmd] .

<!--Image references-->

<!--Article references-->

<!--MSDN references--> 
[sqlcmd]: https://msdn.microsoft.com/library/ms162773.aspx
[Azure portal]: https://portal.azure.com

<!--Other Web references-->
