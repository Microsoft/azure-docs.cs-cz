---
title: Transakční replikace
titleSuffix: Azure SQL Managed Instance
description: Přečtěte si o použití SQL Server transakční replikace se službou Azure SQL Managed instance (Preview).
services: sql-database
ms.service: sql-managed-instance
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein
ms.date: 04/20/2020
ms.openlocfilehash: a335f6ac015397ba2b2634d0d604c194a768260a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91283203"
---
# <a name="transactional-replication-with-azure-sql-managed-instance-preview"></a>Transakční replikace se spravovanou instancí Azure SQL (Preview)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Transakční replikace je funkce spravované instance Azure SQL a SQL Server, která umožňuje replikovat data z tabulky ve spravované instanci Azure SQL nebo instance SQL Server do tabulek umístěných ve vzdálených databázích. Tato funkce umožňuje synchronizovat více tabulek v různých databázích. 

Transakční replikace je aktuálně ve verzi Public Preview pro spravovanou instanci SQL. 

## <a name="overview"></a>Přehled

Transakční replikaci můžete použít k nahrání změn provedených ve spravované instanci SQL Azure na:

- Místní databáze SQL Server nebo na VIRTUÁLNÍm počítači Azure
- Databáze v Azure SQL Database
- Databáze instancí ve spravované instanci Azure SQL

  > [!NOTE]
  > Pokud chcete používat všechny funkce spravované instance SQL Azure, musíte používat nejnovější verze [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) a [SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).

### <a name="components"></a>Komponenty

Klíčovými součástmi transakční replikace jsou **Vydavatel**, **distributor**a **předplatitel**, jak je znázorněno na následujícím obrázku:  

![replikace s SQL Database](./media/replication-transactional-overview/replication-to-sql-database.png)

| Role | Azure SQL Database | Spravovaná instance Azure SQL |
| :----| :------------- | :--------------- |
| **Publisher** | No | Yes |
| **Rozdělovač** | No | Yes|
| **Předplatitel pro vyžádání obsahu** | No | Yes|
| **Předplatitel nabízených oznámení**| Yes | Yes|
| &nbsp; | &nbsp; | &nbsp; |

**Vydavatel** zveřejňuje změny provedené v některých tabulkách (články) odesláním aktualizací distributorovi. Vydavatelem může být spravovaná instance Azure SQL nebo instance SQL Server.

**Distributor** shromažďuje změny v článcích od vydavatele a distribuuje je odběratelům. Distributor může být buď spravovaná instance Azure SQL, nebo instance SQL Server (libovolná verze, pokud je stejná nebo vyšší než verze vydavatele).

**Předplatitel** obdrží změny provedené na vydavateli. Instance SQL Server a Azure SQL Managed instance můžou být nabízené a vyžádané předplatitele, i když je distributorem spravovaná instance Azure SQL a předplatitel není. Databáze v Azure SQL Database může být pouze předplatitel nabízených oznámení.

Spravovaná instance Azure SQL může podporovat předplatitele z následujících verzí SQL Server:

- SQL Server 2016 a novější
- SQL Server 2014 [RTM CU10 (12.0.4427.24)](https://support.microsoft.com/help/3094220/cumulative-update-10-for-sql-server-2014) nebo [SP1 CU3 (12.0.2556.4)](https://support.microsoft.com/help/3094221/cumulative-update-3-for-sql-server-2014-service-pack-1)
- SQL Server 2012 [SP2 CU8 (11.0.5634.1)](https://support.microsoft.com/help/3082561/cumulative-update-8-for-sql-server-2012-sp2) nebo [SP3 (11.0.6020.0)](https://www.microsoft.com/download/details.aspx?id=49996)

   > [!NOTE]
   >
   > - Pro jiné verze SQL Server, které nepodporují publikování do objektů v Azure, je možné použít metodu [opětovného publikování dat](https://docs.microsoft.com/sql/relational-databases/replication/republish-data) k přesunu dat do novějších verzí SQL Server.
   > - Pokud se pokusíte nakonfigurovat replikaci pomocí starší verze, může dojít k chybě MSSQL_REPL20084 (proces se nemohl připojit k odběrateli) a MSSQ_REPL40532 (nemůže otevřít server \<name> požadovaný přihlášením. Přihlášení se nezdařilo.)

### <a name="types-of-replication"></a>Typy replikace

Existují různé [typy replikace](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication):

| Replikace | Azure SQL Database | Spravovaná instance Azure SQL |
| :----| :------------- | :--------------- |
| [**Standardní transakční**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) | Ano (jenom jako předplatitel) | Yes |
| [**Snímek**](https://docs.microsoft.com/sql/relational-databases/replication/snapshot-replication) | Ano (jenom jako předplatitel) | Yes|
| [**Sloučit replikaci**](https://docs.microsoft.com/sql/relational-databases/replication/merge/merge-replication) | No | No|
| [**Peer-to-peer**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | No | No|
| [**Obousměrné**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | No | Yes|
| [**Odběry, které by možné aktualizovat**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | No | No|
| &nbsp; | &nbsp; | &nbsp; |

### <a name="supportability-matrix"></a>Matice podpory

  Matice podpory transakční replikace pro spravovanou instanci SQL Azure je stejná jako ta pro SQL Server.
  
| **Publisher**   | **Rozdělovač** | **Roli** |
| :------------   | :-------------- | :------------- |
| SQL Server 2019 | SQL Server 2019 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/>  |
| SQL Server 2017 | SQL Server 2019 <br/>SQL Server 2017 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 |
| SQL Server 2016 | SQL Server 2019 <br/>SQL Server 2017 <br/> SQL Server 2016 | SQL Server 2019 <br/> SQL Server 2017 <br/>SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 |
| SQL Server 2014 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>| SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2012 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> | SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2008 R2 <br/> SQL Server 2008 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |  SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 <br/>  |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="when-to-use"></a>Kdy je použít

Transakční replikace je užitečná v následujících scénářích:

- Publikování změn provedených v jedné nebo více tabulkách v databázi a jejich distribuce do jedné nebo více databází v instanci SQL Server nebo v Azure SQL Database, která předplatila pro změny.
- Udržujte několik distribuovaných databází v synchronizovaném stavu.
- Migrujte databáze z jedné instance SQL Server nebo spravované instance Azure SQL do jiné databáze tím, že se změny průběžně publikují.

### <a name="compare-data-sync-with-transactional-replication"></a>Porovnání synchronizace dat s transakční replikací

| Kategorie | Synchronizace dat | Transakční replikace |
|---|---|---|
| Výhody | – Aktivní – aktivní podpora<br/>– Obousměrné mezi místními a Azure SQL Database | – Nižší latence<br/>– Transakční konzistence<br/>-Opětovné použití existující topologie po migraci |
| Nevýhody | – 5 min nebo větší latence<br/>-Žádná transakční konzistence<br/>– Vyšší dopad na výkon | -Nelze publikovat z Azure SQL Database <br/>– Náklady vysoké údržby |

## <a name="common-configurations"></a>Běžné konfigurace

Obecně platí, že vydavatel a distributor musí být v cloudu i v místním prostředí. Podporují se tyto konfigurace:

### <a name="publisher-with-local-distributor-on-sql-managed-instance"></a>Vydavatel s místním distributorem na spravované instanci SQL

![Jediná instance jako Vydavatel a distributor](./media/replication-transactional-overview/01-single-instance-asdbmi-pubdist.png)

Vydavatel a distributor jsou nakonfigurovány v rámci jedné spravované instance SQL a distribuují změny do jiné spravované instance SQL, SQL Database nebo instance SQL Server.

### <a name="publisher-with-remote-distributor-on-sql-managed-instance"></a>Vydavatel se vzdáleným distributorem na spravované instanci SQL

V této konfiguraci publikuje jedna spravovaná instance změny distributora, který je umístěný na jiné spravované instanci SQL, která může obsluhovat mnoho zdrojových spravovaných instancí SQL a distribuovat změny do jednoho nebo více cílů na Azure SQL Database, spravované instance Azure SQL nebo SQL Server.

![Samostatné instance pro vydavatele a distributora](./media/replication-transactional-overview/02-separate-instances-asdbmi-pubdist.png)

Vydavatel a distributor jsou nakonfigurovány na dvou spravovaných instancích. Tato konfigurace má několik omezení:

- Obě spravované instance jsou ve stejné virtuální síti.
- Obě spravované instance jsou ve stejném umístění.

### <a name="on-premises-publisherdistributor-with-remote-subscriber"></a>Místní vydavatel nebo distributor se vzdáleným předplatitelem

![Azure SQL Database jako předplatitel](./media/replication-transactional-overview/03-azure-sql-db-subscriber.png)

V této konfiguraci je databáze v Azure SQL Database nebo spravované instanci SQL Azure předplatitelem. Tato konfigurace podporuje migraci z místního prostředí do Azure. Pokud je odběratel databáze v Azure SQL Database, musí být v režimu push.  

## <a name="requirements"></a>Požadavky

- Použijte ověřování SQL pro připojení mezi účastníky replikace.
- Pro pracovní adresář používaný při replikaci použijte sdílenou složku Azure Storage účtu.
- Pro přístup ke sdílené složce Azure otevřete odchozí port TCP 445 v pravidlech zabezpečení podsítě.
- Otevřete odchozí port TCP 1433, pokud je spravovaná instance SQL Vydavatel nebo distributor, a předplatitel není. Je také možné, že budete muset změnit pravidlo zabezpečení NSG Managed instance SQL pro `allow_linkedserver_outbound` **značku cílové služby** port 1433 z `virtualnetwork` na `internet` .
- Umístěte vydavatele i distributora do cloudu nebo do místního prostředí.
- Nakonfigurujte partnerský vztah VPN mezi virtuálními sítěmi účastníků replikace, pokud se virtuální sítě liší.

> [!NOTE]
> Při připojování k souboru Azure Storage může dojít k chybě 53, pokud je NSG port 445 pro skupinu zabezpečení sítě (), když je distributor databáze spravované instance Azure SQL a předplatitel místní. Pokud chcete tento problém vyřešit, [aktualizujte NSG virtuální](/azure/storage/files/storage-troubleshoot-windows-file-connection-problems) sítě.

## <a name="with-failover-groups"></a>Se skupinami převzetí služeb při selhání

[Aktivní geografická replikace](../database/active-geo-replication-overview.md) není u spravované instance SQL podporována pomocí transakční replikace. Místo aktivní geografické replikace používejte [skupiny s automatickým převzetím služeb při selhání](../database/auto-failover-group-overview.md), ale Všimněte si, že při převzetí služeb při selhání se publikace musí [ručně odstranit](transact-sql-tsql-differences-sql-server.md#replication) z primární spravované instance a znovu vytvořit v sekundární spravované instanci SQL.

Pokud je geografická replikace povolená u **vydavatele** nebo **distributora** spravované instance SQL ve [skupině převzetí služeb při selhání](../database/auto-failover-group-overview.md), musí správce spravované instance SQL vyčistit všechny publikace na staré primární primární databázi a po převzetí služeb při selhání je znovu nakonfigurovat na nové primární úrovni. V tomto scénáři jsou potřeba následující aktivity:

1. Zastavte všechny úlohy replikace běžící v databázi, pokud existují.
1. Z vydavatele vyřaďte metadata odběru spuštěním následujícího skriptu v databázi vydavatele:

   ```sql
   EXEC sp_dropsubscription @publication='<name of publication>', @article='all',@subscriber='<name of subscriber>'
   ```

1. Odkládací metadata odběru od odběratele. Spusťte následující skript v databázi předplatného na spravované instanci SQL odběratele:

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

1. Nuceně odstranit starého distributora z původní primární spravované instance SQL (Pokud dojde k převzetí služeb při selhání do staré primární služby, která se použila k distributorovi). Spusťte následující skript v hlavní databázi v původní distributorské spravované instanci SQL:

   ```sql
   EXEC sp_dropdistributor 1,1
   ```

Pokud je geografická replikace povolená u instance **předplatitele** ve skupině převzetí služeb při selhání, měla by se tato publikace nakonfigurovat pro připojení ke koncovému bodu naslouchacího procesu skupiny převzetí služeb při selhání pro spravovanou instanci odběratele. V případě převzetí služeb při selhání závisí následná akce správce spravované instance na typu převzetí služeb při selhání, ke kterému došlo:

- V případě převzetí služeb při selhání bez ztráty dat bude replikace po převzetí služeb při selhání pokračovat v práci.
- V případě převzetí služeb při selhání se ztrátou dat bude replikace fungovat také. Bude znovu replikován ztracené změny.
- V případě převzetí služeb při selhání se ztrátou dat, ale ztráta dat je mimo dobu uchování distribuční databáze, bude muset správce spravované instance SQL znovu inicializovat databázi odběrů.

## <a name="next-steps"></a>Další kroky

Další informace o konfiguraci transakční replikace najdete v následujících kurzech:

- [Konfigurace replikace mezi vydavatelem a odběratelem spravované instance SQL](../managed-instance/replication-between-two-instances-configure-tutorial.md)
- [Konfigurace replikace mezi vydavatelem spravované instance SQL, distributorem spravované instance SQL a předplatitelem SQL Server](../managed-instance/replication-two-instances-and-sql-server-configure-tutorial.md)
- [Vytvořte publikaci](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication).
- [Vytvořte nabízený odběr](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription) pomocí názvu serveru jako odběratele (například `N'azuresqldbdns.database.windows.net` a databáze v Azure SQL Database název jako cílovou databázi (například **AdventureWorks**). )

## <a name="see-also"></a>Viz také  

- [Replikace pomocí spravované instance SQL a skupiny převzetí služeb při selhání](transact-sql-tsql-differences-sql-server.md#replication)
- [Replikace do služby SQL Database](../database/replication-to-sql-database.md)
- [Replikace do spravované instance](../managed-instance/replication-between-two-instances-configure-tutorial.md)
- [Vytvoření publikace](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Vytvoření nabízeného odběru](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Typy replikace](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Monitorování (replikace)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Inicializovat předplatné](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
