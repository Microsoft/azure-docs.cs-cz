---
title: Použití Azure Automation Runbook ke správě zařízení StorSimple
description: Naučte se používat Azure Automation Runbook ke správě zařízení řady StorSimple 8000 v Azure Portal.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 10/23/2017
ms.author: alkohli
ms.openlocfilehash: dff1d8f48b275fa7e45dab93b5ce45962499b450
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90563875"
---
# <a name="use-azure-automation-runbooks-to-manage-storsimple-devices"></a>Použití Azure Automation Runbooky ke správě zařízení StorSimple

Tento článek popisuje, jak se Azure Automation Runbooky používají ke správě zařízení řady StorSimple 8000 v Azure Portal. K dispozici je ukázková sada Runbook, která vás provede jednotlivými kroky konfigurace prostředí pro spuštění této sady Runbook.


## <a name="configure-add-and-run-azure-runbook"></a>Konfigurace, přidání a spuštění Runbooku Azure

V této části najdete příklad skriptu Windows PowerShellu pro StorSimple a podrobnosti o různých krocích potřebných k importu skriptu do sady Runbook a následném publikování a spuštění sady Runbook.

### <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že máte následující:

* aktivní předplatné Azure přidružené k vaší službě StorSimple Device Manager zaregistrovanou pomocí zařízení řady StorSimple 8000.

* Prostředí Windows PowerShell 5,0 nainstalované na vašem počítači (nebo hostitele Windows serveru pro StorSimple, pokud ho používáte).

### <a name="create-automation-runbook-module-in-windows-powershell"></a>Vytvoření modulu Automation Runbook v prostředí Windows PowerShell

Chcete-li vytvořit modul automatizace pro správu zařízení řady StorSimple 8000, proveďte následující kroky:

1. Spusťte prostředí Windows PowerShell. Vytvořte novou složku a změňte adresář na novou složku.

    ```powershell
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```

2. [Stáhněte si NUGET CLI](https://www.nuget.org/downloads) do složky vytvořené v předchozím kroku. Existují různé verze _nuget.exe_. Vyberte verzi odpovídající vaší sadě SDK. Každý odkaz ke stažení odkazuje přímo na soubor _. exe_ . Ujistěte se, že kliknete pravým tlačítkem a uložíte soubor do počítače, ale nebudete ho spouštět z prohlížeče.

    Spuštěním následujícího příkazu můžete také stáhnout a uložit skript ve stejné složce, kterou jste vytvořili dříve.

    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```

3. Stáhněte si závislou sadu SDK.

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```

4. Stáhněte si skript z ukázkového projektu GitHub.

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1 -Out Monitor-Backups.ps1
    ```

5. Vytvořte Azure Automation modul Runbooku pro správu zařízení řady StorSimple 8000. V okně prostředí Windows PowerShell zadejte následující příkazy:

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

6. Ověřte, že je v nástroji vytvořen soubor zip modulu automatizace `C:\scripts\StorSimpleSDKTools` .

    ![Snímek obrazovky zobrazuje složku StorSimpleSDKTools se souborem zip modulu Automation.](./media/storsimple-8000-automation-azurerm-runbook/verify-automation-module.png)

7. Následující výstup se zobrazí, když se modul automatizace vytvoří pomocí prostředí Windows PowerShell.

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

### <a name="import-publish-and-run-automation-runbook"></a>Import, publikování a spuštění Runbooku služby Automation

1. Vytvořte v Azure Portal účet služby Automation spustit jako pro Azure. Provedete to tak, že přejdete na **Azure marketplace > vše** a pak vyhledáte **Automation**. Vyberte **účty Automation**.

    ![hledání – automatizace](./media/storsimple-8000-automation-azurerm-runbook/automation1.png)

2. V okně **Přidat účet Automation** :

   1. Zadejte **název** svého účtu Automation.
   2. Vyberte **předplatné** propojené s vaší službou StorSimple Device Manager.
   3. Vytvořte novou skupinu prostředků nebo vyberte některou z existujících skupin prostředků.
   4. Vyberte **umístění** (Pokud je to možné stejné jako v případě, kde je služba spuštěná).
   5. Ponechte vybranou možnost výchozí **účet Spustit jako** .
   6. Volitelně můžete zaškrtnout **Připnout na řídicí panel**. Klikněte na **Vytvořit**.

       ![Snímek obrazovky se zobrazí v podokně Přidat účet Automation s hodnotami pro předplatné, vytvořit účet Spustit jako pro Azure a vytvořit.](./media/storsimple-8000-automation-azurerm-runbook/create-automation-account.png)

      Po úspěšném vytvoření účtu Automation budete upozorněni. Další informace o tom, jak vytvořit účet Automation, najdete v tématu [Vytvoření účtu Spustit jako](https://docs.microsoft.com/azure/automation/automation-create-runas-account).

3. Abyste zajistili, že vytvořený účet Automation bude mít přístup ke službě StorSimple Device Manager, je potřeba přiřadit příslušné oprávnění k účtu Automation. Přejděte na **řízení přístupu** ve službě StorSimple Device Manager. Klikněte na **+ Přidat** a zadejte název účtu Azure Automation. **Uložte** nastavení.

    ![Přidání oprávnění – automatizace-účet](./media/storsimple-8000-automation-azurerm-runbook/goto-add-roles.png)

4. V nově vytvořeném účtu přejděte na **sdílené prostředky > moduly** a klikněte na **+ Přidat modul**.

5. V okně **Přidat modul** přejděte do umístění modulu zip a vyberte a otevřete modul. Klikněte na **OK**.

    ![Snímek obrazovky se zobrazí v podokně Přidat modul s hodnotou odeslat soubor a v pořádku.](./media/storsimple-8000-automation-azurerm-runbook/add-module.png)

6. Přejděte na **proces automatizace automatizace > Runbooky a klikněte na + Přidat Runbook**. V okně **Přidat Runbook** klikněte na **importovat existující Runbook**. Přejděte na soubor skriptu prostředí Windows PowerShell pro **soubor sady Runbook**. Typ Runbooku se vybere automaticky. Zadejte název a nepovinný popis Runbooku. Klikněte na **Vytvořit**.

    ![přidat modul](./media/storsimple-8000-automation-azurerm-runbook/import-runbook.png)

7. Runbook se přidá do seznamu runbooků. Vyberte tuto sadu Runbook a klikněte na ni.

    ![klikněte na nový-Runbook.](./media/storsimple-8000-automation-azurerm-runbook/verify-runbook-created.png)

8. Upravte Runbook a klikněte na **testovací podokno**. Zadejte parametry, jako je název služby StorSimple Device Manager, název zařízení StorSimple a předplatné. **Spusťte** test. Sestava je generována po dokončení spuštění. Další informace najdete v tématu [Postup testování Runbooku](../automation/automation-first-runbook-textual-powershell.md#step-3---test-the-runbook).

    ![Snímek obrazovky se zobrazí v posunu testu, kde jste canenter hodnoty parametrů, a spusťte test.](./media/storsimple-8000-automation-azurerm-runbook/test-runbook.png)

9. Zkontrolujte výstup Runbooku v podokně test. V případě splnění zavřete podokno. Klikněte na **publikovat** a po zobrazení výzvy k potvrzení, potvrzení a publikování Runbooku.

    ![Snímek obrazovky se zobrazí v podokně upravit PowerShellový Runbook, kde se zobrazí výzva, abyste mohli Runbook publikovat.](./media/storsimple-8000-automation-azurerm-runbook/publish-runbook.png)

## <a name="next-steps"></a>Další kroky

[Ke správě zařízení StorSimple použijte službu StorSimple Device Manager](storsimple-8000-manager-service-administration.md).
