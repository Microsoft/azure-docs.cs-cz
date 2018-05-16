---
title: 'Kurz: Načítání dat a spouštění dotazů v clusteru Apache Spark ve službě Azure HDInsight | Microsoft Docs'
description: Zjistěte, jak načítat data a spouštět interaktivní dotazy v clusterech Spark ve službě Azure HDInsight.
services: azure-hdinsight
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.devlang: na
ms.topic: tutorial
ms.author: jgao
ms.date: 05/07/2018
ms.openlocfilehash: 63a876dc148129cd2a3eb93ed7ab6baf06a07c62
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-load-data-and-run-queries-on-an-apache-spark-cluster-in-azure-hdinsight"></a>Kurz: Načítání dat a spouštění dotazů v clusteru Apache Spark ve službě Azure HDInsight

V tomto kurzu zjistíte, jak vytvořit datový rámec ze souboru CSV a spouštět interaktivní dotazy Spark SQL proti clusteru Apache Spark ve službě Azure HDInsight. Ve Sparku je datový rámec distribuovaná kolekce dat uspořádaných do pojmenovaných sloupců. Datový rámec je koncepčním ekvivalentem tabulky v relační databázi nebo datového rámce v R nebo Pythonu.
 
V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vytvoření datového rámce ze souboru CSV
> * Spouštění dotazů nad datovým rámcem

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* Dokončete rychlý start [Vytvoření clusteru Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="create-a-dataframe-from-a-csv-file"></a>Vytvoření datového rámce ze souboru CSV

Aplikace můžou vytvářet datové rámce z existujících datových sad RDD (Resilient Distributed Dataset), z tabulky Hive nebo ze zdrojů dat pomocí objektu SQLContext. Následující snímek obrazovky ukazuje snímek souboru HVAC.csv použitého v tomto kurzu. Tento soubor CSV je součástí všech clusterů HDInsight Spark. Data zaznamenávají změny teploty několika budov.
    
![Snímek dat pro interaktivní dotaz Spark SQL](./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "Snímek dat pro interaktivní dotaz Spark SQL")


1. Otevřete poznámkový blok Jupyter, který jste vytvořili v části Požadavky.
2. Do prázdné buňky poznámkového bloku vložte následující kód a stisknutím **SHIFT + ENTER** kód spusťte. Kód naimportuje typy potřebné pro tento scénář:

    ```PySpark
    from pyspark.sql import *
    from pyspark.sql.types import *
    ```

    Po spuštění interaktivního dotazu v Jupyter se název okna nebo karty webového prohlížeče zobrazí jako **(Zaneprázdněn)** společně s názvem poznámkového bloku. Zobrazí se také plný kroužek vedle textu **PySpark** v pravém horním rohu. Po dokončení úlohy se změní na prázdný kruh.

    ![Stav interaktivního dotazu Spark SQL](./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "Stav interaktivního dotazu Spark SQL")

3. Spuštěním následujícího kódu vytvořte datový rámec a dočasnou tabulku (**hvac**). Kód neextrahuje všechny sloupce, které jsou v souboru CSV. 

    ```PySpark
    # Create an RDD from sample data
    hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
    # Create a schema for the data
    Entry = Row('Date', 'Time', 'TargetTemp', 'ActualTemp', 'BuildingID')
    
    # Parse the data and create a schema
    hvacParts = hvacText.map(lambda s: s.split(',')).filter(lambda s: s[0] != 'Date')
    hvac = hvacParts.map(lambda p: Entry(str(p[0]), str(p[1]), int(p[2]), int(p[3]), int(p[6])))
    
    # Infer the schema and create a table       
    hvacTable = sqlContext.createDataFrame(hvac)
    hvacTable.registerTempTable('hvactemptable')
    dfw = DataFrameWriter(hvacTable)
    dfw.saveAsTable('hvac')
    ```

    > [!NOTE]
    > Díky použití jádra PySpark k vytvoření poznámkového bloku se kontexty SQL vytvoří automaticky za vás při spuštění první buňky kódu. Není potřeba explicitně vytvářet žádné kontexty.


## <a name="run-queries-on-the-dataframe"></a>Spouštění dotazů nad datovým rámcem

Po vytvoření tabulky můžete nad daty spustit interaktivní dotaz.

1. V prázdné buňce poznámkového bloku spusťte následující kód:

    ```PySpark
    %%sql
    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```

   Vzhledem k tomu, že se v poznámkovém bloku používá jádro PySpark, můžete teď přímo spustit interaktivní dotaz SQL nad dočasnou tabulkou **hvac**.

   Zobrazí se následující tabulkový výstup.

     ![Tabulkový výstup výsledku interaktivního dotazu Spark](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png "Tabulkový výstup výsledku interaktivního dotazu Spark")

3. Výsledky můžete také zobrazit v dalších vizualizacích. Pokud chcete výstup zobrazit v podobě plošného grafu, vyberte **Oblast** a pak nastavte další hodnoty následujícím způsobem.

    ![Plošný graf výsledku interaktivního dotazu Spark](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png "Plošný graf výsledku interaktivního dotazu Spark")

10. V nabídce **Soubor** poznámkového bloku vyberte **Uložit a vytvořit kontrolní bod**. 

11. Pokud právě začínáte s [dalším kurzem](apache-spark-use-bi-tools.md), nechte poznámkový blok otevřený. V opačném případě poznámkový blok zavřete, abyste uvolnili prostředky clusteru. V nabídce **Soubor** poznámkového bloku vyberte **Zavřít a zastavit**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

V případě služby HDInsight se vaše data ukládají ve službě Azure Storage nebo Azure Data Lake Store, takže můžete cluster bezpečně odstranit, když se nepoužívá. Za cluster služby HDInsight se účtují poplatky, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster představují několikanásobek poplatků za úložiště, dává ekonomický smysl odstraňovat clustery, které nejsou používány. Pokud se chystáte hned začít pracovat na dalším kurzu, měli byste cluster zachovat.

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

