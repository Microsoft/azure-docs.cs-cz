---
title: Vytvoření webových rolí a rolí pracovních procesů Azure pro PHP
description: Průvodce vytvářením webových a pracovních rolí PHP v cloudové službě Azure a konfigurací modulu runtime PHP.
services: ''
documentationcenter: php
author: msangapu-msft
manager: gwallace
ms.assetid: 9f7ccda0-bd96-4f7b-a7af-fb279a9e975b
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/11/2018
ms.author: msangapu
ms.openlocfilehash: 1bf0ea35c51b063a7720a1542a23a49fbcdbc557
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88892979"
---
# <a name="create-php-web-and-worker-roles"></a>Vytvoření rolí pracovního procesu a webu PHP

## <a name="overview"></a>Přehled

V této příručce se dozvíte, jak vytvořit webové role nebo role pracovních procesů PHP ve vývojovém prostředí Windows, zvolit konkrétní verzi PHP z dostupných verzí, které jsou k dispozici, změnit konfiguraci PHP, povolit rozšíření a nakonec nasadit do Azure. Popisuje také, jak nakonfigurovat webovou roli nebo roli pracovního procesu, aby používala modul runtime PHP (s vlastní konfigurací a rozšířeními), kterou zadáte.

Azure nabízí tři výpočetní modely pro spouštění aplikací: Azure App Service, Azure Virtual Machines a Azure Cloud Services. Všechny tři modely podporují PHP. Cloud Services, která zahrnuje webové a pracovní role, poskytuje *platformu jako službu (PaaS)*. Webová role v rámci cloudové služby poskytuje vyhrazený webový server Internetová informační služba (IIS) pro hostování front-endové webové aplikace. Role pracovního procesu může spouštět asynchronní, dlouhotrvající nebo trvalé úlohy, které jsou nezávislé na interakci nebo vstupu uživatele.

Další informace o těchto možnostech najdete v tématu [možnosti hostování služby COMPUTE poskytované Azure](cloud-services/cloud-services-choose-me.md).

## <a name="download-the-azure-sdk-for-php"></a>Stáhnout sadu Azure SDK pro PHP

[Sada Azure SDK pro php](https://github.com/Azure/azure-sdk-for-php) se skládá z několika součástí. Tento článek bude používat dvě z nich: Azure PowerShell a emulátory Azure. Tyto dvě součásti lze nainstalovat prostřednictvím Instalace webové platformy Microsoft. Další informace najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/).

## <a name="create-a-cloud-services-project"></a>Vytvoření projektu Cloud Services

Prvním krokem při vytváření webové role nebo role pracovního procesu PHP je vytvoření projektu služby Azure. projekt služby Azure slouží jako logický kontejner pro webové a pracovní role a obsahuje soubory [definice služby (. csdef)] a [Konfigurace služby (. cscfg)] .

Pokud chcete vytvořit nový projekt služby Azure, spusťte Azure PowerShell jako správce a spusťte následující příkaz:

```powershell
PS C:\>New-AzureServiceProject myProject
```

Tento příkaz vytvoří nový adresář ( `myProject` ), do kterého můžete přidat webové a pracovní role.

## <a name="add-php-web-or-worker-roles"></a>Přidat webové role nebo role pracovních procesů PHP

Chcete-li přidat webovou roli PHP do projektu, spusťte následující příkaz v kořenovém adresáři projektu:

```powershell
PS C:\myProject> Add-AzurePHPWebRole roleName
```

V případě role pracovního procesu použijte tento příkaz:

```powershell
PS C:\myProject> Add-AzurePHPWorkerRole roleName
```

> [!NOTE]
> `roleName`Parametr je nepovinný. Je-li tento parametr vynechán, bude automaticky vygenerován název role. První vytvořená webová role bude `WebRole1` , druhá bude atd `WebRole2` . První vytvořená role pracovního procesu bude `WorkerRole1` , druhá bude atd `WorkerRole2` .
>
>

## <a name="use-your-own-php-runtime"></a>Použití vlastního modulu runtime PHP

V některých případech místo výběru integrovaného modulu runtime PHP a jeho konfigurace, jak je popsáno výše, můžete chtít poskytnout vlastní modul runtime PHP. Můžete například použít stejný modul runtime PHP v rámci webové role nebo role pracovního procesu, který používáte ve svém vývojovém prostředí. Díky tomu je snazší zajistit, aby se v produkčním prostředí aplikace neměnila chování.

### <a name="configure-a-web-role-to-use-your-own-php-runtime"></a>Konfigurace webové role pro použití vlastního modulu runtime PHP

Pokud chcete nakonfigurovat webovou roli, aby používala modul runtime PHP, který zadáte, postupujte takto:

1. Vytvořte projekt služby Azure a přidejte webovou roli PHP, jak je popsáno dříve v tomto tématu.
2. Vytvořte `php` složku ve `bin` složce, která se nachází v kořenovém adresáři webové role, a přidejte do složky modul runtime php (všechny binární soubory, konfigurační soubory, podsložky atd.) `php` .
3. VOLITELNÉ Pokud modul runtime PHP používá [ovladače Microsoft pro PHP pro SQL Server][sqlsrv drivers], budete muset nakonfigurovat webovou roli tak, aby při zřizování nainstalovala [SQL Server Native Client 2012][sql native client] . Chcete-li to provést, přidejte [ instalační programsqlncli.msi x64] do `bin` složky v kořenovém adresáři webové role. Spouštěcí skript popsaný v dalším kroku bude při zřizování role tiše spustit instalační program. Pokud modul runtime PHP nepoužívá ovladače Microsoft pro PHP pro SQL Server, můžete z skriptu uvedeného v dalším kroku odebrat následující řádek:

   ```console
   msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
   ```

4. Definujte úlohu po spuštění, která konfiguruje [Internetová informační služba (IIS)][iis.net] , aby používala modul runtime php ke zpracování požadavků na `.php` stránky. Provedete to tak, že `setup_web.cmd` v textovém editoru otevřete soubor (v `bin` souboru kořenového adresáře webové role) a nahradíte jeho obsah následujícím skriptem:

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
5. Přidejte soubory aplikace do kořenového adresáře webové role. To bude kořenový adresář webového serveru.
6. Publikujte aplikaci, jak je popsáno v části [publikování aplikace](#publish-your-application) níže.

> [!NOTE]
> `download.ps1`Skript (ve `bin` složce kořenového adresáře webové role) lze odstranit po provedení výše uvedeného postupu pro použití vlastního modulu runtime php.
>
>

### <a name="configure-a-worker-role-to-use-your-own-php-runtime"></a>Konfigurace role pracovního procesu pro použití vlastního modulu runtime PHP

Chcete-li nakonfigurovat roli pracovního procesu tak, aby používala modul runtime PHP, který zadáte, postupujte takto:

1. Vytvořte projekt služby Azure a přidejte roli pracovního procesu PHP, jak je popsáno dříve v tomto tématu.
2. `php`V kořenovém adresáři role pracovního procesu vytvořte složku a přidejte do ní modul runtime php (všechny binární soubory, konfigurační soubory, podsložky atd.) `php` .
3. VOLITELNÉ Pokud modul runtime PHP používá [ovladače Microsoft pro PHP pro SQL Server][sqlsrv drivers], budete muset nakonfigurovat roli pracovního procesu tak, aby při zřizování nainstalovala [SQL Server Native Client 2012][sql native client] . Pokud to chcete provést, přidejte [ instalační programsqlncli.msi x64] do kořenového adresáře role pracovního procesu. Spouštěcí skript popsaný v dalším kroku bude při zřizování role tiše spustit instalační program. Pokud modul runtime PHP nepoužívá ovladače Microsoft pro PHP pro SQL Server, můžete z skriptu uvedeného v dalším kroku odebrat následující řádek:

   ```console
   msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
   ```

4. V případě zřízení role definujte úlohu po spuštění, která přidá `php.exe` spustitelný soubor do proměnné prostředí PATH role pracovního procesu. Provedete to tak, že `setup_worker.cmd` v textovém editoru otevřete soubor (v kořenovém adresáři role pracovního procesu) a nahradíte jeho obsah následujícím skriptem:

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
5. Přidejte soubory aplikace do kořenového adresáře vaší role pracovního procesu.
6. Publikujte aplikaci, jak je popsáno v části [publikování aplikace](#publish-your-application) níže.

## <a name="run-your-application-in-the-compute-and-storage-emulators"></a>Spuštění aplikace v emulátorech výpočtů a úložiště

Emulátory Azure poskytují místní prostředí, ve kterém můžete otestovat aplikaci Azure před tím, než ji nasadíte do cloudu. Mezi emulátory a prostředím Azure dochází k nějakým rozdílům. Pro lepší pochopení najdete informace v tématu [použití emulátoru Azure Storage pro vývoj a testování](storage/common/storage-use-emulator.md).

Všimněte si, že musíte mít nainstalovaný PHP místně pro použití emulátoru Compute. Emulátor služby COMPUTE bude používat vaši místní instalaci PHP ke spuštění vaší aplikace.

Chcete-li spustit projekt v emulátorech, spusťte následující příkaz z kořenového adresáře vašeho projektu:

```powershell
PS C:\MyProject> Start-AzureEmulator
```

Zobrazí se výstup podobný tomuto:

```output
Creating local package...
Starting Emulator...
Role is running at http://127.0.0.1:81
Started
```

Aplikaci spuštěnou v emulátoru můžete zobrazit tak, že otevřete webový prohlížeč a přejdete na místní adresu zobrazenou ve výstupu ( `http://127.0.0.1:81` v příkladu výstup výše).

Chcete-li zastavit emulátory, spusťte tento příkaz:

```powershell
PS C:\MyProject> Stop-AzureEmulator
```

## <a name="publish-your-application"></a>Publikování aplikace

Chcete-li publikovat aplikaci, je nutné nejprve importovat nastavení publikování pomocí rutiny [Import-AzurePublishSettingsFile](/powershell/module/servicemanagement/azure.service/import-azurepublishsettingsfile) . Potom můžete aplikaci publikovat pomocí rutiny [Publish-AzureServiceProject](/powershell/module/servicemanagement/azure.service/publish-azureserviceproject) . Informace o přihlášení najdete v tématu [instalace a konfigurace Azure PowerShell](/powershell/azure/).

## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu [středisko pro vývojáře PHP](https://azure.microsoft.com/develop/php/).

[install ps and emulators]: https://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[definice služby (. csdef)]: https://msdn.microsoft.com/library/windowsazure/ee758711.aspx
[Konfigurace služby (. cscfg)]: https://msdn.microsoft.com/library/windowsazure/ee758710.aspx
[iis.net]: https://www.iis.net/
[sql native client]: https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation
[sqlsrv drivers]: https://php.net/sqlsrv
[ Instalační programsqlncli.msi x64]: https://go.microsoft.com/fwlink/?LinkID=239648
