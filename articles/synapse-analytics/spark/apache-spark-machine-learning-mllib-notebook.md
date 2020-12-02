---
title: 'Kurz: Vytvoření aplikace Machine Learning pomocí Apache Spark MLlib'
description: V tomto kurzu se dozvíte, jak pomocí Apache Spark MLlib vytvořit aplikaci Machine Learning, která analyzuje datovou sadu pomocí klasifikace prostřednictvím logistické regrese.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: jrasnick
ms.topic: tutorial
ms.subservice: machine-learning
ms.date: 04/15/2020
ms.author: euang
ms.openlocfilehash: 50429696c4cbe10c4723f6d4bb9c9499d9b775c5
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96450415"
---
# <a name="tutorial-build-a-machine-learning-app-with-apache-spark-mllib-and-azure-synapse-analytics"></a>Kurz: Vytvoření aplikace Machine Learning pomocí Apache Spark MLlib a Azure synapse Analytics

V tomto článku se naučíte, jak pomocí Apache Spark [MLlib](https://spark.apache.org/mllib/) vytvořit aplikaci pro strojové učení, která provádí jednoduchou prediktivní analýzu na otevřené datové sadě Azure. Spark nabízí integrované knihovny machine learningu. V tomto příkladu se používá *klasifikace* prostřednictvím logistické regrese.

SparkML a MLlib jsou základní knihovny Sparku, které poskytují mnoho nástrojů, které jsou užitečné pro úlohy strojového učení, včetně nástrojů, které jsou vhodné pro:

- Classification
- Regrese
- Clustering
- Modelování tématu
- Dekompozice hodnot v číslech (SVD) a analýza hlavních komponent (DPS)
- Testování hypotéz a výpočet ukázkových statistik

## <a name="understand-classification-and-logistic-regression"></a>Pochopení klasifikace a logistické regrese

*Klasifikace*, oblíbená úloha strojového učení, je proces řazení vstupních dat do kategorií. Je to úloha klasifikačního algoritmu k tomu, abyste zjistili, jak přiřadit *popisky* k určeným vstupním datům. Můžete si například představit algoritmus strojového učení, který přijímá skladové informace jako vstup a vydělí akcie na dvě kategorie: akcie, které byste měli prodávat, a zásoby, které byste měli zachovat.

*Logistická regrese* je algoritmus, který můžete použít pro klasifikaci. Rozhraní API pro logistické regrese Spark je užitečné pro *binární klasifikaci* nebo pro klasifikaci vstupních dat do jedné ze dvou skupin. Další informace o logistických regresích najdete v tématu [Wikipedii](https://en.wikipedia.org/wiki/Logistic_regression).

V souhrnu proces logistické regrese vytváří *logistické funkce* , které lze použít k předpovědi pravděpodobnosti, že vstupní vektor patří do jedné nebo druhé skupiny.

## <a name="predictive-analysis-example-on-nyc-taxi-data"></a>Příklad prediktivní analýzy na NYC taxislužby data

V tomto příkladu pomocí Sparku provedete určitou prediktivní analýzu dat Tip taxislužby Trip z New York. Data jsou dostupná prostřednictvím [Azure Open DataSet](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). Tato podmnožina DataSet obsahuje informace o žluté taxislužby TRIPS, včetně informací o jednotlivých cestách, počátečním a koncovém času a umístěních, nákladech a dalších zajímavých atributů.

> [!IMPORTANT]
> Při přijímání těchto dat z umístění úložiště se můžou účtovat další poplatky.

V následujících krocích vyvíjíte model, který předpovídá, jestli konkrétní cesta obsahuje Tip nebo ne.

## <a name="create-an-apache-spark--machine-learning-model"></a>Vytvoření modelu Apache Spark Machine Learning

1. Pomocí jádra PySpark vytvořte Poznámkový blok. Pokyny najdete v tématu [vytvoření poznámkového bloku](../quickstart-apache-spark-notebook.md#create-a-notebook).
2. Importujte typy požadované pro tuto aplikaci. Zkopírujte a vložte následující kód do prázdné buňky a stiskněte klávesy **SHIFT + ENTER** nebo buňku spusťte pomocí ikony modrého přehrání nalevo od kódu.

    ```python
    import matplotlib.pyplot as plt
    from datetime import datetime
    from dateutil import parser
    from pyspark.sql.functions import unix_timestamp, date_format, col, when
    from pyspark.ml import Pipeline
    from pyspark.ml import PipelineModel
    from pyspark.ml.feature import RFormula
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorIndexer
    from pyspark.ml.classification import LogisticRegression
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.ml.evaluation import BinaryClassificationEvaluator
    ```

    Z důvodu jádra PySpark nemusíte vytvářet žádné kontexty explicitně. Kontext Spark je automaticky vytvořen za vás při spuštění první buňky kódu.

## <a name="construct-the-input-dataframe"></a>Sestavit vstupní datový rámec

Vzhledem k tomu, že nezpracovaná data jsou ve formátu Parquet, můžete pomocí kontextu Spark načíst soubor do paměti jako datový rámec přímo. Zatímco následující kód používá výchozí možnosti, je možné vynutit mapování datových typů a dalších atributů schématu v případě potřeby.

1. Spusťte následující řádky a vytvořte tak datový rámec Spark vložením kódu do nové buňky. To načte data prostřednictvím rozhraní API Open DataSet. Po přijetí všech těchto dat se vygeneruje přibližně 1 500 000 000 řádků. V závislosti na velikosti vašeho fondu Apache Spark bez serveru může být nezpracovaná data příliš velká nebo mohou trvat příliš dlouho, než budou fungovat. Tato data můžete filtrovat dolů na něco menšího. Následující příklad kódu používá start_date a end_date k použití filtru, který vrací jeden měsíc dat.

    ```python
    from azureml.opendatasets import NycTlcYellow

    end_date = parser.parse('2018-06-06')
    start_date = parser.parse('2018-05-01')
    nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
    filtered_df = nyc_tlc.to_spark_dataframe()
    ```

2. Nevýhodou jednoduchého filtrování je, že ze statistické perspektivy může dojít k posunu dat. Dalším přístupem je použití vzorkování integrovaných do Sparku. Následující kód zmenší datovou sadu dolů na přibližně 2000 řádků, pokud se použije po výše uvedeném kódu. Tento krok vzorkování se dá použít místo jednoduchého filtru nebo ve spojení s jednoduchým filtrem.

    ```python
    # To make development easier, faster and less expensive down sample for now
    sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)
    ```

3. Nyní se můžete podívat na data, abyste viděli, co bylo čteno. Obvykle je lepší zkontrolovat data s podmnožinou a nikoli plnou sadou v závislosti na velikosti datové sady. Následující kód nabízí dva způsoby, jak zobrazit data: bývalá základní a druhá z nich poskytuje mnohem bohatší možnosti práce s mřížkou a také možnost vizualizovat data graficky.

    ```python
    #sampled_taxi_df.show(5)
    display(sampled_taxi_df)
    ```

4. V závislosti na velikosti generované datové sady a potřebě experimentu nebo spuštění poznámkového bloku může být vhodné datovou sadu ukládat do mezipaměti místně v pracovním prostoru. Existují tři způsoby, jak provést explicitní ukládání do mezipaměti:

   - Uložit datový rámec místně jako soubor
   - Uložit datový rámec jako dočasnou tabulku nebo zobrazení
   - Uložit datový rámec jako trvalou tabulku

První 2 z těchto přístupů jsou obsaženy v následujících příkladech kódu.

Vytvoření dočasné tabulky nebo zobrazení poskytuje různé přístupové cesty k datům, ale trvá pouze po dobu trvání relace instance Spark.

```Python
sampled_taxi_df.createOrReplaceTempView("nytaxi")
```

## <a name="prepare-the-data"></a>Příprava dat

Data v jejich nezpracované podobě nejsou často vhodná pro přímý přenos modelu. Pro získání dat do stavu, ve kterém ho model může spotřebovat, je nutné provést sérii akcí.

V kódu níže jsou provedeny čtyři třídy operací:

- Odebrání neplatných nebo nesprávných hodnot prostřednictvím filtrování.
- Odebrání sloupců, které nejsou potřeba.
- Vytvořením nových sloupců odvozených z nezpracovaných dat zajistíte efektivnější fungování modelu, někdy označovaného jako featurization.
- Labeling – vzhledem k tomu, že zadáváte binární klasifikaci (bude existovat Tip nebo není v dané cestě), je nutné převést částku tipu na 0 nebo 1 hodnotu.

```python
taxi_df = sampled_taxi_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'rateCodeId', 'passengerCount'\
                                , 'tripDistance', 'tpepPickupDateTime', 'tpepDropoffDateTime'\
                                , date_format('tpepPickupDateTime', 'hh').alias('pickupHour')\
                                , date_format('tpepPickupDateTime', 'EEEE').alias('weekdayString')\
                                , (unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('tripTimeSecs')\
                                , (when(col('tipAmount') > 0, 1).otherwise(0)).alias('tipped')
                                )\
                        .filter((sampled_taxi_df.passengerCount > 0) & (sampled_taxi_df.passengerCount < 8)\
                                & (sampled_taxi_df.tipAmount >= 0) & (sampled_taxi_df.tipAmount <= 25)\
                                & (sampled_taxi_df.fareAmount >= 1) & (sampled_taxi_df.fareAmount <= 250)\
                                & (sampled_taxi_df.tipAmount < sampled_taxi_df.fareAmount)\
                                & (sampled_taxi_df.tripDistance > 0) & (sampled_taxi_df.tripDistance <= 100)\
                                & (sampled_taxi_df.rateCodeId <= 5)
                                & (sampled_taxi_df.paymentType.isin({"1", "2"}))
                                )
```

Druhý průchod pak provede data a přidá konečné funkce.

```Python
taxi_featurised_df = taxi_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'passengerCount'\
                                                , 'tripDistance', 'weekdayString', 'pickupHour','tripTimeSecs','tipped'\
                                                , when((taxi_df.pickupHour <= 6) | (taxi_df.pickupHour >= 20),"Night")\
                                                .when((taxi_df.pickupHour >= 7) & (taxi_df.pickupHour <= 10), "AMRush")\
                                                .when((taxi_df.pickupHour >= 11) & (taxi_df.pickupHour <= 15), "Afternoon")\
                                                .when((taxi_df.pickupHour >= 16) & (taxi_df.pickupHour <= 19), "PMRush")\
                                                .otherwise(0).alias('trafficTimeBins')
                                              )\
                                       .filter((taxi_df.tripTimeSecs >= 30) & (taxi_df.tripTimeSecs <= 7200))
```

## <a name="create-a-logistic-regression-model"></a>Vytvoření modelu logistické regrese

Posledním úkolem je převést označené údaje do formátu, který lze analyzovat logistickou regresí. Vstup do algoritmu logistické regrese musí být sada *vektorových dvojic popisků*, kde je *vektor funkce* vektor čísel reprezentujících vstupní bod. Proto musíme sloupce kategorií převést na čísla. `trafficTimeBins`Sloupce a se `weekdayString` musí převést na celočíselné reprezentace. K převodu je k dispozici několik přístupů, ale přístup v tomto příkladu je *OneHotEncoding*, což je běžný přístup.

```python
# Since the sample uses an algorithm that only works with numeric features, convert them so they can be consumed
sI1 = StringIndexer(inputCol="trafficTimeBins", outputCol="trafficTimeBinsIndex")
en1 = OneHotEncoder(dropLast=False, inputCol="trafficTimeBinsIndex", outputCol="trafficTimeBinsVec")
sI2 = StringIndexer(inputCol="weekdayString", outputCol="weekdayIndex")
en2 = OneHotEncoder(dropLast=False, inputCol="weekdayIndex", outputCol="weekdayVec")

# Create a new dataframe that has had the encodings applied
encoded_final_df = Pipeline(stages=[sI1, en1, sI2, en2]).fit(taxi_featurised_df).transform(taxi_featurised_df)
```

Výsledkem této akce je nový datový rámec, ve kterém jsou všechny sloupce ve správném formátu pro výuku modelu.

## <a name="train-a-logistic-regression-model"></a>Školení modelu logistické regrese

Prvním úkolem je rozdělit datovou sadu do sady školení a sady testování nebo ověření. Rozdělení tady je libovolné a měli byste se s různými nastaveními rozdělení pohybovat, abyste viděli, jestli mají vliv na model.

```python
#Decide on the split between training and testing data from the dataframe
trainingFraction = 0.7
testingFraction = (1-trainingFraction)
seed = 1234

# Split the dataframe into test and training dataframes
train_data_df, test_data_df = encoded_final_df.randomSplit([trainingFraction, testingFraction], seed=seed)
```

Teď, když existují dva datarámce, je dalším úkolem vytvořit vzor modelu a spustit ho v rámci školení dataframe a pak ověřit proti testovacímu datasnímku. Měli byste experimentovat s různými verzemi vzorce modelu, abyste viděli dopad různých kombinací.

> [!Note]
> Pokud chcete model uložit, budete potřebovat roli Azure pro přispěvatele dat objektů BLOB úložiště. V části účet úložiště přejděte na Access Control (IAM) a vyberte **Přidat přiřazení role**. Přiřaďte roli Azure Přispěvatel dat objektů BLOB úložiště k vašemu serveru SQL Database. Tento krok mohou provádět pouze členové s oprávněním vlastníka. Informace o různých předdefinovaných rolích Azure najdete v tomto [Průvodci](../../role-based-access-control/built-in-roles.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```python
## Create a new LR object for the model
logReg = LogisticRegression(maxIter=10, regParam=0.3, labelCol = 'tipped')

## The formula for the model
classFormula = RFormula(formula="tipped ~ pickupHour + weekdayVec + passengerCount + tripTimeSecs + tripDistance + fareAmount + paymentType+ trafficTimeBinsVec")

## Undertake training and create an LR model
lrModel = Pipeline(stages=[classFormula, logReg]).fit(train_data_df)

## Saving the model is optional but its another form of inter session cache
datestamp = datetime.now().strftime('%m-%d-%Y-%s')
fileName = "lrModel_" + datestamp
logRegDirfilename = fileName
lrModel.save(logRegDirfilename)

## Predict tip 1/0 (yes/no) on the test dataset, evaluation using AUROC
predictions = lrModel.transform(test_data_df)
predictionAndLabels = predictions.select("label","prediction").rdd
metrics = BinaryClassificationMetrics(predictionAndLabels)
print("Area under ROC = %s" % metrics.areaUnderROC)
```

Výstup z této buňky je:

```shell
Area under ROC = 0.9779470729751403
```

## <a name="create-a-visual-representation-of-the-prediction"></a>Vytvoření vizuální reprezentace předpovědi

Nyní můžete vytvořit konečnou vizualizaci, která vám pomůžete v důsledku výsledků tohoto testu. [Křivka Roc](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) je jedním ze způsobů, jak zkontrolovat výsledek.

```python
## Plot the ROC curve, no need for pandas as this uses the modelSummary object
modelSummary = lrModel.stages[-1].summary

plt.plot([0, 1], [0, 1], 'r--')
plt.plot(modelSummary.roc.select('FPR').collect(),
         modelSummary.roc.select('TPR').collect())
plt.xlabel('False Positive Rate')
plt.ylabel('True Positive Rate')
plt.show()
```

![Křivka ROC pro model špičky logistické regrese](./media/apache-spark-machine-learning-mllib-notebook/nyc-taxi-roc.png)

## <a name="shut-down-the-spark-instance"></a>Vypnutí instance Spark

Po dokončení používání aplikace vypněte Poznámkový blok a uvolněte ho tak, že zavřete kartu nebo v dolní části poznámkového bloku vyberete **ukončit relaci** ze stavového panelu.

## <a name="see-also"></a>Viz také

- [Přehled: Apache Spark ve službě Azure synapse Analytics](apache-spark-overview.md)

## <a name="next-steps"></a>Další kroky

- [Dokumentace k rozhraní .NET pro Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Apache Spark oficiální dokumentace](https://spark.apache.org/docs/2.4.5/)

>[!NOTE]
> Některé oficiální dokumentace Apache Spark spoléhá na použití konzoly Spark, která není v Azure synapse Spark dostupná. Místo toho používejte [Poznámkový blok](../quickstart-apache-spark-notebook.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) nebo [IntelliJ](../spark/intellij-tool-synapse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) .
