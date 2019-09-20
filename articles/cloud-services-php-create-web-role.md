---
title: Vytvoření webových rolí a rolí pracovních procesů Azure pro PHP
description: Průvodce vytvářením webových a pracovních rolí PHP v cloudové službě Azure a konfigurací modulu runtime PHP.
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
ms.openlocfilehash: 82bb5f153a2c70d3b26f295925f8e48693bc49b9
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2019
ms.locfileid: "71146870"
---
# <a name="create-php-web-and-worker-roles"></a>Vytvoření rolí pracovního procesu a webu PHP

## <a name="overview"></a>Přehled

V této příručce se dozvíte, jak vytvořit webové role nebo role pracovních procesů PHP ve vývojovém prostředí Windows, zvolit konkrétní verzi PHP z dostupných verzí, které jsou k dispozici, změnit konfiguraci PHP, povolit rozšíření a nakonec nasadit do Azure. Popisuje také, jak nakonfigurovat webovou roli nebo roli pracovního procesu, aby používala modul runtime PHP (s vlastní konfigurací a rozšířeními), kterou zadáte.

V rámci systému Azure jsou dostupné tři výpočetní modely ke spouštění aplikací: Azure App Service, Azure Virtual Machines a Azure Cloud Services. Všechny tři modely podporují PHP. Cloud Services, která zahrnuje webové a pracovní role, poskytuje *platformu jako službu (PaaS)* . Webová role v rámci cloudové služby poskytuje vyhrazený webový server Internetová informační služba (IIS) pro hostování front-endové webové aplikace. Role pracovního procesu může spouštět asynchronní, dlouhotrvající nebo trvalé úlohy, které jsou nezávislé na interakci nebo vstupu uživatele.

Další informace o těchto možnostech najdete v tématu [možnosti hostování služby COMPUTE poskytované Azure](cloud-services/cloud-services-choose-me.md).

## <a name="download-the-azure-sdk-for-php"></a>Stažení sady Azure SDK pro PHP

[Sada Azure SDK pro php](https://github.com/Azure/azure-sdk-for-php) se skládá z několika součástí. Tento článek bude používat dvě z nich: Azure PowerShell a emulátory Azure. Tyto dvě součásti lze nainstalovat prostřednictvím Instalace webové platformy Microsoft. Další informace najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview).

## <a name="create-a-cloud-services-project"></a>Vytvoření projektu Cloud Services

Prvním krokem při vytváření webové role nebo role pracovního procesu PHP je vytvoření projektu služby Azure. projekt služby Azure slouží jako logický kontejner pro webové a pracovní role a obsahuje soubory [definice služby (. csdef)] a [Konfigurace služby (. cscfg)] .

Pokud chcete vytvořit nový projekt služby Azure, spusťte Azure PowerShell jako správce a spusťte následující příkaz:

    PS C:\>New-AzureServiceProject myProject

Tento příkaz vytvoří nový adresář (`myProject`), do kterého můžete přidat webové a pracovní role.

## <a name="add-php-web-or-worker-roles"></a>Přidat webové role nebo role pracovních procesů PHP

Chcete-li přidat webovou roli PHP do projektu, spusťte následující příkaz v kořenovém adresáři projektu:

    PS C:\myProject> Add-AzurePHPWebRole roleName

V případě role pracovního procesu použijte tento příkaz:

    PS C:\myProject> Add-AzurePHPWorkerRole roleName

> [!NOTE]
> `roleName` Parametr je nepovinný. Je-li tento parametr vynechán, bude automaticky vygenerován název role. První vytvořená webová role bude `WebRole1`, druhá `WebRole2`bude atd. První vytvořená role pracovního procesu bude `WorkerRole1`, druhá `WorkerRole2`bude atd.
>
>

## <a name="specify-the-built-in-php-version"></a>Zadat vestavěnou verzi PHP

Když do projektu přidáte webovou roli nebo roli pracovního procesu PHP, konfigurační soubory projektu se upraví tak, aby se při nasazení aplikace PHP nainstalovaná na všech webových nebo pracovních instancích vaší aplikace. Pokud chcete zobrazit verzi PHP, která se nainstaluje ve výchozím nastavení, spusťte následující příkaz:

    PS C:\myProject> Get-AzureServiceProjectRoleRuntime

Výstup z výše uvedeného příkazu bude vypadat podobně jako v následujícím příkladu. V tomto příkladu `IsDefault` je příznak nastaven na `true` 5.3.17 php, což značí, že se bude nainstalovaná výchozí verze PHP.

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

Běhovou verzi PHP můžete nastavit na některou z uvedených verzí PHP. Chcete-li například nastavit verzi PHP (pro roli s názvem `roleName`) na 5.4.0, použijte následující příkaz:

    PS C:\myProject> Set-AzureServiceProjectRole roleName php 5.4.0

> [!NOTE]
> Dostupné verze PHP se můžou v budoucnu změnit.
>
>

## <a name="customize-the-built-in-php-runtime"></a>Přizpůsobení integrovaného modulu runtime PHP

Máte plnou kontrolu nad konfigurací modulu runtime php, který se nainstaluje, když provedete výše uvedené kroky, včetně úpravy `php.ini` nastavení a povolení rozšíření.

Chcete-li přizpůsobit vestavěný modul runtime PHP, postupujte takto:

1. Do adresáře webové role přidejte novou složku s názvem `php`. `bin` V případě role pracovního procesu ji přidejte do kořenového adresáře role.
2. Ve složce vytvořte další složku s názvem `ext`. `php` Do této `.dll` složky umístěte všechny soubory rozšíření ( `php_mongo.dll`například), které chcete povolit.
3. `php.ini` Přidejte soubor`php` do složky. Povolte všechna vlastní rozšíření a nastavte všechny směrnice PHP v tomto souboru. Pokud byste například chtěli zapnout `display_errors` a `php_mongo.dll` povolit rozšíření `php.ini` , obsah souboru by byl následující:

        display_errors=On
        extension=php_mongo.dll

> [!NOTE]
> Všechna nastavení, která jste explicitně nenastavili v `php.ini` souboru, který zadáte, budou automaticky nastavena na výchozí hodnoty. Mějte ale na paměti, že můžete přidat kompletní `php.ini` soubor.
>
>

## <a name="use-your-own-php-runtime"></a>Použití vlastního modulu runtime PHP

V některých případech místo výběru integrovaného modulu runtime PHP a jeho konfigurace, jak je popsáno výše, můžete chtít poskytnout vlastní modul runtime PHP. Můžete například použít stejný modul runtime PHP v rámci webové role nebo role pracovního procesu, který používáte ve svém vývojovém prostředí. Díky tomu je snazší zajistit, aby se v produkčním prostředí aplikace neměnila chování.

### <a name="configure-a-web-role-to-use-your-own-php-runtime"></a>Konfigurace webové role pro použití vlastního modulu runtime PHP

Pokud chcete nakonfigurovat webovou roli, aby používala modul runtime PHP, který zadáte, postupujte takto:

1. Vytvořte projekt služby Azure a přidejte webovou roli PHP, jak je popsáno dříve v tomto tématu.
2. Vytvořte složku ve složce, která se nachází v kořenovém adresáři webové role, a přidejte do `php` složky modul runtime php (všechny binární soubory, konfigurační soubory, podsložky atd.). `bin` `php`
3. VOLITELNÉ Pokud modul runtime PHP používá [ovladače Microsoft pro PHP pro SQL Server][sqlsrv drivers], budete muset nakonfigurovat webovou roli tak, aby při zřizování nainstalovala [SQL Server Native Client 2012][sql native client] . Pokud to chcete provést, přidejte [instalační program sqlncli. msi x64] do `bin` složky v kořenovém adresáři webové role. Spouštěcí skript popsaný v dalším kroku bude při zřizování role tiše spustit instalační program. Pokud modul runtime PHP nepoužívá ovladače Microsoft pro PHP pro SQL Server, můžete z skriptu uvedeného v dalším kroku odebrat následující řádek:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
4. Definujte úlohu po spuštění, která konfiguruje [Internetová informační služba (IIS)][iis.net] , aby používala modul runtime php ke `.php` zpracování požadavků na stránky. Provedete to tak, `setup_web.cmd` že v textovém editoru `bin` otevřete soubor (v souboru kořenového adresáře webové role) a nahradíte jeho obsah následujícím skriptem:

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
> `download.ps1` Skript (`bin` ve složce kořenového adresáře webové role) lze odstranit po provedení výše uvedeného postupu pro použití vlastního modulu runtime php.
>
>

### <a name="configure-a-worker-role-to-use-your-own-php-runtime"></a>Konfigurace role pracovního procesu pro použití vlastního modulu runtime PHP

Chcete-li nakonfigurovat roli pracovního procesu tak, aby používala modul runtime PHP, který zadáte, postupujte takto:

1. Vytvořte projekt služby Azure a přidejte roli pracovního procesu PHP, jak je popsáno dříve v tomto tématu.
2. V kořenovém adresáři role pracovního procesu vytvořte `php` složkuapřidejtedonímodulruntimephp(všechnybinárnísoubory,konfiguračnísoubory,podsložkyatd.).`php`
3. VOLITELNÉ Pokud modul runtime PHP používá [ovladače Microsoft pro PHP pro SQL Server][sqlsrv drivers], budete muset nakonfigurovat roli pracovního procesu tak, aby při zřizování nainstalovala [SQL Server Native Client 2012][sql native client] . Provedete to tak, že do kořenového adresáře role pracovního procesu přidáte [instalační program sqlncli. msi x64] . Spouštěcí skript popsaný v dalším kroku bude při zřizování role tiše spustit instalační program. Pokud modul runtime PHP nepoužívá ovladače Microsoft pro PHP pro SQL Server, můžete z skriptu uvedeného v dalším kroku odebrat následující řádek:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
4. V případě zřízení role definujte úlohu po `php.exe` spuštění, která přidá spustitelný soubor do proměnné prostředí PATH role pracovního procesu. Provedete to tak, `setup_worker.cmd` že v textovém editoru otevřete soubor (v kořenovém adresáři role pracovního procesu) a nahradíte jeho obsah následujícím skriptem:

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

Emulátory Azure poskytují místní prostředí, ve kterém můžete otestovat aplikaci Azure před tím, než ji nasadíte do cloudu. Mezi emulátory a prostředím Azure dochází k nějakým rozdílům. Pro lepší pochopení si přečtěte téma [použití emulátoru úložiště Azure pro vývoj a testování](storage/common/storage-use-emulator.md).

Všimněte si, že musíte mít nainstalovaný PHP místně pro použití emulátoru Compute. Emulátor služby COMPUTE bude používat vaši místní instalaci PHP ke spuštění vaší aplikace.

Chcete-li spustit projekt v emulátorech, spusťte následující příkaz z kořenového adresáře vašeho projektu:

    PS C:\MyProject> Start-AzureEmulator

Zobrazí se výstup podobný tomuto:

    Creating local package...
    Starting Emulator...
    Role is running at http://127.0.0.1:81
    Started

Aplikaci spuštěnou v emulátoru můžete zobrazit tak, že otevřete webový prohlížeč a přejdete na místní adresu zobrazenou ve výstupu (`http://127.0.0.1:81` v příkladu výstup výše).

Chcete-li zastavit emulátory, spusťte tento příkaz:

    PS C:\MyProject> Stop-AzureEmulator

## <a name="publish-your-application"></a>Publikování aplikace

Chcete-li publikovat aplikaci, je nutné nejprve importovat nastavení publikování pomocí rutiny [Import-AzurePublishSettingsFile](https://docs.microsoft.com/powershell/module/servicemanagement/azure/import-azurepublishsettingsfile) . Potom můžete aplikaci publikovat pomocí rutiny [Publish-AzureServiceProject](https://docs.microsoft.com/powershell/module/servicemanagement/azure/publish-azureserviceproject) . Informace o přihlášení najdete v tématu [instalace a konfigurace Azure PowerShell](/powershell/azure/overview).

## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu [středisko pro vývojáře PHP](https://azure.microsoft.com/develop/php/).

[install ps and emulators]: https://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[definice služby (. csdef)]: https://msdn.microsoft.com/library/windowsazure/ee758711.aspx
[Konfigurace služby (. cscfg)]: https://msdn.microsoft.com/library/windowsazure/ee758710.aspx
[iis.net]: https://www.iis.net/
[sql native client]: https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation
[sqlsrv drivers]: https://php.net/sqlsrv
[instalační program sqlncli. msi x64]: https://go.microsoft.com/fwlink/?LinkID=239648
