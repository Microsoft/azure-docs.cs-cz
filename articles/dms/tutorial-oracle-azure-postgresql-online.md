---
title: 'Kurz: Azure Database Migration Service můžete provést online migrace Oracle do služby Azure Database for PostgreSQL | Dokumentace Microsoftu'
description: Zjistěte, jak provést online migraci Oracle v místním nebo na virtuálních počítačích ke službě Azure Database for PostgreSQL s využitím Azure Database Migration Service.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 0b3af3d29e6e938f0301d751a79170c7c1964b45
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243797"
---
# <a name="tutorial-migrate-oracle-to-azure-database-for-postgresql-online-using-dms-preview"></a>Kurz: Migrace Oracle do služby Azure Database for PostgreSQL online pomocí DMS (Preview)

Azure Database Migration Service můžete použít k migraci databáze Oracle databáze hostované v místním nebo na virtuálních počítačích na [– Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/) s minimálními výpadky. Jinými slovy můžete dokončit migraci s minimálními prostoji do aplikace. V tomto kurzu, migrujete **HR** ukázkovou databázi z místní nebo virtuální počítač instanci Oracle 11 g ke službě Azure Database for PostgreSQL s využitím online migrace aktivity v Azure Database Migration Service.

V tomto kurzu se naučíte:
> [!div class="checklist"]
>
> * Posouzení migrace úsilí nástrojem ora2pg.
> * Migrace schématu ukázkový používání ora2pg nástroje.
> * Vytvoření instance služby Azure Database Migration Service.
> * Vytvořte projekt migrace s využitím Azure Database Migration Service.
> * Spuštění migrace
> * Monitorování migrace

> [!NOTE]
> Použití Azure Database Migration Service online migrace vyžaduje vytvoření instance založené na cenovou úroveň Premium.

> [!IMPORTANT]
> Pro zajištění optimálního prostředí společnost Microsoft doporučuje vytvoření instance služby Azure Database Migration Service ve stejné oblasti Azure jako cílové databázi. Přenášení dat mezi oblastmi geografickými lokalitami může zpomalit proces migrace a způsobit chyby.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Tento článek popisuje, jak provést online migraci z Oraclu do služby Azure Database for PostgreSQL.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu je potřeba provést následující:

* Stáhněte a nainstalujte [Oracle 11g vydaná verze 2 (Standard Edition, jeden Standard Edition nebo Enterprise Edition)](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html).
* Stáhněte si ukázku **HR** databáze z [tady](https://docs.oracle.com/database/121/COMSC/installation.htm#COMSC00002).
* Stáhněte a nainstalujte ora2pg buď [Windows](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows.pdf) nebo [Linux](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Linux.pdf).
* [Vytvořte instanci v Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal).
* Připojte se k instanci a vytvoření databáze pomocí pokynů v tomto [dokumentu](https://docs.microsoft.com/azure/postgresql/tutorial-design-database-using-azure-portal).
* Vytvoření služby Azure Virtual Network (VNet) pro Azure Database Migration Service pomocí modelu nasazení Azure Resource Manageru, který poskytuje připojení site-to-site k vašich zdrojových serverů s místními pomocí [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Další informace o vytvoření virtuální sítě, najdete v článku [dokumentace k Virtual Network](https://docs.microsoft.com/azure/virtual-network/)a hlavně článků rychlý start s podrobný.

  > [!NOTE]
  > Při nastavení virtuální sítě, pokud používáte ExpressRoute se síť vytvoření partnerského vztahu Microsoftu, přidejte následující službu [koncové body](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) k podsíti, ve kterém se zřídí služby:
  > * Koncový bod databázového cíl (například koncový bod SQL, koncového bodu služby Cosmos DB a tak dále)
  > * Koncový bod úložiště
  > * Koncový bod služby Service bus
  >
  > Tato konfigurace je nezbytná, protože služba Azure Database Migration Service nemá připojení k Internetu.

* Ujistěte se, že pravidla skupiny zabezpečení sítě virtuální sítě (NSG) nedošlo k blokování následující porty příchozí komunikace k Azure Database Migration Service: 443, 53, 9354, 445, 12000. Další podrobnosti o filtrování provozu skupiny zabezpečení sítě Azure VNet najdete v článku [filtrování provozu sítě s použitím skupin zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm).
* Nakonfigurujte bránu [Windows Firewall pro přístup k databázovému stroji](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Otevřete bránu Windows firewall a povolit Azure Database Migration Service přístup k serveru Oracle zdroj, který ve výchozím nastavení je TCP port 1521.
* Při použití zařízení brány firewall u zdrojových databází, budete muset přidat pravidla firewallu povolující Azure Database Migration Service přístup k zdrojových databází pro migraci.
* Vytvořte úrovni serveru [pravidlo brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) pro službu Azure Database for PostgreSQL umožňuje Azure Database Migration Service přístup k cílovým databázím. Zadejte rozsah podsítě virtuální sítě pro Azure Database Migration Service.
* Povolte přístup ke zdrojové databáze Oracle.

  > [!NOTE]
  > DBA role je vyžadována pro uživatele pro připojení ke zdroji Oracle.

  * Protokoly svému archivu je vyžadován pro přírůstkové synchronizace v Azure Database Migration Service pro zachycení dat změn. Postupujte podle těchto kroků a nakonfigurujte zdroj Oracle:
    * Přihlaste se pomocí oprávnění SYSDBA spuštěním následujícího příkazu:

      ```
      sqlplus (user)/(password) as sysdba
      ```

    * Vypnutí instance databáze spuštěním následujícího příkazu.

      ```
      SHUTDOWN IMMEDIATE;
      ```

      Počkejte potvrzení `'ORACLE instance shut down'`.

    * Spustit novou instanci a připojení (ale neotevírat) databáze, kterou chcete povolit nebo zakázat archivace bu spuštěním následujícího příkazu:

      ```
      STARTUP MOUNT;
      ```

      Databáze se musí připojit; Počkejte na potvrzení "Instance Oracle spuštění".

    * Změna databáze archivace režimu spuštěním následujícího příkazu:

      ```
      ALTER DATABASE ARCHIVELOG;
      ```

    * Otevřete databázi pro běžné operace spuštěním následujícího příkazu:

      ```
      ALTER DATABASE OPEN;
      ```

      Budete muset restartovat OBLOUK souboru se zobrazí.

    * Pokud chcete ověřit, spusťte následující příkaz:

      ```
      SELECT log_mode FROM v$database;
      ```

      Byste měli obdržet odpověď `'ARCHIVELOG'`. Pokud je odpověď na `'NOARCHIVELOG'`, pak tento požadavek není splněná.

  * Povolte dodatečné protokolování pro replikaci pomocí jedné z následujících možností.

    * **Možnost 1**.
      Změna úrovně dodatečné protokolování databáze pro všechny tabulky s PK a jedinečný index. Detekce dotaz vrátí `'IMPLICIT'`.

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY, UNIQUE) COLUMNS;
      ```

      Změna úrovně tabulky dodatečné protokolování. Spusťte pouze pro tabulky, které mají manipulace s daty a nemáte PKs nebo jedinečné indexy.

      ```
      ALTER TABLE [TABLENAME] ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
      ```

    * **Možnost 2**.
      Změna úrovně dodatečné protokolování databáze k pokrytí všech tabulek a detekce dotaz vrátí `'YES'`.

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
      ```

      Změna úrovně tabulky dodatečné protokolování. Postupujte podle logiky níže spusťte pouze jeden příkaz pro každou tabulku.

      Pokud tabulka nemá primární klíč:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY) COLUMNS;
      ```

      Pokud tabulka obsahuje jedinečný index:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG GROUP (first unique index columns) ALWAYS;
      ```

      V opačném případě spusťte následující příkaz:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
      ```

    Pokud chcete ověřit, spusťte následující příkaz:

      ```
      SELECT supplemental_log_data_min FROM v$database;
      ```

    Byste měli obdržet odpověď `'YES'`.

> [!IMPORTANT]
> Pro verzi public preview tohoto scénáře, služba Azure Database Migration Service podporuje verzi Oracle 10g nebo 11g. Zákazníci, kteří používají verzi Oracle 12 c nebo novější by měl Všimněte si, že minimální ověřovací protokol povolen pro ovladače ODBC pro připojení k Oracle musí být 8. Pro zdroj Oracle, který je verze 12c nebo novější, musíte nakonfigurovat ověřování protokolu následujícím způsobem:
>
> * Update SQLNET.ORA:
>
>    ```
>    SQLNET.ALLOWED_LOGON_VERSION_CLIENT = 8
>    SQLNET.ALLOWED_LOGON_VERSION_SERVER = 8
>    ```
>
> * Restartujte počítač nové nastavení projevilo.
> * Změna hesla pro stávající uživatele:
>
>    ```
>    ALTER USER system IDENTIFIED BY {pswd}
>    ```
>
>   Další informace naleznete na stránce [tady](http://www.dba-oracle.com/t_allowed_login_version_server.htm).
>
> Nakonec se nezapomeňte, že změna ověřovací protokol může mít vliv na ověřování klientů.

## <a name="assess-the-effort-for-an-oracle-to-azure-database-for-postgresql-migration"></a>Vyhodnocení úsilí pro Oracle do služby Azure Database for postgresql – migrace

Doporučujeme používat ora2pg k vyhodnocení úsilí nutné k migraci z Oraclu do služby Azure Database for PostgreSQL. Použití `ora2pg -t SHOW_REPORT` směrnice vytvořit sestavu se seznamem všech objektů Oracle, migrace odhadované náklady (ve dnech pro vývojáře) a některých databázové objekty, které mohou vyžadovat zvláštní pozornost v rámci převodu.

Většina zákazníků se věnovat značné množství času, zkontrolujte sestavu posouzení a vzhledem k tomu, automatické a ruční převod úsilí.

Pro konfiguraci a spuštění ora2pg k vytvoření sestavy posouzení, najdete v článku **Premigration: Posouzení** část [Oracle k Azure Database for PostgreSQL kuchařka](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf). Sestavu posouzení ora2pg ukázka je k dispozici pro referenci [tady](http://ora2pg.darold.net/report.html).

## <a name="export-the-oracle-schema"></a>Export schémat Oracle

Doporučujeme použít ora2pg převést schématu Oracle a dalších objektů Oracle (typy, procedury, funkce atd.) na schéma, které je kompatibilní s využitím Azure Database for PostgreSQL. ora2pg zahrnuje mnoho direktivy můžete předem definovat určitých datových typů. Například můžete použít `DATA_TYPE` směrnice nahradit všechny NUMBER(*,0) bigint spíše než NUMERIC(38).

Můžete spustit ora2pg export všech databázových objektů v soubory .sql. Potom můžete zkontrolovat soubory .sql před importem do služby Azure Database for PostgreSQL pomocí psql nebo můžete spustit. Skript SQL v nástroji PgAdmin.

```
psql -f [FILENAME] -h [AzurePostgreConnection] -p 5432 -U [AzurePostgreUser] -d database 
```

Příklad:

```
psql -f %namespace%\schema\sequences\sequence.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database
```

Pro konfiguraci a spuštění ora2pg pro převod schématu, najdete v článku **migrace: Schéma a data** část [Oracle k Azure Database for PostgreSQL kuchařka](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf).

## <a name="set-up-the-schema-in-azure-database-for-postgresql"></a>Nastavení schématu ve službě Azure Database for PostgreSQL

Ve výchozím nastavení Oracle udržuje schema.table.column ve všech případech horní, zatímco PostgreSQL uchová schema.table.column malými písmeny. Azure Database Migration Service spuštění přesun dat z Oracle do služby Azure Database for PostgreSQL musí být schema.table.column případu stejný formát jako zdroj Oracle.

Například, pokud má zdrojový Oracle jako schéma "HR"." ZAMĚSTNANCI,"." EMPLOYEE_ID"a pak PostgreSQL schématu musí používat stejný formát.

Ujistěte se, že případu formát schema.table.column stejný pro Oracle a Azure Database for PostgreSQL, doporučujeme použít následující postup.

> [!NOTE]
> Použít jiný přístup k odvození schématu velká písmena. Pracujeme na vylepšení a automatizovat tento krok.

1. Exportujte schémat ora2pg pomocí malých písmen. V sql skriptu pro vytvoření tabulky vytvořte schéma s velkými písmeny "Schéma" ručně.
2. Importujte zbývající Oracle objekty, jako jsou triggery, sekvencí, postupy, typy a funkce, do služby Azure Database for PostgreSQL.
3. Pokud chcete převést tabulky a sloupce velká písmena, spusťte následující skript:

   ```
   -- INPUT: schema name
   set schema.var = “HR”;

   -- Generate statements to rename tables and columns
   SELECT 1, 'SET search_path = "' ||current_setting('schema.var')||'";'
   UNION ALL 
   SELECT 2, 'alter table "'||c.relname||'" rename '||a.attname||' to "'||upper(a.attname)||'";'
   FROM pg_class c
   JOIN pg_attribute a ON a.attrelid = c.oid
   JOIN pg_type t ON a.atttypid = t.oid
   LEFT JOIN pg_catalog.pg_constraint r ON c.oid = r.conrelid
    AND r.conname = a.attname
   WHERE c.relnamespace = (select oid from pg_namespace where nspname=current_setting('schema.var')) AND a.attnum > 0 AND c.relkind ='r'
   UNION ALL
   SELECT 3, 'alter table '||c.relname||' rename to "'||upper(c.relname)||'";'
   FROM pg_catalog.pg_class c
    LEFT JOIN pg_catalog.pg_namespace n ON n.oid = c.relnamespace
   WHERE c.relkind ='r' AND n.nspname=current_setting('schema.var')
   ORDER BY 1;
   ```

* Vyřaďte cizího klíče v cílové databázi pro úplné načtení ke spuštění. Odkazovat **migrace schématu ukázkové** část článku [tady](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online) skript, který vám umožní vyřadit cizí klíč.
* Azure Database Migration Service použijte ke spuštění pro úplné načtení a synchronizaci.
* Pokud data v cílovou službu Azure Database for postgresql – instance je zachycena se zdrojem, proveďte databáze přímou migraci v Azure Database Migration Service.
* Aby schéma, tabulka a sloupec malá písmena (Pokud je schéma pro službu Azure Database for PostgreSQL by měla být tímto způsobem pro dotaz aplikace), spusťte následující skript:

  ```
  -- INPUT: schema name
  set schema.var = hr;
  
  -- Generate statements to rename tables and columns
  SELECT 1, 'SET search_path = "' ||current_setting('schema.var')||'";'
  UNION ALL
  SELECT 2, 'alter table "'||c.relname||'" rename "'||a.attname||'" to '||lower(a.attname)||';'
  FROM pg_class c
  JOIN pg_attribute a ON a.attrelid = c.oid
  JOIN pg_type t ON a.atttypid = t.oid
  LEFT JOIN pg_catalog.pg_constraint r ON c.oid = r.conrelid
     AND r.conname = a.attname
  WHERE c.relnamespace = (select oid from pg_namespace where nspname=current_setting('schema.var')) AND a.attnum > 0 AND c.relkind ='r'
  UNION ALL
  SELECT 3, 'alter table "'||c.relname||'" rename to '||lower(c.relname)||';'
  FROM pg_catalog.pg_class c
     LEFT JOIN pg_catalog.pg_namespace n ON n.oid = c.relnamespace
  WHERE c.relkind ='r' AND n.nspname=current_setting('schema.var')
  ORDER BY 1;
  ```

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

    Virtuální síť poskytuje Azure Database Migration Service přístup ke zdroji Oracle a cílovou službu Azure Database for postgresql – instance.

    Další informace o tom, jak vytvořit síť VNet na portálu Azure portal najdete v článku [vytvořit virtuální síť pomocí webu Azure portal](https://aka.ms/DMSVnet).

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
4. Na **nový projekt migrace** obrazovky, zadejte název projektu, v **serveru typ zdroje** textového pole, vyberte **Oracle**v **cílový typ serveru**  textového pole, vyberte **– Azure Database for PostgreSQL**.
5. V **zvolte typ aktivity** vyberte **Online data migrace**.

   ![Vytvoření projektu Database Migration Service](media/tutorial-oracle-azure-postgresql-online/dms-create-project5.png)

   > [!NOTE]
   > Alternativně můžete zvolit **pouze vytvořit projekt** teď vytvořit projekt migrace a spustit migraci později.

6. Vyberte **Uložit**, mějte na paměti požadavky pro úspěšné fungování služby Azure Database Migration Service online migrace, a potom vyberte **vytvoření a spuštění aktivity**.

## <a name="specify-source-details"></a>Zadání podrobností o zdroji

* Na **přidat podrobnosti zdroje** obrazovky, zadat podrobnosti připojení pro zdrojovou instanci Oracle.

  ![Obrazovka Přidat podrobnosti zdroje](media/tutorial-oracle-azure-postgresql-online/dms-add-source-details.png)

## <a name="upload-oracle-oci-driver"></a>Nahrát Oracle OCI ovladače

1. Vyberte **Uložit**a pak na **OCI nainstalovat ovladač** obrazovky, přihlaste se ke svému účtu Oracle a stáhnout ovladač **instantclient-basiclite-windows.x64-12.2.0.1.0.zip**(37,128,586 Byte(s)) (SHA1 kontrolní součet: 865082268) z [tady](https://www.oracle.com/technetwork/topics/winx64soft-089540.html#ic_winx64_inst).
2. Stáhněte ovladač do sdílené složky.

   Ujistěte se, že složka je sdílena s uživatelským jménem, který jste zadali s minimálním přístupem jen pro čtení. Azure Database Migration Service přistupuje k nim a čte z sdílenou složku k nahrání OCI ovladač do Azure zosobněním uživatelské jméno, které zadáte.

   Uživatelské jméno, které zadáte, musí být uživatelský účet Windows.

   ![Instalace ovladače OCI](media/tutorial-oracle-azure-postgresql-online/dms-oci-driver-install.png)

## <a name="specify-target-details"></a>Zadání podrobností o cíli

1. Vyberte **Uložit**a pak na **cílit na podrobnosti** obrazovky, zadat podrobnosti připojení pro cílovou službu Azure Database pro PostgreSQL server, který je předem zřízená instance – Azure Database for PostgreSQL, ke kterému **HR** schéma bylo nasazeno.

    ![Obrazovka Podrobnosti cíle](media/tutorial-oracle-azure-postgresql-online/dms-add-target-details1.png)

2. Vyberte **Uložit** a pak na obrazovce **Mapovat na cílové databáze** namapujte zdrojovou a cílovou databázi pro migraci.

    Pokud cílová databáze obsahuje název stejné databáze jako zdrojové databáze, vybere Azure Database Migration Service ve výchozím nastavení cílové databázi.

    ![Mapování na cílové databáze](media/tutorial-oracle-azure-postgresql-online/dms-map-target-details.png)

3. Vyberte **Uložit**, na obrazovce **Shrnutí migrace** do textového pole **Název aktivity** zadejte název aktivity migrace a pak zkontrolujte souhrnné informace a ujistěte se, že podrobnosti zdroje a cíle odpovídají dříve zadaným informacím.

    ![Shrnutí migrace](media/tutorial-oracle-azure-postgresql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>Spuštění migrace

* Vyberte **Spustit migraci**.

  Zobrazí se okno aktivity migrace a **Stav** aktivity bude **Inicializace**.

## <a name="monitor-the-migration"></a>Monitorování migrace

1. Na obrazovce aktivity migrace vyberte **Aktualizovat** a aktualizujte zobrazení, dokud se **Stav** migrace nezmění na **Spuštěno**.

     ![Stav aktivity – spuštění](media/tutorial-oracle-azure-postgresql-online/dms-activity-running.png)

2. V části **název_databáze**, vyberte konkrétní databázi k získání stavu migrace pro **úplné načtení dat** a **Přírůstková synchronizace dat** operace.

    Úplné načtení dat zobrazí stav migrace z hlediska počátečního načtení, zatímco přírůstková synchronizace dat zobrazí stav funkce Change Data Capture (CDC).

     ![Stav aktivity – Úplné načtení dokončeno](media/tutorial-oracle-azure-postgresql-online/dms-activity-full-load-completed.png)

     ![Stav aktivity – Přírůstková synchronizace dat](media/tutorial-oracle-azure-postgresql-online/dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>Provedení přímé migrace

Po dokončení počátečního úplného načtení se databáze označí jako **Připraveno k přímé migraci**.

1. Jakmile budete připraveni dokončit migraci databází, vyberte **Spustit přímou migraci**.

2. Ujistěte se, že jste zastavili všechny příchozí transakce do zdrojové databáze a počkejte, dokud se v čítači **Probíhající změny** nezobrazí **0**.

   ![Spuštění přímé migrace](media/tutorial-oracle-azure-postgresql-online/dms-start-cutover.png)

3. Vyberte **Potvrdit** a pak **Použít**.
4. Když se zobrazí stav migrace databáze **dokončeno**, připojení aplikací k nový cíl – Azure Database for postgresql – instance.

 > [!NOTE]
 > Protože PostgreSQL ve výchozím nastavení má schema.table.column malými písmeny, můžete se vrátit z velká písmena na malá písmena pomocí skriptu v **nastavení schématu ve službě Azure Database for PostgreSQL** dříve v tomto článku.

## <a name="next-steps"></a>Další postup

* Informace o známých problémech a omezeních při provádění online migrací do služby Azure Database for PostgreSQL najdete v článku [Známé problémy s online migracemi do služby Azure Database for PostgreSQLa jejich řešení](known-issues-azure-postgresql-online.md).
* Informace o Azure Database Migration Service, najdete v článku [co je Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
* Informace o službě Azure Database for PostgreSQL, najdete v článku [co je Azure Database for PostgreSQL?](https://docs.microsoft.com/azure/postgresql/overview).
