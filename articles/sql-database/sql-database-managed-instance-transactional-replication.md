---
title: Transakční replikace
description: Přečtěte si o použití SQL Server transakční replikace s databázemi s jedním, sdruženými a instancemi v Azure SQL Database.
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
ms.openlocfilehash: 2a048ddefbcd76193436da13cd3ba68b8b6ffb0a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80607600"
---
# <a name="transactional-replication-with-single-pooled-and-instance-databases-in-azure-sql-database"></a>Transakční replikace s databázemi s jednou, sdruženými a instancemi v Azure SQL Database

Transakční replikace je funkce Azure SQL Database a SQL Server, která umožňuje replikovat data z tabulky v Azure SQL Database nebo SQL Server do tabulek umístěných ve vzdálených databázích. Tato funkce umožňuje synchronizovat více tabulek v různých databázích.

## <a name="when-to-use-transactional-replication"></a>Kdy použít transakční replikaci

Transakční replikace je užitečná v následujících scénářích:
- Publikování změn provedených v jedné nebo více tabulkách v databázi a jejich distribuce do jedné nebo mnoha SQL Server nebo databází Azure SQL, které se přihlásily k odběru změn.
- Udržujte několik distribuovaných databází v synchronizovaném stavu.
- Migrujte databáze z jedné SQL Server nebo spravované instance do jiné databáze průběžným publikováním změn.

## <a name="overview"></a>Přehled

Klíčové komponenty v transakční replikaci jsou uvedené na následujícím obrázku:  

![replikace s SQL Database](media/replication-to-sql-database/replication-to-sql-database.png)

**Vydavatel** je instance nebo server, který publikuje změny provedené v některých tabulkách (články) odesláním aktualizací distributorovi. Publikování do jakékoli databáze SQL Azure z místního SQL Server podporuje následující verze SQL Server:

- SQL Server 2019 (Preview)
- SQL Server 2016 na SQL 2017
- SQL Server 2014 SP1 CU3 nebo vyšší (12.00.4427)
- SQL Server 2014 RTM CU10 (12.00.2556)
- SQL Server 2012 SP3 nebo novější (11.0.6020)
- SQL Server 2012 SP2 CU8 (11.0.5634.0)
- Pro jiné verze SQL Server, které nepodporují publikování do objektů v Azure, je možné použít metodu [opětovného publikování dat](https://docs.microsoft.com/sql/relational-databases/replication/republish-data) k přesunu dat do novějších verzí SQL Server. 

**Distributor** je instance nebo server, který shromažďuje změny v článcích od vydavatele a distribuuje je odběratelům. Distributor může být buď Azure SQL Database spravovaná instance, nebo SQL Server (jakákoli verze, pokud je větší nebo rovna verzi vydavatele). 

**Předplatitelem** je instance nebo server, který přijímá změny provedené u vydavatele. Předplatitelé můžou být databáze jedné, sdružené a instance v Azure SQL Database nebo databázích SQL Server. Předplatitel v jedné nebo sdružené databázi musí být nakonfigurovaný jako předplatitel nabízení. 

| Role | Databáze s jednou a fondem | Databáze instancí |
| :----| :------------- | :--------------- |
| **Microsoft** | Ne | Ano | 
| **Rozdělovač** | Ne | Ano|
| **Předplatitel pro vyžádání obsahu** | Ne | Ano|
| **Předplatitel nabízených oznámení**| Ano | Ano|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > Vyžádané odběry se nepodporují, pokud je distributor instancí databáze a předplatitel není. 

Existují různé [typy replikace](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication):


| Replikace | Databáze s jednou a fondem | Databáze instancí|
| :----| :------------- | :--------------- |
| [**Standardní transakční**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) | Ano (jenom jako předplatitel) | Ano | 
| [**Snímek**](https://docs.microsoft.com/sql/relational-databases/replication/snapshot-replication) | Ano (jenom jako předplatitel) | Ano|
| [**Sloučit replikaci**](https://docs.microsoft.com/sql/relational-databases/replication/merge/merge-replication) | Ne | Ne|
| [**Peer-to-peer**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | Ne | Ne|
| [**Obousměrné**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | Ne | Ano|
| [**Odběry, které by možné aktualizovat**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | Ne | Ne|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > - Pokud se pokusíte nakonfigurovat replikaci pomocí starší verze, může dojít k chybě MSSQL_REPL20084 (proces se nemohl připojit k odběrateli) a MSSQ_REPL40532 (nemůže otevřít název serveru \<> požadovaný přihlášením. Přihlášení se nezdařilo.)
  > - Pokud chcete používat všechny funkce Azure SQL Database, musíte používat nejnovější verze [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) a [Nástroje pro SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).
  
  ### <a name="supportability-matrix-for-instance-databases-and-on-premises-systems"></a>Matice podpory pro databáze instancí a místní systémy
  Matice podpory replikace pro databáze instancí je stejná jako ta pro SQL Server v místním prostředí. 
  
| **Microsoft**   | **Rozdělovač** | **Roli** |
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
- Sdílená složka účtu Azure Storage pro pracovní adresář používaný replikací. 
- Port 445 (odchozí TCP) musí být otevřený v pravidlech zabezpečení podsítě spravované instance pro přístup ke sdílené složce Azure. 
- Port 1433 (odchozí TCP) je nutné otevřít, pokud je Vydavatel nebo distributor na spravované instanci a předplatitel není. Je také možné, že budete muset změnit pravidlo pro odchozí zabezpečení NSG spravované `allow_linkedserver_outbound` instance pro pro **tag cílové služby** port 1433 `virtualnetwork` z `internet`na. 
- Všechny typy účastníků replikace (vydavatel, distributor, předplatitelé pro vyžádání obsahu a nabízený předplatitelé) se dají umístit na spravované instance, ale Vydavatel a distributor musí být buď v cloudu, nebo v místním prostředí.
- Pokud má Vydavatel, distributor a/nebo předplatitele v různých virtuálních sítích, musí být mezi jednotlivými entitami navázán partnerský vztah sítě VPN, aby bylo možné vytvořit partnerský vztah VPN mezi vydavatelem a distributorem a/nebo že mezi distributorem a předplatitelem existuje partnerský vztah VPN. 


>[!NOTE]
> - Při připojování k souboru Azure Storage se může zobrazit chyba 53, pokud je v případě, že je distributor instancí databáze a předplatitelem instance, zablokovaný port 445 pro skupinu zabezpečení sítě (NSG). Pokud chcete tento problém vyřešit, [aktualizujte NSG virtuální](/azure/storage/files/storage-troubleshoot-windows-file-connection-problems) sítě. 


### <a name="compare-data-sync-with-transactional-replication"></a>Porovnání synchronizace dat s transakční replikací

| | Synchronizace dat | Transakční replikace |
|---|---|---|
| Výhody | – Aktivní – aktivní podpora<br/>– Obousměrné mezi místními a Azure SQL Database | – Nižší latence<br/>– Transakční konzistence<br/>-Opětovné použití existující topologie po migraci |
| Nevýhody | – 5 min nebo větší latence<br/>-Žádná transakční konzistence<br/>– Vyšší dopad na výkon | -Nelze publikovat z Azure SQL Database izolované databáze nebo databáze ve fondu<br/>– Náklady vysoké údržby |
| | | |

## <a name="common-configurations"></a>Běžné konfigurace

Obecně platí, že vydavatel a distributor musí být v cloudu i v místním prostředí. Podporují se tyto konfigurace: 

### <a name="publisher-with-local-distributor-on-a-managed-instance"></a>Vydavatel s místním distributorem na spravované instanci

![Jediná instance jako Vydavatel a distributor](media/replication-with-sql-database-managed-instance/01-single-instance-asdbmi-pubdist.png)

Vydavatel a distributor jsou konfigurovány v rámci jedné spravované instance a distribuují změny jiné spravované instance, izolované databáze, databáze ve fondu nebo SQL Server místně. 

### <a name="publisher-with-remote-distributor-on-a-managed-instance"></a>Vydavatel se vzdáleným distributorem na spravované instanci

V této konfiguraci publikuje jedna spravovaná instance změny distributora umístěného na jiné spravované instanci, která může obsluhovat mnoho zdrojových spravovaných instancí a distribuovat změny do jednoho nebo více cílů na spravované instanci, izolovanou databázi, databázi ve fondu nebo SQL Server.

![Samostatné instance pro vydavatele a distributora](media/replication-with-sql-database-managed-instance/02-separate-instances-asdbmi-pubdist.png)

Vydavatel a distributor jsou nakonfigurovány na dvou spravovaných instancích. Tato konfigurace má několik omezení: 

- Obě spravované instance jsou ve stejné virtuální síti.
- Obě spravované instance jsou ve stejném umístění.


### <a name="publisher-and-distributor-on-premises-with-a-subscriber-on-a-single-pooled-and-instance-database"></a>Vydavatel a distributor v místním prostředí s předplatitelem v jedné, sdružené a instance databázi 

![Azure SQL DB jako předplatitel](media/replication-with-sql-database-managed-instance/03-azure-sql-db-subscriber.png)
 
V této konfiguraci je Azure SQL Database (jedna, sdružená a databáze instance) předplatitel. Tato konfigurace podporuje migraci z místního prostředí do Azure. Pokud se odběratel nachází v jedné nebo sdružené databázi, musí být v režimu push.  

## <a name="with-failover-groups"></a>Se skupinami převzetí služeb při selhání

Pokud je geografická replikace povolená u **vydavatele** nebo instance **distributora** ve [skupině převzetí služeb při selhání](sql-database-auto-failover-group.md), musí správce spravované instance vyčistit všechny publikace na staré primární primární úrovni a po převzetí služeb při selhání je znovu nakonfigurovat na nové primární úrovni. V tomto scénáři jsou potřeba následující aktivity:

1. Zastavte všechny úlohy replikace běžící v databázi, pokud existují.
2. Z vydavatele vyřaďte metadata odběru spuštěním následujícího skriptu v databázi vydavatele:

   ```sql
   EXEC sp_dropsubscription @publication='<name of publication>', @article='all',@subscriber='<name of subscriber>'
   ```             
 
1. Odkládací metadata odběru od odběratele. Spusťte následující skript v databázi předplatného na instanci předplatitele:

   ```sql
   EXEC sp_subscription_cleanup
      @publisher = N'<full DNS of publisher, e.g. example.ac2d23028af5.database.windows.net>', 
      @publisher_db = N'<publisher database>', 
      @publication = N'<name of publication>'; 
   ```                

1. Vynuceně vyřaďte všechny replikační objekty od vydavatele spuštěním následujícího skriptu v publikované databázi:

   ```sql
   EXEC sp_removedbreplication
   ```

1. Nuceně odstranit starého distributora z původní primární instance (Pokud dojde k převzetí služeb při selhání do staré primární služby, která se použila k distributorovi). Spusťte následující skript v hlavní databázi v původní spravované instanci distributora:

   ```sql
   EXEC sp_dropdistributor 1,1
   ```

Pokud je geografická replikace povolená u instance **předplatitele** ve skupině převzetí služeb při selhání, měla by se tato publikace nakonfigurovat pro připojení ke koncovému bodu naslouchacího procesu skupiny převzetí služeb při selhání pro spravovanou instanci odběratele. V případě převzetí služeb při selhání závisí následná akce správce spravované instance na typu převzetí služeb při selhání, ke kterému došlo: 

- V případě převzetí služeb při selhání bez ztráty dat bude replikace po převzetí služeb při selhání pokračovat v práci. 
- V případě převzetí služeb při selhání se ztrátou dat bude replikace fungovat také. Bude znovu replikován ztracené změny. 
- V případě převzetí služeb při selhání se ztrátou dat, ale ztráta dat je mimo dobu uchování distribuční databáze, bude muset správce spravované instance znovu inicializovat databázi odběrů. 

## <a name="next-steps"></a>Další kroky

- [Konfigurace replikace mezi vydavatelem a předplatitelem MI](replication-with-sql-database-managed-instance.md)
- [Konfigurace replikace mezi vydavatelem MI, distributorem v mi a předplatitelem SQL Server](sql-database-managed-instance-configure-replication-tutorial.md)
- [Vytvořte publikaci](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication).
- [Vytvořte nabízený odběr](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription) pomocí názvu serveru Azure SQL Database jako předplatitele (například `N'azuresqldbdns.database.windows.net` název Azure SQL Database jako cílovou databázi (například **AdventureWorks**). )


Další informace o konfiguraci transakční replikace najdete v následujících kurzech:



## <a name="see-also"></a>Viz také  

- [Replikace se skupinou MI a převzetí služeb při selhání](sql-database-managed-instance-transact-sql-information.md#replication)
- [Replikace do služby SQL Database](replication-to-sql-database.md)
- [Replikace do spravované instance](replication-with-sql-database-managed-instance.md)
- [Vytvoření publikace](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Vytvoření nabízeného odběru](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Typy replikace](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Monitorování (replikace)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Inicializovat předplatné](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
