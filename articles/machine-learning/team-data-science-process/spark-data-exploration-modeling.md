---
title: Zkoumání a modelování dat pomocí Spark – Team Data Science Process
description: Představuje možnosti pro zkoumání a modelování dat sady nástrojů Spark MLlib v Azure.
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
ms.openlocfilehash: 208f176ca942fb382ff2ed81d872602f7229b0a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76718629"
---
# <a name="data-exploration-and-modeling-with-spark"></a>Zkoumání a modelování dat pomocí Spark

Tento návod používá HDInsight Spark k tomu, aby průzkum dat a binární klasifikace a regresní modelování úkoly na vzorku cesty taxi NYC a fare 2013 datové sady.  Provede vás kroky [procesu datové vědy](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/), end-to-end, pomocí HDInsight Spark clusteru pro zpracování a Azure objekty BLOB pro ukládání dat a modelů. Proces zkoumá a vizualizuje data přenesená z objektu blob úložiště Azure a pak připraví data k vytváření prediktivních modelů. Tyto modely jsou vytvářeny pomocí sady nástrojů Spark MLlib k provedení úloh binární klasifikace a regresního modelování.

* Úkolem **binární klasifikace** je předpovědět, zda je pro cestu zaplacen tip. 
* **Regresní** úkol je předpovědět množství tip na základě jiných funkcí tip. 

Modely, které používáme, zahrnují logistickou a lineární regresi, náhodné lesy a stromy zesílené přechodem:

* [Lineární regrese s SGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) je lineární regresní model, který používá metodu Stochastic Gradient Descent (SGD) a pro optimalizaci a změnu funkce předpovědět částky tipu. 
* [Logistická regrese s LBFGS](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) nebo "logit" regrese, je regresní model, který lze použít, když je závislá proměnná kategorická pro klasifikaci dat. LBFGS je kvazi-Newton ův optimalizační algoritmus, který se blíží algoritmu Broyden-Fletcher-Goldfarb-Shanno (BFGS) pomocí omezeného množství počítačové paměti a který je široce používán ve strojovém učení.
* [Náhodné lesy](https://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) jsou soubory rozhodovacích stromů.  Kombinují mnoho rozhodovacích stromů, aby se snížilo riziko nadměrného vybavení. Náhodné doménové struktury se používají pro regresi a klasifikaci a mohou zpracovávat kategorické prvky a lze je rozšířit na nastavení klasifikace více tříd. Nevyžadují změnu měřítka prvku a jsou schopny zachytit nelinearity a interakce prvků. Náhodné doménové struktury jsou jedním z nejúspěšnějších modelů strojového učení pro klasifikaci a regresi.
* [Gradient posílil stromy](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTS) jsou soubory rozhodnutí stromů. GBTS rozhodnutí vlaku stromy iterativně minimalizovat ztrátu funkce. GBTS se používá pro regresi a klasifikaci a může zpracovávat kategorické funkce, nevyžadují změnu měřítka prvků a jsou schopny zachytit nelinearity a interakce funkcí. Lze je také použít v nastavení klasifikace více tříd.

Kroky modelování také obsahují kód, který ukazuje, jak trénovat, vyhodnocovat a ukládat každý typ modelu. Python byl použit ke kódování řešení a zobrazení příslušných pozemků.   

> [!NOTE]
> Přestože sada nástrojů Spark MLlib je navržena pro práci s velkými datovými sadami, pro usnadnění se zde používá relativně malá ukázka (~30 Mb pomocí řádků 170 K, přibližně 0,1 % původní datové sady NYC). Cvičení zde uvedené běží efektivně (asi za 10 minut) v clusteru HDInsight se 2 pracovními uzly. Stejný kód s menšími úpravami lze použít ke zpracování větších datových sad s příslušnými úpravami pro ukládání dat do mezipaměti v paměti a změnu velikosti clusteru.
> 
> 

## <a name="prerequisites"></a>Požadavky
K dokončení tohoto návodu potřebujete účet Azure a cluster HDInsight Spark 1.6 (nebo Spark 2.0). Pokyny, jak tyto požadavky splnit, najdete v [článku Přehled datových věd pomocí Sparku v Azure HDInsight.](spark-overview.md) Toto téma také obsahuje popis nyc 2013 Taxi data použitá zde a pokyny, jak spustit kód z notebooku Jupyter na clusteru Spark. 

## <a name="spark-clusters-and-notebooks"></a>Skvitové clustery a poznámkové bloky
Kroky nastavení a kód jsou k dispozici v tomto návodu pro použití HDInsight Spark 1.6. Ale notebooky Jupyter jsou k dispozici pro clustery HDInsight Spark 1.6 a Spark 2.0. Popis poznámkových bloků a odkazy na ně jsou k dispozici v [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) pro úložiště GitHub, které je obsahuje. Kromě toho je kód zde a v propojených poznámkových blocích obecný a měl by fungovat na libovolném clusteru Spark. Pokud nepoužíváte HDInsight Spark, kroky nastavení a správy clusteru se mohou mírně lišit od toho, co je zobrazeno zde. Pro větší pohodlí, zde jsou odkazy na notebooky Jupyter pro Spark 1.6 (které mají být spuštěny v jádru pySpark serveru Jupyter Notebook) a Spark 2.0 (ke spuštění v jádru pySpark3 jupyternotebooku):

### <a name="spark-16-notebooks"></a>Spark 1,6 notebooky

[pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb): Poskytuje informace o tom, jak provádět zkoumání dat, modelování a vyhodnocování pomocí několika různých algoritmů.

### <a name="spark-20-notebooks"></a>Spark 2.0 notebooky
Regresní a klasifikační úlohy, které jsou implementovány pomocí clusteru Spark 2.0, jsou v samostatných poznámkových blocích a poznámkový blok klasifikace používá jinou sadu dat:

- [Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Tento soubor poskytuje informace o tom, jak provádět zkoumání dat, modelování a bodování v clusterech Spark 2.0 pomocí nyc taxi výlet a tarif data-set je popsáno [zde](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data). Tento poznámkový blok může být dobrým výchozím bodem pro rychlé prozkoumání kódu, který jsme poskytli pro Spark 2.0. Podrobnější poznámkový blok analyzuje data nyc taxi, najdete v dalším poznámkový blok v tomto seznamu. Podívejte se na poznámky následující v tomto seznamu, který porovnává tyto poznámkové bloky. 
- [Spark2.0-pySpark3_NYC_Taxi_Tip_Regression.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_NYC_Taxi_Tip_Regression.ipynb): Tento soubor ukazuje, jak provádět data hádky (Spark SQL a dataframe operace), průzkum, modelování a bodování pomocí NYC Taxi výlet a jízdné-soubor údajů je popsáno [zde](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data).
- [Spark2.0-pySpark3_Airline_Departure_Delay_Classification.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_Airline_Departure_Delay_Classification.ipynb): Tento soubor ukazuje, jak provádět datové hádky (Spark SQL a dataframe operace), průzkum, modelování a bodování pomocí známé letecké společnosti On-time odletu datové sady z 2011 a 2012. Před modelováním jsme integrovali datovou sadu leteckých společností s údaji o počasí na letišti (například rychlost větru, teploty, nadmořskou výškou atd.), takže tyto funkce počasí mohou být zahrnuty do modelu.

<!-- -->

> [!NOTE]
> Datová sada leteckých společností byla přidána do notebooků Spark 2.0, aby lépe ilustrovala použití klasifikačních algoritmů. Informace o datové sadě v yoletu letecké společnosti a datové matné datové sadě počasí naleznete na následujících odkazech:
> 
> - Údaje o letecké společnosti o odletu:[https://www.transtats.bts.gov/ONTIME/](https://www.transtats.bts.gov/ONTIME/)
> 
> - Údaje o počasí na letišti:[https://www.ncdc.noaa.gov/](https://www.ncdc.noaa.gov/) 

<!-- -->

<!-- -->

> [!NOTE]
> Spuštění notebooků Spark 2.0 na datových sadách zpoždění taxi a leteckých letů může trvat 10 minut nebo déle (v závislosti na velikosti clusteru HDI). První poznámkový blok ve výše uvedeném seznamu ukazuje mnoho aspektů zkoumání dat, vizualizace a tréninku modelu ML v notebooku, který trvá méně času, než se spustí s datovou sadou NYC s ukázkami dolů, ve kterém taxi a jízdné soubory byly pre-připojil: [Spark2.0-pySpark3-machine-learning-data-jiskra-advanced-data-průzkum-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb) Tento notebook trvá mnohem kratší dobu až do konce (2-3 minuty) a může být dobrým výchozím bodem pro rychlé zkoumání kódu jsme poskytli pro Spark 2.0. 

<!-- -->

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

<!-- -->

> [!NOTE]
> Níže uvedené popisy se vztahují k použití Spark 1.6. Pro verze Spark 2.0 použijte výše popsané a propojené poznámkové bloky. 

<!-- -->

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Nastavení: umístění úložišť, knihovny a přednastavený kontext Sparku
Spark je schopný číst a zapisovat do objektu Blob úložiště Azure (označovaný také jako WASB). Takže jakékoli z vašich stávajících dat, které jsou tam uloženy, mohou být zpracovány pomocí Spark a výsledky znovu uloženy v WASB.

Chcete-li uložit modely nebo soubory v WASB, cesta musí být zadánsprávně. Na výchozí kontejner připojený ke clusteru Spark lze odkazovat pomocí cesty začínající na: "wasb:///". Ostatní lokality jsou odkazovány "wasb://".

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Nastavení cest adresářů pro umístění úložišť v wasb
Následující ukázka kódu určuje umístění dat, která mají být přečtena, a cestu pro adresář úložiště modelu, do kterého je uložen výstup modelu:

    # SET PATHS TO FILE LOCATIONS: DATA AND MODEL STORAGE

    # LOCATION OF TRAINING DATA
    taxi_train_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv";

    # SET THE MODEL STORAGE DIRECTORY PATH 
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS NEEDED.
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 


### <a name="import-libraries"></a>Import knihoven
Nastavení také vyžaduje import potřebných knihoven. Nastavte kontext jiskry a importujte potřebné knihovny s následujícím kódem:

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


### <a name="preset-spark-context-and-pyspark-magics"></a>Přednastavený kontext Spark a kouzla PySpark
Jádra PySpark, která jsou dodávána s poznámkovými bloky Jupyter, mají přednastavený kontext. Takže není nutné explicitně nastavit kontexty Spark nebo Hive před zahájením práce s aplikací, kterou vyvíjíte. Tyto kontexty jsou k dispozici ve výchozím nastavení. Tyto kontexty jsou:

* sc - pro Jiskru 
* sqlContext - pro Hive

Jádro PySpark poskytuje některé předdefinované "magie", což jsou speciální příkazy, které můžete volat pomocí %%. Existují dva takové příkazy, které se používají v těchto ukázkách kódu.

* **%%místní** Určuje, že kód v následujících řádcích má být proveden místně. Kód musí být platný kód Pythonu.
* **%%sql -o \<název>proměnných** Spustí dotaz Hive proti sqlContext. Pokud je parametr -o předán, výsledek dotazu je zachován v kontextu %%místní python jako Pandas DataFrame.

Další informace o jádrech notebooků Jupyter a předdefinovaných "kouzlech" naleznete [v tématu Jádra dostupná pro notebooky Jupyter s clustery HDInsight Spark Linux na HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

## <a name="data-ingestion-from-public-blob"></a>Přibíjení dat z veřejného objektu blob
Prvním krokem v procesu datové vědy je ingestování dat, která mají být analyzována ze zdrojů, kde je umístěn a vaše prostředí pro zkoumání dat a modelování. Prostředí je Spark v tomto návodu. Tato část obsahuje kód k dokončení řady úkolů:

* požití vzorku dat, který má být modelován
* čtení ve vstupní datové sadě (uložené jako soubor .tsv)
* formátování a čištění dat
* vytváření a ukládání objektů do mezipaměti (RDD nebo datových rámců) v paměti
* zaregistrujte ji jako temp-table v kontextu SQL.

Zde je kód pro požití dat.

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

**Výstup:**

Doba provedená k provedení nad buňkou: 51.72 sekund

## <a name="data-exploration--visualization"></a>Vizualizace & zkoumání dat
Jakmile jsou data přenesena do Sparku, dalším krokem v procesu datové vědy je získat hlubší pochopení dat prostřednictvím průzkumu a vizualizace. V této části zkontrolujeme data taxi pomocí SQL dotazů a vykreslíme cílové proměnné a perspektivní funkce pro vizuální kontrolu. Konkrétně vykreslujeme frekvenci počtu cestujících při jízdách taxíkem, frekvenci výše spropitného a způsob, jakým se tipy liší podle výše a typu platby.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Vykreslete histogram frekvencí počtu cestujících ve vzorku cest taxíkem
Tento kód a následné úryvky pomocí SQL magie k dotazování vzorku a místní magie k vykreslení dat.

* **SQL magie`%%sql`( )** Jádro HDInsight PySpark podporuje snadné vložkové dotazy HiveQL proti sqlContext. Argument (-o VARIABLE_NAME) zachová výstup dotazu SQL jako Pandas DataFrame na serveru Jupyter. Toto nastavení zpřístupní výstup v místním režimu.
* ** `%%local` Kouzlo** se používá ke spuštění kódu místně na serveru Jupyter, což je headnode clusteru HDInsight. Obvykle používáte `%%local` magii ve `%%sql` spojení s magics s parametrem -o. Parametr -o by místně zachovával výstup dotazu SQL a pak %%local magic by spustil další sadu fragmentu kódu, který by se spustil místně proti výstupu dotazů SQL, které jsou místně trvalé

Výstup je automaticky vizualizován po spuštění kódu.

Tento dotaz načte cesty podle počtu cestujících. 

    # PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

    # HIVEQL QUERY AGAINST THE sqlContext
    %%sql -q -o sqlResults
    SELECT passenger_count, COUNT(*) as trip_counts 
    FROM taxi_train 
    WHERE passenger_count > 0 and passenger_count < 7 
    GROUP BY passenger_count 

Tento kód vytvoří místní datový rámec z výstupu dotazu a vykreslí data. Kouzlo `%%local` vytvoří místní datový rámec `sqlResults`, který lze použít pro vykreslování s matplotlib. 

> [!NOTE]
> Tato magie PySpark se používá vícekrát v tomto návodu. Pokud je velké množství dat, měli byste vzorek vytvořit datový rámec, který se vejde do místní paměti.
> 
> 

    #CREATE LOCAL DATA-FRAME AND USE FOR MATPLOTLIB PLOTTING

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
    # CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
    sqlResults

Zde je kód pro vykreslení cesty podle počtu cestujících

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

**Výstup:**

![Frekvence jízdy podle počtu cestujících](./media/spark-data-exploration-modeling/trip-freqency-by-passenger-count.png)

Pomocí tlačítek nabídky **Typ** v poznámkovém bloku můžete vybrat několik různých typů vizualizací (tabulka, výsečový, spojník, plocha nebo pruh). Zde je zobrazena zápletka baru.

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Vykreslete histogram částek spropitného a způsob, jakým se částka spropitného liší podle počtu cestujících a částek jízdného.
K ukázkovému množství dat použijte dotaz SQL.

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


Tato buňka kódu používá dotaz SQL k vytvoření tří vykreslení dat.

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


**Výstup:** 

![Rozložení částky tipu](./media/spark-data-exploration-modeling/tip-amount-distribution.png)

![Výše tipu podle počtu cestujících](./media/spark-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Částka tipu podle částky jízdného](./media/spark-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>Inženýring, transformace a příprava dat pro modelování
Tato část popisuje a poskytuje kód pro postupy používané k přípravě dat pro použití v modelování ML. Ukazuje, jak provést následující úkoly:

* Vytvoření nové funkce binning hodin do dopravní doby kbelíky
* Indexování a kódování kategorických funkcí
* Vytvoření objektů bodu s popiskem pro vstup do funkcí ML
* Vytvoření náhodného dílčího vzorkování dat a jejich rozdělení do trénovacích a testovacích sad
* měření rozsahu prvků,
* Ukládání objektů do mezipaměti v paměti

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Vytvoření nové funkce binning hodin do dopravní doby kbelíky
Tento kód ukazuje, jak vytvořit novou funkci binning hodin do bloků času provozu a potom, jak do mezipaměti výsledný datový rámec v paměti. Tam, kde se opakovaně používají odolné distribuované datové sady (RDD) a datové rámce, vede ukládání do mezipaměti ke zlepšení doby provádění. V souladu s tím ukládáme rdd a datové rámce v několika fázích v návodu. 

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

**Výstup:** 

126050

### <a name="index-and-encode-categorical-features-for-input-into-modeling-functions"></a>Indexování a kódování kategorických funkcí pro vstup do funkcí modelování
Tato část ukazuje, jak indexovat nebo kódovat kategorické funkce pro vstup do funkcí modelování. Funkce modelování a předvídání mllib vyžadují funkce s kategorickými vstupními daty, které mají být před použitím indexovány nebo kódovány. V závislosti na modelu je třeba je indexovat nebo kódovat různými způsoby:  

* **Modelování založené na stromech** vyžaduje, aby kategorie byly kódovány jako číselné hodnoty (například funkce se třemi kategoriemi může být kódována 0, 1, 2). Tento algoritmus je poskytován mllib je [StringIndexer](https://spark.apache.org/docs/latest/ml-features.html#stringindexer) funkce. Tato funkce kóduje řetězec sloupec popisků do sloupce indexů popisků, které jsou seřazeny podle četnosti popisků. Přestože jsou indexovány číselnými hodnotami pro zpracování vstupů a dat, lze zadat algoritmy založené na stromu, aby s nimi bylo vhodné zacházeno jako s kategoriemi. 
* **Logistické a lineární regrese modely** vyžadují jedno-horké kódování, kde například funkce se třemi kategoriemi lze rozšířit do tří sloupců funkce, přičemž každý z nich obsahuje 0 nebo 1 v závislosti na kategorii pozorování. MLlib poskytuje funkci [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) pro jednohorké kódování. Tento kodér mapuje sloupec indexů popisků na sloupec binárních vektorů, maximálně s jednou jednou hodnotou. Toto kódování umožňuje algoritmy, které očekávají číselné hodnotné funkce, jako je například logistická regrese, které mají být použity na kategorické funkce.

Zde je kód pro indexování a kódování kategorických funkcí:

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

**Výstup:**

Doba provedená k provedení nad buňkou: 1,28 sekundy

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Vytvoření objektů bodu s popiskem pro vstup do funkcí ML
Tato část obsahuje kód, který ukazuje, jak indexovat kategorická textová data jako datový typ označeného bodu a zakódovat je tak, aby je bylo možné použít k trénování a testování logistické regrese MLlib a dalších klasifikačních modelů. Objekty bodů s popiskem jsou odolné distribuované datové sady (RDD) formátované způsobem, který je potřebný jako vstupní data většinou algoritmů ML v MLlib. [Označený bod](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) je místní vektor, hustý nebo řídké, přidružený k popisku/odpovědi.  

Tato část obsahuje kód, který ukazuje, jak indexovat kategorická textová data jako datový typ [označeného bodu](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) a zakódovat je tak, aby je bylo možné použít k trénování a testování logistické regrese MLlib a dalších klasifikačních modelů. Objekty bodů označené popisky jsou odolné distribuované datové sady (RDD), které se skládají z popisku (proměnná target/response) a vektoru prvku. Tento formát je potřeba jako vstup mnoha algoritmy ML v MLlib.

Zde je kód pro indexování a kódování textových funkcí pro binární klasifikaci.

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


Zde je kód pro kódování a index kategorické textové funkce pro lineární regresní analýzy.

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


### <a name="create-a-random-subsampling-of-the-data-and-split-it-into-training-and-testing-sets"></a>Vytvoření náhodného dílčího vzorkování dat a jejich rozdělení do trénovacích a testovacích sad
Tento kód vytvoří náhodný odběr vzorků dat (25 % se používá zde). I když není vyžadováno pro tento příklad vzhledem k velikosti datové sady, ukážeme, jak můžete ukázku zde, takže víte, jak ji použít pro vlastní problém v případě potřeby. Pokud jsou vzorky velké, vzorkování může ušetřit významný čas při trénování modelů. Dále rozdělíme vzorek na tréninkovou část (75% zde) a testovací část (25% zde) pro použití v klasifikaci a regresním modelování.

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

**Výstup:**

Doba provedená k provedení nad buňkou: 0.24 sekund

### <a name="feature-scaling"></a>měření rozsahu prvků,
Funkce škálování, označované také jako normalizace dat, zajišťuje, že funkce s široce vyplacené hodnoty nejsou uvedeny nadměrné vážení v objektivní funkce. Kód pro změnu měřítka funkcí používá [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) ke změně měřítka prvků na odchylku jednotky. Je poskytována společností MLlib pro použití v lineární regresi se Stochastic Gradient Descent (SGD), což je populární algoritmus pro školení široké škály dalších modelů strojového učení, jako jsou regularizované regrese nebo podpůrné vektorové stroje (SVM).

> [!NOTE]
> Zjistili jsme, že algoritmus LinearRegressionWithSGD je citlivý na škálování funkcí.
> 
> 

Zde je kód pro škálování proměnných pro použití s regularizovaným lineárním SGD algoritmem.

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

**Výstup:**

Doba provedená k provedení nad buňkou: 13.17 sekund

### <a name="cache-objects-in-memory"></a>Ukládání objektů do mezipaměti v paměti
Čas pro trénování a testování algoritmů ML lze zkrátit ukládáním objektů vstupního datového rámce používaných pro klasifikaci, regresi a škálované funkce.

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

**Výstup:** 

Doba provedená k provedení nad buňkou: 0.15 sekund

## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>Předpovědět, zda je tip placen pomocí binárních klasifikačních modelů
Tato část ukazuje, jak používat tři modely pro binární klasifikace úkol předpovědět, zda tip je zaplaceno za taxi výlet. Prezentované modely jsou:

* Regrese regrese pravidelné logistiky 
* Náhodný model lesa
* Přechod posílení stromy

Každý oddíl stavebního kódu modelu je rozdělen do kroků: 

1. **Modelová trénovací** data s jednou sadou parametrů
2. **Vyhodnocení modelu** v testovací datové sadě s metrikami
3. **Uložení modelu** do objektu blob pro budoucí spotřebu

### <a name="classification-using-logistic-regression"></a>Klasifikace pomocí logistické regrese
Kód v této části ukazuje, jak trénovat, vyhodnocovat a ukládat logistický regresní model s [LBFGS,](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) který předpovídá, zda je spropitné zaplaceno za cestu v datové sadě taxi a tarifů NYC.

**Trénování logistického regresního modelu pomocí cv a hyperparametrické zametání**

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


**Výstup:** 

Koeficienty: [0.0082065285375, -0,0223675576104, -0,0183812028036, -3,48124578069e-05, -0,00247666947233, -0,0016589881503, 0,067539483338, -0,111823113101, -0,324609912762, -0,204549780032, -1,36499216354, 0,591088507921, - 0.664263411392, -1.00439726852, 3.46567827545, -3.51025855172, -0.0471341112232, -0.043521833294, 0.000243375810385, 0.054518719222]

Zachycení: -0,0111216486893

Doba provedená k provedení nad buňkou: 14.43 sekund

**Vyhodnocení binárního klasifikačního modelu pomocí standardních metrik**

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

**Výstup:** 

Plocha pod PR = 0,985297691373

Plocha pod ROC = 0,983714670256

Souhrnné statistiky

Přesnost = 0,984304060189

Odvolání = 0,984304060189

F1 Skóre = 0,984304060189

Doba provedená k provedení nad buňkou: 57.61 sekund

**Vykreslete křivku ROC.**

*PredictionAndLabelsDF* je registrován jako tabulka, *tmp_results*v předchozí buňce. *tmp_results* lze použít k dotazům a výstupním výsledkům do datového rámce sqlResults pro vykreslování. Tady je kód.

    # QUERY RESULTS                              
    %%sql -q -o sqlResults
    SELECT * from tmp_results


Zde je kód, aby se předpovědi a plot ROC-křivka.

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


**Výstup:**

![Logistická regrese ROC curve.png](./media/spark-data-exploration-modeling/logistic-regression-roc-curve.png)

### <a name="random-forest-classification"></a>Náhodná klasifikace doménové struktury
Kód v této části ukazuje, jak trénovat, vyhodnocovat a ukládat náhodný model doménové struktury, který předpovídá, zda je tip placen za cestu v datové sadě taxi a tarifů NYC.

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

**Výstup:**

Plocha pod ROC = 0,985297691373

Doba provedená k provedení nad buňkou: 31.09 sekund

### <a name="gradient-boosting-trees-classification"></a>Přechod posílení stromy klasifikace
Kód v této části ukazuje, jak trénovat, vyhodnocovat a ukládat gradient posílení stromy model, který předpovídá, zda tip je zaplaceno za cestu v nyc taxi výlet a tarif datové sady.

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


**Výstup:**

Plocha pod ROC = 0,985297691373

Doba provedená k provedení nad buňkou: 19.76 sekund

## <a name="predict-tip-amounts-for-taxi-trips-with-regression-models"></a>Předvídejte částky tipu pro cesty taxíkem s regresními modely
Tato část ukazuje, jak používat tři modely pro regresní úkol předvídání výše tip uhrazené za taxi výlet na základě jiných funkcí tip. Prezentované modely jsou:

* Regrese regularizované lineární regrese
* Náhodná doménová struktura
* Přechod posílení stromy

Tyto modely byly popsány v úvodu. Každý oddíl stavebního kódu modelu je rozdělen do kroků: 

1. **Modelová trénovací** data s jednou sadou parametrů
2. **Vyhodnocení modelu** v testovací datové sadě s metrikami
3. **Uložení modelu** do objektu blob pro budoucí spotřebu

### <a name="linear-regression-with-sgd"></a>Lineární regrese s SGD
Kód v této části ukazuje, jak používat škálované funkce k trénování lineární regrese, která používá stochastický sestup přechodu (SGD) pro optimalizaci a jak skóre, vyhodnocení a uložení modelu v Azure Blob Storage (WASB).

> [!TIP]
> Podle našich zkušeností mohou být problémy s konvergencí modelů LinearRegressionWithSGD a parametry je třeba pečlivě změnit/optimalizovat pro získání platného modelu. Škálování proměnných výrazně pomáhá při konvergenci. 
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

**Výstup:**

Koeficienty: [0.00457675809917, -0.0226314167349, -0.0191910355236, 0.246793409578, 0.312047890459, 0.359634405999, 0.312047890459, 0.3596344059999, 0.312047894599, 0.3596344059999, 0.3120478904599, 0.359634405999, 0.00928692253981, -0.0009871814889428, -0.0888306617845, 0.05693762115553, 0.115519551711, 0.149250164995, - 0.00990211159703, -0.00637410344522, 0.545083566179, -0.536756072402, 0.0105762393099, -0.0130117577055, 0.0129304737772, -0.00171065945959]

Zachytit: 0.853872718283

RMSE = 1,24190115863

R-sqr = 0,608017146081

Doba provedená k provedení nad buňkou: 58.42 sekund

### <a name="random-forest-regression"></a>Náhodná regrese lesa
Kód v této části ukazuje, jak trénovat, vyhodnocovat a ukládat náhodnou regresi doménového lesa, která předpovídá množství tipu pro data cesty taxi NYC.

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

**Výstup:**

RMSE = 0,891209218139

R-sqr = 0,759661334921

Doba provedená k provedení nad buňkou: 49.21 sekund

### <a name="gradient-boosting-trees-regression"></a>Gradient posílení stromů regrese
Kód v této části ukazuje, jak trénovat, vyhodnocovat a ukládat model stromů zvyšujících přechod, který předpovídá množství tipu pro data cesty taxi NYC.

**Školení a hodnocení**

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

**Výstup:**

RMSE = 0,908473148639

R-sqr = 0,753835096681

Doba provedená k provedení nad buňkou: 34.52 sekund

**Plot**

*tmp_results* je registrován jako tabulka Hive v předchozí buňce. Výsledky z tabulky jsou výstupem do datového rámce *sqlResults* pro vykreslování. Zde je kód

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT * from tmp_results

Zde je kód pro vykreslení dat pomocí serveru Jupyter.

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


**Výstup:**

![Skutečné vs-předpovídané-tip-částky](./media/spark-data-exploration-modeling/actual-vs-predicted-tips.png)

## <a name="clean-up-objects-from-memory"></a>Vyčistit objekty z paměti
Slouží `unpersist()` k odstranění objektů uložených v paměti.

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


## <a name="record-storage-locations-of-the-models-for-consumption-and-scoring"></a>Záznam skladových míst modelů pro spotřebu a vyhodnocování
Chcete-li spotřebovat a získat skóre nezávislou datovou sadu popsanou v tématu [Skóre a vyhodnotit modely strojového učení vytvořené společností Spark,](spark-model-consumption.md) musíte zkopírovat a vložit tyto názvy souborů obsahující uložené modely vytvořené zde do poznámkového bloku Consumption Jupyter. Zde je kód pro tisk cest k modelovým souborům, které tam potřebujete.

    # MODEL FILE LOCATIONS FOR CONSUMPTION
    print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
    print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
    print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
    print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
    print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
    print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";


**Výstup**

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05-0317_03_23.516568"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-05-0317_05_21.577773"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05-0317_04_11.950206"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05-0317_06_08.723736"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05-0317_04_36.346583"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05-0317_06_51.737282"

## <a name="whats-next"></a>Co dále?
Nyní, když jste vytvořili regresní a klasifikační modely s Spark MlLib, jste připraveni se naučit, jak skóre a vyhodnocení těchto modelů. Pokročilý přehled dat a modelování notebooku se ponoří hlouběji do včetně křížového ověřování, hyperparametrické zametání a vyhodnocení modelu. 

**Spotřeba modelu:** Informace o tom, jak skóre a vyhodnotit klasifikace a regresní modely vytvořené v tomto tématu, najdete v [tématu skóre a vyhodnotit Spark-postavený modely strojového učení](spark-model-consumption.md).

**Křížové ověřování a hyperparametrické zametání**: Podívejte se na [pokročilé zkoumání dat a modelování se Sparkem](spark-advanced-data-exploration-modeling.md) o tom, jak lze modely trénovat pomocí křížového ověřování a hyperparametrické zametání

