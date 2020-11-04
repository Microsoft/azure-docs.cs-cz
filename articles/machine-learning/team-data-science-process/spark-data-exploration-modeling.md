---
title: Zkoumání a modelování dat pomocí procesu Spark-Team pro datové vědy
description: Prezentuje možnosti zkoumání a modelování dat sady Spark MLlib Toolkit v HDInsight Spark.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: sample
ms.date: 06/03/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath, contperfq4
ms.openlocfilehash: b7a361319c3fc6c80c6dac80c48fb10155a3ff5b
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93314840"
---
# <a name="data-exploration-and-modeling-with-spark"></a>Zkoumání a modelování dat pomocí Spark

Naučte se používat HDInsight Spark k výuce modelů strojového učení pro předpověď taxislužby tarifů pomocí Spark MLlib.

Tato ukázka předvádí různé kroky v rámci [vědeckého zpracování týmových dat](./index.yml). K načtení, prozkoumávání a přípravě dat se používá podmnožina datové sady NYC taxislužby Trip a tarif 2013. Pak se pomocí Sparku MLlib zaměří binární klasifikace a regresní modely, aby se předpovídá, jestli se pro danou cestu má vyplatit tip, a odhadněte částku tipu.

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto Názorného postupu potřebujete účet Azure a cluster HDInsight 1,6 (nebo Spark 2,0). Pokyny, jak tyto požadavky naplnit, najdete v tématu [Přehled vědeckého zpracování dat pomocí Sparku ve službě Azure HDInsight](spark-overview.md) . Toto téma také obsahuje popis zde používaných dat taxislužby NYC 2013 a pokyny, jak spustit kód z poznámkového bloku Jupyter v clusteru Spark. 

### <a name="spark-clusters-and-notebooks"></a>Clustery Spark a poznámkové bloky

Kroky a kód pro instalaci najdete v tomto návodu pro použití HDInsight Spark 1,6. Ale Jupyter poznámkové bloky jsou k dispozici pro clustery HDInsight Spark 1,6 a Spark 2,0. Popis poznámkových bloků a odkazů jsou k dispozici v [Readme.MD](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) pro úložiště GitHubu, které je obsahuje. Kromě toho kód zde a v propojených poznámkových blocích je obecný a měl by fungovat na jakémkoli clusteru Spark. Pokud nepoužíváte HDInsight Spark, kroky pro instalaci a správu clusteru se můžou mírně lišit od toho, co se tady zobrazuje. Tady jsou odkazy na poznámkové bloky Jupyter pro Spark 1,6 (ke spuštění v jádru pySpark serveru Jupyter Notebook) a Spark 2,0 (ke spuštění v jádru pySpark3 serveru Jupyter Notebook):

- [Notebooky Spark 1,6](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/): Poskytněte informace o tom, jak provádět zkoumání dat, modelování a bodování s několika různými algoritmy.
- [Notebooky Spark 2,0](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/): Poskytněte informace o tom, jak provádět úlohy regrese a klasifikace. Datové sady se mohou lišit, ale postupy a koncepty platí pro různé datové sady.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

> [!NOTE]
> Níže uvedené popisy souvisejí s používáním Spark 1,6. Pro verze Spark 2,0 prosím použijte poznámkové bloky popsané a propojené výše. 

## <a name="setup"></a>Nastavení

Spark dokáže číst a zapisovat do Azure Storage Blob (označuje se také jako WASB). Takže všechna vaše existující data jsou uložená pomocí Sparku a výsledky se ukládají znovu v WASB.

Aby bylo možné v WASB ukládat modely nebo soubory, musí být cesta správně zadaná. K výchozímu kontejneru připojenému ke clusteru Spark se dá odkazovat pomocí cesty začínající na: "wasb:///". Na další umístění odkazuje "wasb://".

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Nastavení cest k adresářům pro umístění úložiště v WASB

Následující ukázka kódu určuje umístění dat, která mají být čtena, a cestu k adresáři úložiště modelu, do nějž je výstup modelu uložen:

```python
# SET PATHS TO FILE LOCATIONS: DATA AND MODEL STORAGE

# LOCATION OF TRAINING DATA
taxi_train_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv";

# SET THE MODEL STORAGE DIRECTORY PATH 
# NOTE THAT THE FINAL BACKSLASH IN THE PATH IS NEEDED.
modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 
```

### <a name="import-libraries"></a>Import knihoven

Nastavení také vyžaduje import nezbytných knihoven. Nastavte kontext Spark a importujte potřebné knihovny s následujícím kódem:

```python
# IMPORT LIBRARIES
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

Jádra PySpark, která jsou poskytována pomocí poznámkových bloků Jupyter, mají přednastavený kontext. Takže nemusíte explicitně nastavovat kontexty Sparku nebo podregistru, než začnete pracovat s aplikací, kterou vyvíjíte. Ve výchozím nastavení jsou tyto kontexty k dispozici pro vás. Tyto kontexty jsou:

* SC – pro Spark 
* Kontext SqlContext – podregistr

Jádro PySpark poskytuje některé předdefinované "MAGICS", což jsou speciální příkazy, které můžete volat pomocí%%. Existují dva takové příkazy, které jsou použity v těchto ukázkách kódu.

* **%% místní** Určuje, že kód v následných řádcích má být proveden místně. Kód musí být platný kód Pythonu.
* **%% SQL-o \<variable name>** Spustí dotaz na podregistr pro kontext SqlContext. Pokud je předán parametr-o, výsledek dotazu je trvalý v kontextu%% Local Python jako PANDAS dataframe.

Další informace o jádrech poznámkových bloků Jupyter a předdefinovaných "MAGICS" najdete v tématu [jádra dostupná pro poznámkové bloky Jupyter s clustery HDInsight Spark Linux v HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

## <a name="load-the-data"></a>Načtení dat

Prvním krokem v rámci vědeckého zpracování dat je ingestování dat, která se mají analyzovat ze zdrojů, kde se nachází v prostředí pro zkoumání a modelování dat. Prostředí je Spark v tomto návodu. Tato část obsahuje kód pro dokončení řady úloh:

* ingestování ukázky dat pro modelování
* číst ve vstupní datové sadě (uložené jako soubor. TSV)
* formátování a vyčištění dat
* vytváření objektů a jejich ukládání do mezipaměti (RDD nebo datových rámců) v paměti
* Zaregistrujte ho jako dočasnou tabulku v kontextu SQL.

Zde je kód pro přijímání dat.

```python
# INGEST DATA

# RECORD START TIME
timestart = datetime.datetime.now()

# IMPORT FILE FROM PUBLIC BLOB
taxi_train_file = sc.textFile(taxi_train_file_loc)

# GET SCHEMA OF THE FILE FROM HEADER
schema_string = taxi_train_file.first()
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

# PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
taxi_header = taxi_train_file.filter(lambda l: "medallion" in l)
taxi_temp = taxi_train_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
        .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
                        float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
                        float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))


# CREATE DATA FRAME
taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)

# CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
taxi_df_train_cleaned = taxi_train_df.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
    .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
    .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
    .drop('direct_distance').drop('surcharge')\
    .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )


# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
taxi_df_train_cleaned.cache()
taxi_df_train_cleaned.count()

# REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
taxi_df_train_cleaned.registerTempTable("taxi_train")

# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds";
```

**VÝKONEM**

Doba potřebná k provedení výše v buňce: 51,72 sekund

## <a name="explore-the-data"></a>Prozkoumání dat

Až se data přenesou do Sparku, další krok v rámci vědeckého zpracování dat je získat hlubší porozumění datům prostřednictvím průzkumu a vizualizace. V této části prověříme taxislužby data pomocí dotazů SQL a vykreslíte cílové proměnné a funkce pro vizuální kontrolu. Konkrétně vykreslíme frekvenci počtu cestujících v taxislužby TRIPS, četnost částek v tipech a způsob, jakým se budou zobrazovat tipy podle množství a typu platby.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Sestrojit histogram frekvencí počtu cestujících v ukázce taxislužby cest

Tento kód a následné fragmenty kódu používají SQL Magic k dotazování na ukázku a místní Magic k vykreslení dat.

* **SQL Magic ( `%%sql` )** jádro HDInsight PySpark podporuje jednoduché vložené dotazy HiveQL proti kontext SqlContext. Argument (-o VARIABLE_NAME) uchovává výstup dotazu SQL jako PANDAS dataframe na serveru Jupyter. Toto nastavení zpřístupní výstup v místním režimu.
* **`%%local` Magic** se používá ke spouštění kódu místně na serveru Jupyter, což je hlavnímu uzlu clusteru HDInsight. Obvykle použijete `%%local` Magic ve spojení s `%%sql` parametrem Magic s parametrem-o. Parametr-o by zachoval výstup dotazu SQL místně a potom%% Local Magic bude aktivovat další sadu fragmentů kódu pro místní spuštění s výstupem dotazů SQL, které jsou místně uložené.

Výstup je automaticky vizuálů po spuštění kódu.

Tento dotaz načte počet cest podle počtu cestujících. 

```sql
# PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

# HIVEQL QUERY AGAINST THE sqlContext
%%sql -q -o sqlResults
SELECT passenger_count, COUNT(*) as trip_counts 
FROM taxi_train 
WHERE passenger_count > 0 and passenger_count < 7 
GROUP BY passenger_count 
```

Tento kód vytvoří lokální datový rámec z výstupu dotazu a vykreslí data. `%%local`Magic vytvoří lokální datový rámec, `sqlResults` který se dá použít k vykreslení pomocí matplotlib. 

> [!NOTE]
> Tento PySpark Magic se v tomto průvodci používá několikrát. Pokud je objem dat velký, měli byste vzorkovat, abyste vytvořili rámec dat, který se může vejít do místní paměti.

```python
#CREATE LOCAL DATA-FRAME AND USE FOR MATPLOTLIB PLOTTING

# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
%%local

# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
# CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
sqlResults
```

Tady je kód pro sevykreslení cest podle počtu cestujících.

```python
# PLOT PASSENGER NUMBER VS. TRIP COUNTS
%%local
import matplotlib.pyplot as plt
%matplotlib inline

x_labels = sqlResults['passenger_count'].values
fig = sqlResults[['trip_counts']].plot(kind='bar', facecolor='lightblue')
fig.set_xticklabels(x_labels)
fig.set_title('Counts of trips by passenger count')
fig.set_xlabel('Passenger count in trips')
fig.set_ylabel('Trip counts')
plt.show()
```

**VÝKONEM**

![Četnost cest podle počtu cestujících](./media/spark-data-exploration-modeling/trip-freqency-by-passenger-count.png)

Pomocí tlačítek nabídky **typ** v poznámkovém bloku můžete vybrat mezi několika různými typy vizualizací (tabulka, výsečový, spojnicový, plošný nebo pruhový). Pruhový graf je zobrazen zde.

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Znázorněte histogramy o částkách špičky a o tom, jak se velikost hrotu mění podle počtu cestujících a částek tarifů.

Pro ukázková data použijte dotaz SQL.

```sql
# PLOT HISTOGRAM OF TIP AMOUNTS AND VARIATION BY PASSENGER COUNT AND PAYMENT TYPE

# HIVEQL QUERY AGAINST THE sqlContext
%%sql -q -o sqlResults
SELECT fare_amount, passenger_count, tip_amount, tipped 
FROM taxi_train 
WHERE passenger_count > 0 
AND passenger_count < 7 
AND fare_amount > 0 
AND fare_amount < 200 
AND payment_type in ('CSH', 'CRD') 
AND tip_amount > 0 
AND tip_amount < 25
```

Tato buňka s kódem používá k vytvoření tří dat dotaz SQL.

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
%%local

# HISTOGRAM OF TIP AMOUNTS AND PASSENGER COUNT
ax1 = sqlResults[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
ax1.set_title('Tip amount distribution')
ax1.set_xlabel('Tip Amount ($)')
ax1.set_ylabel('Counts')
plt.suptitle('')
plt.show()

# TIP BY PASSENGER COUNT
ax2 = sqlResults.boxplot(column=['tip_amount'], by=['passenger_count'])
ax2.set_title('Tip amount by Passenger count')
ax2.set_xlabel('Passenger count')
ax2.set_ylabel('Tip Amount ($)')
plt.suptitle('')
plt.show()

# TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
ax = sqlResults.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(sqlResults.passenger_count))
ax.set_title('Tip amount by Fare amount')
ax.set_xlabel('Fare Amount ($)')
ax.set_ylabel('Tip Amount ($)')
plt.axis([-2, 100, -2, 20])
plt.show()
```

**VÝKONEM** 

![Rozdělení částky hrotu](./media/spark-data-exploration-modeling/tip-amount-distribution.png)

![Částka tipu podle počtu cestujících](./media/spark-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Částka tipu podle částky tarifů](./media/spark-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="prepare-the-data"></a>Příprava dat

Tato část popisuje a poskytuje kód pro postupy, které slouží k přípravě dat pro použití v modelování ML. Ukazuje, jak provádět následující úlohy:

* Vytvoření nové funkce binningu hodinami v časových intervalech provozu
* Indexování a kódování funkcí kategorií
* Vytvořit objekty s popiskem pro vstup do funkcí ML
* Vytvoření náhodného dílčího vzorkování dat a jejich rozdělení do sad pro školení a testování
* měření rozsahu prvků,
* Objekty mezipaměti v paměti

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Vytvoření nové funkce binningu hodinami v časových intervalech provozu

Tento kód ukazuje, jak vytvořit novou funkci tím, že binningu hodiny do časových intervalů provozu a následně do mezipaměti výsledný datový rámec v paměti. V případě opakovaného použití odolného distribuovaných datových sad (RDD) a datových rámců ukládá do mezipaměti vedoucí lepší doby spuštění. Proto ukládáme do mezipaměti RDD a datové rámce v několika fázích tohoto návodu. 

```python
# CREATE FOUR BUCKETS FOR TRAFFIC TIMES
sqlStatement = """
    SELECT *,
    CASE
     WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
     WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
     WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
     WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
    END as TrafficTimeBins
    FROM taxi_train 
"""
taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)

# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
# THE .COUNT() GOES THROUGH THE ENTIRE DATA-FRAME,
# MATERIALIZES IT IN MEMORY, AND GIVES THE COUNT OF ROWS.
taxi_df_train_with_newFeatures.cache()
taxi_df_train_with_newFeatures.count()
```

**VÝKONEM**

126050

### <a name="index-and-encode-categorical-features-for-input-into-modeling-functions"></a>Indexování a kódování funkcí kategorií pro vstup do modelovacích funkcí

V této části se dozvíte, jak indexovat nebo kódovat funkce kategorií pro vstup do funkcí modelování. Funkce modelování a předpovědi MLlib vyžadují, aby byly funkce se vstupními daty kategorií indexované nebo kódované před použitím. V závislosti na modelu je třeba je indexovat nebo kódovat různými způsoby:  

* **Modelování na základě stromové struktury** vyžaduje, aby byly kategorie kódovány jako číselné hodnoty (například funkce se třemi kategoriemi může být kódována s hodnotou 0, 1, 2). Tento algoritmus je poskytován funkcí [StringIndexer](https://spark.apache.org/docs/latest/ml-features.html#stringindexer) MLlib. Tato funkce zakóduje řetězcový sloupec popisků na sloupec indexů popisků, které jsou seřazeny podle frekvencí popisků. I když jsou indexované s numerickými hodnotami pro vstup a zpracování dat, lze určit algoritmy založené na stromu, aby byly odpovídajícím způsobem považovány za kategorie. 
* **Logistické a lineární regresní modely** vyžadují jedno horké kódování, kde například funkce se třemi kategoriemi může být rozbalená na tři sloupce funkce, přičemž každý z nich obsahuje 0 nebo 1 v závislosti na kategorii pozorování. MLlib poskytuje funkci [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) k provedení kódování s jedním aktivním systémem. Tento kodér mapuje sloupec indexů popisků na sloupec binárních vektorů s maximálně jednou jednou hodnotou. Toto kódování umožňuje použití algoritmů, které očekávají funkce s numerickou hodnotou, jako je například Logistická regrese, pro funkce kategorií.

Tady je kód pro indexování a kódování funkcí kategorií:

```python
# INDEX AND ENCODE CATEGORICAL FEATURES

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES    
from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, VectorIndexer

# INDEX AND ENCODE VENDOR_ID
stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
model = stringIndexer.fit(taxi_df_train_with_newFeatures) # Input data-frame is the cleaned one from above
indexed = model.transform(taxi_df_train_with_newFeatures)
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

# INDEX AND TRAFFIC TIME BINS
stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
model = stringIndexer.fit(encoded3)
indexed = model.transform(encoded3)
encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
encodedFinal = encoder.transform(indexed)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**VÝKONEM**

Doba potřebná k provedení výše v buňce: 1,28 sekund

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Vytvořit objekty s popiskem pro vstup do funkcí ML

Tato část obsahuje kód, který ukazuje, jak indexovat textová data kategorií jako datový typ bodu s popiskem a kódovat, aby je bylo možné použít ke školení a testování MLlib logistické regrese a dalších modelů klasifikace. Objekty bodu s popiskem jsou odolné distribuované datové sady (RDD) formátované způsobem, který je potřeba pro vstupní data z většiny algoritmů ML v MLlib. [Označený bod](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) je místní vektor, buď hustý, nebo zhuštěný, přidružený k popisku/odpovědi.  

Tato část obsahuje kód, který ukazuje, jak indexovat textová data kategorií jako datový typ [bodu s popiskem](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) a kódovat, aby je bylo možné použít ke školení a testování MLlib logistické regrese a dalších modelů klasifikace. Objekty bodu s popiskem jsou odolné distribuované datové sady (RDD) sestávající z popisku (proměnné Target/Response) a vektoru funkce. Tento formát je potřebný jako vstup v mnoha algoritmech ML v MLlib.

Zde je kód pro indexaci a kódování textových funkcí pro binární klasifikaci.

```python
# FUNCTIONS FOR BINARY CLASSIFICATION

# LOAD LIBRARIES
from pyspark.mllib.regression import LabeledPoint
from numpy import array

# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
def parseRowIndexingBinary(line):
    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex,
                         line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                         line.trip_distance, line.fare_amount])
    labPt = LabeledPoint(line.tipped, features)
    return  labPt

# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC REGRESSION MODELS
def parseRowOneHotBinary(line):
    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                        line.vendorVec.toArray(), line.rateVec.toArray(), 
                                        line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
    labPt = LabeledPoint(line.tipped, features)
    return  labPt
```

Zde je kód pro kódování a indexování kategorií textových funkcí pro analýzu lineární regrese.

```python
# FUNCTIONS FOR REGRESSION WITH TIP AMOUNT AS TARGET VARIABLE

# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
def parseRowIndexingRegression(line):
    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
                         line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                         line.trip_distance, line.fare_amount])

    labPt = LabeledPoint(line.tip_amount, features)
    return  labPt

# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
def parseRowOneHotRegression(line):
    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                        line.vendorVec.toArray(), line.rateVec.toArray(), 
                                        line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
    labPt = LabeledPoint(line.tip_amount, features)
    return  labPt
```

### <a name="create-a-random-subsampling-of-the-data-and-split-it-into-training-and-testing-sets"></a>Vytvoření náhodného dílčího vzorkování dat a jejich rozdělení do sad pro školení a testování

Tento kód vytvoří náhodný vzorkování dat (tady se používá 25%). I když to není v tomto příkladu vyžadováno z důvodu velikosti datové sady, ukážeme vám, jak zde můžete vzorkovat, abyste věděli, jak ho v případě potřeby použít pro vlastní problém. Když jsou ukázky velké, vzorkování může při školicích modelech ušetřit značný čas. Dále rozdělíme ukázku na školicí součást (75% zde) a část testování (25% tady), která se použije v modelování klasifikace a regrese.

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.sql.functions import rand

# SPECIFY SAMPLING AND SPLITTING FRACTIONS
samplingFraction = 0.25;
trainingFraction = 0.75; testingFraction = (1-trainingFraction);
seed = 1234;
encodedFinalSampled = encodedFinal.sample(False, samplingFraction, seed=seed)

# SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST
# INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS (FOR USE LATER IN AN ADVANCED TOPIC)
dfTmpRand = encodedFinalSampled.select("*", rand(0).alias("rand"));
trainData, testData = dfTmpRand.randomSplit([trainingFraction, testingFraction], seed=seed);

# FOR BINARY CLASSIFICATION TRAINING AND TESTING
indexedTRAINbinary = trainData.map(parseRowIndexingBinary)
indexedTESTbinary = testData.map(parseRowIndexingBinary)
oneHotTRAINbinary = trainData.map(parseRowOneHotBinary)
oneHotTESTbinary = testData.map(parseRowOneHotBinary)

# FOR REGRESSION TRAINING AND TESTING
indexedTRAINreg = trainData.map(parseRowIndexingRegression)
indexedTESTreg = testData.map(parseRowIndexingRegression)
oneHotTRAINreg = trainData.map(parseRowOneHotRegression)
oneHotTESTreg = testData.map(parseRowOneHotRegression)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**VÝKONEM**

Doba potřebná k provedení výše uvedené buňky: 0,24 sekunda

### <a name="feature-scaling"></a>měření rozsahu prvků,

Škálování funkcí, označované také jako normalizace dat, zajišťuje, že funkce s rozšířenými vyplacenými hodnotami nejsou ve funkci cíl předány nadměrnému vážení. Kód pro škálování funkcí používá [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) ke škálování funkcí na odchylku jednotek. Poskytuje je MLlib pro použití v lineární regresi s stochastického gradientem (SGD), oblíbeným algoritmem pro školení široké škály dalších modelů strojového učení, jako jsou například regulární regrese nebo Podpora vektorových počítačů (SVM).

> [!NOTE]
> Našli jsme LinearRegressionWithSGD algoritmus, který bude citlivý na škálování funkcí.

Zde je kód pro škálování proměnných pro použití s pravidelným lineárním SGD algoritmem.

```python
# FEATURE SCALING

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.regression import LabeledPoint
from pyspark.mllib.linalg import Vectors
from pyspark.mllib.feature import StandardScaler, StandardScalerModel
from pyspark.mllib.util import MLUtils

# SCALE VARIABLES FOR REGULARIZED LINEAR SGD ALGORITHM
label = oneHotTRAINreg.map(lambda x: x.label)
features = oneHotTRAINreg.map(lambda x: x.features)
scaler = StandardScaler(withMean=False, withStd=True).fit(features)
dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
oneHotTRAINregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))

label = oneHotTESTreg.map(lambda x: x.label)
features = oneHotTESTreg.map(lambda x: x.features)
scaler = StandardScaler(withMean=False, withStd=True).fit(features)
dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
oneHotTESTregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**VÝKONEM**

Doba potřebná k provedení výše v buňce: 13,17 sekund

### <a name="cache-objects-in-memory"></a>Objekty mezipaměti v paměti

Doba potřebná pro školení a testování algoritmů ML se dá snížit ukládáním objektů vstupního datového rámce, které se používají pro klasifikaci, regresi a funkce škálované.

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# FOR BINARY CLASSIFICATION TRAINING AND TESTING
indexedTRAINbinary.cache()
indexedTESTbinary.cache()
oneHotTRAINbinary.cache()
oneHotTESTbinary.cache()

# FOR REGRESSION TRAINING AND TESTING
indexedTRAINreg.cache()
indexedTESTreg.cache()
oneHotTRAINreg.cache()
oneHotTESTreg.cache()

# SCALED FEATURES
oneHotTRAINregScaled.cache()
oneHotTESTregScaled.cache()

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**VÝKONEM** 

Doba potřebná k provedení výše uvedené buňky: 0,15 sekunda

## <a name="train-a-binary-classification-model"></a>Výuka binárního klasifikačního modelu

V této části se dozvíte, jak používat tři modely pro úlohu binární klasifikace s předpovídatm, jestli se pro taxislužbyou cestu neplatí Tip. Prezentované modely:

* Pravidelná Logistická regrese 
* Model náhodné doménové struktury
* Rozvětvení barev ke zvýšení

Oddíl kódu sestavení každého modelu je rozdělen do kroků: 

1. **Model školení** dat pomocí jedné sady parametrů
2. **Vyhodnocení modelu** na testovací sadě dat s metrikami
3. **Ukládá se model** v objektu BLOB pro budoucí spotřebu.

### <a name="classification-using-logistic-regression"></a>Klasifikace pomocí logistické regrese

Kód v této části ukazuje, jak vytvořit model logistické regrese pomocí [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) , který předpovídá, jestli se u cesty NYC taxislužby Trip a datové sady tarifů hradí Tip.

**Školení modelu logistické regrese s využitím CV a úklidu parametrů**

```python
# LOGISTIC REGRESSION CLASSIFICATION WITH CV AND HYPERPARAMETER SWEEPING

# GET ACCURACY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD LIBRARIES
from pyspark.mllib.classification import LogisticRegressionWithLBFGS 
from sklearn.metrics import roc_curve,auc
from pyspark.mllib.evaluation import BinaryClassificationMetrics
from pyspark.mllib.evaluation import MulticlassMetrics


# CREATE MODEL WITH ONE SET OF PARAMETERS
logitModel = LogisticRegressionWithLBFGS.train(oneHotTRAINbinary, iterations=20, initialWeights=None, 
                                               regParam=0.01, regType='l2', intercept=True, corrections=10, 
                                               tolerance=0.0001, validateData=True, numClasses=2)

# PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
# NOTE: There are 20 coefficient terms for the 10 features, 
#       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
print("Coefficients: " + str(logitModel.weights))
print("Intercept: " + str(logitModel.intercept))

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**VÝKONEM** 

Koeficienty: [0.0082065285375,-0.0223675576104,-0.0183812028036,-3.48124578069 e-05,-0.00247646947233,-0.00165897881503, 0.0675394837328,-0.111823113101,-0.324609912762,-0.204549780032,-1.36499216354, 0.591088507921,-0.664263411392,-1,00439726852, 3.46567827545,-3.51025855172,-0.0471341112232,-0.043521833294, 0.000243375810385, 0.054518719222]

Zachytit:-0.0111216486893

Doba potřebná k provedení výše v buňce: 14,43 sekund

**Vyhodnocení binárního klasifikačního modelu se standardními metrikami**

```python
#EVALUATE LOGISTIC REGRESSION MODEL WITH LBFGS

# RECORD START TIME
timestart = datetime.datetime.now()

# PREDICT ON TEST DATA WITH MODEL
predictionAndLabels = oneHotTESTbinary.map(lambda lp: (float(logitModel.predict(lp.features)), lp.label))

# INSTANTIATE METRICS OBJECT
metrics = BinaryClassificationMetrics(predictionAndLabels)

# AREA UNDER PRECISION-RECALL CURVE
print("Area under PR = %s" % metrics.areaUnderPR)

# AREA UNDER ROC CURVE
print("Area under ROC = %s" % metrics.areaUnderROC)
metrics = MulticlassMetrics(predictionAndLabels)

# OVERALL STATISTICS
precision = metrics.precision()
recall = metrics.recall()
f1Score = metrics.fMeasure()
print("Summary Stats")
print("Precision = %s" % precision)
print("Recall = %s" % recall)
print("F1 Score = %s" % f1Score)


## SAVE MODEL WITH DATE-STAMP
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp;
dirfilename = modelDir + logisticregressionfilename;
logitModel.save(sc, dirfilename);

# OUTPUT PROBABILITIES AND REGISTER TEMP TABLE
logitModel.clearThreshold(); # This clears threshold for classification (0.5) and outputs probabilities
predictionAndLabelsDF = predictionAndLabels.toDF()
predictionAndLabelsDF.registerTempTable("tmp_results");

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds";
```

**VÝKONEM** 

Oblast pod PR = 0.985297691373

Oblast pod ROC = 0.983714670256

Souhrnné statistiky

Precision = 0.984304060189

Odvolání = 0.984304060189

Skóre F1 = 0.984304060189

Doba potřebná k provedení výše v buňce: 57,61 sekund

**Znázorněte křivku ROC.**

*PredictionAndLabelsDF* je registrován jako tabulka, *tmp_results* v předchozí buňce. *tmp_results* lze použít k provádění dotazů a výstupních výsledků do sqlResults dat – rámec pro vykreslení. Zde je kód.

```python
# QUERY RESULTS                              
%%sql -q -o sqlResults
SELECT * from tmp_results
```

Zde je kód, který provede předpovědi a sekreslí spojnici ROC.

```python
# MAKE PREDICTIONS AND PLOT ROC-CURVE

# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
%matplotlib inline
from sklearn.metrics import roc_curve,auc

# MAKE PREDICTIONS
predictions_pddf = test_predictions.rename(columns={'_1': 'probability', '_2': 'label'})
prob = predictions_pddf["probability"] 
fpr, tpr, thresholds = roc_curve(predictions_pddf['label'], prob, pos_label=1);
roc_auc = auc(fpr, tpr)

# PLOT ROC CURVE
plt.figure(figsize=(5,5))
plt.plot(fpr, tpr, label='ROC curve (area = %0.2f)' % roc_auc)
plt.plot([0, 1], [0, 1], 'k--')
plt.xlim([0.0, 1.0])
plt.ylim([0.0, 1.05])
plt.xlabel('False Positive Rate')
plt.ylabel('True Positive Rate')
plt.title('ROC Curve')
plt.legend(loc="lower right")
plt.show()
```

**VÝKONEM**

![Logistické regrese curve.png](./media/spark-data-exploration-modeling/logistic-regression-roc-curve.png)

### <a name="random-forest-classification"></a>Klasifikace náhodné doménové struktury

Kód v této části ukazuje, jak se naučit, vyhodnocovat a ukládat náhodný model doménové struktury, který předpovídá, jestli se v datové sadě NYC taxislužby Trip a tarifs neplatí Tip pro cestu.

```python
#PREDICT WHETHER A TIP IS PAID OR NOT USING RANDOM FOREST

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.tree import RandomForest, RandomForestModel
from pyspark.mllib.util import MLUtils
from pyspark.mllib.evaluation import BinaryClassificationMetrics
from pyspark.mllib.evaluation import MulticlassMetrics

# SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

# TRAIN RANDOMFOREST MODEL
rfModel = RandomForest.trainClassifier(indexedTRAINbinary, numClasses=2, 
                                       categoricalFeaturesInfo=categoricalFeaturesInfo,
                                       numTrees=25, featureSubsetStrategy="auto",
                                       impurity='gini', maxDepth=5, maxBins=32)
## UN-COMMENT IF YOU WANT TO PRINT TREES
#print('Learned classification forest model:')
#print(rfModel.toDebugString())

# PREDICT ON TEST DATA AND EVALUATE
predictions = rfModel.predict(indexedTESTbinary.map(lambda x: x.features))
predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)

# AREA UNDER ROC CURVE
metrics = BinaryClassificationMetrics(predictionAndLabels)
print("Area under ROC = %s" % metrics.areaUnderROC)

# PERSIST MODEL IN BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
rfclassificationfilename = "RandomForestClassification_" + datestamp;
dirfilename = modelDir + rfclassificationfilename;

rfModel.save(sc, dirfilename);

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**VÝKONEM**

Oblast pod ROC = 0.985297691373

Doba potřebná k provedení výše v buňce: 31,09 sekund

### <a name="gradient-boosting-trees-classification"></a>Klasifikace pro zvýšení úrovně barev

Kód v této části ukazuje, jak se naučit, vyhodnocovat a ukládat model pro zesílení přechodů, který předpovídá, jestli se v datové sadě NYC taxislužby Trip a tarifs vyplatila špička pro cestu.

```python
#PREDICT WHETHER A TIP IS PAID OR NOT USING GRADIENT BOOSTING TREES

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel

# SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

gbtModel = GradientBoostedTrees.trainClassifier(indexedTRAINbinary, categoricalFeaturesInfo=categoricalFeaturesInfo, numIterations=5)
## UNCOMMENT IF YOU WANT TO PRINT TREE DETAILS
#print('Learned classification GBT model:')
#print(bgtModel.toDebugString())

# PREDICT ON TEST DATA AND EVALUATE
predictions = gbtModel.predict(indexedTESTbinary.map(lambda x: x.features))
predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)

# AREA UNDER ROC CURVE
metrics = BinaryClassificationMetrics(predictionAndLabels)
print("Area under ROC = %s" % metrics.areaUnderROC)

# PERSIST MODEL IN A BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp;
dirfilename = modelDir + btclassificationfilename;

gbtModel.save(sc, dirfilename)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**VÝKONEM**

Oblast pod ROC = 0.985297691373

Doba potřebná k provedení výše v buňce: 19,76 sekund

## <a name="train-a-regression-model"></a>Trénování regresního modelu

V této části se dozvíte, jak používat tři modely pro regresní úlohu předpověď množství tipu placeného pro taxislužby trip na základě dalších funkcí Tip. Prezentované modely:

* Pravidelná lineární regrese
* Náhodná doménová struktura
* Rozvětvení barev ke zvýšení

Tyto modely byly popsány v úvodu. Oddíl kódu sestavení každého modelu je rozdělen do kroků: 

1. **Model školení** dat pomocí jedné sady parametrů
2. **Vyhodnocení modelu** na testovací sadě dat s metrikami
3. **Ukládá se model** v objektu BLOB pro budoucí spotřebu.

### <a name="linear-regression-with-sgd"></a>Lineární regrese s SGD

Kód v této části ukazuje, jak používat škálované funkce k učení lineární regrese, která pro optimalizaci používá stochastického barevný sklon (SGD), a jak skóre, vyhodnotit a uložit model v Azure Blob Storage (WASB).

> [!TIP]
> V našem prostředí mohou nastat problémy se sbližováním modelů LinearRegressionWithSGD a parametry je nutné pečlivě změnit/optimalizovat pro získání platného modelu. Škálování proměnných významně pomáhá při sbližování.

```python
#PREDICT TIP AMOUNTS USING LINEAR REGRESSION WITH SGD

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD LIBRARIES
from pyspark.mllib.regression import LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel
from pyspark.mllib.evaluation import RegressionMetrics
from scipy import stats

# USE SCALED FEATURES TO TRAIN MODEL
linearModel = LinearRegressionWithSGD.train(oneHotTRAINregScaled, iterations=100, step = 0.1, regType='l2', regParam=0.1, intercept = True)

# PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
# NOTE: There are 20 coefficient terms for the 10 features, 
#       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
print("Coefficients: " + str(linearModel.weights))
print("Intercept: " + str(linearModel.intercept))

# SCORE ON SCALED TEST DATA-SET & EVALUATE
predictionAndLabels = oneHotTESTregScaled.map(lambda lp: (float(linearModel.predict(lp.features)), lp.label))
testMetrics = RegressionMetrics(predictionAndLabels)

# PRINT TEST METRICS
print("RMSE = %s" % testMetrics.rootMeanSquaredError)
print("R-sqr = %s" % testMetrics.r2)

# SAVE MODEL WITH DATE-STAMP IN THE DEFAULT BLOB FOR THE CLUSTER
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
dirfilename = modelDir + linearregressionfilename;

linearModel.save(sc, dirfilename)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**VÝKONEM**

Koeficienty: [0.00457675809917,-0.0226314167349,-0.0191910355236, 0.246793409578, 0.312047890459, 0.359634405999, 0.00928692253981,-0.000987181489428,-0.0888306617845, 0.0569376211553, 0.115519551711, 0.149250164995,-0.00990211159703,-0.00637410344522, 0.545083566179,-0.536756072402, 0.0105762393099,-0.0130117577055, 0.0129304737772,-0.00171065945959]

Zachytit: 0.853872718283

RMSE = 1.24190115863

R-SQR = 0.608017146081

Doba potřebná k provedení výše v buňce: 58,42 sekund

### <a name="random-forest-regression"></a>Náhodná regrese doménové struktury

Kód v této části ukazuje, jak se naučit, vyhodnocovat a ukládat náhodné regrese doménové struktury, který předpovídá množství tipů pro data NYC taxislužby.

```python
#PREDICT TIP AMOUNTS USING RANDOM FOREST

# RECORD START TIME
timestart= datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.tree import RandomForest, RandomForestModel
from pyspark.mllib.util import MLUtils
from pyspark.mllib.evaluation import RegressionMetrics


## TRAIN MODEL
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                    numTrees=25, featureSubsetStrategy="auto",
                                    impurity='variance', maxDepth=10, maxBins=32)
## UN-COMMENT IF YOU WANT TO PRING TREES
#print('Learned classification forest model:')
#print(rfModel.toDebugString())

## PREDICT AND EVALUATE ON TEST DATA-SET
predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
predictionAndLabels = oneHotTESTreg.map(lambda lp: lp.label).zip(predictions)

# TEST METRICS
testMetrics = RegressionMetrics(predictionAndLabels)
print("RMSE = %s" % testMetrics.rootMeanSquaredError)
print("R-sqr = %s" % testMetrics.r2)

# SAVE MODEL IN BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
rfregressionfilename = "RandomForestRegression_" + datestamp;
dirfilename = modelDir + rfregressionfilename;

rfModel.save(sc, dirfilename);

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**VÝKONEM**

RMSE = 0.891209218139

R-SQR = 0.759661334921

Doba potřebná k provedení výše v buňce: 49,21 sekund

### <a name="gradient-boosting-trees-regression"></a>Regrese pro zvýšení úrovně barev

Kód v této části ukazuje, jak se naučit, vyhodnocovat a ukládat model pro zesílení přechodů, který předpovídá množství tipů pro data NYC taxislužby.

**Výuka a vyhodnocení**

```python
#PREDICT TIP AMOUNTS USING GRADIENT BOOSTING TREES

# RECORD START TIME
timestart= datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
from pyspark.mllib.util import MLUtils

## TRAIN MODEL
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
gbtModel = GradientBoostedTrees.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo, 
                                                numIterations=10, maxBins=32, maxDepth = 4, learningRate=0.1)

## EVALUATE A TEST DATA-SET
predictions = gbtModel.predict(indexedTESTreg.map(lambda x: x.features))
predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

# TEST METRICS
testMetrics = RegressionMetrics(predictionAndLabels)
print("RMSE = %s" % testMetrics.rootMeanSquaredError)
print("R-sqr = %s" % testMetrics.r2)

# SAVE MODEL IN BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
btregressionfilename = "GradientBoostingTreeRegression_" + datestamp;
dirfilename = modelDir + btregressionfilename;
gbtModel.save(sc, dirfilename)

# CONVERT RESULTS TO DF AND REGISTER TEMP TABLE
test_predictions = sqlContext.createDataFrame(predictionAndLabels)
test_predictions.registerTempTable("tmp_results");

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**VÝKONEM**

RMSE = 0.908473148639

R-SQR = 0.753835096681

Doba potřebná k provedení výše v buňce: 34,52 sekund

**Znázorněte**

*tmp_results* je zaregistrován jako tabulka podregistru v předchozí buňce. Výsledky z tabulky jsou ve výstupu *sqlResults* data-Frame pro vykreslování. Zde je kód

```python
# PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

# SELECT RESULTS
%%sql -q -o sqlResults
SELECT * from tmp_results
```

Zde je kód pro vykreslení dat pomocí serveru Jupyter.

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
%matplotlib inline
import numpy as np

# PLOT 
ax = test_predictions_pddf.plot(kind='scatter', figsize = (6,6), x='_1', y='_2', color='blue', alpha = 0.25, label='Actual vs. predicted');
fit = np.polyfit(test_predictions_pddf['_1'], test_predictions_pddf['_2'], deg=1)
ax.set_title('Actual vs. Predicted Tip Amounts ($)')
ax.set_xlabel("Actual")
ax.set_ylabel("Predicted")
ax.plot(test_predictions_pddf['_1'], fit[0] * test_predictions_pddf['_1'] + fit[1], color='magenta')
plt.axis([-1, 20, -1, 20])
plt.show(ax)
```

**VÝKONEM**

![Skutečnost-vs-předpovězené – Tip – částky](./media/spark-data-exploration-modeling/actual-vs-predicted-tips.png)

## <a name="clean-up-objects-from-memory"></a>Vyčištění objektů z paměti

Slouží `unpersist()` k odstranění objektů uložených v mezipaměti v paměti.

```python
# REMOVE ORIGINAL DFs
taxi_df_train_cleaned.unpersist()
taxi_df_train_with_newFeatures.unpersist()

# FOR BINARY CLASSIFICATION TRAINING AND TESTING
indexedTRAINbinary.unpersist()
indexedTESTbinary.unpersist()
oneHotTRAINbinary.unpersist()
oneHotTESTbinary.unpersist()

# FOR REGRESSION TRAINING AND TESTING
indexedTRAINreg.unpersist()
indexedTESTreg.unpersist()
oneHotTRAINreg.unpersist()
oneHotTESTreg.unpersist()

# SCALED FEATURES
oneHotTRAINregScaled.unpersist()
oneHotTESTregScaled.unpersist()
```

## <a name="save-the-models"></a>Uložení modelů

Aby bylo možné využívat a hodnotit nezávislou datovou sadu popsanou v tématu [skóre a vyhodnotit modely strojového učení v Sparku](spark-model-consumption.md) , musíte zkopírovat a vložit tyto názvy souborů obsahující uložené modely, které tady vytvoří, do Jupyter poznámkového bloku spotřeby. Zde je kód pro vytištění cest k souborům modelů, které potřebujete.

```python
# MODEL FILE LOCATIONS FOR CONSUMPTION
print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";
```

**VÝKONEM**

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05 -0317 _03_ 23.516568"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-05 -0317 _05_ 21.577773"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05 -0317 _04_ 11.950206"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05 -0317 _06_ 08.723736"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05 -0317 _04_ 36.346583"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05 -0317 _06_ 51.737282"

## <a name="whats-next"></a>Co dále?

Teď, když jste vytvořili regresní a klasifikační modely pomocí Spark MlLib, jste připraveni zjistit, jak tyto modely hodnotit a vyhodnocovat. Pokročilý Poznámkový blok pro zkoumání a modelování dat komentáře hlouběji do zahrnutí křížového ověřování, úklidu parametrů a vyhodnocení modelu. 

**Spotřeba modelu:** Informace o tom, jak hodnotit a hodnotit modely klasifikace a regrese vytvořené v tomto tématu, najdete v tématu [skóre a vyhodnocení modelů strojového učení s využitím Sparku](spark-model-consumption.md).

**Křížové ověřování a mazání pomocí parametrů** : viz [Pokročilá kontrola a modelování dat pomocí Sparku](spark-advanced-data-exploration-modeling.md) , jak lze modely vyškolené pomocí křížového ověřování a s možnostími úklidu Hyper-Parameter.