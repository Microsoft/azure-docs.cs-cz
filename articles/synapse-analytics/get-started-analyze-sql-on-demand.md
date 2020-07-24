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
ms.openlocfilehash: 4011cd93879d9203d8231f24bbf531d14e6e815a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87101486"
---
# <a name="analyze-data-with-sql-on-demand"></a>Analýza dat pomocí SQL na vyžádání

V tomto kurzu se naučíte analyzovat data pomocí SQL na vyžádání pomocí dat umístěných v databázích Spark. 

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
