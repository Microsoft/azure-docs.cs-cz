---
title: Spusťte interaktivní dotazy v clusteru Azure HDInsight Spark | Microsoft Docs
description: Rychlý start k HDInsight Spark týkající se vytvoření clusteru Apache Spark ve službě HDInsight.
keywords: spark quickstart, interactive spark, interactive query, hdinsight spark, azure spark
services: hdinsight
documentationcenter: ''
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 11/29/2017
ms.author: jgao
ms.openlocfilehash: 177fb47c72e9abbafcda69416643fbd3848373bd
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="run-interactive-queries-on-spark-clusters-in-hdinsight"></a>Spusťte interaktivní dotazy na clustery Spark v HDInsight

Naučte se používat Poznámkový blok Jupyter ke spouštění interaktivních dotazů Spark SQL na clusteru Spark. 

[Poznámkový blok Jupyter](http://jupyter-notebook.readthedocs.io/en/latest/notebook.html) je aplikace založené na prohlížeči, která rozšiřuje možnosti interaktivního pomocí konzoly na webu. Spark v HDInsight také zahrnuje [poznámkový blok Zeppelin](apache-spark-zeppelin-notebook.md). V tomto kurzu se používá poznámkový blok Jupyter.

Poznámkové bloky Jupyter v clusterech HDInsight podporují tři jádra – **PySpark**, **PySpark3** a **Spark**. V tomto kurzu se používá jádro **PySpark**. Další informace o jádrech a výhody použití jádra **PySpark** najdete v tématu s popisem [použití jader poznámkových bloků Jupyter s clustery Apache Spark v HDInsight](apache-spark-jupyter-notebook-kernels.md). Pomocí poznámkového bloku Zeppelin naleznete v části [clusteru poznámkových bloků Zeppelin použít s Apache Spark v Azure HDInsight](./apache-spark-zeppelin-notebook.md).

V tomto kurzu dotazování dat v souboru csv. Jako dataframe musí nejdřív načíst data do Spark. Potom můžete spouštět dotazy na dataframe pomocí poznámkového bloku Jupyter. 

## <a name="prerequisites"></a>Požadavky

* **Clusteru Azure HDInsight Spark**. Pokyny najdete v tématu [vytvářet cluster Apache Spark v Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* **Poznámkový blok Jupyter pomocí PySpark**. Pokyny najdete v tématu [vytvoření poznámkového bloku Jupyter](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook).

## <a name="create-a-dataframe-from-a-csv-file"></a>Vytvoření dataframe ze souboru csv

Pomocí SQLContext aplikace vytvářet dataframes z existující RDD, z tabulky Hive nebo z datové zdroje. 

**Chcete-li vytvořit dataframe ze souboru csv**

1. Vytvoření poznámkového bloku Jupyter pomocí PySpark, pokud nemáte. Pokyny najdete v tématu [vytvoření poznámkového bloku Jupyter](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook).

2. Vložte následující kód do prázdné buňky poznámkového bloku a potom stiskněte klávesu **SHIFT + ENTER** spustit kód. Kód naimportuje typy potřebné pro tento scénář:

    ```PySpark
    from pyspark.sql import *
    from pyspark.sql.types import *
    ```
    Pomocí jádra PySpark vytvoření poznámkového bloku, Spark a Hive se kontexty automaticky vytvoří za vás při spuštění první buňky kódu. Není nutné explicitně tvořit kontexty vytvořit.

    Při spouštění interaktivních dotazů v Jupyter, zobrazuje titulek okno nebo kartu webového prohlížeče **(zaneprázdněn)** společně s názvem poznámkového bloku. Zobrazí se také plný kroužek vedle textu **PySpark** v pravém horním rohu. Po dokončení úlohy se změní na prázdný kruh.

    ![Stav interaktivního dotazu Spark SQL](./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "Stav interaktivního dotazu Spark SQL")

3. Spusťte následující kód k vytvoření dataframe a do dočasné tabulky (**TVK**) tak, že spustíte následující kód: kód není extrahujte všechny sloupce k dispozici v souboru CSV. 

    ```PySpark
    # Create an RDD from sample data
    hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
    # Create a schema for our data
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
    Následující snímek obrazovky ukazuje snímek souboru HVAC.csv. Soubor csv obsahuje všechny clustery HDInsigt Spark. Data zaznamená změny teploty budovy.

    ![Snímek dat pro interaktivních dotazů Spark SQL](./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "snímek dat pro interaktivních dotazů Spark SQL")

## <a name="run-queries-on-the-dataframe"></a>Spuštění dotazů na dataframe

Po vytvoření tabulky, můžete spustit interaktivní dotaz na data.

**Ke spuštění dotazu**

1. Spusťte následující kód do prázdné buňky poznámkového bloku:

    ```PySpark
    %%sql
    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```

   Protože jádra PySpark se používá v poznámkovém bloku, můžete nyní přímo spustit dotaz interaktivní SQL na dočasnou tabulku **TVK** kterou jste vytvořili pomocí `%%sql` magic. Další informace o magických příkazech `%%sql` a dalších magických příkazech, které jsou k dispozici s jádrem PySpark, najdete v části [Jádra dostupná v poznámkových blocích Jupyter s clustery Spark HDInsight](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

   Ve výchozím nastavení se zobrazí následující tabulkový výstup.

     ![Tabulkový výstup výsledku interaktivního dotazu Spark](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png "Tabulkový výstup výsledku interaktivního dotazu Spark")

3. Výsledky můžete také zobrazit v dalších vizualizacích. Pokud chcete zobrazit plošný graf pro stejný výstup, vyberte **oblasti** pak nastavte jiné hodnoty, jak je zobrazeno.

    ![Plošný graf výsledku interaktivního dotazu Spark](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png "Plošný graf výsledku interaktivního dotazu Spark")

10. Z **soubor** nabídky v poznámkovém bloku, klikněte na tlačítko **uložit a kontrolního bodu**. 

11. Pokud začínáte [další kurz](apache-spark-use-bi-tools.md) teď zůstat otevřeno, poznámkového bloku. Pokud není, vypněte poznámkového bloku k uvolnění prostředků clusteru: z **soubor** nabídky v poznámkovém bloku, klikněte na tlačítko **zavřít a zastavit**.

## <a name="next-step"></a>Další krok

V tomto článku jste se naučili ke spuštění interaktivních dotazů v Spark pomocí poznámkového bloku Jupyter. Přechodu na další článek a zobrazit, jak mohou být vyžádány data, která jste zaregistrovali v Spark do nástroj pro analýzu BI, například Power BI a Tableau. 

> [!div class="nextstepaction"]
>[Spark BI nástroje vizualizaci dat pomocí Azure HDInsight](apache-spark-use-bi-tools.md)




