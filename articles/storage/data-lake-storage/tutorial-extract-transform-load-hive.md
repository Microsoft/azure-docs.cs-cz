---
title: 'Kurz: Provádění operací extrakce, transformace a načítání (ETL) pomocí Hivu ve službě HDInsight – Azure'
description: Zjistěte, jak extrahovat data z nezpracované datové sady CSV, transformovat je pomocí Hivu ve službě HDInsight a pak transformovaná data načíst do databáze SQL Azure pomocí Sqoopu.
services: storage
author: jamesbak
tags: azure-portal
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 06/27/2018
ms.author: jamesbak
ms.custom: H1Hack27Feb2017,hdinsightactive,mvc
ms.openlocfilehash: 376ac623c37183cdfbd6e27c54b0c726928237e2
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39526000"
---
# <a name="tutorial-extract-transform-and-load-data-using-apache-hive-on-azure-hdinsight"></a>Kurz: Extrakce, transformace a načítání dat pomocí Apache Hivu ve službě Azure HDInsight

V tomto kurzu budete importovat nezpracovaný datový soubor CSV do clusteru HDInsight a pak budete transformovat data pomocí Apache Hivu ve službě Azure HDInsight. Po transformaci data načtete do databáze SQL Azure pomocí Apache Sqoopu. V tomto článku se používají veřejně dostupné letové údaje.

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Stažení ukázkových údajů o letech
> * Nahrání dat do clusteru HDInsight
> * Transformace dat pomocí Hivu
> * Vytvoření tabulky v databázi SQL Azure
> * Export dat do databáze SQL Azure pomocí Sqoopu


Následující obrázek ukazuje obvyklý běh aplikace ETL.

![Operace ETL s využitím Apache Hivu ve službě Azure HDInsight](./media/tutorial-extract-transform-load-hive/hdinsight-etl-architecture.png "Operace ETL s využitím Apache Hivu ve službě Azure HDInsight")

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* **Cluster Hadoop s Linuxem ve službě HDInsight**. Kroky, jak vytvořit nový cluster HDInsight s Linuxem, najdete v článku o [nastavení clusterů v HDInsightu pomocí Hadoopu, Sparku, Kafky a dalších](./quickstart-create-connect-hdi-cluster.md).

* **Azure SQL Database**. Databázi SQL Azure použijete jako cílové úložiště dat. Pokud databázi SQL nemáte, přečtěte si téma [Vytvoření databáze SQL Azure na webu Azure Portal](../../sql-database/sql-database-get-started.md).

* **Azure CLI 2.0**. Pokud ještě nemáte nainstalované Azure CLI, přečtěte si téma [Instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), kde najdete další postup.

* **Klient SSH**. Další informace najdete v tématu [Připojení ke službě HDInsight (Hadoop) pomocí SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

> [!IMPORTANT]
> Kroky v tomto dokumentu vyžadují cluster HDInsight s Linuxem. Linux je jediný operační systém, který se používá ve službě Azure HDInsight verze 3.4 nebo novější. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../../hdinsight/hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="download-the-flight-data"></a>Stažení letových údajů

1. Přejděte na web [Research and Innovative Technology Administration, Bureau of Transportation Statistics][rita-website].

2. Na stránce vyberte následující hodnoty:

   | Název | Hodnota |
   | --- | --- |
   | Filter Year (Filtr roku) |2013 |
   | Filter Period (Filtr období) |January (Leden) |
   | Fields (Pole) |Year, FlightDate, UniqueCarrier, Carrier, FlightNum, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. |
   Zrušte zaškrtnutí všech ostatních polí.

3. Vyberte **Download** (Stáhnout). Získáte soubor .zip s vybranými datovými poli.

## <a name="upload-data-to-an-hdinsight-cluster"></a>Nahrání dat do clusteru HDInsight

Do úložiště přidruženého ke clusteru HDInsight můžete data nahrát mnoha způsoby. V této části k nahrání dat použijete `scp`. Další informace o jiných způsobech odesílání dat najdete v článku o [použití Distcp ke kopírování dat mezi objekty Azure Storage Blob a Data Lake Storage Gen2 Preview](use-distcp.md).

1. Otevřete příkazový řádek a pomocí následujícího příkazu nahrajte soubor .zip do hlavního uzlu clusteru HDInsight:

    ```bash
    scp <FILE_NAME>.zip <SSH_USER_NAME>@<CLUSTER_NAME>-ssh.azurehdinsight.net:<FILE_NAME.zip>
    ```

    Nahraďte *FILE_NAME* názvem souboru .zip. Nahraďte *SSH_USER_NAME* přihlašovacím jménem SSH pro cluster HDInsight. Nahraďte *CLUSTER_NAME* názvem clusteru HDInsight.

   > [!NOTE]
   > Pokud k ověření přihlášení SSH používáte heslo, zobrazí se výzva k zadání hesla. Pokud používáte veřejný klíč, budete pravděpodobně muset použít parametr `-i` k zadání cesty k odpovídajícímu privátnímu klíči. Například, `scp -i ~/.ssh/id_rsa FILE_NAME.zip USER_NAME@CLUSTER_NAME-ssh.azurehdinsight.net:`.

2. Po dokončení nahrávání se ke clusteru připojte pomocí SSH. Na příkazovém řádku zadejte následující příkaz:

    ```bash
    ssh <SSH_USER_NAME>@<CLUSTER_NAME>-ssh.azurehdinsight.net
    ```

3. Pomocí následujícího příkazu rozbalte soubor .zip:

    ```bash
    unzip <FILE_NAME>.zip
    ```

    Tento příkaz extrahuje přibližně 60MB soubor *.csv*.

4. Pomocí následujících příkazů vytvořte adresář a zkopírujte do něj soubor *.csv*:

    ```bash
    hdfs dfs -mkdir -p abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/data
    hdfs dfs -put <FILE_NAME>.csv abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/data/
    ```

5. Vytvořte systém souborů Data Lake Storage Gen2.

    ```bash
    hadoop fs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/
    ```

## <a name="transform-data-using-a-hive-query"></a>Transformace dat pomocí dotazu Hive

Úlohu Hive můžete v clusteru HDInsight spustit různými způsoby. V této části ke spuštění úlohy Hive použijete Beeline. Informace o dalších způsobech spuštění úlohy Hive najdete v tématu [Použití Hivu ve službě HDInsight](../../hdinsight/hadoop/hdinsight-use-hive.md).

V rámci úlohy Hive provedete import dat ze souboru .csv do tabulky Hive s názvem **Delays** (Zpoždění).

1. Na příkazovém řádku SSH, který už máte pro cluster HDInsight spuštěný, pomocí následujícího příkazu vytvořte a upravte nový soubor **flightdelays.hql**:

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
    LOCATION 'abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/data';

    -- Drop the delays table if it exists
    DROP TABLE delays;
    -- Create the delays table and populate it with data
    -- pulled in from the CSV file (via the external table defined previously)
    CREATE TABLE delays
    LOCATION abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/processed
    AS
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

2. Soubor uložíte stisknutím klávesy **Esc** a zadáním `:x`.

3. Spusťte Hive a soubor **flightdelays.hql** pomocí následujícího příkazu:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
    ```

4. Po dokončení skriptu __flightdelays.hql__ pomocí následujícího příkazu otevřete interaktivní relaci Beeline:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

5. Po zobrazení příkazového řádku `jdbc:hive2://localhost:10001/>` pomocí následujícího dotazu načtěte data z importovaných dat o zpožděných letech:

    ```hiveql
    INSERT OVERWRITE DIRECTORY 'abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    SELECT regexp_replace(origin_city_name, '''', ''),
        avg(weather_delay)
    FROM delays
    WHERE weather_delay IS NOT NULL
    GROUP BY origin_city_name;
    ```

    Tento dotaz načte seznam měst, ve kterých došlo ke zpožděním kvůli nepřízni počasí, společně s průměrnou délkou zpoždění a uloží ho do umístění `abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output`. Později z tohoto umístění data načte Sqoop a exportuje je do služby Azure SQL Database.

6. Beeline ukončíte zadáním `!quit` na příkazovém řádku.

## <a name="create-a-sql-database-table"></a>Vytvoření tabulky databáze SQL

V této části se předpokládá, že už máte vytvořenou databázi SQL Azure. Pokud ještě databázi SQL nemáte, vytvořte si ji podle pokynů v tématu [Vytvoření databáze SQL na webu Azure Portal](../../sql-database/sql-database-get-started.md).

Pokud už máte databázi SQL, musíte získat název serveru. Název serveru můžete vyhledat na webu [Azure Portal](https://portal.azure.com) tak, že vyberete **Databáze SQL** a vyfiltrujete název databáze, kterou chcete použít. Název serveru se zobrazí ve sloupci **Název serveru**.

![Získání podrobností o serveru SQL Azure](./media/tutorial-extract-transform-load-hive/get-azure-sql-server-details.png "Získání podrobností o serveru SQL Azure")

> [!NOTE]
> Existuje mnoho způsobů, jak se připojit ke službě SQL Database a vytvořit tabulku. V následujících krocích se používá [FreeTDS](http://www.freetds.org/) z clusteru HDInsight.


1. Pokud chcete nainstalovat FreeTDS, použijte následující příkaz z připojení SSH ke clusteru:

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

3. Po dokončení instalace se pomocí následujícího příkazu připojte k serveru služby SQL Database. Nahraďte **serverName** názvem serveru služby SQL Database. Nahraďte **adminLogin** a **adminPassword** přihlašovacími údaji pro službu SQL Database. Nahraďte **databaseName** názvem databáze.

    ```bash
    TDSVER=8.0 tsql -H <SERVER_NAME>.database.windows.net -U <ADMIN_LOGIN> -p 1433 -D <DATABASE_NAME>
    ```

    Po zobrazení výzvy zadejte heslo pro přihlášení správce služby SQL Database.

    Zobrazí se výstup podobný následujícímu textu:

    ```
    locale is "en_US.UTF-8"
    locale charset is "UTF-8"
    using default charset "UTF-8"
    Default database being set to sqooptest
    1>
    ```

4. Na příkazovém řádku `1>` zadejte následující řádky:

    ```hiveql
    CREATE TABLE [dbo].[delays](
    [origin_city_name] [nvarchar](50) NOT NULL,
    [weather_delay] float,
    CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED   
    ([origin_city_name] ASC))
    GO
    ```

    Po zadání příkazu `GO` se vyhodnotí předchozí příkazy. Tento dotaz vytvoří tabulku **delays** s clusterovaným indexem.

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

5. Zadáním `exit` na příkazovém řádku `1>` ukončete nástroj tsql.


## <a name="export-data-to-sql-database-using-sqoop"></a>Export dat do databáze SQL pomocí Sqoopu

V předchozích částech jste zkopírovali transformovaná data do umístění `abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output`. V této části použijete Sqoop k exportu dat z `abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/tutorials/flightdelays/output` do tabulky, kterou jste vytvořili v databázi SQL Azure. 

1. Pomocí následujícího příkazu ověřte, že má Sqoop vhled do vaší databáze SQL:

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433 --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD>
    ```

    Tento příkaz vrátí seznam databází včetně databáze, ve které jste předtím vytvořili tabulku delays.

2. Pomocí následujícího příkazu exportujte data z tabulky hivesampletable do tabulky delays:

    ```bash
    sqoop export --connect 'jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433;database=<DATABASE_NAME>' --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD> --table 'delays' --export-dir 'abfs://<FILE_SYSTEM_NAME>@.dfs.core.windows.net/tutorials/flightdelays/output' 
    --fields-terminated-by '\t' -m 1
    ```

    Sqoop se připojí k databázi obsahující tabulku delays a exportuje do tabulky delays data z adresáře `/tutorials/flightdelays/output`.

3. Po dokončení příkazu sqoop se pomocí nástroje tsql připojte k databázi:

    ```bash
    TDSVER=8.0 tsql -H <SERVER_NAME>.database.windows.net -U <ADMIN_LOGIN> -P <ADMIN_PASSWORD> -p 1433 -D <DATABASE_NAME>
    ```

    Pomocí následujících příkazů ověřte, že se data exportovala do tabulky delays:

    ```sql
    SELECT * FROM delays
    GO
    ```

    Měl by se zobrazit výpis dat v tabulce. Tabulka obsahuje název města a průměrnou délku zpoždění letu pro příslušné město. 

    Zadáním `exit` ukončete nástroj tsql.

## <a name="next-steps"></a>Další kroky

Informace o dalších způsobech práce s daty ve službě HDInsight najdete v následujících článcích:

* [Použití Hivu se službou HDInsight][hdinsight-use-hive]
* [Použití Pigu se službou HDInsight][hdinsight-use-pig]
* [Vývoj programů Java MapReduce pro Hadoop ve službě HDInsight][hdinsight-develop-mapreduce]
* [Vývoj programů MapReduce se streamováním v Pythonu pro službu HDInsight][hdinsight-develop-streaming]
* [Použití Oozie se službou HDInsight][hdinsight-use-oozie]
* [Použití Sqoopu se službou HDInsight][hdinsight-use-sqoop]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[hdinsight-use-oozie]: ../../hdinsight/hdinsight-use-oozie-linux-mac.md
[hdinsight-use-hive]:../../hdinsight/hadoop/hdinsight-use-hive.md
[hdinsight-provision]: ../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-storage]: ../../hdinsight/hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: ../../hdinsight/hdinsight-upload-data.md
[hdinsight-get-started]: ../../hdinsight/hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]:../../hdinsight/hadoop/apache-hadoop-use-sqoop-mac-linux.md
[hdinsight-use-pig]:../../hdinsight/hadoop/hdinsight-use-pig.md
[hdinsight-develop-streaming]:../../hdinsight/hadoop/apache-hadoop-streaming-python.md
[hdinsight-develop-mapreduce]:../../hdinsight/hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
