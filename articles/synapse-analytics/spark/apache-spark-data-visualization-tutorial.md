---
title: Vizualizace dat s využitím Apache Sparku
description: Vytváření bohatých vizualizací dat pomocí Apache Spark a notebooků Azure synapse Analytics
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 10/20/2020
ms.author: midesa
ms.openlocfilehash: 56af49b6fa862c93822293056752182b534ac442
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942267"
---
# <a name="analyze-data-with-apache-spark"></a>Analýza dat pomocí Apache Spark

V tomto kurzu se dozvíte, jak provádět analýzu průzkumnéch dat pomocí Azure Open DataSets a Apache Spark. Výsledky pak můžete vizualizovat v poznámkovém bloku synapse Studio ve službě Azure synapse Analytics.

Konkrétně analyzujeme taxislužby datovou sadu [NYC (New York City)](https://azure.microsoft.com/en-us/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) . Data jsou dostupná prostřednictvím Azure Open DataSet. Tato podmnožina DataSet obsahuje informace o žluté taxislužby TRIPS: informace o jednotlivých cestách, čas zahájení a ukončení a umístění, náklady a další zajímavé atributy.
  
## <a name="before-you-begin"></a>Než začnete
Pomocí [kurzu Vytvoření fondu Apache Spark](../articles/../quickstart-create-apache-spark-pool-studio.md)vytvořte fond Apache Spark. 

## <a name="download-and-prepare-the-data"></a>Stažení a Příprava dat
1. Pomocí jádra PySpark vytvořte Poznámkový blok. Pokyny najdete v tématu [vytvoření poznámkového bloku](../quickstart-apache-spark-notebook.md#create-a-notebook). 
   
   > [!Note]
   > Z důvodu jádra PySpark nemusíte vytvářet žádné kontexty explicitně. Kontext Spark je automaticky vytvořen za vás při spuštění první buňky kódu.

2. V tomto kurzu použijeme několik různých knihoven, které nám pomůžou vizualizovat datovou sadu. K provedení této analýzy importujte následující knihovny: 

   ```python
   import matplotlib.pyplot as plt
   import seaborn as sns
   import pandas as pd
   ```

3. Vzhledem k tomu, že nezpracovaná data jsou ve formátu Parquet, můžete pomocí kontextu Spark načíst soubor do paměti jako datový rámec přímo. Vytvořte datový rámec Spark, a to tak, že načtěte data prostřednictvím rozhraní API Open DataSet. Tady pro odvození datového typu a schématu používáme schéma dataframe Spark *pro čtení* vlastností.

   ```python
   from azureml.opendatasets import NycTlcYellow
   from datetime import datetime
   from dateutil import parser

   end_date = parser.parse('2018-06-06')
   start_date = parser.parse('2018-05-01')
   nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
   df = nyc_tlc.to_spark_dataframe()
   ```

4. Po načtení dat budeme chtít provést některé počáteční filtrování, aby se datová sada vyčistila. Může dojít k odebrání nepotřebných sloupců a přidání sloupců, které extrahují důležité informace. Kromě toho Odfiltrujeme anomálie v rámci datové sady.

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

### <a name="apache-spark-sql-magic"></a>Apache Spark SQL Magic 
Nejdřív provedeme analýzu průzkumnéch dat Apache Spark příkazů SQL a Magic pomocí poznámkového bloku Azure synapse. Až budeme mít náš dotaz, vizualizujeme výsledky pomocí integrované ```chart options``` funkce. 

1. V rámci poznámkového bloku vytvořte novou buňku a zkopírujte následující kód. Pomocí tohoto dotazu chceme pochopit, jak se v období, které jste vybrali, změnily průměrné částky tipů. Tento dotaz také pomůže identifikovat další užitečné poznatky, včetně minimálního/maximálního počtu tipů za den a průměrného počtu tarifů.
   
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

2. Až se dokončí náš dotaz, můžeme výsledky vizualizovat tak, že přepnete do zobrazení grafu. Tento příklad vytvoří spojnicový graf zadáním ```day_of_month``` pole jako klíče a ```avgTipAmount``` jako hodnoty. Po provedení výběru vyberte **použít** k aktualizaci grafu. 
   
## <a name="visualize-data"></a>Vizualizace dat
Kromě integrovaných možností vytváření grafů poznámkového bloku můžete pomocí oblíbených open source knihoven vytvářet vlastní vizualizace. V následujících příkladech použijeme Seaborn a matplotlib. Toto jsou běžně používané knihovny Pythonu pro vizualizaci dat. 

> [!Note]
> Ve výchozím nastavení obsahuje každý fond Apache Spark ve službě Azure synapse Analytics sadu běžně používaných a výchozích knihoven. Úplný seznam knihoven můžete zobrazit v dokumentaci ke [službě Azure synapse runtime](../spark/apache-spark-version-support.md) . Aby bylo možné aplikace zpřístupnit třetí straně nebo místně sestavený kód, můžete také [nainstalovat knihovnu](../spark/apache-spark-azure-portal-add-libraries.md) na jeden z vašich fondů Spark.

1. Abychom usnadnili vývoj a zjednodušili, převedeme si datovou sadu. Použijeme vestavěnou možnost vzorkování Apache Spark. Kromě toho Seaborn i matplotlib vyžadují PANDAS dataframe nebo NumPy Array. Chcete-li získat PANDAS dataframe, pomocí ```toPandas()``` příkazu převeďte datový rámec.

   ```python
   # To make development easier, faster, and less expensive, downsample for now
   sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)

   # The charting package needs a Pandas DataFrame or NumPy array to do the conversion
   sampled_taxi_pd_df = sampled_taxi_df.toPandas()
   ```

1. Chceme pochopit distribuci tipů v naší datové sadě. Matplotlib použijeme k vytvoření histogramu, který ukazuje distribuci velikosti a počtu tipů. Na základě distribuce můžeme vidět, že tipy se zkosí k částkám menším nebo rovným $10.

   ```python
   # Look at a histogram of tips by count by using Matplotlib

   ax1 = sampled_taxi_pd_df['tipAmount'].plot(kind='hist', bins=25, facecolor='lightblue')
   ax1.set_title('Tip amount distribution')
   ax1.set_xlabel('Tip Amount ($)')
   ax1.set_ylabel('Counts')
   plt.suptitle('')
   plt.show()
   ```

   ![Histogram tipů](./media/apache-spark-machine-learning-mllib-notebook/histogram.png)

1. Dále chceme pochopit vztah mezi tipy pro danou cestu a den v týdnu. Pomocí Seaborn můžete vytvořit krabicový graf, který shrnuje trendy pro každý den v týdnu. 

   ```python
   # View the distribution of tips by day of week using Seaborn
   ax = sns.boxplot(x="day_of_week", y="tipAmount",data=sampled_taxi_pd_df, showfliers = False)
   ax.set_title('Tip amount distribution per day')
   ax.set_xlabel('Day of Week')
   ax.set_ylabel('Tip Amount ($)')
   plt.show()

   ```
   ![Graf, který zobrazuje distribuci tipů za den.](./media/apache-spark-data-viz/data-analyst-tutorial-per-day.png)

4. Další hypotéza naší strany by mohla být kladná souvislost mezi počtem cestujících a celkovou taxislužby špičkou. Chcete-li ověřit tento vztah, spusťte následující kód pro vygenerování krabicového grafu, který znázorňuje distribuci tipů pro každý počet cestujících. 
   
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
   ![Graf, který zobrazuje krabicový graf box.](./media/apache-spark-machine-learning-mllib-notebook/box-whisker-plot.png)

1. Nakonec chceme pochopit vztah mezi výší tarifů a množstvím tipu. Na základě výsledků můžeme vidět, že existuje několik pozorování, kde lidé netipí. Zobrazuje se ale také kladný vztah mezi celkovým tarifem a výší špičky.
   
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
   ![Bodový graf částky tipu](./media/apache-spark-machine-learning-mllib-notebook/scatter.png)

## <a name="shut-down-the-spark-instance"></a>Vypnutí instance Spark

Po dokončení spuštění aplikace vypněte Poznámkový blok a uvolněte prostředky. Buď zavřete kartu, nebo v dolní části poznámkového bloku vyberte **ukončit relaci** ze stavového panelu.

## <a name="see-also"></a>Viz také

- [Přehled: Apache Spark ve službě Azure synapse Analytics](apache-spark-overview.md)
- [Sestavení modelu Machine Learning s Apache SparkML](../spark/apache-spark-machine-learning-mllib-notebook.md)

## <a name="next-steps"></a>Další kroky

- [Azure Synapse Analytics](../index.yml)
- [Apache Spark oficiální dokumentace](https://spark.apache.org/docs/latest/)