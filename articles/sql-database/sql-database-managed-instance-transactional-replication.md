---
title: Transakční replikace
description: Další informace o používání transakční replikace serveru SQL Server s databází s jedním, sdruženým a instancí v Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 02/08/2019
ms.openlocfilehash: 41dd336bdb74fbe745ab48ebd3c168af0492ae2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75691005"
---
# <a name="transactional-replication-with-single-pooled-and-instance-databases-in-azure-sql-database"></a>Transakční replikace s databází s jedním, sdruženým a instancí v Azure SQL Database

Transakční replikace je funkce Azure SQL Database a SQL Server, která umožňuje replikovat data z tabulky v Azure SQL Database nebo SQL Server do tabulek umístěných ve vzdálených databázích. Tato funkce umožňuje synchronizovat více tabulek v různých databázích.

## <a name="when-to-use-transactional-replication"></a>Kdy použít transakční replikaci

Transakční replikace je užitečná v následujících scénářích:
- Publikujte změny provedené v jedné nebo více tabulkách v databázi a distribuujte je do jedné nebo mnoha databází SQL Serveru nebo Azure SQL, které se přihlásily ke změnám.
- Uchovávejte několik distribuovaných databází v synchronizovaném stavu.
- Migrujte databáze z jednoho serveru SQL Nebo spravované instance do jiné databáze neustálým publikováním změn.

## <a name="overview"></a>Přehled

Klíčové součásti transakční replikace jsou zobrazeny na následujícím obrázku:  

![replikace s databází SQL](media/replication-to-sql-database/replication-to-sql-database.png)

**Vydavatel** je instance nebo server, který publikuje změny provedené v některých tabulkách (články) odesláním aktualizace distributor. Publikování do libovolné databáze Azure SQL z místního SQL Serveru je podporované následujícími verzemi SQL Serveru:

- SQL Server 2019 (náhled)
- SQL Server 2016 až SQL 2017
- SQL Server 2014 SP1 CU3 nebo vyšší (12.00.4427)
- SQL Server 2014 RTM CU10 (12.00.2556)
- SQL Server 2012 SP3 nebo vyšší (11.0.6020)
- SQL Server 2012 SP2 CU8 (11.0.5634.0)
- Pro jiné verze SQL Server, které nepodporují publikování na objekty v Azure, je možné využít metodu [opětovného publikování dat](https://docs.microsoft.com/sql/relational-databases/replication/republish-data) k přesunutí dat do novějších verzí SQL Serveru. 

**Distributor** je instance nebo server, který shromažďuje změny v článcích z Vydavatel a distribuuje je předplatitelé. Distributor může být buď Azure SQL Database spravované instance nebo SQL Server (libovolná verze tak dlouho, dokud je rovna nebo vyšší než verze Vydavatel). 

**Odběratel** je instance nebo server, který přijímá změny provedené na Vydavatel. Předplatitelé mohou být buď jednu, sdruženou a instancí databází v Azure SQL Database nebo SQL Server databází. Odběratel na jedné nebo sdružené databáze musí být nakonfigurován jako push odběratel. 

| Role | Jednoduché a sdružené databáze | Databáze instancí |
| :----| :------------- | :--------------- |
| **Vydavatel** | Ne | Ano | 
| **Distributor** | Ne | Ano|
| **Vyžádat odběratele** | Ne | Ano|
| **Nabízený odběratel**| Ano | Ano|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > Odběr vyžádat není podporována, pokud distributor je databáze instance a odběratel není. 

Existují různé [typy replikace](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication):


| Replikace | Jednoduché a sdružené databáze | Databáze instancí|
| :----| :------------- | :--------------- |
| [**Standardní transakční**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) | Ano (pouze jako předplatitel) | Ano | 
| [**Snímek**](https://docs.microsoft.com/sql/relational-databases/replication/snapshot-replication) | Ano (pouze jako předplatitel) | Ano|
| [**Slučovací replikace**](https://docs.microsoft.com/sql/relational-databases/replication/merge/merge-replication) | Ne | Ne|
| [**Peer-to-peer**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | Ne | Ne|
| [**Obousměrné**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | Ne | Ano|
| [**Aktualizovatelná předplatná**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | Ne | Ne|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > - Pokus o konfiguraci replikace pomocí starší verze může mít za následek číslo chyby MSSQL_REPL20084 \<(Proces se nemohl připojit k odběrateli.) a MSSQ_REPL40532 (Nelze otevřít název serveru> požadovaný přihlášením. Přihlášení se nezdařilo.)
  > - Chcete-li používat všechny funkce Azure SQL Database, musíte používat nejnovější verze [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) a SQL Server Data Tools [(SSDT).](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)
  
  ### <a name="supportability-matrix-for-instance-databases-and-on-premises-systems"></a>Matice supportability pro databáze instancí a místní systémy
  Matice podpory replikace pro databáze instancí je stejná jako matice pro SQL Server v místním prostředí. 
  
| **Vydavatel**   | **Distributor** | **Odběratel** |
| :------------   | :-------------- | :------------- |
| SQL Server 2019 | SQL Server 2019 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/>  |
| SQL Server 2017 | SQL Server 2019 <br/>SQL Server 2017 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 |
| SQL Server 2016 | SQL Server 2019 <br/>SQL Server 2017 <br/> SQL Server 2016 | SQL Server 2019 <br/> SQL Server 2017 <br/>SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 |
| SQL Server 2014 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>| SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2012 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> | SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 | 
| SQL Server 2008 R2 <br/> SQL Server 2008 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |  SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 <br/>  |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="requirements"></a>Požadavky

- Při připojování mezi účastníky replikace se používá ověřování SQL. 
- Sdílená sada účtu úložiště Azure pro pracovní adresář používaný replikací. 
- Port 445 (TCP odchozí) musí být otevřena v pravidlech zabezpečení podsítě spravované instance pro přístup ke sdílené složce Azure. 
- Port 1433 (TCP odchozí) je třeba otevřít, pokud Vydavatel/Distributor jsou na spravovanou instanci a odběratel je místní.
- Všechny typy účastníků replikace (Vydavatel, Distributor, Vyžádat odběratel a Push odběratel) lze umístit na spravované instance, ale vydavatel a distributor musí být buď v cloudu nebo oba místní.
- Pokud vydavatel, distributor a/nebo odběratel existují v různých virtuálních sítích, pak musí být mezi jednotlivými entitami vytvořen partnerský vztah VPN, takže mezi vydavatelem a distributorem existuje propojení VPN a/nebo je partnerský vztah VPN mezi distributorem a odběratelem. 


>[!NOTE]
> - Při připojování k souboru úložiště Azure se může vyskytnout chyba 53, pokud je port 445 odchozí sítě (NSG) blokován, když je distributor databáze instancí a odběratel je místní. Chcete-li tento problém vyřešit, aktualizujte soubor [NSG virtuální sítě.](/azure/storage/files/storage-troubleshoot-windows-file-connection-problems) 


### <a name="compare-data-sync-with-transactional-replication"></a>Porovnat synchronizaci dat s transakční replikací

| | Synchronizace dat | Transakční replikace |
|---|---|---|
| Výhody | - Aktivní-aktivní podpora<br/>- Obousměrný mezi místní a Azure SQL Database | - Nižší latence<br/>- Transakční konzistence<br/>- Opětovné použití existující topologie po migraci |
| Nevýhody | - 5 minut nebo více latence<br/>- Žádná transakční konzistence<br/>- Vyšší dopad na výkon | - Nelze publikovat z Azure SQL Database jednu databázi nebo sdružené databáze<br/>- Vysoké náklady na údržbu |
| | | |

## <a name="common-configurations"></a>Běžné konfigurace

Obecně platí, že vydavatel a distributor musí být buď v cloudu nebo místně. Podporují se tyto konfigurace: 

### <a name="publisher-with-local-distributor-on-a-managed-instance"></a>Vydavatel s místním distributorem ve spravované instanci

![Jedna instance jako vydavatel a distributor](media/replication-with-sql-database-managed-instance/01-single-instance-asdbmi-pubdist.png)

Vydavatel a distributor jsou konfigurováni v rámci jedné spravované instance a distribuují změny do jiné spravované instance, jedné databáze, sdružené databáze nebo sql serveru místně. 

### <a name="publisher-with-remote-distributor-on-a-managed-instance"></a>Vydavatel se vzdáleným distributorem ve spravované instanci

V této konfiguraci jedna spravovaná instance publikuje změny distributora umístěného na jiné spravované instanci, které mohou obsluhovat mnoho zdrojových spravovaných instancí a distribuovat změny jednoho nebo více cílů na spravované instanci, jedné databázi, sdružené databázi nebo SQL Server.

![Samostatné instance pro vydavatele a distributora](media/replication-with-sql-database-managed-instance/02-separate-instances-asdbmi-pubdist.png)

Vydavatel a distributor jsou konfigurovány na dvou spravovaných instancích. Existují určitá omezení s touto konfigurací: 

- Obě spravované instance jsou na stejné virtuální síti.
- Obě spravované instance jsou ve stejném umístění.


### <a name="publisher-and-distributor-on-premises-with-a-subscriber-on-a-single-pooled-and-instance-database"></a>Vydavatel a místní distributor s odběratelem v databázi jedné, sdružené a instancí 

![Azure SQL DB jako odběratel](media/replication-with-sql-database-managed-instance/03-azure-sql-db-subscriber.png)
 
V této konfiguraci je azure SQL database (jedna, sdružená a databáze instancí) odběratelem. Tato konfigurace podporuje migraci z místního do Azure. Pokud odběratel je na jedné nebo sdružené databáze, musí být v režimu push.  

## <a name="with-failover-groups"></a>Se skupinami s podporou převzetí služeb při selhání

Pokud je geografická replikace povolena v instanci **vydavatele** nebo **distributora** ve [skupině s podporou převzetí služeb při selhání](sql-database-auto-failover-group.md), musí správce spravované instance vyčistit všechny publikace na staré primární a po převzetí služeb při selhání je překonfigurovat na nové primární. V tomto scénáři jsou potřeba následující aktivity:

1. Zastavte všechny úlohy replikace spuštěné v databázi, pokud existují.
2. Vyřadit metadata předplatného z vydavatele spuštěním následujícího skriptu v databázi vydavatele:

   ```sql
   EXEC sp_dropsubscription @publication='<name of publication>', @article='all',@subscriber='<name of subscriber>'
   ```             
 
1. Drop metadata předplatného od odběratele. Spusťte následující skript v databázi odběr ů v instanci odběratele:

   ```sql
   EXEC sp_subscription_cleanup
      @publisher = N'<full DNS of publisher, e.g. example.ac2d23028af5.database.windows.net>', 
      @publisher_db = N'<publisher database>', 
      @publication = N'<name of publication>'; 
   ```                

1. Vynuceným vyřadit všechny replikační objekty z vydavatele spuštěním následujícího skriptu v publikované databázi:

   ```sql
   EXEC sp_removedbreplication
   ```

1. Forcefully drop starý distributor z původní primární instance (pokud převzetí selhání zpět na staré primární, který míval distributor). Spusťte následující skript v hlavní databázi ve staré instanci spravované distributorem:

   ```sql
   EXEC sp_dropdistributor 1,1
   ```

Pokud je geografická replikace povolena v instanci **odběratele** ve skupině s podporou převzetí služeb při selhání, publikace by měla být nakonfigurována tak, aby se připojila k koncovému bodu naslouchací proces skupiny s podporou převzetí služeb při selhání pro instanci spravované odběratelem. V případě převzetí služeb při selhání závisí následná akce správce spravované instance na typu převzetí služeb při selhání, ke kterému došlo: 

- Pro převzetí služeb při selhání bez ztráty dat replikace bude pokračovat v práci po převzetí služeb při selhání. 
- Pro převzetí služeb při selhání se ztrátou dat bude replikace fungovat také. Znovu replikuje ztracené změny. 
- Pro převzetí služeb při selhání se ztrátou dat, ale ztráta dat je mimo období uchování distribuční databáze, správce spravované instance bude muset znovu inicializovat databázi předplatného. 

## <a name="next-steps"></a>Další kroky

- [Konfigurace replikace mezi vydavatelem a odběratelem rozhraní MI](replication-with-sql-database-managed-instance.md)
- [Konfigurace replikace mezi vydavatelem MI, distributorem MI a odběratelem serveru SQL Server](sql-database-managed-instance-configure-replication-tutorial.md)
- [Vytvořte publikaci](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication).
- [Vytvořte nabízené předplatné](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription) pomocí názvu serveru Azure SQL Database `N'azuresqldbdns.database.windows.net` jako odběratele (například a název databáze Azure SQL jako cílové databáze (například **Adventureworks**. )


Další informace o konfiguraci transakční replikace naleznete v následujících kurzech:



## <a name="see-also"></a>Viz také  

- [Replikace s mi a skupinou převzetí služeb při selhání](sql-database-managed-instance-transact-sql-information.md#replication)
- [Replikace do služby SQL Database](replication-to-sql-database.md)
- [Replikace na spravovanou instanci](replication-with-sql-database-managed-instance.md)
- [Vytvoření publikace](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Vytvoření nabízeného předplatného](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Typy replikace](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Monitorování (replikace)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Inicializaci předplatného](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
