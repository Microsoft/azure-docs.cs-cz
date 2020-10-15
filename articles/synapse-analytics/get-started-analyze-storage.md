---
title: 'Kurz: Začínáme s analýzou dat v účtech úložiště'
description: V tomto kurzu se dozvíte, jak analyzovat data umístěná v účtu úložiště.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: overview
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 0b2978335596dd256aa69007245cb86b35a75417
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92090569"
---
# <a name="analyze-data-in-a-storage-account"></a>Analýza dat v účtu úložiště

V tomto kurzu se dozvíte, jak analyzovat data umístěná v účtu úložiště.

## <a name="overview"></a>Přehled

Zatím jsme pokryli scénáře, kdy se data nacházejí v databázích v pracovním prostoru. Teď vám ukážeme, jak pracovat se soubory v účtech úložiště. V tomto scénáři použijeme primární účet úložiště pracovního prostoru a kontejneru, který jsme určili při vytváření pracovního prostoru.

* Název účtu úložiště: **contosolake**
* Název kontejneru v účtu úložiště: **Uživatelé**

### <a name="create-csv-and-parquet-files-in-your-storage-account"></a>Vytváření souborů CSV a Parquet v účtu úložiště

Spusťte následující kód v poznámkovém bloku. Vytvoří soubor CSV a soubor Parquet v účtu úložiště.

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats.csv")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats.parquet")
```

### <a name="analyze-data-in-a-storage-account"></a>Analýza dat v účtu úložiště

1. V synapse studiu otevřete centrum **dat** a pak vyberte **propojit**.
1. Přejít na **účty úložiště**  >  **MyWorkspace (Primary-contosolake)**.
1. Vyberte možnost **Uživatelé (primární)**. Měla by se zobrazit složka **NYCTaxi** . Uvnitř byste měli vidět dvě složky s názvem **PassengerCountStats.csv** a **PassengerCountStats. Parquet**.
1. Otevřete složku **PassengerCountStats. Parquet** . Uvnitř se zobrazí soubor Parquet s názvem, jako je `part-00000-2638e00c-0790-496b-a523-578da9a15019-c000.snappy.parquet` .
1. Klikněte pravým tlačítkem na **. Parquet**a pak vyberte **Nový Poznámkový blok**. Vytvoří Poznámkový blok, který má podobné buňky:

    ```py
    %%pyspark
    data_path = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet', format='parquet')
    data_path.show(100)
    ```

1. Spusťte buňku.
1. Klikněte pravým tlačítkem na soubor Parquet uvnitř a pak vyberte **Nový skript SQL**  >  **Vybrat horní 100 řádků**. Vytvoří skript SQL podobný tomuto:

    ```sql
    SELECT TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [r];
    ```

    V okně skriptu je pole **připojit k** **na vyžádání**nastaveno na hodnotu SQL.

1. Spusťte skript.



## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Orchestrace aktivit s kanály](get-started-pipelines.md)
