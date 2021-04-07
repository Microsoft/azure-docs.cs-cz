---
title: Vytvoření kanálu Apache Spark Machine Learning – Azure HDInsight
description: Pomocí knihovny Apache Spark Machine Learning můžete vytvářet datové kanály ve službě Azure HDInsight.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 07/22/2019
ms.openlocfilehash: e9897c7ba14e6190698cf10792a94acc759699d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98940176"
---
# <a name="create-an-apache-spark-machine-learning-pipeline"></a>Vytvoření kanálu strojového učení Apache Sparku

Škálovatelná knihovna MLlib (Machine Learning Library) Apache Spark přináší možnosti modelování distribuovanému prostředí. Balíček spark [`spark.ml`](https://spark.apache.org/docs/latest/ml-pipeline.html) je sada rozhraní API na vysoké úrovni postavená na objektech Dataframes. Tato rozhraní API vám pomůžou vytvořit a ladit praktické kanály strojového učení.  *Spark Machine Learning*  odkazuje na toto rozhraní API založené na MLlib dataframe, nikoli na starší rozhraní API kanálu založeného na RDD.

Kanál strojového učení (ML) je kompletní pracovní postup, který kombinuje více algoritmů strojového učení dohromady. Může se jednat o mnoho kroků potřebných ke zpracování a získání informací z dat, což vyžaduje sekvenci algoritmů. Kanály definují fáze a pořadí procesu strojového učení. V MLlib jsou fáze kanálu reprezentovány určitým pořadím PipelineStages, kde transformátor a Estimator provádí úlohy.

Transformátor je algoritmus, který transformuje jeden datový rámec na jiný pomocí `transform()` metody. Například transformátor funkce by mohl přečíst jeden sloupec datového rámce, namapovat ho do jiného sloupce a vytvořit výstup nového datového rámce s připojeným mapovaným sloupcem.

Estimator je abstrakce výukových algoritmů a zodpovídá za přizpůsobení nebo školení na datové sadě za účelem vytvoření transformátoru. Estimator implementuje metodu s názvem `fit()` , která přijímá datový rámec a vytvoří datový rámec, což je transformátor.

Každá Bezstavová instance transformátoru nebo Estimator má svůj vlastní jedinečný identifikátor, který se používá při zadávání parametrů. Pro zadání těchto parametrů obojí používá jednotné rozhraní API.

## <a name="pipeline-example"></a>Příklad kanálu

V tomto příkladu se k předvedení praktického použití kanálu ML používá ukázkový `HVAC.csv` datový soubor, který se načte předem do výchozího úložiště pro cluster HDInsight, a to buď Azure Storage, nebo data Lake Storage. Chcete-li zobrazit obsah souboru, přejděte do `/HdiSamples/HdiSamples/SensorSampleData/hvac` adresáře. `HVAC.csv` obsahuje sadu časů s cílovou i skutečnou teplotou pro systémy TVK (*vytápění, větrání a klimatizace*) v různých budovách. Cílem je vyškolit model na datech a vytvořit odhadovou teplotu pro danou budova.

Následující kód:

1. Definuje `LabeledDocument` , který ukládá `BuildingID` , `SystemInfo` (identifikátor a stáří systému) a a `label` (1,0, pokud je budova příliš horká, 0,0 jinak).
2. Vytvoří vlastní funkci analyzátoru `parseDocument` , která provede řádek (řádek) dat a určí, zda je budova "horká" porovnáním cílové teploty se skutečnou teplotou.
3. Použije analyzátor při extrakci zdrojových dat.
4. Vytvoří školicí data.

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

Tento příklad kanálu má tři fáze: `Tokenizer` a `HashingTF` (oba transformátory) a `Logistic Regression` (Estimator).  Extrahovaná a Analyzovaná data v `training` Dataframe přecházejí prostřednictvím kanálu při `pipeline.fit(training)` volání.

1. První fáze, `Tokenizer` , rozdělí `SystemInfo` vstupní sloupec (skládající se z identifikátoru systému a věkové hodnoty) do `words` výstupního sloupce. Tento nový `words` sloupec se přidá do datového rámce. 
2. Druhá fáze, `HashingTF` , převede nový `words` sloupec na vektory funkce. Tento nový  `features` sloupec se přidá do datového rámce. Tyto první dvě fáze jsou transformátory. 
3. Třetí fáze, `LogisticRegression` , je Estimator, a proto kanál volá `LogisticRegression.fit()` metodu pro vytvoření `LogisticRegressionModel` . 

```python
tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)

# Build the pipeline with our tokenizer, hashingTF, and logistic regression stages
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(training)
```

Chcete-li zobrazit `words` nové `features` sloupce a přidané pomocí `Tokenizer` `HashingTF` transformátorů a vzorek `LogisticRegression` Estimator, spusťte `PipelineModel.transform()` v původním dataframe metodu. V produkčním kódu bude další krok předávat do testu dataframe za účelem ověření školení.

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

`model`Objekt se teď dá použít k vytvoření předpovědi. Úplnou ukázku této aplikace pro strojové učení a podrobné pokyny pro její spuštění najdete v tématu [sestavování aplikací Apache Spark Machine Learning v Azure HDInsight](apache-spark-ipython-notebook-machine-learning.md).

## <a name="see-also"></a>Viz také

* [Datové vědy s využitím Scala a Apache Spark v Azure](../../machine-learning/team-data-science-process/scala-walkthrough.md)
