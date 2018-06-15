---
title: Vytvoření kanálu Apache Spark machine learning - Azure HDInsight | Microsoft Docs
description: V knihovně Apache Spark strojové učení k vytvoření datových kanálů.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: maxluk
ms.openlocfilehash: c3ff29404858a768737536e7d31c3c6858eea7d2
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
ms.locfileid: "34164546"
---
# <a name="create-a-spark-machine-learning-pipeline"></a>Vytvoření kanálu strojového učení Sparku

Apache Spark škálovatelné machine learning knihovny (MLlib) přináší modelování funkcí distribuovaném prostředí. Balíček Spark [ `spark.ml` ](http://spark.apache.org/docs/latest/ml-pipeline.html) je sada vysoké úrovně rozhraní API založený na DataFrames. Tato rozhraní API vám pomůžou vytvářet a ladit kanály praktické strojové učení.  *Spark machine learning* odkazuje na tato MLlib DataFrame rozhraní API, ne starší na základě RDD kanálu rozhraní API.

Machine learning kanálu (ML) je kompletní pracovní postup kombinace několik algoritmů strojového učení společně. Může být mnoho kroky potřebné ke zpracování a dozvědět se od data, vyžadování pořadí algoritmů. Kanály definovat fáze a řazení strojového učení procesu. Fáze kanálu v MLlib, jsou reprezentovány konkrétní posloupnost PipelineStages, kde Transformer a odhadu provádět úlohy.

Transformer je algoritmus, který transformuje jeden DataFrame na jiný pomocí `transform()` metoda. Transformer funkce může například číst jeden sloupec DataFrame, mapování na jiný sloupec a výstup nové DataFrame s namapovanou sloupce, přidá se k němu.

Odhadu je abstrakcí učení algoritmy a je odpovědná za hodí nebo k vytvoření Transformer cvičení na datovou sadu. Odhadu implementuje metodu s názvem `fit()`, která přijímá DataFrame a vytváří DataFrame, což je Transformer.

Každá instance bezstavové Transformer nebo odhadu má svůj vlastní jedinečný identifikátor, který se používá při zadání parametrů. Obě pomocí jednotného rozhraní API pro zadání těchto parametrů.

## <a name="pipeline-example"></a>Příklad kanálu

K předvedení praktická použití kanálu ML, tento příklad používá vzorku `HVAC.csv` datový soubor, který obsahuje předem načtený na výchozí úložiště pro váš cluster HDInsight, Azure Storage nebo Data Lake Store. Chcete-li zobrazit obsah souboru, přejděte na `/HdiSamples/HdiSamples/SensorSampleData/hvac` adresáře. `HVAC.csv` obsahuje sadu časy se cílem a skutečný teploty pro TVK (*vytápění, ventilace a klimatizace*) systémy v různých budovy. Cílem je trénování modelu na základě dat a vytvářet prognózy teploty o dané sestavení.

Následující kód:

1. Definuje `LabeledDocument`, která ukládá `BuildingID`, `SystemInfo` (systému identifikátor a stáří) a `label` (1.0, pokud je příliš aktivní, vytvářet 0,0 jinak).
2. Vytvoří vlastní analyzátor funkci `parseDocument` který přebírá řádek dat (řádek) a určuje, zda je sestavení "horkých" tak, že porovnáte teploty cíl skutečné teplotu.
3. Při extrahování zdroje dat se vztahuje analyzátor.
4. Vytvoří Cvičná data.

```python
# The data structure (column meanings) of the data array:
# 0 Date
# 1 Time
# 2 TargetTemp
# 3 ActualTemp
# 4 System
# 5 SystemAge
# 6 BuildingID

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
data = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
training = documents.toDF()
```

Tento příklad kanál má tři fáze: `Tokenizer` a `HashingTF` (obě transformátory), a `Logistic Regression` (odhadu).  Extrahované a analyzovaná data v `training` DataFrame toků prostřednictvím kanálu při `pipeline.fit(training)` je volána.

1. První fázi `Tokenizer`, rozdělí `SystemInfo` vstupní sloupec (který se skládá z hodnoty identifikátor a stáří systému) do `words` výstupního sloupce. Tento nový `words` sloupec přidán do DataFrame. 
2. Druhé fázi `HashingTF`, převede nové `words` sloupce do vektory funkce. Tento nový `features` sloupec přidán do DataFrame. Tyto první dvě fáze jsou transformátory. 
3. Třetí fází `LogisticRegression`, je odhadu, a proto kanálu volání `LogisticRegression.fit()` metoda k vytvoření `LogisticRegressionModel`. 

```python
tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)

# Build the pipeline with our tokenizer, hashingTF, and logistic regression stages
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(training)
```

Zobrazíte nové `words` a `features` sloupce přidané pomocí `Tokenizer` a `HashingTF` transformátory a ukázka `LogisticRegression` odhadu, spusťte `PipelineModel.transform()` metodu na původní DataFrame. V produkčním kódu dalším krokem bude předávat testu DataFrame ověření školení.

```python
peek = model.transform(training)
peek.show()

# Outputs the following:
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+
|BuildingID|SystemInfo|label|   words|            features|       rawPrediction|         probability|prediction|
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+
|         4|     13 20|  0.0|[13, 20]|(262144,[250802,2...|[0.11943986671420...|[0.52982451901740...|       0.0|
|        17|      3 20|  0.0| [3, 20]|(262144,[89074,25...|[0.17511205617446...|[0.54366648775222...|       0.0|
|        18|     17 20|  1.0|[17, 20]|(262144,[64358,25...|[0.14620993833623...|[0.53648750722548...|       0.0|
|        15|      2 23|  0.0| [2, 23]|(262144,[31351,21...|[-0.0361327091023...|[0.49096780538523...|       1.0|
|         3|      16 9|  1.0| [16, 9]|(262144,[153779,1...|[-0.0853679939336...|[0.47867095324139...|       1.0|
|         4|     13 28|  0.0|[13, 28]|(262144,[69821,25...|[0.14630166986618...|[0.53651031790592...|       0.0|
|         2|     12 24|  0.0|[12, 24]|(262144,[187043,2...|[-0.0509556393066...|[0.48726384581522...|       1.0|
|        16|     20 26|  1.0|[20, 26]|(262144,[128319,2...|[0.33829638728900...|[0.58377663577684...|       0.0|
|         9|      16 9|  1.0| [16, 9]|(262144,[153779,1...|[-0.0853679939336...|[0.47867095324139...|       1.0|
|        12|       6 5|  0.0|  [6, 5]|(262144,[18659,89...|[0.07513008136562...|[0.51877369045183...|       0.0|
|        15|     10 17|  1.0|[10, 17]|(262144,[64358,25...|[-0.0291988646553...|[0.49270080242078...|       1.0|
|         7|      2 11|  0.0| [2, 11]|(262144,[212053,2...|[0.03678030020834...|[0.50919403860812...|       0.0|
|        15|      14 2|  1.0| [14, 2]|(262144,[109681,2...|[0.06216423725633...|[0.51553605651806...|       0.0|
|         6|       3 2|  0.0|  [3, 2]|(262144,[89074,21...|[0.00565582077537...|[0.50141395142468...|       0.0|
|        20|     19 22|  0.0|[19, 22]|(262144,[139093,2...|[-0.0769288695989...|[0.48077726176073...|       1.0|
|         8|     19 11|  0.0|[19, 11]|(262144,[139093,2...|[0.04988910033929...|[0.51246968885151...|       0.0|
|         6|      15 7|  0.0| [15, 7]|(262144,[77099,20...|[0.14854929135994...|[0.53706918109610...|       0.0|
|        13|      12 5|  0.0| [12, 5]|(262144,[89689,25...|[-0.0519932532562...|[0.48700461408785...|       1.0|
|         4|      8 22|  0.0| [8, 22]|(262144,[98962,21...|[-0.0120753606650...|[0.49698119651572...|       1.0|
|         7|      17 5|  0.0| [17, 5]|(262144,[64358,89...|[-0.0721054054871...|[0.48198145477106...|       1.0|
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+

only showing top 20 rows
```

`model` Objekt teď umožňuje provádět předpovědi. V celé ukázce tento strojového učení aplikace a podrobné pokyny pro její spuštění, naleznete v části [sestavení Apache Spark strojového učení aplikace v Azure HDInsight](apache-spark-ipython-notebook-machine-learning.md).

## <a name="see-also"></a>Další informace najdete v tématech

* [Vědecké zpracování dat pomocí Scala a Spark v Azure](../../machine-learning/team-data-science-process/scala-walkthrough.md)
