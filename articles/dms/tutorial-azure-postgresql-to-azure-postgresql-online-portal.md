---
title: 'Kurz: migrace Azure DB pro PostgreSQL do Azure DB pro PostgreSQL online prostřednictvím Azure Portal'
titleSuffix: Azure Database Migration Service
description: Naučte se provádět online migraci z jedné databáze Azure DB pro PostgreSQL do jiné Azure Database for PostgreSQL pomocí Azure Database Migration Service prostřednictvím Azure Portal.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 07/21/2020
ms.openlocfilehash: 545359cd68c18fea8bf1369a57226d024489f293
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258910"
---
# <a name="tutorial-migrateupgrade-azure-db-for-postgresql---single-server-to-azure-db-for-postgresql---single-server--online-using-dms-via-the-azure-portal"></a>Kurz: migrace/upgrade databáze Azure DB pro PostgreSQL – jeden server do Azure DB pro PostgreSQL-Single server online pomocí DMS prostřednictvím Azure Portal

Pomocí Azure Database Migration Service můžete migrovat databáze z instance [serveru Azure Database for PostgreSQL na jeden server](../postgresql/overview.md#azure-database-for-postgresql---single-server) do stejné nebo jiné verze Azure Database for PostgreSQL – jedné instance serveru nebo Azure Database for PostgreSQL flexibilního serveru s minimálními výpadky. V tomto kurzu migrujete ukázkovou databázi **pronájmu DVD** z Azure Database for PostgreSQL v10 za účelem na Azure Database for PostgreSQL jeden server pomocí online aktivity migrace v Azure Database Migration Service.

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

> [!IMPORTANT]
> Migrace z Azure Database for PostgreSQL se podporuje pro PostgreSQL verze 10 a novější. Tento kurz můžete také použít k migraci z jedné instance Azure Database for PostgreSQL do jiné instance Azure Database for PostgreSQL nebo instance Citus ().

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu je potřeba provést následující:

* Pro podporované kombinace migrace a verze se můžete podívat na [stav migračních scénářů podporovaných nástrojem Azure Database Migration Service](./resource-scenario-status.md) . 
* Existující instance [Azure Database for PostgreSQL](../postgresql/index.yml) verze 10 a novější s databází **pronájmu DVD** 

    Všimněte si také, že cílová verze Azure Database for PostgreSQL musí být stejná nebo vyšší než verze místní PostgreSQL. Například PostgreSQL 10 se může migrovat na Azure Database for PostgreSQL 10 nebo 11, ale ne na Azure Database for PostgreSQL 9,6.

* [Vytvořte server Azure Database for PostgreSQL](../postgresql/quickstart-create-server-database-portal.md) nebo [vytvořte server Azure Database for PostgreSQL Citus](../postgresql/quickstart-create-hyperscale-portal.md) jako cílový databázový server, do kterého chcete migrovat data.
* Vytvořte Microsoft Azure Virtual Network pro Azure Database Migration Service pomocí modelu nasazení Azure Resource Manager. Další informace o vytváření virtuálních sítí najdete v [dokumentaci k Virtual Network](../virtual-network/index.yml)a zejména v článcích rychlý Start s podrobnými údaji.

* Zajistěte, aby pravidla skupiny zabezpečení sítě (NSG) pro vaši virtuální síť neblokovala následující odchozí komunikační porty Azure Database Migration Service: 443, 53, 9354, 445, 12000. Další podrobnosti o filtrování provozu NSG virtuální sítě najdete v článku [filtrování provozu sítě pomocí skupin zabezpečení sítě](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Vytvořte [pravidlo brány firewall](../azure-sql/database/firewall-configure.md) na úrovni serveru pro zdroj Azure Database for PostgreSQL, aby Azure Database Migration Service mohl získat přístup ke zdrojovým databázím. Zadejte rozsah podsítě virtuální sítě, která se používá pro Azure Database Migration Service.
* Vytvořte [pravidlo brány firewall](../azure-sql/database/firewall-configure.md) na úrovni serveru pro Azure Database for PostgreSQL cíl a umožněte Azure Database Migration Service přístup k cílovým databázím. Zadejte rozsah podsítě virtuální sítě, která se používá pro Azure Database Migration Service.
* [Povolte logickou replikaci](../postgresql/concepts-logical.md) ve zdroji Azure DB pro PostgreSQL. 
* Nastavte následující parametry serveru v instanci Azure Database for PostgreSQL používané jako zdroj:

  * max_replication_slots = [počet slotů], doporučuje se nastavit na **deset slotů**
  * max_wal_senders = [počet souběžných úloh] - parametr max_wal_senders nastaví počet souběžných úloh, které můžete spustit, doporučujeme nastavení na **10 úloh**

> [!NOTE]
> Výše uvedené parametry serveru jsou statické a budou vyžadovat restartování instance Azure Database for PostgreSQL, aby se projevily. Další informace o přepínání parametrů serveru najdete v tématu [Konfigurace parametrů Azure Database for PostgreSQL serveru](../postgresql/howto-configure-server-parameters-using-portal.md).

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
    pg_dump -o -h mypgserver-source.postgres.database.azure.com -U pguser@mypgserver-source -d dvdrental -s -O -x > dvdrentalSchema.sql
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

    Příklad:

    ```
    psql -h mypgserver-source.postgres.database.azure.com  -U pguser@mypgserver-source -d dvdrental citus < dvdrentalSchema.sql
    ```

4. Chcete-li extrahovat skript cizího klíče a přidat jej do cílového umístění (Azure Database for PostgreSQL), spusťte následující skript v PgAdmin nebo psql.

   > [!IMPORTANT]
   > Cizí klíče ve vašem schématu způsobí, že počáteční zatížení a nepřetržitá synchronizace migrace budou neúspěšné.

    ```
    SELECT Q.table_name
        ,CONCAT('ALTER TABLE ','"', table_schema,'"', '.','"', table_name ,'"', STRING_AGG(DISTINCT CONCAT(' DROP CONSTRAINT ','"', foreignkey,'"'), ','), ';') as DropQuery
            ,CONCAT('ALTER TABLE ','"', table_schema,'"', '.','"', table_name,'"', STRING_AGG(DISTINCT CONCAT(' ADD CONSTRAINT ','"', foreignkey,'"', ' FOREIGN KEY (','"', column_name,'"', ')', ' REFERENCES ','"', foreign_table_schema,'"', '.','"', foreign_table_name,'"', '(','"', foreign_column_name,'"', ')',' ON UPDATE ',update_rule,' ON DELETE ',delete_rule), ','), ';') as AddQuery
    FROM
        (SELECT
        S.table_schema,
        S.foreignkey,
        S.table_name,
        STRING_AGG(DISTINCT S.column_name, ',') AS column_name,
        S.foreign_table_schema,
        S.foreign_table_name,
        STRING_AGG(DISTINCT S.foreign_column_name, ',') AS foreign_column_name,
        S.update_rule,
        S.delete_rule
    FROM
        (SELECT DISTINCT
        tc.table_schema,
        tc.constraint_name AS foreignkey,
        tc.table_name,
        kcu.column_name,
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name,
        rc.update_rule AS update_rule,
        rc.delete_rule AS delete_rule
        FROM information_schema.table_constraints AS tc
        JOIN information_schema.key_column_usage AS kcu ON tc.constraint_name = kcu.constraint_name AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu ON ccu.constraint_name = tc.constraint_name AND ccu.table_schema = tc.table_schema
        JOIN information_schema.referential_constraints as rc ON rc.constraint_name = tc.constraint_name AND rc.constraint_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY'
        ) S
        GROUP BY S.table_schema, S.foreignkey, S.table_name, S.foreign_table_schema, S.foreign_table_name,S.update_rule,S.delete_rule
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

   ![Zobrazení předplatných na portálu](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-select-subscriptions.png)

2. Vyberte předplatné, ve kterém chcete vytvořit instanci Azure Database Migration Service a pak vyberte **poskytovatelé prostředků**.

    ![Zobrazení poskytovatelů prostředků](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-select-resource-provider.png)

3. Vyhledejte „migration“ a pak napravo od **Microsoft.DataMigration** vyberte **Zaregistrovat**.

    ![Registrace poskytovatele prostředků](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Vytvoření instance DMS

1. Na webu Azure Portal vyberte **+ Vytvořit prostředek**, vyhledejte „Azure Database Migration Service“ a pak v rozevíracím seznamu vyberte **Azure Database Migration Service**.

    ![Azure Marketplace](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-marketplace.png)

2. Na obrazovce **Azure Database Migration Service** vyberte **Vytvořit**.

    ![Vytvoření instance služby Azure Database Migration Service](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-create1.png)
  
3. Na obrazovce **vytvořit službu migrace** zadejte název, předplatné, novou nebo existující skupinu prostředků a umístění služby.

4. Vyberte existující virtuální síť nebo vytvořte novou.

    Virtuální síť poskytuje Azure Database Migration Service s přístupem ke zdrojovému serveru PostgreSQL a cílové instance Azure Database for PostgreSQL.

    Další informace o tom, jak vytvořit virtuální síť v Azure Portal, najdete v článku [vytvoření virtuální sítě pomocí Azure Portal](../virtual-network/quick-create-portal.md).

5. Vyberte cenovou úroveň.

    Další informace o nákladech a cenových úrovních najdete na [stránce s cenami](https://aka.ms/dms-pricing).

    ![Konfigurace nastavení instance služby Azure Database Migration Service](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-settings4.png)

6. Vyberte možnost **zkontrolovat + vytvořit** a vytvořte službu.

   Vytváření služby bude dokončeno během přibližně 10 až 15 minut.

## <a name="create-a-migration-project"></a>Vytvoření projektu migrace

Po vytvoření služby ji vyhledejte na webu Azure Portal, otevřete ji a pak vytvořte nový projekt migrace.

1. Na webu Azure Portal vyberte **Všechny služby**, vyhledejte „Azure Database Migration Service“ a pak vyberte **Služby Azure Database Migration Service**.

      ![Vyhledat všechny instance Azure Database Migration Service](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-search.png)

2. Na obrazovce **služby Azure Database Migration Services** vyhledejte název Azure Database Migration Service instance, kterou jste vytvořili, vyberte instanci a pak vyberte + **Nový projekt migrace**.

3. Na obrazovce **Nový projekt migrace** zadejte název projektu, v textovém poli **typ zdrojového serveru** vyberte možnost **PostgreSQL**, v textovém poli **typ cílového serveru** vyberte možnost **Azure Database for PostgreSQL**.
    > [!NOTE]
    > V **typu zdrojového serveru** vyberte **PostgreSQL** , i když je zdrojový server instancí **Azure Database for PostgreSQL** .  

4. V části **Zvolte typ aktivity** vyberte možnost **migrace online dat**.

    ![Vytvořit Azure Database Migration Service projekt](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-create-project.png)

    > [!NOTE]
    > Alternativně můžete zvolit **vytvořit projekt pouze** k vytvoření projektu migrace a provést migraci později.

5. Vyberte **Save (Uložit**), poznamenejte si požadavky na úspěšné použití Azure Database Migration Service k migraci dat a pak vyberte **vytvořit a spustit aktivitu**.

## <a name="specify-source-details"></a>Zadání podrobností o zdroji

1. Na obrazovce **Přidat podrobnosti zdroje** zadejte podrobnosti o připojení ke zdrojové instanci PostgreSQL.

    ![Obrazovka Přidat podrobnosti zdroje](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-add-source-details.png)

    > [!NOTE]
    > Podrobnosti, jako je například "název serveru", "port serveru", "název databáze" atd. najdete na portálu **Azure Database for PostgreSQL** .

2. Vyberte **Uložit**.

## <a name="specify-target-details"></a>Zadání podrobností o cíli

1. Na obrazovce **cílové podrobnosti** zadejte podrobnosti o připojení pro cílový server Citus (), což je předem zřízená instance (Citus), na kterou se nasadilo schéma zaplacení **DVD** , pomocí pg_dump.

    ![Obrazovka Podrobnosti cíle](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-add-target-details.png)

    > [!NOTE]
    > Můžete migrovat z instance Azure Database for PostgreSQL do jiné Azure Database for PostgreSQL instance jednoho serveru nebo na server Citus ().

2. Vyberte **Uložit** a pak na obrazovce **Mapovat na cílové databáze** namapujte zdrojovou a cílovou databázi pro migraci.

    Pokud cílová databáze obsahuje stejný název databáze jako zdrojová databáze, Azure Database Migration Service ve výchozím nastavení vybere cílovou databázi.

    ![Obrazovka mapování na cílové databáze](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-map-target-databases.png)

3. Vyberte **Uložit** a pak na obrazovce **Nastavení migrace** přijměte výchozí hodnoty.

    ![Obrazovka nastavení migrace](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-migration-settings.png)

4. Vyberte **Uložit**, na obrazovce **Shrnutí migrace** do textového pole **Název aktivity** zadejte název aktivity migrace a pak zkontrolujte souhrnné informace a ujistěte se, že podrobnosti zdroje a cíle odpovídají dříve zadaným informacím.

    ![Souhrnná obrazovka migrace](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-migration-summary.png)

## <a name="run-the-migration"></a>Spuštění migrace

* Vyberte **Spustit migraci**.

Zobrazí se okno aktivita migrace a **stav** aktivity by se měl aktualizovat, aby se zobrazila **v průběhu zálohování**. Při upgradu z Azure DB pro PostgreSQL 9,5 nebo 9,6 se může zobrazit následující chyba:

**Scénář ohlásil neznámou chybu. 28000: není k dispozici položka pg_hba. conf pro připojení replikace z hostitele "40.121.141.121", uživatel "SR"**

Důvodem je to, že PostgreSQL nemá příslušná oprávnění k vytváření požadovaných artefaktů logické replikace. Pokud chcete povolit požadovaná oprávnění, můžete postupovat takto:

1. Otevřete nastavení zabezpečení připojení pro zdrojový server Azure DB pro PostgreSQL, ze kterého se pokoušíte migrovat nebo upgradovat.
2. Přidejte nové pravidlo brány firewall s názvem končícím na "_replrule" a přidejte IP adresu z chybové zprávy do polí Počáteční IP adresa a koncová IP adresa. Pro výše uvedený příklad chyby –
> Název pravidla brány firewall = sr_replrule; Počáteční IP adresa = 40.121.141.121; Koncová IP adresa = 40.121.141.121

3. Klikněte na Uložit a nechte změnu dokončeno. 
4. Opakujte aktivitu DMS. 

## <a name="monitor-the-migration"></a>Monitorování migrace

1. Na obrazovce aktivity migrace vyberte **Aktualizovat** a aktualizujte zobrazení, dokud se **Stav** migrace nezmění na **Dokončeno**.

     ![Monitorovat proces migrace](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-monitor-migration.png)

2. Po dokončení migrace v části **název databáze** vyberte konkrétní databázi, abyste získali stav migrace **úplných operací načítání dat** a **přírůstkových synchronizací dat** .

   > [!NOTE]
   > **Úplné načtení dat** znázorňuje počáteční stav migrace zatížení, zatímco **přírůstkové synchronizace dat** zobrazuje stav Change Data Capture (CDC).

     ![Podrobnosti o plném zatížení dat](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-full-data-load-details.png)

     ![Podrobnosti o přírůstkové synchronizaci dat](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-incremental-data-sync-details.png)

## <a name="perform-migration-cutover"></a>Provedení přímé migrace

Po dokončení počátečního úplného načtení se databáze označí jako **Připraveno k přímé migraci**.

1. Jakmile budete připraveni dokončit migraci databází, vyberte **Spustit přímou migraci**.

2. Počkejte, než čítač **čekající změny** zobrazí **hodnotu 0** , aby se zajistilo, že všechny příchozí transakce do zdrojové databáze budou zastaveny, zaškrtněte políčko **Potvrdit** a pak vyberte **použít**.

    ![Dokončit obrazovku přímou migraci](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-complete-cutover.png)

3. Jakmile se zobrazí stav migrace databáze **dokončeno**, [znovu vytvořte sekvence](https://wiki.postgresql.org/wiki/Fixing_Sequences) (Pokud je k dispozici) a připojte své aplikace k nové cílové instanci Azure Database for PostgreSQL.
 
> [!NOTE]
> Azure Database Migration Service lze použít k provádění inovací hlavní verze s menším výpadkem v Azure Database for PostgreSQL jednom serveru. Nejprve nakonfigurujete cílovou databázi s požadovanou vyšší verzí PostgreSQL, nastavením sítě a parametry. Potom můžete zahájit migraci na cílové databáze pomocí postupu popsaného výše. Po přímou migraci k cílovému databázovému serveru můžete aktualizovat připojovací řetězec aplikace tak, aby odkazoval na cílový databázový server. 

## <a name="next-steps"></a>Další kroky

* Informace o známých problémech a omezeních při provádění online migrací do služby Azure Database for PostgreSQL najdete v článku [Známé problémy s online migracemi do služby Azure Database for PostgreSQLa jejich řešení](known-issues-azure-postgresql-online.md).
* Informace o službě Azure Database Migration Service najdete v článku [Co je Azure Database Migration Service?](./dms-overview.md).
* Informace o Azure Database for PostgreSQL najdete v článku [co je Azure Database for PostgreSQL?](../postgresql/overview.md).
