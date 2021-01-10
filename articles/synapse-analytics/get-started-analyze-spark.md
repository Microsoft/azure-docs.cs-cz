---
title: 'Rychlý Start: Začínáme s analýzou Sparku'
description: V tomto kurzu se naučíte analyzovat data pomocí Apache Spark.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 2c526d25fed169f702a3786a17717de3cf4dc6af
ms.sourcegitcommit: 31cfd3782a448068c0ff1105abe06035ee7b672a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2021
ms.locfileid: "98059020"
---
# <a name="analyze-with-apache-spark"></a>Analýza pomocí Apache Spark

V tomto kurzu se seznámíte se základními kroky pro načtení a analýzu dat pomocí Apache Spark pro Azure synapse.

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-spark"></a>Analýza dat NYC taxislužby v BLOB Storage pomocí Sparku

1. V **datovém** centru vyberte **Přidat nový prostředek (další** tlačítko plus **propojeno**) > **Procházet galerii**.
1. Vyberte **NYC taxislužby & Limousine provize – žluté taxislužby**.
1. V dolní části stránky vyberte **pokračovat**  >  **Přidat datovou sadu**.
1. V oblasti **propojených** **dat** klikněte pravým tlačítkem myši na položku **Azure Blob Storage** a potom vyberte možnost **ukázkové datové sady**  >  **nyc_tlc_yellow**.
1. Vyberte **Nový Poznámkový blok** pro vytvoření nového poznámkového bloku s následujícím kódem:

    ```py
    from azureml.opendatasets import NycTlcYellow

    data = NycTlcYellow()
    data_df = data.to_spark_dataframe()
    display(data_df.limit(10))
    ```

1. V nabídce **připojit k** v poznámkovém bloku vyberte fond Spark bez serveru.
1. V buňce vyberte **Spustit** .
1. Chcete-li zobrazit pouze schéma pro datový rámec, spusťte buňku s následujícím kódem:

    ```py
    data_df.printSchema()
    ```

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>Načtení dat taxislužby NYC do databáze Spark nyctaxi

Data jsou k dispozici v tabulce v **SQLPOOL1**. Načtěte ho do databáze Spark s názvem **nyctaxi**.

1. V synapse studiu přejdete do centra pro **vývoj** .
1. Vyberte **+**  >  **Poznámkový blok**.
1. V horní části poznámkového bloku nastavte hodnotu **připojit k** **Spark1**.
1. Vyberte **přidat kód** pro přidání buňky kódu poznámkového bloku a zadejte následující text:

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLPOOL1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. V **datovém** centru klikněte pravým tlačítkem na **databáze** a pak vyberte **aktualizovat**. Tyto databáze by se měly zobrazit:

    - **SQLPOOL1 (SQL)**
    - **nyctaxi (Spark)**

## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Analýza dat taxislužby NYC pomocí Sparku a poznámkových bloků

1. Vraťte se do poznámkového bloku.
1. Vytvořte novou buňku kódu a zadejte následující text.

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Spuštěním buňky zobrazte data NYC taxislužby, která jste načetli do databáze **nyctaxi** Spark.
1. Spusťte následující kód, který provede stejnou analýzu, kterou jste provedli dříve s vyhrazeným fondem SQL **SQLPOOL1**. Tento kód uloží a zobrazí výsledky analýzy do tabulky s názvem **nyctaxi. passengercountstats**.

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

## <a name="load-data-from-a-spark-table-into-a-dedicated-sql-pool-table"></a>Načtení dat z tabulky Spark do vyhrazené tabulky fondu SQL

Dříve jste zkopírovali data z vyhrazené tabulky fondu SQL **SQLPOOL1. dbo. Trip** do tabulky Spark **nyctaxi. Trip**. Pak data agreguje do tabulky Spark **nyctaxi. passengercountstats**. Nyní zkopírujete data z **nyctaxi. passengercountstats** do vyhrazené tabulky fondu SQL s názvem **SQLPOOL1. dbo. passengercountstats**.

Spusťte následující buňku v poznámkovém bloku. Nakopíruje agregovanou tabulku Spark zpátky do vyhrazené tabulky fondu SQL.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLPOOL1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Analýza dat pomocí neserverového fondu SQL](get-started-analyze-sql-on-demand.md)
