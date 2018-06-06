---
title: Spusťte místně pomocí sady SDK Azure Data Lake U-SQL skriptů U-SQL
description: Tento článek popisuje postup použití nástroje Azure Data Lake pro Visual Studio pro testování a ladění úloh U-SQL na místní pracovní stanici.
services: data-lake-analytics
ms.service: data-lake-analytics
author: mumian
ms.author: yanacai
manager: kfile
editor: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.topic: conceptual
ms.date: 11/15/2016
ms.openlocfilehash: 322278f00f49f718b1ba560e9d21d0af0be49b18
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34735999"
---
# <a name="runing-u-sql-scripts-locally"></a>Spuštěním skriptů U-SQL místně

Namísto spuštění U-SQL v Azure, můžete spustit U-SQL na vlastní pole. Tomu se říká "místní spuštění" nebo "místní spuštění". 

Místní spuštění U-SQL se zohledněním v těchto nástrojů:
* Nástroje služby Azure Data Lake pro Visual Studio
* Azure Data Lake U-SQL SDK

## <a name="understand-the-data-root-folder-and-the-file-path"></a>Pochopení dat kořenové složky a cesta k souboru

Místní spuštění i U-SQL sady SDK vyžadovat kořenové datové složce. Data kořenové složky je pro účet místního výpočetní "místní úložiště". Je ekvivalentní k účtu Azure Data Lake Store účtu Data Lake Analytics. Přepnutí na jiný data kořenová složka je stejně jako přepnutí na účet jiné úložiště. Pokud chcete pro přístup k běžně sdílený data pomocí různých dat kořenové složky, je nutné použít absolutní cesty ve skriptech. Nebo vytvořte symbolické odkazy systému souborů (například **mklink** na systém souborů NTFS) v kořenové datové složce tak, aby odkazoval sdílená data.

Data kořenové složky se používá pro:

- Ukládání metadat, včetně databází, tabulky, funkce vracející tabulku (Tvf) a sestavení.
- Vyhledání vstupní a výstupní cesty, které jsou definovány jako relativní cesty v U-SQL. Pomocí relativní cesty usnadňuje nasazení vašich projektů U-SQL Azure.

Můžete je relativní cesta a místní cestou absolutní v skriptů U-SQL. Relativní cesta je relativní k cestě zadané kořenové datové složce. Doporučujeme vám, že používáte "/" jako oddělovač cesty upravit skripty kompatibilní se na straně serveru. Zde jsou některé příklady relativní cesty a jejich ekvivalent absolutní cesty. V těchto příkladech je C:\LocalRunDataRoot kořenové datové složce.

|Relativní cesta|Absolutní cesty|
|-------------|-------------|
|/abc/def/input.csv |C:\LocalRunDataRoot\abc\def\input.csv|
|abc/def/input.csv  |C:\LocalRunDataRoot\abc\def\input.csv|
|D:/abc/def/input.csv |D:\abc\def\input.csv|

## <a name="use-local-run-from-visual-studio"></a>Použití místního spuštění ze sady Visual Studio

Nástroje data Lake pro Visual Studio poskytuje možnosti místní spuštění U-SQL v sadě Visual Studio. Pomocí této funkce můžete:

- Spusťte skript U-SQL, který je místně, společně s sestavení C#.
- Ladění sestavení C# místně.
- Vytvořit, zobrazit a odstranění katalogů U-SQL (místních databází, sestavení, schémat a tabulek) z Průzkumníka serveru. Můžete také získat místní katalog také z Průzkumníka serveru.

    ![Nástroje data Lake pro Visual Studio místní spuštění místního katalogu](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-local-catalog.png)

Instalační program nástroje Data Lake vytvoří složku C:\LocalRunRoot má být použit jako výchozí kořenové datové složce. Místní spuštění paralelismus výchozí je 1.

### <a name="to-configure-local-run-in-visual-studio"></a>Konfigurace místního spuštění v sadě Visual Studio

1. Otevřete sadu Visual Studio.
2. Otevřete **Průzkumníka serveru**.
3. Rozbalte položku **Azure** > **Data Lake Analytics**.
4. Klikněte **Data Lake** nabídce a pak klikněte na tlačítko **možnosti a nastavení**.
5. Ve stromu vlevo rozbalte **Azure Data Lake**a potom rozbalte **Obecné**.

    ![Konfigurace nástrojů data Lake pro Visual Studio spustit místní nastavení](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-configure.png)

Projekt Visual Studio U-SQL je vyžadována pro provádění místní spuštění. Tato část se liší od spuštění skriptů U-SQL z Azure.

### <a name="to-run-a-u-sql-script-locally"></a>Místně spustíte skript U-SQL
1. Ze sady Visual Studio otevřete projekt U-SQL.   
2. Skript U-SQL v Průzkumníku řešení klikněte pravým tlačítkem myši a pak klikněte na **odeslat skript**.
3. Vyberte **(místní)** jako účet Analytics, který chcete spustit skript místně.
Můžete také kliknutím **(místní)** účet horní okně Skript a potom klikněte na **odeslání** (nebo použít kombinaci kláves Ctrl + F5 klávesové zkratky).

    ![Nástroje data Lake pro Visual Studio spustit místní odeslání úlohy](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-submit-job.png)

### <a name="debug-scripts-and-c-assemblies-locally"></a>Místní ladění skriptů a sestavení C#

Sestavení C# můžete ladit bez odeslali a zaregistrovali k službě Azure Data Lake Analytics. V souboru kódu i v odkazovaném projektu C# můžete nastavit zarážky.

#### <a name="to-debug-local-code-in-code-behind-file"></a>Postup ladění místního kódu v souboru kódu

1. Nastavte zarážky v souboru kódu.
2. Stisknutím klávesy F5 místně laďte skript.

> [!NOTE]
   > Následující postup funguje pouze v sadě Visual Studio 2015. Ve starší sadě Visual Studio je pravděpodobně nutné ručně přidat soubory PDB.  
   >
   >

#### <a name="to-debug-local-code-in-a-referenced-c-project"></a>Postup ladění místního kódu v odkazovaném projektu C#

1. Vytvořte projekt sestavení C# a sestavte jej tak, aby generoval výstupní knihovnu DLL.
2. Zaregistruje knihovnu DLL pomocí příkazu U-SQL:

        CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
        
3. Nastavte zarážky v kódu C#.
4. Stisknutím klávesy F5 a laďte skript s odkazujícím C# knihovny dll místně.

## <a name="use-local-run-from-the-data-lake-u-sql-sdk"></a>Použití místní spuštění ze sady SDK pro Data Lake U-SQL

Kromě spuštění skriptů U-SQL místně pomocí sady Visual Studio, můžete použít sadu SDK Azure Data Lake U-SQL ke spouštění skriptů U-SQL místně pomocí příkazového řádku a programovací rozhraní. Pomocí těchto je možné škálovat svůj místní test U-SQL.

Další informace o [SDK Azure Data Lake U-SQL](data-lake-analytics-u-sql-sdk.md).


## <a name="next-steps"></a>Další postup

* Pokud chcete zobrazit komplexnější dotaz, najdete v části [analýza webových protokolů pomocí Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Chcete-li zobrazit podrobnosti o úlohách, najdete v části [použití úlohy prohlížeče a zobrazení úloh pro úlohy Azure Data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md).
* Chcete-li použít zobrazení provádění vrcholů, přečtěte si téma [použít zobrazení provádění vrcholů v nástrojů Data Lake pro Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
