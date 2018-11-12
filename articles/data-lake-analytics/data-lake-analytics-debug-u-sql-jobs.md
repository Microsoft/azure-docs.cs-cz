---
title: Ladění uživatelského kódu C# pro neúspěšné úlohy Azure Data Lake U-SQL
description: Tento článek popisuje, jak se ladění chybných vrcholů U-SQL pomocí nástrojů Azure Data Lake pro Visual Studio.
services: data-lake-analytics
ms.service: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: bcd0b01e-1755-4112-8e8a-a5cabdca4df2
ms.topic: conceptual
ms.date: 11/30/2017
ms.openlocfilehash: 11587d5a0520d42d554c13a525c3b57db82326aa
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51229471"
---
# <a name="debug-user-defined-c-code-for-failed-u-sql-jobs"></a>Ladění uživatelského kódu C# pro selhání úloh U-SQL

U-SQL poskytuje model rozšiřitelnosti pomocí jazyka C#. V skriptů U-SQL je jednoduché volání funkcí jazyka C# a provádějí analytické funkce, které deklarativní jazyce podobném SQL nepodporuje. Další informace pro rozšíření U-SQL najdete v tématu [Průvodce programovatelností U-SQL](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-u-sql-programmability-guide#use-user-defined-functions-udf). 

V praxi může být nutné jakéhokoli kódu, ladění, ale je obtížné ladit distribuované úlohy pomocí vlastního kódu v cloudu s omezenou protokolu souborů. [Azure Data Lake Tools for Visual Studio](https://aka.ms/adltoolsvs) poskytuje funkci s názvem **ladění vrcholu se nepovedlo**, která pomáhá můžete snadněji ladit chyby, ke kterým dochází ve vlastním kódu. Selhání úlohy U-SQL služby udržuje stavu selhání a nástroje vám umožní stáhnout selhání prostředí cloudu do místního počítače pro ladění. Místní stažení zachycuje celý cloudovém prostředí, včetně všech vstupních dat a uživatelského kódu.

Toto video ukazuje nepovedlo vrcholu ladění v Azure Data Lake Tools pro Visual Studio.

> [!VIDEO https://www.youtube.com/embed/3enkNvprfm4]
>

> [!IMPORTANT]
> Visual Studio vyžaduje následující dvě aktualizace pro použití této funkce: [Microsoft Visual C++ 2015 Redistributable Update 3](https://www.microsoft.com/en-us/download/details.aspx?id=53840) a [Universal C Runtime pro Windows](https://www.microsoft.com/download/details.aspx?id=50410).
>

## <a name="download-failed-vertex-to-local-machine"></a>Nepovedlo se stáhnout vrchol do místního počítače

Při otevření neúspěšné úlohy v Azure Data Lake Tools pro Visual Studio se zobrazí žlutý pruh oznámení s podrobné chybové zprávy na kartě Chyba.

1. Klikněte na tlačítko **Stáhnout** stáhnout všechny požadované prostředky a vstupní datové proudy. Pokud se stahování nedokončí, klikněte na tlačítko **opakujte**.

2. Klikněte na tlačítko **otevřít** po dokončení stahování pro generování místní prostředí ladění. Otevře se nové řešení ladění, a pokud máte existující řešení otevřít v sadě Visual Studio, prosím ujistěte, že uložte a zavřete ho před laděním.

![Azure Data Lake Analytics U-SQL ladění sady visual studio ke stažení vrcholu](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

## <a name="configure-the-debugging-environment"></a>Konfigurace prostředí ladění

> [!NOTE]
> Před laděním, nezapomeňte se podívat **výjimky modulu Common Language Runtime** v okně Nastavení výjimek (**Ctrl + Alt + E**).

![Azure Data Lake Analytics U-SQL ladění sady visual studio nastavení](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

V nové spuštěnou instanci sady Visual Studio může nebo nemůže najít uživatelem definované zdrojový kód jazyka C#:

1. [Můj zdrojový kód můžete najít v řešení](#source-code-is-included-in-debugging-solution)

2. [Nemůžu najít Můj zdrojový kód v řešení](#source-code-is-not-included-in-debugging-solution)

### <a name="source-code-is-included-in-debugging-solution"></a>Zdrojový kód je součástí řešení ladění

Existují dva možné případy, že jsou zachyceny zdrojový kód C#:

1. Uživatelský kód je definována v souboru kódu na pozadí (obvykle s názvem `Script.usql.cs` v projektu U-SQL).

2. Uživatelský kód je definovaný v C# projekt knihovny tříd aplikace v U-SQL a zaregistrovat jako sestavení s **ladicí informace modulu**.

Pokud zdrojový kód, je naimportován do řešení, můžete použít ladicí nástroje Visual Studio (sledování, proměnné atd.) k vyřešení tohoto problému:

1. Stisknutím klávesy **F5** spusťte ladění. Kód se spustí, dokud nebude zastaven výjimky.

2. Otevření souboru se zdrojovým kódem a nastavit zarážky, stiskněte klávesu **F5** na ladění kódu krok za krokem.

    ![Azure Data Lake Analytics U-SQL ladění výjimek](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-exception.png)

### <a name="source-code-is-not-included-in-debugging-solution"></a>Zdrojový kód není součástí řešení ladění

Pokud uživatelský kód není zahrnutý v souboru kódu na pozadí nebo nezaregistrovala sestavení s **ladicí informace modulu**, pak zdrojový kód není automaticky součástí řešení ladění. V takovém případě budete potřebovat další kroky k přidání zdrojového kódu:

1. Klikněte pravým tlačítkem na **řešení "VertexDebug" > Přidat > existující projekt...**  najít zdrojový kód sestavení a přidat projekt do řešení ladění.

    ![Azure Data Lake Analytics U-SQL ladění přidat projekt](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-add-project-to-debug-solution.png)

2. Získání cesty ke složce projektu pro **FailedVertexDebugHost** projektu. 

3. Klikněte pravým tlačítkem na **projektu přidání sestavení zdrojového kódu > vlastnosti**, vyberte **sestavení** kartu na levé straně a vložte zkopírovaný cestu končí \bin\debug jako **výstup > Výstupní cesta**. Konečné výstupní cesta je jako "<DataLakeTemp path>\fd91dd21-776e-4729-a78b-81ad85a4fba6\loiu0t1y.mfo\FailedVertexDebug\FailedVertexDebugHost\bin\Debug\".

    ![Azure Data Lake Analytics U-SQL ladění nastavit cestu k souboru pdb](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-set-pdb-path.png)

Po těchto nastavení spuštění ladění pomocí **F5** a zarážky. Můžete také použít Visual Studio ladicí nástroje (sledování, proměnné atd.) k vyřešení tohoto problému.

> [!NOTE]
> Znovu sestavte projekt sestavení zdrojového kódu pokaždé, když po úpravě kódu Generovat soubory .pdb aktualizované.

## <a name="resubmit-the-job"></a>Znovu spustit úlohu

Po ladění, pokud projekt úspěšně dokončí v okně výstupu zobrazí následující zpráva:

    The Program 'LocalVertexHost.exe' has exited with code 0 (0x0).

![Azure Data Lake Analytics U-SQL ladění úspěšné](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-succeed.png)

Chcete-li znovu spustit úlohu, která selhala:

1. Pro úlohy s řešeními použití modelu code-behind, zkopírujte kód jazyka C# do souboru zdrojového kódu na pozadí (obvykle `Script.usql.cs`).

2. Pro úlohy se sestaveními klikněte pravým tlačítkem na projekt sestavení zdrojového kódu v řešení ladění a zaregistrovat sestavení aktualizované .dll do katalogu Azure Data Lake.

3. Znovu odešlete úlohu U-SQL.

## <a name="next-steps"></a>Další postup

- [Průvodce programovatelností U-SQL](data-lake-analytics-u-sql-programmability-guide.md)
- [Vývoj operátory definované uživatelem U-SQL pro úlohy Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-user-defined-operators.md)
- [Testování a ladění úloh U-SQL pomocí místního spuštění a sady Azure Data Lake U-SQL SDK](data-lake-analytics-data-lake-tools-local-run.md)
- [Řešení potíží s neobvykle se opakující úlohou](data-lake-analytics-data-lake-tools-debug-recurring-job.md)
