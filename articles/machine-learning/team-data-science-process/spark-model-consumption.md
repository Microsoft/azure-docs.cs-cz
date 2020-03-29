---
title: Zprovoznění modelů strojového učení se strojovým učením vytvořeným společností Spark – proces vědecké ho vědeckého oddělení týmových dat
description: Jak načíst a skóre výukové modely uložené v Azure Blob Storage (WASB) s Pythonem.
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
ms.openlocfilehash: 3f02690d7c54581ed80b521e8222d1bd5964c878
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76718544"
---
# <a name="operationalize-spark-built-machine-learning-models"></a>Zprovoznit modely strojového učení postavené společností Spark

Toto téma ukazuje, jak zprovoznit uložený model strojového učení (ML) pomocí Pythonu v clusterech HDInsight Spark. Popisuje, jak načíst modely strojového učení, které byly vytvořeny pomocí Spark MLlib a uloženy v azure blob storage (WASB) a jak je skóre pomocí datových sad, které byly také uloženy v WASB. Ukazuje, jak předběžně zpracovat vstupní data, transformovat funkce pomocí funkcí indexování a kódování v sadě nástrojů MLlib a jak vytvořit datový objekt s popiskem bodů, který lze použít jako vstup pro vyhodnocování pomocí modelů ML. Modely používané pro vyhodnocování zahrnují lineární regrese, logistickou regresi, náhodné modely doménové struktury a stromové modely pro zvýšení přechodu.

## <a name="spark-clusters-and-jupyter-notebooks"></a>Skupovací clustery a notebooky Jupyter
Kroky nastavení a kód pro zprovoznění modelu ML jsou k dispozici v tomto návodu pro použití clusteru HDInsight Spark 1.6 a clusteru Spark 2.0. Kód pro tyto postupy je také k dispozici v poznámkových blocích Jupyter.

### <a name="notebook-for-spark-16"></a>Notebook pro Spark 1,6
[Notebook pySpark-machine-learning-data-science-spark-model-consumption.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb) Jupyter ukazuje, jak zprovoznit uložený model pomocí Pythonu v clusterech HDInsight. 

### <a name="notebook-for-spark-20"></a>Notebook pro Spark 2.0
Chcete-li upravit poznámkový blok Jupyter pro Spark 1.6 tak, aby se používal s clusterem HDInsight Spark 2.0, nahraďte kódový soubor Pythonu [tímto souborem](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py). Tento kód ukazuje, jak využívat modely vytvořené v Spark 2.0.


## <a name="prerequisites"></a>Požadavky

1. K dokončení tohoto návodu potřebujete účet Azure a cluster HDInsight Spark 1.6 (nebo Spark 2.0). Pokyny, jak tyto požadavky splnit, najdete v [článku Přehled datových věd pomocí Sparku v Azure HDInsight.](spark-overview.md) Toto téma také obsahuje popis nyc 2013 Taxi data použitá zde a pokyny, jak spustit kód z notebooku Jupyter na clusteru Spark. 
2. Vytvořte modely strojového učení, které se tady budou skórovat, pomocí [tématu Zkoumání dat a modelování s tématem Spark](spark-data-exploration-modeling.md) pro cluster Spark 1.6 nebo poznámkové bloky Spark 2.0. 
3. Notebooky Spark 2.0 používají další sadu dat pro úkol klasifikace, známou datovou sadu dat letecké společnosti On-Time odletu z let 2011 a 2012. Popis poznámkových bloků a odkazy na ně jsou k dispozici v [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) pro úložiště GitHub, které je obsahuje. Kromě toho je kód zde a v propojených poznámkových blocích obecný a měl by fungovat na libovolném clusteru Spark. Pokud nepoužíváte HDInsight Spark, kroky nastavení a správy clusteru se mohou mírně lišit od toho, co je zobrazeno zde. 

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Nastavení: umístění úložišť, knihovny a přednastavený kontext Sparku
Spark je schopný číst a zapisovat do objektu blob úložiště Azure (WASB). Takže jakékoli z vašich stávajících dat, které jsou tam uloženy, mohou být zpracovány pomocí Spark a výsledky znovu uloženy v WASB.

Chcete-li uložit modely nebo soubory v WASB, cesta musí být zadánsprávně. Výchozí kontejner připojený ke clusteru Spark lze odkazovat pomocí cesty začínající: *"wasb///"*. Následující ukázka kódu určuje umístění dat, která mají být přečtena, a cestu pro adresář úložiště modelu, do kterého je uložen výstup modelu. 

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Nastavení cest adresářů pro umístění úložišť v wasb
Modely jsou uloženy v: "wasb:///user/remoteuser/NYCTaxi/Models". Pokud tato cesta není správně nastavena, modely nejsou načteny pro vyhodnocování.

Výsledky skóre byly uloženy v: "wasb:///user/remoteuser/NYCTaxi/ScoredResults". Pokud je cesta ke složce nesprávná, výsledky nejsou uloženy v této složce.   

> [!NOTE]
> Umístění cesty souboru lze zkopírovat a vložit do zástupných symbolů v tomto kódu z výstupu poslední buňky **notebooku machine-learning-data-data-exploration-modeling.ipynb.**   
> 
> 

Zde je kód pro nastavení cesty adresáře: 

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

**Výstup:**

datetime.datetime(2016, 4, 25, 23, 56, 19, 229403)

### <a name="import-libraries"></a>Import knihoven
Nastavení kontextu jiskry a import potřebných knihoven pomocí následujícího kódu

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


### <a name="preset-spark-context-and-pyspark-magics"></a>Přednastavený kontext Spark a kouzla PySpark
Jádra PySpark, která jsou dodávána s poznámkovými bloky Jupyter, mají přednastavený kontext. Proto není nutné explicitně nastavit kontexty Spark nebo Hive před zahájením práce s aplikací, kterou vyvíjíte. Tyto kontexty jsou k dispozici ve výchozím nastavení:

* sc - pro Jiskru 
* sqlContext - pro Hive

Jádro PySpark poskytuje některé předdefinované "magie", což jsou speciální příkazy, které můžete volat pomocí %%. Existují dva takové příkazy, které se používají v těchto ukázkách kódu.

* **%%místní** Zadaná, že kód v následujících řádcích je spuštěn místně. Kód musí být platný kód Pythonu.
* **%%sql -o \<název>proměnných** 
* Spustí dotaz Hive proti sqlContext. Pokud je parametr -o předán, výsledek dotazu je zachován v kontextu %%místní python jako datový rámec Pandas.

Další informace o jádrech notebooků Jupyter a předdefinovaných "kouzlech", které poskytují, naleznete [v tématu Jádra dostupná pro notebooky Jupyter s clustery HDInsight Spark Linux na HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

## <a name="ingest-data-and-create-a-cleaned-data-frame"></a>Ingestování dat a vytvoření vyčištěného datového rámce
Tato část obsahuje kód pro řadu úkolů potřebných k ingestování dat, která mají být hodnocena. Přečtěte si ve spojených 0,1% vzorku taxi výlet a tarif soubor (uložený jako soubor .tsv), formát dat a pak vytvoří čistý datový rámec.

Soubory taxi a jízdné byly spojeny na základě postupu uvedeného v: [Proces vědecké analýzy týmových dat v akci: pomocí tématu clusterů HDInsight Hadoop.](hive-walkthrough.md)

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

**Výstup:**

Doba provedená k provedení nad buňkou: 46.37 sekund

## <a name="prepare-data-for-scoring-in-spark"></a>Příprava dat pro vyhodnocování ve Sparku
Tato část ukazuje, jak indexovat, kódovat a škálovat kategorické funkce, abyste je připravili pro použití v algoritmech učení pod dohledem MLlib pro klasifikaci a regresi.

### <a name="feature-transformation-index-and-encode-categorical-features-for-input-into-models-for-scoring"></a>Transformace funkce: index a kódování kategorických funkcí pro vstup do modelů pro vyhodnocování
Tato část ukazuje, jak indexovat `StringIndexer` kategorická `OneHotEncoder` data pomocí a kódovat funkce se vstupem do modelů.

[StringIndexer](https://spark.apache.org/docs/latest/ml-features.html#stringindexer) kóduje řetězec sloupec popisků do sloupce indexů popisků. Indexy jsou seřazeny podle četnosti popisků. 

[OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) mapuje sloupec indexů popisků na sloupec binárních vektorů, maximálně s jednou jednou hodnotou. Toto kódování umožňuje algoritmy, které očekávají nepřetržité hodnotné funkce, jako je například logistická regrese, které mají být použity na kategorické funkce.

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

**Výstup:**

Doba provedená k provedení nad buňkou: 5.37 sekund

### <a name="create-rdd-objects-with-feature-arrays-for-input-into-models"></a>Vytváření objektů RDD s poli prvků pro vstup do modelů
Tato část obsahuje kód, který ukazuje, jak indexovat kategorická textová data jako objekt RDD a jednohorké kódování, aby bylo možné trénovat a testovat logistické regrese MLlib a modely založené na stromech. Indexovaná data jsou uložena v [objektech odolné distribuované datové sady (RDD).](https://spark.apache.org/docs/latest/api/java/org/apache/spark/rdd/RDD.html) RDD jsou základní abstrakce v Spark. RdD objekt představuje neměnné, rozdělené kolekce prvků, které mohou být provozovány paralelně s Spark.

Obsahuje také kód, který ukazuje, `StandardScalar` jak škálovat data s poskytované MLlib pro použití v lineární regresi s Stochastic Gradient Descent (SGD), populární algoritmus pro školení širokou škálu modelů strojového učení. [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) se používá k škálování prvků na odchylku jednotky. Funkce škálování, označované také jako normalizace dat, zajišťuje, že funkce s široce vyplacené hodnoty nejsou uvedeny nadměrné vážení v objektivní funkce. 

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

**Výstup:**

Doba provedená k provedení nad buňkou: 11.72 sekund

## <a name="score-with-the-logistic-regression-model-and-save-output-to-blob"></a>Skóre s logistické regrese modelu a uložit výstup do objektu blob
Kód v této části ukazuje, jak načíst model logistické regrese, který byl uložen v úložišti objektů blob Azure a použít jej k předvídání, zda se tip platí na cestě taxíkem, skóre s metriky standardní klasifikace a pak uložit a vykreslit výsledky do úložiště objektů blob . Výsledky skóre jsou uloženy v objektech RDD. 

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

**Výstup:**

Doba provedená k provedení nad buňkou: 19.22 sekund

## <a name="score-a-linear-regression-model"></a>Skóre lineární regresní model
Použili jsme [LinearRegressionWithSGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) trénovat lineární regresní model pomocí Stochastic Gradient Descent (SGD) pro optimalizaci předpovědět množství tip placené. 

Kód v této části ukazuje, jak načíst model lineární regrese z úložiště objektů blob Azure, skóre pomocí škálovaných proměnných a pak uložte výsledky zpět do objektu blob.

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


**Výstup:**

Doba provedená k provedení nad buňkou: 16.63 sekund

## <a name="score-classification-and-regression-random-forest-models"></a>Klasifikace skóre a regresní náhodné lesní modely
Kód v této části ukazuje, jak načíst uložené klasifikace a regrese náhodné modely doménové struktury uložené v úložišti objektů blob Azure, skóre jejich výkon se standardnítřídění a regresní opatření a pak uložit výsledky zpět do úložiště objektů blob.

[Náhodné lesy](https://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) jsou soubory rozhodovacích stromů.  Kombinují mnoho rozhodovacích stromů, aby se snížilo riziko nadměrného vybavení. Náhodné doménové struktury mohou zpracovávat kategorické prvky, rozšířit na nastavení klasifikace více tříd, nevyžadují změnu měřítka prvků a jsou schopny zachytit nelinearity a interakce prvků. Náhodné doménové struktury jsou jedním z nejúspěšnějších modelů strojového učení pro klasifikaci a regresi.

[spark.mllib](https://spark.apache.org/mllib/) podporuje náhodné doménové struktury pro binární a vícetřídní klasifikaci a pro regresi pomocí spojitých i kategorických prvků. 

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

**Výstup:**

Doba provedená k provedení nad buňkou: 31.07 sekund

## <a name="score-classification-and-regression-gradient-boosting-tree-models"></a>Klasifikace skóre a regresní gradient posílení stromové modely
Kód v této části ukazuje, jak načíst klasifikace a regrese přechodu zvýšení tree modely z úložiště objektů blob Azure, skóre jejich výkon se standardní třídění a regresní opatření a pak uložte výsledky zpět do úložiště objektů blob. 

**spark.mllib** podporuje GBTS pro binární klasifikaci a pro regresi, a to pomocí spojitých i kategorických prvků. 

[Gradient Boosting Trees](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTS) jsou soubory rozhodovacích stromů. GBTS rozhodnutí vlaku stromy iterativně minimalizovat ztrátu funkce. GBTS dokáže zpracovávat kategorické funkce, nevyžadují změnu měřítka prvků a jsou schopny zachytit nelinearity a interakce prvků. Tento algoritmus lze také použít v nastavení klasifikace více tříd.

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

**Výstup:**

Doba provedená k provedení nad buňkou: 14.6 sekund

## <a name="clean-up-objects-from-memory-and-print-scored-file-locations"></a>Vyčištění objektů z paměti a tisku umístění souborů s vyhotovenou skóre
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


**Výstup:**

logRegFileLoc: LogisticRegressionWithLBFGS_2016-05-0317_22_38.953814.txt

linearRegFileLoc: LinearRegressionWithSGD_2016-05-0317_22_58.878949

randomForestClassificationFileLoc: RandomForestClassification_2016-05-0317_23_15.939247.txt

randomForestRegFileLoc: RandomForestRegression_2016-05-0317_23_31.459140.txt

BoostedTreeClassificationFileLoc: GradientBoostingTreeClassification_2016-05-0317_23_49.648334.txt

BoostedTreeRegressionFileLoc: GradientBoostingTreeRegression_2016-05-0317_23_56.860740.txt

## <a name="consume-spark-models-through-a-web-interface"></a>Spotřebovávat modely Spark prostřednictvím webového rozhraní
Spark poskytuje mechanismus pro vzdálené odesílání dávkových úloh nebo interaktivních dotazů prostřednictvím rozhraní REST s komponentou nazvanou Livy. Livy je ve výchozím nastavení povolena v clusteru HDInsight Spark. Další informace o Livy najdete v [tématu: Odeslat úlohy Spark na dálku pomocí Livy](../../hdinsight/spark/apache-spark-livy-rest-interface.md). 

Livy můžete použít ke vzdálenému odeslání úlohy, která dávková dávka skóre soubor, který je uložen v objektu blob Azure a pak zapíše výsledky do jiného objektu blob. Chcete-li to provést, nahrajte skript Pythonu z  
[GitHub](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/Spark/Python/ConsumeGBNYCReg.py) k objektu blob clusteru Spark. Ke kopírování skriptu do objektu blob clusteru můžete použít nástroj, jako je **Microsoft Azure Storage Explorer** nebo **AzCopy.** V našem případě jsme nahráli skript na ***wasb:///example/python/ConsumeGBNYCReg.py***.   

> [!NOTE]
> Přístupové klíče, které potřebujete, najdete na portálu pro účet úložiště přidružený ke clusteru Spark. 
> 
> 

Po nahrání do tohoto umístění se tento skript spustí v clusteru Spark v distribuovaném kontextu. Načte model a spustí předpovědi na vstupní soubory založené na modelu.  

Tento skript můžete vyvolat vzdáleně vytvořením jednoduchého požadavku HTTPS/REST na Livy.  Zde je příkaz curl pro vytvoření požadavku HTTP pro vzdálené vyvolání skriptu Pythonu. Nahraďte CLUSTERLOGIN, CLUSTERPASSWORD, CLUSTERNAME příslušnými hodnotami pro cluster Spark.

    # CURL COMMAND TO INVOKE PYTHON SCRIPT WITH HTTP REQUEST

    curl -k --user "CLUSTERLOGIN:CLUSTERPASSWORD" -X POST --data "{\"file\": \"wasb:///example/python/ConsumeGBNYCReg.py\"}" -H "Content-Type: application/json" https://CLUSTERNAME.azurehdinsight.net/livy/batches

Můžete použít libovolný jazyk ve vzdáleném systému k vyvolání úlohy Spark prostřednictvím Livy vytvořením jednoduchého volání HTTPS se základním ověřováním.   

> [!NOTE]
> Bylo by vhodné použít knihovnu požadavků Pythonu při tomto volání HTTP, ale není aktuálně nainstalovánve výchozím nastavení v Azure Functions. Takže starší HTTP knihovny jsou používány místo.   
> 
> 

Zde je kód Pythonu pro volání HTTP:

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


Tento kód Pythonu můžete také přidat do [Funkce Azure](https://azure.microsoft.com/documentation/services/functions/) a aktivovat odeslání úlohy Spark, které skóre objektu blob na základě různých událostí, jako je časovač, vytvoření nebo aktualizace objektu blob. 

Pokud dáváte přednost prostředí klienta bez kódu, použijte [Azure Logic Apps](https://azure.microsoft.com/documentation/services/app-service/logic/) k vyvolání dávkového vyhodnocování Spark definováním akce HTTP v **Návrháři aplikací logiky** a nastavením jeho parametrů. 

* Na portálu Azure vytvořte novou aplikaci logiky výběrem **možnosti +New** -> **Web + Mobile** -> **Logic App**. 
* Chcete-li vyvolat **Návrhář aplikací logiky**, zadejte název aplikace logiky a plán služby App Service.
* Vyberte akci HTTP a zadejte parametry uvedené na následujícím obrázku:

![Návrhář pro Logic Apps](./media/spark-model-consumption/spark-logica-app-client.png)

## <a name="whats-next"></a>Co dále?
**Křížové ověřování a hyperparametrické zametání**: Podívejte se na [pokročilé zkoumání dat a modelování se Sparkem](spark-advanced-data-exploration-modeling.md) o tom, jak lze modely trénovat pomocí křížového ověřování a hyperparametrické zametání.

