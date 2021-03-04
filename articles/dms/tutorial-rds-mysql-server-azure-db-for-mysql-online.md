---
title: 'Kurz: migrace služby RDS MySQL online na Azure Database for MySQL'
titleSuffix: Azure Database Migration Service
description: Naučte se provádět online migraci ze služby RDS MySQL do Azure Database for MySQL pomocí Azure Database Migration Service.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 06/09/2020
ms.openlocfilehash: 3d200cd9bccecf03f5313058189e134082a1a357
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101742615"
---
# <a name="tutorial-migrate-rds-mysql-to-azure-database-for-mysql-online-using-dms"></a>Kurz: Online migrace MySQL do služby Azure Database for MySQL přes Vzdálenou plochu pomocí DMS

Pomocí Azure Database Migration Service můžete migrovat databáze z instance služby RDS MySQL do [Azure Database for MySQL](../mysql/index.yml) , zatímco zdrojová databáze zůstane během migrace online. Jinými slovy, migraci je možné dosáhnout s minimálními výpadky aplikace. V tomto kurzu migrujete ukázkovou databázi **zaměstnanci** z instance služby RDS MySQL do Azure Database for MySQL pomocí online aktivity migrace v Azure Database Migration Service.

V tomto kurzu se naučíte:
> [!div class="checklist"]
>
> * Pomocí nástrojů mysqldump a MySQL migrujte ukázkové schéma.
> * Vytvořte instanci služby Azure Database Migration Service.
> * Vytvořte projekt migrace pomocí Azure Database Migration Service.
> * Spuštění migrace
> * Monitorujte migraci.

> [!NOTE]
> Použití Azure Database Migration Service k provedení online migrace vyžaduje vytvoření instance založené na cenové úrovni Premium. Další informace najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/database-migration/) Azure Database Migration Service.

> [!IMPORTANT]
> Aby migrace proběhla optimálním způsobem, doporučuje Microsoft vytvořit instanci služby Azure Database Migration Service ve stejné oblasti Azure, ve které je cílová databáze. Přenášení dat mezi oblastmi geografickými lokalitami může zpomalit proces migrace a způsobit chyby.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Tento článek popisuje, jak provést online migraci z instance služby RDS MySQL do Azure Database for MySQL.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu je potřeba provést následující:

* Zajistěte, aby na zdrojovém serveru MySQL běžela podporovaná komunita MySQL Community Edition. Pokud chcete zjistit verzi instance MySQL, spusťte v nástroji MySQL nebo MySQL Workbench příkaz:

    ```
    SELECT @@version;
    ```

    Další informace najdete v článku [podporovaném Azure Database for MySQL verzích](../mysql/concepts-supported-versions.md).

* Stáhněte a nainstalujte [ukázkovou databázi MySQL **Employees**](https://dev.mysql.com/doc/employee/en/employees-installation.html).
* Vytvořte instanci [Azure Database for MySQL](../mysql/quickstart-create-mysql-server-database-using-azure-portal.md).
* Vytvořte Microsoft Azure Virtual Network pro Azure Database Migration Service pomocí modelu nasazení Azure Resource Manager, který umožňuje připojení typu Site-to-site k místním zdrojovým serverům pomocí [ExpressRoute](../expressroute/expressroute-introduction.md) nebo [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Další informace o vytváření virtuálních sítí najdete v [dokumentaci k Virtual Network](../virtual-network/index.yml)a zejména v článcích rychlý Start s podrobnými údaji.
* Zajistěte, aby pravidla skupiny zabezpečení sítě virtuálních sítí neblokovala odchozí port 443 ServiceTag pro ServiceBus, Storage a AzureMonitor. Další podrobnosti o filtrování provozu NSG virtuální sítě najdete v článku [filtrování provozu sítě pomocí skupin zabezpečení sítě](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Nakonfigurujte [bránu Windows Firewall](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) (nebo bránu firewall pro Linux) tak, aby umožňovala přístup k databázovému stroji. Pro MySQL server povolte připojení port 3306.

> [!NOTE]
> Azure Database for MySQL podporuje pouze tabulky InnoDB. Chcete-li převést tabulky MyISAM na InnoDB, přečtěte si článek [Převod tabulek z MyISAM na InnoDB](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html) .

### <a name="set-up-aws-rds-mysql-for-replication"></a>Nastavení AWS VP MySQL pro replikaci

1. Pokud chcete vytvořit novou skupinu parametrů, postupujte podle pokynů AWS v článku [soubory protokolů databáze MySQL](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html)v části **Formát binárního protokolování** .
2. Vytvořte novou skupinu parametrů s následující konfigurací:
    * log_bin = ZAPNUTo
    * binlog_format = row
    * binlog_checksum = žádné
3. Uložte novou skupinu parametrů.
4. Přidružte novou skupinu parametrů k instanci služby RDS MySQL. Může být nutné restartovat počítač.
5. Jakmile je skupina parametrů zavedena, připojte se k instanci MySQL a [nastavte uchování binlog](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/mysql_rds_set_configuration.html#mysql_rds_set_configuration-usage-notes.binlog-retention-hours) aspoň na 5 dní.
```
call mysql.rds_set_configuration('binlog retention hours', 120);
```

## <a name="migrate-the-schema"></a>Migrace schématu

1. Extrahujte schéma ze zdrojové databáze a použijte ho pro cílovou databázi k dokončení migrace všech databázových objektů, jako jsou schémata tabulky, indexy a uložené procedury.

    Nejjednodušší způsob, jak migrovat jenom schéma, je použít mysqldump s parametrem--No-data. Příkaz pro migraci schématu je následující:

    ```
    mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
    ```
    
    Chcete-li například vypsat soubor schématu pro databázi **Employees** , použijte následující příkaz:
    
    ```
    mysqldump -h 10.10.123.123 -u root -p --databases employees --no-data > d:\employees.sql
    ```

2. Importujte schéma do cílové služby, což je Azure Database for MySQL. Chcete-li obnovit soubor s výpisem stavu schématu, spusťte následující příkaz:

    ```
    mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
    ```

    Pokud například chcete importovat schéma pro databázi **Employees** :

    ```
    mysql.exe -h shausample.mysql.database.azure.com -u dms@shausample -p employees < d:\employees.sql
    ```

3. Pokud vaše schéma obsahuje cizí klíče, počáteční načtení a průběžná synchronizace migrace selžou. Chcete-li extrahovat skript cizího klíče a přidat do cílového umístění skript cizího klíče (Azure Database for MySQL), spusťte následující skript v MySQL Workbench:

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
      AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries
      GROUP BY SchemaName;
    ```

4. Spuštěním odkládacího klíče (který je druhým sloupcem) ve výsledku dotazu vyřaďte cizí klíč.

> [!NOTE]
> Azure DMS nepodporuje referenční akci CASCADE, která pomáhá automaticky odstranit nebo aktualizovat shodný řádek v podřízené tabulce při odstranění nebo aktualizaci řádku v nadřazené tabulce. Další informace najdete v části referenční akce článku [omezení cizího klíče](https://dev.mysql.com/doc/refman/8.0/en/create-table-foreign-keys.html)v dokumentaci k MySQL.
> Azure DMS vyžaduje, abyste při počátečním načtení dat vyřadíte omezení cizího klíče v cílovém databázovém serveru a nemůžete použít referenční akce. Pokud vaše zatížení závisí na aktualizaci související podřízené tabulky prostřednictvím této referenční akce, doporučujeme místo toho provést [Výpis a obnovení](../mysql/concepts-migrate-dump-restore.md) . 

5. Pokud jste v datech triggery (vložení nebo aktualizace triggeru), vynutili integritu dat v cíli před replikací dat ze zdroje. Doporučením je zakázat aktivační události ve všech tabulkách *v cíli* během migrace a poté povolit triggery po dokončení migrace.

    Zakázání triggerů v cílové databázi:

    ```
    select concat ('alter table ', event_object_table, ' disable trigger ', trigger_name)
    from information_schema.triggers;
    ```

6. Pokud jsou instance datového typu výčtu v jakékoli tabulce, doporučujeme dočasně aktualizovat datový typ "Character varyed" v cílové tabulce. Po dokončení replikace dat obnovte datový typ na ENUM.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrace poskytovatele prostředků Microsoft.DataMigration

1. Přihlaste se k webu Azure Portal, vyberte **Všechny služby** a pak vyberte **Předplatná**.

   ![Zobrazení předplatných na portálu](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-subscription1.png)

2. Vyberte předplatné, ve kterém chcete vytvořit instanci Azure Database Migration Service a pak vyberte **poskytovatelé prostředků**.

    ![Zobrazení poskytovatelů prostředků](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-resource-provider.png)

3. Vyhledejte „migration“ a pak napravo od **Microsoft.DataMigration** vyberte **Zaregistrovat**.

    ![Registrace poskytovatele prostředků](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-register-resource-provider.png)

## <a name="create-an-instance-of-azure-database-migration-service"></a>Vytvoření instance služby Azure Database Migration Service

1. Na webu Azure Portal vyberte **+ Vytvořit prostředek**, vyhledejte „Azure Database Migration Service“ a pak v rozevíracím seznamu vyberte **Azure Database Migration Service**.

    ![Azure Marketplace](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-marketplace.png)

2. Na obrazovce **Azure Database Migration Service** vyberte **Vytvořit**.

    ![Vytvoření instance služby Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create1.png)
  
3. Na obrazovce **Vytvořit službu Migration Service** zadejte název služby, předplatné a novou nebo existující skupinu prostředků.

4. Vyberte umístění, ve kterém chcete vytvořit instanci Azure Database Migration Service.

5. Vyberte existující virtuální síť nebo vytvořte novou.

    Virtuální síť poskytuje Azure Database Migration Service s přístupem ke zdrojové instanci MySQL a cílové instanci Azure Database for MySQL.

    Další informace o tom, jak vytvořit virtuální síť v Azure Portal, najdete v článku [vytvoření virtuální sítě pomocí Azure Portal](../virtual-network/quick-create-portal.md).

6. Vyberte cenovou úroveň; v případě této online migrace nezapomeňte vybrat cenovou úroveň Premium: 4vCores.

    ![Konfigurace nastavení instance služby Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-settings3.png)

7. Vyberte **Vytvořit** a vytvořte službu.

## <a name="create-a-migration-project"></a>Vytvoření projektu migrace

Po vytvoření služby ji vyhledejte na webu Azure Portal, otevřete ji a pak vytvořte nový projekt migrace.

1. Na webu Azure Portal vyberte **Všechny služby**, vyhledejte „Azure Database Migration Service“ a pak vyberte **Služby Azure Database Migration Service**.

      ![Vyhledání všech instancí služby Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-search.png)

2. Na obrazovce **Služby Azure Database Migration Service** vyhledejte název instance služby Azure Database Migration Service, kterou jste vytvořili, a vyberte ji.

     ![Vyhledání instance služby Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-instance-search.png)

3. Vyberte **+ Nový projekt migrace**.
4. Na obrazovce **Nový projekt migrace** zadejte název projektu, do textového pole **typ zdrojového serveru** vyberte **MySQL** a potom do textového pole **typ cílového serveru** vyberte **AzureDbForMySQL**.
5. V části **Zvolte typ aktivity** vyberte možnost **migrace online dat**.

    > [!IMPORTANT]
    > Ujistěte se, že jste vybrali **online migraci dat**; offline migrace nejsou v tomto scénáři podporovány.

    ![Vytvoření projektu Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create-project6.png)

    > [!NOTE]
    > Alternativně můžete zvolit **vytvořit projekt pouze** k vytvoření projektu migrace a provést migraci později.

6. Vyberte **Uložit**.

7. Vyberte **Vytvořit a spustit aktivitu** a vytvořte projekt a spusťte aktivitu migrace.

    > [!NOTE]
    > Poznamenejte si požadavky potřebné k nastavení online migrace v okně pro vytvoření projektu.

## <a name="specify-source-details"></a>Zadání podrobností o zdroji

* Na obrazovce **Podrobnosti o zdroji migrace** zadejte podrobnosti připojení zdrojové instance MySQL.

   ![Podrobnosti zdroje](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-source-details5.png)

## <a name="specify-target-details"></a>Zadání podrobností o cíli

1. Vyberte **Uložit** a pak na obrazovce **cílové podrobnosti** zadejte podrobnosti o připojení pro cílový Azure Database for MySQL server, který je předem zřízený a má schéma **zaměstnanci** nasazené pomocí MySQLDump.

    ![Výběr cíle](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-select-target5.png)

2. Vyberte **Uložit** a pak na obrazovce **Mapovat na cílové databáze** namapujte zdrojovou a cílovou databázi pro migraci.

    Pokud cílová databáze obsahuje stejný název databáze jako zdrojová databáze, Azure Database Migration Service ve výchozím nastavení vybere cílovou databázi.

    ![Mapování na cílové databáze](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-map-targets-activity5.png)

3. Vyberte **Uložit**, na obrazovce **Shrnutí migrace** do textového pole **Název aktivity** zadejte název aktivity migrace a pak zkontrolujte souhrnné informace a ujistěte se, že podrobnosti zdroje a cíle odpovídají dříve zadaným informacím.

    ![Shrnutí migrace](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-migration-summary2.png)

## <a name="run-the-migration"></a>Spuštění migrace

* Vyberte **Spustit migraci**.

    Zobrazí se okno aktivity migrace a **Stav** aktivity bude **Inicializace**.

## <a name="monitor-the-migration"></a>Monitorování migrace

1. Na obrazovce aktivity migrace vyberte **Aktualizovat** a aktualizujte zobrazení, dokud se **Stav** migrace nezmění na **Spuštěno**.

    ![Stav aktivity – spuštěno](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-activity-status4.png)

2. V části **název databáze** vyberte konkrétní databázi, abyste získali stav migrace pro operace **úplného načtení dat** a **přírůstkové synchronizace dat** .

    **Úplné načtení dat** znázorňuje počáteční stav migrace zatížení, zatímco **přírůstkové synchronizace dat** zobrazuje stav Change Data Capture (CDC).

    ![Obrazovka inventáře – úplné zatížení dat](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-full-load.png)

    ![Obrazovka inventáře – přírůstková synchronizace dat](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-incremental.png)

## <a name="perform-migration-cutover"></a>Provedení přímé migrace

Po dokončení počátečního úplného načtení budou databáze označeny jako **připravené k přímou migraci**.

1. Jakmile budete připraveni dokončit migraci databází, vyberte **Spustit přímou migraci**.

    ![Spustit střih](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-start-cutover.png)

2. Ujistěte se, že jste zastavili všechny příchozí transakce do zdrojové databáze a počkejte, dokud se v čítači **Probíhající změny** nezobrazí **0**.
3. Vyberte **Potvrdit** a pak **Použít**.
4. Až se stav migrace databáze zobrazí jako **dokončený**, připojte své aplikace k nové cílové Azure Database for MySQL databázi.

Vaše online migrace místní instance MySQL do Azure Database for MySQL je teď dokončená.

## <a name="next-steps"></a>Další kroky

* Informace o službě Azure Database Migration Service najdete v článku [Co je Azure Database Migration Service?](./dms-overview.md).
* Informace o Azure Database for MySQL najdete v článku [co je Azure Database for MySQL?](../mysql/overview.md).
* Další otázky získáte v e-mailu s názvem požádat o alias [migrace databáze Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com) .