---
title: Použití Runbooku Azure Automation ke správě zařízení StorSimple | Dokumentace Microsoftu
description: Další informace o použití Runbooku Azure Automation k automatizaci úloh StorSimple
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
ms.openlocfilehash: 30d70bb7e1f868060e3b287a0cdfb117c585b9ba
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2019
ms.locfileid: "58793505"
---
# <a name="use-azure-automation-runbooks-to-manage-storsimple-devices"></a>Použití runbooků Azure Automation ke správě zařízení StorSimple

Tento článek popisuje, jak Azure Automation. runbooky se používají ke správě vašeho zařízení StorSimple řady 8000 na webu Azure portal. Ukázkové sady runbook je zahrnuta vás provede kroky pro konfiguraci vašeho prostředí ke spuštění této sady runbook.


## <a name="configure-add-and-run-azure-runbook"></a>Konfigurace, přidání a spuštění runbooku Azure

Tato část používá příklad skriptu Windows Powershellu pro StorSimple a podrobně popisuje různé kroky potřebné k importujte skript do sady runbook a potom publikovat a spustit sadu runbook.

### <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že máte:

* aktivní předplatné Azure spojené s vaší službou Správce zařízení StorSimple zaregistrovat zařízení řady StorSimple 8000.

* V počítači nainstalovaný Windows PowerShell 5.0 (nebo v systému Windows Server hostitele pro StorSimple, pokud pomocí některého).

### <a name="create-automation-runbook-module-in-windows-powershell"></a>Vytvoření modulu sady runbook automation ve Windows Powershellu

Pokud chcete vytvořit modul služby automation pro správu zařízení řady StorSimple 8000, postupujte následovně:

1. Spuštění Windows Powershellu. Vytvoření nové složky a přejděte do nové složky.

    ```powershell
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```

2. [Stáhněte si rozhraní příkazového řádku NuGet](https://www.nuget.org/downloads) v rámci složky vytvořené v předchozím kroku. Existují různé verze _nuget.exe_. Zvolte verzi odpovídající sady SDK. Každý odkaz ke stažení odkazuje přímo _.exe_ souboru. Nezapomeňte klikněte pravým tlačítkem a uložte soubor do počítače, spíše než jeho spuštění v prohlížeči.

    Můžete také spustit následující příkaz ke stažení a uložení skriptu ve stejné složce, kterou jste vytvořili dříve.

    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```

3. Stáhněte sadu SDK závislá.

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```

4. Stáhněte si skript z ukázkového projektu Githubu.

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1 -Out Monitor-Backups.ps1
    ```

5. Vytvořte modul Azure Automation Runbook ke správě zařízení StorSimple řady 8000. V okně prostředí Windows Powershell zadejte následující příkazy:

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

6. Ověřte, že soubor zip modulu služby automation je vytvořen v `C:\scripts\StorSimpleSDKTools`.

    ![Ověřte modul služby automation](./media/storsimple-8000-automation-azurerm-runbook/verify-automation-module.png)

7. Následující výstup se zobrazí, když se modul automation přes Windows PowerShell.

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

1. Vytvoření účtu automation spustit jako pro Azure na webu Azure Portal. Uděláte to tak, přejděte na **Azure marketplace > vše, co** a vyhledejte **automatizace**. Vyberte **účty Automation**.

    ![search-automation](./media/storsimple-8000-automation-azurerm-runbook/automation1.png)

2. V **přidat účet Automation** okno:

   1. Zadat **název** vašeho účtu Automation.
   2. Vyberte **předplatné** propojenému s vaší službou Správce zařízení StorSimple.
   3. Vytvořit novou skupinu prostředků nebo vyberte existující skupinu prostředků.
   4. Vyberte **umístění** (Pokud je to možné stejná jako ve kterém je vaše služba spuštěná).
   5. Ponechte výchozí nastavení **vytvořit účet Spustit jako** zaškrtnutou možnost.
   6. Volitelně můžete zkontrolovat **připnout na řídicí panel**. Klikněte na možnost **Vytvořit**.

       ![create-automation-account](./media/storsimple-8000-automation-azurerm-runbook/create-automation-account.png)

      Po úspěšném vytvoření účtu automation, budete upozorněni. Další informace o tom, jak vytvořit účet Automation, přejděte na [vytvořit účet Spustit jako](https://docs.microsoft.com/azure/automation/automation-create-runas-account).

3. Aby bylo zajištěno, že účet služby automation, který jste vytvořili můžete přístup ke službě Správce zařízení StorSimple, potřebujete přiřadit příslušná oprávnění k účtu automation. Přejděte na **řízení přístupu** ve službě Správce zařízení StorSimple. Klikněte na tlačítko **+ přidat** a zadejte název vašeho účtu Azure Automation. **Uložit** nastavení.

    ![add-permissions-automation-account](./media/storsimple-8000-automation-azurerm-runbook/goto-add-roles.png)

4. V nově vytvořeného účtu, přejděte na **sdílené prostředky > moduly** a klikněte na tlačítko **+ přidat modul**.

5. V **přidat modul** okno, přejděte do umístění modulu ZIP a vyberte a otevřete modul. Klikněte na **OK**.

    ![Přidat modul](./media/storsimple-8000-automation-azurerm-runbook/add-module.png)

6. Přejděte na **automatizace procesů > sady Runbook a klikněte na tlačítko + Přidat runbook**. V **přidat runbook** okna, klikněte na tlačítko **importovat existující runbook**. Přejděte na soubor skriptu Windows Powershellu pro **soubor sady Runbook**. Typ runbooku se vybere automaticky. Zadejte název a volitelný popis pro sadu runbook. Klikněte na možnost **Vytvořit**.

    ![Přidat modul](./media/storsimple-8000-automation-azurerm-runbook/import-runbook.png)

7. Runbook se přidá do seznamu sad runbook. Vyberte a klikněte na tuto sadu runbook.

    ![click-new-runbook](./media/storsimple-8000-automation-azurerm-runbook/verify-runbook-created.png)

8. Upravit sadu runbook a klikněte na tlačítko **testovací podokno**. Zadejte parametry, jako je například název vaší služby Správce zařízení StorSimple, název zařízení StorSimple a předplatné. **Spustit** testu. Po dokončení spuštění se generuje sestava. Další informace najdete v části [testování sady runbook](../automation/automation-first-runbook-textual-powershell.md#step-3---test-the-runbook).

    ![Test-runbook](./media/storsimple-8000-automation-azurerm-runbook/test-runbook.png)

9. Zkontrolujte výstup z runbooku v testovací podokno. Při splnění zavřete podokno. Klikněte na tlačítko **publikovat** a po zobrazení výzvy k potvrzení, potvrzení a publikovat sadu runbook.

    ![publikování runbooku](./media/storsimple-8000-automation-azurerm-runbook/publish-runbook.png)

## <a name="next-steps"></a>Další postup

[Služba Správce zařízení StorSimple používá ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).
