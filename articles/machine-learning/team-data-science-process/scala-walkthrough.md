---
title: Datové vědy pomocí Scala a Spark v Azure – proces týmové datové vědy
description: Jak používat Scala pro úkoly strojového učení s dohledem s balíčky Spark scalable MLlib a Spark ML v clusteru Azure HDInsight Spark.
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
ms.openlocfilehash: b36a3faab49ee8d51c25aa18879e6f5d1db8c2fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76716759"
---
# <a name="data-science-using-scala-and-spark-on-azure"></a>Vědecké zkoumání dat pomocí Scala a Spark v Azure
Tento článek ukazuje, jak používat Scala pro úkoly strojového učení s dohledem s balíčky Spark scalable MLlib a Spark ML v clusteru Azure HDInsight Spark. Provede vás úkoly, které tvoří [proces datové vědy](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/): ingestování a zkoumání dat, vizualizace, inženýrské funkce, modelování a spotřeba modelu. Modely v článku zahrnují logistickou a lineární regresi, náhodné doménové struktury a stromy (GBTs) podpořené přechodem, kromě dvou běžných úkolů strojového učení pod dohledem:

* Regresní problém: Predikce výše hrotu ($) pro taxi výlet
* Binární klasifikace: Predikce špičky nebo bez špičky (1/0) pro taxi cestu

Proces modelování vyžaduje trénování a vyhodnocení na testovací datové sady a příslušné metriky přesnosti. V tomto článku se dozvíte, jak ukládat tyto modely v úložišti objektů Blob Azure a jak skóre a vyhodnotit jejich prediktivní výkon. Tento článek také popisuje pokročilejší témata, jak optimalizovat modely pomocí křížového ověřování a hyper-parametr zametání. Použitá data jsou ukázkou 2013 NYC taxi výlet a tarif datové sady k dispozici na GitHub.

[Scala](https://www.scala-lang.org/), jazyk založený na virtuálním stroji Java, integruje objektově orientované a funkční jazykové koncepty. Je to škálovatelný jazyk, který je vhodný pro distribuované zpracování v cloudu a běží na clusterech Azure Spark.

[Spark](https://spark.apache.org/) je open source paralelní zpracování framework, který podporuje zpracování v paměti pro zvýšení výkonu aplikací analýzy velkých objemů dat. Modul pro zpracování Spark je navržen pro rychlost, snadné použití a sofistikované analýzy. Díky možnostem distribuovaného výpočtu v paměti je Spark dobrou volbou pro iterativní algoritmy ve strojovém učení a výpočtech grafů. Balíček [spark.ml](https://spark.apache.org/docs/latest/ml-guide.html) poskytuje jednotnou sadu rozhraní API vysoké úrovně postavené na datových rámech, které vám pomohou vytvářet a ladit praktické kanály strojového učení. [MLlib](https://spark.apache.org/mllib/) je škálovatelná knihovna strojového učení sparku, která do tohoto distribuovaného prostředí přináší možnosti modelování.

[HDInsight Spark](../../hdinsight/spark/apache-spark-overview.md) je nabídka open source Spark hostovaná v Azure. Obsahuje také podporu pro poznámkové bloky Jupyter Scala v clusteru Spark a můžete spustit interaktivní dotazy Spark SQL pro transformaci, filtrování a vizualizaci dat uložených v úložišti objektů Blob Azure. Fragmenty kódu Scala v tomto článku, které poskytují řešení a zobrazit příslušné obrázky pro vizualizaci dat spustit v Poznámkových bloků Jupyter nainstalované v clusterech Spark. Kroky modelování v těchto tématech mají kód, který ukazuje, jak trénovat, vyhodnocovat, ukládat a využívat každý typ modelu.

Kroky nastavení a kód v tomto článku jsou pro Azure HDInsight 3.4 Spark 1.6. Kód v tomto článku a v [poznámkovém bloku Scala Jupyter](https://github.com/Azure-Samples/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb) jsou však obecné a měl y fungovat na libovolném clusteru Spark. Kroky nastavení a správy clusteru se mohou mírně lišit od toho, co je uvedeno v tomto článku, pokud nepoužíváte HDInsight Spark.

> [!NOTE]
> Téma, které ukazuje, jak používat Python místo Scala k dokončení úloh pro proces datové vědy od konce, najdete v [tématu Datové vědy pomocí Spark na Azure HDInsight](spark-overview.md).
> 
> 

## <a name="prerequisites"></a>Požadavky
* Mít předplatné Azure. Pokud ještě nemáte, [získejte bezplatnou zkušební verzi Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* K dokončení následujících postupů potřebujete cluster Azure HDInsight 3.4 Spark 1.6. Pokud chcete vytvořit cluster, přečtěte si pokyny v [tématu Začínáme: Vytvoření Apache Spark na Azure HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). V nabídce Vybrat typ clusteru nastavte typ a verzi **clusteru.**

![Konfigurace typu clusteru HDInsight](./media/scala-walkthrough/spark-cluster-on-portal.png)

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
> 
> 

Popis dat o cestě taxíkem nyc a pokyny, jak spustit kód z poznámkového bloku Jupyter v clusteru Spark, najdete v příslušných částech přehled [datových věd pomocí Spark na Azure HDInsight](spark-overview.md).  

## <a name="execute-scala-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Spuštění kódu Scala z poznámkového bloku Jupyter v clusteru Spark
Poznámkový blok Jupyter můžete spustit z webu Azure Portal. Najděte cluster Spark na řídicím panelu a kliknutím na něj přejděte na stránku správy pro váš cluster. Potom klikněte na **Řídicí panely clusteru**a potom kliknutím na **Jupyter Notebook** otevřete poznámkový blok přidružený ke clusteru Spark.

![Řídicí panel clusteru a poznámkové bloky Jupyter](./media/scala-walkthrough/spark-jupyter-on-portal.png)

Můžete také přistupovat k poznámkovým&lt;blokům&gt;Jupyter na https:// názvem cluster .azurehdinsight.net/jupyter. Nahraďte *název clusteru* názvem clusteru. Pro přístup k poznámkovým blokům Jupyter potřebujete heslo pro účet správce.

![Přejděte na poznámkové bloky Jupyter pomocí názvu clusteru](./media/scala-walkthrough/spark-jupyter-notebook.png)

Výběrem **scaly** zobrazíte adresář, který obsahuje několik příkladů předbalených poznámkových bloků, které používají rozhraní Api PySpark. Průzkum modelování a bodování pomocí Scala.ipynb notebook, který obsahuje ukázky kódu pro tuto sadu témat Spark je k dispozici na [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Scala).

Poznámkový blok můžete nahrát přímo z GitHubu na server Jupyter Notebook v clusteru Spark. Na domovské stránce Jupyter klikněte na tlačítko **Nahrát.** V průzkumníku souborů vložte adresu URL GitHubu (nezpracovaný obsah) poznámkového bloku Scala a klikněte na **Otevřít**. Poznámkový blok Scala je k dispozici na následující adrese URL:

[Průzkum-Modelování-a-Bodování-pomocí-Scala.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb)

## <a name="setup-preset-spark-and-hive-contexts-spark-magics-and-spark-libraries"></a>Nastavení: Přednastavené kontexty Spark a Hive, kouzla Spark a knihovny Spark
### <a name="preset-spark-and-hive-contexts"></a>Přednastavené kontexty Spark a Hive
    # SET THE START TIME
    import java.util.Calendar
    val beginningTime = Calendar.getInstance().getTime()


Jádra Spark, která jsou dodávána s notebooky Jupyter, mají přednastavené kontexty. Před zahájením práce s aplikací, kterou vyvíjíte, nemusíte explicitně nastavovat kontexty Spark nebo Hive. Přednastavené kontexty jsou:

* `sc`pro SparkContext
* `sqlContext`pro HiveContext

### <a name="spark-magics"></a>Jiskra magie
Jádro Spark poskytuje některé předdefinované "magie", což jsou speciální `%%`příkazy, které můžete volat s . Dva z těchto příkazů se používají v následujících ukázkách kódu.

* `%%local`určuje, že kód v následujících řádcích bude proveden místně. Kód musí být platný kód Scaly.
* `%%sql -o <variable name>`provede dotaz Hive `sqlContext`proti . Pokud `-o` je parametr předán, výsledek dotazu je `%%local` trvalý v kontextu Scala jako datový rámec Spark.

Další informace o jádrech notebooků Jupyter a jejich předdefinovaných "kouzlech", kterými `%%` voláte (například `%%local`), naleznete v [tématu Jádra dostupná pro notebooky Jupyter s clustery HDInsight Spark Linux na HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

### <a name="import-libraries"></a>Import knihoven
Importujte Spark, MLlib a další knihovny, které budete potřebovat, pomocí následujícího kódu.

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


## <a name="data-ingestion"></a>Přijímání dat
Prvním krokem v procesu datové vědy je ingestovat data, která chcete analyzovat. Data z externích zdrojů nebo systémů, kde jsou umístěna, můžete přenést do prostředí pro zkoumání a modelování dat. V tomto článku jsou data, která ingestujete, spojená 0,1 % ukázky souboru cesty taxíkem a tarifu (uloženého jako soubor TSV). Prostředí pro zkoumání a modelování dat je Spark. Tato část obsahuje kód k dokončení následující řady úkolů:

1. Nastavte cesty adresářů pro ukládání dat a modelu.
2. Čtení ve vstupní sadě dat (uložené jako soubor .tsv).
3. Definujte schéma pro data a vyčistěte data.
4. Vytvořte vyčištěný datový rámec a uvězte jej do mezipaměti.
5. Zaregistrujte data jako dočasnou tabulku v SQLContext.
6. Dotaz na tabulku a importovat výsledky do datového rámce.

### <a name="set-directory-paths-for-storage-locations-in-azure-blob-storage"></a>Nastavení cest k adresářům pro umístění úložišť v úložišti objektů Blob Azure
Spark může číst a zapisovat do úložiště objektů blob Azure. Spark můžete použít ke zpracování všech vašich existujících dat a pak znovu uložit výsledky do úložiště objektů Blob.

Chcete-li uložit modely nebo soubory v úložišti objektů Blob, musíte správně určit cestu. Odkazujte na výchozí kontejner připojený ke clusteru Spark `wasb:///`pomocí cesty, která začíná písmenem . Odkaz na jiná `wasb://`umístění pomocí .

Následující ukázka kódu určuje umístění vstupních dat, která mají být přečtena, a cestu k úložišti objektů Blob, která je připojena ke clusteru Spark, kde bude model uložen.

    # SET PATHS TO DATA AND MODEL FILE LOCATIONS
    # INGEST DATA AND SPECIFY HEADERS FOR COLUMNS
    val taxi_train_file = sc.textFile("wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv")
    val header = taxi_train_file.first;

    # SET THE MODEL STORAGE DIRECTORY PATH
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS REQUIRED.
    val modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";


### <a name="import-data-create-an-rdd-and-define-a-data-frame-according-to-the-schema"></a>Import dat, vytvoření RDD a definování datového rámce podle schématu
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


**Výstup:**

Čas spuštění buňky: 8 sekund.

### <a name="query-the-table-and-import-results-in-a-data-frame"></a>Dotaz na tabulku a import výsledků v datovém rámci
Dále se dotazte na tabulku pro údaje o tarifu, cestujícím a tipu; odfiltrovat poškozená a vzdálená data; a vytisknout několik řádků.

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

**Výstup:**

| fare_amount | passenger_count | tip_amount | Hrotem |
| --- | --- | --- | --- |
|        13.5 |1.0 |2.9 |1.0 |
|        16.0 |2.0 |3.4 |1.0 |
|        10,5 |2.0 |1.0 |1.0 |

## <a name="data-exploration-and-visualization"></a>Zkoumání a vizualizace dat
Po přenesli data do Spark, dalším krokem v procesu datové vědy je získat hlubší pochopení dat prostřednictvím průzkumu a vizualizace. V této části zkontrolujte data taxi pomocí dotazů SQL. Potom importujte výsledky do datového rámce a vykreslete cílové proměnné a perspektivní funkce pro vizuální kontrolu pomocí funkce Jupyter automatické vizualizace.

### <a name="use-local-and-sql-magic-to-plot-data"></a>Použití místních a SQL magie k vykreslení dat
Ve výchozím nastavení je výstup libovolného fragmentu kódu, který spustíte z poznámkového bloku Jupyter, k dispozici v kontextu relace, která je v pracovních uzlech zachována. Pokud chcete uložit výlet do pracovních uzlů pro každý výpočetní program a pokud jsou všechna data, která potřebujete pro váš výpočt, k dispozici místně na uzlu serveru `%%local` Jupyter (což je hlavní uzel), můžete použít magii ke spuštění fragmentu kódu na serveru Jupyter.

* **SQL** magie`%%sql`( ). Jádro HDInsight Spark podporuje snadné vložkové dotazy HiveQL proti SQLContext. Argument`-o VARIABLE_NAME`( ) zachová výstup dotazu SQL jako datový rámec Pandas na serveru Jupyter. Toto nastavení znamená, že výstup bude k dispozici v místním režimu.
* `%%local`**magie**. Kouzlo `%%local` spustí kód místně na serveru Jupyter, což je hlavní uzel clusteru HDInsight. Obvykle používáte `%%local` magii ve `%%sql` spojení `-o` s magií s parametrem. Parametr `-o` by zachovat výstup dotazu SQL místně `%%local` a pak magie by aktivovat další sadu fragment kódu spustit místně proti výstupu dotazů SQL, který je trvale místně.

### <a name="query-the-data-by-using-sql"></a>Dotaz na data pomocí SQL
Tento dotaz načte cesty taxíkem podle výše jízdného, počtu cestujících a částky spropitného.

    # RUN THE SQL QUERY
    %%sql -q -o sqlResults
    SELECT fare_amount, passenger_count, tip_amount, tipped FROM taxi_train WHERE passenger_count > 0 AND passenger_count < 7 AND fare_amount > 0 AND fare_amount < 200 AND payment_type in ('CSH', 'CRD') AND tip_amount > 0 AND tip_amount < 25

V následujícím kódu `%%local` magie vytvoří místní datový rámec, sqlResults. SqlResults můžete použít k vykreslení pomocí matplotlib.

> [!TIP]
> Místní magie se používá vícekrát v tomto článku. Pokud je vaše sada dat velká, ukažte ukázku a vytvořte datový rámec, který se vejde do místní paměti.
> 
> 

### <a name="plot-the-data"></a>Vykreslení dat
Můžete vykreslit pomocí kódu Pythonu po datovém rámci je v místním kontextu jako datový rámec Pandas.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES.
    # CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, ETC.)
    sqlResults


 Jádro Spark automaticky vizualizuje výstup dotazů SQL (HiveQL) po spuštění kódu. Můžete si vybrat z několika typů vizualizací:

* Table
* Výsečový
* Řádek
* Oblast
* Pruhový

Zde je kód pro vykreslení dat:

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


**Výstup:**

![Histogram množství hrotu](./media/scala-walkthrough/plot-tip-amount-histogram.png)

![Výše tipu podle počtu cestujících](./media/scala-walkthrough/plot-tip-amount-by-passenger-count.png)

![Částka tipu podle částky jízdného](./media/scala-walkthrough/plot-tip-amount-by-fare-amount.png)

## <a name="create-features-and-transform-features-and-then-prep-data-for-input-into-modeling-functions"></a>Vytváření funkcí a transformace prvků a příprava dat pro vstup do funkcí modelování
Pro funkce modelování založené na stromech od Spark ML a MLlib je nutné připravit cíl a funkce pomocí různých technik, jako je binning, indexování, kódování jedním horkým vstupem a vektorizace. Zde jsou postupy, které je třeba dodržovat v této části:

1. Vytvořte novou funkci **binning** hodin do dopravní doby kbelíky.
2. Použijte **indexování a jednoaktivní kódování** na kategorické prvky.
3. **Ukázka a rozdělení datové sady** do trénování a testovacích frakcí.
4. **Zadejte trénovací proměnnou a funkce**a potom vytvořte indexované nebo jednohorké kódování školení a testování vstupních bodů odolných distribuovaných datových sad (RDD) nebo datových rámců.
5. Automaticky **kategorizovat a vektorizovat funkce a cíle,** které chcete použít jako vstupy pro modely strojového učení.

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Vytvoření nové funkce binning hodin do dopravní doby kbelíky
Tento kód ukazuje, jak vytvořit novou funkci binning hodin do bloků času provozu a jak ukládat do mezipaměti výsledný datový rámec v paměti. Pokud se rdd a datové rámce používají opakovaně, ukládání do mezipaměti vede ke zlepšení doby provádění. V souladu s tím budete ukládat do mezipaměti rdd a datové rámce v několika fázích v následujících postupech.

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


### <a name="indexing-and-one-hot-encoding-of-categorical-features"></a>Indexování a jednoaktivní kódování kategorických funkcí
Funkce modelování a předvídání mllib vyžadují funkce s kategorickými vstupními daty, které mají být před použitím indexovány nebo kódovány. Tato část ukazuje, jak indexovat nebo kódovat kategorické funkce pro vstup do funkcí modelování.

V závislosti na modelu je třeba indexovat nebo kódovat modely různými způsoby. Například logistické a lineární regresní modely vyžadují kódování jedním horkým. Například funkci se třemi kategoriemi lze rozbalit do tří sloupců prvků. Každý sloupec by obsahoval 0 nebo 1 v závislosti na kategorii pozorování. MLlib poskytuje funkci [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) pro jednohorké kódování. Tento kodér mapuje sloupec indexů popisků na sloupec binárních vektorů s maximálně jednou jednou hodnotou. S tímto kódováním lze algoritmy, které očekávají číselné hodnotné funkce, jako je například logistická regrese, použít na kategorické funkce.

Zde transformujete pouze čtyři proměnné, aby se zobrazily příklady, což jsou řetězce znaků. Můžete také indexovat další proměnné, například den v týdnu, reprezentované číselnými hodnotami, jako kategorické proměnné.

Pro indexování, `StringIndexer()`použití a pro jednohorké `OneHotEncoder()` kódování použijte funkce z MLlib. Zde je kód pro indexování a kódování kategorických funkcí:

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


**Výstup:**

Čas spuštění buňky: 4 sekundy.

### <a name="sample-and-split-the-data-set-into-training-and-test-fractions"></a>Ukázka a rozdělení datové sady na trénovací a testovací frakce
Tento kód vytvoří náhodné vzorkování dat (25 %, v tomto příkladu). Přestože vzorkování není vyžadováno pro tento příklad z důvodu velikosti sady dat, článek ukazuje, jak můžete vzorkovat, abyste věděli, jak ji použít pro vlastní problémy v případě potřeby. Pokud jsou vzorky velké, může to ušetřit významný čas při trénování modelů. Dále rozdělte vzorek do trénovací části (75 %, v tomto příkladu) a testovací součást (25 %, v tomto příkladu) pro použití v klasifikaci a regresním modelování.

Přidejte náhodné číslo (mezi 0 a 1) do každého řádku (ve sloupci "rand"), které lze použít k výběru křížových ověřovacích záhybů během tréninku.

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


**Výstup:**

Čas spuštění buňky: 2 sekundy.

### <a name="specify-training-variable-and-features-and-then-create-indexed-or-one-hot-encoded-training-and-testing-input-labeled-point-rdds-or-data-frames"></a>Zadejte trénovací proměnnou a funkce a potom vytvořte indexované nebo jednohorké kódování školení a testování vstupních bodů RDD nebo datových rámců
Tato část obsahuje kód, který ukazuje, jak indexovat kategorická textová data jako datový typ označeného bodu a zakódovat je, abyste je mohli použít k trénování a testování logistické regrese MLlib a dalších klasifikačních modelů. Objekty s popiskem jsou RDD, které jsou formátovány způsobem, který je potřeba jako vstupní data většinou algoritmů strojového učení v MLlib. [Označený bod](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) je místní vektor, hustý nebo řídké, přidružený k popisku/odpovědi.

V tomto kódu zadáte cílovou (závislou) proměnnou a funkce, které se mají použít k trénování modelů. Potom vytvoříte indexované nebo jednohorké kódované školení a testování vstupních bodů RDD nebo datových rámců.

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


**Výstup:**

Čas spuštění buňky: 4 sekundy.

### <a name="automatically-categorize-and-vectorize-features-and-targets-to-use-as-inputs-for-machine-learning-models"></a>Automatické kategorizace a vektorizace funkcí a cílů, které se mají použít jako vstupy pro modely strojového učení
Spark ML slouží ke kategorizaci cíle a funkcí, které se mají používat ve stromových modelovacích funkcích. Kód dokončí dva úkoly:

* Vytvoří binární cíl pro klasifikaci přiřazením hodnoty 0 nebo 1 ke každému datovému bodu mezi 0 a 1 pomocí prahové hodnoty 0,5.
* Automaticky kategorizuje funkce. Pokud je počet různých číselných hodnot pro libovolnou funkci menší než 32, je tato funkce zařazena do kategorií.

Zde je kód pro tyto dva úkoly.

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



## <a name="binary-classification-model-predict-whether-a-tip-should-be-paid"></a>Binární klasifikační model: Předpovědět, zda má být zaplacen tip
V této části vytvoříte tři typy binárních klasifikačních modelů, které předpovídají, zda má být vyplacen tip:

* Model **logistické regrese** pomocí funkce `LogisticRegression()` Spark ML
* Náhodný **model klasifikace doménové** struktury `RandomForestClassifier()` pomocí funkce Spark ML
* Přechod **posílení stromu klasifikační** `GradientBoostedTrees()` model pomocí funkce MLlib

### <a name="create-a-logistic-regression-model"></a>Vytvoření logistického regresního modelu
Dále vytvořte model logistické regrese pomocí `LogisticRegression()` funkce Spark ML. Kód budovy modelu vytvoříte v řadě kroků:

1. **Trénování** dat modelu s jednou sadou parametrů.
2. **Vyhodnoťte model** v testovací datové sadě pomocí metrik.
3. **Uložte model** ve úložišti objektů Blob pro budoucí spotřebu.
4. **Skóre modelu** proti testovací data.
5. **Výsledky se vykreslují** s křivkami provozní charakteristiky přijímače (ROC).

Zde je kód pro tyto postupy:

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

Načtěte, skóre a uložte výsledky.

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


**Výstup:**

ROC na zkušební údaje = 0,98273814975575999

Použijte Python na místních datových rámcích Pandas k vykreslení křivky ROC.

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


**Výstup:**

![Špička nebo žádná křivka ROC špičky](./media/scala-walkthrough/plot-roc-curve-tip-or-not.png)

### <a name="create-a-random-forest-classification-model"></a>Vytvoření náhodného modelu klasifikace doménové struktury
Dále vytvořte náhodný model klasifikace doménové `RandomForestClassifier()` struktury pomocí funkce Spark ML a poté vyhodnotit model na testovacídata.

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


**Výstup:**

ROC na zkušebních datech = 0,9847103571552683

### <a name="create-a-gbt-classification-model"></a>Vytvoření klasifikačního modelu GBT
Dále vytvořte model klasifikace GBT pomocí `GradientBoostedTrees()` funkce MLlib a potom vyhodnotit model na testovací data.

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


**Výstup:**

Plocha pod křivkou ROC: 0,9846895479241554

## <a name="regression-model-predict-tip-amount"></a>Regresní model: Předpovědět částku špičky
V této části vytvoříte dva typy regresních modelů, které předpovídají množství špičky:

* Regularizovaný **lineární regresní model** pomocí `LinearRegression()` funkce Spark ML. Uložíte model a vyhodnotíte model na testovacích datech.
* **Model regrese stromu pro zvýšení přechodu** `GBTRegressor()` pomocí funkce Spark ML.

### <a name="create-a-regularized-linear-regression-model"></a>Vytvoření regularizovaného lineárního regresního modelu
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


**Výstup:**

Čas spuštění buňky: 13 sekund.

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


**Výstup:**

R-sqr na zkušebních datech = 0,5960320470835743

Dále dotaz výsledky testu jako datový rámec a použijte AutoVizWidget a matplotlib vizualizovat.

    # RUN A SQL QUERY
    %%sql -q -o sqlResults
    select * from testResults

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES
    # CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, AND SO ON)
    sqlResults

Kód vytvoří místní datový rámec z výstupu dotazu a vykreslí data. Kouzlo `%%local` vytvoří místní datový `sqlResults`rámec , který můžete použít k vykreslení pomocí matplotlib.

> [!NOTE]
> Tato magie Spark se používá vícekrát v tomto článku. Pokud je velké množství dat, měli byste vzorek vytvořit datový rámec, který se vejde do místní paměti.
> 
> 

Vytvořte parcely pomocí pythonu matplotlib.

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

**Výstup:**

![Částka tipu: Skutečnost vs. předpovídané](./media/scala-walkthrough/plot-actual-vs-predicted-tip-amount.png)

### <a name="create-a-gbt-regression-model"></a>Vytvoření regresního modelu GBT
Vytvořte regresní model GBT pomocí `GBTRegressor()` funkce Spark ML a pak model vyhodnoťte na testovacích datech.

[Gradient-posílil stromy](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTS) jsou soubory rozhodnutí stromů. GBTS vlaky rozhodnutí stromy iterativně minimalizovat ztrátu funkce. GbTS můžete použít pro regresi a klasifikaci. Mohou zpracovávat kategorické prvky, nevyžadují změnu měřítka prvků a mohou zachytit nelinearity a interakce prvků. Můžete je také použít v nastavení klasifikace více tříd.

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


**Výstup:**

Test R-sqr je: 0.7655383534596654

## <a name="advanced-modeling-utilities-for-optimization"></a>Pokročilé nástroje pro modelování pro optimalizaci
V této části použijete nástroje strojového učení, které vývojáři často používají pro optimalizaci modelu. Konkrétně můžete optimalizovat modely strojového učení třemi různými způsoby pomocí zametacího parametru a křížového ověřování:

* Rozdělit data do vlakových a ověřovacích sad, optimalizovat model pomocí hyperparametrizace na trénovací sadě a vyhodnotit na ověřovací sadě (lineární regrese)
* Optimalizujte model pomocí křížového ověřování a hyper-parametry zametání pomocí Spark ML crossvalidator funkce (binární klasifikace)
* Optimalizujte model pomocí vlastního kódu křížového ověřování a zametání parametrů pro použití libovolné funkce a parametru strojového učení (lineární regrese)

**Křížové ověření** je technika, která posuzuje, jak dobře model trénovaný na známé sadě dat zobecnit předpovědět funkce datových sad, na kterých nebyl vyškolen. Obecnou myšlenkou této techniky je, že model je trénovaný na datové sadě známých dat a pak je přesnost jeho předpovědi testovány proti nezávislé datové sady. Běžnou implementací je rozdělit *k*sadu dat do k-folds a pak trénovat model způsobem kruhového dotazování na všechny, kromě jednoho záhybů.

**Hyper-parametr optimalizace** je problém výběru sadu hyper-parametrů pro algoritmus učení, obvykle s cílem optimalizovat míru výkonu algoritmu na nezávislé datové sady. Hyper-parametr je hodnota, kterou je nutné zadat mimo postup školení modelu. Předpoklady o hodnotách hyperparametrů mohou ovlivnit flexibilitu a přesnost modelu. Rozhodovací stromy mají hyperparametry, například, jako je požadovaná hloubka a počet listů ve stromu. Je nutné nastavit termín chybné klasifikace penále pro zařízení pro vektorovou podporu (SVM).

Běžným způsobem provádění optimalizace hyperparametrů je použití vyhledávání v mřížce, nazývané také **zametání parametrů**. Při hledání v mřížce se provádí vyčerpávající vyhledávání prostřednictvím hodnot zadané podmnožiny hyperparametrizačního prostoru pro algoritmus učení. Křížové ověření může poskytnout metriku výkonu k vyřešení optimálních výsledků vytvořených algoritmem vyhledávání v mřížce. Pokud používáte křížové ověřování hyper-parametr zametání, můžete pomoci omezit problémy, jako je nadměrné přizpůsobení modelu trénovací data. Tímto způsobem model zachová schopnost použít pro obecnou sadu dat, ze kterých byla extrahována data školení.

### <a name="optimize-a-linear-regression-model-with-hyper-parameter-sweeping"></a>Optimalizace lineárního regresního modelu s hyperparametrizací
Dále rozdělte data do sad train a validation, použijte hyperparametrické zametání na trénovací sadě k optimalizaci modelu a vyhodnoťte na ověřovací sadě (lineární regrese).

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


**Výstup:**

Test R-sqr je: 0.6226484708501209

### <a name="optimize-the-binary-classification-model-by-using-cross-validation-and-hyper-parameter-sweeping"></a>Optimalizace binárního klasifikačního modelu pomocí křížového ověřování a hyperparametrické zametání
Tato část ukazuje, jak optimalizovat binární klasifikační model pomocí křížového ověření a hyperparametrika zametání. To používá funkci `CrossValidator` Spark ML.

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


**Výstup:**

Čas spuštění buňky: 33 sekund.

### <a name="optimize-the-linear-regression-model-by-using-custom-cross-validation-and-parameter-sweeping-code"></a>Optimalizace lineárního regresního modelu pomocí vlastního kódu křížového ověření a zametání parametrů
Dále optimalizujte model pomocí vlastního kódu a identifikujte nejlepší parametry modelu pomocí kritéria nejvyšší přesnosti. Potom vytvořte konečný model, vyhodnoťte model na testovacích datech a uložte model v úložišti objektů Blob. Nakonec načtěte model, skóre data testu a vyhodnotit přesnost.

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


**Výstup:**

Čas spuštění buňky: 61 sekund.

## <a name="consume-spark-built-machine-learning-models-automatically-with-scala"></a>Svyužití modelů strojového učení se systémem Spark-built machine learning
Přehled témat, která vás provedou úkoly, které tvoří proces datové vědy v Azure, najdete v [tématu Proces vědecké ho spoje týmových dat](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

[Návody pro zpracování vědeckého procesu týmových dat](walkthroughs.md) popisují další komplexní návody, které ukazují kroky v procesu vědecké ho řízení týmových dat pro konkrétní scénáře. Návody také ilustrují, jak kombinovat cloudové a místní nástroje a služby do pracovního postupu nebo kanálu a vytvořit inteligentní aplikaci.

[Modely strojového učení postavené na Skóre Spark](spark-model-consumption.md) vám ukáže, jak pomocí kódu Scala automaticky načítat a získávat skóre nové datové sady pomocí modelů strojového učení integrovaných ve Sparku a uložených v úložišti objektů Blob Azure. Můžete postupovat podle pokynů, které jsou zde k dispozici, a jednoduše nahradit kód Pythonu kódem Scala v tomto článku pro automatizovanou spotřebu.

