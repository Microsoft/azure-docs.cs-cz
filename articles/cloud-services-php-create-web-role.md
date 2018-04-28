---
title: Vytvoření Azure webových a pracovních rolí pro jazyk PHP
description: Pokyny pro vytváření webových a pracovních rolí PHP v cloudové službě Azure a konfigurace modulu PHP runtime.
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
ms.openlocfilehash: b9f350870dde71666d269aaae9cb7c14aaac5aad
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="how-to-create-php-web-and-worker-roles"></a>Postup vytvoření PHP webových a pracovních rolí
## <a name="overview"></a>Přehled
Tento průvodce vám ukáže, jak vytvořit role web nebo worker PHP ve vývojovém prostředí Windows, vybrat konkrétní verzi PHP z "integrovaných" verzí, které jsou k dispozici, měnit nastavení PHP, povolit rozšíření a nakonec nasazení do Azure. Také popisuje, jak nakonfigurujete roli web nebo worker použít modul runtime PHP (s vlastní konfigurace a rozšíření), který zadáte.

## <a name="what-are-php-web-and-worker-roles"></a>Co jsou webové a pracovní role PHP?
Azure nabízí tři výpočetní modely pro provozování aplikací: Azure App Service, virtuální počítače Azure a cloudových služeb Azure. Všechny tři modely podporují PHP. Cloudové služby, včetně webových a pracovních rolí, poskytuje *platforma jako služba (PaaS)*. Webové role v rámci cloudové služby poskytuje vyhrazený webový server Internetové informační služby (IIS) na hostiteli front-endové webové aplikace. Asynchronní, dlouhotrvající nebo trvalé úlohy, které jsou nezávislé na vstupu nebo interakci uživatelů můžete spustit roli pracovního procesu.

Další informace o těchto možnostech najdete v tématu [výpočetní hostování možnosti poskytované Azure](cloud-services/cloud-services-choose-me.md).

## <a name="download-the-azure-sdk-for-php"></a>Stažení sady Azure SDK pro PHP
[Azure SDK pro jazyk PHP] se skládá z několika součástí. Tento článek se bude používat dvě z nich: Azure PowerShell a Azure emulátorů. Tyto dvě součásti lze nainstalovat pomocí instalačního programu webové platformy Microsoft. Další informace najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview).

## <a name="create-a-cloud-services-project"></a>Vytvoření projektu cloudové služby
Prvním krokem při vytvoření role web nebo worker PHP je vytvoření projektu Azure Service. projekt služby Azure slouží jako kontejner logické pro webové a pracovní role, která obsahuje projektu [služby definice (.csdef)] a [konfigurace služby (.cscfg)] soubory.

Pokud chcete vytvořit nový projekt služeb Azure, spusťte prostředí Azure PowerShell jako správce a spusťte následující příkaz:

    PS C:\>New-AzureServiceProject myProject

Tento příkaz vytvoří nový adresář (`myProject`) do kterého můžete přidat webové a pracovní role.

## <a name="add-php-web-or-worker-roles"></a>Přidání rolí web nebo worker PHP
Pokud chcete přidat do projektu webové role PHP, spusťte následující příkaz v kořenovém adresáři projektu:

    PS C:\myProject> Add-AzurePHPWebRole roleName

Pro roli pracovního procesu použijte tento příkaz:

    PS C:\myProject> Add-AzurePHPWorkerRole roleName

> [!NOTE]
> `roleName` Parametr je nepovinný. Je-li vynechán, budou automaticky generovány název role. Bude vytvořen první webovou roli `WebRole1`, druhý bude `WebRole2`a tak dále. První role pracovního procesu vytvoření bude `WorkerRole1`, druhý bude `WorkerRole2`a tak dále.
>
>

## <a name="specify-the-built-in-php-version"></a>Zadejte předdefinované verzi PHP
Když přidáte roli web nebo worker PHP do projektu, projektu konfigurační soubory jsou upravit tak, aby PHP bude nainstalována na každou instanci vaší aplikace web nebo pracovní proces, při nasazení. Pokud chcete zobrazit verzi PHP, která bude nainstalována ve výchozím nastavení, spusťte následující příkaz:

    PS C:\myProject> Get-AzureServiceProjectRoleRuntime

Výstup z výše uvedeného příkazu bude vypadat podobně jako co jsou uvedeny níže. V tomto příkladu `IsDefault` je příznak nastaven na `true` pro jazyk PHP 5.3.17, která udává, zda je výchozí verze PHP nainstalován.

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

Modul runtime PHP můžete nastavit na verze PHP, které jsou uvedené. Chcete-li například nastavit verzi PHP (pro roli s názvem `roleName`) k 5.4.0, použijte následující příkaz:

    PS C:\myProject> Set-AzureServiceProjectRole roleName php 5.4.0

> [!NOTE]
> K dispozici verze PHP může v budoucnu změnit.
>
>

## <a name="customize-the-built-in-php-runtime"></a>Přizpůsobení předdefinované modulu PHP runtime
Máte plnou kontrolu nad konfigurace modulu PHP runtime, který je nainstalován, pokud budete postupovat podle kroků výše, včetně změny `php.ini` nastavení a povolení rozšíření.

Chcete-li přizpůsobit předdefinované runtime PHP, postupujte takto:

1. Přidejte novou složku, s názvem `php`do `bin` adresář webové role. Pro roli pracovního procesu přidejte ji do role kořenový adresář.
2. V `php` složce vytvořte jinou složku s názvem `ext`. Uveďte všechny `.dll` soubory rozšíření (například `php_mongo.dll`), které chcete povolit v této složce.
3. Přidat `php.ini` do souboru `php` složky. Povolit všechny vlastní rozšíření a nastavte všechny direktivy PHP v tomto souboru. Například, pokud chcete zapnout `display_errors` na a povolte `php_mongo.dll` rozšíření, obsah vaší `php.ini` soubor vypadat takto:

        display_errors=On
        extension=php_mongo.dll

> [!NOTE]
> Všechna nastavení, která v nenastavíte explicitně `php.ini` souboru zadejte bude automaticky nastavit výchozí hodnoty. Ale mějte, můžete přidat úplná `php.ini` souboru.
>
>

## <a name="use-your-own-php-runtime"></a>Použití vlastního modulu PHP runtime
V některých případech, namísto výběru předdefinované modulu PHP runtime a nakonfigurovat jej jak bylo popsáno výše můžete zadat vlastní modul runtime PHP. Například můžete použít stejné modulu PHP runtime v roli web nebo pracovní proces, který používáte ve vašem vývojovém prostředí. To usnadňuje Ujistěte se, že aplikace nedojde ke změně chování v provozním prostředí.

### <a name="configure-a-web-role-to-use-your-own-php-runtime"></a>Konfigurace webové roli, kterou chcete použít vlastní modulu PHP runtime
Pokud chcete konfigurovat webovou roli na-li použít modul runtime PHP, který zadáte, postupujte takto:

1. Vytvoření projektu Azure Service a přidejte webovou roli PHP, jak je popsáno dříve v tomto tématu.
2. Vytvoření `php` složky v `bin` složka, která je v kořenovém adresáři webovou roli a potom přidat do vašeho modulu PHP runtime (všechny binární soubory, konfiguračních souborů, podsložky atd.) `php` složky.
3. (VOLITELNÉ) Pokud vaše modulu PHP runtime používá [Drivers společnosti Microsoft pro jazyk PHP pro SQL Server][sqlsrv drivers], budete muset nakonfigurovat vaši webovou roli nainstalovat [SQL Server Native Client 2012] [ sql native client] když je zřízený. Chcete-li to provést, přidejte [sqlncli.msi x64 instalační program] k `bin` složku v kořenovém adresáři webovou roli. Spuštění skriptu popsané v dalším kroku bude bezobslužně spusťte instalační program při zřízení roli. Pokud vaše modulu PHP runtime nepoužívá Drivers společnosti Microsoft pro jazyk PHP pro SQL Server, můžete odebrat ze skriptu vidět v dalším kroku následující řádek:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
4. Úloha spuštění, který konfiguruje definovat [Internetové informační služby (IIS)] [ iis.net] používat vaše modulu PHP runtime pro zpracování požadavků pro `.php` stránky. Chcete-li to provést, otevřete `setup_web.cmd` souboru (v `bin` souboru webovou roli kořenový adresář) v textovém editoru a nahraďte jeho obsah následující skript:

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
5. Přidání souborů aplikací na vaši webovou roli kořenový adresář. To bude webový server kořenový adresář.
6. Publikování aplikace, jak je popsáno v [publikování aplikace](#publish-your-application) části níže.

> [!NOTE]
> `download.ps1` Skriptu (v `bin` složky webovou roli kořenový adresář) můžete odstranit po podle kroků popsaných výše pro použití vlastního runtime PHP.
>
>

### <a name="configure-a-worker-role-to-use-your-own-php-runtime"></a>Konfigurace role pracovních procesů na využití vlastního modulu runtime PHP
Pokud chcete konfigurovat role pracovních procesů na využití modulu runtime PHP, které poskytujete, postupujte takto:

1. Vytvoření projektu Azure Service a přidejte roli pracovního procesu PHP, jak je popsáno dříve v tomto tématu.
2. Vytvoření `php` složku v kořenovém adresáři role pracovního procesu a pak přidejte do vašeho modulu PHP runtime (všechny binární soubory, konfiguračních souborů, podsložky atd.) `php` složky.
3. (VOLITELNÉ) Pokud vaše modulu PHP runtime používá [Drivers společnosti Microsoft pro jazyk PHP pro SQL Server][sqlsrv drivers], budete muset konfigurovat své role pracovního procesu instalace [SQL Server Native Client 2012] [ sql native client] když je zřízený. Chcete-li to provést, přidejte [sqlncli.msi x64 instalační program] pro roli pracovního procesu kořenový adresář. Spuštění skriptu popsané v dalším kroku bude bezobslužně spusťte instalační program při zřízení roli. Pokud vaše modulu PHP runtime nepoužívá Drivers společnosti Microsoft pro jazyk PHP pro SQL Server, můžete odebrat ze skriptu vidět v dalším kroku následující řádek:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
4. Definování úloha spuštění, který přidává vaší `php.exe` spustitelný soubor do proměnné prostředí PATH role pracovního procesu při zřízení roli. Chcete-li to provést, otevřete `setup_worker.cmd` (v roli pracovního procesu kořenový adresář) soubor v textovém editoru a nahraďte jeho obsah následující skript:

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
5. Přidáte soubory aplikací do kořenového adresáře role pracovního procesu.
6. Publikování aplikace, jak je popsáno v [publikování aplikace](#publish-your-application) části níže.

## <a name="run-your-application-in-the-compute-and-storage-emulators"></a>Spusťte aplikaci v emulátorů výpočetního prostředí a úložiště
Azure emulátorů, zadejte do místního prostředí, ve kterém můžete testovat aplikaci Azure předtím, než můžete nasadit do cloudu. Existují určité rozdíly mezi emulátorů a prostředí Azure. To lépe pochopit, najdete v části [pomocí emulátoru úložiště Azure pro vývoj a testování](storage/common/storage-use-emulator.md).

Všimněte si, že musí mít nainstalované místně pro použití emulátoru služby výpočty v PHP. Emulátoru služby výpočty v použije místní instalace PHP spusťte aplikaci.

Pokud chcete spustit projekt v emulátorů, spusťte následující příkaz z kořenového adresáře projektu na:

    PS C:\MyProject> Start-AzureEmulator

Zobrazí se výstup podobný tomuto:

    Creating local package...
    Starting Emulator...
    Role is running at http://127.0.0.1:81
    Started

Uvidí vaši aplikaci běžící v emulátoru kliknutím otevřete webový prohlížeč a přejděte na adresu místního zobrazené ve výstupu (`http://127.0.0.1:81` ve výstupu v příkladu výše).

Chcete-li zastavit emulátorů, spusťte tento příkaz:

    PS C:\MyProject> Stop-AzureEmulator

## <a name="publish-your-application"></a>Publikování aplikace
Pro publikování aplikace, musíte nejdříve naimportovat vaše nastavení publikování pomocí [Import AzurePublishSettingsFile](https://msdn.microsoft.com/library/azure/dn790370.aspx) rutiny. Potom můžete publikovat aplikace pomocí [Publish-AzureServiceProject](https://msdn.microsoft.com/library/azure/dn495166.aspx) rutiny. Informace o přihlašování najdete v tématu [postup instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).

## <a name="next-steps"></a>Další postup
Další informace najdete v tématu [středisku pro vývojáře PHP](/develop/php/).

[Azure SDK pro jazyk PHP]: /develop/php/common-tasks/download-php-sdk/
[install ps and emulators]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[služby definice (.csdef)]: http://msdn.microsoft.com/library/windowsazure/ee758711.aspx
[konfigurace služby (.cscfg)]: http://msdn.microsoft.com/library/windowsazure/ee758710.aspx
[iis.net]: http://www.iis.net/
[sql native client]: http://msdn.microsoft.com/sqlserver/aa937733.aspx
[sqlsrv drivers]: http://php.net/sqlsrv
[sqlncli.msi x64 instalační program]: http://go.microsoft.com/fwlink/?LinkID=239648
