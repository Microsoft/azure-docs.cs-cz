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
ms.openlocfilehash: 363f2934bbeec266c16711572620e03e69785f94
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "90007192"
---
# <a name="analyze-data-with-sql-pools"></a>Analýza dat pomocí fondů SQL

Azure synapse Analytics nabízí možnost analyzovat data pomocí fondu SQL. V tomto kurzu použijete ukázková data NYC taxislužby k prozkoumání možností analytického fondu SQL.

## <a name="link-the-nyc-taxi-sample-data-into-the-sqldb1-database"></a>Propojit ukázková data NYC taxislužby do databáze SQLDB1

1. V synapse studiu přejděte do centra **dat** vlevo.
1. Klikněte na **+** a vyberte **Procházet ukázky**. Tím se otevře **ukázkové centrum** a otevřete kartu **datové sady** .
1. Vyberte **NYC taxislužby & Limousine provize – žluté taxislužby**. Tato datová sada obsahuje více než 1 500 000 000 řádků.
1. Klikněte na **Přidat datovou sadu**
1. V **datovém** centru pod **odkazem** se zobrazí nová datová sada v tomto umístění **Azure Blob Storage > ukázkové datové sady > nyc_tlc_yellow**   
1. Na kartě s popisem **dotazu ukázková data**vyberte fond SQL s názvem **SQLDB1**.


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

