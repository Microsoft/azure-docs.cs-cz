---
title: Vytvoření aplikace pro strojové učení s Apache Spark MLlib a Azure Synapse Analytics
description: Naučte se používat Apache Spark MLlib k vytvoření aplikace pro strojové učení, která analyzuje datovou sadu pomocí klasifikace prostřednictvím logistické regrese.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: jrasnick, carlrab
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: euang
ms.openlocfilehash: 9dc4047b9e95b088bb614858091f43286cefe361
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430003"
---
# <a name="build-a-machine-learning-app-with-apache-spark-mllib-and-azure-synapse-analytics"></a>Vytvoření aplikace pro strojové učení s Apache Spark MLlib a Azure Synapse Analytics

V tomto článku se dozvíte, jak pomocí Apache Spark [MLlib](https://spark.apache.org/mllib/) vytvořit aplikaci strojového učení, která provádí jednoduchou prediktivní analýzu na otevřené datové sadě Azure. Spark poskytuje integrované knihovny strojového učení. Tento příklad používá *klasifikaci* prostřednictvím logistické regrese.

MLlib je základní knihovna Spark, která poskytuje mnoho nástrojů, které jsou užitečné pro úlohy strojového učení, včetně nástrojů, které jsou vhodné pro:

- Classification
- Regrese
- Clustering
- Modelování témat
- Rozklad singulární hodnoty (SVD) a analýza hlavních součástí (PCA)
- Testování hypotéz a výpočet statistiky vzorků

## <a name="understand-classification-and-logistic-regression"></a>Porozumět klasifikaci a logistické regresi

*Klasifikace*, populární úloha strojového učení, je proces třídění vstupních dat do kategorií. Úlohou klasifikačního algoritmu je zjistit, jak přiřadit *popisky* vstupním datům, která zadáte. Můžete si například myslet algoritmus strojového učení, který přijímá informace o akciích jako vstup a rozděluje akcie do dvou kategorií: akcie, které byste měli prodat, a akcie, které byste měli zachovat.

*Logistická regrese* je algoritmus, který můžete použít pro klasifikaci. Logistické regresní rozhraní API Spark je užitečné pro *binární klasifikaci*nebo klasifikaci vstupních dat do jedné ze dvou skupin. Další informace o logistických regresích naleznete na [Wikipedii](https://en.wikipedia.org/wiki/Logistic_regression).

Stručně řečeno, proces logistické regrese vytváří *logistickou funkci,* kterou lze použít k předvídání pravděpodobnosti, že vstupní vektor patří do jedné nebo druhé skupiny.

## <a name="predictive-analysis-example-on-nyc-taxi-data"></a>Příklad prediktivní analýzy na datech NYC Taxi

V tomto příkladu použijete Spark k provedení prediktivní analýzy dat tipu taxi z New Yorku. Data jsou dostupná prostřednictvím [otevřených datových sad Azure](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). Tato podmnožina datové sady obsahuje informace o žlutých cestách taxíkem, včetně informací o každé cestě, počátečním a koncovém čase a místech, nákladech a dalších zajímavých atributech.

> [!IMPORTANT]
> Za vytažení těchto dat z místa úložiště mohou být účtovány další poplatky.

V následujících krocích vyvíjíte model, který předpovídá, zda určitá cesta obsahuje tip nebo ne.

## <a name="create-an-apache-spark-mllib-machine-learning-app"></a>Vytvoření aplikace apache spark mllib strojového učení

1. Vytvořte poznámkový blok pomocí jádra PySpark. Pokyny najdete v [tématu Vytvoření poznámkového bloku](./apache-spark-notebook-create-spark-use-sql.md#create-a-notebook).
2. Importujte typy požadované pro tuto aplikaci. Zkopírujte a vložte následující kód do prázdné buňky a stiskněte **klávesu SHIFT + ENTER**nebo ji spusťte pomocí modré ikony přehrávání nalevo od kódu.

    ```python
    import matplotlib.pyplot as plt
    from datetime import datetime
    from dateutil import parser
    from pyspark.sql.functions import unix_timestamp
    from pyspark.ml import Pipeline
    from pyspark.ml import PipelineModel
    from pyspark.ml.feature import RFormula
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorIndexer
    from pyspark.ml.classification import LogisticRegression
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.ml.evaluation import BinaryClassificationEvaluator
    ```

    Z důvodu jádra PySpark není nutné explicitně vytvářet žádné kontexty. Kontext Spark se automaticky vytvoří při spuštění první buňky kódu.

## <a name="construct-the-input-dataframe"></a>Vytvoření vstupního datového rámce

Vzhledem k tomu, že nezpracovaná data jsou ve formátu parket, můžete použít kontext Spark k přímému vytažení souboru do paměti jako datového rámce. Zatímco níže uvedený kód používá výchozí možnosti, je možné vpřípadě potřeby vynutit mapování datových typů a dalších atributů schématu.

1. Spusťte následující řádky a vytvořte datový rámec Spark vložením kódu do nové buňky. První část přiřazuje proměnné informace o přístupu k úložišti Azure. Druhá část umožňuje Spark číst z úložiště objektů blob vzdáleně. Poslední řádek kódu čte parkety, ale v tomto okamžiku se nenačtou žádná data.

    ```python
    # Azure storage access info
    blob_account_name = "azureopendatastorage"
    blob_container_name = "nyctlc"
    blob_relative_path = "yellow"
    blob_sas_token = r""

    # Allow SPARK to read from Blob remotely
    wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
    spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),blob_sas_token)

    # SPARK read parquet, note that it won't load any data yet by now
    df = spark.read.parquet(wasbs_path)
    ```

2. Vytažením všech těchto dat generuje asi 1,5 miliardy řádků. V závislosti na velikosti fondu Spark (náhled) mohou být nezpracovaná data příliš velká nebo jejich provoz trvá příliš dlouho. Tato data můžete filtrovat na menší. V případě potřeby přidejte následující řádky, abyste data filtrovat dolů asi 2 miliony řádků pro více citlivé prostředí. Pomocí těchto parametrů natáhnout jeden týden dat.

    ```python
    # Create an ingestion filter
    start_date = '2018-05-01 00:00:00'
    end_date = '2018-05-08 00:00:00'

    filtered_df = df.filter('tpepPickupDateTime > "' + start_date + '" and tpepPickupDateTime < "' + end_date + '"')
    ```

3. Nevýhodou jednoduchého filtrování je to, že ze statistického hlediska může do dat zavést zkreslení. Dalším přístupem je použití vzorkování zabudovaného do Spark. Následující kód snižuje datovou sadu až na 2000 řádků, pokud je použita po výše uvedeném kódu. Tento krok vzorkování lze použít namísto jednoduchého filtru nebo ve spojení s jednoduchým filtrem.

    ```python
    # To make development easier, faster and less expensive down sample for now
    sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)
    ```

4. Nyní je možné se podívat na data a zjistit, co bylo přečteno. Obvykle je lepší zkontrolovat data s podmnožinou, nikoli s úplnou sadou v závislosti na velikosti datové sady. Následující kód nabízí dva způsoby zobrazení dat: první je základní a druhý poskytuje mnohem bohatší mřížky zkušenosti, stejně jako schopnost vizualizovat data graficky.

    ```python
    sampled_taxi_df.show(5)
    display(sampled_taxi_df.show(5))
    ```

5. V závislosti na velikosti generované datové sady a nutnosti experimentovat nebo spustit poznámkový blok mnohokrát, může být vhodné ukládat datové sady místně v pracovním prostoru. Explicitní ukládání do mezipaměti lze provádět třemi způsoby:

   - Místní uložení datového rámce jako souboru
   - Uložení datového rámce jako dočasné tabulky nebo zobrazení
   - Uložení datového rámce jako trvalé tabulky

První 2 z těchto přístupů jsou zahrnuty v následujících příkladech kódu.

Vytvoření dočasné tabulky nebo zobrazení poskytuje různé přístupové cesty k datům, ale trvá pouze po dobu trvání relace instance Spark.

```Python
sampled_taxi_df.createOrReplaceTempView("nytaxi")
```

## <a name="understand-the-data"></a>Pochopení dat

Za normálních okolností byste projít fází *analýzy průzkumných dat* (EDA) v tomto bodě rozvíjet pochopení dat. Následující kód ukazuje tři různé vizualizace dat souvisejících s tipy, které vedou k závěrům o stavu a kvalitě dat.

```python
# The charting package needs a Pandas dataframe or numpy array do the conversion
sampled_taxi_pd_df = sampled_taxi_df.toPandas()

# Look at tips by amount count histogram
ax1 = sampled_taxi_pd_df['tipAmount'].plot(kind='hist', bins=25, facecolor='lightblue')
ax1.set_title('Tip amount distribution')
ax1.set_xlabel('Tip Amount ($)')
ax1.set_ylabel('Counts')
plt.suptitle('')
plt.show()

# How many passengers tip'd by various amounts
ax2 = sampled_taxi_pd_df.boxplot(column=['tipAmount'], by=['passengerCount'])
ax2.set_title('Tip amount by Passenger count')
ax2.set_xlabel('Passenger count')
ax2.set_ylabel('Tip Amount ($)')
plt.suptitle('')
plt.show()

# Look at the relationship between fare and tip amounts
ax = sampled_taxi_pd_df.plot(kind='scatter', x= 'fareAmount', y = 'tipAmount', c='blue', alpha = 0.10, s=2.5*(sampled_taxi_pd_df['passengerCount']))
ax.set_title('Tip amount by Fare amount')
ax.set_xlabel('Fare Amount ($)')
ax.set_ylabel('Tip Amount ($)')
plt.axis([-2, 80, -2, 20])
plt.suptitle('')
plt.show()
```

![Histogram](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-histogram.png)
![Box Vousy](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-box-whisker.png)
![Plot Scatter Plot](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-scatter.png)

## <a name="preparing-the-data"></a>Příprava dat

Data v nezpracované podobě často nejsou vhodná pro přímé předávání modelu. Řada akcí musí být provedena na data, aby se dostal do stavu, kde model může využívat.

V kódu pod čtyřmi třídami se provádějí:

- Odstranění odlehlých hodnot/nesprávných hodnot prostřednictvím filtrování.
- Odstranění sloupců, které nejsou potřeba.
- Vytvoření nových sloupců odvozených z nezpracovaných dat, aby model fungoval efektivněji, někdy nazývané featurization.
- Označení, jak jste podnik binární klasifikace (bude tam tip, nebo ne na dané cestě), je třeba převést částku tip u hodnoty 0 nebo 1.

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

Druhý průchod je pak přes data přidat konečné funkce.

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

## <a name="create-a-logistic-regression-model"></a>Vytvoření logistického regresního modelu

Konečným úkolem je převést označená data do formátu, který lze analyzovat logistickou regresí. Vstup do algoritmu logistické regrese musí být sada *vektorových párů funkce popisku*, kde *vektor prvku* je vektor čísel představující vstupní bod. Takže musíme převést kategorické sloupce na čísla. Sloupce `trafficTimeBins` `weekdayString` a je třeba převést na celé reprezentace. Existuje více přístupů k provedení převodu, ale přístup přijatý v tomto příkladu je *OneHotEncoding*, společný přístup.

```python
# The sample uses an algorithm that only works with numeric features convert them so they can be consumed
sI1 = StringIndexer(inputCol="trafficTimeBins", outputCol="trafficTimeBinsIndex")
en1 = OneHotEncoder(dropLast=False, inputCol="trafficTimeBinsIndex", outputCol="trafficTimeBinsVec")
sI2 = StringIndexer(inputCol="weekdayString", outputCol="weekdayIndex")
en2 = OneHotEncoder(dropLast=False, inputCol="weekdayIndex", outputCol="weekdayVec")

# Create a new dataframe that has had the encodings applied
encoded_final_df = Pipeline(stages=[sI1, en1, sI2, en2]).fit(taxi_featurised_df).transform(taxi_featurised_df)
```

Výsledkem je nový datový rámec se všemi sloupci ve správném formátu pro trénování modelu.

## <a name="train-a-logistic-regression-model"></a>Vyškolit logistický regresní model

Prvním úkolem je rozdělit datovou sadu do trénovací sady a testovací nebo ověřovací sady. Rozdělení je zde libovolné a měli byste si pohrát s různými nastaveními rozdělení, abyste zjistili, zda mají vliv na model.

```python
#Decide on the split between training and testing data from the dataframe
trainingFraction = 0.7
testingFraction = (1-trainingFraction)
seed = 1234

# Split the dataframe into test and training dataframes
train_data_df, test_data_df = encoded_final_df.randomSplit([trainingFraction, testingFraction], seed=seed)
```

Nyní, když existují dva DataFrames, dalším úkolem je vytvořit vzorec modelu a spustit jej proti trénování DataFrame, pak ověřit proti testování DataFrame. Měli byste experimentovat s různými verzemi vzorce modelu, abyste viděli dopad různých kombinací.

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

Výstup z této buňky je

```shell
Area under ROC = 0.9779470729751403
```

## <a name="create-a-visual-representation-of-the-prediction"></a>Vytvoření vizuální reprezentace předpovědi

Nyní můžete vytvořit konečnou vizualizaci, která vám pomůže důvod o výsledcích tohoto testu. [Křivka ROC](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) je jedním ze způsobů, jak zkontrolovat výsledek.

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

![ROC Curve pro model logistické regresní špičky](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-nyctaxi-roc.png "ROC Curve pro model logistické regresní špičky")

## <a name="shut-down-the-spark-instance"></a>Vypnutí instance Spark

Po dokončení spuštění aplikace vypněte poznámkový blok, abyste uvolnili prostředky, zavřením karty nebo vyberte **Ukončit relaci** ze stavového panelu v dolní části poznámkového bloku.

## <a name="see-also"></a>Viz také

- [Přehled: Apache Spark na Azure Synapse Analytics](apache-spark-overview.md)

## <a name="next-steps"></a>Další kroky

- [.NET pro dokumentaci Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Oficiální dokumentace Apache Spark](https://spark.apache.org/docs/latest/)

>[!NOTE]
> Některé oficiální dokumenty Apache Spark závisí na použití konzole Spark, která není k dispozici na Azure Synapse Spark. Místo toho použijte [poznámkový blok](../spark/apache-spark-notebook-create-spark-use-sql.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) nebo prostředí [IntelliJ.](../spark/intellij-tool-synapse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
