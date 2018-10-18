---
title: 'Kurz: Provádění operací ETL pomocí Azure Databricks'
description: Přečtěte si, jak extrahovat data z Data Lake Store do Azure Databricks, jak je transformovat a načíst do Azure SQL Data Warehouse.
services: azure-databricks
author: nitinme
ms.author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.custom: mvc
ms.topic: tutorial
ms.workload: Active
ms.date: 07/26/2018
ms.openlocfilehash: c416937f98f6bcb49f86fce18213ca4ed349c513
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902055"
---
# <a name="tutorial-extract-transform-and-load-data-using-azure-databricks"></a>Kurz: Extrakce, transformace a načtení dat pomocí Azure Databricks

V tomto kurzu budete provádět operace ETL (extrahování, transformace a načítání dat) pomocí Azure Databricks. Budete extrahovat data z Azure Data Lake Store do Azure Databricks, spouštět transformace dat v Azure Databricks a načítat transformovaná data do služby Azure SQL Data Warehouse. 

Postup, který je popsaný v tomto kurzu, používá k přenosu dat do Azure Databricks konektor SQL Data Warehouse pro Azure Databricks. Tento konektor zase používá Azure Blob Storage jako dočasné úložiště dat přenášených mezi clusterem Azure Databricks a službou Azure SQL Data Warehouse.

Následující obrázek ukazuje běh aplikace:

![Azure Databricks se službami Data Lake Store a SQL Data Warehouse](./media/databricks-extract-load-sql-data-warehouse/databricks-extract-transform-load-sql-datawarehouse.png "Azure Databricks se službami Data Lake Store a SQL Data Warehouse")

Tento kurz se zabývá následujícími úkony: 

> [!div class="checklist"]
> * Vytvoření pracovního prostoru Azure Databricks
> * Vytvoření clusteru Spark v Azure Databricks
> * Vytvoření účtu Azure Data Lake Store
> * Nahrání dat do služby Azure Data Lake Store
> * Vytvoření poznámkového bloku v Azure Databricks
> * Extrahování dat ze služby Data Lake Store
> * Transformace dat v Azure Databricks
> * Načtení dat do Azure SQL Data Warehouse

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem, ujistěte se, že splňujete následující požadavky:
- Vytvořili jste Azure SQL Data Warehouse, vytvořili jste na serveru pravidlo firewallu a připojili jste se k serveru jako správce. Použijte postup v tématu [Rychlý start: Vytvoření Azure SQL Data Warehouse](../sql-data-warehouse/create-data-warehouse-portal.md).
- Vytvořili jste v Azure SQL Data Warehouse hlavní databázový klíč. Použijte postup v tématu [Vytvoření hlavního databázového klíče](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-a-database-master-key).
- Vytvořili jste účet Azure Blob Storage a v něm kontejner. A načetli jste přístupový klíč pro přístup k účtu úložiště. Použijte pokyny v tématu [Rychlý start: Vytvoření účtu Azure Blog Storage](../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="log-in-to-the-azure-portal"></a>Přihlášení k portálu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="create-an-azure-databricks-workspace"></a>Vytvoření pracovního prostoru Azure Databricks

V této části vytvoříte pomocí portálu Azure pracovní prostor služby Azure Databricks. 

1. Na webu Azure Portal vyberte **Vytvořit prostředek** > **Data a analýzy** > **Azure Databricks**.

    ![Databricks na webu Azure Portal](./media/databricks-extract-load-sql-data-warehouse/azure-databricks-on-portal.png "Databricks na webu Azure Portal")

3. V části **Služba Azure Databricks** zadejte hodnoty pro vytvoření pracovního prostoru Databricks.

    ![Vytvoření pracovního prostoru služby Azure Databricks](./media/databricks-extract-load-sql-data-warehouse/create-databricks-workspace.png "Vytvoření pracovního prostoru služby Azure Databricks")

    Zadejte následující hodnoty: 
     
    |Vlastnost  |Popis  |
    |---------|---------|
    |**Název pracovního prostoru**     | Zadejte název pracovního prostoru Databricks.        |
    |**Předplatné**     | Z rozevíracího seznamu vyberte své předplatné Azure.        |
    |**Skupina prostředků**     | Určete, jestli chcete vytvořit novou skupinu prostředků, nebo použít existující. Skupina prostředků je kontejner, který obsahuje související prostředky pro řešení Azure. Další informace naleznete v tématu [Přehled skupin prostředků v Azure](../azure-resource-manager/resource-group-overview.md). |
    |**Umístění**     | Vyberte **Východní USA 2**. Další dostupné oblasti najdete v tématu [Dostupné služby Azure podle oblastí](https://azure.microsoft.com/regions/services/).        |
    |**Cenová úroveň**     |  Zvolte úroveň **Standard** nebo **Premium**. Další informace o těchto úrovních najdete na [stránce s cenami za Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

    Vyberte **Připnout na řídicí panel** a potom vyberte **Vytvořit**.

4. Vytvoření účtu trvá několik minut. Během vytváření účtu se na pravé straně portálu zobrazí dlaždice **Odesílání nasazení pro Azure Databricks**. Možná se budete muset posunout do pravé části řídicího panelu, aby se dlaždice zobrazila. V horní části obrazovky se také zobrazí indikátor průběhu. Průběh můžete sledovat v obou oblastech.

    ![Dlaždice nasazení Databricks](./media/databricks-extract-load-sql-data-warehouse/databricks-deployment-tile.png "Dlaždice nasazení Databricks")

## <a name="create-a-spark-cluster-in-databricks"></a>Vytvoření clusteru Spark ve službě Databricks

1. Na webu Azure Portal přejděte do pracovního prostoru Databricks, který jste vytvořili, a vyberte **Spustit pracovní prostor**.

2. Budete přesměrováni na portál Azure Databricks. Na portálu vyberte **Cluster**.

    ![Databricks v Azure](./media/databricks-extract-load-sql-data-warehouse/databricks-on-azure.png "Databricks v Azure")

3. Na stránce **New cluster** (Nový cluster) zadejte hodnoty pro vytvoření clusteru.

    ![Vytvoření clusteru Databricks Spark v Azure](./media/databricks-extract-load-sql-data-warehouse/create-databricks-spark-cluster.png "Vytvoření clusteru Databricks Spark v Azure")

    Přijměte všechny výchozí hodnoty kromě těchto:

    * Zadejte název clusteru.
    * Pro účely tohoto článku vytvořte cluster s modulem runtime verze **4.0**. 
    * Nezapomeňte zaškrtnout políčko **Terminate after ____ minutes of inactivity** (Ukončit po ____ minutách neaktivity). Zadejte dobu (v minutách), po které se má ukončit činnost clusteru, pokud se cluster nepoužívá.
    
    Vyberte **Vytvořit cluster**. Po spuštění clusteru můžete ke clusteru připojit poznámkové bloky a spouštět úlohy Spark.

## <a name="create-an-azure-data-lake-store-account"></a>Vytvoření účtu Azure Data Lake Store

V této části vytvoříte účet Azure Data Lake Store a přidružíte k němu objekt služby Azure Active Directory. V další části tohoto kurzu tento objekt použijete v Azure Databricks pro přístup ke službě Azure Data Lake Store. 

1. Na portálu [Azure Portal](https://portal.azure.com) vyberte **Vytvořit prostředek** > **Úložiště** > **Data Lake Store**.
3. V okně **Nová služba Data Lake Store** zadejte hodnoty tak, jak ukazuje následující snímek obrazovky:
   
    ![Vytvoření nového účtu Azure Data Lake Store](./media/databricks-extract-load-sql-data-warehouse/create-new-datalake-store.png "Vytvoření nového účtu Azure Data Lake Store")

    Zadejte následující hodnoty: 
     
    |Vlastnost  |Popis  |
    |---------|---------|
    |**Název**     | Zadejte jedinečný název účtu Data Lake Storu.        |
    |**Předplatné**     | Z rozevíracího seznamu vyberte své předplatné Azure.        |
    |**Skupina prostředků**     | Pro tento kurz vyberte stejnou skupinu prostředků, jako při vytváření pracovního prostoru Azure Databricks.  |
    |**Umístění**     | Vyberte **Východní USA 2**.  |
    |**Cenový balíček**     |  Vyberte **průběžné platby**. |
    | **Nastavení šifrování** | Nechte výchozí nastavení. |

    Vyberte **Připnout na řídicí panel** a potom vyberte **Vytvořit**.

Teď vytvoříte instanční objekt Azure Active Directory a přidružíte ho k vytvořenému účtu Data Lake Store.

### <a name="create-an-azure-active-directory-service-principal"></a>Vytvoření objektu služby Azure Active Directory
   
1. Na portálu [Azure Portal](https://portal.azure.com) vyberte **Všechny služby** a najděte **Azure Active Directory**.

2. Vyberte **Registrace aplikací**.

   ![Výběr Registrace aplikací](./media/databricks-extract-load-sql-data-warehouse/select-app-registrations.png)

3. Vyberte **Registrace nové aplikace**.

   ![Přidání aplikace](./media/databricks-extract-load-sql-data-warehouse/select-add-app.png)

4. Zadejte název a URL aplikace. V poli **Webová aplikace / webové rozhraní API** vyberte typ vytvářené aplikace. Zadejte přihlašovací adresu URL a vyberte **Vytvořit**.

   ![Pojmenování aplikace](./media/databricks-extract-load-sql-data-warehouse/create-app.png)

Pokud chcete ze služby Azure Databricks získat přístup k účtu Data Lake Store, musíte mít následující hodnoty k vytvořenému objektu služby Azure Active Directory:
- ID aplikace
- Ověřovací klíč
- ID tenanta

V dalších částech načtete tyto hodnoty dříve vytvořeného objektu služby Azure Active Directory.

### <a name="get-application-id-and-authentication-key-for-the-service-principal"></a>Získání ID aplikace a ověřovacího klíče k objektu služby

K programovému přihlášení potřebujete ID aplikace a ověřovací klíč. K získání těchto hodnot použijte následující postup:

1. V Azure Active Directory vyberte z **Registrace aplikací** svou aplikaci.

   ![Výběr aplikace](./media/databricks-extract-load-sql-data-warehouse/select-app.png)

2. Zkopírujte **ID aplikace** a uložte ho v kódu aplikace. V některých [ukázkových aplikacích](#log-in-as-the-application) se tato hodnota označuje jako ID klienta.

   ![ID klienta](./media/databricks-extract-load-sql-data-warehouse/copy-app-id.png)

3. Pokud chcete generovat ověřovací klíč, vyberte **Nastavení**.

   ![Volba Nastavení](./media/databricks-extract-load-sql-data-warehouse/select-settings.png)

4. Pokud chcete generovat ověřovací klíč, vyberte **Klíče**.

   ![Volba Klíče](./media/databricks-extract-load-sql-data-warehouse/select-keys.png)

5. Zadejte popis klíče a jeho dobu platnosti. Až budete hotovi, vyberte **Uložit**.

   ![Uložení klíče](./media/databricks-extract-load-sql-data-warehouse/save-key.png)

   Jakmile klíč uložíte, zobrazí se jeho hodnota. Zkopírujte si ji, protože později už klíč nepůjde načíst. Hodnotu klíče uveďte s ID aplikace, aby bylo možné se přihlásit jako aplikace. Hodnotu klíče uložte na místo, odkud ji aplikace může načíst.

   ![Uložený klíč](./media/databricks-extract-load-sql-data-warehouse/copy-key.png)

### <a name="get-tenant-id"></a>Získání ID tenanta

Při programovém přihlášení potřebujete kromě žádosti o ověření předat i ID tenanta.

1. Vyberte **Azure Active Directory**.

   ![Výběr Azure Active Directory](./media/databricks-extract-load-sql-data-warehouse/select-active-directory.png)

1. K získání ID tenanta vyberte v tenantovi Azure AD možnost **Vlastnosti**.

   ![Výběr položky Vlastnosti v Azure AD](./media/databricks-extract-load-sql-data-warehouse/select-ad-properties.png)

1. Zkopírujte **ID adresáře**. Tato hodnota představuje ID tenanta.

   ![ID tenanta](./media/databricks-extract-load-sql-data-warehouse/copy-directory-id.png) 

## <a name="upload-data-to-data-lake-store"></a>Nahrání dat do Data Lake Store

V této části nahrajete do služby Data Lake Store ukázkový datový soubor. Později tento soubor použijete v Azure Databricks ke spuštění některých transformací. Ukázková data (**small_radio_json.json**), která budete používat v tomto kurzu, jsou k dispozici v tomto [úložišti GitHub](https://github.com/Azure/usql/blob/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json).

1. Na portálu [Azure Portal](https://portal.azure.com) vyberte účet Data Lake Store, který jste vytvořili.

2. Na kartě **Přehled** klikněte na **Průzkumník dat**.

    ![Otevření Průzkumníku dat](./media/databricks-extract-load-sql-data-warehouse/open-data-explorer.png "Otevření Průzkumníku dat")

3. V Průzkumníku dat klikněte na **Nahrát**.

    ![Možnost Nahrát](./media/databricks-extract-load-sql-data-warehouse/upload-to-data-lake-store.png "Možnost Nahrát")

4. V okně **Nahrát soubory** přejděte k umístění ukázkového datového souboru a vyberte **Přidat vybrané soubory**.

    ![Možnost Nahrát](./media/databricks-extract-load-sql-data-warehouse/upload-data.png "Možnost Nahrát")

5. V tomto kurzu jste nahráli datový soubor do kořenového adresáře Data Lake Store. Soubor je k dispozici na adrese `adl://<YOUR_DATA_LAKE_STORE_ACCOUNT_NAME>.azuredatalakestore.net/small_radio_json.json`.

## <a name="associate-service-principal-with-azure-data-lake-store"></a>Přidružení objektu služby k Azure Data Lake Store

V této části přidružíte data v účtu Azure Data Lake Store k vytvořenému objektu služby Azure Active Directory. Tím zajistíte přístupnost účtu Data Lake Store z Azure Databricks. Ve scénáři popisovaném v tomto článku provedete čtení dat z Data Lake Store a naplníte jimi tabulku v SQL Data Warehouse. Jak se píše v článku [Přehled řízení přístupu ve službě Data Lake Store](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions) jsou pro oprávnění ke čtení souboru v Data Lake Store zapotřebí tyto předpoklady:

- Oprávnění **Provést** ve všech složkách ve struktuře složek vedoucí k souboru
- Oprávnění ke **čtení** samotného souboru

Tato oprávnění udělíte pomocí následujících kroků.

1. Na webu [Azure Portal](https://portal.azure.com) vyberte účet Data Lake Store, který jste vytvořili, a potom vyberte **Průzkumník dat**.

    ![Spuštění Průzkumníka dat](./media/databricks-extract-load-sql-data-warehouse/azure-databricks-data-explorer.png "Spuštění Průzkumníka dat")

2. V tomto scénáři se ukázkový datový soubor nachází v kořenovém adresáři struktury složek, takže je potřeba přiřadit oprávnění **Provést** jenom v kořenové složce. Provedete to tak, že v kořenovém adresáři Průzkumníka dat vyberete **Přístup**.

    ![Přidání seznamů ACL pro složku](./media/databricks-extract-load-sql-data-warehouse/add-adls-access-folder-1.png "Přidání seznamů ACL pro složku")

3. V části **Přístup** vyberte **Přidat**.

    ![Přidání seznamů ACL pro složku](./media/databricks-extract-load-sql-data-warehouse/add-adls-access-folder-2.png "Přidání seznamů ACL pro složku")

4. V části **Přiřadit oprávnění** klikněte na **Vybrat uživatele nebo skupinu** a vyhledejte objekt služby Azure Active Directory, který jste předtím vytvořili.

    ![Přidání přístupu k Data Lake Store](./media/databricks-extract-load-sql-data-warehouse/add-adls-access-folder-3.png "Přidání přístupu k Data Lake Store")

    Vyberte objekt služby AAD, který chcete přiřadit, a klikněte na **Vybrat**.

5. V části **Přiřadit oprávnění** klikněte na **Vybrat oprávnění** > **Provést**. Ponechte ostatní výchozí hodnoty a vyberte **OK** v části **Vybrat oprávnění** a potom v části **Přiřadit oprávnění**.

    ![Přidání přístupu k Data Lake Store](./media/databricks-extract-load-sql-data-warehouse/add-adls-access-folder-4.png "Přidání přístupu k Data Lake Store")

6. Vraťte se do Průzkumníka dat a klikněte na soubor, ke kterému chcete přiřadit oprávnění ke čtení. V části **Náhled souboru** vyberte **Přístup**.

    ![Přidání přístupu k Data Lake Store](./media/databricks-extract-load-sql-data-warehouse/add-adls-access-file-1.png "Přidání přístupu k Data Lake Store")

7. V části **Přístup** vyberte **Přidat**. V části **Přiřadit oprávnění** klikněte na **Vybrat uživatele nebo skupinu** a vyhledejte objekt služby Azure Active Directory, který jste předtím vytvořili.

    ![Přidání přístupu k Data Lake Store](./media/databricks-extract-load-sql-data-warehouse/add-adls-access-folder-3.png "Přidání přístupu k Data Lake Store")

    Vyberte objekt služby AAD, který chcete přiřadit, a klikněte na **Vybrat**.

8. V části **Přiřadit oprávnění** klikněte na **Vybrat oprávnění** > **Číst**. Vyberte **OK** v části **Vybrat oprávnění** a potom v části **Přiřadit oprávnění**.

    ![Přidání přístupu k Data Lake Store](./media/databricks-extract-load-sql-data-warehouse/add-adls-access-file-2.png "Přidání přístupu k Data Lake Store")

    Objekt služby má teď dostatečná oprávnění ke čtení ukázkového datového souboru ze služby Azure Data Lake Store.

## <a name="extract-data-from-data-lake-store"></a>Extrahování dat ze služby Data Lake Store

V této části nejprve vytvoříte v pracovním prostoru Azure Databricks poznámkový blok a pak spustíte fragmenty kódu, které extrahují data z Data Lake Store do Azure Databricks.

1. Na portálu [Azure Portal](https://portal.azure.com) přejděte do vytvořeného pracovního prostoru Azure Databricks a vyberte **Spustit pracovní prostor**.

2. V levém podokně vyberte **Pracovní prostor**. V rozevíracím seznamu **Pracovní prostor** vyberte **Vytvořit** > **Poznámkový blok**.

    ![Vytvoření poznámkového bloku v Databricks](./media/databricks-extract-load-sql-data-warehouse/databricks-create-notebook.png "Vytvoření poznámkového bloku v Databricks")

2. V dialogovém okně **Vytvořit poznámkový blok** zadejte název poznámkového bloku. Vyberte jazyk **Scala** a vyberte cluster Spark, který jste vytvořili v předchozí části.

    ![Vytvoření poznámkového bloku v Databricks](./media/databricks-extract-load-sql-data-warehouse/databricks-notebook-details.png "Vytvoření poznámkového bloku v Databricks")

    Vyberte **Vytvořit**.

3. Do prázdné buňky určené pro kód přidejte následující fragment kódu a zástupné hodnoty nahraďte hodnotami objektu služby Azure Active Directory, které jste si uložili v předchozí části.

        spark.conf.set("dfs.adls.oauth2.access.token.provider.type", "ClientCredential")
        spark.conf.set("dfs.adls.oauth2.client.id", "<APPLICATION-ID>")
        spark.conf.set("dfs.adls.oauth2.credential", "<AUTHENTICATION-KEY>")
        spark.conf.set("dfs.adls.oauth2.refresh.url", "https://login.microsoftonline.com/<TENANT-ID>/oauth2/token")

    Stiskněte **SHIFT+ENTER** a spusťte kód v buňce.

4. Teď můžete ukázkový soubor json načíst do Data Lake Store jako datový rámec Azure Databricks. Následující fragment kódu vložte do nové buňky kódu, nahraďte hodnotu zástupného textu a stiskněte **SHIFT+ENTER**.

        val df = spark.read.json("adl://<DATA LAKE STORE NAME>.azuredatalakestore.net/small_radio_json.json")

5. Spuštěním následujícího fragmentu kódu zobrazíte obsah datového rámce.

        df.show()

    Zobrazí se výstup, který bude podobný následujícímu fragmentu kódu:

        +---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
        |               artist|     auth|firstName|gender|itemInSession|  lastName|   length|  level|            location|method|    page| registration|sessionId|                song|status|           ts|userId|
        +---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
        | El Arrebato         |Logged In| Annalyse|     F|            2|Montgomery|234.57914| free  |  Killeen-Temple, TX|   PUT|NextSong|1384448062332|     1879|Quiero Quererte Q...|   200|1409318650332|   309|
        | Creedence Clearwa...|Logged In|   Dylann|     M|            9|    Thomas|340.87138| paid  |       Anchorage, AK|   PUT|NextSong|1400723739332|       10|        Born To Move|   200|1409318653332|    11|
        | Gorillaz            |Logged In|     Liam|     M|           11|     Watts|246.17751| paid  |New York-Newark-J...|   PUT|NextSong|1406279422332|     2047|                DARE|   200|1409318685332|   201|
        ...
        ...

Tím jste extrahovali data z Azure Data Lake Store do Azure Databricks.

## <a name="transform-data-in-azure-databricks"></a>Transformace dat v Azure Databricks

Nezpracovaná ukázková data **small_radio_json.json** obsahují data o posluchačích rozhlasové stanice uspořádaná do různých sloupců. V této části transformujete data tak, aby se z datové sady načetly jenom určité sloupce. 

1. Z vytvořeného datového rámce napřed načtěte jenom sloupce *firstName*, *lastName*, *gender*, *location* a *level*.

        val specificColumnsDf = df.select("firstname", "lastname", "gender", "location", "level")
        specificColumnsDf.show()

    Získáte výstup podobný následujícímu fragmentu kódu:

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

2.  Teď můžete v datech sloupec **level** přejmenovat na **subscription_type**.

        val renamedColumnsDf = specificColumnsDf.withColumnRenamed("level", "subscription_type")
        renamedColumnsDf.show()

    Získáte výstup podobný následujícímu fragmentu kódu:

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

## <a name="load-data-into-azure-sql-data-warehouse"></a>Načtení dat do Azure SQL Data Warehouse

V této části načtete transformovaná data do služby Azure SQL Data Warehouse. Pomocí konektoru Azure SQL Data Warehouse pro Azure Databricks můžete datový rámec nahrát přímo jako tabulku do služby Azure SQL Data Warehouse.

Už jsme si řekli, že konektor Azure SQL Data Warehouse používá k nahrávání dat mezi službami Azure Databricks a Azure SQL Data Warehouse dočasné úložiště Azure Blob Storage. Proto musíte napřed zadat konfiguraci pro připojení k účtu tohoto úložiště. Vytvoření účtu už bylo v předpokladech v úvodu tohoto článku.

1. Zadejte konfiguraci pro přístup k účtu Azure Storage z Azure Databricks.

        val blobStorage = "<STORAGE ACCOUNT NAME>.blob.core.windows.net"
        val blobContainer = "<CONTAINER NAME>"
        val blobAccessKey =  "<ACCESS KEY>"

2. Zadejte dočasnou složku, která se použije k přesunu dat mezi službami Azure Databricks a Azure SQL Data Warehouse.

        val tempDir = "wasbs://" + blobContainer + "@" + blobStorage +"/tempDirs"

3. Spusťte následující fragment kódu, který v konfiguraci uloží přístupové klíče služby Azure Blob Storage. Tím zajistíte, že přístupový klíč nebudete muset mít v poznámkovém bloku jako obyčejný text.

        val acntInfo = "fs.azure.account.key."+ blobStorage
        sc.hadoopConfiguration.set(acntInfo, blobAccessKey)

4. Zadejte hodnoty pro připojení k instanci Azure SQL Data Warehouse. Vytvoření instance SQL Data Warehouse bylo v předpokladech tohoto článku.

        //SQL Data Warehouse related settings
        val dwDatabase = "<DATABASE NAME>"
        val dwServer = "<DATABASE SERVER NAME>" 
        val dwUser = "<USER NAME>"
        val dwPass = "<PASSWORD>"
        val dwJdbcPort =  "1433"
        val dwJdbcExtraOptions = "encrypt=true;trustServerCertificate=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"
        val sqlDwUrl = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass + ";$dwJdbcExtraOptions"
        val sqlDwUrlSmall = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass

5. Spusťte následující fragment kódu, který načte transformovaný datový rámec **renamedColumnsDf** jako tabulku do služby SQL Data Warehouse. Tento fragment kódu vytvoří v SQL databázi tabulku s názvem **SampleTable**. Upozorňujeme, že Azure SQL DW vyžaduje hlavní klíč.  Hlavní klíč můžete vytvořit spuštěním příkazu „CREATE MASTER KEY;“ v sadě SQL Server Management Studio.

        spark.conf.set(
          "spark.sql.parquet.writeLegacyFormat",
          "true")
        
        renamedColumnsDf.write
            .format("com.databricks.spark.sqldw")
            .option("url", sqlDwUrlSmall) 
            .option("dbtable", "SampleTable")
            .option( "forward_spark_azure_storage_credentials","True")
            .option("tempdir", tempDir)
            .mode("overwrite")
            .save()

6. Připojte se k databázi SQL a zkontrolujte, že v ní je tabulka **SampleTable**.

    ![Ověření ukázkové tabulky](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table.png "Ověření ukázkové tabulky")

7. Spusťte výběrový dotaz, kterým ověříte obsah tabulky. Tabulka by měla obsahovat stejná data jako datový rámec **renamedColumnsDf**.

    ![Ověření obsahu ukázkové tabulky](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table-content.png "Ověření obsahu ukázkové tabulky")

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení tohoto kurzu můžete cluster ukončit. Pokud to chcete udělat, v levém podokně v pracovním prostoru Azure Databricks vyberte **Clusters** (Clustery). U clusteru, který chcete ukončit, přesuňte kurzor na tři tečky pod sloupcem **Actions** (Akce) a vyberte ikonu **Terminate** (Ukončit).

![Zastavení clusteru Databricks](./media/databricks-extract-load-sql-data-warehouse/terminate-databricks-cluster.png "Zastavení clusteru Databricks")

Pokud cluster neukončíte ručně, zastaví se automaticky za předpokladu, že jste při vytváření clusteru zaškrtli políčko **Ukončit po __ minutách nečinnosti**. V takovém případě se cluster automaticky zastaví, pokud byl po stanovenou dobu neaktivní.

## <a name="next-steps"></a>Další kroky 
V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření pracovního prostoru Azure Databricks
> * Vytvoření clusteru Spark v Azure Databricks
> * Vytvoření účtu Azure Data Lake Store
> * Nahrání dat do služby Azure Data Lake Store
> * Vytvoření poznámkového bloku v Azure Databricks
> * Extrahování dat ze služby Data Lake Store
> * Transformace dat v Azure Databricks
> * Načtení dat do Azure SQL Data Warehouse

Pokračujte dalším kurzem, ve kterém se naučíte streamovat data v reálném čase do Azure Databricks pomocí služby Azure Event Hubs.

> [!div class="nextstepaction"]
>[Streamování dat do Azure Databricks pomocí služby Event Hubs](databricks-stream-from-eventhubs.md)
