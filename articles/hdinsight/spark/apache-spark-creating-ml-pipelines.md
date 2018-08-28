---
title: Vytvoření Apache Spark machine learningu kanál – Azure HDInsight
description: Vytvářet kanály zpracování dat pomocí knihovny Apache Spark machine learning.
services: hdinsight
ms.service: hdinsight
author: maxluk
ms.author: maxluk
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: 8a1148bbb04bbad2ff25be9bcbe68d086e91027c
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43049249"
---
# <a name="create-a-spark-machine-learning-pipeline"></a>Vytvoření kanálu strojového učení Sparku

Knihovna pro Apache Spark škálovatelné machine learning (MLlib) přináší možnosti modelování pro distribuované prostředí. Balíček Spark [ `spark.ml` ](http://spark.apache.org/docs/latest/ml-pipeline.html) je sada rozhraní API vysoké úrovně využívající datových rámců. Tato rozhraní API umožňují vytvářet a ladit kanály praktické strojového učení.  *Spark machine learning* odkazuje na tento datový rámec MLlib rozhraní API, není starší na základě RDD kanálu rozhraní API.

Služby machine learning (ML) kanálu je kompletní pracovní postup kombinace několik algoritmů strojového učení najednou. Může existovat mnoho kroky potřebné ke zpracování a Učte se od dat vyžadující posloupnost algoritmy. Kanály definovat v jednotlivých fázích a řazení služby machine learning procesu. Fáze kanálu v MLlib, jsou reprezentovány konkrétní posloupnost PipelineStages, kde transformátoru a odhadu provádět úlohy.

Transformátoru je algoritmus, který transformuje jednoho datového rámce na jiný pomocí `transform()` metody. Transformer funkce může například čtení jeden sloupec datový rámec, namapovat jiný sloupec a výstupu nový datový rámec s namapovanou sloupec připojí k němu.

Odhadu je abstrakcí učení se supervizí a zodpovídá za vhodné nebo školení pro datovou sadu vytvořit transformátoru. Odhadu implementuje metodu s názvem `fit()`, který přijímá datový rámec a vytváří datový rámec, který je transformátoru.

Každá bezstavové instance transformátoru nebo odhadu má svůj vlastní jedinečný identifikátor, který se používá při zadávání parametrů. Používají jednotné rozhraní API pro zadání těchto parametrů.

## <a name="pipeline-example"></a>Příklad kanálu

Abychom si předvedli praktické použití kanálu služby ML, v tomto příkladu vzorku `HVAC.csv` datový soubor, který je už načtené v výchozí úložiště pro váš cluster HDInsight, Azure Storage nebo Data Lake Store. Chcete-li zobrazit obsah souboru, přejděte `/HdiSamples/HdiSamples/SensorSampleData/hvac` adresáře. `HVAC.csv` obsahuje sadu dob se cílem a skutečné teploty HVAC (*vytápění, ventilace a klimatizace*) systémy v různých budovy. Cílem je trénování modelu dat a vytvářet prognózy teploty pro dané sestavení.

Následující kód:

1. Definuje `LabeledDocument`, které obchody `BuildingID`, `SystemInfo` (systému identifikátor a věk) a `label` (1.0 Pokud budov je příliš horké, jinak 0,0).
2. Vytvoří vlastní analyzátor funkci `parseDocument` , který přebírá řádku (řádků) dat a určuje, zda je vytvoření "horkými" porovnáním teploty cílové skutečné teplotu.
3. Při extrahování zdrojová data se týká analyzátor.
4. Vytvoří trénovací data.

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

Tento příklad kanál má tři fáze: `Tokenizer` a `HashingTF` (obou transformátory), a `Logistic Regression` (odhad).  Extrahované a analyzovaná data v `training` datový rámec prochází přes kanál při `pipeline.fit(training)` je volána.

1. V první fázi `Tokenizer`, rozdělí `SystemInfo` vstupní sloupec (skládající se z hodnot systémového identifikátoru a stáří) `words` výstupního sloupce. Tato nová `words` sloupec se přidá do datového rámce. 
2. Druhá fáze `HashingTF`, převede novou `words` sloupce do funkce vektorů. Tato nová `features` sloupec se přidá do datového rámce. Tyto první dvě fáze jsou transformátory. 
3. Třetí fází `LogisticRegression`, která je odhadu a proto volání kanálu `LogisticRegression.fit()` metodu za účelem vytvoření `LogisticRegressionModel`. 

```python
tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)

# Build the pipeline with our tokenizer, hashingTF, and logistic regression stages
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(training)
```

Zobrazíte nové `words` a `features` sloupce přidané pomocí `Tokenizer` a `HashingTF` transformátory a vzorky `LogisticRegression` odhad, spusťte `PipelineModel.transform()` metodu na původní datového rámce. V produkčním kódu bude dalším krokem a zajistěte tak předání testu datového rámce ověření školení.

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

`model` Objekt je nyní možné k následné predikci. Úplnou ukázku této strojového učení, aplikace a podrobné pokyny pro její spuštění najdete v tématu [sestavení Apache Spark machine learningu v Azure HDInsight](apache-spark-ipython-notebook-machine-learning.md).

## <a name="see-also"></a>Další informace najdete v tématech

* [Použití Scaly a Sparku v Azure pro datové vědy](../../machine-learning/team-data-science-process/scala-walkthrough.md)
