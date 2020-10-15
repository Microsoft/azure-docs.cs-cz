---
title: 'Kurz: Začínáme s analýzou dat pomocí serverles SQL'
description: V tomto kurzu se naučíte analyzovat data pomocí SQL na vyžádání pomocí dat umístěných v databázích Spark.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: fa87ee06e0ed01828148a58b2d2f754a40d8803a
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92088461"
---
# <a name="analyze-data-with-sql-on-demand"></a>Analýza dat pomocí SQL na vyžádání

V tomto kurzu se dozvíte, jak analyzovat data s využitím SQL serveru bez serveru pomocí služby SQL na vyžádání s využitím dat umístěných v databázích Spark. 

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-sql-on-demand-pool"></a>Analýza dat NYC taxislužby v BLOB Storage s využitím fondu SQL na vyžádání

1. V **datovém** centru pod **odkazem**klikněte pravým tlačítkem na **Azure Blob Storage > ukázkové datové sady > nyc_tlc_yellow** a vyberte **Vybrat prvních 100 řádků** .
1. Tím se vytvoří nový skript SQL s následujícím kódem:

    ```
    SELECT
        TOP 100 *
    FROM
        OPENROWSET(
            BULK     'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
            FORMAT = 'parquet'
        ) AS [result];
    ```
1. Klikněte na **Spustit** .

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-sql-on-demand"></a>Analýza dat NYC taxislužby v databázích Sparku pomocí SQL na vyžádání

Tabulky v databázích Spark jsou automaticky viditelné a mohou být dotazovány na vyžádání SQL.

1. V synapse studiu přejdete do centra pro **vývoj** a vytvoříte nový skript SQL.
1. Nastavte **připojení k** **SQL na vyžádání**.
1. Vložte do skriptu následující text a spusťte skript.

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```

    > [!NOTE]
    > Při prvním spuštění dotazu, který používá SQL na vyžádání, trvá SQL na vyžádání přibližně 10 sekund pro shromáždění prostředků SQL potřebných ke spuštění dotazů. Následné dotazy budou mnohem rychlejší.
  


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Analýza dat v úložišti](get-started-analyze-storage.md)
