---
title: Přístup k datům v Azure Data Lake Storage Gen2 Preview v Azure Databricks pomocí Sparku | Microsoft Docs
description: Naučte se spouštět dotazy Sparku v clusteru Azure Databricks, abyste měli přístup k datům v účtu úložiště Azure Data Lake Storage Gen2.
services: hdinsight,storage
author: dineshm
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 6/27/2018
ms.author: dineshm
ms.openlocfilehash: 7d951a959da28187a5971ee218f2bd921d331727
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43301794"
---
# <a name="tutorial-access-azure-data-lake-storage-gen2-preview-data-with-azure-databricks-using-spark"></a>Kurz: Přístup k datům v Azure Data Lake Storage Gen2 Preview v Azure Databricks pomocí Sparku

V tomto kurzu se naučíte spouštět dotazy Sparku v clusteru Azure Databricks, abyste se mohli dotazovat na data v účtu, který podporuje Azure Data Lake Storage Gen2 Preview.

> [!div class="checklist"]
> * Vytvoření clusteru Databricks
> * Ingestace nestrukturovaných dat do účtu úložiště
> * Aktivace funkce Azure kvůli zpracování dat
> * Spuštění analýzy dat v úložišti objektů blob

## <a name="prerequisites"></a>Požadavky

V tomto kurzu si ukážeme, jak využívat a dotazovat se na data o odletech aerolinek, která dává k dispozici [ministerstvo dopravy USA](https://transtats.bts.gov/Tables.asp?DB_ID=120&DB_Name=Airline%20On-Time%20Performance%20Data&DB_Short_Name=On-Time). Stáhněte si data o leteckých linkách alespoň za dva poslední roky (vyberte všechna pole) a uložte si výsledek do počítače. Nezapomeňte si poznamenat název souboru a cestu, kam si ho stáhnete. Tyto údaje budete potřebovat v dalším kroku.

> [!NOTE]
> Pokud chcete vybrat všechna datová pole, klikněte na zaškrtávací políčko **Prezipped file** (Komprimovaný soubor). Budete stahovat mnoho gigabajtů, ale k analýze potřebujete velký objem dat.

## <a name="create-an-azure-data-lake-storage-gen2-account"></a>Vytvoření účtu Azure Data Lake Storage Gen2

Napřed si vytvořte nový [účet Azure Data Lake Storage Gen2](quickstart-create-account.md) a dejte mu jedinečný název. Pak přejděte k účtu úložiště, abyste mohli načíst konfigurační nastavení.

> [!IMPORTANT]
> Ve verzi Preview funguje služba Azure Functions jenom s účty Azure Data Lake Storage Gen2 vytvořenými s plochým oborem názvů.

1. V části **Nastavení** klikněte na **Přístupové klíče**.
3. Klikněte na tlačítko **Kopírovat** vedle **Key1** a zkopírujte hodnotu klíče.

Název účtu a klíč budete potřebovat v dalších krocích tohoto kurzu. Otevřete textový editor a poznamenejte si do něj název účtu a klíč k pozdějšímu použití.

## <a name="create-a-databricks-cluster"></a>Vytvoření clusteru Databricks

V dalším kroku vytvoříte [cluster Databricks](https://docs.azuredatabricks.net/) jako pracovní prostor pro data.

1. Vytvořte [službu Databricks](https://ms.portal.azure.com/#create/Microsoft.Databricks) a pojmenujte ji **myFlightDataService** (při vytvoření služby nezapomeňte zaškrtnout políčko *Připnout na řídicí panel*).
2. Klikněte, že chcete **spustit pracovní prostor** a otevřete pracovní prostor v novém okně prohlížeče.
3. V navigačním podokně vlevo klikněte na **clustery**.
4. Klikněte, že chcete **vytvořit cluster**.
5. Do pole s **názvem clusteru** zadejte *myFlightDataCluster*.
6. V poli **typu pracovního procesu** vyberte *Standard_D8s_v3*.
7. Hodnotu **minimálního počtu pracovních procesů** změňte na *4*.
8. Nahoře na stránce klikněte, že chcete **vytvořit cluster** (dokončení procesu může trvat pět minut).
9. Jakmile proces doběhne, vyberte na navigačním panelu vlevo nahoře **Azure Databricks**.
10. V dolní polovině stránky vyberte v oddílu **Nový** položku **Poznámkový blok**.
11. Do pole **Název** zadejte libovolný název a jako jazyk vyberte **Python**.
12. Ve všech ostatních polích můžete nechat výchozí hodnoty.
13. Vyberte **Vytvořit**.
14. Do buňky **Cmd 1** vložte následující kód. Hodnoty nahraďte hodnotami svého účtu úložiště, které jste si poznamenali.

    ```bash
    spark.conf.set("fs.azure.account.key.<account_name>.dfs.core.windows.net", "<account_key>") 
    spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "true")
    dbutils.fs.ls("abfss://<file_system>@<account_name>.dfs.core.windows.net/")
    spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "false")
    ```

## <a name="ingest-data"></a>Ingestace dat

### <a name="copy-source-data-into-the-storage-account"></a>Zkopírování zdrojových dat do účtu úložiště

Dalším úkolem je zkopírovat data ze souboru *.csv* do úložiště Azure nástrojem AzCopy. Otevřete okno příkazového řádku a zadejte následující příkazy. Nezapomeňte nahradit zástupné znaky `<DOWNLOAD_FILE_PATH>`, `<ACCOUNT_NAME>` a `<ACCOUNT_KEY>` odpovídajícími hodnotami, které jste si poznamenali v předchozím kroku.

```bash
set ACCOUNT_NAME=<ACCOUNT_NAME>
set ACCOUNT_KEY=<ACCOUNT_KEY>
azcopy cp "<DOWNLOAD_FILE_PATH>" https://<ACCOUNT_NAME>.dfs.core.windows.net/dbricks/folder1/On_Time --recursive 
```

### <a name="use-databricks-notebook-to-convert-csv-to-parquet"></a>Použití poznámkového bloku Databricks k převodu CSV na formát Parquet

Znovu otevřete v prohlížeči Databricks a proveďte následující kroky:

1. Na navigačním panelu vlevo nahoře vyberte **Azure Databricks**.
2. V dolní polovině stránky vyberte v oddílu **Nový** položku **Poznámkový blok**.
3. Do pole **Název** zadejte **CSV2Parquet**.
4. Ve všech ostatních polích můžete nechat výchozí hodnoty.
5. Vyberte **Vytvořit**.
6. Do buňky **Cmd 1** vložte následující kód (tento kód provede automatické uložení v editoru).

    ```python
    #mount Azure Blob Storage as an HDFS file system to your databricks cluster
    #you need to specify a storage account and container to connect to. 
    #use a SAS token or an account key to connect to Blob Storage.  
    accountname = "<insert account name>"
    accountkey = " <insert account key>"
    fullname = "fs.azure.account.key." +accountname+ ".dfs.core.windows.net"
    accountsource = "abfs://dbricks@" +accountname+ ".dfs.core.windows.net/folder1"
    #create a dataframe to read data
    flightDF = spark.read.format('csv').options(header='true', inferschema='true').load(accountsource + "/On_Time_On_Time*.csv")
    #read the all the airline csv files and write the output to parquet format for easy query
    flightDF.write.mode("append").parquet(accountsource + '/parquet/flights')

    #read the flight details parquet file 
    #flightDF = spark.read.format('parquet').options(header='true', inferschema='true').load(accountsource + "/parquet/flights")
    print("Done")
    ```

## <a name="explore-data-using-hadoop-distributed-file-system"></a>Prozkoumání dat pomocí systému souborů HDFS (Hadoop Distributed File System)

Vraťte se do pracovního prostoru Databricks a na navigačním panelu vlevo klikněte na ikonu **Nedávné**.

1. Klikněte na poznámkový blok **Flight Data Analytics** (Analýza letových dat).
2. Stisknutím kombinace kláves **Ctrl+Alt+N** vytvořte novou buňku.

Do buňky **Cmd 1** zadejte všechny následující bloky kódu a stiskněte **Cmd+Enter**, abyste spustili skript Pythonu.

Pokud chcete získat seznam souborů CSV nahraných přes AzCopy, spusťte následující skript:

```python
import os.path
import IPython
from pyspark.sql import SQLContext
source = "abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/"
dbutils.fs.ls(source + "/temp")
display(dbutils.fs.ls(source + "/temp/"))
```

Pokud chcete vytvořit nový soubor a zobrazit seznam souborů ve složce *parquet/flights*, spusťte tento skript:

```python
source = "abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/"

dbutils.fs.help()

dbutils.fs.put(source + "/temp/1.txt", "Hello, World!", True)
dbutils.fs.ls(source + "/temp/parquet/flights")
```
Těmito ukázkami kódu jste prozkoumali hierarchickou povahu systému souborů HDFS (Hadoop Distributed File System) v účtu, který podporuje Azure Data Lake Storage Gen2.

## <a name="query-the-data"></a>Vytváření dotazů na data

Teď můžete začít vytvářet dotazy na data, která jste nahráli do Azure Data Lake Storage. Do buňky **Cmd 1** zadejte všechny následující bloky kódu a stiskněte **Cmd+Enter**, abyste spustili skript Pythonu.

### <a name="simple-queries"></a>Ukázkové dotazy

Pokud chcete vytvořit datové rámce zdrojů dat, spusťte následující skript:

> [!IMPORTANT]
> Nezapomeňte nahradit zástupný symbol **<YOUR_CSV_FILE_NAME>** názvem souboru, který jste si stáhli na začátku tohoto kurzu.

```python
#Copy this into a Cmd cell in your notebook.
acDF = spark.read.format('csv').options(header='true', inferschema='true').load(accountsource + "/<YOUR_CSV_FILE_NAME>.csv")
acDF.write.parquet(accountsource + '/parquet/airlinecodes')

#read the existing parquet file for the flights database that was created via the Azure Function
flightDF = spark.read.format('parquet').options(header='true', inferschema='true').load(accountsource + "/parquet/flights")

#print the schema of the dataframes
acDF.printSchema()
flightDF.printSchema()

#print the flight database size
print("Number of flights in the database: ", flightDF.count())

#show the first 20 rows (20 is the default)
#to show the first n rows, run: df.show(n)
acDF.show(100, False)
flightDF.show(20, False)

#Display to run visualizations
#preferrably run this in a separate cmd cell
display(flightDF)
```

Pokud chcete spustit dotazy, které analyzují data, spusťte následující skript:

```python
#Run each of these queries, preferrably in a separate cmd cell for separate analysis
#create a temporary sql view for querying flight information
FlightTable = spark.read.parquet(accountsource + '/parquet/flights')
FlightTable.createOrReplaceTempView('FlightTable')

#create a temporary sql view for querying airline code information
AirlineCodes = spark.read.parquet(accountsource + '/parquet/airlinecodes')
AirlineCodes.createOrReplaceTempView('AirlineCodes')

#using spark sql, query the parquet file to return total flights in January and February 2016
out1 = spark.sql("SELECT * FROM FlightTable WHERE Month=1 and Year= 2016")
NumJan2016Flights = out1.count()
out2 = spark.sql("SELECT * FROM FlightTable WHERE Month=2 and Year= 2016")
NumFeb2016Flights=out2.count()
print("Jan 2016: ", NumJan2016Flights," Feb 2016: ",NumFeb2016Flights)
Total= NumJan2016Flights+NumFeb2016Flights
print("Total flights combined: ", Total)

# List out all the airports in Texas
out = spark.sql("SELECT distinct(OriginCityName) FROM FlightTable where OriginStateName = 'Texas'") 
print('Airports in Texas: ', out.show(100))

#find all airlines that fly from Texas
out1 = spark.sql("SELECT distinct(Carrier) FROM FlightTable WHERE OriginStateName='Texas'")
print('Airlines that fly to/from Texas: ', out1.show(100, False))
```
### <a name="complex-queries"></a>Složitější dotazy

Pokud chcete spustit následující složitější dotazy, spusťte v poznámkovém bloku postupně jednotlivé segmenty a prohlédněte si výsledky.

```python
#find the airline with the most flights

#create a temporary view to hold the flight delay information aggregated by airline, then select the airline name from the Airlinecodes dataframe
spark.sql("DROP VIEW IF EXISTS v")
spark.sql("CREATE TEMPORARY VIEW v AS SELECT Carrier, count(*) as NumFlights from FlightTable group by Carrier, UniqueCarrier order by NumFlights desc LIMIT 10")
output = spark.sql("SELECT AirlineName FROM AirlineCodes WHERE AirlineCode in (select Carrier from v)")

#show the top row without truncation
output.show(1, False)

#show the top 10 airlines
output.show(10, False)

#Determine which is the least on time airline

#create a temporary view to hold the flight delay information aggregated by airline, then select the airline name from the Airlinecodes dataframe
spark.sql("DROP VIEW IF EXISTS v")
spark.sql("CREATE TEMPORARY VIEW v AS SELECT Carrier, count(*) as NumFlights from FlightTable WHERE DepDelay>60 or ArrDelay>60 group by Carrier, UniqueCarrier order by NumFlights desc LIMIT 10")
output = spark.sql("select * from v")
#output = spark.sql("SELECT AirlineName FROM AirlineCodes WHERE AirlineCode in (select Carrier from v)")
#show the top row without truncation
output.show(1, False)

#which airline improved its performance
#find the airline with the most improvement in delays
#create a temporary view to hold the flight delay information aggregated by airline, then select the airline name from the Airlinecodes dataframe
spark.sql("DROP VIEW IF EXISTS v1")
spark.sql("DROP VIEW IF EXISTS v2")
spark.sql("CREATE TEMPORARY VIEW v1 AS SELECT Carrier, count(*) as NumFlights from FlightTable WHERE (DepDelay>0 or ArrDelay>0) and Year=2016 group by Carrier order by NumFlights desc LIMIT 10")
spark.sql("CREATE TEMPORARY VIEW v2 AS SELECT Carrier, count(*) as NumFlights from FlightTable WHERE (DepDelay>0 or ArrDelay>0) and Year=2017 group by Carrier order by NumFlights desc LIMIT 10")
output = spark.sql("SELECT distinct ac.AirlineName, v1.Carrier, v1.NumFlights, v2.NumFlights from v1 INNER JOIN v2 ON v1.Carrier = v2.Carrier INNER JOIN AirlineCodes ac ON v2.Carrier = ac.AirlineCode WHERE v1.NumFlights > v2.NumFlights")
#show the top row without truncation
output.show(10, False)

#display for visual analysis
display(output)
```

## <a name="next-steps"></a>Další kroky

* [Extrakce, transformace a načítání dat pomocí Apache Hivu ve službě Azure HDInsight](tutorial-extract-transform-load-hive.md)
