---
title: 'Kurz: migrace RDS PostgreSQL online na Azure Database for PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Naučte se provádět online migraci z RDS PostgreSQL do Azure Database for PostgreSQL pomocí Azure Database Migration Service.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 04/11/2020
ms.openlocfilehash: 077bda7c254e00e919a22423038a051dc7eafe60
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "101094893"
---
# <a name="tutorial-migrate-rds-postgresql-to-azure-db-for-postgresql-online-using-dms"></a>Kurz: migrace programu RDS PostgreSQL do služby Azure DB pro PostgreSQL online pomocí DMS

Pomocí Azure Database Migration Service můžete migrovat databáze z instance služby RDS PostgreSQL do [Azure Database for PostgreSQL](../postgresql/index.yml) , zatímco zdrojová databáze zůstane během migrace online. Jinými slovy, migraci je možné dosáhnout s minimálními výpadky aplikace. V tomto kurzu migrujete ukázkovou databázi **pronájmu DVD** z instance služby RDS PostgreSQL 9,6 na Azure Database for PostgreSQL pomocí online aktivity migrace v Azure Database Migration Service.

V tomto kurzu se naučíte:
> [!div class="checklist"]
>
> * Pomocí nástroje pg_dump migrujte vzorové schéma.
> * Vytvořte instanci služby Azure Database Migration Service.
> * Vytvořte projekt migrace pomocí Azure Database Migration Service.
> * Spuštění migrace
> * Monitorujte migraci.
> * Proveďte přímou migraci migrace.

> [!NOTE]
> Použití Azure Database Migration Service k provedení online migrace vyžaduje vytvoření instance založené na cenové úrovni Premium. Další informace najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/database-migration/) Azure Database Migration Service. Šifrováním disku zabráníte krádeži dat během migrace.

> [!IMPORTANT]
> Aby migrace proběhla optimálním způsobem, doporučuje Microsoft vytvořit instanci služby Azure Database Migration Service ve stejné oblasti Azure, ve které je cílová databáze. Přenášení dat mezi oblastmi geografickými lokalitami může zpomalit proces migrace a způsobit chyby.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Tento článek popisuje, jak provést online migraci z místní instance PostgreSQL do Azure Database for PostgreSQL.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu je potřeba provést následující:

* Stáhněte a nainstalujte si [PostgreSQL community edition](https://www.postgresql.org/download/) 9,5, 9,6 nebo 10. Verze zdrojového PostgreSQL serveru musí být 9.5.11, 9.6.7, 10 nebo novější. Další informace najdete v článku [podporované verze databáze PostgreSQL](../postgresql/concepts-supported-versions.md).

   Všimněte si také, že cílová verze Azure Database for PostgreSQL musí být stejná nebo vyšší než verze PostgreSQL služby RDS. Například služba RDS PostgreSQL 9,6 se dá migrovat jenom na Azure Database for PostgreSQL 9,6, 10 nebo 11, ale ne na Azure Database for PostgreSQL 9,5.

* Vytvořte instanci [Azure Database for PostgreSQL](../postgresql/quickstart-create-server-database-portal.md) nebo [Azure Database for PostgreSQL-Citus (škálování](../postgresql/quickstart-create-hyperscale-portal.md)na více instancí). Podrobnosti o tom, jak se připojit k serveru PostgreSQL pomocí pgAdmin, najdete v této [části](../postgresql/quickstart-create-server-database-portal.md#connect-to-the-server-with-psql) dokumentu.
* Vytvořte Microsoft Azure Virtual Network pro Azure Database Migration Service pomocí modelu nasazení Azure Resource Manager, který umožňuje připojení typu Site-to-site k místním zdrojovým serverům pomocí [ExpressRoute](../expressroute/expressroute-introduction.md) nebo [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Další informace o vytváření virtuálních sítí najdete v [dokumentaci k Virtual Network](../virtual-network/index.yml)a zejména v článcích rychlý Start s podrobnými údaji.
* Zajistěte, aby pravidla skupiny zabezpečení sítě virtuálních sítí neblokovala odchozí port 443 ServiceTag pro ServiceBus, Storage a AzureMonitor. Další podrobnosti o filtrování provozu NSG virtuální sítě najdete v článku [filtrování provozu sítě pomocí skupin zabezpečení sítě](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Nakonfigurujte bránu [Windows Firewall pro přístup k databázovému stroji](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Otevřete bránu Windows Firewall, abyste povolili Azure Database Migration Service přístup ke zdrojovému serveru PostgreSQL, který je ve výchozím nastavení port TCP 5432.
* Pokud před zdrojovými databázemi používáte zařízení brány firewall, možná bude potřeba přidat pravidla brány firewall, která službě Azure Database Migration Service povolí přístup ke zdrojovým databázím za účelem migrace.
* Vytvořte [pravidlo brány firewall](https://docs.microsoft.com/azure/postgresql/concepts-firewall-rules) na úrovni serveru pro Azure Database for PostgreSQL Server, aby bylo možné Azure Database Migration Service přístup k cílovým databázím. Zadejte rozsah podsítě virtuální sítě, která se používá pro Azure Database Migration Service.

### <a name="set-up-aws-rds-postgresql-for-replication"></a>Nastavení AWS VP PostgreSQL pro replikaci

1. Pokud chcete vytvořit novou skupinu parametrů, postupujte podle pokynů uvedených v AWS v článku [práce se skupinami parametrů DB](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithParamGroups.html).
2. Pomocí hlavního uživatelského jména se připojte ke zdroji z Azure Database Migration Service. Pokud používáte jiný účet než hlavní uživatelský účet, musí mít účet roli rds_superuser a roli rds_replication. Role rds_replication uděluje oprávnění ke správě logických slotů a ke streamování dat pomocí logických slotů.
3. Vytvořte novou skupinu parametrů s následující konfigurací:

    a. Nastavte parametr rds.logical_replication ve skupině parametrů databáze na hodnotu 1.

    b. max_wal_senders = [počet souběžných úloh] – parametr max_wal_senders nastaví počet souběžných úloh, které mohou být spuštěny, doporučí 10 úkolů.

    c. max_replication_slots – = [počet slotů], doporučuje se nastavit na pět slotů.

4. Přidružte skupinu parametrů, kterou jste vytvořili, do instance RDS PostgreSQL.

## <a name="migrate-the-schema"></a>Migrace schématu

1. Extrahujte schéma ze zdrojové databáze a použijte ho pro cílovou databázi k dokončení migrace všech databázových objektů, jako jsou schémata tabulky, indexy a uložené procedury.

    Nejjednodušší způsob, jak migrovat pouze schéma, je použít pg_dump s možností-s. Další informace najdete v [příkladech](https://www.postgresql.org/docs/9.6/app-pgdump.html#PG-DUMP-EXAMPLES) v kurzu Postgres pg_dump.

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Chcete-li například vypsat soubor schématu pro databázi **dvdrental** , použijte následující příkaz:

    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s  > dvdrentalSchema.sql
    ```

2. V cílové službě vytvořte prázdnou databázi, která je Azure Database for PostgreSQL. Informace o připojení a vytvoření databáze najdete v jednom z následujících článků:

    * [Vytvoření serveru Azure Database for PostgreSQL pomocí Azure Portal](../postgresql/quickstart-create-server-database-portal.md)
    * [Vytvoření serveru Azure Database for PostgreSQLového škálování (Citus) pomocí Azure Portal](../postgresql/quickstart-create-hyperscale-portal.md)

3. Importujte schéma do cílové služby, což je Azure Database for PostgreSQL. Chcete-li obnovit soubor s výpisem stavu schématu, spusťte následující příkaz:

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    Příklad:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental < dvdrentalSchema.sql
    ```

4. Pokud vaše schéma obsahuje cizí klíče, počáteční načtení a průběžná synchronizace migrace selžou. Chcete-li extrahovat skript cizího klíče a přidat do cílového umístění skript cizího klíče (Azure Database for PostgreSQL), spusťte následující skript v PgAdmin nebo v psql:
  
    ```
    SELECT Queries.tablename
           ,concat('alter table ', Queries.tablename, ' ', STRING_AGG(concat('DROP CONSTRAINT ', Queries.foreignkey), ',')) as DropQuery
                ,concat('alter table ', Queries.tablename, ' ',
                                                STRING_AGG(concat('ADD CONSTRAINT ', Queries.foreignkey, ' FOREIGN KEY (', column_name, ')', 'REFERENCES ', foreign_table_name, '(', foreign_column_name, ')' ), ',')) as AddQuery
        FROM
        (SELECT
        tc.table_schema,
        tc.constraint_name as foreignkey,
        tc.table_name as tableName,
        kcu.column_name,
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name
    FROM
        information_schema.table_constraints AS tc
        JOIN information_schema.key_column_usage AS kcu
          ON tc.constraint_name = kcu.constraint_name
          AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu
          ON ccu.constraint_name = tc.constraint_name
          AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY') Queries
      GROUP BY Queries.tablename;
    ```

5. Spuštěním odkládacího klíče (který je druhým sloupcem) ve výsledku dotazu vyřaďte cizí klíč.

6. Pokud jste v datech triggery (vložení nebo aktualizace triggeru), vynutili integritu dat v cíli před replikací dat ze zdroje. Doporučením je zakázat aktivační události ve všech tabulkách *v cíli* během migrace a poté povolit triggery po dokončení migrace.

    Zakázání triggerů v cílové databázi:

    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrace poskytovatele prostředků Microsoft.DataMigration

1. Přihlaste se k webu Azure Portal, vyberte **Všechny služby** a pak vyberte **Předplatná**.

   ![Zobrazení předplatných na portálu](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-select-subscription1.png)

2. Vyberte předplatné, ve kterém chcete vytvořit instanci Azure Database Migration Service a pak vyberte **poskytovatelé prostředků**.

    ![Zobrazení poskytovatelů prostředků](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-select-resource-provider.png)

3. Vyhledejte „migration“ a pak napravo od **Microsoft.DataMigration** vyberte **Zaregistrovat**.

    ![Registrace poskytovatele prostředků](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-register-resource-provider.png)

## <a name="create-an-instance-of-azure-database-migration-service"></a>Vytvoření instance služby Azure Database Migration Service

1. Na webu Azure Portal vyberte **+ Vytvořit prostředek**, vyhledejte „Azure Database Migration Service“ a pak v rozevíracím seznamu vyberte **Azure Database Migration Service**.

    ![Azure Marketplace](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-marketplace.png)

2. Na obrazovce **Azure Database Migration Service** vyberte **Vytvořit**.

    ![Vytvoření instance služby Azure Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create1.png)
  
3. Na obrazovce **Vytvořit službu Migration Service** zadejte název služby, předplatné a novou nebo existující skupinu prostředků.

4. Vyberte umístění, ve kterém chcete vytvořit instanci Azure Database Migration Service.

5. Vyberte existující virtuální síť nebo vytvořte novou.

    Virtuální síť poskytuje Azure Database Migration Service s přístupem ke zdrojové instanci PostgreSQL a cílové Azure Database for PostgreSQL instance.

    Další informace o tom, jak vytvořit virtuální síť v Azure Portal, najdete v článku [vytvoření virtuální sítě pomocí Azure Portal](../virtual-network/quick-create-portal.md).

6. Vyberte cenovou úroveň; v případě této online migrace nezapomeňte vybrat cenovou úroveň Premium: 4vCores.

    ![Konfigurace nastavení instance služby Azure Database Migration Service](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-settings5.png)

7. Vyberte **Vytvořit** a vytvořte službu.

## <a name="create-a-migration-project"></a>Vytvoření projektu migrace

Po vytvoření služby ji vyhledejte na webu Azure Portal, otevřete ji a pak vytvořte nový projekt migrace.

1. Na webu Azure Portal vyberte **Všechny služby**, vyhledejte „Azure Database Migration Service“ a pak vyberte **Služby Azure Database Migration Service**.

      ![Vyhledání všech instancí služby Azure Database Migration Service](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-search.png)

2. Na obrazovce **služby Azure Database Migration Services** vyhledejte název Azure Database Migration Service instance, kterou jste vytvořili, vyberte instanci a pak vyberte + **Nový projekt migrace**.
3. Na obrazovce **Nový projekt migrace** zadejte název projektu, v textovém poli **typ zdrojového serveru** vyberte **AWS VP pro PostgreSQL** a potom v textovém poli **typ cílového serveru** vyberte **Azure Database for PostgreSQL**.
4. V části **Zvolte typ aktivity** vyberte možnost **migrace online dat**.

    > [!IMPORTANT]
    > Ujistěte se, že jste vybrali **online migraci dat**; offline migrace nejsou v tomto scénáři podporovány.

    ![Vytvoření projektu Database Migration Service](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-create-project5.png)

    > [!NOTE]
    > Alternativně můžete zvolit **vytvořit projekt pouze** k vytvoření projektu migrace a provést migraci později.

5. Vyberte **Uložit**.

6. Vyberte **Vytvořit a spustit aktivitu** a vytvořte projekt a spusťte aktivitu migrace.

    > [!NOTE]
    > Poznamenejte si požadavky potřebné k nastavení online migrace v okně pro vytvoření projektu.

## <a name="specify-source-details"></a>Zadání podrobností o zdroji

* Na obrazovce **Přidat podrobnosti zdroje** zadejte podrobnosti o připojení ke zdrojové instanci PostgreSQL.

   ![Podrobnosti zdroje](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-source-details5.png)

## <a name="specify-target-details"></a>Zadání podrobností o cíli

1. Vyberte **Uložit** a pak na obrazovce **cílové podrobnosti** zadejte podrobnosti o připojení pro cílový Azure Database for PostgreSQL Server, který je předem zřízený a má schéma zaplacení **DVD** nasazené pomocí pg_dump.

    ![Údaje o cíli](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-target-details.png)

2. Vyberte **Uložit** a pak na obrazovce **Mapovat na cílové databáze** namapujte zdrojovou a cílovou databázi pro migraci.

    Pokud cílová databáze obsahuje stejný název databáze jako zdrojová databáze, Azure Database Migration Service ve výchozím nastavení vybere cílovou databázi.

    ![Mapování na cílové databáze](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-map-target-databases.png)

3. Vyberte **Uložit**, na obrazovce **Shrnutí migrace** do textového pole **Název aktivity** zadejte název aktivity migrace a pak zkontrolujte souhrnné informace a ujistěte se, že podrobnosti zdroje a cíle odpovídají dříve zadaným informacím.

    ![Shrnutí migrace](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-migration-summary1.png)

## <a name="run-the-migration"></a>Spuštění migrace

* Vyberte **Spustit migraci**.

    Zobrazí se okno aktivity migrace a **Stav** aktivity bude **Inicializace**.

## <a name="monitor-the-migration"></a>Monitorování migrace

1. Na obrazovce aktivity migrace vyberte **Aktualizovat** a aktualizujte zobrazení, dokud se **Stav** migrace nezmění na **Spuštěno**.

    ![Stav aktivity – spuštěno](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-activity-status3.png)

2. V části **název databáze** vyberte konkrétní databázi, abyste získali stav migrace pro operace **úplného načtení dat** a **přírůstkové synchronizace dat** .

    **Úplné načtení dat** znázorňuje počáteční stav migrace zatížení, zatímco **přírůstkové synchronizace dat** zobrazuje stav Change Data Capture (CDC).

    ![Obrazovka inventáře – úplné zatížení dat](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-full-load.png)

    ![Obrazovka inventáře – přírůstková synchronizace dat](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-incremental.png)

## <a name="perform-migration-cutover"></a>Provedení přímé migrace

Po dokončení počátečního úplného načtení budou databáze označeny jako **připravené k přímou migraci**.

1. Jakmile budete připraveni dokončit migraci databází, vyberte **Spustit přímou migraci**.

2. Počkejte, než čítač **čekající změny** zobrazí **hodnotu 0** , aby se zajistilo, že všechny příchozí transakce do zdrojové databáze budou zastaveny, zaškrtněte políčko **Potvrdit** a pak vyberte **použít**.

    ![Dokončit obrazovku přímou migraci](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-complete-cutover.png)

3. Až se stav migrace databáze zobrazí jako **dokončený**, připojte své aplikace k nové cílové Azure Database for PostgreSQL databázi.

Online migrace místní instance služby RDS PostgreSQL na Azure Database for PostgreSQL je teď dokončená.

## <a name="next-steps"></a>Další kroky

* Informace o službě Azure Database Migration Service najdete v článku [Co je Azure Database Migration Service?](./dms-overview.md).
* Informace o Azure Database for PostgreSQL najdete v článku [co je Azure Database for PostgreSQL?](../postgresql/overview.md).
* Další otázky získáte v e-mailu s názvem požádat o alias [migrace databáze Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com) .
