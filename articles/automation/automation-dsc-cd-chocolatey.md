---
title: Průběžné nasazení konfigurace stavu azure automatizace s Chocolatey
description: Popisuje průběžné nasazení DevOps pomocí konfigurace stavu Azure Automation se správcem balíčků Chocolatey. Obsahuje příklad s úplnou šablonou Správce prostředků JSON a zdrojem prostředí PowerShell.
services: automation
ms.subservice: dsc
ms.date: 08/08/2018
ms.topic: conceptual
ms.openlocfilehash: 706ab128af4379a56223ff65fb12f29d37b524f7
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383279"
---
# <a name="provide-continuous-deployment-to-virtual-machines-using-automation-state-configuration-and-chocolatey"></a>Zajistěte průběžné nasazení virtuálních počítačů pomocí konfigurace stavu automatizace a chocolatey

Ve světě DevOps existuje mnoho nástrojů, které vám pomohou s různými body v kanálu průběžné integrace. Azure Automation [State Configuration](automation-dsc-overview.md) je vítaným novým přírůstkem možností, které mohou týmy DevOps využívat. 

Azure Automation je spravovaná služba v Microsoft Azure, která umožňuje automatizovat různé úlohy pomocí runbooků, uzlů a sdílených prostředků, jako jsou přihlašovací údaje, plány a globální proměnné. Konfigurace stavu automatizace Azure rozšiřuje tuto možnost automatizace tak, aby zahrnovala nástroje konfigurace požadovaného stavu prostředí PowerShell (DSC). Zde je skvělý [přehled](automation-dsc-overview.md).

Tento článek ukazuje, jak nastavit průběžné nasazení (CD) pro počítač se systémem Windows. Můžete snadno rozšířit techniku zahrnout tolik počítačů se systémem Windows, jak je to nutné v roli, například webové stránky a přejít odtud na další role.

![Průběžné nasazení pro virtuální počítače IaaS](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci modulu AZ na pracovníka hybridní sady Runbook najdete [v tématu Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). U vašeho účtu Automation můžete aktualizovat moduly na nejnovější verzi pomocí [funkce Jak aktualizovat moduly Azure PowerShellu v Azure Automation](automation-update-azure-modules.md).

## <a name="at-a-high-level"></a>Na vysoké úrovni

Je tu docela dost děje tady, ale naštěstí to může být rozdělena do dvou hlavních procesů:

- Psaní kódu a testování, následné vytváření a publikování instalačních balíčků pro hlavní a dílčí verze systému.
- Vytváření a správa virtuálních aplikací, které instalují a spouštějí kód v balíčcích.  

Jakmile jsou oba tyto základní procesy na svém místě, je snadné automaticky aktualizovat balíček na virtuálních počítačích jako nové verze jsou vytvořeny a nasazeny.

## <a name="component-overview"></a>Přehled komponent

Správci balíčků, jako je [apt-get,](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) jsou dobře známí ve světě Linuxu, ale ne tolik ve světě Windows.
[Chocolatey](https://chocolatey.org/) je taková věc, a Scott Hanselman [blog](https://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) na toto téma je skvělý úvod. Stručně řečeno, Chocolatey umožňuje použít příkazový řádek k instalaci balíčků z centrálního úložiště do operačního systému Windows. Můžete vytvořit a spravovat vlastní úložiště a Chocolatey můžete nainstalovat balíčky z libovolného počtu úložišť, které určíte.

[PowerShell DSC](/powershell/scripting/dsc/overview/overview) je nástroj prostředí PowerShell, který umožňuje deklarovat konfiguraci, kterou chcete pro počítač. Pokud například chcete nainstalovat Chocolatey, nainstalovat službu IIS, otevřít port 80 a nainstalovat verzi 1.0.0 webu, implementuje tuto konfiguraci místní konfigurační nástroj DSC (DSC Local Configuration Manager). Server pro vyžádat DSC obsahuje úložiště konfigurací pro vaše počítače. LCM na každém počítači kontroluje pravidelně, zda jeho konfigurace odpovídá uložené konfiguraci. Může buď hlásit stav, nebo se pokusit uvést počítač zpět do souladu s uloženou konfigurací. Uloženou konfiguraci na serveru pro vyžádat můžete upravit a způsobit tak, že se počítač nebo sada počítačů zarovná se změněnou konfigurací.

Prostředek DSC je modul kódu, který má specifické funkce, jako je například správa sítí, služby Active Directory nebo sql serveru. Chocolatey DSC resource ví, jak získat přístup k nugetserveru (mimo jiné), stáhnout balíčky, nainstalovat balíčky a tak dále. Existuje mnoho dalších prostředků DSC v [Galerii prostředí PowerShell](https://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title). Tyto moduly nainstalujete na serveru pro vyžádat konfiguraci stavu Azure Automation pro použití ve vašich konfiguracích.

Šablony Správce prostředků poskytují deklarativní způsob generování infrastruktury, například sítě, podsítě, zabezpečení sítě a směrování, nástroje pro vyrovnávání zatížení, síťové karty, virtuální počítače a tak dále. Tady je [článek,](../azure-resource-manager/management/deployment-models.md) který porovnává model nasazení Správce prostředků (deklarativní) s modelem nasazení Azure Service Management (ASM nebo classic) (imperativní). Tento článek obsahuje diskusi o základní zprostředkovatelé prostředků: výpočetní prostředky, úložiště a sítě.

Jednou z klíčových funkcí šablony Správce prostředků je jeho schopnost nainstalovat rozšíření virtuálního počítače do virtuálního počítače, jak je zřízena. Rozšíření virtuálního počítače má specifické možnosti, jako je například spuštění vlastního skriptu, instalace antivirového softwaru a spuštění konfiguračního skriptu DSC. Existuje mnoho dalších typů rozšíření virtuálních počítačů.

## <a name="quick-trip-around-the-diagram"></a>Rychlý výlet kolem diagramu

Počínaje v horní části, napíšete kód, sestavíte ho, otestujete a pak vytvoříte instalační balíček. Chocolatey zvládne různé typy instalačních balíčků, jako jsou MSI, MSU, ZIP. A máte plnou sílu PowerShell uděláte skutečnou instalaci, pokud nativní schopnosti Chocolatey nejsou na to. Vložte balíček na nějaké místo dosažitelné – úložiště balíčků. Tento příklad použití používá veřejnou složku v účtu úložiště objektů blob Azure, ale může být kdekoli. Chocolatey pracuje nativně se servery NuGet a několik dalších pro správu metadat balíčku. [Tento článek](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) popisuje možnosti. Příklad použití používá NuGet. Nuspec je metadata o vašich balíčcích. Informace Nuspec je zkompilován do NuPkg a uloženy na serveru NuGet. Když vaše konfigurace požaduje balíček podle názvu a odkazuje na server NuGet, prostředek Chocolatey DSC na virtuálním počítači popadne balíček a nainstaluje jej. Můžete také požádat o konkrétní verzi balíčku.

V levém dolním rohu obrázku je šablona Azure Resource Manager. V tomto příkladu použití rozšíření virtuálního počítače zaregistruje virtuální počítač s serverem pro vyžádat stav Azure Automation jako uzel. Konfigurace je uložena na serveru pro vyžádat dvakrát: jednou jako prostý text a jednou zkompilován jako soubor MOF. Na webu Azure Portal mof představuje konfiguraci uzlu, na rozdíl od jednoduché konfigurace. Je to artefakt, který je spojen s uzlem, takže uzel bude znát jeho konfiguraci. Podrobnosti níže ukazují, jak přiřadit konfiguraci uzlu k uzlu.

Vytvoření Nuspec, kompilace a ukládání na serveru NuGet je malá věc. A už spravujete virtuální manažery. 

Další krok k průběžnému nasazení vyžaduje jednorázové nastavení serveru pro vyžádat, jednorázové registraci uzlů a vytvoření a uložení počáteční konfigurace na serveru. Jako balíčky jsou upgradovány a nasazeny do úložiště, stačí aktualizovat konfiguraci a konfiguraci uzlu na serveru vyžádat podle potřeby.

Pokud nezačínáte se šablonou Správce prostředků, je to v pořádku. Existují příkazy prostředí PowerShell, které vám pomohou zaregistrovat virtuální počítače na serveru pro vyžádat. Další informace najdete [v tématu Onboarding machines for management by Azure Automation State Configuration](automation-dsc-onboarding.md).

## <a name="about-the-usage-example"></a>O příkladu použití

Příklad použití v tomto článku začíná virtuálním počítačem z obecné image Windows Server 2012 R2 z galerie Azure. Můžete začít od libovolného uloženého obrazu a pak vyladit odtud s konfigurací DSC.
Změna konfigurace, která je vypálena do bitové kopie, je však mnohem obtížnější než dynamická aktualizace konfigurace pomocí dsc.

K použití této techniky s virtuálními počítači nemusíte používat šablonu Správce prostředků a rozšíření virtuálního počítače. A vaše virtuální počítače nemusí být v Azure, aby byly pod správou DISKŮ CD. Vše, co je nezbytné, je, že Chocolatey být nainstalován a LCM nakonfigurován na virtuálním počítači, takže ví, kde je server pro vyžádat.

Když aktualizujete balíček na virtuálním počítači, který je v produkčním prostředí, musíte tento virtuální počítač při instalaci aktualizace převést z rotace. Jak to udělat, se značně liší. Například s virtuálním počítačem za Azure Balancer, můžete přidat vlastní sondu. Při aktualizaci virtuálního zařízení, mají koncový bod sondy vrátit 400. Vyladit nezbytné způsobit tuto změnu může být uvnitř konfigurace, stejně jako vyladit přepnout zpět na vrácení 200 po dokončení aktualizace.

Úplný zdroj pro tento příklad použití je v [tomto projektu Visual Studio](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) na GitHubu.

## <a name="step-1-set-up-the-pull-server-and-automation-account"></a>Krok 1: Nastavení serveru pro vyžádat a účtu automatizace

Na ověřeném`Connect-AzAccount`příkazovém řádku prostředí PowerShell: (může trvat několik minut, než je nastaven server pro vytahování)

```azurepowershell-interactive
New-AzResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
New-AzAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT
```

Svůj účet Automation můžete umístit do některé z následujících oblastí (označovaných také jako lokality): Východní USA 2, Jižní střed USA, US Gov Virginia, Západní Evropa, Jihovýchodní Asie, Japonsko – východ, Střední Indie a Austrálie – jihovýchod, Kanada – střed, Severní Evropa.

## <a name="step-2-make-vm-extension-tweaks-to-the-resource-manager-template"></a>Krok 2: Nastavení vylepšení rozšíření virtuálního počítače v šabloně Správce prostředků

Podrobnosti o registraci virtuálního počítače (pomocí rozšíření virtuálního počítače PowerShell DSC) uvedené v této [šabloně Azure Quickstart](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver).
Tento krok zaregistruje nový virtuální počítač se serverem vyžádat v seznamu uzlů konfigurace stavu. Součástí této registrace je určení konfigurace uzlu, která má být použita pro uzel. Tato konfigurace uzlu ještě nemusí existovat na serveru pro vyžádat, takže je v pořádku, že krok 4 je místo, kde se to provádí poprvé. Ale tady v kroku 2 musíte rozhodnout o názvu uzlu a názvu konfigurace. V tomto příkladu použití je uzel "isvbox" a konfigurace je ISVBoxConfig. Název konfigurace uzlu (který bude zadán v deploymenttemplate.json) je tedy "ISVBoxConfig.isvbox".

## <a name="step-3-add-required-dsc-resources-to-the-pull-server"></a>Krok 3: Přidání požadovaných prostředků DSC na server vyžádat

Galerie prostředí PowerShell je instrumentovaná k instalaci prostředků DSC do vašeho účtu Azure Automation.
Přejděte na požadovaný prostředek a klikněte na tlačítko "Nasadit do Azure Automation".

![Příklad galerie prostředí PowerShell](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

Další technika, která byla nedávno přidána na portál Azure, umožňuje vyžádat nové moduly nebo aktualizovat stávající moduly. Proklikejte se prostředkem účtu automatizace, dlaždicí Prostředky a nakonec dlaždicí Moduly. Ikona Galerie procházení umožňuje zobrazit seznam modulů v galerii, přejít k podrobnostem a nakonec importovat do účtu Automation. To je skvělý způsob, jak udržet vaše moduly aktuální čas od času. A funkce importu kontroluje závislosti s jinými moduly, aby bylo zajištěno, že se nic nesynchronizuje.

Nebo je tu manuální přístup. Tento přístup se používá pouze jednou na prostředek, pokud jej nechcete později upgradovat. Další informace o vytváření integračních modulů Prostředí PowerShell najdete v [tématu Vytváření integračních modulů pro azure automation](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/).

>[!NOTE]
>Struktura složek modulu integrace prostředí PowerShell pro počítač se systémem Windows se trochu liší od struktury složek očekávané Azure Automation. 

1. Instalace [rozhraní Windows Management Framework v5](https://aka.ms/wmf5latest) (není potřeba pro Windows 10).

2. Nainstalujte integrační modul.

    ```azurepowershell-interactive
    Install-Module –Name MODULE-NAME`    <—grabs the module from the PowerShell Gallery
    ```

3. Zkopírujte složku modulu z **aplikace c:\Program Files\WindowsPowerShell\Modules\MODULE-NAME** do dočasné složky.

4. Odstraňte ukázky a dokumentaci z hlavní složky.

5. Zapněte hlavní složku a pojmenujte soubor ZIP s názvem složky.

6. Vložte soubor ZIP do dostupného umístění PROTOKOLU HTTP, jako je úložiště objektů blob v účtu azure storage.

7. Spusťte následující příkaz.

    ```azurepowershell-interactive
    New-AzAutomationModule `
      -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
      -Name MODULE-NAME –ContentLink 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
    ```

Zahrnutý příklad implementuje tyto kroky pro cChoco a xNetworking. 

## <a name="step-4-add-the-node-configuration-to-the-pull-server"></a>Krok 4: Přidání konfigurace uzlu na server vyžádat

Není nic zvláštního o prvním importu konfigurace do serveru pro vyžádat a kompilovat. Všechny pozdější importy nebo kompilace stejné konfigurace vypadají úplně stejně. Pokaždé, když aktualizujete balíček a potřebujete jej vytlačit do produkčního prostředí, uděláte tento krok po zajištění správného konfiguračního souboru - včetně nové verze balíčku. Zde je konfigurační soubor **ISVBoxConfig.ps1**:

```powershell
Configuration ISVBoxConfig
{
    Import-DscResource -ModuleName cChoco
    Import-DscResource -ModuleName xNetworking

    Node 'isvbox' {

        cChocoInstaller installChoco
        {
            InstallDir = 'C:\choco'
        }

        WindowsFeature installIIS
        {
            Ensure = 'Present'
            Name   = 'Web-Server'
        }

        xFirewall WebFirewallRule
        {
            Direction    = 'Inbound'
            Name         = 'Web-Server-TCP-In'
            DisplayName  = 'Web Server (TCP-In)'
            Description  = 'IIS allow incoming web site traffic.'
            Enabled       = 'True'
            Action       = 'Allow'
            Protocol     = 'TCP'
            LocalPort    = '80'
            Ensure       = 'Present'
        }

        cChocoPackageInstaller trivialWeb
        {
            Name      = 'trivialweb'
            Version   = '1.0.0'
            Source    = 'MY-NUGET-V2-SERVER-ADDRESS'
            DependsOn = '[cChocoInstaller]installChoco','[WindowsFeature]installIIS'
        }
    }
}
```

Zde je skript **New-ConfigurationScript.ps1** (upraven o použití modulu Az):

```powershell
Import-AzAutomationDscConfiguration `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -SourcePath C:\temp\AzureAutomationDsc\ISVBoxConfig.ps1 `
    -Published –Force

$jobData = Start-AzAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -ConfigurationName ISVBoxConfig

$compilationJobId = $jobData.Id

Get-AzAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -Id $compilationJobId
```

Výsledkem těchto kroků je umístění nové konfigurace uzlu s názvem **ISVBoxConfig.isvbox** na server vyžádat. Název konfigurace uzlu je `configurationName.nodeName`vytvořen jako .

## <a name="step-5-create-and-maintain-package-metadata"></a>Krok 5: Vytvoření a údržba metadat balíčku

Pro každý balíček, který vložíte do úložiště balíčků, potřebujete nuspec, který jej popisuje.
Tento nuspec musí být zkompilován a uložen y na serveru NuGet. Tento proces je popsán [zde](https://docs.nuget.org/create/creating-and-publishing-a-package). Můžete použít MyGet.org jako server NuGet. Prodávají tuto službu, ale mají startér SKU, který je zdarma. Na NuGet.org najdete pokyny k instalaci vlastního serveru NuGet pro vaše soukromé balíčky.

## <a name="step-6-tie-it-all-together"></a>Krok 6: Spojte to všechno dohromady

Pokaždé, když verze projde QA a je schválen pro nasazení, balíček je vytvořen a nuspec a nupkg jsou aktualizovány a nasazeny na server NuGet. Konfigurace (krok 4 výše) musí být také aktualizována, aby souhlasila s novým číslem verze. Potom musí být odeslány na server vyžádat a zkompilovány.

Od tohoto okamžiku je na virtuálních počítačích, které jsou závislé na této konfiguraci, aby vytáhly aktualizaci a nainstalovaly ji. Každá z těchto aktualizací je jednoduchá - jen řádek nebo dva PowerShell. Pro Azure DevOps některé z nich jsou zapouzdřené v sestavení úkoly, které lze zřetězit společně v sestavení. Tento [článek](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery) obsahuje další podrobnosti. Toto [úložiště GitHub](https://github.com/Microsoft/vso-agent-tasks) podrobně popisuje dostupné úlohy sestavení.

## <a name="related-articles"></a>Související články
* [Přehled dsc Azure Automation](automation-dsc-overview.md)
* [Rutiny DSC azure automatizace](https://docs.microsoft.com/powershell/module/azurerm.automation#automation)
* [Onboardingové stroje pro správu pomocí Azure Automation DSC](automation-dsc-onboarding.md)

## <a name="next-steps"></a>Další kroky

- Přehled najdete v tématu [Konfigurace stavu automatizace Azure](automation-dsc-overview.md).
- Další informace najdete [v tématu Začínáme s azure automation state configuration](automation-dsc-getting-started.md).
- Další informace o kompilaci konfigurací DSC, abyste je mohli přiřadit k cílovým uzlům, najdete [v tématu Kompilace konfigurací v konfiguraci stavu automatizace Azure](automation-dsc-compile.md).
- Odkaz na rutinu prostředí PowerShell naleznete v tématu [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Informace o cenách najdete v [tématu Ceny konfigurace stavu Azure Automation](https://azure.microsoft.com/pricing/details/automation/).
- Příklad použití konfigurace stavu azure automatizace v kanálu průběžného nasazení najdete v [tématu průběžné nasazení pomocí konfigurace stavu automatizace Azure a Chocolatey](automation-dsc-cd-chocolatey.md).
