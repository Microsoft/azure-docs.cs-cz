---
title: Správa zařízení StorSimple pomocí sady Azure Automation Runbook
description: Naučte se, jak pomocí sady Azure Automation Runbook automatizovat úlohy StorSimple
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 10/23/2017
ms.author: alkohli
ms.openlocfilehash: 727bebe0c190ed4dff4408884c45fe166ad541a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76276976"
---
# <a name="use-azure-automation-runbooks-to-manage-storsimple-devices"></a>Správa zařízení StorSimple pomocí runbooků Azure Automation

Tento článek popisuje, jak se runbooky Azure Automation používají ke správě zařízení řady StorSimple 8000 na webu Azure Portal. Součástí ukázkového seznamu runbooků jsou kroky konfigurace prostředí pro spuštění tohoto seznamu runbook.


## <a name="configure-add-and-run-azure-runbook"></a>Konfigurace, přidání a spuštění sady Runbook Azure

Tato část má příklad skriptu prostředí Windows PowerShell pro StorSimple a podrobně popisuje různé kroky potřebné k importu skriptu do sady Runbook a následnépublikování a spuštění sady Runbook.

### <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že máte:

* aktivní předplatné Azure přidružené k vaší službě StorSimple Device Manager registrované na zařízení řady StorSimple 8000.

* V počítači je nainstalováno prostředí Windows PowerShell 5.0 (Nebo hostitel systému Windows Server pro váš StorSimple, pokud jej používáte).

### <a name="create-automation-runbook-module-in-windows-powershell"></a>Vytvoření modulu runbook automatizace v prostředí Windows PowerShell

Chcete-li vytvořit modul automatizace pro správu zařízení řady StorSimple 8000, proveďte následující kroky:

1. Spusťte prostředí Windows PowerShell. Vytvořte novou složku a změňte adresář na novou složku.

    ```powershell
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```

2. [Stáhněte si rozhraní se zástupcem vazby NuGet](https://www.nuget.org/downloads) pod složku vytvořenou v předchozím kroku. Existují různé verze _nuget.exe_. Zvolte verzi odpovídající vaší sadě SDK. Každý odkaz ke stažení odkazuje přímo na soubor _EXE._ Nezapomeňte soubor klepnout pravým tlačítkem myši a uložit jej do počítače, nikoli jej spustit z prohlížeče.

    Můžete také spustit následující příkaz ke stažení a uložení skriptu ve stejné složce, kterou jste vytvořili dříve.

    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```

3. Stáhněte závislou sadu SDK.

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```

4. Stáhněte si skript z ukázkového projektu GitHub.

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1 -Out Monitor-Backups.ps1
    ```

5. Vytvořte modul runbooku Azure Automation pro správu zařízení řady StorSimple 8000. V okně Windows Powershell zadejte následující příkazy:

    ```powershell
        # set path variables
        $downloadDir = "C:\scripts\StorSimpleSDKTools"
        $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.StorSimple8000Series"

        #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Series"
        mkdir "$moduleDir"
        Copy-Item "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Microsoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft.Rest.ClientRuntime*.dll" $moduleDir
        Copy-Item "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9-preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\lib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir

        #Don't change the name of the Archive
        compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Management.StorSimple8000Series.zip
    ```

6. Ověřte, zda je v `C:\scripts\StorSimpleSDKTools`aplikaci vytvořen soubor zip modulu automatizace.

    ![ověřit-automatizace-modul](./media/storsimple-8000-automation-azurerm-runbook/verify-automation-module.png)

7. Následující výstup je uveden při vytvoření modulu automatizace prostřednictvím prostředí Windows PowerShell.

    ```powershell
    mkdir C:\scripts\StorSimpleSDKTools
    ```

    ```Output
        Directory: C:\scripts

    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----       10/18/2017   8:43 AM                StorSimpleSDKTools
    ```

    ```powershell
    wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```

    ```powershell
    C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
    ```

    ```Output
    -------------------------------------------
    CUT              CUT
    -------------------------------------------
    Successfully installed 'Microsoft.Azure.Management.Storsimple8000series 1.0.0' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 1.77 sec
    ```

    ```powershell
    C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
    ```

    ```Output
    -------------------------------------------
    CUT              CUT
    -------------------------------------------
    Successfully installed 'Microsoft.IdentityModel.Clients.ActiveDirectory 2.28.3' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 927.64 ms
    ```

    ```powershell
    C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```

    ```Output
    -------------------------------------------
    CUT              CUT
    -------------------------------------------
    Successfully installed 'Microsoft.Rest.ClientRuntime.Azure.Authentication 2.2.9-preview' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 717.48 ms
    ```

    ```powershell
    wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1 -Out Monitor-Backups.ps1
    # set path variables
    $downloadDir = "C:\scripts\StorSimpleSDKTools"
    $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.StorSimple8000Series"
    #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Series"
    mkdir "$moduleDir"
    ```

    ```Output
        Directory: C:\scripts\StorSimpleSDKTools\AutomationModule

    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----       10/18/2017   8:48 AM                Microsoft.Azure.Management.StorSimple8000Series
    ```

    ```powershell
    Copy-Item "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Microsoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft.Rest.ClientRuntime*.dll" $moduleDir
    Copy-Item "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9-preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\lib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir
    #Don't change the name of the Archive
    compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Management.StorSimple8000Series.zip
    ```

### <a name="import-publish-and-run-automation-runbook"></a>Import, publikování a spuštění runbooku Automation

1. Vytvořte účet Azure Run As automation na webu Azure Portal. Chcete-li tak učinit, přejděte na **Azure marketplace > Všechno** a **vyhledejte automatizaci**. Vyberte **automatizační účty**.

    ![automatizace vyhledávání](./media/storsimple-8000-automation-azurerm-runbook/automation1.png)

2. V okně **Přidat účet automatizace:**

   1. Zadejte **název** účtu Automation.
   2. Vyberte **předplatné** spojené se službou StorSimple Device Manager.
   3. Vytvořte novou skupinu prostředků nebo vyberte z existující skupiny prostředků.
   4. Vyberte **umístění** (pokud možno stejné jako místo, kde je spuštěna vaše služba).
   5. Ponechte vybranou výchozí možnost **Vytvořit spustit jako účet.**
   6. Volitelně **zaškrtněte políčko Připnout k řídicímu panelu**. Klikněte na **Vytvořit**.

       ![vytvořit-automatizace-účet](./media/storsimple-8000-automation-azurerm-runbook/create-automation-account.png)

      Po úspěšném vytvoření účtu automatizace budete upozorněni. Další informace o tom, jak vytvořit účet automatizace, naleznete v najdete v na [jet spustit jako účet](https://docs.microsoft.com/azure/automation/automation-create-runas-account).

3. Chcete-li zajistit, aby vytvořený účet automatizace měl přístup ke službě StorSimple Device Manager, je třeba přiřadit účtu automatizace příslušná oprávnění. Přejděte do **řízení přístupu** ve službě Správce zařízení StorSimple. Klikněte na **+ Přidat** a zadejte název účtu Azure Automation. **Uložte** nastavení.

    ![add-permissions-automation-account](./media/storsimple-8000-automation-azurerm-runbook/goto-add-roles.png)

4. V nově vytvořeném účtu přejděte na **sdílené prostředky > moduly** a klepněte na tlačítko **+ Přidat modul**.

5. V okně **Přidat modul** vyhledejte umístění modulu zip a vyberte a otevřete modul. Klikněte na tlačítko **OK**.

    ![přidat modul](./media/storsimple-8000-automation-azurerm-runbook/add-module.png)

6. Přejděte na **> runbooky automatizace procesů a klikněte na + Přidat runbook**. V okně **Přidat runbook** klepněte na **importovat existující runbook**. Přejděte na soubor skriptu prostředí Windows PowerShell pro **soubor runbooku**. Automaticky se vybere typ knihy Runbook. Zadejte název a volitelný popis runbooku. Klikněte na **Vytvořit**.

    ![přidat modul](./media/storsimple-8000-automation-azurerm-runbook/import-runbook.png)

7. Runbook je přidán do seznamu runbooků. Vyberte a klikněte na tento soubor Runbook.

    ![click-new-runbook](./media/storsimple-8000-automation-azurerm-runbook/verify-runbook-created.png)

8. Upravte runbook a klepněte na **podokno Test**. Zadejte parametry, jako je název služby Správce zařízení StorSimple, název zařízení StorSimple a předplatné. **Spusťte** test. Sestava je generována po dokončení spuštění. Další informace naleznete v [otestovaném runbooku](../automation/automation-first-runbook-textual-powershell.md#step-3---test-the-runbook).

    ![testovací runbook](./media/storsimple-8000-automation-azurerm-runbook/test-runbook.png)

9. Zkontrolujte výstup z runbooku v testovacím podokně. Pokud je splněna, zavřete podokno. Klikněte na **Publikovat** a po zobrazení výzvy k potvrzení, potvrzení a publikování runbooku.

    ![publish-runbook](./media/storsimple-8000-automation-azurerm-runbook/publish-runbook.png)

## <a name="next-steps"></a>Další kroky

[Ke správě zařízení StorSimple použijte službu StorSimple](storsimple-8000-manager-service-administration.md)Device Manager .
