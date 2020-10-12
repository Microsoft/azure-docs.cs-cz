---
title: Pokročilé zkoumání a modelování dat pomocí procesu Spark-Team pro datové vědy
description: Pomocí HDInsight Sparku můžete zkoumat data a procházet binární klasifikace a regresní modely pomocí křížového ověřování a optimalizace pro parametry.
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
ms.openlocfilehash: c024b12210d408fe2a9987cba56a08e4b660ae1c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86027541"
---
# <a name="advanced-data-exploration-and-modeling-with-spark"></a>Pokročilé zkoumání a modelování dat pomocí Spark

V tomto návodu se používá HDInsight Spark k provádění zkoumání dat a výukového modelu binární klasifikace a regrese s využitím křížového ověřování a optimalizace pomocí parametrů pro ukázku NYC datové sady taxislužby TRIPS a tarif 2013. Provede vás jednotlivými kroky [procesu pro datové vědy](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)a koncovým účelem použití clusteru HDInsight Spark ke zpracování a objektů BLOB v Azure k ukládání dat a modelů. Tento proces zkoumá a vizualizuje data převedená z Azure Storage Blob a potom připraví data k sestavení prediktivních modelů. Python se použil k zakódování řešení a k zobrazení relevantních ploch. Tyto modely jsou sestavené pomocí sady nástrojů Spark MLlib k provádění binární klasifikace a regresní úlohy modelování. 

* V rámci úlohy **binární klasifikace** je předpovědět, zda je pro danou cestu placen Tip. 
* **Regresní** úlohou je předpovědět množství tipů na základě dalších funkcí Tip. 

Kroky modelování také obsahují kód, který ukazuje, jak vlakovat, vyhodnocovat a ukládat jednotlivé typy modelů. Téma popisuje některé ze stejných zemí jako [zkoumání a modelování dat pomocí Sparku](spark-data-exploration-modeling.md) . Je to ale více "rozšířené" v tom, že používá křížové ověřování s podparametrem, aby bylo možné vyškolit optimálně přesné klasifikace a regresní modely. 

**Křížové ověření (CV)** je technika, která posuzuje, jak dobře je model vyhodnocený na známé sadě dat generalizace pro předpověď funkcí datových sad, na kterých nebyla vyškolená.  Zde používaná společná implementace je rozdělit datovou sadu na skládání K pro a poté vytvořit model v kruhovém dotazování na všechny, ale jedno ze skládání. Možnost modelu pro předpověď přesně v případě testování proti nezávislé datové sadě v tomto skládání není použita ke školení modelu.

**Optimalizace pomocí parametrů** je problém s výběrem sady parametrů pro vzdělávací algoritmus, obvykle s cílem optimalizace míry výkonu algoritmu pro nezávislou datovou sadu. **Parametry** jsou hodnoty, které musí být zadány mimo postup školicího modelu. Předpoklady o těchto hodnotách mohou mít vliv na flexibilitu a přesnost modelů. Rozhodovací stromy mají například parametry, například požadovanou hloubku a počet listů ve stromu. Podpora vektorových počítačů (SVMs) vyžaduje nastavení termínu penalizace o netřídění. 

Běžný způsob, jak provést optimalizaci pomocí parametrů, je zde hledání mřížky nebo **parametr Sweep**. Toto hledání prochází podmnožinou prostoru s parametrem pro vzdělávací algoritmus. Vzájemné ověřování může poskytovat metriku výkonu pro seřazení optimálních výsledků vyprodukovaných algoritmem hledání mřížky. Hodnota CV použitá při rozmazání parametrů pomáhá omezit problémy, jako je třeba přebudování modelu na školicí data, aby model zachoval kapacitu, aby se použila na obecnou sadu dat, ze kterých se data školení extrahují.

Používané modely zahrnují logistické a lineární regrese, náhodné doménové struktury a provedené stromy s barevným přechodem:

* [Lineární regrese s SGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) je model lineární regrese, který používá metodu STOCHASTICKÉHO (SGD) a pro optimalizaci a škálování funkcí k předpovídání placených částek za špičku. 
* [Logistické regrese s LBFGS](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) nebo "logit" regresí je regresní model, který lze použít, když je závislá proměnná kategorií k provedení klasifikace dat. LBFGS je Broyden algoritmus pro optimalizaci v kvazi-Newton, který odpovídá algoritmu – Fletcher – Goldfarb-Shanno (BFGS) pomocí omezeného množství počítačové paměti a který se běžně používá ve službě Machine Learning.
* [Náhodné doménové struktury](https://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) jsou komplety rozhodovacích stromů.  Kombinují mnoho rozhodovacích stromů, aby se snížilo riziko přebudování. Náhodné doménové struktury se používají pro regresi a klasifikaci a mohou zpracovávat funkce kategorií a lze je rozšířit na nastavení třídy s více třídami. Nevyžadují škálování funkcí a můžou zachytit nelinearitu a interakce funkcí. Náhodné doménové struktury jsou jedním z nejčastěji úspěšných modelů strojového učení pro účely klasifikace a regrese.
* Probíhající se rozGBTSelné [stromy](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) () jsou komplety rozhodovacích stromů. GBTS rozhodovací stromy pro vlaky iterativním způsobem, aby se minimalizovala funkce ztráty. GBTS se používá pro regresi a klasifikaci a může zpracovávat funkce kategorií, nevyžadují škálování funkcí a dokáže zachytit nelinearitu a interakce funkcí. Lze je také použít v nastavení třídy s více třídami.

Pro problém binární klasifikace se zobrazí příklady modelování s použitím CV a Sweep parametrů. Jednodušší příklady (bez Sweep parametrů) jsou uvedeny v hlavním tématu pro regresní úkoly. V příloze ale je také k dispozici ověřování pomocí elastického netto pro lineární regresi a CV s parametrem Sweep s použitím náhodné regrese doménové struktury. **Elastická síť** je regulérní regresní metoda pro přizpůsobení modelů lineární regrese, které lineárně kombinuje metriky L1 a L2 jako pokuty metod [laso](https://en.wikipedia.org/wiki/Lasso%20%28statistics%29) a [Ridge](https://en.wikipedia.org/wiki/Tikhonov_regularization) .   

<!-- -->

> [!NOTE]
> I když je sada nástrojů Spark MLlib určená pro práci s velkými datovými sadami, pro pohodlí se zde používá poměrně malá ukázka (~ 30 MB pomocí 170K řádků, přibližně 0,1% původní datové sady NYC). Zde uvedené cvičení funguje efektivně (za přibližně 10 minut) v clusteru HDInsight se 2 pracovními uzly. Stejný kód, s menšími úpravami, lze použít ke zpracování větších datových sad s vhodnými úpravami pro ukládání dat do mezipaměti a změny velikosti clusteru.

<!-- -->

## <a name="setup-spark-clusters-and-notebooks"></a>Nastavení: Clustery Spark a poznámkové bloky
Kroky a kód pro instalaci najdete v tomto návodu pro použití HDInsight Spark 1,6. Ale Jupyter poznámkové bloky jsou k dispozici pro clustery HDInsight Spark 1,6 a Spark 2,0. Popis poznámkových bloků a odkazů jsou k dispozici v [Readme.MD](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) pro úložiště GitHubu, které je obsahuje. Kromě toho kód zde a v propojených poznámkových blocích je obecný a měl by fungovat na jakémkoli clusteru Spark. Pokud nepoužíváte HDInsight Spark, kroky pro instalaci a správu clusteru se můžou mírně lišit od toho, co se tady zobrazuje. Tady jsou odkazy na poznámkové bloky Jupyter pro Spark 1,6 a 2,0, které se mají spustit v jádru pyspark serveru Jupyter Notebook:

### <a name="spark-16-notebooks"></a>Notebooky Spark 1,6

[pySpark-Machine-Learning-data-věda-Spark-Advanced-data-prozkoumává se Modeling. ipynb](https://github.com/Azure-Samples/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): obsahuje témata v poznámkových blocích #1 a vývoj modelů pomocí ladění parametrů a křížového ověřování.

### <a name="spark-20-notebooks"></a>Notebooky Spark 2,0

[Spark 2.0 – pySpark3-Machine-Learning-data-věda-Spark-rozšířené-data-průzkum-Modeling. ipynb](https://github.com/Azure-Samples/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Tento soubor poskytuje informace o tom, jak provádět zkoumání dat, modelování a bodování v clusterech Spark 2,0.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Nastavení: umístění úložiště, knihovny a přednastavený kontext Spark
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
modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";

# PRINT START TIME
import datetime
datetime.datetime.now()
```

**VÝKONEM**

DateTime. DateTime (2016, 4, 18, 17, 36, 27, 832799)

### <a name="import-libraries"></a>Import knihoven
Importujte potřebné knihovny s následujícím kódem:

```python
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
```

### <a name="preset-spark-context-and-pyspark-magics"></a>Přednastavený kontext Sparku a PySpark Magic
Jádra PySpark, která jsou poskytována pomocí poznámkových bloků Jupyter, mají přednastavený kontext. Takže nemusíte explicitně nastavovat kontexty Sparku nebo podregistru, než začnete pracovat s aplikací, kterou vyvíjíte. Ve výchozím nastavení jsou tyto kontexty k dispozici pro vás. Tyto kontexty jsou:

* SC – pro Spark 
* Kontext SqlContext – podregistr

Jádro PySpark poskytuje některé předdefinované "MAGICS", což jsou speciální příkazy, které můžete volat pomocí%%. Existují dva takové příkazy, které jsou použity v těchto ukázkách kódu.

* **%% místní** Určuje, že kód v následných řádcích má být proveden místně. Kód musí být platný kód Pythonu.
* **%% SQL-o \<variable name> ** Spustí dotaz na podregistr pro kontext SqlContext. Pokud je předán parametr-o, výsledek dotazu je trvalý v kontextu%% Local Python jako PANDAS dataframe.

Další informace o jádrech pro Jupyter poznámkových blocích a předdefinovaných "Magic" najdete v tématu [jádra dostupná pro poznámkové bloky Jupyter s clustery HDInsight Spark Linux v HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

## <a name="data-ingestion-from-public-blob"></a>Přijímání dat z veřejného objektu BLOB:
Prvním krokem v rámci vědeckého zpracování dat je ingestování dat, která se mají analyzovat ze zdrojů, kde se nacházejí v prostředí pro zkoumání a modelování dat. Toto prostředí je Spark v tomto návodu. Tato část obsahuje kód pro dokončení řady úloh:

* ingestování ukázky dat pro modelování
* číst ve vstupní datové sadě (uložené jako soubor. TSV)
* formátování a vyčištění dat
* vytváření objektů a jejich ukládání do mezipaměti (RDD nebo datových rámců) v paměti
* Zaregistrujte ho jako dočasnou tabulku v kontextu SQL.

Zde je kód pro přijímání dat.

```python
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
```

**VÝKONEM**

Doba potřebná k provedení výše v buňce: 276,62 sekund

## <a name="data-exploration--visualization"></a>& vizualizace pro zkoumání dat
Až se data přenesou do Sparku, další krok v rámci vědeckého zpracování dat je získat hlubší porozumění datům prostřednictvím průzkumu a vizualizace. V této části prověříme taxislužby data pomocí dotazů SQL a vykreslíte cílové proměnné a funkce pro vizuální kontrolu. Konkrétně vykreslíme frekvenci počtu cestujících v taxislužby TRIPS, četnost částek v tipech a způsob, jakým se budou zobrazovat tipy podle množství a typu platby.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Sestrojit histogram frekvencí počtu cestujících v ukázce taxislužby cest
Tento kód a následné fragmenty kódu používají SQL Magic k dotazování na ukázku a místní Magic k vykreslení dat.

* **SQL Magic ( `%%sql` )** jádro HDInsight PySpark podporuje jednoduché vložené dotazy HiveQL proti kontext SqlContext. Argument (-o VARIABLE_NAME) uchovává výstup dotazu SQL jako PANDAS dataframe na serveru Jupyter. To znamená, že je k dispozici v místním režimu.
* ** `%%local` Magic** se používá ke spouštění kódu místně na serveru Jupyter, což je hlavnímu uzlu clusteru HDInsight. Obvykle použijete `%%local` Magic po `%%sql -o` použití Magic pro spuštění dotazu. Parametr-o by zachoval výstup dotazu SQL místně. Potom `%%local` Magic spustí další sadu fragmentů kódu, aby běžela lokálně na výstupu dotazů SQL, které byly trvale uložené v místním prostředí. Výstup je automaticky vizuálů po spuštění kódu.

Tento dotaz načte počet cest podle počtu cestujících. 

```sql
# PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

# SQL QUERY
%%sql -q -o sqlResults
SELECT passenger_count, COUNT(*) as trip_counts FROM taxi_train WHERE passenger_count > 0 and passenger_count < 7 GROUP BY passenger_count
```

Tento kód vytvoří lokální datový rámec z výstupu dotazu a vykreslí data. `%%local`Magic vytvoří lokální datový rámec, `sqlResults` který se dá použít k vykreslení pomocí matplotlib. 

<!-- -->

> [!NOTE]
> Tento PySpark Magic se v tomto průvodci používá několikrát. Pokud je objem dat velký, měli byste vzorkovat, abyste vytvořili rámec dat, který se může vejít do místní paměti.

<!-- -->

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
%%local

# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
# CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
sqlResults
```

Tady je kód pro sevykreslení cest podle počtu cestujících.

```python
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
```

**VÝKONEM**

![Četnost cest podle počtu cestujících](./media/spark-advanced-data-exploration-modeling/frequency-of-trips-by-passenger-count.png)

Pomocí tlačítek nabídky **typ** v poznámkovém bloku můžete vybrat mezi několika různými typy vizualizací (tabulka, výsečový, spojnicový, plošný nebo pruhový). Pruhový graf je zobrazen zde.

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Znázorněte histogramy o částkách špičky a o tom, jak se velikost hrotu mění podle počtu cestujících a částek tarifů.
Pro ukázková data použijte dotaz SQL..

```sql
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
```

Tato buňka s kódem používá k vytvoření tří dat dotaz SQL.

```python
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
```

**VÝKONEM** 

![Rozdělení částky hrotu](./media/spark-advanced-data-exploration-modeling/tip-amount-distribution.png)

![Částka tipu podle počtu cestujících](./media/spark-advanced-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Částka tipu podle částky tarifů](./media/spark-advanced-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>Technologie, transformace a Příprava dat pro modelování
Tato část popisuje a poskytuje kód pro postupy, které slouží k přípravě dat pro použití v modelování ML. Ukazuje, jak provádět následující úlohy:

* Vytvoření nové funkce rozdělením hodin na časové přihrádky provozu
* Index a on-Hot kategorií funkce kódování
* Vytvořit objekty s popiskem pro vstup do funkcí ML
* Vytvoření náhodného dílčího vzorkování dat a jejich rozdělení do sad pro školení a testování
* měření rozsahu prvků,
* Objekty mezipaměti v paměti

### <a name="create-a-new-feature-by-partitioning-traffic-times-into-bins"></a>Vytvoření nové funkce rozdělením doby provozu do přihrádek
Tento kód ukazuje, jak vytvořit novou funkci rozdělením doby provozu do přihrádek a následně do mezipaměti výsledný datový rámec v paměti. Ukládání do mezipaměti vede k lepší době spuštění, kdy se opakovaně používají odolné distribuované datové sady (RDD) a datové rámce. Proto ukládáme do mezipaměti RDD a datové rámce v několika fázích tohoto Názorného postupu.

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
```

```python
# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
# THE .COUNT() GOES THROUGH THE ENTIRE DATA-FRAME,
# MATERIALIZES IT IN MEMORY, AND GIVES THE COUNT OF ROWS.
taxi_df_train_with_newFeatures.cache()
taxi_df_train_with_newFeatures.count()
```

**VÝKONEM**

126050

### <a name="index-and-one-hot-encode-categorical-features"></a>Index a kategorií funkce kódování s jedním horkou funkcí
V této části se dozvíte, jak indexovat nebo kódovat funkce kategorií pro vstup do funkcí modelování. Funkce modelování a prediktivního MLlib vyžadují, aby byly funkce se vstupními daty kategorií před použitím indexovány nebo zakódované. 

V závislosti na modelu je třeba je indexovat nebo kódovat různými způsoby. Například logistické a lineární regresní modely vyžadují kódování s jedním aktivním umístěním, kde například funkce se třemi kategoriemi může být rozšířena na tři sloupce funkce, přičemž každý z nich obsahuje 0 nebo 1 v závislosti na kategorii sledování. MLlib poskytuje funkci [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) k provedení kódování s jedním aktivním systémem. Tento kodér mapuje sloupec indexů popisků na sloupec binárních vektorů s maximálně jednou jednou hodnotou. Toto kódování umožňuje použití algoritmů, které očekávají funkce s numerickou hodnotou, jako je například Logistická regrese, pro funkce kategorií.

Tady je kód pro indexování a kódování funkcí kategorií:

```python
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
```

**VÝKONEM**

Doba potřebná k provedení výše v buňce: 3,14 sekund

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Vytvořit objekty s popiskem pro vstup do funkcí ML
Tato část obsahuje kód, který ukazuje, jak indexovat textová data kategorií jako datový typ bodu s popiskem a jak je zakódovat. Tato transformace připraví textová data, která se mají použít ke školení a testování MLlib logistické regrese a dalších modelů klasifikace. Objekty bodu s popiskem jsou odolné distribuované datové sady (RDD) formátované způsobem, který je potřeba pro vstupní data z většiny algoritmů ML v MLlib. [Označený bod](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) je místní vektor, buď hustý, nebo zhuštěný, přidružený k popisku/odpovědi.

Zde je kód pro indexaci a kódování textových funkcí pro binární klasifikaci.

```python
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
Tento kód vytvoří náhodný vzorkování dat (tady se používá 25%). I když tento příklad není nutné z důvodu velikosti datové sady, ukážeme vám, jak můžete data vzorkovat. Pak víte, jak ho v případě potřeby použít pro vlastní problém. Když jsou ukázky velké, vzorkování může při školicích modelech ušetřit značný čas. Dále rozdělíme ukázku na školicí součást (75% zde) a část testování (25% tady), která se použije v modelování klasifikace a regrese.

```python
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
```

**VÝKONEM**

Doba potřebná k provedení výše uvedené buňky: 0,31 sekunda

### <a name="feature-scaling"></a>měření rozsahu prvků,
Škálování funkcí, označované také jako normalizace dat, zajišťuje, že funkce s rozšířenými vyplacenými hodnotami nejsou ve funkci cíl předány nadměrnému vážení. Kód pro škálování funkcí používá [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) ke škálování funkcí na odchylku jednotek. Je k dispozici v MLlib pro použití v lineární regresi s stochastického přechodem (SGD). SGD je oblíbený algoritmus pro školení široké škály dalších modelů strojového učení, jako jsou například obyčejné regrese nebo Podpora vektorových počítačů (SVM).   

> [!TIP]
> Našli jsme LinearRegressionWithSGD algoritmus, který bude citlivý na škálování funkcí.   
> 
> 

Zde je kód pro škálování proměnných pro použití s pravidelným lineárním SGD algoritmem.

```python
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

Doba potřebná k provedení výše v buňce: 11,67 sekund

### <a name="cache-objects-in-memory"></a>Objekty mezipaměti v paměti
Doba potřebná pro školení a testování algoritmů ML se dá snížit ukládáním objektů vstupních datových rámců, které se používají pro klasifikaci, regresi a funkce škálované.

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

Doba potřebná k provedení výše uvedené buňky: 0,13 sekunda

## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>Předpovědět, zda je u tohoto tipu placeny binární klasifikační modely
V této části se dozvíte, jak používat tři modely pro úlohu binární klasifikace s předpovídatm, jestli se pro taxislužbyou cestu neplatí Tip. Prezentované modely:

* Logistická regrese 
* Náhodná doménová struktura
* Rozvětvení barev ke zvýšení

Oddíl kódu sestavení každého modelu je rozdělen do kroků: 

1. **Model školení** dat pomocí jedné sady parametrů
2. **Vyhodnocení modelu** na testovací sadě dat s metrikami
3. **Ukládá se model** v objektu BLOB pro budoucí spotřebu.

Ukážeme, jak provést křížové ověření (CV) s parametrem pro mazání pomocí dvou způsobů:

1. Použití **obecného** vlastního kódu, který lze použít na libovolný algoritmus v MLlib a na jakékoli sady parametrů v algoritmu. 
2. Pomocí **funkce kanálu PySpark CrossValidator**. CrossValidator má několik omezení pro Spark 1.5.0: 
   
   * Modely kanálů nelze uložit ani uchovat pro budoucí spotřebu.
   * Nelze použít pro každý parametr v modelu.
   * Nelze použít pro každý MLlib algoritmus.

### <a name="generic-cross-validation-and-hyperparameter-sweeping-used-with-the-logistic-regression-algorithm-for-binary-classification"></a>Obecné vzájemné ověřování a rozmazání pomocí parametrů s využitím algoritmu logistické regrese pro binární klasifikaci
Kód v této části ukazuje, jak vytvořit model logistické regrese pomocí [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) , který předpovídá, jestli se u cesty NYC taxislužby Trip a datové sady tarifů hradí Tip. Model je vyučený pomocí vzájemného ověřování (CV) a překroužení parametrů, které se nasadí s vlastním kódem, který se dá použít pro jakýkoliv z MLlibch algoritmů výuky.   

<!-- -->

> [!NOTE]
> Provedení tohoto vlastního kódu CV může trvat několik minut.

<!-- -->

**Školení modelu logistické regrese s využitím CV a úklidu parametrů**

```python
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
```

**VÝKONEM**

Koeficienty: [0.0082065285375,-0.0223675576104,-0.0183812028036,-3.48124578069 e-05,-0.00247646947233,-0.00165897881503, 0.0675394837328,-0.111823113101,-0.324609912762,-0.204549780032,-1.36499216354, 0.591088507921,-0.664263411392,-1,00439726852, 3.46567827545,-3.51025855172,-0.0471341112232,-0.043521833294, 0.000243375810385, 0.054518719222]

Zachytit:-0.0111216486893

Doba potřebná k provedení výše v buňce: 14,43 sekund

**Vyhodnocení binárního klasifikačního modelu se standardními metrikami**

Kód v této části ukazuje, jak vyhodnotit model logistické regrese proti testovací sadě dat, včetně vykreslení křivky ROC.

```python
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
```

**VÝKONEM**

Oblast pod PR = 0.985336538462

Oblast pod ROC = 0.983383274312

Souhrnné statistiky

Precision = 0.984174341679

Odvolání = 0.984174341679

Skóre F1 = 0.984174341679

Doba potřebná k provedení výše v buňce: 2,67 sekund

**Znázorněte křivku ROC.**

*PredictionAndLabelsDF* je registrován jako tabulka, *tmp_results*v předchozí buňce. *tmp_results* lze použít k provádění dotazů a výstupních výsledků do sqlResults dat – rámec pro vykreslení. Zde je kód.

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
```

**VÝKONEM**

![Křivka logistické regresní ROC pro obecný přístup](./media/spark-advanced-data-exploration-modeling/logistic-regression-roc-curve.png)

**Zachovat model v objektu BLOB pro budoucí spotřebu**

Kód v této části ukazuje, jak uložit model logistické regrese pro spotřebu.

```python
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
```

**VÝKONEM**

Doba potřebná k provedení výše v buňce: 34,57 sekund

### <a name="use-mllibs-crossvalidator-pipeline-function-with-logistic-regression-elastic-regression-model"></a>Použití funkce kanálu CrossValidator pro MLlib s logistickou regresí (elastické regrese)
Kód v této části ukazuje, jak vytvořit model logistické regrese pomocí [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) , který předpovídá, jestli se u cesty NYC taxislužby Trip a datové sady tarifů hradí Tip. Model je vyučený pomocí funkce pro vzájemné ověření (CV) a Sweep, která je naimplementovaná pomocí funkce MLlib CrossValidator kanálu pro CV s vyčištěním parametrů.   

<!-- -->

> [!NOTE]
> Provedení tohoto kódu MLlib CV může trvat několik minut.

<!-- -->

```python
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
``` 

**VÝKONEM**

Doba potřebná k provedení výše v buňce: 107,98 sekund

**Znázorněte křivku ROC.**

*PredictionAndLabelsDF* je registrován jako tabulka, *tmp_results*v předchozí buňce. *tmp_results* lze použít k provádění dotazů a výstupních výsledků do sqlResults dat – rámec pro vykreslení. Zde je kód.

```python
# QUERY RESULTS
%%sql -q -o sqlResults
SELECT label, prediction, probability from tmp_results
```

Zde je kód pro vykreslení křivky ROC.

```python
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
```

**VÝKONEM**

![Logistická křivka ROC pomocí CrossValidatoru MLlib](./media/spark-advanced-data-exploration-modeling/mllib-crossvalidator-roc-curve.png)

### <a name="random-forest-classification"></a>Klasifikace náhodné doménové struktury
Kód v této části ukazuje, jak se naučit, vyhodnocovat a ukládat náhodné regrese doménové struktury, který předpovídá, jestli se pro cestu v datové sadě NYC taxislužby a datové sady tarifů vyplácí Tip.

```python
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
```

**VÝKONEM**

Oblast pod ROC = 0.985336538462

Doba potřebná k provedení výše v buňce: 26,72 sekund

### <a name="gradient-boosting-trees-classification"></a>Klasifikace pro zvýšení úrovně barev
Kód v této části ukazuje, jak se naučit, vyhodnocovat a ukládat model pro zesílení přechodů, který předpovídá, jestli se v datové sadě NYC taxislužby Trip a tarifs vyplatila špička pro cestu.

```python
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
```

**VÝKONEM**

Oblast pod ROC = 0.985336538462

Doba potřebná k provedení výše v buňce: 28,13 sekund

## <a name="predict-tip-amount-with-regression-models-not-using-cv"></a>Předpověď množství tipů pomocí regresních modelů (nepoužívá se CV)
V této části se dozvíte, jak používat tři modely pro regresní úlohu: předpověď částky špičky placené pro taxislužby trip na základě dalších funkcí Tip. Prezentované modely:

* Pravidelná lineární regrese
* Náhodná doménová struktura
* Rozvětvení barev ke zvýšení

Tyto modely byly popsány v úvodu. Oddíl kódu sestavení každého modelu je rozdělen do kroků: 

1. **Model školení** dat pomocí jedné sady parametrů
2. **Vyhodnocení modelu** na testovací sadě dat s metrikami
3. **Ukládá se model** v objektu BLOB pro budoucí spotřebu.   

<!-- -->

> [!NOTE] 
> Křížové ověřování se v této části nepoužívá se třemi regresními modely, protože se ukázalo podrobněji pro modely logistické regrese. Příklad, který ukazuje, jak použít CV s elastickou sítí pro lineární regresi, najdete v příloze tohoto tématu.

<!-- -->

<!-- -->

> [!NOTE] 
> V našem prostředí mohou nastat problémy se sbližováním modelů LinearRegressionWithSGD a parametry je nutné pečlivě změnit/optimalizovat pro získání platného modelu. Škálování proměnných významně pomáhá při sbližování. Flexibilní netto regrese zobrazená v dodatku k tomuto tématu se dá použít taky místo LinearRegressionWithSGD.

<!-- -->

### <a name="linear-regression-with-sgd"></a>Lineární regrese s SGD
Kód v této části ukazuje, jak používat škálované funkce k učení lineární regrese, která pro optimalizaci používá stochastického barevný sklon (SGD), a jak skóre, vyhodnotit a uložit model v Azure Blob Storage (WASB).

> [!TIP]
> V našem prostředí mohou nastat problémy se sbližováním modelů LinearRegressionWithSGD a parametry je nutné pečlivě změnit/optimalizovat pro získání platného modelu. Škálování proměnných významně pomáhá při sbližování.
> 
> 

```python
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
```

**VÝKONEM**

Koeficienty: [0.0141707753435,-0.0252930927087,-0.0231442517137, 0.247070902996, 0.312544147152, 0.360296120645, 0.0122079566092,-0.00456498588241,-0.0898228505177, 0.0714046248793, 0.102171263868, 0.100022455632,-0.00289545676449,-0.00791124681938, 0.54396316518,-0.536293513569, 0.0119076553369,-0.0173039244582, 0.0119632796147, 0.00146764882502]

Zachytit: 0.854507624459

RMSE = 1.23485131376

R-SQR = 0.597963951127

Doba potřebná k provedení výše v buňce: 38,62 sekund

### <a name="random-forest-regression"></a>Náhodná regrese doménové struktury
Kód v této části ukazuje, jak se naučit, vyhodnocovat a ukládat náhodný model doménové struktury, který předpovídá množství tipů pro data NYC taxislužby.   

<!-- -->

> [!NOTE]
> Křížové ověřování s parametry pro mazání pomocí vlastního kódu je k dispozici v dodatku.

<!-- -->

```python
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
```

**VÝKONEM**

RMSE = 0.931981967875

R-SQR = 0.733445485802

Doba potřebná k provedení výše v buňce: 25,98 sekund

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
```

**VÝKONEM**

RMSE = 0.928172197114

R-SQR = 0.732680354389

Doba potřebná k provedení výše v buňce: 20,9 sekund

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
```

![Skutečnost-vs-předpovězené – Tip – částky](./media/spark-advanced-data-exploration-modeling/actual-vs-predicted-tips.png)

## <a name="appendix-additional-regression-tasks-using-cross-validation-with-parameter-sweeps"></a>Příloha: další regresní úlohy s využitím vzájemného ověřování s využitím úklidu parametrů
V tomto dodatku najdete kód, který vám ukáže, jak pomocí elastického netto pro lineární regresi a pomocí vlastního kódu pro regresní regresi v náhodné doménové struktuře dělat CV s parametrem sweep.

### <a name="cross-validation-using-elastic-net-for-linear-regression"></a>Vzájemné ověřování pomocí elastického netto pro lineární regresi
Kód v této části ukazuje, jak provést vzájemné ověřování pomocí elastického netto pro lineární regresi a jak vyhodnotit model proti testovacím datům.

```python
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
```

**VÝKONEM**

Doba potřebná k provedení výše v buňce: 161,21 sekund

**Vyhodnotit pomocí R-SQR metriky**

*tmp_results* je zaregistrován jako tabulka podregistru v předchozí buňce. Výsledky z tabulky jsou ve výstupu *sqlResults* data-Frame pro vykreslování. Zde je kód

```python
# SELECT RESULTS
%%sql -q -o sqlResults
SELECT label,prediction from tmp_results
```

Zde je kód pro výpočet R-SQR.

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
from scipy import stats

#R-SQR TEST METRIC
corstats = stats.linregress(sqlResults['label'],sqlResults['prediction'])
r2 = (corstats[2]*corstats[2])
print("R-sqr = %s" % r2)
```

**VÝKONEM**

R-SQR = 0.619184907088

### <a name="cross-validation-with-parameter-sweep-using-custom-code-for-random-forest-regression"></a>Vzájemné ověřování pomocí úklidu parametrů pomocí vlastního kódu pro regresi náhodné doménové struktury
Kód v této části ukazuje, jak provést vzájemné ověřování pomocí úklidu parametrů pomocí vlastního kódu pro náhodnou regresi doménové struktury a jak vyhodnotit model proti testovacím datům.

```python
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
```

**VÝKONEM**

RMSE = 0.906972198262

R-SQR = 0.740751197012

Doba potřebná k provedení výše v buňce: 69,17 sekund

### <a name="clean-up-objects-from-memory-and-print-model-locations"></a>Vyčištění objektů z paměti a umístění modelů tisku
Slouží `unpersist()` k odstranění objektů uložených v mezipaměti v paměti.

```python
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
```

**VÝKONEM**

PythonRDD [122] v RDD na adrese PythonRDD. Scala: 43

* * Výstupní cesta k souborům modelů, které se mají použít v poznámkovém bloku spotřeby * * Pokud chcete použít a vyhodnotit nezávislou datovou sadu, musíte tyto názvy souborů zkopírovat a vložit do poznámkového bloku "spotřeba".

```python
# PRINT MODEL FILE LOCATIONS FOR CONSUMPTION
print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";
```

**VÝKONEM**

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05 -0316 _47_ 30.096528"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-05 -0316 _51_ 28.433670"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05 -0316 _50_ 17.454440"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05 -0316 _51_ 57.331730"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05 -0316 _50_ 40.138809"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05 -0316 _52_ 18.827237"

## <a name="whats-next"></a>Co dále?
Teď, když jste vytvořili regresní a klasifikační modely pomocí Spark MlLib, jste připraveni zjistit, jak tyto modely hodnotit a vyhodnocovat.

**Spotřeba modelu:** Informace o tom, jak hodnotit a hodnotit modely klasifikace a regrese vytvořené v tomto tématu, najdete v tématu [skóre a vyhodnocení modelů strojového učení s využitím Sparku](spark-model-consumption.md).

