---
title: Vědecké zkoumání dat pomocí Scala a Spark v Azure – vědecké zpracování týmových dat
description: Jak používat Scala pro úkoly technik strojového učení pomocí Sparku škálovatelné MLlib a Spark ML balíčky v clusteru Azure HDInsight Spark.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76716759"
---
# <a name="data-science-using-scala-and-spark-on-azure"></a>Vědecké zkoumání dat pomocí Scala a Spark v Azure
Tento článek ukazuje, jak pomocí Scala pro úkoly technik strojového učení pomocí Sparku škálovatelné MLlib a Spark ML balíčky v clusteru Azure HDInsight Spark. Provede vás úkoly, které tvoří [proces vědeckého zpracování dat](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/): přijímání a zkoumání dat, vizualizace, strojírenství funkcí, modelování a využití modelu. Modely v článku zahrnují logistické a lineární regrese, náhodných doménové struktury a vylepšené přechodu stromů (GBTs), kromě dvě běžné úkoly technik strojového učení:

* Regresní problém: předpověď tip částka cesty taxíkem ($)
* Binární klasifikace: predikce tip nebo tip cesty taxíkem (1/0)

Proces modelování vyžaduje trénování a hodnocení na testovací datové sady a relevantní přesnost metriky. V tomto článku se dozvíte, jak k uložení těchto modelů ve službě Azure Blob storage a jak stanovení skóre a vyhodnotit prediktivní výkonu. Tento článek se týká také pokročilejší témata o tom, jak pomocí křížového ověření a hyperparametrické sweeping optimalizaci modelů. Data používaná je ukázka 2013 NYC taxislužby cesty a tarif datové sady k dispozici na Githubu.

[Scala](https://www.scala-lang.org/)jazyk založený na virtuálním počítači Java integruje koncepty objektově orientovaného a funkčního jazyka. Je škálovatelné jazyk, který skvěle hodí pro distribuované zpracování v cloudu a spouští v clusterech Spark v Azure.

[Spark](https://spark.apache.org/) je open source platforma pro paralelní zpracování, která podporuje zpracování v paměti pro zvýšení výkonu aplikací pro analýzu velkých objemů dat. Modul zpracování Spark je postaven pro rychlost, snadné použití a sofistikované analýzy. Spark v paměti distribuovanou výpočetní možnosti usnadňují dobrou volbu pro iterativní algoritmy ve výpočtech machine learning a grafů. Balíček [Spark.ml](https://spark.apache.org/docs/latest/ml-guide.html) poskytuje jednotnou sadu rozhraní API na nejvyšší úrovni postavených na datových snímcích, které vám pomůžou vytvořit a ladit praktické kanály strojového učení. [MLlib](https://spark.apache.org/mllib/) je škálovatelná knihovna strojového učení Sparku, která přináší možnosti modelování do tohoto distribuovaného prostředí.

[HDInsight Spark](../../hdinsight/spark/apache-spark-overview.md) je nabídka Azure hostovaná jako open source Spark. Také zahrnuje podporu pro poznámkové bloky Jupyter Scala v clusteru Spark a možné spouštění interaktivních dotazů Spark SQL transformace, filtrovat a vizualizovat data uložená v úložišti objektů Blob v Azure. Fragmenty kódu Scala v tomto článku, které poskytují řešení a zobrazit příslušné grafy k vizualizaci dat spouštět v poznámkových blocích Jupyter nainstalované v clusterech Spark. Kroky modelování v těchto tématech mít kód, který popisuje, jak pro trénování, vyhodnocení, uložit a používat každý typ modelu.

Postup instalace a kód v tomto článku jsou pro Azure HDInsight 3.4 Spark 1.6. Kód v tomto článku a [Scala Jupyter notebook](https://github.com/Azure-Samples/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb) jsou ale obecné a měly by fungovat na jakémkoli clusteru Spark. Postup instalace a správy clusteru může být mírně liší, jak je zobrazeno v tomto článku, pokud nepoužíváte HDInsight Spark.

> [!NOTE]
> Téma, které vám ukáže, jak používat Python místo Scala k dokončení úloh pro kompletní proces zpracování dat, najdete v tématu věnovaném [datové vědy pomocí Sparku v Azure HDInsight](spark-overview.md).
> 
> 

## <a name="prerequisites"></a>Předpoklady
* Mít předplatné Azure. Pokud ho ještě nemáte, [Získejte bezplatnou zkušební verzi Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Potřebujete cluster Azure HDInsight 3.4 Spark 1.6 dokončete následující postup. Pokud chcete vytvořit cluster, přečtěte si pokyny v tématu [Začínáme: vytvoření Apache Spark ve službě Azure HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). V nabídce **Vybrat typ clusteru** nastavte typ a verzi clusteru.

![Konfigurace clusteru typu HDInsight](./media/scala-walkthrough/spark-cluster-on-portal.png)

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
> 
> 

Popis údajů o cestách NYC taxislužby a pokyny, jak spustit kód z poznámkového bloku Jupyter v clusteru Spark, najdete v příslušných oddílech v tématu [Přehled vědeckého zpracování dat pomocí Sparku v Azure HDInsight](spark-overview.md).  

## <a name="execute-scala-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Spustit z poznámkového bloku Jupyter pro Spark cluster Scala kódu
Můžete spustit Poznámkový blok Jupyter na webu Azure Portal. Najít cluster Spark na řídicím panelu a potom klikněte na něj přejděte na stránku správy pro váš cluster. V dalším kroku klikněte na **řídicí panely clusteru**a potom kliknutím na **Jupyter notebook** otevřete Poznámkový blok přidružený ke clusteru Spark.

![Řídicí panel clusteru a poznámkové bloky Jupyter](./media/scala-walkthrough/spark-jupyter-on-portal.png)

K poznámkovým blokům můžete také přistupovat na adrese https://&lt;název_clusteru&gt;. azurehdinsight.net/jupyter. Položku *název_clusteru* nahraďte názvem vašeho clusteru. Budete potřebovat heslo pro účet správce pro přístup k poznámkové bloky Jupyter.

![Přejděte do poznámkových bloků Jupyter s použitím názvu clusteru](./media/scala-walkthrough/spark-jupyter-notebook.png)

Pokud chcete zobrazit adresář s několika příklady předbalených poznámkových bloků, které používají rozhraní PySpark API, vyberte **Scala** . Pomocí poznámkového bloku Scala. ipynb, který obsahuje ukázky kódu pro tuto sadu témat Spark, je k dispozici na [GitHubu](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Scala)modelování a vyhodnocování průzkumu.

Můžete nahrát ho přímo z Githubu na server poznámkového bloku Jupyter na svém clusteru Spark. Na domovské stránce Jupyter klikněte na tlačítko **nahrát** . V Průzkumníku souborů vložte adresu URL webu GitHub (RAW Content) do poznámkového bloku Scala a pak klikněte na **otevřít**. Poznámkový blok Scala je k dispozici na na následující adrese URL:

[Průzkum – modelování a bodování – using-Scala. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb)

## <a name="setup-preset-spark-and-hive-contexts-spark-magics-and-spark-libraries"></a>Instalační program: Kontexty přednastavený kontext Spark a Hive, Magic Sparku a Spark knihovny
### <a name="preset-spark-and-hive-contexts"></a>Přednastavení kontexty Spark a Hive
    # SET THE START TIME
    import java.util.Calendar
    val beginningTime = Calendar.getInstance().getTime()


Spark jader, které jsou k dispozici s poznámkovými bloky Jupyter mají přednastavení kontexty. Není nutné explicitně nastavit Spark nebo vyvíjíte Hive kontexty před zahájením práce s aplikací. Přednastavených kontextech jsou:

* `sc` pro SparkContext
* `sqlContext` pro HiveContext

### <a name="spark-magics"></a>Spark Magic
Jádro Sparku poskytuje některé předdefinované "Magic", což jsou speciální příkazy, které můžete volat pomocí `%%`. Dva z těchto příkazů se používají v následující ukázky kódu.

* `%%local` určuje, zda bude kód v dalších řádcích spouštěn místně. Kód musí být platný kód Scala.
* `%%sql -o <variable name>` spustí dotaz na podregistr proti `sqlContext`. Pokud je předán parametr `-o`, výsledek dotazu je trvalý v kontextu `%%local` Scala jako datový rámec Spark.

Další informace o jádrech pro Jupyter poznámkových blocích a jejich předdefinovaných "Magic", která zavoláte s `%%` (například `%%local`), najdete v tématu [jádra dostupná pro poznámkové bloky Jupyter s clustery HDInsight Spark Linux v HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

### <a name="import-libraries"></a>Importovat knihovny
Importujte Spark MLlib a dalších knihoven, které budete potřebovat pomocí následujícího kódu.

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
Prvním krokem v procesu pro datové vědy je zpracování příjmu dat, který chcete analyzovat. Načítání dat z externích zdrojů nebo systémy, ve kterém se nachází do prostředí pro zkoumání a modelování vaší datové. V tomto článku můžete příjímat data je připojené k doméně ukázka 0,1 % taxislužby cesty a tarif souboru (uložený jako soubor TSV). Prostředí pro zkoumání a modelování datové je Spark. Tato část obsahuje kód pro dokončení následující řadu úloh:

1. Nastavit cesty adresáře pro ukládání dat a modelu.
2. Přečíst vstupní datové sady (uložený jako soubor TSV).
3. Definovat schéma pro data a vyčistit data.
4. Vytvořit objekt frame, vyčištěnou dat a uložení do mezipaměti v paměti.
5. Zaregistrujte se jako dočasné tabulky v kontext SQLContext data.
6. Dotaz tabulku a import výsledků do datového rámce.

### <a name="set-directory-paths-for-storage-locations-in-azure-blob-storage"></a>Nastavit cesty adresáře na umístění úložiště ve službě Azure Blob storage
Spark může číst a zapisovat do úložiště objektů Blob v Azure. Můžete použití Spark ke zpracování některý z existujících dat a pak znovu ukládání výsledků do úložiště objektů Blob.

Chcete-li uložit modely nebo souborů v úložišti objektů Blob, správně zadat cestu. Odkažte na výchozí kontejner připojený ke clusteru Spark pomocí cesty, která začíná na `wasb:///`. Odkazování na jiná umístění pomocí `wasb://`.

Následující příklad kódu určuje umístění vstupních dat pro čtení a cestu do úložiště objektů Blob, který je připojen ke clusteru Spark pro uložení modelu.

    # SET PATHS TO DATA AND MODEL FILE LOCATIONS
    # INGEST DATA AND SPECIFY HEADERS FOR COLUMNS
    val taxi_train_file = sc.textFile("wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv")
    val header = taxi_train_file.first;

    # SET THE MODEL STORAGE DIRECTORY PATH
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS REQUIRED.
    val modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";


### <a name="import-data-create-an-rdd-and-define-a-data-frame-according-to-the-schema"></a>Umožňuje importovat data, vytvářet RDD a definovat datového rámce podle schématu
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


**Výkonem**

Čas spuštění buňku: 8 sekund.

### <a name="query-the-table-and-import-results-in-a-data-frame"></a>Dotaz tabulku a importovat výsledky v datovém rámci.
V dalším kroku dotaz tabulku pro tarif, osobní a datový tip; filtrovat data poškozena a odlehlé; a tisk několik řádků.

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

**Výkonem**

| fare_amount | passenger_count | tip_amount | šikmý |
| --- | --- | --- | --- |
|        13.5 |1.0 |2.9 |1.0 |
|        16.0 |2.0 |3.4 |1.0 |
|        10.5 |2.0 |1.0 |1.0 |

## <a name="data-exploration-and-visualization"></a>Zkoumání a vizualizace dat
Pokud přenesete data do Spark, je dalším krokem v procesu pro datové vědy získali lepší představu o datech prostřednictvím zkoumání a vizualizaci. V této části můžete prozkoumat data taxislužby města pomocí dotazů jazyka SQL. Pak importujte výsledky do datového rámce a vyznázorněte cílové proměnné a funkce pro vizuální kontrolu pomocí funkce Automatické vizualizace Jupyter.

### <a name="use-local-and-sql-magic-to-plot-data"></a>Použijte místní verzi a magický příkaz jazyka SQL k vykreslení dat
Ve výchozím nastavení výstup jakékoli fragmenty kódu, který můžete spustit z poznámkového bloku Jupyter je k dispozici v rámci kontextu relace, která se ukládají na pracovních uzlech. Pokud chcete uložit cestu k pracovním uzlům pro každý výpočet a pokud jsou všechna data potřebná pro výpočet místně k dispozici v uzlu serveru Jupyter (který je hlavním uzlem), můžete pomocí `%%local` Magic spustit fragment kódu na serveru Jupyter.

* **SQL Magic** (`%%sql`). Jádra HDInsight Spark podporuje dotazy HiveQL snadno vložené na kontext SQLContext. Argument (`-o VARIABLE_NAME`) uchovává výstup dotazu SQL jako datový rámec PANDAS na serveru Jupyter. Toto nastavení znamená, že výstup bude k dispozici v místním režimu.
* `%%local` **Magic**. `%%local` Magic spouští kód místně na serveru Jupyter, což je hlavní uzel clusteru HDInsight. Obvykle používáte `%%local` Magic ve spojení s `%%sql` Magic s parametrem `-o`. Parametr `-o` porovná výstup dotazu SQL místně a potom `%%local` Magic spustí další sadu fragmentů kódu pro místní spuštění s výstupem dotazů SQL, které jsou trvale uložené v místním prostředí.

### <a name="query-the-data-by-using-sql"></a>Dotazování dat pomocí SQL
Tento dotaz načte cesty taxíkem částka tarif, osobní počet a velikost špičky.

    # RUN THE SQL QUERY
    %%sql -q -o sqlResults
    SELECT fare_amount, passenger_count, tip_amount, tipped FROM taxi_train WHERE passenger_count > 0 AND passenger_count < 7 AND fare_amount > 0 AND fare_amount < 200 AND payment_type in ('CSH', 'CRD') AND tip_amount > 0 AND tip_amount < 25

V následujícím kódu `%%local` Magic vytvoří místní datový rámec sqlResults. SqlResults můžete použít k vykreslení s použitím matplotlib.

> [!TIP]
> Místní magic se používá více než jednou v tomto článku. Pokud vaši datovou sadu je velká, ukázkový prosím k vytvoření datového rámce, který se vejde do místní paměti.
> 
> 

### <a name="plot-the-data"></a>Vykreslení dat
Můžete zobrazit pomocí kódu v Pythonu v místní kontext jako datový rámec Pandas po datového rámce.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES.
    # CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, ETC.)
    sqlResults


 Jádra Spark se automaticky vizualizuje výstup příkazů jazyka SQL (HiveQL) po spuštění kódu. Můžete si vybrat mezi několika typy vizualizací:

* Tabulka
* Výsečový
* Perokresba
* Oblast
* Pruhový

Tady je kód pro vykreslení dat:

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


**Výkonem**

![Tip částka histogramu](./media/scala-walkthrough/plot-tip-amount-histogram.png)

![Tip částka podle počtu osobní](./media/scala-walkthrough/plot-tip-amount-by-passenger-count.png)

![Tip velikost podle velikosti tarif](./media/scala-walkthrough/plot-tip-amount-by-fare-amount.png)

## <a name="create-features-and-transform-features-and-then-prep-data-for-input-into-modeling-functions"></a>Vytvoření funkcí a transformaci funkce a pak pro přípravu dat pro vstup do funkce modelování
U funkce Spark ML a MLlib založený na stromové architektuře modelování budete muset připravit cíl a funkcí pomocí různých technik, jako je například binning, indexování, jeden horkou kódování a vektorizaci. Tady jsou postupy v této části:

1. Vytvořte novou funkci tím, že **binningu** hodiny do časových intervalů provozu.
2. Použijte **indexování a kódování One-Hot** k kategorií funkcím.
3. **Ukázková a rozdělená datová sada** do školicích a testovacích frakcí.
4. **Zadejte školicí proměnnou a funkce**a pak vytvořte indexované nebo jednosměrně zakódované školení a testování vstupních datových sad, které jsou označené jako "RDD", nebo datových rámců.
5. Automatické **kategorizace a vektorizovaná funkcí a cílů** pro použití jako vstupů pro modely strojového učení.

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Vytvořit novou funkci binningu hodin do provozu časovým intervalům
Tento kód ukazuje, jak vytvořit novou funkci binningu hodin do provozu časovým intervalům a výsledný datový rámec v paměti do mezipaměti. Kde Rdd a dat snímků se používá opakovaně, ukládání do mezipaměti vede k lepší spuštění s úspěšností. V několika fázích v následujících postupech budete odpovídajícím způsobem, mezipaměti Rdd a datové rámce.

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


### <a name="indexing-and-one-hot-encoding-of-categorical-features"></a>Indexování a horkou jeden kódování funkcí zařazené do kategorií
Modelování a předpovědět, funkce s kategorií vstupní data indexované nebo se musí kódováním než použití funkce MLlib vyžadují. Tato část ukazuje, jak index nebo kódování zařazené do kategorií funkce pro vstup do funkce modelování.

Potřebujete indexovat nebo kódování vašich modelů různými způsoby v závislosti na modelu. Lineární a logistické regresní modely například vyžadovat jeden horkou kódování. Například funkce s tři kategorie se rozšířit do tří sloupců funkce. Každý sloupec obsahuje 0 nebo 1 podle kategorie hodnotu. MLlib poskytuje funkci [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) pro kódování s jedním aktivním příznakem. Tomto kodéru mapuje sloupec indexů popisek ke sloupci binárního vektorů s maximálně jeden – hodnotu single. S toto kódování algoritmy, které očekávají číselnými hodnotami funkce, jako je logistické regrese, lze použít k funkcím zařazené do kategorií.

Tady můžete transformovat pouze čtyři proměnné zobrazíte příklady, které jsou řetězce znaků. Také můžete indexovat jiné proměnné, jako je například den v týdnu, reprezentovaný číselné hodnoty, jako proměnné zařazené do kategorií.

Pro indexování použijte `StringIndexer()`a pro kódování One-Hot použijte funkce `OneHotEncoder()` z MLlib. Tady je kód pro index a kódování funkcí zařazené do kategorií:

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


**Výkonem**

Čas spuštění buňku: 4 sekundy.

### <a name="sample-and-split-the-data-set-into-training-and-test-fractions"></a>Ukázka a rozdělení datové sady do zlomky trénování a testování
Tento kód vytvoří náhodný vzorkování dat (v tomto příkladu 25 %). I když vzorkování není potřeba například kvůli velikosti datové sady, tento článek ukazuje, jak můžete vzorkovat, abyste věděli, jak používat vlastní problémů v případě potřeby. Když ukázky jsou velké, to ušetří spoustu času, při trénování modelů. Dále rozdělte ukázku na školení část (v tomto příkladu 75 %) a testování částí (v tomto příkladu 25 %) pro použití v zařazení a regresní modelování.

Přidáte náhodné číslo (mezi 0 a 1) pro každý řádek (ve sloupci "rand"), který slouží k výběru přeložení křížového ověření během cvičení.

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


**Výkonem**

Čas spuštění buňku: 2 sekundy.

### <a name="specify-training-variable-and-features-and-then-create-indexed-or-one-hot-encoded-training-and-testing-input-labeled-point-rdds-or-data-frames"></a>Zadejte proměnnou školení a funkce a pak vytvořte indexovaných nebo horkou jeden kódovaný trénování a testování vstupního bodu Rdd nebo datového rámce s popiskem
Tato část obsahuje kód, který ukazuje, jak indexovat zařazené do kategorií textová data jako datový typ s popiskem bodu a jeho kódování, takže ho můžete použít pro trénování a testování logistické regrese MLlib a jiných modelů klasifikace. S popiskem bodových objektů jsou Rdd, které jsou formátovány způsobem, který je potřeba jako vstupní data pro většinu v MLlib algoritmů strojového učení. [Označený bod](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) je místní vektor, buď hustý, nebo zhuštěný, přidružený k popisku/odpovědi.

V tomto kódu je třeba zadat Cílová proměnná (závislé) a funkce, které chcete použít k trénování modelů. Pak vytvoříte indexovaných nebo horkou jeden kódovaný trénování a testování vstupního bodu Rdd nebo datového rámce s popiskem.

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


**Výkonem**

Čas spuštění buňku: 4 sekundy.

### <a name="automatically-categorize-and-vectorize-features-and-targets-to-use-as-inputs-for-machine-learning-models"></a>Automaticky kategorizaci a vektorizovaná funkce a cíle, které chcete použít jako vstupy pro modely strojového učení
Použijte Spark ML ke kategorizaci cíl a funkce, které chcete používat ve funkcích modelování založený na stromové architektuře. Kód provede dvě úlohy:

* Vytvoří binární cíl pro klasifikaci tak, že přiřadíte hodnotu 0 nebo 1 každému datovému bodu mezi 0 a 1 s použitím prahová hodnota 0,5.
* Automaticky rozděluje funkce. Pokud počet různých číselných hodnot u všech funkcí, je menší než 32, jsou rozdělené do kategorií dané funkce.

Tady je kód pro tyto dvě úlohy.

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



## <a name="binary-classification-model-predict-whether-a-tip-should-be-paid"></a>Model binární klasifikace: předpověď, jestli by měla být věnována tip
V této části vytvoříte tři typy binární klasifikační modely a vytvořte předpověď, jestli by měla být věnována tip:

* **Model logistické regrese** s využitím funkce Spark ml `LogisticRegression()`
* **Model klasifikace náhodných doménových struktur** pomocí funkce Spark ml `RandomForestClassifier()`
* **Model klasifikace stromu se zesílením přechodu** pomocí funkce `GradientBoostedTrees()` MLlib

### <a name="create-a-logistic-regression-model"></a>Vytvořte Logistický regresní model
Dále vytvořte model logistické regrese pomocí funkce Spark ML `LogisticRegression()`. Vytvoříte model sestavování kódu v sérii kroků:

1. **Analýza dat modelu** pomocí jedné sady parametrů.
2. **Vyhodnoťte model** pro sadu testovacích dat s metrikami.
3. **Uložte model** v úložišti objektů BLOB pro budoucí spotřebu.
4. Určení **skóre modelu** proti testovacím datům.
5. **Vyznázorněte výsledky** pomocí křivek s provozní charakteristikou (Roc).

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

Načíst, stanovení skóre a uložte výsledky.

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


**Výkonem**

Roc s více TŘÍDAMI v testovacích dat = 0.9827381497557599

Použití Pythonu pro místní snímky dat Pandas k vykreslení křivka roc s více TŘÍDAMI.

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


**Výkonem**

![Popis tlačítka nebo žádná křivka roc s více TŘÍDAMI tip](./media/scala-walkthrough/plot-roc-curve-tip-or-not.png)

### <a name="create-a-random-forest-classification-model"></a>Vytvořte model klasifikace náhodné doménové struktury
Dále vytvořte model klasifikace s náhodnými doménovými strukturami pomocí funkce Spark ML `RandomForestClassifier()` a pak vyhodnoťte model testovacích dat.

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


**Výkonem**

Roc s více TŘÍDAMI v testovacích dat = 0.9847103571552683

### <a name="create-a-gbt-classification-model"></a>Vytvoření modelu GBT klasifikace
Dále vytvořte model klasifikace GBT pomocí funkce `GradientBoostedTrees()` MLlib a pak vyhodnoťte model testovacích dat.

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


**Výkonem**

Oblasti pod křivkou roc s více TŘÍDAMI: 0.9846895479241554

## <a name="regression-model-predict-tip-amount"></a>Regresní model: předpověď velikost špičky
V této části vytvoříte dva typy regresní modely a vytvořte předpověď velikost špičky:

* **Pravidelný lineární regresní model** pomocí funkce Spark ml `LinearRegression()`. Budete uložení modelu a vyhodnocení modelu na testovací data.
* **Model regresního nárůstu přechodu** pomocí funkce Spark ml `GBTRegressor()`.

### <a name="create-a-regularized-linear-regression-model"></a>Vytvoření trénování modelu lineární regrese Vyřešeno
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


**Výkonem**

Čas spuštění buňku: 13 sekund.

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


**Výkonem**

R sqr na testovací data = 0.5960320470835743

V dalším kroku dotazu výsledky testů jako datový rámec a s její vizualizací použijte AutoVizWidget a matplotlib.

    # RUN A SQL QUERY
    %%sql -q -o sqlResults
    select * from testResults

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES
    # CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, AND SO ON)
    sqlResults

Kód vytvoří místní datový rámec z výstupu dotazu a zobrazí data. `%%local` Magic vytvoří místní datový rámec, `sqlResults`, který můžete použít k vykreslení pomocí matplotlib.

> [!NOTE]
> Tato magic Spark se používá více než jednou v tomto článku. Pokud je velké množství dat, by měl ukázkový k vytvoření datového rámce, který se vejde do místní paměti.
> 
> 

Vytvořte grafy pomocí Pythonu matplotlib.

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

**Výkonem**

![Tip množství: skutečnost a předpokládané](./media/scala-walkthrough/plot-actual-vs-predicted-tip-amount.png)

### <a name="create-a-gbt-regression-model"></a>Vytvořte GBT regresní model
Vytvořte GBT regresní model pomocí funkce Spark ML `GBTRegressor()` a pak vyhodnoťte model testovacích dat.

RozGBTSné [stromy](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) () jsou kompletem rozhodovacích stromů. GBTS vlaky rozhodují iterativním způsobem, aby se minimalizovala funkce ztráty. GBTS můžete použít pro regresi a klasifikaci. Jejich zvládne zařazené do kategorií funkcí, nevyžadují, aby funkce škálování a můžete zachytit nonlinearities a interakce funkce. Můžete také lze využít v nastavení multiclass klasifikace.

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


**Výkonem**

Je zkušební R-sqr: 0.7655383534596654

## <a name="advanced-modeling-utilities-for-optimization"></a>Nástroje pro pokročilé modelování pro optimalizaci
V této části použijte nástroje machine learning, které vývojáři často používají pro optimalizaci modelů. Konkrétně můžete optimalizovat modelů strojového učení třemi různými způsoby s použitím parametru sweeping a křížového ověřování:

* Rozdělení dat do sady pro trénování a ověřování, optimalizace modelu s použitím hyperparametrické sweeping na trénovací sady a vyhodnocení na sadě ověření (lineární regrese)
* Optimalizace modelu s použitím křížového ověření a hyperparametrické otevřeného pomocí funkce CrossValidator Spark ML (binární klasifikace)
* Optimalizace modelu pomocí vlastního kódu křížového ověření a parametr sweeping používat jakékoli strojového učení sadu funkcí a parametr (lineární regrese)

**Křížové ověřování** je technika, která posuzuje, jak dobře je model vyhodnocený na známou sadu dat generalizuje, aby předpovídá funkce datových sad, na kterých nebyla vyškolená. Obecnou myšlenku za tento postup je, že model se trénuje na datové sadě známých dat, a pak nemusí být jeho předpovědi je testován vůči nezávislé datové sady. Společná implementace je rozdělit datovou sadu na skládání *k*--a poté vytvořit model v kruhovém dotazování pro všechny kromě jednoho ze skládání.

**Optimalizace parametrů technologie Hyper-** v je problém, který vybírá sadu Hyper-Parameters pro vzdělávací algoritmus, obvykle s cílem optimalizace míry výkonu algoritmu v nezávislé sadě dat. Technologie hyper parametr je hodnota, která je nutné zadat mimo proces trénování modelu. Předpoklady o hyperparametrické hodnoty může ovlivnit, flexibilitu a přesnost modelu. Rozhodovací stromy mají hyperparametry, například, jako je například požadovaný hloubky a počet listů ve stromové struktuře. Je nutné nastavit podmínku chybnou penalizace pro počítač vektorové podpory (SVM).

Běžný způsob, jak provést optimalizaci pomocí technologie Hyper-v, je použít hledání v mřížce, které se označuje také jako **odsweep parametrů**. V mřížce hledání podrobné prohledávání se provádí prostřednictvím hodnoty zadané dílčí hyperparametrické místa pro algoritmus učení. Křížového ověření může poskytovat metrika výkonu optimální výsledky vytvořené metodou mřížky vyhledávacího algoritmu řazení. Pokud používáte sweeping hyperparametrické křížového ověřování, pomáhá omezit problémy, například overfitting modelu na trénovací data. Tímto způsobem model uchovává kapacity použít na obecné sadu dat, ze které byl extrahován trénovací data.

### <a name="optimize-a-linear-regression-model-with-hyper-parameter-sweeping"></a>Optimalizace modelu lineární regrese s hyperparametrické sweeping
V dalším kroku rozdělení dat na trénování a ověření sady, použijte hyperparametrické cílit na konkrétní sadu školení pro optimalizaci modelu a vyhodnocení na sadě ověření (lineární regrese).

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


**Výkonem**

Je zkušební R-sqr: 0.6226484708501209

### <a name="optimize-the-binary-classification-model-by-using-cross-validation-and-hyper-parameter-sweeping"></a>Optimalizovat binární klasifikační model s použitím křížového ověření a hyperparametrické sweeping
V této části se dozvíte, jak optimalizovat binární klasifikační model s použitím křížového ověření a hyperparametrické sweeping. Používá funkci Spark ML `CrossValidator`.

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


**Výkonem**

Čas spuštění buňku: 33 sekund.

### <a name="optimize-the-linear-regression-model-by-using-custom-cross-validation-and-parameter-sweeping-code"></a>Pomocí vlastního kódu křížového ověření a parametr sweeping optimalizovat trénování modelu lineární regrese
V dalším kroku optimalizovat modelu s použitím vlastního kódu a určení nejlepších parametrů modelu pomocí kritéria nejvyšší přesností. Potom vytvořte finálního modelu, vyhodnocení modelu na testovací data a uložit model ve službě Blob storage. Nakonec načíst model, skóre testovacích dat a vyzkoušejte přesnost.

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


**Výkonem**

Čas spuštění buňku: 61 sekund.

## <a name="consume-spark-built-machine-learning-models-automatically-with-scala"></a>Použití modelů předdefinovaných ve Spark machine learning automaticky pomocí Scala
Přehled témat, která vás provedou úkoly, které se týkají procesu datové vědy v Azure, najdete v tématu věnovaném [vědeckému zpracování týmových dat](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

[Návody pro vědecké zpracování týmových dat](walkthroughs.md) popisují další komplexní návody, které ukazují kroky v procesu vědeckého zpracování týmových dat pro konkrétní scénáře. Názorné postupy také ukazují, jak zkombinovat cloudové a místní nástroje a služby do pracovního postupu nebo kanálu pro vytváření inteligentních aplikací.

[Skóre Sparkem vytvořených modelů strojového učení](spark-model-consumption.md) se dozvíte, jak používat Scala kód k automatickému načítání a hodnocení nových datových sad pomocí modelů strojového učení sestavených v Sparku a uložených v úložišti objektů BLOB v Azure. Můžete podle pokynů uvedených zde a jednoduše nahradit Scala kódu v tomto článku pro automatické použití kódu Pythonu.

