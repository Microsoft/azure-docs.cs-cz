---
title: Pokročilé zkoumání a modelování dat pomocí Spark – Team Data Science Process
description: Pomocí hdinsightové sparky můžete prozkoumat data a trénovat binární klasifikační a regresní modely pomocí křížového ověřování a hyperparametrické optimalizace.
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
ms.openlocfilehash: 15d9d186ef36ee9181a6ce0386aa9cc5de7838e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76718646"
---
# <a name="advanced-data-exploration-and-modeling-with-spark"></a>Pokročilé zkoumání a modelování dat pomocí Spark

Tento návod používá HDInsight Spark k průzkumu dat a trénování binárních klasifikací a regresních modelů pomocí křížového ověření a optimalizace hyperparametrů na vzorku datové sady taxi nyc a fare 2013. Provede vás kroky [procesu datové vědy](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/), end-to-end, pomocí HDInsight Spark clusteru pro zpracování a Azure objekty BLOB pro ukládání dat a modelů. Proces zkoumá a vizualizuje data přenesená z objektu blob úložiště Azure a pak připraví data k vytváření prediktivních modelů. Python byl použit ke kódování řešení a zobrazení příslušných pozemků. Tyto modely jsou vytvářeny pomocí sady nástrojů Spark MLlib k provedení úloh binární klasifikace a regresního modelování. 

* Úkolem **binární klasifikace** je předpovědět, zda je pro cestu zaplacen tip. 
* **Regresní** úkol je předpovědět množství tip na základě jiných funkcí tip. 

Kroky modelování také obsahují kód, který ukazuje, jak trénovat, vyhodnocovat a ukládat každý typ modelu. Téma pokrývá některé stejné země jako průzkum dat a modelování s tématem [Spark.](spark-data-exploration-modeling.md) Ale je to více "pokročilé" v tom, že také používá křížové ověření s hyperparametry zametání trénovat optimálně přesné klasifikace a regresní modely. 

**Křížové ověření (CV)** je technika, která posuzuje, jak dobře model trénovaný na známé sadě dat generalizuje k předpovídání funkcí datových sad, na kterých nebyl trénovaný.  Běžná implementace zde používá je rozdělit datovou sadu do K záhyby a pak trénovat model v kruhovém dotazování způsobem na všechny, ale jeden z záhybů. Schopnost modelu přesně předpovědět při testování proti nezávislé datové sady v tomto záhybu, který se nepoužívá k trénování modelu, se posuzuje.

**Optimalizace hyperparametrů** je problém výběru sady hyperparametrů pro algoritmus učení, obvykle s cílem optimalizovat míru výkonu algoritmu na nezávislé datové sadě. **Hyperparametry** jsou hodnoty, které musí být zadány mimo postup školení modelu. Předpoklady o těchto hodnotách mohou ovlivnit flexibilitu a přesnost modelů. Rozhodovací stromy mají hyperparametry, například, jako je požadovaná hloubka a počet listů ve stromu. Podpora Vector Machines (SVMs) vyžadují nastavení chybné klasifikace trest termín. 

Běžným způsobem, jak provést optimalizaci hyperparametrů, která je zde použita, je vyhledávání mřížky nebo **zametání parametrů**. Toto hledání prochází podmnožinou hyperparametrický prostor pro algoritmus učení. Křížové ověření může poskytnout metriku výkonu k vyřešení optimálních výsledků vytvořených algoritmem vyhledávání v mřížce. CV používá s hyperparameter zametání pomáhá omezit problémy, jako je nadměrné přizpůsobení modelu data školení tak, aby model zachová kapacitu použít na obecnou sadu dat, ze kterých byla extrahována trénovací data.

Modely, které používáme, zahrnují logistickou a lineární regresi, náhodné lesy a stromy zesílené přechodem:

* [Lineární regrese s SGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) je lineární regresní model, který používá metodu Stochastic Gradient Descent (SGD) a pro optimalizaci a změnu funkce předpovědět částky tipu. 
* [Logistická regrese s LBFGS](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) nebo "logit" regrese, je regresní model, který lze použít, když je závislá proměnná kategorická pro klasifikaci dat. LBFGS je kvazi-Newton ův optimalizační algoritmus, který se blíží algoritmu Broyden-Fletcher-Goldfarb-Shanno (BFGS) pomocí omezeného množství počítačové paměti a který je široce používán ve strojovém učení.
* [Náhodné lesy](https://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) jsou soubory rozhodovacích stromů.  Kombinují mnoho rozhodovacích stromů, aby se snížilo riziko nadměrného vybavení. Náhodné doménové struktury se používají pro regresi a klasifikaci a mohou zpracovávat kategorické prvky a lze je rozšířit na nastavení klasifikace více tříd. Nevyžadují změnu měřítka prvku a jsou schopny zachytit nelinearity a interakce prvků. Náhodné doménové struktury jsou jedním z nejúspěšnějších modelů strojového učení pro klasifikaci a regresi.
* [Gradient posílil stromy](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTS) jsou soubory rozhodnutí stromů. GBTS rozhodnutí vlaku stromy iterativně minimalizovat ztrátu funkce. GBTS se používá pro regresi a klasifikaci a může zpracovávat kategorické funkce, nevyžadují změnu měřítka prvků a jsou schopny zachytit nelinearity a interakce funkcí. Lze je také použít v nastavení klasifikace více tříd.

Příklady modelování pomocí CV a Hyperparameter sweep jsou zobrazeny pro binární klasifikace problém. Jednodušší příklady (bez zametá parametr) jsou uvedeny v hlavním tématu pro regresní úkoly. Ale v příloze je také prezentována validace pomocí elastické sítě pro lineární regresi a CV s parametrem sweep pomocí náhodné regrese lesa. **Elastická síť** je metoda regrese upravidelnou pro přizpůsobení lineárních regresních modelů, která lineárně kombinuje metriky L1 a L2 jako sankce metod [laso](https://en.wikipedia.org/wiki/Lasso%20%28statistics%29) a [hřeben.](https://en.wikipedia.org/wiki/Tikhonov_regularization)   

<!-- -->

> [!NOTE]
> Přestože sada nástrojů Spark MLlib je navržena pro práci s velkými datovými sadami, pro usnadnění se zde používá relativně malá ukázka (~30 Mb pomocí řádků 170 K, přibližně 0,1 % původní datové sady NYC). Cvičení zde uvedené běží efektivně (asi za 10 minut) v clusteru HDInsight se 2 pracovními uzly. Stejný kód s menšími úpravami lze použít ke zpracování větších datových sad s příslušnými úpravami pro ukládání dat do mezipaměti v paměti a změnu velikosti clusteru.

<!-- -->

## <a name="setup-spark-clusters-and-notebooks"></a>Nastavení: Clustery a poznámkové bloky Spark
Kroky nastavení a kód jsou k dispozici v tomto návodu pro použití HDInsight Spark 1.6. Ale notebooky Jupyter jsou k dispozici pro clustery HDInsight Spark 1.6 a Spark 2.0. Popis poznámkových bloků a odkazy na ně jsou k dispozici v [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) pro úložiště GitHub, které je obsahuje. Kromě toho je kód zde a v propojených poznámkových blocích obecný a měl by fungovat na libovolném clusteru Spark. Pokud nepoužíváte HDInsight Spark, kroky nastavení a správy clusteru se mohou mírně lišit od toho, co je zobrazeno zde. Pro větší pohodlí, zde jsou odkazy na notebooky Jupyter pro Spark 1.6 a 2.0, které mají být spuštěny v jádru pyspark na jupyter notebookserveru:

### <a name="spark-16-notebooks"></a>Spark 1,6 notebooky

[pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Zahrnuje témata v #1 notebooků a vývoj modelů pomocí hyperparametrizování a křížového ověřování.

### <a name="spark-20-notebooks"></a>Spark 2.0 notebooky

[Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Tento soubor obsahuje informace o tom, jak provádět zkoumání dat, modelování a vyhodnocování v clusterech Spark 2.0.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

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
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";

    # PRINT START TIME
    import datetime
    datetime.datetime.now()

**Výstup**

datetime.datetime(2016, 4, 18, 17, 36, 27, 832799)

### <a name="import-libraries"></a>Import knihoven
Importujte potřebné knihovny s následujícím kódem:

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


### <a name="preset-spark-context-and-pyspark-magics"></a>Přednastavený kontext Spark a kouzla PySpark
Jádra PySpark, která jsou dodávána s poznámkovými bloky Jupyter, mají přednastavený kontext. Takže není nutné explicitně nastavit kontexty Spark nebo Hive před zahájením práce s aplikací, kterou vyvíjíte. Tyto kontexty jsou k dispozici ve výchozím nastavení. Tyto kontexty jsou:

* sc - pro Jiskru 
* sqlContext - pro Hive

Jádro PySpark poskytuje některé předdefinované "magie", což jsou speciální příkazy, které můžete volat pomocí %%. Existují dva takové příkazy, které se používají v těchto ukázkách kódu.

* **%%místní** Určuje, že kód v následujících řádcích má být proveden místně. Kód musí být platný kód Pythonu.
* **%%sql -o \<název>proměnných** Spustí dotaz Hive proti sqlContext. Pokud je parametr -o předán, výsledek dotazu je zachován v kontextu %%místní python jako Pandas DataFrame.

Další informace o jádrech notebooků Jupyter a předdefinovaných "kouzlech", které poskytují, naleznete [v tématu Jádra dostupná pro notebooky Jupyter s clustery HDInsight Spark Linux na HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

## <a name="data-ingestion-from-public-blob"></a>Přijímat data z veřejného objektu blob:
Prvním krokem v procesu datové vědy je ingestování dat, která mají být analyzována ze zdrojů, kde se nacházejí do prostředí pro zkoumání a modelování dat. Toto prostředí je Spark v tomto návodu. Tato část obsahuje kód k dokončení řady úkolů:

* požití vzorku dat, který má být modelován
* čtení ve vstupní datové sadě (uložené jako soubor .tsv)
* formátování a čištění dat
* vytváření a ukládání objektů do mezipaměti (RDD nebo datových rámců) v paměti
* zaregistrujte ji jako temp-table v kontextu SQL.

Zde je kód pro požití dat.

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


**Výstup**

Doba provedená k provedení nad buňkou: 276.62 sekund

## <a name="data-exploration--visualization"></a>Vizualizace & zkoumání dat
Jakmile jsou data přenesena do Sparku, dalším krokem v procesu datové vědy je získat hlubší pochopení dat prostřednictvím průzkumu a vizualizace. V této části zkontrolujeme data taxi pomocí SQL dotazů a vykreslíme cílové proměnné a perspektivní funkce pro vizuální kontrolu. Konkrétně vykreslujeme frekvenci počtu cestujících při jízdách taxíkem, frekvenci výše spropitného a způsob, jakým se tipy liší podle výše a typu platby.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Vykreslete histogram frekvencí počtu cestujících ve vzorku cest taxíkem
Tento kód a následné úryvky pomocí SQL magie k dotazování vzorku a místní magie k vykreslení dat.

* **SQL magie`%%sql`( )** Jádro HDInsight PySpark podporuje snadné vložkové dotazy HiveQL proti sqlContext. Argument (-o VARIABLE_NAME) zachová výstup dotazu SQL jako Pandas DataFrame na serveru Jupyter. To znamená, že je k dispozici v místním režimu.
* ** `%%local` Kouzlo** se používá ke spuštění kódu místně na serveru Jupyter, což je headnode clusteru HDInsight. Obvykle používáte `%%local` kouzlo po `%%sql -o` kouzlo se používá ke spuštění dotazu. Parametr -o by místně zachovává výstup dotazu SQL. Pak `%%local` kouzlo aktivuje další sadu fragmentů kódu spustit místně proti výstupu dotazů SQL, která byla trvale místně. Výstup je automaticky vizualizován po spuštění kódu.

Tento dotaz načte cesty podle počtu cestujících. 

    # PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

    # SQL QUERY
    %%sql -q -o sqlResults
    SELECT passenger_count, COUNT(*) as trip_counts FROM taxi_train WHERE passenger_count > 0 and passenger_count < 7 GROUP BY passenger_count


Tento kód vytvoří místní datový rámec z výstupu dotazu a vykreslí data. Kouzlo `%%local` vytvoří místní datový rámec `sqlResults`, který lze použít pro vykreslování s matplotlib. 

<!-- -->

> [!NOTE]
> Tato magie PySpark se používá vícekrát v tomto návodu. Pokud je velké množství dat, měli byste vzorek vytvořit datový rámec, který se vejde do místní paměti.

<!-- -->

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
    # CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
    sqlResults

Zde je kód pro vykreslení cesty podle počtu cestujících

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

**Výstup**

![Četnost jízd podle počtu cestujících](./media/spark-advanced-data-exploration-modeling/frequency-of-trips-by-passenger-count.png)

Pomocí tlačítek nabídky **Typ** v poznámkovém bloku můžete vybrat několik různých typů vizualizací (tabulka, výsečový, spojník, plocha nebo pruh). Zde je zobrazena zápletka baru.

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Vykreslete histogram částek spropitného a způsob, jakým se částka spropitného liší podle počtu cestujících a částek jízdného.
K ukázkovému množství dat použijte dotaz SQL.

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


Tato buňka kódu používá dotaz SQL k vytvoření tří vykreslení dat.

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


**Výstup:** 

![Rozložení částky tipu](./media/spark-advanced-data-exploration-modeling/tip-amount-distribution.png)

![Výše tipu podle počtu cestujících](./media/spark-advanced-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Částka tipu podle částky jízdného](./media/spark-advanced-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>Inženýring, transformace a příprava dat pro modelování
Tato část popisuje a poskytuje kód pro postupy používané k přípravě dat pro použití v modelování ML. Ukazuje, jak provést následující úkoly:

* Vytvoření nové funkce rozdělením hodin do přihrádek doby provozu
* Index a on-hot kódovat kategorické funkce
* Vytvoření objektů bodu s popiskem pro vstup do funkcí ML
* Vytvoření náhodného dílčího vzorkování dat a jejich rozdělení do trénovacích a testovacích sad
* měření rozsahu prvků,
* Ukládání objektů do mezipaměti v paměti

### <a name="create-a-new-feature-by-partitioning-traffic-times-into-bins"></a>Vytvoření nové funkce rozdělením doby provozu do přihrádek
Tento kód ukazuje, jak vytvořit novou funkci rozdělením doby provozu do přihrádek a potom jak uložit výsledný datový rámec do mezipaměti v paměti. Ukládání do mezipaměti vede ke zlepšení doby provádění, kde jsou opakovaně používány odolné distribuované datové sady (RDD) a datové rámce. Takže v tomto návodu ukládáme do mezipaměti RDD a datové rámce v několika fázích.

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

**Výstup**

126050

### <a name="index-and-one-hot-encode-categorical-features"></a>Index a jednohorké kódování kategorických funkcí
Tato část ukazuje, jak indexovat nebo kódovat kategorické funkce pro vstup do funkcí modelování. Funkce modelování a předvídání MLlib vyžadují, aby funkce s kategorickými vstupními daty byly před použitím indexovány nebo kódovány. 

V závislosti na modelu je nutné je indexovat nebo kódovat různými způsoby. Například logistické a lineární regrese modely vyžadují jedno-horké kódování, kde například funkce se třemi kategoriemi lze rozbalit do tří sloupců funkce, přičemž každý z nich obsahuje 0 nebo 1 v závislosti na kategorii pozorování. MLlib poskytuje funkci [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) pro jednohorké kódování. Tento kodér mapuje sloupec indexů popisků na sloupec binárních vektorů, maximálně s jednou jednou hodnotou. Toto kódování umožňuje algoritmy, které očekávají číselné hodnotné funkce, jako je například logistická regrese, které mají být použity na kategorické funkce.

Zde je kód pro indexování a kódování kategorických funkcí:

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


**Výstup**

Doba provedená k provedení nad buňkou: 3.14 sekund

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Vytvoření objektů bodu s popiskem pro vstup do funkcí ML
Tato část obsahuje kód, který ukazuje, jak indexovat kategorická textová data jako datový typ označeného bodu a jak je zakódovat. Tato transformace připraví textová data, která mají být použita k trénování a testování logistické regrese MLlib a dalších klasifikačních modelů. Objekty bodů s popiskem jsou odolné distribuované datové sady (RDD) formátované způsobem, který je potřebný jako vstupní data většinou algoritmů ML v MLlib. [Označený bod](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) je místní vektor, hustý nebo řídké, přidružený k popisku/odpovědi.

Zde je kód pro indexování a kódování textových funkcí pro binární klasifikaci.

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

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC REGRESSION MODELS
    def parseRowOneHotBinary(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                   line.vendorVec.toArray(), line.rateVec.toArray(), line.paymentVec.toArray()), axis=0)
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
Tento kód vytvoří náhodný odběr vzorků dat (25 % se používá zde). I když není vyžadováno pro tento příklad vzhledem k velikosti datové sady, ukážeme, jak můžete vzorkovat data zde. Pak víte, jak ji použít pro svůj vlastní problém v případě potřeby. Pokud jsou vzorky velké, vzorkování může ušetřit významný čas při trénování modelů. Dále rozdělíme vzorek na tréninkovou část (75% zde) a testovací část (25% zde) pro použití v klasifikaci a regresním modelování.

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

**Výstup**

Doba provedená k provedení nad buňkou: 0.31 sekund

### <a name="feature-scaling"></a>měření rozsahu prvků,
Funkce škálování, označované také jako normalizace dat, zajišťuje, že funkce s široce vyplacené hodnoty nejsou uvedeny nadměrné vážení v objektivní funkce. Kód pro změnu měřítka funkcí používá [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) ke změně měřítka prvků na odchylku jednotky. Poskytuje jej společnost MLlib pro použití v lineární regresi se stochastickým gradientním sestupem (SGD). SGD je populární algoritmus pro školení širokou škálu dalších modelů strojového učení, jako jsou regularizované regrese nebo podpora vektorových strojů (SVM).   

> [!TIP]
> Zjistili jsme, že algoritmus LinearRegressionWithSGD je citlivý na škálování funkcí.   
> 
> 

Zde je kód pro škálování proměnných pro použití s regularizovaným lineárním SGD algoritmem.

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

**Výstup**

Doba provedená k provedení nad buňkou: 11.67 sekund

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

**Výstup** 

Doba provedená k provedení nad buňkou: 0.13 sekund

## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>Předpovědět, zda je tip placen pomocí binárních klasifikačních modelů
Tato část ukazuje, jak používat tři modely pro binární klasifikace úkol předpovědět, zda tip je zaplaceno za taxi výlet. Prezentované modely jsou:

* Logistická regrese 
* Náhodná doménová struktura
* Přechod posílení stromy

Každý oddíl stavebního kódu modelu je rozdělen do kroků: 

1. **Modelová trénovací** data s jednou sadou parametrů
2. **Vyhodnocení modelu** v testovací datové sadě s metrikami
3. **Uložení modelu** do objektu blob pro budoucí spotřebu

Ukazujeme, jak provést křížové ověření (CV) s parametrem zametání dvěma způsoby:

1. Pomocí **obecného** vlastního kódu, který lze použít na libovolný algoritmus v MLlib a na všechny sady parametrů v algoritmu. 
2. Pomocí **funkce pySpark CrossValidator pipeline .** CrossValidator má několik omezení pro Spark 1.5.0: 
   
   * Modely kanálu nelze uložit nebo trvalé pro budoucí spotřebu.
   * Nelze použít pro každý parametr v modelu.
   * Nelze použít pro každý algoritmus MLlib.

### <a name="generic-cross-validation-and-hyperparameter-sweeping-used-with-the-logistic-regression-algorithm-for-binary-classification"></a>Obecné křížové ověřování a hyperparametrické zametání používané s logistickým regresním algoritmem pro binární klasifikaci
Kód v této části ukazuje, jak trénovat, vyhodnocovat a ukládat logistický regresní model s [LBFGS,](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) který předpovídá, zda je spropitné zaplaceno za cestu v datové sadě taxi a tarifů NYC. Model je trénován pomocí křížového ověření (CV) a hyperparametrické zametání implementováno s vlastním kódem, který lze použít na libovolný algoritmus učení v MLlib.   

<!-- -->

> [!NOTE]
> Spuštění tohoto vlastního kódu životopisu může trvat několik minut.

<!-- -->

**Trénování logistického regresního modelu pomocí cv a hyperparametrické zametání**

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


**Výstup**

Koeficienty: [0.0082065285375, -0,0223675576104, -0,0183812028036, -3,48124578069e-05, -0,00247666947233, -0,0016589881503, 0,067539483338, -0,111823113101, -0,324609912762, -0,204549780032, -1,36499216354, 0,591088507921, - 0.664263411392, -1.00439726852, 3.46567827545, -3.51025855172, -0.0471341112232, -0.043521833294, 0.000243375810385, 0.054518719222]

Zachycení: -0,0111216486893

Doba provedená k provedení nad buňkou: 14.43 sekund

**Vyhodnocení binárního klasifikačního modelu pomocí standardních metrik**

Kód v této části ukazuje, jak vyhodnotit model logistické regrese proti testovací datové sadě, včetně vykreslení křivky ROC.

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


**Výstup**

Plocha pod PR = 0,985336538462

Plocha pod ROC = 0,983383274312

Souhrnné statistiky

Přesnost = 0,984174341679

Odvolání = 0,984174341679

F1 Skóre = 0,984174341679

Doba provedená k provedení nad buňkou: 2.67 sekund

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


**Výstup**

![Logická regresní ROC křivka pro obecný přístup](./media/spark-advanced-data-exploration-modeling/logistic-regression-roc-curve.png)

**Zachovat model v objektu blob pro budoucí spotřebu**

Kód v této části ukazuje, jak uložit model logistické regrese pro spotřebu.

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


**Výstup**

Doba provedená k provedení nad buňkou: 34.57 sekund

### <a name="use-mllibs-crossvalidator-pipeline-function-with-logistic-regression-elastic-regression-model"></a>Použití funkce kanálu CrossValidator společnosti MLlib s logistickou regresní (elastickou regresní) modelem
Kód v této části ukazuje, jak trénovat, vyhodnocovat a ukládat logistický regresní model s [LBFGS,](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) který předpovídá, zda je spropitné zaplaceno za cestu v datové sadě taxi a tarifů NYC. Model je trénován pomocí křížovévalidace (CV) a hyperparametrické zametání implementované pomocí funkce potrubí MLlib CrossValidator pro CV s parametrem sweep.   

<!-- -->

> [!NOTE]
> Spuštění tohoto kódu CV MLlib může trvat několik minut.

<!-- -->

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

**Výstup**

Doba provedená k provedení nad buňkou: 107.98 sekund

**Vykreslete křivku ROC.**

*PredictionAndLabelsDF* je registrován jako tabulka, *tmp_results*v předchozí buňce. *tmp_results* lze použít k dotazům a výstupním výsledkům do datového rámce sqlResults pro vykreslování. Tady je kód.

    # QUERY RESULTS
    %%sql -q -o sqlResults
    SELECT label, prediction, probability from tmp_results

Zde je kód pro vykreslení křivky ROC.

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


**Výstup**

![Logická regresní ROC křivka pomocí MLlibova crossvalidátoru](./media/spark-advanced-data-exploration-modeling/mllib-crossvalidator-roc-curve.png)

### <a name="random-forest-classification"></a>Náhodná klasifikace doménové struktury
Kód v této části ukazuje, jak trénovat, vyhodnocovat a ukládat náhodnou regresi doménového lesa, která předpovídá, zda je tip placen za cestu v datové sadě taxi a tarifů NYC.

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


**Výstup**

Plocha pod ROC = 0,985336538462

Doba provedená k provedení nad buňkou: 26.72 sekund

### <a name="gradient-boosting-trees-classification"></a>Přechod posílení stromy klasifikace
Kód v této části ukazuje, jak trénovat, vyhodnocovat a ukládat gradient posílení stromy model, který předpovídá, zda tip je zaplaceno za cestu v nyc taxi výlet a tarif datové sady.

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

**Výstup**

Plocha pod ROC = 0,985336538462

Doba provedená k provedení nad buňkou: 28.13 sekund

## <a name="predict-tip-amount-with-regression-models-not-using-cv"></a>Předpovědět množství špičky u regresních modelů (bez cv)
Tato část ukazuje, jak používat tři modely pro regresní úkol: předpovědět částku tipu zaplacenou za cestu taxíkem na základě dalších funkcí tipu. Prezentované modely jsou:

* Regrese regularizované lineární regrese
* Náhodná doménová struktura
* Přechod posílení stromy

Tyto modely byly popsány v úvodu. Každý oddíl stavebního kódu modelu je rozdělen do kroků: 

1. **Modelová trénovací** data s jednou sadou parametrů
2. **Vyhodnocení modelu** v testovací datové sadě s metrikami
3. **Uložení modelu** do objektu blob pro budoucí spotřebu   

<!-- -->

> [!NOTE] 
> Křížové ověření se nepoužívá se třemi regresními modely v této části, protože to bylo podrobně zobrazeno pro logistické regresní modely. Příklad, který ukazuje, jak používat životopis s elastickou sítí pro lineární regresi, je uveden v dodatku k tomuto tématu.

<!-- -->

<!-- -->

> [!NOTE] 
> Podle našich zkušeností mohou být problémy s konvergencí modelů LinearRegressionWithSGD a parametry je třeba pečlivě změnit/optimalizovat pro získání platného modelu. Škálování proměnných výrazně pomáhá při konvergenci. Elastické čisté regrese, uvedené v dodatku k tomuto tématu, lze také použít namísto LinearRegressionWithSGD.

<!-- -->

### <a name="linear-regression-with-sgd"></a>Lineární regrese s SGD
Kód v této části ukazuje, jak používat škálované funkce k trénování lineární regrese, která používá stochastický sestup přechodu (SGD) pro optimalizaci a jak skóre, vyhodnocení a uložení modelu v Azure Blob Storage (WASB).

> [!TIP]
> Podle našich zkušeností mohou být problémy s konvergencí modelů LinearRegressionWithSGD a parametry je třeba pečlivě změnit/optimalizovat pro získání platného modelu. Škálování proměnných výrazně pomáhá při konvergenci.
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

**Výstup**

Koeficienty: [0.0141707753435, -0,0252930927087, -0,0231442517137, 0,2470709029996, 0,312544147152, 0,360296120645, 0,0122079566092, -0,00456498588241, -0,0898228505177, 0,0714046248793, 0,102171263868, 0,1000224555632, -0,002895456649, - 0.00791124681938, 0.54396316518, -0.536293513569, 0.0119076553369, -0.0173039244582, 0.0119632796147, 0.00146764882502]

Zachycení: 0.854507624459

RMSE = 1,23485131376

R-sqr = 0,597963951127

Doba provedená k provedení nad buňkou: 38.62 sekund

### <a name="random-forest-regression"></a>Náhodná regrese lesa
Kód v této části ukazuje, jak trénovat, vyhodnocovat a ukládat náhodný model doménové struktury, který předpovídá částku tipu pro data cesty taxi NYC.   

<!-- -->

> [!NOTE]
> Křížové ověření s parametrem zametání pomocí vlastního kódu je k dispozici v dodatku.

<!-- -->

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

**Výstup**

RMSE = 0,931981967875

R-sqr = 0,733445485802

Doba provedená k provedení nad buňkou: 25.98 sekund

### <a name="gradient-boosting-trees-regression"></a>Gradient posílení stromů regrese
Kód v této části ukazuje, jak trénovat, vyhodnocovat a ukládat model stromů zvyšujících přechod, který předpovídá množství tipu pro data cesty taxi NYC.

**Školení a hodnocení**

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


**Výstup**

RMSE = 0,928172197114

R-sqr = 0,732680354389

Doba provedená k provedení nad buňkou: 20,9 sekundy

**Plot**

*tmp_results* je registrován jako tabulka Hive v předchozí buňce. Výsledky z tabulky jsou výstupem do datového rámce *sqlResults* pro vykreslování. Zde je kód

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT * from tmp_results


Zde je kód pro vykreslení dat pomocí serveru Jupyter.

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

![Skutečné vs-předpovídané-tip-částky](./media/spark-advanced-data-exploration-modeling/actual-vs-predicted-tips.png)

## <a name="appendix-additional-regression-tasks-using-cross-validation-with-parameter-sweeps"></a>Dodatek: Další regresní úlohy pomocí křížového ověření s parametrem sweeps
Tento dodatek obsahuje kód, který ukazuje, jak provést životopis pomocí elastické sítě pro lineární regrese a jak provést CV s parametrem sweep pomocí vlastního kódu pro náhodnou regresi doménové struktury.

### <a name="cross-validation-using-elastic-net-for-linear-regression"></a>Křížové ověření pomocí elastické sítě pro lineární regresi
Kód v této části ukazuje, jak provést křížové ověření pomocí elastické sítě pro lineární regrese a jak vyhodnotit model proti testovacím datům.

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

    # CONVERT TO DF AND SAVE REGISTER DF AS TABLE
    predictionAndLabels.registerTempTable("tmp_results");

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**Výstup**

Doba provedená k provedení nad buňkou: 161.21 sekund

**Vyhodnocení pomocí metriky R-SQR**

*tmp_results* je registrován jako tabulka Hive v předchozí buňce. Výsledky z tabulky jsou výstupem do datového rámce *sqlResults* pro vykreslování. Zde je kód

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT label,prediction from tmp_results


Zde je kód pro výpočet R-sqr.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    from scipy import stats

    #R-SQR TEST METRIC
    corstats = stats.linregress(sqlResults['label'],sqlResults['prediction'])
    r2 = (corstats[2]*corstats[2])
    print("R-sqr = %s" % r2)


**Výstup**

R-sqr = 0,619184907088

### <a name="cross-validation-with-parameter-sweep-using-custom-code-for-random-forest-regression"></a>Křížové ověření s parametrem sweep pomocí vlastního kódu pro náhodnou regresi doménové struktury
Kód v této části ukazuje, jak provést křížové ověření s parametrem sweep pomocí vlastního kódu pro náhodnou regresi doménové struktury a jak vyhodnotit model proti testovacím datům.

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


**Výstup**

RMSE = 0,906972198262

R-sqr = 0,740751197012

Doba provedená k provedení nad buňkou: 69.17 sekund

### <a name="clean-up-objects-from-memory-and-print-model-locations"></a>Vyčištění objektů z umístění paměťových a tiskových modelů
Slouží `unpersist()` k odstranění objektů uložených v paměti.

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


**Výstup**

PythonRDD[122] ve společnosti RDD at PythonRDD.scala: 43

**Výstupní cesta k souborům modelů, které mají být použity v poznámkovém bloku spotřeby. ** Chcete-li konzumovat a získat nezávislou datovou sadu, musíte tyto názvy souborů zkopírovat a vložit do poznámkového bloku "Spotřeba".

    # PRINT MODEL FILE LOCATIONS FOR CONSUMPTION
    print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
    print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
    print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
    print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
    print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
    print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";


**Výstup**

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05-0316_47_30.096528"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-05-0316_51_28.433670"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05-0316_50_17.454440"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05-0316_51_57.331730"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05-0316_50_40.138809"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05-0316_52_18.827237"

## <a name="whats-next"></a>Co dále?
Nyní, když jste vytvořili regresní a klasifikační modely s Spark MlLib, jste připraveni se naučit, jak skóre a vyhodnocení těchto modelů.

**Spotřeba modelu:** Informace o tom, jak skóre a vyhodnotit klasifikace a regresní modely vytvořené v tomto tématu, najdete v [tématu skóre a vyhodnotit Spark-postavený modely strojového učení](spark-model-consumption.md).

