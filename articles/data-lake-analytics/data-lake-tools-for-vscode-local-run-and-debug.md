---
title: Spuštění U-SQL a ladění místně v nástrojů Azure Data Lake pro Visual Studio Code
description: Další informace o použití nástroje Azure Data Lake pro Visual Studio Code ke spouštění a ladění úloh U-SQL místně.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jejiang
ms.author: jejiang
manager: kfile
editor: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.topic: conceptual
ms.date: 07/14/2017
ms.openlocfilehash: 850b13becb2137c9e881b2d6a657bbd06216e96e
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2018
ms.locfileid: "36317142"
---
# <a name="run-u-sql-and-debug-locally-in-visual-studio-code"></a>Spuštění U-SQL a ladění místně v kódu Visual Studio
Tento článek popisuje, jak spustit úlohy U-SQL na místním vývojovém počítači pro urychlení časná kódování fáze nebo pokud chcete ladit kód místně v kódu Visual Studio. Pokyny nástroj Azure Data Lake pro Visual Studio Code, najdete v části [nástrojů pomocí Azure Data Lake pro Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md). 

## <a name="set-up-the-u-sql-local-run-environment"></a>Nastavení prostředí pro místní spuštění U-SQL

1. Vyberte Ctrl + Shift + P otevřete paletu příkaz, a potom zadejte **ADL: Stáhněte si balíček místní spuštění** stáhnout balíčky.  

   ![Stáhnout balíčky ADL LocalRun závislostí](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/downloadtheadllocalrunpackage.png)

2. Vyhledejte závislosti balíčků z cesty uvedené v **výstup** podokně a pak nainstalujte BuildTools a Win10SDK 10240. Tady je příklad cesty:  
`C:\Users\xxx\AppData\Roaming\LocalRunDependency` 

   ![Vyhledejte závislosti balíčků](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/LocateDependencyPath.png)

   2.1 k instalaci **BuildTools**, klikněte na tlačítko visualcppbuildtools_full.exe ve složce LocalRunDependency a pak postupujte podle pokynů průvodce.   

    ![Nainstalujte BuildTools](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallBuildTools.png)

   2.2 k instalaci **Win10SDK 10240**, klikněte na tlačítko sdksetup.exe ve složce LocalRunDependency/Win10SDK_10.0.10240_2 a pak postupujte podle pokynů průvodce.  

    ![Nainstalujte Win10SDK 10240](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallWin10SDK.png)

3. Nastavte proměnnou prostředí. Nastavte **SCOPE_CPP_SDK** proměnnou prostředí:  
`C:\Users\XXX\AppData\Roaming\LocalRunDependency\CppSDK_3rdparty`  


## <a name="start-the-local-run-service-and-submit-the-u-sql-job-to-a-local-account"></a>Spusťte službu místní spuštění a odeslání úlohy U-SQL pro místní účet 
U prvního uživatele, použijte **ADL: Stáhněte si balíček místní spuštění** stáhnout místní spuštění balíčky, pokud máte není [nastavení prostředí pro místní spuštění U-SQL](#set-up-the-u-sql-local-run-environment).

1. Vyberte Ctrl + Shift + P otevřete paletu příkaz, a potom zadejte **ADL: spustit místní spustit službu**.   
2. Vyberte **přijmout** poprvé přijmout licenční podmínky pro Software společnosti Microsoft. 

   ![Přijmout licenční podmínky pro Software společnosti Microsoft](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/AcceptEULA.png)   
3. Otevře se konzola cmd. Pro uživatele, musíte zadat **3**a potom vyhledejte cestu místní složky pro vaše data vstup a výstup. Pro další možnosti můžete použít výchozí hodnoty. 

   ![Nástroje data Lake pro Visual Studio Code místní spuštění cmd](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-cmd.png)
4. Vyberte Ctrl + Shift + P otevřít palety příkazu, zadejte **ADL: odeslat úlohu**a potom vyberte **místní** se odeslat úlohu místní účet.

   ![Vyberte místní nástrojů data Lake pro Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-select-local.png)
5. Po odeslání úlohy můžete zobrazit podrobnosti o odeslání. Chcete-li zobrazit podrobnosti o odeslání, vyberte **jobUrl** v **výstup** okno. Můžete také zobrazit stav úlohy odeslání z konzoly cmd. Zadejte **7** v konzole cmd, pokud chcete zjistit podrobnosti úlohy.

   ![Nástroje data Lake pro Visual Studio Code místní spuštění výstupu](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-result.png)
   ![nástroje Data Lake pro Visual Studio Code místní spuštění cmd stav](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-localrun-cmd-status.png) 


## <a name="start-a-local-debug-for-the-u-sql-job"></a>Spustit místní ladění pro projekt U-SQL  
Pro první uživatel:

1. Použití **ADL: Stáhněte si balíček místní spuštění** stáhnout místní spuštění balíčky, pokud máte není [nastavení prostředí pro místní spuštění U-SQL](#set-up-the-u-sql-local-run-environment).
2. Nainstalujte rozhraní .NET Core SDK 2.0 jako navrhované v okně, pokud není nainstalovaná.
 
  ![připomenutí nainstaluje Dotnet.](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/remind-install-dotnet.png)
3. Instalace jazyka C# pro Visual Studio Code jako navržený v okně se zprávou, pokud není nainstalována. Klikněte na tlačítko **nainstalovat** chcete pokračovat a pak restartujte VSCode.

    ![Připomenutí k instalaci jazyka C#](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/install-csharp.png)

Postupujte podle následujících kroků a provést místní ladění:
  
1. Vyberte Ctrl + Shift + P otevřete paletu příkaz, a potom zadejte **ADL: spustit místní spustit službu**. Otevře se konzola cmd. Ujistěte se, že **DataRoot** nastavena.
2. Nastavte zarážky v vaší C# kódu.
3. Zpět na editor skriptů, klikněte pravým tlačítkem a vyberte **ADL: místní ladění**.
    
   ![Nástroje data Lake pro Visual Studio Code výsledek místní ladění](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-debug-result.png)


## <a name="next-steps"></a>Další postup
* [Použití nástrojů Azure Data Lake pro Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
* [Vývoj U-SQL s Python, R a CSharp pro Azure Data Lake Analytics v VSCode](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
* [Začínáme s Data Lake Analytics pomocí prostředí PowerShell](data-lake-analytics-get-started-powershell.md)
* [Začínáme s Data Lake Analytics pomocí portálu Azure](data-lake-analytics-get-started-portal.md)
* [Pomocí nástrojů Data Lake pro Visual Studio pro vývoj aplikací U-SQL](data-lake-analytics-data-lake-tools-get-started.md)
* [Použití Data Lake Analytics(U-SQL) katalogu](data-lake-analytics-use-u-sql-catalog.md)
