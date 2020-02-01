---
title: Kurz – provádění operací ETL pomocí Azure Databricks
description: V tomto kurzu zjistíte, jak extrahovat data z Data Lake Storage Gen2 do Azure Databricks, transformovat data a pak načíst data do Azure SQL Data Warehouse.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.custom: mvc
ms.topic: tutorial
ms.date: 01/29/2020
ms.openlocfilehash: 4c73a1e314888d99f4a5beea997265d28077e847
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2020
ms.locfileid: "76898616"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-azure-databricks"></a>Kurz: extrakce, transformace a načtení dat pomocí Azure Databricks

V tomto kurzu provedete operaci ETL (extrakce, transformace a načítání dat) pomocí Azure Databricks. Data z Azure Data Lake Storage Gen2 můžete extrahovat do Azure Databricks, spustit transformace dat v Azure Databricks a načíst transformovaná data do Azure SQL Data Warehouse.

Postup, který je popsaný v tomto kurzu, používá k přenosu dat do Azure Databricks konektor SQL Data Warehouse pro Azure Databricks. Tento konektor zase používá Azure Blob Storage jako dočasné úložiště dat přenášených mezi clusterem Azure Databricks a službou Azure SQL Data Warehouse.

Následující obrázek ukazuje běh aplikace:

![Azure Databricks s Data Lake Store a SQL Data Warehouse](./media/databricks-extract-load-sql-data-warehouse/databricks-extract-transform-load-sql-datawarehouse.png "Azure Databricks s Data Lake Store a SQL Data Warehouse")

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Vytvořte službu Azure Databricks.
> * Vytvořte v Azure Databricks cluster Spark.
> * Vytvořte v účtu Data Lake Storage Gen2 systém souborů.
> * Nahrajte ukázková data na účet Azure Data Lake Storage Gen2.
> * Vytvoření instančního objektu.
> * Extrahuje data z účtu Azure Data Lake Storage Gen2.
> * Transformuje data v Azure Databricks.
> * Načte data do Azure SQL Data Warehouse.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

> [!Note]
> Tento kurz se nedá provést pomocí **předplatného Azure free zkušební verze**.
> Pokud máte bezplatný účet, přejděte na svůj profil a změňte si předplatné na **průběžné platby**. Další informace najdete na stránce [bezplatného účtu Azure](https://azure.microsoft.com/free/). Pak [odeberte limit útraty](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)a [požádejte o zvýšení kvóty](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) pro vCPU ve vaší oblasti. Když vytváříte pracovní prostor Azure Databricks, můžete vybrat cenovou úroveň **DBU (Premium-14-days)** a poskytnout tak přístup k pracovnímu prostoru zdarma Premium Azure Databricks DBU po dobu 14 dnů.
     
## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem, dokončete tyto úkoly:

* Vytvořte službu Azure SQL Data Warehouse, vytvořte pravidlo brány firewall na úrovni serveru a připojte se k serveru jako správce serveru. Další informace najdete [v tématu rychlý Start: vytvoření a dotazování služby Azure SQL Data Warehouse v Azure Portal](../sql-data-warehouse/create-data-warehouse-portal.md).

* Vytvořte hlavní klíč pro Azure SQL Data Warehouse. Viz [Vytvoření hlavního klíče databáze](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-a-database-master-key).

* Vytvořili jste účet Azure Blob Storage a v něm kontejner. A načetli jste přístupový klíč pro přístup k účtu úložiště. Další informace najdete v tématu [rychlý Start: nahrání, stažení a výpis objektů BLOB pomocí Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md).

* Vytvořte účet úložiště Azure Data Lake Storage Gen2. Další informace najdete v tématu [rychlý Start: vytvoření účtu úložiště Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-quickstart-create-account.md).

* Vytvoření instančního objektu. Viz [Postup: použití portálu k vytvoření aplikace a instančního objektu služby Azure AD, který má přístup k prostředkům](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

   K dispozici je několik konkrétních věcí, které budete muset udělat při provádění kroků v tomto článku.

   * Při provádění kroků v části [přiřazení aplikace k roli](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) v článku je třeba přiřadit roli **Přispěvatel dat objektů BLOB úložiště** k instančnímu objektu v rozsahu Data Lake Storage Gen2 účtu. Pokud přiřadíte roli nadřazené skupině prostředků nebo předplatnému, obdržíte chyby související s oprávněními, dokud tato přiřazení role nerozšíříte do účtu úložiště.

      Pokud byste chtěli použít seznam řízení přístupu (ACL) k přidružení instančního objektu ke konkrétnímu souboru nebo adresáři, referenční [řízení přístupu v Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-access-control.md).

   * Při provádění kroků v části [získat hodnoty pro přihlášení v](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) článku Vložte ID TENANTA, ID aplikace a tajné hodnoty do textového souboru. Budete je potřebovat brzy.

* Přihlaste se na web [Azure Portal](https://portal.azure.com/).

## <a name="gather-the-information-that-you-need"></a>Shromážděte informace, které potřebujete.

Ujistěte se, že jste dokončili požadavky tohoto kurzu.

   Než začnete, měli byste mít tyto položky informací:

   : heavy_check_mark: název databáze, název databázového serveru, uživatelské jméno a heslo ke službě Azure SQL Data Warehouse.

   : heavy_check_mark: přístupový klíč účtu úložiště objektů BLOB.

   : heavy_check_mark: název vašeho účtu úložiště Data Lake Storage Gen2.

   : heavy_check_mark: ID tenanta vašeho předplatného.

   : heavy_check_mark: ID aplikace aplikace, kterou jste zaregistrovali v Azure Active Directory (Azure AD).

   : heavy_check_mark: ověřovací klíč pro aplikaci, kterou jste zaregistrovali ve službě Azure AD.

## <a name="create-an-azure-databricks-service"></a>Vytvoření služby Azure Databricks

V této části vytvoříte službu Azure Databricks pomocí Azure Portal.

1. V nabídce Azure Portal vyberte **vytvořit prostředek**.

    ![Vytvoření prostředku na Azure Portal](./media/databricks-extract-load-sql-data-warehouse/azure-databricks-on-portal.png)

    Pak vyberte **Analytics** > **Azure Databricks**.

    ![Vytvoření Azure Databricks na Azure Portal](./media/databricks-extract-load-sql-data-warehouse/azure-databricks-resource-create.png)



2. V části **Azure Databricks služba**zadejte následující hodnoty pro vytvoření služby datacihly:

    |Vlastnost  |Popis  |
    |---------|---------|
    |**Název pracovního prostoru**     | Zadejte název pracovního prostoru Databricks.        |
    |**Předplatné**     | Z rozevíracího seznamu vyberte své předplatné Azure.        |
    |**Skupina prostředků**     | Určete, jestli chcete vytvořit novou skupinu prostředků, nebo použít existující. Skupina prostředků je kontejner, který obsahuje související prostředky pro řešení Azure. Další informace naleznete v tématu [Přehled skupin prostředků v Azure](../azure-resource-manager/management/overview.md). |
    |**Umístění**     | Vyberte **Západní USA 2**.  Další dostupné oblasti najdete v tématu [Dostupné služby Azure podle oblastí](https://azure.microsoft.com/regions/services/).      |
    |**Cenová úroveň**     |  Vyberte **Standard**.     |

3. Vytvoření účtu trvá několik minut. Chcete-li monitorovat stav operace, zobrazte indikátor průběhu v horní části.

4. Vyberte **Připnout na řídicí panel** a potom vyberte **Vytvořit**.

## <a name="create-a-spark-cluster-in-azure-databricks"></a>Vytvoření clusteru Spark v Azure Databricks

1. V Azure Portal otevřete službu datacihly, kterou jste vytvořili, a vyberte **Spustit pracovní prostor**.

2. Budete přesměrováni na portál Azure Databricks. Na portálu vyberte **Cluster**.

    ![Datacihly v Azure](./media/databricks-extract-load-sql-data-warehouse/databricks-on-azure.png "Datacihly v Azure")

3. Na stránce **New cluster** (Nový cluster) zadejte hodnoty pro vytvoření clusteru.

    ![Vytvoření clusteru datacihly Spark v Azure](./media/databricks-extract-load-sql-data-warehouse/create-databricks-spark-cluster.png "Vytvoření clusteru datacihly Spark v Azure")

4. Zadejte hodnoty následujících polí a potvrďte výchozí hodnoty dalších polí:

    * Zadejte název clusteru.

    * Ujistěte se, že jste zaškrtli políčko **ukončit po \_\_ minut nečinnosti** . Pokud se cluster nepoužívá, zadejte dobu (v minutách), po kterou má cluster skončit.

    * Vyberte **Vytvořit cluster**. Po spuštění clusteru můžete ke clusteru připojit poznámkové bloky a spouštět úlohy Spark.

## <a name="create-a-file-system-in-the-azure-data-lake-storage-gen2-account"></a>Vytvoření systému souborů v účtu Azure Data Lake Storage Gen2

V této části vytvoříte v pracovním prostoru Azure Databricks Poznámkový blok a pak spustíte fragmenty kódu pro konfiguraci účtu úložiště.

1. V [Azure Portal](https://portal.azure.com)otevřete službu Azure Databricks, kterou jste vytvořili, a vyberte **Spustit pracovní prostor**.

2. Na levé straně vyberte **pracovní prostor**. V rozevíracím seznamu **Pracovní prostor** vyberte **Vytvořit** > **Poznámkový blok**.

    ![Vytvoření poznámkového bloku v datacihlech](./media/databricks-extract-load-sql-data-warehouse/databricks-create-notebook.png "Vytvoření poznámkového bloku v datacihlech")

3. V dialogovém okně **Vytvořit poznámkový blok** zadejte název poznámkového bloku. Vyberte jazyk **Scala** a vyberte cluster Spark, který jste vytvořili v předchozí části.

    ![Zadání podrobností pro Poznámkový blok v datacihlách](./media/databricks-extract-load-sql-data-warehouse/databricks-notebook-details.png "Zadání podrobností pro Poznámkový blok v datacihlách")

4. Vyberte **Vytvořit**.

5. Následující blok kódu nastaví výchozí přihlašovací údaje instančního objektu pro libovolný účet ADLS Gen 2, ke kterému se přistupoval v relaci Spark. Druhý blok kódu připojí název účtu k nastavení k zadání přihlašovacích údajů pro konkrétní účet ADLS Gen 2.  Zkopírujte a vložte blok kódu do první buňky Azure Databricks poznámkového bloku.

   **Konfigurace relace**

   ```scala
   val appID = "<appID>"
   val password = "<password>"
   val tenantID = "<tenant-id>"

   spark.conf.set("fs.azure.account.auth.type", "OAuth")
   spark.conf.set("fs.azure.account.oauth.provider.type", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
   spark.conf.set("fs.azure.account.oauth2.client.id", "<appID>")
   spark.conf.set("fs.azure.account.oauth2.client.secret", "<password>")
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

6. V tomto bloku kódu nahraďte `<app-id>`, `<secret>`, `<tenant-id>`a `<storage-account-name>` hodnoty zástupných symbolů v tomto bloku kódu hodnotami, které jste shromáždili při dokončování požadavků tohoto kurzu. Nahraďte hodnotu zástupného symbolu `<file-system-name>` jakýmkoli názvem, který chcete systému souborů poskytnout.

   * `<app-id>`a `<secret>` jsou z aplikace, kterou jste zaregistrovali se službou Active Directory, v rámci vytváření instančního objektu.

   * `<tenant-id>` je z vašeho předplatného.

   * `<storage-account-name>` je název vašeho účtu úložiště Azure Data Lake Storage Gen2.

7. Stiskněte klávesy **SHIFT + ENTER** a spusťte kód v tomto bloku.

## <a name="ingest-sample-data-into-the-azure-data-lake-storage-gen2-account"></a>Ingestování ukázkových dat do účtu Azure Data Lake Storage Gen2

Než se pustíte do této části, je potřeba nejprve splnit následující požadavky:

Do buňky poznámkového bloku zadejte následující kód:

    %sh wget -P /tmp https://raw.githubusercontent.com/Azure/usql/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json

V buňce stiskněte **SHIFT + ENTER** a kód se spustí.

Nyní vložte následující kód do nové buňky pod tímto kódem a nahraďte hodnoty zobrazené v závorkách stejnými hodnotami, které jste použili dříve:

    dbutils.fs.cp("file:///tmp/small_radio_json.json", "abfss://" + fileSystemName + "@" + storageAccountName + ".dfs.core.windows.net/")

V buňce stiskněte **SHIFT + ENTER** a kód se spustí.

## <a name="extract-data-from-the-azure-data-lake-storage-gen2-account"></a>Extrakce dat z Azure Data Lake Storage Gen2 účtu

1. Ukázkový soubor JSON teď můžete načíst jako datový rámec v Azure Databricks. Do nové buňky vložte následující kód. Zástupné symboly zobrazené v závorkách nahraďte hodnotami.

   ```scala
   val df = spark.read.json("abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/small_radio_json.json")
   ```
2. Stiskněte klávesy **SHIFT + ENTER** a spusťte kód v tomto bloku.

3. Chcete-li zobrazit obsah datového rámce, spusťte následující kód:

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

Nezpracovaná ukázková data **small_radio_json. JSON** zachytí cílovou skupinu pro radiovou stanici a má různé sloupce. V této části Transformujte data tak, aby se z datové sady načítala jenom konkrétní sloupce.

1. Nejdříve načtěte pouze sloupce **FirstName**, **LastName**, **pohlaví**, **Location**a **Level** z datového rámce, který jste vytvořili.

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

V této části načtete transformovaná data do služby Azure SQL Data Warehouse. Pomocí konektoru Azure SQL Data Warehouse můžete Azure Databricks přímo nahrát datový rámec jako tabulku v SQL Data Warehouse.

Jak už bylo zmíněno dříve, konektor SQL Data Warehouse používá úložiště objektů BLOB v Azure jako dočasné úložiště pro nahrávání dat mezi Azure Databricks a Azure SQL Data Warehouse. Proto musíte napřed zadat konfiguraci pro připojení k účtu tohoto úložiště. Účet již musíte mít již vytvořen jako součást požadavků pro tento článek.

1. Zadejte konfiguraci pro přístup k účtu Azure Storage z Azure Databricks.

   ```scala
   val blobStorage = "<blob-storage-account-name>.blob.core.windows.net"
   val blobContainer = "<blob-container-name>"
   val blobAccessKey =  "<access-key>"
   ```

2. Zadejte dočasnou složku, která se má použít při přesouvání dat mezi Azure Databricks a Azure SQL Data Warehouse.

   ```scala
   val tempDir = "wasbs://" + blobContainer + "@" + blobStorage +"/tempDirs"
   ```

3. Spusťte následující fragment kódu, který v konfiguraci uloží přístupové klíče služby Azure Blob Storage. Tato akce zajistí, že nebudete muset přístupový klíč v poznámkovém bloku uchovávat v prostém textu.

   ```scala
   val acntInfo = "fs.azure.account.key."+ blobStorage
   sc.hadoopConfiguration.set(acntInfo, blobAccessKey)
   ```

4. Zadejte hodnoty pro připojení k instanci Azure SQL Data Warehouse. Je nutné, abyste vytvořili datový sklad SQL jako předpoklad. Použijte plně kvalifikovaný název serveru pro **dwServer**. Například, `<servername>.database.windows.net`.

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

5. Spusťte následující fragment kódu, který načte transformovaný datový rámec **renamedColumnsDF**jako tabulku v SQL Data Warehouse. Tento fragment kódu vytvoří v SQL databázi tabulku s názvem **SampleTable**.

   ```scala
   spark.conf.set(
       "spark.sql.parquet.writeLegacyFormat",
       "true")

   renamedColumnsDF.write.format("com.databricks.spark.sqldw").option("url", sqlDwUrlSmall).option("dbtable", "SampleTable")       .option( "forward_spark_azure_storage_credentials","True").option("tempdir", tempDir).mode("overwrite").save()
   ```

   > [!NOTE]
   > Tato ukázka používá příznak `forward_spark_azure_storage_credentials`, který způsobí, že SQL Data Warehouse přístup k datům z úložiště objektů BLOB pomocí přístupového klíče. Toto je jediná podporovaná metoda ověřování.
   >
   > Pokud je Blob Storage Azure omezené na výběr virtuálních sítí, SQL Data Warehouse [místo přístupových klíčů vyžaduje identita spravované služby](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Tím dojde k chybě "Tato žádost není autorizována k provedení této operace".

6. Připojte se k databázi SQL a ověřte, že se zobrazí databáze s názvem **Samples**.

   ![Ověření ukázkové tabulky](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table.png "Ověřit ukázkovou tabulku")

7. Spusťte výběrový dotaz, kterým ověříte obsah tabulky. Tabulka by měla mít stejná data jako **renamedColumnsDF** dataframe.

    ![Ověření obsahu ukázkové tabulky](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table-content.png "Ověření obsahu ukázkové tabulky")

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení kurzu můžete cluster ukončit. V pracovním prostoru Azure Databricks na levé straně vyberte **clustery** . Pokud chcete cluster ukončit, v části **Akce**přejděte na tři tečky (...) a vyberte ikonu **ukončit** .

![Zastavení clusteru datacihly](./media/databricks-extract-load-sql-data-warehouse/terminate-databricks-cluster.png "Zastavení clusteru datacihly")

Pokud cluster neukončíte ručně, zastaví se automaticky za předpokladu, že jste při vytváření clusteru zaškrtli políčko **ukončit po \_\_ minut nečinnosti** . V takovém případě se cluster automaticky zastaví, pokud je po určenou dobu neaktivní.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření služby Azure Databricks
> * Vytvoření clusteru Spark v Azure Databricks
> * Vytvoření poznámkového bloku v Azure Databricks
> * Extrakce dat z Data Lake Storage Gen2 účtu
> * Transformace dat v Azure Databricks
> * Načtení dat do Azure SQL Data Warehouse

Pokračujte dalším kurzem, ve kterém se naučíte streamovat data v reálném čase do Azure Databricks pomocí služby Azure Event Hubs.

> [!div class="nextstepaction"]
>[Streamování dat do Azure Databricks pomocí služby Event Hubs](databricks-stream-from-eventhubs.md)
