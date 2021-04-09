---
title: Modely strojového učení zprovoznění Spark – vědecké zpracování týmových dat
description: Jak načíst a vyhodnotit modely učení, které jsou uložené v Azure Blob Storage (WASB) pomocí Pythonu.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 659f61734001163ada6fd26caabe2fb007892f61
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104774486"
---
# <a name="operationalize-spark-built-machine-learning-models"></a>Modely strojového učení založené na zprovoznění Spark

V tomto tématu se dozvíte, jak zprovoznění uložený model strojového učení (ML) pomocí Pythonu v clusterech HDInsight Spark. Popisuje, jak načíst modely strojového učení, které byly sestaveny pomocí Spark MLlib a uložené v Azure Blob Storage (WASB), a jak je vyhodnotit pomocí datových sad, které jsou také uloženy v WASB. Ukazuje, jak předzpracovat vstupní data, transformovat funkce pomocí funkcí indexování a kódování v sadě MLlib Toolkit a jak vytvořit datový objekt bodu s popiskem, který lze použít jako vstup pro bodování s modely ML. Mezi modely používané pro hodnocení patří lineární regrese, logistické regrese, modely náhodných struktur doménové struktury a barevné navýšení modelových stromů.

## <a name="spark-clusters-and-jupyter-notebooks"></a>Clustery Spark a poznámkové bloky Jupyter
Postup nastavení a kód pro zprovozněníí modelu ML jsou uvedené v tomto návodu pro použití clusteru HDInsight Spark 1,6 i clusteru Spark 2,0. Kód pro tyto postupy je také k dispozici v poznámkových blocích Jupyter.

### <a name="notebook-for-spark-16"></a>Poznámkový blok pro Spark 1,6
Poznámkový blok [pySpark-Machine-Learning-data-věda-Spark-model-spotřebe. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb) Jupyter ukazuje, jak zprovoznění uložený model pomocí Pythonu v clusterech HDInsight. 

### <a name="notebook-for-spark-20"></a>Poznámkový blok pro Spark 2,0
Pokud chcete upravit Poznámkový blok Jupyter pro Spark 1,6 pro použití s clusterem HDInsight Spark 2,0, nahraďte soubor kódu Pythonu [tímto souborem](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py). Tento kód ukazuje, jak spotřebovávat modely vytvořené ve Spark 2,0.


## <a name="prerequisites"></a>Požadavky

1. K dokončení tohoto Názorného postupu potřebujete účet Azure a cluster HDInsight 1,6 (nebo Spark 2,0). Pokyny, jak tyto požadavky naplnit, najdete v tématu [Přehled vědeckého zpracování dat pomocí Sparku ve službě Azure HDInsight](spark-overview.md) . Toto téma také obsahuje popis zde používaných dat taxislužby NYC 2013 a pokyny, jak spustit kód z poznámkového bloku Jupyter v clusteru Spark. 
2. Vytvářejte modely strojového učení, které se tady mají vyhodnotit, pomocí tématu [zkoumání a modelování dat pomocí Sparku](spark-data-exploration-modeling.md) pro cluster Spark 1,6 nebo pro notebooky Spark 2,0. 
3. Poznámkové bloky Spark 2,0 používají další sadu dat pro úlohu klasifikace, známou datovou datovou sadu v čase za běhu z 2011 a 2012. Popis poznámkových bloků a odkazů jsou k dispozici v [Readme.MD](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) pro úložiště GitHubu, které je obsahuje. Kromě toho kód zde a v propojených poznámkových blocích je obecný a měl by fungovat na jakémkoli clusteru Spark. Pokud nepoužíváte HDInsight Spark, kroky pro instalaci a správu clusteru se můžou mírně lišit od toho, co se tady zobrazuje. 

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Nastavení: umístění úložiště, knihovny a přednastavený kontext Spark
Spark může číst a zapisovat do Azure Storage Blob (WASB). Takže všechna vaše existující data jsou uložená pomocí Sparku a výsledky se ukládají znovu v WASB.

Aby bylo možné v WASB ukládat modely nebo soubory, musí být cesta správně zadaná. K výchozímu kontejneru připojenému ke clusteru Spark se dá odkazovat pomocí cesty začínající na: *"wasb////"*. Následující ukázka kódu určuje umístění dat, která mají být čtena, a cestu k adresáři úložiště modelu, na který je uložen výstup modelu. 

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Nastavení cest k adresářům pro umístění úložiště v WASB
Modely jsou uloženy v: "wasb:///user/remoteuser/NYCTaxi/Models". Pokud tato cesta není správně nastavená, modely se nenačtou pro bodování.

Výsledky skóre byly uloženy v: "wasb:///user/remoteuser/NYCTaxi/ScoredResults". Pokud cesta ke složce není správná, výsledky se do této složky neuloží.   

> [!NOTE]
> Umístění cesty k souboru je možné zkopírovat a vložit do zástupných symbolů v tomto kódu z výstupu poslední buňky v poznámkovém bloku **Machine-Learning-data-věda-Spark-data-prozkoumává-Modeling. ipynb** .   
> 
> 

Tady je kód pro nastavení cest k adresáři: 

```python
# LOCATION OF DATA TO BE SCORED (TEST DATA)
taxi_test_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Test.tsv";

# SET THE MODEL STORAGE DIRECTORY PATH 
# NOTE THE LAST BACKSLASH IN THIS PATH IS NEEDED
modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 

# SET SCORDED RESULT DIRECTORY PATH
# NOTE THE LAST BACKSLASH IN THIS PATH IS NEEDED
scoredResultDir = "wasb:///user/remoteuser/NYCTaxi/ScoredResults/"; 

# FILE LOCATIONS FOR THE MODELS TO BE SCORED
logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-04-1817_40_35.796789"
linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-04-1817_44_00.993832"
randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-04-1817_42_58.899412"
randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-04-1817_44_27.204734"
BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-04-1817_43_16.354770"
BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-04-1817_44_46.206262"

# RECORD START TIME
import datetime
datetime.datetime.now()
```

**VÝKONEM**

DateTime. DateTime (2016, 4, 25, 23, 56, 19, 229403)

### <a name="import-libraries"></a>Import knihoven
Nastavte kontext Spark a importujte potřebné knihovny s následujícím kódem.

```python
#IMPORT LIBRARIES
import pyspark
from pyspark import SparkConf
from pyspark import SparkContext
from pyspark.sql import SQLContext
import matplotlib
import matplotlib.pyplot as plt
from pyspark.sql import Row
from pyspark.sql.functions import UserDefinedFunction
from pyspark.sql.types import *
import atexit
from numpy import array
import numpy as np
import datetime
```

### <a name="preset-spark-context-and-pyspark-magics"></a>Přednastavený kontext Sparku a PySpark Magic
Jádra PySpark, která jsou poskytována pomocí poznámkových bloků Jupyter, mají přednastavený kontext. Proto nemusíte explicitně nastavovat kontexty Sparku nebo podregistru, než začnete pracovat s aplikací, kterou vyvíjíte. Tyto kontexty jsou ve výchozím nastavení k dispozici:

* SC – pro Spark 
* Kontext SqlContext – podregistr

Jádro PySpark poskytuje některé předdefinované "MAGICS", což jsou speciální příkazy, které můžete volat pomocí%%. Existují dva takové příkazy, které jsou použity v těchto ukázkách kódu.

* **%% místní** Určili jsme, že kód na dalších řádcích se spustí místně. Kód musí být platný kód Pythonu.
* **%% SQL-o \<variable name>** 
* Spustí dotaz na podregistr pro kontext SqlContext. Pokud je předán parametr-o, výsledek dotazu je trvalý v kontextu%% Local Python jako PANDAS dataframe.

Další informace o jádrech pro Jupyter poznámkových blocích a předdefinovaných "Magic" najdete v tématu [jádra dostupná pro poznámkové bloky Jupyter s clustery HDInsight Spark Linux v HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

## <a name="ingest-data-and-create-a-cleaned-data-frame"></a>Ingestování dat a vytvoření vyčištěného datového rámce
Tato část obsahuje kód pro řadu úkolů potřebných k ingestování dat, která mají být vyhodnocena. Přečtěte si připojenou 0,1% ukázku souboru taxislužby Trip a jízdné (uložený jako soubor. TSV), naformátujte data a pak vytvořte čistý datový rámec.

Soubory taxislužby Trip a jízdné byly připojeny na základě postupu uvedeného v tématu: [vědecký proces týmového zpracování dat v akci: použití clusterů HDInsight Hadoop](hive-walkthrough.md) .

```python
# INGEST DATA AND CREATE A CLEANED DATA FRAME

# RECORD START TIME
timestart = datetime.datetime.now()

# IMPORT FILE FROM PUBLIC BLOB
taxi_test_file = sc.textFile(taxi_test_file_loc)

# GET SCHEMA OF THE FILE FROM HEADER
taxi_header = taxi_test_file.filter(lambda l: "medallion" in l)

# PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
taxi_temp = taxi_test_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
        .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
                        float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
                        float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))

# GET SCHEMA OF THE FILE FROM HEADER
schema_string = taxi_test_file.first()
fields = [StructField(field_name, StringType(), True) for field_name in schema_string.split('\t')]
fields[7].dataType = IntegerType() #Pickup hour
fields[8].dataType = IntegerType() # Pickup week
fields[9].dataType = IntegerType() # Weekday
fields[10].dataType = IntegerType() # Passenger count
fields[11].dataType = FloatType() # Trip time in secs
fields[12].dataType = FloatType() # Trip distance
fields[19].dataType = FloatType() # Fare amount
fields[20].dataType = FloatType() # Surcharge
fields[21].dataType = FloatType() # Mta_tax
fields[22].dataType = FloatType() # Tip amount
fields[23].dataType = FloatType() # Tolls amount
fields[24].dataType = FloatType() # Total amount
fields[25].dataType = IntegerType() # Tipped or not
fields[26].dataType = IntegerType() # Tip class
taxi_schema = StructType(fields)

# CREATE DATA FRAME
taxi_df_test = sqlContext.createDataFrame(taxi_temp, taxi_schema)

# CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
taxi_df_test_cleaned = taxi_df_test.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
    .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
    .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
    .drop('direct_distance').drop('surcharge')\
    .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )

# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
taxi_df_test_cleaned.cache()
taxi_df_test_cleaned.count()

# REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
taxi_df_test_cleaned.registerTempTable("taxi_test")

# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**VÝKONEM**

Doba potřebná k provedení výše v buňce: 46,37 sekund

## <a name="prepare-data-for-scoring-in-spark"></a>Příprava dat pro vyhodnocování ve Sparku
V této části se dozvíte, jak indexovat, kódovat a škálovat funkce kategorií, abyste je připravili pro použití v algoritmech učení MLlib pod dohledem pro klasifikaci a regresi.

### <a name="feature-transformation-index-and-encode-categorical-features-for-input-into-models-for-scoring"></a>Transformace funkcí: index a kódování funkcí kategorií pro vstup do modelů pro bodování
V této části se dozvíte, jak indexovat data kategorií pomocí `StringIndexer` funkcí a zakódovat `OneHotEncoder` je do modelů.

[StringIndexer](https://spark.apache.org/docs/latest/ml-features.html#stringindexer) zakóduje řetězcový sloupec Labels na sloupec indexů popisků. Indexy jsou seřazené podle frekvencí popisků. 

[OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) mapuje sloupec indexů popisků na sloupec binárních vektorů s maximálně jednou jednou hodnotou. Toto kódování umožňuje použití algoritmů, které očekávají průběžné funkce, jako je například logistické regrese, pro funkce kategorií.

```python
#INDEX AND ONE-HOT ENCODE CATEGORICAL FEATURES

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, VectorIndexer

# CREATE FOUR BUCKETS FOR TRAFFIC TIMES
sqlStatement = """
    SELECT *,
    CASE
     WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
     WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
     WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
     WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
    END as TrafficTimeBins
    FROM taxi_test 
"""
taxi_df_test_with_newFeatures = sqlContext.sql(sqlStatement)

# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
taxi_df_test_with_newFeatures.cache()
taxi_df_test_with_newFeatures.count()

# INDEX AND ONE-HOT ENCODING
stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
model = stringIndexer.fit(taxi_df_test_with_newFeatures) # Input data-frame is the cleaned one from above
indexed = model.transform(taxi_df_test_with_newFeatures)
encoder = OneHotEncoder(dropLast=False, inputCol="vendorIndex", outputCol="vendorVec")
encoded1 = encoder.transform(indexed)

# INDEX AND ENCODE RATE_CODE
stringIndexer = StringIndexer(inputCol="rate_code", outputCol="rateIndex")
model = stringIndexer.fit(encoded1)
indexed = model.transform(encoded1)
encoder = OneHotEncoder(dropLast=False, inputCol="rateIndex", outputCol="rateVec")
encoded2 = encoder.transform(indexed)

# INDEX AND ENCODE PAYMENT_TYPE
stringIndexer = StringIndexer(inputCol="payment_type", outputCol="paymentIndex")
model = stringIndexer.fit(encoded2)
indexed = model.transform(encoded2)
encoder = OneHotEncoder(dropLast=False, inputCol="paymentIndex", outputCol="paymentVec")
encoded3 = encoder.transform(indexed)

# INDEX AND ENCODE TRAFFIC TIME BINS
stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
model = stringIndexer.fit(encoded3)
indexed = model.transform(encoded3)
encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
encodedFinal = encoder.transform(indexed)

# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**VÝKONEM**

Doba potřebná k provedení výše v buňce: 5,37 sekund

### <a name="create-rdd-objects-with-feature-arrays-for-input-into-models"></a>Vytváření RDD objektů s poli funkcí pro vstup do modelů
Tato část obsahuje kód, který ukazuje, jak indexovat kategorií textová data jako objekt RDD a jedno horké kódování, aby bylo možné ho použít ke školení a testování MLlib logistické analýzy a modelů založených na stromové struktuře. Indexovaná data jsou uložena v objektech [odolné distribuované datové sady (RDD)](https://spark.apache.org/docs/latest/api/java/org/apache/spark/rdd/RDD.html) . RDD jsou základní abstrakcí ve Sparku. Objekt RDD představuje neměnné kolekce prvků, které mohou být ovládány paralelně s Sparkem.

Obsahuje také kód, který ukazuje, jak škálovat data pomocí `StandardScalar` MLlib, který je k dispozici pro použití v lineární regresi s stochastického přechodem (SGD), oblíbeným algoritmem pro školení široké škály modelů strojového učení. [StandardScaler](https://spark.apache.org/docs/latest/api/python/reference/api/pyspark.mllib.tree.RandomForest.html#pyspark.mllib.tree.RandomForest
) se používá k horizontálnímu navýšení kapacity funkcí na jednotkovou odchylku. Škálování funkcí, označované také jako normalizace dat, zajišťuje, že funkce s rozšířenými vyplacenými hodnotami nejsou ve funkci cíl předány nadměrnému vážení. 

```python
# CREATE RDD OBJECTS WITH FEATURE ARRAYS FOR INPUT INTO MODELS

# RECORD START TIME
timestart = datetime.datetime.now()

# IMPORT LIBRARIES
from pyspark.mllib.linalg import Vectors
from pyspark.mllib.feature import StandardScaler, StandardScalerModel
from pyspark.mllib.util import MLUtils
from numpy import array

# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
def parseRowIndexingBinary(line):
    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex,
                         line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                         line.trip_distance, line.fare_amount])
    return  features

# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC REGRESSION MODELS
def parseRowOneHotBinary(line):
    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                        line.vendorVec.toArray(), line.rateVec.toArray(), 
                                        line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
    return  features

# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
def parseRowIndexingRegression(line):
    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
                         line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                         line.trip_distance, line.fare_amount])
    return  features

# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
def parseRowOneHotRegression(line):
    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                        line.vendorVec.toArray(), line.rateVec.toArray(), 
                                        line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
    return  features

# FOR BINARY CLASSIFICATION TRAINING AND TESTING
indexedTESTbinary = encodedFinal.map(parseRowIndexingBinary)
oneHotTESTbinary = encodedFinal.map(parseRowOneHotBinary)

# FOR REGRESSION CLASSIFICATION TRAINING AND TESTING
indexedTESTreg = encodedFinal.map(parseRowIndexingRegression)
oneHotTESTreg = encodedFinal.map(parseRowOneHotRegression)

# SCALING FEATURES FOR LINEARREGRESSIONWITHSGD MODEL
scaler = StandardScaler(withMean=False, withStd=True).fit(oneHotTESTreg)
oneHotTESTregScaled = scaler.transform(oneHotTESTreg)

# CACHE RDDS IN MEMORY
indexedTESTbinary.cache();
oneHotTESTbinary.cache();
indexedTESTreg.cache();
oneHotTESTreg.cache();
oneHotTESTregScaled.cache();

# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**VÝKONEM**

Doba potřebná k provedení výše v buňce: 11,72 sekund

## <a name="score-with-the-logistic-regression-model-and-save-output-to-blob"></a>Skóre pomocí modelu logistické regrese a uložení výstupu do objektu BLOB
Kód v této části ukazuje, jak načíst model logistické regrese uložený v úložišti objektů BLOB v Azure a použít ho k předpovídání, jestli se na taxislužbyé cestě vyplácí tip, vyhodnotí se standardními metrikami klasifikace a výsledky se pak uloží a vykreslí do úložiště objektů BLOB. Výsledky skóre jsou uloženy v objektech RDD. 

```python
# SCORE AND EVALUATE LOGISTIC REGRESSION MODEL

# RECORD START TIME
timestart = datetime.datetime.now()

# IMPORT LIBRARIES
from pyspark.mllib.classification import LogisticRegressionModel

## LOAD SAVED MODEL
savedModel = LogisticRegressionModel.load(sc, logisticRegFileLoc)
predictions = oneHotTESTbinary.map(lambda features: (float(savedModel.predict(features))))

## SAVE SCORED RESULTS (RDD) TO BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp + ".txt";
dirfilename = scoredResultDir + logisticregressionfilename;
predictions.saveAsTextFile(dirfilename)


# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds";
```

**VÝKONEM**

Doba potřebná k provedení výše v buňce: 19,22 sekund

## <a name="score-a-linear-regression-model"></a>Skóre modelu lineární regrese
[LinearRegressionWithSGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) používáme k vytvoření trendu lineární regrese s využitím stochastického přechodu (SGD) pro optimalizaci, který předpovídá množství placeného tipu. 

Kód v této části ukazuje, jak načíst model lineární regrese z úložiště objektů BLOB v Azure, skóre pomocí proměnných s měřítkem a pak výsledky uložit zpátky do objektu BLOB.

```python
#SCORE LINEAR REGRESSION MODEL

# RECORD START TIME
timestart = datetime.datetime.now()

#LOAD LIBRARIES
from pyspark.mllib.regression import LinearRegressionWithSGD, LinearRegressionModel

# LOAD MODEL AND SCORE USING ** SCALED VARIABLES **
savedModel = LinearRegressionModel.load(sc, linearRegFileLoc)
predictions = oneHotTESTregScaled.map(lambda features: (float(savedModel.predict(features))))

# SAVE RESULTS
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
dirfilename = scoredResultDir + linearregressionfilename;
predictions.saveAsTextFile(dirfilename)

# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**VÝKONEM**

Doba potřebná k provedení výše v buňce: 16,63 sekund

## <a name="score-classification-and-regression-random-forest-models"></a>Klasifikace skóre a regresní modely s náhodnými doménovými strukturami
Kód v této části ukazuje, jak načíst uloženou klasifikaci a regresní modely s náhodnými doménovými strukturami, které jsou uložené v Azure Blob Storage, poznamenejte si jejich výkon se standardním klasifikátorem a regresními mírami a pak výsledky uložte zpátky do úložiště objektů BLOB.

[Náhodné doménové struktury](https://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) jsou komplety rozhodovacích stromů.  Kombinují mnoho rozhodovacích stromů, aby se snížilo riziko přebudování. Náhodné doménové struktury mohou zpracovávat funkce kategorií, rozšíření na nastavení více tříd, nevyžadují škálování funkcí a mohou zachytit nelinearitu a interakce funkcí. Náhodné doménové struktury jsou jedním z nejčastěji úspěšných modelů strojového učení pro účely klasifikace a regrese.

[Spark. mllib](https://spark.apache.org/mllib/) podporuje náhodné doménové struktury pro binární klasifikaci a klasifikaci s více třídami a pro regresi pomocí kontinuálních i kategorií funkcí. 

```python
# SCORE RANDOM FOREST MODELS FOR CLASSIFICATION AND REGRESSION

# RECORD START TIME
timestart = datetime.datetime.now()

#IMPORT MLLIB LIBRARIES    
from pyspark.mllib.tree import RandomForest, RandomForestModel


# CLASSIFICATION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB
savedModel = RandomForestModel.load(sc, randomForestClassificationFileLoc)
predictions = savedModel.predict(indexedTESTbinary)

# SAVE RESULTS
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
rfclassificationfilename = "RandomForestClassification_" + datestamp + ".txt";
dirfilename = scoredResultDir + rfclassificationfilename;
predictions.saveAsTextFile(dirfilename)


# REGRESSION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB
savedModel = RandomForestModel.load(sc, randomForestRegFileLoc)
predictions = savedModel.predict(indexedTESTreg)

# SAVE RESULTS
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
rfregressionfilename = "RandomForestRegression_" + datestamp + ".txt";
dirfilename = scoredResultDir + rfregressionfilename;
predictions.saveAsTextFile(dirfilename)

# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds";
```

**VÝKONEM**

Doba potřebná k provedení výše v buňce: 31,07 sekund

## <a name="score-classification-and-regression-gradient-boosting-tree-models"></a>Klasifikace skóre a nárůst barevného přechodu regrese pro stromové modely
Kód v této části ukazuje, jak z úložiště objektů BLOB v Azure načíst klasifikaci a regresní přechodové modely, které zvyšují výkon pomocí standardního klasifikátoru a regresních měr, a pak výsledky uloží zpátky do úložiště objektů BLOB. 

**Spark. mllib** podporuje GBTS pro binární klasifikaci a regresi pomocí kontinuálních i kategorií funkcí. 

GBTS (probíhající se [rozvětvení barev](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) ) jsou komplety rozhodovacích stromů. GBTS rozhodovací stromy pro vlaky iterativním způsobem, aby se minimalizovala funkce ztráty. GBTS může zpracovávat funkce kategorií, nevyžadují škálování funkcí a dokáže zachytit nelinearitu a interakce funkcí. Tento algoritmus lze použít také v nastavení více tříd klasifikace.

```python
# SCORE GRADIENT BOOSTING TREE MODELS FOR CLASSIFICATION AND REGRESSION

# RECORD START TIME
timestart = datetime.datetime.now()

#IMPORT MLLIB LIBRARIES
from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel

# CLASSIFICATION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB

#LOAD AND SCORE THE MODEL
savedModel = GradientBoostedTreesModel.load(sc, BoostedTreeClassificationFileLoc)
predictions = savedModel.predict(indexedTESTbinary)

# SAVE RESULTS
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp + ".txt";
dirfilename = scoredResultDir + btclassificationfilename;
predictions.saveAsTextFile(dirfilename)


# REGRESSION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB

# LOAD AND SCORE MODEL 
savedModel = GradientBoostedTreesModel.load(sc, BoostedTreeRegressionFileLoc)
predictions = savedModel.predict(indexedTESTreg)

# SAVE RESULTS
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
btregressionfilename = "GradientBoostingTreeRegression_" + datestamp + ".txt";
dirfilename = scoredResultDir + btregressionfilename;
predictions.saveAsTextFile(dirfilename)


# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**VÝKONEM**

Doba potřebná k provedení výše v buňce: 14,6 sekund

## <a name="clean-up-objects-from-memory-and-print-scored-file-locations"></a>Vyčistit objekty z paměti a tisknout umístění souborů s skóre

```python
# UNPERSIST OBJECTS CACHED IN MEMORY
taxi_df_test_cleaned.unpersist()
indexedTESTbinary.unpersist();
oneHotTESTbinary.unpersist();
indexedTESTreg.unpersist();
oneHotTESTreg.unpersist();
oneHotTESTregScaled.unpersist();


# PRINT OUT PATH TO SCORED OUTPUT FILES
print "logisticRegFileLoc: " + logisticregressionfilename;
print "linearRegFileLoc: " + linearregressionfilename;
print "randomForestClassificationFileLoc: " + rfclassificationfilename;
print "randomForestRegFileLoc: " + rfregressionfilename;
print "BoostedTreeClassificationFileLoc: " + btclassificationfilename;
print "BoostedTreeRegressionFileLoc: " + btregressionfilename;
```

**VÝKONEM**

logisticRegFileLoc: LogisticRegressionWithLBFGS_2016-05-0317_22_38.953814.txt

linearRegFileLoc: LinearRegressionWithSGD_2016-05 -0317 _22_ 58.878949

randomForestClassificationFileLoc: RandomForestClassification_2016-05-0317_23_15.939247.txt

randomForestRegFileLoc: RandomForestRegression_2016-05-0317_23_31.459140.txt

BoostedTreeClassificationFileLoc: GradientBoostingTreeClassification_2016-05-0317_23_49.648334.txt

BoostedTreeRegressionFileLoc: GradientBoostingTreeRegression_2016-05-0317_23_56.860740.txt

## <a name="consume-spark-models-through-a-web-interface"></a>Využívání modelů Spark prostřednictvím webového rozhraní
Spark poskytuje mechanismus pro vzdálené odesílání dávkových úloh nebo interaktivních dotazů prostřednictvím rozhraní REST s komponentou s názvem Livy. Livy je ve výchozím nastavení povolená v clusteru HDInsight Spark. Další informace o Livy najdete v tématu [vzdálené odeslání úloh Sparku pomocí Livy](../../hdinsight/spark/apache-spark-livy-rest-interface.md). 

Livy můžete použít k vzdálenému odeslání úlohy, která dávkou vyhodnotí soubor uložený v objektu blob Azure a pak zapíše výsledky do jiného objektu BLOB. Pokud to chcete provést, nahrajte skript Pythonu z  
[GitHub](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/Spark/Python/ConsumeGBNYCReg.py) do objektu BLOB clusteru Spark. Ke zkopírování skriptu do objektu BLOB clusteru můžete použít nástroj, například **Průzkumník služby Microsoft Azure Storage** nebo **AzCopy** . V našem případě jsme nahráli skript na **_wasb:///example/Python/ConsumeGBNYCReg.py_**.   

> [!NOTE]
> Přístupové klávesy, které potřebujete, najdete na portálu pro účet úložiště, který je přidružený ke clusteru Spark. 
> 
> 

Po nahrání do tohoto umístění se tento skript spustí v rámci clusteru Spark v distribuovaném kontextu. Načte model a spustí předpovědi na vstupních souborech založených na modelu.  

Tento skript můžete vyvolat vzdáleně tím, že v Livy vytvoříte jednoduchou žádost HTTPS/REST.  Tady je příkaz složeného příkazu k vytvoření požadavku HTTP pro vzdálené vyvolání skriptu Pythonu. Nahraďte CLUSTERLOGIN, CLUSTERPASSWORD a název_clusteru odpovídajícími hodnotami pro váš cluster Spark.

```console
# CURL COMMAND TO INVOKE PYTHON SCRIPT WITH HTTP REQUEST

curl -k --user "CLUSTERLOGIN:CLUSTERPASSWORD" -X POST --data "{\"file\": \"wasb:///example/python/ConsumeGBNYCReg.py\"}" -H "Content-Type: application/json" https://CLUSTERNAME.azurehdinsight.net/livy/batches
```

K vyvolání úlohy Spark prostřednictvím Livy pomocí jednoduchého volání HTTPS se základním ověřováním můžete použít libovolný jazyk na vzdáleném systému.   

> [!NOTE]
> Při provádění tohoto volání HTTP by bylo vhodné použít knihovnu požadavků Pythonu, ale ve výchozím nastavení se v Azure Functions aktuálně neinstaluje. Místo toho se používají starší knihovny HTTP.   
> 
> 

Tady je kód Pythonu pro volání HTTP:

```python
#MAKE AN HTTPS CALL ON LIVY. 

import os

# OLDER HTTP LIBRARIES USED HERE INSTEAD OF THE REQUEST LIBRARY AS THEY ARE AVAILABLE BY DEFAULT
import httplib, urllib, base64

# REPLACE VALUE WITH ONES FOR YOUR SPARK CLUSTER
host = '<spark cluster name>.azurehdinsight.net:443'
username='<username>'
password='<password>'

#AUTHORIZATION
conn = httplib.HTTPSConnection(host)
auth = base64.encodestring('%s:%s' % (username, password)).replace('\n', '')
headers = {'Content-Type': 'application/json', 'Authorization': 'Basic %s' % auth}

# SPECIFY THE PYTHON SCRIPT TO RUN ON THE SPARK CLUSTER
# IN THE FILE PARAMETER OF THE JSON POST REQUEST BODY
r=conn.request("POST", '/livy/batches', '{"file": "wasb:///example/python/ConsumeGBNYCReg.py"}', headers )
response = conn.getresponse().read()
print(response)
conn.close()
```

Můžete taky přidat tento kód Pythonu pro [Azure Functions](https://azure.microsoft.com/documentation/services/functions/) , který aktivuje odeslání úlohy Sparku, která vyhodnotí objekt blob na základě různých událostí, jako je časovač, vytvoření nebo aktualizace objektu BLOB. 

Pokud dáváte přednost kódu bez klienta, použijte [Azure Logic Apps](https://azure.microsoft.com/documentation/services/app-service/logic/) k vyvolání vyhodnocování dávkového zpracování Spark definováním akce HTTP v **Návrháři Logic Apps** a nastavením jeho parametrů. 

* Z Azure Portal vytvořte novou aplikaci logiky tak, že vyberete **+ Nová**  ->  **web a mobilní zařízení**  ->  **Aplikace logiky**. 
* Chcete-li vyvolat **Logic Apps návrháře**, zadejte název aplikace logiky a plán App Service.
* Vyberte akci HTTP a zadejte parametry uvedené na následujícím obrázku:

![Návrhář pro Logic Apps](./media/spark-model-consumption/spark-logica-app-client.png)

## <a name="whats-next"></a>Co dále?
**Křížové ověřování a** nakládání s parametry: Přečtěte si článek [pokročilý průzkum a modelování dat pomocí Sparku](spark-advanced-data-exploration-modeling.md) , jak lze modely vyškolené pomocí křížového ověřování a s využitím úklidu Hyper-parametr.

