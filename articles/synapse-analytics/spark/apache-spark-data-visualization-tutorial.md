---
title: Vizualizace dat pomocí Apache Spark
description: Vytváření bohatých vizualizací dat pomocí Apache Spark a notebooků Azure synapse Analytics
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 10/20/2020
ms.author: midesa
ms.openlocfilehash: 820dd8b607f5ec2fdc44d25063e0a941f76237ad
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919742"
---
# <a name="analyze-data-with-apache-spark"></a>Analýza dat pomocí Apache Spark

V tomto kurzu se naučíte, jak provádět analýzu průzkumnéch dat pomocí Azure Open DataSets a Apache Spark a potom vizualizace výsledků v poznámkovém bloku Azure synapse Studio.

Konkrétně analyzujeme datovou sadu [taxislužby v New York City (NYC)](https://azure.microsoft.com/en-us/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) . Data jsou dostupná prostřednictvím Azure Open DataSet. Tato podmnožina DataSet obsahuje informace o žluté taxislužby TRIPS, včetně informací o jednotlivých cestách, počátečním a koncovém čase a umístěních, nákladech a dalších zajímavých atributů.
  
## <a name="before-you-begin"></a>Než začnete
- Pomocí [kurzu Vytvoření fondu Apache Spark](../articles/../quickstart-create-apache-spark-pool-studio.md) vytvořte fond Apache Spark. 

## <a name="download-and-prepare-the-data"></a>Stažení a Příprava dat
1. Pomocí jádra PySpark vytvořte Poznámkový blok. Pokyny najdete v tématu [vytvoření poznámkového bloku](https://docs.microsoft.com/azure/synapse-analytics/quickstart-apache-spark-notebook#create-a-notebook). 
   
> [!Note]
> 
> Z důvodu jádra PySpark nemusíte vytvářet žádné kontexty explicitně. Kontext Spark je automaticky vytvořen za vás při spuštění první buňky kódu.
>

2. V tomto kurzu budeme používat několik různých knihoven, které nám pomůžou vizualizovat datovou sadu. K provedení této analýzy bude nutné importovat následující knihovny: 

```python
import matplotlib.pyplot as plt
import seaborn as sns
import pandas as pd
```

3. Vzhledem k tomu, že nezpracovaná data jsou ve formátu Parquet, můžete pomocí kontextu Spark načíst soubor do paměti jako datový rámec přímo. Vytvořte datový rámec Spark, a to tak, že načtěte data prostřednictvím rozhraní API Open DataSet. Tady budeme pro odvození datového typu a schématu použít schéma dataframe Spark *pro čtení* vlastností.

```python
from azureml.opendatasets import NycTlcYellow
from datetime import datetime
from dateutil import parser

end_date = parser.parse('2018-06-06')
start_date = parser.parse('2018-05-01')
nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
df = nyc_tlc.to_spark_dataframe()
```

4. Po načtení dat budeme chtít provést některé počáteční filtrování, které datovou sadu vyčistí. Můžeme odebrat nepotřebné sloupce a přidat další sloupce, které extrahují důležité informace. Kromě toho také Odfiltrujeme anomálie v rámci datové sady.

```python
# Filter the dataset 
from pyspark.sql.functions import *

filtered_df = df.select('vendorID', 'passengerCount', 'tripDistance','paymentType', 'fareAmount', 'tipAmount'\
                                , date_format('tpepPickupDateTime', 'hh').alias('hour_of_day')\
                                , dayofweek('tpepPickupDateTime').alias('day_of_week')\
                                , dayofmonth(col('tpepPickupDateTime')).alias('day_of_month'))\
                            .filter((df.passengerCount > 0)\
                                & (df.tipAmount >= 0)\
                                & (df.fareAmount >= 1) & (df.fareAmount <= 250)\
                                & (df.tripDistance > 0) & (df.tripDistance <= 200))

filtered_df.createOrReplaceTempView("taxi_dataset")
```

## <a name="analyze-data"></a>Analýza dat
 Jako analytik dat máte k dispozici rozsáhlou škálu nástrojů, které vám pomůžou s extrakcí přehledů z těchto dat. V této části kurzu si projdeme několik užitečných nástrojů, které jsou dostupné v rámci notebooků Azure synapse Analytics. V této analýze chceme porozumět faktorům, které poskytují vyšší taxislužby tipy pro naše vybrané období.

###  <a name="apache-spark-sql-magic"></a>Apache Spark SQL Magic 
Nejprve provedeme analýzu dat průzkumné pomocí Apache Spark příkazů SQL a Magic pomocí poznámkového bloku synapse. Jakmile máme dotaz, budeme výsledky vizualizovat pomocí integrované ```chart options``` funkce. 

1. V rámci poznámkového bloku vytvořte novou buňku a zkopírujte kód níže. Pomocí tohoto dotazu chceme pochopit, jak se v období, které jste vybrali, změnila průměrná výše tipů. Tento dotaz také pomůže identifikovat další užitečné poznatky, včetně minimálního/maximálního počtu tipů za den a průměrného počtu tarifů.
   
```sql
%%sql
SELECT 
    day_of_month
    , MIN(tipAmount) AS minTipAmount
    , MAX(tipAmount) AS maxTipAmount
    , AVG(tipAmount) AS avgTipAmount
    , AVG(fareAmount) as fareAmount
FROM taxi_dataset 
GROUP BY day_of_month
ORDER BY day_of_month ASC
```

2. Jakmile se dokončí naše dotazy, můžeme výsledky vizualizovat tak, že přepnete do **zobrazení grafu**. V tomto příkladu vytvoříme **Spojnicový graf** zadáním ```day_of_month``` pole jako **klíče** a ```avgTipAmount``` jako **hodnoty**. Po provedení výběru klikněte na **použít** , aby se graf aktualizoval. 
   
## <a name="visualize-data"></a>Vizualizace dat
Kromě integrovaných možností vytváření grafů poznámkového bloku můžete také pomocí oblíbených open source knihoven vytvářet vlastní vizualizace. V následujících příkladech použijeme Seaborn a matplotlib, které často používají knihovny Python pro vizualizaci dat. 

> [!Note]
> 
> Ve výchozím nastavení každý fond služby Azure synapse Analytics Apache Spark obsahuje sadu běžně používaných a výchozích knihoven. Úplný seznam knihoven můžete zobrazit v [modulu runtime Azure synapse](../spark/apache-spark-version-support.md). nápovědě. Kromě toho, pokud chcete, aby vaše aplikace k dispozici třetí strana nebo místně sestavený kód, můžete [knihovnu nainstalovat](../spark/apache-spark-azure-portal-add-libraries.md) na jeden z vašich fondů Spark (Preview).
>

1. Abychom usnadnili vývoj a zjednodušili si ho, povedeme ukázku datové sady. Použijeme vestavěnou možnost vzorkování Apache Spark. Kromě toho Seaborn i matplotlib vyžadují PANDAS dataframe nebo numpy Array. K získání PANDAS dataframe použijeme ```toPandas()``` příkaz pro převod našeho datového rámce.

```python
# To make development easier, faster and less expensive down sample for now
sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)

# The charting package needs a Pandas dataframe or numpy array do the conversion
sampled_taxi_pd_df = sampled_taxi_df.toPandas()
```

1. Nejdřív chceme pochopit distribuci tipů v naší datové sadě. Použijeme matplotlib k vytvoření histogramu znázorňujícího rozdělení množství a počtu tipů. Na základě distribuce můžeme vidět, že tipy se zkosí až k částkám menším nebo rovným $10.

```python
# Look at tips by count histogram using Matplotlib

ax1 = sampled_taxi_pd_df['tipAmount'].plot(kind='hist', bins=25, facecolor='lightblue')
ax1.set_title('Tip amount distribution')
ax1.set_xlabel('Tip Amount ($)')
ax1.set_ylabel('Counts')
plt.suptitle('')
plt.show()
```

![Histogram tipů](./media/apache-spark-machine-learning-mllib-notebook/histogram.png)

1. Dále chceme pochopit vztah mezi tipy pro danou cestu a den v týdnu. Použijeme Seaborn k vytvoření krabicového grafu shrnujících trendy pro každý den v týdnu. 

```python
# View the distribution of tips by day of week using Seaborn
ax = sns.boxplot(x="day_of_week", y="tipAmount",data=sampled_taxi_pd_df, showfliers = False)
ax.set_title('Tip amount distribution per day')
ax.set_xlabel('Day of Week')
ax.set_ylabel('Tip Amount ($)')
plt.show()

```
![Distribuce tipů za den](./media/apache-spark-data-viz/data-analyst-tutorial-per-day.png)

4. Nyní další hypotéza, kterou jsme zjistili, že existuje pozitivní vztah mezi počtem cestujících a celkovou taxislužby špičkou. Pokud chcete ověřit tuto relaci, spusťte následující kód, který ilustruje distribuci tipů pro každý počet spolujezdců. 
   
```python
# How many passengers tipped by various amounts 
ax2 = sampled_taxi_pd_df.boxplot(column=['tipAmount'], by=['passengerCount'])
ax2.set_title('Tip amount by Passenger count')
ax2.set_xlabel('Passenger count')
ax2.set_ylabel('Tip Amount ($)')
ax2.set_ylim(0,30)
plt.suptitle('')
plt.show()
```
![Krabicový graf box](./media/apache-spark-machine-learning-mllib-notebook/box-whisker-plot.png)

1. Nakonec chceme pochopit vztah mezi velikostí hrotu částky tarifů. Na základě výsledků můžeme vidět, že je k dispozici několik pozorování, kde lidé netipují. Zobrazuje se ale také kladný vztah mezi celkovým tarifem a výší špičky.
   
```python
# Look at the relationship between fare and tip amounts

ax = sampled_taxi_pd_df.plot(kind='scatter', x= 'fareAmount', y = 'tipAmount', c='blue', alpha = 0.10, s=2.5*(sampled_taxi_pd_df['passengerCount']))
ax.set_title('Tip amount by Fare amount')
ax.set_xlabel('Fare Amount ($)')
ax.set_ylabel('Tip Amount ($)')
plt.axis([-2, 80, -2, 20])
plt.suptitle('')
plt.show()
```
![Bodový graf](./media/apache-spark-machine-learning-mllib-notebook/scatter.png)

## <a name="shut-down-the-spark-instance"></a>Vypnutí instance Spark

Po dokončení používání aplikace vypněte Poznámkový blok a uvolněte ho tak, že zavřete kartu nebo v dolní části poznámkového bloku vyberete **ukončit relaci** ze stavového panelu.

## <a name="see-also"></a>Viz také

- [Přehled: Apache Spark ve službě Azure synapse Analytics](apache-spark-overview.md)
- [Sestavení modelu Machine Learning s Apache SparkML](../spark/apache-spark-machine-learning-mllib-notebook.md)

## <a name="next-steps"></a>Další kroky

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Apache Spark oficiální dokumentace](https://spark.apache.org/docs/latest/)