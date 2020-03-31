---
title: Úvodní příručka – spuštění úlohy Spark na Azure Databricks pomocí portálu Azure
description: Tento rychlý start ukazuje, jak pomocí portálu Azure vytvořit pracovní prostor Azure Databricks, cluster Apache Spark a spustit úlohu Spark.
services: azure-databricks
ms.service: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.date: 03/23/2020
ms.custom: mvc
ms.openlocfilehash: d6af521238a034bc22612335119f08284b87eb4b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80132708"
---
# <a name="quickstart-run-a-spark-job-on-azure-databricks-using-the-azure-portal"></a>Rychlý start: Spuštění úlohy Spark job v Azure Databricks pomocí portálu Azure

V tomto rychlém startu použijete portál Azure k vytvoření pracovního prostoru Azure Databricks s clusterem Apache Spark. Spustíte úlohu v clusteru a pomocí vlastních grafů vytvoříte sestavy v reálném čase z bostonských bezpečnostních dat.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/)

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [portálu Azure](https://portal.azure.com).

> [!Note]
> Tento kurz nelze provést pomocí **bezplatného zkušebního předplatného Azure**.
> Pokud máte bezplatný účet, přejděte na svůj profil a změňte předplatné na **průběžně placené**. Další informace najdete na stránce [bezplatného účtu Azure](https://azure.microsoft.com/free/). Potom [odeberte limit útraty](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)a [požádejte o zvýšení kvóty](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) pro virtuální procesory ve vaší oblasti. Když vytvoříte pracovní prostor Azure Databricks, můžete vybrat **zkušební (premium - 14denní jednotku DBU)** a poskytnout tak pracovnímu prostoru přístup k bezplatným dbům Azure Databricks Azure na 14 dní.

## <a name="create-an-azure-databricks-workspace"></a>Vytvoření pracovního prostoru Azure Databricks

V této části vytvoříte pomocí portálu Azure pracovní prostor služby Azure Databricks.

1. Na webu Azure Portal vyberte **Vytvořit zdroj** > **Analytics** > **Azure Databricks**.

    ![Datové cihly na webu Azure Portal](./media/quickstart-create-databricks-workspace-portal/azure-databricks-on-portal.png "Datové cihly na webu Azure Portal")

2. V části **Služba Azure Databricks** zadejte hodnoty pro vytvoření pracovního prostoru Databricks.

    ![Vytvoření pracovního prostoru Azure Databricks](./media/quickstart-create-databricks-workspace-portal/create-databricks-workspace.png "Vytvoření pracovního prostoru Azure Databricks")

    Zadejte následující hodnoty:
    
    |Vlastnost  |Popis  |
    |---------|---------|
    |**Název pracovního prostoru**     | Zadejte název pracovního prostoru Databricks.        |
    |**Předplatné**     | Z rozevíracího seznamu vyberte své předplatné Azure.        |
    |**Skupina prostředků**     | Určete, jestli chcete vytvořit novou skupinu prostředků, nebo použít existující. Skupina prostředků je kontejner, který obsahuje související prostředky pro řešení Azure. Další informace naleznete v tématu [Přehled skupin prostředků v Azure](../azure-resource-manager/management/overview.md). |
    |**Umístění**     | Vyberte **USA – západ 2**. Další dostupné oblasti najdete v tématu [Dostupné služby Azure podle oblastí](https://azure.microsoft.com/regions/services/).        |
    |**Cenová úroveň**     |  Vyberte si mezi **standardními**, **prémiovými**nebo **zkušebními verzemi**. Další informace o těchto úrovních najdete na [stránce s cenami za Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

3. Vyberte **Zkontrolovat + Vytvořit**a potom **Vytvořit**. Vytvoření pracovního prostoru trvá několik minut. Během vytváření pracovního prostoru můžete zobrazit stav nasazení v **oznámení .** Po dokončení tohoto procesu se váš uživatelský účet automaticky přidá jako správce v pracovním prostoru.

    ![Dlaždice nasazení Datových cihel](./media/quickstart-create-databricks-workspace-portal/databricks-deployment-tile.png "Dlaždice nasazení Datových cihel")

    Pokud se nasazení pracovního prostoru nezdaří, pracovní prostor je stále vytvořen ve stavu selhání. Odstraňte neúspěšný pracovní prostor a vytvořte nový pracovní prostor, který vyřeší chyby nasazení. Při odstranění nezdařeného pracovního prostoru se odstraní také skupina spravovaných prostředků a všechny úspěšně nasazené prostředky.

## <a name="create-a-spark-cluster-in-databricks"></a>Vytvoření clusteru Spark ve službě Databricks

> [!NOTE]
> Pokud chcete k vytvoření clusteru Azure Databricks použít bezplatný účet, přejděte na svůj profil a změňte své předplatné na **Průběžné platby**. Další informace najdete na stránce [bezplatného účtu Azure](https://azure.microsoft.com/free/).

1. Na webu Azure Portal přejděte do pracovního prostoru Databricks, který jste vytvořili, a klikněte na **Spustit pracovní prostor**.

2. Budete přesměrováni na portál Azure Databricks. Na portálu klikněte na **Nový cluster**.

    ![Datové cihly v Azure](./media/quickstart-create-databricks-workspace-portal/databricks-on-azure.png "Datové cihly v Azure")

3. Na stránce **New cluster** (Nový cluster) zadejte hodnoty pro vytvoření clusteru.

    ![Vytvoření clusteru Databricks Spark v Azure](./media/quickstart-create-databricks-workspace-portal/create-databricks-spark-cluster.png "Vytvoření clusteru Databricks Spark v Azure")

    Přijměte všechny výchozí hodnoty kromě následujících:

   * Zadejte název clusteru.
   * Pro tento článek vytvořte cluster s **5.3** runtime.
   * Ujistěte se, že jste zaškrtli zaškrtávací políčko **Ukončit po \_ \_ minutách nečinnosti.** Zadejte dobu (v minutách), po které se má ukončit činnost clusteru, pokud se cluster nepoužívá.
    
     Vyberte **Vytvořit cluster**. Po spuštění clusteru můžete ke clusteru připojit poznámkové bloky a spouštět úlohy Spark.

Další informace o vytváření clusterů najdete v tématu [Vytvoření clusteru Spark v Azure Databricks](/azure/databricks/clusters/create).

## <a name="run-a-spark-sql-job"></a>Spuštění úlohy Spark SQL

Proveďte následující úkoly, abyste vytvořili poznámkový blok v Databricks, nakonfigurujte poznámkový blok tak, aby četl data z datových sad Azure Open, a pak spusťte úlohu Spark SQL na datech.

1. V levém podokně vyberte **Azure Databricks**. V seznamu **Běžné úkoly**vyberte **Nový poznámkový blok**.

    ![Vytvoření poznámkového bloku v datových cihlách](./media/quickstart-create-databricks-workspace-portal/databricks-create-notebook.png "Vytvoření poznámkového bloku v datových cihlách")

2. V dialogovém okně **Vytvořit poznámkový blok** zadejte název, vyberte **Python** jako jazyk a vyberte cluster Spark, který jste vytvořili dříve.

    ![Vytvoření poznámkového bloku v datových cihlách](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-details.png "Vytvoření poznámkového bloku v datových cihlách")

    Vyberte **Vytvořit**.

3. V tomto kroku vytvořte datový rámec Spark s bostonským bezpečnostními daty z [otevřených datových sad Azure](https://azure.microsoft.com/services/open-datasets/catalog/boston-safety-data/#AzureDatabricks)a použijte SQL k dotazování na data.

   Následující příkaz nastaví informace o přístupu k úložišti Azure. Vložte tento kód PySpark do první buňky a pomocí **Shift+Enter** kód spusťte.

   ```python
   blob_account_name = "azureopendatastorage"
   blob_container_name = "citydatacontainer"
   blob_relative_path = "Safety/Release/city=Boston"
   blob_sas_token = r"?st=2019-02-26T02%3A34%3A32Z&se=2119-02-27T02%3A34%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=XlJVWA7fMXCSxCKqJm8psMOh0W4h7cSYO28coRqF2fs%3D"
   ```

   Následující příkaz umožňuje Spark číst z úložiště objektů Blob vzdáleně. Vložte tento kód PySpark do další buňky a pomocí **Shift+Enter** kód spusťte.

   ```python
   wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
   spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
   print('Remote blob path: ' + wasbs_path)
   ```

   Následující příkaz vytvoří datový rámec. Vložte tento kód PySpark do další buňky a pomocí **Shift+Enter** kód spusťte.

   ```python
   df = spark.read.parquet(wasbs_path)
   print('Register the DataFrame as a SQL temporary view: source')
   df.createOrReplaceTempView('source')
   ```

4. Spuštění příkazu SQL vrátí prvních 10 řádků dat z dočasného zobrazení nazývaného **zdroj**. Vložte tento kód PySpark do další buňky a pomocí **Shift+Enter** kód spusťte.

   ```python
   print('Displaying top 10 rows: ')
   display(spark.sql('SELECT * FROM source LIMIT 10'))
   ```

5. Zobrazí se tabulkový výstup jako na následujícím snímku obrazovky (zobrazí se jenom některé sloupce):

    ![Ukázková data](./media/quickstart-create-databricks-workspace-portal/databricks-sample-csv-data.png "Ukázka dat JSON")

6. Nyní vytvoříte vizuální reprezentaci těchto dat, která ukazuje, kolik událostí zabezpečení je hlášeno pomocí aplikace Citizens Connect a aplikace City Worker app místo jiných zdrojů. V dolní části tabulkového výstupu vyberte ikonu **Pruhový graf** a klikněte na **Možnosti vykreslení**.

    ![Vytvoření pruhového grafu](./media/quickstart-create-databricks-workspace-portal/create-plots-databricks-notebook.png "Vytvoření pruhového grafu")

8. V části **Customize Plot** (Přizpůsobit graf) přetáhněte hodnoty, jak ukazuje snímek obrazovky.

    ![Přizpůsobení výsečového grafu](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-customize-plot.png "Přizpůsobení pruhového grafu")

   * Nastavit **klíče** na **zdroj**.
   * Nastavte **hodnoty** na **<\id>**.
   * V poli **Aggregation** (Agregace) vyberte možnost **COUNT** (Počet).
   * Nastavení **typu Zobrazení** na **výsečový graf**.

     Klikněte na **Použít**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení tohoto článku můžete cluster ukončit. Pokud to chcete udělat, v levém podokně v pracovním prostoru Azure Databricks vyberte **Clusters** (Clustery). U clusteru, který chcete ukončit, přesuňte kurzor na tři tečky pod sloupcem **Actions** (Akce) a vyberte ikonu **Terminate** (Ukončit).

![Zastavení clusteru Databricks](./media/quickstart-create-databricks-workspace-portal/terminate-databricks-cluster.png "Zastavení clusteru Databricks")

Pokud cluster ručně neukončíte, bude automaticky ukončen za předpokladu, že jste při vytváření clusteru zaškrtli políčko **Ukončit po \_ \_ minutách nečinnosti.** V takovém případě se cluster automaticky zastaví, pokud byl po stanovenou dobu neaktivní.

## <a name="next-steps"></a>Další kroky

V tomto článku jste vytvořili cluster Spark v Azure Databricks a spustili úlohu Spark pomocí dat z otevřených datových sad Azure. Můžete si také projít článek [Zdroje dat Spark](/azure/databricks/data/data-sources/index) a zjistit, jak do Azure Databricks importovat data z jiných zdrojů dat. V dalším článku se dozvíte, jak pomocí Azure Databricks provést operaci ETL (extrakce, transformace a načítání dat).

> [!div class="nextstepaction"]
>[Extrakce, transformace a načítání dat pomocí Azure Databricks](databricks-extract-load-sql-data-warehouse.md)
