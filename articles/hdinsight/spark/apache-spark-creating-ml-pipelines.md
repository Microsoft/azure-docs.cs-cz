---
title: Vytvoření kanálu strojového učení Apache Spark – Azure HDInsight
description: Pomocí knihovny strojového učení Apache Spark můžete vytvářet datové kanály v Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/22/2019
ms.openlocfilehash: b0de9103fd022dc74e7c75017a602eb6701686fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73494657"
---
# <a name="create-an-apache-spark-machine-learning-pipeline"></a>Vytvoření kanálu strojového učení Apache Sparku

Škálovatelná knihovna strojového učení (MLlib) apache spark přináší funkce modelování do distribuovaného prostředí. Balíček [`spark.ml`](https://spark.apache.org/docs/latest/ml-pipeline.html) Spark je sada vysoce úrovňových api vytvořených na datových rámcích. Tato api vám pomohou vytvořit a optimalizovat praktické kanály strojového učení.  *Spark strojové učení* odkazuje na toto rozhraní API založené na MLlib DataFrame, nikoli starší rozhraní API kanálu založené na RDD.

Kanál strojového učení (ML) je kompletní pracovní postup kombinující více algoritmů strojového učení dohromady. Ke zpracování a učení se z dat může být zapotřebí mnoho kroků, které vyžadují posloupnost algoritmů. Kanály definují fáze a řazení procesu strojového učení. V MLlib fáze kanálu jsou reprezentovány konkrétní posloupnost PipelineStages, kde Transformer a Estimator každý provádět úkoly.

Transformer je algoritmus, který transformuje jeden `transform()` DataFrame do jiného pomocí metody. Transformátor funkcí může například číst jeden sloupec datového rámce, mapovat jej na jiný sloupec a vyprovodit nový datový rámec s připojeným mapovaným sloupcem.

Odhad je abstrakce učení algoritmy a je zodpovědný za montáž nebo školení na datové sady k výrobě Transformer. Odhad implementuje metodu `fit()`s názvem , která přijímá DataFrame a vytváří DataFrame, což je Transformer.

Každá bezstavová instance Transformer nebo Estimator má svůj vlastní jedinečný identifikátor, který se používá při zadávání parametrů. Oba používají jednotné rozhraní API pro určení těchto parametrů.

## <a name="pipeline-example"></a>Příklad kanálu

Chcete-li demonstrovat praktické použití kanálu ML, `HVAC.csv` tento příklad používá ukázkový datový soubor, který je předinstalovaný ve výchozím úložišti pro váš cluster HDInsight, azure storage nebo úložiště datových jezer. Chcete-li zobrazit obsah souboru, `/HdiSamples/HdiSamples/SensorSampleData/hvac` přejděte do adresáře. `HVAC.csv`obsahuje soubor časů s cílovými i skutečnými teplotami pro systémy HVAC *(topení, větrání a klimatizace)* v různých budovách. Cílem je trénovat model na datech a vytvořit předpověď teploty pro danou budovu.

Následující kód:

1. Definuje `LabeledDocument`, který ukládá `BuildingID` `SystemInfo` , (identifikátor systému a stáří) a `label` (1.0, pokud je budova příliš horká, 0,0 jinak).
2. Vytvoří vlastní funkci `parseDocument` analyzátoru, která přebírá řádek (řádek) dat a určuje, zda je budova "horká" porovnáním cílové teploty se skutečnou teplotou.
3. Použije analyzátor při extrahování zdrojových dat.
4. Vytvoří trénovací data.

```python
from pyspark.ml import Pipeline
from pyspark.ml.classification import LogisticRegression
from pyspark.ml.feature import HashingTF, Tokenizer
from pyspark.sql import Row

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
data = sc.textFile(
    "wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
training = documents.toDF()
```

Tento příklad kanálu má `Tokenizer` `HashingTF` tři fáze: a `Logistic Regression` (oba Transformers) a (Odhad).  Extrahovaná a analyzovaná data `training` v datovém rámci `pipeline.fit(training)` procházejí kanálem, když je volána.

1. První fáze `Tokenizer`, rozdělí `SystemInfo` vstupní sloupec (skládající se z identifikátoru systému a věkových hodnot) do výstupního `words` sloupce. Tento `words` nový sloupec je přidán do datového rámce. 
2. Druhá fáze `HashingTF`, převede `words` nový sloupec na vektory prvků. Tento `features` nový sloupec je přidán do datového rámce. Tyto první dvě fáze jsou Transformers. 
3. Třetí fáze `LogisticRegression`, je Estimator, a proto `LogisticRegression.fit()` kanál volá `LogisticRegressionModel`metodu k vytvoření . 

```python
tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)

# Build the pipeline with our tokenizer, hashingTF, and logistic regression stages
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(training)
```

Chcete-li `words` zobrazit `features` nové a `Tokenizer` `HashingTF` sloupce přidané transformátory `LogisticRegression` a ukázku `PipelineModel.transform()` odhadu, spusťte metodu na původní dataframe. V produkčním kódu by dalším krokem bylo předat v testu DataFrame k ověření školení.

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

Objekt `model` lze nyní použít k předpovědi. Úplný ukázku této aplikace strojového učení a podrobné pokyny pro její spuštění najdete v tématu [Vytváření aplikací apache spark strojového učení na Azure HDInsight](apache-spark-ipython-notebook-machine-learning.md).

## <a name="see-also"></a>Viz také

* [Datová věda využívající Scalu a Apache Spark v Azure](../../machine-learning/team-data-science-process/scala-walkthrough.md)
