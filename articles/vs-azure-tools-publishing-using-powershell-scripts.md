---
title: Pomocí skriptů prostředí PowerShell systému Windows k publikování pro vývojáře a testovací prostředí | Microsoft Docs
description: Další informace o použití skriptů prostředí Windows PowerShell ze sady Visual Studio pro publikování pro vývoj a testování prostředí.
services: visual-studio-online
documentationcenter: na
author: ghogen
manager: douge
editor: ''
ms.assetid: 5fff1301-5469-4d97-be88-c85c30f837c1
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2016
ms.author: ghogen
ms.openlocfilehash: 58d1c8398e626544a7b02198ec0431203aedcc81
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="using-windows-powershell-scripts-to-publish-to-dev-and-test-environments"></a>Použití skriptů Windows PowerShellu k publikování do vývojových a testovacích prostředí

Když vytvoříte webovou aplikaci v sadě Visual Studio, můžete vygenerovat skript prostředí Windows PowerShell, který můžete později použít k automatizaci publikování webu do Azure jako webové aplikace ve službě Azure App Service nebo virtuální počítač. Můžete upravit a rozšířit skript prostředí Windows PowerShell v editoru Visual Studio podle svých potřeb nebo skript integrovat existující sestavení, testování a publikování skripty.

Pomocí těchto skriptů, můžete zřídit vlastní verze webu pro dočasné použití (také označované jako vývojářů a testovací prostředí). Může například nastavit na konkrétní verzi vašeho webu na virtuálním počítači Azure nebo na přípravný slot na web, který chcete spustit testovací sadu, reprodukujte chyby, testovací oprava chyb, zkušební verze navrhované změny nebo nastavit vlastní prostředí pro ukázku nebo prezentace. Po vytvoření skriptu, který publikuje projektu, můžete znovu spustit skript podle potřeby znovu vytvořte identické prostředí nebo spusťte skript s vlastní sestavení webové aplikace k vytvoření vlastního prostředí pro testování.

## <a name="prerequisites"></a>Požadavky

* Azure SDK 2.3 nebo novější. V tématu [soubory ke stažení sady Visual Studio](http://go.microsoft.com/fwlink/?LinkID=624384). (Není potřeba sadu Azure SDK ke generování skriptů pro webové projekty. Tato funkce je pro webové projekty, není webové role v cloudové služby.)
* Prostředí Azure PowerShell 0.7.4 nebo novější. Viz téma [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).
* [Prostředí Windows PowerShell 3.0](http://go.microsoft.com/?linkid=9811175) nebo novější.

## <a name="additional-tools"></a>Další nástroje

Další nástroje a zdroje pro práci s prostředím PowerShell v sadě Visual Studio pro vývoj pro Azure jsou k dispozici. V tématu [prostředí PowerShell nástroje pro sadu Visual Studio](http://go.microsoft.com/fwlink/?LinkId=404012).

## <a name="generating-the-publish-scripts"></a>Generování skriptů publikování

Můžete vygenerovat skripty publikování pro virtuální počítač, který je hostitelem vašeho webu, když vytvoříte nový projekt pomocí následujících [tyto pokyny](virtual-machines/windows/classic/web-app-visual-studio.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Můžete také [generovat publikovat skripty pro webové aplikace v Azure App Service](app-service/app-service-web-get-started-dotnet.md).

## <a name="scripts-that-visual-studio-generates"></a>Skripty, které generuje Visual Studio

Visual Studio vytvoří řešení úrovni složku s názvem **PublishScripts** obsahující dva soubory prostředí Windows PowerShell, skript publikování pro virtuální počítač nebo webu a modul, který obsahuje funkce, které můžete použít v skripty. Visual Studio také vygeneruje soubor ve formátu JSON, který určuje podrobnosti projektu, který nasazujete.

### <a name="windows-powershell-publish-script"></a>Publikování skriptu prostředí Windows PowerShell

Skript publikování obsahuje konkrétní publikovat kroky pro nasazení na webu nebo virtuální počítač. Visual Studio poskytuje pro prostředí Windows PowerShell vývoj zvýrazňování syntaxe. Nápověda pro funkce je k dispozici, a můžete volně upravit funkce ve skriptu podle svých potřeb změny.

### <a name="windows-powershell-module"></a>Modul prostředí Windows PowerShell

Modul Windows PowerShell, který generuje Visual Studio obsahuje funkce, které používá skript publikovat. Tyto funkce Azure PowerShell nejsou určeny k upravit. Viz téma [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).

### <a name="json-configuration-file"></a>Konfigurační soubor JSON
Vytvoření souboru JSON v **konfigurace** složky a obsahuje konfigurační data, která určuje přesně prostředky, ke kterým chcete nasadit do Azure. Název souboru, který generuje Visual Studio je projekt název WAWS-dev.json Pokud jste vytvořili na webu nebo projektu název VM-dev.json, pokud jste vytvořili virtuální počítač. Tady je příklad konfiguračního souboru JSON, který se vygeneruje při vytvoření webu. Většina hodnot je není potřeba vysvětlovat. Název webu je generován Azure, takže se nemusí odpovídat název projektu.

```json
{
    "environmentSettings": {
        "webSite": {
            "name": "WebApplication26632",
            "location": "West US"
        },
        "databases": [{
            "connectionStringName": "DefaultConnection",
            "databaseName": "WebApplication26632_db",
            "serverName": "YourDatabaseServerName",
            "user": "sqluser2",
            "password": "",
            "edition": "",
            "size": "",
            "collation": "",
            "location": "West US"
        }]
    }
}
```

Když vytvoříte virtuální počítač, bude vypadat podobně jako následující konfigurační soubor JSON. Cloudové služby se vytvoří jako kontejner pro virtuální počítač. Virtuální počítač obsahuje obvyklé koncové body pro webový přístup prostřednictvím protokolu HTTP a HTTPS, jakož i koncové body pro nasazení webu, která umožňuje publikovat na web z vašeho místního počítače, vzdálené plochy a prostředí Windows PowerShell.

```json
{
    "environmentSettings": {
        "cloudService": {
            "name": "myusernamevm1",
            "affinityGroup": "",
            "location": "West US",
            "virtualNetwork": "",
            "subnet": "",
            "availabilitySet": "",
            "virtualMachine": {
                "name": "myusernamevm1",
                "vhdImage": "a699494373c04fc0bc8f2bb1389d6106__Win2K8R2SP1-Datacenter-201403.01-en.us-127GB.vhd",
                "size": "Small",
                "user": "vmuser1",
                "password": "",
                "enableWebDeployExtension": true,
                "endpoints": [{
                        "name": "Http",
                        "protocol": "TCP",
                        "publicPort": "80",
                        "privatePort": "80"
                    },
                    {
                        "name": "Https",
                        "protocol": "TCP",
                        "publicPort": "443",
                        "privatePort": "443"
                    },
                    {
                        "name": "WebDeploy",
                        "protocol": "TCP",
                        "publicPort": "8172",
                        "privatePort": "8172"
                    },
                    {
                        "name": "Remote Desktop",
                        "protocol": "TCP",
                        "publicPort": "3389",
                        "privatePort": "3389"
                    },
                    {
                        "name": "Powershell",
                        "protocol": "TCP",
                        "publicPort": "5986",
                        "privatePort": "5986"
                    }
                ]
            }
        },
        "databases": [{
            "connectionStringName": "",
            "databaseName": "",
            "serverName": "",
            "user": "",
            "password": ""
        }],
        "webDeployParameters": {
            "iisWebApplicationName": "Default Web Site"
        }
    }
}
```

Můžete upravit JSON konfiguraci chcete změnit, co se stane, když spustíte skripty publikování. `cloudService` a `virtualMachine` oddíly jsou povinné, ale můžete odstranit `databases` části Pokud tomu tak není. Vlastnosti, které jsou prázdné v výchozí konfigurační soubor, který generuje Visual Studio jsou volitelné. Tyto vlastnosti, které mají v konfiguračním souboru na výchozí hodnoty jsou požadovány.

Pokud máte web, který má několik prostředí nasazení (označované jako sloty) místo jednoho pracoviště v Azure, můžete zahrnout název slotu názvu webu v konfiguračním souboru JSON. Například, pokud máte web s názvem **server** a slotu pro něj s názvem **testování** je identifikátor URI `mysite-test.cloudapp.net`, ale mysite(test) je správný název pro použití v konfiguračním souboru. Můžete provést jenom Pokud tento web a sloty, které již existují v rámci vašeho předplatného. Pokud ještě neexistují, vytvořit web tak, že spustíte skript bez zadání přihrádky, pak vytvořte slot v [portál Azure](https://portal.azure.com/), a poté spusťte skript s názvem upravené webové stránky. Další informace o nasazovací sloty pro webové aplikace najdete v tématu [nastavení přípravných prostředí pro webové aplikace v Azure App Service](app-service/web-sites-staged-publishing.md).

## <a name="how-to-run-the-publish-scripts"></a>Jak spustit skripty publikování

Pokud jste spustili nikdy skript prostředí Windows PowerShell před, musíte nejprve nastavit zásady spouštění umožňující spouštění skriptů. Zásady je funkce zabezpečení, chcete-li zabránit uživatelům ve spouštění skriptů prostředí Windows PowerShell, pokud jsou snadno napadnutelný malwaru nebo viry, které zahrnují spouštění skriptů.

### <a name="run-the-script"></a>Spusťte skript

1. Vytvořte balíček nasazení webu pro váš projekt. Balíček nasazení webu je komprimovaný archiv (soubor .zip), které obsahují soubory, které chcete zkopírovat do svého webu nebo virtuálního počítače. Balíčky webového nasazení v sadě Visual Studio můžete vytvořit pro všechny webové aplikace.

![Vytváření webového nasazení balíčku](./media/vs-azure-tools-publishing-using-powershell-scripts/IC767885.png)

Další informace najdete v tématu [postupy: vytvoření balíčku pro nasazení webu v sadě Visual Studio](https://msdn.microsoft.com/library/dd465323.aspx). Můžete také automatizovat vytvoření vašeho balíčku nástroje nasazení webu, jak je popsáno v [přizpůsobení a rozšíření scripts[(#customizing-and-extending-publish-scripts) publikovat]

1. V **Průzkumníku řešení**, otevřete v místní nabídce pro skript a potom zvolte **otevřete pomocí prostředí PowerShell ISE**.
2. Pokud se spouštění skriptů prostředí Windows PowerShell na tomto počítači poprvé, otevřete okno příkazového řádku s oprávněními správce a zadejte následující příkaz:

    ```powershell
    Set-ExecutionPolicy RemoteSigned
    ```

3. Přihlaste se k Azure pomocí následujícího příkazu.

    ```powershell
    Add-AzureAccount
    ```

    Po zobrazení výzvy zadejte své uživatelské jméno a heslo.

    Všimněte si, že při automatizaci skript tuto metodu, jak poskytnout přihlašovací údaje Azure nefunguje. Místo toho používejte `.publishsettings` souboru k zadání přihlašovacích údajů. Jednou, můžete použít příkaz **Get-AzurePublishSettingsFile** ke stažení souboru z Azure a následně použít **Import AzurePublishSettingsFile** se soubor naimportovat. Podrobné pokyny najdete v tématu [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).

4. (Volitelné) Pokud chcete vytvořit prostředky Azure, jako je například virtuální počítač, databáze a webu bez publikování webové aplikace, použijte **publikovat WebApplication.ps1** s **-konfigurace**argument nastaven na hodnotu konfiguračního souboru JSON. Tento příkaz používá k určení, které prostředky pro vytvoření konfiguračního souboru JSON. Protože používá výchozí nastavení pro další argumenty příkazového řádku, vytvoří prostředky, ale nepodporuje publikování webové aplikace. -Verbose – možnost získáte další informace o co se děje.

    ```powershell
    Publish-WebApplication.ps1 -Verbose –Configuration C:\Path\WebProject-WAWS-dev.json
    ```

5. Použití **publikovat WebApplication.ps1** příkaz, jak je znázorněno v jednom z následujících příkladech vyvolání skriptu a publikování webové aplikace. Pokud potřebujete přepíší výchozí nastavení pro žádné další argumenty, jako je například název odběru, publikujte název balíčku, přihlašovací údaje virtuálního počítače nebo přihlašovací údaje databáze serveru, můžete určit tyto parametry. Použití **– podrobný** možnost zobrazit další informace o průběhu procesu publikování.

    ```powershell
    Publish-WebApplication.ps1 –Configuration C:\Path\WebProject-WAWS-dev-json `
    –SubscriptionName Contoso `
    -WebDeployPackage C:\Documents\Azure\ADWebApp.zip `
    -DatabaseServerPassword @{Name="dbServerName";Password="adminPassword"} `
    -Verbose
    ```

    Pokud vytváříte virtuální počítač, příkaz vypadá takto. Tento příklad také ukazuje, jak zadat pověření pro více databází. Pro virtuální počítače, které tyto skripty vytvořit není certifikát SSL od důvěryhodné kořenové autority. Proto je nutné použít **– AllowUntrusted** možnost.

    ```powershell
    Publish-WebApplication.ps1 `
    -Configuration C:\Path\ADVM-VM-test.json `
    -SubscriptionName Contoso `
    -WebDeployPackage C:\Path\ADVM.zip `
    -AllowUntrusted `
    -VMPassword @{name = "vmUserName"; password = "YourPasswordHere"} `
    -DatabaseServerPassword @{Name="server1";Password="adminPassword1"}, @{Name="server2";Password="adminPassword2"} `
    -Verbose
    ```

    Databáze můžete vytvářet skript, ale nevytvoří databázové servery. Pokud chcete vytvořit databázový server, můžete použít **New-AzureSqlDatabaseServer** funkce v modulu Azure.

## <a name="customizing-and-extending-the-publish-scripts"></a>Přizpůsobení a rozšíření skripty publikování
Můžete upravit skript publikování a konfigurační soubor JSON. Funkce v modulu Windows PowerShell **AzureWebAppPublishModule.psm1** nejsou určeny má být změněn. Pokud chcete zadat jinou databázi nebo změnit některé vlastnosti virtuálního počítače, upravte konfigurační soubor JSON. Pokud chcete rozšířit funkce skript, který chcete automatizovat vytváření a testování projektu, můžete implementovat funkce zástupných procedur v **publikovat WebApplication.ps1**.

K automatizaci vytváření projektu, přidat kód, který volá MSBuild `New-WebDeployPackage` jak ukazuje tento příklad kódu. Cesta k příkazu MSBuild se liší v závislosti na verzi sady Visual Studio, které jste nainstalovali. Chcete-li získat správnou cestu, můžete použít funkci **Get-MSBuildCmd**, jak je uvedeno v následujícím příkladu.

### <a name="to-automate-building-your-project"></a>K automatizaci vytváření projektu
1. Přidat `$ProjectFile` parametru v části globální param.

    ```powershell
    [Parameter(Mandatory = $false)]
    [ValidateScript({Test-Path $_ -PathType Leaf})]
    [String]
    $ProjectFile,
    ```

2. Zkopírujte funkci `Get-MSBuildCmd` do souboru skriptu.

    ```powershell
    function Get-MSBuildCmd
    {
            process
    {

                $path =  Get-ChildItem "HKLM:\SOFTWARE\Microsoft\MSBuild\ToolsVersions\" |
                                    Sort-Object {[double]$_.PSChildName} -Descending |
                                    Select-Object -First 1 |
                                    Get-ItemProperty -Name MSBuildToolsPath |
                                    Select -ExpandProperty MSBuildToolsPath

                $path = (Join-Path -Path $path -ChildPath 'msbuild.exe')

            return Get-Item $path
        }
    }
    ```

3. Nahraďte `New-WebDeployPackage` s následující kód a nahraďte zástupné symboly v řádku vytváření `$msbuildCmd`. Tento kód je pro Visual Studio 2017. Pokud používáte Visual Studio 2015, změňte **VisualStudioVersion** vlastnost `14.0` (`12.0` pro Visual Studio 2013).

    ```powershell
    function New-WebDeployPackage
    {
        #Write a function to build and package your web application
    ```

    Chcete-li vytvořit webovou aplikaci, použijte MsBuild.exe. Nápovědu najdete v tématu Reference k příkazovému řádku MSBuild na: [http://go.microsoft.com/fwlink/?LinkId=391339](http://go.microsoft.com/fwlink/?LinkId=391339)

    ```powershell
    Write-VerboseWithTime 'Build-WebDeployPackage: Start'

    $msbuildCmd = '"{0}" "{1}" /T:Rebuild;Package /P:VisualStudioVersion=15.0 /p:OutputPath="{2}\MSBuildOutputPath" /flp:logfile=msbuild.log,v=d' -f (Get-MSBuildCmd), $ProjectFile, $scriptDirectory

    Write-VerboseWithTime ('Build-WebDeployPackage: ' + $msbuildCmd)
    ```

### <a name="start-execution-of-the-build-command"></a>Spustit provádění příkazu sestavení

```powershell
$job = Start-Process cmd.exe -ArgumentList('/C "' + $msbuildCmd + '"') -WindowStyle Normal -Wait -PassThru

if ($job.ExitCode -ne 0) {
    throw('MsBuild exited with an error. ExitCode:' + $job.ExitCode)
}

#Obtain the project name
$projectName = (Get-Item $ProjectFile).BaseName

#Construct the path to web deploy zip package
$DeployPackageDir =  '.\MSBuildOutputPath\_PublishedWebsites\{0}_Package\{0}.zip' -f $projectName

#Get the full path for the web deploy zip package. This is required for MSDeploy to work
$WebDeployPackage = Resolve-Path –LiteralPath $DeployPackageDir

Write-VerboseWithTime 'Build-WebDeployPackage: End'

return $WebDeployPackage
}
```

1. Volání `New-WebDeployPackage` funkce před tento řádek: `$Config = Read-ConfigFile $Configuration` pro webové aplikace nebo `$Config = Read-ConfigFile $Configuration -HasWebDeployPackage:([Bool]$WebDeployPackage)` pro virtuální počítače.

    ```powershell
    if($ProjectFile)
    {
    $WebDeployPackage = New-WebDeployPackage
    }
    ```

2. Vyvolání vlastní skript z příkazového řádku pomocí předávání `$Project` argument, jako v následujícím příkladu:

    ```powershell
    .\Publish-WebApplicationVM.ps1 -Configuration .\Configurations\WebApplication5-VM-dev.json `
    -ProjectFile ..\WebApplication5\WebApplication5.csproj `
    -VMPassword @{Name="VMUser";Password="Test.123"} `
    -AllowUntrusted `
    -Verbose
    ```

    Pro automatizaci testování vaší aplikace, přidejte kód, který `Test-WebApplication`. Nezapomeňte zrušte komentář u řádků v **publikovat WebApplication.ps1** kde jsou tyto funkce volána. Pokud nezadáte implementace, můžete ručně vytvořit projekt pomocí sady Visual Studio a pak spusťte skript publikování, který chcete publikovat do Azure.

## <a name="publishing-function-summary"></a>Publikování souhrn funkcí
Chcete-li získat nápovědu pro funkce, které můžete použít na příkazovém řádku prostředí Windows PowerShell, použijte příkaz `Get-Help function-name`. V nápovědě zahrnuje parametr nápovědy a příklady. Aby se stejný text nápovědy je také ve zdrojových souborech skriptu **AzureWebAppPublishModule.psm1** a **publikovat WebApplication.ps1**. V jazyce Visual Studio jsou lokalizované skriptu a Nápověda.

**AzureWebAppPublishModule**

| Název funkce | Popis |
| --- | --- |
| Add-AzureSQLDatabase |Vytvoří novou databázi Azure SQL. |
| Add-AzureSQLDatabases |Vytvoří databáze Azure SQL z hodnot v konfiguračním souboru JSON, který generuje Visual Studio. |
| Add-AzureVM |Vytvoří virtuální počítač Azure a vrátí adresu URL nasazené virtuálního počítače. Funkce nastaví požadavky a pak zavolá **New-AzureVM** – funkce (Azure modul) k vytvoření nového virtuálního počítače. |
| Add-AzureVMEndpoints |Přidá nový vstupní koncové body k virtuálnímu počítači a vrátí virtuální počítač s nový koncový bod. |
| Add-AzureVMStorage |Vytvoří nový účet úložiště Azure v aktuálním předplatném. Název účtu začíná řetězcem "devtest", za nímž následuje jedinečný alfanumerický řetězec. Vrátí název nového účtu úložiště. Zadejte umístění nebo skupina vztahů pro nový účet úložiště. |
| Add-AzureWebsite |Vytvoří web se zadaným názvem a umístěním. Tato funkce volá **New-AzureWebsite** funkce v modulu Azure. Pokud předplatné už neobsahuje web se zadaným názvem, tato funkce vytvoří web a vrátí objekt webu. Funkce `$null`. |
| Backup-Subscription |Uloží aktuální předplatné Azure v `$Script:originalSubscription` proměnné v oboru skriptu. Tato funkce uloží aktuální předplatné Azure (jak získat `Get-AzureSubscription -Current`) a jeho účet úložiště a předplatné, které mění tímto skriptem (uložené v proměnné `$UserSpecifiedSubscription`) a jeho účet úložiště, v oboru skriptu. Ukládání hodnot, můžete pomocí funkce, jako například `Restore-Subscription`, pokud chcete obnovit původní aktuální předplatné a účet úložiště pro aktuální stav, pokud došlo ke změně aktuálního stavu. |
| Find-AzureVM |Získá zadaný virtuální počítač Azure. |
| Format-DevTestMessageWithTime |Přidá k datu a času na zprávy. Tato funkce je určená pro zpráv zapsaných do datové proudy chyba a podrobná. |
| Get-AzureSQLDatabaseConnectionString |Sestaví připojovacího řetězce pro připojení k databázi Azure SQL. |
| Get-AzureVMStorage |Vrací název první účet úložiště se stejným názvem vzor "devtest*" (malá a velká písmena) v zadaném umístění nebo skupina vztahů. Pokud "devtest*" účet úložiště neodpovídá umístění nebo skupina vztahů, funkce se přeskočí. Zadejte umístění nebo skupině vztahů. |
| Get-MSDeployCmd |Vrátí příkaz ke spuštění nástroje MsDeploy.exe. |
| New-AzureVMEnvironment |Vyhledá nebo vytvoří virtuální počítač v rámci předplatného, které se shodují s hodnotami v konfiguračním souboru JSON. |
| Publish-WebPackage |Používá MsDeploy.exe a webové publikování balíčku. Soubor ZIP k nasazení prostředků na webu. Tato funkce negeneruje žádný výstup. Pokud volání MSDeploy.exe selže, funkce vyvolá výjimku. Chcete-li získat podrobnější výstup, použijte **-Verbose** možnost. |
| Publish-WebPackageToVM |Ověřuje hodnoty parametru a potom zavolá **publikovat WebPackage** funkce. |
| ConfigFile pro čtení |Ověří konfiguračního souboru JSON a vrátí hodnotu hash tabulku vybraných hodnot. |
| Obnovení předplatného |Obnoví aktuální předplatné na původního předplatného. |
| Test-AzureModule |Vrátí `$true` Pokud je nainstalovaný modul Azure verze 0.7.4 nebo novější. Vrátí `$false` Pokud modul není nainstalován nebo je starší verze. Tato funkce nemá žádné parametry. |
| Test-AzureModuleVersion |Vrátí `$true` Pokud je verze modulu Azure 0.7.4 nebo novější. Vrátí `$false` Pokud modul není nainstalován nebo je starší verze. Tato funkce nemá žádné parametry. |
| Test-HttpsUrl |Vstupní adresa URL převede na objekt System.Uri. Vrátí `$True` Pokud se absolutní adresu URL a jeho schéma https. Vrátí `$false` Pokud adresa URL je relativní, jeho schématu není HTTPS nebo vstupní řetězec nelze převést na adresu URL. |
| Test člena |Vrátí `$true` Pokud vlastnosti nebo metody je členem objektu. Jinak vrátí `$false`. |
| Zápis ErrorWithTime |Zapíše chybovou zprávu s předponou aktuální čas. Tato funkce volá **formátu DevTestMessageWithTime** funkce pro předřazení čas před zápisu do datového proudu chyba se zprávou. |
| Write-HostWithTime |Zapíše zprávu do hostitelského programu (**Write-Host**) s předponou aktuální čas. Zápis do hostitelského programu účinek se liší. Většina programů tohoto hostitele prostředí Windows PowerShell zápisu tyto zprávy standardním výstupu. |
| Write-VerboseWithTime |Zapíše podrobnou zprávu s předponou aktuální čas. Vzhledem k tomu, že zavolá **Write-Verbose**, ve zprávě zobrazí, jenom když bude skript spuštěn s **podrobné** parametr nebo když **VerbosePreference** předvoleb je nastaven na  **Pokračovat**. |

**Publish-WebApplication**

| Název funkce | Popis |
| --- | --- |
| New-AzureWebApplicationEnvironment |Vytvoří prostředky Azure, jako je web nebo virtuálního počítače. |
| New-WebDeployPackage |Tato funkce není implementována. Můžete přidat příkazy v této funkci můžete sestavit projekt. |
| Publikování AzureWebApplication |Publikuje webovou aplikaci do Azure. |
| Publikovat webovou aplikaci |Vytvoří a nasadí webových aplikací, virtuálních počítačů, databází SQL a účty úložiště pro webový projekt sady Visual Studio. |
| Test-WebApplication |Tato funkce není implementována. Můžete přidat příkazy v této funkci můžete testování vaší aplikace. |

## <a name="next-steps"></a>Další postup
Další informace o prostředí PowerShell skriptování čtení [skriptování v prostředí Windows PowerShell](https://technet.microsoft.com/library/bb978526.aspx) a jiné skripty prostředí Azure PowerShell v [centra skriptů](https://azure.microsoft.com/documentation/scripts/).
