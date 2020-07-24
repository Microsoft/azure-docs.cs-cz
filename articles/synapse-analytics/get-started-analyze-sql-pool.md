---
title: 'Kurz: Začínáme s analýzou dat pomocí fondu SQL'
description: V tomto kurzu použijete ukázková data NYC taxislužby k prozkoumání možností analytického fondu SQL.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 948de6968d336d16554fe0a0c2f7d0c5b7367f15
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87101495"
---
# <a name="analyze-data-with-sql-pools"></a>Analýza dat pomocí fondů SQL

Azure synapse Analytics nabízí možnost analyzovat data pomocí fondu SQL. V tomto kurzu použijete ukázková data NYC taxislužby k prozkoumání možností analytického fondu SQL.

## <a name="load-the-nyc-taxi-sample-data-into-the-sqldb1-database"></a>Načtení ukázkových dat NYC taxislužby do databáze SQLDB1

1. V synapse studiu v nabídce nejvyšší úrovně na modré nabídce vyberte ikonu otazníku (**?**).
1. Vyberte **Začínáme**s  >  **úvodním startem**.
1. Na kartě s popisem **dotazu ukázková data**vyberte fond SQL s názvem **SQLDB1**.
1. Vyberte **data dotazu**. Krátce se zobrazí oznámení "načtení ukázkových dat". Světle modrý stavový řádek v horní části synapse studia indikuje, že se data načítají do SQLDB1.
1. Jakmile se stavový řádek změní na zelený, zavřete ho.

## <a name="explore-the-nyc-taxi-data-in-the-sql-pool"></a>Prozkoumejte data NYC taxislužby ve fondu SQL.

1. V synapse studiu přejdete do centra **dat** .
1. Přejít na **SQLDB1**  >  **tabulky**SQLDB1. Zobrazí se několik načtených tabulek.
1. Klikněte pravým tlačítkem na **dbo. Tabulka cest** a výběr **nového skriptu SQL**  >  **Vyberte horní 100 řádků**.
1. Počkejte, než se vytvoří a spustí nový skript SQL.
1. Všimněte si, že v horní části SQL Script **Connect** se automaticky nastaví fond SQL s názvem **SQLDB1**.
1. Nahraďte text skriptu SQL tímto kódem a spusťte ho.

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.Trip
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount
    ```

    Tento dotaz ukazuje, jak celková vzdálenost cest a Průměrná doba odezvy se vztahují k počtu cestujících.
1. V okně výsledek skriptu SQL změňte **zobrazení** na **graf** , aby se zobrazila vizualizace výsledků jako spojnicový graf.



## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Analýza pomocí Sparku](get-started-analyze-spark.md)

