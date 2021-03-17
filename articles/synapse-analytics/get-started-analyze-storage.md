---
title: 'Kurz: Začínáme s analýzou dat v účtech úložiště'
description: V tomto kurzu se dozvíte, jak analyzovat data umístěná v účtu úložiště.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 71ba3d99ceee89464dafdf5bf4c16e70df146bef
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102426072"
---
# <a name="analyze-data-in-a-storage-account"></a>Analýza dat v účtu úložiště

V tomto kurzu se dozvíte, jak analyzovat data umístěná v účtu úložiště.

## <a name="overview"></a>Přehled

Zatím jsme pokryli scénáře, kdy se data nacházejí v databázích v pracovním prostoru. Teď vám ukážeme, jak pracovat se soubory v účtech úložiště. V tomto scénáři použijeme primární účet úložiště pracovního prostoru a kontejneru, který jsme určili při vytváření pracovního prostoru.

* Název účtu úložiště: **contosolake**
* Název kontejneru v účtu úložiště: **Uživatelé**

### <a name="create-csv-and-parquet-files-in-your-storage-account"></a>Vytváření souborů CSV a Parquet v účtu úložiště

Spusťte následující kód v poznámkovém bloku v nové buňce kódu. Vytvoří soubor CSV a soubor Parquet v účtu úložiště.

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats_csvformat")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats_parquetformat")
```

### <a name="analyze-data-in-a-storage-account"></a>Analýza dat v účtu úložiště

Můžete analyzovat data ve výchozím účtu ADLS Gen2 v pracovním prostoru nebo můžete k pracovnímu prostoru propojit účet ADLS Gen2 nebo úložiště objektů BLOB pomocí možnosti "**Spravovat**" > "**propojené služby**" > "**New**" (následující postup bude odkazovat na primární adls Gen2 účet).

1. V synapse studiu otevřete centrum **dat** a pak vyberte **propojit**.
1. Přejít na **Azure Data Lake Storage Gen2**  >  **MyWorkspace (Primary-contosolake)**.
1. Vyberte možnost **Uživatelé (primární)**. Měla by se zobrazit složka **NYCTaxi** . Uvnitř byste měli vidět dvě složky s názvem **PassengerCountStats_csvformat** a **PassengerCountStats_parquetformat**.
1. Otevřete složku **PassengerCountStats_parquetformat** . Uvnitř se zobrazí soubor Parquet s názvem, jako je `part-00000-2638e00c-0790-496b-a523-578da9a15019-c000.snappy.parquet` .
1. Klikněte pravým tlačítkem myši na **. Parquet**, vyberte **Nový Poznámkový blok** a pak vyberte **načíst do datového rámce**. Vytvoří se nový Poznámkový blok s buňkou, jako je tato:

    ```py
    %%pyspark
    df = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet', format='parquet')
    display(df.limit(10))
    ```

1. Připojte se k fondu Spark s názvem **Spark1**. Spusťte buňku.
1. Klikněte zpátky do složky **Uživatelé** . Znovu klikněte pravým tlačítkem na soubor **. Parquet** a pak vyberte **Nový skript SQL**  >  **Vybrat horní 100 řádků**. Vytvoří skript SQL podobný tomuto:

    ```sql
    SELECT 
        TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [result]
    ```

    V okně skriptu se ujistěte, že je pole **připojit k** nastavené na **integrovaný** fond SQL bez serveru.

1. Spusťte skript.



## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Orchestrace aktivit s kanály](get-started-pipelines.md)
