---
title: 'Kurz: Pomocí Azure Database Migration Service online migrace RDS MySQL do služby Azure Database for MySQL | Dokumentace Microsoftu'
description: Zjistěte, jak provést online migraci z RDS MySQL do služby Azure Database for MySQL pomocí Azure Database Migration Service.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 04/30/2019
ms.openlocfilehash: 3f5a69454356d16fd6f3c5dddb063fcf8e450014
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943662"
---
# <a name="tutorial-migrate-rds-mysql-to-azure-database-for-mysql-online-using-dms"></a>Kurz: Migrace RDS MySQL do služby Azure Database for MySQL online pomocí DMS

Azure Database Migration Service můžete použít k migraci databází z instance RDS MySQL pro [– Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/) když zdrojová databáze zůstane online v průběhu migrace. Jinými slovy můžete dosáhnout migrace s minimálními prostoji do aplikace. V tomto kurzu, migrujete **zaměstnanci** ukázkovou databázi z instance RDS MySQL do služby Azure Database for MySQL s využitím online migrace aktivity v Azure Database Migration Service.

V tomto kurzu se naučíte:
> [!div class="checklist"]
>
> * Migrace schématu vzorku pomocí nástrojů mysqldump využívající databázi mysql.
> * Vytvoření instance služby Azure Database Migration Service.
> * Vytvořte projekt migrace s využitím Azure Database Migration Service.
> * Spuštění migrace
> * Monitorování migrace

> [!NOTE]
> Použití Azure Database Migration Service online migrace vyžaduje vytvoření instance založené na cenovou úroveň Premium. Další informace najdete v tématu Azure Database Migration Service [ceny](https://azure.microsoft.com/pricing/details/database-migration/) stránky.

> [!IMPORTANT]
> Aby migrace proběhla optimálním způsobem, doporučuje Microsoft vytvořit instanci služby Azure Database Migration Service ve stejné oblasti Azure, ve které je cílová databáze. Přenášení dat mezi oblastmi geografickými lokalitami může zpomalit proces migrace a způsobit chyby.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Tento článek popisuje, jak provést migraci online z instance RDS MySQL do služby Azure Database for MySQL.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu je potřeba provést následující:

* Ujistěte se, že zdrojový MySQL server je spuštěna podporovaná MySQL community edition. Pro určení verze instance MySQL v nástroj mysql nebo MySQL Workbench, spusťte příkaz:

    ```
    SELECT @@version;
    ```

    Další informace najdete v článku [podporované – Azure Database for MySQL – verze](https://docs.microsoft.com/azure/mysql/concepts-supported-versions).

* Stáhněte a nainstalujte [MySQL **zaměstnanci** ukázkovou databázi](https://dev.mysql.com/doc/employee/en/employees-installation.html).
* Vytvoření instance [– Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/quickstart-create-mysql-server-database-using-azure-portal).
* Vytvoření služby Azure Virtual Network (VNet) pro Azure Database Migration Service pomocí modelu nasazení Azure Resource Manageru, který poskytuje připojení site-to-site k vašich zdrojových serverů s místními pomocí [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
* Ujistěte se, že pravidla skupiny zabezpečení sítě VNet nedošlo k blokování následující porty příchozí komunikace k Azure Database Migration Service: 53, 443, 9354, 445 a 12000. Další podrobnosti o filtrování provozu skupiny zabezpečení sítě Azure VNet najdete v článku [filtrování provozu sítě s použitím skupin zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Nakonfigurujte bránu [Windows Firewall pro přístup k databázovému stroji](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Otevřete bránu Windows firewall a povolit Azure Database Migration Service přístup k serveru MySQL zdroj, který ve výchozím nastavení je TCP port 3306.
* Pokud před zdrojovými databázemi používáte zařízení brány firewall, možná bude potřeba přidat pravidla brány firewall, která službě Azure Database Migration Service povolí přístup ke zdrojovým databázím za účelem migrace.
* Vytvořte úrovni serveru [pravidlo brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) pro Azure Database for MySQL server Azure Database Migration Service přístup k cílovým databázím. Zadejte rozsah podsítě virtuální sítě pro Azure Database Migration Service.

> [!NOTE]
> Azure Database for MySQL podporuje pouze InnoDB tabulky. Pokud chcete převést tabulky MyISAM na InnoDB, naleznete v článku [převod tabulky MyISAM na InnoDB](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html) .

### <a name="set-up-aws-rds-mysql-for-replication"></a>Nastavení AWS RDS MySQL pro replikaci

1. Pokud chcete vytvořit novou skupinu parametrů, postupujte podle pokynů od AWS v článku [soubory protokolů databáze MySQL](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html)v **formátu binárního protokolování** oddílu.
2. Vytvořte novou skupinu parametrů s následující konfigurací:
    * binlog_format = řádek
    * binlog_checksum = NONE
3. Uložte novou skupinu parametrů.

## <a name="migrate-the-schema"></a>Migrace schématu

1. Extrahování schématu ze zdrojové databáze a použít na cílovou databázi a dokončit migraci všech databázových objektů, jako jsou schémata tabulek, indexů a uložené procedury.

    Nejjednodušší způsob, jak migrovat pouze schéma se pomocí mysqldump pomocí parametru--no-data. Příkaz k migraci schématu je:

    ```
    mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
    ```
    
    Například soubor schématu pro se stavem systému **zaměstnanci** databáze, použijte následující příkaz:
    
    ```
    mysqldump -h 10.10.123.123 -u root -p --databases employees --no-data > d:\employees.sql
    ```

2. Importujte schéma do cílové služby, která je – Azure Database for MySQL. Chcete-li obnovit soubor s výpisem paměti schématu, spusťte následující příkaz:

    ```
    mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
    ```

    Například, chcete-li importovat schéma pro **zaměstnanci** databáze:

    ```
    mysql.exe -h shausample.mysql.database.azure.com -u dms@shausample -p employees < d:\employees.sql
    ```

3. Pokud vaše schéma obsahuje cizí klíče, počáteční načtení a průběžná synchronizace migrace selžou. Extrahovat rozevírací cizího klíče skriptů a přidat skript cizí klíče v cílovém umístění (Azure Database for MySQL), spusťte následující skript v aplikaci MySQL Workbench:

    ```
    SET group_concat_max_len = 8192;
        SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery
        FROM
        (SELECT
        KCU.REFERENCED_TABLE_SCHEMA as SchemaName,
        KCU.TABLE_NAME,
        KCU.COLUMN_NAME,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery
        FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC
        WHERE
          KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME
          AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA
      AND KCU.REFERENCED_TABLE_SCHEMA = ['SchemaName') Queries
      GROUP BY SchemaName;
    ```

4. Přetažení cizí klíč (což je druhý sloupec) spusťte ve výsledku dotazu. odpojení cizí klíč.

5. Pokud máte v datech aktivační události (insert nebo update aktivovat), vynutí integrity dat v cílové před replikaci dat ze zdroje. Doporučuje se zakázat aktivační události ve všech tabulkách *v cílovém* během migrace a potom povolit aktivačních událostí po migrace dokončit.

    Chcete-li zakázat aktivační události v cílové databázi:

    ```
    select concat ('alter table ', event_object_table, ' disable trigger ', trigger_name)
    from information_schema.triggers;
    ```

6. Pokud je instance datového typu VÝČTU v žádné tabulky, doporučujeme dočasně aktualizovat datový typ znak různé v cílové tabulce. Po dokončení replikace dat pak vrátit datový typ pro výčet.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrace poskytovatele prostředků Microsoft.DataMigration

1. Přihlaste se k webu Azure Portal, vyberte **Všechny služby** a pak vyberte **Předplatná**.

   ![Zobrazení předplatných na portálu](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-subscription1.png)

2. Vyberte předplatné, ve kterém chcete vytvořit instanci služby Azure Database Migration Service a pak vyberte **poskytovatelů prostředků**.

    ![Zobrazení poskytovatelů prostředků](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-resource-provider.png)

3. Vyhledejte „migration“ a pak napravo od **Microsoft.DataMigration** vyberte **Zaregistrovat**.

    ![Registrace poskytovatele prostředků](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-register-resource-provider.png)

## <a name="create-an-instance-of-azure-database-migration-service"></a>Vytvoření instance služby Azure Database Migration Service

1. Na webu Azure Portal vyberte **+ Vytvořit prostředek**, vyhledejte „Azure Database Migration Service“ a pak v rozevíracím seznamu vyberte **Azure Database Migration Service**.

    ![Azure Marketplace](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-marketplace.png)

2. Na obrazovce **Azure Database Migration Service** vyberte **Vytvořit**.

    ![Vytvoření instance služby Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create1.png)
  
3. Na obrazovce **Vytvořit službu Migration Service** zadejte název služby, předplatné a novou nebo existující skupinu prostředků.

4. Vyberte umístění, ve kterém chcete vytvořit instanci služby Azure Database Migration Service.

5. Vyberte existující virtuální síť nebo vytvořte novou.

    Virtuální síť poskytuje přístup k instanci MySQL zdroj a cíl – Azure Database pro instanci MySQL – Azure Database Migration Service.

    Další informace o tom, jak vytvořit síť VNet na portálu Azure portal najdete v článku [vytvořit virtuální síť pomocí webu Azure portal](https://aka.ms/DMSVnet).

6. Vyberte cenovou úroveň; pro tuto online migraci je nutné vybrat Premium: 4vCores cenovou úroveň.

    Další informace o nákladech a cenových úrovních najdete na [stránce s cenami](https://aka.ms/dms-pricing). Pokud potřebujete pomoc s výběrem správné Azure Database Migration Service cenovou úroveň, podívejte se na doporučení účtování [tady](https://go.microsoft.com/fwlink/?linkid=861067).

     ![Konfigurace nastavení instance služby Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-settings3.png)

7. Vyberte **Vytvořit** a vytvořte službu.

## <a name="create-a-migration-project"></a>Vytvoření projektu migrace

Po vytvoření služby ji vyhledejte na webu Azure Portal, otevřete ji a pak vytvořte nový projekt migrace.

1. Na webu Azure Portal vyberte **Všechny služby**, vyhledejte „Azure Database Migration Service“ a pak vyberte **Služby Azure Database Migration Service**.

      ![Vyhledání všech instancí služby Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-search.png)

2. Na obrazovce **Služby Azure Database Migration Service** vyhledejte název instance služby Azure Database Migration Service, kterou jste vytvořili, a vyberte ji.

     ![Vyhledání instance služby Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-instance-search.png)

3. Vyberte **+ Nový projekt migrace**.
4. Na **nový projekt migrace** obrazovky, zadejte název projektu, v **serveru typ zdroje** textového pole, vyberte **MySQL**a potom v **cíl Typ serveru** textového pole, vyberte **AzureDbForMySQL**.
5. V **zvolte typ aktivity** vyberte **Online data migrace**.

    > [!IMPORTANT]
    > Je potřeba vybrat možnost **Online data migrace**; offline migrace nejsou podporované pro tento scénář.

    ![Vytvoření projektu Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create-project6.png)

    > [!NOTE]
    > Alternativně můžete zvolit **pouze vytvořit projekt** teď vytvořit projekt migrace a spustit migraci později.

6. Vyberte **Uložit**.

7. Vyberte **Vytvořit a spustit aktivitu** a vytvořte projekt a spusťte aktivitu migrace.

    > [!NOTE]
    > Ujistěte se prosím poznámku o požadavky potřebné k nastavení online migrace v okně vytváření projektu.

## <a name="specify-source-details"></a>Zadání podrobností o zdroji

* Na **podrobnosti zdroje migrace** obrazovky, zadat podrobnosti připojení pro instanci MySQL zdroje.

   ![Podrobnosti zdroje](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-source-details5.png)

## <a name="specify-target-details"></a>Zadání podrobností o cíli

1. Vyberte **Uložit**a pak na **cílit na podrobnosti** obrazovky, zadat podrobnosti připojení pro cílovou službu Azure Database pro server MySQL, který je předem zřízený a má **zaměstnanci** nasazení pomocí MySQLDump schématu.

    ![Výběr cíle](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-select-target5.png)

2. Vyberte **Uložit** a pak na obrazovce **Mapovat na cílové databáze** namapujte zdrojovou a cílovou databázi pro migraci.

    Pokud cílová databáze obsahuje název stejné databáze jako zdrojové databáze, vybere Azure Database Migration Service ve výchozím nastavení cílové databázi.

    ![Mapování na cílové databáze](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-map-targets-activity5.png)

3. Vyberte **Uložit**, na obrazovce **Shrnutí migrace** do textového pole **Název aktivity** zadejte název aktivity migrace a pak zkontrolujte souhrnné informace a ujistěte se, že podrobnosti zdroje a cíle odpovídají dříve zadaným informacím.

    ![Shrnutí migrace](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-migration-summary2.png)

## <a name="run-the-migration"></a>Spuštění migrace

* Vyberte **Spustit migraci**.

    Zobrazí se okno aktivity migrace a **Stav** aktivity bude **Inicializace**.

## <a name="monitor-the-migration"></a>Monitorování migrace

1. Na obrazovce aktivity migrace vyberte **Aktualizovat** a aktualizujte zobrazení, dokud se **Stav** migrace nezmění na **Spuštěno**.

    ![Stav aktivity – spuštění](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-activity-status4.png)

2. V části **NÁZEV_DATABÁZE**, vyberte konkrétní databázi k získání stavu migrace pro **úplné načtení dat** a **Přírůstková synchronizace dat** operace.

    **Úplné načtení dat** zobrazuje stav migrace počátečním načtení, zatímco **Přírůstková synchronizace dat** ukazuje změnit stav data capture (CDC).

    ![Načtení inventáře obrazovky – úplná](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-full-load.png)

    ![Obrazovka inventáře - Přírůstková synchronizace dat](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-incremental.png)

## <a name="perform-migration-cutover"></a>Provedení přímé migrace

Po dokončení počáteční úplné načtení databází se označí **připravení Cutover**.

1. Jakmile budete připraveni dokončit migraci databází, vyberte **Spustit přímou migraci**.

    ![Začněte od začátku vyjmutí](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-start-cutover.png)

2. Ujistěte se, že jste zastavili všechny příchozí transakce do zdrojové databáze a počkejte, dokud se v čítači **Probíhající změny** nezobrazí **0**.
3. Vyberte **Potvrdit** a pak **Použít**.
4. Když se zobrazí stav migrace databáze **dokončeno**, připojení aplikací k nový cíl – Azure Database pro databázi MySQL.

Online migrace místní instanci MySQL do služby Azure Database for MySQL je nyní dokončena.

## <a name="next-steps"></a>Další postup

* Informace o službě Azure Database Migration Service najdete v článku [Co je Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
* Informace o službě Azure Database for MySQL, najdete v článku [co je Azure Database for MySQL?](https://docs.microsoft.com/azure/mysql/overview).
* Další otázky, e-mailu [požádejte migracemi databází Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com) alias.
