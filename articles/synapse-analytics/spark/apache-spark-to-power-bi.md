---
title: Poznámkové bloky s funkcí Azure Synapse Studio
description: Tento kurz poskytuje přehled o tom, jak vytvořit řídicí panel Power BI pomocí Apache Spark a fondu SQL bez serveru.
services: synapse-analytics
author: midesa
ms.author: midesa
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.date: 11/16/2020
ms.openlocfilehash: 53fb256d3a0fba1cc10cafb239ff7f7bb21ac4eb
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121069"
---
# <a name="tutorial-create-a-power-bi-report-using-apache-spark-and-azure-synapse-analytics"></a>Kurz: Vytvoření sestavy Power BI pomocí Apache Spark a Azure synapse Analytics

Organizace často potřebují zpracovávat velké objemy dat před tím, než zachovávají klíčové obchodní účastníky. V tomto kurzu se dozvíte, jak pomocí integrovaného prostředí ve službě Azure synapse Analytics zpracovávat data pomocí Apache Spark a později data obsluhují koncovým uživatelům prostřednictvím Power BI a SQL bez serveru.

## <a name="before-you-begin"></a>Než začnete
- [Pracovní prostor Azure synapse Analytics](../quickstart-create-workspace.md) s účtem úložiště adls Gen2 nakonfigurovaný jako výchozí úložiště. 
- Power BI pracovní prostor a Power BI Desktop k vizualizaci dat. Podrobnosti najdete v tématu [Vytvoření pracovního prostoru Power BI](/power-bi/service-create-the-new-workspaces) a [instalace Power BI desktopu](https://powerbi.microsoft.com/downloads/) .
- Propojená služba pro připojení ke službě Azure synapse Analytics a pracovním prostorům Power BI. Podrobnosti najdete v tématu [připojení k pracovnímu prostoru Power BI](../quickstart-power-bi.md) .
- Fond Apache Spark bez serveru v pracovním prostoru analýzy synapse Podrobnosti najdete v tématu [Vytvoření fondu Apache Spark bez serveru](../quickstart-create-apache-spark-pool-studio.md) .
  
## <a name="download-and-prepare-the-data"></a>Stažení a Příprava dat
V tomto příkladu použijete Apache Spark k provedení některých analýz dat Tip pro taxislužby Trip z Praha. Data jsou dostupná prostřednictvím [Azure Open DataSet](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). Tato podmnožina DataSet obsahuje informace o žluté taxislužby TRIPS, včetně informací o jednotlivých cestách, počátečním a koncovém času a umístěních, nákladech a dalších zajímavých atributů.

1. Spusťte následující řádky a vytvořte tak datový rámec Spark vložením kódu do nové buňky. To načte data prostřednictvím rozhraní API Open DataSet. Po přijetí všech těchto dat se vygeneruje přibližně 1 500 000 000 řádků. Následující příklad kódu používá start_date a end_date k použití filtru, který vrací jeden měsíc dat.
   
   ```python
    from azureml.opendatasets import NycTlcYellow
    from dateutil import parser
    from datetime import datetime

    end_date = parser.parse('2018-06-06')
    start_date = parser.parse('2018-05-01')
    nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
    filtered_df = nyc_tlc.to_spark_dataframe()
   ```
2. Při použití Apache Spark SQL vytvoříme databázi s názvem NycTlcTutorial. Tuto databázi použijeme k uložení výsledků našich zpracování dat.
   ```python
   %%pyspark
    spark.sql("CREATE DATABASE IF NOT EXISTS NycTlcTutorial")
   ```
3. Dále použijeme operace Spark dataframe ke zpracování dat. V následujícím kódu provedeme následující transformace:
   1. Odebrání sloupců, které nejsou potřeba.
   2. Odebrání neplatných nebo nesprávných hodnot prostřednictvím filtrování.
   3. Vytváření nových funkcí jako ```tripTimeSecs``` a ```tipped``` pro další analýzu.
    ```python
    from pyspark.sql.functions import unix_timestamp, date_format, col, when

    taxi_df = filtered_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'rateCodeId', 'passengerCount'\
                                    , 'tripDistance', 'tpepPickupDateTime', 'tpepDropoffDateTime'\
                                    , date_format('tpepPickupDateTime', 'hh').alias('pickupHour')\
                                    , date_format('tpepPickupDateTime', 'EEEE').alias('weekdayString')\
                                    , (unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('tripTimeSecs')\
                                    , (when(col('tipAmount') > 0, 1).otherwise(0)).alias('tipped')
                                    )\
                            .filter((filtered_df.passengerCount > 0) & (filtered_df.passengerCount < 8)\
                                    & (filtered_df.tipAmount >= 0) & (filtered_df.tipAmount <= 25)\
                                    & (filtered_df.fareAmount >= 1) & (filtered_df.fareAmount <= 250)\
                                    & (filtered_df.tipAmount < filtered_df.fareAmount)\
                                    & (filtered_df.tripDistance > 0) & (filtered_df.tripDistance <= 100)\
                                    & (filtered_df.rateCodeId <= 5)
                                    & (filtered_df.paymentType.isin({"1", "2"})))
    ```
4. Nakonec uložíme náš datový rámec pomocí ```saveAsTable``` metody Apache Spark. To vám umožní dotazovat se na stejnou tabulku a připojit se k ní pomocí neserverových fondů SQL.
  ```python
     taxi_df.write.mode("overwrite").saveAsTable("NycTlcTutorial.nyctaxi")
  ```
   
## <a name="query-data-using-serverless-sql-pools"></a>Dotazování na data s využitím fondů SQL bez serveru
Azure synapse Analytics umožňuje různým výpočetním modulům pracovních prostorů sdílet databáze a tabulky mezi svými fondy Apache Spark serverů bez serveru a SQL fondem bez serveru. Tato možnost se využívá prostřednictvím funkce [správy sdílených metadat](../metadata/overview.md) synapse. V důsledku toho se databáze Spark vytvořené a jejich Parquet tabulky budou zobrazovat ve fondu SQL bez serveru v pracovním prostoru.

Dotazování na Apache Spark tabulku pomocí vašeho fondu SQL bez serveru:
   1. Po uložení Apache Spark tabulky přepněte na kartu **data** .
   
   2. V části **pracovní prostory** Najděte Apache Spark tabulku, kterou jste právě vytvořili, vyberte **Nový skript SQL** a pak **Vyberte horní 100 řádků**. 
      
      :::image type="content" source="../spark/media/apache-spark-power-bi/query-spark-table-with-sql.png" alt-text="Dotaz SQL." border="true":::

   3. Můžete pokračovat v upřesnění dotazu nebo dokonce vizualizace výsledků pomocí možností grafů SQL.

## <a name="connect-to-power-bi"></a>Připojení k Power BI
Dále připojíme náš fond SQL bez serveru k naší Power BI pracovnímu prostoru. Jakmile budete mít pracovní prostor připojený, budete moct vytvářet Power BI sestavy přímo z Azure synapse Analytics i z Power BI Desktop.

>[!Note]
> Než začnete, budete muset nastavit propojenou službu do svého [pracovního prostoru Power BI](../quickstart-power-bi.md) a stáhnout [Power BI Desktop](/power-bi/service-create-the-new-workspaces).  

Připojení našeho fondu SQL bez serveru k našemu pracovnímu prostoru Power BI:

1.  Přejděte na kartu **Power BI datové sady** a vyberte možnost pro **+ Nová datová sada**. Z příkazového řádku Stáhněte soubor. pbids z databáze SQL Analytics, kterou chcete použít jako zdroj dat. 
   :::image type="content" source="../spark/media/apache-spark-power-bi/power-bi-datasets.png" alt-text="Power BI datové sady." border="true":::

2.  Otevřete soubor s Power BI Desktop a vytvořte datovou sadu. Po otevření souboru se připojte k databázi SQL serveru pomocí možnosti **účet Microsoft** a **Import** . 
   

## <a name="create-a-power-bi-report"></a>Vytvoření sestavy Power BI
1. Z Power BI plochy teď můžete do sestavy přidat nějaký graf s přísamostatným **klíčem** .
   
   1. Přetáhněte sloupec průměr *tipAmount* k **analýze** osy.
   
   2. Přetáhněte sloupce **weekdayString**, Average **TripDistance** a Average **tripTimeSecs** do pole **vysvětlit podle** osy. 
   
   :::image type="content" source="../spark/media/apache-spark-power-bi/power-bi-desktop.png" alt-text="Power BI Desktop." border="true":::

2. Na kartě Domů Power BI plochy vyberte **publikovat** a **uložte** změny. Zadejte název souboru a uložte tuto sestavu do *pracovního prostoru NycTaxiTutorial*.
   
3. Kromě toho můžete také vytvořit vizualizace Power BI v rámci pracovního prostoru Azure synapse Analytics. Provedete to tak, že v pracovním prostoru Azure synapse přejdete na kartu **vývoj** a otevřete kartu Power BI. Odsud můžete vybrat sestavu a pokračovat v sestavování dalších vizualizací. 
   
   :::image type="content" source="../spark/media/apache-spark-power-bi/power-bi-synapse.png" alt-text="Pracovní prostor analýzy Azure synapse." border="true":::

Další podrobnosti o tom, jak vytvořit datovou sadu pomocí SQL bez serveru a jak se připojit k Power BI, najdete v tomto kurzu o [připojení k Power BI desktopu](../../synapse-analytics/sql/tutorial-connect-power-bi-desktop.md) .

## <a name="next-steps"></a>Další kroky
Další informace o možnostech vizualizace dat ve službě Azure synapse Analytics můžete dál zobrazit v následujících dokumentech a kurzech:
   - [Vizualizace dat s využitím Apache Sparkch fondů bez serveru](../spark/apache-spark-data-visualization-tutorial.md)
   - [Přehled vizualizace dat pomocí Apache Sparkch fondů](../spark/apache-spark-data-visualization.md)