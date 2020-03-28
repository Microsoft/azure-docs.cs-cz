---
title: 'Kurz: Načítání dat & spouštění dotazů pomocí Apache Spark – Azure HDInsight'
description: Kurz – Naučte se načítat data a spouštět interaktivní dotazy v clusterech Spark v Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc
ms.date: 02/12/2020
ms.openlocfilehash: 5eb6788a558e4429296731f1693edd18bf92f98f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77198884"
---
# <a name="tutorial-load-data-and-run-queries-on-an-apache-spark-cluster-in-azure-hdinsight"></a>Kurz: Načítání dat a spouštění dotazů v clusteru Apache Spark ve službě Azure HDInsight

V tomto kurzu se dozvíte, jak vytvořit datový rámec ze souboru CSV a jak spustit interaktivní dotazy Spark SQL proti clusteru [Apache Spark](https://spark.apache.org/) v Azure HDInsight. Ve Sparku je datový rámec distribuovaná kolekce dat uspořádaných do pojmenovaných sloupců. Datový rámec je koncepčním ekvivalentem tabulky v relační databázi nebo datového rámce v R nebo Pythonu.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vytvoření datového rámce ze souboru CSV
> * Spouštění dotazů nad datovým rámcem

## <a name="prerequisites"></a>Požadavky

Cluster Apache Spark ve službě HDInsight. Viz [Vytvoření clusteru Apache Spark](./apache-spark-jupyter-spark-sql-use-portal.md).

## <a name="create-a-jupyter-notebook"></a>Vytvoření poznámkového bloku Jupyter

Jupyter Notebook je interaktivní prostředí poznámkového bloku, které podporuje různé programovací jazyky. Poznámkový blok umožňuje pracovat s daty, kombinovat kód s textem markdownu a provádět jednoduché vizualizace.

1. Upravte `https://SPARKCLUSTER.azurehdinsight.net/jupyter` adresu URL `SPARKCLUSTER` nahrazením názvem clusteru Spark. Poté zadejte upravenou adresu URL ve webovém prohlížeči. Po zobrazení výzvy zadejte přihlašovací údaje clusteru.

2. Na webové stránce Jupyter, Vyberte **nový** > **PySpark** vytvořit poznámkový blok.

   ![Vytvoření poznámkového bloku Jupyter pro spuštění interaktivního dotazu Spark SQL](./media/apache-spark-load-data-run-query/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Vytvoření poznámkového bloku Jupyter pro spuštění interaktivního dotazu Spark SQL")

   Vytvoří se a otevře se nový poznámkový blok s názvem Untitled(`Untitled.ipynb`).

    > [!NOTE]  
    > Díky použití jádra PySpark k vytvoření poznámkového bloku se relace `spark` vytvoří automaticky za vás při spuštění první buňky kódu. Není potřeba relaci vytvářet explicitně.

## <a name="create-a-dataframe-from-a-csv-file"></a>Vytvoření datového rámce ze souboru CSV

Aplikace můžete vytvářet datové rámce přímo ze souborů nebo složek ve vzdáleném úložišti, jako je Azure Storage nebo Azure Data Lake Storage; ze stolu Úlu; nebo z jiných zdrojů dat podporovaných Sparkem, jako je Cosmos DB, Azure SQL DB, DW a tak dále. Následující snímek obrazovky ukazuje snímek souboru HVAC.csv použitého v tomto kurzu. Tento soubor CSV je součástí všech clusterů HDInsight Spark. Data zaznamenávají změny teploty několika budov.

![Snímek dat pro interaktivní dotaz Spark SQL](./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "Snímek dat pro interaktivní dotaz Spark SQL")

1. Vložte následující kód do prázdné buňky poznámkového bloku Jupyter a stisknutím kláves **SHIFT + ENTER** kód spusťte. Kód naimportuje typy potřebné pro tento scénář:

    ```python
    from pyspark.sql import *
    from pyspark.sql.types import *
    ```

    Po spuštění interaktivního dotazu v Jupyter se název okna nebo karty webového prohlížeče zobrazí jako **(Zaneprázdněn)** společně s názvem poznámkového bloku. Zobrazí se také plný kroužek vedle textu **PySpark** v pravém horním rohu. Po dokončení úlohy se změní na prázdný kruh.

    ![Stav interaktivního dotazu Spark SQL](./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "Stav interaktivního dotazu Spark SQL")

1. Všimněte si, že id relace vrácena. Z obrázku výše, id relace je 0. V případě potřeby můžete načíst podrobnosti `https://CLUSTERNAME.azurehdinsight.net/livy/sessions/ID/statements` relace přechodem na místo, kde CLUSTERNAME je název clusteru Spark a ID je vaše id relace.

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

     ![Výstup tabulky interaktivního výsledku dotazu Spark](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png "Výstup tabulky interaktivního výsledku dotazu Spark")

2. Výsledky můžete také zobrazit v dalších vizualizacích. Pokud chcete výstup zobrazit v podobě plošného grafu, vyberte **Oblast** a pak nastavte další hodnoty následujícím způsobem.

    ![Plošný graf interaktivního výsledku dotazu Spark](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png "Plošný graf interaktivního výsledku dotazu Spark")

3. Na řádku nabídek poznámkového bloku přejděte na **Soubor** > **uložit a kontrolní bod**.

4. Pokud právě začínáte s [dalším kurzem](apache-spark-use-bi-tools.md), nechte poznámkový blok otevřený. Pokud ne, vypněte poznámkový blok a uvolněte prostředky clusteru: z řádku nabídek poznámkového bloku přejděte na **Zavřít** >  **a zastavit soubor**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

S HDInsight, vaše data a Jupyter poznámkové bloky jsou uloženy v Azure Storage nebo Azure Data Lake Storage, takže můžete bezpečně odstranit cluster, když není v provozu. Účtuje se vám také cluster HDInsight, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster jsou mnohonásobně vyšší než poplatky za úložiště, má ekonomické smysl odstranit clustery, když nejsou používány. Pokud se chystáte hned začít pracovat na dalším kurzu, měli byste cluster zachovat.

Otevřete cluster na webu Azure Portal a vyberte **Odstranit**.

![Odstranění clusteru HDInsight](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "Odstranění clusteru HDInsight")

Můžete také výběrem názvu skupiny prostředků otevřít stránku skupiny prostředků a pak vybrat **Odstranit skupinu prostředků**. Odstraněním skupiny prostředků odstraníte cluster HDInsight Spark i výchozí účet úložiště.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak vytvořit datový rámec ze souboru CSV a jak spustit interaktivní dotazy Spark SQL proti clusteru Apache Spark v Azure HDInsight. Přejdete k dalšímu článku a zjistěte, jak lze data zaregistrovaná v Apache Spark vtáhnout do analytického nástroje BI, jako je Power BI.

> [!div class="nextstepaction"]
> [Analýza dat pomocí nástrojů BI](apache-spark-use-bi-tools.md)
