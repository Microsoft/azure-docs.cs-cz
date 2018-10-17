---
title: Provedení online migrace PostgreSQL do služby Azure Database for MySQL pomocí služby Azure Database Migration Service | Microsoft Docs
description: Zjistěte, jak pomocí služby Azure Database Migration Service provést online migraci z místního PostgreSQL do služby Azure Database for PostgreSQL.
services: dms
author: HJToland3
ms.author: scphang
manager: craigg
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 09/26/2018
ms.openlocfilehash: 51103f6661be22e355aafe01783af5afddd51f54
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47182463"
---
# <a name="migrate-postgresql-to-azure-database-for-postgresql-online-using-dms"></a>Migrace PostgreSQL do Azure Database for PostgreSQL online pomocí DMS
Pomocí služby Azure Database Migration Service můžete migrovat databáze z místní instance PostgreSQL do služby [Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/) s minimálními výpadky. Jinými slovy, můžete dosáhnout migrace s minimálními výpadky aplikace. V tomto kurzu provedete migraci ukázkové databáze **DVD Rental** z místní instance PostgreSQL verze 9.6 do služby Azure Database for PostgreSQL pomocí aktivity online migrace ve službě Azure Database Migration Service.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Migrujte ukázkové schéma pomocí nástroje pgdump.
> * Vytvoření instance služby Azure Database Migration Service
> * Vytvoření projektu migrace pomocí služby Azure Database Migration Service
> * Spuštění migrace
> * Monitorování migrace

> [!IMPORTANT]
> Aby migrace proběhla optimálním způsobem, doporučuje Microsoft vytvořit instanci služby Azure Database Migration Service ve stejné oblasti Azure, ve které je cílová databáze. Přenášení dat mezi oblastmi geografickými lokalitami může zpomalit proces migrace a způsobit chyby.

## <a name="prerequisites"></a>Požadavky
Pro absolvování tohoto kurzu je potřeba provést následující:

- Stáhněte a nainstalujte [PostgreSQL Community Edition](https://www.postgresql.org/download/) verze 9.5, 9.6 nebo 10.3. Zdrojový PostgreSQL Server musí mít verzi 9.5.11, 9.6.7, 10.3 nebo novější. Další informace najdete v článku [podporované verze databáze PostgreSQL](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions).

    Kromě toho místní verze PostgreSQL musí odpovídat Azure Database for PostgreSQL. Například PostgreSQL verze 9.5.11.5 je možné migrovat pouze do služby Azure Database for PostgreSQL verze 9.5.11 a ne verze 9.6.7.

- [Vytvořte instanci v Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal).  
- Vytvořte pro službu Azure Database Migration Service virtuální síť s použitím modelu nasazení Azure Resource Manager, který poskytuje možnosti připojení typu Site-to-Site k místním zdrojovým serverům prostřednictvím [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo sítě [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Zajistěte, aby pravidla skupiny zabezpečení virtuální sítě Azure neblokovala následující komunikační porty: 443, 53, 9354, 445 a 12000. Další podrobnosti o filtrování provozu pomocí skupiny zabezpečení virtuální sítě Azure najdete v článku [Filtrování provozu sítě s použitím skupin zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm).
- Nakonfigurujte bránu [Windows Firewall pro přístup k databázovému stroji](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Otevřete bránu Windows Firewall a povolte službě Azure Database Migration Service přístup ke zdrojovému serveru PostgreSQL, který ve výchozím nastavení probíhá přes port TCP 5432.
- Pokud před zdrojovými databázemi používáte zařízení brány firewall, možná bude potřeba přidat pravidla brány firewall, která službě Azure Database Migration Service povolí přístup ke zdrojovým databázím za účelem migrace.
- Vytvořte pro službu Azure Database for PostgreSQL [pravidlo brány firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) na úrovni serveru, které službě Azure Database Migration Service povolí přístup k cílovým databázím. Zadejte rozsah podsítí virtuální sítě použité pro službu Azure Database Migration Service.
- Existují dvě metody pro vyvolání rozhraní příkazového řádku:
    - V nabídce v pravém horním rohu webu Azure Portal, zvolte tlačítko Cloud Shell:
 
       ![Tlačítko Cloud Shell na webu Azure Portal](media\tutorial-postgresql-to-azure-postgresql-online\cloud-shell-button.png)
 
    - Nainstalujte a spusťte místně rozhraní příkazového řádku. CLI 2.0 je nástroj příkazového řádku pro správu prostředků Azure.
     
       Pokyny ke stažení CLI najdete v článku [Instalace Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Tento článek také uvádí platformy, které podporují CLI 2.0.
         
       Pokud chcete nastavit subsystém Windows pro Linux (WSL), postupujte podle pokynů [Průvodce instalací systému Windows 10](https://docs.microsoft.com/windows/wsl/install-win10).
 
- Povolte logickou repliku v souboru postgresql.config a nastavte následující parametry:
    - wal_level = **logical**
    - max_replication_slots = [počet slotů], doporučujeme nastavení na **5 slotů**
    - max_wal_senders = [počet souběžných úloh] - parametr max_wal_senders nastaví počet souběžných úloh, které můžete spustit, doporučujeme nastavení na **10 úloh**

## <a name="migrate-the-sample-schema"></a>Migrace ukázkového schématu
K dokončení všech databázových objektů, jako jsou schémata tabulek, indexy a uložené procedury, potřebujeme extrahovat schéma ze zdrojové databáze a použít ho na databázi.

1. Pomocí příkazu pg_dump -s můžete vytvořit soubor schématu s výpisem paměti pro databázi. 

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Chcete-li například vypsat schéma souboru databáze dvdrental:
    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s  > dvdrentalSchema.sql
    ```
 
    Další informace o používání nástroje pg_dump, viz příklady v kurzu [pg-dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES).
 
2. Vytvořte prázdnou databázi v cílovém prostředí, což je Azure Database for PostgreSQL.

    Podrobnosti o tom, jak se připojit a vytvořit databázi, najdete v článku [Vytvoření serveru Azure Database for PostgreSQL na webu Azure Portal](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal).

3. Importujte schéma do cílové databáze, kterou jste vytvořili pomocí obnovení souboru se schématem výpisu paměti.

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql 
    ```

    Příklad:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental < dvdrentalSchema.sql
    ```
4. Pokud vaše schéma obsahuje cizí klíče, počáteční načtení a průběžná synchronizace migrace selžou. Spusťte následující skript v nástroji PgAdmin nebo psql, abyste extrahovali odstraněný skript cizího klíče a přidali skript cizí klíče do cílového umístění (Azure Database for PostgreSQL).
    
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

    Spusťte skript pro odstranění cizího klíče (druhý sloupec) ve výsledku dotazu odstraňte cizí klíč.

5.  Triggery v datech (trigger vložení nebo aktualizace) budou dříve než u replikovaných dat ze zdroje vynucovat integritu dat v cíli. Během migrace se doporučuje zakázat triggery ve všech tabulkách **v cíli** a znovu je povolit až po dokončení migrace.

    Pokud chcete zakázat triggery v cílové databázi, použijte následující příkaz:

    ```
    select concat ('alter table ', event_object_table, ' disable trigger ', trigger_name)
    from information_schema.triggers;
    ```

6.  Pokud je v některé tabulce datový typ ENUM, doporučujeme ho v cílové tabulce dočasně aktualizovat na datový typ „odlišující se znaky“. Po dokončení replikace dat vraťte datový typ na ENUM.

## <a name="provisioning-an-instance-of-dms-using-the-cli"></a>Zřízení instance DMS pomocí rozhraní příkazového řádku

1.  Nainstalujte rozšíření synchronizace dms:
    - K Azure se přihlásíte spuštěním následujícího příkazu:        
        ```
        az login
        ```

    - Po zobrazení výzvy otevřete webový prohlížeč a zadejte kód pro ověření vašeho zařízení. Postupujte podle uvedených pokynů.
    - Přidání rozšíření dms:
        - K zobrazení seznamu dostupných rozšíření spusťte následující příkaz:

            ```
            az extension list-available –otable
            ```
        - Rozšíření nainstalujete spuštěním tohoto příkazu:

            ```
            az extension add –n dms-preview
            ```

    - Chcete-li ověřit, že máte správně nainstalované rozšíření dms, spusťte následující příkaz:
 
        ```
        az extension list -otable
        ```
        Měl by se zobrazit následující výstup:     

        ```
        ExtensionType    Name
        ---------------  ------
        whl              dms
        ```

    - Kdykoli spuštěním zobrazíte všechny příkazy podporované v DMS:
        ```
        az dms -h
        ```
    - Pokud máte několik předplatných Azure, spuštěním následujícího příkazu nastavíte předplatné, které chcete použít k zřízení instance služby DMS.

         ```
        az account set -s 97181df2-909d-420b-ab93-1bff15acb6b7
         ```

2.  Zřízení instance DMS spuštěním následujícího příkazu:

    ```
    az dms create -l [location] -n <newServiceName> -g <yourResourceGroupName> --sku-name BusinessCritical_4vCores --subnet/subscriptions/{vnet subscription id}/resourceGroups/{vnet resource group}/providers/Microsoft.Network/virtualNetworks/{vnet name}/subnets/{subnet name} –tags tagName1=tagValue1 tagWithNoValue
    ```

    Například následující příkaz vytvoří službu v:
    - Umístění: USA – východ 2
    - Předplatné: 97181df2-909d-420b-ab93-1bff15acb6b7
    - Název skupina prostředků: PostgresDemo
    - Název služby DMS: PostgresCLI

    ```
    az dms create -l eastus2 -g PostgresDemo -n PostgresCLI --subnet /subscriptions/97181df2-909d-420b-ab93-1bff15acb6b7/resourceGroups/ERNetwork/providers/Microsoft.Network/virtualNetworks/AzureDMS-CORP-USC-VNET-5044/subnets/Subnet-1 --sku-name BusinessCritical_4vCores
    ```
    Vytvoření instance služby DMS trvá asi 10 až 12 minut.

3. Pokud chcete zjistit IP adresu DMS agenta tak, že ho přidáte do souboru Postgres pg_hba.conf, spusťte následující příkaz:

    ```
    az network nic list -g <ResourceGroupName>--query '[].ipConfigurations | [].privateIpAddress'
    ```
    Příklad:

    ```
    az network nic list -g PostgresDemo --query '[].ipConfigurations | [].privateIpAddress'
    ```

    Zobrazený výsledek by měl vypadat přibližně jako tato adresa: 

    ```
    [
      "172.16.136.18"
    ]
    ```

4. Přidejte IP adresu agenta DMS do souboru Postgres pg_hba.conf.
    - Po dokončení zřízení v DMS si poznamenejte IP adresu DMS.
    - Přidejte ke zdroji IP adresu do souboru pg_hba.conf podobně jako následující položky:

        ```
        host    all     all     172.16.136.18/10    md5
        host    replication     postgres    172.16.136.18/10    md5
        ```

5. Dále spuštěním následujícího příkazu vytvořte projekt migrace PostgreSQL:
    
    ```
    az dms project create -l <location> -g <ResourceGroupName> --service-name <yourServiceName> --source-platform PostgreSQL --target-platform AzureDbforPostgreSQL -n <newProjectName>
    ```
    Například následující příkaz vytvoří projekt s použitím těchto parametrů:

      - Umístění: USA – středozápad
      - Název skupina prostředků: PostgresDemo
      - Název služby: PostgresCLI
      - Název projektu: PGMigration
      - Zdrojová platforma: PostgreSQL
      - Cílová platforma: AzureDbForPostgreSql
 
    ```
    az dms project create -l eastus2 -n PGMigration -g PostgresDemo --service-name PostgresCLI --source-platform PostgreSQL --target-platform AzureDbForPostgreSql
    ```
                
6. Vytvoření úlohy migrace PostgreSQL pomocí následujících kroků.

    Tento krok zahrnuje použití zdrojové IP adresy, ID uživatele a hesla, cílové IP adresy, ID uživatele, hesla a typ úlohy k navázání možnosti připojení.

    - Pokud chcete zobrazit úplný seznam možností, spusťte příkaz:
        ```
        az dms project task create -h
        ```

        Jak u zdroje, tak u cíle připojení vstupní parametr odkazuje na soubor json, který má seznam objektů.
 
        Formát objektu připojení JSON pro připojení PostgreSQL.
        
        ```
        {
                    "userName": "user name",    // if this is missing or null, you will be prompted
                    "password": null,           // if this is missing or null (highly recommended) you will
                be prompted
                    "serverName": "server name",
                    "databaseName": "database name", // if this is missing, it will default to the 'postgres'
                server
                    "port": 5432                // if this is missing, it will default to 5432
                }
        ```

    - Existuje také volitelný databázový soubor json, který obsahuje seznam objektů json. Pro PostgreSQL formát objektu JSON možností databáze je zobrazen níže:

        ```
        [
            {
                "name": "source database",
                "target_database_name": "target database",
            },
            ...n
        ]
        ```

    - Vytvořte soubor json v aplikaci Poznámkový blok, zkopírujte následující příkazy a vložte je do souboru a pak soubor uložte v umístění C:\DMS\source.json.
         ```
        {
                    "userName": "postgres",    
                    "password": null,           
                be prompted
                    "serverName": "13.51.14.222",
                    "databaseName": "dvdrental", 
                    "port": 5432                
                }
         ```
    - Vytvořte jiný soubor s názvem target.json a uložte ho jako C:\DMS\target.json. Zahrňte následující příkazy:
        ```
        {
                "userName": " dms@builddemotarget",    
                "password": null,           
                "serverName": " builddemotarget.postgres.database.azure.com",
                "databaseName": "inventory", 
                "port": 5432                
            }
        ```
    - Vytvořte soubor json možností databáze, který obsahuje seznam inventáře jako databázi, kterou chcete migrovat:
        ``` 
        [
            {
                "name": "dvdrental",
                "target_database_name": "dvdrental",
            }
        ]
        ```
    - Spusťte následující příkaz, který přijímá zdroj, cíl a soubory json možností databáze.

        ``` 
        az dms project task create -g PostgresDemo --project-name PGMigration --source-platform postgresql --target-platform azuredbforpostgresql --source-connection-json c:\DMS\source.json --database-options-json C:\DMS\option.json --service-name PostgresCLI --target-connection-json c:\DMS\target.json –task-type OnlineMigration -n runnowtask    
        ``` 

    V tomto okamžiku jste úspěšně odeslali úlohu migrace.

7.  Chcete-li zobrazit průběh úlohy, spusťte následující příkaz:

    ```
    az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
    ```

    NEBO

     ```
    az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask --expand output
     ```

8. Můžete také dotázat stav migrace z výstupu rozbalení:

    ```
    az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask --expand output --query 'properties.output[].migrationState | [0]' "READY_TO_COMPLETE"
    ```

## <a name="understanding-migration-task-status"></a>Porozumění stavu úlohy migrace
Ve výstupním souboru existuje několik parametrů, které označují průběh migrace. Viz například následující výstupní soubor:

    ```
    "output": [                                 Database Level
          {
            "appliedChanges": 0,        //Total incremental sync applied after full load
            "cdcDeleteCounter": 0       // Total delete operation  applied after full load
            "cdcInsertCounter": 0,      // Total insert operation applied after full load
            "cdcUpdateCounter": 0,      // Total update operation applied after full load
            "databaseName": "inventory",
            "endedOn": null,
            "fullLoadCompletedTables": 2,   //Number of tables completed full load
            "fullLoadErroredTables": 0, //Number of tables that contain migration error
            "fullLoadLoadingTables": 0, //Number of tables that are in loading status
            "fullLoadQueuedTables": 0,  //Number of tables that are in queued status
            "id": "db|inventory",
            "incomingChanges": 0,       //Number of changes after full load
            "initializationCompleted": true,
            "latency": 0,
            "migrationState": "READY_TO_COMPLETE",  //Status of migration task. READY_TO_COMPLETE means the database is ready for cutover
            "resultType": "DatabaseLevelOutput",
            "startedOn": "2018-07-05T23:36:02.27839+00:00"
          },
          {
            "databaseCount": 1,
            "endedOn": null,
            "id": "dd27aa3a-ed71-4bff-ab34-77db4261101c",
            "resultType": "MigrationLevelOutput",
            "sourceServer": "138.91.123.10",
            "sourceVersion": "PostgreSQL",
            "startedOn": "2018-07-05T23:36:02.27839+00:00",
            "state": "PENDING",
            "targetServer": "builddemotarget.postgres.database.azure.com",
            "targetVersion": "Azure Database for PostgreSQL"
          },
          {                                     Table 1
            "cdcDeleteCounter": 0,
            "cdcInsertCounter": 0,
            "cdcUpdateCounter": 0,
            "dataErrorsCount": 0,
            "databaseName": "inventory",
            "fullLoadEndedOn": "2018-07-05T23:36:20.740701+00:00",  //Full load completed time
            "fullLoadEstFinishTime": "1970-01-01T00:00:00+00:00",
            "fullLoadStartedOn": "2018-07-05T23:36:15.864552+00:00",    //Full load started time
            "fullLoadTotalRows": 10,                    //Number of rows loaded in full load
            "fullLoadTotalVolumeBytes": 7056,               //Volume in Bytes in full load
            "id": "or|inventory|public|actor",          
            "lastModifiedTime": "2018-07-05T23:36:16.880174+00:00",
            "resultType": "TableLevelOutput",
            "state": "COMPLETED",                   //State of migration for this table
            "tableName": "public.catalog",              //Table name
            "totalChangesApplied": 0                //Total sync changes that applied after full load
          },
          {                                     Table 2
            "cdcDeleteCounter": 0,
            "cdcInsertCounter": 50,
            "cdcUpdateCounter": 0,
            "dataErrorsCount": 0,
            "databaseName": "inventory",
            "fullLoadEndedOn": "2018-07-05T23:36:23.963138+00:00",
            "fullLoadEstFinishTime": "1970-01-01T00:00:00+00:00",
            "fullLoadStartedOn": "2018-07-05T23:36:19.302013+00:00",
            "fullLoadTotalRows": 112,
            "fullLoadTotalVolumeBytes": 46592,
            "id": "or|inventory|public|address",
            "lastModifiedTime": "2018-07-05T23:36:20.308646+00:00",
            "resultType": "TableLevelOutput",
            "state": "COMPLETED",
            "tableName": "public.orders",
            "totalChangesApplied": 0
          }
        ],                          DMS migration task state
        "state": "Running", //Migration task state – Running means it is still listening to any changes that might come in                  
        "taskType": null
      },
      "resourceGroup": "PostgresDemo",
      "type": "Microsoft.DataMigration/services/projects/tasks"
    ```

## <a name="cutover-migration-task"></a>Úloha přímé migrace
Databáze je připravená k přímé migraci po úplném načtení. V závislosti na tom, jak vytížený je zdrojový server s novými příchozími transakcemi, může být úloha DMS stále provedena po dokončení úplného načtení.

Aby byla všechna data zachycena, ověřte počet řádků mezi zdrojovými a cílovými databázemi. Můžete například použít následující příkaz:

```
"migrationState": "READY_TO_COMPLETE", //Status of migration task. READY_TO_COMPLETE means database is ready for cutover
 "incomingChanges": 0,  //continue to check for a period of 5-10 minutes to make sure no new incoming changes that need to be applied to the target server
   "fullLoadTotalRows": 10, //full load for table 1
    "cdcDeleteCounter": 0,  //delete, insert and update counter on incremental sync after full load
    "cdcInsertCounter": 50,
    "cdcUpdateCounter": 0,
     "fullLoadTotalRows": 112,  //full load for table 2
```

1.  Pomocí následujícího příkazu proveďte úlohu přímé migrace databáze:

    ```
    az dms project task cutover -h
    ```

    Příklad:

    ```
    az dms project task cutover --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask  --database-name Inventory
    ```

2.  Chcete-li sledovat průběh přímé migrace, spusťte následující příkaz:

    ```
    az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
    ```

## <a name="service-project-task-cleanup"></a>Služba projekt, úloha vyčištění
Pokud potřebujete zrušit nebo odstranit všechny úlohy, projekt nebo služby DMS, proveďte zrušení v následujícím pořadí:
- Zrušte jakoukoli běžící úlohu.
- Odstraňte úlohu.
- Odstraňte projekt. 
- Odstraňte službu DMS.

1.  Chcete-li zrušit běžící úlohu, použijte následující příkaz:
    ```
    az dms project task cancel --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
     ```

2.  Chcete-li odstranit běžící úlohu, použijte následující příkaz:
    ```
    az dms project task delete --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
    ```

3.  Chcete-li zrušit běžící projekt, použijte následující příkaz:
     ```
    az dms project task cancel -n runnowtask --project-name PGMigration -g PostgresDemo --service-name PostgresCLI
     ```

4.  Chcete-li odstranit běžící projekt, použijte následující příkaz:
    ```
    az dms project task delete -n runnowtask --project-name PGMigration -g PostgresDemo --service-name PostgresCLI
    ```

5.  Chcete-li odstranit službu DMS, použijte následující příkaz:

     ```
    az dms delete -g ProgresDemo -n PostgresCLI
     ```

## <a name="next-steps"></a>Další kroky
- Informace o známých problémech a omezeních při provádění online migrací do služby Azure Database for PostgreSQL najdete v článku [Známé problémy s online migracemi do služby Azure Database for PostgreSQLa jejich řešení](known-issues-azure-postgresql-online.md).
- Informace o službě Azure Database Migration Service najdete v článku [Co je Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
- Informace o službě Azure Database for PostgreSQL najdete v článku [Co je Azure Database for PostgreSQL?](https://docs.microsoft.com/azure/postgresql/overview).