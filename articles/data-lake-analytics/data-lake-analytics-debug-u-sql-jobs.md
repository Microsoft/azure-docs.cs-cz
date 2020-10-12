---
title: Ladění kódu C# pro úlohy Azure Data Lake U-SQL
description: Tento článek popisuje, jak pomocí Nástroje Azure Data Lake pro Visual Studio ladit vrchol neúspěšného ladění U-SQL.
services: data-lake-analytics
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.assetid: bcd0b01e-1755-4112-8e8a-a5cabdca4df2
ms.topic: how-to
ms.date: 11/30/2017
ms.openlocfilehash: ca6fe4ad35e59472e8cf8f3b8476417e01c2668f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87131867"
---
# <a name="debug-user-defined-c-code-for-failed-u-sql-jobs"></a>Ladit uživatelsky definovaný kód C# pro nezdařené úlohy U-SQL

U-SQL poskytuje rozšiřitelný model pomocí C#. Ve skriptech U-SQL je snadné volat funkce jazyka C# a provádět analytické funkce, které jazyk SQL podobně jako deklarativní jazyk nepodporuje. Další informace o rozšiřitelnosti U-SQL najdete v tématu [Příručka k programovatelnosti u-SQL](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-u-sql-programmability-guide#use-user-defined-functions-udf). 

V praxi může nějaký kód vyžadovat ladění, ale je těžké ladit distribuovanou úlohu s vlastním kódem v cloudu s použitím omezených souborů protokolu. [Nástroje Azure Data Lake pro Visual Studio](https://aka.ms/adltoolsvs) poskytuje funkci s názvem **ladění neúspěšných vrcholů**, která vám pomůže snadněji ladit selhání, ke kterým dochází ve vašem vlastním kódu. Pokud úloha U-SQL selže, služba udržuje stav selhání a nástroj vám pomůže stáhnout prostředí selhání cloudu do místního počítače pro ladění. Místní stahování zachytí celé cloudové prostředí včetně všech vstupních dat a uživatelského kódu.

Následující video ukazuje neúspěšný ladění vrcholu v Nástroje Azure Data Lake pro Visual Studio.

> [!VIDEO https://www.youtube.com/embed/3enkNvprfm4]
>

> [!IMPORTANT]
> Pro použití této funkce vyžaduje Visual Studio následující dvě aktualizace: [Microsoft Visual C++ 2015 Distribuovatelný Update 3](https://www.microsoft.com/en-us/download/details.aspx?id=53840) a [Universal C Runtime pro Windows](https://www.microsoft.com/download/details.aspx?id=50410).
>

## <a name="download-failed-vertex-to-local-machine"></a>Nepovedlo se stáhnout vrchol do místního počítače.

Když v Nástroje Azure Data Lake pro Visual Studio otevřete úlohu, která selhala, zobrazí se žlutý výstražný pruh s podrobnými chybovými zprávami na kartě chyba.

1. Kliknutím na **Stáhnout** stáhněte všechny požadované prostředky a vstupní streamy. Pokud stahování nedokončíte, klikněte na tlačítko **Opakovat**.

2. Po dokončení stahování klikněte na **otevřít** a vygenerujte místní ladicí prostředí. Otevře se nové řešení pro ladění. Pokud už máte otevřené řešení v aplikaci Visual Studio, nezapomeňte ho před laděním Uložit a zavřít.

![Azure Data Lake Analytics ladění U-SQL – vrchol stažení sady Visual Studio](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

## <a name="configure-the-debugging-environment"></a>Konfigurace ladicího prostředí

> [!NOTE]
> Před laděním nezapomeňte zkontrolovat **výjimky modulu CLR (Common Language Runtime** ) v okně Nastavení výjimek (**CTRL + ALT + E**).

![Azure Data Lake Analytics nastavení ladění U-SQL pro Visual Studio](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

V nově spuštěné instanci sady Visual Studio můžete nebo nemůžete najít zdrojový kód C# definovaný uživatelem:

1. [Můžu najít zdrojový kód v řešení](#source-code-is-included-in-debugging-solution)

2. [V řešení nejde najít zdrojový kód](#source-code-is-not-included-in-debugging-solution)

### <a name="source-code-is-included-in-debugging-solution"></a>Zdrojový kód je zahrnutý v řešení ladění.

Existují dva případy, kdy je zaznamenán zdrojový kód C#:

1. Uživatelský kód je definovaný v souboru kódu na pozadí (obvykle se jmenuje `Script.usql.cs` v projektu U-SQL).

2. Uživatelský kód je definován v projektu knihovny tříd jazyka C# pro aplikaci U-SQL a registrovaný jako sestavení s **informacemi o ladění**.

Pokud je zdrojový kód importován do řešení, můžete k řešení problému použít ladicí nástroje sady Visual Studio (kukátko, proměnné atd.):

1. Stisknutím klávesy **F5** spusťte ladění. Kód se spustí, dokud jej nezastaví výjimka.

2. Otevřete soubor zdrojového kódu a nastavte zarážky a potom stisknutím klávesy **F5** ladit kód krok za krokem.

    ![Výjimka ladění U-SQL Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-exception.png)

### <a name="source-code-is-not-included-in-debugging-solution"></a>Zdrojový kód není zahrnutý v řešení ladění.

Pokud kód uživatele není zahrnutý v souboru kódu na pozadí nebo jste nezaregistrovali sestavení s **informacemi o ladění**, není zdrojový kód automaticky zahrnut do řešení ladění. V takovém případě budete potřebovat dodatečné kroky pro přidání zdrojového kódu:

1. Klikněte pravým tlačítkem na **řešení ' VertexDebug ' > přidat > existující projekt...** pro vyhledání zdrojového kódu sestavení a přidání projektu do řešení ladění.

    ![Přidat projekt pro ladění U-SQL Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-add-project-to-debug-solution.png)

2. Získejte cestu ke složce projektu pro projekt **FailedVertexDebugHost** . 

3. Right-Click **přidaných projektů zdrojového kódu sestavení > vlastnosti**vyberte kartu **sestavení** vlevo a vložte zkopírovanou cestu končící na \bin\debug jako výstupní **cestu > výstupní cesta**. Poslední výstupní cesta je jako `<DataLakeTemp path>\fd91dd21-776e-4729-a78b-81ad85a4fba6\loiu0t1y.mfo\FailedVertexDebug\FailedVertexDebugHost\bin\Debug\` .

    ![Azure Data Lake Analytics nastavení ladění U-SQL nastavte cestu PDB](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-set-pdb-path.png)

Po těchto nastaveních spusťte ladění pomocí **F5** a zarážek. K řešení problému můžete také použít ladicí nástroje sady Visual Studio (kukátko, proměnné atd.).

> [!NOTE]
> Sestavte projekt zdrojového kódu sestavení pokaždé, když upravíte kód pro generování aktualizovaných souborů. pdb.

## <a name="resubmit-the-job"></a>Odešlete úlohu znovu.

Po ladění, pokud se projekt úspěšně dokončí, zobrazí se v okně výstup následující zpráva:

`The Program 'LocalVertexHost.exe' has exited with code 0 (0x0).`

![Úspěšné ladění U-SQL Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-succeed.png)

Opětovné odeslání neúspěšné úlohy:

1. Pro úlohy s řešeními s kódem na pozadí zkopírujte kód jazyka C# do zdrojového souboru kódu na pozadí (obvykle `Script.usql.cs` ).

2. Pro úlohy se sestavením klikněte pravým tlačítkem na projekt zdrojového kódu sestavení v řešení ladění a zaregistrujte aktualizované sestavení. dll do katalogu Azure Data Lake.

3. Znovu odešlete úlohu U-SQL.

## <a name="next-steps"></a>Další kroky

- [Průvodce programovatelností U-SQL](data-lake-analytics-u-sql-programmability-guide.md)
- [Vývoj uživatelem definovaných operátorů U-SQL pro úlohy Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-user-defined-operators.md)
- [Testování a ladění úloh U-SQL pomocí místního spuštění a sady Azure Data Lake U-SQL SDK](data-lake-analytics-data-lake-tools-local-run.md)
- [Řešení potíží s neobvyklou opakovanou úlohou](data-lake-analytics-data-lake-tools-debug-recurring-job.md)
