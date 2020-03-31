---
title: Průběžné nasazení konfigurace stavu azure automatizace s chocolatey
description: Průběžné nasazení DevOps pomocí konfigurace stavu Azure Automation, DSC a Správce balíčků Chocolatey.  Příklad s úplnou šablonou Správce prostředků JSON a zdrojem prostředí PowerShell.
services: automation
ms.subservice: dsc
ms.date: 08/08/2018
ms.topic: conceptual
ms.openlocfilehash: 4445f6e9b72380b66f3282d50871b4283f7fc7fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75966747"
---
# <a name="usage-example-continuous-deployment-to-virtual-machines-using-automation-state-configuration-and-chocolatey"></a>Příklad použití: Průběžné nasazení do virtuálních počítačů pomocí konfigurace stavu automatizace a Chocolatey

Ve světě DevOps existuje mnoho nástrojů, které vám pomohou s různými body v kanálu průběžné integrace. Azure Automation State Configuration je vítaným novým přírůstkem možností, které mohou týmy DevOps využívat. Tento článek ukazuje nastavení průběžného nasazení (CD) pro počítač se systémem Windows. Můžete snadno rozšířit techniku zahrnout tolik počítačů se systémem Windows, jak je to nutné v roli (webové stránky, například) a odtud na další role stejně.

![Průběžné nasazení pro virtuální počítače IaaS](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## <a name="at-a-high-level"></a>Na vysoké úrovni

Tam je docela dost děje tady, ale naštěstí to může být rozdělena do dvou hlavních procesů:

- Psaní kódu a testování, následné vytváření a publikování instalačních balíčků pro hlavní a dílčí verze systému.
- Vytváření a správa virtuálních aplikací, které nainstalují a spustí kód v balíčcích.  

Jakmile jsou oba tyto základní procesy na místě, je to krátký krok k automatické aktualizaci balíčku spuštěného na konkrétním virtuálním počítači, protože se vytvářejí a nasazují nové verze.

## <a name="component-overview"></a>Přehled komponent

Správci balíčků, jako je [apt-get,](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) jsou ve světě Linuxu velmi dobře známí, ale ne tolik ve světě Windows.
[Chocolatey](https://chocolatey.org/) je taková věc, a Scott Hanselman [blog](https://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) na toto téma je skvělý úvod. Stručně řečeno, Chocolatey umožňuje instalovat balíčky z centrálního úložiště balíčků do systému Windows pomocí příkazového řádku. Můžete vytvořit a spravovat vlastní úložiště a Chocolatey můžete nainstalovat balíčky z libovolného počtu úložišť, které určíte.

Konfigurace požadovaného stavu (DSC)[(přehled)](/powershell/scripting/dsc/overview/overview)je nástroj prostředí PowerShell, který umožňuje deklarovat požadovanou konfiguraci pro počítač. Můžete například říci: "Chci nainstalovanou chocolatey, chci nainstalovat iis, chci otevřít port 80, chci nainstalovanou verzi 1.0.0 svých webových stránek." DSC Místní Configuration Manager (LCM) implementuje tuto konfiguraci. Server pro vyžádat dsc obsahuje úložiště konfigurací pro vaše počítače. LCM na každém počítači kontroluje pravidelně, zda jeho konfigurace odpovídá uložené konfiguraci. Může buď hlásit stav, nebo se pokusit uvést počítač zpět do souladu s uloženou konfigurací. Uloženou konfiguraci na serveru pro vyžádat můžete upravit a způsobit tak, že se počítač nebo sada počítačů zarovná se změněnou konfigurací.

Azure Automation je spravovaná služba v Microsoft Azure, která umožňuje automatizovat různé úkoly pomocí runbooků, uzlů, přihlašovacích údajů, prostředků a prostředků, jako jsou plány a globální proměnné.
Konfigurace stavu automatizace Azure rozšiřuje tuto možnost automatizace tak, aby zahrnovala nástroje PowerShell DSC. Zde je skvělý [přehled](automation-dsc-overview.md).

DSC Resource je modul kódu, který má specifické funkce, jako je například správa sítí, služby Active Directory nebo SQL Server. Chocolatey DSC resource ví, jak získat přístup k nugetserveru (mimo jiné), stáhnout balíčky, nainstalovat balíčky a tak dále. Existuje mnoho dalších prostředků DSC v [Galerii prostředí PowerShell](https://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title).
Tyto moduly se instalují do serveru pro vyžádat konfiguraci stavu Azure Automation (vámi), aby je mohly používat vaše konfigurace.

Šablony Správce prostředků poskytují deklarativní způsob generování infrastruktury – například sítě, podsítě, zabezpečení sítě a směrování, nástroje pro vyrovnávání zatížení, síťové karty, virtuální počítače a tak dále. Tady je [článek,](../azure-resource-manager/management/deployment-models.md) který porovnává model nasazení Správce prostředků (deklarativní) s modelem nasazení Azure Service Management (ASM nebo classic) (imperativní) a popisuje základní zprostředkovatele prostředků, výpočetní prostředky, úložiště a síť.

Jednou z klíčových funkcí šablony Správce prostředků je jeho schopnost nainstalovat rozšíření virtuálního počítače do virtuálního počítače, jak je zřízena. Rozšíření virtuálního počítače má specifické funkce, jako je spuštění vlastního skriptu, instalace antivirového softwaru nebo spuštění konfiguračního skriptu DSC. Existuje mnoho dalších typů rozšíření virtuálních počítačů.

## <a name="quick-trip-around-the-diagram"></a>Rychlý výlet kolem diagramu

Počínaje v horní části, napíšete kód, sestavení a testování a pak vytvoříte instalační balíček.
Chocolatey zvládne různé typy instalačních balíčků, jako jsou MSI, MSU, ZIP. A máte plnou sílu PowerShell dělat skutečnou instalaci, pokud Chocolateys nativní schopnosti nejsou zcela na to. Vložte balíček na nějaké místo dosažitelné – úložiště balíčků. Tento příklad použití používá veřejnou složku v účtu úložiště objektů blob Azure, ale může být kdekoli. Chocolatey pracuje nativně se servery NuGet a několik dalších pro správu metadat balíčku. [Tento článek](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) popisuje možnosti. Tento příklad použití používá NuGet. Nuspec je metadata o vašich balíčcích. Nuspec jsou "zkompilovány" do NuPkg a uloženy na serveru NuGet. Když vaše konfigurace požaduje balíček podle názvu a odkazuje na server NuGet, chocolatey DSC resource (nyní na virtuálním počítači) uchopí balíček a nainstaluje ho za vás. Můžete také požádat o konkrétní verzi balíčku.

V levé dolní části obrázku je šablona Azure Resource Manager. V tomto příkladu použití rozšíření virtuálního počítače zaregistruje virtuální počítač s Azure Automation State Configuration Pull Server (to znamená, že server pro vyžádat) jako uzel. Konfigurace je uložena na serveru vyžádat.
Ve skutečnosti je uložen dvakrát: jednou jako prostý text a jednou zkompilován jako soubor MOF (pro ty, kteří o takových věcech vědí.) Na portálu MOF je "konfigurace uzlu" (na rozdíl od jednoduše "konfigurace"). Je to artefakt, který je spojen s uzl, takže uzel bude znát jeho konfiguraci. Podrobnosti níže ukazují, jak přiřadit konfiguraci uzlu k uzlu.

Pravděpodobně jste již dělá trochu na vrcholu, nebo většina z toho. Vytvoření nuspec, kompilace a ukládání na serveru NuGet je malá věc. A už spravujete virtuální manažery. Provedení dalšího kroku k průběžnému nasazení vyžaduje nastavení serveru pro vyžádat (jednou), registraci uzlů s ním (jednou) a vytvoření a uložení konfigurace tam (zpočátku). Potom jako balíčky jsou upgradovány a nasazeny do úložiště, aktualizujte konfigurace a konfigurace uzlu v vyžádat server (opakujte podle potřeby).

Pokud nezačínáte se šablonou Správce prostředků, je to také v pořádku. Existují rutiny prostředí PowerShell, které vám pomohou zaregistrovat virtuální počítače pomocí serveru pro vyžádat a všech ostatních. Další podrobnosti najdete v tomto článku: [Onboarding počítače pro správu pomocí konfigurace stavu automatizace Azure](automation-dsc-onboarding.md).

## <a name="step-1-setting-up-the-pull-server-and-automation-account"></a>Krok 1: Nastavení serveru pro vyžádat a účtu automatizace

Na ověřeném`Connect-AzureRmAccount`příkazovém řádku prostředí PowerShell: (může trvat několik minut, než je nastaven server pro vytahování)

```azurepowershell-interactive
New-AzureRmResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
New-AzureRmAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT
```

Svůj účet automatizace můžete umístit do některé z následujících oblastí (aka umístění): Východní USA 2, Jižní střed USA, US Gov Virginia, Západní Evropa, Jihovýchodní Asie, Japonsko – východ, Střední Indie a Austrálie – jihovýchod, Kanada – střed, Severní Evropa.

## <a name="step-2-vm-extension-tweaks-to-the-resource-manager-template"></a>Krok 2: Vylepšení rozšíření virtuálního počítače na šablonu Správce prostředků

Podrobnosti o registraci virtuálního počítače (pomocí rozšíření virtuálního počítače PowerShell DSC) uvedené v této [šabloně Azure Quickstart](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver).
Tento krok zaregistruje nový virtuální počítač se serverem vyžádat v seznamu uzlů konfigurace stavu. Součástí této registrace je určení konfigurace uzlu, která má být použita pro uzel. Tato konfigurace uzlu ještě nemusí existovat na serveru pro vyžádat, takže je v pořádku, že krok 4 je místo, kde se to provádí poprvé. Ale tady v kroku 2 musíte rozhodnout o názvu uzlu a názvu konfigurace. V tomto příkladu použití je uzel "isvbox" a konfigurace je ISVBoxConfig. Název konfigurace uzlu (který bude zadán v deploymenttemplate.json) je tedy "ISVBoxConfig.isvbox".

## <a name="step-3-adding-required-dsc-resources-to-the-pull-server"></a>Krok 3: Přidání požadovaných prostředků DSC na server vyžádat

Galerie prostředí PowerShell je instrumentovaná k instalaci prostředků DSC do vašeho účtu Azure Automation.
Přejděte na požadovaný prostředek a klikněte na tlačítko "Nasadit do Azure Automation".

![Příklad galerie prostředí PowerShell](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

Další technika, která byla nedávno přidána na portál Azure Portal, umožňuje vyžádat nové moduly nebo aktualizovat stávající moduly. Proklikejte se zdrojem účtu automatizace, dlaždicí Prostředky a nakonec dlaždicí Moduly. Ikona Galerie procházení umožňuje zobrazit seznam modulů v galerii, přejít k podrobnostem a nakonec importovat do účtu automation. To je skvělý způsob, jak udržet vaše moduly aktuální čas od času. A funkce importu kontroluje závislosti s jinými moduly, aby bylo zajištěno, že se nic nesynchronizuje.

Nebo je tu manuální přístup. Struktura složek modulu integrace prostředí PowerShell pro počítač se systémem Windows se trochu liší od struktury složek očekávané Azure Automation.
To vyžaduje trochu vylepšování z vaší strany. Ale není to těžké, a to se provádí pouze jednou za zdroj (pokud chcete upgradovat v budoucnu.) Další informace o vytváření modulů integrace Prostředí PowerShell najdete v tomto článku: [Vytváření integračních modulů pro Azure Automation](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/)

- Nainstalujte modul, který potřebujete, na pracovní stanici, a to následovně:
  - Instalace [rozhraní Windows Management Framework, v5](https://aka.ms/wmf5latest) (není potřeba pro Windows 10)
  - `Install-Module –Name MODULE-NAME`< – popadne modul z Galerie prostředí PowerShell
- Kopírování složky `c:\Program Files\WindowsPowerShell\Modules\MODULE-NAME` modulu z dočasné složky
- Odstranění vzorků a dokumentace z hlavní složky
- Zip hlavní složky, pojmenování souborzip přesně stejný jako složka 
- Vložte soubor ZIP do dostupného umístění PROTOKOLU HTTP, jako je úložiště objektů blob v účtu úložiště Azure.
- Spusťte tento PowerShell:

  ```powershell
  New-AzureRmAutomationModule `
    -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -Name MODULE-NAME –ContentLink 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
  ```

Zahrnutý příklad provede tyto kroky pro cChoco a xNetworking. Speciální zacházení pro cChoco naleznete v [poznámkách.](#notes)

## <a name="step-4-adding-the-node-configuration-to-the-pull-server"></a>Krok 4: Přidání konfigurace uzlu na server vyžádat

Není nic zvláštního o prvním importu konfigurace do serveru pro vyžádat a kompilovat. Všechny následné importy/kompilace stejné konfigurace vypadají úplně stejně. Pokaždé, když aktualizujete balíček a potřebujete jej vytlačit do produkčního prostředí, uděláte tento krok po zajištění správného konfiguračního souboru - včetně nové verze balíčku. Tady je konfigurační soubor a PowerShell:

SOUBOR ISVBoxConfig.ps1:

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

New-ConfigurationScript.ps1:

```powershell
Import-AzureRmAutomationDscConfiguration `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -SourcePath C:\temp\AzureAutomationDsc\ISVBoxConfig.ps1 `
    -Published –Force

$jobData = Start-AzureRmAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -ConfigurationName ISVBoxConfig

$compilationJobId = $jobData.Id

Get-AzureRmAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -Id $compilationJobId
```

Výsledkem těchto kroků je umístění nové konfigurace uzlu s názvem "ISVBoxConfig.isvbox" na server vyžádat. Název konfigurace uzlu je vytvořen jako "configurationName.nodeName".

## <a name="step-5-creating-and-maintaining-package-metadata"></a>Krok 5: Vytváření a údržba metadat balíčku

Pro každý balíček, který vložíte do úložiště balíčků, potřebujete nuspec, který jej popisuje.
Tento nuspec musí být zkompilován a uložen y na serveru NuGet. Tento proces je popsán [zde](https://docs.nuget.org/create/creating-and-publishing-a-package). Můžete použít MyGet.org jako server NuGet. Prodávají tuto službu, ale mají startér SKU, který je zdarma. V NuGet.org najdete pokyny k instalaci vlastního nugetového serveru pro vaše soukromé balíčky.

## <a name="step-6-tying-it-all-together"></a>Krok 6: Vázání to všechno dohromady

Pokaždé, když verze projde QA a je schválen pro nasazení, balíček je vytvořen, nuspec a nupkg aktualizována a nasazena na server NuGet. Kromě toho musí být konfigurace (krok 4 výše) aktualizována, aby bylo možné souhlasit s novým číslem verze. Musí být odeslána na server vyžádat a zkompilována.
Od tohoto okamžiku je na virtuálních počítačích, které jsou závislé na této konfiguraci, aby vytáhly aktualizaci a nainstalovaly ji. Každá z těchto aktualizací je jednoduchá – pouze jeden řádek nebo dva prostředí PowerShell. V případě Azure DevOps některé z nich jsou zapouzdřeny v sestavení úkoly, které lze zřetězit společně v sestavení. Tento [článek](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery) obsahuje další podrobnosti. Tento [úložiště GitHub](https://github.com/Microsoft/vso-agent-tasks) podrobně popisuje různé dostupné úlohy sestavení.

## <a name="notes"></a>Poznámky

Tento příklad využití začíná virtuálním počítačem z obecné image Windows Server 2012 R2 z galerie Azure. Můžete začít od libovolného uloženého obrazu a pak vyladit odtud s konfigurací DSC.
Změna konfigurace, která je vypálena do bitové kopie, je však mnohem obtížnější než dynamická aktualizace konfigurace pomocí dsc.

K použití této techniky s virtuálními počítači nemusíte používat šablonu Správce prostředků a rozšíření virtuálního počítače. A vaše virtuální počítače nemusí být v Azure, aby byly pod správou DISKŮ CD. Vše, co je nezbytné, je, že Chocolatey být nainstalován a LCM nakonfigurován na virtuálním počítači, takže ví, kde je server pro vyžádat.

Samozřejmě, když aktualizujete balíček na virtuálním počítači, který je v produkčním prostředí, musíte tento virtuální počítač při instalaci aktualizace vyřadit z rotace. Jak to udělat, se značně liší. Například s virtuálním počítačem za Azure Balancer, můžete přidat vlastní sondu. Při aktualizaci virtuálního zařízení, mají koncový bod sondy vrátit 400. Vyladit nezbytné způsobit tuto změnu může být uvnitř konfigurace, stejně jako vyladit přepnout zpět na vrácení 200 po dokončení aktualizace.

Úplný zdroj pro tento příklad použití je v [tomto projektu Visual Studio](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) na GitHubu.

## <a name="related-articles"></a>Související články
* [Přehled dsc Azure Automation](automation-dsc-overview.md)
* [Rutiny DSC azure automatizace](https://docs.microsoft.com/powershell/module/azurerm.automation#automation)
* [Onboardingové stroje pro správu pomocí Azure Automation DSC](automation-dsc-onboarding.md)

## <a name="next-steps"></a>Další kroky

- Přehled najdete v tématu [Konfigurace stavu automatizace Azure](automation-dsc-overview.md)
- Další informace najdete v tématu [Začínáme s konfigurací stavu automatizace Azure](automation-dsc-getting-started.md)
- Další informace o kompilaci konfigurací DSC, abyste je mohli přiřadit k cílovým uzlům, najdete [v tématu Kompilace konfigurací v konfiguraci stavu automatizace Azure](automation-dsc-compile.md)
- Informace o odkazu na rutinu prostředí PowerShell najdete v tématu [Rutiny konfigurace stavu automatizace Azure](/powershell/module/azurerm.automation/#automation)
- Informace o cenách najdete v [tématu Ceny konfigurace stavu Azure Automation](https://azure.microsoft.com/pricing/details/automation/)
- Příklad použití konfigurace stavu azure automatizace v kanálu průběžného nasazení najdete v [tématu Průběžné nasazení pomocí konfigurace stavu automatizace Azure a Chocolatey](automation-dsc-cd-chocolatey.md)
