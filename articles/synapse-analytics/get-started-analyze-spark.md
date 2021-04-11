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
ms.date: 03/24/2021
ms.openlocfilehash: 0becbbdb68f75072e10a51f5a2eae95291b9ed77
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105108328"
---
# <a name="analyze-with-apache-spark"></a>Analýza pomocí Apache Spark

V tomto kurzu se seznámíte se základními kroky pro načtení a analýzu dat pomocí Apache Spark pro Azure synapse.

## <a name="create-a-serverless-apache-spark-pool"></a>Vytvoření fondu Apache Spark bez serveru

1. V synapse studiu v levém podokně vyberte **Spravovat**  >  **fondy Apache Spark**.
1. Vybrat **Nový** 
1. Pro **Apache Spark název fondu** zadejte **Spark1**.
1. Jako **Velikost uzlu** zadejte **malá**.
1. Pro **počet uzlů** nastavte Minimum na 3 a maximum na 3.
1. Vyberte **Zkontrolovat a vytvořit** > **Vytvořit**. Váš fond Apache Spark bude připravený během několika sekund.

## <a name="understanding-serverless-apache-spark-pools"></a>Principy Apache Sparkch fondů bez serveru

Fond Spark bez serveru je způsob, jak určit, jak chce uživatel pracovat se Sparkem. Když začnete používat fond, v případě potřeby se vytvoří relace Spark. Fond řídí, kolik prostředků Sparku bude tato relace používat, a jak dlouho bude relace před automatickým pozastavením trvat. Platíte za prostředky Spark používané během této relace, nikoli pro samotný fond. Tímto způsobem fond Spark vám umožní pracovat se Sparkem, aniž by se museli starat o správu clusterů. To se podobá tomu, jak funguje fond SQL bez serveru.

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-spark"></a>Analýza dat NYC taxislužby v BLOB Storage pomocí Sparku

1. V synapse studiu přejít do centra pro **vývoj**
2. Vytvoří nový Poznámkový blok s výchozí jazykovou sadou nastavenou na **PySpark (Python)**.
3. Vytvořte novou buňku kódu a vložte do ní následující kód.
    ```py
    %%pyspark
    from azureml.opendatasets import NycTlcYellow

    data = NycTlcYellow()
    df = data.to_spark_dataframe()
    # Display 10 rows
    display(df.limit(10))
    ```
1. V poznámkovém bloku vyberte v nabídce **připojit k** **Spark1** fond Spark bez serveru, který jsme vytvořili dříve.
1. V buňce vyberte **Spustit** . Synapse spustí novou relaci Spark a v případě potřeby tuto buňku spustí. Pokud je potřeba nová relace Sparku, zpočátku bude trvat přibližně dva sekundy. 
1. Pokud chcete pouze zobrazit schéma pro datový rámec, spusťte buňku s následujícím kódem:

    ```py
    df.printSchema()
    ```

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>Načtení dat taxislužby NYC do databáze Spark nyctaxi

Data jsou k dispozici prostřednictvím datového rámce s názvem **data**. Načtěte ho do databáze Spark s názvem **nyctaxi**.

1. Přidejte do poznámkového bloku nový a potom zadejte následující kód:

    ```py
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```
## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Analýza dat taxislužby NYC pomocí Sparku a poznámkových bloků

1. Vraťte se do poznámkového bloku.
1. Vytvořte novou buňku kódu a zadejte následující kód. 

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Spuštěním buňky zobrazíte data NYC taxislužby, která jste načetli do databáze **nyctaxi** Spark.
1. Vytvořte novou buňku kódu a zadejte následující kód. Analyzujeme tato data a výsledky se uloží do tabulky s názvem **nyctaxi. passengercountstats**.

   ```py
   %%pyspark
   df = spark.sql("""
      SELECT PassengerCount,
          SUM(TripDistance) as SumTripDistance,
          AVG(TripDistance) as AvgTripDistance
      FROM nyctaxi.trip
      WHERE TripDistance > 0 AND PassengerCount > 0
      GROUP BY PassengerCount
      ORDER BY PassengerCount
   """) 
   display(df)
   df.write.saveAsTable("nyctaxi.passengercountstats")
   ```

1. Ve výsledcích buňky vyberte možnost **graf** , aby se zobrazila data vizuálů.


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Analýza dat pomocí vyhrazeného fondu SQL](get-started-analyze-sql-pool.md)
