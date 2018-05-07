---
title: Vědecké zpracování dat pomocí Scala a Spark v Azure | Microsoft Docs
description: Jak používat Scala pro úkoly pod dohledem strojového učení s Spark škálovatelné MLlib a Spark ML balíčky v clusteru Azure HDInsight Spark.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: a7c97153-583e-48fe-b301-365123db3780
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: deguhath
ms.openlocfilehash: 8f8b252d8771dff23d0a8c89e057fc17ba321a65
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="data-science-using-scala-and-spark-on-azure"></a>Vědecké zkoumání dat pomocí Scala a Spark v Azure
Tento článek ukazuje, jak používat Scala pro úkoly strojového učení s Spark škálovatelné MLlib a Spark ML balíčky v clusteru Azure HDInsight Spark. Provede vás provedou úlohami, které tvoří [vědecké zpracování dat proces](http://aka.ms/datascienceprocess): přijímání dat a zkoumání, vizualizace, funkce analýzy, modelování a spotřeba modelu. Modely v článku zahrnují logistic a lineární regrese, náhodné doménové struktury a přechodu boosted stromy (GBTs), kromě dvě běžné úkoly strojového učení:

* Regrese problému: předpovědi velikost tip ($) pro cestu taxíkem
* Binární klasifikace: předpovědi tip nebo cesty taxíkem žádné tip (1 nebo 0)

Proces modelování vyžaduje trénování a hodnocení na testovací datové sady a relevantní přesnost metriky. V tomto článku můžete zjistěte, jak ukládat tyto modely v Azure Blob storage a jak stanovení skóre a vyhodnotit jejich prediktivní výkonu. Tento článek se týká také pokročilejším tématům o tom, jak optimalizovat modely s použitím sweeping křížové ověření a technologie hyper parametr. Data použitá je ukázka 2013 NYC taxíkem služební cestě a tarif datová sada k dispozici na Githubu.

[Scala](http://www.scala-lang.org/), jazyk založené na virtuálním počítači Java integruje koncepty jazyka objektově orientované a funkční. Je škálovatelná jazyk, který skvěle hodí pro distribuované zpracování v cloudu a běží na clustery Spark v Azure.

[Spark](http://spark.apache.org/) představuje rozhraní pro paralelní zpracování open source, který podporuje zpracování v paměti pro zvýšení výkonu aplikací analýzy velkých objemů dat. Modul zpracování Spark je vytvořené pro rychlost, snadné použití a sofistikované analytics. Možnosti v paměti distribuované výpočtů Spark ho nastavit správnou volbu pro iterativní algoritmy v machine learning a grafů výpočty. [Spark.ml](http://spark.apache.org/docs/latest/ml-guide.html) balíček poskytuje jednotnou sadu vysoké úrovně rozhraní API vytvořená na základě dat snímků, které vám pomůžou vytvářet a ladit praktické strojového učení kanály. [MLlib](http://spark.apache.org/mllib/) je Spark škálovatelné machine learning knihovny, která přináší modelování možnosti pro tento distribuovaném prostředí.

[HDInsight Spark](../../hdinsight/spark/apache-spark-overview.md) je nabídku Azure hostovaná Spark open source. Také zahrnuje podporu pro poznámkové bloky Jupyter Scala na clusteru Spark a můžete spustit interaktivních dotazů Spark SQL k transformaci, filtrování a vizualizovat data uložená v úložišti objektů Blob Azure. Fragmenty kódu Scala v tomto článku s řešení, které ukazují relevantní pozemků k vizualizaci dat spustit v poznámkové bloky Jupyter nainstalovat na clusteru Spark. Modelování kroky v těchto tématech obsahovat kód, který ukazuje, jak cvičení, hodnocení, uložit a používat každý typ modelu.

Postup instalace a kódu v tomto článku jsou pro Azure HDInsight 3.4 Spark 1.6. Ale kód v tomto článku a v [poznámkového bloku Jupyter Scala](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration%20Modeling%20and%20Scoring%20using%20Scala.ipynb) obecné a by měla fungovat v jakémkoliv clusteru Spark. Kroky instalace a Správa clusteru může být mírně lišit od co se zobrazí v tomto článku, pokud nepoužíváte HDInsight Spark.

> [!NOTE]
> Téma, které ukazuje, jak k dokončení úloh na začátku do konce proces vědecké zpracování dat použít Python, nikoli Scala, najdete v části [vědecké zpracování dat pomocí Spark v Azure HDInsight](spark-overview.md).
> 
> 

## <a name="prerequisites"></a>Požadavky
* Mít předplatné Azure. Pokud jste již nemají, [získat bezplatnou zkušební verzi Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Budete potřebovat clusteru Azure HDInsight 3.4 Spark 1.6 proveďte následující postupy. K vytvoření clusteru, postupujte podle pokynů v [Začínáme: Vytvořte Apache Spark v Azure HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Nastavení clusteru typ a verze na **vybrat typ clusteru** nabídky.

![Konfigurace typu clusteru HDInsight](./media/scala-walkthrough/spark-cluster-on-portal.png)

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
> 
> 

Popis NYC taxíkem cestě dat a pokyny, jak provést kód z poznámkového bloku Jupyter v clusteru Spark, najdete v příslušných částech [přehled o vědecké zpracování dat pomocí Spark v Azure HDInsight](spark-overview.md).  

## <a name="execute-scala-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Spuštění z poznámkového bloku Jupyter v clusteru Spark Scala kódu
Můžete spustit poznámkového bloku Jupyter z portálu Azure. Najděte cluster Spark na řídicím panelu a klikněte na něj zadejte na stránce Správa clusteru. Klikněte na tlačítko **řídicí panely clusteru**a potom klikněte na **Poznámkový blok Jupyter** otevřete Poznámkový blok spojené s clusterem Spark.

![Řídicí panel clusteru a poznámkové bloky Jupyter](./media/scala-walkthrough/spark-jupyter-on-portal.png)

Také můžete přístup poznámkové bloky Jupyter na https://&lt;clustername&gt;.azurehdinsight.net/jupyter. Nahraďte *clustername* s názvem vašeho clusteru. Potřebujete heslo pro účet správce pro přístup k Jupyter notebooks.

![Přejděte na poznámkové bloky Jupyter s použitím názvu clusteru](./media/scala-walkthrough/spark-jupyter-notebook.png)

Vyberte **Scala** zobrazíte adresář, který má několik příkladů hotových poznámkových bloků, které používají rozhraní API PySpark. Ukázky zkoumání modelování a vyhodnocování pomocí poznámkového bloku Scala.ipynb, který obsahuje kód pro tuto sadu témat Spark je k dispozici na [Githubu](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Scala).

Můžete nahrát poznámkového bloku přímo z Githubu se serverem Poznámkový blok Jupyter v clusteru Spark. Na domovské stránce Jupyter, klikněte **nahrát** tlačítko. V Průzkumníku souborů, vložte adresu URL GitHub (nezpracovaná obsah) Scala Poznámkový blok a pak klikněte na **otevřete**. Poznámkový blok Scala je k dispozici na následující adrese URL:

[Exploration-Modeling-and-Scoring-using-Scala.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb)

## <a name="setup-preset-spark-and-hive-contexts-spark-magics-and-spark-libraries"></a>Instalace: Kontexty přednastavených Spark a Hive, Spark Magic a knihoven Spark
### <a name="preset-spark-and-hive-contexts"></a>Předvolby kontexty Spark a Hive
    # SET THE START TIME
    import java.util.Calendar
    val beginningTime = Calendar.getInstance().getTime()


Kontexty mít přednastavení jádrech Spark, které jsou k dispozici s poznámkovými bloky Jupyter. Nemusíte explicitně nastavit Spark nebo vývoji Hive kontexty před zahájením práce s aplikací. Přednastavené kontexty jsou:

* `sc` pro SparkContext
* `sqlContext` pro HiveContext

### <a name="spark-magics"></a>Spark Magic
Spark jádra poskytuje některé předdefinované "Magic", které jsou speciální příkazy, které můžete volat s `%%`. V následující ukázky kódu se používají dva z těchto příkazů.

* `%%local` Určuje, že kód na další řádek bude proveden místně. Kód musí být platný kód Scala.
* `%%sql -o <variable name>` provede dotaz Hive proti `sqlContext`. Pokud `-o` parametr se předává, výsledek dotazu je uchován v `%%local` Scala kontextu jako snímek dat Spark.

Pro další informace o jádrech pro poznámkové bloky Jupyter a jejich předdefinované "magics", volání s `%%` (například `%%local`), najdete v části [jádra dostupná pro poznámkové bloky Jupyter s HDInsight Spark Linux clusterů v HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

### <a name="import-libraries"></a>Importovat knihovny
Importujte Spark, MLlib a další knihovny, které budete potřebovat pomocí následující kód.

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
Prvním krokem v procesu vědecké zpracování dat je ingestovat data, která chcete analyzovat. Přenést data z externích zdrojů nebo systémy, kde se nachází do vašeho prostředí zkoumání a modelování data. V tomto článku data, která jste ingestování je připojený k ukázka 0,1 % taxíkem služební cestě a tarif souboru (uložený jako soubor TSV). Data zkoumání a modelování prostředí je Spark. Tato část obsahuje kód pro dokončení následující řadu úloh:

1. Nastavit cesty adresáře pro data a modelu úložiště.
2. Přečtěte si v sadě vstupní data (uložené jako soubor TSV).
3. Definovat schéma pro data a vyčistit data.
4. Vytvořte rámeček vyčištěnými dat a uložení do mezipaměti v paměti.
5. Zaregistrujte se data jako do dočasné tabulky v SQLContext.
6. Dotaz tabulku a importovat výsledky do rámečku data.

### <a name="set-directory-paths-for-storage-locations-in-azure-blob-storage"></a>Nastavení cesty adresáře pro umístění úložiště v Azure Blob storage
Spark můžete číst a zapisovat do úložiště objektů Blob v Azure. Můžete používat Spark ke zpracování stávající data a pak znovu ukládání výsledků do úložiště objektů Blob.

Chcete-li uložit modely nebo soubory v úložišti objektů Blob, správně zadejte cestu. Referenční výchozí kontejner, který je připojen ke clusteru Spark pomocí cesty, který začíná `wasb:///`. Referenční jiných umístění pomocí `wasb://`.

Následující ukázka kódu určuje umístění jsou vstupní data čtení a cestu k úložišti objektů Blob, který je připojen ke clusteru Spark, kde bude uložen modelu.

    # SET PATHS TO DATA AND MODEL FILE LOCATIONS
    # INGEST DATA AND SPECIFY HEADERS FOR COLUMNS
    val taxi_train_file = sc.textFile("wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv")
    val header = taxi_train_file.first;

    # SET THE MODEL STORAGE DIRECTORY PATH
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS REQUIRED.
    val modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";


### <a name="import-data-create-an-rdd-and-define-a-data-frame-according-to-the-schema"></a>Umožňuje importovat data, vytvořit RDD a definovat data rámce podle schématu.
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

### <a name="query-the-table-and-import-results-in-a-data-frame"></a>Dotaz tabulku a importovat výsledky v rámci dat
V dalším kroku dotazu v tabulce tarif, osobní a tip data; filtrování dat poškozen a odlehlé; a tisk několik řádků.

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

| fare_amount | passenger_count | tip_amount | vysypávány |
| --- | --- | --- | --- |
|        13.5 |1.0 |2.9 |1.0 |
|        16.0 |2.0 |3.4 |1.0 |
|        10.5 |2.0 |1.0 |1.0 |

## <a name="data-exploration-and-visualization"></a>Zkoumání dat a vizualizaci
Po přenést data do Spark, je dalším krokem v procesu vědecké zpracování dat získali lepší představu o dat přes zkoumání a vizualizace. V této části Zkontrolujte taxíkem dat pomocí dotazů SQL. Pak importujte výsledky do rámečku dat k vykreslení cílových proměnných a potenciální funkcí pro visual kontroly pomocí funkce Automatické vizualizace z Jupyter.

### <a name="use-local-and-sql-magic-to-plot-data"></a>Použít místní a SQL magic k vykreslení dat
Ve výchozím nastavení je k dispozici v kontextu relace, která jsou ukládána na pracovních uzlech výstup všech fragment kódu, který spustíte z poznámkového bloku Jupyter. Pokud chcete uložit cestě k pracovním uzlům pro každý výpočty, a pokud všechna data, je třeba vaše výpočetní je dostupný místně na uzel serveru Jupyter (což je hlavního uzlu), můžete použít `%%local` magic ke spuštění fragmentu kódu na Jupyter Server.

* **SQL magic** (`%%sql`). Jádro HDInsight Spark podporuje dotazy na snadno vložené HiveQL pro SQLContext. (`-o VARIABLE_NAME`) Argument potrvají výstup příkazu jazyka SQL jako rámeček Pandas dat na serveru Jupyter. To znamená, že budete mít k dispozici v místním režimu.
* `%%local` **Magic**. `%%local` Magic běží kód místně na serveru Jupyter, což je hlavního uzlu v clusteru HDInsight. Obvykle použijete, `%%local` magic ve spojení s `%%sql` magic s `-o` parametr. `-o` Parametr by zachovat výstup příkazu jazyka SQL místně a potom `%%local` magic by aktivovat další sadu fragment kódu ke spouštění místně na výstupu dotazů SQL, který je místně trvalé.

### <a name="query-the-data-by-using-sql"></a>Dotaz na data pomocí SQL
Tento dotaz načte služebních cest taxíkem velikost tarif, osobní počet a velikost tip.

    # RUN THE SQL QUERY
    %%sql -q -o sqlResults
    SELECT fare_amount, passenger_count, tip_amount, tipped FROM taxi_train WHERE passenger_count > 0 AND passenger_count < 7 AND fare_amount > 0 AND fare_amount < 200 AND payment_type in ('CSH', 'CRD') AND tip_amount > 0 AND tip_amount < 25

V následujícím kódu `%%local` magic vytvoří místní data rámce, sqlResults. SqlResults můžete použít k vykreslení pomocí matplotlib.

> [!TIP]
> Místní magic se používá více než jednou. v tomto článku. Pokud je velké datové sady, prosím ukázkové vytvořit datové rámce, který můžete začlenit do místní paměti.
> 
> 

### <a name="plot-the-data"></a>Vykreslení dat
Můžete zobrazit pomocí kód Python po lokální kontext jako snímek dat Pandas rámečku data.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES.
    # CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, ETC.)
    sqlResults


 Spark jádra automaticky vizualizuje výstup dotazy SQL (HiveQL), po spuštění kódu. Můžete si vybrat mezi několik typů vizualizace:

* Table
* Výsečový
* Perokresba
* Oblast
* Pruhový

Tady je kód k vykreslení dat:

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

![Tip velikost histogram](./media/scala-walkthrough/plot-tip-amount-histogram.png)

![Tip velikost podle počtu osobní](./media/scala-walkthrough/plot-tip-amount-by-passenger-count.png)

![Tip velikost podle velikosti tarif](./media/scala-walkthrough/plot-tip-amount-by-fare-amount.png)

## <a name="create-features-and-transform-features-and-then-prep-data-for-input-into-modeling-functions"></a>Vytvoření funkce transformace funkce a potom připravená data data pro vstup do funkce modelování
Pro funkce na základě stromu modelování ze Spark ML a MLlib budete muset připravit cílový a funkcí pomocí různých technik, jako například přihrádkování, indexování, jeden horkou kódování a vectorization. Zde je postup použijte v této části:

1. Vytvořit novou funkci ve **přihrádkování** čas do provozu časové intervaly.
2. Použít **horkou jeden kódování a indexování** kategorií funkcí.
3. **Ukázkové a rozdělení v datové sadě** do učení a testovací zlomků.
4. **Zadejte proměnnou školení a funkce**a poté vytvořit indexované nebo horkou jeden kódovaný školení a testování vstupní bod s popiskem odolné distribuovaných datové sady (RDDs) nebo datové rámce.
5. Automaticky **kategorií a vectorize funkce a cíle** chcete použít jako vstupy pro modely machine learning.

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Vytvořit novou funkci přihrádkování čas do provozu čas intervalů
Tento kód ukazuje, jak vytvořit novou funkci přihrádkování čas do kbelíků dobu provozu a jak pro ukládání do mezipaměti výsledné datové rámce v paměti. Kde rámce RDDs a data se používají opakovaně, ukládání do mezipaměti vede k lepší časy spuštění. V několika fázích v následujících postupech budete odpovídajícím způsobem, mezipaměti RDDs a datové rámce.

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


### <a name="indexing-and-one-hot-encoding-of-categorical-features"></a>Indexování a jeden horkou kódování kategorií funkcí
Modelování a předvídání funkce MLlib vyžadují funkce s kategorií vstupní data na indexované nebo kódovaný před použití. V této části ukazuje, jak index nebo kódování kategorií funkce pro vstup do funkce modelování.

Budete muset index nebo kódování modely různými způsoby v závislosti na modelu. Například logistic a lineární regrese modely vyžadovat horkou jeden kódování. Například můžete do tři sloupce funkce rozšířit funkce s tří kategorií. Každý sloupec obsahuje 0 nebo 1 v závislosti na kategorii pozorování. Poskytuje MLlib [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) funkce pro jeden horkou kódování. Tato kodér mapuje sloupec popisek indexů ke sloupci binárního vektory s maximálně jednu jeden – hodnotu. Toto kódování, lze použít algoritmy, které očekávají číselných hodnot funkce, jako je logistic regression kategorií funkcí.

Zde transformace pouze čtyři proměnné, které chcete zobrazit příklady, které jsou řetězce znaků. Další proměnné, jako například den v týdnu, reprezentována číselné hodnoty, jako kategorií proměnné také mohou indexu.

Pro indexování, použijte `StringIndexer()`a pro jeden horkou kódování, použijte `OneHotEncoder()` funkce z MLlib. Tady je kód pro index a kódování kategorií funkce:

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

### <a name="sample-and-split-the-data-set-into-training-and-test-fractions"></a>Ukázka a rozdělení datové sady do učení a testovací zlomků
Tento kód vytvoří náhodné vzorky dat (v tomto příkladu 25 %). I když vzorkování není potřeba v tomto příkladu kvůli překročení velikosti datové sady, článek ukazuje, jak můžete zkusit, abyste věděli, jak používat vlastní problémů v případě potřeby. Po velká vzorky lze ušetřit čas významné během cvičení modelů. Dále rozdělte vzorku na školení část (v tomto příkladu 75 %) a testování část (v tomto příkladu 25 %) pro použití v klasifikaci a regresní modelování.

Přidáte na každý řádek (ve sloupci "rand –"), který slouží k výběru křížové ověření složení během cvičení náhodné číslo (mezi 0 a 1).

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

### <a name="specify-training-variable-and-features-and-then-create-indexed-or-one-hot-encoded-training-and-testing-input-labeled-point-rdds-or-data-frames"></a>Zadejte proměnnou školení a funkce a pak vytvořte indexované nebo jeden horkou kódovaný trénování a testování vstup s názvem bez přípony bodu RDDs nebo data rámce
Tato část obsahuje kód, který ukazuje, jak indexu kategorií textová data jako datový typ s popiskem bodu a jeho kódování, takže ho můžete a natrénuje a otestuje MLlib logistic regression a jinými modely klasifikace. S popiskem bodu objekty jsou RDDs naformátované způsobem, který je nutný jako vstupní data pro většinu v MLlib algoritmů strojového učení. A [s názvem bez přípony bodu](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) je přidružen místní vektoru hustých nebo zhuštění, popisek nebo odpověď.

V tento kód zadejte Cílová proměnná (závislé) a funkce, které chcete použít k trénování modely. Pak vytvoříte indexované nebo jeden horkou kódovaný trénování a testování vstup s názvem bez přípony bodu RDDs nebo data rámce.

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

### <a name="automatically-categorize-and-vectorize-features-and-targets-to-use-as-inputs-for-machine-learning-models"></a>Automaticky kategorií a vectorize funkce a cíle, které chcete použít jako vstupy pro modelů strojového učení
Použijte Spark ML ke kategorizaci cíle a funkce, které chcete použít na základě stromu modelování funkcí. Kód dokončení dvě úlohy:

* Vytvoří binární cíl pro klasifikaci přiřazením hodnoty 0 nebo 1 pro každý datový bod mezi 0 a 1 pomocí prahovou hodnotu 0,5.
* Automaticky rozděluje funkce. Pokud počet jedinečných hodnot číselné u všech funkcí je menší než 32, je zařazený do kategorie této funkce.

Zde je kód pro tyto dvě úlohy.

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



## <a name="binary-classification-model-predict-whether-a-tip-should-be-paid"></a>Model binární klasifikace: předpovědět, zda by měl být placené tip
V této části můžete vytvořit tři typy binární klasifikace modely k předvídání, zda by měl být placené tip:

* A **logistic regresní model** pomocí Spark ML `LogisticRegression()` – funkce
* A **model klasifikace náhodných doménové struktury** pomocí Spark ML `RandomForestClassifier()` – funkce
* A **přechodu zvýšení skóre modelu klasifikace stromu** pomocí MLlib `GradientBoostedTrees()` – funkce

### <a name="create-a-logistic-regression-model"></a>Vytvoření modelu logistic regression
Dále vytvořte logistic regresní model pomocí Spark ML `LogisticRegression()` funkce. Vytvoříte model vytváření kódu v sérii kroků:

1. **Trénování modelu** dat pomocí jednu sadu parametrů.
2. **Vyhodnocení modelu** na testovací datové sady s metriky.
3. **Model uložte** v úložišti objektů Blob pro budoucí spotřeby.
4. **Modul score model** proti testovacích datech.
5. **Vykreslení výsledky** s příjemce operační křivek vlastnosti (ROC).

Tady je kód pro tyto postupy:

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

Zatížení, stanovení skóre a uložte výsledky.

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

ROC na testovací data = 0.9827381497557599

K vykreslení křivka ROC použijte jazyk Python na místní Pandas datové rámce.

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

![Tip nebo žádné křivka ROC tipu](./media/scala-walkthrough/plot-roc-curve-tip-or-not.png)

### <a name="create-a-random-forest-classification-model"></a>Vytvoření modelu klasifikace náhodných doménové struktury
Dále vytvořte model klasifikace náhodných doménové struktury pomocí Spark ML `RandomForestClassifier()` fungovat a pak vyhodnotit modelu na testovacích datech.

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

ROC na testovací data = 0.9847103571552683

### <a name="create-a-gbt-classification-model"></a>Vytvoření modelu GBT klasifikace
Dále vytvořte klasifikaci model GBT pomocí MLlib na `GradientBoostedTrees()` fungovat a pak vyhodnotit modelu na testovacích datech.

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

Oblasti v rámci křivka ROC: 0.9846895479241554

## <a name="regression-model-predict-tip-amount"></a>Regresní model: předpovědi velikost tipu
V této části vytvoříte dva typy regrese modely k předvídání velikost tip:

* A **model lineární regrese Vyřešeno** pomocí Spark ML `LinearRegression()` funkce. Můžete uložit model a vyhodnocení modelu na testovacích datech.
* A **zvyšovat skóre přechodu stromu regresní model** pomocí Spark ML `GBTRegressor()` funkce.

### <a name="create-a-regularized-linear-regression-model"></a>Vytvořit model lineární regrese Vyřešeno
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

R – sqr na testovací data = 0.5960320470835743

V dalším kroku dotaz výsledky testů jako snímek dat a použít AutoVizWidget a matplotlib k vizualizaci ho.

    # RUN A SQL QUERY
    %%sql -q -o sqlResults
    select * from testResults

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES
    # CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, AND SO ON)
    sqlResults

Kód vytvoří místní data snímku z výstupu dotazu a ukazuje zeměpisný data. `%%local` Magic vytvoří místní data rámce, `sqlResults`, který můžete použít k vykreslení s matplotlib.

> [!NOTE]
> Tato magic Spark se používá více než jednou. v tomto článku. Pokud je velké množství dat, by měl ukázkové vytvořit datové rámce, který můžete začlenit do místní paměti.
> 
> 

Vytvořte pozemků pomocí Python matplotlib.

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

![Tip velikost: skutečnost a předpokládaných](./media/scala-walkthrough/plot-actual-vs-predicted-tip-amount.png)

### <a name="create-a-gbt-regression-model"></a>Vytvoření GBT regresní model
Vytvoření GBT regresní model pomocí Spark ML `GBTRegressor()` fungovat a pak vyhodnotit modelu na testovacích datech.

[Boosted přechodu stromy](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) jsou komplety rozhodovací stromy. GBTs cvičení stromů rozhodnutí interaktivně, aby se minimalizoval funkci ztrátu. GBTs můžete použít pro regresní a klasifikace. Se může zpracovat kategorií funkce, nevyžadují funkce škálování a můžete zaznamenat nonlinearities a interakce funkce. Také můžete je v nastavení multiclass klasifikace.

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

Je test R-sqr: 0.7655383534596654

## <a name="advanced-modeling-utilities-for-optimization"></a>Pokročilé modelování nástrojů pro optimalizaci
V této části použijte nástroje learning počítače, které vývojáři často používají pro optimalizaci modelu. Konkrétně můžete pomocí parametru (vymetání) komínů a křížové ověření optimalizovat modely machine learning třemi různými způsoby:

* Rozdělení dat do vlaku a ověření sady, optimalizovat modelu s použitím technologie hyper parametr (vymetání) komínů na trénovací sady a vyhodnocení na sadu ověření (lineární regrese)
* Optimalizovat modelu s použitím křížové ověření a technologie hyper parametr komínů pomocí funkce CrossValidator Spark ML (binární klasifikace)
* Optimalizovat modelu s použitím vlastní kód křížové ověření a parametr sweeping používat všechny strojového učení sadu funkce a parametr (lineární regrese)

**Křížové ověření** je technika, který vyhodnocuje, jak dobře model trénink na známé sadu dat bude generalize k předvídání funkce datové sady, na kterých je nebyla cvičena. Obecné cílem tato technika je cvičení modelu na datové sady známých dat, a pak je testován přesnost jeho předpovědi vůči nezávislé datové sady. Běžná implementace je rozdělit datové sady do *tisíc*-složení a potom trénování modelu v kruhového dotazování na všechny kromě jednoho složení.

**Technologie Hyper parametr optimalizace** je tento problém vybrat sadu technologie hyper parametrů pro algoritmu učení, obvykle s cílem optimalizace měření výkonu algoritmus na nezávislé datové sady. Technologie hyper parametr je hodnota, která je nutné zadat mimo proces školení modelu. Předpoklady o technologie hyper parametr hodnoty může ovlivnit flexibilitu a přesnosti modelu. Rozhodovací stromy například mít technologie hyper parametry, jako je například požadovaný hloubkou a počet nechá ve stromu. Je nutné nastavit termín snížení chybnou klasifikaci pro podporu vektoru počítač (SVM).

Běžný způsob provedení optimalizace hyper parametr je používání funkce Hledat mřížky, označované taky jako **oblouku parametr**. V hledání mřížky provádí podrobné prohledávání prostřednictvím hodnoty podmnožinu zadaný hyper parametr prostoru pro algoritmus učení. Křížového ověření může poskytovat metriky výkonu seřadit optimální výsledky vyprodukované vyhledávací algoritmus mřížky. Pokud používáte křížové ověření (technologie hyper parametr vymetání) komínů, můžete pomoct limit problémy jako overfitting modelu pro Cvičná data. Tímto způsobem modelu zachová kapacity, které chcete použít pro obecné sadu dat, ze kterého jste extrahovali Cvičná data.

### <a name="optimize-a-linear-regression-model-with-hyper-parameter-sweeping"></a>Optimalizace model lineární regrese s hyper parametr (vymetání) komínů
Dále rozdělit data do vlaku a ověření sad, použití technologie hyper parametr komínů na trénovací sady k optimalizaci modelu a vyhodnocení na sadu ověření (lineární regrese).

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

Je test R-sqr: 0.6226484708501209

### <a name="optimize-the-binary-classification-model-by-using-cross-validation-and-hyper-parameter-sweeping"></a>Optimalizovat binární klasifikace modelu s použitím (vymetání) křížové ověření a technologie hyper parametr komínů
V této části ukazuje, jak optimalizovat binární klasifikace modelu s použitím sweeping křížové ověření a technologie hyper parametr. Tato služba využívá Spark ML `CrossValidator` funkce.

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

### <a name="optimize-the-linear-regression-model-by-using-custom-cross-validation-and-parameter-sweeping-code"></a>Optimalizovat model lineární regrese s použitím vlastní křížové ověření a parametr sweeping kód
V dalším kroku optimalizovat modelu s použitím vlastního kódu a identifikovat nejlepší parametry modelu pomocí kritéria nejvyšší přesnost. Pak vytvořte konečné modelu, vyhodnocení modelu na testovací data a uložit model v úložišti objektů Blob. Nakonec načíst model, stanovení skóre testovací data a vyhodnotit přesnost.

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

## <a name="consume-spark-built-machine-learning-models-automatically-with-scala"></a>Využívat modelů learning vytvořené Spark počítač automaticky pomocí Scala
Přehled témata, která vás provede procesem úlohy, které tvoří proces vědecké zpracování dat v Azure najdete v tématu [proces vědecké účely dat Team](http://aka.ms/datascienceprocess).

[Tým datové vědy proces návody](walkthroughs.md) popisuje další návody začátku do konce, které ukazují kroků v procesu vědecké účely Team dat u konkrétních scénářů. Názorné postupy také ukazují, jak kombinovat cloudové a místní nástroje a služby do pracovního postupu nebo kanálu vytvoření inteligentního aplikace.

[Stanovení skóre modely vytvořené Spark strojové učení](spark-model-consumption.md) ukazuje, jak pomocí Scala kódu automaticky načíst a stanovíte jeho skóre nové sady dat s modely machine learning součástí Spark a uloží do úložiště objektů Blob v Azure. Můžete podle podle pokynů k dispozici a jednoduše místo kód Python Scala kód v tomto článku automatizované spotřeby.

