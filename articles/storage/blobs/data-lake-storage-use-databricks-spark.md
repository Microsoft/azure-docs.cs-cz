---
title: 'Kurz: Azure Data Lake Storage Gen2, Azure Databricks & Spark | Dokumenty společnosti Microsoft'
description: Tento kurz ukazuje, jak spustit dotazy Spark v clusteru Azure Databricks pro přístup k datům v účtu úložiště Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 5889afa033b30606f8981ddb826aa192f24efa10
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312917"
---
# <a name="tutorial-azure-data-lake-storage-gen2-azure-databricks--spark"></a>Kurz: Azure Data Lake Storage Gen2, Azure Databricks & Spark

Tento kurz ukazuje, jak připojit cluster Azure Databricks k datům uloženým v účtu úložiště Azure, který má povolenou Azure Data Lake Storage Gen2. Toto připojení umožňuje nativně spouštět dotazy a analýzy z vašeho clusteru na vašich datech.

V tomto kurzu provedete následující:

> [!div class="checklist"]
> * Vytvoření clusteru Databricks
> * Ingestace nestrukturovaných dat do účtu úložiště
> * Spouštění analýz na datech v úložišti objektů Blob

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

* Vytvořte účet Azure Data Lake Storage Gen2.

  Viz [Vytvoření účtu Azure Data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md).

* Ujistěte se, že váš uživatelský účet má [roli přispěvatele dat objektů blob úložiště](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac) přiřazenou.

* Nainstalujte AzCopy v10. Viz [Přenos dat pomocí AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

* Vytvořte instanční objekt. Viz [Postup: Pomocí portálu vytvořte instanční objekt azure a služby, který má přístup k prostředkům](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

  Existuje několik konkrétních věcí, které budete muset udělat, když provedete kroky v tomto článku.

  :heavy_check_mark: Při provádění kroků v části [Přiřazení aplikace k](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application) části role článku nezapomeňte přiřadit roli **přispěvatele dat objektů blob úložiště** k instančnímu objektu.

  > [!IMPORTANT]
  > Nezapomeňte přiřadit roli v oboru účtu úložiště Storage Storage Data Lake Storage. Roli můžete přiřadit nadřazené skupině prostředků nebo předplatnému, ale budete dostávat chyby související s oprávněními, dokud se tato přiřazení rolí nerozšíří do účtu úložiště.

  :heavy_check_mark: Při provádění kroků v části [Získat hodnoty pro podepisování v](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) článku vložte ID klienta, ID aplikace a tajné hodnoty klienta do textového souboru. Brzy je budeš potřebovat.

### <a name="download-the-flight-data"></a>Stažení letových údajů

Tento kurz používá letová data z Úřadu pro statistiku dopravy k předvedení, jak provést operaci ETL. Chcete-li dokončit kurz, je nutné tato data stáhnout.

1. Přejít na [výzkum a inovativní technologie správy, Úřad pro dopravní statistiky](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time).

2. Zaškrtnutím **políčka Prezip File** zaškrtněte všechna datová pole.

3. Vyberte tlačítko **Stáhnout** a uložte výsledky do počítače. 

4. Rozbalte obsah zip souboru zip a poznamenejte si název souboru a cestu k souboru. Tyto informace potřebujete v pozdějším kroku.

## <a name="create-an-azure-databricks-service"></a>Vytvoření služby Azure Databricks

V této části vytvoříte službu Azure Databricks pomocí portálu Azure.

1. Na webu Azure Portal vyberte **Vytvořit zdroj** > **Analytics** > **Azure Databricks**.

    ![Datové cihly na webu Azure Portal](./media/data-lake-storage-use-databricks-spark/azure-databricks-on-portal.png "Datové cihly na webu Azure Portal")

2. V části **Azure Databricks Service**zadejte následující hodnoty pro vytvoření služby Databricks:

    |Vlastnost  |Popis  |
    |---------|---------|
    |**Název pracovního prostoru**     | Zadejte název pracovního prostoru Databricks.  |
    |**Předplatné**     | Z rozevíracího seznamu vyberte své předplatné Azure.        |
    |**Skupina prostředků**     | Určete, jestli chcete vytvořit novou skupinu prostředků, nebo použít existující. Skupina prostředků je kontejner, který obsahuje související prostředky pro řešení Azure. Další informace naleznete v tématu [Přehled skupin prostředků v Azure](../../azure-resource-manager/management/overview.md). |
    |**Umístění**     | Vyberte **USA – západ 2**. Další dostupné oblasti najdete v tématu [Dostupné služby Azure podle oblastí](https://azure.microsoft.com/regions/services/).       |
    |**Cenová úroveň**     |  Vyberte **standardní**.     |

    ![Vytvoření pracovního prostoru Azure Databricks](./media/data-lake-storage-use-databricks-spark/create-databricks-workspace.png "Vytvoření služby Azure Databricks")

3. Vytvoření účtu trvá několik minut. Chcete-li sledovat stav operace, zobrazte indikátor průběhu nahoře.

4. Vyberte **Připnout na řídicí panel** a potom vyberte **Vytvořit**.

## <a name="create-a-spark-cluster-in-azure-databricks"></a>Vytvoření clusteru Spark v Azure Databricks

1. Na webu Azure Portal přejděte na službu Databricks, kterou jste vytvořili, a vyberte **Spustit pracovní prostor**.

2. Budete přesměrováni na portál Azure Databricks. Na portálu vyberte **Cluster**.

    ![Datové cihly v Azure](./media/data-lake-storage-use-databricks-spark/databricks-on-azure.png "Datové cihly v Azure")

3. Na stránce **New cluster** (Nový cluster) zadejte hodnoty pro vytvoření clusteru.

    ![Vytvoření clusteru Databricks Spark v Azure](./media/data-lake-storage-use-databricks-spark/create-databricks-spark-cluster.png "Vytvoření clusteru Databricks Spark v Azure")

    Zadejte hodnoty následujících polí a potvrďte výchozí hodnoty dalších polí:

    - Zadejte název clusteru.
     
    - Nezapomeňte zaškrtnout políčko **Terminate after 120 minutes of inactivity** (Ukončit po 120 minutách nečinnosti). Zadejte dobu (v minutách), po které se má ukončit činnost clusteru, pokud se cluster nepoužívá.

4. Vyberte **Vytvořit cluster**. Po spuštění clusteru můžete k clusteru připojit poznámkové bloky a spustit úlohy Spark.

## <a name="ingest-data"></a>Ingestace dat

### <a name="copy-source-data-into-the-storage-account"></a>Zkopírování zdrojových dat do účtu úložiště

Pomocí aplikace AzCopy zkopírujte data ze souboru *.csv* do účtu Data Lake Storage Gen2.

1. Otevřete okno příkazového řádku a zadejte následující příkaz pro přihlášení k účtu úložiště.

   ```bash
   azcopy login
   ```

   Podle pokynů, které se zobrazí v okně příkazového řádku, ověřte svůj uživatelský účet.

2. Chcete-li kopírovat data z účtu *.csv,* zadejte následující příkaz.

   ```bash
   azcopy cp "<csv-folder-path>" https://<storage-account-name>.dfs.core.windows.net/<container-name>/folder1/On_Time.csv
   ```

   * Nahraďte `<csv-folder-path>` zástupnou hodnotu cestou k souboru *.csv.*

   * `<storage-account-name>` Nahraďte zástupnou hodnotu názvem svého účtu úložiště.

   * Nahraďte `<container-name>` zástupný symbol názvem kontejneru v účtu úložiště.

## <a name="create-a-container-and-mount-it"></a>Vytvoření kontejneru a jeho připojení

V této části vytvoříte kontejner a složku ve vašem účtu úložiště.

1. Na [webu Azure Portal](https://portal.azure.com)přejděte na službu Azure Databricks, kterou jste vytvořili, a vyberte **Spustit pracovní prostor**.

2. Vlevo vyberte **Pracovní prostor**. V rozevíracím seznamu **Pracovní prostor** vyberte **Vytvořit** > **Poznámkový blok**.

    ![Vytvoření poznámkového bloku v datové cihly](./media/data-lake-storage-use-databricks-spark/databricks-create-notebook.png "Vytvoření poznámkového bloku v datových cihlách")

3. V dialogovém okně **Vytvořit poznámkový blok** zadejte název poznámkového bloku. Vyberte **Python** jako jazyk a pak vyberte cluster Spark, který jste vytvořili dříve.

4. Vyberte **Vytvořit**.

5. Zkopírujte a vložte následující blok kódu do první buňky, ale tento kód ještě nespouštějte.

    ```Python
    configs = {"fs.azure.account.auth.type": "OAuth",
           "fs.azure.account.oauth.provider.type": "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
           "fs.azure.account.oauth2.client.id": "<appId>",
           "fs.azure.account.oauth2.client.secret": "<clientSecret>",
           "fs.azure.account.oauth2.client.endpoint": "https://login.microsoftonline.com/<tenant>/oauth2/token",
           "fs.azure.createRemoteFileSystemDuringInitialization": "true"}

    dbutils.fs.mount(
    source = "abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/folder1",
    mount_point = "/mnt/flightdata",
    extra_configs = configs)
    ```

18. V tomto bloku kódu `appId` `clientSecret`nahraďte hodnoty , , `tenant`a `storage-account-name` zástupný symbol v tomto bloku kódu hodnotami, které jste shromáždili při vyplňování předpokladů tohoto kurzu. Nahraďte `container-name` zástupnou hodnotu názvem kontejneru.

19. Stisknutím kláves **SHIFT + ENTER** spusťte kód v tomto bloku.

   Ponechte tento poznámkový blok otevřený, protože k němu později přidáte příkazy.

### <a name="use-databricks-notebook-to-convert-csv-to-parquet"></a>Použití poznámkového bloku Databricks k převodu CSV na formát Parquet

Do dříve vytvořeného poznámkového bloku přidejte novou buňku a do této buňky vložte následující kód. 

```python
# Use the previously established DBFS mount point to read the data.
# create a data frame to read data.

flightDF = spark.read.format('csv').options(
    header='true', inferschema='true').load("/mnt/flightdata/*.csv")

# read the airline csv file and write the output to parquet format for easy query.
flightDF.write.mode("append").parquet("/mnt/flightdata/parquet/flights")
print("Done")
```

## <a name="explore-data"></a>Zkoumání dat

Do nové buňky vložte následující kód, abyste získali seznam souborů CSV nahraných přes AzCopy.

```python
import os.path
import IPython
from pyspark.sql import SQLContext
display(dbutils.fs.ls("/mnt/flightdata"))
```

Pokud chcete vytvořit nový soubor a zobrazit seznam souborů ve složce *parquet/flights*, spusťte tento skript:

```python
dbutils.fs.put("/mnt/flightdata/1.txt", "Hello, World!", True)
dbutils.fs.ls("/mnt/flightdata/parquet/flights")
```

Na těchto vzorových kódech jste prozkoumali hierarchickou povahu systému souborů HDFS s využitím dat uložených v účtu úložiště s povolenou službou Data Lake Storage Gen2.

## <a name="query-the-data"></a>Vytváření dotazů na data

Teď můžete začít vytvářet dotazy na data, která jste nahráli do svého účtu úložiště. Do buňky **Cmd 1** zadejte všechny následující bloky kódu a stiskněte **Cmd+Enter**, abyste spustili skript Pythonu.

Chcete-li vytvořit datové rámce pro zdroje dat, spusťte následující skript:

* Nahraďte `<csv-folder-path>` zástupnou hodnotu cestou k souboru *.csv.*

```python
# Copy this into a Cmd cell in your notebook.
acDF = spark.read.format('csv').options(
    header='true', inferschema='true').load("/mnt/flightdata/On_Time.csv")
acDF.write.parquet('/mnt/flightdata/parquet/airlinecodes')

# read the existing parquet file for the flights database that was created earlier
flightDF = spark.read.format('parquet').options(
    header='true', inferschema='true').load("/mnt/flightdata/parquet/flights")

# print the schema of the dataframes
acDF.printSchema()
flightDF.printSchema()

# print the flight database size
print("Number of flights in the database: ", flightDF.count())

# show the first 20 rows (20 is the default)
# to show the first n rows, run: df.show(n)
acDF.show(100, False)
flightDF.show(20, False)

# Display to run visualizations
# preferably run this in a separate cmd cell
display(flightDF)
```

Zadejte tento skript a spusťte některé základní analytické dotazy proti datům.

```python
# Run each of these queries, preferably in a separate cmd cell for separate analysis
# create a temporary sql view for querying flight information
FlightTable = spark.read.parquet('/mnt/flightdata/parquet/flights')
FlightTable.createOrReplaceTempView('FlightTable')

# create a temporary sql view for querying airline code information
AirlineCodes = spark.read.parquet('/mnt/flightdata/parquet/airlinecodes')
AirlineCodes.createOrReplaceTempView('AirlineCodes')

# using spark sql, query the parquet file to return total flights in January and February 2016
out1 = spark.sql("SELECT * FROM FlightTable WHERE Month=1 and Year= 2016")
NumJan2016Flights = out1.count()
out2 = spark.sql("SELECT * FROM FlightTable WHERE Month=2 and Year= 2016")
NumFeb2016Flights = out2.count()
print("Jan 2016: ", NumJan2016Flights, " Feb 2016: ", NumFeb2016Flights)
Total = NumJan2016Flights+NumFeb2016Flights
print("Total flights combined: ", Total)

# List out all the airports in Texas
out = spark.sql(
    "SELECT distinct(OriginCityName) FROM FlightTable where OriginStateName = 'Texas'")
print('Airports in Texas: ', out.show(100))

# find all airlines that fly from Texas
out1 = spark.sql(
    "SELECT distinct(Reporting_Airline) FROM FlightTable WHERE OriginStateName='Texas'")
print('Airlines that fly to/from Texas: ', out1.show(100, False))
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nejsou potřeba, odstraňte skupinu prostředků a všechny související prostředky. Chcete-li tak učinit, vyberte skupinu prostředků pro účet úložiště a vyberte **odstranit**.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"] 
> [Extrakce, transformace a načítání dat pomocí Apache Hivu ve službě Azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md)
