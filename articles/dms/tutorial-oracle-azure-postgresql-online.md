---
title: 'Kurz: Migrace oracle online do databáze Azure pro PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Naučte se provádět online migraci z místního řešení Oracle nebo na virtuálních počítačích do Azure Database for PostgreSQL pomocí služby Azure Database Migration Service.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/24/2020
ms.openlocfilehash: 956523e2b51795a4bc97c653dab8b408b06061f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255562"
---
# <a name="tutorial-migrate-oracle-to-azure-database-for-postgresql-online-using-dms-preview"></a>Kurz: Migrace Oracle do databáze Azure pro PostgreSQL online pomocí DMS (Preview)

Pomocí služby Azure Database Migration Service můžete migrovat databáze z databází Oracle hostovaných místně nebo na virtuálních počítačích do [Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/) s minimálními prostoji. Jinými slovy můžete dokončit migraci s minimálními prostoji do aplikace. V tomto kurzu migrujete ukázkovou databázi **hr** z místní instance nebo instance virtuálního počítače Oracle 11g do Azure Database for PostgreSQL pomocí aktivity migrace online ve službě Migrace databáze Azure.

V tomto kurzu se naučíte:
> [!div class="checklist"]
>
> * Vyhodnoťte úsilí o migraci pomocí nástroje ora2pg.
> * Migrujte vzorové schéma pomocí nástroje ora2pg.
> * Vytvořte instanci služby Azure Database Migration Service.
> * Vytvořte projekt migrace pomocí služby Migrace databáze Azure.
> * Spuštění migrace
> * Monitorování migrace

> [!NOTE]
> Použití služby Migrace databáze Azure k provedení migrace online vyžaduje vytvoření instance založené na cenové úrovni Premium.

> [!IMPORTANT]
> Pro optimální prostředí migrace Microsoft doporučuje vytvořit instanci služby Migrace databáze Azure ve stejné oblasti Azure jako cílová databáze. Přenášení dat mezi oblastmi geografickými lokalitami může zpomalit proces migrace a způsobit chyby.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Tento článek popisuje, jak provést online migraci z Oracle do Azure Database pro PostgreSQL.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu je potřeba provést následující:

* Stáhněte a nainstalujte [verzi Oracle 11g 2 (Standard Edition, Standard Edition One nebo Enterprise Edition).](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html)
* Stáhněte si ukázkovou **HR** databázi [zde](https://docs.oracle.com/database/121/COMSC/installation.htm#COMSC00002).
* Stáhněte si a [nainstalujte ora2pg na Windows nebo Linux](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf).
* [Vytvořte instanci v Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal).
* Připojte se k instanci a vytvořte databázi pomocí instrukce v tomto [dokumentu](https://docs.microsoft.com/azure/postgresql/tutorial-design-database-using-azure-portal).
* Vytvořte službu Migrace databáze Microsoft Azure pro Azure pomocí modelu nasazení Azure Resource Manageru, který poskytuje připojení k místním zdrojovým serverům site-to-site pomocí [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Další informace o vytvoření virtuální sítě naleznete v [dokumentaci k virtuální síti](https://docs.microsoft.com/azure/virtual-network/)a zejména v článcích rychlého startu s podrobnými podrobnostmi.

  > [!NOTE]
  > Pokud během instalace virtuální sítě používáte ExpressRoute s partnerským vztahem k síti společnosti Microsoft, přidejte do podsítě, ve které bude služba zřízena, následující [koncové body služby:](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)
  >
  > * Cílový koncový bod databáze (například koncový bod SQL, koncový bod Cosmos DB a tak dále)
  > * Koncový bod úložiště
  > * Koncový bod sběrnice
  >
  > Tato konfigurace je nezbytná, protože služba Azure Database Migration Service nemá připojení k internetu.

* Ujistěte se, že pravidla skupiny zabezpečení sítě virtuální sítě (NSG) neblokují následující příchozí komunikační porty do služby Migrace databáze Azure: 443, 53, 9354, 445, 12000. Další podrobnosti o filtrování přenosů skupin nsg ve virtuální síti naleznete v článku [Filtrování síťového provozu se skupinami zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm).
* Nakonfigurujte bránu [Windows Firewall pro přístup k databázovému stroji](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Otevřete bránu firewall systému Windows a povolte službě Azure Database Migration Service přístup ke zdrojovému serveru Oracle, který je ve výchozím nastavení portem TCP 1521.
* Při použití zařízení brány firewall před zdrojovou databází, budete muset přidat pravidla brány firewall povolit Azure Database Migration Service pro přístup ke zdrojové databáze pro migraci.
* Vytvořte pravidlo [brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) na úrovni serveru pro Azure Database for PostgreSQL, které umožní přístup služby Azure Database Migration Service k cílovým databázím. Zadejte rozsah podsítí virtuální sítě používané pro službu Migrace databáze Azure.
* Povolte přístup ke zdrojovým databázím Oracle.

  > [!NOTE]
  > Role DBA je vyžadována pro připojení uživatele ke zdroji Oracle.

  * Protokoly archivu opakování je vyžadovánpro přírůstkovou synchronizaci ve službě Migrace databáze Azure pro zachycení změny dat. Chcete-li nakonfigurovat zdroj oracle, postupujte takto:
    * Přihlaste se pomocí oprávnění SYSDBA spuštěním následujícího příkazu:

      ```
      sqlplus (user)/(password) as sysdba
      ```

    * Vypněte instanci databáze spuštěním následujícího příkazu.

      ```
      SHUTDOWN IMMEDIATE;
      ```

      Počkejte na `'ORACLE instance shut down'`potvrzení .

    * Spusťte novou instanci a připojte (ale neotevírejte) databázi, abyste povolili nebo zakázali archivaci bu s následujícím příkazem:

      ```
      STARTUP MOUNT;
      ```

      Databáze musí být připojena; počkejte na potvrzení "Instance Oracle spuštěna".

    * Změňte režim archivace databáze spuštěním následujícího příkazu:

      ```
      ALTER DATABASE ARCHIVELOG;
      ```

    * Otevřete databázi pro normální operace spuštěním následujícího příkazu:

      ```
      ALTER DATABASE OPEN;
      ```

      Pravděpodobně bude nutné restartovat soubor ARC.

    * Chcete-li ověřit, spusťte následující příkaz:

      ```
      SELECT log_mode FROM v$database;
      ```

      Měli byste obdržet `'ARCHIVELOG'`odpověď . Pokud je `'NOARCHIVELOG'`odpověď , pak požadavek není splněn.

  * Povolte doplňkové protokolování pro replikaci pomocí jedné z následujících možností.

    * **Možnost 1**.
      Změňte doplňkové protokolování na úrovni databáze tak, aby pokrývalo všechny tabulky pomocí PK a jedinečného indexu. Zjišťování dotazvrátí `'IMPLICIT'`.

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY, UNIQUE) COLUMNS;
      ```

      Změňte doplňkové protokolování na úrovni tabulky. Spouštějte pouze pro tabulky, které mají manipulaci s daty a nemají PKnebo jedinečné indexy.

      ```
      ALTER TABLE [TABLENAME] ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
      ```

    * **Možnost č. 2**.
      Změňte doplňkové protokolování na úrovni databáze tak, aby `'YES'`pokrývalo všechny tabulky, a vrátí dotaz zjišťování .

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
      ```

      Změňte doplňkové protokolování na úrovni tabulky. Postupujte podle níže uvedené logiky a spusťte pouze jeden příkaz pro každou tabulku.

      Pokud má tabulka primární klíč:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY) COLUMNS;
      ```

      Pokud má tabulka jedinečný index:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG GROUP (first unique index columns) ALWAYS;
      ```

      V opačném případě spusťte následující příkaz:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
      ```

    Chcete-li ověřit, spusťte následující příkaz:

      ```
      SELECT supplemental_log_data_min FROM v$database;
      ```

    Měli byste obdržet `'YES'`odpověď .

## <a name="assess-the-effort-for-an-oracle-to-azure-database-for-postgresql-migration"></a>Posouzení úsilí o databázi Oracle to Azure pro migraci PostgreSQL

Doporučujeme použít ora2pg k posouzení úsilí potřebného k migraci z Oracle do Azure Database pro PostgreSQL. Pomocí `ora2pg -t SHOW_REPORT` této směrnice můžete vytvořit sestavu se seznamem všech objektů Oracle, odhadovaných nákladů na migraci (ve dnech vývojářů) a určitých databázových objektů, které mohou vyžadovat zvláštní pozornost jako součást převodu.

Většina zákazníků stráví značné množství času kontrolou hodnotící zprávy a zvážením automatického a ručního převodu.

Pokud chcete nakonfigurovat a spustit ora2pg a vytvořit zprávu o hodnocení, přečtěte si část **Premigrace: Hodnocení** v [kuchařce Oracle to Azure Database for PostgreSQL Cookbook](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf). Vzorek ora2pg hodnotící zpráva je k dispozici pro referenci [zde](https://ora2pg.darold.net/report.html).

## <a name="export-the-oracle-schema"></a>Export schématu Oracle

Doporučujeme použít ora2pg převést schéma Oracle a další objekty Oracle (typy, postupy, funkce atd.) na schéma, které je kompatibilní s Azure Database pro PostgreSQL. ora2pg obsahuje mnoho direktiv, které vám pomohou předem definovat určité datové typy. Například můžete použít `DATA_TYPE` direktivu nahradit všechny NUMBER(*,0) s bigint spíše než NUMERIC(38).

Můžete spustit ora2pg exportovat každý z databázových objektů v .sql soubory. Potom můžete zkontrolovat .sql soubory před jejich importem do Databáze Azure pro PostgreSQL pomocí pSQL nebo můžete spustit . SQL skript v PgAdmin.

```
psql -f [FILENAME] -h [AzurePostgreConnection] -p 5432 -U [AzurePostgreUser] -d database 
```

Například:

```
psql -f %namespace%\schema\sequences\sequence.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database
```

Pokud chcete nakonfigurovat a spustit ora2pg pro převod schématu, přečtěte si část **Migrace: Schéma a data** v [databázi Oracle do Azure pro postgreSQL kuchařku](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf).

## <a name="set-up-the-schema-in-azure-database-for-postgresql"></a>Nastavení schématu v databázi Azure pro PostgreSQL

Před spuštěním kanálu migrace ve službě Azure Database Migration Service můžete převést schémata tabulek Oracle, uložené procedury, balíčky a další databázové objekty tak, aby byly kompatibilní s postgresem pomocí ora2pg. Podívejte se na níže uvedené odkazy, jak pracovat s ora2pg:

* [Instalace ora2pg v systému Windows](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf)
* [Kuchařka migrace Oracle to Azure PostgreSQL](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf)

Služba migrace databáze Azure můžete také vytvořit schéma tabulky PostgreSQL. Služba přistupuje k schématu tabulky v připojeném zdroji Oracle a vytvoří kompatibilní schéma tabulky v Azure Database pro PostgreSQL. Nezapomeňte ověřit a zkontrolovat formát schématu v Azure Database for PostgreSQL po dokončení vytváření schématu a přesunutí dat službou Azure Database Migration Service.

> [!IMPORTANT]
> Služba migrace databáze Azure pouze vytvoří schéma tabulky; jiné databázové objekty, jako jsou uložené procedury, balíčky, indexy atd., nejsou vytvořeny.

Také nezapomeňte vynechat cizí klíč v cílové databázi pro úplné zatížení ke spuštění. Skript, který můžete použít k přetažení cizího klíče, naleznete v části **Migrace ukázkového schématu** [v](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online) článku. Pomocí služby Migrace databáze Azure můžete spustit pro úplné načtení a synchronizaci.

### <a name="when-the-postgresql-table-schema-already-exists"></a>Pokud již schéma tabulky PostgreSQL existuje

Pokud vytvoříte schéma PostgreSQL pomocí nástrojů, jako je ora2pg před spuštěním přesunu dat pomocí služby Migrace databáze Azure, namapujte zdrojové tabulky na cílové tabulky ve službě Migrace databáze Azure.

1. Když vytvoříte nový projekt migrace Oracle to Azure Database for PostgreSQL, budete vyzváni k výběru cílové databáze a schématu cíle v kroku Výběr schémat. Vyplňte cílovou databázi a schéma cíle.

   ![Zobrazení předplatných na portálu](media/tutorial-oracle-azure-postgresql-online/dms-map-to-target-databases.png)

2. Na obrazovce **Nastavení migrace** se zobrazí seznam tabulek ve zdroji Oracle. Služba migrace databáze Azure se pokusí porovnat tabulky ve zdrojové a cílové tabulky na základě názvu tabulky. Pokud existuje více odpovídajících cílových tabulek s různým velikostem písmen, můžete vybrat cílovou tabulku, na kterou chcete mapovat.

    ![Zobrazení předplatných na portálu](media/tutorial-oracle-azure-postgresql-online/dms-migration-settings.png)

> [!NOTE]
> Pokud potřebujete mapovat názvy zdrojových tabulek [dmsfeedback@microsoft.com](mailto:dmsfeedbac@microsoft.com) na tabulky s různými názvy, e-mail a můžeme poskytnout skript pro automatizaci procesu.

### <a name="when-the-postgresql-table-schema-doesnt-exist"></a>Pokud schéma tabulky PostgreSQL neexistuje

Pokud cílová databáze PostgreSQL neobsahuje žádné informace o schématu tabulky, služba migrace databáze Azure převede zdrojové schéma a znovu jej vytvoří v cílové databázi. Nezapomeňte, že služba Migrace databáze Azure vytvoří jenom schéma tabulky, nikoli jiné databázové objekty, jako jsou uložené procedury, balíčky a indexy.
Chcete-li, aby služba Migrace databáze Azure vytvořila schéma za vás, ujistěte se, že vaše cílové prostředí obsahuje schéma bez existujících tabulek. Pokud služba Migrace databáze Azure zjistí libovolnou tabulku, služba předpokládá, že schéma bylo vytvořeno externím nástrojem, jako je ora2pg.

> [!IMPORTANT]
> Služba migrace databáze Azure vyžaduje, aby všechny tabulky byly vytvořeny stejným způsobem, a to buď pomocí služby Migrace databáze Azure, nebo pomocí nástroje, jako je ora2pg, ale ne obojí.

Jak začít:

1. Vytvořte schéma v cílové databázi na základě požadavků aplikace. Ve výchozím nastavení postgresql schéma tabulky a názvy sloupců jsou malá písmena. Schéma tabulky Oracle a sloupce jsou naopak ve výchozím nastavení ve všech velkých a velkých písmenech.
2. V kroku Vybrat schémata zadejte cílovou databázi a cílové schéma.
3. Na základě schématu, které vytvoříte v Databázi Azure pro PostgreSQL, používá Služba migrace databáze Azure následující pravidla transformace:

    Pokud název schématu ve zdroji Oracle a odpovídá tomu v Azure Database for PostgreSQL, pak služba Migrace databáze Azure *vytvoří schéma tabulky pomocí stejného případu jako v cílovém .*

    Například:

    | Schéma zdrojové společnosti Oracle | Cílová databáze PostgreSQL.Schéma | DMS vytvořil schema.table.column |
    | ------------- | ------------- | ------------- |
    | HR | targetHR.public | public.countries.country_id |
    | HR | cílHR.trgthr | trgthr.countries.country_id |
    | HR | targetHR.TARGETHR | "TARGETHR". ZEMĚ"." COUNTRY_ID" |
    | HR | targetHR.HR | "Hr". ZEMĚ"." COUNTRY_ID" |
    | HR | targetHR.Hr | *Nelze mapovat smíšené případy. |

    *Chcete-li vytvořit smíšené schéma případu a názvy tabulek v cílové postgreSQL, kontaktujte [dmsfeedback@microsoft.com](mailto:dmsfeedback@microsoft.com). Můžeme poskytnout skript pro nastavení schématu tabulky smíšených případů v cílové databázi PostgreSQL.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrace poskytovatele prostředků Microsoft.DataMigration

1. Přihlaste se k webu Azure Portal, vyberte **Všechny služby** a pak vyberte **Předplatná**.

   ![Zobrazení předplatných na portálu](media/tutorial-oracle-azure-postgresql-online/portal-select-subscriptions.png)

2. Vyberte předplatné, ve kterém chcete vytvořit instanci služby Azure Database Migration Service, a pak vyberte **Poskytovatelé prostředků**.

    ![Zobrazení poskytovatelů prostředků](media/tutorial-oracle-azure-postgresql-online/portal-select-resource-provider.png)

3. Vyhledejte „migration“ a pak napravo od **Microsoft.DataMigration** vyberte **Zaregistrovat**.

    ![Registrace poskytovatele prostředků](media/tutorial-oracle-azure-postgresql-online/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Vytvoření instance DMS

1. Na webu Azure Portal vyberte **+ Vytvořit prostředek**, vyhledejte „Azure Database Migration Service“ a pak v rozevíracím seznamu vyberte **Azure Database Migration Service**.

    ![Azure Marketplace](media/tutorial-oracle-azure-postgresql-online/portal-marketplace.png)

2. Na obrazovce **Azure Database Migration Service** vyberte **Vytvořit**.

    ![Vytvoření instance služby Azure Database Migration Service](media/tutorial-oracle-azure-postgresql-online/dms-create2.png)
  
3. Na obrazovce **Vytvořit službu Migration Service** zadejte název služby, předplatné a novou nebo existující skupinu prostředků.

4. Vyberte existující virtuální síť nebo vytvořte novou.

    Virtuální síť poskytuje službě Azure Database Migration Service přístup ke zdrojové službě Oracle a cílové instanci Azure Database for PostgreSQL.

    Další informace o tom, jak vytvořit virtuální síť na webu Azure Portal, najdete v článku [Vytvoření virtuální sítě pomocí portálu Azure](https://aka.ms/DMSVnet).

5. Vyberte cenovou úroveň.

    Další informace o nákladech a cenových úrovních najdete na [stránce s cenami](https://aka.ms/dms-pricing).

    ![Konfigurace nastavení instance služby Azure Database Migration Service](media/tutorial-oracle-azure-postgresql-online/dms-settings5.png)

6. Vyberte **Vytvořit** a vytvořte službu.

## <a name="create-a-migration-project"></a>Vytvoření projektu migrace

Po vytvoření služby ji vyhledejte na webu Azure Portal, otevřete ji a pak vytvořte nový projekt migrace.

1. Na webu Azure Portal vyberte **Všechny služby**, vyhledejte „Azure Database Migration Service“ a pak vyberte **Služby Azure Database Migration Service**.

    ![Vyhledání všech instancí služby Azure Database Migration Service](media/tutorial-oracle-azure-postgresql-online/dms-search.png)

2. Na obrazovce **Služby Azure Database Migration Service** vyhledejte název instance služby Azure Database Migration Service, kterou jste vytvořili, a vyberte ji.

    ![Vyhledání instance služby Azure Database Migration Service](media/tutorial-oracle-azure-postgresql-online/dms-instance-search.png)

3. Vyberte **+ Nový projekt migrace**.
4. Na obrazovce **Nový projekt migrace** zadejte název projektu, v textovém poli Typ **zdrojového serveru** vyberte **oracle**, v textovém poli **Typ cílového serveru** vyberte Azure Database for **PostgreSQL**.
5. V části **Zvolit typ aktivity** vyberte **Online migraci dat**.

   ![Vytvoření projektu Database Migration Service](media/tutorial-oracle-azure-postgresql-online/dms-create-project5.png)

   > [!NOTE]
   > Alternativně můžete zvolit **Vytvořit projekt pouze** k vytvoření projektu migrace nyní a provést migraci později.

6. Vyberte **Uložit**, poznamenejte si požadavky, abyste úspěšně použili službu Migrace databáze Azure k provedení migrace online, a pak vyberte **Vytvořit a spustit aktivitu**.

## <a name="specify-source-details"></a>Zadání podrobností o zdroji

* Na obrazovce **Přidat podrobnosti o zdroji** zadejte podrobnosti o připojení pro zdrojovou instanci Oracle.

  ![Obrazovka Přidat podrobnosti zdroje](media/tutorial-oracle-azure-postgresql-online/dms-add-source-details.png)

## <a name="upload-oracle-oci-driver"></a>Nahrání ovladače Oracle OCI

1. Vyberte **Uložit**a potom na obrazovce **Instalace ovladače OCI** přihlaste se ke svému účtu Oracle a stáhněte si ovladač **instantclient-basiclite-windows.x64-12.2.0.1.0.zip** (37,128,586 Byte(s)) (SHA1 Checksum: 865082268) [odtud](https://www.oracle.com/technetwork/topics/winx64soft-089540.html#ic_winx64_inst).
2. Stáhněte ovladač do sdílené složky.

   Ujistěte se, že složka je sdílena s uživatelským jménem, které jste zadali s minimálním přístupem jen pro čtení. Služba Migrace databáze Azure přistupuje a čte ze sdílené položky a nahraje ovladač OCI do Azure tím, že se zosobní za zadané uživatelské jméno.

   Zadané uživatelské jméno musí být uživatelský účet systému Windows.

   ![Instalace ovladače OCI](media/tutorial-oracle-azure-postgresql-online/dms-oci-driver-install.png)

## <a name="specify-target-details"></a>Zadání podrobností o cíli

1. Vyberte **Uložit**a potom na obrazovce **Podrobnosti cíl,** zadejte podrobnosti o připojení pro cílovou databázi Azure pro PostgreSQL server, což je předem zřízená instance Azure Database for PostgreSQL, do které bylo nasazeno schéma **HR.**

    ![Obrazovka Podrobnosti cíle](media/tutorial-oracle-azure-postgresql-online/dms-add-target-details1.png)

2. Vyberte **Uložit** a pak na obrazovce **Mapovat na cílové databáze** namapujte zdrojovou a cílovou databázi pro migraci.

    Pokud cílová databáze obsahuje stejný název databáze jako zdrojová databáze, služba Migrace databáze Azure vybere cílovou databázi ve výchozím nastavení.

    ![Mapování na cílové databáze](media/tutorial-oracle-azure-postgresql-online/dms-map-target-details.png)

3. Vyberte **Uložit**, na obrazovce **Shrnutí migrace** do textového pole **Název aktivity** zadejte název aktivity migrace a pak zkontrolujte souhrnné informace a ujistěte se, že podrobnosti zdroje a cíle odpovídají dříve zadaným informacím.

    ![Shrnutí migrace](media/tutorial-oracle-azure-postgresql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>Spuštění migrace

* Vyberte **Spustit migraci**.

  Zobrazí se okno aktivity migrace a **inicializace** **stavu** aktivity .

## <a name="monitor-the-migration"></a>Monitorování migrace

1. Na obrazovce aktivity migrace vyberte **Aktualizovat** a aktualizujte zobrazení, dokud se **Stav** migrace nezmění na **Spuštěno**.

     ![Stav aktivity - spuštěno](media/tutorial-oracle-azure-postgresql-online/dms-activity-running.png)

2. V části **Název databáze**vyberte konkrétní databázi, abyste se dostali ke stavu migrace pro **operace úplného načtení dat** a **přírůstkové synchronizace dat.**

    Úplné načtení dat zobrazí stav migrace z hlediska počátečního načtení, zatímco přírůstková synchronizace dat zobrazí stav funkce Change Data Capture (CDC).

     ![Stav aktivity – Úplné načtení dokončeno](media/tutorial-oracle-azure-postgresql-online/dms-activity-full-load-completed.png)

     ![Stav aktivity – Přírůstková synchronizace dat](media/tutorial-oracle-azure-postgresql-online/dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>Provedení přímé migrace

Po dokončení počátečního úplného načtení se databáze označí jako **Připraveno k přímé migraci**.

1. Jakmile budete připraveni dokončit migraci databází, vyberte **Spustit přímou migraci**.

2. Ujistěte se, že jste zastavili všechny příchozí transakce do zdrojové databáze a počkejte, dokud se v čítači **Probíhající změny** nezobrazí **0**.

   ![Spuštění přímé migrace](media/tutorial-oracle-azure-postgresql-online/dms-start-cutover.png)

3. Vyberte **Potvrdit** a pak **Použít**.
4. Když se zobrazí stav migrace databáze **Dokončeno**, připojte aplikace k nové cílové instanci Azure Database for PostgreSQL.

 > [!NOTE]
 > Vzhledem k tomu, že PostgreSQL ve výchozím nastavení má schema.table.column v malých písmen, můžete vrátit z velkých písmen na malá písmena pomocí skriptu v **Nastavit schéma v Azure Database for PostgreSQL** části dříve v tomto článku.

## <a name="next-steps"></a>Další kroky

* Informace o známých problémech a omezeních při provádění online migrací do služby Azure Database for PostgreSQL najdete v článku [Známé problémy s online migracemi do služby Azure Database for PostgreSQLa jejich řešení](known-issues-azure-postgresql-online.md).
* Informace o službě Migrace databáze Azure najdete v článku [Co je služba migrace databáze Azure?](https://docs.microsoft.com/azure/dms/dms-overview).
* Informace o Azure Database for PostgreSQL najdete v článku [Co je databáze Azure pro PostgreSQL?](https://docs.microsoft.com/azure/postgresql/overview).
