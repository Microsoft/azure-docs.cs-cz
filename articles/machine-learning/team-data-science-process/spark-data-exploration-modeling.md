---
title: Zkoumání dat a modelování pomocí Spark | Microsoft Docs
description: Umožňující prezentovat funkce zkoumání a modelování dat sady nástrojů Spark MLlib v Azure.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: jhubbard
editor: cgronlun
ms.assetid: b989b918-5ba5-4696-b8d0-76ae510a23f4
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2017
ms.author: deguhath
ms.openlocfilehash: d505d432897eb7f4e792b45a95ec91ce1802eeb8
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="data-exploration-and-modeling-with-spark"></a>Zkoumání a modelování dat pomocí Spark
[!INCLUDE [machine-learning-spark-modeling](../../../includes/machine-learning-spark-modeling.md)]

Tento návod používá HDInsight Spark pro zkoumání dat a binární klasifikaci a regresní modelování úlohy na vzorku NYC taxi cestě a jízdenky 2013 datovou sadu.  Provede vás provede postupem [proces vědecké účely dat](http://aka.ms/datascienceprocess), klient server, pomocí clusteru služby HDInsight Spark pro zpracování a Azure objektů BLOB k ukládání dat a modely. Proces jsou zde popsány vizualizuje dat získaných z objektu Blob úložiště Azure a pak připraví dat za účelem vytvoření prediktivní modely. Tyto modely jsou sestavení pomocí sady nástrojů pro Spark MLlib provádění binární klasifikaci a regresní modelování úkolů.

* **Binární klasifikace** úloh je k předvídání, zda je tip placené pro cestu. 
* **Regrese** úloh je k předvídání množství tip podle dalších funkcí tip. 

Modely, které používáme zahrnují logistic a lineární regrese, náhodné doménové struktury a přechodu boosted stromů:

* [Lineární regrese s SGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) je model lineární regrese, který používá metodu Stochastického přechodu klesání (SGD) a škálování k předvídání objemy tip placené pro optimalizaci a funkce. 
* [Logistic regression s LBFGS](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) nebo "logit" regression je regresní model, který lze použít při kategorií udělat klasifikace dat je závislé proměnné. LBFGS je algoritmus optimalizace jako Newton, blíží algoritmus Broyden – Fletcher – Goldfarb – Shanno (BFGS) pomocí omezené množství paměti počítače a který se často používá v machine learning.
* [Náhodné doménových strukturách](http://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) jsou komplety rozhodovací stromy.  Že kombinují mnoho rozhodovacích stromů, aby se snížilo riziko overfitting. Náhodné doménovými strukturami se používají pro regresní a klasifikaci a dokáže zpracovat kategorií funkce a lze rozšířit pro nastavení více třídami klasifikace. Tyto nevyžadují funkce škálování a mohli zaznamenat nelineárností a funkci interakce. Náhodné doménových strukturách jsou jedním z těch nejúspěšnějších strojového učení modely pro klasifikaci a regrese.
* [Přechodu boosted stromy](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) jsou komplety rozhodovací stromy. GBTs cvičení stromů rozhodnutí interaktivně, aby se minimalizoval funkci ztrátu. GBTs se používají pro regresní a klasifikaci a dokáže zpracovat kategorií funkce, nevyžadují funkce škálování a mohli zaznamenat nelineárností a funkci interakce. Můžete také používají v nastavení multiclass klasifikace.

Modelování kroky také obsahovat kód znázorňující cvičení, hodnocení a uložit každý typ modelu. Python byla použita k code řešení a zobrazíte relevantní pozemků.   

> [!NOTE]
> I když toolkit Spark MLlib je navržen pro práci na velkých datových sad, relativně malé ukázkové (pomocí 170 tisíc řádků, přibližně 0,1 % původní datové sady NYC ~ 30 Mb) se zde používá ke zvýšení pohodlí. Cvičení zadané tady běží efektivně (v přibližně 10 minut) v clusteru HDInsight s 2 uzlů pracovního procesu. Stejný kód, s menšími změnami, můžete použít ke zpracování větší-sady dat, se změny, které pro ukládání do mezipaměti data v paměti a změna velikosti clusteru.
> 
> 

## <a name="prerequisites"></a>Požadavky
Budete potřebovat účet Azure a Spark 1.6 (nebo Spark 2.0) clusteru HDInsight k dokončení tohoto postupu. Najdete v článku [přehled o vědecké zpracování dat pomocí Spark v Azure HDInsight](spark-overview.md) pokyny o tom, jak splnit tyto požadavky. Toto téma obsahuje také popis NYC 2013 taxíkem data použít se zde a pokyny, jak provést kód z poznámkového bloku Jupyter v clusteru Spark. 

## <a name="spark-clusters-and-notebooks"></a>Clustery Spark a poznámkových bloků
Kroky instalace a kódu jsou uvedené v tomto názorném postupu pro používání HDInsight Spark 1.6. Ale poznámkové bloky Jupyter jsou k dispozici pro clustery HDInsight Spark 1.6 a Spark 2.0. Popis poznámkových bloků a odkazy na ně jsou součástí [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) úložiště Githubu, které je obsahují. Kromě toho kód sem a v propojených poznámkových bloků je obecný a by měla fungovat v jakémkoliv clusteru Spark. Pokud nepoužíváte HDInsight Spark, může být mírně lišit od co je tady uvedené kroky nastavení a Správa clusteru. Pro větší pohodlí si zde jsou uvedeny odkazy na poznámkové bloky Jupyter pro Spark 1.6 (musí být spuštěny v jádra pySpark Poznámkový blok Jupyter serveru) a 2.0 Spark (Chcete-li spustit v jádru pySpark3 Poznámkový blok Jupyter serveru):

### <a name="spark-16-notebooks"></a>Spark 1.6 poznámkových bloků

[pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb): poskytuje informace o tom, jak provést zkoumání dat, modelování a vyhodnocování se několik různých algoritmů.

### <a name="spark-20-notebooks"></a>Spark 2.0 poznámkových bloků
Regrese a klasifikace úlohy, které jsou implementovány pomocí clusteru Spark 2.0 jsou v samostatných poznámkových bloků a poznámkového bloku klasifikaci používá jinou sadu dat:

- [Spark2.0-pySpark3-Machine-Learning-data-Science-Spark-Advanced-data-Exploration-Modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Tento soubor obsahuje informace o tom, jak provést zkoumání dat, modelování a vyhodnocování v rámci Spark 2.0 clusterů pomocí cesty NYC taxíkem a tarif popsané na sadu dat [zde](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data). Tento poznámkový blok, může být to dobrý výchozí bod pro zkoumání rychle kód, který jsme připravili pro Spark 2.0. Podrobnější Poznámkový blok analyzuje data taxíkem NYC, naleznete další poznámkového bloku v tomto seznamu. Najdete v poznámkách k následující tento seznam porovnávající tyto poznámkových bloků. 
- [Spark2.0 pySpark3_NYC_Taxi_Tip_Regression.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_NYC_Taxi_Tip_Regression.ipynb): Tento soubor ukazuje, jak provést data wrangling (Spark SQL a dataframe operations), zkoumání, modelování a vyhodnocování pomocí cesty NYC taxíkem a tarif sady dat popsané [sem ](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data).
- [Spark2.0 pySpark3_Airline_Departure_Delay_Classification.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_Airline_Departure_Delay_Classification.ipynb): Tento soubor ukazuje, jak provést data wrangling (Spark SQL a dataframe operations), zkoumání, modelování a vyhodnocování pomocí známých na čas odeslání letecká společnost datové sady z 2011 a 2012. Jsme integrované letecká společnost datovou sadu s daty počasí letiště (např. větru, teploty, výška atd.) před modelování, takže tyto funkce počasí můžou být součástí modelu.

<!-- -->

> [!NOTE]
> Datová sada letecká společnost byl přidán do poznámkových bloků Spark 2.0 abychom vám lépe předvedli použití algoritmů klasifikace. V následujících tématech informace o letecká společnost v době odeslání datovou sadu a počasí datové sady:

>- Data na čas odeslání letecká společnost: [http://www.transtats.bts.gov/ONTIME/](http://www.transtats.bts.gov/ONTIME/)

>- Letiště počasí data: [https://www.ncdc.noaa.gov/](https://www.ncdc.noaa.gov/) 
> 
> 

<!-- -->

<!-- -->

> [!NOTE]
Poznámkové bloky Spark 2.0 na NYC taxíkem a letecká společnost letu zpoždění-sady dat může trvat 10 minut nebo déle ke spuštění (v závislosti na velikosti vašeho clusteru HDI). První poznámkového bloku v seznamu nahoře zobrazí mnoho aspektů zkoumání dat, vizualizace a ML školení v poznámkovém bloku, který zabere to méně času se spouští s nižší vzorkovat NYC datové sady, ve kterém byly soubory taxíkem a tarif předem připojený k modelu: [Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb) tento poznámkový blok trvá mnohem kratší dobu dokončit (v minutách 2-3) a může být dobrou výchozí bod pro rychle prohlížení kódu uvádíme Spark 2.0. 

<!-- -->

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

<!-- -->

> [!NOTE]
Níže jsou související s použitím Spark 1.6. Verze Spark 2.0 použijte poznámkových bloků popsané a uvedený výše. 

<!-- -->

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Instalace: umístění úložiště, knihovny a kontext přednastavené Spark
Spark se bude moct číst a zapisovat do Azure Blob Storage (WASB). Takže existující data uložená existuje může zpracovat pomocí Spark a výsledky uložené v WASB znovu.

Cesta k uložení modely nebo souborů v WASB, je třeba zadat správně. Výchozí kontejner, který je připojen ke clusteru Spark se může odkazovat pomocí cesty od verze: "wasb: / / /". Odkazují jiné umístění "wasb: / /".

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Nastavení cesty adresáře pro umístění úložiště v WASB
Následující příklad kódu určuje umístění dat ke čtení a cesty k adresáři modelu úložiště, kde je uložen výstupní modelu:

    # SET PATHS TO FILE LOCATIONS: DATA AND MODEL STORAGE

    # LOCATION OF TRAINING DATA
    taxi_train_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv";

    # SET THE MODEL STORAGE DIRECTORY PATH 
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS NEEDED.
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 


### <a name="import-libraries"></a>Importovat knihovny
Nastavení taky vyžaduje import potřebné knihovny. Nastavit kontext spark a importovat potřebné knihovny s následujícím kódem:

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


### <a name="preset-spark-context-and-pyspark-magics"></a>Předvolby kontextu Spark a Magic PySpark
Jádra PySpark, které jsou k dispozici s poznámkovými bloky Jupyter mít přednastavené kontextu. Proto není potřeba nastavit Spark nebo vývoji Hive kontexty explicitně před zahájením práce s aplikací. Tyto kontexty jsou dostupné ve výchozím nastavení. Tyto kontexty jsou:

* sc - pro Spark 
* sqlContext - pro Hive

Poskytuje jádra PySpark některé předdefinované "Magic", které jsou speciální příkazy, které můžete volat s %%. Existují dva takové příkazy, které se používají v tyto ukázky kódu.

* **%% místní** Určuje, že kód v dalších řádcích se má provádět místně. Kód musí být platný kód Python.
* **%% sql -o <variable name>**  provede dotaz Hive proti sqlContext. Pokud je předán parametr -o, výsledek dotazu je uchován v %% lokální kontext Python jako Pandas DataFrame.

Pro další informace o jádrech pro poznámkové bloky Jupyter a předdefinovanou "magics", poskytují, najdete v části [jádra dostupná pro poznámkové bloky Jupyter s HDInsight Spark Linux clusterů v HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

## <a name="data-ingestion-from-public-blob"></a>Přijímání dat z veřejného objektu blob
Prvním krokem v procesu vědecké účely dat je ingestují data, která má být analyzován z zdrojů kde je umístěn do vašeho prostředí zkoumání a modelování data. Prostředí je Spark v tomto návodu. Tato část obsahuje kód pro dokončení řadu úloh:

* ingestování vzorek dat modelovat
* čtení ve vstupní datové sady (uložený jako soubor TSV)
* formátování a vyčištění dat.
* vytvářet a ukládat do mezipaměti objektů (RDDs nebo datových rámců) v paměti
* Zaregistrujte se jako dočasné tabulky v kontextu SQL.

Zde je kód pro přijímat data.

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

Doba k provedení výše buňky: 51.72 sekund

## <a name="data-exploration--visualization"></a>Zkoumání dat a vizualizaci
Jakmile data vstoupila v Spark, je dalším krokem v procesu vědecké účely dat získali lepší představu o dat přes zkoumání a vizualizace. V této části jsme zkontrolujte taxíkem dat pomocí dotazů SQL a vykreslení cílových proměnných a potenciální funkcí pro visual kontroly. Konkrétně jsme vykreslení četnost počty osobní v taxi cest, frekvenci tip objemu a jak se typy liší podle částka platby a typu.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Vykreslení histogram frekvencí počet osobní v ukázce taxíkem cest
Tento kód a následné fragmenty použijte k dotazování na ukázkové a místní magic k vykreslení dat SQL magic.

* **SQL magic (`%%sql`)** jádra PySpark HDInsight podporuje snadno vložené HiveQL dotazy proti sqlContext. (-O VARIABLE_NAME) argument potrvají výstup příkazu jazyka SQL jako Pandas DataFrame na serveru Jupyter. To znamená, že je k dispozici v místním režimu.
* **`%%local` Magic** slouží ke spouštění kódu místně na serveru Jupyter, což je headnode clusteru HDInsight. Obvykle použijete, `%%local` magic ve spojení s `%%sql` magic s parametrem -o. Parametr -o by zachovat výstup příkazu jazyka SQL místně a pak %% místní magic by aktivovat další sadu fragment kódu ke spouštění místně na výstupu dotazů SQL, který je místně trvalé

Výstup se automaticky vizualizuje po spuštění kódu.

Tento dotaz načte služebních cest podle počtu osobní. 

    # PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

    # HIVEQL QUERY AGAINST THE sqlContext
    %%sql -q -o sqlResults
    SELECT passenger_count, COUNT(*) as trip_counts 
    FROM taxi_train 
    WHERE passenger_count > 0 and passenger_count < 7 
    GROUP BY passenger_count 

Tento kód vytvoří místní data snímku z výstupu dotazu a ukazuje zeměpisný data. `%%local` Magic vytvoří místní rámce dat, `sqlResults`, který může být použit pro vykreslení s matplotlib. 

> [!NOTE]
> Tato PySpark magic se používá více než jednou. v tomto návodu. Pokud je velké množství dat, by měl ukázkové k vytvoření data rámce, který můžete začlenit do místní paměti.
> 
> 

    #CREATE LOCAL DATA-FRAME AND USE FOR MATPLOTLIB PLOTTING

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
    # CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
    sqlResults

Zde je kód k vykreslení služebních cest dle počtů osobní

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

![Frekvence cestě podle počtu osobní](./media/spark-data-exploration-modeling/trip-freqency-by-passenger-count.png)

Můžete vybrat mezi několika různých typů vizualizace (tabulky, kruhový, řádku, oblasti nebo panelu) pomocí **typ** tlačítka nabídky v poznámkovém bloku. Vykreslení panelu se zobrazí tady.

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Vykreslení histogram tip objemy a jak se liší podle počtu a tarif objemy osobní tip velikost.
Pomocí příkazu jazyka SQL ukázková data.

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


Tuto buňku kód používá k vytvoření tři pozemků data dotazu SQL.

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

![Tip rozdělení částky](./media/spark-data-exploration-modeling/tip-amount-distribution.png)

![Tip velikost podle počtu osobní](./media/spark-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Tip velikost podle velikosti tarif](./media/spark-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>Funkce analýzy, transformaci a data přípravy pro modelování
Tato část popisuje a poskytuje kód pro postupy, které slouží k přípravě dat pro použití v ML modelování. Ukazuje, jak provést následující úkoly:

* Vytvořit novou funkci přihrádkování čas do provozu čas intervalů
* Index a kódování kategorií funkce
* Vytváření objektů s popiskem bod pro vstup do funkce ML
* Vytvořit náhodné dílčí vzorkování dat a rozdělit ho na trénování a testování sad
* Funkce škálování
* Objekty mezipaměti v paměti

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Vytvořit novou funkci přihrádkování čas do provozu čas intervalů
Tento kód ukazuje postup vytvořit novou funkci přihrádkování čas do kbelíků čas provoz a výsledné datové rámce v paměti do mezipaměti. Pokud se opakovaně používají odolné distribuované datové sady (RDDs) a datových rámců, ukládání do mezipaměti vede k lepší časy spuštění. Podle toho jsme mezipaměti RDDs a datové rámce v několika fázích v návodu. 

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

### <a name="index-and-encode-categorical-features-for-input-into-modeling-functions"></a>Index a kódování kategorií funkce pro vstup do funkce modelování
V této části ukazuje, jak index nebo kódování kategorií funkce pro vstup do funkce modelování. Modelování a předvídání funkce MLlib vyžadují funkce s kategorií vstupní data na indexované nebo kódovaný před použití. V závislosti na modelu budete muset index nebo zakódovat je různými způsoby:  

* **Na základě stromu modelování** vyžaduje kategorií kódovaný jako číselné hodnoty (například funkce s tří kategorií může být kódován 0, 1, 2). To zajišťuje na MLlib [StringIndexer](http://spark.apache.org/docs/latest/ml-features.html#stringindexer) funkce. Tato funkce kóduje sloupec řetězce popisků ke sloupci popisek indexy, které jsou seřazené podle četnosti popisek. I když indexované číselné hodnoty vstupu a manipulaci s daty, k je považovat za správně kategorií lze zadat algoritmů na základě stromu. 
* **Modely Logistic a lineární regrese** vyžadují jeden horkou kódování, kde, například funkce s tří kategorií lze rozšířit na tři sloupce funkce, s každou obsahující 0 nebo 1 v závislosti na kategorii pozorování. Poskytuje MLlib [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) funkce udělat za provozu jeden kódování. Tato kodér mapuje sloupec popisek indexů ke sloupci binárního vektory, s maximálně jednu jeden – hodnotu. Toto kódování umožňuje algoritmy, které očekávají číselných hodnot funkce, jako je logistic regression, má být použita pro kategorií funkce.

Tady je kód pro index a kódování kategorií funkce:

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

Doba k provedení výše buňky: 1,28 sekund

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Vytváření objektů s popiskem bod pro vstup do funkce ML
Tato část obsahuje kód, který ukazuje, jak index kategorií textová data jako datový typ s popiskem bodu a jeho kódování, aby se může použít k trénování a testování MLlib logistic regression a jinými modely klasifikace. S popiskem bodu objekty jsou odolné distribuované datové sady (RDD) formátu způsobem, který je nutný jako vstupní data pro většinu ML algoritmy v MLlib. A [s názvem bez přípony bodu](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) je přidružen místní vektoru hustých nebo zhuštění, popisek nebo odpověď.  

Tato část obsahuje kód, který ukazuje, jak index kategorií textová data jako [s názvem bez přípony bodu](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) datový typ a zakódovat je, aby se může použít k trénování a testování MLlib logistic regression a jinými modely klasifikace. S popiskem bodu objekty jsou odolné distribuované datové sady (RDD) skládající se z funkce vector a popisku (cíl a odpověď proměnné). Tento formát je potřeba jako vstup mnoho algoritmy ML v MLlib.

Tady je kód pro index a kódování textu funkce pro binární klasifikaci.

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

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC RERESSION MODELS
    def parseRowOneHotBinary(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        labPt = LabeledPoint(line.tipped, features)
        return  labPt


Tady je kód ke kódování a indexu kategorií text funkce pro analýzu lineární regrese.

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


### <a name="create-a-random-sub-sampling-of-the-data-and-split-it-into-training-and-testing-sets"></a>Vytvořit náhodné dílčí vzorkování dat a rozdělit ho na trénování a testování sad
Tento kód vytvoří náhodné vzorky dat (25 % tady slouží). I když to není nutné v tomto příkladu kvůli překročení velikosti datové sady, ukážeme, jak můžete vzorkovat tady, víte, jak používat pro váš vlastní problém v případě potřeby. Po velká vzorky lze ušetřit čas významné při školení modelů. Další jsme rozdělit vzorku na školení část (v tomto poli 75 %) a testování částí (zde 25 %) pro použití v klasifikaci a regresní modelování.

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

Doba k provedení nad buňku: 0,24 sekund

### <a name="feature-scaling"></a>Funkce škálování
Funkce škálování, známé taky jako data normalizaci zajistí, že funkce široce Celková uhrazená hodnotami není zadaný nadměrné naváží ve funkci cíle. Kód pro funkci škálování používá [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) funkce, které chcete odchylku jednotky škálování. Pochází od MLlib pro použití v lineární regrese s Stochastického přechodu klesání (SGD), oblíbených algoritmus pro trénování širokou škálu jiných modelů strojového učení například Vyřešeno regresí nebo support vector počítače (SVM).

> [!NOTE]
> Našli jsme algoritmus LinearRegressionWithSGD být citlivé funkce škálování.
> 
> 

Tady je kód, který škálování proměnné pro použití s regularized lineární SGD algoritmus.

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

Doba k provedení výše buňky: 13.17 sekund

### <a name="cache-objects-in-memory"></a>Objekty mezipaměti v paměti
Ukládání do mezipaměti na vstupní data rámce objekty používá pro klasifikaci, regrese a rozšířit funkce může snížit čas potřebný pro trénování a testování ML algoritmů.

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

Doba k provedení nad buňku: 0,15 sekund

## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>Předpovědět, zda je tip placené s modely binární klasifikace
Tato část uvádí, jak použít tři modely pro predikci úlohy binární klasifikace zda tip je placené taxíkem cesty. Modely uvedené jsou:

* Vyřešeno logistic regression 
* Model náhodných doménové struktury
* Přechodu zvýšení skóre stromů

Každý model vytváření části kódu je rozdělená do kroků: 

1. **Model školení** dat pomocí jednu sadu parametrů
2. **Model vyhodnocení** na testovací datové sady s metriky
3. **Ukládání modelu** v objektu blob pro budoucí spotřeba

### <a name="classification-using-logistic-regression"></a>Klasifikace pomocí logistic regression
Kód v této části ukazuje, jak pro trénování, hodnocení a uložit logistic regresní model s [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) který předpovídá, zda je tip zaplacení cesty v NYC taxíkem služební cestě a tarif datovou sadu.

**Trénování modelu logistic regression pomocí odchylka nákladů a hyperparameter (vymetání) komínů**

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

Koeficienty: [0.0082065285375-0.0223675576104,-0.0183812028036, - 3.48124578069e-05-0.00247646947233,-0.00165897881503, 0.0675394837328,-0.111823113101,-0.324609912762,-0.204549780032,-1.36499216354, 0.591088507921,-0.664263411392,-1.00439726852, 3.46567827545,-3.51025855172,-0.0471341112232,-0.043521833294, 0.000243375810385, 0.054518719222]

Zachycení:-0.0111216486893

Doba k provedení výše buňky: 14.43 sekund

**Vyhodnocení modelu binární klasifikace s standardní metriky**

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

Oblasti v rámci PR = 0.985297691373

Oblasti v rámci ROC = 0.983714670256

Souhrnné statistiky

Přesnost = 0.984304060189

Odvolat = 0.984304060189

F1 Stanovení skóre = 0.984304060189

Doba k provedení výše buňky: 57.61 sekund

**Vykreslení křivka ROC.**

*PredictionAndLabelsDF* je zaregistrován jako tabulku, *tmp_results*, v předchozí buňku. *tmp_results* slouží k proveďte dotazy a výstup výsledků do sqlResults dat rámce pro vykreslení. Zde je kód.

    # QUERY RESULTS                              
    %%sql -q -o sqlResults
    SELECT * from tmp_results


Tady je kód vykreslení křivka ROC a provádět předpovědi.

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

![Logistic regression ROC curve.png](./media/spark-data-exploration-modeling/logistic-regression-roc-curve.png)

### <a name="random-forest-classification"></a>Klasifikace náhodných doménové struktury
Kód v této části ukazuje, jak pro trénování, hodnocení a uložit model náhodných doménové struktury, který předpovídá, zda je tip zaplacení cesty v NYC taxíkem služební cestě a tarif datovou sadu.

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

Oblasti v rámci ROC = 0.985297691373

Doba k provedení výše buňky: 31.09 sekund

### <a name="gradient-boosting-trees-classification"></a>Přechodu zvýšení skóre klasifikace stromů
Kód v této části ukazuje, jak cvičení, vyhodnotit a uložte přechodu zvýšení skóre stromy model, který předpovídá, zda je pro cesty v cestě taxíkem NYC placené tip a jízdenky datovou sadu.

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

Oblasti v rámci ROC = 0.985297691373

Doba k provedení výše buňky: 19.76 sekund

## <a name="predict-tip-amounts-for-taxi-trips-with-regression-models"></a>Předpověď objemy tip pro služebních cest taxíkem s modely regrese
Tato část uvádí, jak použít tři modely pro predikci množství tip úlohy regrese placené taxíkem cesty na základě jiných funkcí tip. Modely uvedené jsou:

* Vyřešeno lineární regrese
* Náhodné doménové struktury
* Přechodu zvýšení skóre stromů

Tyto modely byly popsané v úvodu. Každý model vytváření části kódu je rozdělená do kroků: 

1. **Model školení** dat pomocí jednu sadu parametrů
2. **Model vyhodnocení** na testovací datové sady s metriky
3. **Ukládání modelu** v objektu blob pro budoucí spotřeba

### <a name="linear-regression-with-sgd"></a>Lineární regrese s SGD
Kód v této části ukazuje, jak používat škálovat funkce ke cvičení lineární regrese, který stochastického přechodu klesání (SGD) používá pro optimalizaci a jak stanovení skóre, hodnocení a uložit model v Azure Blob Storage (WASB).

> [!TIP]
> V našich zkušeností může být problémy s konvergence LinearRegressionWithSGD modelů a parametry musí být optimalizované pečlivě pro získání platný model jejich změnit. Škálování proměnných výrazně pomáhá s konvergence. 
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

Koeficienty: [0.00457675809917,-0.0226314167349,-0.0191910355236, 0.246793409578, 0.312047890459, 0.359634405999, 0.00928692253981,-0.000987181489428,-0.0888306617845, 0.0569376211553, 0.115519551711, 0.149250164995,-0.00990211159703,-0.00637410344522, 0.545083566179,-0.536756072402, 0.0105762393099,-0.0130117577055, 0.0129304737772,-0.00171065945959]

Zachytávat: 0.853872718283

RMSE = 1.24190115863

R sqr = 0.608017146081

Doba k provedení výše buňky: 58.42 sekund

### <a name="random-forest-regression"></a>Regrese náhodných doménové struktury
Kód v této části ukazuje, jak cvičení, hodnocení a uložit regrese náhodných doménové struktury, který předpovídá velikost tip pro data NYC taxíkem cesty.

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

Doba k provedení výše buňky: 49.21 sekund

### <a name="gradient-boosting-trees-regression"></a>Přechodu zvýšení skóre regresní stromy
Kód v této části ukazuje, jak pro trénování, hodnocení a uložit přechodu zvýšení skóre stromy model, který předpovídá velikost tip pro data NYC taxíkem cesty.

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

    # CONVER RESULTS TO DF AND REGISER TEMP TABLE
    test_predictions = sqlContext.createDataFrame(predictionAndLabels)
    test_predictions.registerTempTable("tmp_results");

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**VÝSTUP:**

RMSE = 0.908473148639

R sqr = 0.753835096681

Doba k provedení výše buňky: 34.52 sekund

**Vykreslení.**

*tmp_results* je registrován jako tabulku Hive v předchozí buňku. Výsledky z tabulky se výstup do *sqlResults* dat rámce pro vykreslení. Zde je kód

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT * from tmp_results

Tady je kód k vykreslení data s využitím serveru Jupyter.

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

![Skutečný vs předpovědět tip objemy](./media/spark-data-exploration-modeling/actual-vs-predicted-tips.png)

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


## <a name="record-storage-locations-of-the-models-for-consumption-and-scoring"></a>Umístění úložiště záznam modely pro využívání a vyhodnocování
Spotřebovat a stanovení skóre nezávislé datové sadě služby popsané v [skóre a vyhodnocení modelů vytvořené Spark strojové učení](spark-model-consumption.md) tématu, musíte zkopírovat a vložit tyto názvy souborů obsahující uložené modely vytvořená zde do poznámkového bloku Jupyter spotřeby. Tady je kód, který vytiskněte cesty k souborům modelu, potřebujete existuje.

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
Teď, když jste vytvořili regrese a klasifikace modely s Spark MlLib, jste připraveni se dozvíte, jak pro stanovení skóre a vyhodnocení těchto modelů. Zkoumání pokročilé dat a modelování poznámkového bloku dives hlubší do včetně křížového ověřování, technologie hyper parametr komínů a vyhodnocení modelu. 

**Model spotřeby:** další postup stanovení skóre a vyhodnocení modelů klasifikace a regrese vytvořené v tomto tématu najdete v tématu [skóre a vyhodnocení modelů learning vytvořené Spark počítač](spark-model-consumption.md).

**Křížové ověření a hyperparameter (vymetání) komínů**: najdete v části [Advanced zkoumání dat a modelování pomocí Spark](spark-advanced-data-exploration-modeling.md) na tom, jak může být modely Trénink pomocí (vymetání) křížové ověření a technologie hyper parametr komínů

