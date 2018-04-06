---
title: Vývoj skriptů U-SQL pomocí nástrojů Data Lake pro Visual Studio | Dokumentace Microsoftu
description: Naučte se nainstalovat nástroje Data Lake pro Visual Studio a vyvíjet a testovat skripty U-SQL.
services: data-lake-analytics
documentationcenter: ''
author: saveenr
manager: jhubbard
editor: cgronlun
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/28/2017
ms.author: saveenr, yanacai
ms.openlocfilehash: b94c035083a00867139b6c58bc8b2ded08e970b0
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="develop-u-sql-scripts-by-using-data-lake-tools-for-visual-studio"></a>Vývoj skriptů U-SQL pomocí nástrojů Data Lake pro Visual Studio
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]


Naučte se používat sadu Visual Studio k vytváření účtů Azure Data Lake Analytics, definování úloh v [U-SQL](data-lake-analytics-u-sql-get-started.md) a odesílání úloh do služby Data Lake Analytics. Další informace o Data Lake Analytics najdete v tématu [Přehled Azure Data Lake Analytics](data-lake-analytics-overview.md).


## <a name="prerequisites"></a>Požadavky

* **Visual Studio:** Podporovány jsou všechny edice kromě Express.
    * Visual Studio 2017
    * Visual Studio 2015
    * Visual Studio 2013
* Sada **Microsoft Azure SDK pro .NET** verze 2.7.1 nebo novější.  Nainstalujte ji pomocí [Instalačního programu webové platformy](http://www.microsoft.com/web/downloads/platform.aspx).
* **Účet Data Lake Analytics**. Informace o vytvoření účtu najdete v tématu [Začínáme s Azure Data Lake Analytics pomocí webu Azure Portal](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio"></a>Instalace nástrojů Azure Data Lake pro Visual Studio

### <a name="install-azure-data-lake-tools-for-visual-studio-2017"></a>Instalace nástrojů Azure Data Lake pro Visual Studio 2017

Nástroje Azure Data Lake pro Visual Studio se podporují v sadě Visual Studio 2017 verze 15.3 nebo novější. Tento nástroj je součástí sad funkcí **Ukládání a zpracování dat** a **Vývoj pro Azure** v Instalačním programu pro Visual Studio. V rámci instalace sady Visual Studio povolte jednu z těchto dvou sad funkcí.  

Sadu funkcí **Ukládání a zpracování dat** povolíte následujícím způsobem: ![Povolení sady funkcí Ukládání a zpracování dat](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2017-install-01.png)

Sadu funkcí **Vývoj pro Azure** povolíte následujícím způsobem: ![Povolení sady funkcí Vývoj pro Azure](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2017-install-02.png)

### <a name="install-azure-data-lake-tools-for-visual-studio-2013-and-2015"></a>Instalace nástrojů Azure Data Lake pro Visual Studio 2013 a 2015

Stáhněte nástroje Azure Data Lake pro Visual Studio z webu [Download Center](http://aka.ms/adltoolsvs) a nainstalujte je. Po instalaci si všimněte, že:
* Uzel **Průzkumník serveru** > **Azure** obsahuje uzel **Data Lake Analytics**. 
* Nabídka **Nástroje** obsahuje položku **Data Lake**.

## <a name="connect-to-an-azure-data-lake-analytics-account"></a>Připojení k účtu Azure Data Lake Analytics

1. Otevřete sadu Visual Studio.
2. Otevřete Průzkumníka serveru výběrem **Zobrazení** > **Průzkumník serveru**.
3. Klikněte pravým tlačítkem na **Azure**. Pak vyberte **Připojit k předplatnému Microsoft Azure** a postupujte podle pokynů.
4. V Průzkumníku serveru vyberte **Azure** > **Data Lake Analytics**. Zobrazí se seznam vašich účtů Data Lake Analytics.


## <a name="write-your-first-u-sql-script"></a>Napsání prvního skriptu U-SQL

Následující text je jednoduchý skript U-SQL. Definuje malou datovou sadu a zapíše ji do výchozího úložiště Data Lake Store jako soubor s názvem `/data.csv`.

```
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

### <a name="submit-a-data-lake-analytics-job"></a>Odeslání úlohy Data Lake Analytics

1. Vyberte **Soubor** > **Nový** > **Projekt**.

2. Vyberte typ **Projekt U-SQL** a pak klikněte na **OK**. Visual Studio vytvoří řešení se souborem **Script.usql**.

3. Vložte předchozí skript do okna **Script.usql**.

4. V levém horním rohu okna **Script.usql** zadejte účet Data Lake Analytics.

    ![Odeslání projektu U-SQL sady Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job.png)

5. V levém horním rohu okna **Script.usql** vyberte **Odeslat**.
6. Zkontrolujte **Účet Analytics** a pak vyberte **Odeslat**. Po dokončení odeslání jsou výsledky odeslání dostupné ve výsledcích nástrojů Data Lake pro Visual Studio.

    ![Odeslání projektu U-SQL sady Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job-advanced.png)
7. Pokud chcete zobrazit nejnovější stav úlohy a aktualizovat obrazovku, klikněte na **Aktualizovat**. Když se úloha úspěšně dokončí, zobrazí se **Graf úlohy**, **Operace s metadaty**, **Historie stavů**, **Diagnostika**:

    ![Graf výkonu úlohy U-SQL Visual Studio Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-performance-graph.png)

   * **Souhrn úlohy** zobrazuje souhrn úlohy.   
   * **Podrobnosti o úloze** zobrazují konkrétnější informace o úloze, včetně skriptu, prostředků a vrcholů.
   * **Graf úlohy** vizualizuje průběh úlohy.
   * **Operace s metadaty** zobrazují všechny akce provedené s katalogem U-SQL.
   * **Data** zobrazují všechny vstupy a výstupy.
   * **Diagnostika** poskytuje pokročilé analýzy spouštění úlohy a optimalizace výkonu.

### <a name="to-check-job-state"></a>Postup kontroly stavu úlohy

1. V Průzkumníku serveru vyberte **Azure** > **Data Lake Analytics**. 
2. Rozbalte název účtu Data Lake Analytics.
3. Dvakrát klikněte na **Úlohy**.
4. Vyberte úlohu, kterou jste dříve odeslali.

### <a name="to-see-the-output-of-a-job"></a>Zobrazení výstupu úlohy

1. V Průzkumníku serveru přejděte na úlohu, kterou jste odeslali.
2. Klikněte na kartu **Data**.
3. Na kartě **Výstupy úlohy** vyberte soubor `"/data.csv"`.

## <a name="next-steps"></a>Další kroky

* [Spouštění skriptů U-SQL na vlastní pracovní stanici za účelem testování a ladění](data-lake-analytics-data-lake-tools-local-run.md)
* [Ladění kódu C# v úlohách U-SQL pomocí nástrojů Azure Data Lake pro Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
* [Použití nástrojů Azure Data Lake pro Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
