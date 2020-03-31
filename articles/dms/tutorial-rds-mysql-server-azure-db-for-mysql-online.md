---
title: 'Kurz: Migrace RDS MySQL online do databáze Azure pro MySQL'
titleSuffix: Azure Database Migration Service
description: Naučte se provádět online migraci z RDS MySQL do databáze Azure pro MySQL pomocí služby Migrace databáze Azure.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: c34de48d0184057f42d1b779abee56e1fa9ac169
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255168"
---
# <a name="tutorial-migrate-rds-mysql-to-azure-database-for-mysql-online-using-dms"></a>Kurz: Migrace RDS MySQL do databáze Azure pro MySQL online pomocí DMS

Službu migrace databáze Azure můžete použít k migraci databází z instance RDS MySQL do [databáze Azure pro MySQL,](https://docs.microsoft.com/azure/mysql/) zatímco zdrojová databáze zůstane během migrace online. Jinými slovy migrace lze dosáhnout s minimálníprostožně do aplikace. V tomto kurzu migrujete ukázkovou databázi **zaměstnanců** z instance RDS MySQL do databáze Azure pro MySQL pomocí aktivity migrace online ve službě Migrace databáze Azure.

V tomto kurzu se naučíte:
> [!div class="checklist"]
>
> * Migrujte ukázkové schéma pomocí mysqldump a mysql nástroje.
> * Vytvořte instanci služby Azure Database Migration Service.
> * Vytvořte projekt migrace pomocí služby Migrace databáze Azure.
> * Spuštění migrace
> * Monitorování migrace

> [!NOTE]
> Použití služby Migrace databáze Azure k provedení migrace online vyžaduje vytvoření instance založené na cenové úrovni Premium. Další informace najdete na stránce [s cenami](https://azure.microsoft.com/pricing/details/database-migration/) služby Azure Database Migration Service.

> [!IMPORTANT]
> Aby migrace proběhla optimálním způsobem, doporučuje Microsoft vytvořit instanci služby Azure Database Migration Service ve stejné oblasti Azure, ve které je cílová databáze. Přenášení dat mezi oblastmi geografickými lokalitami může zpomalit proces migrace a způsobit chyby.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Tento článek popisuje, jak provést online migraci z instance RDS MySQL do Azure Database for MySQL.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu je potřeba provést následující:

* Ujistěte se, že zdrojový server MySQL používá podporovanou komunitní edici MySQL. Chcete-li určit verzi instance MySQL, v nástroji mysql nebo MySQL Workbench spusťte příkaz:

    ```
    SELECT @@version;
    ```

    Další informace naleznete v článku [Podporovaná databáze Azure pro verze MySQL](https://docs.microsoft.com/azure/mysql/concepts-supported-versions).

* Stáhněte a nainstalujte [ukázkovou databázi **Zaměstnanců** MySQL](https://dev.mysql.com/doc/employee/en/employees-installation.html).
* Vytvořte instanci [Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/quickstart-create-mysql-server-database-using-azure-portal).
* Vytvořte službu Migrace databáze Microsoft Azure pro Azure pomocí modelu nasazení Azure Resource Manageru, který poskytuje připojení k místním zdrojovým serverům site-to-site pomocí [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Další informace o vytvoření virtuální sítě naleznete v [dokumentaci k virtuální síti](https://docs.microsoft.com/azure/virtual-network/)a zejména v článcích rychlého startu s podrobnými podrobnostmi.
* Ujistěte se, že pravidla skupiny zabezpečení sítě virtuální sítě neblokují následující příchozí komunikační porty služby Azure Database Migration Service: 443, 53, 9354, 445 a 12000. Další podrobnosti o filtrování přenosů skupin nsg ve virtuální síti naleznete v článku [Filtrování síťového provozu se skupinami zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Nakonfigurujte [bránu Windows Firewall](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) (nebo bránu firewall pro Linux) tak, aby umožňovala přístup k databázovému stroji. Pro MySQL server povolte port 3306 pro připojení.

> [!NOTE]
> Azure Database for MySQL podporuje jenom tabulky InnoDB. Chcete-li převést tabulky MyISAM na InnoDB, naleznete v článku [Převod tabulek z MyISAM na InnoDB](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html) .

### <a name="set-up-aws-rds-mysql-for-replication"></a>Nastavení služby AWS RDS MySQL pro replikaci

1. Chcete-li vytvořit novou skupinu parametrů, postupujte podle pokynů poskytnutých společností AWS v článku [Soubory protokolu databáze MySQL](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html)v části **Formát binárního protokolování.**
2. Vytvořte novou skupinu parametrů s následující konfigurací:
    * binlog_format = row
    * binlog_checksum = ŽÁDNÝ
3. Uložte novou skupinu parametrů.
4. Přidružte novou skupinu parametrů k instanci RDS MySQL. Může být vyžadováno restartování počítače.

## <a name="migrate-the-schema"></a>Migrace schématu

1. Extrahujte schéma ze zdrojové databáze a aplikujte na cílovou databázi k dokončení migrace všech databázových objektů, jako jsou schémata tabulky, indexy a uložené procedury.

    Nejjednodušší způsob, jak migrovat pouze schéma je použít mysqldump s parametrem --no-data. Příkaz pro migraci schématu je:

    ```
    mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
    ```
    
    Chcete-li například vyřadit soubor schématu pro databázi **Zaměstnanci,** použijte následující příkaz:
    
    ```
    mysqldump -h 10.10.123.123 -u root -p --databases employees --no-data > d:\employees.sql
    ```

2. Importujte schéma do cílové služby, což je Azure Database for MySQL. Chcete-li obnovit soubor výpisu schématu, spusťte následující příkaz:

    ```
    mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
    ```

    Chcete-li například importovat schéma pro databázi **Zaměstnanci:**

    ```
    mysql.exe -h shausample.mysql.database.azure.com -u dms@shausample -p employees < d:\employees.sql
    ```

3. Pokud vaše schéma obsahuje cizí klíče, počáteční načtení a průběžná synchronizace migrace selžou. Chcete-li extrahovat skript drop cizího klíče a přidat skript cizího klíče v cílovém umístění (Azure Database for MySQL), spusťte v mysql workbench následující skript:

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

4. Spusťte přetažení cizího klíče (což je druhý sloupec) ve výsledku dotazu k přetažení cizího klíče.

5. Pokud máte aktivační události (vložit nebo aktualizovat aktivační událost) v datech, bude vynucovat integritu dat v cíli před replikací dat ze zdroje. Doporučujeme zakázat aktivační události ve všech tabulkách *v cíli* během migrace a po dokončení migrace povolit aktivační události.

    Zakázání aktivačních událostí v cílové databázi:

    ```
    select concat ('alter table ', event_object_table, ' disable trigger ', trigger_name)
    from information_schema.triggers;
    ```

6. Pokud existují instance datového typu ENUM v libovolné tabulce, doporučujeme dočasně aktualizovat na datový typ 'znak měnící' v cílové tabulce. Replikace dat WHen je dokončena a potom vrátí datový typ do eNUM.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrace poskytovatele prostředků Microsoft.DataMigration

1. Přihlaste se k webu Azure Portal, vyberte **Všechny služby** a pak vyberte **Předplatná**.

   ![Zobrazení předplatných na portálu](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-subscription1.png)

2. Vyberte předplatné, ve kterém chcete vytvořit instanci služby Migrace databáze Azure, a pak vyberte **zprostředkovatele prostředků**.

    ![Zobrazení poskytovatelů prostředků](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-resource-provider.png)

3. Vyhledejte „migration“ a pak napravo od **Microsoft.DataMigration** vyberte **Zaregistrovat**.

    ![Registrace poskytovatele prostředků](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-register-resource-provider.png)

## <a name="create-an-instance-of-azure-database-migration-service"></a>Vytvoření instance služby Migrace databáze Azure

1. Na webu Azure Portal vyberte **+ Vytvořit prostředek**, vyhledejte „Azure Database Migration Service“ a pak v rozevíracím seznamu vyberte **Azure Database Migration Service**.

    ![Azure Marketplace](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-marketplace.png)

2. Na obrazovce **Azure Database Migration Service** vyberte **Vytvořit**.

    ![Vytvoření instance služby Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create1.png)
  
3. Na obrazovce **Vytvořit službu Migration Service** zadejte název služby, předplatné a novou nebo existující skupinu prostředků.

4. Vyberte umístění, ve kterém chcete vytvořit instanci služby Migrace databáze Azure.

5. Vyberte existující virtuální síť nebo vytvořte novou.

    Virtuální síť poskytuje službě Migrace databáze Azure přístup ke zdrojové instanci MySQL a cílové instanci Azure Database for MySQL.

    Další informace o tom, jak vytvořit virtuální síť na webu Azure Portal, najdete v článku [Vytvoření virtuální sítě pomocí portálu Azure](https://aka.ms/DMSVnet).

6. Vyberte cenovou úroveň; Pro tuto online migraci nezapomeňte vybrat cenovou úroveň Premium: 4vCores.

    ![Konfigurace nastavení instance služby Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-settings3.png)

7. Vyberte **Vytvořit** a vytvořte službu.

## <a name="create-a-migration-project"></a>Vytvoření projektu migrace

Po vytvoření služby ji vyhledejte na webu Azure Portal, otevřete ji a pak vytvořte nový projekt migrace.

1. Na webu Azure Portal vyberte **Všechny služby**, vyhledejte „Azure Database Migration Service“ a pak vyberte **Služby Azure Database Migration Service**.

      ![Vyhledání všech instancí služby Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-search.png)

2. Na obrazovce **Služby Azure Database Migration Service** vyhledejte název instance služby Azure Database Migration Service, kterou jste vytvořili, a vyberte ji.

     ![Vyhledání instance služby Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-instance-search.png)

3. Vyberte **+ Nový projekt migrace**.
4. Na obrazovce **Nový projekt migrace** zadejte název projektu, v textovém poli Typ **zdrojového serveru** vyberte **MySQL**a potom v textovém poli **Typ cílového serveru** vyberte **AzureDbForMySQL**.
5. V části **Zvolit typ aktivity** vyberte **Online migraci dat**.

    > [!IMPORTANT]
    > Nezapomeňte vybrat **online migraci dat**. offline migrace nejsou pro tento scénář podporovány.

    ![Vytvoření projektu Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create-project6.png)

    > [!NOTE]
    > Alternativně můžete zvolit **Vytvořit projekt pouze** k vytvoření projektu migrace nyní a provést migraci později.

6. Vyberte **Uložit**.

7. Vyberte **Vytvořit a spustit aktivitu** a vytvořte projekt a spusťte aktivitu migrace.

    > [!NOTE]
    > Poznamenejte si prosím předpoklady potřebné k nastavení migrace online v okně pro vytváření projektu.

## <a name="specify-source-details"></a>Zadání podrobností o zdroji

* Na obrazovce **podrobností zdroje migrace** zadejte podrobnosti o připojení pro zdrojovou instanci MySQL.

   ![Podrobnosti zdroje](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-source-details5.png)

## <a name="specify-target-details"></a>Zadání podrobností o cíli

1. Vyberte **Uložit**a potom na obrazovce **Podrobnosti cíl,** zadejte podrobnosti o připojení pro cílovou databázi Azure pro server MySQL, která je předem zřízená a má schéma **Zaměstnanci** nasazené pomocí MySQLDump.

    ![Výběr cíle](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-select-target5.png)

2. Vyberte **Uložit** a pak na obrazovce **Mapovat na cílové databáze** namapujte zdrojovou a cílovou databázi pro migraci.

    Pokud cílová databáze obsahuje stejný název databáze jako zdrojová databáze, služba Migrace databáze Azure vybere cílovou databázi ve výchozím nastavení.

    ![Mapování na cílové databáze](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-map-targets-activity5.png)

3. Vyberte **Uložit**, na obrazovce **Shrnutí migrace** do textového pole **Název aktivity** zadejte název aktivity migrace a pak zkontrolujte souhrnné informace a ujistěte se, že podrobnosti zdroje a cíle odpovídají dříve zadaným informacím.

    ![Shrnutí migrace](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-migration-summary2.png)

## <a name="run-the-migration"></a>Spuštění migrace

* Vyberte **Spustit migraci**.

    Zobrazí se okno aktivity migrace a **Stav** aktivity bude **Inicializace**.

## <a name="monitor-the-migration"></a>Monitorování migrace

1. Na obrazovce aktivity migrace vyberte **Aktualizovat** a aktualizujte zobrazení, dokud se **Stav** migrace nezmění na **Spuštěno**.

    ![Stav aktivity - spuštěno](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-activity-status4.png)

2. V části **NÁZEV DATABÁZE**vyberte konkrétní databázi, abyste se dostali ke stavu migrace pro **operace úplného načtení dat** a **přírůstkové synchronizace dat.**

    **Úplné zatížení dat** zobrazuje stav počáteční migrace zatížení, zatímco **přírůstková synchronizace dat** zobrazuje stav sběru dat (CDC).

    ![Obrazovka zásob - úplné načtení dat](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-full-load.png)

    ![Obrazovka Zásob – přírůstková synchronizace dat](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-incremental.png)

## <a name="perform-migration-cutover"></a>Provedení přímé migrace

Po dokončení počátečního úplného načtení jsou databáze označeny **jako Připraveno k ujetí.**

1. Jakmile budete připraveni dokončit migraci databází, vyberte **Spustit přímou migraci**.

    ![Začněte řezat znovu](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-start-cutover.png)

2. Ujistěte se, že jste zastavili všechny příchozí transakce do zdrojové databáze a počkejte, dokud se v čítači **Probíhající změny** nezobrazí **0**.
3. Vyberte **Potvrdit** a pak **Použít**.
4. Když se zobrazí stav migrace databáze **Dokončeno**, připojte aplikace k nové cílové databázi Azure pro mySQL.

Vaše online migrace místní instance MySQL do Azure Database for MySQL je teď dokončena.

## <a name="next-steps"></a>Další kroky

* Informace o službě Azure Database Migration Service najdete v článku [Co je Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
* Informace o Azure Database for MySQL najdete v článku [Co je databáze Azure pro MySQL?](https://docs.microsoft.com/azure/mysql/overview).
* Další otázky se můžete zeptat na alias [Migrace databáze Azure.](mailto:AskAzureDatabaseMigrations@service.microsoft.com)
