---
title: Kurz – provádění operací ETL pomocí Azure Databricks
description: V tomto kurzu se dozvíte, jak extrahovat data z Data Lake Storage Gen2 do Azure Databricks, transformovat data a pak načíst data do Azure Synapse Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.custom: mvc
ms.topic: tutorial
ms.date: 01/29/2020
ms.openlocfilehash: fa7750a6e7888b6ca13c1ec32cabee9bcf803e65
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382729"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-azure-databricks"></a>Kurz: Extrahujte, transformujte a načtěte data pomocí Azure Databricks

V tomto kurzu provedete operaci ETL (extrahování, transformace a načítání dat) pomocí Azure Databricks. Extrahujete data z Azure Data Lake Storage Gen2 do Azure Databricks, spustíte transformace na datech v Azure Databricks a načtete transformovaná data do Azure Synapse Analytics.

Kroky v tomto kurzu používají konektor Azure Synapse pro Azure Databricks k přenosu dat do Azure Databricks. Tento konektor zase používá Azure Blob Storage jako dočasné úložiště pro data přenášená mezi clusterem Azure Databricks a Azure Synapse.

Následující obrázek ukazuje běh aplikace:

![Azure Databricks s úložištěm datových jezer a Azure Synapse](./media/databricks-extract-load-sql-data-warehouse/databricks-extract-transform-load-sql-datawarehouse.png "Azure Databricks s úložištěm datových jezer a Azure Synapse")

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Vytvořte službu Azure Databricks.
> * Vytvořte cluster Spark v Azure Databricks.
> * Vytvořte systém souborů v účtu Data Lake Storage Gen2.
> * Nahrajte ukázková data do účtu Azure Data Lake Storage Gen2.
> * Vytvořte instanční objekt.
> * Extrahujte data z účtu Azure Data Lake Storage Gen2.
> * Transformujte data v Azure Databricks.
> * Načtení dat do Azure Synapse.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

> [!Note]
> Tento kurz nelze provést pomocí **bezplatného zkušebního předplatného Azure**.
> Pokud máte bezplatný účet, přejděte na svůj profil a změňte předplatné na **průběžně placené**. Další informace najdete na stránce [bezplatného účtu Azure](https://azure.microsoft.com/free/). Potom [odeberte limit útraty](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)a [požádejte o zvýšení kvóty](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) pro virtuální procesory ve vaší oblasti. Když vytvoříte pracovní prostor Azure Databricks, můžete vybrat **zkušební (premium - 14denní jednotku DBU)** a poskytnout tak pracovnímu prostoru přístup k bezplatným dbům Azure Databricks Azure na 14 dní.
     
## <a name="prerequisites"></a>Požadavky

Před zahájením tohoto kurzu proveďte tyto úkoly:

* Vytvořte Azure Synapse, vytvořte pravidlo brány firewall na úrovni serveru a připojte se k serveru jako správce serveru. Viz [Úvodní příručka: Vytvoření a dotazování fondu Synapse SQL pomocí portálu Azure](../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md).

* Vytvořte hlavní klíč pro Azure Synapse. Viz [Vytvoření hlavního klíče databáze](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-a-database-master-key).

* Vytvořili jste účet Azure Blob Storage a v něm kontejner. A načetli jste přístupový klíč pro přístup k účtu úložiště. Viz [Úvodní příručka: Nahrávání, stahování a seznam objektů BLOB s portálem Azure](../storage/blobs/storage-quickstart-blobs-portal.md).

* Vytvořte účet úložiště Azure Data Lake Storage Gen2. Viz [Úvodní příručka: Vytvoření účtu úložiště Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-quickstart-create-account.md).

* Vytvořte instanční objekt. Viz [Postup: Pomocí portálu vytvořte instanční objekt azure a služby, který má přístup k prostředkům](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

   Existuje několik konkrétních věcí, které budete muset udělat, když provedete kroky v tomto článku.

   * Při provádění kroků v [části Přiřadit aplikaci k](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application) části role článku nezapomeňte přiřadit roli **přispěvatele dat objektů blob úložiště** k instančnímu objektu v oboru účtu Gen2 úložiště datového bodu. Pokud roli přiřadíte nadřazené skupině prostředků nebo předplatnému, budete dostávat chyby související s oprávněními, dokud se tato přiřazení rolí nerozšíří do účtu úložiště.

      Pokud dáváte přednost použití seznamu řízení přístupu (ACL) k přidružení instančního objektu k určitému souboru nebo adresáři, odkazujte na [řízení přístupu v Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-access-control.md).

   * Při provádění kroků v části [Získat hodnoty pro podepisování v](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) článku vložte ID klienta, ID aplikace a tajné hodnoty do textového souboru.

* Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="gather-the-information-that-you-need"></a>Shromážděte informace, které potřebujete

Ujistěte se, že jste dokončili předpoklady tohoto kurzu.

   Než začnete, měli byste mít tyto informace:

   :heavy_check_mark: Název databáze, název databázového serveru, uživatelské jméno a heslo vaší Azure Synapse.

   :heavy_check_mark: Přístupový klíč vašeho účtu úložiště objektů blob.

   :heavy_check_mark: Název vašeho účtu úložiště Data Lake Storage Gen2.

   :heavy_check_mark: ID klienta vašeho předplatného.

   :heavy_check_mark: ID aplikace aplikace, kterou jste zaregistrovali ve službě Azure Active Directory (Azure AD).

   :heavy_check_mark: Ověřovací klíč pro aplikaci, kterou jste zaregistrovali ve službě Azure AD.

## <a name="create-an-azure-databricks-service"></a>Vytvoření služby Azure Databricks

V této části vytvoříte službu Azure Databricks pomocí portálu Azure.

1. V nabídce webu Azure Portal vyberte **Vytvořit prostředek**.

    ![Vytvoření prostředku na webu Azure Portal](./media/databricks-extract-load-sql-data-warehouse/azure-databricks-on-portal.png)

    Potom vyberte **Analytics** > **Azure Databricks**.

    ![Vytvoření Datových cihel Azure na webu Azure Portal](./media/databricks-extract-load-sql-data-warehouse/azure-databricks-resource-create.png)



2. V části **Azure Databricks Service**zadejte následující hodnoty pro vytvoření služby Databricks:

    |Vlastnost  |Popis  |
    |---------|---------|
    |**Název pracovního prostoru**     | Zadejte název pracovního prostoru Databricks.        |
    |**Předplatné**     | Z rozevíracího seznamu vyberte své předplatné Azure.        |
    |**Skupina prostředků**     | Určete, jestli chcete vytvořit novou skupinu prostředků, nebo použít existující. Skupina prostředků je kontejner, který obsahuje související prostředky pro řešení Azure. Další informace naleznete v tématu [Přehled skupin prostředků v Azure](../azure-resource-manager/management/overview.md). |
    |**Umístění**     | Vyberte **USA – západ 2**.  Další dostupné oblasti najdete v tématu [Dostupné služby Azure podle oblastí](https://azure.microsoft.com/regions/services/).      |
    |**Cenová úroveň**     |  Vyberte **standardní**.     |

3. Vytvoření účtu trvá několik minut. Chcete-li sledovat stav operace, zobrazte indikátor průběhu nahoře.

4. Vyberte **Připnout na řídicí panel** a potom vyberte **Vytvořit**.

## <a name="create-a-spark-cluster-in-azure-databricks"></a>Vytvoření clusteru Spark v Azure Databricks

1. Na webu Azure Portal přejděte na službu Databricks, kterou jste vytvořili, a vyberte **Spustit pracovní prostor**.

2. Budete přesměrováni na portál Azure Databricks. Na portálu vyberte **Cluster**.

    ![Datové cihly v Azure](./media/databricks-extract-load-sql-data-warehouse/databricks-on-azure.png "Datové cihly v Azure")

3. Na stránce **New cluster** (Nový cluster) zadejte hodnoty pro vytvoření clusteru.

    ![Vytvoření clusteru Databricks Spark v Azure](./media/databricks-extract-load-sql-data-warehouse/create-databricks-spark-cluster.png "Vytvoření clusteru Databricks Spark v Azure")

4. Zadejte hodnoty následujících polí a potvrďte výchozí hodnoty dalších polí:

    * Zadejte název clusteru.

    * Ujistěte se, že jste zaškrtli políčko **Ukončit po \_ \_ minutách nečinnosti.** Pokud se cluster nepoužívá, zadejte dobu trvání (v minutách) k ukončení clusteru.

    * Vyberte **Vytvořit cluster**. Po spuštění clusteru můžete k clusteru připojit poznámkové bloky a spustit úlohy Spark.

## <a name="create-a-file-system-in-the-azure-data-lake-storage-gen2-account"></a>Vytvoření systému souborů v účtu Azure Data Lake Storage Gen2

V této části vytvoříte poznámkový blok v pracovním prostoru Azure Databricks a pak spustíte fragmenty kódu pro konfiguraci účtu úložiště.

1. Na [webu Azure Portal](https://portal.azure.com)přejděte na službu Azure Databricks, kterou jste vytvořili, a vyberte **Spustit pracovní prostor**.

2. Vlevo vyberte **Pracovní prostor**. V rozevíracím seznamu **Pracovní prostor** vyberte **Vytvořit** > **Poznámkový blok**.

    ![Vytvoření poznámkového bloku v datové cihly](./media/databricks-extract-load-sql-data-warehouse/databricks-create-notebook.png "Vytvoření poznámkového bloku v datových cihlách")

3. V dialogovém okně **Vytvořit poznámkový blok** zadejte název poznámkového bloku. Vyberte jazyk **Scala** a vyberte cluster Spark, který jste vytvořili v předchozí části.

    ![Poskytnutí podrobností o poznámkovém bloku v databricks](./media/databricks-extract-load-sql-data-warehouse/databricks-notebook-details.png "Poskytnutí podrobností o poznámkovém bloku v databricks")

4. Vyberte **Vytvořit**.

5. Následující blok kódu nastaví výchozí pověření zaregistrovaných služby pro všechny účtu ADLS Gen 2, ke které se přistupuje v relaci Spark. Druhý blok kódu připojí název účtu k nastavení k určení pověření pro konkrétní účet ADLS Gen 2.  Zkopírujte a vložte buď blok kódu do první buňky poznámkového bloku Azure Databricks.

   **Konfigurace relace**

   ```scala
   val appID = "<appID>"
   val secret = "<secret>"
   val tenantID = "<tenant-id>"

   spark.conf.set("fs.azure.account.auth.type", "OAuth")
   spark.conf.set("fs.azure.account.oauth.provider.type", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
   spark.conf.set("fs.azure.account.oauth2.client.id", "<appID>")
   spark.conf.set("fs.azure.account.oauth2.client.secret", "<secret>")
   spark.conf.set("fs.azure.account.oauth2.client.endpoint", "https://login.microsoftonline.com/<tenant-id>/oauth2/token")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "true")
   ```

   **Konfigurace účtu**

   ```scala
   val storageAccountName = "<storage-account-name>"
   val appID = "<app-id>"
   val secret = "<secret>"
   val fileSystemName = "<file-system-name>"
   val tenantID = "<tenant-id>"

   spark.conf.set("fs.azure.account.auth.type." + storageAccountName + ".dfs.core.windows.net", "OAuth")
   spark.conf.set("fs.azure.account.oauth.provider.type." + storageAccountName + ".dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
   spark.conf.set("fs.azure.account.oauth2.client.id." + storageAccountName + ".dfs.core.windows.net", "" + appID + "")
   spark.conf.set("fs.azure.account.oauth2.client.secret." + storageAccountName + ".dfs.core.windows.net", "" + secret + "")
   spark.conf.set("fs.azure.account.oauth2.client.endpoint." + storageAccountName + ".dfs.core.windows.net", "https://login.microsoftonline.com/" + tenantID + "/oauth2/token")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "true")
   dbutils.fs.ls("abfss://" + fileSystemName  + "@" + storageAccountName + ".dfs.core.windows.net/")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "false")
   ```

6. V tomto bloku kódu `<app-id>` `<secret>`nahraďte hodnoty , , `<tenant-id>`a `<storage-account-name>` zástupný symbol v tomto bloku kódu hodnotami, které jste shromáždili při vyplňování předpokladů tohoto kurzu. Nahraďte `<file-system-name>` zástupnou hodnotu jakýmkoli názvem, který chcete systému souborů přidělit.

   * The `<app-id>`, `<secret>` a jsou z aplikace, kterou jste zaregistrovali ve službě Active Directory jako součást vytváření instančního objektu.

   * Je `<tenant-id>` z vašeho předplatného.

   * Je `<storage-account-name>` název vašeho účtu úložiště Azure Data Lake Storage Gen2.

7. Stisknutím kláves **SHIFT + ENTER** spusťte kód v tomto bloku.

## <a name="ingest-sample-data-into-the-azure-data-lake-storage-gen2-account"></a>Ingestování ukázkových dat do účtu Azure Data Lake Storage Gen2

Než se pustíte do této části, je potřeba nejprve splnit následující požadavky:

Do buňky poznámkového bloku zadejte následující kód:

    %sh wget -P /tmp https://raw.githubusercontent.com/Azure/usql/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json

V buňce spusťte kód stisknutím **kláves SHIFT + ENTER.**

Nyní v nové buňce pod touto buňkou zadejte následující kód a nahraďte hodnoty, které se zobrazují v závorkách, stejnými hodnotami, které jste použili dříve:

    dbutils.fs.cp("file:///tmp/small_radio_json.json", "abfss://" + fileSystemName + "@" + storageAccountName + ".dfs.core.windows.net/")

V buňce spusťte kód stisknutím **kláves SHIFT + ENTER.**

## <a name="extract-data-from-the-azure-data-lake-storage-gen2-account"></a>Extrahování dat z účtu Azure Data Lake Storage Gen2

1. Teď můžete načíst ukázkový soubor json jako datový rámec v Azure Databricks. Vložte následující kód do nové buňky. Nahraďte zástupné symboly zobrazené v závorkách hodnotami.

   ```scala
   val df = spark.read.json("abfss://" + fileSystemName + "@" + storageAccountName + ".dfs.core.windows.net/small_radio_json.json")
   ```
2. Stisknutím kláves **SHIFT + ENTER** spusťte kód v tomto bloku.

3. Chcete-li zobrazit obsah datového rámce, spusťte následující kód:

    ```scala
    df.show()
    ```
   Zobrazí se výstup, který bude podobný následujícímu fragmentu kódu:

   ```output
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

Nezpracovaná ukázková data **small_radio_json.json** soubor zachycuje publikum pro rozhlasové stanice a má různé sloupce. V této části můžete transformovat data tak, aby načítala pouze určité sloupce z datové sady.

1. Nejprve načtěte z vytvořeného datového rámce pouze sloupce **firstName**, **lastName**, **gender**, **location**a **level.**

   ```scala
   val specificColumnsDf = df.select("firstname", "lastname", "gender", "location", "level")
   specificColumnsDf.show()
   ```

   Obdržíte výstup, jak je znázorněno v následujícím úryvku:

   ```output
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

   Obdržíte výstup, jak je znázorněno v následujícím fragmentu.

   ```output
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

## <a name="load-data-into-azure-synapse"></a>Načítání dat do Azure Synapse

V této části nahrajete transformovaná data do Azure Synapse. Pomocí konektoru Azure Synapse pro Azure Databricks přímo nahrát datový rámec jako tabulka ve fondu Synapse Spark.

Jak již bylo zmíněno dříve, konektor Azure Synapse používá azure blob úložiště jako dočasné úložiště k nahrávání dat mezi Azure Databricks a Azure Synapse. Proto musíte napřed zadat konfiguraci pro připojení k účtu tohoto úložiště. Již musíte vytvořit účet jako součást předpokladů pro tento článek.

1. Zadejte konfiguraci pro přístup k účtu Azure Storage z Azure Databricks.

   ```scala
   val blobStorage = "<blob-storage-account-name>.blob.core.windows.net"
   val blobContainer = "<blob-container-name>"
   val blobAccessKey =  "<access-key>"
   ```

2. Zadejte dočasnou složku, která se má použít při přesouvání dat mezi Azure Databricks a Azure Synapse.

   ```scala
   val tempDir = "wasbs://" + blobContainer + "@" + blobStorage +"/tempDirs"
   ```

3. Spusťte následující fragment kódu, který v konfiguraci uloží přístupové klíče služby Azure Blob Storage. Tato akce zajistí, že není třeba uchovávat přístupový klíč v poznámkovém bloku ve formátu prostého textu.

   ```scala
   val acntInfo = "fs.azure.account.key."+ blobStorage
   sc.hadoopConfiguration.set(acntInfo, blobAccessKey)
   ```

4. Zadejte hodnoty pro připojení k instanci Azure Synapse. Jako předpoklad musíte vytvořit službu Azure Synapse Analytics. Použijte plně kvalifikovaný název serveru pro **dwServer**. Například, `<servername>.database.windows.net`.

   ```scala
   //Azure Synapse related settings
   val dwDatabase = "<database-name>"
   val dwServer = "<database-server-name>"
   val dwUser = "<user-name>"
   val dwPass = "<password>"
   val dwJdbcPort =  "1433"
   val dwJdbcExtraOptions = "encrypt=true;trustServerCertificate=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"
   val sqlDwUrl = "jdbc:sqlserver://" + dwServer + ":" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass + ";$dwJdbcExtraOptions"
   val sqlDwUrlSmall = "jdbc:sqlserver://" + dwServer + ":" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass
   ```

5. Spusťte následující úryvek k načtení transformovaného datového rámce **s názvemColumnsDF**jako tabulky v Azure Synapse. Tento fragment kódu vytvoří v SQL databázi tabulku s názvem **SampleTable**.

   ```scala
   spark.conf.set(
       "spark.sql.parquet.writeLegacyFormat",
       "true")

   renamedColumnsDF.write.format("com.databricks.spark.sqldw").option("url", sqlDwUrlSmall).option("dbtable", "SampleTable")       .option( "forward_spark_azure_storage_credentials","True").option("tempdir", tempDir).mode("overwrite").save()
   ```

   > [!NOTE]
   > Tato ukázka `forward_spark_azure_storage_credentials` používá příznak, který způsobí, že Azure Synapse pro přístup k datům z úložiště objektů blob pomocí přístupového klíče. Toto je jediná podporovaná metoda ověřování.
   >
   > Pokud je úložiště objektů blob Azure omezeno na vybrané virtuální sítě, Azure Synapse vyžaduje [identitu spravované služby místo přístupových klíčů](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). To způsobí chybu "Tento požadavek není oprávněn k provedení této operace."

6. Připojte se k databázi SQL a ověřte, zda se zobrazí databáze s názvem **SampleTable**.

   ![Ověření ukázkové tabulky](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table.png "Ověřit ukázkovou tabulku")

7. Spusťte výběrový dotaz, kterým ověříte obsah tabulky. Tabulka by měla mít stejná data jako přejmenovaný datový rámec **ColumnsDF.**

    ![Ověření obsahu ukázkové tabulky](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table-content.png "Ověření obsahu ukázkové tabulky")

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení kurzu můžete cluster ukončit. Z pracovního prostoru Azure Databricks vyberte **clustery** vlevo. Chcete-li clusteru ukončit, přejděte v části **Akce**na tři tečky (...) a vyberte ikonu **Ukončit.**

![Zastavení clusteru Databricks](./media/databricks-extract-load-sql-data-warehouse/terminate-databricks-cluster.png "Zastavení clusteru Databricks")

Pokud cluster ručně neukončíte, automaticky se zastaví za předpokladu, že jste při vytváření clusteru zaškrtli políčko **Ukončit po \_ \_ minutách nečinnosti.** V takovém případě se cluster automaticky zastaví, pokud byl po zadaný čas neaktivní.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření služby Azure Databricks
> * Vytvoření clusteru Spark v Azure Databricks
> * Vytvoření poznámkového bloku v Azure Databricks
> * Extrahování dat z účtu Data Lake Storage Gen2
> * Transformace dat v Azure Databricks
> * Načítání dat do Azure Synapse

Pokračujte dalším kurzem, ve kterém se naučíte streamovat data v reálném čase do Azure Databricks pomocí služby Azure Event Hubs.

> [!div class="nextstepaction"]
>[Streamování dat do Azure Databricks pomocí služby Event Hubs](databricks-stream-from-eventhubs.md)
