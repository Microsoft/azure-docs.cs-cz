---
title: 'Úvodní příručka: Analýza dat v Azure Data Lake Storage Gen2 pomocí Azure Databricks | Dokumenty společnosti Microsoft'
description: Naučte se spouštět úlohu Spark na Azure Databricks pomocí portálu Azure a účtu úložiště Azure Data Lake Storage Gen2.
author: normesta
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: quickstart
ms.date: 02/17/2020
ms.reviewer: jeking
ms.openlocfilehash: 346795b79a78589d949b035a803a67a9e5a2e8e5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77470730"
---
# <a name="quickstart-analyze-data-with-databricks"></a>Úvodní příručka: Analýza dat pomocí datových cihel

V tomto rychlém startu spustíte úlohu Apache Spark pomocí Azure Databricks k provádění analýz na datech uložených v účtu úložiště. V rámci úlohy Spark budete analyzovat data předplatného rádiových kanálů, abyste získali přehled o bezplatném/placeném využití na základě demografických údajů.

## <a name="prerequisites"></a>Požadavky

* Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* Název vašeho účtu úložiště Azure Data Lake Gen2. [Vytvořte účet úložiště Azure Data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md).

* ID klienta, ID aplikace a heslo instančního objektu Azure s přiřazenou rolí **přispěvatele dat objektů blob úložiště**. [Vytvořte instanční objekt](../../active-directory/develop/howto-create-service-principal-portal.md).

  > [!IMPORTANT]
  > Přiřaďte roli v oboru účtu úložiště Storage Storage Data Lake Storage. Roli můžete přiřadit nadřazené skupině prostředků nebo předplatnému, ale budete dostávat chyby související s oprávněními, dokud se tato přiřazení rolí nerozšíří do účtu úložiště.

## <a name="create-an-azure-databricks-workspace"></a>Vytvoření pracovního prostoru Azure Databricks

V této části vytvoříte pomocí portálu Azure pracovní prostor služby Azure Databricks.

1. Na webu Azure Portal vyberte **Vytvořit zdroj** > **Analytics** > **Azure Databricks**.

    ![Datové cihly na webu Azure Portal](./media/data-lake-storage-quickstart-create-databricks-account/azure-databricks-on-portal.png "Datové cihly na webu Azure Portal")

2. V části **Služba Azure Databricks** zadejte hodnoty pro vytvoření pracovního prostoru Databricks.

    ![Vytvoření pracovního prostoru Azure Databricks](./media/data-lake-storage-quickstart-create-databricks-account/create-databricks-workspace.png "Vytvoření pracovního prostoru Azure Databricks")

    Zadejte následující hodnoty:

    |Vlastnost  |Popis  |
    |---------|---------|
    |**Název pracovního prostoru**     | Zadejte název pracovního prostoru Databricks.        |
    |**Předplatné**     | Z rozevíracího seznamu vyberte své předplatné Azure.        |
    |**Skupina prostředků**     | Určete, jestli chcete vytvořit novou skupinu prostředků, nebo použít existující. Skupina prostředků je kontejner, který obsahuje související prostředky pro řešení Azure. Další informace naleznete v tématu [Přehled skupin prostředků v Azure](../../azure-resource-manager/management/overview.md). |
    |**Umístění**     | Vyberte **USA – západ 2**. Pokud chcete, můžete si vybrat jinou veřejnou oblast.        |
    |**Cenová úroveň**     |  Zvolte úroveň **Standard** nebo **Premium**. Další informace o těchto úrovních najdete na [stránce s cenami za Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

3. Vytvoření účtu trvá několik minut. Chcete-li sledovat stav operace, zobrazte indikátor průběhu nahoře.

4. Vyberte **Připnout na řídicí panel** a potom vyberte **Vytvořit**.

## <a name="create-a-spark-cluster-in-databricks"></a>Vytvoření clusteru Spark ve službě Databricks

1. Na webu Azure Portal přejděte do pracovního prostoru Databricks, který jste vytvořili, a vyberte **Spustit pracovní prostor**.

2. Budete přesměrováni na portál Azure Databricks. Na portálu vyberte **Nový** > **cluster**.

    ![Datové cihly v Azure](./media/data-lake-storage-quickstart-create-databricks-account/databricks-on-azure.png "Datové cihly v Azure")

3. Na stránce **New cluster** (Nový cluster) zadejte hodnoty pro vytvoření clusteru.

    ![Vytvoření clusteru Databricks Spark v Azure](./media/data-lake-storage-quickstart-create-databricks-account/create-databricks-spark-cluster.png "Vytvoření clusteru Databricks Spark v Azure")

    Zadejte hodnoty následujících polí a potvrďte výchozí hodnoty dalších polí:

    - Zadejte název clusteru.
     
    - Nezapomeňte zaškrtnout políčko **Terminate after 120 minutes of inactivity** (Ukončit po 120 minutách nečinnosti). Zadejte dobu (v minutách), po které se má ukončit činnost clusteru, pokud se cluster nepoužívá.

4. Vyberte **Vytvořit cluster**. Po spuštění clusteru můžete ke clusteru připojit poznámkové bloky a spouštět úlohy Spark.

Další informace o vytváření clusterů najdete v tématu [Vytvoření clusteru Spark v Azure Databricks](https://docs.azuredatabricks.net/user-guide/clusters/create.html).

## <a name="create-storage-account-container"></a>Vytvoření kontejneru účtu úložiště

V této části nejprve vytvoříte v pracovním prostoru Azure Databricks poznámkový blok a pak spustíte fragmenty kódu, kterými nakonfigurujete účet úložiště.

1. Na portálu [Azure Portal](https://portal.azure.com) přejděte do vytvořeného pracovního prostoru Azure Databricks a vyberte **Spustit pracovní prostor**.

2. V levém podokně vyberte **Pracovní prostor**. V rozevíracím seznamu **Pracovní prostor** vyberte **Vytvořit** > **Poznámkový blok**.

    ![Vytvoření poznámkového bloku v datových cihlách](./media/data-lake-storage-quickstart-create-databricks-account/databricks-create-notebook.png "Vytvoření poznámkového bloku v datových cihlách")

3. V dialogovém okně **Vytvořit poznámkový blok** zadejte název poznámkového bloku. Vyberte jazyk **Scala** a vyberte cluster Spark, který jste vytvořili v předchozí části.

    ![Vytvoření poznámkového bloku v datových cihlách](./media/data-lake-storage-quickstart-create-databricks-account/databricks-notebook-details.png "Vytvoření poznámkového bloku v datových cihlách")

    Vyberte **Vytvořit**.

4. Zkopírujte a vložte následující blok kódu do první buňky, ale tento kód ještě nespouštějte.

   ```scala
   spark.conf.set("fs.azure.account.auth.type.<storage-account-name>.dfs.core.windows.net", "OAuth")
   spark.conf.set("fs.azure.account.oauth.provider.type.<storage-account-name>.dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
   spark.conf.set("fs.azure.account.oauth2.client.id.<storage-account-name>.dfs.core.windows.net", "<appID>")
   spark.conf.set("fs.azure.account.oauth2.client.secret.<storage-account-name>.dfs.core.windows.net", "<password>")
   spark.conf.set("fs.azure.account.oauth2.client.endpoint.<storage-account-name>.dfs.core.windows.net", "https://login.microsoftonline.com/<tenant-id>/oauth2/token")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "true")
   dbutils.fs.ls("abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "false")

   ```
5. V tomto bloku kódu `storage-account-name` `appID`nahraďte hodnoty , , `password`a `tenant-id` zástupný symbol v tomto bloku kódu hodnotami, které jste shromáždili při vytváření instančního objektu. `container-name` Nastavte zástupnou hodnotu na jakýkoli název, který chcete dát kontejneru.

6. Stisknutím kláves **SHIFT + ENTER** spusťte kód v tomto bloku.

## <a name="ingest-sample-data"></a>Ingestace ukázkových dat

Než se pustíte do této části, je potřeba nejprve splnit následující požadavky:

Do buňky poznámkového bloku zadejte následující kód:

    %sh wget -P /tmp https://raw.githubusercontent.com/Azure/usql/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json

V buňce spusťte kód stisknutím **kláves SHIFT + ENTER.**

Nyní v nové buňce pod touto buňkou zadejte následující kód a nahraďte hodnoty, které se zobrazují v závorkách, stejnými hodnotami, které jste použili dříve:

    dbutils.fs.cp("file:///tmp/small_radio_json.json", "abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/")

V buňce spusťte kód stisknutím **kláves SHIFT + ENTER.**

## <a name="run-a-spark-sql-job"></a>Spuštění úlohy Spark SQL

Ke spuštění úlohy Spark SQL na datech použijte následující postup.

1. Spuštěním příkazu SQL vytvořte dočasnou tabulku pomocí dat z ukázkového datového souboru JSON **small_radio_json.json**. V následujícím fragmentu kódu nahraďte zástupné hodnoty názvem vašeho kontejneru a názvem účtu úložiště. Vytvořený poznámkový blok použijte k vložení fragmentu do nové buňky kódu v poznámkovém bloku a stiskněte SHIFT+ENTER.

    ```sql
    %sql
    DROP TABLE IF EXISTS radio_sample_data;
    CREATE TABLE radio_sample_data
    USING json
    OPTIONS (
     path  "abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/small_radio_json.json"
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

    ![Ukázka dat JSON](./media/data-lake-storage-quickstart-create-databricks-account/databricks-sample-csv-data.png "Ukázka dat JSON")

    Ukázková data mimo jiné zachycují pohlaví publika rozhlasového kanálu (název sloupce, **pohlaví)** a to, zda je jejich předplatné bezplatné nebo placené (název sloupce, **úroveň).**

4. Teď vytvoříte vizuální reprezentaci těchto dat, která bude znázorňovat, kolik uživatelů obou pohlaví má bezplatné účty a kolik je platících předplatitelů. Ve spodní části tabulkového výstupu klikněte na ikonu **Bar chart** (Pruhový graf) ikonu a potom na **Plot Options** (Možnosti grafu).

    ![Vytvoření pruhového grafu](./media/data-lake-storage-quickstart-create-databricks-account/create-plots-databricks-notebook.png "Vytvoření pruhového grafu")

5. V části **Customize Plot** (Přizpůsobit graf) přetáhněte hodnoty, jak ukazuje snímek obrazovky.

    ![Přizpůsobení pruhového grafu](./media/data-lake-storage-quickstart-create-databricks-account/databricks-notebook-customize-plot.png "Přizpůsobení pruhového grafu")

    - V poli **Keys** (Klíče) nastavte hodnotu **gender** (Pohlaví).
    - V poli **Seskupení sérií** nastavte hodnotu **level** (Úroveň).
    - V poli **Values** (Hodnoty) nastavte hodnotu **level** (Úroveň).
    - V poli **Aggregation** (Agregace) vyberte možnost **COUNT** (Počet).

6. Klikněte na **Použít**.

7. Výstup bude obsahovat vizuální reprezentaci znázorněnou na následujícím snímku obrazovky:

     ![Přizpůsobení pruhového grafu](./media/data-lake-storage-quickstart-create-databricks-account/databricks-sql-query-output-bar-chart.png "Přizpůsobení pruhového grafu")

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení tohoto článku můžete cluster ukončit. V pracovním prostoru Azure Databricks vyberte **Clusters** (Clustery) a najděte cluster, který chcete ukončit. Přesuňte kurzor na tři tečky pod sloupcem **Actions** (Akce) a vyberte ikonu **Terminate** (Ukončit).

![Zastavení clusteru Databricks](./media/data-lake-storage-quickstart-create-databricks-account/terminate-databricks-cluster.png "Zastavení clusteru Databricks")

Pokud cluster ručně neukončíte, automaticky se zastaví za předpokladu, že jste při vytváření clusteru zaškrtli políčko **Ukončit po \_ \_ minutách nečinnosti.** Pokud jste tuto možnost nastavili, cluster se po stanovené době nečinnosti zastaví.

## <a name="next-steps"></a>Další kroky

V tomto článku jste v Azure Databricks vytvořili cluster Spark a pak jste ke spuštění úlohy Spark použili data v účtu úložiště s povolenou službou Data Lake Storage Gen2.

V dalším článku se dozvíte, jak pomocí Azure Databricks provést operaci ETL (extrakce, transformace a načítání dat).

> [!div class="nextstepaction"]
>[Extrahujte, transformujte a načítajíte data pomocí Azure Databricks](../../azure-databricks/databricks-extract-load-sql-data-warehouse.md).

- Informace o tom, jak importovat data z jiných zdrojů dat do Azure Databricks, najdete v [tématu Zdroje dat Spark](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html).

- Další informace o dalších způsobech přístupu k Azure Data Lake Storage Gen2 z pracovního prostoru Azure Databricks najdete v tématu [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html).
