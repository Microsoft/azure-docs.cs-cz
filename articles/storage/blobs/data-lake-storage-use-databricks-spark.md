---
title: 'Kurz: Přístup k Azure Data Lake Storage Gen2 dat pomocí Azure Databricks pomocí Spark | Dokumentace Microsoftu'
description: Tento kurz ukazuje, jak spustit dotazy Spark na clusteru Azure Databricks pro přístup k datům v účtu úložiště Azure Data Lake Storage Gen2.
services: storage
author: dineshmurthy
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 01/29/2019
ms.author: dineshm
ms.openlocfilehash: e448ef0de9ef5560c1b4ea0df5c02e8efd8c0ea9
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55891653"
---
# <a name="tutorial-access-data-lake-storage-gen2-data-with-azure-databricks-using-spark"></a>Kurz: Data Lake Storage Gen2 pro přístup k datům Azure Databricks pomocí Spark

V tomto kurzu se dozvíte, jak se připojit k datům uloženým v účtu úložiště Azure s Azure Data Lake Storage Gen2 povolené cluster Azure Databricks. Toto připojení vám umožňuje nativně dotazy a analýzy můžete spouštět z clusteru s vašimi daty.

V tomto kurzu provedete následující:

> [!div class="checklist"]
> * Vytvoření clusteru Databricks
> * Ingestace nestrukturovaných dat do účtu úložiště
> * Spouštět analýzy na data v úložišti objektů Blob

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* Vytvoření účtu Azure Data Lake Storage Gen2.

  Zobrazit [vytvoření účtu Azure Data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md).

* Ujistěte se, že váš uživatelský účet má [role Přispěvatel dat objektu Blob úložiště](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac) přiřazené.

* Nainstalujte AzCopy v10. Zobrazit [přenos dat pomocí AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

### <a name="download-the-flight-data"></a>Stažení letových údajů

Tento kurz používá zapisovači letových údajů z kanceláře Transportation statistiky a ukazuje, jak k provádění operací ETL. Tato data k dokončení tohoto kurzu musíte stáhnout.

1. Přejděte na [výzkumu a inovativní technologie správy, Bureau of Transportation statistiky](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time).

2. Vyberte **Prezipped souboru** zaškrtávací políčko Vybrat všechna datová pole.

3. Vyberte **Stáhnout** tlačítko a uložte výsledky do počítače. 

4. Rozbalte obsah souboru ZIP a poznamenejte si název souboru a cestu k souboru. Tyto informace v pozdějším kroku budete potřebovat.

## <a name="get-your-storage-account-name"></a>Získejte název svého účtu úložiště

Budete potřebovat název účtu úložiště. Chcete-li získat, přihlaste se [webu Azure portal](https://portal.azure.com/), zvolte **všechny služby** a filtrováním podle termín *úložiště*. Vyberte **účty úložiště** a vyhledejte svůj účet úložiště.

Vložte název do textového souboru. Brzy je budete potřebovat.

<a id="service-principal"/>

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

Vytvoření instančního objektu služby podle pokynů v tomto tématu: [Postup: Použití portálu k vytvoření aplikace a instančního objektu, který má přístup k prostředkům Azure AD](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

Existuje několik věcí, které budete muset udělat při provádění kroků v tomto článku.

:heavy_check_mark: Při provádění kroků v [přiřazení aplikace k roli](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) části tohoto článku, nezapomeňte přiřadit aplikaci do **Role Přispěvatel úložiště objektů Blob**.

:heavy_check_mark: Při provádění kroků v [získání hodnot pro přihlášení](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) část článku, vložte ID tenanta, ID aplikace a hodnoty klíče ověřování do textového souboru. Brzy ty budete potřebovat.

## <a name="create-an-azure-databricks-service"></a>Vytvoření služby Azure Databricks

V této části Vytvoření služby Azure Databricks s využitím webu Azure portal.

1. Na webu Azure Portal vyberte **Vytvořit prostředek** > **Analýza** > **Azure Databricks**.

    ![Databricks na webu Azure Portal](./media/data-lake-storage-use-databricks-spark/azure-databricks-on-portal.png "Databricks na webu Azure Portal")

2. V části **služba Azure Databricks**, zadejte následující hodnoty pro vytvoření služby Databricks:

    |Vlastnost  |Popis  |
    |---------|---------|
    |**Název pracovního prostoru**     | Zadejte název pracovního prostoru Databricks.  |
    |**Předplatné**     | Z rozevíracího seznamu vyberte své předplatné Azure.        |
    |**Skupina prostředků**     | Určete, jestli chcete vytvořit novou skupinu prostředků, nebo použít existující. Skupina prostředků je kontejner, který obsahuje související prostředky pro řešení Azure. Další informace naleznete v tématu [Přehled skupin prostředků v Azure](../../azure-resource-manager/resource-group-overview.md). |
    |**Umístění**     | Vyberte **Západní USA 2**. Další dostupné oblasti najdete v tématu [Dostupné služby Azure podle oblastí](https://azure.microsoft.com/regions/services/).       |
    |**Cenová úroveň**     |  Vyberte **standardní**.     |

    ![Vytvoření pracovního prostoru Azure Databricks](./media/data-lake-storage-use-databricks-spark/create-databricks-workspace.png "vytvoření služby Azure Databricks")

3. Vyberte **Připnout na řídicí panel** a potom vyberte **Vytvořit**.

4. Vytvoření účtu trvá několik minut. Během vytváření účtu na portálu se zobrazí **odesílá se nasazení pro Azure Databricks** dlaždice na pravé straně. Pokud chcete monitorovat stav operace, zobrazte indikátor průběhu v horní části.

    ![Dlaždice nasazení Databricks](./media/data-lake-storage-use-databricks-spark/databricks-deployment-tile.png "Dlaždice nasazení Databricks")

## <a name="create-a-spark-cluster-in-azure-databricks"></a>Vytvoření clusteru Spark v Azure Databricks

1. Na webu Azure Portal, přejděte na službu Databricks, který jste vytvořili a vyberte **spustit pracovní prostor**.

2. Budete přesměrováni na portál Azure Databricks. Na portálu vyberte **Cluster**.

    ![Databricks v Azure](./media/data-lake-storage-use-databricks-spark/databricks-on-azure.png "Databricks v Azure")

3. Na stránce **New cluster** (Nový cluster) zadejte hodnoty pro vytvoření clusteru.

    ![Vytvoření clusteru Databricks Spark v Azure](./media/data-lake-storage-use-databricks-spark/create-databricks-spark-cluster.png "Vytvoření clusteru Databricks Spark v Azure")

4. Zadejte hodnoty následujících polí a potvrďte výchozí hodnoty dalších polí:

    * Zadejte název clusteru.

    * Pro účely tohoto článku vytvořte cluster pomocí **5.1** modulu runtime.

    * Ujistěte se, že jste vybrali **po provedení \_ \_ počet minut nečinnosti** zaškrtávací políčko. Pokud se nepoužívá clusteru, cluster ukončit poskytnou doba trvání (v minutách).

    * Vyberte **Vytvořit cluster**. Po spuštění clusteru můžete ke clusteru připojit poznámkové bloky a spouštět úlohy Spark.

## <a name="create-a-file-system-and-mount-it"></a>Vytvořit systém souborů a připojte ji zadáním

V této části vytvoříte systém souborů a složky v účtu úložiště.

1. V [webu Azure portal](https://portal.azure.com), přejděte do služby Azure Databricks, který jste vytvořili a vyberte **spustit pracovní prostor**.

2. Na levé straně vyberte **pracovní prostor**. V rozevíracím seznamu **Pracovní prostor** vyberte **Vytvořit** > **Poznámkový blok**.

    ![Vytvoření poznámkového bloku v Databricks](./media/data-lake-storage-use-databricks-spark/databricks-create-notebook.png "vytvoření poznámkového bloku v Databricks")

3. V dialogovém okně **Vytvořit poznámkový blok** zadejte název poznámkového bloku. Vyberte **Python** jako jazyk a pak vyberte Sparku clusteru, který jste vytvořili dříve.

4. Vyberte **Vytvořit**.

5. Zkopírujte a vložte následující blok kódu do první buňky, ale není ještě tento kód spustit.

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
18. V tomto bloku kódu, nahraďte `storage-account-name`, `application-id`, `authentication-id`, a `tenant-id` hodnoty zástupných symbolů v tomto bloku kódu nahraďte hodnotami, které jste shromáždili, když jste dokončili kroky v konfiguraci účtu úložiště sady aside a [Vytvoření instančního objektu](#service-principal) částech tohoto článku. Nahradit `file-system-name` zástupný symbol libovolný název, který chcete udělit systému souborů.

19. Stisknutím klávesy **SHIFT + ENTER** klíče pro spuštění kódu v tomto bloku. 

    Tento poznámkový blok nechte otevřené, protože příkazy se do ní přidat později.

## <a name="ingest-data"></a>Ingestace dat

### <a name="copy-source-data-into-the-storage-account"></a>Zkopírování zdrojových dat do účtu úložiště

Pomocí AzCopy můžete kopírovat data z vašeho *CSV* souborů do účtu Data Lake Storage Gen2.

1. Otevřete okno příkazového řádku a zadejte následující příkaz k přihlášení do účtu úložiště.

   ```bash
   azcopy login
   ```

   Postupujte podle pokynů jsou zobrazeny v okně příkazového řádku k ověření uživatelského účtu.

2. Pro kopírování dat z *CSV* account, zadejte následující příkaz.

   ```bash
   azcopy cp "<csv-folder-path>" https://<storage-account-name>.dfs.core.windows.net/<file-system-name>/folder1/On_Time
   ```
   * Nahradit `<csv-folder-path>` hodnotu zástupného symbolu se cesta k adresáři *CSV* soubor (s výjimkou názvu souboru).

   * Nahradit `storage-account-name` zástupnou hodnotu s názvem účtu úložiště.

   * Nahradit `file-system-name` zástupný symbol libovolný název, který chcete udělit systému souborů.

### <a name="use-databricks-notebook-to-convert-csv-to-parquet"></a>Použití poznámkového bloku Databricks k převodu CSV na formát Parquet

V poznámkovém bloku, který jste dříve vytvořili přidejte novou buňku a vložte následující kód do buňky. Nahraďte `storage-account-name` hodnotu zástupného symbolu v tento fragment kódu s názvem složka, kterou jste uložili soubor csv.

```python
# Use the previously established DBFS mount point to read the data.
# create a data frame to read data.

flightDF = spark.read.format('csv').options(header='true', inferschema='true').load("/mnt/flightdata/On_Time/<your-folder-name>/*.csv")

# read the airline csv file and write the output to parquet format for easy query.
 flightDF.write.mode("append").parquet("/mnt/flightdata/parquet/flights")
 print("Done")
 ```

## <a name="explore-data"></a>Zkoumání dat

Do nové buňky vložte následující kód k získání seznamu nebo souborů CSV pomocí nástroje AzCopy nahraje. Nahraďte `<csv-folder-path>` hodnotu zástupného symbolu se stejnou hodnotou pro tento zástupný text, který jste použili dříve.

```python
import os.path
import IPython
from pyspark.sql import SQLContext
display(dbutils.fs.ls("/mnt/flightdata/On_Time/<your-folder-name>"))
```

Pokud chcete vytvořit nový soubor a zobrazit seznam souborů ve složce *parquet/flights*, spusťte tento skript:

```python
dbutils.fs.put("/mnt/flightdata/1.txt", "Hello, World!", True)
dbutils.fs.ls("/mnt/flightdata/parquet/flights")
```

Na těchto vzorových kódech jste prozkoumali hierarchickou povahu systému souborů HDFS s využitím dat uložených v účtu úložiště s povolenou službou Data Lake Storage Gen2.

## <a name="query-the-data"></a>Vytváření dotazů na data

Teď můžete začít vytvářet dotazy na data, která jste nahráli do svého účtu úložiště. Do buňky **Cmd 1** zadejte všechny následující bloky kódu a stiskněte **Cmd+Enter**, abyste spustili skript Pythonu.

Pokud chcete vytvořit datové rámce pro zdroje dat, spusťte následující skript:

* Nahradit `<csv-folder-path>` hodnotu zástupného symbolu se cesta k adresáři *CSV* soubor (s výjimkou názvu souboru).

* Nahradit `<your-csv-file-name` zástupnou hodnotu s názvem vaší *sdíleného svazku clusteru* souboru.

```python
#Copy this into a Cmd cell in your notebook.
acDF = spark.read.format('csv').options(header='true', inferschema='true').load("/mnt/flightdata/On_Time/<your-folder-name>/<your-csv-file-name>.csv")
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

Zadejte tento skript spusťte některé základní analýzy dotazy na data.

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
out1 = spark.sql("SELECT distinct(Reporting_Airline) FROM FlightTable WHERE OriginStateName='Texas'")
print('Airlines that fly to/from Texas: ', out1.show(100, False))
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste už nepotřebujete, odstraňte skupinu prostředků a všechny související prostředky. Uděláte to tak, vyberte skupinu prostředků pro účet úložiště a vyberte **odstranit**.

## <a name="next-steps"></a>Další postup

[!div class="nextstepaction"] 
> [Extrakce, transformace a načítání dat pomocí Apache Hivu ve službě Azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md)
