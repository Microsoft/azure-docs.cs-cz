---
title: 'Kurz: migrace PostgreSQL do Azure DB pro PostgreSQL online prostřednictvím Azure Portal'
titleSuffix: Azure Database Migration Service
description: Přečtěte si, jak provést online migraci z místního PostgreSQLu do Azure Database for PostgreSQL pomocí Azure Database Migration Service prostřednictvím Azure Portal.
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
ms.openlocfilehash: d28c45b2d0fc1a123f44020f42c4d2c59c593cb2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101709916"
---
# <a name="tutorial-migrate-postgresql-to-azure-db-for-postgresql-online-using-dms-via-the-azure-portal"></a>Kurz: migrace PostgreSQL do Azure DB pro PostgreSQL online pomocí DMS prostřednictvím Azure Portal

Pomocí Azure Database Migration Service můžete migrovat databáze z místní instance PostgreSQL a [Azure Database for PostgreSQL](../postgresql/index.yml) s minimálními prostoji do aplikace. V tomto kurzu migrujete ukázkovou databázi pro **pronájem DVD** z místní instance PostgreSQL 9,6 na Azure Database for PostgreSQL pomocí online aktivity migrace v Azure Database Migration Service.

V tomto kurzu se naučíte:
> [!div class="checklist"]
>
> * Pomocí nástroje pg_dump migrujte vzorové schéma.
> * Vytvořte instanci služby Azure Database Migration Service.
> * Vytvoří projekt migrace v Azure Database Migration Service.
> * Spuštění migrace
> * Monitorujte migraci.
> * Proveďte přímou migraci migrace.

> [!NOTE]
> Použití Azure Database Migration Service k provedení online migrace vyžaduje vytvoření instance založené na cenové úrovni Premium. Šifrujeme disk, aby se zabránilo krádeži dat během migrace.

> [!IMPORTANT]
> Pro optimální prostředí migrace doporučuje Microsoft vytvořit instanci Azure Database Migration Service ve stejné oblasti Azure jako cílová databáze. Přenášení dat mezi oblastmi geografickými lokalitami může zpomalit proces migrace a způsobit chyby.

## <a name="prerequisites"></a>Předpoklady

Pro absolvování tohoto kurzu je potřeba provést následující:

* Stáhněte a nainstalujte si [PostgreSQL community edition](https://www.postgresql.org/download/) 9,4, 9,5, 9,6 nebo 10. Verze zdrojového PostgreSQL serveru musí být 9,4, 9,5, 9,6, 10 nebo 11. Další informace najdete v článku [podporované verze databáze PostgreSQL](../postgresql/concepts-supported-versions.md).

    Všimněte si také, že cílová verze Azure Database for PostgreSQL musí být stejná nebo vyšší než verze místní PostgreSQL. Například PostgreSQL 9,6 může migrovat na Azure Database for PostgreSQL 9,6, 10 nebo 11, ale ne na Azure Database for PostgreSQL 9,5.

* [Vytvořte server Azure Database for PostgreSQL](../postgresql/quickstart-create-server-database-portal.md) nebo [Vytvořte Azure Database for PostgreSQL Citus (-The Scale) Server](../postgresql/quickstart-create-hyperscale-portal.md).
* Vytvořte Microsoft Azure Virtual Network pro Azure Database Migration Service pomocí modelu nasazení Azure Resource Manager, který umožňuje připojení typu Site-to-site k místním zdrojovým serverům pomocí [ExpressRoute](../expressroute/expressroute-introduction.md) nebo [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Další informace o vytváření virtuálních sítí najdete v [dokumentaci k Virtual Network](../virtual-network/index.yml)a zejména v článcích rychlý Start s podrobnými údaji.

    > [!NOTE]
    > Pokud při instalaci virtuální sítě používáte ExpressRoute s partnerským vztahem k síti Microsoftu, přidejte do podsítě, ve které se služba zřídí, tyto [koncové body](../virtual-network/virtual-network-service-endpoints-overview.md) služby:
    >
    > * Koncový bod cílové databáze (například koncový bod SQL, Cosmos DB koncový bod atd.)
    > * Koncový bod úložiště
    > * Koncový bod služby Service Bus
    >
    > Tato konfigurace je nezbytná, protože Azure Database Migration Service nemá připojení k Internetu.

* Zajistěte, aby pravidla skupiny zabezpečení sítě (NSG) pro vaši virtuální síť neblokovala odchozí port 443 z ServiceTag pro ServiceBus, úložiště a AzureMonitor. Další podrobnosti o filtrování provozu NSG virtuální sítě najdete v článku [filtrování provozu sítě pomocí skupin zabezpečení sítě](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Nakonfigurujte bránu [Windows Firewall pro přístup k databázovému stroji](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Otevřete bránu Windows Firewall, abyste povolili Azure Database Migration Service přístup ke zdrojovému serveru PostgreSQL, který je ve výchozím nastavení port TCP 5432.
* Pokud před zdrojovými databázemi používáte zařízení brány firewall, možná bude potřeba přidat pravidla brány firewall, která službě Azure Database Migration Service povolí přístup ke zdrojovým databázím za účelem migrace.
* Vytvořte [pravidlo brány firewall](../postgresql/concepts-firewall-rules.md) na úrovni serveru pro Azure Database for PostgreSQL, které povolí Azure Database Migration Service přístup k cílovým databázím. Zadejte rozsah podsítě virtuální sítě, která se používá pro Azure Database Migration Service.
* Povolte logickou repliku v souboru postgresql.config a nastavte následující parametry:

  * wal_level = **logical**
  * max_replication_slots = [počet slotů], doporučuje se nastavit na **pět slotů**
  * max_wal_senders = [počet souběžných úloh] - parametr max_wal_senders nastaví počet souběžných úloh, které můžete spustit, doporučujeme nastavení na **10 úloh**

> [!IMPORTANT]
> Všechny tabulky v existující databázi potřebují primární klíč, aby bylo možné synchronizovat změny v cílové databázi.

## <a name="migrate-the-sample-schema"></a>Migrace ukázkového schématu

K dokončení všech databázových objektů, jako jsou schémata tabulek, indexy a uložené procedury, potřebujeme extrahovat schéma ze zdrojové databáze a použít ho na databázi.

1. Pomocí příkazu pg_dump -s můžete vytvořit soubor schématu s výpisem paměti pro databázi.

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Chcete-li například vytvořit soubor s výpisem stavu schématu pro databázi **dvdrental** :

    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s -O -x > dvdrentalSchema.sql
    ```

    Další informace o používání nástroje pg_dump, viz příklady v kurzu [pg-dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES).

2. Vytvořte prázdnou databázi v cílovém prostředí, což je Azure Database for PostgreSQL.

    Podrobnosti o tom, jak připojit a vytvořit databázi, najdete v článku [vytvoření serveru Azure Database for PostgreSQL v Azure Portal](../postgresql/quickstart-create-server-database-portal.md) nebo [vytvoření serveru Azure Database for PostgreSQLového škálování (Citus) v Azure Portal](../postgresql/quickstart-create-hyperscale-portal.md).

    > [!NOTE]
    > Instance Azure Database for PostgreSQL – Citus () má pouze jednu databázi: **Citus**.

3. Importujte schéma do cílové databáze, kterou jste vytvořili pomocí obnovení souboru se schématem výpisu paměti.

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    Například:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental citus < dvdrentalSchema.sql
    ```

4. Chcete-li extrahovat skript cizího klíče a přidat jej do cílového umístění (Azure Database for PostgreSQL), spusťte následující skript v PgAdmin nebo psql.

   > [!IMPORTANT]
   > Cizí klíče ve vašem schématu způsobí, že počáteční zatížení a nepřetržitá synchronizace migrace budou neúspěšné.

    ```
    SELECT Q.table_name
        ,CONCAT('ALTER TABLE ', table_schema, '.', table_name, STRING_AGG(DISTINCT CONCAT(' DROP CONSTRAINT ', foreignkey), ','), ';') as DropQuery
            ,CONCAT('ALTER TABLE ', table_schema, '.', table_name, STRING_AGG(DISTINCT CONCAT(' ADD CONSTRAINT ', foreignkey, ' FOREIGN KEY (', column_name, ')', ' REFERENCES ', foreign_table_schema, '.', foreign_table_name, '(', foreign_column_name, ')' ), ','), ';') as AddQuery
    FROM
        (SELECT
        S.table_schema,
        S.foreignkey,
        S.table_name,
        STRING_AGG(DISTINCT S.column_name, ',') AS column_name,
        S.foreign_table_schema,
        S.foreign_table_name,
        STRING_AGG(DISTINCT S.foreign_column_name, ',') AS foreign_column_name
    FROM
        (SELECT DISTINCT
        tc.table_schema,
        tc.constraint_name AS foreignkey,
        tc.table_name,
        kcu.column_name,
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name
        FROM information_schema.table_constraints AS tc
        JOIN information_schema.key_column_usage AS kcu ON tc.constraint_name = kcu.constraint_name AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu ON ccu.constraint_name = tc.constraint_name AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY'
        ) S
        GROUP BY S.table_schema, S.foreignkey, S.table_name, S.foreign_table_schema, S.foreign_table_name
        ) Q
        GROUP BY Q.table_schema, Q.table_name;
    ```

5. Spusťte skript pro odstranění cizího klíče (druhý sloupec) ve výsledku dotazu odstraňte cizí klíč.

6. Chcete-li zakázat aktivační události v cílové databázi, spusťte následující skript.

   > [!IMPORTANT]
   > Aktivační události (INSERT nebo Update) v datech vynutila integritu dat v cíli před daty replikovanými ze zdroje. V důsledku toho se doporučuje zakázat aktivační události ve všech tabulkách **v cíli** během migrace a poté znovu povolit triggery po dokončení migrace.

    ```
    SELECT DISTINCT CONCAT('ALTER TABLE ', event_object_schema, '.', event_object_table, ' DISABLE TRIGGER ', trigger_name, ';')
    FROM information_schema.triggers
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrace poskytovatele prostředků Microsoft.DataMigration

1. Přihlaste se k webu Azure Portal, vyberte **Všechny služby** a pak vyberte **Předplatná**.

   ![Zobrazení předplatných na portálu](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-select-subscriptions.png)

2. Vyberte předplatné, ve kterém chcete vytvořit instanci Azure Database Migration Service a pak vyberte **poskytovatelé prostředků**.

    ![Zobrazení poskytovatelů prostředků](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-select-resource-provider.png)

3. Vyhledejte „migration“ a pak napravo od **Microsoft.DataMigration** vyberte **Zaregistrovat**.

    ![Registrace poskytovatele prostředků](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Vytvoření instance DMS

1. Na webu Azure Portal vyberte **+ Vytvořit prostředek**, vyhledejte „Azure Database Migration Service“ a pak v rozevíracím seznamu vyberte **Azure Database Migration Service**.

    ![Azure Marketplace](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-marketplace.png)

2. Na obrazovce **Azure Database Migration Service** vyberte **Vytvořit**.

    ![Vytvoření instance služby Azure Database Migration Service](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create1.png)
  
3. Na obrazovce **vytvořit službu migrace** zadejte název, předplatné, novou nebo existující skupinu prostředků a umístění služby.

4. Vyberte existující virtuální síť nebo vytvořte novou.

    Virtuální síť poskytuje Azure Database Migration Service s přístupem ke zdrojovému serveru PostgreSQL a cílové instance Azure Database for PostgreSQL.

    Další informace o tom, jak vytvořit virtuální síť v Azure Portal, najdete v článku [vytvoření virtuální sítě pomocí Azure Portal](../virtual-network/quick-create-portal.md).

5. Vyberte cenovou úroveň.

    Další informace o nákladech a cenových úrovních najdete na [stránce s cenami](https://aka.ms/dms-pricing).

    ![Konfigurace nastavení instance služby Azure Database Migration Service](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-settings4.png)

6. Vyberte možnost **zkontrolovat + vytvořit** a vytvořte službu.

   Vytváření služby bude dokončeno během přibližně 10 až 15 minut.

## <a name="create-a-migration-project"></a>Vytvoření projektu migrace

Po vytvoření služby ji vyhledejte na webu Azure Portal, otevřete ji a pak vytvořte nový projekt migrace.

1. Na webu Azure Portal vyberte **Všechny služby**, vyhledejte „Azure Database Migration Service“ a pak vyberte **Služby Azure Database Migration Service**.

      ![Vyhledat všechny instance Azure Database Migration Service](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-search.png)

2. Na obrazovce **služby Azure Database Migration Services** vyhledejte název Azure Database Migration Service instance, kterou jste vytvořili, vyberte instanci a pak vyberte + **Nový projekt migrace**.

3. Na obrazovce **Nový projekt migrace** zadejte název projektu, v textovém poli **typ zdrojového serveru** vyberte možnost **PostgreSQL**, v textovém poli **typ cílového serveru** vyberte možnost **Azure Database for PostgreSQL**.

4. V části **Zvolte typ aktivity** vyberte možnost **migrace online dat**.

    ![Vytvořit Azure Database Migration Service projekt](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create-project.png)

    > [!NOTE]
    > Alternativně můžete zvolit **vytvořit projekt pouze** k vytvoření projektu migrace a provést migraci později.

5. Vyberte **Save (Uložit**), poznamenejte si požadavky na úspěšné použití Azure Database Migration Service k migraci dat a pak vyberte **vytvořit a spustit aktivitu**.

## <a name="specify-source-details"></a>Zadání podrobností o zdroji

1. Na obrazovce **Přidat podrobnosti zdroje** zadejte podrobnosti o připojení ke zdrojové instanci PostgreSQL.

    ![Obrazovka Přidat podrobnosti zdroje](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-source-details.png)

2. Vyberte **Uložit**.

## <a name="specify-target-details"></a>Zadání podrobností o cíli

1. Na obrazovce **cílové podrobnosti** zadejte podrobnosti o připojení pro cílový server Citus (), což je předem zřízená instance (Citus), na kterou se nasadilo schéma zaplacení **DVD** , pomocí pg_dump.

    ![Obrazovka Podrobnosti cíle](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-target-details.png)

2. Vyberte **Uložit** a pak na obrazovce **Mapovat na cílové databáze** namapujte zdrojovou a cílovou databázi pro migraci.

    Pokud cílová databáze obsahuje stejný název databáze jako zdrojová databáze, Azure Database Migration Service ve výchozím nastavení vybere cílovou databázi.

    ![Obrazovka mapování na cílové databáze](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-map-target-databases.png)

3. Vyberte **Uložit** a pak na obrazovce **Nastavení migrace** přijměte výchozí hodnoty.

    ![Obrazovka nastavení migrace](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-settings.png)

4. Vyberte **Uložit**, na obrazovce **Shrnutí migrace** do textového pole **Název aktivity** zadejte název aktivity migrace a pak zkontrolujte souhrnné informace a ujistěte se, že podrobnosti zdroje a cíle odpovídají dříve zadaným informacím.

    ![Souhrnná obrazovka migrace](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-summary.png)

## <a name="run-the-migration"></a>Spuštění migrace

* Vyberte **Spustit migraci**.

    Zobrazí se okno aktivita migrace a **stav** aktivity by se měl aktualizovat, aby se zobrazila **v průběhu zálohování**.

## <a name="monitor-the-migration"></a>Monitorování migrace

1. Na obrazovce aktivity migrace vyberte **Aktualizovat** a aktualizujte zobrazení, dokud se **Stav** migrace nezmění na **Dokončeno**.

     ![Monitorovat proces migrace](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-monitor-migration.png)

2. Po dokončení migrace v části **název databáze** vyberte konkrétní databázi, abyste získali stav migrace **úplných operací načítání dat** a **přírůstkových synchronizací dat** .

   > [!NOTE]
   > **Úplné načtení dat** znázorňuje počáteční stav migrace zatížení, zatímco **přírůstkové synchronizace dat** zobrazuje stav Change Data Capture (CDC).

     ![Podrobnosti o plném zatížení dat](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-full-data-load-details.png)

     ![Podrobnosti o přírůstkové synchronizaci dat](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-incremental-data-sync-details.png)

## <a name="perform-migration-cutover"></a>Provedení přímé migrace

Po dokončení počátečního úplného načtení se databáze označí jako **Připraveno k přímé migraci**.

1. Jakmile budete připraveni dokončit migraci databází, vyberte **Spustit přímou migraci**.

2. Počkejte, než čítač **čekající změny** zobrazí **hodnotu 0** , aby se zajistilo, že všechny příchozí transakce do zdrojové databáze budou zastaveny, zaškrtněte políčko **Potvrdit** a pak vyberte **použít**.

    ![Dokončit obrazovku přímou migraci](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-complete-cutover.png)

3. Jakmile se zobrazí stav migrace databáze **dokončeno**, [znovu vytvořte sekvence](https://wiki.postgresql.org/wiki/Fixing_Sequences) (Pokud je k dispozici) a připojte své aplikace k nové cílové instanci Azure Database for PostgreSQL.

## <a name="next-steps"></a>Další kroky

* Informace o známých problémech a omezeních při provádění online migrací do služby Azure Database for PostgreSQL najdete v článku [Známé problémy s online migracemi do služby Azure Database for PostgreSQLa jejich řešení](known-issues-azure-postgresql-online.md).
* Informace o službě Azure Database Migration Service najdete v článku [Co je Azure Database Migration Service?](./dms-overview.md).
* Informace o Azure Database for PostgreSQL najdete v článku [co je Azure Database for PostgreSQL?](../postgresql/overview.md).