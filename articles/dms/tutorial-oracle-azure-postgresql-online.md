---
title: 'Kurz: migrace Oracle online na Azure Database for PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Naučte se provádět online migraci z místního prostředí Oracle nebo na virtuální počítače, abyste Azure Database for PostgreSQL pomocí Azure Database Migration Service.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 01/24/2020
ms.openlocfilehash: 407183837f7be01f5182ff0890426170da223161
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91363167"
---
# <a name="tutorial-migrate-oracle-to-azure-database-for-postgresql-online-using-dms-preview"></a>Kurz: migrace Oracle pro Azure Database for PostgreSQL online pomocí DMS (Preview)

Pomocí Azure Database Migration Service můžete migrovat databáze z databází Oracle hostovaných místně nebo na virtuálních počítačích a [Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/) s minimálními výpadky. Jinými slovy, můžete dokončit migraci s minimálními prostoji do aplikace. V tomto kurzu migrujete ukázkovou databázi **HR** z místní nebo instance virtuálního počítače Oracle 11g na Azure Database for PostgreSQL pomocí online aktivity migrace v Azure Database Migration Service.

V tomto kurzu se naučíte:
> [!div class="checklist"]
>
> * Vyhodnoťte úsilí při migraci pomocí nástroje ora2pg.
> * Pomocí nástroje ora2pg migrujte vzorové schéma.
> * Vytvořte instanci služby Azure Database Migration Service.
> * Vytvořte projekt migrace pomocí Azure Database Migration Service.
> * Spuštění migrace
> * Monitorujte migraci.

> [!NOTE]
> Použití Azure Database Migration Service k provedení online migrace vyžaduje vytvoření instance založené na cenové úrovni Premium.

> [!IMPORTANT]
> Pro optimální prostředí migrace doporučuje Microsoft vytvořit instanci Azure Database Migration Service ve stejné oblasti Azure jako cílová databáze. Přenášení dat mezi oblastmi geografickými lokalitami může zpomalit proces migrace a způsobit chyby.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Tento článek popisuje, jak provést online migraci z Oracle do Azure Database for PostgreSQL.

## <a name="prerequisites"></a>Předpoklady

Pro absolvování tohoto kurzu je potřeba provést následující:

* Stáhněte a nainstalujte [Oracle 11G verze 2 (Standard Edition, Standard Edition One nebo Enterprise Edition)](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html).
* Stáhněte si ukázkovou databázi **HR** z [tohoto místa](https://docs.oracle.com/database/121/COMSC/installation.htm#COMSC00002).
* Stáhněte a [nainstalujte ora2pg buď na Windows, nebo Linux](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf).
* [Vytvořte instanci v Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal).
* Připojte se k instanci a vytvořte databázi pomocí instrukcí v tomto [dokumentu](https://docs.microsoft.com/azure/postgresql/tutorial-design-database-using-azure-portal).
* Vytvořte Microsoft Azure Virtual Network pro Azure Database Migration Service pomocí modelu nasazení Azure Resource Manager, který umožňuje připojení typu Site-to-site k místním zdrojovým serverům pomocí [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Další informace o vytváření virtuálních sítí najdete v [dokumentaci k Virtual Network](https://docs.microsoft.com/azure/virtual-network/)a zejména v článcích rychlý Start s podrobnými údaji.

  > [!NOTE]
  > Pokud při instalaci virtuální sítě používáte ExpressRoute s partnerským vztahem k síti Microsoftu, přidejte do podsítě, ve které se služba zřídí, tyto [koncové body](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) služby:
  >
  > * Koncový bod cílové databáze (například koncový bod SQL, Cosmos DB koncový bod atd.)
  > * Koncový bod úložiště
  > * Koncový bod služby Service Bus
  >
  > Tato konfigurace je nezbytná, protože Azure Database Migration Service nemá připojení k Internetu.

* Zajistěte, aby pravidla skupiny zabezpečení sítě (NSG) vaší virtuální sítě neblokovala následující příchozí komunikační porty Azure Database Migration Service: 443, 53, 9354, 445, 12000. Další podrobnosti o filtrování provozu NSG virtuální sítě najdete v článku [filtrování provozu sítě pomocí skupin zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm).
* Nakonfigurujte bránu [Windows Firewall pro přístup k databázovému stroji](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Otevřete bránu Windows Firewall a umožněte Azure Database Migration Service přistupovat ke zdrojovému serveru Oracle, který je ve výchozím nastavení port TCP 1521.
* Pokud používáte zařízení brány firewall před zdrojovými databázemi, budete možná muset přidat pravidla firewallu, která Azure Database Migration Service umožní přístup ke zdrojovým databázím pro migraci.
* Vytvořte [pravidlo brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) na úrovni serveru pro Azure Database for PostgreSQL, které povolí Azure Database Migration Service přístup k cílovým databázím. Zadejte rozsah podsítě virtuální sítě, která se používá pro Azure Database Migration Service.
* Povolte přístup ke zdrojovým databázím Oracle.

  > [!NOTE]
  > Aby se uživatel mohl připojit ke zdroji Oracle, je potřeba role DBA.

  * Pro přírůstkovou synchronizaci v Azure Database Migration Service k zaznamenání změny dat jsou vyžadovány protokoly opětovného spuštění archivu. Pomocí těchto kroků nakonfigurujete zdroj Oracle:
    * Přihlaste se pomocí oprávnění SYSDBA spuštěním následujícího příkazu:

      ```
      sqlplus (user)/(password) as sysdba
      ```

    * Spuštěním následujícího příkazu vypněte instanci databáze.

      ```
      SHUTDOWN IMMEDIATE;
      ```

      Počkejte na potvrzení `'ORACLE instance shut down'` .

    * Spusťte novou instanci a připojte ji (ale neotevírejte), abyste povolili nebo zakázali archivaci bu, a to spuštěním následujícího příkazu:

      ```
      STARTUP MOUNT;
      ```

      Databáze musí být připojená. Počkejte na potvrzení výskytu instance Oracle spuštěno.

    * Režim archivace databáze změňte spuštěním následujícího příkazu:

      ```
      ALTER DATABASE ARCHIVELOG;
      ```

    * Spuštěním následujícího příkazu otevřete databázi pro běžné operace:

      ```
      ALTER DATABASE OPEN;
      ```

      Možná bude nutné restartovat soubor ARC, aby se zobrazila.

    * K ověření spusťte následující příkaz:

      ```
      SELECT log_mode FROM v$database;
      ```

      Měli byste obdržet odpověď `'ARCHIVELOG'` . Pokud je odpověď `'NOARCHIVELOG'` , pak se požadavek nesplní.

  * Pomocí jedné z následujících možností zapněte dodatečné protokolování replikace.

    * **Možnost 1**.
      Změňte doplňkové protokolování na úrovni databáze tak, aby se pokryly všechny tabulky s PK a jedinečným indexem. Zjišťovací dotaz vrátí `'IMPLICIT'` .

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY, UNIQUE) COLUMNS;
      ```

      Změna doplňkového protokolování na úrovni tabulky Spouštět pouze pro tabulky, které mají manipulaci s daty a nemají PKs nebo jedinečné indexy.

      ```
      ALTER TABLE [TABLENAME] ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
      ```

    * **Možnost 2**.
      Změnou doplňkového protokolování na úrovni databáze pokryjete všechny tabulky a dotaz na vyhledávání vrátí `'YES'` .

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
      ```

      Změna doplňkového protokolování na úrovni tabulky Použijte následující logiku a spusťte pouze jeden příkaz pro každou tabulku.

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

    K ověření spusťte následující příkaz:

      ```
      SELECT supplemental_log_data_min FROM v$database;
      ```

    Měli byste obdržet odpověď `'YES'` .

## <a name="assess-the-effort-for-an-oracle-to-azure-database-for-postgresql-migration"></a>Vyhodnocení úsilí pro Oracle k Azure Database for PostgreSQL migrace

Doporučujeme používat ora2pg k vyhodnocení úsilí potřebného k migraci z Oracle na Azure Database for PostgreSQL. Pomocí této `ora2pg -t SHOW_REPORT` direktivy můžete vytvořit sestavu se seznamem všech objektů Oracle, odhadovaných nákladů na migraci (ve dnech vývojářů) a některých databázových objektů, které mohou vyžadovat zvláštní pozornost v rámci převodu.

Většina zákazníků bude strávit značnou dobu, kterou si prohlédnete v sestavě posouzení a bere v úvahách automatické a ruční převody.

Pokud chcete nakonfigurovat a spustit ora2pg pro vytvoření sestavy posouzení, přečtěte si část **"předmigrace: posouzení"** v tématu [Oracle na Azure Database for PostgreSQL kuchařka](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf). K dispozici je ukázková sestava ora2pg Assessment [pro referenci.](https://ora2pg.darold.net/report.html)

## <a name="export-the-oracle-schema"></a>Export schématu Oracle

Doporučujeme použít ora2pg k převedení schématu Oracle a dalších objektů Oracle (typů, procedur, funkcí atd.) na schéma, které je kompatibilní s Azure Database for PostgreSQL. ora2pg obsahuje mnoho direktiv, které vám pomůžou předem definovat určité datové typy. Můžete například použít `DATA_TYPE` direktivu k nahrazení celého čísla (*, 0) číslem bigint, nikoli číselné (38).

Můžete spustit ora2pg pro export všech databázových objektů do souborů. SQL. Pak si můžete prohlédnout soubory. SQL před importem do Azure Database for PostgreSQL pomocí psql nebo můžete spustit. Skript SQL v PgAdmin.

```
psql -f [FILENAME] -h [AzurePostgreConnection] -p 5432 -U [AzurePostgreUser] -d database 
```

Například:

```
psql -f %namespace%\schema\sequences\sequence.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database
```

Chcete-li nakonfigurovat a spustit ora2pg pro převod schématu, přečtěte si část **migrace: schéma a data** v [Oracle na Azure Database for PostgreSQL kuchařka](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf).

## <a name="set-up-the-schema-in-azure-database-for-postgresql"></a>Nastavení schématu v Azure Database for PostgreSQL

Před spuštěním kanálu migrace v Azure Database Migration Service můžete zvolit převod schémat tabulky Oracle, uložených procedur, balíčků a dalších databázových objektů, aby byly kompatibilní s použitím ora2pg. V následujících odkazech najdete informace o tom, jak pracovat s ora2pg:

* [Instalace ora2pg ve Windows](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf)
* [Kuchařka migrace z Oracle do Azure PostgreSQL](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf)

Azure Database Migration Service může také vytvořit schéma tabulky PostgreSQL. Služba přistupuje ke schématu tabulky v připojeném zdroji Oracle a vytvoří v Azure Database for PostgreSQL kompatibilní schéma tabulky. Nezapomeňte ověřit a zkontrolovat formát schématu v Azure Database for PostgreSQL poté, co Azure Database Migration Service dokončí vytváření schématu a přesun dat.

> [!IMPORTANT]
> Azure Database Migration Service pouze vytváření schématu tabulky; nevytvoří se žádné další databázové objekty, jako jsou uložené procedury, balíčky, indexy atd.

Nezapomeňte také vyřadit cizí klíč v cílové databázi, aby bylo možné spustit úplné načtení. Informace o skriptu, který můžete použít k vyřazení cizího klíče, najdete v části věnované **migraci ukázkového schématu** [v článku.](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online) Pro spuštění úplného načtení a synchronizace použijte Azure Database Migration Service.

### <a name="when-the-postgresql-table-schema-already-exists"></a>Pokud schéma tabulky PostgreSQL již existuje

Pokud vytvoříte schéma PostgreSQL pomocí nástrojů, jako je ora2pg před spuštěním přesunu dat pomocí Azure Database Migration Service, namapujte zdrojové tabulky na cílové tabulky v Azure Database Migration Service.

1. Když vytvoříte nový projekt pro Azure Database for PostgreSQL migrace pro Oracle, budete vyzváni k výběru cílové databáze a cílového schématu v kroku vybrat schémata. Vyplňte cílovou databázi a cílové schéma.

   ![Snímek obrazovky ukazuje mapování na cílové databáze.](media/tutorial-oracle-azure-postgresql-online/dms-map-to-target-databases.png)

2. Na obrazovce **Nastavení migrace** se zobrazí seznam tabulek ve zdroji Oracle. Azure Database Migration Service se pokusí párovat tabulky ve zdrojové a cílové tabulce na základě názvu tabulky. Pokud existuje více vyhovujících cílových tabulek s různou velikostí písmen, můžete vybrat cílovou tabulku, ke které se má mapovat.

    ![Snímek obrazovky ukazuje nastavení migrace.](media/tutorial-oracle-azure-postgresql-online/dms-migration-settings.png)

> [!NOTE]
> Pokud potřebujete mapovat názvy zdrojových tabulek k tabulkám s různými názvy, e-mailem [dmsfeedback@microsoft.com](mailto:dmsfeedbac@microsoft.com) a můžeme poskytnout skript pro automatizaci tohoto procesu.

### <a name="when-the-postgresql-table-schema-doesnt-exist"></a>Když schéma tabulky PostgreSQL neexistuje

Pokud cílová databáze PostgreSQL neobsahuje žádné informace o schématu tabulky, Azure Database Migration Service převede zdrojové schéma a znovu vytvoří v cílové databázi. Nezapomeňte, Azure Database Migration Service vytvoří pouze schéma tabulky, nikoli jiné databázové objekty, jako jsou uložené procedury, balíčky a indexy.
Pokud chcete, aby vám Azure Database Migration Service vytvořit schéma za vás, ujistěte se, že cílové prostředí obsahuje schéma bez existujících tabulek. Pokud Azure Database Migration Service zjistí jakoukoli tabulku, služba předpokládá, že schéma bylo vytvořeno externím nástrojem, jako je ora2pg.

> [!IMPORTANT]
> Azure Database Migration Service vyžaduje, aby se všechny tabulky vytvořily stejným způsobem, a to pomocí Azure Database Migration Service nebo nástroje, jako je ora2pg, ale ne z obou možností.

Jak začít:

1. Vytvořte schéma v cílové databázi podle požadavků vaší aplikace. Ve výchozím nastavení jsou názvy schémat a sloupců PostgreSQL nižší použita. Schéma tabulky a sloupce Oracle jsou naopak standardně ve výchozím nastavení velkými písmeny.
2. V kroku vybrat schémata určete cílovou databázi a cílové schéma.
3. Na základě schématu, které vytvoříte v Azure Database for PostgreSQL, Azure Database Migration Service používá následující pravidla transformace:

    Pokud název schématu ve zdroji Oracle odpovídá hodnotě v Azure Database for PostgreSQL, Azure Database Migration Service *vytvoří schéma tabulky pomocí stejného případu jako v cíli*.

    Například:

    | Zdrojové schéma Oracle | Cílová databáze PostgreSQL. schéma | DMS vytvořil schéma. Table. Column. |
    | ------------- | ------------- | ------------- |
    | HR | targetHR. Public | public.countries.country_id |
    | HR | targetHR.trgthr | trgthr.countries.country_id |
    | HR | targetHR.TARGETHR | "TARGETHR"." ZEMĚ "." COUNTRY_ID " |
    | HR | targetHR.HR | "HR". ZEMĚ "." COUNTRY_ID " |
    | HR | targetHR.Hr | * Nejde namapovat smíšené případy. |

    * Pokud chcete v cílovém PostgreSQL vytvořit kombinaci velkých a malých písmen a názvů tabulek, obraťte se na [dmsfeedback@microsoft.com](mailto:dmsfeedback@microsoft.com) . V cílové databázi PostgreSQL můžeme poskytnout skript pro nastavení schématu smíšené velikosti tabulky.

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

    Virtuální síť poskytuje Azure Database Migration Service s přístupem ke zdrojovému Oracle a cílové instanci Azure Database for PostgreSQL.

    Další informace o tom, jak vytvořit virtuální síť v Azure Portal, najdete v článku [vytvoření virtuální sítě pomocí Azure Portal](https://aka.ms/DMSVnet).

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
4. Na obrazovce **Nový projekt migrace** zadejte název projektu, v textovém poli **typ zdrojového serveru** vyberte možnost **Oracle**, v textovém poli **typ cílového serveru** vyberte možnost **Azure Database for PostgreSQL**.
5. V části **Zvolte typ aktivity** vyberte možnost **migrace online dat**.

   ![Vytvoření projektu Database Migration Service](media/tutorial-oracle-azure-postgresql-online/dms-create-project5.png)

   > [!NOTE]
   > Alternativně můžete zvolit **vytvořit projekt pouze** k vytvoření projektu migrace a provést migraci později.

6. Vyberte **Save (Uložit**), poznamenejte si požadavky na úspěšné použití Azure Database Migration Service k provedení online migrace a pak vyberte **vytvořit a spustit aktivitu**.

## <a name="specify-source-details"></a>Zadání podrobností o zdroji

* Na obrazovce **Přidat podrobnosti zdroje** zadejte podrobnosti o připojení zdrojové instance Oracle.

  ![Obrazovka Přidat podrobnosti zdroje](media/tutorial-oracle-azure-postgresql-online/dms-add-source-details.png)

## <a name="upload-oracle-oci-driver"></a>Nahrát ovladač Oracle OCI

1. Vyberte **Uložit**a pak na obrazovce **instalace ovladače rozhraní OCI** se přihlaste k účtu Oracle a stáhněte **instantclient-basiclite-windows.x64-12.2.0.1.0.zip** ovladače (37 128 586 bajtů) (s kontrolním součtem SHA1:865082268) z [tohoto místa](https://www.oracle.com/technetwork/topics/winx64soft-089540.html#ic_winx64_inst).
2. Stáhněte ovladač do sdílené složky.

   Ujistěte se, že je složka sdílená s uživatelským jménem, které jste zadali s minimálním přístupem jen pro čtení. Azure Database Migration Service přistupuje ke sdílené složce a přečte se z ní, aby se do Azure nahrál ovladač OCI vyvoláním uživatelského jména, které zadáte.

   Uživatelské jméno, které zadáte, musí být uživatelský účet systému Windows.

   ![Instalace ovladače OCI](media/tutorial-oracle-azure-postgresql-online/dms-oci-driver-install.png)

## <a name="specify-target-details"></a>Zadání podrobností o cíli

1. Vyberte **Uložit**a pak na obrazovce **cílové podrobnosti** zadejte podrobnosti o připojení pro cílový Azure Database for PostgreSQL Server, což je předem zřízená instance Azure Database for PostgreSQL, na kterou se schéma **lidských zdrojů** nasadilo.

    ![Obrazovka Podrobnosti cíle](media/tutorial-oracle-azure-postgresql-online/dms-add-target-details1.png)

2. Vyberte **Uložit** a pak na obrazovce **Mapovat na cílové databáze** namapujte zdrojovou a cílovou databázi pro migraci.

    Pokud cílová databáze obsahuje stejný název databáze jako zdrojová databáze, Azure Database Migration Service ve výchozím nastavení vybere cílovou databázi.

    ![Mapování na cílové databáze](media/tutorial-oracle-azure-postgresql-online/dms-map-target-details.png)

3. Vyberte **Uložit**, na obrazovce **Shrnutí migrace** do textového pole **Název aktivity** zadejte název aktivity migrace a pak zkontrolujte souhrnné informace a ujistěte se, že podrobnosti zdroje a cíle odpovídají dříve zadaným informacím.

    ![Shrnutí migrace](media/tutorial-oracle-azure-postgresql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>Spuštění migrace

* Vyberte **Spustit migraci**.

  Zobrazí se okno aktivita migrace a **stav** aktivity se **inicializuje**.

## <a name="monitor-the-migration"></a>Monitorování migrace

1. Na obrazovce aktivity migrace vyberte **Aktualizovat** a aktualizujte zobrazení, dokud se **Stav** migrace nezmění na **Spuštěno**.

     ![Stav aktivity – spuštěno](media/tutorial-oracle-azure-postgresql-online/dms-activity-running.png)

2. V části **název databáze**vyberte konkrétní databázi, abyste získali stav migrace pro operace **úplného načtení dat** a **přírůstkové synchronizace dat** .

    Úplné načtení dat zobrazí stav migrace z hlediska počátečního načtení, zatímco přírůstková synchronizace dat zobrazí stav funkce Change Data Capture (CDC).

     ![Stav aktivity – Úplné načtení dokončeno](media/tutorial-oracle-azure-postgresql-online/dms-activity-full-load-completed.png)

     ![Stav aktivity – Přírůstková synchronizace dat](media/tutorial-oracle-azure-postgresql-online/dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>Provedení přímé migrace

Po dokončení počátečního úplného načtení se databáze označí jako **Připraveno k přímé migraci**.

1. Jakmile budete připraveni dokončit migraci databází, vyberte **Spustit přímou migraci**.

2. Ujistěte se, že jste zastavili všechny příchozí transakce do zdrojové databáze a počkejte, dokud se v čítači **Probíhající změny** nezobrazí **0**.

   ![Spuštění přímé migrace](media/tutorial-oracle-azure-postgresql-online/dms-start-cutover.png)

3. Vyberte **Potvrdit** a pak **Použít**.
4. Až se stav migrace databáze zobrazí jako **dokončený**, připojte své aplikace k nové cílové Azure Database for PostgreSQL instanci.

 > [!NOTE]
 > Vzhledem k tomu, že PostgreSQL ve výchozím nastavení má Schema. Table. Column malými písmeny, můžete vrátit z velkých písmen do malých písmen pomocí skriptu v části **Nastavení schématu v Azure Database for PostgreSQL** výše v tomto článku.

## <a name="next-steps"></a>Další kroky

* Informace o známých problémech a omezeních při provádění online migrací do služby Azure Database for PostgreSQL najdete v článku [Známé problémy s online migracemi do služby Azure Database for PostgreSQLa jejich řešení](known-issues-azure-postgresql-online.md).
* Informace o Azure Database Migration Service najdete v článku [co je Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
* Informace o Azure Database for PostgreSQL najdete v článku [co je Azure Database for PostgreSQL?](https://docs.microsoft.com/azure/postgresql/overview).
