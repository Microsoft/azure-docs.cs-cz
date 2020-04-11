---
title: 'Kurz: Migrace RDS PostgreSQL online do databáze Azure pro PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Naučte se provádět online migraci z RDS PostgreSQL do Databáze Azure pro PostgreSQL pomocí služby Migrace databáze Azure.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 04/11/2020
ms.openlocfilehash: be6f0cd734d31f43557b49f8e9314e925b383899
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113950"
---
# <a name="tutorial-migrate-rds-postgresql-to-azure-db-for-postgresql-online-using-dms"></a>Kurz: Migrace RDS PostgreSQL do Azure DB pro PostgreSQL online pomocí DMS

Službu migrace databáze Azure můžete použít k migraci databází z instance RDS PostgreSQL do [databáze Azure pro PostgreSQL,](https://docs.microsoft.com/azure/postgresql/) zatímco zdrojová databáze zůstane během migrace online. Jinými slovy migrace lze dosáhnout s minimálníprostožně do aplikace. V tomto kurzu migrujete ukázkovou databázi **dvd z** instance RDS PostgreSQL 9.6 do Databáze Azure pro PostgreSQL pomocí aktivity migrace online ve službě Migrace databáze Azure.

V tomto kurzu se naučíte:
> [!div class="checklist"]
>
> * Migrujte ukázkové schéma pomocí nástroje pg_dump.
> * Vytvořte instanci služby Azure Database Migration Service.
> * Vytvořte projekt migrace pomocí služby Migrace databáze Azure.
> * Spuštění migrace
> * Monitorování migrace
> * Proveďte přechod migrace.

> [!NOTE]
> Použití služby migrace databáze Azure k provedení migrace online vyžaduje vytvoření instance založené na cenové úrovni Premium. Další informace najdete na stránce [s cenami](https://azure.microsoft.com/pricing/details/database-migration/) služby Azure Database Migration Service. Šifrujeme disk, abychom zabránili krádeži dat během procesu migrace.

> [!IMPORTANT]
> Aby migrace proběhla optimálním způsobem, doporučuje Microsoft vytvořit instanci služby Azure Database Migration Service ve stejné oblasti Azure, ve které je cílová databáze. Přenášení dat mezi oblastmi geografickými lokalitami může zpomalit proces migrace a způsobit chyby.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Tento článek popisuje, jak provést online migraci z místní instance PostgreSQL do databáze Azure pro PostgreSQL.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu je potřeba provést následující:

* Stáhněte a nainstalujte [komunitní edici PostgreSQL](https://www.postgresql.org/download/) 9.5, 9.6 nebo 10. Zdroj PostgreSQL Server verze musí být 9.5.11, 9.6.7, 10 nebo novější. Další informace naleznete v článku [Podporované verze databáze PostgreSQL](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions).

   Všimněte si také, že cílová databáze Azure pro postgreSQL verze musí být rovna nebo novější než verze RDS PostgreSQL. Například RDS PostgreSQL 9.6 můžete jenom migrovat do Databáze Azure pro PostgreSQL 9.6, 10 nebo 11, ale ne do Azure Database pro PostgreSQL 9.5.

* Vytvořte instanci [Databáze Azure pro PostgreSQL](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) nebo [Azure Database for PostgreSQL – Hyperscale (Citus).](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal) Podrobnosti o tom, jak se připojit k PostgreSQL Serveru pomocí pgAdmin, naleznete v této [části](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal#connect-to-the-postgresql-server-using-pgadmin) dokumentu.
* Vytvořte službu Migrace databáze Microsoft Azure pro Azure pomocí modelu nasazení Azure Resource Manageru, který poskytuje připojení k místním zdrojovým serverům site-to-site pomocí [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Další informace o vytvoření virtuální sítě naleznete v [dokumentaci k virtuální síti](https://docs.microsoft.com/azure/virtual-network/)a zejména v článcích rychlého startu s podrobnými podrobnostmi.
* Ujistěte se, že pravidla skupiny zabezpečení sítě virtuální sítě neblokují následující příchozí komunikační porty služby Azure Database Migration Service: 443, 53, 9354, 445 a 12000. Další podrobnosti o filtrování přenosů skupin nsg ve virtuální síti naleznete v článku [Filtrování síťového provozu se skupinami zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Nakonfigurujte bránu [Windows Firewall pro přístup k databázovému stroji](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Otevřete bránu firewall systému Windows, abyste povolili službě Azure Database Migration Service přístup ke zdrojovému serveru PostgreSQL, který je ve výchozím nastavení tcp port em 5432.
* Pokud před zdrojovými databázemi používáte zařízení brány firewall, možná bude potřeba přidat pravidla brány firewall, která službě Azure Database Migration Service povolí přístup ke zdrojovým databázím za účelem migrace.
* Vytvořte pravidlo [brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) na úrovni serveru pro server Azure Database for PostgreSQL, které umožní přístup služby Azure Database Migration Service k cílovým databázím. Zadejte rozsah podsítí virtuální sítě používané pro službu Migrace databáze Azure.

### <a name="set-up-aws-rds-postgresql-for-replication"></a>Nastavení aws RDS PostgreSQL pro replikaci

1. Chcete-li vytvořit novou skupinu parametrů, postupujte podle pokynů poskytnutých AWS v článku [Práce se skupinami parametrů DB](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithParamGroups.html).
2. Pomocí hlavního uživatelského jména se můžete připojit ke zdroji ze služby Migrace databáze Azure. Pokud používáte jiný účet než hlavní uživatelský účet, musí mít rds_superuser roli a roli rds_replication. Role rds_replication uděluje oprávnění ke správě logických slotů a streamování dat pomocí logických slotů.
3. Vytvořte novou skupinu parametrů s následující konfigurací:

    a. Nastavte parametr rds.logical_replication ve skupině parametrů DB na hodnotu 1.

    b. max_wal_senders =[počet souběžných úloh] - Parametr max_wal_senders nastaví počet souběžných úloh, které lze spustit, doporučujeme 10 úloh.

    c. max_replication_slots – = [počet slotů], doporučujeme nastavit na pět slotů.

4. Přidružte vytvořenou skupinu parametrů k instanci RDS PostgreSQL.

## <a name="migrate-the-schema"></a>Migrace schématu

1. Extrahujte schéma ze zdrojové databáze a aplikujte na cílovou databázi k dokončení migrace všech databázových objektů, jako jsou schémata tabulky, indexy a uložené procedury.

    Nejjednodušší způsob, jak migrovat pouze schéma, je použít pg_dump s volbou -s. Další informace naleznete v [příkladech](https://www.postgresql.org/docs/9.6/app-pgdump.html#PG-DUMP-EXAMPLES) v kurzu pg_dump Postgres.

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Chcete-li například vyřadit soubor schématu pro databázi **dvdrental,** použijte následující příkaz:

    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s  > dvdrentalSchema.sql
    ```

2. Vytvořte prázdnou databázi v cílové službě, což je Azure Database for PostgreSQL. Chcete-li se připojit a vytvořit databázi, podívejte se na jeden z následujících článků:

    * [Vytvoření databáze Azure pro PostgreSQL server pomocí portálu Azure](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal)
    * [Vytvoření databáze Azure pro server PostgreSQL – Hyperscale (Citus) pomocí portálu Azure](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal)

3. Importujte schéma do cílové služby, což je Azure Database for PostgreSQL. Chcete-li obnovit soubor výpisu schématu, spusťte následující příkaz:

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    Příklad:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental < dvdrentalSchema.sql
    ```

4. Pokud vaše schéma obsahuje cizí klíče, počáteční načtení a průběžná synchronizace migrace selžou. Pokud chcete extrahovat skript cizího klíče a přidat skript cizího klíče do cíle (Azure Database for PostgreSQL), spusťte následující skript v PgAdmin nebo v psql:
  
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

5. Spusťte přetažení cizího klíče (což je druhý sloupec) ve výsledku dotazu k přetažení cizího klíče.

6. Pokud máte aktivační události (vložit nebo aktualizovat aktivační událost) v datech, bude vynucovat integritu dat v cíli před replikací dat ze zdroje. Doporučujeme zakázat aktivační události ve všech tabulkách *v cíli* během migrace a po dokončení migrace povolit aktivační události.

    Zakázání aktivačních událostí v cílové databázi:

    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrace poskytovatele prostředků Microsoft.DataMigration

1. Přihlaste se k webu Azure Portal, vyberte **Všechny služby** a pak vyberte **Předplatná**.

   ![Zobrazení předplatných na portálu](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-select-subscription1.png)

2. Vyberte předplatné, ve kterém chcete vytvořit instanci služby Migrace databáze Azure, a pak vyberte **zprostředkovatele prostředků**.

    ![Zobrazení poskytovatelů prostředků](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-select-resource-provider.png)

3. Vyhledejte „migration“ a pak napravo od **Microsoft.DataMigration** vyberte **Zaregistrovat**.

    ![Registrace poskytovatele prostředků](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-register-resource-provider.png)

## <a name="create-an-instance-of-azure-database-migration-service"></a>Vytvoření instance služby Migrace databáze Azure

1. Na webu Azure Portal vyberte **+ Vytvořit prostředek**, vyhledejte „Azure Database Migration Service“ a pak v rozevíracím seznamu vyberte **Azure Database Migration Service**.

    ![Azure Marketplace](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-marketplace.png)

2. Na obrazovce **Azure Database Migration Service** vyberte **Vytvořit**.

    ![Vytvoření instance služby Azure Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create1.png)
  
3. Na obrazovce **Vytvořit službu Migration Service** zadejte název služby, předplatné a novou nebo existující skupinu prostředků.

4. Vyberte umístění, ve kterém chcete vytvořit instanci služby Migrace databáze Azure.

5. Vyberte existující virtuální síť nebo vytvořte novou.

    Virtuální síť poskytuje službě Migrace databáze Azure přístup ke zdrojové instanci PostgreSQL a cílové instanci Azure Database for PostgreSQL.

    Další informace o tom, jak vytvořit virtuální síť na webu Azure Portal, najdete v článku [Vytvoření virtuální sítě pomocí portálu Azure](https://aka.ms/DMSVnet).

6. Vyberte cenovou úroveň; Pro tuto online migraci nezapomeňte vybrat cenovou úroveň Premium: 4vCores.

    ![Konfigurace nastavení instance služby Azure Database Migration Service](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-settings5.png)

7. Vyberte **Vytvořit** a vytvořte službu.

## <a name="create-a-migration-project"></a>Vytvoření projektu migrace

Po vytvoření služby ji vyhledejte na webu Azure Portal, otevřete ji a pak vytvořte nový projekt migrace.

1. Na webu Azure Portal vyberte **Všechny služby**, vyhledejte „Azure Database Migration Service“ a pak vyberte **Služby Azure Database Migration Service**.

      ![Vyhledání všech instancí služby Azure Database Migration Service](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-search.png)

2. Na obrazovce **Služby migrace databáze Azure** vyhledejte název instance služby Migrace databáze Azure, kterou jste vytvořili, vyberte instanci a pak vyberte + **Nový projekt migrace**.
3. Na obrazovce **Nový projekt migrace** zadejte název projektu, v textovém poli Typ **zdrojového serveru** vyberte **AWS RDS pro PostgreSQL**a potom v textovém poli **Typ cílového serveru** vyberte Azure Database for **PostgreSQL**.
4. V části **Zvolit typ aktivity** vyberte **Online migraci dat**.

    > [!IMPORTANT]
    > Nezapomeňte vybrat **online migraci dat**. offline migrace nejsou pro tento scénář podporovány.

    ![Vytvoření projektu Database Migration Service](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-create-project5.png)

    > [!NOTE]
    > Alternativně můžete zvolit **Vytvořit projekt pouze** k vytvoření projektu migrace nyní a provést migraci později.

5. Vyberte **Uložit**.

6. Vyberte **Vytvořit a spustit aktivitu** a vytvořte projekt a spusťte aktivitu migrace.

    > [!NOTE]
    > Poznamenejte si prosím předpoklady potřebné k nastavení migrace online v okně pro vytváření projektu.

## <a name="specify-source-details"></a>Zadání podrobností o zdroji

* Na obrazovce **Přidat podrobnosti o zdroji** zadejte podrobnosti o připojení pro zdrojovou instanci PostgreSQL.

   ![Podrobnosti zdroje](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-source-details5.png)

## <a name="specify-target-details"></a>Zadání podrobností o cíli

1. Vyberte **Uložit**a potom na obrazovce **Podrobnosti cíl,** zadejte podrobnosti o připojení pro cílovou databázi Azure pro PostgreSQL server, který je předem zřízena a má schéma **dvd výpůjčky** nasazené pomocí pg_dump.

    ![Podrobnosti o cíli](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-target-details.png)

2. Vyberte **Uložit** a pak na obrazovce **Mapovat na cílové databáze** namapujte zdrojovou a cílovou databázi pro migraci.

    Pokud cílová databáze obsahuje stejný název databáze jako zdrojová databáze, služba Migrace databáze Azure vybere cílovou databázi ve výchozím nastavení.

    ![Mapování na cílové databáze](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-map-target-databases.png)

3. Vyberte **Uložit**, na obrazovce **Shrnutí migrace** do textového pole **Název aktivity** zadejte název aktivity migrace a pak zkontrolujte souhrnné informace a ujistěte se, že podrobnosti zdroje a cíle odpovídají dříve zadaným informacím.

    ![Shrnutí migrace](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-migration-summary1.png)

## <a name="run-the-migration"></a>Spuštění migrace

* Vyberte **Spustit migraci**.

    Zobrazí se okno aktivity migrace a **Stav** aktivity bude **Inicializace**.

## <a name="monitor-the-migration"></a>Monitorování migrace

1. Na obrazovce aktivity migrace vyberte **Aktualizovat** a aktualizujte zobrazení, dokud se **Stav** migrace nezmění na **Spuštěno**.

    ![Stav aktivity - spuštěno](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-activity-status3.png)

2. V části **NÁZEV DATABÁZE**vyberte konkrétní databázi, abyste se dostali ke stavu migrace pro **operace úplného načtení dat** a **přírůstkové synchronizace dat.**

    **Úplné zatížení dat** zobrazuje stav počáteční migrace zatížení, zatímco **přírůstková synchronizace dat** zobrazuje stav sběru dat (CDC).

    ![Obrazovka zásob - úplné načtení dat](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-full-load.png)

    ![Obrazovka Zásob – přírůstková synchronizace dat](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-incremental.png)

## <a name="perform-migration-cutover"></a>Provedení přímé migrace

Po dokončení počátečního úplného načtení jsou databáze označeny **jako Připraveno k ujetí.**

1. Jakmile budete připraveni dokončit migraci databází, vyberte **Spustit přímou migraci**.

2. Počkejte, až se v počítadlovém okně **Čekající změny** zobrazí **hodnota 0,** abyste zajistili, že budou zastaveny všechny příchozí transakce do zdrojové databáze, zaškrtněte políčko **Potvrdit** a pak vyberte **použít**.

    ![Kompletní přetoková obrazovka](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-complete-cutover.png)

3. Když se zobrazí stav migrace databáze **Dokončeno**, připojte aplikace k nové cílové databázi Azure database for PostgreSQL.

Vaše online migrace místní instance RDS PostgreSQL do Azure Database for PostgreSQL je teď dokončená.

## <a name="next-steps"></a>Další kroky

* Informace o službě Azure Database Migration Service najdete v článku [Co je Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
* Informace o Azure Database for PostgreSQL najdete v článku [Co je databáze Azure pro PostgreSQL?](https://docs.microsoft.com/azure/postgresql/overview).
* Další otázky se můžete zeptat na alias [Migrace databáze Azure.](mailto:AskAzureDatabaseMigrations@service.microsoft.com)
