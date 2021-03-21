---
title: Replikace Azure SQL Server do Azure SQL Database
description: V rámci jednosměrné transakce nebo topologie replikace snímků můžete nakonfigurovat databázi v Azure SQL Database jako předplatitele nabízených oznámení.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: mathoma
ms.date: 04/28/2020
ms.openlocfilehash: 6ff1d485ab4c0662ae8a9d754ce67b1446b76fcc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92780950"
---
# <a name="replication-to-azure-sql-database"></a>Replikace do Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Můžete nakonfigurovat Azure SQL Database jako předplatitele nabízené replikace v jednosměrné topologii transakce nebo replikace snímků.

> [!NOTE]
> Tento článek popisuje použití [transakční replikace](/sql/relational-databases/replication/transactional/transactional-replication) v Azure SQL Database. Nesouvisí s [aktivní geografickou replikací](./active-geo-replication-overview.md), Azure SQL Database funkcí, která umožňuje vytvářet kompletní čitelné repliky jednotlivých databází.

## <a name="supported-configurations"></a>Podporované konfigurace
  
- Azure SQL Database může být pouze předplatitelem nabízených oznámení SQL Server vydavatele a distributora.  
- Instance SQL Server fungující jako Vydavatel nebo distributor může být instancí [SQL Server spouštěnou místně](https://www.microsoft.com/sql-server/sql-server-downloads), [SPRAVOVANOU instancí Azure SQL](../managed-instance/instance-create-quickstart.md)nebo instancí [SQL Server spuštěnou na virtuálním počítači Azure v cloudu](../virtual-machines/windows/sql-vm-create-portal-quickstart.md). 
- Distribuční databáze a agenti replikace nelze umístit do databáze v Azure SQL Database.  
- Je podporována [snímková](/sql/relational-databases/replication/snapshot-replication) a [Jednosměrná transakční](/sql/relational-databases/replication/transactional/transactional-replication) replikace. Transakční replikace peer-to-peer a slučovací replikace se nepodporují.

### <a name="versions"></a>Verze  

Aby bylo možné úspěšně provést replikaci do databáze v Azure SQL Database, SQL Server vydavatelé a distributoři musí použít (alespoň) jednu z následujících verzí:

Publikování do libovolného Azure SQL Database z databáze SQL Server podporuje následující verze SQL Server:

- SQL Server 2016 a vyšší
- SQL Server 2014 [RTM CU10 (12.0.4427.24)](https://support.microsoft.com/help/3094220/cumulative-update-10-for-sql-server-2014) nebo [SP1 CU3 (12.0.2556.4)](https://support.microsoft.com/help/3094221/cumulative-update-3-for-sql-server-2014-service-pack-1)
- SQL Server 2012 [SP2 CU8 (11.0.5634.1)](https://support.microsoft.com/help/3082561/cumulative-update-8-for-sql-server-2012-sp2) nebo [SP3 (11.0.6020.0)](https://www.microsoft.com/download/details.aspx?id=49996)

> [!NOTE]
> Pokud se pokusíte nakonfigurovat replikaci pomocí nepodporované verze, může dojít k chybě číslo MSSQL_REPL20084 (proces se nemohl připojit k odběrateli) a MSSQL_REPL40532 (nemůže otevřít server \<name> požadovaný přihlášením. Přihlášení se nezdařilo.).  

Pokud chcete používat všechny funkce Azure SQL Database, musíte používat nejnovější verze nástrojů [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) a [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt).  

### <a name="types-of-replication"></a>Typy replikace

Existují různé [typy replikace](/sql/relational-databases/replication/types-of-replication):

| Replikace | Azure SQL Database | Spravovaná instance Azure SQL |
| :----| :------------- | :--------------- |
| [**Standardní transakční**](/sql/relational-databases/replication/transactional/transactional-replication) | Ano (jenom jako předplatitel) | Yes | 
| [**Snímek**](/sql/relational-databases/replication/snapshot-replication) | Ano (jenom jako předplatitel) | Yes|
| [**Sloučit replikaci**](/sql/relational-databases/replication/merge/merge-replication) | No | No|
| [**Peer-to-peer**](/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | No | No|
| [**Obousměrné**](/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | No | Yes|
| [**Odběry, které by možné aktualizovat**](/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | No | No|
| &nbsp; | &nbsp; | &nbsp; |

  
## <a name="remarks"></a>Poznámky

- Jsou podporovány pouze nabízené odběry Azure SQL Database.  
- Replikaci lze konfigurovat pomocí [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) nebo spuštěním příkazů jazyka Transact-SQL na vydavateli. Replikaci nelze nakonfigurovat pomocí Azure Portal.  
- Replikace může pro připojení k Azure SQL Database používat pouze přihlášení SQL Server ověřování.
- Replikované tabulky musí mít primární klíč.  
- Musíte mít existující předplatné Azure.  
- Předplatitel Azure SQL Database může být v libovolné oblasti.  
- Jedna publikace na SQL Server může podporovat předplatitele Azure SQL Database a SQL Server (místní i SQL Server ve virtuálních počítačích Azure).  
- Správa replikace, monitorování a řešení potíží se musí provádět z SQL Server spíše než Azure SQL Database.  
- `@subscriber_type = 0`Pro SQL Database se podporuje jenom **sp_addsubscription** .  
- Azure SQL Database nepodporuje replikaci obousměrné, okamžité, aktualizovatelné ani peer-to-peer.

## <a name="replication-architecture"></a>Architektura replikace  

![Diagram znázorňuje architekturu replikace s Azure SQL Database, která obsahuje několik clusterů pro předplatitele v různých oblastech, a místní virtuální počítače Azure, které obsahují vydavatele, spustitelný soubor LogRead a distributory, které se připojují ke vzdáleným clusterům.](./media/replication-to-sql-database/replication-to-sql-database.png)  

## <a name="scenarios"></a>Scénáře  

### <a name="typical-replication-scenario"></a>Scénář typické replikace  

1. Vytvoření publikace transakční replikace v databázi SQL Server.  
2. V SQL Server použijte **Průvodce novým odběrem** nebo příkazy jazyka Transact-SQL k vytvoření nabízeného oznámení do předplatného pro Azure SQL Database.  
3. Při použití jedné a sdružené databáze v Azure SQL Database je počáteční datová sada snímkem vytvořeným agentem snímku, který distribuuje a používá agent distribuce. Pomocí vydavatele spravované instance SQL můžete také použít zálohu databáze k osazení Azure SQL Database předplatitele.

### <a name="data-migration-scenario"></a>Scénář migrace dat  

1. K replikaci dat z databáze SQL Server do Azure SQL Database můžete použít transakční replikaci.  
2. Přesměrujte klienta nebo aplikace střední vrstvy, aby bylo možné aktualizovat kopii databáze.  
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

### <a name="limitations-to-be-determined"></a>Omezení, která mají být určena

- Kopírovat kolaci  
- Provádění v serializované transakci SP  

## <a name="examples"></a>Příklady

Vytvořte publikaci a nabízený odběr. Další informace naleznete v tématu:
  
- [Vytvoření publikace](/sql/relational-databases/replication/publish/create-a-publication)
- [Vytvořte nabízený odběr](/sql/relational-databases/replication/create-a-push-subscription/) pomocí názvu serveru jako předplatitele (například **N'azuresqldbdns. Database. Windows. NET**) a názvu Azure SQL Database jako cílovou databázi (například **AdventureWorks**).  

## <a name="see-also"></a>Viz také  

- [Transakční replikace](../managed-instance/replication-transactional-overview.md)
- [Vytvoření publikace](/sql/relational-databases/replication/publish/create-a-publication)
- [Vytvoření nabízeného odběru](/sql/relational-databases/replication/create-a-push-subscription/)
- [Typy replikace](/sql/relational-databases/replication/types-of-replication)
- [Monitorování (replikace)](/sql/relational-databases/replication/monitor/monitoring-replication)
- [Inicializovat předplatné](/sql/relational-databases/replication/initialize-a-subscription)