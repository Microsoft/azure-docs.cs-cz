---
title: Ladění úloh U-SQL – Nástroje datového jezera Azure pro kód Visual Studia
description: Zjistěte, jak používat Nástroje datového jezera Azure pro kód Visual Studia ke spuštění a ladění úloh U-SQL místně.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.topic: conceptual
ms.date: 07/14/2017
ms.openlocfilehash: e51b5640163546c673a1b0f61da47ccd992f27ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72030033"
---
# <a name="run-u-sql-and-debug-locally-in-visual-studio-code"></a>Spuštění U-SQL a ladění místně v kódu sady Visual Studio
Tento článek popisuje, jak spustit úlohy U-SQL v místním vývojovém počítači, aby se urychlily fáze časného kódování nebo debugkód místně v kódu sady Visual Studio. Pokyny k nástroji Azure Data Lake Tool pro kód Visual Studia najdete v [tématu Použití nástrojů Datového jezera Azure pro kód Visual Studia](data-lake-analytics-data-lake-tools-for-vscode.md).

Akce pro místní spuštění U-SQL a místní ladění U-SQL podporují pouze instalace Windows v nástrojích Azure Data Lake Tools for Visual Studio. Instalace v operačních systémech macOS a Linux tuto funkci nepodporují.

## <a name="set-up-the-u-sql-local-run-environment"></a>Nastavení místního spuštění prostředí U-SQL

1. Vyberte Ctrl+Shift+P, chcete-li otevřít paletu příkazů, a pak zadejte **ADL: Stáhnout balíček místního spuštění** a stáhněte si balíčky.  

   ![Stažení balíčků závislostí místního spuštění ADL](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/downloadtheadllocalrunpackage.png)

2. Vyhledejte balíčky závislostí z cesty zobrazené v podokně **Výstup** a nainstalujte buildtools a win10SDK 10240. Zde je příklad cesty:  
`C:\Users\xxx\AppData\Roaming\LocalRunDependency` 

   ![Vyhledání balíčků závislostí](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/LocateDependencyPath.png)

   2.1 Chcete-li nainstalovat **buildtools**, klepněte ve složce LocalRunDependency na soubor visualcppbuildtools_full.exe a postupujte podle pokynů průvodce.   

    ![Instalace buildtoolsu](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallBuildTools.png)

   2.2 Chcete-li nainstalovat **aplikaci Win10SDK 10240**, klepněte na soubor sdksetup.exe ve složce LocalRunDependency/Win10SDK_10.0.10240_2 a postupujte podle pokynů průvodce.  

    ![Instalace win10SDK 10240](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallWin10SDK.png)

3. Nastavte proměnnou prostředí. Nastavte proměnnou **prostředí SCOPE_CPP_SDK** na:  
`C:\Users\XXX\AppData\Roaming\LocalRunDependency\CppSDK_3rdparty`  


## <a name="start-the-local-run-service-and-submit-the-u-sql-job-to-a-local-account"></a>Spuštění služby místního spuštění a odeslání úlohy U-SQL místnímu účtu 
Pro prvního uživatele použijte **ADL: Stáhnout balíček místního spuštění** ke stažení balíčků místního spuštění, pokud jste [nenastavili místní spuštění prostředí U-SQL](#set-up-the-u-sql-local-run-environment).

1. Vyberte Ctrl+Shift+P, chcete-li otevřít paletu příkazů, a pak zadejte **ADL: Start Local Run Service**.   
2. Chcete-li poprvé přijmout licenční podmínky pro software společnosti Microsoft, vyberte možnost **Přijmout.** 

   ![Přijměte licenční podmínky pro software společnosti Microsoft](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/AcceptEULA.png)   
3. Otevře se konzola cmd. Pro první uživatele je třeba zadat **hodnotu 3**a potom vyhledat cestu k místní složce pro vstup a výstup dat. Pokud neúspěšně definujete cestu pomocí zpětná lomítka, zkuste lomítka proložit. Pro další možnosti můžete použít výchozí hodnoty.

   ![Nástroje datového jezera pro místní spuštění kódu sady Visual Studio](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-cmd.png)
4. Vyberte Ctrl+Shift+P, chcete-li otevřít paletu příkazů, zadejte **ADL: Odeslat úlohu**a pak vyberte **Místní,** chcete-li úlohu odeslat do místního účtu.

   ![Nástroje datového jezera pro kód sady Visual Studio vyberte místní](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-select-local.png)
5. Po odeslání můžete zobrazit podrobnosti o odeslání. Chcete-li zobrazit podrobnosti o odeslání, vyberte v okně **Výstup** položku **adresa jobUrl.** Můžete také zobrazit stav odeslání úlohy z konzoly cmd. Pokud **7** chcete vědět více podrobností o práci, zadejte do konzole cmd 7.

   ![Data Lake Tools for Visual](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-result.png)
   ![Studio Code místní spuštění výstupu Data Lake Tools for Visual Studio Code místní spuštění stavu cmd](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-localrun-cmd-status.png) 


## <a name="start-a-local-debug-for-the-u-sql-job"></a>Spuštění místního ladění úlohy U-SQL  
Pro prvního uživatele:

1. Použití **ADL: Stáhnout balíček místního spuštění** ke stažení balíčků místního spuštění, pokud jste [nenastavili místní spuštění prostředí U-SQL](#set-up-the-u-sql-local-run-environment).
2. Pokud není nainstalována, nainstalujte do okna se zprávou .NET Core SDK 2.0, jak je navrženo.
 
  ![připomenutí nainstaluje Dotnet](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/remind-install-dotnet.png)
3. Pokud není nainstalován, nainstalujte c# pro kód sady Visual Studio, jak je navrženo v poli se zprávou.Chcete-li pokračovat, klepněte na **tlačítko Nainstalovat** a restartujte program VSCode.

    ![Připomenutí instalace C #](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/install-csharp.png)

Postupujte podle následujících kroků k provedení místního ladění:
  
1. Vyberte Ctrl+Shift+P, chcete-li otevřít paletu příkazů, a pak zadejte **ADL: Start Local Run Service**. Otevře se konzola cmd. Ujistěte se, že **je nastavena DataRoot.**
2. Nastavte zarážku v kódu C# na pozadí.
3. Zpět na editor skriptů, klepněte pravým tlačítkem myši a vyberte **ADL: Místní ladění**.
    
   ![Data Lake Tools for Visual Studio Code výsledek místního ladění](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-debug-result.png)


## <a name="next-steps"></a>Další kroky
* [Použití nástrojů Azure Data Lake pro Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
* [Vývoj U-SQL s Pythonem, R a CSharpem pro Azure Data Lake Analytics ve VSCode](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
* [Začínáme s Analýzou datových jezer pomocí PowerShellu](data-lake-analytics-get-started-powershell.md)
* [Začínáme s Analýzou datového jezera pomocí portálu Azure](data-lake-analytics-get-started-portal.md)
* [Použití nástrojů data lake pro Visual Studio pro vývoj aplikací U-SQL](data-lake-analytics-data-lake-tools-get-started.md)
* [Použití katalogu Data Lake Analytics(U-SQL)](data-lake-analytics-use-u-sql-catalog.md)
