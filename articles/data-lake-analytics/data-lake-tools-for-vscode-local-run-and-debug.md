---
title: Spuštění U-SQL a ladit v místním prostředí v Azure Data Lake Tools pro Visual Studio Code
description: Další informace o použití Azure Data Lake Tools pro Visual Studio Code pro spouštění a ladění úloh U-SQL místně.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.topic: conceptual
ms.date: 07/14/2017
ms.openlocfilehash: 42982e3fa0a854109a6b887640604498ac68847c
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2018
ms.locfileid: "45632255"
---
# <a name="run-u-sql-and-debug-locally-in-visual-studio-code"></a>Spuštění U-SQL a místní ladění ve Visual Studio Code
Tento článek popisuje, jak spouštět úlohy U-SQL na místním vývojovém počítači, ke zrychlení kódování počátečních fázích nebo chcete-li ladit kód místně v aplikaci Visual Studio Code. Pokyny pro nástroj Azure Data Lake pro Visual Studio Code najdete v tématu [použití Azure Data Lake Tools pro Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md).

Pouze Windows instalace nástrojů Azure Data Lake pro Visual Studio podporují akci spuštění U-SQL místně a ladění U-SQL místně. Instalace v systémech založených na Linuxu a macOS nepodporují tuto funkci.

## <a name="set-up-the-u-sql-local-run-environment"></a>Nastavení prostředí pro místní spuštění U-SQL

1. Vyberte kombinaci kláves Ctrl + Shift + P otevřete paletu příkazů, a pak zadejte **ADL: Stáhněte si místní spuštění balíčku** stáhnout balíčky.  

   ![Stáhnout balíčky závislostí LocalRun ADL](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/downloadtheadllocalrunpackage.png)

2. Vyhledejte balíčky závislostí z cesty je znázorněno **výstup** podokně a potom nainstalujte BuildTools a Win10SDK 10240. Tady je příklad cesty:  
`C:\Users\xxx\AppData\Roaming\LocalRunDependency` 

   ![Vyhledejte balíčky závislostí](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/LocateDependencyPath.png)

   2.1 k instalaci **BuildTools**, klikněte ve složce LocalRunDependency visualcppbuildtools_full.exe a pak postupujte podle pokynů průvodce.   

    ![Nainstalujte BuildTools](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallBuildTools.png)

   2.2 k instalaci **Win10SDK 10240**, klikněte ve složce LocalRunDependency/Win10SDK_10.0.10240_2 sdksetup.exe a pak postupujte podle pokynů průvodce.  

    ![Nainstalujte Win10SDK 10240](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallWin10SDK.png)

3. Nastavte proměnnou prostředí. Nastavte **SCOPE_CPP_SDK** proměnné prostředí:  
`C:\Users\XXX\AppData\Roaming\LocalRunDependency\CppSDK_3rdparty`  


## <a name="start-the-local-run-service-and-submit-the-u-sql-job-to-a-local-account"></a>Spustit službu místní spuštění a odeslání úlohy U-SQL pro místní účet 
Poprvé, použijte **ADL: Stáhněte si místní spuštění balíčku** stáhnout místní spuštění balíčky, pokud ne [nastavení prostředí pro místní spuštění U-SQL](#set-up-the-u-sql-local-run-environment).

1. Vyberte kombinaci kláves Ctrl + Shift + P otevřete paletu příkazů, a pak zadejte **ADL: spustit místní službu spustit**.   
2. Vyberte **přijmout** přijmout licenční podmínky pro Software společnosti Microsoft poprvé. 

   ![Přijměte licenční podmínky pro Software společnosti Microsoft](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/AcceptEULA.png)   
3. Otevře se konzola příkazového řádku. Pro první uživatele, budete muset zadat **3**a pak vyhledejte cestu místní složky pro váš datový vstup a výstup. Další možnosti můžete použít výchozí hodnoty. 

   ![Data Lake Tools pro Visual Studio Code místní spuštění příkazového řádku](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-cmd.png)
4. Vyberte kombinaci kláves Ctrl + Shift + P otevřete paletu příkazů zobrazíte zadáním **ADL: odeslat úlohu**a pak vyberte **místní** se odeslat úlohu do místní účet.

   ![Vyberte místní data Lake Tools pro Visual Studio Code](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-select-local.png)
5. Po odeslání úlohy můžete zobrazit podrobnosti o odeslání. Chcete-li zobrazit podrobnosti o odeslání, vyberte **jobUrl** v **výstup** okna. Můžete také zobrazit stav odeslání úlohy z konzoly příkazového řádku. Zadejte **7** v konzole nástroje příkazového řádku, pokud chcete zjistit další podrobnosti o úloze.

   ![Nástroje data Lake pro Visual Studio Code místní spuštění výstupu](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-result.png)
   ![nástrojů Data Lake pro Visual Studio Code místní cmd stav spuštění](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-localrun-cmd-status.png) 


## <a name="start-a-local-debug-for-the-u-sql-job"></a>Spusťte místní ladění pro úlohu U-SQL  
Pro první uživatele:

1. Použití **ADL: Stáhněte si místní spuštění balíčku** stáhnout místní spuštění balíčky, pokud ne [nastavení prostředí pro místní spuštění U-SQL](#set-up-the-u-sql-local-run-environment).
2. Nainstalujte .NET Core SDK 2.0 jak je navrženo v okně se zprávou, pokud není nainstalovaná.
 
  ![připomenutí nainstaluje Dotnet](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/remind-install-dotnet.png)
3. Instalace jazyka C# pro Visual Studio Code jako navržený v okně se zprávou, pokud není nainstalovaný. Klikněte na tlačítko **nainstalovat** pokračovat a restartujte VSCode.

    ![Připomenutí instalace jazyka C#](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/install-csharp.png)

Postupujte podle následujících kroků a proveďte místní ladění:
  
1. Vyberte kombinaci kláves Ctrl + Shift + P otevřete paletu příkazů, a pak zadejte **ADL: spustit místní službu spustit**. Otevře se konzola příkazového řádku. Ujistěte se, že **DataRoot** nastavena.
2. Nastavte zarážku do vašeho jazyka C# kódu na pozadí.
3. Zpět na editor skriptů, klikněte pravým tlačítkem a vyberte **ADL: místní ladění**.
    
   ![Data Lake Tools pro Visual Studio Code místních ladicích výsledek](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-debug-result.png)


## <a name="next-steps"></a>Další postup
* [Použití nástrojů Azure Data Lake pro Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
* [Vývoj U-SQL pomocí Pythonu, R a CSharp pro Azure Data Lake Analytics ve VSCode](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
* [Začínáme s Data Lake Analytics pomocí Powershellu](data-lake-analytics-get-started-powershell.md)
* [Začínáme s Data Lake Analytics pomocí webu Azure portal](data-lake-analytics-get-started-portal.md)
* [Pomocí nástrojů Data Lake pro Visual Studio pro vývoj aplikací v U-SQL](data-lake-analytics-data-lake-tools-get-started.md)
* [Použití Data Lake Analytics(U-SQL) katalogu](data-lake-analytics-use-u-sql-catalog.md)
