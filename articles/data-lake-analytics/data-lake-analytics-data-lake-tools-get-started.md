---
title: Dotaz Azure Data Lake Analytics – Visual Studio
description: Naučte se nainstalovat nástroje Data Lake pro Visual Studio a vyvíjet a testovat skripty U-SQL.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 08/30/2019
ms.openlocfilehash: d3812cb27d2d9ea7a49038f566f8b4cc5d779172
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2020
ms.locfileid: "95241688"
---
# <a name="develop-u-sql-scripts-by-using-data-lake-tools-for-visual-studio"></a>Vývoj skriptů U-SQL pomocí nástrojů Data Lake pro Visual Studio

[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Nástroje Azure Data Lake a Stream Analytics zahrnují funkce související se dvěma službami Azure, Azure Data Lake Analytics a Azure Stream Analytics. Další informace o scénářích Azure Stream Analytics naleznete v tématu [Azure Stream Analytics Tools for Visual Studio](../stream-analytics/stream-analytics-tools-for-visual-studio-install.md).

Tento článek popisuje, jak pomocí sady Visual Studio vytvořit účty Azure Data Lake Analytics. Můžete definovat úlohy v [u-SQL](data-lake-analytics-u-sql-get-started.md)a odesílat úlohy do služby Data Lake Analytics. Další informace o Data Lake Analytics najdete v tématu [Azure Data Lake Analytics Overview](data-lake-analytics-overview.md).

> [!IMPORTANT]
> Doporučujeme, abyste upgradovali na verzi Nástroje Azure Data Lake pro Visual Studio 2.3.3000.4 nebo novější. Předchozí verze jsou zastaralé a nejsou už k dispozici ke stažení.
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

Tento kurz vyžaduje, aby byly nainstalované Nástroje Data Lake pro Visual Studio. Další informace najdete v tématu [Instalace nástrojů Data Lake pro Visual Studio](data-lake-analytics-data-lake-tools-install.md).

## <a name="connect-to-an-azure-data-lake-analytics-account"></a>Připojení k účtu Azure Data Lake Analytics

1. Otevřete sadu Visual Studio.

1. Otevřete **aplikaci Data Lake Analytics Explorer** výběrem možnosti **Zobrazit**  >  **Data Lake Analytics Explorer**.

1. Klikněte pravým tlačítkem na **Azure** a pak vyberte **připojit k Microsoft Azure předplatnému**. Při **přihlašování ke svému účtu** postupujte podle pokynů.

1. V **Průzkumník serveru** vyberte **Azure**  >  **Data Lake Analytics**. Zobrazí se seznam vašich účtů Data Lake Analytics.

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

1. V aplikaci Visual Studio vyberte **soubor**  >  **Nový**  >  **projekt**.

1. Vyberte typ **projektu U-SQL** a pak vyberte **Další**. V **konfiguraci nového projektu** vyberte **vytvořit**.

   Visual Studio vytvoří řešení, které obsahuje soubor **script. usql** .

1. Vložte skript z [napsání prvního skriptu U-SQL](#write-your-first-u-sql-script) do okna **script. usql** .

1. V **Průzkumník řešení** klikněte pravým tlačítkem myši na **script. Usql** a vyberte **Odeslat skript**.

1. V možnosti **Odeslat úlohu** zvolte účet Data Lake Analytics a vyberte **Odeslat**.

   ![Odeslání projektu U-SQL sady Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-submit-job-vs2019.png)

Po odeslání úlohy se otevře karta **Zobrazení úlohy**, na které se zobrazí průběh úlohy.

* **Souhrn úlohy** zobrazuje souhrn úlohy.
* **Graf úlohy** vizualizuje průběh úlohy.
* **Operace s metadaty** zobrazují všechny akce provedené s katalogem U-SQL.
* **Data** zobrazují všechny vstupy a výstupy.
* **Historie stavu** ukazuje časovou osu a podrobnosti o stavu.
* **Analýza au** ukazuje, kolik jednotek Austrálie se v úloze použilo, a prozkoumejte simulace různých strategií přidělení au.
* **Diagnostika** poskytuje pokročilé analýzy spouštění úlohy a optimalizace výkonu.

![Graf výkonu úlohy U-SQL Visual Studio Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-performance-graph.png)

Pokud chcete zobrazit nejnovější stav úlohy a aktualizovat obrazovku, vyberte **aktualizovat**.

## <a name="check-job-status"></a>Kontrola stavu úlohy

1. V **Průzkumník serveru** vyberte **Azure**  >  **Data Lake Analytics**.

1. Rozbalte název účtu Data Lake Analytics.

1. Dvakrát klikněte na **Úlohy**.

1. Vyberte úlohu, kterou jste dříve odeslali.

## <a name="see-the-job-output"></a>Zobrazení výstupu úlohy

1. V **Průzkumník serveru** přejděte do úlohy, kterou jste odeslali.

1. Klikněte na kartu **Data**.

1. Na kartě **Výstupy úlohy** vyberte soubor `"/data.csv"`.

## <a name="next-steps"></a>Další kroky

* [Spouštění skriptů U-SQL na vlastní pracovní stanici za účelem testování a ladění](data-lake-analytics-data-lake-tools-local-run.md)
* [Ladění kódu C# v úlohách U-SQL pomocí nástrojů Azure Data Lake pro Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
* [Použití nástrojů Azure Data Lake pro Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
