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
ms.openlocfilehash: 39ba8a5884abb4be9fa0b8e32a292e06738e1550
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98935648"
---
# <a name="tutorial-build-a-machine-learning-app-with-apache-spark-mllib-and-azure-synapse-analytics"></a>Kurz: Vytvoření aplikace Machine Learning pomocí Apache Spark MLlib a Azure synapse Analytics

V tomto článku se naučíte, jak pomocí Apache Spark [MLlib](https://spark.apache.org/mllib/) vytvořit aplikaci pro strojové učení, která provádí jednoduchou prediktivní analýzu na otevřené datové sadě Azure. Spark nabízí integrované knihovny machine learningu. V tomto příkladu se používá *klasifikace* prostřednictvím logistické regrese.

SparkML a MLlib jsou základní knihovny Sparku, které poskytují mnoho nástrojů, které jsou užitečné pro úlohy strojového učení, včetně nástrojů, které jsou vhodné pro:

- Klasifikace
- Regrese
- Clustering
- Modelování tématu
- Dekompozice hodnot v číslech (SVD) a analýza hlavních komponent (DPS)
- Testování hypotéz a výpočet ukázkových statistik

## <a name="understand-classification-and-logistic-regression"></a>Pochopení klasifikace a logistické regrese

*Klasifikace*, oblíbená úloha strojového učení, je proces řazení vstupních dat do kategorií. Je to úloha klasifikačního algoritmu k tomu, abyste zjistili, jak přiřadit *popisky* k vstupním datům, která zadáte. Můžete si například představit algoritmus strojového učení, který přijímá skladové informace jako vstup a dělí sklady do dvou kategorií: zásob, které byste měli prodávat, a zásob, které byste měli zachovat.

*Logistická regrese* je algoritmus, který můžete použít pro klasifikaci. Rozhraní API pro logistické regrese Spark je užitečné pro *binární klasifikaci* nebo pro klasifikaci vstupních dat do jedné ze dvou skupin. Další informace o logistické regresi najdete v tématu [Wikipedii](https://en.wikipedia.org/wiki/Logistic_regression).

V souhrnu proces logistické regrese vytváří *logistickou funkci* , kterou můžete použít k předpovědi pravděpodobnosti, že vstupní vektor patří do jedné nebo druhé skupiny.

## <a name="predictive-analysis-example-on-nyc-taxi-data"></a>Příklad prediktivní analýzy na NYC taxislužby data

V tomto příkladu pomocí Sparku provedete určitou prediktivní analýzu dat taxislužby s špičkou v New Yorku. Data jsou dostupná prostřednictvím [Azure Open DataSet](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). Tato podmnožina DataSet obsahuje informace o žluté taxislužby TRIPS, včetně informací o jednotlivých cestách, počátečním a koncovém času a umístěních, nákladech a dalších zajímavých atributů.

> [!IMPORTANT]
> Při načítání těchto dat ze svého umístění úložiště se můžou účtovat další poplatky.

V následujících krocích vyvíjíte model, který předpovídá, jestli konkrétní cesta obsahuje Tip nebo ne.

## <a name="create-an-apache-spark-machine-learning-model"></a>Vytvoření modelu Apache Spark Machine Learning

1. Pomocí jádra PySpark vytvořte Poznámkový blok. Pokyny najdete v tématu [vytvoření poznámkového bloku](../quickstart-apache-spark-notebook.md#create-a-notebook).
2. Importujte typy požadované pro tuto aplikaci. Zkopírujte a vložte následující kód do prázdné buňky a stiskněte klávesu SHIFT + ENTER. Nebo spusťte buňku pomocí modré ikony přehrání nalevo od kódu.

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

Vzhledem k tomu, že nezpracovaná data jsou ve formátu Parquet, můžete pomocí kontextu Spark načíst soubor do paměti jako datový rámec přímo. I když kód v následujících krocích používá výchozí možnosti, je možné v případě potřeby vynutit mapování datových typů a dalších atributů schématu.

1. Spusťte následující řádky a vytvořte tak datový rámec Spark vložením kódu do nové buňky. Tento krok načte data prostřednictvím rozhraní API Open DataSet. Po přijetí všech těchto dat se vygeneruje přibližně 1 500 000 000 řádků. 

   V závislosti na velikosti vašeho fondu Apache Spark bez serveru může být nezpracovaná data příliš velká nebo mohou trvat příliš dlouho, než budou fungovat. Tato data můžete filtrovat dolů na něco menšího. Následující příklad kódu používá `start_date` a `end_date` k použití filtru, který vrací jeden měsíc dat.

    ```python
    from azureml.opendatasets import NycTlcYellow

    end_date = parser.parse('2018-06-06')
    start_date = parser.parse('2018-05-01')
    nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
    filtered_df = nyc_tlc.to_spark_dataframe()
    ```

2. Nevýhodou jednoduchého filtrování je, že ze statistické perspektivy může dojít k posunu dat. Dalším přístupem je použití vzorkování integrovaných do Sparku. 

   Následující kód snižuje datovou sadu na přibližně 2 000 řádků, pokud se použije po předchozím kódu. Tento krok vzorkování můžete použít místo jednoduchého filtru nebo ve spojení s jednoduchým filtrem.

    ```python
    # To make development easier, faster, and less expensive, downsample for now
    sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)
    ```

3. Teď se můžete podívat na data, abyste viděli, co bylo čteno. Obvykle je lepší zkontrolovat data s podmnožinou a nikoli plnou sadou v závislosti na velikosti datové sady. 

   Následující kód nabízí dva způsoby, jak zobrazit data. První způsob je základní. Druhý způsob nabízí mnohem bohatší možnosti práce s mřížkou, společně s možností vizualizace dat graficky.

    ```python
    #sampled_taxi_df.show(5)
    display(sampled_taxi_df)
    ```

4. V závislosti na velikosti generované datové sady a potřebě experimentu nebo spuštění poznámkového bloku můžete chtít datovou sadu ukládat do mezipaměti místně v pracovním prostoru. Existují tři způsoby, jak provést explicitní ukládání do mezipaměti:

   - Uložte datový rámec místně jako soubor.
   - Uložte datový rámec jako dočasnou tabulku nebo zobrazení.
   - Uložte datový rámec jako trvalou tabulku.

První dva z těchto přístupů jsou uvedeny v následujících příkladech kódu.

Vytvoření dočasné tabulky nebo zobrazení poskytuje různé přístupové cesty k datům, ale trvá pouze po dobu trvání relace instance Spark.

```Python
sampled_taxi_df.createOrReplaceTempView("nytaxi")
```

## <a name="prepare-the-data"></a>Příprava dat

Data v jejím nezpracovaném formuláři nejsou často vhodná pro přímé předávání do modelu. Je nutné provést sérii akcí s daty a získat tak stav, ve kterém ho model dokáže spotřebovat.

V následujícím kódu provedete čtyři třídy operací:

- Odebrání mimo jiné nebo nesprávné hodnoty prostřednictvím filtrování.
- Odebrání sloupců, které nejsou potřeba.
- Vytvořením nových sloupců odvozených z nezpracovaných dat zajistíte efektivnější fungování modelu. Tato operace se někdy označuje jako featurization.
- Označování. Vzhledem k tomu, že zadáváte binární klasifikaci (bude existovat Tip nebo není v dané cestě), je nutné převést částku tipu na hodnotu 0 nebo 1.

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

Pak provedete druhý průchod daty a přidáte konečné funkce.

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

Posledním úkolem je převést označené údaje do formátu, který lze analyzovat prostřednictvím logistické regrese. Vstup do algoritmu logistické regrese musí být sada *dvojic vektorů popisků a funkcí*, kde *vektor funkce* je vektor čísel, která představuje vstupní bod. 

Proto je nutné převést sloupce kategorií na čísla. Konkrétně je nutné převést `trafficTimeBins` sloupce a na `weekdayString` celočíselné reprezentace. Je k dispozici několik přístupů k převodu. Následující příklad přijímá `OneHotEncoder` přístup, což je běžné.

```python
# Because the sample uses an algorithm that works only with numeric features, convert them so they can be consumed
sI1 = StringIndexer(inputCol="trafficTimeBins", outputCol="trafficTimeBinsIndex")
en1 = OneHotEncoder(dropLast=False, inputCol="trafficTimeBinsIndex", outputCol="trafficTimeBinsVec")
sI2 = StringIndexer(inputCol="weekdayString", outputCol="weekdayIndex")
en2 = OneHotEncoder(dropLast=False, inputCol="weekdayIndex", outputCol="weekdayVec")

# Create a new DataFrame that has had the encodings applied
encoded_final_df = Pipeline(stages=[sI1, en1, sI2, en2]).fit(taxi_featurised_df).transform(taxi_featurised_df)
```

Výsledkem této akce je nový datový rámec, ve kterém jsou všechny sloupce ve správném formátu pro výuku modelu.

## <a name="train-a-logistic-regression-model"></a>Školení modelu logistické regrese

Prvním úkolem je rozdělit datovou sadu do sady školení a sady testování nebo ověření. Rozdělení tady je libovolné. Experimentujte s různými nastaveními rozdělení, abyste viděli, jestli mají vliv na model.

```python
# Decide on the split between training and testing data from the DataFrame
trainingFraction = 0.7
testingFraction = (1-trainingFraction)
seed = 1234

# Split the DataFrame into test and training DataFrames
train_data_df, test_data_df = encoded_final_df.randomSplit([trainingFraction, testingFraction], seed=seed)
```

Teď, když existují dva datarámce, je dalším úkolem vytvořit vzor modelu a spustit ho v rámci školicích kurzů. Pak můžete ověřit proti testovacímu dataframe. Experimentujte s různými verzemi vzorce modelu, abyste viděli dopad různých kombinací.

> [!Note]
> Pokud chcete model uložit, budete potřebovat roli Azure pro *přispěvatele dat objektů BLOB úložiště* . V rámci svého účtu úložiště, přejít na **Access Control (IAM)** a vyberte **Přidat přiřazení role**. Přiřaďte roli Přispěvatel dat objektů BLOB úložiště k vašemu serveru Azure SQL Database. Tento krok mohou provádět pouze členové s oprávněními vlastníka. 
>
>Informace o různých předdefinovaných rolích Azure najdete v [této příručce](../../role-based-access-control/built-in-roles.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```python
## Create a new logistic regression object for the model
logReg = LogisticRegression(maxIter=10, regParam=0.3, labelCol = 'tipped')

## The formula for the model
classFormula = RFormula(formula="tipped ~ pickupHour + weekdayVec + passengerCount + tripTimeSecs + tripDistance + fareAmount + paymentType+ trafficTimeBinsVec")

## Undertake training and create a logistic regression model
lrModel = Pipeline(stages=[classFormula, logReg]).fit(train_data_df)

## Saving the model is optional, but it's another form of inter-session cache
datestamp = datetime.now().strftime('%m-%d-%Y-%s')
fileName = "lrModel_" + datestamp
logRegDirfilename = fileName
lrModel.save(logRegDirfilename)

## Predict tip 1/0 (yes/no) on the test dataset; evaluation using area under ROC
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
## Plot the ROC curve; no need for pandas, because this uses the modelSummary object
modelSummary = lrModel.stages[-1].summary

plt.plot([0, 1], [0, 1], 'r--')
plt.plot(modelSummary.roc.select('FPR').collect(),
         modelSummary.roc.select('TPR').collect())
plt.xlabel('False Positive Rate')
plt.ylabel('True Positive Rate')
plt.show()
```

![Graf, který zobrazuje křivku ROC pro logistické regrese v modelu Tip.](./media/apache-spark-machine-learning-mllib-notebook/nyc-taxi-roc.png)

## <a name="shut-down-the-spark-instance"></a>Vypnutí instance Spark

Po dokončení spuštění aplikace vypněte Poznámkový blok a uvolněte ho zavřením karty. Nebo v dolní části poznámkového bloku vyberte **ukončit relaci** z panelu stavu.

## <a name="see-also"></a>Viz také

- [Přehled: Apache Spark ve službě Azure synapse Analytics](apache-spark-overview.md)

## <a name="next-steps"></a>Další kroky

- [Dokumentace k rozhraní .NET pro Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](../index.yml)
- [Apache Spark oficiální dokumentace](https://spark.apache.org/docs/2.4.5/)

>[!NOTE]
> Některé oficiální dokumentace Apache Spark spoléhá na použití konzoly Spark, která není k dispozici v Apache Spark ve službě Azure synapse Analytics. Místo toho používejte [Poznámkový blok](../quickstart-apache-spark-notebook.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) nebo [IntelliJ](../spark/intellij-tool-synapse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) .