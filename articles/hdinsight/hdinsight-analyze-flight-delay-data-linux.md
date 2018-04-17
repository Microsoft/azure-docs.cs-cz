---
title: Analýza dat zpoždění letu pomocí Hive v HDInsight - Azure | Microsoft Docs
description: Další informace o použití Hive k analýze dat letu na HDInsight se systémem Linux a pak exportovat data do databáze SQL pomocí Sqoop.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 0c23a079-981a-4079-b3f7-ad147b4609e5
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: cc5d48b881ba59679c19baa3506c3c14c0db8048
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="analyze-flight-delay-data-by-using-hive-on-linux-based-hdinsight"></a>Analýza dat zpoždění letu pomocí Hive v HDInsight se systémem Linux

Zjistěte, jak analyzovat data zpoždění letu pomocí Hive v HDInsight se systémem Linux a jak exportovat data do Azure SQL Database pomocí Sqoop.

> [!IMPORTANT]
> Kroky v tomto dokumentu vyžadují clusteru služby HDInsight, který používá Linux. Linux je jenom operační systém používaný v Azure HDInsight verze 3.4 nebo novější. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="prerequisites"></a>Požadavky

* **Cluster služby HDInsight**. V tématu [začněte s Hadoop v HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md) kroky k vytvoření nového clusteru HDInsight se systémem Linux.

* **Azure SQL Database**. Jako cílového úložiště dat používáte Azure SQL database. Pokud nemáte databázi SQL, najdete v části [vytvoření Azure SQL database na portálu Azure](../sql-database/sql-database-get-started.md).

* **Azure CLI**. Pokud jste nenainstalovali Azure CLI, najdete v části [nainstalovat Azure CLI 1.0](../cli-install-nodejs.md) pro další kroky.

## <a name="download-the-flight-data"></a>Stáhněte si data pohybující se

1. Přejděte do [výzkum a inovativní technologie správy, statistický úřad Transport][rita-website].

2. Na stránce vyberte následující hodnoty:

   | Název | Hodnota |
   | --- | --- |
   | Filtr roku |2013 |
   | Filtrovat období |Leden |
   | Pole |Rok, FlightDate, UniqueCarrier, operátora, FlightNum, OriginAirportID, původu, OriginCityName, OriginState, DestAirportID, cíle, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay NASDelay, SecurityDelay, LateAircraftDelay. |
   Zrušte zaškrtnutí všech ostatních polí. 

3. Vyberte **Stáhnout**.

## <a name="upload-the-data"></a>Odeslání dat

1. Použijte následující příkaz k nahrání souboru ZIP do hlavního uzlu clusteru HDInsight:

    ```
    scp FILENAME.zip USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:
    ```

    Nahraďte *FILENAME* s názvem souboru ZIP. Nahraďte *uživatelské jméno* s přihlašování přes SSH pro HDInsight cluster. Nahraďte *CLUSTERNAME* s názvem clusteru HDInsight.

   > [!NOTE]
   > Pokud použijete heslo ověření přihlášení SSH, se zobrazí výzva k zadání hesla. Pokud používáte veřejný klíč, možná budete muset použít `-i` parametr a zadejte cestu k odpovídající soukromý klíč. Například, `scp -i ~/.ssh/id_rsa FILENAME.zip USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`.

2. Po dokončení nahrávání se připojte ke clusteru pomocí protokolu SSH:

    ```ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net```

    Další informace najdete v tématu [Připojení ke službě HDInsight (Hadoop) pomocí SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

3. Rozbalte soubor .zip pomocí následujícího příkazu:

    ```
    unzip FILENAME.zip
    ```

    Tento příkaz extrahuje soubor CSV, který je přibližně 60 MB.

4. Použijte následující příkaz k vytvoření adresáře v úložišti HDInsight a pak zkopírujte soubor do adresáře:

    ```
    hdfs dfs -mkdir -p /tutorials/flightdelays/data
    hdfs dfs -put FILENAME.csv /tutorials/flightdelays/data/
    ```

## <a name="create-and-run-the-hiveql"></a>Vytvoření a spuštění HiveQL

Použijte následující postup k importu dat ze souboru CSV do Hive tabulku s názvem **zpoždění**.

1. Pomocí následujícího příkazu můžete vytvářet a upravovat nový soubor s názvem **flightdelays.hql**:

    ```
    nano flightdelays.hql
    ```

    Jako obsah tohoto souboru použijte následující text:

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

2. Pokud chcete uložit soubor, použijte kombinaci kláves Ctrl + X, potom Y.

3. Spuštění Hive a spuštění **flightdelays.hql** souboru, použijte následující příkaz:

    ```
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
    ```

4. Po __flightdelays.hql__ dokončení spuštění skriptu, otevřete relaci interaktivní Beeline použijte následující příkaz:

    ```
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

5. Když se zobrazí `jdbc:hive2://localhost:10001/>` řádku, použijte následující dotaz pro načtení dat z data pohybující se importované zpoždění:

    ```hiveql
    INSERT OVERWRITE DIRECTORY '/tutorials/flightdelays/output'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    SELECT regexp_replace(origin_city_name, '''', ''),
        avg(weather_delay)
    FROM delays
    WHERE weather_delay IS NOT NULL
    GROUP BY origin_city_name;
    ```

    Tento dotaz načte seznam města, ve kterých zkušeného počasí zpozdí spolu se průměrné zpoždění a ukládá ji do `/tutorials/flightdelays/output`. Později Sqoop čte data z tohoto umístění a vyexportuje ji do Azure SQL Database.

6. Chcete-li ukončit Beeline, zadejte `!quit` příkazového řádku.

## <a name="create-a-sql-database"></a>Vytvoření databáze SQL

Pokud už máte databázi SQL, musíte získat název serveru. K vyhledání názvu serveru v [portál Azure](https://portal.azure.com), vyberte **databází SQL**a potom na název databáze, kterou chcete použít filtr. Název serveru, je uvedena ve **SERVER** sloupce.

Pokud ještě nemáte databázi SQL, použijte informace v [vytvoření Azure SQL database na portálu Azure](../sql-database/sql-database-get-started.md) k jeho vytvoření. Uložte název serveru, který se používá pro databázi.

## <a name="create-a-sql-database-table"></a>Vytvořit tabulku databáze SQL

> [!NOTE]
> Pro připojení k databázi SQL a vytvořte tabulku mnoha způsoby. Následující postup použijte [FreeTDS](http://www.freetds.org/) z clusteru HDInsight.


1. Chcete-li nainstalovat FreeTDS, použijte následující příkaz z připojení SSH do clusteru:

    ```
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

3. Po dokončení instalace použijte následující příkaz pro připojení k databázi SQL serveru. Nahraďte **serverName** s názvem serveru SQL Database. Nahraďte **adminLogin** a **adminPassword** s přihlášení pro databázi SQL. Nahraďte **databaseName** s názvem databáze.

    ```
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -p 1433 -D <databaseName>
    ```

    Po zobrazení výzvy zadejte heslo pro přihlašovací jméno správce databáze SQL.

    Zobrazí se výstup podobný následujícímu:

    ```
    locale is "en_US.UTF-8"
    locale charset is "UTF-8"
    using default charset "UTF-8"
    Default database being set to sqooptest
    1>
    ```

4. Na `1>` výzva, zadejte následující řádky:

    ```
    CREATE TABLE [dbo].[delays](
    [origin_city_name] [nvarchar](50) NOT NULL,
    [weather_delay] float,
    CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED   
    ([origin_city_name] ASC))
    GO
    ```

    Když `GO` příkaz je zadán, jsou vyhodnocovány předchozí příkazy. Tento dotaz vytvoří tabulku s názvem **zpoždění**, clusterovaný index.

    Chcete-li ověřit, zda byl vytvořen v tabulce použijte následující dotaz:

    ```
    SELECT * FROM information_schema.tables
    GO
    ```

    Výstup se bude podobat následujícímu:

    ```
    TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
    databaseName       dbo     delays      BASE TABLE
    ```

5. Zadejte `exit` na `1>` výzvy ukončete nástroj tsql.

## <a name="export-data-with-sqoop"></a>Export dat s Sqoop

1. Pomocí následujícího příkazu ověřte, že Sqoop vidí vaší databázi SQL:

    ```
    sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>
    ```

    Tento příkaz vrátí seznam databází, včetně databáze, ve které jste předtím vytvořili v tabulce zpoždění.

2. Exportovat data z hivesampletable do tabulky zpoždění, použijte následující příkaz:

    ```
    sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=<databaseName>' --username <adminLogin> --password <adminPassword> --table 'delays' --export-dir '/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
    ```

    Sqoop připojuje k databázi, která obsahuje tabulku zpoždění a exportuje data z `/tutorials/flightdelays/output` adresář do tabulky zpoždění.

3. Po dokončení příkazu sqoop, použijte nástroj tsql pro připojení k databázi:

    ```
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D <databaseName>
    ```

    Chcete-li ověřit, že se data vyexportovala do tabulky zpoždění, použijte následující příkazy:

    ```
    SELECT * FROM delays
    GO
    ```

    Měli byste vidět seznam dat v tabulce. Typ `exit` ukončete nástroj tsql.

## <a name="next-steps"></a>Další postup

Informace o další způsoby, jak pracovat s daty v HDInsight, naleznete v následujících článcích:

* [Použití Hivu se službou HDInsight][hdinsight-use-hive]
* [Použijte Oozie s HDInsight][hdinsight-use-oozie]
* [Použití nástroje Sqoop s HDInsight][hdinsight-use-sqoop]
* [Použití Pigu se službou HDInsight][hdinsight-use-pig]
* [Vývoj aplikací Java MapReduce pro Hadoop v HDInsight][hdinsight-develop-mapreduce]
* [Vývoj Python streamování MapReduce programy pro HDInsight][hdinsight-develop-streaming]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]:hadoop/apache-hadoop-use-sqoop-mac-linux.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
[hdinsight-develop-streaming]:hadoop/apache-hadoop-streaming-python.md
[hdinsight-develop-mapreduce]:hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
