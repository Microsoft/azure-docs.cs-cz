---
title: 'Kurz: Vytvoření aplikace strojového učení Sparku v Azure HDInsight'
description: Podrobné pokyny k vytvoření aplikace strojového učení Apache Sparku v clusterech HDInsight Spark pomocí poznámkového bloku Jupyter.
services: hdinsight
ms.service: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: jasonh
ms.openlocfilehash: 397b622e4d58687dcd893cd448295192d7a2e407
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39623098"
---
# <a name="tutorial-build-a-spark-machine-learning-application-in-hdinsight"></a>Kurz: Vytvoření aplikace strojového učení Sparku ve službě HDInsight 

V tomto kurzu zjistíte, jak pomocí poznámkového bloku Jupyter vytvořit aplikaci strojového učení Apache Sparku pro službu Azure HDInsight. 

[MLlib](https://spark.apache.org/docs/1.1.0/mllib-guide.html) je knihovna škálovatelného strojového učení Sparku, která se skládá z běžných algoritmů a nástrojů strojového učení, včetně klasifikace, regrese, clusteringu, filtrování založeného na spolupráci, snížení počtu dimenzí a také základních primitiv optimalizace.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vývoj aplikace strojového učení Sparku

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky:

Potřebujete následující položku:

* Dokončený rychlý start [Vytvoření clusteru Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="understand-the-data-set"></a>Vysvětlení datové sady

Aplikace používá ukázková data ze souboru HVAC.csv, který je standardně k dispozici ve všech clusterech. Soubor se nachází v umístění **\HdiSamples\HdiSamples\SensorSampleData\hvac**. Data ukazují cílovou teplotu a skutečnou teplotu několika budov s nainstalovanými systémy HVAC. Sloupec **System** představuje ID systému a sloupec **SystemAge** představuje počet let, kolik je systém HVAC v budově umístěný. Pomocí těchto dat můžete odhadnout, jestli budova bude teplejší nebo studenější na základě cílové teploty, daného ID systému a stáří systému.

![Snímek dat použitých v příkladu strojového učení Sparku](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-understand-data.png "Snímek dat použitých v příkladu strojového učení Sparku")

## <a name="develop-a-spark-machine-learning-application-using-spark-mllib"></a>Vývoj aplikace strojového učení Sparku pomocí knihovny Spark MLlib

V této aplikaci použijete [kanál ML](https://spark.apache.org/docs/2.2.0/ml-pipeline.html) Sparku k provedení klasifikace dokumentu. Kanály ML poskytují jednotnou sadu rozhraní API vysoké úrovně postavených nad datovými rámci, která uživatelům pomáhají vytvářet a ladit užitečné kanály strojového učení. V kanálu rozdělíte dokument na slova, převedete slova na vektory číselné funkce a nakonec pomocí vektorů číselné funkce a popisků sestavíte prediktivní model. Proveďte následující kroky a vytvořte aplikaci.

1. Vytvořte poznámkový blok Jupyter pomocí jádra PySpark. Pokyny najdete v tématu [Vytvoření poznámkového bloku Jupyter](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook).
2. Naimportujte typy potřebné pro tento scénář. Do prázdné buňky vložte následující fragment kódu a pak stiskněte **SHIFT + ENTER**. 

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
3. Načtěte data (ze souboru hvac.csv), proveďte jejich parsování a použijte je k natrénování modelu. 

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
    data = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
    training = documents.toDF()
    ```

    Ve fragmentu kódu definujete funkci, která porovnává skutečnou teplotu s cílovou teplotou. Pokud je skutečná teplota vyšší, budova je teplá a označí se hodnotou **1.0**. V opačném případě je budova studená a označí se hodnotou **0.0**. 

4. Nakonfigurujte kanál strojového učení Sparku, který se skládá ze tří fází: tokenizer, hashingTF a lr. 

    ```PySpark
    tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
    hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
    lr = LogisticRegression(maxIter=10, regParam=0.01)
    pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])
    ```

    Další informace o kanálu a jak funguje najdete v článku věnovaném <a href="http://spark.apache.org/docs/latest/ml-guide.html#how-it-works" target="_blank">kanálu strojového učení Sparku</a>.

5. Přizpůsobte kanál pro trénovací dokument.
   
    ```PySpark
    model = pipeline.fit(training)
    ```

6. Ověřte trénovací dokument a zkontrolujte pokrok s aplikací.
   
    ```PySpark
    training.show()
    ```
   
    Výstup je podobný tomuto:

    ```
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

    ![Snímek výstupních dat pro příklad strojového učení Sparku](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-output-data.png "Snímek výstupních dat pro příklad strojového učení Sparku")

    Všimněte si, že skutečná teplota je nižší než cílová teplota. To značí, že je budova studená. Proto je v trénovacím výstupu hodnota **label** na prvním řádku **0.0**, což znamená, že budova není teplá.

7. Připravte datovou sadu, pro kterou chcete natrénovaný model spustit. Provedete to tak, že předáte ID systému a stáří systému (v trénovacím výstupu označené jako **SystemInfo**) a model odhadne, jestli budova s tímto ID systému a stářím systému bude teplejší (označení 1.0) nebo studenější (označení 0.0).
   
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
8. Nakonec na základě testovacích dat vytvořte předpovědi. 
   
    ```PySpark
    # Make predictions on test documents and print columns of interest
    prediction = model.transform(test)
    selected = prediction.select("SystemInfo", "prediction", "probability")
    for row in selected.collect():
        print row
    ```

    Výstup je podobný tomuto:

    ```   
    Row(SystemInfo=u'20 25', prediction=1.0, probability=DenseVector([0.4999, 0.5001]))
    Row(SystemInfo=u'4 15', prediction=0.0, probability=DenseVector([0.5016, 0.4984]))
    Row(SystemInfo=u'16 9', prediction=1.0, probability=DenseVector([0.4785, 0.5215]))
    Row(SystemInfo=u'9 22', prediction=1.0, probability=DenseVector([0.4549, 0.5451]))
    Row(SystemInfo=u'17 10', prediction=1.0, probability=DenseVector([0.4925, 0.5075]))
    Row(SystemInfo=u'7 22', prediction=0.0, probability=DenseVector([0.5015, 0.4985]))
    ```
   
   Na prvním řádku předpovědi vidíte, že v případě systému HVAC s ID 20 a stářím 25 let je budova teplá (**prediction=1.0**). První hodnota DenseVector (0.49999) odpovídá předpovědi 0.0 a druhá hodnota (0.5001) odpovídá předpovědi 1.0. Přestože je druhá hodnota pouze nepatrně vyšší, model ve výstupu zobrazí **prediction=1.0**.
10. Vypněte poznámkový blok a uvolněte tak prostředky. Provedete to tak, že v nabídce **Soubor** poznámkového bloku vyberete **Zavřít a zastavit**. Tato akce poznámkový blok vypne a zavře.

## <a name="use-anaconda-scikit-learn-library-for-spark-machine-learning"></a>Použití knihovny Anaconda scikit-learn pro strojové učení Sparku
Clustery Apache Spark ve službě HDInsight obsahují knihovny Anaconda. Jejich součástí je také knihovna **scikit-learn** pro strojové učení. Knihovna obsahuje také různé datové sady, které můžete použít k vytváření ukázkových aplikací přímo z poznámkového bloku Jupyter. Příklady použití knihovny scikit-learn najdete tady: [http://scikit-learn.org/stable/auto_examples/index.html](http://scikit-learn.org/stable/auto_examples/index.html).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

* Vývoj aplikace strojového učení Sparku

V dalším kurzu se dozvíte, jak používat IntelliJ IDEA pro úlohy Sparku. 

> [!div class="nextstepaction"]
> [Vytvoření aplikace Scala Maven pomocí IntelliJ](./apache-spark-create-standalone-application.md)

