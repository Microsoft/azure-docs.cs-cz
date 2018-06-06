---
title: Ladění uživatelem definované C# – kód pro selhání úloh Azure Data Lake U-SQL
description: Tento článek popisuje, jak k ladění selhání vrchol U-SQL pomocí nástrojů Azure Data Lake pro Visual Studio.
services: data-lake-analytics
ms.service: data-lake-analytics
author: yanancai
ms.author: yanacai
manager: kfile
editor: jasonwhowell
ms.assetid: bcd0b01e-1755-4112-8e8a-a5cabdca4df2
ms.topic: conceptual
ms.date: 11/30/2017
ms.openlocfilehash: d8926f0defdfcc0e045ae87de6fd82755a5a35eb
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34736843"
---
# <a name="debug-user-defined-c-code-for-failed-u-sql-jobs"></a>Ladění uživatelem definované C# – kód pro selhání úloh U-SQL

U-SQL poskytuje model rozšiřitelnosti pomocí jazyka C#. V skriptů U-SQL je snadné volání funkcí jazyka C# a provádějí analytické funkce, které jazyka deklarativní SQL nepodporuje. Další informace pro rozšiřitelnost U-SQL najdete v tématu [U-SQL programovatelnosti průvodce](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-u-sql-programmability-guide#use-user-defined-functions-udf). 

V praxi kód, může být nutné ladění, ale je obtížné ladění distribuované úlohy pomocí vlastního kódu v cloudu se soubory protokolu omezené. [Nástroje Azure Data Lake pro Visual Studio](http://aka.ms/adltoolsvs) poskytuje funkci **se nezdařilo ladění vrchol**, což pomáhá snadněji ladění chyb, ke kterým dochází ve vaší vlastní kód. Pokud se nezdaří úlohy U-SQL, služba udržuje stav selhání a nástroj umožňuje stáhnout prostředí selhání cloudu do místního počítače, pro ladění. Místní stahování zaznamená celý cloudové prostředí, včetně všech vstupních dat a uživatelského kódu.

Toto video ukazuje se nezdařilo vrchol ladění ve nástrojů Azure Data Lake pro Visual Studio.

> [!VIDEO https://www.youtube.com/embed/3enkNvprfm4]
>

> [!IMPORTANT]
> Visual Studio vyžaduje následující dvě aktualizace pro použití této funkce: [Microsoft Visual C++ 2015 Redistributable Update 3](https://www.microsoft.com/en-us/download/details.aspx?id=53840) a [Universal C Runtime pro systém Windows](https://www.microsoft.com/download/details.aspx?id=50410).
>

## <a name="download-failed-vertex-to-local-machine"></a>Vrchol stažení se nezdařilo místním počítači

Při otevření neúspěšnou úlohu v nástrojů Azure Data Lake pro Visual Studio zobrazí žlutý výstrahy pruh s podrobné chybové zprávy v kartě chyby.

1. Klikněte na tlačítko **Stáhnout** a stahovat požadované prostředky a vstupní datové proudy. Pokud stahování nedokončí, klikněte na tlačítko **opakujte**.

2. Klikněte na tlačítko **otevřete** po dokončení stahování pro generování prostředí místní ladění. Otevře se nové ladění řešení, a pokud máte existující řešení otevřít v sadě Visual Studio, prosím Ujistěte se, že uložte a zavřete před ladění.

![Azure Data Lake Analytics U-SQL vrchol stažení sady visual studio pro ladění](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

## <a name="configure-the-debugging-environment"></a>Konfigurace ladění prostředí

> [!NOTE]
> Před ladění, nezapomeňte zkontrolovat **výjimky modulu CLR** v okně Nastavení výjimky (**Ctrl + Alt + E**).

![Azure Data Lake Analytics U-SQL ladění sady visual studio nastavení](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

V nové instanci spuštěného Visual Studio může nebo nemusí najít uživatelem definované zdrojový kód C#:

1. [I my zdrojový kód najdete v řešení](#source-code-is-included-in-debugging-solution)

2. [Nelze najít zdrojový kód v řešení](#source-code-is-not-included-in-debugging-solution)

### <a name="source-code-is-included-in-debugging-solution"></a>Zdrojový kód je součástí ladění řešení

Existují dva případy, že zachycenou zdrojového kódu C#:

1. Kód uživatele je definována v souboru kódu na pozadí (obvykle s názvem `Script.usql.cs` v projektu U-SQL).

2. Uživatelský kód je definovaný v C# projektu knihovny tříd součásti pro aplikaci U-SQL a registrován jako sestavení s **ladicí informace**.

Pokud zdrojový kód je importovat do řešení, můžete použít nástroje sady Visual Studio ladění (sledovat, proměnné atd.) k vyřešení tohoto problému:

1. Stiskněte klávesu **F5** spustit ladění. Kód běží, dokud nebude zastaven výjimkou.

2. Otevření souboru se zdrojovým kódem a nastavit zarážky, stiskněte **F5** k ladění kódu krok za krokem.

    ![Azure Data Lake Analytics U-SQL ladění výjimek](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-exception.png)

### <a name="source-code-is-not-included-in-debugging-solution"></a>Zdrojový kód není součástí ladění řešení

Pokud uživatelský kód není zahrnutá v souboru kódu na pozadí nebo neregistrovala sestavení s **ladicí informace**, zdrojový kód není automaticky zahrnuty v ladění řešení. V takovém případě je třeba další kroky k přidání zdrojového kódu:

1. Klikněte pravým tlačítkem na **řešení 'VertexDebug' > Přidat > existující projekt...**  k vyhledání sestavení zdrojového kódu a přidejte projekt k ladění řešení.

    ![Přidání projektu Azure Data Lake Analytics U-SQL ladění](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-add-project-to-debug-solution.png)

2. Cesta ke složce projektu pro získání **FailedVertexDebugHost** projektu. 

3. Klikněte pravým tlačítkem na **projektu přidané sestavení zdrojového kódu > vlastnosti**, vyberte **sestavení** kartě na levé straně a vložte zkopírovaný cesta konče \bin\debug jako **výstup > Výstupní cesta**. Konečné výstupní cesta je jako "<DataLakeTemp path>\fd91dd21-776e-4729-a78b-81ad85a4fba6\loiu0t1y.mfo\FailedVertexDebug\FailedVertexDebugHost\bin\Debug\".

    ![Azure Data Lake Analytics U-SQL ladění, nastavte cestu pdb](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-set-pdb-path.png)

Po tato nastavení, spusťte ladění pomocí **F5** a zarážky. Můžete také použít Visual Studio ladicí nástroje (sledovat, proměnné atd.) k vyřešení tohoto problému.

> [!NOTE]
> Znovu sestavte projekt sestavení zdrojového kódu pokaždé, když po úpravě kód pro vygenerování souborů aktualizované pdb.

## <a name="resubmit-the-job"></a>Odešlete znovu úlohu

Po ladění, po úspěšném dokončení projektu ve výstupním okně zobrazí následující zprávu:

    The Program 'LocalVertexHost.exe' has exited with code 0 (0x0).

![Azure Data Lake Analytics U-SQL ladění úspěšné](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-succeed.png)

Odeslat znovu úlohu došlo k chybě:

1. Pro úlohy s kódem v pozadí řešení, zkopírujte kód C# do zdrojového souboru kódu na pozadí (obvykle `Script.usql.cs`).

2. Pro úlohy s sestavení klikněte pravým tlačítkem na projekt sestavení zdrojového kódu v ladění řešení a zaregistrujte aktualizované .dll sestavení do katalogu Azure Data Lake.

3. Odešlete znovu úlohu U-SQL.

## <a name="next-steps"></a>Další postup

- [Průvodce programovatelnosti U-SQL](data-lake-analytics-u-sql-programmability-guide.md)
- [Vývoj U-SQL uživatelem definované operátory pro úlohy Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-user-defined-operators.md)
- [Testování a ladění úloh U-SQL pomocí místního spuštění a sady Azure Data Lake U-SQL SDK](data-lake-analytics-data-lake-tools-local-run.md)
- [Řešení potíží s neobvyklé opakování úlohy](data-lake-analytics-data-lake-tools-debug-recurring-job.md)
