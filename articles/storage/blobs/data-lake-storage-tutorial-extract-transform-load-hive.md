---
title: 'Kurz: Extrahujte, transformujte a načtěte data pomocí Azure HDInsightu'
description: V tomto kurzu se dozvíte, jak extrahovat data z nezpracované datové sady CSV, transformovat je pomocí Apache Hive na Azure HDInsight a pak načíst transformovaná data do Azure SQL Database pomocí Sqoop.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: c9ed675dc970b093f6407d15b3db2ac2668c626b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74327564"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-azure-hdinsight"></a>Kurz: Extrahujte, transformujte a načtěte data pomocí Azure HDInsightu

V tomto kurzu provedete operaci ETL: extrahovat, transformovat a načíst data. Vezmete nezpracovaný datový soubor CSV, importujete ho do clusteru Azure HDInsight, transformujete ho pomocí Apache Hive a načtete ho do databáze Azure SQL s Apache Sqoop.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Extrahujte a nahrajte data do clusteru HDInsight.
> * Transformujte data pomocí Apache Hive.
> * Načtěte data do databáze Azure SQL pomocí Sqoop.

Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

* **Účet úložiště Azure Data Lake Storage Gen2, který je nakonfigurovaný pro HDInsight**

    Viz [Použití Azure Data Lake Storage Gen2 s clustery Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2).

* **Cluster Hadoop založený na Linuxu na HDInsightu**

    Viz [Úvodní příručka: Začněte s Apache Hadoop a Apache Hive v Azure HDInsight pomocí portálu Azure](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-linux-create-cluster-get-started-portal).

* **Azure SQL Database**: Jako cílové úložiště dat použijete databázi Azure SQL. Pokud databázi SQL nemáte, přečtěte si téma [Vytvoření databáze Azure SQL na webu Azure Portal](../../sql-database/sql-database-get-started.md).

* **Azure CLI**: Pokud jste nenainstalovali azure cli, najdete [v tématu instalace příkazového příkazového příkazu k Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

* **Klient zabezpečeného prostředí (SSH):** Další informace naleznete [v tématu Připojení k rozhraní HDInsight (Hadoop) pomocí ssh](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="download-the-flight-data"></a>Stažení letových údajů

1. Přejděte na web [Research and Innovative Technology Administration, Bureau of Transportation Statistics](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time).

2. Na stránce vyberte následující hodnoty:

   | Name (Název) | Hodnota |
   | --- | --- |
   | Filter Year (Filtr roku) |2013 |
   | Filter Period (Filtr období) |January (Leden) |
   | Pole |Year, FlightDate, Reporting_Airline, IATA_CODE_Reporting_Airline, Flight_Number_Reporting_Airline, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. |
   
   Zrušte zaškrtnutí všech ostatních polí.

3. Vyberte **Download** (Stáhnout). Získáte soubor .zip s vybranými datovými poli.

## <a name="extract-and-upload-the-data"></a>Extrahování a nahrávání dat

V této části nahrajete data do clusteru HDInsight a pak je zkopírujete do účtu Data Lake Storage Gen2.

1. Otevřete příkazový řádek a pomocí následujícího příkazu Zabezpečená kopie (Scp) nahrajte soubor ZIP do hlavního uzlu clusteru HDInsight:

   ```bash
   scp <file-name>.zip <ssh-user-name>@<cluster-name>-ssh.azurehdinsight.net:<file-name.zip>
   ```

   * Nahraďte `<file-name>` zástupný symbol názvem souboru ZIP.
   * Nahraďte `<ssh-user-name>` zástupný symbol přihlášením SSH pro cluster HDInsight.
   * Nahraďte `<cluster-name>` zástupný symbol názvem clusteru HDInsight.

   Pokud k ověření přihlášení SSH používáte heslo, zobrazí se výzva k zadání hesla.

   Pokud používáte veřejný klíč, budete pravděpodobně muset použít parametr `-i` k zadání cesty k odpovídajícímu privátnímu klíči. Například, `scp -i ~/.ssh/id_rsa <file_name>.zip <user-name>@<cluster-name>-ssh.azurehdinsight.net:`.

2. Po dokončení nahrávání se ke clusteru připojte pomocí SSH. Na příkazovém řádku zadejte následující příkaz:

   ```bash
   ssh <ssh-user-name>@<cluster-name>-ssh.azurehdinsight.net
   ```

3. Pomocí následujícího příkazu rozbalte soubor .zip:

   ```bash
   unzip <file-name>.zip
   ```

   Příkaz extrahuje soubor **.csv.**

4. Pomocí následujícího příkazu vytvořte kontejner Gen2 úložiště datového jezera.

   ```bash
   hadoop fs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/
   ```

   Nahraďte `<container-name>` zástupný symbol názvem, který chcete dát kontejneru.

   Nahraďte `<storage-account-name>` zástupný symbol názvem svého účtu úložiště.

5. K vytvoření adresáře použijte následující příkaz.

   ```bash
   hdfs dfs -mkdir -p abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data
   ```

6. Ke zkopírování souboru *CSV* do adresáře použijte následující příkaz:

   ```bash
   hdfs dfs -put "<file-name>.csv" abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data/
   ```

   Pokud název souboru obsahuje mezery nebo speciální znaky, použijte kolem názvu souboru uvozovky.

## <a name="transform-the-data"></a>Transformace dat

V této části použijete Beeline ke spuštění úlohy Apache Hive.

Jako součást úlohy Apache Hive importujete data ze souboru .csv do tabulky Apache Hive s názvem **delays**.

1. Z výzvy SSH, kterou již máte pro cluster HDInsight, vytvořte a upravte nový soubor s názvem **flightdelays.hql**pomocí následujícího příkazu :

   ```bash
   nano flightdelays.hql
   ```

2. Následující text upravte `<container-name>` tak, že nahradíte zástupné symboly a `<storage-account-name>` názvem účtu kontejneru a úložiště. Poté zkopírujte a vložte text do nano konzole stisknutím klávesy SHIFT spolu s tlačítkem kliknutí pravým tlačítkem myši.

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
    LOCATION 'abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data';

    -- Drop the delays table if it exists
    DROP TABLE delays;
    -- Create the delays table and populate it with data
    -- pulled in from the CSV file (via the external table defined previously)
    CREATE TABLE delays
    LOCATION 'abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/processed'
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

3. Uložte soubor pomocí kombinace kláves CTRL+X a po zobrazení výzvy zadejte `Y` text.

4. Spusťte Hive a soubor **flightdelays.hql** pomocí následujícího příkazu:

   ```bash
   beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
   ```

5. Po dokončení skriptu __flightdelays.hql__ pomocí následujícího příkazu otevřete interaktivní relaci Beeline:

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

   Tento dotaz načte seznam měst, ve kterých došlo ke zpožděním kvůli nepřízni počasí, společně s průměrnou délkou zpoždění a uloží ho do umístění `abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output`. Později z tohoto umístění data načte Sqoop a exportuje je do služby Azure SQL Database.

7. Beeline ukončíte zadáním `!quit` na příkazovém řádku.

## <a name="create-a-sql-database-table"></a>Vytvoření tabulky databáze SQL

Pro tuto operaci potřebujete název serveru z databáze SQL. Chcete-li najít název serveru, proveďte tyto kroky.

1. Přejděte na [portál Azure](https://portal.azure.com).

2. Vyberte **možnost Databáze SQL**.

3. Filtrujte podle názvu databáze, kterou se rozhodnete použít. Název serveru se zobrazí ve sloupci **Název serveru**.

4. Filtrujte název databáze, kterou chcete použít. Název serveru se zobrazí ve sloupci **Název serveru**.

    ![Získání podrobností o serveru Azure SQL](./media/data-lake-storage-tutorial-extract-transform-load-hive/get-azure-sql-server-details.png "Získání podrobností o serveru Azure SQL")

    Existuje mnoho způsobů, jak se připojit ke službě SQL Database a vytvořit tabulku. V následujících krocích se používá [FreeTDS](https://www.freetds.org/) z clusteru HDInsight.

5. Pokud chcete nainstalovat FreeTDS, použijte následující příkaz z připojení SSH ke clusteru:

   ```bash
   sudo apt-get --assume-yes install freetds-dev freetds-bin
   ```

6. Po dokončení instalace se pomocí následujícího příkazu připojte k databázovému serveru SQL.

   ```bash
   TDSVER=8.0 tsql -H '<server-name>.database.windows.net' -U '<admin-login>' -p 1433 -D '<database-name>'
    ```
   * `<server-name>` Nahraďte zástupný symbol názvem serveru SQL Database.

   * `<admin-login>` Nahraďte zástupný symbol přihlášením správce pro databázi SQL.

   * Nahrazení `<database-name>` zástupného symbolu názvem databáze

   Po zobrazení výzvy zadejte heslo pro přihlášení správce databáze SQL.

   Zobrazí se výstup podobný následujícímu textu:

   ```
   locale is "en_US.UTF-8"
   locale charset is "UTF-8"
   using default charset "UTF-8"
   Default database being set to sqooptest
   1>
   ```

7. Na `1>` výzvu zadejte následující příkazy:

   ```hiveql
   CREATE TABLE [dbo].[delays](
   [OriginCityName] [nvarchar](50) NOT NULL,
   [WeatherDelay] float,
   CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED
   ([OriginCityName] ASC))
   GO
   ```

8. Po zadání příkazu `GO` se vyhodnotí předchozí příkazy.

   Dotaz vytvoří tabulku s názvem **zpoždění**, která má seskupený index.

9. Pomocí následujícího dotazu ověřte, zda je tabulka vytvořena:

   ```hiveql
   SELECT * FROM information_schema.tables
   GO
   ```

   Výstup se bude podobat následujícímu:

   ```
   TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
   databaseName       dbo             delays        BASE TABLE
   ```

10. Zadáním `exit` na příkazovém řádku `1>` ukončete nástroj tsql.

## <a name="export-and-load-the-data"></a>Export a načtení dat

V předchozích částech jste zkopírovali transformovaná data v umístění `abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output`. V této části použijete Sqoop k `abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output` exportu dat do tabulky, kterou jste vytvořili v databázi Azure SQL.

1. Pomocí následujícího příkazu ověřte, že má Sqoop vhled do vaší databáze SQL:

   ```bash
   sqoop list-databases --connect jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433 --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD>
   ```

   Příkaz vrátí seznam databází, včetně databáze, ve které jste vytvořili tabulku **zpoždění.**

2. Pomocí následujícího příkazu exportujte data z tabulky **podvzorkovací tabulky** do tabulky **zpoždění:**

   ```bash
   sqoop export --connect 'jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433;database=<DATABASE_NAME>' --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD> --table 'delays' --export-dir 'abfs://<container-name>@.dfs.core.windows.net/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
   ```

   Sqoop se připojí k databázi, která obsahuje tabulku `/tutorials/flightdelays/output` **zpoždění** a exportuje data z adresáře do tabulky **zpoždění.**

3. Po `sqoop` dokončení příkazu použijte nástroj tsql pro připojení k databázi:

   ```bash
   TDSVER=8.0 tsql -H <SERVER_NAME>.database.windows.net -U <ADMIN_LOGIN> -P <ADMIN_PASSWORD> -p 1433 -D <DATABASE_NAME>
   ```

4. Pomocí následujících příkazů ověřte, zda byla data exportována do tabulky **zpoždění:**

   ```sql
   SELECT * FROM delays
   GO
   ```

   Měl by se zobrazit výpis dat v tabulce. Tabulka obsahuje název města a průměrnou délku zpoždění letu pro příslušné město.

5. Zadejte `exit` pro ukončení tsql utility.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Všechny prostředky použité v tomto kurzu již existují. Není nutné žádné čištění.

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak pracovat s daty ve službě HDInsight, najdete v následujícím článku:

> [!div class="nextstepaction"]
> [Použití služby Azure Data Lake Storage Gen2 s clustery Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
