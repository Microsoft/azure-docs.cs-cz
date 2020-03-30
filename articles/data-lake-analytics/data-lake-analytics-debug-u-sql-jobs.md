---
title: Ladění kódu C# pro úlohy Azure Data Lake U-SQL
description: Tento článek popisuje, jak ladit vrchol u-SQL se nezdařilo pomocí Nástroje Azure Data Lake tools pro Visual Studio.
services: data-lake-analytics
ms.service: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: bcd0b01e-1755-4112-8e8a-a5cabdca4df2
ms.topic: conceptual
ms.date: 11/30/2017
ms.openlocfilehash: 72239fc1679d2ebbfd9c9b5be6b79b58efb760cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71315812"
---
# <a name="debug-user-defined-c-code-for-failed-u-sql-jobs"></a>Ladění uživatelem definovaného kódu Jazyka C pro neúspěšné úlohy U-SQL

U-SQL poskytuje model rozšiřitelnosti pomocí jazyka C#. Ve skriptech U-SQL je snadné volat funkce Jazyka C# a provádět analytické funkce, které deklarativní jazyk podobný SQL nepodporuje. Další informace o rozšiřitelnosti u-SQL naleznete [v průvodci programovatelností u-SQL](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-u-sql-programmability-guide#use-user-defined-functions-udf). 

V praxi může být nutné ladění jakéhokoli kódu, ale je těžké ladit distribuovanou úlohu s vlastním kódem v cloudu s omezenými soubory protokolu. [Nástroje datového jezera Azure pro Visual Studio](https://aka.ms/adltoolsvs) poskytuje funkci s názvem Selhání **vertex ladění**, který vám pomůže snadněji ladit chyby, ke kterým dochází ve vašem vlastním kódu. Když se nezdaří úloha U-SQL, služba zachová stav selhání a nástroj vám pomůže stáhnout prostředí selhání cloudu do místního počítače pro ladění. Místní stahování zachycuje celé cloudové prostředí, včetně všech vstupních dat a uživatelského kódu.

Následující video ukazuje neúspěšné ladění vrcholu v Nástrojích datového jezera Azure pro Visual Studio.

> [!VIDEO https://www.youtube.com/embed/3enkNvprfm4]
>

> [!IMPORTANT]
> Visual Studio vyžaduje následující dvě aktualizace pro použití této funkce: [Microsoft Visual C++ 2015 Redistributable Update 3](https://www.microsoft.com/en-us/download/details.aspx?id=53840) a Universal C [Runtime pro Windows](https://www.microsoft.com/download/details.aspx?id=50410).
>

## <a name="download-failed-vertex-to-local-machine"></a>Stažení neúspěšného vrcholu do místního počítače

Když otevřete neúspěšnou úlohu v Azure Data Lake Tools pro Visual Studio, zobrazí se žlutý panel s podrobnými chybovými zprávami na kartě chyby.

1. Kliknutím na **Stáhnout** stáhněte všechny potřebné prostředky a vstupní datové proudy. Pokud se stahování nedokončí, klepněte na tlačítko **Opakovat**.

2. Chcete-li vygenerovat místní prostředí ladění, klepněte na **tlačítko Otevřít** po dokončení stahování. Otevře se nové ladicí řešení a pokud máte v sadě Visual Studio otevřené existující řešení, před laděním jej nezapomeňte uložit a zavřít.

![Azure Data Lake Analytics U-SQL ladění visual studio stáhnout vrchol](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

## <a name="configure-the-debugging-environment"></a>Konfigurace prostředí ladění

> [!NOTE]
> Před laděním nezapomeňte zkontrolovat **výjimky běžného za běhu času** v okně Nastavení výjimky (**Ctrl + Alt + E**).

![Nastavení vizuálního studia u-SQL ladění Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

V nově spuštěné instanci sady Visual Studio můžete nebo nemusíte najít uživatelem definovaný zdrojový kód jazyka C#:

1. [V řešení najdu zdrojový kód](#source-code-is-included-in-debugging-solution)

2. [Nelze najít zdrojový kód v řešení](#source-code-is-not-included-in-debugging-solution)

### <a name="source-code-is-included-in-debugging-solution"></a>Zdrojový kód je součástí řešení ladění

Existují dva případy, kdy je zachycen zdrojový kód jazyka C#:

1. Uživatelský kód je definován v souboru s `Script.usql.cs` kódem na pozadí (obvykle pojmenovaný v projektu U-SQL).

2. Uživatelský kód je definován v projektu knihovny tříd jazyka C# pro aplikaci U-SQL a registrován jako sestavení s **ladicími informacemi**.

Pokud je zdrojový kód importován do řešení, můžete k řešení problému použít nástroje pro ladění sady Visual Studio (hodinky, proměnné atd.):

1. Stisknutím klávesy **F5** spusťte ladění. Kód se spustí, dokud není zastaven výjimkou.

2. Otevřete soubor zdrojového kódu a nastavte zarážky a stisknutím **klávesy F5** kód laděte krok za krokem.

    ![Výjimka pro ladění U-SQL služby Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-exception.png)

### <a name="source-code-is-not-included-in-debugging-solution"></a>Zdrojový kód není součástí řešení ladění.

Pokud uživatelský kód není součástí souboru s kódem na pozadí nebo jste nezaregistrovali sestavení s **informacemi o ladění**, není zdrojový kód automaticky zahrnut do řešení ladění. V takovém případě potřebujete další kroky k přidání zdrojového kódu:

1. Klepněte pravým tlačítkem myši na **položku Řešení "VertexDebug" > Přidat > existujícího projektu...** a vyhledejte zdrojový kód sestavení a přidejte projekt do ladicího řešení.

    ![Projekt přidání ladění U-SQL služby Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-add-project-to-debug-solution.png)

2. Získejte cestu ke složce projektu **FailedVertexDebugHost** projektu. 

3. Klepněte pravým tlačítkem myši na **přidaný projekt zdrojového kódu sestavení > vlastnosti**, vyberte kartu **Sestavení** vlevo a vložte zkopírovanou cestu končící \bin\debug jako **výstupní > výstupní cestu**. Konečná výstupní cesta `<DataLakeTemp path>\fd91dd21-776e-4729-a78b-81ad85a4fba6\loiu0t1y.mfo\FailedVertexDebug\FailedVertexDebugHost\bin\Debug\`je jako .

    ![Cesta pdb sady pdb služby Azure Data Lake Analytics U-SQL](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-set-pdb-path.png)

Po těchto nastaveních spusťte ladění pomocí **F5** a zarážek. K řešení problému můžete také použít nástroje pro ladění sady Visual Studio (sledování, proměnné atd.).

> [!NOTE]
> Znovu sestavte projekt zdrojového kódu sestavení pokaždé, když změníte kód tak, aby generoval aktualizované soubory PDB.

## <a name="resubmit-the-job"></a>Znovu odeslat úlohu

Po ladění, pokud projekt úspěšně dokončí výstupní okno zobrazí následující zprávu:

    The Program 'LocalVertexHost.exe' has exited with code 0 (0x0).

![Úspěšné ladění U-SQL služby Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-succeed.png)

Opětovné odeslání neúspěšné úlohy:

1. Pro úlohy s řešením s kódem na pozadí zkopírujte kód `Script.usql.cs`Jazyka C# do zdrojového souboru s kódem na pozadí (obvykle).

2. U úloh se sestaveními klikněte pravým tlačítkem myši na projekt zdrojového kódu sestavení v řešení ladění a zaregistrujte aktualizovaná sestavení DLL do katalogu Azure Data Lake.

3. Znovu odešlete úlohu U-SQL.

## <a name="next-steps"></a>Další kroky

- [Průvodce programovatelností U-SQL](data-lake-analytics-u-sql-programmability-guide.md)
- [Vývoj uživatelských operátorů U-SQL pro úlohy Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-user-defined-operators.md)
- [Testování a ladění úloh U-SQL pomocí místního spuštění a sady Azure Data Lake U-SQL SDK](data-lake-analytics-data-lake-tools-local-run.md)
- [Řešení potíží s neobvyklou opakovanou úlohou](data-lake-analytics-data-lake-tools-debug-recurring-job.md)
