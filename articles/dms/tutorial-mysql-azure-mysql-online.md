---
title: 'Kurz: migrace MySQL online na Azure Database for MySQL'
titleSuffix: Azure Database Migration Service
description: Naučte se provádět online migraci z místního MySQL do Azure Database for MySQL pomocí Azure Database Migration Service.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 01/08/2020
ms.openlocfilehash: 754d8cc9e79bc100e87f56c6fc33102963e53e8d
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817953"
---
# <a name="tutorial-migrate-mysql-to-azure-database-for-mysql-online-using-dms"></a>Kurz: Online migrace MySQL do služby Azure Database for MySQL pomocí DMS

Pomocí Azure Database Migration Service můžete migrovat databáze z místní instance MySQL do [Azure Database for MySQL](../mysql/index.yml) s minimálními výpadky. Jinými slovy, můžete dosáhnout migrace s minimálními výpadky aplikace. V tomto kurzu migrujete ukázkovou databázi **Employees** z místní instance MySQL 5,7 na Azure Database for MySQL pomocí online aktivity migrace v Azure Database Migration Service.

> [!IMPORTANT]
> Scénář online migrace MySQL to Azure Database for MySQL je nahrazen paralelně, vysoce vykonávajícím scénářem offline migrace od 1. června 2021. Pro online migrace můžete tuto novou nabídku použít spolu s [replikací dat](https://docs.microsoft.com/azure/mysql/concepts-data-in-replication). Alternativně můžete použít Open Source nástroje, jako je [MyDumper/MyLoader](https://centminmod.com/mydumper.html) , při replikaci dat pro online migrace. 

V tomto kurzu se naučíte:
> [!div class="checklist"]
>
> * Migrace ukázkového schématu pomocí nástroje mysqldump
> * Vytvořte instanci služby Azure Database Migration Service.
> * Vytvořte projekt migrace pomocí Azure Database Migration Service.
> * Spuštění migrace
> * Monitorujte migraci.

> [!NOTE]
> Použití Azure Database Migration Service k provedení online migrace vyžaduje vytvoření instance založené na cenové úrovni Premium.

> [!IMPORTANT]
> Pro optimální prostředí migrace doporučuje Microsoft vytvořit instanci Azure Database Migration Service ve stejné oblasti Azure jako cílová databáze. Přenášení dat mezi oblastmi geografickými lokalitami může zpomalit proces migrace a způsobit chyby.

> [!NOTE]
> Komunikace bez posunu
>
> Microsoft podporuje různé a zahrnuté prostředí. Tento článek obsahuje odkazy na _podřízený_ text. [Průvodce stylem Microsoft pro komunikaci bez předplatných](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) se tímto způsobem rozpoznává jako vyloučené slovo. Toto slovo se v tomto článku používá kvůli konzistenci, protože je aktuálně slovo, které se zobrazuje v softwaru. Když se software aktualizuje, aby se odebralo slovo, aktualizuje se tento článek na zarovnání.
>


## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu je potřeba provést následující:

* Stáhněte a nainstalujte [MySQL Community Edition](https://dev.mysql.com/downloads/mysql/) verze 5.6 nebo 5.7. Verze místního MySQL musí odpovídat verzi služby Azure Database for MySQL. Například MySQL verze 5.6 je možné migrovat pouze do služby Azure Database for MySQL verze 5.6, u které neproběhl upgrade na verzi 5.7. Migrace do MySQL 8,0 se nepodporují.
* [Vytvořte instanci ve službě Azure Database for MySQL](../mysql/quickstart-create-mysql-server-database-using-azure-portal.md). Další informace o tom, jak připojit a vytvořit databázi pomocí aplikace Workbench, najdete v článku [použití MySQL Workbench k připojení a dotazování dat](../mysql/connect-workbench.md) .  
* Vytvořte Microsoft Azure Virtual Network pro Azure Database Migration Service pomocí modelu nasazení Azure Resource Manager, který zajišťuje připojení typu Site-to-site k místním zdrojovým serverům pomocí [ExpressRoute](../expressroute/expressroute-introduction.md) nebo [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Další informace o vytváření virtuálních sítí najdete v [dokumentaci k Virtual Network](../virtual-network/index.yml)a zejména v článcích rychlý Start s podrobnými údaji.

    > [!NOTE]
    > Pokud při instalaci služby Virtual networkNet používáte ExpressRoute s partnerským vztahem k síti Microsoftu, přidejte do podsítě, ve které se služba zřídí, následující [koncové body](../virtual-network/virtual-network-service-endpoints-overview.md) služby:
    >
    > * Koncový bod cílové databáze (například koncový bod SQL, Cosmos DB koncový bod atd.)
    > * Koncový bod úložiště
    > * Koncový bod služby Service Bus
    >
    > Tato konfigurace je nezbytná, protože Azure Database Migration Service nemá připojení k Internetu.

* Zajistěte, aby pravidla skupiny zabezpečení sítě virtuálních sítí neblokovala odchozí port 443 ServiceTag pro ServiceBus, Storage a AzureMonitor. Další podrobnosti o filtrování provozu NSG virtuální sítě najdete v článku [filtrování provozu sítě pomocí skupin zabezpečení sítě](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Nakonfigurujte bránu [Windows Firewall pro přístup k databázovému stroji](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Otevřete bránu Windows Firewall, abyste povolili Azure Database Migration Service přístup ke zdrojovému serveru MySQL, který je ve výchozím nastavení port TCP 3306.
* Pokud používáte zařízení brány firewall před zdrojovými databázemi, budete možná muset přidat pravidla firewallu, která Azure Database Migration Service umožní přístup ke zdrojovým databázím pro migraci.
* Vytvořte [pravidlo brány firewall](../azure-sql/database/firewall-configure.md) na úrovni serveru pro Azure Database for MySQL, které povolí Azure Database Migration Service přístup k cílovým databázím. Zadejte rozsah podsítě virtuální sítě, která se používá pro Azure Database Migration Service.
* Zdrojová instance MySQL musí být na podporované edici MySQL Community Edition. Pokud chcete určit verzi instance MySQL, v nástroji MySQL nebo aplikaci MySQL Workbench spusťte následující příkaz:

    ```
    SELECT @@version;
    ```

* Azure Database for MySQL podporuje pouze tabulky InnoDB. Informace o převodu tabulek MyISAM na tabulky InnoDB najdete v článku [Převod tabulek z MyISAM na InnoDB](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html).

* V souboru my.ini (Windows) nebo my.cnf (Unix) ve zdrojové databázi povolte binární protokolování pomocí následující konfigurace:

  * **server_id** = 1 nebo větší (relevantní jenom pro MySQL 5.6)
  * **protokolovací přihrádka** = \<path> (relevantní pouze pro MySQL 5,6)    Příklad: log-bin = jednotku e:\ MySQL_logs \BinLog
  * **binlog_format** = row
  * **Expire_logs_days** = 5 (nedoporučuje se používat nula, relevantní pouze pro MySQL 5,6)
  * **Binlog_row_image** = full (relevantní jenom pro MySQL 5.6)
  * **log_slave_updates** = 1

* Uživatel musí mít roli ReplicationAdmin s následujícími oprávněními:

  * **KLIENT REPLIKACE** – Vyžaduje se pouze u úloh zpracování změn. Jinými slovy, úlohy pouze s úplným načtením toto oprávnění nevyžadují.
  * **REPLIKA REPLIKACE** – Vyžaduje se pouze u úloh zpracování změn. Jinými slovy, úlohy pouze s úplným načtením toto oprávnění nevyžadují.
  * **SUPER** – Vyžaduje se pouze ve verzích MySQL starších než 5.6.6.

## <a name="migrate-the-sample-schema"></a>Migrace ukázkového schématu

K dokončení všech databázových objektů, jako jsou schémata tabulek, indexy a uložené procedury, potřebujeme extrahovat schéma ze zdrojové databáze a použít ho na databázi. K extrahování schématu můžete použít nástroj mysqldump s parametrem `--no-data`.

Za předpokladu, že máte ukázkovou databázi MySQL **Employees** v místním systému, příkaz k migraci schématu pomocí mysqldump je:

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

Pokud vaše schéma obsahuje cizí klíče, počáteční načtení a průběžná synchronizace migrace selžou.  Spusťte následující skript v aplikaci MySQL Workbench pro extrakci skriptu cizího klíče a přidejte do něj skript cizího klíče.

```sql
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

> [!NOTE]
> Azure DMS nepodporuje referenční akci CASCADE, která pomáhá automaticky odstranit nebo aktualizovat shodný řádek v podřízené tabulce při odstranění nebo aktualizaci řádku v nadřazené tabulce. Další informace najdete v části referenční akce článku [omezení cizího klíče](https://dev.mysql.com/doc/refman/8.0/en/create-table-foreign-keys.html)v dokumentaci k MySQL.
> Azure DMS vyžaduje, abyste při počátečním načtení dat vyřadíte omezení cizího klíče v cílovém databázovém serveru a nemůžete použít referenční akce. Pokud vaše zatížení závisí na aktualizaci související podřízené tabulky prostřednictvím této referenční akce, doporučujeme místo toho provést [Výpis a obnovení](../mysql/concepts-migrate-dump-restore.md) . 


> [!IMPORTANT]
> Pokud importujete data pomocí zálohy, odeberte příkazy CREATE DEFINer ručně nebo pomocí příkazu--Skip-define při provádění mysqldump. Aby bylo možné vytvořit a omezit na Azure Database for MySQL, musí mít definováno oprávnění super.

Pokud máte v databázi triggery, vynutila integritu dat v cíli před úplnou migrací dat ze zdroje. Doporučením je zakázat aktivační události ve všech tabulkách v cíli během migrace a poté povolit triggery po dokončení migrace.

Spusťte následující skript v aplikaci MySQL Workbench v cílové databázi pro extrakci skriptu pro odkládací událost a přidání skriptu triggeru.

```sql
SELECT
    SchemaName,
    GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery,
    Concat('DELIMITER $$ \n\n', GROUP_CONCAT(AddQuery SEPARATOR '$$\n'), '$$\n\nDELIMITER ;') as AddQuery
FROM
(
SELECT 
    TRIGGER_SCHEMA as SchemaName,
    Concat('DROP TRIGGER `', TRIGGER_NAME, "`") as DropQuery,
    Concat('CREATE TRIGGER `', TRIGGER_NAME, '` ', ACTION_TIMING, ' ', EVENT_MANIPULATION, 
            '\nON `', EVENT_OBJECT_TABLE, '`\n' , 'FOR EACH ', ACTION_ORIENTATION, ' ',
            ACTION_STATEMENT) as AddQuery
FROM  
    INFORMATION_SCHEMA.TRIGGERS
ORDER BY EVENT_OBJECT_SCHEMA, EVENT_OBJECT_TABLE, ACTION_TIMING, EVENT_MANIPULATION, ACTION_ORDER ASC
) AS Queries
GROUP BY SchemaName
```

V cílové databázi spusťte dotaz vygenerované aktivační události vyřazení (sloupec DropQuery) ve výsledku pro vyřazení aktivačních událostí. Dotaz přidat aktivační událost lze uložit, aby bylo možné použít dokončení migrace dat.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrace poskytovatele prostředků Microsoft.DataMigration

1. Přihlaste se k webu Azure Portal, vyberte **Všechny služby** a pak vyberte **Předplatná**.

   ![Zobrazení předplatných na portálu](media/tutorial-mysql-to-azure-mysql-online/01-portal-select-subscriptions.png)

2. Vyberte předplatné, ve kterém chcete vytvořit instanci Azure Database Migration Service a pak vyberte **poskytovatelé prostředků**.

    ![Zobrazení poskytovatelů prostředků](media/tutorial-mysql-to-azure-mysql-online/02-01-portal-select-resource-provider.png)

3. Vyhledejte „migration“ a pak napravo od **Microsoft.DataMigration** vyberte **Zaregistrovat**.

    ![Registrace poskytovatele prostředků](media/tutorial-mysql-to-azure-mysql-online/02-02-portal-register-resource-provider.png)

## <a name="create-a-database-migration-service-instance"></a>Vytvoření instance Database Migration Service

1. Na webu Azure Portal vyberte **+ Vytvořit prostředek**, vyhledejte „Azure Database Migration Service“ a pak v rozevíracím seznamu vyberte **Azure Database Migration Service**.

    ![Azure Marketplace](media/tutorial-mysql-to-azure-mysql-online/03-dms-portal-marketplace.png)

2. Na obrazovce **Azure Database Migration Service** vyberte **Vytvořit**.

    ![Vytvoření instance služby Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-online/04-dms-portal-marketplace-create.png)
  
3. Na obrazovce **Vytvořit službu Migration Service** zadejte název služby, předplatné a novou nebo existující skupinu prostředků.

4. Vyberte cenovou úroveň a přejděte na obrazovku sítě. Možnost offline migrace je dostupná jak v cenové úrovni úrovně Standard, tak i Premium.

    Další informace o nákladech a cenových úrovních najdete na [stránce s cenami](https://aka.ms/dms-pricing).

    ![Konfigurace nastavení Azure Database Migration Service Basic](media/tutorial-mysql-to-azure-mysql-online/05-dms-portal-create-basic.png)

5. Vyberte ze seznamu existující virtuální síť nebo zadejte název nové virtuální sítě, která se má vytvořit. Přejděte na obrazovku revize + vytvořit. Volitelně můžete přidat značky ke službě pomocí obrazovky značky.

    Virtuální síť poskytuje Azure Database Migration Service s přístupem ke zdrojovému SQL Server a cílové instanci Azure SQL Database.

    ![Konfigurace nastavení Azure Database Migration Service sítě](media/tutorial-mysql-to-azure-mysql-online/06-dms-portal-create-networking.png)

    Další informace o tom, jak vytvořit virtuální síť v Azure Portal, najdete v článku [vytvoření virtuální sítě pomocí Azure Portal](../virtual-network/quick-create-portal.md).

6. Zkontrolujte konfigurace a vyberte **vytvořit** a vytvořte službu.
    
    ![Vytvoření Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-online/07-dms-portal-create-submit.png)

## <a name="create-a-migration-project"></a>Vytvoření projektu migrace

Po vytvoření služby ji vyhledejte na webu Azure Portal, otevřete ji a pak vytvořte nový projekt migrace.  

1. Na webu Azure Portal vyberte **Všechny služby**, vyhledejte „Azure Database Migration Service“ a pak vyberte **Služby Azure Database Migration Service**.

    ![Vyhledat všechny instance Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-online/08-01-dms-portal-search-service.png)

2. Z výsledků hledání vyberte instanci migrační služby a vyberte + **Nový projekt migrace**.
    
    ![Vytvoření nového projektu migrace](media/tutorial-mysql-to-azure-mysql-online/08-02-dms-portal-new-project.png)

3. Na obrazovce **Nový projekt migrace** zadejte název projektu, v poli Výběr **typu zdrojového serveru** vyberte **MySQL**, v poli Výběr **typu cílového serveru** vyberte položku **Azure Database for MySQL** a v poli Výběr **typu aktivity migrace** vyberte možnost **migrace online dat**. Vyberte **Vytvořit a spustit aktivitu**.

    ![Vytvoření projektu Database Migration Service](media/tutorial-mysql-to-azure-mysql-online/09-dms-portal-project-mysql-create.png)

    > [!NOTE]
    > Alternativně můžete zvolit **vytvořit projekt pouze** k vytvoření projektu migrace a provést migraci později.

## <a name="configure-migration-project"></a>Konfigurace projektu migrace

1. Na obrazovce **Vybrat zdroj** zadejte podrobnosti připojení zdrojové instance MySQL a vyberte **Další: vybrat cílovou>>**

    ![Přidat obrazovku podrobností zdroje](media/tutorial-mysql-to-azure-mysql-online/10-dms-portal-project-mysql-source.png)

2. Na obrazovce **vybrat cíl** zadejte podrobnosti o připojení pro cílovou instanci Azure Database for MySQL a vyberte **Další: Vyberte databáze>>**

    ![Přidat obrazovku s podrobnostmi o cíli](media/tutorial-mysql-to-azure-mysql-online/11-dms-portal-project-mysql-target.png)

3. Na obrazovce **Vybrat databáze** namapujte zdrojovou a cílovou databázi pro migraci a vyberte **Další: konfigurace nastavení migrace>>**. Pokud chcete zdroj nastavit jako jen pro čtení, můžete vybrat možnost **vytvořit zdrojový server** jen pro čtení, ale buďte opatrní, protože se jedná o nastavení na úrovni serveru. Pokud je tato možnost vybrána, nastaví celý server jen pro čtení, nikoli jenom pro vybrané databáze.
    
    Pokud cílová databáze obsahuje stejný název databáze jako zdrojová databáze, Azure Database Migration Service ve výchozím nastavení vybere cílovou databázi.
    ![Obrazovka s podrobnostmi o databázi](media/tutorial-mysql-to-azure-mysql-online/12-dms-portal-project-mysql-select-db.png)
    
    > [!NOTE] 
   > I když v tomto kroku můžete vybrat více databází, každá instance Azure Database Migration Service podporuje až 4 databáze pro souběžnou migraci. Také platí omezení 10 instancí Azure Database Migration Service pro každé předplatné na oblast. Například pokud máte databáze 80, můžete je 40 migrovat do stejné oblasti souběžně, ale pouze v případě, že jste vytvořili 10 instancí Azure Database Migration Service.

4. Na obrazovce **Konfigurovat nastavení migrace** vyberte tabulky, které mají být součástí migrace, a vyberte **další: Souhrn>>**. Pokud mají cílové tabulky nějaká data, ve výchozím nastavení nejsou vybrané, ale můžete je explicitně vybrat a před zahájením migrace se zkrátí.

    ![Obrazovka vybrat tabulky](media/tutorial-mysql-to-azure-mysql-online/13-dms-portal-project-mysql-select-tbl.png)

5. Na obrazovce **Souhrn** zadejte do textového pole **název aktivity** název aktivity migrace a zkontrolujte souhrn, abyste se ujistili, že se podrobnosti o zdroji a cíli shodují s tím, co jste předtím zadali.

    ![Shrnutí projektu migrace](media/tutorial-mysql-to-azure-mysql-online/14-dms-portal-project-mysql-activity-summary.png)

6. Vyberte **Spustit migraci**. Zobrazí se okno aktivity migrace a **Stav** aktivity bude **Inicializace**. Když se spustí migrace tabulky, **stav** se změní na **spuštěno** .

## <a name="monitor-the-migration"></a>Monitorování migrace

1. Na obrazovce aktivity migrace vyberte **Aktualizovat** a aktualizujte zobrazení, dokud se **Stav** migrace nezmění na **Dokončeno**.

     ![Stav aktivity – Dokončeno](media/tutorial-mysql-to-azure-mysql-online/15-dms-activity-completed.png)

2. Výběrem konkrétní databáze v části **Název databáze** zobrazíte stav migrace pro operace **Úplné načtení dat** a **Přírůstková synchronizace dat**.

    Úplné načtení dat zobrazí stav migrace z hlediska počátečního načtení, zatímco přírůstková synchronizace dat zobrazí stav funkce Change Data Capture (CDC).

     ![Stav aktivity – Úplné načtení dokončeno](media/tutorial-mysql-to-azure-mysql-online/16-dms-activity-full-load-completed.png)

     ![Stav aktivity – Přírůstková synchronizace dat](media/tutorial-mysql-to-azure-mysql-online/17-dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>Provedení přímé migrace

Po dokončení počátečního úplného načtení se databáze označí jako **Připraveno k přímé migraci**.

1. Jakmile budete připraveni dokončit migraci databází, vyberte **Spustit přímou migraci**.

    ![Spuštění přímé migrace](media/tutorial-mysql-to-azure-mysql-online/18-dms-start-cutover.png)

2. Ujistěte se, že jste zastavili všechny příchozí transakce do zdrojové databáze a počkejte, dokud se v čítači **Probíhající změny** nezobrazí **0**.
3. Vyberte **Potvrdit** a pak **Použít**.
4. Když se stav migrace databází změní na **Dokončeno**, připojte své aplikace k nové cílové službě Azure SQL Database.

## <a name="next-steps"></a>Další kroky

* Informace o známých problémech a omezeních při provádění online migrací do služby Azure Database for MySQL najdete v článku [Známé problémy s online migracemi do služby Azure Database for MySQL a jejich řešení](known-issues-azure-mysql-online.md).
* Informace o Azure Database Migration Service najdete v článku [co je Azure Database Migration Service?](./dms-overview.md).
* Informace o Azure Database for MySQL najdete v článku [co je Azure Database for MySQL?](../mysql/overview.md).
