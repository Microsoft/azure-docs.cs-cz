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
ms.openlocfilehash: 7c228bfe5897b45e6345234f2ed8e0f5cfbec73a
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312786"
---
# <a name="analyze-data-with-a-serverless-sql-pool"></a>Analýza dat pomocí neserverového fondu SQL

V tomto kurzu se naučíte analyzovat data pomocí neserverového fondu SQL. 

## <a name="the-built-in-serverless-sql-pool"></a>Integrovaný fond SQL bez serveru

Neserverové fondy SQL umožňují používat SQL bez nutnosti rezervovat kapacitu. Faktura za fond SQL bez serveru je založena na množství zpracovaných dat pro spuštění dotazu a nikoli na počtu uzlů použitých ke spuštění dotazu.

Každý pracovní prostor obsahuje předem nakonfigurovaný fond SQL bez serveru s názvem **integrovaný**. 

## <a name="analyze-nyc-taxi-data-with-a-serverless-sql-pool"></a>Analýza dat taxislužby NYC pomocí neserverového fondu SQL


1. V synapse studiu přejdete do centra pro **vývoj** .
1. Vytvořte nový skript SQL.
1. Do skriptu vložte následující kód.

    ```
    SELECT
        TOP 100 *
    FROM
        OPENROWSET(
                BULK 'https://contosolake.dfs.core.windows.net/users/NYCTripSmall.parquet',
            FORMAT='PARQUET'
        ) AS [result]
    ```
1. Klikněte na **Spustit** .

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Analýza dat s využitím fondu Spark bez serveru](get-started-analyze-spark.md)
