---
title: 'Kurz: Začínáme s analýzou dat pomocí SQL na vyžádání'
description: V tomto kurzu se naučíte analyzovat data pomocí SQL na vyžádání pomocí dat umístěných v databázích Spark.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 93ebc1c5e89e54f4813f270b9f8b7b13f672fbe3
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2020
ms.locfileid: "90016108"
---
# <a name="analyze-data-with-sql-on-demand"></a>Analýza dat pomocí SQL na vyžádání

V tomto kurzu se naučíte analyzovat data pomocí SQL na vyžádání pomocí dat umístěných v databázích Spark. 

## <a name="analyze-nyc-taxi-data-in-blob-storage--using-sql-on-demand"></a>Analýza dat NYC taxislužby v úložišti objektů BLOB pomocí SQL na vyžádání

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
> [Analýza pomocí Sparku](get-started-analyze-spark.md)
