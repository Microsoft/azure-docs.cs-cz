---
title: 'Kurz: Provádění operací ETL pomocí Azure Databricks'
description: Zjistěte, jak extrahovat data z Data Lake Storage Gen2 do Azure Databricks, transformaci dat a pak načíst data do Azure SQL Data Warehouse.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.custom: mvc
ms.topic: tutorial
ms.date: 05/07/2019
ms.openlocfilehash: e2110378d16ff5826b8ded4620276b784ef1d68e
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65203355"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-azure-databricks"></a>Kurz: Extrakce, transformace a načítání dat pomocí Azure Databricks

V tomto kurzu provedete ETL (extrakce, transformace a načítání dat) operace s využitím Azure Databricks. Extrahovat data z Azure Data Lake Storage Gen2 do Azure Databricks, spouštět transformace dat v Azure Databricks a načítat Transformovaná data do Azure SQL Data Warehouse.

Postup, který je popsaný v tomto kurzu, používá k přenosu dat do Azure Databricks konektor SQL Data Warehouse pro Azure Databricks. Tento konektor zase používá Azure Blob Storage jako dočasné úložiště dat přenášených mezi clusterem Azure Databricks a službou Azure SQL Data Warehouse.

Následující obrázek ukazuje běh aplikace:

![Azure Databricks se službami Data Lake Store a SQL Data Warehouse](./media/databricks-extract-load-sql-data-warehouse/databricks-extract-transform-load-sql-datawarehouse.png "Azure Databricks se službami Data Lake Store a SQL Data Warehouse")

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Vytvoření služby Azure Databricks.
> * Vytvoření clusteru Spark v Azure Databricks.
> * Vytvořte systém souborů v účtu Data Lake Storage Gen2.
> * Nahrání ukázkových dat do účtu Azure Data Lake Storage Gen2.
> * Vytvoření instančního objektu.
> * Extrahujte data z účtu Azure Data Lake Storage Gen2.
> * Transformujte data v Azure Databricks.
> * Načtení dat do Azure SQL Data Warehouse.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

> [!Note]
> V tomto kurzu není možné provést pomocí **bezplatnou zkušební verzi předplatného Azure**.
> Pokud chcete k vytvoření clusteru Azure Databricks použít bezplatný účet, přejděte na svůj profil a změňte své předplatné na **Průběžné platby**. Další informace najdete na stránce [bezplatného účtu Azure](https://azure.microsoft.com/free/).
     
## <a name="prerequisites"></a>Požadavky

Než zahájíte tento kurz, proveďte tyto úlohy:

* Vytvoření služby Azure SQL data warehouse, vytvořte pravidlo brány firewall na úrovni serveru a připojení k serveru jako správce serveru. Zobrazit [rychlý start: Vytvořit a dotazovat službu Azure SQL data warehouse na webu Azure Portal](../sql-data-warehouse/create-data-warehouse-portal.md).

* Vytvořte hlavní klíč databáze pro službu Azure SQL data warehouse. Zobrazit [vytvořte hlavní klíč databáze](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-a-database-master-key).

* Vytvořili jste účet Azure Blob Storage a v něm kontejner. A načetli jste přístupový klíč pro přístup k účtu úložiště. Zobrazit [rychlý start: Nahrávání, stahování a výpis objektů BLOB pomocí webu Azure portal](../storage/blobs/storage-quickstart-blobs-portal.md).

* Vytvoření účtu úložiště Azure Data Lake Storage Gen2. Zobrazit [rychlý start: Vytvoření účtu úložiště Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-quickstart-create-account.md).

*  Vytvoření instančního objektu. Zobrazit [jak: Použití portálu k vytvoření aplikace a instančního objektu, který má přístup k prostředkům Azure AD](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

   Existuje několik určité akce, které budete muset udělat při provádění kroků v tomto článku.

   * Při provádění kroků v [přiřazení aplikace k roli](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) části tohoto článku, ujistěte se, že k přiřazení **Přispěvatel dat objektu Blob úložiště** roli instančnímu objektu služby.

     > [!IMPORTANT]
     > Ujistěte se, že přiřazení role v rámci účtu úložiště Data Lake Storage Gen2. Roli můžete přiřadit do nadřazené skupiny prostředků nebo předplatného, ale se zobrazí chyby související s oprávněními, dokud tato přiřazení rolí se rozšíří do účtu úložiště.

   * Při provádění kroků v [získání hodnot pro přihlášení](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) část článku, vložte ID tenanta, ID aplikace a hodnoty klíče ověřování do textového souboru. Brzy ty budete potřebovat.

* Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="gather-the-information-that-you-need"></a>Shromážděte informace, které potřebujete

Ujistěte se, že je splnit požadavky tohoto kurzu.

   Než začnete, měli byste tyto položky informace:

   :heavy_check_mark:  Název databáze, název databázového serveru, uživatelské jméno a heslo služby Azure SQL Data warehouse.

   :heavy_check_mark:  Přístupový klíč účtu úložiště objektů blob.

   :heavy_check_mark:  Název účtu úložiště Data Lake Storage Gen2.

   :heavy_check_mark:  ID tenanta předplatného.

   :heavy_check_mark:  ID aplikace, aplikace, které jste zaregistrovali pomocí služby Azure Active Directory (Azure AD).

   :heavy_check_mark:  Ověřovací klíč pro aplikace, které jste zaregistrovali pomocí služby Azure AD.

## <a name="create-an-azure-databricks-service"></a>Vytvoření služby Azure Databricks

V této části Vytvoření služby Azure Databricks s využitím webu Azure portal.

1. Na webu Azure Portal vyberte **Vytvořit prostředek** > **Analýza** > **Azure Databricks**.

    ![Databricks na webu Azure Portal](./media/databricks-extract-load-sql-data-warehouse/azure-databricks-on-portal.png "Databricks na webu Azure Portal")

2. V části **služba Azure Databricks**, zadejte následující hodnoty pro vytvoření služby Databricks:

    |Vlastnost  |Popis  |
    |---------|---------|
    |**Název pracovního prostoru**     | Zadejte název pracovního prostoru Databricks.        |
    |**Předplatné**     | Z rozevíracího seznamu vyberte své předplatné Azure.        |
    |**Skupina prostředků**     | Určete, jestli chcete vytvořit novou skupinu prostředků, nebo použít existující. Skupina prostředků je kontejner, který obsahuje související prostředky pro řešení Azure. Další informace naleznete v tématu [Přehled skupin prostředků v Azure](../azure-resource-manager/resource-group-overview.md). |
    |**Umístění**     | Vyberte **Západní USA 2**.  Další dostupné oblasti najdete v tématu [Dostupné služby Azure podle oblastí](https://azure.microsoft.com/regions/services/).      |
    |**Cenová úroveň**     |  Vyberte **standardní**.     |

3. Vyberte **Připnout na řídicí panel** a potom vyberte **Vytvořit**.

4. Vytvoření účtu trvá několik minut. Během vytváření účtu na portálu se zobrazí **odesílá se nasazení pro Azure Databricks** dlaždice na pravé straně. Pokud chcete monitorovat stav operace, zobrazte indikátor průběhu v horní části.

    ![Dlaždice nasazení Databricks](./media/databricks-extract-load-sql-data-warehouse/databricks-deployment-tile.png "Dlaždice nasazení Databricks")

## <a name="create-a-spark-cluster-in-azure-databricks"></a>Vytvoření clusteru Spark v Azure Databricks

1. Na webu Azure Portal, přejděte na službu Databricks, který jste vytvořili a vyberte **spustit pracovní prostor**.

2. Budete přesměrováni na portál Azure Databricks. Na portálu vyberte **Cluster**.

    ![Databricks v Azure](./media/databricks-extract-load-sql-data-warehouse/databricks-on-azure.png "Databricks v Azure")

3. Na stránce **New cluster** (Nový cluster) zadejte hodnoty pro vytvoření clusteru.

    ![Vytvoření clusteru Databricks Spark v Azure](./media/databricks-extract-load-sql-data-warehouse/create-databricks-spark-cluster.png "Vytvoření clusteru Databricks Spark v Azure")

4. Zadejte hodnoty následujících polí a potvrďte výchozí hodnoty dalších polí:

    * Zadejte název clusteru.

    * Pro účely tohoto článku vytvořte cluster pomocí **5.1** modulu runtime.

    * Ujistěte se, že jste vybrali **po provedení \_\_ počet minut nečinnosti** zaškrtávací políčko. Pokud se nepoužívá clusteru, cluster ukončit poskytnou doba trvání (v minutách).

    * Vyberte **Vytvořit cluster**. Po spuštění clusteru můžete ke clusteru připojit poznámkové bloky a spouštět úlohy Spark.

## <a name="create-a-file-system-in-the-azure-data-lake-storage-gen2-account"></a>Vytvořit systém souborů v účtu Azure Data Lake Storage Gen2

V této části vytvoříte v pracovním prostoru Azure Databricks Poznámkový blok a pak spustíte fragmenty kódu můžete nakonfigurovat účet úložiště

1. V [webu Azure portal](https://portal.azure.com), přejděte do služby Azure Databricks, který jste vytvořili a vyberte **spustit pracovní prostor**.

2. Na levé straně vyberte **pracovní prostor**. V rozevíracím seznamu **Pracovní prostor** vyberte **Vytvořit** > **Poznámkový blok**.

    ![Vytvoření poznámkového bloku v Databricks](./media/databricks-extract-load-sql-data-warehouse/databricks-create-notebook.png "vytvoření poznámkového bloku v Databricks")

3. V dialogovém okně **Vytvořit poznámkový blok** zadejte název poznámkového bloku. Vyberte jazyk **Scala** a vyberte cluster Spark, který jste vytvořili v předchozí části.

    ![Zadejte podrobnosti pro poznámkového bloku v Databricks](./media/databricks-extract-load-sql-data-warehouse/databricks-notebook-details.png "zadejte podrobnosti pro poznámkového bloku v Databricks")

4. Vyberte **Vytvořit**.

5. Zkopírujte a vložte následující blok kódu do první buňky.

   ```scala
   spark.conf.set("fs.azure.account.auth.type.<storage-account-name>.dfs.core.windows.net", "OAuth")
   spark.conf.set("fs.azure.account.oauth.provider.type.<storage-account-name>.dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
   spark.conf.set("fs.azure.account.oauth2.client.id.<storage-account-name>.dfs.core.windows.net", "<application-id>")
   spark.conf.set("fs.azure.account.oauth2.client.secret.<storage-account-name>.dfs.core.windows.net", "<authentication-key>")
   spark.conf.set("fs.azure.account.oauth2.client.endpoint.<storage-account-name>.dfs.core.windows.net", "https://login.microsoftonline.com/<tenant-id>/oauth2/token")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "true")
   dbutils.fs.ls("abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "false")
   ```

6. V tomto bloku kódu, nahraďte `application-id`, `authentication-id`, `tenant-id`, a `storage-account-name` zástupné hodnoty hodnotami, které jste shromáždili během dokončování požadavky v tomto kurzu v tomto bloku kódu. Nahraďte `file-system-name` hodnotu zástupného symbolu pomocí cokoli, co název chcete umožnit systému souborů.

   * `application-id`, A `authentication-id` pocházejí z aplikace, které jste zaregistrovali pomocí služby active directory při vytváření instančního objektu.

   * `tenant-id` Je ze svého předplatného.

   * `storage-account-name` Je název vašeho účtu úložiště Azure Data Lake Storage Gen2.

7. Stisknutím klávesy **SHIFT + ENTER** klíče pro spuštění kódu v tomto bloku.

## <a name="ingest-sample-data-into-the-azure-data-lake-storage-gen2-account"></a>Ingestování ukázková data do účtu Azure Data Lake Storage Gen2

Než se pustíte do této části, je potřeba nejprve splnit následující požadavky:

Do buňky poznámkového bloku zadejte následující kód:

    %sh wget -P /tmp https://raw.githubusercontent.com/Azure/usql/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json

V buňce, stiskněte klávesu **SHIFT + ENTER** spuštění kódu.

Teď do nové buňky pod tohohle, zadejte následující kód a nahraďte hodnoty, které se zobrazují v závorkách se stejnými hodnotami, které jste použili dříve:

    dbutils.fs.cp("file:///tmp/small_radio_json.json", "abfss://<file-system>@<account-name>.dfs.core.windows.net/")

V buňce, stiskněte klávesu **SHIFT + ENTER** spuštění kódu.

## <a name="extract-data-from-the-azure-data-lake-storage-gen2-account"></a>Extrahovat data z účtu Azure Data Lake Storage Gen2

1. Nyní můžete načíst ukázkový soubor json jako datový rámec v Azure Databricks. Vložte následující kód do nové buňky. Nahraďte zástupné symboly v závorce s vašimi hodnotami.

   ```scala
   val df = spark.read.json("abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/small_radio_json.json")
   ```

   * Nahradit `file-system-name` zástupnou hodnotu s názvem, který jste zadali systému souborů v Průzkumníku služby Storage.

   * Nahradit `storage-account-name` zástupný symbol s názvem účtu úložiště.

2. Stisknutím klávesy **SHIFT + ENTER** klíče pro spuštění kódu v tomto bloku.

3. Spuštěním následujícího kódu zobrazíte obsah datového rámce:

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

## <a name="transform-data-in-azure-databricks"></a>Transformace dat v Azure Databricks

Nezpracovaná ukázková data **small_radio_json.json** souboru zaznamená cílovou skupinu stanice a má různé sloupce. V této části transformujete data z datové sady načetly jenom určité sloupce.

1. Nejdřív, načtěte jenom sloupce **firstName**, **lastName**, **pohlaví**, **umístění**, a **úroveň**z datového rámce, který jste vytvořili.

   ```scala
   val specificColumnsDf = df.select("firstname", "lastname", "gender", "location", "level")
   specificColumnsDf.show()
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

2. Teď můžete v datech sloupec **level** přejmenovat na **subscription_type**.

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

## <a name="load-data-into-azure-sql-data-warehouse"></a>Načtení dat do Azure SQL Data Warehouse

V této části načtete transformovaná data do služby Azure SQL Data Warehouse. Konektor Azure SQL Data Warehouse pro Azure Databricks můžete datový rámec nahrát přímo jako tabulku do SQL data warehouse.

Jak už bylo zmíněno dříve, konektor SQL Data Warehouse používá Azure Blob storage jako dočasné úložiště k odesílání dat mezi službami Azure Databricks a Azure SQL Data Warehouse. Proto musíte napřed zadat konfiguraci pro připojení k účtu tohoto úložiště. Musíte již jste již vytvořili účet jako součást požadavků pro účely tohoto článku.

1. Zadejte konfiguraci pro přístup k účtu Azure Storage z Azure Databricks.

   ```scala
   val blobStorage = "<blob-storage-account-name>.blob.core.windows.net"
   val blobContainer = "<blob-container-name>"
   val blobAccessKey =  "<access-key>"
   ```

2. Zadejte dočasnou složku pro použití při přesouvání dat mezi službami Azure Databricks a Azure SQL Data Warehouse.

   ```scala
   val tempDir = "wasbs://" + blobContainer + "@" + blobStorage +"/tempDirs"
   ```

3. Spusťte následující fragment kódu, který v konfiguraci uloží přístupové klíče služby Azure Blob Storage. Tím zajistíte, že není nutné udržovat přístupový klíč do poznámkového bloku ve formátu prostého textu.

   ```scala
   val acntInfo = "fs.azure.account.key."+ blobStorage
   sc.hadoopConfiguration.set(acntInfo, blobAccessKey)
   ```

4. Zadejte hodnoty pro připojení k instanci Azure SQL Data Warehouse. Musíte mít vytvořený SQL data warehouse jako předpoklad.

   ```scala
   //SQL Data Warehouse related settings
   val dwDatabase = "<database-name>"
   val dwServer = "<database-server-name>"
   val dwUser = "<user-name>"
   val dwPass = "<password>"
   val dwJdbcPort =  "1433"
   val dwJdbcExtraOptions = "encrypt=true;trustServerCertificate=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"
   val sqlDwUrl = "jdbc:sqlserver://" + dwServer + ":" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass + ";$dwJdbcExtraOptions"
   val sqlDwUrlSmall = "jdbc:sqlserver://" + dwServer + ":" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass
   ```

5. Spusťte následující fragment kódu pro načtení transformovaný datový rámec **renamedColumnsDF**, jako tabulky v SQL data warehouse. Tento fragment kódu vytvoří v SQL databázi tabulku s názvem **SampleTable**.

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

   > [!NOTE]
   > Tento příklad používá `forward_spark_azure_storage_credentials` příznak, který způsobí, že SQL Data Warehouse pro přístup k datům ze služby blob storage pomocí přístupové klávesy. Toto je jediná podporovaná metoda ověřování.
   >
   > Pokud Azure Blob Storage je omezený na vybrané virtuální sítě, SQL Data Warehouse vyžaduje [identita spravované služby se namísto přístupové klíče](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). To způsobí, že chyba "Tento požadavek není autorizovaný k provedení této operace."

6. Připojení k SQL database a ověřte, jestli se databáze s názvem **SampleTable**.

   ![Ověření ukázkové tabulky](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table.png "ověření ukázkové tabulky")

7. Spusťte výběrový dotaz, kterým ověříte obsah tabulky. Tabulka by měla obsahovat stejná data jako **renamedColumnsDF** datového rámce.

    ![Ověření obsahu ukázkové tabulky](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table-content.png "ověření obsahu ukázkové tabulky")

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení tohoto kurzu můžete cluster ukončit. V pracovním prostoru Azure Databricks vyberte **clustery** na levé straně. Pro cluster ukončit, v části **akce**, přejděte na tři tečky (...) a vyberte **Terminate** ikonu.

![Zastavení clusteru Databricks](./media/databricks-extract-load-sql-data-warehouse/terminate-databricks-cluster.png "Zastavení clusteru Databricks")

Pokud cluster není ukončit ručně, se automaticky zastaví, pokud jste vybrali **po provedení \_\_ počet minut nečinnosti** zaškrtávací políčko při vytváření clusteru. V takovém případě se cluster automaticky zastaví, pokud byl neaktivní po zadanou dobu.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření služby Azure Databricks
> * Vytvoření clusteru Spark v Azure Databricks
> * Vytvoření poznámkového bloku v Azure Databricks
> * Extrahovat data z účtu Data Lake Storage Gen2
> * Transformace dat v Azure Databricks
> * Načtení dat do Azure SQL Data Warehouse

Pokračujte dalším kurzem, ve kterém se naučíte streamovat data v reálném čase do Azure Databricks pomocí služby Azure Event Hubs.

> [!div class="nextstepaction"]
>[Streamování dat do Azure Databricks pomocí služby Event Hubs](databricks-stream-from-eventhubs.md)
