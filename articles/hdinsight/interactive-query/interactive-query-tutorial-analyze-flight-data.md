---
title: 'Kurz: Operace ETL s interaktivním dotazem – Azure HDInsight'
description: Kurz – Naučte se extrahovat data z nezpracované datové sady CSV. Transformujte jej pomocí interaktivního dotazu na HDInsight. Pak načtěte transformovaná data do databáze Azure SQL pomocí Apache Sqoop.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc
ms.date: 07/02/2019
ms.openlocfilehash: 7413a32fdddb579bad61c9cfe539be6aaeae9881
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313745"
---
# <a name="tutorial-extract-transform-and-load-data-using-interactive-query-in-azure-hdinsight"></a>Kurz: Extrahování, transformace a načítání dat pomocí interaktivního dotazu v Azure HDInsight

V tomto kurzu stáhnete nezpracovaný datový soubor CSV veřejně dostupných letových dat. Importujte je do úložiště clusteru HDInsight a pak transformujte data pomocí interaktivního dotazu v Azure HDInsight. Po transformaci dat načtete tato data do databáze Azure SQL pomocí [Apache Sqoop](https://sqoop.apache.org/).

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Stažení ukázkových údajů o letech
> * Nahrání dat do clusteru HDInsight
> * Transformace dat pomocí interaktivního dotazu
> * Vytvoření tabulky v databázi Azure SQL
> * Export dat do databáze Azure SQL pomocí Sqoop

## <a name="prerequisites"></a>Požadavky

* Cluster interaktivních dotazů na hdinsightu. Viz [Vytvoření clusterů Apache Hadoop pomocí portálu Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) a vyberte interaktivní **dotaz** pro **typ clusteru**.

* Databáze Azure SQL. Databázi Azure SQL použijete jako cílové úložiště dat. Pokud databázi SQL nemáte, přečtěte si téma [Vytvoření databáze Azure SQL na webu Azure Portal](/azure/sql-database/sql-database-single-database-get-started).

* Klient SSH. Další informace naleznete [v tématu Připojení k HDInsight (Apache Hadoop) pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="download-the-flight-data"></a>Stažení letových údajů

1. Přejděte na web [Research and Innovative Technology Administration, Bureau of Transportation Statistics](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time).

2. Na stránce zrušte zaškrtnutí všech polí a vyberte následující hodnoty:

   | Name (Název) | Hodnota |
   | --- | --- |
   | Filter Year (Filtr roku) |2019 |
   | Filter Period (Filtr období) |January (Leden) |
   | Fields (Pole) |`Year, FlightDate, Reporting_Airline, DOT_ID_Reporting_Airline, Flight_Number_Reporting_Airline, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay`. |

3. Vyberte **Download** (Stáhnout). Získáte soubor .zip s vybranými datovými poli.

## <a name="upload-data-to-an-hdinsight-cluster"></a>Nahrání dat do clusteru HDInsight

Do úložiště přidruženého ke clusteru HDInsight můžete data nahrát mnoha způsoby. V této části k nahrání dat použijete `scp`. Informace o dalších způsobech nahrání dat najdete v tématu [Nahrání dat do služby HDInsight](../hdinsight-upload-data.md).

1. Nahrajte soubor ZIP do hlavního uzlu clusteru HDInsight. Níže uvedený příkaz upravte nahrazením `FILENAME` názvem souboru `CLUSTERNAME` ZIP a názvem clusteru HDInsight. Potom otevřete příkazový řádek, nastavte pracovní adresář do umístění souboru a zadejte příkaz.

    ```cmd
    scp FILENAME.zip sshuser@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.zip
    ```

    Pokud se zobrazí výzva, zadejte ano nebo ne. Text není při psaní v okně viditelný.

2. Po dokončení nahrávání se ke clusteru připojte pomocí SSH. Upravte níže uvedený `CLUSTERNAME` příkaz nahrazením názvem clusteru HDInsight. Potom zadejte následující příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Nastavte proměnnou prostředí po navázání připojení SSH. `FILE_NAME`Nahraďte `SQL_DATABASE` `SQL_USER`, `SQL_SERVERNAME` `SQL_PASWORD` , , , a s příslušnými hodnotami. Pak zadejte příkaz:

    ```bash
    export FILENAME=FILE_NAME
    export SQLSERVERNAME=SQL_SERVERNAME
    export DATABASE=SQL_DATABASE
    export SQLUSER=SQL_USER
    export SQLPASWORD='SQL_PASWORD'
    ```

4. Rozbalte soubor ZIP zadáním následujícího příkazu:

    ```bash
    unzip $FILENAME.zip
    ```

5. Vytvořte adresář v úložišti HDInsight a potom zkopírujte soubor .csv do adresáře zadáním následujícího příkazu:

    ```bash
    hdfs dfs -mkdir -p /tutorials/flightdelays/data
    hdfs dfs -put $FILENAME.csv /tutorials/flightdelays/data/
    ```

## <a name="transform-data-using-a-hive-query"></a>Transformace dat pomocí dotazu Hive

Úlohu Hive můžete v clusteru HDInsight spustit různými způsoby. V této části použijete [Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline%E2%80%93CommandLineShell) ke spuštění úlohy Hive. Informace o dalších metodách spuštění úlohy Hive naleznete v [tématu Použití Apache Hive na HDInsight](../hadoop/hdinsight-use-hive.md).

V rámci úlohy Hive provedete import dat ze souboru .csv do tabulky Hive s názvem **Delays** (Zpoždění).

1. Z výzvy SSH, kterou již máte pro cluster HDInsight, vytvořte a upravte nový soubor s názvem **flightdelays.hql**:

    ```bash
    nano flightdelays.hql
    ```

2. Jako obsah souboru použijte následující text:

    ```hiveql
    DROP TABLE delays_raw;
    -- Creates an external table over the csv file
    CREATE EXTERNAL TABLE delays_raw (
        YEAR string,
        FL_DATE string,
        UNIQUE_CARRIER string,
        CARRIER string,
        FL_NUM string,
        ORIGIN_AIRPORT_ID string,
        ORIGIN string,
        ORIGIN_CITY_NAME string,
        ORIGIN_CITY_NAME_TEMP string,
        ORIGIN_STATE_ABR string,
        DEST_AIRPORT_ID string,
        DEST string,
        DEST_CITY_NAME string,
        DEST_CITY_NAME_TEMP string,
        DEST_STATE_ABR string,
        DEP_DELAY_NEW float,
        ARR_DELAY_NEW float,
        CARRIER_DELAY float,
        WEATHER_DELAY float,
        NAS_DELAY float,
        SECURITY_DELAY float,
        LATE_AIRCRAFT_DELAY float)
    -- The following lines describe the format and location of the file
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE
    LOCATION '/tutorials/flightdelays/data';

    -- Drop the delays table if it exists
    DROP TABLE delays;
    -- Create the delays table and populate it with data
    -- pulled in from the CSV file (via the external table defined previously)
    CREATE TABLE delays AS
    SELECT YEAR AS year,
        FL_DATE AS flight_date,
        substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier,
        substring(CARRIER, 2, length(CARRIER) -1) AS carrier,
        substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num,
        ORIGIN_AIRPORT_ID AS origin_airport_id,
        substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code,
        substring(ORIGIN_CITY_NAME, 2) AS origin_city_name,
        substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr,
        DEST_AIRPORT_ID AS dest_airport_id,
        substring(DEST, 2, length(DEST) -1) AS dest_airport_code,
        substring(DEST_CITY_NAME,2) AS dest_city_name,
        substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr,
        DEP_DELAY_NEW AS dep_delay_new,
        ARR_DELAY_NEW AS arr_delay_new,
        CARRIER_DELAY AS carrier_delay,
        WEATHER_DELAY AS weather_delay,
        NAS_DELAY AS nas_delay,
        SECURITY_DELAY AS security_delay,
        LATE_AIRCRAFT_DELAY AS late_aircraft_delay
    FROM delays_raw;
    ```

3. Soubor uložíte stisknutím **kláves Ctrl + X**a potom **klávesou Y**a zadejte.

4. Spusťte Hive a soubor **flightdelays.hql** pomocí následujícího příkazu:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
    ```

5. Po dokončení skriptu **flightdelays.hql** pomocí následujícího příkazu otevřete interaktivní relaci Beeline:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

6. Po zobrazení příkazového řádku `jdbc:hive2://localhost:10001/>` pomocí následujícího dotazu načtěte data z importovaných dat o zpožděných letech:

    ```hiveql
    INSERT OVERWRITE DIRECTORY '/tutorials/flightdelays/output'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    SELECT regexp_replace(origin_city_name, '''', ''),
        avg(weather_delay)
    FROM delays
    WHERE weather_delay IS NOT NULL
    GROUP BY origin_city_name;
    ```

    Tento dotaz načte seznam měst, ve kterých došlo ke zpožděním kvůli nepřízni počasí, společně s průměrnou délkou zpoždění a uloží ho do umístění `/tutorials/flightdelays/output`. Později z tohoto umístění data načte Sqoop a exportuje je do služby Azure SQL Database.

7. Beeline ukončíte zadáním `!quit` na příkazovém řádku.

## <a name="create-a-sql-database-table"></a>Vytvoření tabulky databáze SQL

Existuje mnoho způsobů, jak se připojit ke službě SQL Database a vytvořit tabulku. V následujících krocích se používá [FreeTDS](http://www.freetds.org/) z clusteru HDInsight.

1. Chcete-li nainstalovat FreeTDS, použijte následující příkaz z otevřeného připojení SSH ke clusteru:

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. Po dokončení instalace se pomocí následujícího příkazu připojte k serveru služby SQL Database.

    ```bash
    TDSVER=8.0 tsql -H $SQLSERVERNAME.database.windows.net -U $SQLUSER -p 1433 -D $DATABASE -P $SQLPASWORD
    ```

    Zobrazí se výstup podobný následujícímu textu:

    ```
    locale is "en_US.UTF-8"
    locale charset is "UTF-8"
    using default charset "UTF-8"
    Default database being set to <yourdatabase>
    1>
    ```

3. Na příkazovém řádku `1>` zadejte následující řádky:

    ```hiveql
    CREATE TABLE [dbo].[delays](
    [origin_city_name] [nvarchar](50) NOT NULL,
    [weather_delay] float,
    CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED
    ([origin_city_name] ASC))
    GO
    ```

    Po zadání příkazu `GO` se vyhodnotí předchozí příkazy. Tento příkaz vytvoří tabulku s názvem **zpoždění**s seskupeným indexem.

    K ověření vytvoření tabulky použijte následující dotaz:

    ```hiveql
    SELECT * FROM information_schema.tables
    GO
    ```

    Výstup se bude podobat následujícímu:

    ```
    TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
    databaseName       dbo             delays        BASE TABLE
    ```

4. Zadáním `exit` na příkazovém řádku `1>` ukončete nástroj tsql.

## <a name="export-data-to-sql-database-using-apache-sqoop"></a>Export dat do databáze SQL pomocí Apache Sqoop

V předchozích částech jste zkopírovali transformovaná data do umístění `/tutorials/flightdelays/output`. V této části použijete Sqoop k exportu dat z `/tutorials/flightdelays/output` do tabulky, kterou jste vytvořili v databázi Azure SQL.

1. Ověřte, zda sqoop může zobrazit vaši databázi SQL zadáním následujícího příkazu:

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://$SQLSERVERNAME.database.windows.net:1433 --username $SQLUSER --password $SQLPASWORD
    ```

    Tento příkaz vrátí seznam databází, včetně databáze, ve `delays` které jste tabulku vytvořili dříve.

2. Exportujte `/tutorials/flightdelays/output` data `delays` z tabulky zadáním následujícího příkazu:

    ```bash
    sqoop export --connect "jdbc:sqlserver://$SQLSERVERNAME.database.windows.net:1433;database=$DATABASE" --username $SQLUSER --password $SQLPASWORD --table 'delays' --export-dir '/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
    ```

    Sqoop se připojí k databázi, která obsahuje `delays` `/tutorials/flightdelays/output` tabulku, `delays` a exportuje data z adresáře do tabulky.

3. Po dokončení příkazu sqoop se pomocí nástroje tsql připojte k databázi zadáním následujícího příkazu:

    ```bash
    TDSVER=8.0 tsql -H $SQLSERVERNAME.database.windows.net -U $SQLUSER -p 1433 -D $DATABASE -P $SQLPASWORD
    ```

    Pomocí následujících příkazů ověřte, že se data exportovala do tabulky delays:

    ```sql
    SELECT * FROM delays
    GO
    ```

    Měl by se zobrazit výpis dat v tabulce. Tabulka obsahuje název města a průměrnou délku zpoždění letu pro příslušné město.

    Zadáním `exit` ukončete nástroj tsql.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení kurzu můžete cluster odstranit. S HDInsight, vaše data jsou uloženy ve službě Azure Storage, takže můžete bezpečně odstranit clusteru, když není v provozu. Účtuje se vám také cluster HDInsight, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster jsou mnohonásobně vyšší než poplatky za úložiště, má ekonomické smysl odstranit clustery, když nejsou používány.

Pokud chcete odstranit cluster, přečtěte si informace [o odstranění clusteru HDInsight pomocí prohlížeče, PowerShellu nebo rozhraní příkazového příkazu k Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vzali nezpracovaný datový soubor CSV, importovali ho do úložiště clusteru HDInsight a pak jste data transformovali pomocí interaktivního dotazu v Azure HDInsight.  Přejdete k dalšímu kurzu, kde se dozvíte o konektoru Apache Hive Warehouse Connector.

> [!div class="nextstepaction"]
> [Integrace Apache Spark a Apache Hive s konektorem Hive Warehouse Connector](./apache-hive-warehouse-connector.md)
