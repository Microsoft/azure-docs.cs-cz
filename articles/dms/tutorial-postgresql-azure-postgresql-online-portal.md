---
title: 'Kurz: Migrace PostgreSQL do Azure DB pro PostgreSQL online přes portál Azure'
titleSuffix: Azure Database Migration Service
description: Naučte se provádět online migraci z místního PostgreSQL do Databáze Azure pro PostgreSQL pomocí služby Azure Database Migration Service prostřednictvím portálu Azure.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 03/25/2020
ms.openlocfilehash: 4985c492c8ca71da87cf1a519ebc658c203d3952
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246972"
---
# <a name="tutorial-migrate-postgresql-to-azure-db-for-postgresql-online-using-dms-via-the-azure-portal"></a>Kurz: Migrace PostgreSQL do Azure DB pro PostgreSQL online pomocí DMS přes portál Azure

Službu migrace databáze Azure můžete použít k migraci databází z místní instance PostgreSQL do [Databáze Azure pro PostgreSQL](https://docs.microsoft.com/azure/postgresql/) s minimálními prostoji do aplikace. V tomto kurzu migrujete ukázkovou databázi **dvd z** místní instance PostgreSQL 9.6 do Databáze Azure pro PostgreSQL pomocí aktivity migrace online ve službě Migrace databáze Azure.

V tomto kurzu se naučíte:
> [!div class="checklist"]
>
> * Migrujte ukázkové schéma pomocí nástroje pg_dump.
> * Vytvořte instanci služby Azure Database Migration Service.
> * Vytvořte projekt migrace ve službě Migrace databáze Azure.
> * Spuštění migrace
> * Monitorování migrace
> * Proveďte přechod migrace.

> [!NOTE]
> Použití služby Migrace databáze Azure k provedení migrace online vyžaduje vytvoření instance založené na cenové úrovni Premium. Šifrujeme disk, abychom zabránili krádeži dat během procesu migrace

> [!IMPORTANT]
> Pro optimální prostředí migrace Microsoft doporučuje vytvořit instanci služby Migrace databáze Azure ve stejné oblasti Azure jako cílová databáze. Přenášení dat mezi oblastmi geografickými lokalitami může zpomalit proces migrace a způsobit chyby.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu je potřeba provést následující:

* Stáhněte a nainstalujte [komunitní edici PostgreSQL](https://www.postgresql.org/download/) 9.4, 9.5, 9.6 nebo 10. Zdroj PostgreSQL Server verze musí být 9.4, 9.5, 9.6, 10 nebo 11. Další informace naleznete v článku [Podporované verze databáze PostgreSQL](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions).

    Kromě toho místní verze PostgreSQL musí odpovídat Azure Database for PostgreSQL. Například PostgreSQL 9.6 můžete jenom migrovat do databáze Azure pro PostgreSQL 9.6, 10 nebo 11, ale ne do Databáze Azure pro PostgreSQL 9.5.

* [Vytvořte databázi Azure pro postgreSQL server](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) nebo [vytvořte databázi Azure pro server PostgreSQL – Hyperscale (Citus).](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal)
* Vytvořte službu Migrace databáze Microsoft Azure pro Azure pomocí modelu nasazení Azure Resource Manageru, který poskytuje připojení k místním zdrojovým serverům site-to-site pomocí [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Další informace o vytvoření virtuální sítě naleznete v [dokumentaci k virtuální síti](https://docs.microsoft.com/azure/virtual-network/)a zejména v článcích rychlého startu s podrobnými podrobnostmi.

    > [!NOTE]
    > Pokud během instalace virtuální sítě používáte ExpressRoute s partnerským vztahem k síti společnosti Microsoft, přidejte do podsítě, ve které bude služba zřízena, následující [koncové body služby:](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)
    >
    > * Cílový koncový bod databáze (například koncový bod SQL, koncový bod Cosmos DB a tak dále)
    > * Koncový bod úložiště
    > * Koncový bod sběrnice
    >
    > Tato konfigurace je nezbytná, protože služba Azure Database Migration Service nemá připojení k internetu.

* Ujistěte se, že pravidla skupiny zabezpečení sítě (NSG) pro vaši virtuální síť neblokují následující příchozí komunikační porty pro službu Migrace databáze Azure: 443, 53, 9354, 445, 12000. Další podrobnosti o filtrování přenosů skupin nsg ve virtuální síti naleznete v článku [Filtrování síťového provozu se skupinami zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm).
* Nakonfigurujte bránu [Windows Firewall pro přístup k databázovému stroji](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Otevřete bránu firewall systému Windows, abyste povolili službě Azure Database Migration Service přístup ke zdroji PostgreSQL Server, který je ve výchozím nastavení tcp port 5432.
* Pokud před zdrojovými databázemi používáte zařízení brány firewall, možná bude potřeba přidat pravidla brány firewall, která službě Azure Database Migration Service povolí přístup ke zdrojovým databázím za účelem migrace.
* Vytvořte pravidlo [brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) na úrovni serveru pro Azure Database for PostgreSQL, které umožní službě Migrace databáze Azure přístup k cílovým databázím. Zadejte rozsah podsítí virtuální sítě používané pro službu Migrace databáze Azure.
* Povolte logickou repliku v souboru postgresql.config a nastavte následující parametry:

  * wal_level = **logical**
  * max_replication_slots = [počet slotů], doporučujeme nastavit na **pět slotů**
  * max_wal_senders = [počet souběžných úloh] - parametr max_wal_senders nastaví počet souběžných úloh, které můžete spustit, doporučujeme nastavení na **10 úloh**

> [!IMPORTANT]
> Všechny tabulky v existující databázi potřebují primární klíč, aby bylo zajištěno, že změny lze synchronizovat s cílovou databází.

## <a name="migrate-the-sample-schema"></a>Migrace ukázkového schématu

K dokončení všech databázových objektů, jako jsou schémata tabulek, indexy a uložené procedury, potřebujeme extrahovat schéma ze zdrojové databáze a použít ho na databázi.

1. Pomocí příkazu pg_dump -s můžete vytvořit soubor schématu s výpisem paměti pro databázi.

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Chcete-li například vytvořit soubor výpisu schématu pro databázi **dvdrental:**

    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s -O -x > dvdrentalSchema.sql
    ```

    Další informace o používání nástroje pg_dump, viz příklady v kurzu [pg-dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES).

2. Vytvořte prázdnou databázi v cílovém prostředí, což je Azure Database for PostgreSQL.

    Podrobnosti o tom, jak se připojit a vytvořit databázi, najdete v článku [Vytvoření databáze Azure pro PostgreSQL server na webu Azure Portal](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) nebo vytvoření azure databáze pro [PostgreSQL – hyperškálování (Citus) server na webu Azure Portal](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal).

    > [!NOTE]
    > Instance Azure Database for PostgreSQL – Hyperscale (Citus) má jenom jednu databázi: **citus**.

3. Importujte schéma do cílové databáze, kterou jste vytvořili pomocí obnovení souboru se schématem výpisu paměti.

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    Například:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental citus < dvdrentalSchema.sql
    ```

4. Chcete-li extrahovat skript cizího klíče a přidat ho do cíle (Azure Database for PostgreSQL), v PgAdmin nebo v psql, spusťte následující skript.

   > [!IMPORTANT]
   > Cizí klíče ve schématu způsobí selhání počátečního zatížení a nepřetržité synchronizace migrace.

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

6. Chcete-li zakázat aktivační události v cílové databázi, spusťte níže uvedený skript.

   > [!IMPORTANT]
   > Aktivační události (vložení nebo aktualizace) v data vynucují integritu dat v cíli před data replikována ze zdroje. V důsledku toho doporučujeme zakázat aktivační události ve všech tabulkách **v cíli** během migrace a po dokončení migrace aktivační události znovu povolit.

    ```
    SELECT DISTINCT CONCAT('ALTER TABLE ', event_object_schema, '.', event_object_table, ' DISABLE TRIGGER ', trigger_name, ';')
    FROM information_schema.triggers
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrace poskytovatele prostředků Microsoft.DataMigration

1. Přihlaste se k webu Azure Portal, vyberte **Všechny služby** a pak vyberte **Předplatná**.

   ![Zobrazení předplatných na portálu](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-select-subscriptions.png)

2. Vyberte předplatné, ve kterém chcete vytvořit instanci služby Migrace databáze Azure, a pak vyberte **zprostředkovatele prostředků**.

    ![Zobrazení poskytovatelů prostředků](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-select-resource-provider.png)

3. Vyhledejte „migration“ a pak napravo od **Microsoft.DataMigration** vyberte **Zaregistrovat**.

    ![Registrace poskytovatele prostředků](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Vytvoření instance DMS

1. Na webu Azure Portal vyberte **+ Vytvořit prostředek**, vyhledejte „Azure Database Migration Service“ a pak v rozevíracím seznamu vyberte **Azure Database Migration Service**.

    ![Azure Marketplace](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-marketplace.png)

2. Na obrazovce **Azure Database Migration Service** vyberte **Vytvořit**.

    ![Vytvoření instance služby Azure Database Migration Service](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create1.png)
  
3. Na obrazovce **Vytvořit službu migrace** zadejte název, předplatné, novou nebo existující skupinu prostředků a umístění služby.

4. Vyberte existující virtuální síť nebo vytvořte novou.

    Virtuální síť poskytuje službě Migrace databáze Azure přístup ke zdrojovému serveru PostgreSQL a cílové instanci Azure Database for PostgreSQL.

    Další informace o tom, jak vytvořit virtuální síť na webu Azure Portal, najdete v článku [Vytvoření virtuální sítě pomocí portálu Azure](https://aka.ms/DMSVnet).

5. Vyberte cenovou úroveň.

    Další informace o nákladech a cenových úrovních najdete na [stránce s cenami](https://aka.ms/dms-pricing).

    ![Konfigurace nastavení instance služby Azure Database Migration Service](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-settings4.png)

6. Chcete-li službu vytvořit, vyberte **zkontrolovat + vytvořit.**

   Vytvoření služby bude dokončeno během cca 10 až 15 minut.

## <a name="create-a-migration-project"></a>Vytvoření projektu migrace

Po vytvoření služby ji vyhledejte na webu Azure Portal, otevřete ji a pak vytvořte nový projekt migrace.

1. Na webu Azure Portal vyberte **Všechny služby**, vyhledejte „Azure Database Migration Service“ a pak vyberte **Služby Azure Database Migration Service**.

      ![Vyhledání všech instancí služby Migrace databáze Azure](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-search.png)

2. Na obrazovce **Azure Database Migration Services** vyhledejte název instance služby Migrace databáze Azure, kterou jste vytvořili, vyberte instanci a pak vyberte + Nový projekt **migrace**.

3. Na obrazovce **Nový projekt migrace** zadejte název projektu, v textovém poli Typ **zdrojového serveru** vyberte **PostgresSQL**v textovém poli **Typ cílového serveru** a vyberte Azure Database for **PostgreSQL**.

4. V části **Zvolit typ aktivity** vyberte **Online migraci dat**.

    ![Vytvoření projektu služby migrace databáze Azure](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create-project.png)

    > [!NOTE]
    > Alternativně můžete zvolit **Vytvořit projekt pouze** k vytvoření projektu migrace nyní a provést migraci později.

5. Vyberte **Uložit**, poznamenejte si požadavky, abyste úspěšně použili k migraci dat službu Migrace databáze Azure, a pak vyberte **Vytvořit a spustit aktivitu**.

## <a name="specify-source-details"></a>Zadání podrobností o zdroji

1. Na obrazovce **Přidat podrobnosti o zdroji** zadejte podrobnosti o připojení pro zdrojovou instanci PostgreSQL.

    ![Obrazovka Přidat podrobnosti zdroje](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-source-details.png)

2. Vyberte **Uložit**.

## <a name="specify-target-details"></a>Zadání podrobností o cíli

1. Na obrazovce **Podrobnosti cíle** zadejte podrobnosti o připojení pro cílový server Hyperscale (Citus), což je předem zřízená instance Hyperscale (Citus), do kterého bylo schéma **dvd rentals** nasazeno pomocí pg_dump.

    ![Obrazovka Podrobnosti cíle](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-target-details.png)

2. Vyberte **Uložit** a pak na obrazovce **Mapovat na cílové databáze** namapujte zdrojovou a cílovou databázi pro migraci.

    Pokud cílová databáze obsahuje stejný název databáze jako zdrojová databáze, služba Migrace databáze Azure vybere cílovou databázi ve výchozím nastavení.

    ![Obrazovka Mapovat na cílové databáze](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-map-target-databases.png)

3. Vyberte **Uložit**a potom na obrazovce **Nastavení migrace** přijměte výchozí hodnoty.

    ![Obrazovka Nastavení migrace](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-settings.png)

4. Vyberte **Uložit**, na obrazovce **Shrnutí migrace** do textového pole **Název aktivity** zadejte název aktivity migrace a pak zkontrolujte souhrnné informace a ujistěte se, že podrobnosti zdroje a cíle odpovídají dříve zadaným informacím.

    ![Obrazovka Souhrn migrace](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-summary.png)

## <a name="run-the-migration"></a>Spuštění migrace

* Vyberte **Spustit migraci**.

    Zobrazí se okno aktivity migrace a **stav** aktivity by se měl aktualizovat tak, aby se zobrazoval jako **probíhá zálohování**.

## <a name="monitor-the-migration"></a>Monitorování migrace

1. Na obrazovce aktivity migrace vyberte **Aktualizovat** a aktualizujte zobrazení, dokud se **Stav** migrace nezmění na **Dokončeno**.

     ![Sledování procesu migrace](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-monitor-migration.png)

2. Po dokončení migrace vyberte v části **Název databáze**konkrétní databázi, abyste se dostali ke stavu migrace pro **operace úplného načtení dat** a **přírůstkové synchronizace dat.**

   > [!NOTE]
   > **Úplné zatížení dat** zobrazuje stav počáteční migrace zatížení, zatímco **přírůstková synchronizace dat** zobrazuje stav sběru dat (CDC).

     ![Úplné podrobnosti o načítání dat](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-full-data-load-details.png)

     ![Podrobnosti o přírůstkové synchronizaci dat](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-incremental-data-sync-details.png)

## <a name="perform-migration-cutover"></a>Provedení přímé migrace

Po dokončení počátečního úplného načtení se databáze označí jako **Připraveno k přímé migraci**.

1. Jakmile budete připraveni dokončit migraci databází, vyberte **Spustit přímou migraci**.

2. Počkejte, až se v počítadlovém okně **Čekající změny** zobrazí **hodnota 0,** abyste zajistili, že budou zastaveny všechny příchozí transakce do zdrojové databáze, zaškrtněte políčko **Potvrdit** a pak vyberte **použít**.

    ![Kompletní přetoková obrazovka](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-complete-cutover.png)

3. Když se zobrazí stav migrace databáze **Dokončeno**, připojte aplikace k nové cílové instanci Azure Database for PostgreSQL.

## <a name="next-steps"></a>Další kroky

* Informace o známých problémech a omezeních při provádění online migrací do služby Azure Database for PostgreSQL najdete v článku [Známé problémy s online migracemi do služby Azure Database for PostgreSQLa jejich řešení](known-issues-azure-postgresql-online.md).
* Informace o službě Azure Database Migration Service najdete v článku [Co je Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
* Informace o Azure Database for PostgreSQL najdete v článku [Co je databáze Azure pro PostgreSQL?](https://docs.microsoft.com/azure/postgresql/overview).
