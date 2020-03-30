---
title: Dotaz na Azure Data Lake Analytics – Visual Studio
description: Naučte se nainstalovat nástroje Data Lake pro Visual Studio a vyvíjet a testovat skripty U-SQL.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: dacce0d4f40f077b5da6221000192a4398da99e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260342"
---
# <a name="develop-u-sql-scripts-by-using-data-lake-tools-for-visual-studio"></a>Vývoj skriptů U-SQL pomocí nástrojů Data Lake pro Visual Studio

[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Nástroje Azure Data Lake a Stream Analytics zahrnují funkce související se dvěma službami Azure, Azure Data Lake Analytics a Azure Stream Analytics. Další informace o scénářích Azure Stream Analytics najdete v [tématu nástroje Azure Stream Analytics pro Visual Studio](../stream-analytics/stream-analytics-tools-for-visual-studio-install.md).

Tento článek popisuje, jak pomocí Visual Studia vytvořit účty Azure Data Lake Analytics. Můžete definovat úlohy v [U-SQL](data-lake-analytics-u-sql-get-started.md)a odeslat úlohy do služby Data Lake Analytics. Další informace o službě Data Lake Analytics najdete v [tématu Přehled Azure Data Lake Analytics](data-lake-analytics-overview.md).

> [!IMPORTANT]
> Doporučujeme upgradovat na Nástroje datového jezera Azure pro Visual Studio verze 2.3.3000.4 nebo novější. Předchozí verze jsou zastaralé a nejsou už k dispozici ke stažení.
>
> 1. Zkontrolujte, jestli používáte Nástroje Azure Data Lake pro Visual Studio verze starší než 2.3.3000.4.
>
>    ![Kontrola verze nástrojů](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-about-data-lake.png)
>
> 1. Pokud je vaše verze starší než 2.3.3000.4, navštivte centrum pro stahování a aktualizujte Nástroje Azure Data Lake pro Visual Studio:
>    - [Pro Visual Studio 2017 a 2019](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools)
>    - [Visual Studio 2013 a 2015](https://www.microsoft.com/en-us/download/details.aspx?id=49504)

## <a name="prerequisites"></a>Požadavky

* **Visual Studio:** Podporovány jsou všechny edice kromě Express.

  * Visual Studio 2019
  * Visual Studio 2017
  * Visual Studio 2015
  * Visual Studio 2013

* Sada **Microsoft Azure SDK pro .NET** verze 2.7.1 nebo novější. Nainstalujte ji pomocí [Instalačního programu webové platformy](https://www.microsoft.com/web/downloads/platform.aspx).
* **Účet Data Lake Analytics**. Informace o vytvoření účtu najdete v tématu [Začínáme s Azure Data Lake Analytics pomocí webu Azure Portal](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio"></a>Instalace nástrojů Azure Data Lake pro Visual Studio

Tento kurz vyžaduje, aby byly nainstalované Nástroje Data Lake pro Visual Studio. Další informace naleznete v [tématu Instalace nástrojů datového jezera pro visual studio](data-lake-analytics-data-lake-tools-install.md).

## <a name="connect-to-an-azure-data-lake-analytics-account"></a>Připojení k účtu Azure Data Lake Analytics

1. Otevřete sadu Visual Studio.

1. Sem **Sem Explorer serveru** vyberte **Zobrazit** > **Průzkumníka serveru**.

1. Klikněte pravým tlačítkem na **Azure**a pak vyberte **Připojit k předplatnému Microsoft Azure**. **Přihlaste se ke svému účtu**, postupujte podle pokynů.

1. V **Průzkumníkovi serveru**vyberte **Azure** > **Data Lake Analytics**. Zobrazí se seznam vašich účtů Data Lake Analytics.

## <a name="write-your-first-u-sql-script"></a>Napsání prvního skriptu U-SQL

Následující text je jednoduchý skript U-SQL. Definuje malou datovou sadu a zapíše ji do výchozího úložiště Data Lake Store jako soubor s názvem `/data.csv`.

```sql
USE DATABASE master;
USE SCHEMA dbo;
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

## <a name="submit-a-data-lake-analytics-job"></a>Odeslání úlohy Data Lake Analytics

1. V sadě Visual Studio vyberte **Soubor** > **nový** > **projekt**.

1. Vyberte typ **projektu U-SQL** a pak vyberte **Další**. V **okně Konfigurace nového projektu**vyberte **vytvořit**.

   Visual Studio vytvoří řešení, které obsahuje soubor **Script.usql.**

1. Vložte skript z [Napište svůj první U-SQL skript](#write-your-first-u-sql-script) do okna **Script.usql.**

1. V **Průzkumníku řešení**klepněte pravým tlačítkem myši na **soubor Script.usql**a vyberte **příkaz Odeslat skript**.

1. V **možnosti Odeslat úlohu**zvolte svůj účet Data Lake Analytics a vyberte **Odeslat**.

   ![Odeslání projektu U-SQL sady Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-submit-job-vs2019.png)

Po odeslání úlohy se otevře karta **Zobrazení úlohy**, na které se zobrazí průběh úlohy.

* **Souhrn úlohy** zobrazuje souhrn úlohy.
* **Graf úlohy** vizualizuje průběh úlohy.
* **Operace s metadaty** zobrazují všechny akce provedené s katalogem U-SQL.
* **Data** zobrazují všechny vstupy a výstupy.
* **Historie stavu** ukazuje časovou osu a podrobnosti o stavu.
* **Analýza AU** ukazuje, kolik au byly použity v úloze a prozkoumat simulace různých strategií přidělení AU.
* **Diagnostika** poskytuje pokročilé analýzy spouštění úlohy a optimalizace výkonu.

![Graf výkonu úlohy U-SQL Visual Studio Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-performance-graph.png)

Chcete-li zobrazit nejnovější stav úlohy a aktualizovat obrazovku, vyberte **aktualizovat**.

## <a name="check-job-status"></a>Kontrola stavu úlohy

1. V **Průzkumníkovi serveru**vyberte **Azure** > **Data Lake Analytics**.

1. Rozbalte název účtu Data Lake Analytics.

1. Dvakrát klikněte na **Úlohy**.

1. Vyberte úlohu, kterou jste dříve odeslali.

## <a name="see-the-job-output"></a>Zobrazení výstupu úlohy

1. V **Průzkumníkovi serveru**přejděte k úloze, kterou jste odeslali.

1. Klikněte na kartu **Data**.

1. Na kartě **Výstupy úlohy** vyberte soubor `"/data.csv"`.

## <a name="next-steps"></a>Další kroky

* [Spouštění skriptů U-SQL na vlastní pracovní stanici za účelem testování a ladění](data-lake-analytics-data-lake-tools-local-run.md)
* [Ladění kódu C# v úlohách U-SQL pomocí nástrojů Azure Data Lake pro Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
* [Použití nástrojů Azure Data Lake pro Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
