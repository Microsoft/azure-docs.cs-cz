---
title: 'Kurz: Začínáme s analýzou dat pomocí neserverového fondu SQL'
description: V tomto kurzu se naučíte analyzovat data pomocí neserverového fondu SQL pomocí dat umístěných v databázích Spark.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 4ca9ababbeb7843f1a014a4bd51a5e24a74acbae
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322941"
---
# <a name="analyze-data-with-serverless-sql-pool-in-azure-synapse-analytics"></a>Analýza dat pomocí neserverového fondu SQL ve službě Azure synapse Analytics

V tomto kurzu se naučíte analyzovat data pomocí neserverového fondu SQL pomocí dat umístěných v databázích Spark. 

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-serverless-sql-pool"></a>Analýza dat NYC taxislužby v BLOB Storage s využitím fondu SQL bez serveru

1. V **datovém** centru pod **odkazem** klikněte pravým tlačítkem na **Azure Blob Storage > ukázkové datové sady > nyc_tlc_yellow** a vyberte **Vybrat prvních 100 řádků** .
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

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-serverless-sql-pool"></a>Analýza dat NYC taxislužby v databázích Sparku pomocí SQL fondu bez serveru

Tabulky v databázích Spark jsou automaticky viditelné a mohou být dotazovány fondem SQL bez serveru.

1. V synapse studiu přejdete do centra pro **vývoj** a vytvoříte nový skript SQL.
1. Nastavte **připojení k** **fondu SQL bez serveru**.
1. Vložte do skriptu následující text a spusťte skript.

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```

    > [!NOTE]
    > Při prvním spuštění dotazu, který používá fond SQL bez serveru, trvá přibližně 10 sekund pro shromažďování prostředků SQL bez serveru potřebných ke spuštění vašich dotazů. Následné dotazy budou mnohem rychlejší.
  


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Analýza dat v úložišti](get-started-analyze-storage.md)
