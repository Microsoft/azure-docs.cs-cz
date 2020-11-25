---
title: 'Kurz: Azure Data Lake Storage Gen2, Azure Databricks & Spark | Microsoft Docs'
description: V tomto kurzu se dozvíte, jak spouštět dotazy Spark na clusteru Azure Databricks pro přístup k datům v účtu úložiště Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: normesta
ms.reviewer: dineshm
ms.custom: devx-track-python
ms.openlocfilehash: 5fce5871b4bd6c3e2353f7df04018e88b86ec4c7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95912514"
---
# <a name="tutorial-azure-data-lake-storage-gen2-azure-databricks--spark"></a>Kurz: Azure Data Lake Storage Gen2, Azure Databricks & Spark

V tomto kurzu se dozvíte, jak připojit cluster Azure Databricks k datům uloženým v účtu úložiště Azure, který má povolený Azure Data Lake Storage Gen2. Toto připojení umožňuje nativně spouštět dotazy a analýzy z vašeho clusteru na vašich datech.

V tomto kurzu:

> [!div class="checklist"]
> * Vytvoření clusteru Databricks
> * Ingestace nestrukturovaných dat do účtu úložiště
> * Spuštění analýzy dat v úložišti objektů BLOB

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* Vytvořte účet Azure Data Lake Storage Gen2.

  Přečtěte si téma [Vytvoření účtu úložiště pro použití s Azure Data Lake Storage Gen2](create-data-lake-storage-account.md).

* Ujistěte se, že váš uživatelský účet má přiřazenou [roli Přispěvatel dat objektů BLOB úložiště](../common/storage-auth-aad-rbac-portal.md) .

* Nainstalujte AzCopy v10 za účelem. Viz [přenos dat pomocí AzCopy v10 za účelem](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

* Vytvoření instančního objektu. Viz [Postup: použití portálu k vytvoření aplikace a instančního objektu služby Azure AD, který má přístup k prostředkům](../../active-directory/develop/howto-create-service-principal-portal.md).

  K dispozici je několik konkrétních věcí, které budete muset udělat při provádění kroků v tomto článku.

  : heavy_check_mark: při provádění kroků v části [přiřazení aplikace k roli](../../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application) v článku se ujistěte, že k instančnímu objektu přiřadíte roli **Přispěvatel dat objektu BLOB služby Storage** .

  > [!IMPORTANT]
  > Ujistěte se, že roli přiřadíte v oboru účtu úložiště Data Lake Storage Gen2. K nadřazené skupině prostředků nebo předplatnému můžete přiřadit roli, ale chyby související s oprávněními obdržíte, dokud tato přiřazení role nerozšíříte do účtu úložiště.

  : heavy_check_mark: při provádění kroků v části [získat hodnoty pro přihlášení do](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) tohoto článku Vložte ID TENANTA, ID aplikace a hodnoty tajného klíče klienta do textového souboru. Budete je potřebovat brzy.

### <a name="download-the-flight-data"></a>Stažení letových údajů

V tomto kurzu se k předvedení operace ETL používá letová data z statistiky předsednictví. Tato data musíte stáhnout, abyste mohli kurz dokončit.

1. Přejít na [výzkum a inovativní správu technologií, statistiky dopravy](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time).

2. Pokud chcete vybrat všechna datová pole, zaškrtněte políčko pro **soubor ve formátu ZIP** .

3. Klikněte na tlačítko **Stáhnout** a uložte výsledky do svého počítače. 

4. Rozbalte obsah souboru zip a poznamenejte si název souboru a cestu k souboru. Tyto informace budete potřebovat v pozdějším kroku.

## <a name="create-an-azure-databricks-service"></a>Vytvoření služby Azure Databricks

V této části vytvoříte službu Azure Databricks pomocí Azure Portal.

1. V Azure Portal vyberte vytvořit Azure Databricks **prostředků**  >  **Analytics**  >  **Azure Databricks**.

    ![Datacihly na Azure Portal](./media/data-lake-storage-use-databricks-spark/azure-databricks-on-portal.png "Datacihly na Azure Portal")

2. V části **Azure Databricks služba** zadejte následující hodnoty pro vytvoření služby datacihly:

    |Vlastnost  |Popis  |
    |---------|---------|
    |**Název pracovního prostoru**     | Zadejte název pracovního prostoru Databricks.  |
    |**Předplatné**     | Z rozevíracího seznamu vyberte své předplatné Azure.        |
    |**Skupina prostředků**     | Určete, jestli chcete vytvořit novou skupinu prostředků, nebo použít existující. Skupina prostředků je kontejner, který uchovává související prostředky pro řešení Azure. Další informace naleznete v tématu [Přehled skupin prostředků v Azure](../../azure-resource-manager/management/overview.md). |
    |**Umístění**     | Vyberte **USA – západ 2**. Další dostupné oblasti najdete v tématu [Dostupné služby Azure podle oblastí](https://azure.microsoft.com/regions/services/).       |
    |**Cenová úroveň**     |  Vyberte **Standard**.     |

    ![Vytvoření pracovního prostoru Azure Databricks](./media/data-lake-storage-use-databricks-spark/create-databricks-workspace.png "Vytvoření služby Azure Databricks")

3. Vytvoření účtu trvá několik minut. Chcete-li monitorovat stav operace, zobrazte indikátor průběhu v horní části.

4. Vyberte **Připnout na řídicí panel** a potom vyberte **Vytvořit**.

## <a name="create-a-spark-cluster-in-azure-databricks"></a>Vytvoření clusteru Spark v Azure Databricks

1. V Azure Portal otevřete službu datacihly, kterou jste vytvořili, a vyberte **Spustit pracovní prostor**.

2. Budete přesměrováni na portál Azure Databricks. Na portálu vyberte **Cluster**.

    ![Datacihly v Azure](./media/data-lake-storage-use-databricks-spark/databricks-on-azure.png "Datacihly v Azure")

3. Na stránce **New cluster** (Nový cluster) zadejte hodnoty pro vytvoření clusteru.

    ![Vytvoření clusteru datacihly Spark v Azure](./media/data-lake-storage-use-databricks-spark/create-databricks-spark-cluster.png "Vytvoření clusteru datacihly Spark v Azure")

    Zadejte hodnoty následujících polí a potvrďte výchozí hodnoty dalších polí:

    - Zadejte název clusteru.
     
    - Nezapomeňte zaškrtnout políčko **Terminate after 120 minutes of inactivity** (Ukončit po 120 minutách nečinnosti). Zadejte dobu (v minutách), po které se má ukončit činnost clusteru, pokud se cluster nepoužívá.

4. Vyberte **vytvořit cluster**. Po spuštění clusteru můžete ke clusteru připojit poznámkové bloky a spouštět úlohy Spark.

## <a name="ingest-data"></a>Ingestace dat

### <a name="copy-source-data-into-the-storage-account"></a>Zkopírování zdrojových dat do účtu úložiště

Pomocí AzCopy zkopírujte data ze souboru *. csv* do účtu Data Lake Storage Gen2.

1. Otevřete okno příkazového řádku a zadejte následující příkaz, který se přihlásí k účtu úložiště.

   ```bash
   azcopy login
   ```

   Postupujte podle pokynů, které se zobrazí v okně příkazového řádku, a ověřte svůj uživatelský účet.

2. Pokud chcete kopírovat data z účtu *. csv* , zadejte následující příkaz.

   ```bash
   azcopy cp "<csv-folder-path>" https://<storage-account-name>.dfs.core.windows.net/<container-name>/folder1/On_Time.csv
   ```

   * Nahraďte `<csv-folder-path>` hodnotu zástupného symbolu cestou k souboru *. csv* .

   * Nahraďte `<storage-account-name>` hodnotu zástupného symbolu názvem vašeho účtu úložiště.

   * `<container-name>`Zástupný symbol nahraďte názvem kontejneru v účtu úložiště.

## <a name="create-a-container-and-mount-it"></a>Vytvoření kontejneru a jeho připojení

V této části vytvoříte kontejner a složku ve svém účtu úložiště.

1. V [Azure Portal](https://portal.azure.com)otevřete službu Azure Databricks, kterou jste vytvořili, a vyberte **Spustit pracovní prostor**.

2. Na levé straně vyberte **pracovní prostor**. V rozevíracím seznamu **Pracovní prostor** vyberte **Vytvořit** > **Poznámkový blok**.

    ![Vytvoření poznámkového bloku v datacihlech](./media/data-lake-storage-use-databricks-spark/databricks-create-notebook.png "Vytvoření poznámkového bloku v datacihlech")

3. V dialogovém okně **Vytvořit poznámkový blok** zadejte název poznámkového bloku. Jako jazyk vyberte **Python** a pak vyberte cluster Spark, který jste vytvořili dříve.

4. Vyberte **Vytvořit**.

5. Zkopírujte následující blok kódu a vložte ho do první buňky, ale tento kód ještě nespouštějte.

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

18. V tomto bloku kódu nahraďte `appId` `clientSecret` `tenant` `storage-account-name` zástupné hodnoty,, a v tomto bloku kódu hodnotami, které jste shromáždili při dokončování požadavků tohoto kurzu. Nahraďte `container-name` hodnotu zástupného symbolu názvem kontejneru.

19. Stiskněte klávesy **SHIFT + ENTER** a spusťte kód v tomto bloku.

   Ponechte tento poznámkový blok otevřený a později do něj přidejte příkazy.

### <a name="use-databricks-notebook-to-convert-csv-to-parquet"></a>Použití poznámkového bloku Databricks k převodu CSV na formát Parquet

V poznámkovém bloku, který jste vytvořili dříve, přidejte novou buňku a vložte do této buňky následující kód. 

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

Do nové buňky vložte následující kód, který načte seznam souborů CSV odeslaných prostřednictvím AzCopy.

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

* Nahraďte `<csv-folder-path>` hodnotu zástupného symbolu cestou k souboru *. csv* .

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

Tento skript zadejte, pokud chcete spustit některé základní dotazy analýzy pro data.

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

Pokud už je nepotřebujete, odstraňte skupinu prostředků a všechny související prostředky. Provedete to tak, že vyberete skupinu prostředků pro účet úložiště a vyberete **Odstranit**.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"] 
> [Extrakce, transformace a načítání dat pomocí Apache Hivu ve službě Azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md)