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
ms.date: 12/31/2020
ms.openlocfilehash: 5f0a7477df2e281748c053ea8c7e7d3e79626296
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104588014"
---
# <a name="analyze-data-with-a-serverless-sql-pool"></a>Analýza dat pomocí neserverového fondu SQL

V tomto kurzu se naučíte analyzovat data pomocí neserverového fondu SQL pomocí dat umístěných v databázích Spark. 

## <a name="the-built-in-serverless-sql-pool"></a>Integrovaný fond SQL bez serveru

Neserverové fondy SQL umožňují používat SQL bez nutnosti rezervovat kapacitu. Faktura za fond SQL bez serveru je založena na množství zpracovaných dat pro spuštění dotazu a nikoli na počtu uzlů použitých ke spuštění dotazu.

Každý pracovní prostor obsahuje předem nakonfigurovaný fond SQL bez serveru s názvem **integrovaný**. 

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-serverless-sql-pool"></a>Analýza dat NYC taxislužby v BLOB Storage s využitím fondu SQL bez serveru

V této části použijete fond SQL bez serveru k analýze dat NYC taxislužby v účtu Azure Blob Storage.

1. V synapse studiu přejdete do centra pro **vývoj** .
1. Vytvořte nový skript SQL.
1. Do skriptu vložte následující kód.

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

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Analýza dat s využitím fondu Spark bez serveru](get-started-analyze-spark.md)
