---
title: Vytvoření webových a pracovních rolí Azure pro PHP
description: Průvodce vytvářením webových a pracovních rolí PHP v cloudové službě Azure a konfigurací runtime PHP.
services: ''
documentationcenter: php
author: msangapu
manager: cfowler
ms.assetid: 9f7ccda0-bd96-4f7b-a7af-fb279a9e975b
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/11/2018
ms.author: msangapu
ms.openlocfilehash: 54410e1e70a2ec0d3a9e2f853dc9556cd05996ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297250"
---
# <a name="create-php-web-and-worker-roles"></a>Vytvoření rolí pracovního procesu a webu PHP

## <a name="overview"></a>Přehled

Tato příručka vám ukáže, jak vytvořit webové nebo pracovní role PHP ve vývojovém prostředí Windows, vybrat konkrétní verzi PHP z dostupných "vestavěných" verzí, změnit konfiguraci PHP, povolit rozšíření a nakonec nasadit do Azure. Popisuje také, jak nakonfigurovat webovou nebo pracovní roli pro použití runtime PHP (s vlastní konfigurací a rozšířeními), které zadáte.

Azure poskytuje tři výpočetní modely pro spouštění aplikací: Azure App Service, Virtuální počítače Azure a Azure Cloud Services. Všechny tři modely podporují PHP. Cloudové služby, které zahrnují webové a pracovní role, poskytuje *platformu jako službu (PaaS).* V rámci cloudové služby poskytuje webová role vyhrazený webový server Internetové informační služby (IIS) pro hostování front-endových webových aplikací. Role pracovního procesu může spouštět asynchronní, dlouhotrvající nebo trvalé úlohy nezávisle na interakci s uživatelem nebo vstupu.

Další informace o těchto možnostech najdete [v tématu Možnosti hostování výpočetních prostředků poskytované Azure](cloud-services/cloud-services-choose-me.md).

## <a name="download-the-azure-sdk-for-php"></a>Stažení sady Azure SDK pro PHP

[Sada Azure SDK pro PHP](https://github.com/Azure/azure-sdk-for-php) se skládá z několika součástí. Tento článek bude používat dva z nich: Azure PowerShell a emulátory Azure. Tyto dvě součásti lze nainstalovat prostřednictvím Instalační služby webové platformy společnosti Microsoft. Další informace najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview).

## <a name="create-a-cloud-services-project"></a>Vytvoření projektu cloudových služeb

Prvním krokem při vytváření webové nebo pracovní role PHP je vytvoření projektu služby Azure Service. Projekt služby Azure slouží jako logický kontejner pro webové a pracovní role a obsahuje definice služby projektu [(.csdef)] a [konfigurace služby (.cscfg)] soubory.

Pokud chcete vytvořit nový projekt Služby Azure, spusťte Azure PowerShell jako správce a spusťte následující příkaz:

    PS C:\>New-AzureServiceProject myProject

Tento příkaz vytvoří nový`myProject`adresář ( ), do kterého můžete přidat webové a pracovní role.

## <a name="add-php-web-or-worker-roles"></a>Přidání webových nebo pracovních rolí PHP

Chcete-li do projektu přidat webovou roli PHP, spusťte následující příkaz z kořenového adresáře projektu:

    PS C:\myProject> Add-AzurePHPWebRole roleName

Pro roli pracovního procesu použijte tento příkaz:

    PS C:\myProject> Add-AzurePHPWorkerRole roleName

> [!NOTE]
> Parametr `roleName` je volitelný. Pokud je vynechán, bude automaticky generován název role. První webová role `WebRole1`vytvořená bude `WebRole2`, druhá bude a tak dále. První vytvořená role `WorkerRole1`pracovníka bude `WorkerRole2`, druhá bude a tak dále.
>
>

## <a name="use-your-own-php-runtime"></a>Použijte vlastní php runtime

V některých případech, namísto výběru vestavěného běhu PHP a jeho konfigurace, jak je popsáno výše, můžete poskytnout svůj vlastní runtime PHP. Můžete například použít stejný běh php ve webové nebo roli pracovního procesu, který používáte ve vývojovém prostředí. To usnadňuje zajištění, že aplikace nezmění chování ve vašem provozním prostředí.

### <a name="configure-a-web-role-to-use-your-own-php-runtime"></a>Konfigurace webové role pro použití vlastního runtime PHP

Chcete-li nakonfigurovat webovou roli tak, aby používala zadaný runtime PHP, postupujte takto:

1. Vytvořte projekt služby Azure a přidejte webovou roli PHP, jak je popsáno výše v tomto tématu.
2. Vytvořte `php` složku `bin` ve složce, která je v kořenovém adresáři webové role, a pak do `php` složky přidejte runtime PHP (všechny binární soubory, konfigurační soubory, podsložky atd.).
3. (NEPOVINNÉ) Pokud váš modul runtime PHP používá [ovladače Microsoft pro PHP pro SQL Server][sqlsrv drivers], budete muset nakonfigurovat webovou roli pro instalaci [nativního klienta SQL Server 2012,][sql native client] když je zřízen. Chcete-li to provést, přidejte [instalační program sqlncli.msi x64] do `bin` složky v kořenovém adresáři webové role. Spouštěcí skript popsaný v dalším kroku bude tiše spustit instalační program při zřízení role. Pokud modul runtime PHP nepoužívá ovladače Microsoft pro PHP pro SQL Server, můžete ze skriptu uvedeného v dalším kroku odebrat následující řádek ze skriptu:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
4. Definujte úlohu spuštění, která konfiguruje [Internetovou informační službu (IIS)][iis.net] tak, aby používala běhový čas PHP ke zpracování požadavků na `.php` stránky. Chcete-li to `setup_web.cmd` provést, otevřete soubor (v `bin` souboru kořenového adresáře webové role) v textovém editoru a nahraďte jeho obsah následujícím skriptem:

    ```cmd
    @ECHO ON
    cd "%~dp0"

    if "%EMULATED%"=="true" exit /b 0

    msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

    SET PHP_FULL_PATH=%~dp0php\php-cgi.exe
    SET NEW_PATH=%PATH%;%RoleRoot%\base\x86

    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%',maxInstances='12',idleTimeout='60000',activityTimeout='3600',requestTimeout='60000',instanceMaxRequests='10000',protocol='NamedPipe',flushNamedPipe='False']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PATH',value='%NEW_PATH%']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PHP_FCGI_MAX_REQUESTS',value='10000']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/handlers /+"[name='PHP',path='*.php',verb='GET,HEAD,POST',modules='FastCgiModule',scriptProcessor='%PHP_FULL_PATH%',resourceType='Either',requireAccess='Script']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /"[fullPath='%PHP_FULL_PATH%'].queueLength:50000"
    ```
5. Přidejte soubory aplikace do kořenového adresáře webové role. Bude to kořenový adresář webového serveru.
6. Publikujte aplikaci, jak je popsáno v části [Publikovat aplikaci](#publish-your-application) níže.

> [!NOTE]
> Skript `download.ps1` (ve `bin` složce kořenového adresáře webové role) lze odstranit po postupujte podle výše popsaných kroků pro použití vlastního runtime PHP.
>
>

### <a name="configure-a-worker-role-to-use-your-own-php-runtime"></a>Konfigurace role pracovníka tak, aby používala vlastní runtime PHP

Chcete-li nakonfigurovat roli pracovního procesu tak, aby používala zadaný zaběhu PHP, postupujte takto:

1. Vytvořte projekt služby Azure a přidejte roli pracovního procesu PHP, jak je popsáno výše v tomto tématu.
2. Vytvořte `php` složku v kořenovém adresáři role pracovního procesu a pak do `php` složky přidejte runtime PHP (všechny binární soubory, konfigurační soubory, podsložky atd.).
3. (NEPOVINNÉ) Pokud váš modul runtime PHP používá [ovladače Microsoft pro PHP pro SQL Server][sqlsrv drivers], budete muset nakonfigurovat roli pracovního procesu pro instalaci [nativního klienta SQL Server 2012,][sql native client] když je zřízen. Chcete-li to provést, přidejte [instalační program sqlncli.msi x64] do kořenového adresáře role pracovního procesu. Spouštěcí skript popsaný v dalším kroku bude tiše spustit instalační program při zřízení role. Pokud modul runtime PHP nepoužívá ovladače Microsoft pro PHP pro SQL Server, můžete ze skriptu uvedeného v dalším kroku odebrat následující řádek ze skriptu:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
4. Definujte úlohu při `php.exe` spuštění, která přidá spustitelný soubor do proměnné prostředí PATH role pracovního procesu při zřízení role. Chcete-li to `setup_worker.cmd` provést, otevřete soubor (v kořenovém adresáři role pracovního procesu) v textovém editoru a nahraďte jeho obsah následujícím skriptem:

    ```cmd
    @echo on

    cd "%~dp0"

    echo Granting permissions for Network Service to the web root directory...
    icacls ..\ /grant "Network Service":(OI)(CI)W
    if %ERRORLEVEL% neq 0 goto error
    echo OK

    if "%EMULATED%"=="true" exit /b 0

    msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

    setx Path "%PATH%;%~dp0php" /M

    if %ERRORLEVEL% neq 0 goto error

    echo SUCCESS
    exit /b 0

    :error

    echo FAILED
    exit /b -1
    ```
5. Přidejte soubory aplikace do kořenového adresáře role pracovního procesu.
6. Publikujte aplikaci, jak je popsáno v části [Publikovat aplikaci](#publish-your-application) níže.

## <a name="run-your-application-in-the-compute-and-storage-emulators"></a>Spuštění aplikace v emulátorech výpočetních prostředků a úložišť

Emulátory Azure poskytují místní prostředí, ve kterém můžete otestovat svou aplikaci Azure před nasazením do cloudu. Existují určité rozdíly mezi emulátory a prostředí Azure. Pokud toho pochopte lépe, [přečtěte si téma Použití emulátoru úložiště Azure pro vývoj a testování](storage/common/storage-use-emulator.md).

Všimněte si, že musíte mít PHP nainstalován místně používat výpočetní emulátor. Emulátor výpočetních prostředků použije místní instalaci PHP ke spuštění aplikace.

Chcete-li spustit projekt v emulátorech, proveďte následující příkaz z kořenového adresáře projektu:

    PS C:\MyProject> Start-AzureEmulator

Uvidíte výstup podobný tomuto:

    Creating local package...
    Starting Emulator...
    Role is running at http://127.0.0.1:81
    Started

Aplikaci spuštěnou v emulátoru můžete vidět otevřením webového prohlížeče a procházením místní adresy zobrazené ve výstupu (v`http://127.0.0.1:81` příkladu výstupu výše).

Chcete-li zastavit emulátory, proveďte tento příkaz:

    PS C:\MyProject> Stop-AzureEmulator

## <a name="publish-your-application"></a>Publikování aplikace

Chcete-li publikovat aplikaci, musíte nejprve importovat nastavení publikování pomocí rutiny [Import-AzurePublishSettingsFile.](https://docs.microsoft.com/powershell/module/servicemanagement/azure/import-azurepublishsettingsfile) Potom můžete publikovat aplikaci pomocí rutiny [Publish-AzureServiceProject.](https://docs.microsoft.com/powershell/module/servicemanagement/azure/publish-azureserviceproject) Informace o přihlášení najdete v tématu [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview).

## <a name="next-steps"></a>Další kroky

Další informace naleznete v [centru pro vývojáře PHP](https://azure.microsoft.com/develop/php/).

[install ps and emulators]: https://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[definice služby (.csdef)]: https://msdn.microsoft.com/library/windowsazure/ee758711.aspx
[konfigurace služby (.cscfg)]: https://msdn.microsoft.com/library/windowsazure/ee758710.aspx
[iis.net]: https://www.iis.net/
[sql native client]: https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation
[sqlsrv drivers]: https://php.net/sqlsrv
[instalační program sqlncli.msi x64]: https://go.microsoft.com/fwlink/?LinkID=239648
