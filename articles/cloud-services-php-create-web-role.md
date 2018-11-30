---
title: Vytvoření webové a pracovní role Azure pro jazyk PHP
description: Průvodce můžete vytvářet webové a pracovní role PHP ve službě Azure cloud service a konfigurace modulu runtime PHP.
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
ms.openlocfilehash: 83834104dd73e4381947903196ad35c3497b64a1
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52425672"
---
# <a name="create-php-web-and-worker-roles"></a>Vytvoření rolí pracovního procesu a webu PHP

## <a name="overview"></a>Přehled

Tato příručka obsahuje pokyny k vytvoření webové nebo pracovní role PHP v prostředí pro vývoj Windows, vybrat konkrétní verzi PHP z "integrované" verze k dispozici, změňte konfiguraci PHP, povolit rozšíření a nakonec nasadit do Azure. Také popisuje, jak nakonfigurovat webové nebo pracovní role, použít modul runtime PHP (s vlastní konfigurace a rozšíření), který zadáte.

Azure nabízí tři výpočetní modely spouštění aplikací: Azure App Service, Azure Virtual Machines a Azure Cloud Services. Všechny tři modely podporují PHP. Cloud Services, které zahrnuje webové a pracovní role, poskytuje *platforma jako služba (PaaS)*. Webová role v rámci cloudové služby poskytuje vyhrazený webový server Internetové informační služby (IIS) pro hostování front-endových webových aplikací. Role pracovního procesu dokážou spouštět asynchronní, dlouhotrvající nebo trvalé úlohy, které jsou nezávislé na vstupu nebo interakci uživatelů.

Další informace o těchto možnostech najdete v tématu [poskytovaný platformou Azure možností hostování výpočetních řešení](cloud-services/cloud-services-choose-me.md).

## <a name="download-the-azure-sdk-for-php"></a>Stažení sady Azure SDK pro PHP

[Sady Azure SDK pro jazyk PHP](php-download-sdk.md) se skládá z několika součástí. Tento článek bude používat dva z nich: prostředí Azure PowerShell a emulátory Azure. Tyto dvě složky lze nainstalovat pomocí instalačního programu webové platformy. Další informace najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview).

## <a name="create-a-cloud-services-project"></a>Vytvořte projekt cloudové služby

Prvním krokem při vytváření webové nebo pracovní role PHP je vytvoření projektu služby Azure. Projekt Azure Service slouží jako logický kontejner pro webové a pracovní role, a obsahuje projektu [definičních (.csdef) služby] a [konfigurace služby (.cscfg)] soubory.

Chcete-li vytvořit nový projekt služby Azure, spustit prostředí Azure PowerShell jako správce a spusťte následující příkaz:

    PS C:\>New-AzureServiceProject myProject

Tento příkaz vytvoří nový adresář (`myProject`) ke kterému můžete přidat webové a pracovní role.

## <a name="add-php-web-or-worker-roles"></a>Přidání webové nebo pracovní role PHP

Přidat do projektu webové role PHP, spusťte následující příkaz v kořenovém adresáři projektu:

    PS C:\myProject> Add-AzurePHPWebRole roleName

Pro roli pracovního procesu použijte tento příkaz:

    PS C:\myProject> Add-AzurePHPWorkerRole roleName

> [!NOTE]
> `roleName` Parametr je nepovinný. Pokud je vynechán, budou automaticky generovány název role. Vytvoření první webové role bude `WebRole1`, druhá bude `WebRole2`, a tak dále. První role pracovního procesu vytvoří bude `WorkerRole1`, druhá bude `WorkerRole2`, a tak dále.
>
>

## <a name="specify-the-built-in-php-version"></a>Zadejte integrované verze PHP

Když přidáte do projektu webové nebo pracovní role PHP, konfigurační soubory projektu jsou upraveny, aby PHP se nainstaluje na každý instance webové nebo pracovní aplikace při jejím nasazení. Pokud chcete zobrazit verzi PHP, který se nainstaluje ve výchozím nastavení, spusťte následující příkaz:

    PS C:\myProject> Get-AzureServiceProjectRoleRuntime

Výstup z výše uvedeného příkazu bude vypadat podobně jako na co je uveden níže. V tomto příkladu `IsDefault` příznak je nastaven na `true` pro jazyk PHP 5.3.17, označující, že bude výchozí verzi PHP nainstalován.

```
Runtime Version     PackageUri                      IsDefault
------- -------     ----------                      ---------
Node 0.6.17         http://nodertncu.blob.core...   False
Node 0.6.20         http://nodertncu.blob.core...   True
Node 0.8.4          http://nodertncu.blob.core...   False
IISNode 0.1.21      http://nodertncu.blob.core...   True
Cache 1.8.0         http://nodertncu.blob.core...   True
PHP 5.3.17          http://nodertncu.blob.core...   True
PHP 5.4.0           http://nodertncu.blob.core...   False
```

Verze modulu runtime PHP můžete nastavit na některou z verzí jazyka PHP, které jsou uvedeny. Chcete-li například nastavit verzi PHP (pro roli s názvem `roleName`) k 5.4.0, použijte následující příkaz:

    PS C:\myProject> Set-AzureServiceProjectRole roleName php 5.4.0

> [!NOTE]
> Dostupné verze PHP může v budoucnu změnit.
>
>

## <a name="customize-the-built-in-php-runtime"></a>Přizpůsobení předdefinovaných modulu runtime PHP

Získejte úplnou kontrolu nad konfigurací modulu PHP runtime, který je nainstalován, pokud budete postupovat podle výše uvedené kroky, včetně změny `php.ini` nastavení a povolení rozšíření.

Přizpůsobení předdefinovaných modulu runtime PHP, postupujte podle těchto kroků:

1. Přidat novou složku s názvem `php`, možnosti `bin` adresáři webové role. Pro roli pracovního procesu přidejte ho do kořenového adresáře této role.
2. V `php` složku, vytvořte jinou složku s názvem `ext`. Vložit některé `.dll` soubory rozšíření (například `php_mongo.dll`), který chcete povolit v této složce.
3. Přidat `php.ini` do souboru `php` složky. Povolte všechny vlastní rozšíření a nastavte jakékoli direktivy PHP v tomto souboru. Například, pokud chcete zapnout `display_errors` na a povolte `php_mongo.dll` rozšíření, obsah vaší `php.ini` soubor by měl vypadat takto:

        display_errors=On
        extension=php_mongo.dll

> [!NOTE]
> Všechna nastavení, která v nenastavíte explicitně `php.ini` souboru zadejte bude automaticky nastavit na výchozí hodnoty. Však mít na paměti, že můžete přidat kompletní `php.ini` souboru.
>
>

## <a name="use-your-own-php-runtime"></a>Použít vlastní PHP runtime

V některých případech se místo výběru předdefinovaných PHP runtime a konfigurace ji jak je popsáno výše můžete chtít poskytnout vlastní PHP runtime. Například můžete použít stejné modulu runtime PHP ve webové nebo pracovní role, který používáte ve svém vývojovém prostředí. To umožňuje jednodušší zajistit, že aplikace nedojde ke změně chování v produkčním prostředí.

### <a name="configure-a-web-role-to-use-your-own-php-runtime"></a>Konfigurace webové role, použít vlastní PHP runtime

Pokud chcete nakonfigurovat webové role pro použití PHP runtime, který zadáte, postupujte takto:

1. Vytvoření projektu Azure Service a přidat webové role PHP, jak je popsáno výše v tomto tématu.
2. Vytvoření `php` složky `bin` složku, která je v kořenovém adresáři webové role a potom přidat do vašeho prostředí runtime PHP (všechny binární soubory, konfiguračních souborů, podsložky atd.) `php` složky.
3. (VOLITELNÉ) Pokud se používá PHP runtime [Drivers společnosti Microsoft pro jazyk PHP pro SQL Server][sqlsrv drivers], budete muset nakonfigurovat vaši webovou roli nainstalovat [SQL Server Native Client 2012] [ sql native client] při zřízení. Chcete-li to provést, přidejte [instalační program sqlncli.msi x64] k `bin` složku v kořenovém adresáři webové role. Spouštěcí skript je popsáno v dalším kroku se bez upozornění spusťte instalační program při zřízení roli. Pokud prostředí PHP runtime nepoužívá Drivers společnosti Microsoft pro jazyk PHP pro SQL Server, můžete odebrat následující řádek ze skriptu je znázorněno v následujícím kroku:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
4. Definujte úlohy po spuštění, který konfiguruje [Internetové informační služby (IIS)] [ iis.net] zpracovávat požadavky pro použití PHP runtime `.php` stránky. Chcete-li to provést, otevřete `setup_web.cmd` souboru (v `bin` soubor kořenový adresář webové role) v textovém editoru a jeho obsah nahraďte následující skript:

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
5. Přidáte soubory aplikace do kořenového adresáře webové role. Bude jím kořenový adresář webového serveru.
6. Publikování aplikace, jak je popsáno v [aplikace můžete publikovat](#publish-your-application) níže v části.

> [!NOTE]
> `download.ps1` Skriptu (v `bin` složky kořenový adresář webové role) je možné odstranit po postupujte podle kroků popsaných výše pro použití vlastní PHP runtime.
>
>

### <a name="configure-a-worker-role-to-use-your-own-php-runtime"></a>Konfigurovat roli pracovního procesu, který chcete použít vlastní PHP runtime

Konfigurace role pracovního procesu použití PHP runtime, který zadáte, postupujte takto:

1. Vytvoření projektu Azure Service a přidat roli pracovního procesu PHP, jak je popsáno výše v tomto tématu.
2. Vytvoření `php` složku v kořenovém adresáři role pracovního procesu a potom přidat do vašeho prostředí runtime PHP (všechny binární soubory, konfiguračních souborů, podsložky atd.) `php` složky.
3. (VOLITELNÉ) Pokud se používá PHP runtime [Drivers společnosti Microsoft pro jazyk PHP pro SQL Server][sqlsrv drivers], budete muset konfigurovat své role pracovního procesu instalace [SQL Server Native Client 2012] [ sql native client] při zřízení. Chcete-li to provést, přidejte [instalační program sqlncli.msi x64] do kořenového adresáře role pracovního procesu. Spouštěcí skript je popsáno v dalším kroku se bez upozornění spusťte instalační program při zřízení roli. Pokud prostředí PHP runtime nepoužívá Drivers společnosti Microsoft pro jazyk PHP pro SQL Server, můžete odebrat následující řádek ze skriptu je znázorněno v následujícím kroku:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
4. Definujte úlohy po spuštění, která přidá vaši `php.exe` spustitelný soubor do proměnné prostředí PATH role pracovního procesu při zřízení roli. Chcete-li to provést, otevřete `setup_worker.cmd` souboru (v kořenovém adresáři role pracovního procesu) v textovém editoru a jeho obsah nahraďte následující skript:

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
5. Přidáte soubory aplikace do kořenového adresáře role pracovního procesu.
6. Publikování aplikace, jak je popsáno v [aplikace můžete publikovat](#publish-your-application) níže v části.

## <a name="run-your-application-in-the-compute-and-storage-emulators"></a>Spusťte aplikaci na výpočetní výkon a úložiště emulátorů

Emulátory Azure poskytují místní prostředí, ve kterém můžete testovat vaše aplikace Azure před jejím nasazením do cloudu. Existuje několik rozdílů mezi emulátory a prostředí Azure. Chcete-li lépe pochopit, přečtěte si téma [použití emulátoru úložiště Azure pro vývoj a testování](storage/common/storage-use-emulator.md).

Všimněte si, že musíte mít nainstalovaný místně na emulátor služby výpočty pomocí PHP. Emulátor služby výpočty použije místní instalace PHP ke spuštění aplikace.

Spustit projekt v emulátorů, spusťte následující příkaz z kořenového adresáře projektu:

    PS C:\MyProject> Start-AzureEmulator

Zobrazí se výstup podobný tomuto:

    Creating local package...
    Starting Emulator...
    Role is running at http://127.0.0.1:81
    Started

Zobrazí se vaše aplikace běžící v emulátoru, že otevřete webový prohlížeč a přechodu na místní adrese uvedené ve výstupu (`http://127.0.0.1:81` na výše uvedeném příkladu výstupu).

Pokud chcete zastavit emulátorů, spusťte tento příkaz:

    PS C:\MyProject> Stop-AzureEmulator

## <a name="publish-your-application"></a>Publikování aplikace

Můžete publikovat svoji aplikaci, je nutné nejprve importovat vaše nastavení publikování pomocí [Import AzurePublishSettingsFile](https://docs.microsoft.com/powershell/module/servicemanagement/azure/import-azurepublishsettingsfile) rutiny. Pak můžete publikovat aplikaci s použitím [Publish-AzureServiceProject](https://docs.microsoft.com/powershell/module/servicemanagement/azure/publish-azureserviceproject) rutiny. Informace o přihlašování najdete v tématu [instalace a konfigurace Azure Powershellu](/powershell/azure/overview).

## <a name="next-steps"></a>Další postup

Další informace najdete v tématu [středisko pro vývojáře PHP](https://azure.microsoft.com/develop/php/).

[install ps and emulators]: https://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[definičních (.csdef) služby]: https://msdn.microsoft.com/library/windowsazure/ee758711.aspx
[konfigurace služby (.cscfg)]: https://msdn.microsoft.com/library/windowsazure/ee758710.aspx
[iis.net]: https://www.iis.net/
[sql native client]: https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation
[sqlsrv drivers]: https://php.net/sqlsrv
[instalační program sqlncli.msi x64]: https://go.microsoft.com/fwlink/?LinkID=239648
