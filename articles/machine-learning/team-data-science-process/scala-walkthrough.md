---
title: Datové vědy s využitím Scala a Sparku v Azure – proces vědeckého zpracování dat v týmu
description: Jak používat Scala pro dohled nad úkoly strojového učení s balíčky Spark Scalable MLlib a Spark ML na Azure HDInsight Sparkm clusteru.
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
ms.openlocfilehash: 9ae4549fe343422bbf60275a97768ca407f2dc7c
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321376"
---
# <a name="data-science-using-scala-and-spark-on-azure"></a>Vědecké zkoumání dat pomocí Scala a Spark v Azure
V tomto článku se dozvíte, jak používat Scala pro dohled nad úkoly strojového učení s balíčky Spark Scalable MLlib a Spark ML na Azure HDInsight Sparkm clusteru. Provede vás úkoly, které tvoří [proces vědeckého zpracování dat](./index.yml): přijímání a zkoumání dat, vizualizace, strojírenství funkcí, modelování a využití modelu. Mezi modely v tomto článku patří logistická a lineární regrese, náhodné doménové struktury a stromy se zesílenými GBTsmi, kromě dvou běžných úloh pod dohledem strojového učení:

* Problém regrese: předpověď míry špičky ($) pro cestu taxislužby
* Binární klasifikace: předpověď tipu nebo No Tip (1/0) pro taxislužby cestu

Proces modelování vyžaduje školení a vyhodnocení pro sadu testovacích dat a relevantní metriky přesnosti. V tomto článku se dozvíte, jak tyto modely ukládat do služby Azure Blob Storage a jak určit skóre a vyhodnotit jejich prediktivní výkon. Tento článek obsahuje taky pokročilejší témata o tom, jak optimalizovat modely pomocí křížového ověřování a úklidu Hyper-Parameter. Data, která se používají, jsou Ukázka datové sady 2013 NYC taxislužby pro cestu a tarify dostupné na GitHubu.

[Scala](https://www.scala-lang.org/)jazyk založený na virtuálním počítači Java integruje koncepty objektově orientovaného a funkčního jazyka. Jedná se o škálovatelný jazyk, který je vhodný pro distribuované zpracování v cloudu a běží na clusterech Azure Spark.

[Spark](https://spark.apache.org/) je open source platforma pro paralelní zpracování, která podporuje zpracování v paměti pro zvýšení výkonu aplikací pro analýzu velkých objemů dat. Modul pro zpracování Spark je založený na rychlosti, snadném použití a propracované analýze. Funkce distribuovaného výpočtu v paměti Sparku nabízí dobrou volbu pro iterativní algoritmy v rámci strojového učení a výpočtů grafů. Balíček [Spark.ml](https://spark.apache.org/docs/latest/ml-guide.html) poskytuje jednotnou sadu rozhraní API na nejvyšší úrovni postavených na datových snímcích, které vám pomůžou vytvořit a ladit praktické kanály strojového učení. [MLlib](https://spark.apache.org/mllib/) je škálovatelná knihovna strojového učení Sparku, která přináší možnosti modelování do tohoto distribuovaného prostředí.

[HDInsight Spark](../../hdinsight/spark/apache-spark-overview.md) je nabídka Azure hostovaná jako open source Spark. Zahrnuje také podporu pro notebooky Jupyter Scala v clusteru Spark a může spouštět interaktivní dotazy Spark SQL pro transformaci, filtrování a vizualizaci dat uložených v úložišti objektů BLOB v Azure. Fragmenty kódu Scala v tomto článku, které poskytují řešení a znázorňují relevantní zobrazení, aby bylo možné vizualizovat data spuštěná v poznámkových blocích Jupyter nainstalovaných v clusterech Spark. Postup modelování v těchto tématech obsahuje kód, který vám ukáže, jak vlakovat, vyhodnocovat, ukládat a spotřebovat jednotlivé typy modelů.

Postup nastavení a kód v tomto článku jsou pro Azure HDInsight 3,4 Spark 1,6. Kód v tomto článku a [Scala Jupyter notebook](https://github.com/Azure-Samples/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb) jsou ale obecné a měly by fungovat na jakémkoli clusteru Spark. Pokud nepoužíváte HDInsight Spark, můžou se kroky instalace a správy clusteru mírně lišit od toho, co se zobrazuje v tomto článku.

> [!NOTE]
> Téma, které vám ukáže, jak používat Python místo Scala k dokončení úloh pro kompletní proces zpracování dat, najdete v tématu věnovaném [datové vědy pomocí Sparku v Azure HDInsight](spark-overview.md).
> 
> 

## <a name="prerequisites"></a>Předpoklady
* Mít předplatné Azure. Pokud ho ještě nemáte, [Získejte bezplatnou zkušební verzi Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* K provedení následujících postupů potřebujete cluster Azure HDInsight 3,4 Spark 1,6. Pokud chcete vytvořit cluster, přečtěte si pokyny v tématu [Začínáme: vytvoření Apache Spark ve službě Azure HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). V nabídce **Vybrat typ clusteru** nastavte typ a verzi clusteru.

![Konfigurace typu clusteru HDInsight](./media/scala-walkthrough/spark-cluster-on-portal.png)

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
> 
> 

Popis údajů o cestách NYC taxislužby a pokyny, jak spustit kód z poznámkového bloku Jupyter v clusteru Spark, najdete v příslušných oddílech v tématu [Přehled vědeckého zpracování dat pomocí Sparku v Azure HDInsight](spark-overview.md).  

## <a name="execute-scala-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Spouštění kódu Scala z poznámkového bloku Jupyter na clusteru Spark
Z Azure Portal můžete spustit Poznámkový blok Jupyter. Na řídicím panelu najděte cluster Spark a kliknutím na něj zadejte stránku správy pro svůj cluster. V dalším kroku klikněte na **řídicí panely clusteru** a potom kliknutím na **Jupyter notebook** otevřete Poznámkový blok přidružený ke clusteru Spark.

![Řídicí panely clusteru a poznámkové bloky Jupyter](./media/scala-walkthrough/spark-jupyter-on-portal.png)

K poznámkovým blokům můžete získat přístup také v https:// &lt; název_clusteru &gt; . azurehdinsight.NET/Jupyter. Položku *název_clusteru* nahraďte názvem vašeho clusteru. Pro přístup k notebookům Jupyter potřebujete heslo pro účet správce.

![Přejít na poznámkové bloky Jupyter pomocí názvu clusteru](./media/scala-walkthrough/spark-jupyter-notebook.png)

Pokud chcete zobrazit adresář s několika příklady předbalených poznámkových bloků, které používají rozhraní PySpark API, vyberte **Scala** . Pomocí poznámkového bloku Scala. ipynb, který obsahuje ukázky kódu pro tuto sadu témat Spark, je k dispozici na [GitHubu](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Scala)modelování a vyhodnocování průzkumu.

Poznámkový blok můžete nahrát přímo z GitHubu do serveru Jupyter Notebook v clusteru Spark. Na domovské stránce Jupyter klikněte na tlačítko **nahrát** . V Průzkumníku souborů vložte adresu URL webu GitHub (RAW Content) do poznámkového bloku Scala a pak klikněte na **otevřít**. Poznámkový blok Scala je k dispozici na následující adrese URL:

[Průzkum – modelování a bodování – using-Scala. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb)

## <a name="setup-preset-spark-and-hive-contexts-spark-magics-and-spark-libraries"></a>Nastavení: přednastavené kontexty Sparku a podregistru, Spark Magic a knihovny Spark
### <a name="preset-spark-and-hive-contexts"></a>Přednastavené Spark a kontexty podregistru

```scala
# SET THE START TIME
import java.util.Calendar
val beginningTime = Calendar.getInstance().getTime()
```

Jádra Sparku, která jsou k dispozici s poznámkovým blokům Jupyter, mají přednastavené kontexty. Než začnete pracovat s aplikací, kterou vyvíjíte, nemusíte explicitně nastavovat kontexty Sparku nebo podregistru. Přednastavené kontexty jsou:

* `sc` pro SparkContext
* `sqlContext` pro HiveContext

### <a name="spark-magics"></a>Spark Magic
Jádro Sparku poskytuje některé předdefinované "Magic", což jsou speciální příkazy, které můžete volat pomocí `%%` . Dva z těchto příkazů jsou používány v následujících ukázkách kódu.

* `%%local` Určuje, že kód v následných řádcích bude spuštěn místně. Kód musí být platný Scala kód.
* `%%sql -o <variable name>` spustí dotaz na podregistr `sqlContext` . Pokud `-o` je předán parametr, výsledek dotazu je trvalý v `%%local` kontextu Scala jako datový rámec Spark.

Další informace o jádrech pro notebooky Jupyter a jejich předdefinovaných "Magic", která zavoláte `%%` (například `%%local` ), najdete v tématu [jádra dostupná pro poznámkové bloky Jupyter s clustery HDInsight Spark Linux v HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

### <a name="import-libraries"></a>Import knihoven
Naimportujte Spark, MLlib a další knihovny, které budete potřebovat, pomocí následujícího kódu.

```scala
# IMPORT SPARK AND JAVA LIBRARIES
import org.apache.spark.sql.SQLContext
import org.apache.spark.sql.functions._
import java.text.SimpleDateFormat
import java.util.Calendar
import sqlContext.implicits._
import org.apache.spark.sql.Row

# IMPORT SPARK SQL FUNCTIONS
import org.apache.spark.sql.types.{StructType, StructField, StringType, IntegerType, FloatType, DoubleType}
import org.apache.spark.sql.functions.rand

# IMPORT SPARK ML FUNCTIONS
import org.apache.spark.ml.Pipeline
import org.apache.spark.ml.feature.{StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer, Binarizer}
import org.apache.spark.ml.tuning.{ParamGridBuilder, TrainValidationSplit, CrossValidator}
import org.apache.spark.ml.regression.{LinearRegression, LinearRegressionModel, RandomForestRegressor, RandomForestRegressionModel, GBTRegressor, GBTRegressionModel}
import org.apache.spark.ml.classification.{LogisticRegression, LogisticRegressionModel, RandomForestClassifier, RandomForestClassificationModel, GBTClassifier, GBTClassificationModel}
import org.apache.spark.ml.evaluation.{BinaryClassificationEvaluator, RegressionEvaluator, MulticlassClassificationEvaluator}

# IMPORT SPARK MLLIB FUNCTIONS
import org.apache.spark.mllib.linalg.{Vector, Vectors}
import org.apache.spark.mllib.util.MLUtils
import org.apache.spark.mllib.classification.{LogisticRegressionWithLBFGS, LogisticRegressionModel}
import org.apache.spark.mllib.regression.{LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel}
import org.apache.spark.mllib.tree.{GradientBoostedTrees, RandomForest}
import org.apache.spark.mllib.tree.configuration.BoostingStrategy
import org.apache.spark.mllib.tree.model.{GradientBoostedTreesModel, RandomForestModel, Predict}
import org.apache.spark.mllib.evaluation.{BinaryClassificationMetrics, MulticlassMetrics, RegressionMetrics}

# SPECIFY SQLCONTEXT
val sqlContext = new SQLContext(sc)
```

## <a name="data-ingestion"></a>Přijímání dat
Prvním krokem v rámci vědeckého zpracování dat je ingestování dat, která chcete analyzovat. Data přenesete z externích zdrojů nebo systémů, ve kterých se nachází, do prostředí pro zkoumání a modelování dat. V tomto článku jsou data, která ingestují, spojená s 0,1% ukázkou taxislužby a souboru jízdné (uložený jako soubor. TSV). Prostředí pro zkoumání a modelování dat je Spark. Tato část obsahuje kód pro dokončení následující řady úloh:

1. Nastavte cesty k adresářům pro data a úložiště modelu.
2. Přečtěte si vstupní datovou sadu (uloženou jako soubor. TSV).
3. Definujte schéma pro data a vyčistěte data.
4. Vytvořte vyčištěný datový rámec a zapíšete ho do mezipaměti v paměti.
5. Zaregistrujte data jako dočasnou tabulku v kontext SqlContext.
6. Dotazování tabulky a Import výsledků do datového rámce.

### <a name="set-directory-paths-for-storage-locations-in-azure-blob-storage"></a>Nastavení cest k adresářům pro umístění úložiště ve službě Azure Blob Storage
Spark může číst a zapisovat do úložiště objektů BLOB v Azure. Spark můžete použít ke zpracování libovolných stávajících dat a pak výsledky uložit znovu v úložišti objektů BLOB.

Chcete-li uložit modely nebo soubory v úložišti objektů blob, je nutné správně zadat cestu. Vytvořte odkaz na výchozí kontejner připojený ke clusteru Spark pomocí cesty, která začíná `wasb:///` . Odkazování na jiná umístění pomocí `wasb://` .

Následující ukázka kódu určuje umístění vstupních dat, která mají být čtena, a cestu k úložišti objektů blob, který je připojen ke clusteru Spark, kde bude model uložen.

```scala
# SET PATHS TO DATA AND MODEL FILE LOCATIONS
# INGEST DATA AND SPECIFY HEADERS FOR COLUMNS
val taxi_train_file = sc.textFile("wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv")
val header = taxi_train_file.first;

# SET THE MODEL STORAGE DIRECTORY PATH
# NOTE THAT THE FINAL BACKSLASH IN THE PATH IS REQUIRED.
val modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";
```

### <a name="import-data-create-an-rdd-and-define-a-data-frame-according-to-the-schema"></a>Import dat, vytvoření RDD a definování datového rámce podle schématu

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# DEFINE THE SCHEMA BASED ON THE HEADER OF THE FILE
val sqlContext = new SQLContext(sc)
val taxi_schema = StructType(
    Array(
        StructField("medallion", StringType, true),
        StructField("hack_license", StringType, true),
        StructField("vendor_id", StringType, true),
        StructField("rate_code", DoubleType, true),
        StructField("store_and_fwd_flag", StringType, true),
        StructField("pickup_datetime", StringType, true),
        StructField("dropoff_datetime", StringType, true),
        StructField("pickup_hour", DoubleType, true),
        StructField("pickup_week", DoubleType, true),
        StructField("weekday", DoubleType, true),
        StructField("passenger_count", DoubleType, true),
        StructField("trip_time_in_secs", DoubleType, true),
        StructField("trip_distance", DoubleType, true),
        StructField("pickup_longitude", DoubleType, true),
        StructField("pickup_latitude", DoubleType, true),
        StructField("dropoff_longitude", DoubleType, true),
        StructField("dropoff_latitude", DoubleType, true),
        StructField("direct_distance", StringType, true),
        StructField("payment_type", StringType, true),
        StructField("fare_amount", DoubleType, true),
        StructField("surcharge", DoubleType, true),
        StructField("mta_tax", DoubleType, true),
        StructField("tip_amount", DoubleType, true),
        StructField("tolls_amount", DoubleType, true),
        StructField("total_amount", DoubleType, true),
        StructField("tipped", DoubleType, true),
        StructField("tip_class", DoubleType, true)
        )
    )

# CAST VARIABLES ACCORDING TO THE SCHEMA
val taxi_temp = (taxi_train_file.map(_.split("\t"))
                        .filter((r) => r(0) != "medallion")
                         .map(p => Row(p(0), p(1), p(2),
                            p(3).toDouble, p(4), p(5), p(6), p(7).toDouble, p(8).toDouble, p(9).toDouble, p(10).toDouble,
                            p(11).toDouble, p(12).toDouble, p(13).toDouble, p(14).toDouble, p(15).toDouble, p(16).toDouble,
                            p(17), p(18), p(19).toDouble, p(20).toDouble, p(21).toDouble, p(22).toDouble,
                            p(23).toDouble, p(24).toDouble, p(25).toDouble, p(26).toDouble)))


# CREATE AN INITIAL DATA FRAME AND DROP COLUMNS, AND THEN CREATE A CLEANED DATA FRAME BY FILTERING FOR UNWANTED VALUES OR OUTLIERS
val taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)

val taxi_df_train_cleaned = (taxi_train_df.drop(taxi_train_df.col("medallion"))
        .drop(taxi_train_df.col("hack_license")).drop(taxi_train_df.col("store_and_fwd_flag"))
        .drop(taxi_train_df.col("pickup_datetime")).drop(taxi_train_df.col("dropoff_datetime"))
        .drop(taxi_train_df.col("pickup_longitude")).drop(taxi_train_df.col("pickup_latitude"))
        .drop(taxi_train_df.col("dropoff_longitude")).drop(taxi_train_df.col("dropoff_latitude"))
        .drop(taxi_train_df.col("surcharge")).drop(taxi_train_df.col("mta_tax"))
        .drop(taxi_train_df.col("direct_distance")).drop(taxi_train_df.col("tolls_amount"))
        .drop(taxi_train_df.col("total_amount")).drop(taxi_train_df.col("tip_class"))
        .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200"));

# CACHE AND MATERIALIZE THE CLEANED DATA FRAME IN MEMORY
taxi_df_train_cleaned.cache()
taxi_df_train_cleaned.count()

# REGISTER THE DATA FRAME AS A TEMPORARY TABLE IN SQLCONTEXT
taxi_df_train_cleaned.registerTempTable("taxi_train")

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");
```

**Výkonem**

Čas, kdy se má buňka spustit: 8 sekund

### <a name="query-the-table-and-import-results-in-a-data-frame"></a>Dotazování tabulky a Import výsledků do datového rámce
Potom v tabulce Dotazujte data tarifů, osob a tipů. odfiltrovat poškozená a neležící data; a tisknout několik řádků.

```scala
# QUERY THE DATA
val sqlStatement = """
    SELECT fare_amount, passenger_count, tip_amount, tipped
    FROM taxi_train
    WHERE passenger_count > 0 AND passenger_count < 7
    AND fare_amount > 0 AND fare_amount < 200
    AND payment_type in ('CSH', 'CRD')
    AND tip_amount > 0 AND tip_amount < 25
"""
val sqlResultsDF = sqlContext.sql(sqlStatement)

# SHOW ONLY THE TOP THREE ROWS
sqlResultsDF.show(3)
```

**Výkonem**

| fare_amount | passenger_count | tip_amount | po obskládkované |
| --- | --- | --- | --- |
|        13,5 |1.0 |2.9 |1.0 |
|        16,0 |2.0 |3.4 |1.0 |
|        10,5 |2.0 |1.0 |1.0 |

## <a name="data-exploration-and-visualization"></a>Zkoumání a vizualizace dat
Po převedení dat do Sparku je dalším krokem v procesu zpracování dat, jak získat hlubší porozumění datům prostřednictvím průzkumu a vizualizace. V této části prohlížíte taxislužby data pomocí dotazů SQL. Pak importujte výsledky do datového rámce a vyznázorněte cílové proměnné a funkce pro vizuální kontrolu pomocí funkce Automatické vizualizace Jupyter.

### <a name="use-local-and-sql-magic-to-plot-data"></a>Použití místních a SQL Magic k vykreslení dat
Ve výchozím nastavení je výstup veškerého fragmentu kódu, který spustíte z Jupyter poznámkového bloku, dostupný v kontextu relace, která je trvalá na pracovních uzlech. Pokud chcete uložit cestu k pracovním uzlům pro každý výpočet a pokud jsou všechna data potřebná pro výpočet místně k dispozici v uzlu serveru Jupyter (který je hlavním uzlem), můžete použít `%%local` Magic a spustit fragment kódu na serveru Jupyter.

* **SQL Magic** ( `%%sql` ). Jádro HDInsight Spark podporuje jednoduché vložené dotazy HiveQL proti kontext SqlContext. Argument ( `-o VARIABLE_NAME` ) uchovává výstup dotazu SQL jako PANDAS datový rámec na serveru Jupyter. Toto nastavení znamená, že výstup bude k dispozici v místním režimu.
* `%%local`**Magic**. `%%local`Magic spustí kód místně na serveru Jupyter, což je hlavní uzel clusteru HDInsight. Obvykle používáte `%%local` Magic ve spojení s `%%sql` Magic s `-o` parametrem. `-o`Parametr by zachoval výstup dotazu SQL místně a potom `%%local` Magic spustí další sadu fragmentů kódu pro místní spuštění s výstupem dotazů SQL, které jsou trvale uložené v místním prostředí.

### <a name="query-the-data-by-using-sql"></a>Dotazování dat pomocí SQL
Tento dotaz načte taxislužby TRIPS podle množství jízdného, počtu spolujezdců a částky tipu.

```scala
# RUN THE SQL QUERY
%%sql -q -o sqlResults
SELECT fare_amount, passenger_count, tip_amount, tipped FROM taxi_train WHERE passenger_count > 0 AND passenger_count < 7 AND fare_amount > 0 AND fare_amount < 200 AND payment_type in ('CSH', 'CRD') AND tip_amount > 0 AND tip_amount < 25
```

V následujícím kódu `%%local` vytvoří Magic místní datový rámec sqlResults. SqlResults můžete použít k vykreslení pomocí matplotlib.

> [!TIP]
> Místní Magic se v tomto článku používá několikrát. Pokud je vaše datová sada rozsáhlá, vytvořte prosím v ukázce datový rámec, který se může vejít do místní paměti.
> 
> 

### <a name="plot-the-data"></a>Vykreslení dat
Můžete vykreslovat pomocí kódu Python po tom, co je datový rámec v místním kontextu jako PANDAS datový rámec.

```scala
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
%%local

# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES.
# CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, ETC.)
sqlResults
```

 Jádro Spark automaticky vizualizuje výstup dotazů SQL (HiveQL) po spuštění kódu. Můžete si vybrat z několika typů vizualizací:

* Tabulka
* Výsečový
* Spojnicový
* Plošný
* Pruhový

Zde je kód, který sekreslí data:

```scala
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
import matplotlib.pyplot as plt
%matplotlib inline

# PLOT TIP BY PAYMENT TYPE AND PASSENGER COUNT
ax1 = sqlResults[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
ax1.set_title('Tip amount distribution')
ax1.set_xlabel('Tip Amount ($)')
ax1.set_ylabel('Counts')
plt.suptitle('')
plt.show()

# PLOT TIP BY PASSENGER COUNT
ax2 = sqlResults.boxplot(column=['tip_amount'], by=['passenger_count'])
ax2.set_title('Tip amount by Passenger count')
ax2.set_xlabel('Passenger count')
ax2.set_ylabel('Tip Amount ($)')
plt.suptitle('')
plt.show()

# PLOT TIP AMOUNT BY FARE AMOUNT; SCALE POINTS BY PASSENGER COUNT
ax = sqlResults.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(sqlResults.passenger_count))
ax.set_title('Tip amount by Fare amount')
ax.set_xlabel('Fare Amount ($)')
ax.set_ylabel('Tip Amount ($)')
plt.axis([-2, 80, -2, 20])
plt.show()
```

**Výkonem**

![Histogram množství tipů](./media/scala-walkthrough/plot-tip-amount-histogram.png)

![Částka tipu podle počtu cestujících](./media/scala-walkthrough/plot-tip-amount-by-passenger-count.png)

![Částka tipu podle částky tarifů](./media/scala-walkthrough/plot-tip-amount-by-fare-amount.png)

## <a name="create-features-and-transform-features-and-then-prep-data-for-input-into-modeling-functions"></a>Vytváření funkcí a transformačních funkcí a Příprava dat pro vstup do modelovacích funkcí
Pro funkce modelování založené na stromové struktuře z Spark ML a MLlib je nutné připravit cíle a funkce pomocí různých technik, jako je například binningu, indexování, kódování One-Hot a vektory. Tady je postup, jak postupovat v této části:

1. Vytvořte novou funkci tím, že **binningu** hodiny do časových intervalů provozu.
2. Použijte **indexování a kódování One-Hot** k kategorií funkcím.
3. **Ukázková a rozdělená datová sada** do školicích a testovacích frakcí.
4. **Zadejte školicí proměnnou a funkce** a pak vytvořte indexované nebo jednosměrně zakódované školení a testování vstupních datových sad, které jsou označené jako "RDD", nebo datových rámců.
5. Automatické **kategorizace a vektorizovaná funkcí a cílů** pro použití jako vstupů pro modely strojového učení.

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Vytvoření nové funkce binningu hodinami v časových intervalech provozu
Tento kód ukazuje, jak vytvořit novou funkci tím, že binningu hodiny do časových intervalů provozu a jak ukládat výsledný datový rámec do mezipaměti v paměti. Místo toho, aby se RDD a datové rámce opakovaně používaly, zadává do mezipaměti zájem lepší doby spuštění. Proto v následujících postupech budete ukládat do mezipaměti RDD a datové snímky v několika fázích.

```scala
# CREATE FOUR BUCKETS FOR TRAFFIC TIMES
val sqlStatement = """
    SELECT *,
    CASE
     WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night"
     WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush"
     WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
     WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
    END as TrafficTimeBins
    FROM taxi_train
"""
val taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)

# CACHE THE DATA FRAME IN MEMORY AND MATERIALIZE THE DATA FRAME IN MEMORY
taxi_df_train_with_newFeatures.cache()
taxi_df_train_with_newFeatures.count()
```

### <a name="indexing-and-one-hot-encoding-of-categorical-features"></a>Indexování a kódování s jedním horkou funkcí kategorií
Funkce modelování a předpovědi MLlib vyžadují, aby byly funkce se vstupními daty kategorií indexované nebo kódované před použitím. V této části se dozvíte, jak indexovat nebo kódovat funkce kategorií pro vstup do funkcí modelování.

V závislosti na modelu musí být modely indexovány nebo kódovány různými způsoby. Například logistické a lineární regresní modely vyžadují kódování s jedním aktivním systémem. Například funkce se třemi kategoriemi se dá rozšířit na tři sloupce funkce. Každý sloupec by v závislosti na kategorii pozorování obsahoval hodnotu 0 nebo 1. MLlib poskytuje funkci [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) pro kódování s jedním aktivním příznakem. Tento kodér mapuje sloupec indexů popisků na sloupec binárních vektorů s maximálně jednou jednou hodnotou. S tímto kódováním je možné použít pro funkce kategorií algoritmy, které očekávají funkce s numerickou hodnotou, jako je například Logistická regrese.

Zde převedete pouze čtyři proměnné pro zobrazení příkladů, což jsou řetězce znaků. Můžete také indexovat další proměnné, jako je například den v týdnu, reprezentované číselnými hodnotami, jako kategorií proměnné.

Pro indexování, použití `StringIndexer()` a pro kódování One-Hot použijte `OneHotEncoder()` funkce z MLlib. Tady je kód pro indexování a kódování funkcí kategorií:

```scala
# CREATE INDEXES AND ONE-HOT ENCODED VECTORS FOR SEVERAL CATEGORICAL FEATURES

# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# INDEX AND ENCODE VENDOR_ID
val stringIndexer = new StringIndexer().setInputCol("vendor_id").setOutputCol("vendorIndex").fit(taxi_df_train_with_newFeatures)
val indexed = stringIndexer.transform(taxi_df_train_with_newFeatures)
val encoder = new OneHotEncoder().setInputCol("vendorIndex").setOutputCol("vendorVec")
val encoded1 = encoder.transform(indexed)

# INDEX AND ENCODE RATE_CODE
val stringIndexer = new StringIndexer().setInputCol("rate_code").setOutputCol("rateIndex").fit(encoded1)
val indexed = stringIndexer.transform(encoded1)
val encoder = new OneHotEncoder().setInputCol("rateIndex").setOutputCol("rateVec")
val encoded2 = encoder.transform(indexed)

# INDEX AND ENCODE PAYMENT_TYPE
val stringIndexer = new StringIndexer().setInputCol("payment_type").setOutputCol("paymentIndex").fit(encoded2)
val indexed = stringIndexer.transform(encoded2)
val encoder = new OneHotEncoder().setInputCol("paymentIndex").setOutputCol("paymentVec")
val encoded3 = encoder.transform(indexed)

# INDEX AND TRAFFIC TIME BINS
val stringIndexer = new StringIndexer().setInputCol("TrafficTimeBins").setOutputCol("TrafficTimeBinsIndex").fit(encoded3)
val indexed = stringIndexer.transform(encoded3)
val encoder = new OneHotEncoder().setInputCol("TrafficTimeBinsIndex").setOutputCol("TrafficTimeBinsVec")
val encodedFinal = encoder.transform(indexed)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");
```

**Výkonem**

Čas, kdy se má buňka spustit: 4 sekundy

### <a name="sample-and-split-the-data-set-into-training-and-test-fractions"></a>Ukázková a rozdělená datová sada do školicích a testovacích frakcí
Tento kód vytvoří náhodný odběr dat (25%, v tomto příkladu). I když vzorkování není pro tento příklad vyžadováno z důvodu velikosti datové sady, v článku se dozvíte, jak můžete vzorkovat, abyste věděli, jak ho používat pro vlastní problémy v případě potřeby. Když jsou ukázky velké, může to během výuky modelů ušetřit spoustu času. Dále rozdělte vzorek do školicí části (v tomto příkladu 75%, v tomto příkladu) a část testování (25%, v tomto příkladu), která se použije při klasifikaci a regresní modelování.

Přidejte náhodné číslo (mezi 0 a 1) do každého řádku (ve sloupci Rand), který se dá použít k výběru skládání křížového ověřování během školení.

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# SPECIFY SAMPLING AND SPLITTING FRACTIONS
val samplingFraction = 0.25;
val trainingFraction = 0.75;
val testingFraction = (1-trainingFraction);
val seed = 1234;
val encodedFinalSampledTmp = encodedFinal.sample(withReplacement = false, fraction = samplingFraction, seed = seed)
val sampledDFcount = encodedFinalSampledTmp.count().toInt

val generateRandomDouble = udf(() => {
    scala.util.Random.nextDouble
})

# ADD A RANDOM NUMBER FOR CROSS-VALIDATION
val encodedFinalSampled = encodedFinalSampledTmp.withColumn("rand", generateRandomDouble());

# SPLIT THE SAMPLED DATA FRAME INTO TRAIN AND TEST, WITH A RANDOM COLUMN ADDED FOR DOING CROSS-VALIDATION (SHOWN LATER)
# INCLUDE A RANDOM COLUMN FOR CREATING CROSS-VALIDATION FOLDS
val splits = encodedFinalSampled.randomSplit(Array(trainingFraction, testingFraction), seed = seed)
val trainData = splits(0)
val testData = splits(1)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");
```

**Výkonem**

Čas, kdy se má buňka spustit: 2 sekundy

### <a name="specify-training-variable-and-features-and-then-create-indexed-or-one-hot-encoded-training-and-testing-input-labeled-point-rdds-or-data-frames"></a>Zadejte proměnnou a funkce pro školení a pak vytvořte indexované nebo jednosměrně zakódované školení a testování vstupních RDD bodů nebo datových snímků s popiskem.
Tato část obsahuje kód, který ukazuje, jak indexovat kategorií textová data jako datový typ bodu s popisky a zakódovat je, abyste je mohli použít ke školení a testování MLlib logistické regrese a dalších modelů klasifikace. Objekty bodu s popiskem jsou RDD formátované způsobem, který je potřeba pro vstupní data z většiny algoritmů strojového učení v MLlib. [Označený bod](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) je místní vektor, buď hustý, nebo zhuštěný, přidružený k popisku/odpovědi.

V tomto kódu určíte cílovou (závislou) proměnnou a funkce, které se mají použít k učení modelů. Pak vytvoříte indexované nebo jednosměrně zakódované školení a testování vstupních RDD bodových bloků a datových rámců.

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# MAP NAMES OF FEATURES AND TARGETS FOR CLASSIFICATION AND REGRESSION PROBLEMS
val featuresIndOneHot = List("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))
val featuresIndIndex = List("paymentIndex", "vendorIndex", "rateIndex", "TrafficTimeBinsIndex", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))

# SPECIFY THE TARGET FOR CLASSIFICATION ('tipped') AND REGRESSION ('tip_amount') PROBLEMS
val targetIndBinary = List("tipped").map(encodedFinalSampled.columns.indexOf(_))
val targetIndRegression = List("tip_amount").map(encodedFinalSampled.columns.indexOf(_))

# CREATE INDEXED LABELED POINT RDD OBJECTS
val indexedTRAINbinary = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
val indexedTESTbinary = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
val indexedTRAINreg = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
val indexedTESTreg = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))

# CREATE INDEXED DATA FRAMES THAT YOU CAN USE TO TRAIN BY USING SPARK ML FUNCTIONS
val indexedTRAINbinaryDF = indexedTRAINbinary.toDF()
val indexedTESTbinaryDF = indexedTESTbinary.toDF()
val indexedTRAINregDF = indexedTRAINreg.toDF()
val indexedTESTregDF = indexedTESTreg.toDF()

# CREATE ONE-HOT ENCODED (VECTORIZED) DATA FRAMES THAT YOU CAN USE TO TRAIN BY USING SPARK ML FUNCTIONS
val assemblerOneHot = new VectorAssembler().setInputCols(Array("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount")).setOutputCol("features")
val OneHotTRAIN = assemblerOneHot.transform(trainData)
val OneHotTEST = assemblerOneHot.transform(testData)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");
```

**Výkonem**

Čas, kdy se má buňka spustit: 4 sekundy

### <a name="automatically-categorize-and-vectorize-features-and-targets-to-use-as-inputs-for-machine-learning-models"></a>Automatické kategorizace a vektorizovaná funkcí a cílů pro použití jako vstupů pro modely strojového učení
Použijte Spark ML ke kategorizaci cíle a funkcí pro použití v modelovacích funkcích založených na stromu. Kód dokončí dvě úlohy:

* Vytvoří binární cíl pro klasifikaci přiřazením hodnoty 0 nebo 1 ke každému datovému bodu mezi 0 a 1 pomocí prahové hodnoty 0,5.
* Automaticky kategorizuje funkce. Pokud je počet různých číselných hodnot pro libovolnou funkci menší než 32, je tato funkce zařazená do kategorií.

Zde je kód pro tyto dvě úlohy.

```scala
# CATEGORIZE FEATURES AND BINARIZE THE TARGET FOR THE BINARY CLASSIFICATION PROBLEM

# TRAIN DATA
val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
val indexerModel = indexer.fit(indexedTRAINbinaryDF)
val indexedTrainwithCatFeat = indexerModel.transform(indexedTRAINbinaryDF)
val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
val indexedTRAINwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)

# TEST DATA
val indexerModel = indexer.fit(indexedTESTbinaryDF)
val indexedTrainwithCatFeat = indexerModel.transform(indexedTESTbinaryDF)
val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
val indexedTESTwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)

# CATEGORIZE FEATURES FOR THE REGRESSION PROBLEM
# CREATE PROPERLY INDEXED AND CATEGORIZED DATA FRAMES FOR TREE-BASED MODELS

# TRAIN DATA
val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
val indexerModel = indexer.fit(indexedTRAINregDF)
val indexedTRAINwithCatFeat = indexerModel.transform(indexedTRAINregDF)

# TEST DATA
val indexerModel = indexer.fit(indexedTESTbinaryDF)
val indexedTESTwithCatFeat = indexerModel.transform(indexedTESTregDF)
```


## <a name="binary-classification-model-predict-whether-a-tip-should-be-paid"></a>Model binární klasifikace: předpověď, zda má být vyplacen Tip
V této části vytvoříte tři typy binárních klasifikačních modelů, které budou předpovídat, zda by měl být Tip zaplacen:

* **Model logistické regrese** s využitím funkce Spark ml `LogisticRegression()`
* **Model klasifikace náhodných doménových struktur** pomocí funkce Spark ml `RandomForestClassifier()`
* **Model klasifikace stromu se zesílením přechodu** pomocí `GradientBoostedTrees()` funkce MLlib

### <a name="create-a-logistic-regression-model"></a>Vytvoření modelu logistické regrese
Dále vytvořte model logistické regrese pomocí funkce Spark ML `LogisticRegression()` . Vytvoříte model vytváření kódu v řadě kroků:

1. **Analýza dat modelu** pomocí jedné sady parametrů.
2. **Vyhodnoťte model** pro sadu testovacích dat s metrikami.
3. **Uložte model** v úložišti objektů BLOB pro budoucí spotřebu.
4. Určení **skóre modelu** proti testovacím datům.
5. **Vyznázorněte výsledky** pomocí křivek s provozní charakteristikou (Roc).

Zde je kód pro tyto postupy:

```scala
# CREATE A LOGISTIC REGRESSION MODEL
val lr = new LogisticRegression().setLabelCol("tipped").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)
val lrModel = lr.fit(OneHotTRAIN)

# PREDICT ON THE TEST DATA SET
val predictions = lrModel.transform(OneHotTEST)

# SELECT `BinaryClassificationEvaluator()` TO COMPUTE THE TEST ERROR
val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
val ROC = evaluator.evaluate(predictions)
println("ROC on test data = " + ROC)

# SAVE THE MODEL
val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
val modelName = "LogisticRegression__"
val filename = modelDir.concat(modelName).concat(datestamp)
lrModel.save(filename);
```

Načtěte, skóre a uložte výsledky.

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# LOAD THE SAVED MODEL AND SCORE THE TEST DATA SET
val savedModel = org.apache.spark.ml.classification.LogisticRegressionModel.load(filename)
println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")

# SCORE THE MODEL ON THE TEST DATA
val predictions = savedModel.transform(OneHotTEST).select("tipped","probability","rawPrediction")
predictions.registerTempTable("testResults")

# SELECT `BinaryClassificationEvaluator()` TO COMPUTE THE TEST ERROR
val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
val ROC = evaluator.evaluate(predictions)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");

# PRINT THE ROC RESULTS
println("ROC on test data = " + ROC)
```

**Výkonem**

ROC na testovacích datech = 0.9827381497557599

K vykreslení křivky ROC použijte Python na místních PANDAS datových snímků.

```scala
# QUERY THE RESULTS
%%sql -q -o sqlResults
SELECT tipped, probability from testResults


# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
%matplotlib inline
from sklearn.metrics import roc_curve,auc

sqlResults['probFloat'] = sqlResults.apply(lambda row: row['probability'].values()[0][1], axis=1)
predictions_pddf = sqlResults[["tipped","probFloat"]]

# PREDICT THE ROC CURVE
# predictions_pddf = sqlResults.rename(columns={'_1': 'probability', 'tipped': 'label'})
prob = predictions_pddf["probFloat"]
fpr, tpr, thresholds = roc_curve(predictions_pddf['tipped'], prob, pos_label=1);
roc_auc = auc(fpr, tpr)

# PLOT THE ROC CURVE
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

**Výkonem**

![Tip nebo žádná křivka s tipem ROC](./media/scala-walkthrough/plot-roc-curve-tip-or-not.png)

### <a name="create-a-random-forest-classification-model"></a>Vytvoření modelu klasifikace s náhodnými doménovými strukturami
Dále vytvořte model klasifikace náhodných doménových struktur pomocí funkce Spark ML `RandomForestClassifier()` a pak vyhodnoťte model testovacích dat.

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# CREATE THE RANDOM FOREST CLASSIFIER MODEL
val rf = new RandomForestClassifier().setLabelCol("labelBin").setFeaturesCol("featuresCat").setNumTrees(10).setSeed(1234)

# FIT THE MODEL
val rfModel = rf.fit(indexedTRAINwithCatFeatBinTarget)
val predictions = rfModel.transform(indexedTESTwithCatFeatBinTarget)

# EVALUATE THE MODEL
val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
val Test_f1Score = evaluator.evaluate(predictions)
println("F1 score on test data: " + Test_f1Score);

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");

# CALCULATE BINARY CLASSIFICATION EVALUATION METRICS
val evaluator = new BinaryClassificationEvaluator().setLabelCol("label").setRawPredictionCol("probability").setMetricName("areaUnderROC")
val ROC = evaluator.evaluate(predictions)
println("ROC on test data = " + ROC)
```

**Výkonem**

ROC na testovacích datech = 0.9847103571552683

### <a name="create-a-gbt-classification-model"></a>Vytvoření modelu klasifikace GBT
Dále vytvořte model klasifikace GBT pomocí `GradientBoostedTrees()` funkce MLlib a pak vyhodnoťte model testovacích dat.

```scala
# TRAIN A GBT CLASSIFICATION MODEL BY USING MLLIB AND A LABELED POINT

# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# DEFINE THE GBT CLASSIFICATION MODEL
val boostingStrategy = BoostingStrategy.defaultParams("Classification")
boostingStrategy.numIterations = 20
boostingStrategy.treeStrategy.numClasses = 2
boostingStrategy.treeStrategy.maxDepth = 5
boostingStrategy.treeStrategy.categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))

# TRAIN THE MODEL
val gbtModel = GradientBoostedTrees.train(indexedTRAINbinary, boostingStrategy)

# SAVE THE MODEL IN BLOB STORAGE
val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
val modelName = "GBT_Classification__"
val filename = modelDir.concat(modelName).concat(datestamp)
gbtModel.save(sc, filename);

# EVALUATE THE MODEL ON TEST INSTANCES AND THE COMPUTE TEST ERROR
val labelAndPreds = indexedTESTbinary.map { point =>
  val prediction = gbtModel.predict(point.features)
  (point.label, prediction)
}
val testErr = labelAndPreds.filter(r => r._1 != r._2).count.toDouble / indexedTRAINbinary.count()
//println("Learned classification GBT model:\n" + gbtModel.toDebugString)
println("Test Error = " + testErr)

# USE BINARY AND MULTICLASS METRICS TO EVALUATE THE MODEL ON THE TEST DATA
val metrics = new MulticlassMetrics(labelAndPreds)
println(s"Precision: ${metrics.precision}")
println(s"Recall: ${metrics.recall}")
println(s"F1 Score: ${metrics.fMeasure}")

val metrics = new BinaryClassificationMetrics(labelAndPreds)
println(s"Area under PR curve: ${metrics.areaUnderPR}")
println(s"Area under ROC curve: ${metrics.areaUnderROC}")

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");

# PRINT THE ROC METRIC
println(s"Area under ROC curve: ${metrics.areaUnderROC}")
```

**Výkonem**

Oblast s křivkou ROC: 0.9846895479241554

## <a name="regression-model-predict-tip-amount"></a>Regresní model: předpověď hodnoty špičky
V této části vytvoříte dva typy regresních modelů pro předpověď velikosti tipu:

* **Pravidelný lineární regresní model** pomocí funkce Spark ml `LinearRegression()` . Tento model uložíte a vyhodnotí model testovacích dat.
* **Model regresního nárůstu přechodu** pomocí funkce Spark ml `GBTRegressor()` .

### <a name="create-a-regularized-linear-regression-model"></a>Vytvoření obyčejného modelu lineární regrese

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# CREATE A REGULARIZED LINEAR REGRESSION MODEL BY USING THE SPARK ML FUNCTION AND DATA FRAMES
val lr = new LinearRegression().setLabelCol("tip_amount").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)

# FIT THE MODEL BY USING DATA FRAMES
val lrModel = lr.fit(OneHotTRAIN)
println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")

# SUMMARIZE THE MODEL OVER THE TRAINING SET AND PRINT METRICS
val trainingSummary = lrModel.summary
println(s"numIterations: ${trainingSummary.totalIterations}")
println(s"objectiveHistory: ${trainingSummary.objectiveHistory.toList}")
trainingSummary.residuals.show()
println(s"RMSE: ${trainingSummary.rootMeanSquaredError}")
println(s"r2: ${trainingSummary.r2}")

# SAVE THE MODEL IN AZURE BLOB STORAGE
val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
val modelName = "LinearRegression__"
val filename = modelDir.concat(modelName).concat(datestamp)
lrModel.save(filename);

# PRINT THE COEFFICIENTS
println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")

# SCORE THE MODEL ON TEST DATA
val predictions = lrModel.transform(OneHotTEST)

# EVALUATE THE MODEL ON TEST DATA
val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
val r2 = evaluator.evaluate(predictions)
println("R-sqr on test data = " + r2)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");
```


**Výkonem**

Čas, kdy se má buňka spustit: 13 sekund

```scala
# LOAD A SAVED LINEAR REGRESSION MODEL FROM BLOB STORAGE AND SCORE A TEST DATA SET

# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# LOAD A SAVED LINEAR REGRESSION MODEL FROM AZURE BLOB STORAGE
val savedModel = org.apache.spark.ml.regression.LinearRegressionModel.load(filename)
println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")

# SCORE THE MODEL ON TEST DATA
val predictions = savedModel.transform(OneHotTEST).select("tip_amount","prediction")
predictions.registerTempTable("testResults")

# EVALUATE THE MODEL ON TEST DATA
val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
val r2 = evaluator.evaluate(predictions)
println("R-sqr on test data = " + r2)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");

# PRINT THE RESULTS
println("R-sqr on test data = " + r2)
```

**Výkonem**

R-SQR v testovacích datech = 0.5960320470835743

Pak Dotazujte výsledky testu jako datový rámec a k vizualizaci použijte AutoVizWidget a matplotlib.

```sql
# RUN A SQL QUERY
%%sql -q -o sqlResults
select * from testResults

# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
%%local

# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES
# CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, AND SO ON)
sqlResults
```

Kód vytvoří místní datový rámec z výstupu dotazu a vykreslí data. `%%local`Magic vytvoří místní datový rámec, `sqlResults` který můžete použít k vykreslení pomocí matplotlib.

> [!NOTE]
> Tento Spark Magic se používá několikrát v tomto článku. Pokud je objem dat velký, měli byste vzorkovat, abyste vytvořili datový rámec, který se může vejít do místní paměti.
> 
> 

Vytváření ploch pomocí Pythonu matplotlib.

```scala
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
sqlResults
%matplotlib inline
import numpy as np

# PLOT THE RESULTS
ax = sqlResults.plot(kind='scatter', figsize = (6,6), x='tip_amount', y='prediction', color='blue', alpha = 0.25, label='Actual vs. predicted');
fit = np.polyfit(sqlResults['tip_amount'], sqlResults['prediction'], deg=1)
ax.set_title('Actual vs. Predicted Tip Amounts ($)')
ax.set_xlabel("Actual")
ax.set_ylabel("Predicted")
#ax.plot(sqlResults['tip_amount'], fit[0] * sqlResults['prediction'] + fit[1], color='magenta')
plt.axis([-1, 15, -1, 8])
plt.show(ax)
```

**Výkonem**

![Hodnota tipu: skutečná oproti předpokládanému](./media/scala-walkthrough/plot-actual-vs-predicted-tip-amount.png)

### <a name="create-a-gbt-regression-model"></a>Vytvoření GBT regresního modelu
Vytvořte GBT regresní model pomocí funkce Spark ML `GBTRegressor()` a pak vyhodnoťte model testovacích dat.

RozGBTSné [stromy](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) () jsou kompletem rozhodovacích stromů. GBTS vlaky rozhodují iterativním způsobem, aby se minimalizovala funkce ztráty. GBTS můžete použít pro regresi a klasifikaci. Můžou zpracovávat funkce kategorií, nevyžadují škálování funkcí a můžou zachytit nelinearity a interakce funkcí. Můžete je také použít v nastavení klasifikace s více třídami.

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# TRAIN A GBT REGRESSION MODEL
val gbt = new GBTRegressor().setLabelCol("label").setFeaturesCol("featuresCat").setMaxIter(10)
val gbtModel = gbt.fit(indexedTRAINwithCatFeat)

# MAKE PREDICTIONS
val predictions = gbtModel.transform(indexedTESTwithCatFeat)

# COMPUTE TEST SET R2
val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
val Test_R2 = evaluator.evaluate(predictions)


# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");

# PRINT THE RESULTS
println("Test R-sqr is: " + Test_R2);
```

**Výkonem**

Test R-SQR je: 0.7655383534596654

## <a name="advanced-modeling-utilities-for-optimization"></a>Pokročilé nástroje pro modelování pro optimalizaci
V této části použijete nástroje strojového učení, které vývojáři často používají pro optimalizaci modelu. Konkrétně můžete optimalizovat modely strojového učení třemi různými způsoby pomocí mazání parametrů a křížového ověřování:

* Rozdělení dat na sady vlakových a ověřovacích sad, optimalizace modelu s použitím úklidu parametrů technologie Hyper-v sadě školení a vyhodnocení v sadě ověřování (lineární regrese)
* Optimalizujte model pomocí křížového ověřování a přechodu s použitím technologie Hyper-parametr pomocí funkce CrossValidator Spark ML (binární klasifikace).
* Optimalizujte model pomocí vlastního kódu pro křížové ověřování a nastavování parametrů pro použití libovolné funkce machine learningu a sady parametrů (lineární regrese).

**Křížové ověřování** je technika, která posuzuje, jak dobře je model vyhodnocený na známou sadu dat generalizuje, aby předpovídá funkce datových sad, na kterých nebyla vyškolená. Obecný nápad za tímto postupem je, že model je vyškolen na datové sadě známých dat a pak je přesnost jeho předpovědi testována na nezávislou datovou sadu. Společná implementace je rozdělit datovou sadu na skládání *k* --a poté vytvořit model v kruhovém dotazování pro všechny kromě jednoho ze skládání.

**Optimalizace parametrů technologie Hyper-** v je problém, který vybírá sadu Hyper-Parameters pro vzdělávací algoritmus, obvykle s cílem optimalizace míry výkonu algoritmu v nezávislé sadě dat. Parametr Hyper-v je hodnota, kterou je nutné zadat mimo postup při výuce modelu. Předpoklady týkající se hodnot parametrů technologie Hyper-v mohou ovlivnit flexibilitu a přesnost modelu. Rozhodovací stromy mají parametry typu Hyper-v, například požadovanou hloubku a počet listů ve stromu. Je nutné nastavit termín penalizace netřídění na počítač pro vektorový vektor (SVM).

Běžný způsob, jak provést optimalizaci pomocí technologie Hyper-v, je použít hledání v mřížce, které se označuje také jako **odsweep parametrů**. V hledání v mřížce je podrobné vyhledávání provedeno prostřednictvím hodnot zadané podmnožiny prostoru Hyper-parametru pro vzdělávací algoritmus. Křížové ověřování může poskytovat metriku výkonu pro řazení optimálních výsledků, které jsou vytvářené algoritmem hledání mřížky. Pokud používáte křížové ověřování s využitím automatického ověřování, můžete přispět k omezení problémů, jako je například přebudování modelu pro školení dat. Tímto způsobem model zachovává kapacitu, aby se použila na obecnou sadu dat, ze kterých byly extrahovány školicí data.

### <a name="optimize-a-linear-regression-model-with-hyper-parameter-sweeping"></a>Optimalizujte model lineární regrese s použitím úklidu parametrů Hyper-v
Dále můžete rozdělit data na sady vlakových a ověřovacích sad, použít pro optimalizaci modelu a vyhodnotit sadu ověření (lineární regresi) pomocí technologie Hyper-v.

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# RENAME `tip_amount` AS A LABEL
val OneHotTRAINLabeled = OneHotTRAIN.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
val OneHotTESTLabeled = OneHotTEST.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
OneHotTRAINLabeled.cache()
OneHotTESTLabeled.cache()

# DEFINE THE ESTIMATOR FUNCTION: `THE LinearRegression()` FUNCTION
val lr = new LinearRegression().setLabelCol("label").setFeaturesCol("features").setMaxIter(10)

# DEFINE THE PARAMETER GRID
val paramGrid = new ParamGridBuilder().addGrid(lr.regParam, Array(0.1, 0.01, 0.001)).addGrid(lr.fitIntercept).addGrid(lr.elasticNetParam, Array(0.1, 0.5, 0.9)).build()

# DEFINE THE PIPELINE WITH A TRAIN/TEST VALIDATION SPLIT (75% IN THE TRAINING SET), AND THEN THE SPECIFY ESTIMATOR, EVALUATOR, AND PARAMETER GRID
val trainPct = 0.75
val trainValidationSplit = new TrainValidationSplit().setEstimator(lr).setEvaluator(new RegressionEvaluator).setEstimatorParamMaps(paramGrid).setTrainRatio(trainPct)

# RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
val model = trainValidationSplit.fit(OneHotTRAINLabeled)

# MAKE PREDICTIONS ON THE TEST DATA BY USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORMS THE BEST
val testResults = model.transform(OneHotTESTLabeled).select("label", "prediction")

# COMPUTE TEST SET R2
val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
val Test_R2 = evaluator.evaluate(testResults)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");

println("Test R-sqr is: " + Test_R2);
```

**Výkonem**

Test R-SQR je: 0.6226484708501209

### <a name="optimize-the-binary-classification-model-by-using-cross-validation-and-hyper-parameter-sweeping"></a>Optimalizace binárního klasifikačního modelu pomocí křížového ověřování a úklidu Hyper-Parameter
V této části se dozvíte, jak optimalizovat model binární klasifikace pomocí křížového ověřování a úklidu Hyper-Parameter. Používá funkci Spark ML `CrossValidator` .

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# CREATE DATA FRAMES WITH PROPERLY LABELED COLUMNS TO USE WITH THE TRAIN AND TEST SPLIT
val indexedTRAINwithCatFeatBinTargetRF = indexedTRAINwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
val indexedTESTwithCatFeatBinTargetRF = indexedTESTwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
indexedTRAINwithCatFeatBinTargetRF.cache()
indexedTESTwithCatFeatBinTargetRF.cache()

# DEFINE THE ESTIMATOR FUNCTION
val rf = new RandomForestClassifier().setLabelCol("label").setFeaturesCol("features").setImpurity("gini").setSeed(1234).setFeatureSubsetStrategy("auto").setMaxBins(32)

# DEFINE THE PARAMETER GRID
val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(4,8)).addGrid(rf.numTrees, Array(5,10)).addGrid(rf.minInstancesPerNode, Array(100,300)).build()

# SPECIFY THE NUMBER OF FOLDS
val numFolds = 3

# DEFINE THE TRAIN/TEST VALIDATION SPLIT (75% IN THE TRAINING SET)
val CrossValidator = new CrossValidator().setEstimator(rf).setEvaluator(new BinaryClassificationEvaluator).setEstimatorParamMaps(paramGrid).setNumFolds(numFolds)

# RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
val model = CrossValidator.fit(indexedTRAINwithCatFeatBinTargetRF)

# MAKE PREDICTIONS ON THE TEST DATA BY USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORMS THE BEST
val testResults = model.transform(indexedTESTwithCatFeatBinTargetRF).select("label", "prediction")

# COMPUTE THE TEST F1 SCORE
val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
val Test_f1Score = evaluator.evaluate(testResults)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");
```

**Výkonem**

Čas, kdy se má buňka spustit: 33 sekund

### <a name="optimize-the-linear-regression-model-by-using-custom-cross-validation-and-parameter-sweeping-code"></a>Optimalizujte model lineární regrese pomocí vlastního kódu křížového ověřování a kódu pro mazání parametrů
Dále Optimalizujte model pomocí vlastního kódu a Identifikujte nejlepší parametry modelu pomocí kritéria nejvyšší přesnosti. Pak vytvořte finální model, vyhodnoťte model testovacích dat a uložte model do úložiště objektů BLOB. Nakonec načtěte model, data testu skóre a přesnost vyhodnoťte.

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# DEFINE THE PARAMETER GRID AND THE NUMBER OF FOLDS
val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(5,10)).addGrid(rf.numTrees, Array(10,25,50)).build()

val nFolds = 3
val numModels = paramGrid.size
val numParamsinGrid = 2

# SPECIFY THE NUMBER OF CATEGORIES FOR CATEGORICAL VARIABLES
val categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))

var maxDepth = -1
var numTrees = -1
var param = ""
var paramval = -1
var validateLB = -1.0
var validateUB = -1.0
val h = 1.0 / nFolds;
val RMSE  = Array.fill(numModels)(0.0)

# CREATE K-FOLDS
val splits = MLUtils.kFold(indexedTRAINbinary, numFolds = nFolds, seed=1234)


# LOOP THROUGH K-FOLDS AND THE PARAMETER GRID TO GET AND IDENTIFY THE BEST PARAMETER SET BY LEVEL OF ACCURACY
for (i <- 0 to (nFolds-1)) {
    validateLB = i * h
    validateUB = (i + 1) * h
    val validationCV = trainData.filter($"rand" >= validateLB  && $"rand" < validateUB)
    val trainCV = trainData.filter($"rand" < validateLB  || $"rand" >= validateUB)
    val validationLabPt = validationCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
    val trainCVLabPt = trainCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
    validationLabPt.cache()
    trainCVLabPt.cache()

    for (nParamSets <- 0 to (numModels-1)) {
        for (nParams <- 0 to (numParamsinGrid-1)) {
            param = paramGrid(nParamSets).toSeq(nParams).param.toString.split("__")(1)
            paramval = paramGrid(nParamSets).toSeq(nParams).value.toString.toInt
            if (param == "maxDepth") {maxDepth = paramval}
            if (param == "numTrees") {numTrees = paramval}
        }
        val rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                  numTrees=numTrees, maxDepth=maxDepth,
                                                  featureSubsetStrategy="auto",impurity="variance", maxBins=32)
        val labelAndPreds = validationLabPt.map { point =>
                                                 val prediction = rfModel.predict(point.features)
                                                  ( prediction, point.label )
                                                }
        val validMetrics = new RegressionMetrics(labelAndPreds)
        val rmse = validMetrics.rootMeanSquaredError
        RMSE(nParamSets) += rmse
    }
    validationLabPt.unpersist();
    trainCVLabPt.unpersist();
}
val minRMSEindex = RMSE.indexOf(RMSE.min)

# GET THE BEST PARAMETERS FROM A CROSS-VALIDATION AND PARAMETER SWEEP
var best_maxDepth = -1
var best_numTrees = -1
for (nParams <- 0 to (numParamsinGrid-1)) {
    param = paramGrid(minRMSEindex).toSeq(nParams).param.toString.split("__")(1)
    paramval = paramGrid(minRMSEindex).toSeq(nParams).value.toString.toInt
    if (param == "maxDepth") {best_maxDepth = paramval}
    if (param == "numTrees") {best_numTrees = paramval}
}

# CREATE THE BEST MODEL WITH THE BEST PARAMETERS AND A FULL TRAINING DATA SET
val best_rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                  numTrees=best_numTrees, maxDepth=best_maxDepth,
                                                  featureSubsetStrategy="auto",impurity="variance", maxBins=32)

# SAVE THE BEST RANDOM FOREST MODEL IN BLOB STORAGE
val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
val modelName = "BestCV_RF_Regression__"
val filename = modelDir.concat(modelName).concat(datestamp)
best_rfModel.save(sc, filename);

# PREDICT ON THE TRAINING SET WITH THE BEST MODEL AND THEN EVALUATE
val labelAndPreds = indexedTESTreg.map { point =>
                                        val prediction = best_rfModel.predict(point.features)
                                        ( prediction, point.label )
                                       }

val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
val test_rsqr = new RegressionMetrics(labelAndPreds).r2

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");


# LOAD THE MODEL
val savedRFModel = RandomForestModel.load(sc, filename)

val labelAndPreds = indexedTESTreg.map { point =>
                                        val prediction = savedRFModel.predict(point.features)
                                        ( prediction, point.label )
                                       }
# TEST THE MODEL
val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
val test_rsqr = new RegressionMetrics(labelAndPreds).r2
```

**Výkonem**

Čas, kdy se má buňka spustit: 61 sekund

## <a name="consume-spark-built-machine-learning-models-automatically-with-scala"></a>Automatické využívání modelů strojového učení pomocí Sparku s Scala
Přehled témat, která vás provedou úkoly, které se týkají procesu datové vědy v Azure, najdete v tématu věnovaném [vědeckému zpracování týmových dat](./index.yml).

[Návody pro vědecké zpracování týmových dat](walkthroughs.md) popisují další komplexní návody, které ukazují kroky v procesu vědeckého zpracování týmových dat pro konkrétní scénáře. Návody také ilustrují, jak sloučit cloudové a místní nástroje a služby do pracovního postupu nebo kanálu a vytvořit tak inteligentní aplikaci.

[Skóre Sparkem vytvořených modelů strojového učení](spark-model-consumption.md) se dozvíte, jak používat Scala kód k automatickému načítání a hodnocení nových datových sad pomocí modelů strojového učení sestavených v Sparku a uložených v úložišti objektů BLOB v Azure. Můžete postupovat podle zde uvedených pokynů a jednoduše nahradit kód Pythonu pomocí kódu Scala v tomto článku pro automatizovanou spotřebu.