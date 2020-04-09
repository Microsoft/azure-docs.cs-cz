---
title: 'Kurz: Vytvoření aplikace pro strojové učení Spark – Azure HDInsight'
description: Výuka – podrobný návod, jak vytvořit aplikaci Apache Spark pro strojové učení v clusterech HDInsight Spark pomocí notebooku Jupyter.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc
ms.date: 04/07/2020
ms.openlocfilehash: 963f5bd4dfdd9dda78a437bdb1111c9eec2795dc
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878438"
---
# <a name="tutorial-build-an-apache-spark-machine-learning-application-in-azure-hdinsight"></a>Kurz: Vytvoření aplikace apache spark strojového učení v Azure HDInsight

V tomto kurzu se dozvíte, jak pomocí [poznámkového bloku Jupyter](https://jupyter.org/) vytvořit aplikaci [Apache Spark](./apache-spark-overview.md) pro strojové učení pro Azure HDInsight.

[MLlib](https://spark.apache.org/docs/latest/ml-guide.html) je knihovna strojového učení společnosti Spark, která se skládá z běžných algoritmů učení a nástrojů. (Klasifikace, regrese, shlukování, kolaborativní filtrování a snížení dimenzionality. Také základní optimalizace primitiv.)

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vývoj aplikace apache spark strojového učení

## <a name="prerequisites"></a>Požadavky

* Cluster Apache Spark ve službě HDInsight. Viz [Vytvoření clusteru Apache Spark](./apache-spark-jupyter-spark-sql-use-portal.md).

* Znalost používání poznámkových bloků Jupyter se Sparkem ve službě HDInsight. Další informace najdete v [tématu Načítání dat a spouštění dotazů pomocí Apache Spark na HDInsight .](./apache-spark-load-data-run-query.md)

## <a name="understand-the-data-set"></a>Vysvětlení datové sady

Aplikace používá ukázková data **HVAC.csv,** která jsou ve výchozím nastavení k dispozici ve všech clusterech. Soubor je umístěn `\HdiSamples\HdiSamples\SensorSampleData\hvac`na adrese . Data ukazují cílovou teplotu a skutečnou teplotu několika budov s nainstalovanými systémy HVAC. Sloupec **System** představuje ID systému a sloupec **SystemAge** představuje počet let, kolik je systém HVAC v budově umístěný. Můžete předpovědět, zda bude budova teplejší nebo chladnější na základě cílové teploty, daného ID systému a stáří systému.

![Snímek dat použitých pro příklad strojového učení Spark](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-understand-data.png "Snímek dat použitých pro příklad strojového učení Spark")

## <a name="develop-a-spark-machine-learning-application-using-spark-mllib"></a>Vývoj aplikace strojového učení Sparku pomocí knihovny Spark MLlib

Tato aplikace používá kanál Spark [ML](https://spark.apache.org/docs/2.2.0/ml-pipeline.html) k provedení klasifikace dokumentů. Ml kanály poskytují jednotnou sadu rozhraní API vysoké úrovně postavené na dataframes. DataFrames pomáhají uživatelům vytvářet a ladit praktické kanály strojového učení. V kanálu rozdělíte dokument na slova, převedete slova na vektory číselné funkce a nakonec pomocí vektorů číselné funkce a popisků sestavíte prediktivní model. Chcete-li vytvořit aplikaci, postupujte takto.

1. Vytvořte poznámkový blok Jupyter pomocí jádra PySpark. Pokyny najdete v tématu [Vytvoření poznámkového bloku Jupyter](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook).

1. Naimportujte typy potřebné pro tento scénář. Do prázdné buňky vložte následující fragment kódu a pak stiskněte **SHIFT + ENTER**.

    ```PySpark
    from pyspark.ml import Pipeline
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml.feature import HashingTF, Tokenizer
    from pyspark.sql import Row

    import os
    import sys
    from pyspark.sql.types import *

    from pyspark.mllib.classification import LogisticRegressionWithSGD
    from pyspark.mllib.regression import LabeledPoint
    from numpy import array
    ```

1. Načtěte data (ze souboru hvac.csv), proveďte jejich parsování a použijte je k natrénování modelu.

    ```PySpark
    # Define a type called LabelDocument
    LabeledDocument = Row("BuildingID", "SystemInfo", "label")

    # Define a function that parses the raw CSV file and returns an object of type LabeledDocument
    def parseDocument(line):
        values = [str(x) for x in line.split(',')]
        if (values[3] > values[2]):
            hot = 1.0
        else:
            hot = 0.0

        textValue = str(values[4]) + " " + str(values[5])

        return LabeledDocument((values[6]), textValue, hot)

    # Load the raw HVAC.csv file, parse it using the function
    data = sc.textFile("/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
    training = documents.toDF()
    ```

    Ve fragmentu kódu definujete funkci, která porovnává skutečnou teplotu s cílovou teplotou. Pokud je skutečná teplota vyšší, budova je teplá a označí se hodnotou **1.0**. V opačném případě je budova studená a označí se hodnotou **0.0**.

1. Nakonfigurujte kanál strojového učení Sparku, který se skládá ze tří fází: tokenizer, hashingTF a lr.

    ```PySpark
    tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
    hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
    lr = LogisticRegression(maxIter=10, regParam=0.01)
    pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])
    ```

    Další informace o kanálu a jeho fungování najdete v [tématu Apache Spark machine learning pipeline](https://spark.apache.org/docs/latest/ml-pipeline.html).

1. Přizpůsobte kanál pro trénovací dokument.

    ```PySpark
    model = pipeline.fit(training)
    ```

1. Ověřte trénovací dokument a zkontrolujte pokrok s aplikací.

    ```PySpark
    training.show()
    ```

    Výstup je podobný tomuto:

    ```output
    +----------+----------+-----+
    |BuildingID|SystemInfo|label|
    +----------+----------+-----+
    |         4|     13 20|  0.0|
    |        17|      3 20|  0.0|
    |        18|     17 20|  1.0|
    |        15|      2 23|  0.0|
    |         3|      16 9|  1.0|
    |         4|     13 28|  0.0|
    |         2|     12 24|  0.0|
    |        16|     20 26|  1.0|
    |         9|      16 9|  1.0|
    |        12|       6 5|  0.0|
    |        15|     10 17|  1.0|
    |         7|      2 11|  0.0|
    |        15|      14 2|  1.0|
    |         6|       3 2|  0.0|
    |        20|     19 22|  0.0|
    |         8|     19 11|  0.0|
    |         6|      15 7|  0.0|
    |        13|      12 5|  0.0|
    |         4|      8 22|  0.0|
    |         7|      17 5|  0.0|
    +----------+----------+-----+
    ```

    Porovnejte výstup s nezpracovaným souborem CSV. Například první řádek souboru CSV obsahuje tato data:

    ![Snímek výstupních dat pro příklad strojového učení Spark](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-output-data.png "Snímek výstupních dat pro příklad strojového učení Spark")

    Všimněte si, že skutečná teplota je nižší než cílová teplota. To značí, že je budova studená. Hodnota **popisku** v prvním řádku je **0,0**, což znamená, že budova není horká.

1. Připravte datovou sadu, pro kterou chcete natrénovaný model spustit. Chcete-li tak učinit, předáte ID systému a stáří systému (označené jako **SystemInfo** ve výstupu školení). Model předpovídá, zda budova s tímto ID systému a stáří systému bude teplejší (označené 1.0) nebo chladnější (označené 0,0).

    ```PySpark
    # SystemInfo here is a combination of system ID followed by system age
    Document = Row("id", "SystemInfo")
    test = sc.parallelize([(1L, "20 25"),
                    (2L, "4 15"),
                    (3L, "16 9"),
                    (4L, "9 22"),
                    (5L, "17 10"),
                    (6L, "7 22")]) \
        .map(lambda x: Document(*x)).toDF()
    ```

1. Nakonec na základě testovacích dat vytvořte předpovědi.

    ```PySpark
    # Make predictions on test documents and print columns of interest
    prediction = model.transform(test)
    selected = prediction.select("SystemInfo", "prediction", "probability")
    for row in selected.collect():
        print row
    ```

    Výstup je podobný tomuto:

    ```output  
    Row(SystemInfo=u'20 25', prediction=1.0, probability=DenseVector([0.4999, 0.5001]))
    Row(SystemInfo=u'4 15', prediction=0.0, probability=DenseVector([0.5016, 0.4984]))
    Row(SystemInfo=u'16 9', prediction=1.0, probability=DenseVector([0.4785, 0.5215]))
    Row(SystemInfo=u'9 22', prediction=1.0, probability=DenseVector([0.4549, 0.5451]))
    Row(SystemInfo=u'17 10', prediction=1.0, probability=DenseVector([0.4925, 0.5075]))
    Row(SystemInfo=u'7 22', prediction=0.0, probability=DenseVector([0.5015, 0.4985]))
    ```

   Sledujte první řádek v předpovědi. U klimatizačního systému s ID 20 a stářím systému 25 let je budova horká (**predikce = 1,0**). První hodnota DenseVector (0.49999) odpovídá předpovědi 0.0 a druhá hodnota (0.5001) odpovídá předpovědi 1.0. Přestože je druhá hodnota pouze nepatrně vyšší, model ve výstupu zobrazí **prediction=1.0**.

1. Vypněte poznámkový blok a uvolněte tak prostředky. Provedete to tak, že v nabídce **Soubor** poznámkového bloku vyberete **Zavřít a zastavit**. Tato akce poznámkový blok vypne a zavře.

## <a name="use-anaconda-scikit-learn-library-for-spark-machine-learning"></a>Použití knihovny Anaconda scikit-learn pro strojové učení Sparku

Clustery Apache Spark ve službě HDInsight obsahují knihovny Anaconda. Jejich součástí je také knihovna **scikit-learn** pro strojové učení. Knihovna obsahuje také různé datové sady, které můžete použít k vytváření ukázkových aplikací přímo z poznámkového bloku Jupyter. Příklady použití knihovny scikit-learn [https://scikit-learn.org/stable/auto_examples/index.html](https://scikit-learn.org/stable/auto_examples/index.html)naleznete v tématu .

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete nadále používat tuto aplikaci, odstraňte cluster, který jste vytvořili pomocí následujících kroků:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Do pole **Hledat** v horní části zadejte **HDInsight**.

1. V části **Služby** **vyberte clustery HDInsight** .

1. V seznamu clusterů HDInsight, který se zobrazí, vyberte **...** vedle clusteru, který jste vytvořili pro tento kurz.

1. Vyberte **Odstranit**. Vyberte **ano**.

![Portál Azure odstraní cluster HDInsight](./media/apache-spark-ipython-notebook-machine-learning/hdinsight-azure-portal-delete-cluster.png "Odstranění clusteru HDInsight")

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak pomocí poznámkového bloku Jupyter vytvořit aplikaci Apache Spark pro strojové učení pro Azure HDInsight. V dalším kurzu se dozvíte, jak používat IntelliJ IDEA pro úlohy Sparku.

> [!div class="nextstepaction"]
> [Vytvoření aplikace Scala Maven pomocí IntelliJ](./apache-spark-create-standalone-application.md)
