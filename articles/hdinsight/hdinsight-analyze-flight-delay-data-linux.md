---
title: 'Kurz: Provedení extrakce, transformace a načítání (ETL) operací s použitím Hive v HDInsight – Azure '
description: Zjistěte, jak extrahovat data z nezpracované datové sady CSV, transformovat je pomocí Hivu ve službě HDInsight a pak transformovaná data načíst do databáze Azure SQL pomocí Apache Sqoopu.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.date: 05/15/2019
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive,mvc
ms.openlocfilehash: ac1ae7ed761099a19accf55e9e4dab61193c2de7
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967799"
---
# <a name="tutorial-extract-transform-and-load-data-using-apache-hive-in-azure-hdinsight"></a>Kurz: Extrakce, transformace a načítání dat pomocí Apache Hive v Azure HDInsight

V tomto kurzu trvat nezpracovaný datový soubor CSV veřejně dostupné zapisovači letových údajů, importujte ho do úložiště clusteru HDInsight a pak transformuje data pomocí [Apache Hive](https://hive.apache.org/) v Azure HDInsight. Po transformaci dat, načtete data do databáze Azure SQL pomocí [Apache Sqoop](https://sqoop.apache.org/).

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Stažení ukázkových údajů o letech
> * Nahrání dat do clusteru HDInsight
> * Transformace dat pomocí Hivu
> * Vytvoření tabulky ve službě Azure SQL database
> * Použít Sqoop k exportování dat do Azure SQL database

Následující obrázek ukazuje obvyklý běh aplikace ETL.

![Operace ETL s využitím Apache Hivu ve službě Azure HDInsight](./media/hdinsight-analyze-flight-delay-data-linux/hdinsight-etl-architecture.png "Operace ETL s využitím Apache Hivu ve službě Azure HDInsight")

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* Cluster Apache Hadoop v HDInsight. Zobrazit [Začínáme s HDInsight v Linuxu](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* Azure SQL Database. Databázi Azure SQL použijete jako cílové úložiště dat. Pokud databázi SQL nemáte, přečtěte si téma [Vytvoření databáze Azure SQL na webu Azure Portal](../sql-database/sql-database-single-database-get-started.md).

* Klient SSH. Další informace najdete v tématu [připojení k HDInsight (Apache Hadoop) pomocí protokolu SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="download-the-flight-data"></a>Stažení letových údajů

1. Přejděte do [výzkumu a inovativní technologie správy, Bureau of Transportation statistiky](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time).

2. Na stránce zrušte zaškrtnutí všech polí a pak vyberte následující hodnoty:

   | Název | Hodnota |
   | --- | --- |
   | Filter Year (Filtr roku) |2019 |
   | Filter Period (Filtr období) |January (Leden) |
   | Fields (Pole) |Rok, FlightDate, Reporting_Airline, DOT_ID_Reporting_Airline, Flight_Number_Reporting_Airline, OriginAirportID, původu, OriginCityName, OriginState, DestAirportID, cíl, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. |

3. Vyberte **Download** (Stáhnout). Získáte soubor .zip s vybranými datovými poli.

## <a name="upload-data-to-an-hdinsight-cluster"></a>Nahrání dat do clusteru HDInsight

Do úložiště přidruženého ke clusteru HDInsight můžete data nahrát mnoha způsoby. V této části k nahrání dat použijete `scp`. Informace o dalších způsobech nahrání dat najdete v tématu [Nahrání dat do služby HDInsight](hdinsight-upload-data.md).

1. Nahrajte soubor ZIP do hlavního uzlu clusteru HDInsight. Upravte následující příkaz tak, že nahradíte `FILENAME` s názvem souboru ZIP a `CLUSTERNAME` s názvem clusteru HDInsight. Pak otevřete příkazový řádek, nastavte pracovní adresář na umístění souboru a potom zadejte příkaz.

    ```cmd
    scp FILENAME.zip sshuser@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.zip
    ```

2. Po dokončení nahrávání se ke clusteru připojte pomocí SSH. Upravte následující příkaz tak, že nahradíte `CLUSTERNAME` s názvem clusteru HDInsight. Potom zadejte následující příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Nastavte proměnnou prostředí po vytvoření připojení SSH. Nahraďte `FILE_NAME`, `SQL_SERVERNAME`, `SQL_DATABASE`, `SQL_USER`, a `SQL_PASWORD` příslušnými hodnotami. Potom zadejte příkaz:

    ```bash
    export FILENAME=FILE_NAME
    export SQLSERVERNAME=SQL_SERVERNAME
    export DATABASE=SQL_DATABASE
    export SQLUSER=SQL_USER
    export SQLPASWORD='SQL_PASWORD'
    ```

4. Rozbalte soubor .zip tak, že zadáte následující příkaz:

    ```bash
    unzip $FILENAME.zip
    ```

5. Vytvoření adresáře ve službě HDInsight storage a potom tento soubor .csv zkopírujte do adresáře tak, že zadáte následující příkaz:

    ```bash
    hdfs dfs -mkdir -p /tutorials/flightdelays/data
    hdfs dfs -put $FILENAME.csv /tutorials/flightdelays/data/
    ```

## <a name="transform-data-using-a-hive-query"></a>Transformace dat pomocí dotazu Hive

Úlohu Hive můžete v clusteru HDInsight spustit různými způsoby. V této části použijete [Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline%E2%80%93CommandLineShell) ke spuštění úlohy Hive. Informace o dalších metodách spuštění úlohy Hive najdete v tématu [použití Apache Hive s HDInsight](./hadoop/hdinsight-use-hive.md).

V rámci úlohy Hive provedete import dat ze souboru .csv do tabulky Hive s názvem **Delays** (Zpoždění).

1. Z řádku SSH, které už máte pro HDInsight cluster, pomocí následujícího příkazu vytvořte a upravte nový soubor s názvem **flightdelays.hql**:

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

3. Chcete-li soubor uložit, stiskněte **Ctrl + X**, pak **y**, stiskněte enter.

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

1. K instalaci FreeTDS, použijte následující příkaz z otevřené připojení SSH ke clusteru:

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

    Po zadání příkazu `GO` se vyhodnotí předchozí příkazy. Tento příkaz vytvoří tabulku s názvem **zpoždění**, pomocí clusterovaného indexu.

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

## <a name="export-data-to-sql-database-using-apache-sqoop"></a>Export dat do SQL database s použitím Apache Sqoop

V předchozích částech jste zkopírovali transformovaná data do umístění `/tutorials/flightdelays/output`. V této části použijete Sqoop k exportu dat z `/tutorials/flightdelays/output` do tabulky, kterou jste vytvořili v databázi Azure SQL.

1. Ověřte, že Sqoop vidí vaše databáze SQL tak, že zadáte následující příkaz:

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://$SQLSERVERNAME.database.windows.net:1433 --username $SQLUSER --password $SQLPASWORD
    ```

    Tento příkaz vrátí seznam databází, včetně databáze, ve které jste vytvořili `delays` tabulky dříve.

2. Export dat z `/tutorials/flightdelays/output` k `delays` tabulku tak, že zadáte následující příkaz:

    ```bash
    sqoop export --connect "jdbc:sqlserver://$SQLSERVERNAME.database.windows.net:1433;database=$DATABASE" --username $SQLUSER --password $SQLPASWORD --table 'delays' --export-dir '/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
    ```

    Sqoop se připojí k databázi, která obsahuje `delays` tabulky a exportuje data z `/tutorials/flightdelays/output` do adresáře `delays` tabulky.

3. Po dokončení příkazu sqoop pomocí tsql nástroje pro připojení k databázi zadáním následujícího příkazu:

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

Po dokončení kurzu můžete cluster odstranit. Pomocí HDInsight jsou vaše data uložena v Azure Storage, takže můžete clusteru bezpečně odstranit, pokud není používán. Za cluster služby HDInsight se účtují poplatky, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster představují několikanásobek poplatků za úložiště, dává ekonomický smysl odstraňovat clustery, které nejsou používány.

Odstranění clusteru, naleznete v tématu [odstranění clusteru HDInsight pomocí prohlížeče, Powershellu nebo rozhraní příkazového řádku Azure](./hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Další postup

V tomto kurzu jste provedli nezpracovaná data souboru CSV, importovat do úložiště clusteru HDInsight a pak transformuje data pomocí Apache Hive v Azure HDInsight.  V dalším kurzu se dozvíte, jak pomocí služby Azure Data Factory vytvářet clustery HDInsight Hadoop na vyžádání.

> [!div class="nextstepaction"]
>[Vytváření clusterů na vyžádání Apache Hadoop v HDInsight pomocí Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md)

Informace o dalších způsobech práce s daty ve službě HDInsight najdete v následujících článcích:

* [Kurz: Extrakce, transformace a načítání dat pomocí Apache Hive v Azure HDInsight](../storage/blobs/data-lake-storage-tutorial-extract-transform-load-hive.md)
* [Použití Apache Hivu se službou HDInsight](hadoop/hdinsight-use-hive.md)
* [Vývoj programů Java MapReduce pro Apache Hadoop v HDInsight](hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [Použití Apache Oozie s HDInsight](hdinsight-use-oozie-linux-mac.md)
* [Použití Apache Sqoop s HDInsight](hadoop/apache-hadoop-use-sqoop-mac-linux.md)