---
title: Použití Azure Automation Runbook ke správě zařízení StorSimple | Microsoft Docs
description: Naučte se používat Azure Automation Runbook k automatizaci úloh StorSimple
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 10/23/2017
ms.author: alkohli
ms.openlocfilehash: cfd0e4dbb6a4f24df5ba42cd45f9c16fbe5b493c
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2017
ms.locfileid: "23934828"
---
# <a name="use-azure-automation-runbooks-to-manage-storsimple-devices"></a>Používat Azure Automation. runbooky ke správě zařízení StorSimple

Tento článek popisuje, jak Azure Automation runbook se používají ke správě zařízení řady StorSimple 8000 na portálu Azure. Ukázkové sady runbook je zahrnut pro vás provede kroky konfigurace prostředí pro spuštění této sady runbook.


## <a name="configure-add-and-run-azure-runbook"></a>Konfigurace, přidat a spuštění sady runbook Azure

Tato část trvá příklad skriptu Windows Powershellu pro StorSimple a podrobnosti o různých kroky potřebné k importu skript do sady runbook a publikování a spuštění sady runbook.

### <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, zda máte:

* aktivní předplatné Azure spojené s služby StorSimple Manager zařízení zaregistrovali zařízení řady StorSimple 8000.


* V počítači nainstalováno prostředí Windows PowerShell 5.0 (nebo, Windows Server hostitele pro vaše zařízení StorSimple, pokud pomocí jednoho).


### <a name="create-automation-runbook-module-in-windows-powershell"></a>Vytvořit modul automatizace sady runbook v prostředí Windows PowerShell

Pokud chcete vytvořit modul automatizace pro správu zařízení řady StorSimple 8000, proveďte následující kroky:

1. Spusťte Windows PowerShell. Vytvořte novou složku a změnit adresář, do nové složky.

    ```
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```    
2. [Stáhnout rozhraní příkazového řádku NuGet](http://www.nuget.org/downloads) ve složce vytvořili v předchozím kroku. Existují různé verze _nuget.exe_. Vyberte odpovídající vaší SDK verze. Každý odkaz ke stažení přímo na body _.exe_ souboru. Klikněte pravým tlačítkem myši a uložte soubor do počítače se spíše než spuštěna z prohlížeče.

    Můžete taky spustit následující příkaz ke stažení a uložení skriptu ve stejné složce, kterou jste vytvořili dříve.
    
    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```
3. Stažení sady SDK závislé.

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```    
4. Stáhněte skript z ukázkového projektu Githubu.

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1 -Out Monitor-Backups.ps1

    ```
5. Vytvořte modul Azure Automation Runbook ke správě zařízení StorSimple řady 8000. V okně prostředí Windows Powershell zadejte následující příkazy:

    ```
            # set path variables
            $downloadDir = "C:\scripts\StorSimpleSDKTools"
            $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.StorSimple8000Series"

            #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Series"
            mkdir "$moduleDir"
            copy "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Microsoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft.Rest.ClientRuntime*.dll" $moduleDir
            copy "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9-preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\lib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir

            #Don't change the name of the Archive
            compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Management.StorSimple8000Series.zip

    ```

6. Zkontrolujte, zda je soubor zip modulu automatizace vytvořen v `C:\scripts\StorSimpleSDKTools`.

    ![Ověřte modul automation.](./media/storsimple-8000-automation-azurerm-runbook/verify-automation-module.png)

7. Tento výstup se zobrazí, když modul automatizace je vytvořen pomocí prostředí Windows PowerShell. 

    ```
    -----------------------------------------
    Windows PowerShell
    Copyright (C) 2016 Microsoft Corporation. All rights reserved.

    PS C:\WINDOWS\system32> mkdir C:\scripts\StorSimpleSDKTools

        Directory: C:\scripts

    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----       10/18/2017   8:43 AM                StorSimpleSDKTools


    PS C:\WINDOWS\system32> cd c:\scripts\StorSimpleSDKTools
    PS C:\scripts\StorSimpleSDKTools> wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorS
    impleSDKTools\nuget.exe
    PS C:\scripts\StorSimpleSDKTools> C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8
    000series

    -------------------------------------------
    CUT              CUT  
    -------------------------------------------
    Successfully installed 'Microsoft.Azure.Management.Storsimple8000series 1.0.0' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 1.77 sec
    PS C:\scripts\StorSimpleSDKTools> C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.Active
    Directory -Version 2.28.3

    -------------------------------------------
    CUT              CUT  
    -------------------------------------------
    Successfully installed 'Microsoft.IdentityModel.Clients.ActiveDirectory 2.28.3' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 927.64 ms
    PS C:\scripts\StorSimpleSDKTools> C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Aut
    hentication -Version 2.2.9-preview

    -------------------------------------------
    CUT              CUT  
    -------------------------------------------
    Successfully installed 'Microsoft.Rest.ClientRuntime.Azure.Authentication 2.2.9-preview' to C:\scripts\StorSimpleSDKTool
    s
    Executing nuget actions took 717.48 ms
    PS C:\scripts\StorSimpleSDKTools> wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Mo
    nitor-Backups.ps1 -Out Monitor-Backups.ps1
    PS C:\scripts\StorSimpleSDKTools> # set path variables
    PS C:\scripts\StorSimpleSDKTools>             $downloadDir = "C:\scripts\StorSimpleSDKTools"
    PS C:\scripts\StorSimpleSDKTools>             $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.Sto
    rSimple8000Series"
    PS C:\scripts\StorSimpleSDKTools>
    PS C:\scripts\StorSimpleSDKTools>             #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Se
    ries"
    PS C:\scripts\StorSimpleSDKTools>             mkdir "$moduleDir"

        Directory: C:\scripts\StorSimpleSDKTools\AutomationModule


    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----       10/18/2017   8:48 AM                Microsoft.Azure.Management.StorSimple8000Series

    PS C:\scripts\StorSimpleSDKTools>             copy "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\
    lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
    PS C:\scripts\StorSimpleSDKTools>             copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Mic
    rosoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
    PS C:\scripts\StorSimpleSDKTools>             copy "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft
    .Rest.ClientRuntime*.dll" $moduleDir
    PS C:\scripts\StorSimpleSDKTools>             copy "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $
    moduleDir
    PS C:\scripts\StorSimpleSDKTools>             copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9
    -preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
    PS C:\scripts\StorSimpleSDKTools>             copy "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\l
    ib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir
    PS C:\scripts\StorSimpleSDKTools>
    PS C:\scripts\StorSimpleSDKTools>             #Don't change the name of the Archive
    PS C:\scripts\StorSimpleSDKTools>             compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Manag
    ement.StorSimple8000Series.zip
    PS C:\scripts\StorSimpleSDKTools>

    -------------------------------------------

    ```

### <a name="import-publish-and-run-automation-runbook"></a>Import, publikování a spuštění sady runbook automatizace

1. Vytvoření účtu automation spustit v Azure jako na portálu Azure. Chcete-li to provést, přejděte na **Azure marketplace > všechno, co** a poté vyhledejte **automatizace**. Vyberte **účty Automation**.

    ![hledání automatizace](./media/storsimple-8000-automation-azurerm-runbook/automation1.png)

2. V **přidat účet Automation** okno:

    1. Zadejte **název** účtu Automation.
    2. Vyberte **předplatné** propojené s služby StorSimple Manager zařízení.
    3. Vytvořte novou skupinu prostředků nebo vyberte existující skupinu prostředků.
    4. Vyberte **umístění** (Pokud je to možné stejná jako se spuštěným služby).
    5. Ponechte výchozí nastavení **vytvořit účet Spustit jako** možnost.
    5. Volitelně můžete zkontrolovat **připnout na řídicí panel**. Klikněte na možnost **Vytvořit**.

        ![Vytvoření účtu automation](./media/storsimple-8000-automation-azurerm-runbook/create-automation-account.png)

    Po úspěšném vytvoření účtu automation, budete upozorněni. Další informace o tom, jak vytvořit účet Automation, přejděte na [vytvořit účet Spustit jako](https://docs.microsoft.com/azure/automation/automation-create-runas-account).

3. Aby se zajistilo, že vytvořený účet automation mají přístup ke službě StorSimple Manager zařízení, musíte přiřadit příslušná oprávnění k účtu automation. Přejděte na **řízení přístupu** ve službě StorSimple Manager zařízení. Klikněte na tlačítko **+ přidat** a zadejte název účtu Azure Automation. **Uložit** nastavení.

    ![Přidat oprávnění--účet automation](./media/storsimple-8000-automation-azurerm-runbook/goto-add-roles.png)

4. V nově vytvořený účet, přejděte do **sdílené prostředky > moduly** a klikněte na tlačítko **+ přidat modul**.

5. V **přidat modul** okno, přejděte do umístění komprimované modul a vyberte a otevřete modul. Klikněte na **OK**.

    ![Přidat modul](./media/storsimple-8000-automation-azurerm-runbook/add-module.png)

6. Přejděte na **automatizace procesu > sady Runbook a klikněte na tlačítko + Přidat runbook**. V **přidat runbook** okně klikněte na tlačítko **importovat stávající runbook**. Přejděte na soubor skriptu prostředí Windows PowerShell pro **soubor sady Runbook**. Typ runbooku je automaticky vybrán. Zadejte název a volitelný popis pro sadu runbook. Klikněte na možnost **Vytvořit**.

    ![Přidat modul](./media/storsimple-8000-automation-azurerm-runbook/import-runbook.png)

7. Runbook se přidá do seznamu sad runbook. Vyberte a klikněte na tuto sadu runbook.

    ![Klikněte na novou sadu runbook](./media/storsimple-8000-automation-azurerm-runbook/verify-runbook-created.png)

8. Upravte sadu runbook a klikněte na tlačítko **testovací podokno**. Zadejte parametry, jako je například název vaší služby StorSimple Manager zařízení, název zařízení StorSimple a odběr. **Spustit** test. Kdy je sestava vygenerována po dokončení spuštění. Další informace, přejděte na [postup testování sady runbook](../automation/automation-first-runbook-textual-powershell.md#step-3---test-the-runbook).

    ![Test-runbook](./media/storsimple-8000-automation-azurerm-runbook/test-runbook.png)

9. Zkontrolujte výstup z runbooku v testovací podokno. Pokud budete s informacemi spokojeni, zavřete podokno. Klikněte na tlačítko **publikovat** a po zobrazení výzvy k potvrzení, potvrďte a publikovat sadu runbook.

    ![publikování runbooku](./media/storsimple-8000-automation-azurerm-runbook/publish-runbook.png)


## <a name="next-steps"></a>Další kroky

[Pomocí Správce zařízení StorSimple služby ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).