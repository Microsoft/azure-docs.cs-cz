---
title: Zkoumání a modelování se Sparkem - vědecké zpracování týmových dat
description: Představuje funkce zkoumání a modelování dat Spark MLlib sady nástrojů v Azure.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 03/15/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 32b457858598ed3dec4a1398cb45310dba877bf3
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54447900"
---
# <a name="data-exploration-and-modeling-with-spark"></a>Zkoumání a modelování dat pomocí Spark

Tento návod používá HDInsight Spark provedete zkoumání dat a binární klasifikačních a regresních modelování úkoly k ukázce NYC taxi cesty a jízdenky 2013 datové sady.  Provede vás provede postupem [vědecké zpracování dat](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/), end až do konce, použití cluster HDInsight Spark pro zpracování a Azure BLOB k ukládání dat a modely. Proces zkoumá a vizualizuje dat získaných z objektu Blob služby Azure Storage a pak připraví data k vytvoření prediktivních modelů. Tyto modely jsou sestavení pomocí nástrojů knihovna Spark MLlib vykonávat binární klasifikačních a regresních modelování.

* **Binární klasifikace** úkol je předpovědět, jestli jde placenou tip pro cestu. 
* **Regrese** úkolu je předpovědět, částky spropitného založených na jiných funkcích tip. 

Modely, které používáme zahrnují logistické a lineární regrese, náhodných doménové struktury a přechodu Posílený stromové struktury:

* [Lineární regrese s SGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) je model lineární regrese, který používá metodu pomocí Stochastického přechodu sestup (SGD) a pro optimalizaci a funkce škálování předpovídat špičky částky zaplacené. 
* [Logistickou regresi s LBFGS](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) nebo regresní model, který se dá použít při závislé proměnné je zařazená do kategorií provedete klasifikace dat je "logit" regrese. LBFGS je dál Newton optimalizace algoritmus, který blíží algoritmu Broyden – Fletcher – Goldfarb – Shanno (BFGS) pomocí omezené množství paměti a, která se často používá ve službě machine learning.
* [Náhodné doménových struktur](http://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) jsou umožňující rozhodovacích stromů.  Jejich kombinací mnoha rozhodovacích stromů, aby se snížilo riziko overfitting. Náhodné doménové struktury se používají pro regresní a klasifikace a dokáže zpracovat zařazené do kategorií funkcí a je možné rozšířit na nastavení klasifikace víc tříd. Se nevyžadují, aby funkce škálování a budou moct zachytit nelineárností a funkce interakce. Náhodné doménových struktur jsou jednou z nejvíce úspěšný strojového učení pro klasifikačních a regresních modelů.
* [Přechod boosted stromů](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) jsou umožňující rozhodovacích stromů. GBTs trénování rozhodovacích stromů zavádět postupně, chcete-li minimalizovat ztrátu funkce. GBTs se používají pro regresní a klasifikace a dokáže zpracovat zařazené do kategorií funkce, nevyžadují, aby funkce škálování a budou moct zachytit nelineárností a funkce interakce. Můžete také používají v nastavení multiclass klasifikace.

Kroky modelování také obsahovat kód ukazující způsob trénování, vyhodnocení a uložte každý typ modelu. Python se použil, jak se programují řešení a zobrazíte relevantní vykreslení.   

> [!NOTE]
> Přestože knihovna Spark MLlib toolkit je navržen pro práci na velkých datových sad, relativně malým vzorkem (pomocí 170 tisíc řádků, přibližně u 0,1 % původní datové sady NYC ~ 30 Mb) se tady používá ke zvýšení pohodlí. Cvičení zde běží na clusteru služby HDInsight pomocí 2 pracovní uzly efektivně (během 10 minut). Stejný kód s malými změnami lze použít ke zpracování větší – sady dat se změny, které vyžaduje pro ukládání dat v paměti do mezipaměti a změna velikosti clusteru.
> 
> 

## <a name="prerequisites"></a>Požadavky
Potřebujete účet Azure a Spark 1.6 (nebo Spark 2.0) clusteru HDInsight k dokončení tohoto návodu. Zobrazit [přehled datové vědy pomocí Sparku v Azure HDInsight](spark-overview.md) pokyny o tom, jak tyto požadavky splňují. Toto téma obsahuje také popis dat taxislužby NYC 2013 se tady použít a pokyny o tom, jak spustit kód z poznámkového bloku Jupyter v clusteru Spark. 

## <a name="spark-clusters-and-notebooks"></a>Clustery Spark a poznámkové bloky
Postup instalace a kódu jsou k dispozici v tomto názorném postupu pro používání HDInsight Spark 1.6. Ale poznámkové bloky Jupyter jsou k dispozici pro clustery HDInsight Spark 1.6 i Spark 2.0. Popis poznámkových bloků a odkazy na nich jsou součástí [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) pro úložiště GitHub, které je obsahují. Kromě toho kód tady v propojených poznámkových bloků je obecný a by mělo fungovat jakéhokoli jiného clusteru Spark. Pokud nepoužíváte HDInsight Spark, může být mírně lišit od co je znázorněna zde kroky instalace a správy clusteru. Pro usnadnění práce tady jsou odkazy na poznámkové bloky Jupyter pro Spark 1.6 (ke spuštění v jádra pySpark aplikace Jupyter Notebook server) a Spark 2.0 (ke spuštění v pySpark3 jádra aplikace Jupyter Notebook serveru):

### <a name="spark-16-notebooks"></a>Poznámkové bloky Spark 1.6

[pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb): Poskytuje informace o tom, jak provádět zkoumání dat, modelování a vyhodnocování se několik různých algoritmů.

### <a name="spark-20-notebooks"></a>Poznámkové bloky Spark 2.0
Regrese a klasifikace úlohy, které jsou implementovány pomocí clusteru Spark 2.0 jsou v samostatných poznámkových bloků a Poznámkový blok klasifikaci používá jinou sadu dat:

- [Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Tento soubor obsahuje informace o tom, jak provádět zkoumání dat, modelování, a hodnocení ve Spark 2.0 clusterů pomocí cesty taxíkem NYC a tarif datové sady je popsáno [tady](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data). Tento poznámkový blok může být dobrým výchozím bodem rychle prozkoumat kód, který jsme připravili pro Spark 2.0. Poznámkový blok podrobnější analyzuje data taxislužby NYC, najdete v části Další poznámkového bloku v tomto seznamu. Naleznete v poznámkách za tímto seznamem, které porovnávají tyto poznámkové bloky. 
- [Spark2.0-pySpark3_NYC_Taxi_Tip_Regression.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_NYC_Taxi_Tip_Regression.ipynb): Tento soubor ukazuje, jak provádět tahání dat (Spark SQL a datový rámec operace), průzkum, modelování a vyhodnocování pomocí cesty taxíkem NYC a tarif datové sady je popsáno [tady](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data).
- [Spark2.0-pySpark3_Airline_Departure_Delay_Classification.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_Airline_Departure_Delay_Classification.ipynb): Tento soubor ukazuje, jak provádět tahání dat (Spark SQL a datový rámec operace), průzkum, modelování a vyhodnocování pomocí dobře známých letecká společnost na čas odeslání datové sady z 2011 a 2012. Integrovali jsme letecká společnost datovou sadu s daty o počasí letiště (například rychlost větru, teplota, výška atd.) před modelování, takže tyto funkce počasí mohou být součástí modelu.

<!-- -->

> [!NOTE]
> Datová sada letecká společnost byl přidán do poznámkových bloků Spark 2.0 abychom vám lépe předvedli použití klasifikace algoritmů. V následujících tématech o letecká společnost včas odeslání datovou sadu a datovou sadu weather:

>- Data o včasných odeslání letecká společnost: [http://www.transtats.bts.gov/ONTIME/](http://www.transtats.bts.gov/ONTIME/)

>- Data o počasí letiště: [https://www.ncdc.noaa.gov/](https://www.ncdc.noaa.gov/) 
> 
> 

<!-- -->

<!-- -->

> [!NOTE]
Poznámkové bloky Spark 2.0 na NYC taxislužby města a letecká společnost letu zpoždění-sady dat může trvat 10 minut nebo déle ke spuštění (v závislosti na velikosti vašeho clusteru Hdinsight). První poznámkového bloku v seznamu nahoře ukazuje mnoho aspektů zkoumání, vizualizaci a trénování modelů ML v poznámkovém bloku, která přebírá méně času na spuštění pomocí předvýpočtem zredukovaných NYC datové sady, ve kterém byly soubory taxislužby města a tarif předem připojené k doméně: [Spark2.0-pySpark3-Machine-Learning-data-Science-Spark-Advanced-data-Exploration-Modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb) tento poznámkový blok trvá mnohem kratší dobu dokončení (2 až 3 minut) a může být vhodná výchozí bod pro rychlé zkoumání kódu uvádíme pro Spark 2.0. 

<!-- -->

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

<!-- -->

> [!NOTE]
Níže se vztahují k použití Spark 1.6. Pro verze Spark 2.0 použijte prosím poznámkových bloků popsaných a propojené výše. 

<!-- -->

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Instalační program: umístění úložiště, knihovny a přednastavený kontext Spark
Spark je možné číst a zapisovat do objektu Blob Azure Storage (WASB). Takže existující data uložená existuje mohou být zpracovány pomocí Sparku a výsledky uložené znovu v WASB.

Pokud chcete uložit soubory nebo modely WASB, cesta musí být zadán správně. Výchozí kontejner připojené ke clusteru Spark může být odkazováno pomocí cesta začínající: "wasb: / / / / /". Jiné umístění je odkazováno dle "wasb: / /".

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Nastavit cesty adresáře na umístění úložiště v WASB
Následující příklad kódu určuje umístění dat pro čtení a cestu k adresáři modelu úložiště, do kterého se uloží výstupní modelu:

    # SET PATHS TO FILE LOCATIONS: DATA AND MODEL STORAGE

    # LOCATION OF TRAINING DATA
    taxi_train_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv";

    # SET THE MODEL STORAGE DIRECTORY PATH 
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS NEEDED.
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 


### <a name="import-libraries"></a>Importovat knihovny
Nastavení rovněž vyžaduje import potřebné knihovny. Nastavit kontext spark a naimportujte potřebné knihovny s následujícím kódem:

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


### <a name="preset-spark-context-and-pyspark-magics"></a>Přednastavený kontext Spark a PySpark Magic
Jádra PySpark, které jsou k dispozici s poznámkovými bloky Jupyter nemají přednastavený kontext. Proto není nutné nastavovat Spark nebo vyvíjíte Hive kontexty explicitně předtím, než začnete pracovat s aplikací. Kontexty jsou k dispozici ve výchozím nastavení. Kontexty jsou:

* sc - pro Spark 
* kontext sqlContext - pro Hive

Jádra PySpark poskytuje některé předdefinované "Magic", které jsou speciální příkazy, které lze volat s %%. Existují dva tyto příkazy, které se používají v těchto ukázek kódu.

* **%% místní** Určuje, že kód v dalších řádcích, je spuštěn lokálně. Kód musí být platný kód Pythonu.
* **%% sql -o <variable name>**  spustí dotaz Hive proti kontext sqlContext. Pokud je předán parametr -o výsledek dotazu se ukládají v %% místní kontext Python jako Pandas DataFrame.

Pro další informace o jádrech pro poznámkové bloky Jupyter a předdefinované "magics", která poskytují, naleznete v tématu [jádra dostupná pro poznámkové bloky Jupyter s HDInsight Spark Linux clusterů v HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

## <a name="data-ingestion-from-public-blob"></a>Příjem dat z veřejných objektů blob
Prvním krokem při vědecké zpracování dat je k ingestování dat má být analyzován z zdrojů kde je umístěn do prostředí pro zkoumání a modelování vaší datové. Prostředí je Spark v tomto názorném postupu. Tato část obsahuje kód pro dokončení řadu úkolů:

* ingestování vzorek dat modelovat
* přečíst vstupní datové sady (uložený jako soubor TSV)
* formátování a vyčištění dat
* vytvářet a ukládat do mezipaměti (Rdd nebo datové rámce) objektů v paměti
* Zaregistrujte se jako dočasné tabulky v kontextu SQL.

Zde je kód pro ingestování.

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

**VÝSTUP:**

Čas potřebný k provedení nad buňkou: 51.72 sekund

## <a name="data-exploration--visualization"></a>Zkoumání a vizualizace
Jakmile data, nemůžete do Sparku, je dalším krokem v vědecké zpracování dat získali lepší představu o datech prostřednictvím zkoumání a vizualizaci. V této části jsme zkoumat data taxislužby města pomocí dotazů SQL a vykreslení cíl proměnné a pomáhala potenciálním funkce pro vizuální kontrolu. Konkrétně jsme vykreslení frekvence počty osobní v cesty taxíkem frekvence částky tip a jak tipy lišit podle výše platby a typu.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Zobrazit histogram počtu frekvencí osobní v ukázce cesty taxíkem
Tento kód a další fragmenty použití magický příkaz jazyka SQL k dotazování ukázka a místní magic k vykreslení data.

* **Magický příkaz jazyka SQL (`%%sql`)** jádra PySpark HDInsight podporuje snadno vložené HiveQL dotazů kontext sqlContext. (-O VARIABLE_NAME) argument ukládá jako Pandas DataFrame na serveru Jupyter výstup příkazu jazyka SQL. To znamená, že je k dispozici v místním režimu.
*  **`%%local` Magic** se používá ke spouštění kódu místně na serveru Jupyter, což je hlavního uzlu clusteru HDInsight. Obvykle použijete `%%local` magic ve spojení s `%%sql` magic s parametrem -o. Parametr -o by trvalý výstup příkazu jazyka SQL místně a pak %% místní magic aktivuje další sadu fragment kódu pro místní spuštění proti výstup dotazy SQL, který se ukládají místně

Výstup se automaticky vizualizuje po spuštění kódu.

Tento dotaz načte zkracuje dobu odezvy podle počtu osobní. 

    # PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

    # HIVEQL QUERY AGAINST THE sqlContext
    %%sql -q -o sqlResults
    SELECT passenger_count, COUNT(*) as trip_counts 
    FROM taxi_train 
    WHERE passenger_count > 0 and passenger_count < 7 
    GROUP BY passenger_count 

Tento kód vytvoří místní datový rámec z výstupu dotazu a zobrazí data. `%%local` Magic vytvoří místní snímek dat, `sqlResults`, kterou lze použít pro vykreslení s matplotlib. 

> [!NOTE]
> Tato PySpark magic se používá více než jednou v tomto názorném postupu. Pokud je velké množství dat, by měl ukázkový vytvořte datový rámec, který se vejde do místní paměti.
> 
> 

    #CREATE LOCAL DATA-FRAME AND USE FOR MATPLOTLIB PLOTTING

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
    # CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
    sqlResults

Tady je kód pro vykreslení zkracuje dobu odezvy dle počtů osobní

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

**VÝSTUP:**

![O jízdách frekvenci podle počtu osobní](./media/spark-data-exploration-modeling/trip-freqency-by-passenger-count.png)

Můžete vybrat mezi několik různých typů vizualizace (tabulky, výsečové, řádek, oblasti nebo panelu) s použitím **typ** tlačítka nabídky v poznámkovém bloku. Pruhový graf je znázorněna zde.

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Zobrazte histogram tip částek a jak se liší podle částky počet a tarif osobní velikost špičky.
Použijte dotaz SQL na ukázková data.

    #PLOT HISTOGRAM OF TIP AMOUNTS AND VARIATION BY PASSENGER COUNT AND PAYMENT TYPE

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


Tuto buňku kódu použije k vytvoření tří vykreslení dat bude příkaz jazyka SQL.

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


**VÝSTUP:** 

![Rozdělení částky tip](./media/spark-data-exploration-modeling/tip-amount-distribution.png)

![Tip částka podle počtu osobní](./media/spark-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Tip velikost podle velikosti tarif](./media/spark-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>Funkce analýzy, transformace a data přípravy pro modelování
Tato část popisuje a obsahuje kód pro procedury používané k přípravě dat pro použití v modelu ML. Ukazuje, jak provádět následující úlohy:

* Vytvořit novou funkci binningu hodin do provozu časovým intervalům
* Indexování a kódování funkcí zařazené do kategorií
* Vytváření objektů označených bodu pro vstup do funkce ML
* Vytvořit náhodný dílčí vzorkování dat a rozdělit na trénování a testování sad
* Funkce škálování
* Mezipaměti objektů v paměti

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Vytvořit novou funkci binningu hodin do provozu časovým intervalům
Tento kód ukážeme, jak vytvořit novou funkci binningu hodiny do časovým intervalům provoz a výsledné datového rámce v paměti do mezipaměti. Tam, kde se opakovaně používá odolné distribuované sady rdd (Resilient) a datové rámce, ukládání do mezipaměti vede k lepší spuštění s úspěšností. Proto jsme mezipaměti Rdd a datové rámce v několika fázích v tomto návodu. 

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

**VÝSTUP:** 

126050

### <a name="index-and-encode-categorical-features-for-input-into-modeling-functions"></a>Indexování a kódování zařazené do kategorií funkce pro vstup do funkce modelování
Tato část ukazuje, jak index nebo kódování zařazené do kategorií funkce pro vstup do funkce modelování. Modelování a předpovědět, funkce s kategorií vstupní data indexované nebo se musí kódováním než použití funkce MLlib vyžadují. V závislosti na modelu budete muset index nebo kódování různými způsoby:  

* **Založený na stromové architektuře modelování** vyžaduje kategorie kódovaný jako číselné hodnoty (například funkce s tři kategorie mohou být kódovány s 0, 1, 2). To je poskytována pro MLlib [StringIndexer](http://spark.apache.org/docs/latest/ml-features.html#stringindexer) funkce. Tato funkce kóduje řetězec sloupec s popisky sloupců popisek indexů, které jsou řazeny podle frekvence popisek. I když indexované číselné hodnoty pro vstup a zpracování dat, k odpovídajícím způsobem zpracovávat jako kategorie lze zadat algoritmy založený na stromové architektuře. 
* **Modely logistické a lineární regrese** vyžadují jeden horkou kódování, kde, například funkce s tři kategorie se rozšířit do tří sloupců funkce s každou obsahující 0 nebo 1 podle kategorie hodnotu. Poskytuje MLlib [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) funkce dělat hot jeden kódování. Tomto kodéru mapuje sloupec indexů popisek ke sloupci binárního vektorů s maximálně jeden – hodnotu single. Toto kódování umožňuje algoritmy, které očekávají číselnými hodnotami funkce, jako je logistické regrese, použít zařazené do kategorií funkce.

Tady je kód pro index a kódování funkcí zařazené do kategorií:

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

**VÝSTUP:**

Čas potřebný k provedení nad buňkou: 1,28 sekund

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Vytváření objektů označených bodu pro vstup do funkce ML
Tato část obsahuje kód, který ukazuje, jak indexovat zařazené do kategorií textová data jako datový typ s popiskem bodu a kódovat, takže je možné pro trénování a testování logistické regrese MLlib a jiných modelů klasifikace. S popiskem bodových objektů jsou odolné distribuovaných datových sad (RDD) ve formátu způsobem, který je potřeba jako vstupní data pro většinu algoritmů ML v MLlib. A [označené jako bod](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) je přidružený místní vektoru, dense nebo zhuštěný, popisek a odpovědí.  

Tato část obsahuje kód, který ukazuje, jak indexovat data zařazená do kategorií textu jako [označené jako bod](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) datový typ a kódovat, takže je možné pro trénování a testování logistické regrese MLlib a jiných modelů klasifikace. S popiskem bodových objektů jsou odolné distribuovaných datových sad (RDD) skládající se z popisku (cíl/odpověď proměnná) a funkce vektoru. Tento formát je potřeba jako vstup pro mnoho algoritmů ML v MLlib.

Tady je kód pro indexování a kódování funkcí text pro binární klasifikaci.

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


Tady je kód, který má být zakódován a indexu zařazené do kategorií textové funkce pro analýzu lineární regrese.

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


### <a name="create-a-random-sub-sampling-of-the-data-and-split-it-into-training-and-testing-sets"></a>Vytvořit náhodný dílčí vzorkování dat a rozdělit na trénování a testování sad
Tento kód vytvoří náhodný vzorkování dat (25 % používá zde). I když není nutné v tomto příkladu kvůli velikosti datové sady, vám ukážeme, jak můžete vzorkovat zde abyste věděli, jak ho použít pro vlastní problém v případě potřeby. Když ukázky jsou velké, to ušetří spoustu času při trénování modelů. Dále jsme vzorku rozdělit na části školení (zde 75 %) a testování částí (zde 25 %) pro použití v zařazení a regresní modelování.

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

**VÝSTUP:**

Čas potřebný k provedení nad buňkou: 0,24 sekund

### <a name="feature-scaling"></a>Funkce škálování
Funkce škálování, označované také jako data normalizace, zajistí, že funkce se značně Celková uhrazená hodnotami jsou neudělil nadměrné naváží ve funkci cíle. Kód pro funkce používá škálování [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) funkce, které se odchylka jednotek škálování. Podle MLlib je určen pro použití v lineární regrese s pomocí Stochastického přechodu sestup (SGD), Oblíbené algoritmů pro trénování širokou řadu jiných modelů strojového učení, jako je například Vyřešeno regresí nebo počítače vektorové podpory (SVM).

> [!NOTE]
> Našli jsme algoritmu LinearRegressionWithSGD brát ohled na funkce škálování.
> 
> 

Tady je kód, který škálování proměnné pro použití s regularized lineárního algoritmu SGD.

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

**VÝSTUP:**

Čas potřebný k provedení nad buňkou: 13.17 sekund

### <a name="cache-objects-in-memory"></a>Mezipaměti objektů v paměti
Ukládání do mezipaměti vstupní datový rámec objekty používané pro klasifikace, regrese a škálovat funkce lze snížit čas potřebný pro trénování a testování algoritmů ML.

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

**VÝSTUP:** 

Čas potřebný k provedení nad buňkou: 0,15 sekund

## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>Předpovědět, jestli je binární klasifikační modely placené tip
Tato část ukazuje, jak použijte tři modely pro úlohy binární klasifikace: předpověď, jestli jde placenou tip pro cesty taxíkem. Modely uvedené jsou:

* Vyřešeno logistické regrese 
* Model náhodného doménové struktury
* Stromy přechodu zvýšení skóre

Každý model vytváření část kódu je rozdělený do kroků: 

1. **Model školení** dat pomocí jednu sadu parametrů
2. **Model hodnocení** na testovací datové sady s metrikami
3. **Probíhá ukládání modelu** v objektu blob pro budoucí využití

### <a name="classification-using-logistic-regression"></a>Klasifikace pomocí logistické regrese
Kód v této části ukazuje, jak pro trénování, vyhodnocení a uložit model logistické regrese s [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) , který předpovídá, jestli je tip placené cesty v NYC taxislužby cesty a tarif datové sady.

**Trénování modelu logistické regrese pomocí CV a hyperparameter sweeping**

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


**VÝSTUP:** 

Koeficienty: [0.0082065285375-0.0223675576104,-0.0183812028036, - 3.48124578069e - 05-0.00247646947233,-0.00165897881503, 0.0675394837328,-0.111823113101,-0.324609912762,-0.204549780032,-1.36499216354, 0.591088507921, - 0.664263411392-1.00439726852, 3.46567827545,-3.51025855172,-0.0471341112232,-0.043521833294, 0.000243375810385, 0.054518719222]

Zachycení:-0.0111216486893

Čas potřebný k provedení nad buňkou: 14.43 sekund

**Vyhodnocení binární klasifikační model pomocí standardních metrik**

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

**VÝSTUP:** 

Oblasti v rámci žádosti o přijetí změn = 0.985297691373

Oblast pod roc s více TŘÍDAMI = 0.983714670256

Souhrnné statistiky

Přesnost = 0.984304060189

Odvolat = 0.984304060189

F1 Skóre = 0.984304060189

Čas potřebný k provedení nad buňkou: 57.61 sekund

**Vykreslení křivka roc s více TŘÍDAMI.**

*PredictionAndLabelsDF* je zaregistrovaný jako tabulky, *tmp_results*, v předcházejí buňky. *tmp_results* slouží k provádění dotazů a výstup do data snímků sqlResults pro vykreslení. Zde je kód.

    # QUERY RESULTS                              
    %%sql -q -o sqlResults
    SELECT * from tmp_results


Tady je kód pro predikci a vykreslení křivka roc s více TŘÍDAMI.

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


**VÝSTUP:**

![Logistické regrese curve.png roc s více TŘÍDAMI](./media/spark-data-exploration-modeling/logistic-regression-roc-curve.png)

### <a name="random-forest-classification"></a>Klasifikace náhodné doménové struktury
Kód v této části ukazuje, jak pro trénování, vyhodnocení a uložení náhodných doménovou strukturu modelu, který předpovídá, jestli je tip placené cesty v NYC taxislužby cesty a tarif datovou sadu.

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

**VÝSTUP:**

Oblast pod roc s více TŘÍDAMI = 0.985297691373

Čas potřebný k provedení nad buňkou: 31.09 sekund

### <a name="gradient-boosting-trees-classification"></a>Přechodu zvýšení skóre klasifikace stromů
Kód v této části ukazuje, jak trénovat, vyhodnotit a uložit přechodu zvýšení skóre stromů modelu pro předpověď, jestli je tip placené výlet do cesty taxíkem NYC a jízdenky datové sady.

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


**VÝSTUP:**

Oblast pod roc s více TŘÍDAMI = 0.985297691373

Čas potřebný k provedení nad buňkou: 19.76 sekund

## <a name="predict-tip-amounts-for-taxi-trips-with-regression-models"></a>Předpověď částky tip pro cesty taxíkem s regresních modelů
Tato část ukazuje, jak použít tři modely pro úlohu regrese předpověď částky spropitného placené cesty taxíkem založených na jiných funkcích tip. Modely uvedené jsou:

* Vyřešeno lineární regrese
* Náhodné doménové struktury
* Stromy přechodu zvýšení skóre

Tyto modely jsou popsány v úvodu. Každý model vytváření část kódu je rozdělený do kroků: 

1. **Model školení** dat pomocí jednu sadu parametrů
2. **Model hodnocení** na testovací datové sady s metrikami
3. **Probíhá ukládání modelu** v objektu blob pro budoucí využití

### <a name="linear-regression-with-sgd"></a>Lineární regrese s SGD
Kód v této části ukazuje, jak používat škálován funkce k trénování lineární regrese, který se používá pro optimalizaci pomocí stochastického sestupu (SGD) a jak skóre, vyhodnocení a uložit model v Azure Blob Storage (WASB).

> [!TIP]
> Podle našich zkušeností může být problémy s konvergence LinearRegressionWithSGD modely a parametry musí být změněno či optimalizovanou pečlivě pro získání platný model. Škálování proměnných výrazně pomáhá s konvergence. 
> 
> 

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

**VÝSTUP:**

Koeficienty: [0.00457675809917-0.0226314167349,-0.0191910355236, 0.246793409578, 0.312047890459, 0.359634405999, 0.00928692253981,-0.000987181489428,-0.0888306617845, 0.0569376211553, 0.115519551711, 0.149250164995,- 0.00990211159703-0.00637410344522, 0.545083566179,-0.536756072402, 0.0105762393099,-0.0130117577055, 0.0129304737772,-0.00171065945959]

Zachycení: 0.853872718283

RMSE = 1.24190115863

R sqr = 0.608017146081

Čas potřebný k provedení nad buňkou: 58.42 sekund

### <a name="random-forest-regression"></a>Regrese Random doménové struktury
Kód v této části ukazuje, jak pro trénování, vyhodnotit a uložení náhodných doménové struktury regrese, který bude předpovídat částka tip pro data o jízdách NYC taxislužby.

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

**VÝSTUP:**

RMSE = 0.891209218139

R sqr = 0.759661334921

Čas potřebný k provedení nad buňkou: 49.21 sekund

### <a name="gradient-boosting-trees-regression"></a>Přechodu zvýšení skóre regresní stromy
Kód v této části ukazuje, jak pro trénování, vyhodnocení a uložení přechodu zvýšení skóre stromů modelu, který bude předpovídat částka tip pro data o jízdách NYC taxislužby.

**Natrénování a vyhodnocení**

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

**VÝSTUP:**

RMSE = 0.908473148639

R sqr = 0.753835096681

Čas potřebný k provedení nad buňkou: 34.52 sekund

**Vykreslení**

*tmp_results* je zaregistrovaný jako tabulku Hive v předcházejí buňky. Výsledky z tabulky jsou výstup do *sqlResults* datového rámce pro vykreslení. Zde je kód

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT * from tmp_results

Tady je kód pro vykreslení dat pomocí Jupyter serveru.

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


**VÝSTUP:**

![Skutečná vs předpovědět – tip objemy](./media/spark-data-exploration-modeling/actual-vs-predicted-tips.png)

## <a name="clean-up-objects-from-memory"></a>Vyčištění objektů z paměti
Použití `unpersist()` odstranit objekty uložené v mezipaměti v paměti.

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


## <a name="record-storage-locations-of-the-models-for-consumption-and-scoring"></a>Záznam umístění úložiště pro využití a vyhodnocování modelů
K využití a stanovíte jeho skóre nezávislé datové sadě služby podle [skóre a vyhodnocení modelů předdefinovaných ve Spark machine learning](spark-model-consumption.md) tématu, musíte zkopírovat a vložit tyto názvy soubor obsahující uložený modelů vytvořených tady do spotřeba Poznámkový blok Jupyter. Tady je kód, který vytiskne cesty k souborů modelů, které potřebujete existuje.

    # MODEL FILE LOCATIONS FOR CONSUMPTION
    print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
    print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
    print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
    print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
    print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
    print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";


**VÝSTUP**

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05-0317_03_23.516568"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-05-0317_05_21.577773"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05-0317_04_11.950206"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05-0317_06_08.723736"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05-0317_04_36.346583"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05-0317_06_51.737282"

## <a name="whats-next"></a>Co dále?
Teď, když jste vytvořili modely regrese a klasifikaci s knihovna Spark MlLib, jste připraveni se naučíte stanovení skóre a vyhodnocovat u nich tyto modely. Pokročilé zkoumání a modelování poznámkového bloku podrobně včetně křížového ověřování, hyperparametrické cílit na konkrétní, věnuje podrobnější a model hodnocení. 

**Využití modelu:** Informace o určení skóre a vyhodnocovat u nich klasifikačních a regresních modelů vytvořených v tomto tématu najdete v tématu [skóre a vyhodnocení modelů strojového učení předdefinovaných Spark](spark-model-consumption.md).

**Křížové ověření a hyperparameter sweeping**: Naleznete v tématu [rozšířené zkoumání a modelování se Sparkem](spark-advanced-data-exploration-modeling.md) na to, jak modely můžete pomocí křížového ověření a hyperparametrické sweeping školení

