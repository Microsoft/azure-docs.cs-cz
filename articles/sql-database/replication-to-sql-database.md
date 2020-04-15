---
title: Replikace
description: Informace o používání replikace SQL Serveru s jednotlivými databázemi a databázemi Azure SQL Database v elastických fondech
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: mathoma
ms.date: 01/25/2019
ms.openlocfilehash: f28269b067ee98d69a97799911fd2d84a7f91e34
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381152"
---
# <a name="replication-to-sql-database-single-and-pooled-databases"></a>Replikace do jedné databáze SQL a sdružených databází

Replikaci serveru SQL Server lze nakonfigurovat na jednu a sdruženou databázi na [serveru SQL Database](sql-database-servers.md) v databázi Azure SQL Database.  

## <a name="supported-configurations"></a>**Podporované konfigurace:**
  
- SQL Server může být instancí SQL Serveru spuštěného místně nebo instancí SQL Serveru spuštěného ve virtuálním počítači Azure v cloudu. Další informace najdete v tématu [SQL Server na Virtuální počítače Azure přehled](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-infrastructure-services/).  
- Databáze Azure SQL musí být nabízený odběratel vydavatele SERVERU SQL Server.  
- Distribuční databázi a agenty replikace nelze umístit do databáze Azure SQL.  
- Snímek a jednosměrná transakční replikace jsou podporovány. Transakční replikace typu peer-to-peer a slučovací replikace nejsou podporovány.
- Replikace je dostupná pro verzi Public Preview v spravované instanci Azure SQL Database. Spravovaná instance může hostovat databáze vydavatele, distributora a odběratele. Další informace naleznete v [tématu Replikace se spravovanou instancí databáze SQL](replication-with-sql-database-managed-instance.md).

## <a name="versions"></a>Verze  

Místní vydavatelé a distributoři serveru SQL Server musí používat (alespoň) jednu z následujících verzí:  

- SQL Server 2016 a vyšší
- SQL Server 2014 [RTM CU10 (12.0.4427.24)](https://support.microsoft.com/help/3094220/cumulative-update-10-for-sql-server-2014) nebo [SP1 CU3 (12.0.2556.4)](https://support.microsoft.com/help/3094221/cumulative-update-3-for-sql-server-2014-service-pack-1)
- SQL Server 2012 [SP2 CU8 (11.0.5634.1)](https://support.microsoft.com/help/3082561/cumulative-update-8-for-sql-server-2012-sp2) nebo [SP3 (11.0.6020.0)](https://www.microsoft.com/download/details.aspx?id=49996)

> [!NOTE]
> Pokus o konfiguraci replikace pomocí nepodporované verze může mít za následek číslo chyby MSSQL_REPL20084 \<(Proces se nemohl připojit k odběrateli.) a MSSQL_REPL40532 (Nelze otevřít název serveru> požadované přihlášením. Přihlášení se nezdařilo.).  

Chcete-li používat všechny funkce azure sql database, musíte používat nejnovější verze [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) a SQL Server Data [Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).  

  
## <a name="remarks"></a>Poznámky

- Replikaci lze konfigurovat pomocí [sql server management studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) nebo spuštěním Příkazy Transact-SQL na vydavatele. Replikaci nelze konfigurovat pomocí portálu Azure.  
- Replikace může používat jenom přihlášení ověřování sql serveru pro připojení k databázi Azure SQL.
- Replikované tabulky musí mít primární klíč.  
- Musíte mít existující předplatné Azure.  
- Předplatitel databáze Azure SQL může být v libovolné oblasti.  
- Jedna publikace na SQL Serveru může podporovat předplatitele Azure SQL Database i SQL Server (místní a SQL Server ve virtuálním počítači Azure).  
- Správa replikace, monitorování a řešení potíží musí být provedeny z místního serveru SQL Server.  
- Podporovány jsou jenom nabízená předplatná do Azure SQL Database.  
- Pouze `@subscriber_type = 0` je podporován a **sp_addsubscription** pro sql database.  
- Azure SQL Database nepodporuje obousměrnou, okamžitou, aktualizovatelnou nebo peer to peer replikaci.

## <a name="replication-architecture"></a>Architektura replikace  

![replikace na databázi SQL](./media/replication-to-sql-database/replication-to-sql-database.png)  

## <a name="scenarios"></a>Scénáře  

### <a name="typical-replication-scenario"></a>Typický scénář replikace  

1. Vytvořte publikaci transakční replikace v místní databázi serveru SQL Server.  
2. Na místním serveru SQL Server použijte **Průvodce novým odběrem** nebo příkazy Transact-SQL k vytvoření nabízení předplatného azure sql database.  
3. S jedním a sdružené databáze v Azure SQL Database počáteční sada dat je snímek, který je vytvořen agentsnímek a distribuovány a použity agentdistribuce. S databází spravovaných instancí můžete také použít zálohu databáze k osivu databáze odběratele.

### <a name="data-migration-scenario"></a>Scénář migrace dat  

1. Transakční replikace slouží k replikaci dat z místní databáze SERVERU SQL server do databáze Azure SQL Database.  
2. Přesměrujte klientské nebo středně vrstvé aplikace a aktualizujte kopii databáze Azure SQL.  
3. Zastavte aktualizaci verze tabulky serveru SQL Server a odeberte publikaci.  

## <a name="limitations"></a>Omezení

Pro předplatná Azure SQL Database nejsou podporovány následující možnosti:

- Kopírovat přidružení skupin souborů  
- Kopírování schémat dělení na tabulky  
- Kopírovat schémata dělení indexu  
- Kopírovat uživatelem definované statistiky  
- Kopírovat výchozí vazby  
- Kopírovat vazby pravidel  
- Kopírování fulltextových indexů  
- Kopírovat XML XSD  
- Kopírování indexů XML  
- Kopírovat oprávnění  
- Kopírování prostorových indexů  
- Kopírovat filtrované indexy  
- Kopírovat atribut komprese dat  
- Kopírovat atribut řídké sloupce  
- Převést datový proud souborů na datové typy MAX  
- Převést hierarchyid na datové typy MAX  
- Převést prostorové na datové typy MAX  
- Kopírování rozšířených vlastností  
- Kopírovat oprávnění  

### <a name="limitations-to-be-determined"></a>Omezení, která mají být stanovena

- Kopírovat řazení  
- Provedení serializované transakce sp  

## <a name="examples"></a>Příklady

Vytvořte publikaci a nabízené předplatné. Další informace naleznete v tématu:
  
- [Vytvoření publikace](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Vytvořte push subscription](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/) pomocí názvu serveru Azure SQL Database jako odběratele (například **N'azuresqldbdns.database.windows.net"** a název databáze Azure SQL jako cílové databáze (například **AdventureWorks).**  

## <a name="see-also"></a>Viz také  

- [Transakční replikace](sql-database-managed-instance-transactional-replication.md)
- [Vytvoření publikace](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Vytvoření nabízeného předplatného](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Typy replikace](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Monitorování (replikace)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Inicializaci předplatného](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
