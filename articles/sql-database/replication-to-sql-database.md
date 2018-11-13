---
title: Replikace do Azure SQL Database | Microsoft Docs"
description: Další informace o použití replikace systému SQL Server s Azure SQL Database izolovaných databází a databází v elastických fondech
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: mathoma
manager: craigg
ms.date: 11/09/2018
ms.openlocfilehash: 44d6135c8a259d3d4c791d359136963483088c5a
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2018
ms.locfileid: "51567889"
---
# <a name="replication-to-sql-database-single-and-pooled-databases"></a>Replikace do jednoho a ve fondu databází SQL Database

Replikace systému SQL Server lze konfigurovat pro databáze ve fondu a jeden pro [logický server](sql-database-logical-servers.md) ve službě Azure SQL Database.  

## <a name="supported-configurations"></a>**Podporované konfigurace:**
  
- SQL Server může být instance systému SQL Server spuštěnou v místním nebo instance systému SQL Server běžící na virtuálním počítači Azure v cloudu. Další informace najdete v tématu [systému SQL Server na Azure Virtual Machines – přehled](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-infrastructure-services/).  
- Azure SQL database musí být odběratelem nabízených vydavatele typu SQL Server.  
- Distribuční databázi a agenty replikace nelze umístit na službu Azure SQL database.  
- Snímek a jednosměrná transakční replikace se podporují. Transakční replikace peer-to-peer a slučovací replikace nepodporuje.
- Replikace je dostupná ve veřejné verzi preview na Azure SQL Database Managed Instance. Spravovaná Instance může hostovat databáze vydavatele, distributor a odběratele. Další informace najdete v tématu [replikace pomocí služby SQL Database Managed Instance](replication-with-sql-database-managed-instance.md).

## <a name="versions"></a>Verze  

- Vydavatelem a distributorem musí být dlouhý aspoň na jednu z následujících verzí:  
- SQL Server 2017 (14.x)
- SQL Server 2016 (13.x)
- SQL Server 2014 (12.x) SP1 CU3
- SQL Server 2014 (12.x) RTM CU10
- SQL Server 2012 (11.x) SP2 CU8 nebo s aktualizací SP3
- Přistoupíte ke konfiguraci replikace pomocí starší verze může vést k chybě číslo MSSQL_REPL20084 (procesu nemůže připojit k odběrateli.) a MSSQL_REPL40532 (nejde otevřít server \<name > požadovaný v přihlášení. Přihlášení se nezdařilo.).  
- Chcete-li používat všechny funkce služby Azure SQL Database, musíte používat nejnovější verze [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) a [SQL Server Data Tools](https://docs.microsoft.com//sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017).  
  
## <a name="remarks"></a>Poznámky

- Replikaci je možné nakonfigurovat pomocí [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) nebo spuštěním příkazů jazyka Transact-SQL ve vydavateli. Nejde nakonfigurovat replikaci pomocí webu Azure portal.  
- Replikace lze použít pouze ověřovací přihlášení SQL serveru pro připojení k databázi Azure SQL.
- Replikované tabulky musí mít primární klíč.  
- Musíte mít předplatné Azure.  
- Předplatitele Azure SQL database může být v libovolné oblasti.  
- Jedna publikace na SQL serveru může podporovat Azure SQL Database a odběratelé systému SQL Server (místní a SQL Server na virtuálním počítači Azure).  
- Replikace, monitorování, správy a řešení potíží se musí provádět z místního SQL serveru.  
- Podporují se jenom nabízené odběry na Azure SQL Database.  
- Pouze `@subscriber_type = 0` je podporována v **sp_addsubscription** pro službu SQL Database.  
- Azure SQL Database nepodporuje obousměrnou, okamžitě, aktualizovat nebo typu peer to peer replikace.

## <a name="replication-architecture"></a>Architektura replikace  

![replikace do sql database](./media/replication-to-sql-database/replication-to-sql-database.png)  

## <a name="scenarios"></a>Scénáře  

### <a name="typical-replication-scenario"></a>Replikace Typický scénář  

1. Vytvoření publikace transakční replikace na místní databázi systému SQL Server.  
2. Na místní SQL Server pomocí **Průvodce novým odběrem** nebo příkazy jazyka Transact-SQL k vytvoření oznámení na předplatné Azure SQL Database.  
3. S databázemi ve fondu a jeden ve službě Azure SQL Database Počáteční sada dat je snímek, který je vytvořil agenta snímku a distribuované a použít tak agenta distribuce. S Azure SQL Database Managed Instance můžete také použít zálohu databáze k přidání dat do databáze odběratele.

### <a name="data-migration-scenario"></a>Scénáře migrace dat  

1. Replikace dat z místní databáze systému SQL Server do služby Azure SQL Database pomocí transakční replikace.  
2. Přesměrování klienta nebo aplikace střední vrstvy aktualizovat kopii databáze Azure SQL.  
3. Zastavit aktualizace verze systému SQL Server tabulky a odeberte publikace.  

## <a name="limitations"></a>Omezení

Následující možnosti nejsou podporovány pro předplatná Azure SQL Database:

- Zkopírujte soubor přidružení skupin  
- Kopírovat tabulku schémata dělení  
- Zkopírujte index schémata dělení  
- Zkopírujte statistiky definované uživatelem  
- Zkopírujte výchozí vazby  
- Kopírovat pravidlo vazby  
- Zkopírujte fulltextové indexy  
- Zkopírujte XML XSD  
- Zkopírujte XML indexy  
- Kopírování oprávnění  
- Zkopírujte prostorové indexy  
- Zkopírujte filtrované indexy  
- Zkopírujte atribut komprese dat  
- Zkopírujte atribut sadu zhuštěných sloupců  
- Převést filestream na maximální počet datových typů  
- Převést hierarchyid na maximální počet datových typů  
- Převod na datové typy maximální počet prostorových  
- Zkopírujte rozšířených vlastností  
- Kopírování oprávnění  

### <a name="limitations-to-be-determined"></a>Omezení, která určí

- Zkopírujte kolace  
- Spuštění v rámci serializovaná transakce SP  

## <a name="examples"></a>Příklady

Vytvořte publikaci a nabízený odběr. Další informace naleznete v tématu:
  
- [Vytvořit publikaci](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Vytvoření odběru Push](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/) pomocí název logického serveru databáze Azure SQL jako odběratele (například **N'azuresqldbdns.database.windows.net "**) a název databáze Azure SQL jako (určení databáze například **AdventureWorks**).  

## <a name="see-also"></a>Viz také  

- [Vytvořit publikaci](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Vytvořit nabízený odběr](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Typy replikace](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Sledování (replikace)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Inicializace odběru](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
