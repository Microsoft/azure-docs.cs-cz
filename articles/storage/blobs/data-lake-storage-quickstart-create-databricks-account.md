---
title: 'Rychlý start: Analýza dat v Azure Data Lake Storage Gen2 pomocí Azure Databricks | Dokumentace Microsoftu'
description: Zjistěte, jak spustit úlohu Spark v Azure Databricks pomocí portálu Azure a účet úložiště Azure Data Lake Storage Gen2.
services: storage
author: normesta
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: quickstart
ms.date: 02/15/2019
ms.openlocfilehash: 83d1b4737e15239532f8b751d2018d5e6f783562
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2019
ms.locfileid: "56342758"
---
# <a name="quickstart-analyze-data-in-azure-data-lake-storage-gen2-by-using-azure-databricks"></a>Rychlý start: Analýza dat v Azure Data Lake Storage Gen2 pomocí Azure Databricks

V tomto rychlém startu se dozvíte, jak se spouští úloha Apache Spark pomocí Azure Databricks provádět analýzu na datech uložených v účtu úložiště, který má Azure Data Lake Storage Gen2 povolena.

V rámci úlohy Spark analyzujete data o předplatných rozhlasových a získejte přehled o bezplatném a placeném využití na základě demografických údajů.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* Vytvoření účtu úložiště Data Lake Gen2. Zobrazit [rychlý start: Vytvoření účtu úložiště Azure Data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md)

  Vložte název účtu úložiště do textového souboru. Brzy je budete potřebovat.

*  Vytvoření instančního objektu. Zobrazit [jak: Použití portálu k vytvoření aplikace a instančního objektu, který má přístup k prostředkům Azure AD](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

   Existuje několik určité akce, které budete muset udělat při provádění kroků v tomto článku.

   :heavy_check_mark: Při provádění kroků v [přiřazení aplikace k roli](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) části tohoto článku, ujistěte se, že k přiřazení **Role Přispěvatel úložiště objektů Blob** instančnímu objektu služby v rámci Data Lake Účet úložiště Gen2 úložiště.

   :heavy_check_mark: Při provádění kroků v [získání hodnot pro přihlášení](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) část článku, vložte ID tenanta, ID aplikace a hodnoty klíče ověřování do textového souboru. Brzy ty budete potřebovat.

## <a name="create-an-azure-databricks-workspace"></a>Vytvoření pracovního prostoru Azure Databricks

V této části vytvoříte pomocí portálu Azure pracovní prostor služby Azure Databricks.

1. Na webu Azure Portal vyberte **Vytvořit prostředek** > **Analýza** > **Azure Databricks**.

    ![Databricks na webu Azure Portal](./media/data-lake-storage-quickstart-create-databricks-account/azure-databricks-on-portal.png "Databricks na webu Azure Portal")

2. V části **Služba Azure Databricks** zadejte hodnoty pro vytvoření pracovního prostoru Databricks.

    ![Vytvoření pracovního prostoru služby Azure Databricks](./media/data-lake-storage-quickstart-create-databricks-account/create-databricks-workspace.png "Vytvoření pracovního prostoru služby Azure Databricks")

    Zadejte následující hodnoty:

    |Vlastnost  |Popis  |
    |---------|---------|
    |**Název pracovního prostoru**     | Zadejte název pracovního prostoru Databricks.        |
    |**Předplatné**     | Z rozevíracího seznamu vyberte své předplatné Azure.        |
    |**Skupina prostředků**     | Určete, jestli chcete vytvořit novou skupinu prostředků, nebo použít existující. Skupina prostředků je kontejner, který obsahuje související prostředky pro řešení Azure. Další informace naleznete v tématu [Přehled skupin prostředků v Azure](../../azure-resource-manager/resource-group-overview.md). |
    |**Umístění**     | Vyberte **Západní USA 2**. Pokud chcete, můžete si vybrat jinou veřejnou oblast.        |
    |**Cenová úroveň**     |  Zvolte úroveň **Standard** nebo **Premium**. Další informace o těchto úrovních najdete na [stránce s cenami za Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

    Vyberte **Připnout na řídicí panel** a potom klikněte na **Vytvořit**.

3. Trvá si trochu času vytvořit pracovní prostor. Při vytváření pracovního prostoru **odesílá se nasazení pro Azure Databricks** dlaždici se zobrazí na pravé straně. Pokud chcete zobrazit nadpis, budete muset posunout doprava na řídicím panelu. Je také indikátor průběhu, který se zobrazí v horní části obrazovky. Průběh můžete sledovat v obou oblastech.

    ![Dlaždice nasazení Databricks](./media/data-lake-storage-quickstart-create-databricks-account/databricks-deployment-tile.png "Dlaždice nasazení Databricks")

## <a name="create-a-spark-cluster-in-databricks"></a>Vytvoření clusteru Spark ve službě Databricks

1. Na webu Azure Portal přejděte do pracovního prostoru Databricks, který jste vytvořili, a vyberte **Spustit pracovní prostor**.

2. Budete přesměrováni na portál Azure Databricks. Na portálu vyberte **Nový** > **Cluster**.

    ![Databricks v Azure](./media/data-lake-storage-quickstart-create-databricks-account/databricks-on-azure.png "Databricks v Azure")

3. Na stránce **New cluster** (Nový cluster) zadejte hodnoty pro vytvoření clusteru.

    ![Vytvoření clusteru Databricks Spark v Azure](./media/data-lake-storage-quickstart-create-databricks-account/create-databricks-spark-cluster.png "Vytvoření clusteru Databricks Spark v Azure")

    Přijměte všechny výchozí hodnoty kromě následujících:

    * Zadejte název clusteru.
    * Vytvořte cluster pomocí **5.1** modulu runtime.
    * Nezapomeňte zaškrtnout políčko **Terminate after 120 minutes of inactivity** (Ukončit po 120 minutách nečinnosti). Zadejte dobu (v minutách), po které se má ukončit činnost clusteru, pokud se cluster nepoužívá.

4. Vyberte **Vytvořit cluster**. Po spuštění clusteru můžete ke clusteru připojit poznámkové bloky a spouštět úlohy Spark.

Další informace o vytváření clusterů najdete v tématu [Vytvoření clusteru Spark v Azure Databricks](https://docs.azuredatabricks.net/user-guide/clusters/create.html).

## <a name="create-storage-account-file-system"></a>Vytvoření systému souborů v účtu úložiště

V této části nejprve vytvoříte v pracovním prostoru Azure Databricks poznámkový blok a pak spustíte fragmenty kódu, kterými nakonfigurujete účet úložiště.

1. Na portálu [Azure Portal](https://portal.azure.com) přejděte do vytvořeného pracovního prostoru Azure Databricks a vyberte **Spustit pracovní prostor**.

2. V levém podokně vyberte **Pracovní prostor**. V rozevíracím seznamu **Pracovní prostor** vyberte **Vytvořit** > **Poznámkový blok**.

    ![Vytvoření poznámkového bloku v Databricks](./media/data-lake-storage-quickstart-create-databricks-account/databricks-create-notebook.png "Vytvoření poznámkového bloku v Databricks")

3. V dialogovém okně **Vytvořit poznámkový blok** zadejte název poznámkového bloku. Vyberte jazyk **Scala** a vyberte cluster Spark, který jste vytvořili v předchozí části.

    ![Vytvoření poznámkového bloku v Databricks](./media/data-lake-storage-quickstart-create-databricks-account/databricks-notebook-details.png "Vytvoření poznámkového bloku v Databricks")

    Vyberte **Vytvořit**.

4. Zkopírujte a vložte následující blok kódu do první buňky, ale není ještě tento kód spustit.

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

    > [!NOTE]
    > Tento blok kódu přímo má přístup k Data Lake Gen2 koncový bod pomocí OAuth, ale existují jiné způsoby připojení k vašemu účtu Data Lake Storage Gen2 pracovního prostoru Databricks. Můžete třeba připojit systém souborů pomocí OAuth nebo přímý přístup pomocí sdíleného klíče. <br>Mezi příklady těchto přístupů najdete v tématu [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html) článku na webu Azure Databricks.

5. V tomto bloku kódu, nahraďte `storage-account-name`, `application-id`, `authentication-id`, a `tenant-id` zástupné hodnoty hodnotami, které jste shromáždili při vytváření instančního objektu v tomto bloku kódu. Nastavte `file-system-name` hodnotu zástupného symbolu cokoli, co můžete pojmenovat chcete umožnit systému souborů.

6. Stisknutím klávesy **SHIFT + ENTER** klíče pro spuštění kódu v tomto bloku.

## <a name="ingest-sample-data"></a>Ingestace ukázkových dat

Než se pustíte do této části, je potřeba nejprve splnit následující požadavky:

Do buňky poznámkového bloku zadejte následující kód:

    %sh wget -P /tmp https://raw.githubusercontent.com/Azure/usql/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json

V buňce, stiskněte klávesu **SHIFT + ENTER** spuštění kódu.

Teď do nové buňky pod tohohle, zadejte následující kód a nahraďte hodnoty, které se zobrazují v závorkách se stejnými hodnotami, které jste použili dříve:

    dbutils.fs.cp("file:///tmp/small_radio_json.json", "abfss://<file-system>@<account-name>.dfs.core.windows.net/")

V buňce, stiskněte klávesu **SHIFT + ENTER** spuštění kódu.

## <a name="run-a-spark-sql-job"></a>Spuštění úlohy Spark SQL

Ke spuštění úlohy Spark SQL na datech použijte následující postup.

1. Spuštěním příkazu SQL vytvořte dočasnou tabulku pomocí dat z ukázkového datového souboru JSON **small_radio_json.json**. V následujícím fragmentu kódu nahraďte zástupné hodnoty názvy vašeho systému souborů a účtu úložiště. Vytvořený poznámkový blok použijte k vložení fragmentu do nové buňky kódu v poznámkovém bloku a stiskněte SHIFT+ENTER.

    ```sql
    %sql
    DROP TABLE IF EXISTS radio_sample_data;
    CREATE TABLE radio_sample_data
    USING json
    OPTIONS (
     path  "abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/<PATH>/small_radio_json.json"
    )
    ```

    Po úspěšném dokončení příkazu získáte všechna data ze souboru JSON ve formátu tabulky v clusteru Databricks.

    Magický příkaz jazyka `%sql` umožňuje spustit z poznámkového bloku kód SQL, i když je poznámkový blok jiného typu. Další informace najdete v článku [Kombinování jazyků v poznámkovém bloku](https://docs.azuredatabricks.net/user-guide/notebooks/index.html#mixing-languages-in-a-notebook).

2. Podívejme se na snímek ukázkových dat JSON, abyste lépe pochopili dotaz, který spouštíte. Vložte do buňky kódu následující fragment kódu a stiskněte klávesy **SHIFT + ENTER**.

    ```sql
    %sql
    SELECT * from radio_sample_data
    ```

3. Zobrazí se tabulkový výstup jako na následujícím snímku obrazovky (zobrazí se jenom některé sloupce):

    ![Ukázková data JSON](./media/data-lake-storage-quickstart-create-databricks-account/databricks-sample-csv-data.png "Ukázková data JSON")

    Vedle dalších podrobných informací ukázková data zachycují pohlaví posluchačů určité rozhlasové (název sloupce **pohlaví**) a zda je jejich předplatného bezplatné i placené (název sloupce **úroveň**).

4. Teď vytvoříte vizuální reprezentaci těchto dat, která bude znázorňovat, kolik uživatelů obou pohlaví má bezplatné účty a kolik je platících předplatitelů. Ve spodní části tabulkového výstupu klikněte na ikonu **Bar chart** (Pruhový graf) ikonu a potom na **Plot Options** (Možnosti grafu).

    ![Vytvoření pruhového grafu](./media/data-lake-storage-quickstart-create-databricks-account/create-plots-databricks-notebook.png "Vytvoření pruhového grafu")

5. V části **Customize Plot** (Přizpůsobit graf) přetáhněte hodnoty, jak ukazuje snímek obrazovky.

    ![Přizpůsobení pruhového grafu](./media/data-lake-storage-quickstart-create-databricks-account/databricks-notebook-customize-plot.png "Přizpůsobení pruhového grafu")

    - V poli **Keys** (Klíče) nastavte hodnotu **gender** (Pohlaví).
    - V poli **Seskupení sérií** nastavte hodnotu **level** (Úroveň).
    - V poli **Values** (Hodnoty) nastavte hodnotu **level** (Úroveň).
    - V poli **Aggregation** (Agregace) vyberte možnost **COUNT** (Počet).

6. Klikněte na tlačítko **Použít**.

7. Výstup bude obsahovat vizuální reprezentaci znázorněnou na následujícím snímku obrazovky:

     ![Přizpůsobení pruhového grafu](./media/data-lake-storage-quickstart-create-databricks-account/databricks-sql-query-output-bar-chart.png "Přizpůsobení pruhového grafu")

## <a name="clean-up-resources"></a>Vyčištění prostředků

Jakmile budete hotovi s tímto článkem, můžete cluster ukončit. V pracovním prostoru Azure Databricks vyberte **Clusters** (Clustery) a najděte cluster, který chcete ukončit. Přesuňte kurzor na tři tečky pod sloupcem **Actions** (Akce) a vyberte ikonu **Terminate** (Ukončit).

![Zastavení clusteru Databricks](./media/data-lake-storage-quickstart-create-databricks-account/terminate-databricks-cluster.png "Zastavení clusteru Databricks")

Pokud neukončíte ručně cluster automaticky zastaví, pokud jste vybrali **po provedení \_ \_ počet minut nečinnosti** zaškrtávací políčko při vytváření clusteru. Pokud jste tuto možnost nastavili, cluster se po stanovené době nečinnosti zastaví.

## <a name="next-steps"></a>Další postup

V tomto článku jste v Azure Databricks vytvořili cluster Spark a pak jste ke spuštění úlohy Spark použili data v účtu úložiště s povolenou službou Data Lake Storage Gen2. Můžete si také projít článek [Zdroje dat Spark](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html) a zjistit, jak do Azure Databricks importovat data z jiných zdrojů dat. V dalším článku se dozvíte, jak pomocí Azure Databricks provést operaci ETL (extrakce, transformace a načítání dat).

> [!div class="nextstepaction"]
>[Extrakce, transformace a načítání dat pomocí Azure Databricks](../../azure-databricks/databricks-extract-load-sql-data-warehouse.md)
