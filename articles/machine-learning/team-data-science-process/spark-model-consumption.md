---
title: Zprovoznění modelů strojového učení předdefinovaných Spark | Dokumentace Microsoftu
description: Jak načíst a stanovíte jeho skóre modelů učení uložená v Azure Blob Storage (WASB) s využitím Pythonu.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
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
ms.openlocfilehash: 76b67112ade2d160f281533c4be7d41c98087398
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2018
ms.locfileid: "51345534"
---
# <a name="operationalize-spark-built-machine-learning-models"></a>Zprovoznění modelů strojového učení předdefinovaných Spark

Toto téma ukazuje, jak zprovoznit model uložené machine learning (ML) pomocí Pythonu v clusterech HDInsight Spark. Popisuje, jak načíst modely strojového učení, které se sestavily pomocí Spark MLlib a uložená v Azure Blob Storage (WASB) a jak skóre u datových sad, které jsou uložené taky v WASB. Zobrazuje předběžně zpracovat vstupní data, transformovali funkcí s použitím funkcí indexování a kódování v sadě nástrojů MLlib a vytvoření datového objektu s popiskem bod, který může sloužit jako vstup pro vyhodnocení s modely ML. Modely použité pro vyhodnocování zahrnují lineární regrese, logistické regrese, náhodných modely doménové struktury a přechodu modely zvýšení úrovně stromu.

## <a name="spark-clusters-and-jupyter-notebooks"></a>Clustery Spark a poznámkové bloky Jupyter
Postup instalace a kód pro zprovoznění modelu ML jsou k dispozici v tomto názorném postupu pro použití clusteru služby HDInsight Spark 1.6, jakož i cluster Spark 2.0. Kód pro tyto postupy také najdete v poznámkových bloků Jupyter.

### <a name="notebook-for-spark-16"></a>Poznámkový blok pro Spark 1.6
[PySpark-machine-learning-data-science-spark-model-consumption.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb) Poznámkový blok Jupyter ukazuje, jak na zprovoznění modelu uložené pomocí Pythonu v clusterech HDInsight. 

### <a name="notebook-for-spark-20"></a>Poznámkový blok pro Spark 2.0
Chcete-li upravit poznámkového bloku Jupyter pro Spark 1.6 používat s clusterem HDInsight Spark 2.0, nahradit soubor kódu Python s [tento soubor](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py). Tento kód ukazuje, jak využívat modelů vytvořených v Spark 2.0.


## <a name="prerequisites"></a>Požadavky

1. Potřebujete účet Azure a Spark 1.6 (nebo Spark 2.0) clusteru HDInsight k dokončení tohoto návodu. Zobrazit [přehled datové vědy pomocí Sparku v Azure HDInsight](spark-overview.md) pokyny o tom, jak tyto požadavky splňují. Toto téma obsahuje také popis dat taxislužby NYC 2013 se tady použít a pokyny o tom, jak spustit kód z poznámkového bloku Jupyter v clusteru Spark. 
2. Musíte také vytvořit modely strojového učení z práce prostřednictvím zohlednit tady [pro zkoumání a modelování se Sparkem](spark-data-exploration-modeling.md) téma určené pro cluster Spark 1.6 nebo poznámkových bloků Spark 2.0. 
3. Poznámkové bloky Spark 2.0 použít další datové sady pro úkol klasifikace, dobře známé letecká společnost na čas odeslání datové sady z 2011 a 2012. Popis poznámkových bloků a odkazy na nich jsou součástí [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) pro úložiště GitHub, které je obsahují. Kromě toho kód tady v propojených poznámkových bloků je obecný a by mělo fungovat jakéhokoli jiného clusteru Spark. Pokud nepoužíváte HDInsight Spark, může být mírně lišit od co je znázorněna zde kroky instalace a správy clusteru. 

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Instalační program: umístění úložiště, knihovny a přednastavený kontext Spark
Spark je možné číst a zapisovat do Azure Storage Blob (WASB). Takže existující data uložená existuje mohou být zpracovány pomocí Sparku a výsledky uložené znovu v WASB.

Pokud chcete uložit soubory nebo modely WASB, cesta musí být zadán správně. Výchozí kontejner připojené ke clusteru Spark může být odkazováno pomocí cesta začínající: *"wasb / / / / /"*. Následující příklad kódu určuje umístění dat pro čtení a cestu k adresáři modelu úložiště, do kterého se uloží výstupní modelu. 

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Nastavit cesty adresáře na umístění úložiště v WASB
Modely se ukládají do: "wasb: / / / uživatel/remoteuser/NYCTaxi/modely". Pokud tato cesta není správně nastavená, modely nejsou načtené pro vyhodnocení.

Scored výsledky byly uloženy v: "wasb: / / / uživatel/remoteuser/NYCTaxi/ScoredResults". Pokud je nesprávná cesta ke složce, výsledky nejsou uloženy v této složce.   

> [!NOTE]
> Cesta k umístění souborů lze kopírovat a vložit do zástupné symboly v tomto kódu z výstupu poslední buňku **machine-learning-data-science-spark-data-exploration-modeling.ipynb** poznámkového bloku.   
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
Nastavit kontext spark a naimportujte potřebné knihovny s následujícím kódem

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


### <a name="preset-spark-context-and-pyspark-magics"></a>Přednastavený kontext Spark a PySpark Magic
Jádra PySpark, které jsou k dispozici s poznámkovými bloky Jupyter nemají přednastavený kontext. Proto není nutné nastavovat Spark nebo vyvíjíte Hive kontexty explicitně předtím, než začnete pracovat s aplikací. Toto jsou k dispozici ve výchozím nastavení. Kontexty jsou:

* sc - pro Spark 
* kontext sqlContext - pro Hive

Jádra PySpark poskytuje některé předdefinované "Magic", které jsou speciální příkazy, které lze volat s %%. Existují dva tyto příkazy, které se používají v těchto ukázek kódu.

* **%% místní** zadat, že kód v další řádek je spuštěn místně. Kód musí být platný kód Pythonu.
* **%% sql -o <variable name>** 
* Spustí dotaz Hive proti kontext sqlContext. Pokud je předán parametr -o výsledek dotazu se ukládají v %% místní kontext Python jako Pandas dataframe.

Pro další informace o jádrech pro poznámkové bloky Jupyter a předdefinované "magics", která poskytují, naleznete v tématu [jádra dostupná pro poznámkové bloky Jupyter s HDInsight Spark Linux clusterů v HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

## <a name="ingest-data-and-create-a-cleaned-data-frame"></a>Příjem dat a vytvořit objekt frame, vyčištěnou dat
Tato část obsahuje kód pro řadu úloh potřebných k ingestování dat zohlednit. Číst v připojeném k ukázce 0,1 % taxislužby cesty a tarif souboru (uložený jako soubor TSV), formát data a pak vytvoří čisté datového rámce.

Soubory odezvy a tarif taxislužby byly připojené na základě na postup uvedený v: [vědecké zpracování týmových dat v akci: clusterů systému HDInsight Hadoop](hive-walkthrough.md) tématu.

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

Čas potřebný k provedení nad buňkou: 46.37 sekund

## <a name="prepare-data-for-scoring-in-spark"></a>Příprava dat pro hodnocení ve Spark
Tato část ukazuje, jak index, kódování a škálovat zařazené do kategorií funkcí, abyste je připravili k použití v učení MLlib pod dohledem algoritmy pro klasifikaci a regrese.

### <a name="feature-transformation-index-and-encode-categorical-features-for-input-into-models-for-scoring"></a>Funkce transformace: index a kódování zařazené do kategorií funkce pro vstup do modelů pro vyhodnocení
Tato část ukazuje, jak data zařazená do kategorií pomocí indexu `StringIndexer` a kódování funkcí `OneHotEncoder` vstup do modelů.

[StringIndexer](http://spark.apache.org/docs/latest/ml-features.html#stringindexer) kóduje sloupec řetězcového typu popisky na sloupec indexů popisek. Indexy jsou řazeny podle frekvence popisek. 

[OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) mapuje sloupec indexů popisek ke sloupci binárního vektorů s maximálně jeden – hodnotu single. Toto kódování umožňuje algoritmy, které očekávají průběžné Vážíme si toho funkce, jako je logistické regrese, použít zařazené do kategorií funkce.

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

Čas potřebný k provedení nad buňkou: 5.37 sekund

### <a name="create-rdd-objects-with-feature-arrays-for-input-into-models"></a>Vytváření objektů RDD s poli funkce pro vstup do modelů
Tato část obsahuje kód, který ukazuje, jak indexovat zařazené do kategorií textová data jako objekt RDD a horkou jeden kódovat, je možné pro trénování a testování založený na stromové architektuře modely a MLlib logistické regrese. Indexovaná data uložená v [odolné Distributed Dataset (RDD)](http://spark.apache.org/docs/latest/api/java/org/apache/spark/rdd/RDD.html) objekty. Jedná se o základní abstrakce ve Sparku. Objekt RDD představuje neměnné a dělené sadu prvků, které mohou být provozována paralelně se Sparkem.

Také obsahuje kód, který ukazuje, jak škálovat data `StandardScalar` poskytované MLlib pro použití v lineární regrese s pomocí Stochastického přechodu sestup (SGD), Oblíbené algoritmů pro trénování širokou škálu modelů strojového učení. [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) se používá funkce, které se odchylka jednotek škálování. Funkce škálování, označované také jako data normalizace, zajistí, že funkce se značně Celková uhrazená hodnotami jsou neudělil nadměrné naváží ve funkci cíle. 

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

Čas potřebný k provedení nad buňkou: 11.72 sekund

## <a name="score-with-the-logistic-regression-model-and-save-output-to-blob"></a>Stanovení skóre s Logistický regresní Model a uložte výstup do objektu blob
Kód v této části ukazuje, jak načíst Logistický regresní Model, který byl uložen v úložišti objektů blob v Azure a použít ho k předpovědět, jestli je na cesty taxíkem placené tip, skóre s metrikami standardní klasifikace, uložit a zobrazit výsledky do objektu blob stora ge. Scored výsledky jsou uloženy v objektech RDD. 

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

Čas potřebný k provedení nad buňkou: 19.22 sekund

## <a name="score-a-linear-regression-model"></a>Určení skóre modelu lineární regrese
Použili jsme [LinearRegressionWithSGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) pro trénování modelu lineární regrese pomocí Stochastického přechodu sestup (SGD) pro optimalizaci odhadnout množství tip placené. 

Kód v této části ukazuje, jak načtení modelu lineární regrese z úložiště objektů blob v Azure, stanovení skóre pomocí škálován proměnných a uložte výsledky zpět do objektu blob.

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

Čas potřebný k provedení nad buňkou: 16.63 sekund

## <a name="score-classification-and-regression-random-forest-models"></a>Skóre klasifikačních a regresních modelů doménové struktury náhodné
Kód v této části ukazuje, jak načíst uložené klasifikace a regresních modelů náhodné doménové struktury uložit ve službě Azure blob storage, hodnocení výkonu pomocí standardní třídění a regresní opatření a uložte výsledky zpět do úložiště objektů blob.

[Náhodné doménových struktur](http://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) jsou umožňující rozhodovacích stromů.  Jejich kombinací mnoha rozhodovacích stromů, aby se snížilo riziko overfitting. Náhodné doménových struktur může zpracovat zařazené do kategorií funkce rozšíření do nastavení klasifikace víc tříd, nevyžadují, aby funkce škálování a budou moct zachytit nelineárností a funkce interakce. Náhodné doménových struktur jsou jednou z nejvíce úspěšný strojového učení pro klasifikačních a regresních modelů.

[Spark.mllib](http://spark.apache.org/mllib/) podporuje náhodné doménové struktury pro binární a víc tříd klasifikaci a regrese, pomocí funkce nepřetržitý a kategorií. 

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

Čas potřebný k provedení nad buňkou: 31.07 sekund

## <a name="score-classification-and-regression-gradient-boosting-tree-models"></a>Skóre klasifikačních a regresních přechodu zvýšení úrovně stromu modelů
Kód v této části ukazuje, jak načíst klasifikačních a regresních přechodu zvýšení úrovně stromu modelů ze služby Azure blob storage, hodnocení výkonu pomocí standardní třídění a regresní opatření a uložte výsledky zpět do úložiště objektů blob. 

**Spark.mllib** podporuje GBTs pro binární klasifikaci a regrese, pomocí funkce nepřetržitý a kategorií. 

[Přechodu zvýšení skóre stromů](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) jsou umožňující rozhodovacích stromů. GBTs trénování rozhodovacích stromů zavádět postupně, chcete-li minimalizovat ztrátu funkce. GBTs zvládne zařazené do kategorií funkcí, nevyžadují, aby funkce škálování a budou moct zachytit nelineárností a funkce interakce. Můžete také používají v nastavení multiclass klasifikace.

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

Čas potřebný k provedení nad buňkou: 14.6 sekund

## <a name="clean-up-objects-from-memory-and-print-scored-file-locations"></a>Vyčištění objektů v paměti a tisk skóre umístění souborů
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

## <a name="consume-spark-models-through-a-web-interface"></a>Použití Sparku modelů prostřednictvím webového rozhraní
Spark poskytuje mechanismus pro vzdáleně pomocí komponenty s názvem Livy odeslání dávkových úloh Hive nebo interaktivní dotazy pomocí rozhraní REST. Livy je povolené ve výchozím nastavení na svém clusteru HDInsight Spark. Další informace o Livy najdete v tématu: [odesílání Sparkových úloh pomocí Livy vzdáleně](../../hdinsight/spark/apache-spark-livy-rest-interface.md). 

Vám pomůže Livy vzdáleně odešlete úlohu, jejíž batch skóre, které se soubor, který je uložený v objektu blob Azure a výsledky pak zapíše do jiného objektu blob. K tomuto účelu můžete nahrát skript Pythonu z  
[GitHub](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/Spark/Python/ConsumeGBNYCReg.py) do objektu blob z clusteru Spark. Můžete použít nástroje, jako je **Microsoft Azure Storage Explorer** nebo **AzCopy** zkopírujte skript do objektu blob clusteru. V našem případě jsme nahráli skript, který chcete ***wasb:///example/python/ConsumeGBNYCReg.py***.   

> [!NOTE]
> Přístupové klíče, které budete potřebovat najdete na portálu pro účet úložiště související s clusterem Spark. 
> 
> 

Po nahrání do tohoto umístění. Tento skript se spustí v clusteru Spark v distribuované kontextu. Načte modelu a předpovědi poběží vstupní soubory na základě modelu.  

Tento skript můžete spustit vzdáleně tím, že na Livy jednoduché požadavku HTTPS/REST.  Tady je příkaz curl k vytvoření požadavku HTTP na vzdáleně spouštět skript v jazyce Python. Nahraďte příslušnými hodnotami pro váš cluster Spark CLUSTERLOGIN, CLUSTERPASSWORD, název clusteru.

    # CURL COMMAND TO INVOKE PYTHON SCRIPT WITH HTTP REQUEST

    curl -k --user "CLUSTERLOGIN:CLUSTERPASSWORD" -X POST --data "{\"file\": \"wasb:///example/python/ConsumeGBNYCReg.py\"}" -H "Content-Type: application/json" https://CLUSTERNAME.azurehdinsight.net/livy/batches

K vyvolání úlohu Spark prostřednictvím Livy tím, že jednoduchá volání protokolu HTTPS se základním ověřováním můžete používat jakýkoli jazyk ve vzdáleném systému.   

> [!NOTE]
> Bylo by vhodné použít knihovnu Pythonu požadavky při provádění volání HTTP, ale není aktuálně nainstalovaný ve výchozím nastavení ve službě Azure Functions. Místo toho se používají tak starší knihovny HTTP.   
> 
> 

Zde je kód Python pro volání HTTP:

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


Můžete také přidat tento kód Python [Azure Functions](https://azure.microsoft.com/documentation/services/functions/) aktivovat odeslání úlohy Spark, který získává objekt blob na základě různých událostí, jako je časovač, vytvoření nebo aktualizaci objektu blob. 

Pokud dáváte přednost prostředí bezplatného klienta kódu, použijte [Azure Logic Apps](https://azure.microsoft.com/documentation/services/app-service/logic/) k vyvolání Spark dávkového vyhodnocování tak, že definujete akce HTTP na **návrhář pro Logic Apps** a nastavení jeho parametry. 

* Z webu Azure portal vytvořte novou aplikaci logiky tak, že vyberete **+ nová** -> **Web + mobilní zařízení** -> **aplikace logiky**. 
* Zobrazí se **návrhář pro Logic Apps**, zadejte název aplikace logiky a plán služby App Service.
* Vyberte akci HTTP a zadejte parametry, které je znázorněno na následujícím obrázku:

![Návrhář pro Logic Apps](./media/spark-model-consumption/spark-logica-app-client.png)

## <a name="whats-next"></a>Co dále?
**Křížového ověření a hyperparameter sweeping**: naleznete v tématu [rozšířené zkoumání a modelování se Sparkem](spark-advanced-data-exploration-modeling.md) na to, jak modely můžete pomocí křížového ověření a hyperparametrické sweeping školení.

