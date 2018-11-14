---
title: 'Kurz: Načítání dat a spouštění dotazů v clusteru Apache Spark ve službě Azure HDInsight '
description: Zjistěte, jak načítat data a spouštět interaktivní dotazy v clusterech Spark ve službě Azure HDInsight.
services: azure-hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.author: hrasheed
ms.date: 11/06/2018
ms.openlocfilehash: 85afc16fe6bcae4e0a7218fa9f66bab3e947ec6b
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51244065"
---
# <a name="tutorial-load-data-and-run-queries-on-an-apache-spark-cluster-in-azure-hdinsight"></a>Kurz: Načítání dat a spouštění dotazů v clusteru Apache Spark ve službě Azure HDInsight

V tomto kurzu zjistíte, jak vytvořit datový rámec ze souboru CSV a spouštět interaktivní dotazy Spark SQL proti clusteru Apache Spark ve službě Azure HDInsight. Ve Sparku je datový rámec distribuovaná kolekce dat uspořádaných do pojmenovaných sloupců. Datový rámec je koncepčním ekvivalentem tabulky v relační databázi nebo datového rámce v R nebo Pythonu.
 
V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vytvoření datového rámce ze souboru CSV
> * Spouštění dotazů nad datovým rámcem

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* Dokončený rychlý start [Vytvoření clusteru Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="create-a-dataframe-from-a-csv-file"></a>Vytvoření datového rámce ze souboru CSV

Aplikace může vytvářet datové rámce přímo ze souborů nebo složek ve vzdáleném úložišti, jako je Azure Storage nebo Azure Data Lake Storage, z tabulky Hive nebo z dalších zdrojů dat podporovaných Sparkem, jako je služba Cosmos DB, Azure SQL Database, Data Warehouse atd. Následující snímek obrazovky ukazuje snímek souboru HVAC.csv použitého v tomto kurzu. Tento soubor CSV je součástí všech clusterů HDInsight Spark. Data zaznamenávají změny teploty několika budov.
    
![Snímek dat pro interaktivní dotaz Spark SQL](./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "Snímek dat pro interaktivní dotaz Spark SQL")


1. Otevřete poznámkový blok Jupyter, který jste vytvořili v části Požadavky.
2. Do prázdné buňky poznámkového bloku vložte následující kód a stisknutím **SHIFT + ENTER** kód spusťte. Kód naimportuje typy potřebné pro tento scénář:

    ```python
    from pyspark.sql import *
    from pyspark.sql.types import *
    ```

    Po spuštění interaktivního dotazu v Jupyter se název okna nebo karty webového prohlížeče zobrazí jako **(Zaneprázdněn)** společně s názvem poznámkového bloku. Zobrazí se také plný kroužek vedle textu **PySpark** v pravém horním rohu. Po dokončení úlohy se změní na prázdný kruh.

    ![Stav interaktivního dotazu Spark SQL](./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "Stav interaktivního dotazu Spark SQL")

3. Spuštěním následujícího kódu vytvořte datový rámec a dočasnou tabulku (**hvac**). 

    ```python
    # Create a dataframe and table from sample data
    csvFile = spark.read.csv('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv', header=True, inferSchema=True)
    csvFile.write.saveAsTable("hvac")
    ```

    > [!NOTE]
    > Díky použití jádra PySpark k vytvoření poznámkového bloku se relace `spark` vytvoří automaticky za vás při spuštění první buňky kódu. Není potřeba relaci vytvářet explicitně.


## <a name="run-queries-on-the-dataframe"></a>Spouštění dotazů nad datovým rámcem

Po vytvoření tabulky můžete nad daty spustit interaktivní dotaz.

1. V prázdné buňce poznámkového bloku spusťte následující kód:

    ```sql
    %%sql
    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```

   Zobrazí se následující tabulkový výstup.

     ![Tabulkový výstup výsledku interaktivního dotazu Spark](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png "Tabulkový výstup výsledku interaktivního dotazu Spark")

3. Výsledky můžete také zobrazit v dalších vizualizacích. Pokud chcete výstup zobrazit v podobě plošného grafu, vyberte **Oblast** a pak nastavte další hodnoty následujícím způsobem.

    ![Plošný graf výsledku interaktivního dotazu Spark](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png "Plošný graf výsledku interaktivního dotazu Spark")

10. V nabídce **Soubor** poznámkového bloku vyberte **Uložit a vytvořit kontrolní bod**. 

11. Pokud právě začínáte s [dalším kurzem](apache-spark-use-bi-tools.md), nechte poznámkový blok otevřený. V opačném případě poznámkový blok zavřete, abyste uvolnili prostředky clusteru. V nabídce **Soubor** poznámkového bloku vyberte **Zavřít a zastavit**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Se službou HDInsight se vaše data a poznámkové bloky Jupyter ukládají ve službě Azure Storage nebo Azure Data Lake Store, takže můžete cluster bezpečně odstranit, když se nepoužívá. Za cluster služby HDInsight se účtují poplatky, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster představují několikanásobek poplatků za úložiště, dává ekonomický smysl odstraňovat clustery, které nejsou používány. Pokud se chystáte hned začít pracovat na dalším kurzu, měli byste cluster zachovat.

Otevřete cluster na webu Azure Portal a vyberte **Odstranit**.

![Odstranění clusteru HDInsight](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "Odstranění clusteru HDInsight")

Můžete také výběrem názvu skupiny prostředků otevřít stránku skupiny prostředků a pak vybrat **Odstranit skupinu prostředků**. Odstraněním skupiny prostředků odstraníte cluster HDInsight Spark i výchozí účet úložiště.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

* Vytvoření datového rámce Spark
* Spouštění dotazů Spark SQL nad datovým rámcem

V dalším článku se dozvíte, jak můžete data zaregistrovaná ve Sparku přetáhnout do nástroje pro analýzu BI, jako je Power BI. 
> [!div class="nextstepaction"]
> [Analýza dat pomocí nástrojů BI](apache-spark-use-bi-tools.md)

