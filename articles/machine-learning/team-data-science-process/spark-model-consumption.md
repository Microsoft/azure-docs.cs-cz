---
title: Zprovoznit modely vytvořené Spark počítač learning | Microsoft Docs
description: Jak načíst a stanovíte jeho skóre learning modely uložené v Azure Blob Storage (WASB) s Python.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: jhubbard
editor: cgronlun
ms.assetid: 626305a2-0abf-4642-afb0-dad0f6bd24e9
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2017
ms.author: deguhath
ms.openlocfilehash: 9760144630cfce03471b3ae8fa4487e0392bfa7b
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34839669"
---
# <a name="operationalize-spark-built-machine-learning-models"></a>Zprovoznit learning modely vytvořené Spark počítače
[!INCLUDE [machine-learning-spark-modeling](../../../includes/machine-learning-spark-modeling.md)]

Toto téma ukazuje, jak zprovoznit model uložené machine learning (ML) používá Python v clusterech HDInsight Spark. Popisuje jak načíst machine learning modely, které jste vytvořili pomocí Spark MLlib a uložené v Azure Blob Storage (WASB) a jak je skóre s datové sady, které byly uloženy také v WASB. Zobrazuje postup předběžné zpracování vstupních dat, transformace funkcí s použitím funkce indexování a kódování v sadě nástrojů MLlib a jak vytvořit datový objekt s popiskem bod, který lze použít jako vstup pro vyhodnocování s modely ML. Modely použité pro vyhodnocování zahrnují lineární regrese, Logistic Regression, náhodné modely doménové struktury a modely stromu přechodu zvyšovat skóre.

## <a name="spark-clusters-and-jupyter-notebooks"></a>Clustery Spark a poznámkové bloky Jupyter
Kroky instalace a kód, který zprovoznit ML model jsou uvedené v tomto názorném postupu pro používání clusteru služby HDInsight Spark 1.6, jakož i cluster Spark 2.0. Kód pro tyto postupy je také součástí poznámkové bloky Jupyter.

### <a name="notebook-for-spark-16"></a>Poznámkový blok pro Spark 1.6
[PySpark-machine-learning-data-science-spark-model-consumption.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb) Poznámkový blok Jupyter ukazuje, jak zprovoznit model uložené v clusterech prostředí HDInsight pomocí Pythonu. 

### <a name="notebook-for-spark-20"></a>Poznámkový blok pro Spark 2.0
Pokud chcete upravit poznámkového bloku Jupyter pro 1.6 Spark pro použití s clusteru služby HDInsight Spark 2.0, nahradit soubor kód Python s [tento soubor](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py). Tento kód ukazuje, jak využívat modelů vytvořených v Spark 2.0.


## <a name="prerequisites"></a>Požadavky

1. Budete potřebovat účet Azure a Spark 1.6 (nebo Spark 2.0) clusteru HDInsight k dokončení tohoto postupu. Najdete v článku [přehled o vědecké zpracování dat pomocí Spark v Azure HDInsight](spark-overview.md) pokyny o tom, jak splnit tyto požadavky. Toto téma obsahuje také popis NYC 2013 taxíkem data použít se zde a pokyny, jak provést kód z poznámkového bloku Jupyter v clusteru Spark. 
2. Musíte taky vytvořit strojového učení modely, které se má vypočítat zde skóre projdete [zkoumání dat a modelování pomocí Spark](spark-data-exploration-modeling.md) téma je určené pro cluster Spark 1.6 nebo poznámkových bloků Spark 2.0. 
3. Poznámkové bloky Spark 2.0 používat další datové sady pro úlohu klasifikace, dobře známé letecká společnost na čas odeslání datové sady z 2011 a 2012. Popis poznámkových bloků a odkazy na ně jsou součástí [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) úložiště Githubu, které je obsahují. Kromě toho kód sem a v propojených poznámkových bloků je obecný a by měla fungovat v jakémkoliv clusteru Spark. Pokud nepoužíváte HDInsight Spark, může být mírně lišit od co je tady uvedené kroky nastavení a Správa clusteru. 

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Instalace: umístění úložiště, knihovny a kontext přednastavené Spark
Spark se bude moct číst a zapisovat do Azure úložiště objektů Blob (WASB). Takže existující data uložená existuje může zpracovat pomocí Spark a výsledky uložené v WASB znovu.

Cesta k uložení modely nebo souborů v WASB, je třeba zadat správně. Výchozí kontejner, který je připojen ke clusteru Spark se může odkazovat pomocí cesty počínaje: *"wasb / / /"*. Následující příklad kódu určuje umístění dat ke čtení a cesty k adresáři modelu úložiště, kde je uložen výstupní modelu. 

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Nastavení cesty adresáře pro umístění úložiště v WASB
Modely se ukládají do: "wasb: / / / uživatel/remoteuser/NYCTaxi/modely". Pokud tato cesta není správně nastavena, modely nenačtou pro vyhodnocování.

Scored výsledky byly uloženy v: "wasb: / / / uživatel/remoteuser/NYCTaxi/ScoredResults". Pokud cesta ke složce není v pořádku, výsledky nejsou uloženy v této složce.   

> [!NOTE]
> Cesta k umístění souborů lze kopírovat a vložit do zástupných symbolů v tento kód z výstupu poslední buňky **machine-learning-data-science-spark-data-exploration-modeling.ipynb** poznámkového bloku.   
> 
> 

Tady je kód pro nastavení cesty k adresáři: 

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

**VÝSTUP:**

DateTime.DateTime (2016, 4, 25, 23, 56, 19, 229403)

### <a name="import-libraries"></a>Importovat knihovny
Nastavit kontext spark a importovat potřebné knihovny s následujícím kódem

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


### <a name="preset-spark-context-and-pyspark-magics"></a>Předvolby kontextu Spark a Magic PySpark
Jádra PySpark, které jsou k dispozici s poznámkovými bloky Jupyter mít přednastavené kontextu. Proto není potřeba nastavit Spark nebo vývoji Hive kontexty explicitně před zahájením práce s aplikací. Tyto jsou dostupné ve výchozím nastavení. Tyto kontexty jsou:

* sc - pro Spark 
* sqlContext - pro Hive

Poskytuje jádra PySpark některé předdefinované "Magic", které jsou speciální příkazy, které můžete volat s %%. Existují dva takové příkazy, které se používají v tyto ukázky kódu.

* **%% místní** zadat, že kód v další řádek se spustí místně. Kód musí být platný kód Python.
* **%% sql -o <variable name>** 
* Provede dotaz Hive proti sqlContext. Pokud je předán parametr -o, výsledek dotazu je uchován v %% lokální kontext Python jako Pandas dataframe.

Pro další informace o jádrech pro poznámkové bloky Jupyter a předdefinovanou "magics", poskytují, najdete v části [jádra dostupná pro poznámkové bloky Jupyter s HDInsight Spark Linux clusterů v HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

## <a name="ingest-data-and-create-a-cleaned-data-frame"></a>Načítání dat a vytvořte rámeček vyčištěnými dat
Tato část obsahuje kód pro řadu úkoly vyžadované ke zpracování příjmu dat do má vypočítat skóre. Číst v připojené k ukázce 0,1 % taxíkem služební cestě a tarif souboru (uložený jako soubor TSV), formát data a poté vytvoří vyčištění dat rámce.

Taxíkem služební cestě a tarif soubory byly spojené na základě na postup uvedený v: [Team datové vědy procesu v akci: pomocí clusterů systému HDInsight Hadoop](hive-walkthrough.md) tématu.

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

**VÝSTUP:**

Doba k provedení výše buňky: 46.37 sekund

## <a name="prepare-data-for-scoring-in-spark"></a>Příprava dat pro vyhodnocování v Spark
V této části ukazuje, jak index, kódovat a škálování kategorií funkce, které chcete připravit je pro použití v MLlib pod dohledem learning algoritmy pro klasifikaci a regrese.

### <a name="feature-transformation-index-and-encode-categorical-features-for-input-into-models-for-scoring"></a>Funkce transformace: index a kódování kategorií funkce pro vstup do modely pro vyhodnocování
V této části ukazuje, jak index kategorizovaná data pomocí `StringIndexer` a kódování funkcí s `OneHotEncoder` vstup na modely.

[StringIndexer](http://spark.apache.org/docs/latest/ml-features.html#stringindexer) kóduje sloupec řetězce popisků ke sloupci popisek indexy. Indexy jsou seřazené podle četnosti popisek. 

[OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) mapuje sloupec popisek indexů ke sloupci binárního vektory, s maximálně jednu jeden – hodnotu. Toto kódování umožňuje algoritmy, které očekávají průběžné cenná funkce, jako je logistic regression, má být použita pro kategorií funkce.

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

**VÝSTUP:**

Doba k provedení výše buňky: 5.37 sekund

### <a name="create-rdd-objects-with-feature-arrays-for-input-into-models"></a>Vytvoření RDD objektů s poli funkce pro vstup na modely
Tato část obsahuje kód, který ukazuje, jak index kategorií textová data jako objekt RDD a jeden horkou zakódovat je, proto ji můžete použít pro trénování a testování MLlib logistic regression a na základě stromu modely. Indexované data jsou uložena v [odolné distribuované datovou sadu (RDD)](http://spark.apache.org/docs/latest/api/java/org/apache/spark/rdd/RDD.html) objekty. Jedná se o základní abstrakci v Spark. Objekt RDD představuje kolekci neměnné, oddílů elementů, které lze provozovat na paralelně s Spark.

Také obsahuje kód, který ukazuje, jak škálování dat pomocí `StandardScalar` poskytované MLlib pro použití v lineární regrese s Stochastického přechodu klesání (SGD), oblíbených algoritmus pro trénování širokou škálu modely machine learning. [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) se používá funkce, které chcete odchylku jednotky škálování. Funkce škálování, známé taky jako data normalizaci zajistí, že funkce široce Celková uhrazená hodnotami není zadaný nadměrné naváží ve funkci cíle. 

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

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC RERESSION MODELS
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

**VÝSTUP:**

Doba k provedení výše buňky: 11.72 sekund

## <a name="score-with-the-logistic-regression-model-and-save-output-to-blob"></a>Stanovení skóre s modelem Logistic Regression a ukládání výstupu do objektu blob
Kód v této části ukazuje, jak načíst Logistic regresní Model, který byl uložen v úložišti objektů blob v Azure a použít ho k předvídání, zda je na cestě taxíkem placené tip, skóre s standardní klasifikace metriky a potom uložte a vykreslení výsledky do objektu blob stora ge. Scored výsledky jsou uloženy v RDD objekty. 

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

**VÝSTUP:**

Doba k provedení výše buňky: 19.22 sekund

## <a name="score-a-linear-regression-model"></a>Určení skóre modelu lineární regrese
Použili jsme [LinearRegressionWithSGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) ke cvičení model lineární regrese pomocí Stochastického přechodu klesání (SGD) pro optimalizaci k předvídání množství tip placené. 

Kód v této části ukazuje, jak načíst Model lineární regrese z Azure blob storage, stanovení skóre pomocí škálovat proměnné a poté uložte výsledky zpět na objekt blob.

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


**VÝSTUP:**

Doba k provedení výše buňky: 16.63 sekund

## <a name="score-classification-and-regression-random-forest-models"></a>Stanovení skóre klasifikace a regrese náhodných modely doménové struktury
Kód v této části ukazuje, jak načíst uložené klasifikace a regrese náhodných doménové struktury modely uloží do úložiště objektů blob v Azure, stanovení skóre výkonu s standardní třídění a regrese míry a potom uložte výsledky zpět do úložiště objektů blob.

[Náhodné doménových strukturách](http://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) jsou komplety rozhodovací stromy.  Že kombinují mnoho rozhodovacích stromů, aby se snížilo riziko overfitting. Náhodné doménových struktur dokáže zpracovat kategorií funkce rozšíření pro nastavení více třídami klasifikace, nevyžadují funkce škálování a mohli zaznamenat nelineárností a funkci interakce. Náhodné doménových strukturách jsou jedním z těch nejúspěšnějších strojového učení modely pro klasifikaci a regrese.

[Spark.mllib](http://spark.apache.org/mllib/) podporuje náhodných doménové struktury pro více třídami a binární klasifikaci a pro regresní pomocí funkce nepřetržitý a kategorií. 

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

**VÝSTUP:**

Doba k provedení výše buňky: 31.07 sekund

## <a name="score-classification-and-regression-gradient-boosting-tree-models"></a>Stanovení skóre klasifikace a regrese přechodu zvyšovat skóre stromu modely
Kód v této části ukazuje, jak načíst klasifikace a regrese přechodu zvyšovat skóre stromu modely z Azure blob storage, stanovení skóre výkonu s standardní třídění a regrese opatření a poté uložte výsledky zpět do úložiště objektů blob. 

**Spark.mllib** podporuje GBTs pro binární klasifikaci a pro regresní pomocí funkce nepřetržitý a kategorií. 

[Přechodu zvyšovat skóre stromy](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) jsou komplety rozhodovací stromy. GBTs cvičení stromů rozhodnutí interaktivně, aby se minimalizoval funkci ztrátu. GBTs může zpracovat kategorií funkce, nevyžadují funkce škálování a mohli zaznamenat nelineárností a funkci interakce. Můžete také používají v nastavení multiclass klasifikace.

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

**VÝSTUP:**

Doba k provedení nad buňku: 14.6 sekund

## <a name="clean-up-objects-from-memory-and-print-scored-file-locations"></a>Vyčištění objektů z paměti a tisk skóre pro magnitudu umístění souborů
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


**VÝSTUP:**

logisticRegFileLoc: LogisticRegressionWithLBFGS_2016-05-0317_22_38.953814.txt

linearRegFileLoc: LinearRegressionWithSGD_2016-05-0317_22_58.878949

randomForestClassificationFileLoc: RandomForestClassification_2016-05-0317_23_15.939247.txt

randomForestRegFileLoc: RandomForestRegression_2016-05-0317_23_31.459140.txt

BoostedTreeClassificationFileLoc: GradientBoostingTreeClassification_2016-05-0317_23_49.648334.txt

BoostedTreeRegressionFileLoc: GradientBoostingTreeRegression_2016-05-0317_23_56.860740.txt

## <a name="consume-spark-models-through-a-web-interface"></a>Využívat modely Spark pomocí webového rozhraní
Spark poskytuje mechanismus vzdáleně odeslat úlohy batch nebo interaktivní dotazy pomocí rozhraní REST s komponenty s názvem Livy. Livy je povoleno ve výchozím nastavení v clusteru HDInsight Spark. Další informace o Livy najdete v tématu: [úlohy odeslání Spark vzdáleně pomocí Livy](../../hdinsight/spark/apache-spark-livy-rest-interface.md). 

Livy můžete vzdáleně odeslat úlohu, která dávky skóre soubor, který je uložený v objektu blob Azure a pak zapíše výsledky do jiného objektu blob. K tomuto účelu můžete odeslat skript Python z  
[GitHub](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/Spark/Python/ConsumeGBNYCReg.py) do objektu blob clusteru Spark. Můžete použít nástroje, jako je **Microsoft Azure Storage Explorer** nebo **AzCopy** zkopírujte skript do objektu blob clusteru. V našem případě jsme skript, který chcete nahrát ***wasb:///example/python/ConsumeGBNYCReg.py***.   

> [!NOTE]
> Přístupové klávesy, které budete potřebovat naleznete na portálu pro účet úložiště související s clusterem Spark. 
> 
> 

Po nahrání do tohoto umístění tento skript se spouští v rámci clusteru Spark v distribuované kontextu. Načítá modelu a spouští na vstupní soubory na základě modelu předpovědi.  

Tento skript můžete spustit vzdáleně pomocí jednoduchého požadavku HTTPS nebo REST na Livy.  Zde je příkaz curl k vytvoření žádosti HTTP určený k vyvolání skript Pythonu vzdáleně. Nahraďte příslušnými hodnotami pro váš cluster Spark CLUSTERLOGIN, CLUSTERPASSWORD, název clusteru.

    # CURL COMMAND TO INVOKE PYTHON SCRIPT WITH HTTP REQUEST

    curl -k --user "CLUSTERLOGIN:CLUSTERPASSWORD" -X POST --data "{\"file\": \"wasb:///example/python/ConsumeGBNYCReg.py\"}" -H "Content-Type: application/json" https://CLUSTERNAME.azurehdinsight.net/livy/batches

Jakýkoli jazyk ve vzdáleném systému slouží k vyvolání úlohy Spark pomocí Livy tím, že jednoduché volání HTTPS se základním ověřováním.   

> [!NOTE]
> Je vhodné na používání knihovny, Python požadavky při provádění této volání protokolu HTTP, ale není momentálně nainstalována ve výchozím nastavení v Azure Functions. Aby se místo toho používat starší knihovny HTTP.   
> 
> 

Tady je kód Python pro volání protokolu HTTP:

    #MAKE AN HTTPS CALL ON LIVY. 

    import os

    # OLDER HTTP LIBRARIES USED HERE INSTEAD OF THE REQUEST LIBRARY AS THEY ARE AVAILBLE BY DEFAULT
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


Můžete také přidat tento kód Python, aby [Azure Functions](https://azure.microsoft.com/documentation/services/functions/) k aktivaci úlohy odeslání Spark, která skóre na základě různých událostí jako časovače, vytvoření nebo aktualizace objektu blob objektu blob. 

Pokud dáváte přednost volné klientského prostředí kódu, použijte [Azure Logic Apps](https://azure.microsoft.com/documentation/services/app-service/logic/) k vyvolání Spark dávkového vyhodnocování definováním akce HTTP na **logiku aplikace Návrhář** a nastavení jeho parametry. 

* Z portálu Azure vytvořit novou aplikaci logiky výběrem **+ nový** -> **Web + mobilní** -> **aplikace logiky**. 
* Se zprovoznit **logiku aplikace Návrhář**, zadejte název aplikace logiky a plán služby App Service.
* Vyberte akci HTTP a zadejte parametry vidět na následujícím obrázku:

![Návrhář pro Logic Apps](./media/spark-model-consumption/spark-logica-app-client.png)

## <a name="whats-next"></a>Co dále?
**Křížové ověření a hyperparameter (vymetání) komínů**: najdete v části [Advanced zkoumání dat a modelování pomocí Spark](spark-advanced-data-exploration-modeling.md) na tom, jak může být modely Trénink pomocí sweeping křížové ověření a technologie hyper parametr.

