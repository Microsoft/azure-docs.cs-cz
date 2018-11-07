---
title: Začínáme s Azure Data Lake Analytics s využitím sady Visual Studio
description: Naučte se nainstalovat nástroje Data Lake pro Visual Studio a vyvíjet a testovat skripty U-SQL.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.topic: conceptual
ms.date: 08/13/2018
ms.openlocfilehash: c41ac2246386b8838d1310a9cd5c03dd0cf5d8e4
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51244504"
---
# <a name="develop-u-sql-scripts-by-using-data-lake-tools-for-visual-studio"></a>Vývoj skriptů U-SQL pomocí nástrojů Data Lake pro Visual Studio
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Naučte se používat sadu Visual Studio k vytváření účtů Azure Data Lake Analytics, definování úloh v [U-SQL](data-lake-analytics-u-sql-get-started.md) a odesílání úloh do služby Data Lake Analytics. Další informace o Data Lake Analytics najdete v tématu [Přehled Azure Data Lake Analytics](data-lake-analytics-overview.md).

>[!IMPORTANT]
> Microsoft doporučuje, abyste Nástroje Azure Data Lake pro Visual Studio upgradovali na verzi 2.3.3000.4 nebo novější. Předchozí verze jsou zastaralé a nejsou už k dispozici ke stažení. 
>
>**Co potřeba udělat?**
>
>1. Zkontrolujte, jestli používáte Nástroje Azure Data Lake pro Visual Studio verze starší než 2.3.3000.4. 
>   
>   ![Kontrola verze nástrojů](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-about-data-lake.png)
> 
>2. Pokud je vaše verze starší než 2.3.3000.4, navštivte centrum pro stahování a aktualizujte Nástroje Azure Data Lake pro Visual Studio: 
>    - [Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools)
>    - [Visual Studio 2013 a 2015](https://www.microsoft.com/en-us/download/details.aspx?id=49504)


## <a name="prerequisites"></a>Požadavky

* **Visual Studio:** Podporovány jsou všechny edice kromě Express.
    * Visual Studio 2017
    * Visual Studio 2015
    * Visual Studio 2013
* Sada **Microsoft Azure SDK pro .NET** verze 2.7.1 nebo novější.  Nainstalujte ji pomocí [Instalačního programu webové platformy](https://www.microsoft.com/web/downloads/platform.aspx).
* **Účet Data Lake Analytics**. Informace o vytvoření účtu najdete v tématu [Začínáme s Azure Data Lake Analytics pomocí webu Azure Portal](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio"></a>Instalace nástrojů Azure Data Lake pro Visual Studio

Tento kurz vyžaduje, aby byly nainstalované Nástroje Data Lake pro Visual Studio. Postupujte podle [pokynů k instalaci](data-lake-analytics-data-lake-tools-install.md).

## <a name="connect-to-an-azure-data-lake-analytics-account"></a>Připojení k účtu Azure Data Lake Analytics

1. Otevřete sadu Visual Studio.

2. Otevřete Průzkumníka serveru výběrem **Zobrazení** > **Průzkumník serveru**.

3. Klikněte pravým tlačítkem na **Azure**. Pak vyberte **Připojit k předplatnému Microsoft Azure** a postupujte podle pokynů.

4. V Průzkumníku serveru vyberte **Azure** > **Data Lake Analytics**. Zobrazí se seznam vašich účtů Data Lake Analytics.

## <a name="write-your-first-u-sql-script"></a>Napsání prvního skriptu U-SQL

Následující text je jednoduchý skript U-SQL. Definuje malou datovou sadu a zapíše ji do výchozího úložiště Data Lake Store jako soubor s názvem `/data.csv`.

```
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

1. Vyberte **Soubor** > **Nový** > **Projekt**.

2. Vyberte typ **Projekt U-SQL** a pak klikněte na **OK**. Visual Studio vytvoří řešení se souborem **Script.usql**.

3. Vložte předchozí skript do okna **Script.usql**.

4. V levém horním rohu okna **Script.usql** zadejte účet Data Lake Analytics.

    ![Odeslání projektu U-SQL sady Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job.png)

5. V levém horním rohu okna **Script.usql** vyberte **Odeslat**.

6. Po odeslání úlohy se otevře karta **Zobrazení úlohy**, na které se zobrazí průběh úlohy. Pokud chcete zobrazit nejnovější stav úlohy a aktualizovat obrazovku, klikněte na **Aktualizovat**.

    ![Graf výkonu úlohy U-SQL Visual Studio Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-performance-graph.png)

   * **Souhrn úlohy** zobrazuje souhrn úlohy.   
   * **Graf úlohy** vizualizuje průběh úlohy.
   * **Operace s metadaty** zobrazují všechny akce provedené s katalogem U-SQL.
   * **Data** zobrazují všechny vstupy a výstupy.
   * **Historie stavu** ukazuje časovou osu a podrobnosti o stavu.
   * **Analýza AU** ukazuje, kolik se v úloze použilo jednotek analýzy, a umožňuje prozkoumat simulace odlišných strategií přidělení jednotek analýzy.
   * **Diagnostika** poskytuje pokročilé analýzy spouštění úlohy a optimalizace výkonu.

## <a name="check-job-status"></a>Kontrola stavu úlohy

1. V Průzkumníku serveru vyberte **Azure** > **Data Lake Analytics**.

2. Rozbalte název účtu Data Lake Analytics.

3. Dvakrát klikněte na **Úlohy**.

4. Vyberte úlohu, kterou jste dříve odeslali.

## <a name="see-the-job-output"></a>Zobrazení výstupu úlohy

1. V Průzkumníku serveru přejděte na úlohu, kterou jste odeslali.

2. Klikněte na kartu **Data**.

3. Na kartě **Výstupy úlohy** vyberte soubor `"/data.csv"`.

## <a name="next-steps"></a>Další postup

* [Spouštění skriptů U-SQL na vlastní pracovní stanici za účelem testování a ladění](data-lake-analytics-data-lake-tools-local-run.md)
* [Ladění kódu C# v úlohách U-SQL pomocí nástrojů Azure Data Lake pro Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
* [Použití nástrojů Azure Data Lake pro Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
