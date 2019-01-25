---
title: 'Kurz: Přečtěte si k provedení extrakce, načítání a operací přenosu s využitím Azure Databricks'
description: V tomto kurzu se dozvíte, jak extrahovat data z Azure Data Lake Storage Gen2 ve verzi Preview do Azure Databricks, transformaci dat a pak načíst data do Azure SQL Data Warehouse.
services: storage
author: jamesbak
ms.service: storage
ms.author: jamesbak
ms.topic: tutorial
ms.date: 01/14/2019
ms.component: data-lake-storage-gen2
ms.openlocfilehash: c80cd5893c5d1f7c9941c979f87924d2943ba8d4
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2019
ms.locfileid: "54904433"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-azure-databricks"></a>Kurz: Extrakce, transformace a načítání dat pomocí Azure Databricks

V tomto kurzu provedete ETL (extrakce, transformace a načítání dat) operace s využitím Azure Databricks. Extrahovat data z Azure Data Lake Storage Gen2 do Azure Databricks, spouštět transformace dat v Azure Databricks a pak načítat Transformovaná data do Azure SQL Data Warehouse.

Postup, který je popsaný v tomto kurzu, používá k přenosu dat do Azure Databricks konektor SQL Data Warehouse pro Azure Databricks. Tento konektor zase používá Azure Blob Storage jako dočasné úložiště dat přenášených mezi clusterem Azure Databricks a službou Azure SQL Data Warehouse.

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Vytvoření pracovního prostoru Azure Databricks.
> * Vytvoření clusteru Spark v Azure Databricks.
> * Vytvořit systém souborů a nahrání dat do služby Azure Data Lake Storage Gen2.
> * Vytvoření instančního objektu.
> * Extrahujte data z Data Lake Store.
> * Transformujte data v Azure Databricks.
> * Načtení dat do Azure SQL Data Warehouse.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

> [!div class="checklist"]
> * Vytvoření služby Azure SQL data warehouse, vytvořte pravidlo brány firewall na úrovni serveru a připojení k serveru jako správce serveru. Zobrazit [rychlý start: Vytvoření služby Azure SQL data warehouse](../../sql-data-warehouse/create-data-warehouse-portal.md).
> * Vytvořte hlavní klíč databáze pro službu Azure SQL data warehouse. Zobrazit [vytvořte hlavní klíč databáze](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-a-database-master-key).
> * Vytvoření účtu Azure Data Lake Storage Gen2. Zobrazit [vytvoření účtu Azure Data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md).
> * Vytvořili jste účet Azure Blob Storage a v něm kontejner. Zobrazit [rychlý start: Vytvoření účtu služby Azure Blob storage](storage-quickstart-blobs-portal.md).
> * Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="create-an-azure-databricks-workspace"></a>Vytvoření pracovního prostoru Azure Databricks

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

## <a name="create-a-spark-cluster-in-azure-databricks"></a>Vytvoření clusteru Spark v Azure Databricks

1. Na webu Azure Portal, přejděte do pracovního prostoru Databricks, který jste vytvořili a vyberte **spustit pracovní prostor**.

2. Budete přesměrováni na portál Azure Databricks. Na portálu vyberte **Cluster**.

    ![Databricks v Azure](./media/data-lake-storage-handle-data-using-databricks/databricks-on-azure.png "Databricks v Azure")

3. Na stránce **New cluster** (Nový cluster) zadejte hodnoty pro vytvoření clusteru.

    ![Vytvoření clusteru Databricks Spark v Azure](./media/data-lake-storage-handle-data-using-databricks/create-databricks-spark-cluster.png "Vytvoření clusteru Databricks Spark v Azure")

4. Zadejte hodnoty následujících polí a potvrďte výchozí hodnoty dalších polí:

    * Zadejte název clusteru.

    * Pro účely tohoto článku vytvořte cluster pomocí **5.1** modulu runtime.

    * Ujistěte se, že jste vybrali **po provedení \_ \_ počet minut nečinnosti** zaškrtávací políčko. Pokud se nepoužívá clusteru, cluster ukončit poskytnou doba trvání (v minutách).

    * Vyberte **Vytvořit cluster**. Po spuštění clusteru můžete ke clusteru připojit poznámkové bloky a spouštět úlohy Spark.

## <a name="create-a-file-system-and-upload-sample-data"></a>Vytvořit systém souborů a nahrání ukázkových dat

Nejprve vytvořte systému souborů ve vašem účtu Data Lake Storage Gen2. Potom můžete nahrát soubor ukázkových dat do Data Lake Store. Později tento soubor použijete v Azure Databricks ke spuštění některých transformací.

1. Stáhněte si [small_radio_json.json](https://github.com/Azure/usql/blob/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json) ukázkový datový soubor do vašeho místního systému souborů.

2. Z [webu Azure portal](https://portal.azure.com/), přejděte do účtu Data Lake Storage Gen2, kterou jste vytvořili jako požadavky do tohoto kurzu.

3. Z **přehled** stránce účtu úložiště vyberte **otevřít v Průzkumníkovi**.

   ![Otevřete Průzkumníka služby Storage](./media/data-lake-storage-handle-data-using-databricks/data-lake-storage-open-storage-explorer.png "otevřete Průzkumníka služby Storage")

4. Vyberte **otevřete Průzkumníka služby Azure Storage** otevřete Průzkumníka služby Storage.

   ![Otevřít Průzkumníka služby Storage se zobrazí druhá výzva](./media/data-lake-storage-handle-data-using-databricks/data-lake-storage-open-storage-explorer-2.png "se zobrazí druhá výzva otevřete Průzkumníka služby Storage")

   Otevře se Průzkumník služby Storage. Můžete vytvořit systém souborů a nahrání ukázkových dat pomocí pokynů v tomto tématu: [Rychlé zprovoznění: Pomocí Průzkumníka služby Azure Storage ke správě dat v účtu služby Azure Data Lake Storage Gen2](data-lake-storage-explorer.md).

<a id="service-principal"/>

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

Vytvoření instančního objektu služby podle pokynů v tomto tématu: [Postup: Použití portálu k vytvoření aplikace a instančního objektu, který má přístup k prostředkům Azure AD](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

Existuje několik určité akce, které budete muset udělat při provádění kroků v tomto článku.

:heavy_check_mark: Při provádění kroků v [vytvoření aplikace Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application) části tohoto článku, nezapomeňte nastavit **přihlašovací adresa URL** pole **vytvořit** dialogové okno pro identifikátor URI koncového bodu právě shromažďují.

:heavy_check_mark: Při provádění kroků v [přiřazení aplikace k roli](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) části tohoto článku, nezapomeňte přiřadit aplikaci do **Role Přispěvatel úložiště objektů Blob**.

:heavy_check_mark: Při provádění kroků v [získání hodnot pro přihlášení](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) část článku, vložte ID tenanta, ID aplikace a hodnoty klíče ověřování do textového souboru. Brzy ty budete potřebovat.
Nejprve vytvořte ve vašem pracovním prostoru Azure Databricks Poznámkový blok a pak spustíte fragmenty kódu pro vytváření systému souborů ve vašem účtu úložiště.

## <a name="extract-data-from-the-data-lake-store"></a>Extrahovat data z Data Lake Store

V této části vytvořte v pracovním prostoru Azure Databricks Poznámkový blok a pak spustíte fragmenty kódu extrahovat data z Data Lake Store do Azure Databricks.

1. V [webu Azure portal](https://portal.azure.com), přejděte do pracovního prostoru Azure Databricks, který jste vytvořili a vyberte **spustit pracovní prostor**.

2. Na levé straně vyberte **pracovní prostor**. V rozevíracím seznamu **Pracovní prostor** vyberte **Vytvořit** > **Poznámkový blok**.

    ![Vytvoření poznámkového bloku v Databricks](./media/data-lake-storage-handle-data-using-databricks/databricks-create-notebook.png "vytvoření poznámkového bloku v Databricks")

3. V dialogovém okně **Vytvořit poznámkový blok** zadejte název poznámkového bloku. Vyberte jazyk **Scala** a vyberte cluster Spark, který jste vytvořili v předchozí části.

    ![Zadejte podrobnosti pro poznámkového bloku v Databricks](./media/data-lake-storage-handle-data-using-databricks/databricks-notebook-details.png "zadejte podrobnosti pro poznámkového bloku v Databricks")

4. Vyberte **Vytvořit**.

5. Zkopírujte a vložte následující blok kódu do první buňky.

   ```scala
   spark.conf.set("fs.azure.account.auth.type.<storage-account-name>.dfs.core.windows.net", "OAuth")
   spark.conf.set("fs.azure.account.oauth.provider.type.<storage-account-name>.dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
   spark.conf.set("fs.azure.account.oauth2.client.id.<storage-account-name>.dfs.core.windows.net", "<application-id>")
   spark.conf.set("fs.azure.account.oauth2.client.secret.<storage-account-name>.dfs.core.windows.net", "<authentication-key>")
   spark.conf.set("fs.azure.account.oauth2.client.endpoint.<storage-account-name>.dfs.core.windows.net", "https://login.microsoftonline.com/<tenant-id>/oauth2/token")
   ```

6. V tomto bloku kódu, nahraďte `application-id`, `authentication-id`, a `tenant-id` hodnoty zástupných symbolů v tomto bloku kódu nahraďte hodnotami, které jste shromáždili, když jste dokončili kroky v [odložit konfigurací účtu úložiště](#config). Nahradit `storage-account-name` zástupnou hodnotu s názvem účtu úložiště.

7. Stisknutím klávesy **SHIFT + ENTER** klíče pro spuštění kódu v tomto bloku.

8. Nyní můžete načíst ukázkový soubor json jako datový rámec v Azure Databricks. Vložte následující kód do nové buňky. Nahraďte zástupné symboly v závorce s vašimi hodnotami.

   ```scala
   val df = spark.read.json("abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/small_radio_json.json")
   ```

   * Nahradit `file-system-name` zástupnou hodnotu s názvem, který jste zadali systému souborů v Průzkumníku služby Storage.

   * Nahradit `storage-account-name` zástupný symbol s názvem účtu úložiště.

9. Stisknutím klávesy **SHIFT + ENTER** klíče pro spuštění kódu v tomto bloku.

10. Spuštěním následujícího kódu zobrazíte obsah datového rámce:

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

Jak už bylo zmíněno dříve, konektor SQL Data Warehouse používá Azure Blob storage jako dočasné úložiště k odesílání dat mezi službami Azure Databricks a Azure SQL Data Warehouse. Proto musíte napřed zadat konfiguraci pro připojení k účtu tohoto úložiště. Je potřeba už měli vytvořený účet jako součást požadavků pro účely tohoto článku.

1. Zadejte konfiguraci pro přístup k účtu Azure Storage z Azure Databricks.

   ```scala
   val blobStorage = "<blob-storage-account-name>.blob.core.windows.net"
   val blobContainer = "<blob-container-name>"
   val authenticationKey =  "<authentication-key>"
   ```

2. Zadejte dočasnou složku pro použití při přesouvání dat mezi službami Azure Databricks a Azure SQL Data Warehouse.

   ```scala
   val tempDir = "wasbs://" + blob-container-name + "@" + blobStorage +"/tempDirs"
   ```

3. Spusťte následující fragment kódu, který v konfiguraci uloží přístupové klíče služby Azure Blob Storage. Tím zajistíte, že není nutné udržovat přístupový klíč do poznámkového bloku ve formátu prostého textu.

   ```scala
   val acntInfo = "fs.azure.account.key."+ blobStorage
   sc.hadoopConfiguration.set(acntInfo, authenticationKey)
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
   val sqlDwUrl = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass + ";$dwJdbcExtraOptions"
   val sqlDwUrlSmall = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass
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

6. Připojení k SQL database a ověřte, jestli se databáze s názvem **SampleTable**.

   ![Ověření ukázkové tabulky](./media/data-lake-storage-handle-data-using-databricks/verify-sample-table.png "ověření ukázkové tabulky")

7. Spusťte výběrový dotaz, kterým ověříte obsah tabulky. Tabulka by měla obsahovat stejná data jako **renamedColumnsDF** datového rámce.

    ![Ověření obsahu ukázkové tabulky](./media/data-lake-storage-handle-data-using-databricks/verify-sample-table-content.png "ověření obsahu ukázkové tabulky")

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení tohoto kurzu můžete cluster ukončit. V pracovním prostoru Azure Databricks vyberte **clustery** na levé straně. Pro cluster ukončit, v části **akce**, přejděte na tři tečky (...) a vyberte **Terminate** ikonu.

![Zastavení clusteru Databricks](./media/data-lake-storage-handle-data-using-databricks/terminate-databricks-cluster.png "Zastavení clusteru Databricks")

Pokud cluster není ukončit ručně, se automaticky zastaví, pokud jste vybrali **po provedení \_ \_ počet minut nečinnosti** zaškrtávací políčko při vytváření clusteru. V takovém případě se cluster automaticky zastaví, pokud byl neaktivní po zadanou dobu.

## <a name="next-steps"></a>Další postup

Přejděte k dalšímu kurzu se naučíte Streamovat data v reálném čase do Azure Databricks pomocí služby Azure Event Hubs.

> [!div class="nextstepaction"]
>[Stream dat do Azure Databricks pomocí služby Event Hubs](../../azure-databricks/databricks-stream-from-eventhubs.md)
