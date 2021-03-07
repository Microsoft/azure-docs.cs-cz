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
ms.date: 12/31/2020
ms.openlocfilehash: 6b3c1ac2ea3625a768e16a3465230a5386c98ddc
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102423709"
---
# <a name="analyze-with-apache-spark"></a>Analýza pomocí Apache Spark

V tomto kurzu se seznámíte se základními kroky pro načtení a analýzu dat pomocí Apache Spark pro Azure synapse.

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-spark"></a>Analýza dat NYC taxislužby v BLOB Storage pomocí Sparku

1. V **datovém** centru kliknutím na **+** tlačítko **přidejte nový prostředek** a pak klikněte na >> **Procházet galerii**. 
1. Vyhledejte **NYC taxislužby & Limousine provize – žluté taxislužby** a klikněte na ni. 
1. V dolní části stránky stiskněte **pokračovat** a pak **přidejte datovou sadu**. 
1. V **datovém** centru v oblasti **propojených** dat klikněte pravým tlačítkem na **Azure Blob Storage >> ukázkové datové sady >> nyc_tlc_yellow** a vyberte **Nový Poznámkový blok** a pak **načtěte do datového rámce**.
1. Tím se vytvoří nový Poznámkový blok s následujícím kódem:
    ```

    from azureml.opendatasets import NycTlcYellow

    data = NycTlcYellow()
    df = data.to_spark_dataframe()
    # Display 10 rows
    display(df.limit(10))
    ```
1. V poznámkovém bloku vyberte v nabídce **připojit k** **Spark1** fond Spark bez serveru, který jsme vytvořili dříve.
1. V buňce vyberte **Spustit** . Synapse spustí novou relaci Spark a v případě potřeby tuto buňku spustí. Pokud potřebujete novou relaci Sparku, intially bude trvat přibližně dva sekundy. 
1. Pokud chcete pouze zobrazit schéma pro datový rámec, spusťte buňku s následujícím kódem:
    ```

    df.printSchema()
    ```

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>Načtení dat taxislužby NYC do databáze Spark nyctaxi

Data jsou k dispozici v tabulce v **SQLPOOL1**. Načtěte ho do databáze Spark s názvem **nyctaxi**.

1. V synapse studiu přejdete do centra pro **vývoj** .
1. Vyberte **+**  >  **Poznámkový blok**.
1. V horní části poznámkového bloku nastavte hodnotu **připojit k** **Spark1**.
1. Do první buňky kódu v poznámkovém bloku zadejte následující kód:


    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLPOOL1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```


1. Spusťte skript. Může to trvat 2-3 minut.
1. V **datovém** centru na kartě **pracovní prostor** klikněte pravým tlačítkem na **databáze** a pak vyberte **aktualizovat**. V seznamu by se teď měla zobrazit databáze **nyctaxi (Spark)** .


## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Analýza dat taxislužby NYC pomocí Sparku a poznámkových bloků

1. Vraťte se do poznámkového bloku.
1. Vytvořte novou buňku kódu a zadejte následující kód. 


   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Spuštěním buňky zobrazíte data NYC taxislužby, která jste načetli do databáze **nyctaxi** Spark.
1. Vytvořte novou buňku kódu a zadejte následující kód. Pak spusťte tuto buňku a proveďte stejnou analýzu, kterou jsme dříve zahrnuli do vyhrazeného fondu SQL **SQLPOOL1**. Tento kód uloží a zobrazí výsledky analýzy do tabulky s názvem **nyctaxi. passengercountstats**.


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

Dříve jsme zkopírovali data z vyhrazené tabulky SQLPOOL1 fondu SQL **. dbo. Trip** do tabulky Spark **nyctaxi. Trip**. Pak data agreguje do tabulky Spark **nyctaxi. passengercountstats**. Nyní zkopírujete data z **nyctaxi. passengercountstats** do vyhrazené tabulky fondu SQL s názvem **SQLPOOL1. dbo. passengercountstats**.

1. Vytvořte novou buňku kódu a zadejte následující kód. Spusťte buňku v poznámkovém bloku. Nakopíruje agregovanou tabulku Spark zpátky do vyhrazené tabulky fondu SQL.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLPOOL1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Analýza dat pomocí neserverového fondu SQL](get-started-analyze-sql-on-demand.md)
