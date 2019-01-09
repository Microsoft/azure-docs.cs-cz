---
title: 'Kurz: Přečtěte si k provedení extrakce, načítání a operací přenosu s využitím Azure Databricks'
description: V tomto kurzu se dozvíte, jak extrahovat data z Azure Data Lake Storage Gen2 ve verzi Preview do Azure Databricks, transformaci dat a pak načíst data do Azure SQL Data Warehouse.
services: storage
author: jamesbak
ms.service: storage
ms.author: jamesbak
ms.topic: tutorial
ms.date: 12/06/2018
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 6b2812e31174c4e5d61ae9941563e39357de9522
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54107085"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-azure-databricks"></a>Kurz: Extrakce, transformace a načítání dat pomocí Azure Databricks

V tomto kurzu jste pomocí Azure Databricks provádět ETL (extrakce, transformace a načítání dat) operace. Přesun dat z účtu služby Azure Storage s Azure Data Lake Storage Gen2 povolené do služby Azure SQL Data Warehouse.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření pracovního prostoru Azure Databricks.
> * Vytvoření clusteru Spark v Azure Databricks.
> * Vytvoření účtu Azure Data Lake Storage Gen2 podporuje.
> * Nahrání dat do Azure Data Lake Storage Gen2.
> * Vytvoření poznámkového bloku v Azure Databricks.
> * Extrahujte data z Data Lake Storage Gen2.
> * Transformujte data v Azure Databricks.
> * Načtení dat do Azure SQL Data Warehouse.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* Vytvoření služby Azure SQL data warehouse, vytvořte pravidlo brány firewall na úrovni serveru a připojení k serveru jako správce serveru. Postupujte podle pokynů [rychlý start: Vytvoření služby Azure SQL data warehouse](../../sql-data-warehouse/create-data-warehouse-portal.md) článku.
* Vytvořte hlavní klíč databáze pro službu Azure SQL data warehouse. Postupujte podle pokynů [vytvořte hlavní klíč databáze](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-a-database-master-key) článku.
* [Vytvoření účtu Azure Data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md).
* Stáhněte si soubor s ukázkovými daty (**small_radio_json.json**) z úložiště [příkladů a sledování problémů U-SQL](https://github.com/Azure/usql/blob/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json) a poznamenejte si cestu, kam jste tento soubor uložili.
* Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="create-the-workspace"></a>Vytvořit pracovní prostor

V této části vytvoříte pracovní prostor služby Azure Databricks s využitím webu Azure portal.

1. Na webu Azure Portal vyberte **Vytvořit prostředek** > **Analýza** > **Azure Databricks**.

    ![Databricks na webu Azure Portal](./media/data-lake-storage-handle-data-using-databricks/azure-databricks-on-portal.png "Databricks na webu Azure Portal")

1. V části **služba Azure Databricks**, zadejte následující hodnoty pro vytvoření pracovního prostoru Databricks:

    |Vlastnost  |Popis  |
    |---------|---------|
    |**Název pracovního prostoru**     | Zadejte název pracovního prostoru Databricks.        |
    |**Předplatné**     | Z rozevíracího seznamu vyberte své předplatné Azure.        |
    |**Skupina prostředků**     | Určete, jestli chcete vytvořit novou skupinu prostředků, nebo použít existující. Skupina prostředků je kontejner, který obsahuje související prostředky pro řešení Azure. Další informace naleznete v tématu [Přehled skupin prostředků v Azure](../../azure-resource-manager/resource-group-overview.md). |
    |**Umístění**     | Vyberte **Západní USA 2**.        |
    |**Cenová úroveň**     |  Vyberte **standardní**.     |

    ![Vytvoření pracovního prostoru služby Azure Databricks](./media/data-lake-storage-handle-data-using-databricks/create-databricks-workspace.png "Vytvoření pracovního prostoru služby Azure Databricks")

1. Vyberte **Připnout na řídicí panel** a potom vyberte **Vytvořit**.

1. Vytvoření účtu trvá několik minut. Během vytváření účtu na portálu se zobrazí **odesílá se nasazení pro Azure Databricks** dlaždice na pravé straně. Pokud chcete monitorovat stav operace, zobrazte indikátor průběhu v horní části.

    ![Dlaždice nasazení Databricks](./media/data-lake-storage-handle-data-using-databricks/databricks-deployment-tile.png "Dlaždice nasazení Databricks")

## <a name="create-the-spark-cluster"></a>Vytvoření clusteru Spark

K provádění operací v tomto kurzu, budete potřebovat Spark cluster. Použijte následující postup k vytvoření clusteru Spark.

1. Na webu Azure Portal, přejděte do pracovního prostoru Databricks, který jste vytvořili a vyberte **spustit pracovní prostor**.

1. Budete přesměrováni na portál Azure Databricks. Na portálu vyberte **Cluster**.

    ![Databricks v Azure](./media/data-lake-storage-handle-data-using-databricks/databricks-on-azure.png "Databricks v Azure")

1. Na stránce **New cluster** (Nový cluster) zadejte hodnoty pro vytvoření clusteru.

    ![Vytvoření clusteru Databricks Spark v Azure](./media/data-lake-storage-handle-data-using-databricks/create-databricks-spark-cluster.png "Vytvoření clusteru Databricks Spark v Azure")

1. Zadejte hodnoty následujících polí a potvrďte výchozí hodnoty dalších polí:

    * Zadejte název clusteru.
    * Pro účely tohoto článku vytvořte cluster pomocí **5.1** modulu runtime.
    * Ujistěte se, že jste vybrali **po provedení \_ \_ počet minut nečinnosti** zaškrtávací políčko. Pokud se nepoužívá clusteru, cluster ukončit poskytnou doba trvání (v minutách).

1. Vyberte **Vytvořit cluster**.

Po spuštění clusteru můžete ke clusteru připojit poznámkové bloky a spouštět úlohy Spark.

## <a name="create-a-file-system"></a>Vytvořit systém souborů

K ukládání dat ve vašem účtu úložiště Data Lake Storage Gen2, budete muset vytvořit systém souborů.

Nejprve vytvořte ve vašem pracovním prostoru Azure Databricks Poznámkový blok a pak spustíte fragmenty kódu pro vytváření systému souborů ve vašem účtu úložiště.

1. V [webu Azure portal](https://portal.azure.com), přejděte do pracovního prostoru Azure Databricks, který jste vytvořili a vyberte **spustit pracovní prostor**.

1. Na levé straně vyberte **pracovní prostor**. V rozevíracím seznamu **Pracovní prostor** vyberte **Vytvořit** > **Poznámkový blok**.

    ![Vytvoření poznámkového bloku v Databricks](./media/data-lake-storage-handle-data-using-databricks/databricks-create-notebook.png "vytvoření poznámkového bloku v Databricks")

1. V dialogovém okně **Vytvořit poznámkový blok** zadejte název poznámkového bloku. Vyberte jazyk **Scala** a vyberte cluster Spark, který jste vytvořili v předchozí části.

    ![Zadejte podrobnosti pro poznámkového bloku v Databricks](./media/data-lake-storage-handle-data-using-databricks/databricks-notebook-details.png "zadejte podrobnosti pro poznámkového bloku v Databricks")

    Vyberte **Vytvořit**.

1. Zadejte následující kód do první buňky Poznámkový blok a spouštění kódu. Nahraďte zástupné symboly v závorce v ukázce vlastními hodnotami:

    ```scala
    %python%
    configs = {"fs.azure.account.auth.type": "OAuth",
        "fs.azure.account.oauth.provider.type": "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
        "fs.azure.account.oauth2.client.id": "<service-client-id>",
        "fs.azure.account.oauth2.client.secret": "<service-credentials>",
        "fs.azure.account.oauth2.client.endpoint": "https://login.microsoftonline.com/<tenant-id>/oauth2/token"}
     
    dbutils.fs.mount(
        source = "abfss://<file-system-name>@<account-name>.dfs.core.windows.net/[<directory-name>]",
        mount_point = "/mnt/<mount-name>",
        extra_configs = configs)
    ```

1. Vyberte klávesy Shift + Enter pro spuštění kódu.

Tím vytvoříte systém souborů pro účet úložiště.

## <a name="upload-the-sample-data"></a>Nahrání ukázkových dat

Dalším krokem je ukázkový datový soubor nahrát do účtu úložiště pro transformaci dále v Azure Databricks.

Nahrání ukázkových dat, který jste stáhli do účtu úložiště. Metoda, která slouží k nahrávání dat do účtu úložiště se liší v závislosti na tom, jestli máte hierarchického oboru názvů povolené.

Provést odeslání, můžete použít Azure Data Factory, distp nebo AzCopy (verze 10). AzCopy verze 10 je momentálně dostupný jenom prostřednictvím ve verzi preview. Pokud chcete použít AzCopy, vložte do příkazového okna následující kód:

```bash
set ACCOUNT_NAME=<ACCOUNT_NAME>
set ACCOUNT_KEY=<ACCOUNT_KEY>
azcopy cp "<DOWNLOAD_PATH>\small_radio_json.json" https://<ACCOUNT_NAME>.dfs.core.windows.net/data --recursive 
```

## <a name="extract-the-data"></a>Extrahovat data

Pro práci s ukázkovými daty ve službě Databricks, budete muset extrahovat data z vašeho účtu úložiště.

Vraťte se do vaší poznámkového bloku Databricks a zadejte následující kód do nové buňky v poznámkovém bloku.

Přidejte následující fragment kódu do buňky prázdný kód. Nahraďte zástupné symboly v závorkách hodnotami, které jste předtím uložili z účtu úložiště.

```scala
dbutils.widgets.text("storage_account_name", "STORAGE_ACCOUNT_NAME", "<YOUR_STORAGE_ACCOUNT_NAME>")
dbutils.widgets.text("storage_account_access_key", "YOUR_ACCESS_KEY", "<YOUR_STORAGE_ACCOUNT_SHARED_KEY>")
```

Vyberte klávesy Shift + Enter pro spuštění kódu.

Teď můžete ukázkový soubor JSON načíst jako datový rámec do služby Azure Databricks. Vložte následující kód do nové buňky. Nahraďte zástupné symboly v závorce s vašimi hodnotami.

```scala
val df = spark.read.json("abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/data/small_radio_json.json")
```

Vyberte klávesy Shift + Enter pro spuštění kódu.

Spuštěním následujícího kódu zobrazíte obsah datového rámce:

```scala
df.show()
```

Zobrazí se výstup, který bude podobný následujícímu fragmentu kódu:

```bash
+---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
|               artist|     auth|firstName|gender|itemInSession|  lastName|   length|  level|            location|method|    page| registration|sessionId|                song|status|           ts|userId|
+---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
| El Arrebato         |Logged In| Annalyse|     F|            2|Montgomery|234.57914| free  |  Killeen-Temple, TX|   PUT|NextSong|1384448062332|     1879|Quiero Quererte Q...|   200|1409318650332|   309|
| Creedence Clearwa...|Logged In|   Dylann|     M|            9|    Thomas|340.87138| paid  |       Anchorage, AK|   PUT|NextSong|1400723739332|       10|        Born To Move|   200|1409318653332|    11|
| Gorillaz            |Logged In|     Liam|     M|           11|     Watts|246.17751| paid  |New York-Newark-J...|   PUT|NextSong|1406279422332|     2047|                DARE|   200|1409318685332|   201|
...
...
```

Tím jste extrahovali data z Azure Data Lake Storage Gen2 do Azure Databricks.

## <a name="transform-the-data"></a>Transformace dat

Nezpracovaná ukázková data **small_radio_json.json** souboru zaznamená cílovou skupinu stanice a má různé sloupce. V této části transformujete data z datové sady načetly jenom určité sloupce.

Nejdřív, načtěte jenom sloupce **firstName**, **lastName**, **pohlaví**, **umístění**, a **úroveň**z datového rámce, který jste vytvořili.

```scala
val specificColumnsDf = df.select("firstname", "lastname", "gender", "location", "level")
```

Zobrazí se výstup, jak je znázorněno v následujícím fragmentu kódu:

```bash
+---------+----------+------+--------------------+-----+
|firstname|  lastname|gender|            location|level|
+---------+----------+------+--------------------+-----+
| Annalyse|Montgomery|     F|  Killeen-Temple, TX| free|
|   Dylann|    Thomas|     M|       Anchorage, AK| paid|
|     Liam|     Watts|     M|New York-Newark-J...| paid|
|     Tess|  Townsend|     F|Nashville-Davidso...| free|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
|     Alan|     Morse|     M|Chicago-Napervill...| paid|
|Gabriella|   Shelton|     F|San Jose-Sunnyval...| free|
|   Elijah|  Williams|     M|Detroit-Warren-De...| paid|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
|     Tess|  Townsend|     F|Nashville-Davidso...| free|
|     Alan|     Morse|     M|Chicago-Napervill...| paid|
|     Liam|     Watts|     M|New York-Newark-J...| paid|
|     Liam|     Watts|     M|New York-Newark-J...| paid|
|   Dylann|    Thomas|     M|       Anchorage, AK| paid|
|     Alan|     Morse|     M|Chicago-Napervill...| paid|
|   Elijah|  Williams|     M|Detroit-Warren-De...| paid|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
|     Alan|     Morse|     M|Chicago-Napervill...| paid|
|   Dylann|    Thomas|     M|       Anchorage, AK| paid|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
+---------+----------+------+--------------------+-----+
```

Teď můžete v datech sloupec **level** přejmenovat na **subscription_type**.

```scala
val renamedColumnsDF = specificColumnsDf.withColumnRenamed("level", "subscription_type")
renamedColumnsDF.show()
```

Zobrazí se výstup, jak je znázorněno v následujícím fragmentu kódu.

```bash
+---------+----------+------+--------------------+-----------------+
|firstname|  lastname|gender|            location|subscription_type|
+---------+----------+------+--------------------+-----------------+
| Annalyse|Montgomery|     F|  Killeen-Temple, TX|             free|
|   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
|     Liam|     Watts|     M|New York-Newark-J...|             paid|
|     Tess|  Townsend|     F|Nashville-Davidso...|             free|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
|     Alan|     Morse|     M|Chicago-Napervill...|             paid|
|Gabriella|   Shelton|     F|San Jose-Sunnyval...|             free|
|   Elijah|  Williams|     M|Detroit-Warren-De...|             paid|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
|     Tess|  Townsend|     F|Nashville-Davidso...|             free|
|     Alan|     Morse|     M|Chicago-Napervill...|             paid|
|     Liam|     Watts|     M|New York-Newark-J...|             paid|
|     Liam|     Watts|     M|New York-Newark-J...|             paid|
|   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
|     Alan|     Morse|     M|Chicago-Napervill...|             paid|
|   Elijah|  Williams|     M|Detroit-Warren-De...|             paid|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
|     Alan|     Morse|     M|Chicago-Napervill...|             paid|
|   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
|  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
+---------+----------+------+--------------------+-----------------+
```

## <a name="load-the-data"></a>Načtení dat

V této části načtete transformovaná data do služby Azure SQL Data Warehouse. Konektor Azure SQL Data Warehouse pro Azure Databricks můžete datový rámec nahrát přímo jako tabulku do SQL data warehouse.

Konektor SQL Data Warehouse využívá úložiště objektů Blob v Azure jako dočasné úložiště pro nahrávání dat mezi službami Azure Databricks a Azure SQL Data Warehouse. Proto musíte napřed zadat konfiguraci pro připojení k účtu tohoto úložiště. Je potřeba už měli vytvořený účet jako součást požadavků pro účely tohoto článku.

Zadejte konfiguraci pro přístup k účtu Azure Storage z Azure Databricks.

```scala
val storageURI = "<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net"
val fileSystemName = "<FILE_SYSTEM_NAME>"
val accessKey =  "<ACCESS_KEY>"
```

Zadejte dočasnou složku pro použití při přesouvání dat mezi službami Azure Databricks a Azure SQL Data Warehouse.

```scala
val tempDir = "abfs://" + fileSystemName + "@" + storageURI +"/tempDirs"
```

Spusťte následující fragment kódu, který v konfiguraci uloží přístupové klíče služby Azure Blob Storage. Tím zajistíte, že není nutné udržovat přístupový klíč do poznámkového bloku ve formátu prostého textu.

```scala
val acntInfo = "fs.azure.account.key."+ storageURI
sc.hadoopConfiguration.set(acntInfo, accessKey)
```

Zadejte hodnoty pro připojení k instanci Azure SQL Data Warehouse. Musíte mít vytvořený SQL data warehouse jako předpoklad.

```scala
//SQL Data Warehouse related settings
val dwDatabase = "<DATABASE NAME>"
val dwServer = "<DATABASE SERVER NAME>" 
val dwUser = "<USER NAME>"
val dwPass = "<PASSWORD>"
val dwJdbcPort =  "1433"
val dwJdbcExtraOptions = "encrypt=true;trustServerCertificate=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"
val sqlDwUrl = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass + ";$dwJdbcExtraOptions"
val sqlDwUrlSmall = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass
```

Spusťte následující fragment kódu pro načtení transformovaný datový rámec **renamedColumnsDF**, jako tabulky v SQL data warehouse. Tento fragment kódu vytvoří v SQL databázi tabulku s názvem **SampleTable**.

```scala
spark.conf.set(
    "spark.sql.parquet.writeLegacyFormat",
    "true")
    
renamedColumnsDF.write
    .format("com.databricks.spark.sqldw")
    .option("url", sqlDwUrlSmall) 
    .option("dbtable", "SampleTable")
    .option( "forward_spark_azure_storage_credentials","True")
    .option("tempdir", tempDir)
    .mode("overwrite")
    .save()
```

Připojení k SQL database a ověřte, jestli se databáze s názvem **SampleTable**.

![Ověření ukázkové tabulky](./media/data-lake-storage-handle-data-using-databricks/verify-sample-table.png "ověření ukázkové tabulky")

Spusťte výběrový dotaz, kterým ověříte obsah tabulky. Tabulka by měla obsahovat stejná data jako **renamedColumnsDF** datového rámce.

![Ověření obsahu ukázkové tabulky](./media/data-lake-storage-handle-data-using-databricks/verify-sample-table-content.png "ověření obsahu ukázkové tabulky")

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení tohoto kurzu můžete cluster ukončit. V pracovním prostoru Azure Databricks vyberte **clustery** na levé straně. Pro cluster ukončit, v části **akce**, přejděte na tři tečky (...) a vyberte **Terminate** ikonu.

![Zastavení clusteru Databricks](./media/data-lake-storage-handle-data-using-databricks/terminate-databricks-cluster.png "Zastavení clusteru Databricks")

Pokud cluster není ukončit ručně, se automaticky zastaví, pokud jste vybrali **po provedení \_ \_ počet minut nečinnosti** zaškrtávací políčko při vytváření clusteru. V takovém případě se cluster automaticky zastaví, pokud byl neaktivní po zadanou dobu.

## <a name="next-steps"></a>Další postup

Přejděte k dalšímu kurzu se naučíte Streamovat data v reálném čase do Azure Databricks pomocí služby Azure Event Hubs.

> [!div class="nextstepaction"]
>[Stream dat do Azure Databricks pomocí služby Event Hubs](../../azure-databricks/databricks-stream-from-eventhubs.md)
