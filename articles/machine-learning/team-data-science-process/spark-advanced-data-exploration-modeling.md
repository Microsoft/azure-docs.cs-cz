---
title: Pokročilé zkoumání a modelování se Sparkem | Dokumentace Microsoftu
description: Pomocí HDInsight Spark ke zkoumání dat a trénování binární klasifikačních a regresních modelů pomocí křížového ověření a hyperparameter optimalizace.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: jhubbard
editor: cgronlun
ms.assetid: f90d9a80-4eaf-437b-a914-23514390cd60
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: deguhath
ms.openlocfilehash: a81c23d6acb79e42157ac7d804dac259723b3b0e
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37114346"
---
# <a name="advanced-data-exploration-and-modeling-with-spark"></a>Pokročilé zkoumání a modelování dat pomocí Spark
[!INCLUDE [machine-learning-spark-modeling](../../../includes/machine-learning-spark-modeling.md)]

Tento návod používá ke zkoumání dat a trénování binární klasifikace a regresních modelů pomocí křížového ověření HDInsight Spark a optimalizace hyperparameter k ukázce NYC taxi cesty a jízdenky 2013 datové sady. Provede vás provede postupem [vědecké zpracování dat](http://aka.ms/datascienceprocess), end až do konce, použití cluster HDInsight Spark pro zpracování a Azure BLOB k ukládání dat a modely. Proces zkoumá a vizualizuje dat získaných z objektu Blob služby Azure Storage a pak připraví data k vytvoření prediktivních modelů. Python se použil, jak se programují řešení a zobrazíte relevantní vykreslení. Tyto modely jsou sestavení pomocí nástrojů knihovna Spark MLlib vykonávat binární klasifikačních a regresních modelování. 

* **Binární klasifikace** úkol je předpovědět, jestli jde placenou tip pro cestu. 
* **Regrese** úkolu je předpovědět, částky spropitného založených na jiných funkcích tip. 

Kroky modelování také obsahovat kód ukazující způsob trénování, vyhodnocení a uložte každý typ modelu. Téma pokrývá některé stejném základu jako [pro zkoumání a modelování se Sparkem](spark-data-exploration-modeling.md) tématu. Ale to je "rozšířené", také používá cross-validation s hyperparameter cílit na konkrétní k trénování klasifikačních a regresních modelů optimálně přesné. 

**Křížového ověření (CV)** je technika, která vyhodnocuje, jak dobře zobecňuje modelů trénovaných na známé sady dat se predikce funkcí datové sady, na kterém je nebyla cvičena.  Běžnou implementaci se tady použít je K přeložení rozdělit datovou sadu a pak trénování modelu způsobem kruhové dotazování na všechny kromě jednoho složení. Možnost model k predikci přesně, při testování proti nezávislé datové sady v této fold nelze použít pro trénování modelu je použit k vyhodnocení.

**Optimalizace Hyperparameter** potížím, které vyberete sadu hyperparameters pro algoritmu učení, obvykle s cílem optimalizovat míru výkonu algoritmus v nezávislé datové sady. **Hyperparameters** jsou hodnoty, které musí být zadán mimo proces trénování modelu. Předpoklady o tyto hodnoty může ovlivnit, flexibilitu a přesnost modelu. Rozhodovací stromy mají hyperparameters, například, jako je například požadovaný hloubky a počet listů ve stromové struktuře. Support Vector počítače (SVMs) vyžaduje, aby byla termín chybnou penalizace. 

Běžný způsob, jak provádět optimalizaci hyperparameter se tady použít je mřížka vyhledávání, nebo **Uklízení parametrů**. To se skládá z provádění dokončil důkladné prohledání prostřednictvím hodnoty podmnožinu místo hyperparameter zadaný pro algoritmus učení. Křížového ověření může poskytovat metrika výkonu optimální výsledky vytvořené metodou mřížky vyhledávacího algoritmu řazení. CV použít s hyperparameter úklidem pomůže omezit problémy, například overfitting modelu na trénovací data tak, aby model uchovává kapacity použít na obecné sadu dat, ze které byl extrahován trénovací data.

Modely, které používáme zahrnují logistické a lineární regrese, náhodných doménové struktury a přechodu Posílený stromové struktury:

* [Lineární regrese s SGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) je model lineární regrese, který používá metodu pomocí Stochastického přechodu sestup (SGD) a pro optimalizaci a funkce škálování předpovídat špičky částky zaplacené. 
* [Logistickou regresi s LBFGS](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) nebo regresní model, který se dá použít při závislé proměnné je zařazená do kategorií provedete klasifikace dat je "logit" regrese. LBFGS je dál Newton optimalizace algoritmus, který blíží algoritmu Broyden – Fletcher – Goldfarb – Shanno (BFGS) pomocí omezené množství paměti a, která se často používá ve službě machine learning.
* [Náhodné doménových struktur](http://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) jsou umožňující rozhodovacích stromů.  Jejich kombinací mnoha rozhodovacích stromů, aby se snížilo riziko overfitting. Náhodné doménové struktury se používají pro regresní a klasifikace a dokáže zpracovat zařazené do kategorií funkcí a je možné rozšířit na nastavení klasifikace víc tříd. Se nevyžadují, aby funkce škálování a budou moct zachytit nelineárností a funkce interakce. Náhodné doménových struktur jsou jednou z nejvíce úspěšný strojového učení pro klasifikačních a regresních modelů.
* [Přechod boosted stromů](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) jsou umožňující rozhodovacích stromů. GBTs trénování rozhodovacích stromů zavádět postupně, chcete-li minimalizovat ztrátu funkce. GBTs se používají pro regresní a klasifikace a dokáže zpracovat zařazené do kategorií funkce, nevyžadují, aby funkce škálování a budou moct zachytit nelineárností a funkce interakce. Můžete také používají v nastavení multiclass klasifikace.

Příklad použití CV a Hyperparameter modelování oblouku jsou platné pro binárního klasifikačního problému. Jednodušší příklady (bez parametrů přesune) jsou uvedeny v hlavní téma pro regresní úlohy. Ale v dodatku, jsou také uvedeny ověření pomocí elastických net pro lineární regrese a CV pomocí parametru parametrů pro regresní náhodné doménové struktury. **Elastic net** je vyřešeno regrese metody pro přizpůsobení modely lineární regrese, který lineárně kombinuje metriky L1 a L2 jako sankce [laso](https://en.wikipedia.org/wiki/Lasso%20%28statistics%29) a [ridge](https://en.wikipedia.org/wiki/Tikhonov_regularization)metody.   

> [!NOTE]
> Přestože knihovna Spark MLlib toolkit je navržen pro práci na velkých datových sad, relativně malým vzorkem (pomocí 170 tisíc řádků, přibližně u 0,1 % původní datové sady NYC ~ 30 Mb) se tady používá ke zvýšení pohodlí. Cvičení zde běží na clusteru služby HDInsight pomocí 2 pracovní uzly efektivně (během 10 minut). Stejný kód s malými změnami lze použít ke zpracování větší – sady dat se změny, které vyžaduje pro ukládání dat v paměti do mezipaměti a změna velikosti clusteru.
> 
> 

## <a name="setup-spark-clusters-and-notebooks"></a>Instalační program: Clustery Spark a poznámkové bloky
Postup instalace a kódu jsou k dispozici v tomto názorném postupu pro používání HDInsight Spark 1.6. Ale poznámkové bloky Jupyter jsou k dispozici pro clustery HDInsight Spark 1.6 i Spark 2.0. Popis poznámkových bloků a odkazy na nich jsou součástí [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) pro úložiště GitHub, které je obsahují. Kromě toho kód tady v propojených poznámkových bloků je obecný a by mělo fungovat jakéhokoli jiného clusteru Spark. Pokud nepoužíváte HDInsight Spark, může být mírně lišit od co je znázorněna zde kroky instalace a správy clusteru. Pro usnadnění práce tady jsou odkazy na poznámkové bloky Jupyter pro Spark 1.6 a 2.0 musí být spuštěny v jádra pyspark aplikace Jupyter Notebook server:

### <a name="spark-16-notebooks"></a>Poznámkové bloky Spark 1.6

[pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): obsahuje témata v poznámkovém bloku #1 a vývoje modelů pomocí hyperparametrů a křížového ověření.

### <a name="spark-20-notebooks"></a>Poznámkové bloky Spark 2.0

[Spark2.0-pySpark3-Machine-Learning-data-Science-Spark-Advanced-data-Exploration-Modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Tento soubor obsahuje informace o tom, jak provádět zkoumání dat, modelování a vyhodnocování v clusterech Spark 2.0.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

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
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";

    # PRINT START TIME
    import datetime
    datetime.datetime.now()

**VÝSTUP**

DateTime.DateTime (2016, 4, 18, 17, 36, 27, 832799)

### <a name="import-libraries"></a>Importovat knihovny
Naimportujte potřebné knihovny s následujícím kódem:

    # LOAD PYSPARK LIBRARIES
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

## <a name="data-ingestion-from-public-blob"></a>Příjem dat z veřejných objektů blob:
Prvním krokem při vědecké zpracování dat je k ingestování dat má být analyzován ze zdrojů, kde se nachází na zkoumání a modelování prostředí. Toto prostředí je Spark v tomto názorném postupu. Tato část obsahuje kód pro dokončení řadu úkolů:

* ingestování vzorek dat modelovat
* přečíst vstupní datové sady (uložený jako soubor TSV)
* formátování a vyčištění dat
* vytvářet a ukládat do mezipaměti (Rdd nebo datové rámce) objektů v paměti
* Zaregistrujte se jako dočasné tabulky v kontextu SQL.

Zde je kód pro ingestování.

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

    # CACHE & MATERIALIZE DATA-FRAME IN MEMORY. GOING THROUGH AND COUNTING NUMBER OF ROWS MATERIALIZES THE DATA-FRAME IN MEMORY
    taxi_df_train_cleaned.cache()
    taxi_df_train_cleaned.count()

    # REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
    taxi_df_train_cleaned.registerTempTable("taxi_train")

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**VÝSTUP**

Čas potřebný k provedení nad buňkou: 276.62 sekund

## <a name="data-exploration--visualization"></a>Zkoumání a vizualizace
Jakmile data, nemůžete do Sparku, je dalším krokem v vědecké zpracování dat získali lepší představu o datech prostřednictvím zkoumání a vizualizaci. V této části jsme zkoumat data taxislužby města pomocí dotazů SQL a vykreslení cíl proměnné a pomáhala potenciálním funkce pro vizuální kontrolu. Konkrétně jsme vykreslení frekvence počty osobní v cesty taxíkem frekvence částky tip a jak tipy lišit podle výše platby a typu.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Zobrazit histogram počtu frekvencí osobní v ukázce cesty taxíkem
Tento kód a další fragmenty použití magický příkaz jazyka SQL k dotazování ukázka a místní magic k vykreslení data.

* **Magický příkaz jazyka SQL (`%%sql`)** jádra PySpark HDInsight podporuje snadno vložené HiveQL dotazů kontext sqlContext. (-O VARIABLE_NAME) argument ukládá jako Pandas DataFrame na serveru Jupyter výstup příkazu jazyka SQL. To znamená, že je k dispozici v místním režimu.
* **`%%local` Magic** se používá ke spouštění kódu místně na serveru Jupyter, což je hlavního uzlu clusteru HDInsight. Obvykle použijete `%%local` magic po `%%sql -o` magic slouží ke spuštění dotazu. Parametr -o by trvalý výstup příkazu jazyka SQL místně. Pak bude `%%local` magic vyvolá další sadu fragmenty kódu do výstupu dotazů SQL, který obsahuje místní trvalé spouštět místně. Výstup se automaticky vizualizuje po spuštění kódu.

Tento dotaz načte zkracuje dobu odezvy podle počtu osobní. 

    # PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

    # SQL QUERY
    %%sql -q -o sqlResults
    SELECT passenger_count, COUNT(*) as trip_counts FROM taxi_train WHERE passenger_count > 0 and passenger_count < 7 GROUP BY passenger_count


Tento kód vytvoří místní datový rámec z výstupu dotazu a zobrazí data. `%%local` Magic vytvoří místní snímek dat, `sqlResults`, kterou lze použít pro vykreslení s matplotlib. 

> [!NOTE]
> Tato PySpark magic se používá více než jednou v tomto názorném postupu. Pokud je velké množství dat, by měl ukázkový vytvořte datový rámec, který se vejde do místní paměti.
> 
> 

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
    # CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
    sqlResults

Tady je kód pro vykreslení zkracuje dobu odezvy dle počtů osobní

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    import matplotlib.pyplot as plt
    %matplotlib inline

    # PLOT PASSENGER NUMBER VS TRIP COUNTS
    x_labels = sqlResults['passenger_count'].values
    fig = sqlResults[['trip_counts']].plot(kind='bar', facecolor='lightblue')
    fig.set_xticklabels(x_labels)
    fig.set_title('Counts of trips by passenger count')
    fig.set_xlabel('Passenger count in trips')
    fig.set_ylabel('Trip counts')
    plt.show()

**VÝSTUP**

![Frekvence zkracuje dobu odezvy podle počtu osobní](./media/spark-advanced-data-exploration-modeling/frequency-of-trips-by-passenger-count.png)

Můžete vybrat mezi několik různých typů vizualizace (tabulky, výsečové, řádek, oblasti nebo panelu) s použitím **typ** tlačítka nabídky v poznámkovém bloku. Pruhový graf je znázorněna zde.

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Zobrazte histogram tip částek a jak se liší podle částky počet a tarif osobní velikost špičky.
Použít dotaz SQL na ukázková data...

    # SQL SQUERY
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

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    %matplotlib inline

    # TIP BY PAYMENT TYPE AND PASSENGER COUNT
    ax1 = resultsPDDF[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
    ax1.set_title('Tip amount distribution')
    ax1.set_xlabel('Tip Amount ($)')
    ax1.set_ylabel('Counts')
    plt.suptitle('')
    plt.show()

    # TIP BY PASSENGER COUNT
    ax2 = resultsPDDF.boxplot(column=['tip_amount'], by=['passenger_count'])
    ax2.set_title('Tip amount ($) by Passenger count')
    ax2.set_xlabel('Passenger count')
    ax2.set_ylabel('Tip Amount ($)')
    plt.suptitle('')
    plt.show()

    # TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
    ax = resultsPDDF.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(resultsPDDF.passenger_count))
    ax.set_title('Tip amount by Fare amount ($)')
    ax.set_xlabel('Fare Amount')
    ax.set_ylabel('Tip Amount')
    plt.axis([-2, 120, -2, 30])
    plt.show()


**VÝSTUP:** 

![Rozdělení částky tip](./media/spark-advanced-data-exploration-modeling/tip-amount-distribution.png)

![Tip částka podle počtu osobní](./media/spark-advanced-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Tip částka podle tarif velikost](./media/spark-advanced-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>Funkce analýzy, transformace a data přípravy pro modelování
Tato část popisuje a obsahuje kód pro procedury používané k přípravě dat pro použití v modelu ML. Ukazuje, jak provádět následující úlohy:

* Vytvořit novou funkci díky rozdělení hodin do přihrádek doba provozu
* Indexování a na horkou kódování zařazené do kategorií funkce
* Vytváření objektů označených bodu pro vstup do funkce ML
* Vytvořit náhodný dílčí vzorkování dat a rozdělit na trénování a testování sad
* Funkce škálování
* Mezipaměti objektů v paměti

### <a name="create-a-new-feature-by-partitioning-traffic-times-into-bins"></a>Vytvořit novou funkci díky rozdělení doby provozu do přihrádek
Tento kód ukazuje, jak vytvořit novou funkci díky rozdělení doby provozu do přihrádek a výsledný datový rámec v paměti do mezipaměti. Ukládání do mezipaměti vede k lepší doba provádění odolné distribuované sady rdd (Resilient) a datových rámců použití opakovaně. Proto jsme mezipaměti Rdd a datové rámce v několika fázích v tomto názorném postupu.

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

**VÝSTUP**

126050

### <a name="index-and-one-hot-encode-categorical-features"></a>Indexování a horkou jeden kódování zařazené do kategorií funkce
Tato část ukazuje, jak index nebo kódování zařazené do kategorií funkce pro vstup do funkce modelování. Modelování a předpovědět, funkce MLlib nastavení vyžadují indexované nebo kódovaný před použití funkce se vstupním kategorizovaná data. 

V závislosti na modelu budete muset index nebo kódování různými způsoby. Například Logistic a lineární regrese modely vyžadují jeden horkou kódování, kde, například se rozšířit funkce s tři kategorie do tří sloupců funkce s každou obsahující 0 nebo 1 podle kategorie hodnotu. Poskytuje MLlib [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) funkce dělat hot jeden kódování. Tomto kodéru mapuje sloupec indexů popisek ke sloupci binárního vektorů s maximálně jeden – hodnotu single. Toto kódování umožňuje algoritmy, které očekávají číselnými hodnotami funkce, jako je logistické regrese, použít zařazené do kategorií funkce.

Tady je kód pro index a kódování funkcí zařazené do kategorií:

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer

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


**VÝSTUP**

Čas potřebný k provedení nad buňkou: 3.14 sekund

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Vytváření objektů označených bodu pro vstup do funkce ML
Tato část obsahuje kód, který ukazuje, jak k indexování zařazené do kategorií textová data jako datový typ s popiskem bodu a jeho kódování. To připraví ho k trénování a testování logistické regrese MLlib a jiných modelů klasifikace. S popiskem bodových objektů jsou odolné distribuovaných datových sad (RDD) ve formátu způsobem, který je potřeba jako vstupní data pro většinu algoritmů ML v MLlib. A [označené jako bod](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) je přidružený místní vektoru, dense nebo zhuštěný, popisek a odpovědí.

Tady je kód pro indexování a kódování funkcí text pro binární klasifikaci.

    # FUNCTIONS FOR BINARY CLASSIFICATION

    # LOAD LIBRARIES
    from pyspark.mllib.regression import LabeledPoint
    from numpy import array

    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingBinary(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.pickup_hour, line.weekday,
                             line.passenger_count, line.trip_time_in_secs, line.trip_distance, line.fare_amount])
        labPt = LabeledPoint(line.tipped, features)
        return  labPt

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC RERESSION MODELS
    def parseRowOneHotBinary(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                   line.vendorVec.toArray(), line.rateVec.toArray(), line.paymentVec.toArray()), axis=0)
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
Tento kód vytvoří náhodný vzorkování dat (25 % používá zde). I když není nutné v tomto příkladu kvůli velikosti datové sady, vám ukážeme, jak můžete vzorkovat data zde. Potom můžete vědět, jak ho použít pro vlastní problém v případě potřeby. Když ukázky jsou velké, to ušetří spoustu času při trénování modelů. Dále jsme vzorku rozdělit na části školení (zde 75 %) a testování částí (zde 25 %) pro použití v zařazení a regresní modelování.

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # SPECIFY SAMPLING AND SPLITTING FRACTIONS
    from pyspark.sql.functions import rand

    samplingFraction = 0.25;
    trainingFraction = 0.75; testingFraction = (1-trainingFraction);
    seed = 1234;
    encodedFinalSampled = encodedFinal.sample(False, samplingFraction, seed=seed)

    # SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST, WITH A RANDOM COLUMN ADDED FOR DOING CV (SHOWN LATER)
    # INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS
    dfTmpRand = encodedFinalSampled.select("*", rand(0).alias("rand"));
    trainData, testData = dfTmpRand.randomSplit([trainingFraction, testingFraction], seed=seed);

    # CACHE TRAIN AND TEST DATA
    trainData.cache()
    testData.cache()

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

**VÝSTUP**

Čas potřebný k provedení nad buňkou: 0.31 sekund

### <a name="feature-scaling"></a>Funkce škálování
Funkce škálování, označované také jako data normalizace, zajistí, že funkce se značně Celková uhrazená hodnotami jsou neudělil nadměrné naváží ve funkci cíle. Kód pro funkce používá škálování [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) funkce, které se odchylka jednotek škálování. Podle MLlib je určen pro použití v lineární regrese s pomocí Stochastického přechodu sestup (SGD). SGD je Oblíbené algoritmů pro trénování širokou řadu jiných modelů strojového učení, jako je například Vyřešeno regresí nebo počítače vektorové podpory (SVM).   

> [!TIP]
> Našli jsme algoritmu LinearRegressionWithSGD brát ohled na funkce škálování.   
> 
> 

Tady je kód, který škálování proměnné pro použití s regularized lineárního algoritmu SGD.

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

**VÝSTUP**

Čas potřebný k provedení nad buňkou: 11.67 sekund

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

**VÝSTUP** 

Čas potřebný k provedení nad buňkou: 0,13 sekund

## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>Předpovědět, jestli je binární klasifikační modely placené tip
Tato část ukazuje, jak použijte tři modely pro úlohy binární klasifikace: předpověď, jestli jde placenou tip pro cesty taxíkem. Modely uvedené jsou:

* Logistické regrese 
* Náhodné doménové struktury
* Stromy přechodu zvýšení skóre

Každý model vytváření část kódu je rozdělený do kroků: 

1. **Model školení** dat pomocí jednu sadu parametrů
2. **Model hodnocení** na testovací datové sady s metrikami
3. **Probíhá ukládání modelu** v objektu blob pro budoucí využití

Vám ukážeme, jak provést křížové ověření (CV) s parametrem cílit na konkrétní dvěma způsoby:

1. Pomocí **obecný** nastaví vlastní kód, který je možné použít s libovolným algoritmem v MLlib a žádné parametry v algoritmu. 
2. Použití **pySpark CrossValidator kanálu funkce**. Všimněte si, že CrossValidator má několik omezení pro Spark 1.5.0: 
   
   * Modely kanálu nelze uložit/zachovat pro budoucí použití.
   * Nelze použít pro každý parametr v modelu.
   * Nelze použít pro každý MLlib algoritmus.

### <a name="generic-cross-validation-and-hyperparameter-sweeping-used-with-the-logistic-regression-algorithm-for-binary-classification"></a>Obecný křížové ověření a hyperparameter sweeping použitých v algoritmu logistické regrese pro binární klasifikaci
Kód v této části ukazuje, jak pro trénování, vyhodnocení a uložit model logistické regrese s [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) , který předpovídá, jestli je tip placené cesty v NYC taxislužby cesty a tarif datové sady. Model se trénuje pomocí křížové ověření (CV) a hyperparameter sweeping implementovaná vlastním kódem, který lze použít k některé z algoritmů učení v MLlib.   

> [!NOTE]
> Spuštění vlastního kódu CV může trvat několik minut.
> 
> 

**Trénování modelu logistické regrese pomocí CV a hyperparameter sweeping**

    # LOGISTIC REGRESSION CLASSIFICATION WITH CV AND HYPERPARAMETER SWEEPING

    # GET ACCURACY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD LIBRARIES
    from pyspark.mllib.classification import LogisticRegressionWithLBFGS 
    from pyspark.mllib.evaluation import BinaryClassificationMetrics

    # CREATE PARAMETER GRID FOR LOGISTIC REGRESSION PARAMETER SWEEP
    from sklearn.grid_search import ParameterGrid
    grid = [{'regParam': [0.01, 0.1], 'iterations': [5, 10], 'regType': ["l1", "l2"], 'tolerance': [1e-3, 1e-4]}]
    paramGrid = list(ParameterGrid(grid))
    numModels = len(paramGrid)

    # SET NUM FOLDS AND NUM PARAMETER SETS TO SWEEP ON
    nFolds = 3;
    h = 1.0 / nFolds;
    metricSum = np.zeros(numModels);

    # BEGIN CV WITH PARAMETER SWEEP
    for i in range(nFolds):
        # Create training and x-validation sets
        validateLB = i * h
        validateUB = (i + 1) * h
        condition = (trainData["rand"] >= validateLB) & (trainData["rand"] < validateUB)
        validation = trainData.filter(condition)
        # Create LabeledPoints from data-frames
        if i > 0:
            trainCVLabPt.unpersist()
            validationLabPt.unpersist()
        trainCV = trainData.filter(~condition)
        trainCVLabPt = trainCV.map(parseRowOneHotBinary)
        trainCVLabPt.cache()
        validationLabPt = validation.map(parseRowOneHotBinary)
        validationLabPt.cache()
        # For parameter sets compute metrics from x-validation
        for j in range(numModels):
            regt = paramGrid[j]['regType']
            regp = paramGrid[j]['regParam']
            iters = paramGrid[j]['iterations']
            tol = paramGrid[j]['tolerance']
            # Train logistic regression model with hypermarameter set
            model = LogisticRegressionWithLBFGS.train(trainCVLabPt, regType=regt, iterations=iters,  
                                                      regParam=regp, tolerance = tol, intercept=True)
            predictionAndLabels = validationLabPt.map(lambda lp: (float(model.predict(lp.features)), lp.label))
            # Use ROC-AUC as accuracy metrics
            validMetrics = BinaryClassificationMetrics(predictionAndLabels)
            metric = validMetrics.areaUnderROC
            metricSum[j] += metric

    avgAcc = metricSum / nFolds;
    bestParam = paramGrid[np.argmax(avgAcc)];

    # UNPERSIST OBJECTS
    trainCVLabPt.unpersist()
    validationLabPt.unpersist()

    # TRAIN ON FULL TRAIING SET USING BEST PARAMETERS FROM CV/PARAMETER SWEEP
    logitBest = LogisticRegressionWithLBFGS.train(oneHotTRAINbinary, regType=bestParam['regType'], 
                                                  iterations=bestParam['iterations'], 
                                                  regParam=bestParam['regParam'], tolerance = bestParam['tolerance'], 
                                                  intercept=True)


    # PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
    # NOTE: There are 20 coefficient terms for the 10 features, 
    #       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
    print("Coefficients: " + str(logitBest.weights))
    print("Intercept: " + str(logitBest.intercept))

    # PRINT ELAPSED TIME    
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**VÝSTUP**

Koeficienty: [0.0082065285375-0.0223675576104,-0.0183812028036, - 3.48124578069e - 05-0.00247646947233,-0.00165897881503, 0.0675394837328,-0.111823113101,-0.324609912762,-0.204549780032,-1.36499216354, 0.591088507921, - 0.664263411392-1.00439726852, 3.46567827545,-3.51025855172,-0.0471341112232,-0.043521833294, 0.000243375810385, 0.054518719222]

Zachycení:-0.0111216486893

Čas potřebný k provedení nad buňkou: 14.43 sekund

**Vyhodnocení binární klasifikační model pomocí standardních metrik**

Kód v této části ukazuje, jak vyhodnotit Logistický regresní model proti testovací data sadu, včetně diagramů křivky roc s více TŘÍDAMI.

    # RECORD START TIME
    timestart = datetime.datetime.now()

    #IMPORT LIBRARIES
    from sklearn.metrics import roc_curve,auc
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.mllib.evaluation import MulticlassMetrics

    # PREDICT ON TEST DATA WITH BEST/FINAL MODEL
    predictionAndLabels = oneHotTESTbinary.map(lambda lp: (float(logitBest.predict(lp.features)), lp.label))

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

    # OUTPUT PROBABILITIES AND REGISTER TEMP TABLE
    logitBest.clearThreshold(); # This clears threshold for classification (0.5) and outputs probabilities
    predictionAndLabelsDF = predictionAndLabels.toDF()
    predictionAndLabelsDF.registerTempTable("tmp_results");

    # PRINT ELAPSED TIME    
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**VÝSTUP**

Oblasti v rámci žádosti o přijetí změn = 0.985336538462

Oblast pod roc s více TŘÍDAMI = 0.983383274312

Souhrnné statistiky

Přesnost = 0.984174341679

Odvolat = 0.984174341679

F1 Skóre = 0.984174341679

Čas potřebný k provedení nad buňkou: 2.67 sekund

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

    #PREDICTIONS
    predictions_pddf = sqlResults.rename(columns={'_1': 'probability', '_2': 'label'})
    prob = predictions_pddf["probability"] 
    fpr, tpr, thresholds = roc_curve(predictions_pddf['label'], prob, pos_label=1);
    roc_auc = auc(fpr, tpr)

    # PLOT ROC CURVES
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


**VÝSTUP**

![Křivka roc s více TŘÍDAMI logistické regrese pro obecný přístup](./media/spark-advanced-data-exploration-modeling/logistic-regression-roc-curve.png)

**Zachovat modelu do objektu BLOB pro budoucí využití**

Kód v této části ukazuje, jak uložit model logistické regrese za využití.

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.classification import LogisticRegressionModel

    # PERSIST MODEL
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp;
    dirfilename = modelDir + logisticregressionfilename;

    logitBest.save(sc, dirfilename);

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";


**VÝSTUP**

Čas potřebný k provedení nad buňkou: 34.57 sekund

### <a name="use-mllibs-crossvalidator-pipeline-function-with-logistic-regression-elastic-regression-model"></a>Pomocí funkce kanálu CrossValidator MLlib na model logistické regrese (elastické regrese)
Kód v této části ukazuje, jak pro trénování, vyhodnocení a uložit model logistické regrese s [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) , který předpovídá, jestli je tip placené cesty v NYC taxislužby cesty a tarif datové sady. Model se trénuje pomocí křížové ověření (CV) a hyperparameter sweeping implementuje pomocí funkce MLlib CrossValidator kanálu pro CV s Uklízení parametrů.   

> [!NOTE]
> Spuštění tohoto kódu MLlib CV může trvat několik minut.
> 
> 

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml import Pipeline
    from pyspark.ml.evaluation import BinaryClassificationEvaluator
    from pyspark.ml.tuning import CrossValidator, ParamGridBuilder
    from sklearn.metrics import roc_curve,auc

    # DEFINE ALGORITHM / MODEL
    lr = LogisticRegression()

    # DEFINE GRID PARAMETERS
    paramGrid = ParamGridBuilder().addGrid(lr.regParam, (0.01, 0.1))\
                                  .addGrid(lr.maxIter, (5, 10))\
                                  .addGrid(lr.tol, (1e-4, 1e-5))\
                                  .addGrid(lr.elasticNetParam, (0.25,0.75))\
                                  .build()

    # DEFINE CV WITH PARAMETER SWEEP
    cv = CrossValidator(estimator= lr,
                        estimatorParamMaps=paramGrid,
                        evaluator=BinaryClassificationEvaluator(),
                        numFolds=3)

    # CONVERT TO DATA-FRAME: THIS DOES NOT RUN ON RDDs
    trainDataFrame = sqlContext.createDataFrame(oneHotTRAINbinary, ["features", "label"])

    # TRAIN WITH CROSS-VALIDATION
    cv_model = cv.fit(trainDataFrame)


    ## PREDICT AND EVALUATE ON TEST DATA-SET

    # USE TEST DATASET FOR PREDICTION
    testDataFrame = sqlContext.createDataFrame(oneHotTESTbinary, ["features", "label"])
    test_predictions = cv_model.transform(testDataFrame)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**VÝSTUP**

Čas potřebný k provedení nad buňkou: 107.98 sekund

**Vykreslení křivka roc s více TŘÍDAMI.**

*PredictionAndLabelsDF* je zaregistrovaný jako tabulky, *tmp_results*, v předcházejí buňky. *tmp_results* slouží k provádění dotazů a výstup do data snímků sqlResults pro vykreslení. Zde je kód.

    # QUERY RESULTS
    %%sql -q -o sqlResults
    SELECT label, prediction, probability from tmp_results

Tady je kód pro vykreslení křivka roc s více TŘÍDAMI.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES 
    %%local
    from sklearn.metrics import roc_curve,auc

    # ROC CURVE
    prob = [x["values"][1] for x in sqlResults["probability"]]
    fpr, tpr, thresholds = roc_curve(sqlResults['label'], prob, pos_label=1);
    roc_auc = auc(fpr, tpr)

    #PLOT
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


**VÝSTUP**

![Křivka ROC logistické regrese pomocí CrossValidator MLlib.](./media/spark-advanced-data-exploration-modeling/mllib-crossvalidator-roc-curve.png)

### <a name="random-forest-classification"></a>Klasifikace náhodné doménové struktury
Kód v této části ukazuje, jak pro trénování, vyhodnocení a uložení náhodných doménové struktury regrese, který předpovídá, jestli je tip placené cesty v NYC taxislužby cesty a tarif datovou sadu.

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
    ## UN-COMMENT IF YOU WANT TO PRING TREES
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


**VÝSTUP**

Oblast pod roc s více TŘÍDAMI = 0.985336538462

Čas potřebný k provedení nad buňkou: 26.72 sekund

### <a name="gradient-boosting-trees-classification"></a>Přechodu zvýšení skóre klasifikace stromů
Kód v této části ukazuje, jak trénovat, vyhodnotit a uložit přechodu zvýšení skóre stromů modelu pro předpověď, jestli je tip placené výlet do cesty taxíkem NYC a jízdenky datové sady.

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel

    # SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

    gbtModel = GradientBoostedTrees.trainClassifier(indexedTRAINbinary, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                    numIterations=10)
    ## UNCOMMENT IF YOU WANT TO PRINT TREE DETAILS
    #print('Learned classification GBT model:')
    #print(bgtModel.toDebugString())

    # PREDICT ON TEST DATA AND EVALUATE
    predictions = gbtModel.predict(indexedTESTbinary.map(lambda x: x.features))
    predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)

    # Area under ROC curve
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

**VÝSTUP**

Oblast pod roc s více TŘÍDAMI = 0.985336538462

Čas potřebný k provedení nad buňkou: 28.13 sekund

## <a name="predict-tip-amount-with-regression-models-not-using-cv"></a>Predikce velikost špičky v regresních modelů (bez použití CV)
Tato část ukazuje, jak použít tři modely pro úlohu regrese: předpověď tip částky zaplacené cesty taxíkem založených na jiných funkcích tip. Modely uvedené jsou:

* Vyřešeno lineární regrese
* Náhodné doménové struktury
* Stromy přechodu zvýšení skóre

Tyto modely jsou popsány v úvodu. Každý model vytváření část kódu je rozdělený do kroků: 

1. **Model školení** dat pomocí jednu sadu parametrů
2. **Model hodnocení** na testovací datové sady s metrikami
3. **Probíhá ukládání modelu** v objektu blob pro budoucí využití   

> AZURE Poznámka: Cross-validation se nepoužívá tři regresních modelů v této části, protože to se zobrazilo podrobněji a logistické regresní modely. Příklad ukazuje, jak pomocí CV elastické Net pro lineární regrese je k dispozici v dodatku v tomto tématu.
> 
> AZURE Poznámka: Podle našich zkušeností může existovat problémy s konvergence LinearRegressionWithSGD modelů a parametry musí být změněno či optimalizovanou pečlivě pro získání platný model. Škálování proměnných výrazně pomáhá s konvergence. Elastické net regrese, uvedené v dodatku k tomuto tématu, je také použít místo LinearRegressionWithSGD.
> 
> 

### <a name="linear-regression-with-sgd"></a>Lineární regrese s SGD
Kód v této části ukazuje, jak používat škálován funkce k trénování lineární regrese, který se používá pro optimalizaci pomocí stochastického sestupu (SGD) a jak skóre, vyhodnocení a uložit model v Azure Blob Storage (WASB).

> [!TIP]
> Podle našich zkušeností může být problémy s konvergence LinearRegressionWithSGD modely a parametry musí být změněno či optimalizovanou pečlivě pro získání platný model. Škálování proměnných výrazně pomáhá s konvergence.
> 
> 

    # LINEAR REGRESSION WITH SGD 

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

    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)

    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
    dirfilename = modelDir + linearregressionfilename;

    linearModel.save(sc, dirfilename)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**VÝSTUP**

Koeficienty: [0.0141707753435-0.0252930927087,-0.0231442517137, 0.247070902996, 0.312544147152, 0.360296120645, 0.0122079566092,-0.00456498588241,-0.0898228505177, 0.0714046248793, 0.102171263868, 0.100022455632,-0.00289545676449,- 0.00791124681938 0.54396316518,-0.536293513569, 0.0119076553369,-0.0173039244582, 0.0119632796147, 0.00146764882502]

Zachytit: 0.854507624459

RMSE = 1.23485131376

R sqr = 0.597963951127

Čas potřebný k provedení nad buňkou: 38.62 sekund

### <a name="random-forest-regression"></a>Regrese Random doménové struktury
Kód v této části ukazuje, jak pro trénování, vyhodnocení a uložení náhodných doménovou strukturu modelu pro předpověď částka tip pro data o jízdách NYC taxislužby.   

> [!NOTE]
> Křížové ověření s parametrem cílit na konkrétní psát vlastní kód je k dispozici v dodatku.
> 
> 

    #PREDICT TIP AMOUNTS USING RANDOM FOREST

    # RECORD START TIME
    timestart= datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import RegressionMetrics


    # TRAIN MODEL
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                        numTrees=25, featureSubsetStrategy="auto",
                                        impurity='variance', maxDepth=10, maxBins=32)
    # UN-COMMENT IF YOU WANT TO PRING TREES
    #print('Learned classification forest model:')
    #print(rfModel.toDebugString())

    # PREDICT AND EVALUATE ON TEST DATA-SET
    predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = oneHotTESTreg.map(lambda lp: lp.label).zip(predictions)

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

**VÝSTUP**

RMSE = 0.931981967875

R sqr = 0.733445485802

Čas potřebný k provedení nad buňkou: 25.98 sekund

### <a name="gradient-boosting-trees-regression"></a>Přechodu zvýšení skóre regresní stromy
Kód v této části ukazuje, jak pro trénování, vyhodnocení a uložení přechodu zvýšení skóre stromů modelu, který bude předpovídat částka tip pro data o jízdách NYC taxislužby.

**Natrénování a vyhodnocení**

    #PREDICT TIP AMOUNTS USING GRADIENT BOOSTING TREES

    # RECORD START TIME
    timestart= datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
    from pyspark.mllib.util import MLUtils

    # TRAIN MODEL
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    gbtModel = GradientBoostedTrees.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo, 
                                                    numIterations=10, maxBins=32, maxDepth = 4, learningRate=0.1)

    # EVALUATE A TEST DATA-SET
    predictions = gbtModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES
    test_predictions= sqlContext.createDataFrame(predictionAndLabels)
    test_predictions_pddf = test_predictions.toPandas()

    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btregressionfilename = "GradientBoostingTreeRegression_" + datestamp;
    dirfilename = modelDir + btregressionfilename;
    gbtModel.save(sc, dirfilename)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**VÝSTUP**

RMSE = 0.928172197114

R sqr = 0.732680354389

Čas potřebný k provedení nad buňkou: 20.9 sekund

**Vykreslení**

*tmp_results* je zaregistrovaný jako tabulku Hive v předcházejí buňky. Výsledky z tabulky jsou výstup do *sqlResults* datového rámce pro vykreslení. Zde je kód

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT * from tmp_results


Tady je kód pro vykreslení dat pomocí Jupyter serveru.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    import numpy as np

    # PLOT
    ax = sqlResults.plot(kind='scatter', figsize = (6,6), x='_1', y='_2', color='blue', alpha = 0.25, label='Actual vs. predicted');
    fit = np.polyfit(sqlResults['_1'], sqlResults['_2'], deg=1)
    ax.set_title('Actual vs. Predicted Tip Amounts ($)')
    ax.set_xlabel("Actual")
    ax.set_ylabel("Predicted")
    ax.plot(sqlResults['_1'], fit[0] * sqlResults['_1'] + fit[1], color='magenta')
    plt.axis([-1, 15, -1, 15])
    plt.show(ax)

![Skutečná vs předpovědět – tip objemy](./media/spark-advanced-data-exploration-modeling/actual-vs-predicted-tips.png)

## <a name="appendix-additional-regression-tasks-using-cross-validation-with-parameter-sweeps"></a>Dodatek: Další regresní úlohy pomocí parametrických křížového ověření
Tento dodatek obsahuje kód, který ukazuje, jak provést CV pomocí Elastických net pro lineární regrese a o tom, jak CV Uklízení parametrů pomocí vlastního kódu pro regresní náhodné doménové struktury.

### <a name="cross-validation-using-elastic-net-for-linear-regression"></a>Křížové ověření pomocí Elastic net pro lineární regrese
Kód v této části ukazuje, jak křížové ověření pomocí Elastických net pro lineární regrese a vyhodnocení modelu proti testovací data.

    ###  CV USING ELASTIC NET FOR LINEAR REGRESSION

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.ml.regression import LinearRegression
    from pyspark.ml import Pipeline
    from pyspark.ml.evaluation import RegressionEvaluator
    from pyspark.ml.tuning import CrossValidator, ParamGridBuilder

    # DEFINE ALGORITHM/MODEL
    lr = LinearRegression()

    # DEFINE GRID PARAMETERS
    paramGrid = ParamGridBuilder().addGrid(lr.regParam, (0.01, 0.1))\
                                  .addGrid(lr.maxIter, (5, 10))\
                                  .addGrid(lr.tol, (1e-4, 1e-5))\
                                  .addGrid(lr.elasticNetParam, (0.25,0.75))\
                                  .build() 

    # DEFINE PIPELINE 
    # SIMPLY THE MODEL HERE, WITHOUT TRANSFORMATIONS
    pipeline = Pipeline(stages=[lr])

    # DEFINE CV WITH PARAMETER SWEEP
    cv = CrossValidator(estimator= lr,
                        estimatorParamMaps=paramGrid,
                        evaluator=RegressionEvaluator(),
                        numFolds=3)

    # CONVERT TO DATA FRAME, AS CROSSVALIDATOR WON'T RUN ON RDDS
    trainDataFrame = sqlContext.createDataFrame(oneHotTRAINreg, ["features", "label"])

    # TRAIN WITH CROSS-VALIDATION
    cv_model = cv.fit(trainDataFrame)


    # EVALUATE MODEL ON TEST SET
    testDataFrame = sqlContext.createDataFrame(oneHotTESTreg, ["features", "label"])

    # MAKE PREDICTIONS ON TEST DOCUMENTS
    # cvModel uses the best model found (lrModel).
    predictionAndLabels = cv_model.transform(testDataFrame)

    # CONVERT TO DF AND SAVE REGISER DF AS TABLE
    predictionAndLabels.registerTempTable("tmp_results");

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**VÝSTUP**

Čas potřebný k provedení nad buňkou: 161.21 sekund

**Vyhodnotit s R-SQR metrika**

*tmp_results* je zaregistrovaný jako tabulku Hive v předcházejí buňky. Výsledky z tabulky jsou výstup do *sqlResults* datového rámce pro vykreslení. Zde je kód

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT label,prediction from tmp_results


Tady je kód pro výpočet R sqr.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    from scipy import stats

    #R-SQR TEST METRIC
    corstats = stats.linregress(sqlResults['label'],sqlResults['prediction'])
    r2 = (corstats[2]*corstats[2])
    print("R-sqr = %s" % r2)


**VÝSTUP**

R sqr = 0.619184907088

### <a name="cross-validation-with-parameter-sweep-using-custom-code-for-random-forest-regression"></a>Křížové ověření s Uklízení parametrů pomocí vlastního kódu pro regresní náhodné doménové struktury
Kód v této části ukazuje, jak křížové ověření s Uklízení parametrů pomocí vlastního kódu pro regresní náhodné doménové struktury a vyhodnocení modelu proti testovací data.

    # RECORD START TIME
    timestart= datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    # GET ACCURARY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import RegressionMetrics
    from sklearn.grid_search import ParameterGrid

    ## CREATE PARAMETER GRID
    grid = [{'maxDepth': [5,10], 'numTrees': [25,50]}]
    paramGrid = list(ParameterGrid(grid))

    ## SPECIFY LEVELS OF CATEGORICAL VARIBLES
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

    # SPECIFY NUMFOLDS AND ARRAY TO HOLD METRICS
    nFolds = 3;
    numModels = len(paramGrid)
    h = 1.0 / nFolds;
    metricSum = np.zeros(numModels);

    for i in range(nFolds):
        # Create training and x-validation sets
        validateLB = i * h
        validateUB = (i + 1) * h
        condition = (trainData["rand"] >= validateLB) & (trainData["rand"] < validateUB)
        validation = trainData.filter(condition)
        # Create labeled points from data-frames
        if i > 0:
            trainCVLabPt.unpersist()
            validationLabPt.unpersist()
        trainCV = trainData.filter(~condition)
        trainCVLabPt = trainCV.map(parseRowIndexingRegression)
        trainCVLabPt.cache()
        validationLabPt = validation.map(parseRowIndexingRegression)
        validationLabPt.cache()
        # For parameter sets compute metrics from x-validation
        for j in range(numModels):
            maxD = paramGrid[j]['maxDepth']
            numT = paramGrid[j]['numTrees']
            # Train logistic regression model with hypermarameter set
            rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                        numTrees=numT, featureSubsetStrategy="auto",
                                        impurity='variance', maxDepth=maxD, maxBins=32)
            predictions = rfModel.predict(validationLabPt.map(lambda x: x.features))
            predictionAndLabels = validationLabPt.map(lambda lp: lp.label).zip(predictions)
            # Use ROC-AUC as accuracy metrics
            validMetrics = RegressionMetrics(predictionAndLabels)
            metric = validMetrics.rootMeanSquaredError
            metricSum[j] += metric

    avgAcc = metricSum/nFolds;
    bestParam = paramGrid[np.argmin(avgAcc)];

    # UNPERSIST OBJECTS
    trainCVLabPt.unpersist()
    validationLabPt.unpersist()

    ## TRAIN FINAL MODL WIHT BEST PARAMETERS
    rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                        numTrees=bestParam['numTrees'], featureSubsetStrategy="auto",
                                        impurity='variance', maxDepth=bestParam['maxDepth'], maxBins=32)

    # EVALUATE MODEL ON TEST DATA
    predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

    #PRINT TEST METRICS
    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**VÝSTUP**

RMSE = 0.906972198262

R sqr = 0.740751197012

Čas potřebný k provedení nad buňkou: 69.17 sekund

### <a name="clean-up-objects-from-memory-and-print-model-locations"></a>Vyčištění objektů z paměti a tisku modelu umístění
Použití `unpersist()` odstranit objekty uložené v mezipaměti v paměti.

    # UNPERSIST OBJECTS CACHED IN MEMORY

    # REMOVE ORIGINAL DFs
    taxi_df_train_cleaned.unpersist()
    taxi_df_train_with_newFeatures.unpersist()
    trainData.unpersist()
    trainData.unpersist()

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


**VÝSTUP**

PythonRDD [122] na RDD na PythonRDD.scala: 43

** Výtisk cestu k souborům model pro použití v poznámkovém bloku spotřeby. ** K využívání a stanovíte jeho skóre nezávislé datové sady, budete muset zkopírovat a vložit tyto názvy souborů v "Poznámkový blok spotřeby".

    # PRINT MODEL FILE LOCATIONS FOR CONSUMPTION
    print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
    print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
    print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
    print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
    print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
    print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";


**VÝSTUP**

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05-0316_47_30.096528"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-05-0316_51_28.433670"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05-0316_50_17.454440"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05-0316_51_57.331730"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05-0316_50_40.138809"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05-0316_52_18.827237"

## <a name="whats-next"></a>Co dále?
Teď, když jste vytvořili modely regrese a klasifikaci s knihovna Spark MlLib, jste připraveni se naučíte stanovení skóre a vyhodnocovat u nich tyto modely.

**Model spotřeby:** informace o určení skóre a vyhodnocovat u nich klasifikačních a regresních modelů vytvořených v tomto tématu najdete v tématu [skóre a vyhodnocení modelů strojového učení předdefinovaných Spark](spark-model-consumption.md).

