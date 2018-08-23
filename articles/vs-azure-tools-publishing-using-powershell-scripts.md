---
title: Pomocí skriptů Windows Powershellu k publikování do vývojových a testovacích prostředí | Dokumentace Microsoftu
description: Další informace o použití skriptů Windows Powershellu ze sady Visual Studio k publikování vývojové a testovací prostředí.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 5fff1301-5469-4d97-be88-c85c30f837c1
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/11/2016
ms.author: ghogen
ms.openlocfilehash: dac5425f72ff57e412be664e1bc0c84aee3dec1f
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/18/2018
ms.locfileid: "42054643"
---
# <a name="using-windows-powershell-scripts-to-publish-to-dev-and-test-environments"></a>Použití skriptů Windows PowerShellu k publikování do vývojových a testovacích prostředí

Při vytváření webové aplikace v sadě Visual Studio, můžete vygenerovat skript prostředí Windows PowerShell, který můžete později použít k automatickému publikování vašeho webu do Azure jako webovou aplikaci ve službě Azure App Service nebo virtuální počítač. Můžete upravit a rozšířit skript Windows Powershellu v editoru sady Visual Studio tak, aby vyhovoval vašim požadavkům nebo můžete skript integrovat s existující sestavení, testování a publikování skripty.

Tyto skripty můžete zřídit vlastní verze (označované také jako. vývojové a testovací prostředí) vašeho webu pro dočasné použití. Může například nastavit konkrétní verzi webu na virtuálním počítači Azure nebo na přípravný slot na webu můžete spustit testovací sadu, reprodukovat chybu, otestovat oprava chyby, zkušební verze navrhované změny nebo nastavení vlastního prostředí pro ukázku nebo prezentace. Po vytvoření skriptu, který publikuje váš projekt, můžete znovu spustit skript podle potřeby znovu vytvořte identické prostředí nebo skript spouštět pomocí vlastní sestavení webové aplikace k vytvoření vlastního prostředí pro testování.

## <a name="prerequisites"></a>Požadavky

* Azure SDK 2.3 nebo novější. Zobrazit [soubory ke stažení Visual Studio](http://go.microsoft.com/fwlink/?LinkID=624384). (Není nutné sadu Azure SDK se vygenerovat skripty pro webové projekty. Tato funkce je pro webové projekty nejsou webové role v cloudových službách.)
* Prostředí Azure PowerShell 0.7.4 nebo novější. Viz téma [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).
* [Windows PowerShell 3.0](http://go.microsoft.com/?linkid=9811175) nebo novější.

## <a name="additional-tools"></a>Další nástroje

Další nástroje a prostředky pro práci s prostředím PowerShell v sadě Visual Studio pro vývoj pro Azure jsou k dispozici. Zobrazit [nástroje PowerShell Tools for Visual Studio](http://go.microsoft.com/fwlink/?LinkId=404012).

## <a name="generating-the-publish-scripts"></a>Generování skriptů publikování

Můžete generovat publikovat skriptů pro virtuální počítač, který je hostitelem vašeho webu, když vytvoříte nový projekt pomocí následujících [tyto pokyny](virtual-machines/windows/classic/web-app-visual-studio.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Můžete také [generování skriptů pro službu web apps ve službě Azure App Service publikování](app-service/scripts/app-service-powershell-deploy-github.md).

## <a name="scripts-that-visual-studio-generates"></a>Skripty, které generuje sada Visual Studio

Sada Visual Studio generuje úrovni řešení složku s názvem **PublishScripts** , která obsahuje dva soubory prostředí Windows PowerShell, publikovat skript pro váš virtuální počítač nebo webu a modul, který obsahuje funkce, které můžete použít v skripty. Visual Studio také vygeneruje soubor ve formátu JSON, který určuje informace o projektu, které nasazujete.

### <a name="windows-powershell-publish-script"></a>Publikování skript prostředí Windows PowerShell

Skript publikování obsahuje publikovat konkrétní kroky pro nasazování na web nebo do virtuálního počítače. Visual Studio nabízí pro vývoj pro Windows PowerShell barevné zvýrazňování syntaxe. Nápověda pro funkce je k dispozici, a můžete volně upravit funkce v skript tak, aby vyhovovala vaší měnícím se požadavkům.

### <a name="windows-powershell-module"></a>Modul prostředí Windows PowerShell

Modul prostředí Windows PowerShell, který generuje sada Visual Studio obsahuje funkce, které používá skript publikovat. Tyto funkce prostředí Azure PowerShell nejsou mají být změněna. Viz téma [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).

### <a name="json-configuration-file"></a>Konfigurační soubor JSON
Soubor JSON je vytvořen **konfigurace** složky a obsahuje konfigurační data, která určuje, které přesně prostředky k nasazení do Azure. Název souboru, který generuje sada Visual Studio je projekt název WAWS-dev.json Pokud jste vytvořili na webu nebo projekt název VM-dev.json, pokud jste vytvořili virtuální počítač. Tady je příklad konfiguračního souboru JSON, který je generován při vytvoření webu. Většina hodnot není potřeba vysvětlovat. Název webu je generován Azure, takže se nemusí shodovat se název vašeho projektu.

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

Při vytváření virtuálního počítače konfiguračního souboru JSON vypadá nějak takto. Cloudová služba se vytvoří jako kontejner pro virtuální počítač. Virtuální počítač obsahuje obvyklé koncové body pro webový přístup prostřednictvím protokolu HTTP a HTTPS, jakož i koncové body pro nasazení webu, který umožňuje publikování na web z místního počítače, vzdálených ploch a prostředí Windows PowerShell.

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

Můžete upravit JSON konfiguraci chcete změnit, co se stane při spuštění skriptů publikování. `cloudService` a `virtualMachine` oddíly jsou povinné, ale můžete odstranit `databases` části, pokud už nepotřebujete. Vlastnosti, které jsou prázdné ve výchozí konfigurační soubor, který generuje sada Visual Studio jsou volitelné. vlastnosti, které obsahují hodnoty v konfiguračním souboru výchozí jsou povinné.

Pokud máte web, který má více prostředí pro nasazení (označuje se jako sloty) místo jednoho pracoviště v Azure, můžete zahrnout název slotu názvu webu v konfiguračním souboru JSON. Například, pokud máte web, který je pojmenován **mysite** a slotem pro něj s názvem **testování** identifikátoru URI je `mysite-test.cloudapp.net`, ale mysite(test) je správný název určený v konfiguračním souboru. Lze provést pouze pokud webu a slotů, které již existují v rámci vašeho předplatného. Pokud ještě neexistují, vytvořte na webu pomocí skriptu pro bez zadání slotu, vytvořte místo v [webu Azure portal](https://portal.azure.com/), a poté spusťte skript s názvem změny webu. Další informace o nasazovací sloty pro web apps, najdete v části [nastavení přípravných prostředí pro web apps ve službě Azure App Service](app-service/web-sites-staged-publishing.md).

## <a name="how-to-run-the-publish-scripts"></a>Spouštění skriptů publikování

Pokud jste nikdy spustit skript prostředí Windows PowerShell před, musíte nejprve nastavte zásady spouštění umožňující spouštění skriptů. Zásady je funkce zabezpečení, která zabránit uživatelům ve spouštění skriptů prostředí Windows PowerShell v případě, že jsou citlivé na malwaru a virů, které se týkají spouštění skriptů.

### <a name="run-the-script"></a>Spusťte skript

1. Vytvořte balíček nasazení webu pro váš projekt. Balíček nasazení webu je komprimovaný archiv (soubor .zip), které obsahují soubory, které chcete zkopírovat do svého webu nebo virtuálního počítače. Balíčky nasazení webu v sadě Visual Studio můžete vytvořit pro jakékoli webové aplikaci.

![Vytvoření webové nasazení balíčku](./media/vs-azure-tools-publishing-using-powershell-scripts/IC767885.png)

Další informace najdete v tématu [postupy: vytvoření balíčku pro nasazení webu v sadě Visual Studio](https://msdn.microsoft.com/library/dd465323.aspx). Můžete také automatizovat vytváření balíčku Webdeploy, jak je popsáno v [přizpůsobení a rozšíření scripts[(#customizing-and-extending-publish-scripts) publikovat]

1. V **Průzkumníka řešení**, otevřete kontextovou nabídku pro skript a klikněte na tlačítko **otevřít v PowerShell ISE**.
2. Pokud se spouštění skriptů prostředí Windows PowerShell na tomto počítači poprvé, otevřete okno příkazového řádku s oprávněními správce a zadejte následující příkaz:

    ```powershell
    Set-ExecutionPolicy RemoteSigned
    ```

3. Přihlaste se do Azure pomocí následujícího příkazu.

    ```powershell
    Add-AzureAccount
    ```

    Po zobrazení výzvy zadejte svoje uživatelské jméno a heslo.

    Všimněte si, že při automatizaci skriptu, tato metoda poskytuje přihlašovací údaje Azure nefunguje. Místo toho používejte `.publishsettings` souboru k zadání přihlašovacích údajů. Jednou pouze, je použít příkaz **Get-AzurePublishSettingsFile** stáhne soubor z Azure, a poté použijte **Import AzurePublishSettingsFile** se soubor naimportovat. Podrobné pokyny najdete v tématu [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).

4. (Volitelné) Pokud chcete vytvářet prostředky Azure, jako je například virtuální počítač, databáze a webu bez publikování webové aplikace, použijte **publikovat WebApplication.ps1** příkazů **– konfigurace**argument nastavení do konfiguračního souboru JSON. Tento příkazový řádek využívá konfigurační soubor JSON k určení, které prostředky k vytvoření. Protože ho používá výchozí nastavení pro další argumenty příkazového řádku, vytvoří prostředky, ale nepodporuje publikování vašich webových aplikací. -Verbose – možnost získáte další informace o tom, co se děje.

    ```powershell
    Publish-WebApplication.ps1 -Verbose –Configuration C:\Path\WebProject-WAWS-dev.json
    ```

5. Použití **publikovat WebApplication.ps1** příkaz, jak je znázorněno v jednom z následujících příkladů pro vyvolání skriptu a publikování vašich webových aplikací. Pokud je potřeba přepsat výchozí nastavení pro všechny další argumenty, jako je například název odběru, publikujte název balíčku, přihlašovací údaje virtuálního počítače nebo přihlašovací údaje serveru databáze, můžete zadat tyto parametry. Použití **– Verbose** můžete zobrazit další informace o průběhu procesu publikování.

    ```powershell
    Publish-WebApplication.ps1 –Configuration C:\Path\WebProject-WAWS-dev-json `
    –SubscriptionName Contoso `
    -WebDeployPackage C:\Documents\Azure\ADWebApp.zip `
    -DatabaseServerPassword @{Name="dbServerName";Password="adminPassword"} `
    -Verbose
    ```

    Pokud vytváříte virtuální počítač, příkaz vypadá takto. Tento příklad také ukazuje, jak zadat pověření pro více databází. Pro virtuální počítače, které vytvářejí tyto skripty není certifikát SSL od důvěryhodné kořenové autoritě. Proto budete muset použít **– AllowUntrusted** možnost.

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

    Skript můžete vytvářet databáze, ale nevytváří databázové servery. Pokud chcete vytvořit databázový server, můžete použít **New-AzureSqlDatabaseServer** funkce v modulu Azure.

## <a name="customizing-and-extending-the-publish-scripts"></a>Přizpůsobení a rozšíření skriptů publikování
Můžete přizpůsobit publikovat skript a konfigurační soubor JSON. Funkce v modulu Windows PowerShell **AzureWebAppPublishModule.psm1** nejsou mají být změněna. Pokud chcete zadat jinou databázi, nebo změnit některé vlastnosti virtuálního počítače, upravte konfigurační soubor JSON. Pokud chcete rozšířit funkce skript, který chcete automatizovat vytváření a testování projektu, můžete implementovat zástupné procedury funkcí v **publikovat WebApplication.ps1**.

K automatizaci sestavení projektu, přidejte kód, který volá MSBuild `New-WebDeployPackage` jak je znázorněno v tomto příkladu kódu. Cesta k příkazu MSBuild se liší v závislosti na verzi sady Visual Studio, které jste nainstalovali. Chcete-li získat správnou cestu, můžete použít funkci **Get-MSBuildCmd**, jak je znázorněno v tomto příkladu.

### <a name="to-automate-building-your-project"></a>K automatizaci sestavování projektu
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

3. Nahraďte `New-WebDeployPackage` s následující kód a nahraďte zástupné symboly při konstrukci řádku `$msbuildCmd`. Tento kód je pro Visual Studio 2017. Pokud používáte Visual Studio 2015, změňte **VisualStudioVersion** vlastnost `14.0` (`12.0` pro Visual Studio 2013).

    ```powershell
    function New-WebDeployPackage
    {
        #Write a function to build and package your web application
    ```

    Pokud chcete vytvořit webovou aplikaci, použijte MsBuild.exe. Nápovědu najdete v tématu MSBuild Reference k příkazovému řádku na: [http://go.microsoft.com/fwlink/?LinkId=391339](http://go.microsoft.com/fwlink/?LinkId=391339)

    ```powershell
    Write-VerboseWithTime 'Build-WebDeployPackage: Start'

    $msbuildCmd = '"{0}" "{1}" /T:Rebuild;Package /P:VisualStudioVersion=15.0 /p:OutputPath="{2}\MSBuildOutputPath" /flp:logfile=msbuild.log,v=d' -f (Get-MSBuildCmd), $ProjectFile, $scriptDirectory

    Write-VerboseWithTime ('Build-WebDeployPackage: ' + $msbuildCmd)
    ```

### <a name="start-execution-of-the-build-command"></a>Spuštění příkazu k sestavení

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

1. Volání `New-WebDeployPackage` funkce před tímto řádkem: `$Config = Read-ConfigFile $Configuration` pro web apps nebo `$Config = Read-ConfigFile $Configuration -HasWebDeployPackage:([Bool]$WebDeployPackage)` pro virtuální počítače.

    ```powershell
    if($ProjectFile)
    {
    $WebDeployPackage = New-WebDeployPackage
    }
    ```

2. Vyvolat vlastní skript z příkazového řádku pomocí předání `$Project` argument, jako v následujícím příkladu:

    ```powershell
    .\Publish-WebApplicationVM.ps1 -Configuration .\Configurations\WebApplication5-VM-dev.json `
    -ProjectFile ..\WebApplication5\WebApplication5.csproj `
    -VMPassword @{Name="VMUser";Password="Test.123"} `
    -AllowUntrusted `
    -Verbose
    ```

    Pokud chcete automatizovat testování vaší aplikace, přidejte kód pro `Test-WebApplication`. Nezapomeňte zrušte komentář u řádků v **publikovat WebApplication.ps1** kde jsou tyto funkce volány. Pokud nezadáte implementaci, můžete ručně vytvořit projekt pomocí sady Visual Studio a spusťte skript publikování publikovat do Azure.

## <a name="publishing-function-summary"></a>Souhrn publikování – funkce
Chcete-li získat nápovědu pro funkce, které můžete použít na příkazovém řádku prostředí Windows PowerShell, použijte příkaz `Get-Help function-name`. Nápověda obsahuje nápovědu k parametrům a příklady. Stejný text nápovědy je rovněž ve zdrojových souborech skript **AzureWebAppPublishModule.psm1** a **publikovat WebApplication.ps1**. V jazyce Visual Studio jsou lokalizovány skript a pomoc.

**AzureWebAppPublishModule**

| Název funkce | Popis |
| --- | --- |
| Add-AzureSQLDatabase |Vytvoří novou databázi Azure SQL. |
| Add-AzureSQLDatabases |Vytvoří databáze Azure SQL z hodnot v konfiguračním souboru JSON, který generuje sada Visual Studio. |
| Add-AzureVM |Vytvoří virtuální počítač Azure a vrátí adresu URL nasazeného virtuálního počítače. Funkce nastaví požadavky a pak zavolá **New-AzureVM** – funkce (modul Azure) k vytvoření nového virtuálního počítače. |
| Add-AzureVMEndpoints |Přidá nové vstupní koncové body do virtuálních počítačů a vrátí virtuální počítač s nový koncový bod. |
| Add-AzureVMStorage |Vytvoří nový účet úložiště Azure v rámci aktuálního předplatného. Název účtu začíná na "devtest", za nímž následuje jedinečný alfanumerický řetězec. Funkce vrací název nového účtu úložiště. Zadejte umístění nebo skupina vztahů pro nový účet úložiště. |
| Add-AzureWebsite |Vytvoří web se zadaným názvem a umístěním. Tato funkce volá **New-AzureWebsite** funkce v modulu Azure. Pokud předplatné už nezahrnuje web se zadaným názvem, tato funkce vytvoří a vrátí objekt webu. V opačném případě vrátí `$null`. |
| Backup-Subscription |Uloží aktuální předplatné Azure v `$Script:originalSubscription` proměnné v oboru skriptu. Tato funkce uloží aktuální předplatné Azure (získaný pomocí `Get-AzureSubscription -Current`) a jeho účet úložiště a předplatné, které tento skript změní (uložené v proměnné `$UserSpecifiedSubscription`) a jeho účet úložiště v oboru skriptu. Ukládání hodnot, můžete pomocí funkce, jako například `Restore-Subscription`, pokud chcete obnovit původní aktuální předplatné a účet úložiště pro aktuální stav, pokud došlo ke změně aktuální stav. |
| Find-AzureVM |Získá zadaný virtuální počítač Azure. |
| Format-DevTestMessageWithTime |Připojí na začátek datum a čas na zprávu. Tato funkce je navržená pro zprávy zapisované do streamů chyba a podrobná. |
| Get-AzureSQLDatabaseConnectionString |Sestavuje připojovací řetězec pro připojení k databázi Azure SQL. |
| Get-AzureVMStorage |Vrací název prvního účtu úložiště se vzorem názvu "devtest *" (malá a velká písmena) v zadaném umístění nebo skupina vztahů. Pokud "devtest*" účet úložiště neodpovídá umístění nebo skupina vztahů, funkce ho ignoruje. Zadejte umístění nebo skupinu vztahů. |
| Get-MSDeployCmd |Vrátí příkaz pro spuštění nástroje MsDeploy.exe. |
| New-AzureVMEnvironment |Vyhledá nebo vytvoří virtuální počítač v rámci předplatného, který odpovídá hodnotám v konfiguračním souboru JSON. |
| Publish-WebPackage |Používá MsDeploy.exe a webového publikování balíčku. Soubor ZIP do nasazení prostředků na webu. Tato funkce negeneruje žádný výstup. Pokud se nepovede zavolat MSDeploy.exe, funkce vyvolá výjimku. Chcete-li získat podrobnější výstup, použijte **-Verbose** možnost. |
| Publish-WebPackageToVM |Ověřuje hodnoty parametrů a pak zavolá **Publish-WebPackage** funkce. |
| Read-ConfigFile |Ověří konfigurační soubor JSON a vrátí zatřiďovací tabulku vybraných hodnot. |
| Restore-Subscription |Obnoví aktuální předplatné původního předplatného. |
| Test-AzureModule |Vrátí `$true` Pokud nainstalovaný modul Azure verze 0.7.4 nebo novější. Vrátí `$false` Pokud modul není nainstalován nebo je starší verze. Tato funkce nemá žádné parametry. |
| Test-AzureModuleVersion |Vrátí `$true` Pokud modul Azure verze 0.7.4 nebo novější. Vrátí `$false` Pokud modul není nainstalován nebo je starší verze. Tato funkce nemá žádné parametry. |
| Test-HttpsUrl |Převádí vstupní adresu URL na objekt System.Uri. Vrátí `$True` Pokud je adresa URL absolutní a její schéma je https. Vrátí `$false` adresa URL je relativní, její schéma není HTTPS, zda vstupní řetězec nelze převést na adresu URL. |
| Test člen |Vrátí `$true` Pokud vlastnost nebo metoda je členem objektu. V opačném případě vrátí `$false`. |
| Zápis ErrorWithTime |Zapíše chybovou zprávu s aktuálním časem na začátku. Tato funkce volá **Format-DevTestMessageWithTime** funkce čas dříve, než zápisu zprávy do chybového proudu. |
| Zápis HostWithTime |Zapíše zprávu do hostujícího programu (**Write-Host**) s aktuálním časem na začátku. Účinek zápisu do hostujícího programu se liší. Většina programů, které hostují Windows PowerShell psát tyto zprávy do standardního výstupu. |
| Write-VerboseWithTime |Zapíše podrobnou zprávu s aktuálním časem na začátku. Jelikož volá **Write-Verbose**, pouze při spuštění skriptu se zobrazí zpráva **Verbose** parametr nebo pokud **VerbosePreference** předvoleb je nastavena na  **Pokračovat**. |

**Publikovat webovou aplikaci**

| Název funkce | Popis |
| --- | --- |
| Nové AzureWebApplicationEnvironment |Vytváří prostředky Azure, jako je například webu nebo virtuálního počítače. |
| New-WebDeployPackage |Tato funkce není implementována. Přidání příkazů v této funkci chcete projekt sestavit. |
| Publikování AzureWebApplication |Publikuje webovou aplikaci do Azure. |
| Publikovat webovou aplikaci |Vytvoří a nasadí Web Apps, virtuálních počítačů, databází SQL a účtů úložiště pro webový projekt sady Visual Studio. |
| Test-WebApplication |Tato funkce není implementována. Přidání příkazů v této funkci pro testování vaší aplikace. |

## <a name="next-steps"></a>Další postup
Další informace o skriptování Powershellu načtením [skriptování v prostředí Windows PowerShell](https://technet.microsoft.com/library/bb978526.aspx) a zobrazit další skripty Azure Powershellu v [centra skriptů](https://azure.microsoft.com/documentation/scripts/).
