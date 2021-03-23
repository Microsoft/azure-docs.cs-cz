---
title: 'Kurz: načtení dat & spouštění dotazů s využitím Apache Spark-Azure HDInsight'
description: Kurz – Naučte se načítat data a spouštět interaktivní dotazy na clusterech Spark v Azure HDInsight.
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc
ms.date: 02/12/2020
ms.openlocfilehash: de17bf02392f0bb05820fabba3f9057e067391cf
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104865906"
---
# <a name="tutorial-load-data-and-run-queries-on-an-apache-spark-cluster-in-azure-hdinsight"></a>Kurz: Načítání dat a spouštění dotazů v clusteru Apache Spark ve službě Azure HDInsight

V tomto kurzu se naučíte, jak vytvořit datový rámec ze souboru CSV a jak spouštět interaktivní dotazy Spark SQL s [Apache Sparkm](https://spark.apache.org/) clusterem v Azure HDInsight. Ve Sparku je datový rámec distribuovaná kolekce dat uspořádaných do pojmenovaných sloupců. Datový rámec je koncepčním ekvivalentem tabulky v relační databázi nebo datového rámce v R nebo Pythonu.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vytvoření datového rámce ze souboru CSV
> * Spouštění dotazů nad datovým rámcem

## <a name="prerequisites"></a>Předpoklady

Cluster Apache Spark ve službě HDInsight. Viz [Vytvoření clusteru Apache Spark](./apache-spark-jupyter-spark-sql-use-portal.md).

## <a name="create-a-jupyter-notebook"></a>Vytvoříte poznámkový blok Jupyter Notebooks.

Jupyter Notebook je interaktivní prostředí poznámkového bloku, které podporuje různé programovací jazyky. Poznámkový blok umožňuje pracovat s daty, kombinovat kód s textem markdownu a provádět jednoduché vizualizace.

1. Upravte adresu URL `https://SPARKCLUSTER.azurehdinsight.net/jupyter` tak, že nahradíte `SPARKCLUSTER` název vašeho clusteru Spark. Pak zadejte upravenou adresu URL do webového prohlížeče. Po zobrazení výzvy zadejte přihlašovací údaje clusteru.

2. Z webové stránky Jupyter vyberte **Nový**  >  **PySpark** a vytvořte Poznámkový blok.

   :::image type="content" source="./media/apache-spark-load-data-run-query/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png " alt-text="Vytvoření Jupyter Notebook pro spuštění interaktivního dotazu Spark SQL" border="true":::

   Vytvoří se nový Poznámkový blok a otevře se s názvem bez názvu ( `Untitled.ipynb` ).

    > [!NOTE]  
    > Díky použití jádra PySpark k vytvoření poznámkového bloku se relace `spark` vytvoří automaticky za vás při spuštění první buňky kódu. Není potřeba relaci vytvářet explicitně.

## <a name="create-a-dataframe-from-a-csv-file"></a>Vytvoření datového rámce ze souboru CSV

Aplikace mohou vytvářet datový rámec přímo ze souborů nebo složek na vzdáleném úložišti, jako je například Azure Storage nebo Azure Data Lake Storage; z tabulky podregistru; nebo z jiných zdrojů dat, které podporuje Spark, například Cosmos DB, Azure SQL DB, DW a tak dále. Následující snímek obrazovky ukazuje snímek souboru HVAC.csv použitého v tomto kurzu. Tento soubor CSV je součástí všech clusterů HDInsight Spark. Data zaznamenávají změny teploty několika budov.

:::image type="content" source="./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png " alt-text="Snímek dat pro interaktivní dotaz Spark SQL" border="true":::

1. Vložte následující kód do prázdné buňky Jupyter Notebook a stisknutím kláves **SHIFT + ENTER** kód spusťte. Kód naimportuje typy potřebné pro tento scénář:

    ```python
    from pyspark.sql import *
    from pyspark.sql.types import *
    ```

    Po spuštění interaktivního dotazu v Jupyter se název okna nebo karty webového prohlížeče zobrazí jako **(Zaneprázdněn)** společně s názvem poznámkového bloku. Zobrazí se také plný kroužek vedle textu **PySpark** v pravém horním rohu. Po dokončení úlohy se změní na prázdný kruh.

    :::image type="content" source="./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png " alt-text="Stav interaktivního dotazu Spark SQL" border="true":::

1. Všimněte si vráceného ID relace. Z obrázku výše je ID relace 0. V případě potřeby můžete načíst podrobnosti o relaci tak, že přejdete na `https://CLUSTERNAME.azurehdinsight.net/livy/sessions/ID/statements` místo, kde název_clusteru je název vašeho clusteru Spark a ID je číslo ID vaší relace.

1. Spuštěním následujícího kódu vytvořte datový rámec a dočasnou tabulku (**hvac**).

    ```python
    # Create a dataframe and table from sample data
    csvFile = spark.read.csv('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv', header=True, inferSchema=True)
    csvFile.write.saveAsTable("hvac")
    ```

## <a name="run-queries-on-the-dataframe"></a>Spouštění dotazů nad datovým rámcem

Po vytvoření tabulky můžete nad daty spustit interaktivní dotaz.

1. V prázdné buňce poznámkového bloku spusťte následující kód:

    ```sql
    %%sql
    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```

   Zobrazí se následující tabulkový výstup.

     :::image type="content" source="./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png " alt-text="Výstup tabulky pro interaktivní výsledek dotazu Spark" border="true":::

2. Výsledky můžete také zobrazit v dalších vizualizacích. Pokud chcete výstup zobrazit v podobě plošného grafu, vyberte **Oblast** a pak nastavte další hodnoty následujícím způsobem.

    :::image type="content" source="./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png " alt-text="Plošný graf interaktivního dotazu Spark – výsledek" border="true":::

3. V řádku nabídek poznámkového bloku přejděte na **soubor**  >  **Uložit a kontrolní bod**.

4. Pokud právě začínáte s [dalším kurzem](apache-spark-use-bi-tools.md), nechte poznámkový blok otevřený. Pokud ne, vypněte Poznámkový blok a uvolněte prostředky clusteru: z řádku nabídek Poznámkový blok přejděte na **soubor**  >   **Zavřít a** zastavte.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Se službou HDInsight jsou vaše data a Jupyter poznámkové bloky uložené v Azure Storage nebo Azure Data Lake Storage, takže můžete cluster bezpečně odstranit, pokud se nepoužívá. Účtují se vám také poplatky za cluster HDInsight, a to i v případě, že se už nepoužívá. Vzhledem k tomu, že se poplatky za cluster mnohokrát účtují rychleji než poplatky za úložiště, má ekonomický smysl odstraňovat clustery, když se nepoužívají. Pokud se chystáte hned začít pracovat na dalším kurzu, měli byste cluster zachovat.

Otevřete cluster na webu Azure Portal a vyberte **Odstranit**.

:::image type="content" source="./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png " alt-text="Odstranit cluster HDInsight" border="true":::

Můžete také výběrem názvu skupiny prostředků otevřít stránku skupiny prostředků a pak vybrat **Odstranit skupinu prostředků**. Odstraněním skupiny prostředků odstraníte cluster HDInsight Spark i výchozí účet úložiště.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak vytvořit datový rámec ze souboru CSV a jak spouštět interaktivní dotazy Spark SQL s Apache Sparkm clusterem v Azure HDInsight. V dalším článku zjistíte, jak se data, která jste zaregistrovali v Apache Spark můžete načíst do nástroje BI Analytics, jako je například Power BI.

> [!div class="nextstepaction"]
> [Analýza dat pomocí nástrojů BI](apache-spark-use-bi-tools.md)
