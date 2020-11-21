---
title: 'Rychlý Start: Analýza dat v Azure Data Lake Storage Gen2 pomocí Azure Databricks | Microsoft Docs'
description: Naučte se spouštět úlohu Sparku na Azure Databricks pomocí Azure Portal a účtu úložiště Azure Data Lake Storage Gen2.
author: normesta
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: quickstart
ms.date: 06/12/2020
ms.reviewer: jeking
ms.openlocfilehash: 5bdf1cd510ed0d997e5e59c6492117a4d567c43e
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024598"
---
# <a name="quickstart-analyze-data-with-databricks"></a>Rychlý Start: Analýza dat pomocí datacihlů

V tomto rychlém startu spustíte úlohu Apache Spark pomocí Azure Databricks k provádění analýz dat uložených v účtu úložiště. V rámci úlohy Spark budete analyzovat data předplatného rádiového kanálu, abyste získali přehled o bezplatném nebo placeném využití na základě demografických údajů.

## <a name="prerequisites"></a>Požadavky

* Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* Účet úložiště, který má povolenou funkci hierarchického oboru názvů. Pokud ho chcete vytvořit, přečtěte si téma [Vytvoření účtu úložiště pro použití s Azure Data Lake Storage Gen2](create-data-lake-storage-account.md).

* ID tenanta, ID aplikace a heslo instančního objektu Azure s přiřazenou rolí **přispěvatele dat objektu BLOB služby Storage**. [Vytvoření instančního objektu](../../active-directory/develop/howto-create-service-principal-portal.md).

  > [!IMPORTANT]
  > Přiřaďte roli v oboru účtu úložiště Data Lake Storage Gen2. K nadřazené skupině prostředků nebo předplatnému můžete přiřadit roli, ale chyby související s oprávněními obdržíte, dokud tato přiřazení role nerozšíříte do účtu úložiště.

## <a name="create-an-azure-databricks-workspace"></a>Vytvoření pracovního prostoru Azure Databricks

V této části vytvoříte pomocí portálu Azure pracovní prostor služby Azure Databricks.

1. V Azure Portal vyberte vytvořit Azure Databricks **prostředků**  >  **Analytics**  >  **Azure Databricks**.

    ![Datacihly na Azure Portal](./media/data-lake-storage-quickstart-create-databricks-account/azure-databricks-on-portal.png "Datacihly na Azure Portal")

2. V části **Služba Azure Databricks** zadejte hodnoty pro vytvoření pracovního prostoru Databricks.

    ![Vytvoření pracovního prostoru Azure Databricks](./media/data-lake-storage-quickstart-create-databricks-account/create-databricks-workspace.png "Vytvoření pracovního prostoru Azure Databricks")

    Zadejte následující hodnoty:

    |Vlastnost  |Popis  |
    |---------|---------|
    |**Název pracovního prostoru**     | Zadejte název pracovního prostoru Databricks.        |
    |**Předplatné**     | Z rozevíracího seznamu vyberte své předplatné Azure.        |
    |**Skupina prostředků**     | Určete, jestli chcete vytvořit novou skupinu prostředků, nebo použít existující. Skupina prostředků je kontejner, který uchovává související prostředky pro řešení Azure. Další informace naleznete v tématu [Přehled skupin prostředků v Azure](../../azure-resource-manager/management/overview.md). |
    |**Umístění**     | Vyberte **USA – západ 2**. Pokud chcete, můžete si vybrat jinou veřejnou oblast.        |
    |**Cenová úroveň**     |  Zvolte úroveň **Standard** nebo **Premium**. Další informace o těchto úrovních najdete na [stránce s cenami za Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

3. Vytvoření účtu trvá několik minut. Chcete-li monitorovat stav operace, zobrazte indikátor průběhu v horní části.

4. Vyberte **Připnout na řídicí panel** a potom vyberte **Vytvořit**.

## <a name="create-a-spark-cluster-in-databricks"></a>Vytvoření clusteru Spark ve službě Databricks

1. Na webu Azure Portal přejděte do pracovního prostoru Databricks, který jste vytvořili, a vyberte **Spustit pracovní prostor**.

2. Budete přesměrováni na portál Azure Databricks. Na portálu vyberte **Nový**  >  **cluster**.

    ![Datacihly v Azure](./media/data-lake-storage-quickstart-create-databricks-account/databricks-on-azure.png "Datacihly v Azure")

3. Na stránce **New cluster** (Nový cluster) zadejte hodnoty pro vytvoření clusteru.

    ![Vytvoření clusteru datacihly Spark v Azure](./media/data-lake-storage-quickstart-create-databricks-account/create-databricks-spark-cluster.png "Vytvoření clusteru datacihly Spark v Azure")

    Zadejte hodnoty následujících polí a potvrďte výchozí hodnoty dalších polí:

    - Zadejte název clusteru.
     
    - Nezapomeňte zaškrtnout políčko **Terminate after 120 minutes of inactivity** (Ukončit po 120 minutách nečinnosti). Zadejte dobu (v minutách), po které se má ukončit činnost clusteru, pokud se cluster nepoužívá.

4. Vyberte **vytvořit cluster**. Po spuštění clusteru můžete ke clusteru připojit poznámkové bloky a spouštět úlohy Spark.

Další informace o vytváření clusterů najdete v tématu [Vytvoření clusteru Spark v Azure Databricks](https://docs.azuredatabricks.net/user-guide/clusters/create.html).

## <a name="create-notebook"></a>Vytvořit Poznámkový blok

V této části nejprve vytvoříte v pracovním prostoru Azure Databricks poznámkový blok a pak spustíte fragmenty kódu, kterými nakonfigurujete účet úložiště.

1. Na portálu [Azure Portal](https://portal.azure.com) přejděte do vytvořeného pracovního prostoru Azure Databricks a vyberte **Spustit pracovní prostor**.

2. V levém podokně vyberte **Pracovní prostor**. V rozevíracím seznamu **Pracovní prostor** vyberte **Vytvořit** > **Poznámkový blok**.

    ![Snímek obrazovky, který ukazuje, jak vytvořit Poznámkový blok v datacihlech a zvýraznit možnost nabídky vytvořit > Poznámkový blok.](./media/data-lake-storage-quickstart-create-databricks-account/databricks-create-notebook.png "Vytvoření poznámkového bloku v datacihlech")

3. V dialogovém okně **Vytvořit poznámkový blok** zadejte název poznámkového bloku. Vyberte jazyk **Scala** a vyberte cluster Spark, který jste vytvořili v předchozí části.

    ![Vytvoření poznámkového bloku v datacihlech](./media/data-lake-storage-quickstart-create-databricks-account/databricks-notebook-details.png "Vytvoření poznámkového bloku v datacihlech")

    Vyberte **Vytvořit**.

4. Zkopírujte následující blok kódu a vložte ho do první buňky, ale tento kód ještě nespouštějte.

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
5. V tomto bloku kódu nahraďte `storage-account-name` `appID` `password` `tenant-id` zástupné hodnoty,, a v tomto bloku kódu hodnotami, které jste shromáždili při vytváření instančního objektu. Nastavte `container-name` hodnotu zástupný symbol na libovolný název, který chcete kontejneru přidělit.

6. Stiskněte klávesy **SHIFT + ENTER** a spusťte kód v tomto bloku.

## <a name="ingest-sample-data"></a>Ingestace ukázkových dat

Než se pustíte do této části, je potřeba nejprve splnit následující požadavky:

Do buňky poznámkového bloku zadejte následující kód:

```bash
%sh wget -P /tmp https://raw.githubusercontent.com/Azure/usql/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json
```

V buňce stiskněte **SHIFT + ENTER** a kód se spustí.

Nyní vložte následující kód do nové buňky pod tímto kódem a nahraďte hodnoty zobrazené v závorkách stejnými hodnotami, které jste použili dříve:

```python
dbutils.fs.cp("file:///tmp/small_radio_json.json", "abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/")
```

V buňce stiskněte **SHIFT + ENTER** a kód se spustí.

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

    ![Ukázková data JSON](./media/data-lake-storage-quickstart-create-databricks-account/databricks-sample-csv-data.png "Ukázková data JSON")

    Kromě dalších podrobností vzorová data zachycují pohlaví posluchačů rádiového kanálu (název sloupce, **pohlaví**) a zda je jejich předplatné bezplatné nebo placené (název sloupce, **úroveň**).

4. Teď vytvoříte vizuální reprezentaci těchto dat, která bude znázorňovat, kolik uživatelů obou pohlaví má bezplatné účty a kolik je platících předplatitelů. Ve spodní části tabulkového výstupu klikněte na ikonu **Bar chart** (Pruhový graf) ikonu a potom na **Plot Options** (Možnosti grafu).

    ![Vytvořit pruhový graf](./media/data-lake-storage-quickstart-create-databricks-account/create-plots-databricks-notebook.png "Vytvořit pruhový graf")

5. V části **Customize Plot** (Přizpůsobit graf) přetáhněte hodnoty, jak ukazuje snímek obrazovky.

    ![Snímek obrazovky, na kterém se zobrazuje obrazovka pro přizpůsobení a hodnoty, které můžete přetáhnout](./media/data-lake-storage-quickstart-create-databricks-account/databricks-notebook-customize-plot.png "Přizpůsobení pruhového grafu")

    - V poli **Keys** (Klíče) nastavte hodnotu **gender** (Pohlaví).
    - V poli **Seskupení sérií** nastavte hodnotu **level** (Úroveň).
    - V poli **Values** (Hodnoty) nastavte hodnotu **level** (Úroveň).
    - V poli **Aggregation** (Agregace) vyberte možnost **COUNT** (Počet).

6. Klikněte na **Použít**.

7. Výstup bude obsahovat vizuální reprezentaci znázorněnou na následujícím snímku obrazovky:

     ![Přizpůsobení pruhového grafu](./media/data-lake-storage-quickstart-create-databricks-account/databricks-sql-query-output-bar-chart.png "Přizpůsobení pruhového grafu")

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete s tímto článkem hotoví, můžete cluster ukončit. V pracovním prostoru Azure Databricks vyberte **Clusters** (Clustery) a najděte cluster, který chcete ukončit. Přesuňte kurzor na tři tečky pod sloupcem **Actions** (Akce) a vyberte ikonu **Terminate** (Ukončit).

![Zastavení clusteru datacihly](./media/data-lake-storage-quickstart-create-databricks-account/terminate-databricks-cluster.png "Zastavení clusteru datacihly")

Pokud neukončíte ručně cluster, který se automaticky zastaví, za předpokladu, že jste při vytváření clusteru zaškrtli políčko **ukončit po \_ \_ minutách nečinnosti** . Pokud jste tuto možnost nastavili, cluster se po stanovené době nečinnosti zastaví.

## <a name="next-steps"></a>Další kroky

V tomto článku jste v Azure Databricks vytvořili cluster Spark a pak jste ke spuštění úlohy Spark použili data v účtu úložiště s povolenou službou Data Lake Storage Gen2.

V dalším článku se dozvíte, jak pomocí Azure Databricks provést operaci ETL (extrakce, transformace a načítání dat).

> [!div class="nextstepaction"]
>[Extrakce, transformace a načtení dat pomocí Azure Databricks](../../azure-databricks/databricks-extract-load-sql-data-warehouse.md).

- Informace o tom, jak importovat data z jiných zdrojů dat do Azure Databricks, najdete v tématu [zdroje dat Spark](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html).

- Další informace o dalších způsobech přístupu k Azure Data Lake Storage Gen2 z pracovního prostoru Azure Databricks najdete v tématu [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html).
