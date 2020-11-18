---
title: 'Rychlý Start: Začínáme s analýzou Sparku'
description: V tomto kurzu se naučíte analyzovat data pomocí Apache Spark
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 07537e26b169414e3f8ec35cc32945c20f7eb7ce
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2020
ms.locfileid: "94843277"
---
# <a name="analyze-with-apache-spark"></a>Analýza pomocí Apache Spark

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-spark"></a>Analýza dat NYC taxislužby v BLOB Storage pomocí Sparku

V tomto kurzu se seznámíte se základními kroky pro načtení a analýzu dat pomocí Apache Spark pro Azure synapse.

1. V **datovém** centru klikněte na **Přidat nový prostředek (další** tlačítko plus **propojený**)  >> **Procházet ukázky**. 
1. Vyhledejte **NYC taxislužby & Limousine provize – žluté taxislužby** a klikněte na ni. 
1. V dolní části stránky stiskněte **pokračovat** a potom **přidejte datovou sadu**. 
1. Nyní v centru **dat** pod **odkazem** klikněte pravým tlačítkem myši na **Azure Blob Storage >> ukázkové datové sady >> nyc_tlc_yellow** a vyberte **Nový Poznámkový blok** .
1. Tím se vytvoří nový Poznámkový blok s následujícím kódem:
    ```
    from azureml.opendatasets import NycTlcYellow

    data = NycTlcYellow()
    data_df = data.to_spark_dataframe()
    display(data_df.limit(10))
    ```
1. V poznámkovém bloku vyberte v nabídce **připojit k** možnost fond Spark bez serveru.
1. Vyberte v buňce **Spustit** .
1. Pokud chcete pouze zobrazit schéma pro datový rámec, spusťte buňku s následujícím kódem:
    ```
    data_df.printSchema()
    ```

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>Načtení dat taxislužby NYC do databáze Spark nyctaxi

K dispozici jsou data v tabulce v **SQLPOOL1**. Načtěte ho do databáze Spark s názvem **nyctaxi**.

1. V synapse studiu přejdete do centra pro **vývoj** .
1. Vyberte **+**  >  **Poznámkový blok**.
1. V horní části poznámkového bloku nastavte hodnotu **připojit k** **Spark1**.
1. Vyberte **přidat kód** pro přidání buňky kódu poznámkového bloku a vložte následující text:

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLPOOL1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. Přejděte do centra **dat** , klikněte pravým tlačítkem na **databáze** a pak vyberte **aktualizovat**. Tyto databáze by se měly zobrazit:

    - **SQLPOOL1 (SQL)**
    - **nyctaxi (Spark)**

## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Analýza dat taxislužby NYC pomocí Sparku a poznámkových bloků

1. Vraťte se do poznámkového bloku.
1. Vytvořte novou buňku kódu a zadejte následující text. Pak spuštěním buňky zobrazte data NYC taxislužby, která jsme načetli do databáze **nyctaxi** Spark.

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Spusťte následující kód, který provede stejnou analýzu, kterou jsme dříve zahrnuli do vyhrazeného fondu SQL **SQLPOOL1**. Tento kód uloží výsledky analýzy do tabulky s názvem **nyctaxi. passengercountstats** a vizualizuje výsledky.

   ```py
   %%pyspark
   df = spark.sql("""
      SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
      FROM nyctaxi.trip
      WHERE TripDistanceMiles > 0 AND PassengerCount > 0
      GROUP BY PassengerCount
      ORDER BY PassengerCount
   """) 
   display(df)
   df.write.saveAsTable("nyctaxi.passengercountstats")
   ```

1. Ve výsledcích buňky vyberte možnost **graf** , aby se zobrazila data vizuálů.

## <a name="customize-data-visualization-with-spark-and-notebooks"></a>Přizpůsobení vizualizace dat pomocí Sparku a poznámkových bloků

Můžete řídit, jak se grafy vykreslují pomocí poznámkových bloků. Následující kód ukazuje jednoduchý příklad. Používá oblíbené knihovny **matplotlib** a **Seaborn**. Kód vykresluje stejný druh spojnicového grafu jako příkazy jazyka SQL, které byly dříve spuštěny.

```py
%%pyspark
import matplotlib.pyplot
import seaborn

seaborn.set(style = "whitegrid")
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.toPandas()
seaborn.lineplot(x="PassengerCount", y="SumTripDistance" , data = df)
seaborn.lineplot(x="PassengerCount", y="AvgTripDistance" , data = df)
matplotlib.pyplot.show()
```



## <a name="load-data-from-a-spark-table-into-a-dedicated-sql-pool-table"></a>Načtení dat z tabulky Spark do vyhrazené tabulky fondu SQL

Dříve jsme zkopírovali data z vyhrazené tabulky SQLPOOL1 fondu SQL **. dbo. Trip** do tabulky Spark **nyctaxi. Trip**. Pomocí Sparku jsme data agreguje do tabulky Spark **nyctaxi. passengercountstats**. Nyní zkopírujeme data z **nyctaxi. passengercountstats** do vyhrazené tabulky fondu SQL s názvem **SQLPOOL1. dbo. passengercountstats**.

Spusťte následující buňku v poznámkovém bloku. Nakopíruje agregovanou tabulku Spark zpátky do vyhrazené tabulky fondu SQL.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLPOOL1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Analýza dat pomocí neserverového fondu SQL](get-started-analyze-sql-on-demand.md)


