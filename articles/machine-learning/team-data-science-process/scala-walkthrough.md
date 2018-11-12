---
title: Vědecké zkoumání dat pomocí Scala a Spark v Azure | Dokumentace Microsoftu
description: Jak používat Scala pro úkoly technik strojového učení pomocí Sparku škálovatelné MLlib a Spark ML balíčky v clusteru Azure HDInsight Spark.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: a7c97153-583e-48fe-b301-365123db3780
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: deguhath
ms.openlocfilehash: b90603490af851d9b7ca735b00ee7d6ca5d53951
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51233520"
---
# <a name="data-science-using-scala-and-spark-on-azure"></a>Vědecké zkoumání dat pomocí Scala a Spark v Azure
Tento článek ukazuje, jak pomocí Scala pro úkoly technik strojového učení pomocí Sparku škálovatelné MLlib a Spark ML balíčky v clusteru Azure HDInsight Spark. Provede vás provedou úlohami, které tvoří [vědecké zkoumání dat](https://aka.ms/datascienceprocess): příjem dat a zkoumání, vizualizaci, vytváření funkcí, modelování a modelu využití. Modely v článku zahrnují logistické a lineární regrese, náhodných doménové struktury a vylepšené přechodu stromů (GBTs), kromě dvě běžné úkoly technik strojového učení:

* Regresní problém: předpověď tip částka cesty taxíkem ($)
* Binární klasifikace: predikce tip nebo tip cesty taxíkem (1/0)

Proces modelování vyžaduje trénování a hodnocení na testovací datové sady a relevantní přesnost metriky. V tomto článku se dozvíte, jak k uložení těchto modelů ve službě Azure Blob storage a jak stanovení skóre a vyhodnotit prediktivní výkonu. Tento článek se týká také pokročilejší témata o tom, jak pomocí křížového ověření a hyperparametrické sweeping optimalizaci modelů. Data používaná je ukázka 2013 NYC taxislužby cesty a tarif datové sady k dispozici na Githubu.

[Scala](http://www.scala-lang.org/), objektově orientované a funkční jazykové koncepce integruje jazyk založený na virtuálním počítači Java. Je škálovatelné jazyk, který skvěle hodí pro distribuované zpracování v cloudu a spouští v clusterech Spark v Azure.

[Spark](http://spark.apache.org/) je open-source platforma paralelní zpracování, která podporuje zpracování v paměti pro zvýšení výkonu velkých objemů dat analytických aplikací. Modul zpracování Spark je postaven pro rychlost, snadné použití a sofistikované analýzy. Spark v paměti distribuovanou výpočetní možnosti usnadňují dobrou volbu pro iterativní algoritmy ve výpočtech machine learning a grafů. [Spark.ml](http://spark.apache.org/docs/latest/ml-guide.html) balíček poskytuje jednotné sadu rozhraní API vysoké úrovně postavené na data snímků, které vám pomůžou vytvořit a vyladit praktické strojového učení kanály. [MLlib](http://spark.apache.org/mllib/) je knihovny Spark škálovatelné machine learning, která přináší možnosti modelování do této distribuované prostředí.

[HDInsight Spark](../../hdinsight/spark/apache-spark-overview.md) je nabídka hostovaných v Azure z open-source Spark. Také zahrnuje podporu pro poznámkové bloky Jupyter Scala v clusteru Spark a možné spouštění interaktivních dotazů Spark SQL transformace, filtrovat a vizualizovat data uložená v úložišti objektů Blob v Azure. Fragmenty kódu Scala v tomto článku, které poskytují řešení a zobrazit příslušné grafy k vizualizaci dat spouštět v poznámkových blocích Jupyter nainstalované v clusterech Spark. Kroky modelování v těchto tématech mít kód, který popisuje, jak pro trénování, vyhodnocení, uložit a používat každý typ modelu.

Postup instalace a kód v tomto článku jsou pro Azure HDInsight 3.4 Spark 1.6. Ale kódu v tomto článku a [Poznámkový blok Jupyter Scala](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration%20Modeling%20and%20Scoring%20using%20Scala.ipynb) jsou obecné a by mělo fungovat jakéhokoli jiného clusteru Spark. Postup instalace a správy clusteru může být mírně liší, jak je zobrazeno v tomto článku, pokud nepoužíváte HDInsight Spark.

> [!NOTE]
> Téma, které se dozvíte, jak pomocí Pythonu, spíše než Scala k provedení úloh pro proces vědy Data začátku do konce, naleznete v tématu [vědecké zkoumání dat pomocí Sparku v Azure HDInsight](spark-overview.md).
> 
> 

## <a name="prerequisites"></a>Požadavky
* Mít předplatné Azure. Pokud již nemáte, [získat bezplatnou zkušební verzi Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Potřebujete cluster Azure HDInsight 3.4 Spark 1.6 dokončete následující postup. K vytvoření clusteru, postupujte podle pokynů v [Začínáme: vytvoření Apache Spark v Azure HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Nastavit typ clusteru a verzi na **vybrat typ clusteru** nabídky.

![Konfigurace clusteru typu HDInsight](./media/scala-walkthrough/spark-cluster-on-portal.png)

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
> 
> 

Popis data o jízdách taxislužby NYC a pokyny o tom, jak spustit kód z poznámkového bloku Jupyter pro Spark cluster, najdete v příslušných částech [přehled datové vědy pomocí Sparku v Azure HDInsight](spark-overview.md).  

## <a name="execute-scala-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Spustit z poznámkového bloku Jupyter pro Spark cluster Scala kódu
Můžete spustit Poznámkový blok Jupyter na webu Azure Portal. Najít cluster Spark na řídicím panelu a potom klikněte na něj přejděte na stránku správy pro váš cluster. Klepnutím na tlačítko **řídicí panely clusteru**a potom klikněte na tlačítko **Poznámkový blok Jupyter** otevřete Poznámkový blok přidružené ke clusteru Spark.

![Řídicí panel clusteru a poznámkové bloky Jupyter](./media/scala-walkthrough/spark-jupyter-on-portal.png)

Můžete také přejít poznámkové bloky Jupyter v https://&lt;clustername&gt;.azurehdinsight.net/jupyter. Nahraďte *clustername* s názvem vašeho clusteru. Budete potřebovat heslo pro účet správce pro přístup k poznámkové bloky Jupyter.

![Přejděte do poznámkových bloků Jupyter s použitím názvu clusteru](./media/scala-walkthrough/spark-jupyter-notebook.png)

Vyberte **Scala** zobrazíte adresář, který obsahuje několik příkladů předpřipravenou poznámkových bloků, které používají rozhraní API PySpark. Zkoumání modelování a vyhodnocování Scala.ipynb Poznámkový blok, který obsahuje kód pomocí ukázky pro tato sada témat Spark je k dispozici na [Githubu](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Scala).

Můžete nahrát ho přímo z Githubu na server poznámkového bloku Jupyter na svém clusteru Spark. Na domovské stránce Jupyter, klikněte na tlačítko **nahrát** tlačítko. V Průzkumníku souborů, vložte adresu URL Githubu (nezpracovaný obsah) poznámkového bloku Scala a pak klikněte na tlačítko **otevřít**. Poznámkový blok Scala je k dispozici na na následující adrese URL:

[Exploration-Modeling-and-Scoring-using-Scala.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb)

## <a name="setup-preset-spark-and-hive-contexts-spark-magics-and-spark-libraries"></a>Instalační program: Kontexty přednastavený kontext Spark a Hive, Magic Sparku a Spark knihovny
### <a name="preset-spark-and-hive-contexts"></a>Přednastavení kontexty Spark a Hive
    # SET THE START TIME
    import java.util.Calendar
    val beginningTime = Calendar.getInstance().getTime()


Spark jader, které jsou k dispozici s poznámkovými bloky Jupyter mají přednastavení kontexty. Není nutné explicitně nastavit Spark nebo vyvíjíte Hive kontexty před zahájením práce s aplikací. Přednastavených kontextech jsou:

* `sc` pro SparkContext
* `sqlContext` pro HiveContext

### <a name="spark-magics"></a>Spark Magic
Jádra Spark poskytuje některé předdefinované "Magic,", které jsou speciální příkazy, které lze volat s `%%`. Dva z těchto příkazů se používají v následující ukázky kódu.

* `%%local` Určuje, že kód v dalších řádcích se spustí místně. Kód musí být platný kód Scala.
* `%%sql -o <variable name>` Spustí dotaz Hive proti `sqlContext`. Pokud `-o` parametr se předává, výsledek dotazu se ukládají v `%%local` Scala kontextu jako datový rámec Spark.

Pro další informace o jádrech pro poznámkové bloky Jupyter a jejich předdefinované "magics", který voláte s `%%` (například `%%local`), najdete v článku [jádra dostupná pro poznámkové bloky Jupyter s HDInsight Spark Linux clusterů v HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

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

Chcete-li uložit modely nebo souborů v úložišti objektů Blob, správně zadat cestu. Odkazovat na výchozí kontejner připojené ke clusteru Spark pomocí cesty, který začíná `wasb:///`. Odkazovat na jiné umístění s využitím `wasb://`.

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


**Výstup:**

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

**Výstup:**

| fare_amount | passenger_count | tip_amount | šikmý |
| --- | --- | --- | --- |
|        13.5 |1.0 |2.9 |1.0 |
|        16.0 |2.0 |3.4 |1.0 |
|        10.5 |2.0 |1.0 |1.0 |

## <a name="data-exploration-and-visualization"></a>Zkoumání a vizualizace dat
Pokud přenesete data do Spark, je dalším krokem v procesu pro datové vědy získali lepší představu o datech prostřednictvím zkoumání a vizualizaci. V této části můžete prozkoumat data taxislužby města pomocí dotazů jazyka SQL. Výsledky pak importujte do datového rámce k vykreslení cíl proměnné a pomáhala potenciálním funkce pro vizuální kontrolu pomocí funkce Automatické vizualizace Jupyter.

### <a name="use-local-and-sql-magic-to-plot-data"></a>Použijte místní verzi a magický příkaz jazyka SQL k vykreslení dat
Ve výchozím nastavení výstup jakékoli fragmenty kódu, který můžete spustit z poznámkového bloku Jupyter je k dispozici v rámci kontextu relace, která se ukládají na pracovních uzlech. Pokud chcete uložit cesty k pracovním uzlům pro každý výpočet, a pokud všechna data, je třeba vaše výpočtu je k dispozici místně na uzel serveru Jupyter (která je k hlavnímu uzlu), můžete použít `%%local` magic spouštět fragment kódu aplikace Jupyter Server.

* **Magický příkaz jazyka SQL** (`%%sql`). Jádra HDInsight Spark podporuje dotazy HiveQL snadno vložené na kontext SQLContext. (`-o VARIABLE_NAME`) Argument ukládá jako datový rámec Pandas na serveru aplikace Jupyter výstup příkazu jazyka SQL. To znamená, že bude k dispozici v místním režimu.
* `%%local` **Magic**. `%%local` Magic spouští kód místně na serveru Jupyter je k hlavnímu uzlu clusteru HDInsight. Obvykle použijete `%%local` magic ve spojení s `%%sql` magic s `-o` parametru. `-o` Parametr by trvalý výstup příkazu jazyka SQL místně a potom `%%local` magic aktivuje další sadu fragment kódu pro místní spuštění proti výstup dotazy SQL, který se ukládají místně.

### <a name="query-the-data-by-using-sql"></a>Dotazování dat pomocí SQL
Tento dotaz načte cesty taxíkem částka tarif, osobní počet a velikost špičky.

    # RUN THE SQL QUERY
    %%sql -q -o sqlResults
    SELECT fare_amount, passenger_count, tip_amount, tipped FROM taxi_train WHERE passenger_count > 0 AND passenger_count < 7 AND fare_amount > 0 AND fare_amount < 200 AND payment_type in ('CSH', 'CRD') AND tip_amount > 0 AND tip_amount < 25

V následujícím kódu `%%local` magic vytvoří místní datový rámec, sqlResults. SqlResults můžete použít k vykreslení s použitím matplotlib.

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

* Table
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


**Výstup:**

![Tip částka histogramu](./media/scala-walkthrough/plot-tip-amount-histogram.png)

![Tip částka podle počtu osobní](./media/scala-walkthrough/plot-tip-amount-by-passenger-count.png)

![Tip velikost podle velikosti tarif](./media/scala-walkthrough/plot-tip-amount-by-fare-amount.png)

## <a name="create-features-and-transform-features-and-then-prep-data-for-input-into-modeling-functions"></a>Vytvoření funkcí a transformaci funkce a pak pro přípravu dat pro vstup do funkce modelování
U funkce Spark ML a MLlib založený na stromové architektuře modelování budete muset připravit cíl a funkcí pomocí různých technik, jako je například binning, indexování, jeden horkou kódování a vektorizaci. Tady jsou postupy v této části:

1. Vytvořit novou funkci ve **binning** hodin do provozu čas kbelíků.
2. Použít **indexování a za běhu jeden kódování** zařazené do kategorií funkcí.
3. **Ukázky a datové sady rozdělit** do zlomky trénování a testování.
4. **Zadejte proměnnou školení a funkce**a pak vytvořit indexované nebo horkou jeden kódování, školení a testování vstupního bodu s popiskem odolné distribuované sady rdd (Resilient) nebo datové rámce.
5. Automaticky **kategorizaci a vektorizovaná funkce a cíle** použít jako vstupy pro modely strojového učení.

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

Potřebujete indexovat nebo kódování vašich modelů různými způsoby v závislosti na modelu. Lineární a logistické regresní modely například vyžadovat jeden horkou kódování. Například funkce s tři kategorie se rozšířit do tří sloupců funkce. Každý sloupec obsahuje 0 nebo 1 podle kategorie hodnotu. Poskytuje MLlib [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) funkce pro jeden horkou kódování. Tomto kodéru mapuje sloupec indexů popisek ke sloupci binárního vektorů s maximálně jeden – hodnotu single. S toto kódování algoritmy, které očekávají číselnými hodnotami funkce, jako je logistické regrese, lze použít k funkcím zařazené do kategorií.

Tady můžete transformovat pouze čtyři proměnné zobrazíte příklady, které jsou řetězce znaků. Také můžete indexovat jiné proměnné, jako je například den v týdnu, reprezentovaný číselné hodnoty, jako proměnné zařazené do kategorií.

Pro indexování, použijte `StringIndexer()`a ten hot kódování, použijte `OneHotEncoder()` funkcí z MLlib. Tady je kód pro index a kódování funkcí zařazené do kategorií:

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


**Výstup:**

Čas spuštění buňku: 2 sekundy.

### <a name="specify-training-variable-and-features-and-then-create-indexed-or-one-hot-encoded-training-and-testing-input-labeled-point-rdds-or-data-frames"></a>Zadejte proměnnou školení a funkce a pak vytvořte indexovaných nebo horkou jeden kódovaný trénování a testování vstupního bodu Rdd nebo datového rámce s popiskem
Tato část obsahuje kód, který ukazuje, jak indexovat zařazené do kategorií textová data jako datový typ s popiskem bodu a jeho kódování, takže ho můžete použít pro trénování a testování logistické regrese MLlib a jiných modelů klasifikace. S popiskem bodových objektů jsou Rdd, které jsou formátovány způsobem, který je potřeba jako vstupní data pro většinu v MLlib algoritmů strojového učení. A [označené jako bod](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) je přidružený místní vektoru, dense nebo zhuštěný, popisek a odpovědí.

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


**Výstup:**

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

* A **model logistické regrese** pomocí Spark ML `LogisticRegression()` – funkce
* A **model klasifikace náhodné doménové struktury** pomocí Spark ML `RandomForestClassifier()` – funkce
* A **přechodu zvýšení skóre model klasifikace stromu** pomocí MLlib `GradientBoostedTrees()` – funkce

### <a name="create-a-logistic-regression-model"></a>Vytvořte Logistický regresní model
Dále vytvořte model logistické regrese pomocí Spark ML `LogisticRegression()` funkce. Vytvoříte model sestavování kódu v sérii kroků:

1. **Trénování modelu** dat pomocí jednu sadu parametrů.
2. **Vyhodnocení modelu** na testovací datové sady s metrikami.
3. **Uložit model** v úložišti objektů Blob pro budoucí použití.
4. **Modul score model** proti testovací data.
5. **Zobrazit výsledky** s příjemce provozních charakteristik (roc s více TŘÍDAMI) křivky.

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


**Výstup:**

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


**Výstup:**

![Popis tlačítka nebo žádná křivka roc s více TŘÍDAMI tip](./media/scala-walkthrough/plot-roc-curve-tip-or-not.png)

### <a name="create-a-random-forest-classification-model"></a>Vytvořte model klasifikace náhodné doménové struktury
Dále vytvořte model klasifikace náhodné doménové struktury pomocí Spark ML `RandomForestClassifier()` funkci a pak vyhodnotit model na testovací data.

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

Roc s více TŘÍDAMI v testovacích dat = 0.9847103571552683

### <a name="create-a-gbt-classification-model"></a>Vytvoření modelu GBT klasifikace
Dále vytvořte model klasifikace GBT pomocí vaší MLlib `GradientBoostedTrees()` funkci a pak vyhodnotit model na testovací data.

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

Oblasti pod křivkou roc s více TŘÍDAMI: 0.9846895479241554

## <a name="regression-model-predict-tip-amount"></a>Regresní model: předpověď velikost špičky
V této části vytvoříte dva typy regresní modely a vytvořte předpověď velikost špičky:

* A **trénování modelu lineární regrese Vyřešeno** pomocí Spark ML `LinearRegression()` funkce. Budete uložení modelu a vyhodnocení modelu na testovací data.
* A **zvýšení skóre přechodu stromu regresní model** pomocí Spark ML `GBTRegressor()` funkce.

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


**Výstup:**

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


**Výstup:**

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

Kód vytvoří místní datový rámec z výstupu dotazu a zobrazí data. `%%local` Magic vytvoří místní datový rámec, `sqlResults`, který můžete použít k vykreslení s matplotlib.

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

**Výstup:**

![Tip množství: skutečnost a předpokládané](./media/scala-walkthrough/plot-actual-vs-predicted-tip-amount.png)

### <a name="create-a-gbt-regression-model"></a>Vytvořte GBT regresní model
Vytvoření GBT regresní model pomocí Spark ML `GBTRegressor()` funkci a pak vyhodnotit model na testovací data.

[Vylepšené přechodu stromů](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) jsou umožňující rozhodovacích stromů. GBTs trénování rozhodovacích stromů zavádět postupně, chcete-li minimalizovat ztrátu funkce. Vám pomůže GBTs regrese a klasifikace. Jejich zvládne zařazené do kategorií funkcí, nevyžadují, aby funkce škálování a můžete zachytit nonlinearities a interakce funkce. Můžete také lze využít v nastavení multiclass klasifikace.

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

Je zkušební R-sqr: 0.7655383534596654

## <a name="advanced-modeling-utilities-for-optimization"></a>Nástroje pro pokročilé modelování pro optimalizaci
V této části použijte nástroje machine learning, které vývojáři často používají pro optimalizaci modelů. Konkrétně můžete optimalizovat modelů strojového učení třemi různými způsoby s použitím parametru sweeping a křížového ověřování:

* Rozdělení dat do sady pro trénování a ověřování, optimalizace modelu s použitím hyperparametrické sweeping na trénovací sady a vyhodnocení na sadě ověření (lineární regrese)
* Optimalizace modelu s použitím křížového ověření a hyperparametrické otevřeného pomocí funkce CrossValidator Spark ML (binární klasifikace)
* Optimalizace modelu pomocí vlastního kódu křížového ověření a parametr sweeping používat jakékoli strojového učení sadu funkcí a parametr (lineární regrese)

**Křížové ověření** je technika, která vyhodnocuje, jak dobře budou generalize modelů trénovaných na známé sady dat předpovědět funkcí datové sady, na kterých je nebyla cvičena. Obecnou myšlenku za tento postup je, že model se trénuje na datové sadě známých dat, a pak nemusí být jeho předpovědi je testován vůči nezávislé datové sady. Běžná implementace je rozdělit datové sady do *k*-zapnuta a potom trénování modelu způsobem kruhové dotazování na všechny kromě jednoho složení.

**Technologie Hyper parametr optimalizace** potížím, které vyberete sadu hyperparametry pro algoritmu učení, obvykle s cílem optimalizovat míru výkonu algoritmus v nezávislé datové sady. Technologie hyper parametr je hodnota, která je nutné zadat mimo proces trénování modelu. Předpoklady o hyperparametrické hodnoty může ovlivnit, flexibilitu a přesnost modelu. Rozhodovací stromy mají hyperparametry, například, jako je například požadovaný hloubky a počet listů ve stromové struktuře. Je nutné nastavit podmínku chybnou penalizace pro počítač vektorové podpory (SVM).

Běžný způsob, jak provádět optimalizaci hyperparametrické je vyhledat mřížky, zkratka **Uklízení parametrů**. V mřížce hledání podrobné prohledávání se provádí prostřednictvím hodnoty zadané dílčí hyperparametrické místa pro algoritmus učení. Křížového ověření může poskytovat metrika výkonu optimální výsledky vytvořené metodou mřížky vyhledávacího algoritmu řazení. Pokud používáte sweeping hyperparametrické křížového ověřování, pomáhá omezit problémy, například overfitting modelu na trénovací data. Tímto způsobem model uchovává kapacity použít na obecné sadu dat, ze které byl extrahován trénovací data.

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


**Výstup:**

Je zkušební R-sqr: 0.6226484708501209

### <a name="optimize-the-binary-classification-model-by-using-cross-validation-and-hyper-parameter-sweeping"></a>Optimalizovat binární klasifikační model s použitím křížového ověření a hyperparametrické sweeping
V této části se dozvíte, jak optimalizovat binární klasifikační model s použitím křížového ověření a hyperparametrické sweeping. Tento mechanismus využívá Spark ML `CrossValidator` funkce.

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


**Výstup:**

Čas spuštění buňku: 61 sekund.

## <a name="consume-spark-built-machine-learning-models-automatically-with-scala"></a>Použití modelů předdefinovaných ve Spark machine learning automaticky pomocí Scala
Přehled o tématech, které vás provedou úkoly, které tvoří procesu pro datové vědy v Azure, najdete v části [vědecké zpracování týmových dat](https://aka.ms/datascienceprocess).

[Průvodce vědeckým zpracováním dat týmu](walkthroughs.md) popisuje další začátku do konce postupy, které popisují kroky v vědecké zpracování týmových dat pro konkrétní scénáře. Názorné postupy také ukazují, jak zkombinovat cloudové a místní nástroje a služby do pracovního postupu nebo kanálu pro vytváření inteligentních aplikací.

[Hodnotit modely předdefinovaných ve Spark machine learning](spark-model-consumption.md) ukazuje, jak pomocí Scala kódu se automaticky načíst a stanovíte jeho skóre nové datové sady s modely machine learning součástí Spark a uložen v úložišti objektů Blob v Azure. Můžete podle pokynů uvedených zde a jednoduše nahradit Scala kódu v tomto článku pro automatické použití kódu Pythonu.

