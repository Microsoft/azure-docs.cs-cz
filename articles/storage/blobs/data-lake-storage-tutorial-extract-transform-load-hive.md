---
title: 'Kurz: extrakce, transformace a načtení dat pomocí Azure HDInsight'
description: V tomto kurzu se naučíte extrahovat data z nezpracované datové sady CSV, transformovat ji pomocí Apache Hive ve službě Azure HDInsight a pak načíst transformovaná data do Azure SQL Database pomocí Sqoop.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: f8210c3bc0437180ace110f8decd9f83e18650ed
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98661929"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-azure-hdinsight"></a>Kurz: extrakce, transformace a načtení dat pomocí Azure HDInsight

V tomto kurzu provedete operaci ETL: extrakce, transformace a načtení dat. Použijete nezpracovaný datový soubor CSV, naimportujete ho do clusteru Azure HDInsight, transformujte ho pomocí Apache Hive a načtete ho do Azure SQL Database pomocí Apache Sqoop.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Extrahujte a nahrajte data do clusteru HDInsight.
> * Transformujte data pomocí Apache Hive.
> * Načtěte data pro Azure SQL Database pomocí Sqoop.

Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

* **Účet úložiště Azure Data Lake Storage Gen2, který je nakonfigurovaný pro HDInsight**

    Viz [použití Azure Data Lake Storage Gen2 s clustery Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md).

* **Cluster Hadoop se systémem Linux ve službě HDInsight**

    Další informace najdete [v tématu rychlý Start: Začínáme s Apache Hadoop a Apache Hive v Azure HDInsight pomocí Azure Portal](../../hdinsight/hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md).

* **Azure SQL Database**: jako cílové úložiště dat používáte Azure SQL Database. Pokud nemáte databázi v SQL Database, přečtěte si téma [Vytvoření databáze v Azure SQL Database v Azure Portal](../../azure-sql/database/single-database-create-quickstart.md).

* **Azure CLI**: Pokud jste nenainstalovali Azure CLI, přečtěte si téma [instalace Azure CLI](/cli/azure/install-azure-cli).

* **Klient Secure Shell (SSH)**: Další informace naleznete v tématu [připojení ke službě HDInsight (HADOOP) pomocí SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="download-the-flight-data"></a>Stažení letových údajů

1. Přejděte na web [Research and Innovative Technology Administration, Bureau of Transportation Statistics](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time).

2. Na stránce vyberte následující hodnoty:

   | Name | Hodnota |
   | --- | --- |
   | Filter Year (Filtr roku) |2013 |
   | Filter Period (Filtr období) |January (Leden) |
   | Pole |Year, FlightDate, Reporting_Airline, IATA_CODE_Reporting_Airline, Flight_Number_Reporting_Airline, OriginAirportID, původ, OriginCityName, OriginState, DestAirportID, cíl, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. |
   
   Zrušte zaškrtnutí všech ostatních polí.

3. Vyberte **Stáhnout**. Získáte soubor .zip s vybranými datovými poli.

## <a name="extract-and-upload-the-data"></a>Extrakce a nahrání dat

V této části nahrajete data do clusteru HDInsight a pak tato data zkopírujete do svého účtu Data Lake Storage Gen2.

1. Otevřete příkazový řádek a pomocí následujícího příkazu zabezpečeného kopírování (SCP) Nahrajte soubor zip do hlavního uzlu clusteru HDInsight:

   ```bash
   scp <file-name>.zip <ssh-user-name>@<cluster-name>-ssh.azurehdinsight.net:<file-name.zip>
   ```

   * `<file-name>`Zástupný symbol nahraďte názvem souboru. zip.
   * `<ssh-user-name>`Zástupný symbol nahraďte přihlašovacím jménem SSH pro cluster HDInsight.
   * `<cluster-name>`Zástupný symbol nahraďte názvem clusteru HDInsight.

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

   Příkaz extrahuje soubor **. csv** .

4. K vytvoření kontejneru Data Lake Storage Gen2 použijte následující příkaz.

   ```bash
   hadoop fs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/
   ```

   `<container-name>`Zástupný symbol nahraďte názvem, který chcete poskytnout kontejneru.

   `<storage-account-name>`Zástupný symbol nahraďte názvem vašeho účtu úložiště.

5. Pomocí následujícího příkazu vytvořte adresář.

   ```bash
   hdfs dfs -mkdir -p abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data
   ```

6. Pomocí následujícího příkazu zkopírujte soubor *. csv* do adresáře:

   ```bash
   hdfs dfs -put "<file-name>.csv" abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data/
   ```

   Pokud název souboru obsahuje mezery nebo speciální znaky, použijte kolem názvu souboru uvozovky.

## <a name="transform-the-data"></a>Transformace dat

V této části použijete Beeline ke spuštění úlohy Apache Hive.

V rámci úlohy Apache Hive naimportujete data ze souboru. CSV do tabulky Apache Hive s názvem **zpoždění**.

1. Z příkazového řádku SSH, který už máte pro cluster HDInsight, použijte následující příkaz k vytvoření a úpravě nového souboru s názvem     **flightdelays. HQL**:

   ```bash
   nano flightdelays.hql
   ```

2. Upravte následující text nahrazením `<container-name>` `<storage-account-name>` zástupných symbolů a názvem svého kontejneru a účtu úložiště. Pak tento text zkopírujte a vložte do konzoly nano pomocí klávesy SHIFT a kliknutím pravým tlačítkem myši na tlačítko.

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

3. Soubor uložte pomocí kombinace kláves CTRL + X a `Y` po zobrazení výzvy zadejte.

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

Pro tuto operaci budete potřebovat název serveru z SQL Database. Pokud chcete najít název vašeho serveru, proveďte tyto kroky.

1. Přejděte na [Azure Portal](https://portal.azure.com).

2. Vyberte **databáze SQL**.

3. Vyfiltrujte název databáze, kterou se rozhodnete použít. Název serveru se zobrazí ve sloupci **Název serveru**.

4. Vyfiltrujte název databáze, kterou chcete použít. Název serveru se zobrazí ve sloupci **Název serveru**.

    ![Získat podrobnosti o Azure SQL serveru](./media/data-lake-storage-tutorial-extract-transform-load-hive/get-azure-sql-server-details.png "Získat podrobnosti o Azure SQL serveru")

    Existuje mnoho způsobů, jak se připojit ke službě SQL Database a vytvořit tabulku. V následujících krocích se používá [FreeTDS](https://www.freetds.org/) z clusteru HDInsight.

5. Pokud chcete nainstalovat FreeTDS, použijte následující příkaz z připojení SSH ke clusteru:

   ```bash
   sudo apt-get --assume-yes install freetds-dev freetds-bin
   ```

6. Po dokončení instalace se připojte k SQL Database pomocí následujícího příkazu.

   ```bash
   TDSVER=8.0 tsql -H '<server-name>.database.windows.net' -U '<admin-login>' -p 1433 -D '<database-name>'
    ```
   * `<server-name>`Zástupný text nahraďte názvem logického SQL serveru.

   * `<admin-login>`Zástupný symbol nahraďte přihlašovacím jménem správce pro SQL Database.

   * `<database-name>`Zástupný text nahraďte názvem databáze.

   Po zobrazení výzvy zadejte heslo pro přihlašovací jméno správce SQL Database.

   Zobrazí se výstup podobný následujícímu textu:

   ```
   locale is "en_US.UTF-8"
   locale charset is "UTF-8"
   using default charset "UTF-8"
   Default database being set to sqooptest
   1>
   ```

7. Na `1>` příkazovém řádku zadejte následující příkazy:

   ```hiveql
   CREATE TABLE [dbo].[delays](
   [OriginCityName] [nvarchar](50) NOT NULL,
   [WeatherDelay] float,
   CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED
   ([OriginCityName] ASC))
   GO
   ```

8. Po zadání příkazu `GO` se vyhodnotí předchozí příkazy.

   Dotaz vytvoří tabulku s názvem **zpoždění**, která má clusterovaný index.

9. K ověření, že je tabulka vytvořená, použijte následující dotaz:

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

## <a name="export-and-load-the-data"></a>Exportovat a načíst data

V předchozích částech jste do umístění zkopírovali transformovaná data  `abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output` . V této části použijete Sqoop k exportu dat z `abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output` do tabulky, kterou jste vytvořili v Azure SQL Database.

1. Pomocí následujícího příkazu ověřte, že má Sqoop vhled do vaší databáze SQL:

   ```bash
   sqoop list-databases --connect jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433 --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD>
   ```

   Příkaz vrátí seznam databází, včetně databáze, ve které jste vytvořili tabulku **zpoždění** .

2. Pomocí následujícího příkazu exportujte data z tabulky **hivesampletable** do tabulky **zpoždění** :

   ```bash
   sqoop export --connect 'jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433;database=<DATABASE_NAME>' --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD> --table 'delays' --export-dir 'abfs://<container-name>@.dfs.core.windows.net/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
   ```

   Sqoop se připojí k databázi, která obsahuje tabulku **zpoždění** , a exportuje data z `/tutorials/flightdelays/output` adresáře do tabulky s **prodlevami** .

3. Po `sqoop` dokončení příkazu se připojte k databázi pomocí nástroje TSQL:

   ```bash
   TDSVER=8.0 tsql -H <SERVER_NAME>.database.windows.net -U <ADMIN_LOGIN> -P <ADMIN_PASSWORD> -p 1433 -D <DATABASE_NAME>
   ```

4. Pomocí následujících příkazů ověřte, zda byla data exportována do tabulky s **prodlevami** :

   ```sql
   SELECT * FROM delays
   GO
   ```

   Měl by se zobrazit výpis dat v tabulce. Tabulka obsahuje název města a průměrnou délku zpoždění letu pro příslušné město.

5. Pokud `exit` chcete nástroj TSQL ukončit, zadejte.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Všechny prostředky použité v tomto kurzu jsou již existující. Není nutné žádné vyčištění.

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak pracovat s daty v HDInsight, najdete v následujícím článku:

> [!div class="nextstepaction"]
> [Použití služby Azure Data Lake Storage Gen2 s clustery Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)