---
title: Transakční replikace s Azure SQL Database | Microsoft Docs"
description: Další informace o použití transakční replikace systému SQL Server se samostatným ve fondu a instanci databáze ve službě Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 1c542c1e906b078b76b78ed30af8bdf67110199c
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55814108"
---
# <a name="transactional-replication-with-standalone-pooled-and-instance-databases-in-azure-sql-database"></a>Transakční replikace se samostatným, ve fondu a instanci databáze ve službě Azure SQL Database

Transakční replikace je funkce služby Azure SQL Database a SQL Server, který umožňuje replikovat data z tabulky ve službě Azure SQL Database nebo SQL Server do tabulek umístit do vzdálené databáze. Tato funkce umožňuje synchronizovat více tabulek v různých databázích.

## <a name="when-to-use-transactional-replication"></a>Použití transakční replikace

Transakční replikace je užitečná v následujících scénářích:

- Publikovat změny provedené v jedné nebo více tabulek v databázi a Rozdejte je jeden nebo více systému SQL Server a Azure SQL databáze, které přihlášený(á) k odběru změn.
- Zachovat několik distribuované databáze ve stavu synchronizovaná.
- Migrace databází z jednoho serveru SQL Server nebo spravované Instance do jiné databáze a průběžně publikujte změny.

## <a name="overview"></a>Přehled

Klíčové komponenty v transakční replikaci můžete vidět na následujícím obrázku:  

![Replikace SQL Database](media/replication-to-sql-database/replication-to-sql-database.png)


**Vydavatele** odesláním aktualizací k distributorovi je instance nebo serveru, který publikuje změny provedené na některé tabulky (články). Publikování do jakékoli SQL Azure je databáze z SQL serveru v místním podporuje následující verze systému SQL Server:

   - SQL Server 2019 (preview)
   - SQL Server 2016 na SQL 2017
   - SQL Server 2014 SP1 CU3 nebo větší (12.00.4427)
   - SQL Server 2014 RTM CU10 (12.00.2556)
   - SQL Server 2012 SP3 nebo větší (11.0.6020)
   - SQL Server 2012 SP2 CU8 (11.0.5634.0)
   - Pro jiné verze systému SQL Server, které nepodporují publikování na objekty v Azure, je možné využívat [znovu publikovat data](https://docs.microsoft.com/sql/relational-databases/replication/republish-data) metody pro přesun dat do novější verze systému SQL Server. 

**Distributora** je instance nebo serveru, který shromažďuje údaje o změnách v článcích od vydavatele a distribuuje je pro předplatitele. Distributor může být Azure SQL Database Managed Instance nebo SQL Server (libovolná verze jak dlouhé je se rovná nebo je vyšší než verze, vydavatel). 

**Odběratele** je instance nebo serveru, který přijímá změny provedené ve vydavateli. Předplatitelé může být buď samostatně, ve fondu a instance databází do databází Azure SQL Database nebo SQL Server. Odběratel v samostatné nebo dotazů databáze musí být nakonfigurován jako odběratel nabízených oznámení. 

| Role | Nasazení samostatné služby a databáze ve fondu | Instance databáze |
| :----| :------------- | :--------------- |
| **Publisher** | Ne | Ano | 
| **Distributor** | Ne | Ano|
| **Předplatitele o přijetí změn** | Ne | Ano|
| **Odběratel nabízených oznámení**| Ano | Ano|
| &nbsp; | &nbsp; | &nbsp; |

Existují různé [typy replikace](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication?view=sql-server-2017):


| Replikace | Nasazení samostatné služby a databáze ve fondu | Instance databáze|
| :----| :------------- | :--------------- |
| [**transakční**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) | Ano (jenom jako předplatitel) | Ano | 
| [**snímek**](https://docs.microsoft.com/sql/relational-databases/replication/snapshot-replication) | Ano (jenom jako předplatitel) | Ano|
| [**Sloučení replikace**](https://docs.microsoft.com/sql/relational-databases/replication/merge/merge-replication) | Ne | Ne|
| [**Peer-to-peer**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | Ne | Ne|
| **Jednosměrná** | Ano | Ano|
| [**Obousměrné**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | Ne | Ano|
| [**Aktualizovatelné odběry**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | Ne | Ne|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > - Přistoupíte ke konfiguraci replikace pomocí starší verze může vést k chybě číslo MSSQL_REPL20084 (procesu nemůže připojit k odběrateli.) a MSSQ_REPL40532 (nejde otevřít server \<name > požadovaný v přihlášení. Přihlášení se nezdařilo.)
  > - Chcete-li používat všechny funkce služby Azure SQL Database, musíte používat nejnovější verze [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) a [SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017).

## <a name="requirements"></a>Požadavky

- Připojení pomocí ověřování SQL mezi účastníky replikace. 
- Sdílení účet úložiště Azure pro pracovní adresář, používané při replikaci. 
- Port 445 (odchozí TCP) musí být otevřené v pravidlech zabezpečení podsíť Managed Instance pro přístup ke sdílené složce Azure. 
- Port 1433 (odchozí TCP) je potřeba otevřít, pokud vydavatel/distributora Managed Instance a odběratele je místní. 

## <a name="common-configurations"></a>Obvyklé konfigurace

Obecně platí vydavatelem a distributorem musí být buď v cloudu nebo místně. Podporují se následující konfigurace: 

### <a name="publisher-with-local-distributor-on-a-managed-instance"></a>Vydavatele s místní Distributor na Managed Instance

![Jedna instance jako vydavateli a distributorovi ](media/replication-with-sql-database-managed-instance/01-single-instance-asdbmi-pubdist.png)

Vydavateli a distributorovi jsou nakonfigurované v rámci jednoho Managed Instance a distribuce změny jiných Managed Instance, izolovaná databáze, databáze ve fondu, nebo místní SQL Server. V této konfiguraci vydavatel/spravované Instance distributora nelze konfigurovat pomocí [geografickou replikaci a automatické převzetí služeb při selhání skupiny](sql-database-auto-failover-group.md).

### <a name="publisher-with-remote-distributor-on-a-managed-instance"></a>Vydavatele s vzdáleného distributora na Managed Instance

V této konfiguraci jednoho Managed Instance publikuje změny v distributoru umístěny na jiné mi, který může sloužit velký počet spravovaných instancí zdroje a distribuovat změny na jeden nebo více cílů na Managed Instance, izolovaná databáze, databáze ve fondu, nebo SQL Server.

![Samostatné instance pro vydavatele a distributora](media/replication-with-sql-database-managed-instance/02-separate-instances-asdbmi-pubdist.png)

Vydavateli a distributorovi konfigurují na dvou spravovaných instancí. V této konfiguraci

- Obě Managed instance jsou ve stejné virtuální síti.
- Obě Managed instance jsou ve stejném umístění.
- Publikování spravované instance, které jsou hostiteli a nejde ho distributora databází [geograficky replikovaný pomocí automatického převzetí služeb při selhání groups](sql-database-auto-failover-group.md).

### <a name="publisher-and-distributor-on-premises-with-a-subscriber-on-a-standalone-pooled-and-instance-database"></a>Vydavateli a distributorovi místně pomocí odběratele na samostatném, ve fondu a instanci databáze 

![Azure SQL Database jako odběratele](media/replication-with-sql-database-managed-instance/03-azure-sql-db-subscriber.png)
 
V této konfiguraci je Azure SQL Database (samostatný, ve fondu a instanci databáze) odběratele. Tato konfigurace podporuje migraci z místního do Azure. Pokud je předplatitel v samostatné nebo databázi ve fondu, musí být v režimu nabízení.  

## <a name="next-steps"></a>Další postup

1. [Konfigurace transakční replikace pro Managed Instance](replication-with-sql-database-managed-instance.md#configure-publishing-and-distribution-example). 
1. [Vytvoření publikace](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication).
1. [Vytvořit nabízený odběr](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription) pomocí názvu serveru Azure SQL Database jako odběratele (například `N'azuresqldbdns.database.windows.net` a název cílové databáze Azure SQL Database (například **Adventureworks**. )


## <a name="see-also"></a>Viz také  

- [Replikace do služby SQL Database](replication-to-sql-database.md)
- [Replikace do spravované Instance](replication-with-sql-database-managed-instance.md)
- [Vytvořit publikaci](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Vytvořit nabízený odběr](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Typy replikace](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Sledování (replikace)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Inicializace odběru](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
