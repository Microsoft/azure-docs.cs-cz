---
title: 'Kurz: Přístup k datům v Azure Data Lake Storage Gen2 Preview v Azure Databricks pomocí Sparku | Microsoft Docs'
description: Tento kurz ukazuje, jak spustit dotazy Spark na clusteru Azure Databricks pro přístup k datům v účtu úložiště Azure Data Lake Storage Gen2.
services: storage
author: dineshmurthy
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 01/14/2019
ms.author: dineshm
ms.openlocfilehash: e72a4f71a42a892d14fad076b124426f0c32ac7d
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2019
ms.locfileid: "54321802"
---
# <a name="tutorial-access-data-lake-storage-gen2-preview-data-with-azure-databricks-using-spark"></a>Kurz: Přístup k verzi Preview služby Data Lake Storage Gen2 dat pomocí Azure Databricks pomocí Spark

V tomto kurzu se dozvíte, jak se připojit k datům uloženým v účtu úložiště Azure s Azure Data Lake Storage Gen2 cluster Azure Databricks (Preview) povolena. Toto připojení vám umožňuje nativně dotazy a analýzy můžete spouštět z clusteru s vašimi daty.

V tomto kurzu provedete následující:

> [!div class="checklist"]
> * Vytvoření clusteru Databricks
> * Ingestace nestrukturovaných dat do účtu úložiště
> * Spuštění analýzy dat v úložišti objektů blob

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

V tomto kurzu si ukážeme, jak využívat a dotazovat se na data o odletech aerolinek, která dává k dispozici [ministerstvo dopravy USA](https://transtats.bts.gov/DL_SelectFields.asp). 

1. Vyberte **Prezipped souboru** zaškrtávací políčko Vybrat všechna datová pole.
2. Vyberte **Stáhnout** a uložte výsledky do svého počítače.
3. Poznamenejte si název souboru a cestu souboru ke stažení; Tyto informace v pozdějším kroku budete potřebovat.

K dokončení tohoto kurzu, budete potřebovat účet úložiště s možností analýzy jednotlivých. Doporučujeme, abyste dokončení našich [rychlý Start](data-lake-storage-quickstart-create-account.md) k tomuto tématu, aby bylo možné vytvořit. 

## <a name="set-aside-storage-account-configuration"></a>Odložení konfigurace účtu úložiště

Budete potřebovat název účtu úložiště a koncovým bodem systému souborů identifikátoru URI.

Pokud chcete získat název účtu úložiště na webu Azure Portal, zvolte **všechny služby** a filtrováním podle termín *úložiště*. Vyberte **účty úložiště** a vyhledejte svůj účet úložiště.

Pokud chcete získat koncový bod systému souborů identifikátoru URI, zvolte **vlastnosti**a v podokně vlastností najít hodnotu **primární ADLS koncový bod SOUBOROVÉ systému** pole.

Vložte obě tyto hodnoty do textového souboru. Brzy je budete potřebovat.

<a id="service-principal"/>

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

Vytvoření instančního objektu služby podle pokynů v tomto tématu: [Postup: Použití portálu k vytvoření aplikace a instančního objektu, který má přístup k prostředkům Azure AD](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

Existuje několik určité akce, které budete muset udělat při provádění kroků v tomto článku.

:heavy_check_mark: Při provádění kroků v [vytvoření aplikace Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application) části tohoto článku, nezapomeňte nastavit **přihlašovací adresa URL** pole **vytvořit** dialogové okno pro identifikátor URI koncového bodu právě shromažďují.

:heavy_check_mark: Při provádění kroků v [přiřazení aplikace k roli](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) části tohoto článku, nezapomeňte přiřadit aplikaci do **Role Přispěvatel úložiště objektů Blob**.

:heavy_check_mark: Při provádění kroků v [získání hodnot pro přihlášení](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) část článku, vložte ID tenanta, ID aplikace a hodnoty klíče ověřování do textového souboru. Brzy ty budete potřebovat.

## <a name="create-a-databricks-cluster"></a>Vytvoření clusteru Databricks

Dalším krokem je vytvoření clusteru Databricks vytvořit pracovní prostor data.

1. Z [webu Azure portal](https://portal.azure.com)vyberte **vytvořit prostředek**.
2. Zadejte **Azure Databricks** do vyhledávacího pole.
3. Vyberte **vytvořit** v okně Azure Databricks.
4. Pojmenujte službu Databricks **myFlightDataService** (ujistěte se, že ke kontrole *připnout na řídicí panel* zaškrtávací políčko při vytvoření služby).
5. Vyberte **spustit pracovní prostor** otevřete pracovní prostor v novém okně prohlížeče.
6. Vyberte **clustery** v levém navigačním panelu.
7. Vyberte **vytvoření clusteru**.
8. Do pole s **názvem clusteru** zadejte **myFlightDataCluster**.
9. V poli **typu pracovního procesu** vyberte **Standard_D8s_v3**.
10. Hodnotu **minimálního počtu pracovních procesů** změňte na **4**.
11. Vyberte **vytvořit Cluster** v horní části stránky. (Tento proces může trvat až 5 minut na dokončení.)
12. Po dokončení procesu vyberte **Azure Databricks** v levém horním rohu navigačního panelu.
13. V dolní polovině stránky vyberte v oddílu **Nový** položku **Poznámkový blok**.
14. Zadejte název zvoleného v **název** pole a vyberte **Python** jako jazyk.
15. Ve všech ostatních polích můžete nechat výchozí hodnoty.
16. Vyberte **Vytvořit**.
17. Zkopírujte a vložte následující blok kódu do první buňky, ale není ještě tento kód spustit.

    ```Python
    configs = {"fs.azure.account.auth.type": "OAuth",
           "fs.azure.account.oauth.provider.type": "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
           "fs.azure.account.oauth2.client.id": "<application-id>",
           "fs.azure.account.oauth2.client.secret": "<authentication-id>",
           "fs.azure.account.oauth2.client.endpoint": "https://login.microsoftonline.com/<tenant-id>/oauth2/token",
           "fs.azure.createRemoteFileSystemDuringInitialization": "true"}

    dbutils.fs.mount(
    source = "abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/folder1",
    mount_point = "/mnt/flightdata",
    extra_configs = configs)
    ```
18. V tomto bloku kódu, nahraďte `storage-account-name`, `application-id`, `authentication-id`, a `tenant-id` zástupné hodnoty hodnotami, které jste shromáždili, když jste dokončili kroky v v tomto bloku kódu [vyhradit účet úložiště konfigurace](#config) a [vytvoření instančního objektu](#service-principal) částech tohoto článku. Nahradit `file-system-name` zástupný symbol libovolný název, který chcete udělit systému souborů.

19. Stisknutím klávesy **SHIFT + ENTER** klíče pro spuštění kódu v tomto bloku.

## <a name="ingest-data"></a>Ingestace dat

### <a name="copy-source-data-into-the-storage-account"></a>Zkopírování zdrojových dat do účtu úložiště

Dalším úkolem je zkopírovat data ze souboru *.csv* do úložiště Azure nástrojem AzCopy. Otevřete okno příkazového řádku a zadejte následující příkazy. Nezapomeňte nahradit zástupné symboly `<DOWNLOAD_FILE_PATH>`, `<ACCOUNT_NAME>`, a `<ACCOUNT_KEY>` s odpovídajícími hodnotami, které jste nastavili jste si poznamenali v předchozím kroku.

```bash
set ACCOUNT_NAME=<ACCOUNT_NAME>
set ACCOUNT_KEY=<ACCOUNT_KEY>
azcopy cp "<DOWNLOAD_FILE_PATH>" https://<ACCOUNT_NAME>.dfs.core.windows.net/dbricks/folder1/On_Time --recursive 
```

### <a name="use-databricks-notebook-to-convert-csv-to-parquet"></a>Použití poznámkového bloku Databricks k převodu CSV na formát Parquet

Znovu otevřete v prohlížeči Databricks a proveďte následující kroky:

1. Vyberte **Azure Databricks** v levém horním rohu navigačního panelu.
2. V dolní polovině stránky vyberte v oddílu **Nový** položku **Poznámkový blok**.
3. Do pole **Název** zadejte **CSV2Parquet**.
4. Ve všech ostatních polích můžete nechat výchozí hodnoty.
5. Vyberte **Vytvořit**.
6. Vložte následující kód do **Cmd 1** buňky. (Tento kód automaticky – uloží v editoru.)

    ```python
    # Use the previously established DBFS mount point to read the data
    # create a dataframe to read data
    flightDF = spark.read.format('csv').options(header='true', inferschema='true').load("/mnt/flightdata/On_Time_On_Time*.csv")
    # read the all the airline csv files and write the output to parquet format for easy query
    flightDF.write.mode("append").parquet("/mnt/flightdata/parquet/flights")
    print("Done")
    ```

## <a name="explore-data"></a>Zkoumání dat

Vraťte se do pracovního prostoru Databricks a vyberte **poslední** ikony v levém navigačním panelu.

1. Vyberte **letu Data Analytics** poznámkového bloku.
2. Stisknutím kombinace kláves **Ctrl+Alt+N** vytvořte novou buňku.

Do buňky **Cmd 1** zadejte všechny následující bloky kódu a stiskněte **Cmd+Enter**, abyste spustili skript Pythonu.

Pokud chcete získat seznam souborů CSV nahraných přes AzCopy, spusťte následující skript:

```python
import os.path
import IPython
from pyspark.sql import SQLContext
display(dbutils.fs.ls("/mnt/flightdata/temp/"))
```

Pokud chcete vytvořit nový soubor a zobrazit seznam souborů ve složce *parquet/flights*, spusťte tento skript:

```python
dbutils.fs.put("/mnt/flightdata/temp/1.txt", "Hello, World!", True)
dbutils.fs.ls("/mnt/flightdata/temp/parquet/flights")
```

Na těchto vzorových kódech jste prozkoumali hierarchickou povahu systému souborů HDFS s využitím dat uložených v účtu úložiště s povolenou službou Data Lake Storage Gen2.

## <a name="query-the-data"></a>Vytváření dotazů na data

Teď můžete začít vytvářet dotazy na data, která jste nahráli do svého účtu úložiště. Do buňky **Cmd 1** zadejte všechny následující bloky kódu a stiskněte **Cmd+Enter**, abyste spustili skript Pythonu.

### <a name="run-simple-queries"></a>Spuštění jednoduchých dotazů

Pokud chcete vytvořit datové rámce zdrojů dat, spusťte následující skript:

> [!IMPORTANT]
> Nezapomeňte nahradit zástupný symbol **<YOUR_CSV_FILE_NAME>** názvem souboru, který jste si stáhli na začátku tohoto kurzu.

```python
#Copy this into a Cmd cell in your notebook.
acDF = spark.read.format('csv').options(header='true', inferschema='true').load("/mnt/flightdata/<YOUR_CSV_FILE_NAME>.csv")
acDF.write.parquet('/mnt/flightdata/parquet/airlinecodes')

#read the existing parquet file for the flights database that was created earlier
flightDF = spark.read.format('parquet').options(header='true', inferschema='true').load("/mnt/flightdata/parquet/flights")

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
#preferably run this in a separate cmd cell
display(flightDF)
```

Pokud chcete spustit dotazy, které analyzují data, spusťte následující skript:

```python
#Run each of these queries, preferably in a separate cmd cell for separate analysis
#create a temporary sql view for querying flight information
FlightTable = spark.read.parquet('/mnt/flightdata/parquet/flights')
FlightTable.createOrReplaceTempView('FlightTable')

#create a temporary sql view for querying airline code information
AirlineCodes = spark.read.parquet('/mnt/flightdata/parquet/airlinecodes')
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

### <a name="run-complex-queries"></a>Spouštění složitých dotazů

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

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste už nepotřebujete, odstraňte skupinu prostředků a všechny související prostředky. Uděláte to tak, vyberte skupinu prostředků pro účet úložiště a vyberte **odstranit**.

## <a name="next-steps"></a>Další postup

[!div class="nextstepaction"] 
> [Extrakce, transformace a načítání dat pomocí Apache Hivu ve službě Azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md)

