---
title: 'Kurz: Migrace MySQL online do databáze Azure pro MySQL'
titleSuffix: Azure Database Migration Service
description: Naučte se provádět online migraci z mySQL místně do databáze Azure pro MySQL pomocí služby Migrace databáze Azure.
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
ms.openlocfilehash: 7c8087a01bb71657e816be89b6a562dd4783b271
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240737"
---
# <a name="tutorial-migrate-mysql-to-azure-database-for-mysql-online-using-dms"></a>Kurz: Online migrace MySQL do služby Azure Database for MySQL pomocí DMS

Službu migrace databáze Azure můžete použít k migraci databází z místní instance MySQL do [Databáze Azure pro MySQL](https://docs.microsoft.com/azure/mysql/) s minimálními prostoji. Jinými slovy, můžete dosáhnout migrace s minimálními výpadky aplikace. V tomto kurzu migrujete ukázkovou databázi **zaměstnanců** z místní instance MySQL 5.7 do databáze Azure pro MySQL pomocí aktivity migrace online ve službě Migrace databáze Azure.

V tomto kurzu se naučíte:
> [!div class="checklist"]
>
> * Migrace ukázkového schématu pomocí nástroje mysqldump
> * Vytvořte instanci služby Azure Database Migration Service.
> * Vytvořte projekt migrace pomocí služby Migrace databáze Azure.
> * Spuštění migrace
> * Monitorování migrace

> [!NOTE]
> Použití služby Migrace databáze Azure k provedení migrace online vyžaduje vytvoření instance založené na cenové úrovni Premium.

> [!IMPORTANT]
> Pro optimální prostředí migrace Microsoft doporučuje vytvořit instanci služby Migrace databáze Azure ve stejné oblasti Azure jako cílová databáze. Přenášení dat mezi oblastmi geografickými lokalitami může zpomalit proces migrace a způsobit chyby.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu je potřeba provést následující:

* Stáhněte a nainstalujte [MySQL Community Edition](https://dev.mysql.com/downloads/mysql/) verze 5.6 nebo 5.7. Verze místního MySQL musí odpovídat verzi služby Azure Database for MySQL. Například MySQL verze 5.6 je možné migrovat pouze do služby Azure Database for MySQL verze 5.6, u které neproběhl upgrade na verzi 5.7.
* [Vytvořte instanci ve službě Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/quickstart-create-mysql-server-database-using-azure-portal). Podrobnosti o připojení a vytvoření databáze pomocí webu Azure Portal najdete v článku [Připojení a dotazování dat pomocí aplikace MySQL Workbench](https://docs.microsoft.com/azure/mysql/connect-workbench).  
* Vytvořte službu Migrace databáze Microsoft Azure pro Azure pomocí modelu nasazení Azure Resource Manageru, který poskytuje připojení k místním zdrojovým serverům site-to-site pomocí [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Další informace o vytvoření virtuální sítě naleznete v [dokumentaci k virtuální síti](https://docs.microsoft.com/azure/virtual-network/)a zejména v článcích rychlého startu s podrobnými podrobnostmi.

    > [!NOTE]
    > Pokud během instalace virtuální sítě Síť používáte ExpressRoute s partnerským vztahem k síti společnosti Microsoft, přidejte do podsítě, ve které bude služba zřízena, následující [koncové body služby:](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)
    >
    > * Cílový koncový bod databáze (například koncový bod SQL, koncový bod Cosmos DB a tak dále)
    > * Koncový bod úložiště
    > * Koncový bod sběrnice
    >
    > Tato konfigurace je nezbytná, protože služba Azure Database Migration Service nemá připojení k internetu.

* Ujistěte se, že pravidla skupiny zabezpečení sítě virtuální sítě neblokují následující příchozí komunikační porty služby Azure Database Migration Service: 443, 53, 9354, 445, 12000. Další podrobnosti o filtrování přenosů skupin nsg ve virtuální síti naleznete v článku [Filtrování síťového provozu se skupinami zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm).
* Nakonfigurujte bránu [Windows Firewall pro přístup k databázovému stroji](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Otevřete bránu firewall systému Windows, abyste povolili službě Migrace databáze Azure přístup ke zdroji MySQL Server, který je ve výchozím nastavení tcp port 3306.
* Při použití zařízení brány firewall před zdrojovou databází, budete muset přidat pravidla brány firewall povolit Azure Database Migration Service pro přístup ke zdrojové databáze pro migraci.
* Vytvořte pravidlo [brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) na úrovni serveru pro Azure Database for MySQL, které umožní přístup služby Azure Database Migration Service k cílovým databázím. Zadejte rozsah podsítí virtuální sítě používané pro službu Migrace databáze Azure.
* Zdrojová instance MySQL musí být na podporované edici MySQL Community Edition. Pokud chcete určit verzi instance MySQL, v nástroji MySQL nebo aplikaci MySQL Workbench spusťte následující příkaz:

    ```
    SELECT @@version;
    ```

* Azure Database for MySQL podporuje pouze tabulky InnoDB. Informace o převodu tabulek MyISAM na tabulky InnoDB najdete v článku [Převod tabulek z MyISAM na InnoDB](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html).

* V souboru my.ini (Windows) nebo my.cnf (Unix) ve zdrojové databázi povolte binární protokolování pomocí následující konfigurace:

  * **server_id** = 1 nebo větší (relevantní jenom pro MySQL 5.6)
  * **cesta k přihrádce** = \<protokolu> (relevantní pouze pro MySQL 5.6) Například: log-bin = E:\MySQL_logs\BinLog
  * **binlog_format** = row
  * **Expire_logs_days** = 5 (doporučuje se nepoužívat nulu; relevantní pouze pro MySQL 5.6)
  * **Binlog_row_image** = full (relevantní jenom pro MySQL 5.6)
  * **log_slave_updates** = 1

* Uživatel musí mít roli ReplicationAdmin s následujícími oprávněními:

  * **KLIENT REPLIKACE** – Vyžaduje se pouze u úloh zpracování změn. Jinými slovy, úlohy pouze s úplným načtením toto oprávnění nevyžadují.
  * **REPLIKA REPLIKACE** – Vyžaduje se pouze u úloh zpracování změn. Jinými slovy, úlohy pouze s úplným načtením toto oprávnění nevyžadují.
  * **SUPER** – Vyžaduje se pouze ve verzích MySQL starších než 5.6.6.

## <a name="migrate-the-sample-schema"></a>Migrace ukázkového schématu

K dokončení všech databázových objektů, jako jsou schémata tabulek, indexy a uložené procedury, potřebujeme extrahovat schéma ze zdrojové databáze a použít ho na databázi. K extrahování schématu můžete použít nástroj mysqldump s parametrem `--no-data`.

Za předpokladu, že máte MySQL **Zaměstnanci** ukázkové databáze v místním systému, příkaz k migraci schématu pomocí mysqldump je:

```
mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
```

Například:

```
mysqldump -h 10.10.123.123 -u root -p --databases employees --no-data > d:\employees.sql
```

Pokud chcete importovat schéma do cílové služby Azure Database for MySQL, spusťte následující příkaz:

```
mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
 ```

Například:

```
mysql.exe -h shausample.mysql.database.azure.com -u dms@shausample -p employees < d:\employees.sql
 ```

Pokud vaše schéma obsahuje cizí klíče, počáteční načtení a průběžná synchronizace migrace selžou.  Spusťte následující skript v MySQL Workbench extrahovat drop cizí klíč skript a přidat cizí klíč skript.

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

Spuštěním skriptu pro odstranění cizího klíče (druhý sloupec) ve výsledku dotazu odstraňte cizí klíč.

> [!IMPORTANT]
> Pokud importdat pomocí zálohy, odeberte příkazy CREATE DEFINER ručně nebo pomocí příkazu --skip-definer při provádění mysqldump. DEFINER vyžaduje super oprávnění k vytvoření a je omezena v Azure Database pro MySQL.

Pokud máte aktivační událost v datech (vložit nebo aktualizovat aktivační událost), bude vynutit integritu dat v cíli před replikovaných dat ze zdroje. Během migrace se doporučuje zakázat triggery ve všech tabulkách v cíli a povolit je až po dokončení migrace.

Chcete-li zakázat aktivační události v cílové databázi, použijte následující příkaz:

```
SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrace poskytovatele prostředků Microsoft.DataMigration

1. Přihlaste se k webu Azure Portal, vyberte **Všechny služby** a pak vyberte **Předplatná**.

   ![Zobrazení předplatných na portálu](media/tutorial-mysql-to-azure-mysql-online/portal-select-subscriptions.png)

2. Vyberte předplatné, ve kterém chcete vytvořit instanci služby Migrace databáze Azure, a pak vyberte **zprostředkovatele prostředků**.

    ![Zobrazení poskytovatelů prostředků](media/tutorial-mysql-to-azure-mysql-online/portal-select-resource-provider.png)

3. Vyhledejte „migration“ a pak napravo od **Microsoft.DataMigration** vyberte **Zaregistrovat**.

    ![Registrace poskytovatele prostředků](media/tutorial-mysql-to-azure-mysql-online/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Vytvoření instance DMS

1. Na webu Azure Portal vyberte **+ Vytvořit prostředek**, vyhledejte „Azure Database Migration Service“ a pak v rozevíracím seznamu vyberte **Azure Database Migration Service**.

    ![Azure Marketplace](media/tutorial-mysql-to-azure-mysql-online/portal-marketplace.png)

2. Na obrazovce **Azure Database Migration Service** vyberte **Vytvořit**.

    ![Vytvoření instance služby Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-online/dms-create1.png)
  
3. Na obrazovce **Vytvořit službu Migration Service** zadejte název služby, předplatné a novou nebo existující skupinu prostředků.

4. Vyberte existující virtuální síť nebo vytvořte novou.

    Virtuální síť poskytuje službě Migrace databáze Azure přístup ke zdrojovému serveru SQL Server a cílové instanci Azure SQL Database.

    Další informace o tom, jak vytvořit virtuální síť na webu Azure Portal, najdete v článku [Vytvoření virtuální sítě pomocí portálu Azure](https://aka.ms/DMSVnet).

5. Vyberte cenovou úroveň.

    Další informace o nákladech a cenových úrovních najdete na [stránce s cenami](https://aka.ms/dms-pricing).

    ![Konfigurace nastavení instance služby Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-online/dms-settings3.png)

6. Vyberte **Vytvořit** a vytvořte službu.

## <a name="create-a-migration-project"></a>Vytvoření projektu migrace

Po vytvoření služby ji vyhledejte na webu Azure Portal, otevřete ji a pak vytvořte nový projekt migrace.

1. Na webu Azure Portal vyberte **Všechny služby**, vyhledejte „Azure Database Migration Service“ a pak vyberte **Služby Azure Database Migration Service**.

      ![Vyhledání všech instancí služby Migrace databáze Azure](media/tutorial-mysql-to-azure-mysql-online/dms-search.png)

2. Na obrazovce **Azure Database Migration Services** vyhledejte název instance služby Migrace databáze Azure, kterou jste vytvořili, a vyberte instanci.

     ![Vyhledání instance služby Migrace databáze Azure](media/tutorial-mysql-to-azure-mysql-online/dms-instance-search.png)

3. Vyberte **+ Nový projekt migrace**.
4. Na obrazovce **Nový projekt migrace** zadejte název projektu, v textovém poli **Typ zdrojového serveru** vyberte **MySQL** a v textovém poli **Typ cílového serveru** vyberte **AzureDbForMySQL**.
5. V části **Zvolte typ aktivity** vyberte **Online migrace dat**.

    ![Vytvoření projektu Database Migration Service](media/tutorial-mysql-to-azure-mysql-online/dms-create-project4.png)

    > [!NOTE]
    > Alternativně můžete zvolit **Vytvořit projekt pouze** k vytvoření projektu migrace nyní a provést migraci později.

6. Vyberte **Uložit**, přečtěte si požadavky na úspěšné použití DMS k migraci dat a pak vyberte **Vytvořit a spustit aktivitu**.

## <a name="specify-source-details"></a>Zadání podrobností o zdroji

1. Na obrazovce **Přidat podrobnosti zdroje** zadejte podrobnosti o připojení ke zdrojové instanci MySQL.

    ![Obrazovka Přidat podrobnosti zdroje](media/tutorial-mysql-to-azure-mysql-online/dms-add-source-details.png)

## <a name="specify-target-details"></a>Zadání podrobností o cíli

1. Vyberte **Uložit** a pak na obrazovce **Podrobnosti cíle** zadejte podrobnosti o připojení k cílovému serveru služby Azure Database for MySQL. Tím je předem zřízená instance služby Azure Database for MySQL, do které se pomocí nástroje mysqldump nasadilo schéma **Employees**.

    ![Obrazovka Podrobnosti cíle](media/tutorial-mysql-to-azure-mysql-online/dms-add-target-details.png)

2. Vyberte **Uložit** a pak na obrazovce **Mapovat na cílové databáze** namapujte zdrojovou a cílovou databázi pro migraci.

    Pokud cílová databáze obsahuje stejný název databáze jako zdrojová databáze, služba Migrace databáze Azure vybere cílovou databázi ve výchozím nastavení.

    ![Mapování na cílové databáze](media/tutorial-mysql-to-azure-mysql-online/dms-map-target-details.png)
   > [!NOTE] 
   > I když v tomto kroku můžete vybrat více databází, každá instance služby Migrace databáze Azure podporuje až čtyři databáze pro souběžnou migraci. Je tu také limit dvou instancí služby Migrace databáze Azure na oblast v předplatném. Například pokud máte 40 databází k migraci, můžete migrovat pouze osm z nich současně a pouze v případě, že jste vytvořili dvě instance služby Migrace databáze Azure.

3. Vyberte **Uložit**, na obrazovce **Shrnutí migrace** do textového pole **Název aktivity** zadejte název aktivity migrace a pak zkontrolujte souhrnné informace a ujistěte se, že podrobnosti zdroje a cíle odpovídají dříve zadaným informacím.

    ![Shrnutí migrace](media/tutorial-mysql-to-azure-mysql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>Spuštění migrace

* Vyberte **Spustit migraci**.

    Zobrazí se okno aktivity migrace a **inicializace** **stavu** aktivity .

## <a name="monitor-the-migration"></a>Monitorování migrace

1. Na obrazovce aktivity migrace vyberte **Aktualizovat** a aktualizujte zobrazení, dokud se **Stav** migrace nezmění na **Dokončeno**.

     ![Stav aktivity – Dokončeno](media/tutorial-mysql-to-azure-mysql-online/dms-activity-completed.png)

2. Výběrem konkrétní databáze v části **Název databáze** zobrazíte stav migrace pro operace **Úplné načtení dat** a **Přírůstková synchronizace dat**.

    Úplné načtení dat zobrazí stav migrace z hlediska počátečního načtení, zatímco přírůstková synchronizace dat zobrazí stav funkce Change Data Capture (CDC).

     ![Stav aktivity – Úplné načtení dokončeno](media/tutorial-mysql-to-azure-mysql-online/dms-activity-full-load-completed.png)

     ![Stav aktivity – Přírůstková synchronizace dat](media/tutorial-mysql-to-azure-mysql-online/dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>Provedení přímé migrace

Po dokončení počátečního úplného načtení se databáze označí jako **Připraveno k přímé migraci**.

1. Jakmile budete připraveni dokončit migraci databází, vyberte **Spustit přímou migraci**.

    ![Spuštění přímé migrace](media/tutorial-mysql-to-azure-mysql-online/dms-start-cutover.png)

2. Ujistěte se, že jste zastavili všechny příchozí transakce do zdrojové databáze a počkejte, dokud se v čítači **Probíhající změny** nezobrazí **0**.
3. Vyberte **Potvrdit** a pak **Použít**.
4. Když se stav migrace databází změní na **Dokončeno**, připojte své aplikace k nové cílové službě Azure SQL Database.

## <a name="next-steps"></a>Další kroky

* Informace o známých problémech a omezeních při provádění online migrací do služby Azure Database for MySQL najdete v článku [Známé problémy s online migracemi do služby Azure Database for MySQL a jejich řešení](known-issues-azure-mysql-online.md).
* Informace o službě Migrace databáze Azure najdete v článku [Co je služba migrace databáze Azure?](https://docs.microsoft.com/azure/dms/dms-overview).
* Informace o Azure Database for MySQL najdete v článku [Co je databáze Azure pro MySQL?](https://docs.microsoft.com/azure/mysql/overview).
