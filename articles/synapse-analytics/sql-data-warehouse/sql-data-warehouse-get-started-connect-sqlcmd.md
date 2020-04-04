---
title: Spojit se s sqlcmd
description: Pomocí nástroje příkazového řádku sqlcmd se můžete připojit k fondu Synapse SQL a zadat dotaz na fond SYNAPse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: cfb1b7558f8d7675009a0ebc729cc1a560f03d12
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633377"
---
# <a name="connect-to-synapse-sql-pool-with-sqlcmd"></a>Připojení k fondu Synapse SQL s sqlcmd
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [Sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Pomocí nástroje příkazového řádku [sqlcmd][sqlcmd] se můžete připojit k fondu SQL a dotazovat se na něj.  

## <a name="1-connect"></a>1. Připojte
Chcete-li začít s [sqlcmd][sqlcmd], otevřete příkazový řádek a zadejte **sqlcmd** následovaný připojovacím řetězcem pro databázi fondu SQL. Připojovací řetězec bude muset mít následující parametry:

* **Server (-S):** Server v následující podobě: `<`název serveru`>`.database.windows.net
* **Database (-d):** Název databáze
* **Povolit uvozovky (-I):** Identifikátory v uvozovkách musí být povoleny pro připojení k instanci fondu SQL.

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

## <a name="next-steps"></a>Další kroky
Další informace o možnostech dostupných v sqlcmd naleznete v [dokumentaci sqlcmd](https://msdn.microsoft.com/library/ms162773.aspx) .
