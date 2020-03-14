---
title: Replikace
description: Další informace o použití SQL Server replikace s Azure SQL Database izolovanými databázemi a databázemi v elastických fondech
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: mathoma
ms.date: 01/25/2019
ms.openlocfilehash: f718bc17b987926f4324635f096d5983acdb63fc
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79256468"
---
# <a name="replication-to-sql-database-single-and-pooled-databases"></a>Replikace do SQL Database jedné a sdružené databáze

SQL Server replikaci je možné nakonfigurovat na [servery SQL Database](sql-database-servers.md) v Azure SQL Database na jednu databázi ve fondu.  

## <a name="supported-configurations"></a>**Podporované konfigurace:**
  
- SQL Server může být instance SQL Server spuštěná místně nebo instance SQL Server spuštěná na virtuálním počítači Azure v cloudu. Další informace najdete v tématu [přehled SQL Server v Azure Virtual Machines](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-infrastructure-services/).  
- Azure SQL Database musí být předplatitelem nabízených oznámení SQL Server vydavateli.  
- Distribuční databáze a agenti replikace nelze umístit do databáze SQL Azure.  
- Je podporována snímková a jednosměrná transakční replikace. Transakční replikace peer-to-peer a slučovací replikace se nepodporují.
- Replikace je k dispozici pro verzi Public Preview na Azure SQL Database Managed instance. Spravovaná instance může hostovat databáze vydavatelů, distributorů a předplatitelů. Další informace najdete v tématu [replikace s SQL Database Managed instance](replication-with-sql-database-managed-instance.md).

## <a name="versions"></a>Verze  

Místní SQL Server vydavatelé a distributoři musí používat (aspoň jednu z následujících verzí):  

- SQL Server 2016 a vyšší
- SQL Server 2014 [RTM CU10 (12.0.4427.24)](https://support.microsoft.com/help/3094220/cumulative-update-10-for-sql-server-2014) nebo [SP1 CU3 (12.0.2556.4)](https://support.microsoft.com/help/3094221/cumulative-update-3-for-sql-server-2014-service-pack-1)
- SQL Server 2012 [SP2 CU8 (11.0.5634.1)](https://support.microsoft.com/help/3082561/cumulative-update-8-for-sql-server-2012-sp2) nebo [SP3 (11.0.6020.0)](https://www.microsoft.com/download/details.aspx?id=49996)

> [!NOTE]
> Pokud se pokusíte nakonfigurovat replikaci pomocí nepodporované verze, může dojít k chybě číslo MSSQL_REPL20084 (proces se nemohl připojit k odběrateli) a MSSQL_REPL40532 (nemůže otevřít server \<název > požadovaný přihlášením. Přihlášení se nezdařilo.).  

Pokud chcete používat všechny funkce Azure SQL Database, musíte používat nejnovější verze nástrojů [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) a [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).  

  
## <a name="remarks"></a>Poznámky

- Replikaci lze konfigurovat pomocí [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) nebo spuštěním příkazů jazyka Transact-SQL na vydavateli. Replikaci nelze nakonfigurovat pomocí Azure Portal.  
- Replikace může pro připojení k databázi SQL Azure použít jenom přihlášení SQL Server ověřování.
- Replikované tabulky musí mít primární klíč.  
- Musíte mít existující předplatné Azure.  
- Předplatitel Azure SQL Database může být v libovolné oblasti.  
- Jedna publikace na SQL Server může podporovat předplatitele Azure SQL Database a SQL Server (místní i SQL Server ve virtuálních počítačích Azure).  
- Správa replikace, monitorování a řešení potíží se musí provádět z místních SQL Server.  
- Jsou podporovány pouze nabízené odběry Azure SQL Database.  
- V **sp_addsubscription** pro SQL Database se podporuje jenom `@subscriber_type = 0`.  
- Azure SQL Database nepodporuje replikaci obousměrné, okamžité, aktualizovatelné ani peer-to-peer.

## <a name="replication-architecture"></a>Architektura replikace  

![replication-to-sql-database](./media/replication-to-sql-database/replication-to-sql-database.png)  

## <a name="scenarios"></a>Scénáře  

### <a name="typical-replication-scenario"></a>Scénář typické replikace  

1. Vytvoření publikace transakční replikace v místní databázi SQL Server.  
2. Na místních SQL Server použijte **Průvodce novým předplatným** nebo příkazy jazyka Transact-SQL k vytvoření nabízeného oznámení do předplatného pro Azure SQL Database.  
3. Při použití jedné a sdružené databáze v Azure SQL Database je počáteční datová sada snímkem vytvořeným agentem snímku, který distribuuje a používá agent distribuce. Pomocí databáze spravované instance můžete také použít zálohu databáze k osazení databáze odběratele.

### <a name="data-migration-scenario"></a>Scénář migrace dat  

1. K replikaci dat z místní databáze SQL Server do Azure SQL Database můžete použít transakční replikaci.  
2. Přesměrujte klienta nebo aplikace střední vrstvy, aby bylo možné aktualizovat kopii databáze SQL Azure.  
3. Ukončení aktualizace verze SQL Server tabulky a odebrání publikace.  

## <a name="limitations"></a>Omezení

Pro Azure SQL Database odběry nejsou podporovány následující možnosti:

- Kopírovat přidružení skupin souborů  
- Kopírovat schémata dělení tabulky  
- Kopírovat schémata dělení indexů  
- Kopírovat uživatelsky definované statistiky  
- Kopírovat výchozí vazby  
- Kopírovat vazby pravidla  
- Kopírovat fulltextové indexy  
- Kopírovat XSD XML  
- Kopírovat indexy XML  
- Kopírovat oprávnění  
- Kopírovat prostorové indexy  
- Kopírovat filtrované indexy  
- Kopírovat atribut komprese dat  
- Kopírovat atribut zhuštěného sloupce  
- Převést datový typ FileStream na maximum  
- Převést hierarchyid na maximální počet datových typů  
- Převod prostorových datových typů na maximum  
- Kopírovat rozšířené vlastnosti  
- Kopírovat oprávnění  

### <a name="limitations-to-be-determined"></a>Omezení, která mají být určena

- Kopírovat kolaci  
- Provádění v serializované transakci SP  

## <a name="examples"></a>Příklady

Vytvořte publikaci a nabízený odběr. Další informace naleznete v tématu:
  
- [Vytvoření publikace](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Vytvořte nabízený odběr](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/) pomocí názvu serveru Azure SQL Database jako předplatitele (například **N'azuresqldbdns. Database. Windows. NET**) a názvu databáze SQL Azure jako cílové databáze (například **AdventureWorks**).  

## <a name="see-also"></a>Viz také  

- [Transakční replikace](sql-database-managed-instance-transactional-replication.md)
- [Vytvoření publikace](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Vytvoření nabízeného odběru](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Typy replikace](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Monitorování (replikace)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Inicializovat předplatné](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
