---
title: Pokročilé zkoumání dat a modelování pomocí Spark | Microsoft Docs
description: Pomocí HDInsight Spark proveďte zkoumání dat a cvičení binární klasifikace a regrese modelů pomocí křížové ověření a hyperparameter optimalizace.
services: machine-learning
documentationcenter: ''
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: f90d9a80-4eaf-437b-a914-23514390cd60
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: bradsev
ms.openlocfilehash: 3d4cd29596b14e6de07fdf06191f7d69c2d39641
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="advanced-data-exploration-and-modeling-with-spark"></a>Pokročilé zkoumání a modelování dat pomocí Spark
[!INCLUDE [machine-learning-spark-modeling](../../../includes/machine-learning-spark-modeling.md)]

Tento návod používá HDInsight Spark uděláte zkoumání dat a cvičení binární klasifikace a modely regrese pomocí křížové ověření a hyperparameter optimalizace na základě vzorku NYC taxi cestě a jízdenky 2013 datovou sadu. Provede vás provede postupem [proces vědecké účely dat](http://aka.ms/datascienceprocess), klient server, pomocí clusteru služby HDInsight Spark pro zpracování a Azure objektů BLOB k ukládání dat a modely. Proces jsou zde popsány vizualizuje dat získaných z objektu Blob úložiště Azure a pak připraví dat za účelem vytvoření prediktivní modely. Python byla použita k code řešení a zobrazíte relevantní pozemků. Tyto modely jsou sestavení pomocí sady nástrojů pro Spark MLlib provádění binární klasifikaci a regresní modelování úkolů. 

* **Binární klasifikace** úloh je k předvídání, zda je tip placené pro cestu. 
* **Regrese** úloh je k předvídání množství tip podle dalších funkcí tip. 

Modelování kroky také obsahovat kód znázorňující cvičení, hodnocení a uložit každý typ modelu. Téma pokrývá některé stejné zemi jako [zkoumání dat a modelování pomocí Spark](spark-data-exploration-modeling.md) tématu. Ale ho je "složitější," také používá křížového ověřování s hyperparameter komínů ke cvičení optimálně přesné klasifikace a regrese modelů. 

**Křížové ověření (odchylka nákladů)** je technika, který vyhodnocuje, jak dobře model trénink na známé sadu dat umožňuje zobecnit pro predikci funkcí datové sady, na kterém je nebyla cvičena.  Běžná implementace použít zde je k rozdělení datové sady na tisíc složení a pak trénování modelu v kruhového dotazování na všechny kromě jednoho složení. Se hodnotí schopnost modelu, který má předpovědi přesně, při testování proti nezávislé datovou sadu v této násobek nejsou používány k trénování modelu.

**Optimalizace Hyperparameter** je tento problém vybrat sadu hyperparameters pro algoritmu učení, obvykle s cílem optimalizace měření výkonu algoritmus na nezávislé datové sady. **Hyperparameters** jsou hodnoty, které musí být zadán mimo proces školení modelu. Předpoklady o tyto hodnoty může ovlivnit flexibilitu a přesnosti modelu. Rozhodovací stromy mít hyperparameters, například jako je například požadovaný hloubkou a počet nechá ve stromové struktuře. Podpora vektoru počítače (SVMs) vyžadují, nastavení termín snížení chybnou klasifikaci. 

Běžný způsob provedení optimalizace hyperparameter použít zde je hledání mřížky nebo **oblouku parametr**. Tento postup se skládá provádět důkladné prohledání prostřednictvím hodnoty podmnožinu místo hyperparameter zadaný pro algoritmus učení. Křížového ověření může poskytovat metriky výkonu seřadit optimální výsledky vyprodukované vyhledávací algoritmus mřížky. Odchylka nákladů použít s obezřetností pomáhá hyperparameter limit problémy jako overfitting modelu pro Cvičná data tak, aby model uchovává kapacity, které chcete použít pro obecné sadu dat, ze kterého jste extrahovali Cvičná data.

Modely, které používáme zahrnují logistic a lineární regrese, náhodné doménové struktury a přechodu boosted stromů:

* [Lineární regrese s SGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) je model lineární regrese, který používá metodu Stochastického přechodu klesání (SGD) a škálování k předvídání objemy tip placené pro optimalizaci a funkce. 
* [Logistic regression s LBFGS](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) nebo "logit" regression je regresní model, který lze použít při kategorií udělat klasifikace dat je závislé proměnné. LBFGS je algoritmus optimalizace jako Newton, blíží algoritmus Broyden – Fletcher – Goldfarb – Shanno (BFGS) pomocí omezené množství paměti počítače a který se často používá v machine learning.
* [Náhodné doménových strukturách](http://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) jsou komplety rozhodovací stromy.  Že kombinují mnoho rozhodovacích stromů, aby se snížilo riziko overfitting. Náhodné doménovými strukturami se používají pro regresní a klasifikaci a dokáže zpracovat kategorií funkce a lze rozšířit pro nastavení více třídami klasifikace. Tyto nevyžadují funkce škálování a mohli zaznamenat nelineárností a funkci interakce. Náhodné doménových strukturách jsou jedním z těch nejúspěšnějších strojového učení modely pro klasifikaci a regrese.
* [Přechodu boosted stromy](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) jsou komplety rozhodovací stromy. GBTs cvičení stromů rozhodnutí interaktivně, aby se minimalizoval funkci ztrátu. GBTs se používají pro regresní a klasifikaci a dokáže zpracovat kategorií funkce, nevyžadují funkce škálování a mohli zaznamenat nelineárností a funkci interakce. Můžete také používají v nastavení multiclass klasifikace.

Modelování příklady použití odchylka nákladů a Hyperparameter oblouku se zobrazují pro problém binární klasifikace. Jednodušší příklady (bez parametru přesune) jsou uvedeny na hlavní téma pro regresní úlohy. Ale v příloze, jsou také uvedené ověření pomocí elastické net pro lineární regrese a odchylka nákladů pomocí parametru oblouku pro regresní náhodných doménové struktury. **Elastická net** je vyřešeno regrese metoda pro to lineárně hodí lineární regrese modely kombinuje metriky L1 a L2 jako postihy z [laso](https://en.wikipedia.org/wiki/Lasso%20%28statistics%29) a [ridge](https://en.wikipedia.org/wiki/Tikhonov_regularization) metody.   

> [!NOTE]
> I když toolkit Spark MLlib je navržen pro práci na velkých datových sad, relativně malé ukázkové (pomocí 170 tisíc řádků, přibližně 0,1 % původní datové sady NYC ~ 30 Mb) se zde používá ke zvýšení pohodlí. Cvičení zadané tady běží efektivně (v přibližně 10 minut) v clusteru HDInsight s 2 uzlů pracovního procesu. Stejný kód, s menšími změnami, můžete použít ke zpracování větší-sady dat, se změny, které pro ukládání do mezipaměti data v paměti a změna velikosti clusteru.
> 
> 

## <a name="setup-spark-clusters-and-notebooks"></a>Instalační program: Clustery Spark a poznámkových bloků
Kroky instalace a kódu jsou uvedené v tomto názorném postupu pro používání HDInsight Spark 1.6. Ale poznámkové bloky Jupyter jsou k dispozici pro clustery HDInsight Spark 1.6 a Spark 2.0. Popis poznámkových bloků a odkazy na ně jsou součástí [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) úložiště Githubu, které je obsahují. Kromě toho kód sem a v propojených poznámkových bloků je obecný a by měla fungovat v jakémkoliv clusteru Spark. Pokud nepoužíváte HDInsight Spark, může být mírně lišit od co je tady uvedené kroky nastavení a Správa clusteru. Pro větší pohodlí si zde jsou uvedeny odkazy na poznámkové bloky Jupyter pro Spark 1.6 a 2.0 ke spuštění v jádra pyspark Poznámkový blok Jupyter serveru:

### <a name="spark-16-notebooks"></a>Spark 1.6 poznámkových bloků

[pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): obsahuje témata v poznámkovém bloku #1 a modelu vývoj pomocí hyperparameter ladění a křížové ověření.

### <a name="spark-20-notebooks"></a>Spark 2.0 poznámkových bloků

[Spark2.0-pySpark3-Machine-Learning-data-Science-Spark-Advanced-data-Exploration-Modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Tento soubor obsahuje informace o tom, jak provést zkoumání dat, modelování a vyhodnocování clustery Spark 2.0.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

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
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";

    # PRINT START TIME
    import datetime
    datetime.datetime.now()

**OUTPUT**

datetime.datetime(2016, 4, 18, 17, 36, 27, 832799)

### <a name="import-libraries"></a>Importovat knihovny
Importujte knihovny potřebné následujícím kódem:

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


### <a name="preset-spark-context-and-pyspark-magics"></a>Předvolby kontextu Spark a Magic PySpark
Jádra PySpark, které jsou k dispozici s poznámkovými bloky Jupyter mít přednastavené kontextu. Proto není potřeba nastavit Spark nebo vývoji Hive kontexty explicitně před zahájením práce s aplikací. Tyto kontexty jsou dostupné ve výchozím nastavení. Tyto kontexty jsou:

* sc - pro Spark 
* sqlContext - pro Hive

Poskytuje jádra PySpark některé předdefinované "Magic", které jsou speciální příkazy, které můžete volat s %%. Existují dva takové příkazy, které se používají v tyto ukázky kódu.

* **%% místní** Určuje, že kód v dalších řádcích se má provádět místně. Kód musí být platný kód Python.
* **%% sql -o <variable name>**  provede dotaz Hive proti sqlContext. Pokud je předán parametr -o, výsledek dotazu je uchován v %% lokální kontext Python jako Pandas DataFrame.

Pro další informace o jádrech pro poznámkové bloky Jupyter a předdefinovanou "magics", poskytují, najdete v části [jádra dostupná pro poznámkové bloky Jupyter s HDInsight Spark Linux clusterů v HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

## <a name="data-ingestion-from-public-blob"></a>Přijímání dat z veřejného objektu blob:
Prvním krokem v procesu vědecké účely dat je ingestují data, která má být analyzován ze zdrojů, kde se nachází na zkoumání dat a modelování prostředí. Toto prostředí je Spark v tomto návodu. Tato část obsahuje kód pro dokončení řadu úloh:

* ingestování vzorek dat modelovat
* čtení ve vstupní datové sady (uložený jako soubor TSV)
* formátování a vyčištění dat.
* vytvářet a ukládat do mezipaměti objektů (RDDs nebo datových rámců) v paměti
* Zaregistrujte se jako dočasné tabulky v kontextu SQL.

Zde je kód pro přijímat data.

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


**OUTPUT**

Doba k provedení výše buňky: 276.62 sekund

## <a name="data-exploration--visualization"></a>Zkoumání dat a vizualizaci
Jakmile data vstoupila v Spark, je dalším krokem v procesu vědecké účely dat získali lepší představu o dat přes zkoumání a vizualizace. V této části jsme zkontrolujte taxíkem dat pomocí dotazů SQL a vykreslení cílových proměnných a potenciální funkcí pro visual kontroly. Konkrétně jsme vykreslení četnost počty osobní v taxi cest, frekvenci tip objemu a jak se typy liší podle částka platby a typu.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Vykreslení histogram frekvencí počet osobní v ukázce taxíkem cest
Tento kód a následné fragmenty použijte k dotazování na ukázkové a místní magic k vykreslení dat SQL magic.

* **SQL magic (`%%sql`)** jádra PySpark HDInsight podporuje snadno vložené HiveQL dotazy proti sqlContext. (-O VARIABLE_NAME) argument potrvají výstup příkazu jazyka SQL jako Pandas DataFrame na serveru Jupyter. To znamená, že je k dispozici v místním režimu.
*  **`%%local` Magic** slouží ke spouštění kódu místně na serveru Jupyter, což je headnode clusteru HDInsight. Obvykle použijete, `%%local` magic po `%%sql -o` magic slouží ke spuštění dotazu. Parametr -o by zachovat výstup příkazu jazyka SQL místně. Pak se `%%local` magic aktivuje další sadu fragmenty kódu ke spouštění místně na výstupu dotazů SQL, který obsahuje místně trvalé. Výstup se automaticky vizualizuje po spuštění kódu.

Tento dotaz načte služebních cest podle počtu osobní. 

    # PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

    # SQL QUERY
    %%sql -q -o sqlResults
    SELECT passenger_count, COUNT(*) as trip_counts FROM taxi_train WHERE passenger_count > 0 and passenger_count < 7 GROUP BY passenger_count


Tento kód vytvoří místní data snímku z výstupu dotazu a ukazuje zeměpisný data. `%%local` Magic vytvoří místní rámce dat, `sqlResults`, který může být použit pro vykreslení s matplotlib. 

> [!NOTE]
> Tato PySpark magic se používá více než jednou. v tomto návodu. Pokud je velké množství dat, by měl ukázkové k vytvoření data rámce, který můžete začlenit do místní paměti.
> 
> 

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
    # CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
    sqlResults

Zde je kód k vykreslení služebních cest dle počtů osobní

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

**OUTPUT**

![Frekvence služebních cest podle počtu osobní](./media/spark-advanced-data-exploration-modeling/frequency-of-trips-by-passenger-count.png)

Můžete vybrat mezi několika různých typů vizualizace (tabulky, kruhový, řádku, oblasti nebo panelu) pomocí **typ** tlačítka nabídky v poznámkovém bloku. Vykreslení panelu se zobrazí tady.

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Vykreslení histogram tip objemy a jak se liší podle počtu a tarif objemy osobní tip velikost.
Pomocí příkazu jazyka SQL ukázková data...

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


Tuto buňku kód používá k vytvoření tři pozemků data dotazu SQL.

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

![Tip rozdělení částky](./media/spark-advanced-data-exploration-modeling/tip-amount-distribution.png)

![Tip velikost podle počtu osobní](./media/spark-advanced-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Tip velikost podle tarif velikost](./media/spark-advanced-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>Funkce analýzy, transformaci a data přípravy pro modelování
Tato část popisuje a poskytuje kód pro postupy, které slouží k přípravě dat pro použití v ML modelování. Ukazuje, jak provést následující úkoly:

* Vytvořte novou funkci tak, že dělení čas do přihrádek doba provozu
* Index a na horkou kódování kategorií funkce
* Vytváření objektů s popiskem bod pro vstup do funkce ML
* Vytvořit náhodné dílčí vzorkování dat a rozdělit ho na trénování a testování sad
* Funkce škálování
* Objekty mezipaměti v paměti

### <a name="create-a-new-feature-by-partitioning-traffic-times-into-bins"></a>Vytvořte novou funkci tak, že dělení časy provoz do přihrádek
Tento kód ukazuje postup vytvořte novou funkci tak, že dělení časy provoz do přihrádek a výsledné datové rámce v paměti do mezipaměti. Ukládání do mezipaměti vede k lepší doba provádění odolné distribuované datové sady (RDDs) a datových rámců použití opakovaně. Ano jsme mezipaměti RDDs a datové rámce v několika fázích v tomto návodu.

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

**OUTPUT**

126050

### <a name="index-and-one-hot-encode-categorical-features"></a>Index a jeden horkou kódování kategorií funkce
V této části ukazuje, jak index nebo kódování kategorií funkce pro vstup do funkce modelování. Modelování a předvídání funkce MLlib požadovat, aby se funkce s kategorií vstupní data indexované nebo kódovaný před použití. 

V závislosti na modelu budete muset index nebo zakódovat je různými způsoby. Například Logistic a lineární regrese modely vyžadují jeden horkou kódování, kde, například funkce s tří kategorií lze rozšířit na tři sloupce funkce, s každou obsahující 0 nebo 1 v závislosti na kategorii pozorování. Poskytuje MLlib [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) funkce udělat za provozu jeden kódování. Tato kodér mapuje sloupec popisek indexů ke sloupci binárního vektory, s maximálně jednu jeden – hodnotu. Toto kódování umožňuje algoritmy, které očekávají číselných hodnot funkce, jako je logistic regression, má být použita pro kategorií funkce.

Tady je kód pro index a kódování kategorií funkce:

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


**OUTPUT**

Doba k provedení nad buňku: 3.14 sekund

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Vytváření objektů s popiskem bod pro vstup do funkce ML
Tato část obsahuje kód, který ukazuje postup indexu kategorií textová data jako datový typ s popiskem bodu a jak zakódovat je. Připraví ji mohli použít pro trénování a testování MLlib logistic regression a jinými modely klasifikace. S popiskem bodu objekty jsou odolné distribuované datové sady (RDD) formátu způsobem, který je nutný jako vstupní data pro většinu ML algoritmy v MLlib. A [s názvem bez přípony bodu](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) je přidružen místní vektoru hustých nebo zhuštění, popisek nebo odpověď.

Tady je kód pro index a kódování textu funkce pro binární klasifikaci.

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
Tento kód vytvoří náhodné vzorky dat (25 % tady slouží). I když to není nutné v tomto příkladu kvůli překročení velikosti datové sady, ukážeme, jak můžete zkusit tady data. Pak víte, jak používat pro váš vlastní problém v případě potřeby. Po velká vzorky lze ušetřit čas významné při školení modelů. Další jsme rozdělit vzorku na školení část (v tomto poli 75 %) a testování částí (zde 25 %) pro použití v klasifikaci a regresní modelování.

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

**OUTPUT**

Doba k provedení výše buňky: 0.31 sekund

### <a name="feature-scaling"></a>Funkce škálování
Funkce škálování, známé taky jako data normalizaci zajistí, že funkce široce Celková uhrazená hodnotami není zadaný nadměrné naváží ve funkci cíle. Kód pro funkci škálování používá [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) funkce, které chcete odchylku jednotky škálování. Pochází od MLlib pro použití v lineární regrese s Stochastického přechodu klesání (SGD). SGD je Oblíbené algoritmus pro trénování širokou škálu jiných modelů strojového učení například Vyřešeno regresí nebo support vector počítače (SVM).   

> [!TIP]
> Našli jsme algoritmus LinearRegressionWithSGD být citlivé funkce škálování.   
> 
> 

Tady je kód, který škálování proměnné pro použití s regularized lineární SGD algoritmus.

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

**OUTPUT**

Doba k provedení výše buňky: 11.67 sekund

### <a name="cache-objects-in-memory"></a>Objekty mezipaměti v paměti
Ukládání do mezipaměti na vstupní data rámce objekty používá pro klasifikaci, regresi a škálovat funkce může snížit čas potřebný pro trénování a testování ML algoritmů.

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

**OUTPUT** 

Doba k provedení nad buňku: 0,13 sekund

## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>Předpovědět, zda je tip placené s modely binární klasifikace
Tato část uvádí, jak použít tři modely pro predikci úlohy binární klasifikace zda tip je placené taxíkem cesty. Modely uvedené jsou:

* Logistic regression 
* Náhodné doménové struktury
* Přechodu zvýšení skóre stromů

Každý model vytváření části kódu je rozdělená do kroků: 

1. **Model školení** dat pomocí jednu sadu parametrů
2. **Model vyhodnocení** na testovací datové sady s metriky
3. **Ukládání modelu** v objektu blob pro budoucí spotřeba

Ukážeme, jak to provést křížové ověření (odchylka nákladů) s parametrem komínů dvěma způsoby:

1. Pomocí **Obecné** vlastní kód, který lze použít s libovolným algoritmem v MLlib a libovolný parametr nastaví v algoritmu. 
2. Pomocí **pySpark CrossValidator kanálu funkce**. Všimněte si, že CrossValidator má několik omezení pro Spark 1.5.0: 
   
   * Modely kanálu nelze uložit nebo trvalá pro budoucí spotřeby.
   * Nelze použít pro každý parametr v modelu.
   * Nelze použít pro každý MLlib algoritmus.

### <a name="generic-cross-validation-and-hyperparameter-sweeping-used-with-the-logistic-regression-algorithm-for-binary-classification"></a>Obecná křížové ověření a hyperparameter (vymetání) komínů použít s logistic regresní algoritmus pro binární klasifikaci
Kód v této části ukazuje, jak pro trénování, hodnocení a uložit logistic regresní model s [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) který předpovídá, zda je tip zaplacení cesty v NYC taxíkem služební cestě a tarif datovou sadu. Cvičení modelu pomocí křížové ověření (odchylka nákladů) a hyperparameter (vymetání) komínů implementuje pomocí vlastní kód, který lze použít k některému z algoritmů učení v MLlib.   

> [!NOTE]
> Spuštění vlastního kódu odchylka nákladů může trvat několik minut.
> 
> 

**Trénování modelu logistic regression pomocí odchylka nákladů a hyperparameter (vymetání) komínů**

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


**OUTPUT**

Koeficienty: [0.0082065285375-0.0223675576104,-0.0183812028036, - 3.48124578069e-05-0.00247646947233,-0.00165897881503, 0.0675394837328,-0.111823113101,-0.324609912762,-0.204549780032,-1.36499216354, 0.591088507921,-0.664263411392,-1.00439726852, 3.46567827545,-3.51025855172,-0.0471341112232,-0.043521833294, 0.000243375810385, 0.054518719222]

Zachycení:-0.0111216486893

Doba k provedení výše buňky: 14.43 sekund

**Vyhodnocení modelu binární klasifikace s standardní metriky**

Kód v této části ukazuje, jak vyhodnotit proti testovací data sada, včetně vykreslení křivka ROC logistic regresní model.

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


**OUTPUT**

Oblasti v rámci PR = 0.985336538462

Oblasti v rámci ROC = 0.983383274312

Souhrnné statistiky

Přesnost = 0.984174341679

Odvolat = 0.984174341679

F1 Stanovení skóre = 0.984174341679

Doba k provedení výše buňky: 2.67 sekund

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


**OUTPUT**

![Křivka ROC logistic regression pro obecný přístup](./media/spark-advanced-data-exploration-modeling/logistic-regression-roc-curve.png)

**Zachovat modelu do objektu BLOB pro budoucí spotřeba**

Kód v této části ukazuje, jak uložit logistic regresní model pro používání.

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


**OUTPUT**

Doba k provedení výše buňky: 34.57 sekund

### <a name="use-mllibs-crossvalidator-pipeline-function-with-logistic-regression-elastic-regression-model"></a>Pomocí funkce kanálu CrossValidator na MLlib s modelem logistic regression (elastické regrese)
Kód v této části ukazuje, jak pro trénování, hodnocení a uložit logistic regresní model s [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) který předpovídá, zda je tip zaplacení cesty v NYC taxíkem služební cestě a tarif datovou sadu. Pomocí křížové ověření (odchylka nákladů) a hyperparameter (vymetání) komínů implementuje pomocí funkce MLlib CrossValidator kanálu pro odchylka nákladů pomocí parametru oblouku cvičení modelu.   

> [!NOTE]
> Spuštění tohoto kódu odchylka MLlib nákladů může trvat několik minut.
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

**OUTPUT**

Doba k provedení výše buňky: 107.98 sekund

**Vykreslení křivka ROC.**

*PredictionAndLabelsDF* je zaregistrován jako tabulku, *tmp_results*, v předchozí buňku. *tmp_results* slouží k proveďte dotazy a výstup výsledků do sqlResults dat rámce pro vykreslení. Zde je kód.

    # QUERY RESULTS
    %%sql -q -o sqlResults
    SELECT label, prediction, probability from tmp_results

Tady je kód k vykreslení křivka ROC.

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


**OUTPUT**

![Pomocí CrossValidator na MLlib křivka ROC logistic regression](./media/spark-advanced-data-exploration-modeling/mllib-crossvalidator-roc-curve.png)

### <a name="random-forest-classification"></a>Klasifikace náhodných doménové struktury
Kód v této části ukazuje, jak pro trénování, hodnocení a uložit regrese náhodných doménové struktury, který předpovídá, zda je tip zaplacení cesty v NYC taxíkem služební cestě a tarif datovou sadu.

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


**OUTPUT**

Oblasti v rámci ROC = 0.985336538462

Doba k provedení výše buňky: 26.72 sekund

### <a name="gradient-boosting-trees-classification"></a>Přechodu zvýšení skóre klasifikace stromů
Kód v této části ukazuje, jak cvičení, vyhodnotit a uložte přechodu zvýšení skóre stromy model, který předpovídá, zda je pro cesty v cestě taxíkem NYC placené tip a jízdenky datovou sadu.

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

**OUTPUT**

Oblasti v rámci ROC = 0.985336538462

Doba k provedení výše buňky: 28.13 sekund

## <a name="predict-tip-amount-with-regression-models-not-using-cv"></a>Předpověď tip velikost s modely regrese (nepoužíváte odchylka nákladů)
Tato část ukazuje způsob použití, tři modely pro úlohu regrese: předpovědi tip velikost placené taxíkem cesty na základě jiných funkcí tip. Modely uvedené jsou:

* Vyřešeno lineární regrese
* Náhodné doménové struktury
* Přechodu zvýšení skóre stromů

Tyto modely byly popsané v úvodu. Každý model vytváření části kódu je rozdělená do kroků: 

1. **Model školení** dat pomocí jednu sadu parametrů
2. **Model vyhodnocení** na testovací datové sady s metriky
3. **Ukládání modelu** v objektu blob pro budoucí spotřeba   

> AZURE Poznámka: Křížové ověření není použít s modely tři Regrese v této části, vzhledem k tomu, že to se zobrazí v souvislosti s modelem logistic regression. Příkladem zobrazujícím postup používání odchylka nákladů s elastické Net pro lineární regrese najdete v příloze tohoto tématu.
> 
> AZURE Poznámka: V našich zkušeností, může být problémy s konvergence LinearRegressionWithSGD modelů a parametry musí být optimalizované pečlivě pro získání platný model jejich změnit. Škálování proměnných výrazně pomáhá s konvergence. Elastické net regrese uveden v dodatku k tomuto tématu, můžete použít také místo LinearRegressionWithSGD.
> 
> 

### <a name="linear-regression-with-sgd"></a>Lineární regrese s SGD
Kód v této části ukazuje, jak používat škálovat funkce ke cvičení lineární regrese, který stochastického přechodu klesání (SGD) používá pro optimalizaci a jak stanovení skóre, hodnocení a uložit model v Azure Blob Storage (WASB).

> [!TIP]
> V našich zkušeností může být problémy s konvergence LinearRegressionWithSGD modelů a parametry musí být optimalizované pečlivě pro získání platný model jejich změnit. Škálování proměnných výrazně pomáhá s konvergence.
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

**OUTPUT**

Coefficients: [0.0141707753435, -0.0252930927087, -0.0231442517137, 0.247070902996, 0.312544147152, 0.360296120645, 0.0122079566092, -0.00456498588241, -0.0898228505177, 0.0714046248793, 0.102171263868, 0.100022455632, -0.00289545676449, -0.00791124681938, 0.54396316518, -0.536293513569, 0.0119076553369, -0.0173039244582, 0.0119632796147, 0.00146764882502]

Zachytávat: 0.854507624459

RMSE = 1.23485131376

R sqr = 0.597963951127

Doba k provedení výše buňky: 38.62 sekund

### <a name="random-forest-regression"></a>Regrese náhodných doménové struktury
Kód v této části ukazuje, jak pro trénování, hodnocení a uložit model náhodných doménové struktury, který předpovídá velikost tip pro data NYC taxíkem cesty.   

> [!NOTE]
> Křížové ověření s parametrem komínů pomocí vlastního kódu najdete v příloze.
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

**OUTPUT**

RMSE = 0.931981967875

R-sqr = 0.733445485802

Doba k provedení výše buňky: 25.98 sekund

### <a name="gradient-boosting-trees-regression"></a>Přechodu zvýšení skóre regresní stromy
Kód v této části ukazuje, jak pro trénování, hodnocení a uložit přechodu zvýšení skóre stromy model, který předpovídá velikost tip pro data NYC taxíkem cesty.

** Natrénování a vyhodnocení **

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


**OUTPUT**

RMSE = 0.928172197114

R sqr = 0.732680354389

Doba k provedení výše buňky: 20.9 sekund

**Vykreslení.**

*tmp_results* je registrován jako tabulku Hive v předchozí buňku. Výsledky z tabulky se výstup do *sqlResults* dat rámce pro vykreslení. Zde je kód

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT * from tmp_results


Tady je kód k vykreslení data s využitím serveru Jupyter.

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

![Actual-vs-predicted-tip-amounts](./media/spark-advanced-data-exploration-modeling/actual-vs-predicted-tips.png)

## <a name="appendix-additional-regression-tasks-using-cross-validation-with-parameter-sweeps"></a>Dodatek: Další regresní úlohy křížového ověření pomocí parametru změny
Tento dodatek obsahuje kódu, který ukazuje, jak to provést pomocí elastické net pro lineární regrese odchylka nákladů a jak to provést odchylka nákladů s parametr oblouku pomocí vlastní kód pro regresní náhodných doménové struktury.

### <a name="cross-validation-using-elastic-net-for-linear-regression"></a>Křížové ověření pomocí Elastická net pro lineární regrese
Kód v této části ukazuje, jak křížové ověření pomocí elastické net pro lineární regrese a jak k vyhodnocení modelu pro testovací data.

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


**OUTPUT**

Doba k provedení výše buňky: 161.21 sekund

**Vyhodnocení s metrikou R SQR**

*tmp_results* je registrován jako tabulku Hive v předchozí buňku. Výsledky z tabulky se výstup do *sqlResults* dat rámce pro vykreslení. Zde je kód

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT label,prediction from tmp_results


Tady je kód R sqr vypočítat.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    from scipy import stats

    #R-SQR TEST METRIC
    corstats = stats.linregress(sqlResults['label'],sqlResults['prediction'])
    r2 = (corstats[2]*corstats[2])
    print("R-sqr = %s" % r2)


**OUTPUT**

R sqr = 0.619184907088

### <a name="cross-validation-with-parameter-sweep-using-custom-code-for-random-forest-regression"></a>Křížové ověření pomocí parametru oblouku pomocí vlastní kód pro regresní náhodných doménové struktury
Kód v této části ukazuje, jak křížové ověření pomocí parametru oblouku pomocí vlastní kód pro regresní náhodných doménové struktury a jak k vyhodnocení modelu pro testovací data.

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


**OUTPUT**

RMSE = 0.906972198262

R sqr = 0.740751197012

Doba k provedení výše buňky: 69.17 sekund

### <a name="clean-up-objects-from-memory-and-print-model-locations"></a>Vyčištění objektů z paměti a umístění tiskové modelu
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


**OUTPUT**

PythonRDD [122] v RDD v PythonRDD.scala: 43

** Výtisk cesta k souborům modelu, který se má použít v poznámkovém bloku spotřeby. ** Pokud chcete využívat a stanovení skóre nezávislé datové sady, musíte zkopírovat a vložit tyto názvy souborů v "poznámkového bloku spotřeby".

    # PRINT MODEL FILE LOCATIONS FOR CONSUMPTION
    print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
    print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
    print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
    print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
    print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
    print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";


**OUTPUT**

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05-0316_47_30.096528"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-05-0316_51_28.433670"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05-0316_50_17.454440"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05-0316_51_57.331730"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05-0316_50_40.138809"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05-0316_52_18.827237"

## <a name="whats-next"></a>Co dále?
Teď, když jste vytvořili regrese a klasifikace modely s Spark MlLib, jste připraveni se dozvíte, jak pro stanovení skóre a vyhodnocení těchto modelů.

**Model spotřeby:** další postup stanovení skóre a vyhodnocení modelů klasifikace a regrese vytvořené v tomto tématu najdete v tématu [skóre a vyhodnocení modelů learning vytvořené Spark počítač](spark-model-consumption.md).

