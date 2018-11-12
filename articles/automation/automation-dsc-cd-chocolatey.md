---
title: Azure Automation stavu konfigurace průběžného nasazování pomocí Chocolatey
description: Průběžné nasazování DevOps pomocí Správce balíčků konfigurace stavu služby Azure Automation DSC a Chocolatey  Příklad s plnou šablony JSON Resource Manageru a Powershellu zdroje.
services: automation
ms.service: automation
ms.component: dsc
author: bobbytreed
ms.author: robreed
ms.date: 08/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f685b584b701d2772ec5b3915facb97f0d15658a
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51259169"
---
# <a name="usage-example-continuous-deployment-to-virtual-machines-using-automation-state-configuration-and-chocolatey"></a>Příklad použití: Průběžné nasazování pomocí Chocolatey a automatizace stavu konfigurace virtuálních počítačů

Ve světě DevOps celá řada nástrojů, která vám pomůže s různými body v kanálu průběžné integrace. Konfigurace stavu Azure Automation je úvodní novým rozšířením možností, které můžete použít týmy DevOps. Tento článek ukazuje nastavení nahoru průběžného nasazování (CD) pro počítače Windows. Můžete jednoduše rozšířit techniku zahrnout tolik počítačů Windows podle potřeby v roli (webový server, například) a v něm i další role.

![Průběžné nasazování pro virtuální počítače IaaS](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## <a name="at-a-high-level"></a>Na vysoké úrovni

Je poměrně tady děje, ale naštěstí můžete být rozdělenou do dvou hlavních procesů:

- Psaní kódu, testování, vytváření a publikování instalační balíčky pro hlavní verze a podverze systému.
- Vytváření a správa virtuálních počítačů, které budou nainstalovány a spouštění kódu vytvořeného v balíčcích.  

Jakmile jsou obě tyto klíčové procesy na místě, je krátký krok k automatické aktualizaci balíčku spouštěného na jakékoli konkrétního virtuálního počítače, jako jsou vytvoření a nasazení nové verze.

## <a name="component-overview"></a>Přehled komponenty

Správci balíčků, jako [apt-get](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) jsou poměrně dobře známé ve světě Linux, ale ne ve světě Windows.
[Chocolatey](https://chocolatey.org/) je taková věc a Scott Hanselman [blogu](http://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) k danému tématu je vynikající úvod. Řečeno v kostce Chocolatey můžete instalovat balíčky z centrálního úložiště balíčků do systému Windows z příkazového řádku. Můžete vytvářet a spravovat své vlastní úložiště, a Chocolatey balíčky můžete nainstalovat z různých úložišť, které určíte.

Desired State Configuration (DSC) ([přehled](/powershell/dsc/overview)) je nástroj PowerShell, který umožňuje deklarovat konfiguraci, kterou chcete použít pro počítače s. Například můžete napsat, "Chci Chocolatey nainstalovaný, má nainstalovanou službu IIS, chci otevřený port 80, chci, aby verze 1.0.0 svého webu." Správce služby DSC místní konfigurace (LCM) implementuje tuto konfiguraci. DSC serveru vyžádaných replikací s obsahuje úložiště konfigurací vašich počítačů. LCM na každý počítač se zkontroluje pravidelně pokud jeho konfigurace odpovídá uloženou konfiguraci. Můžete buď informuje o stavu nebo pokus o vrácení počítač do zarovnání k uložené konfiguraci. Můžete upravit uloženou konfiguraci na serveru vyžádané replikace způsobit počítače nebo sadu počítačů pro zarovnání se změny konfigurace.

Azure Automation je služba spravovaná v Microsoft Azure, který umožňuje automatizaci různých úloh pomocí runbooků, uzly, přihlašovací údaje, prostředky a prostředky, jako jsou plány a globální proměnné.
Konfigurace stavu automatizace Azure rozšiřuje tyto schopnosti nástroje prostředí PowerShell DSC služby automation. Tady je skvělý [přehled](automation-dsc-overview.md).

Prostředek DSC se modul kódu, který obsahuje specifické možnosti, jako je například Správa sítě služby Active Directory nebo serveru SQL Server. Chocolatey prostředků DSC ví, jak přistupovat k NuGet Server (mimo jiné), stáhněte si balíčky, instalaci balíčků a tak dále. Existuje mnoho dalších prostředků DSC ve [Galerie prostředí PowerShell](http://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title).
Tyto moduly nainstalují do Azure Automation stavu konfigurace o přijetí změn serveru (vámi), se dají ve vaší konfigurace.

Šablony Resource Manageru poskytují deklarativní způsob generování infrastruktury – například sítě, podsítě, zabezpečení sítě a směrování, načítání nástroje pro vyrovnávání, síťové adaptéry, virtuální počítače a tak dále. Tady je [článku](../azure-resource-manager/resource-manager-deployment-model.md) , který porovnává modelu nasazení Resource Manager (deklarativní) pomocí modelu nasazení Azure Service Management (ASM nebo classic) (závazné) a popisuje hlavní poskytovatelé prostředků, výpočetní výkon, úložiště a síť.

Klíčovou vlastností šablony Resource Manageru je jeho schopnost nainstalovat rozšíření virtuálního počítače k virtuálnímu počítači, jako je zřízený. Rozšíření virtuálního počítače má specifické možnosti jako vlastní skript, instalace antivirový software nebo spuštěním skriptu konfigurace DSC. Existuje mnoho dalších typů rozšíření virtuálních počítačů.

## <a name="quick-trip-around-the-diagram"></a>Rychlé jízdy okolo diagramu

Spouští se v horní části, při psaní kódu, kompilují a testují a potom vytvoření instalačního balíčku.
Chocolatey může zpracovávat různé typy instalačních balíčků, jako je například MSI, MSU, ZIP. A máte plný výkon prostředí PowerShell pro případ Chocolateys nativní funkce nejsou zcela až vlastní instalace. Umístěte balíček do někde dostupný – úložiště balíčků. Tento příklad použití používá veřejné složky v účtu úložiště objektů blob v Azure, ale může být kdekoli. Chocolatey spolupracuje nativně servery NuGet a několik dalších pro správu metadata balíčků. [Tento článek](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) popisuje možnosti. Tento příklad použití používá NuGet. Souboru Nuspec jsou metadata informace o balíčcích. Souboru Nuspec jsou "zkompilován" společnosti NuPkg a uloženy v NuGet server. Při konfiguraci požadavků balíčku podle názvu a odkazuje na NuGet server, získá balíček Chocolatey prostředku DSC (nyní ve virtuálním počítači) a nainstaluje za vás. Můžete také požádat o konkrétní verzi balíčku.

V levé dolní části obrázku je šablonu Azure Resource Manageru. V tomto příkladu využití zaregistruje rozšíření virtuálního počítače virtuální počítač s Azure Automation stavu konfigurace o přijetí změn serverem (to znamená, serveru vyžádané replikace) jako uzel. Konfigurace je uložena v tomto serveru.
Ve skutečnosti, uloží se dvakrát: jednou jako prostý text a po kompilaci do souboru MOF (pro ty, které vědět o takové věci.) Na portálu je soubor MOF "konfigurace uzlu" (na rozdíl od jednoduše "konfigurace"). Je artefakt, který je spojen s uzlem, takže vědí, jeho konfigurace uzlu. Podrobnosti níže ukazují, jak přiřadit konfiguraci uzlu k uzlu.

Pravděpodobně již děláte bit v horní části nebo ho většina. Vytvoření souboru nuspec, kompilaci a jeho uložení v NuGet server je malých věcí. A už spravujete virtuální počítače. S ohledem na další krok pro průběžné nasazování vyžaduje nastavení serveru vyžádané replikace (jednou), registrace uzly s ním (jednou) a vytváření a ukládání konfigurace existuje (počáteční). Balíčky jsou upgradovány a nasazené do úložiště aktualizujte konfiguraci a konfigurace uzlu v tomto serveru (opakujte podle potřeby).

Pokud se spouští pomocí šablony Resource Manageru, který je také OK. Jsou navržené tak, aby vám pomůže registrovat své virtuální počítače se serverem o přijetí změn a všechny ostatní rutiny prostředí PowerShell. Další podrobnosti najdete v tomto článku: [připojování počítačů pro správu Azure Automation stavu konfigurace](automation-dsc-onboarding.md).

## <a name="step-1-setting-up-the-pull-server-and-automation-account"></a>Krok 1: Vytvoření uživatelského účtu pro server a automatizace o přijetí změn

Na ověřeného (`Connect-AzureRmAccount`) příkazového řádku prostředí PowerShell: (může trvat několik minut, než se nastavení serveru vyžádané replikace)

    New-AzureRmResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
    New-AzureRmAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT

Můžete umístit svůj účet automation na některou z následujících oblastí (označuje se také jako umístění): východní USA 2, střed USA – Jih, Virginia státní správy USA, západní Evropa, jihovýchodní Asie, Japonsko – východ, střed Indie a Austrálie – jihovýchod, Kanada, – střed, Severní Evropa.

## <a name="step-2-vm-extension-tweaks-to-the-resource-manager-template"></a>Krok 2: Jsme sekce rozšíření virtuálního počítače v šabloně Resource Manageru

Podrobnosti o registraci virtuálního počítače (pomocí rozšíření PowerShell DSC virtuálního počítače) zadaný v tomto [šablonu pro rychlý start Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver).
Tento krok zaregistruje nový virtuální počítač serveru vyžádané replikace v seznamu Stav konfigurace uzlů. Součástí této registraci je určení konfigurace uzlu se použijí k uzlu. Tato konfigurace uzlu nebude muset ještě neexistuje v tomto serveru, takže je v pořádku, který je kroku 4, kde je to poprvé. Ale tady v kroku 2 je potřeba se rozhodli název uzlu a název konfigurace. V tomto příkladu využití uzlu je "isvbox" a "ISVBoxConfig" je konfigurace. Aby se název konfigurace uzlu (aby se zadal v DeploymentTemplate.json) "ISVBoxConfig.isvbox".

## <a name="step-3-adding-required-dsc-resources-to-the-pull-server"></a>Krok 3: Přidání požadované prostředky DSC na serveru vyžádané replikace

Galerie prostředí PowerShell je instrumentováno pro instalaci DSC prostředky ke svému účtu Azure Automation.
Přejděte k prostředku a klikněte na tlačítko "Nasazení do služby Azure Automation".

![Příklad galerie prostředí PowerShell](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

Další technikou naposledy přidaný do portálu Azure můžete získává nové moduly nebo aktualizovat existující moduly. Proklikejte se prostřednictvím prostředků účtu Automation, na dlaždici prostředky a nakonec modulů vedle sebe. Ikona procházet galerii umožňuje zobrazit seznam modulů v galerii, přejít na podrobnosti a nakonec naimportovat do svého účtu služby Automation. To je skvělý způsob, jak udržovat moduly aktuální čas od času. A funkce importu ověří závislosti s ostatními moduly zajistit, že se že nic získá synchronizovaný.

Nebo je ruční přístup. Struktura složek integrace modulu prostředí PowerShell pro počítače Windows se trochu liší od strukturu složek, očekává se službou Azure Automation.
Tento postup vyžaduje trochu úprava z vaší strany. Ale není pevný, a to se provádí jenom jednou pro každý prostředek (Pokud nechcete v budoucnu ho upgradovat.) Další informace o vytváření modulů Powershellu integrace, najdete v tomto článku: [vytváření modulů integrace pro Azure Automation.](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/)

- Nainstalujte modul, který je nutné na pracovní stanice následujícím způsobem:
  - Nainstalujte [Windows Management Framework, verze 5](https://aka.ms/wmf5latest) (nevyžaduje se pro Windows 10)
  - `Install-Module –Name MODULE-NAME`    < – okamžikem vydání modulu z Galerie prostředí PowerShell
- Zkopírujte složku modulu z `c:\Program Files\WindowsPowerShell\Modules\MODULE-NAME` do dočasné složky
- Odstranit ukázky a dokumentaci ve složce hlavní
- Hlavní složku pojmenování souboru ZIP, stejně jako složku ZIP 
- Uložte soubor ZIP do dostupná umístění HTTP, jako je například úložiště objektů blob v účtu služby Azure Storage.
- Spuštěním tohoto prostředí PowerShell:

  ```powershell
  New-AzureRmAutomationModule `
    -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -Name MODULE-NAME –ContentLink 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
  ```

Zahrnuté příklad provádí tyto kroky pro cChoco a xNetworking. Zobrazit [poznámky](#notes) pro zvláštní zacházení cChoco.

## <a name="step-4-adding-the-node-configuration-to-the-pull-server"></a>Krok 4: Přidání konfigurace uzlu na serveru vyžádané replikace

Není nic zvláštního o prvním importujte konfiguraci na serveru vyžádané replikace a kompilace. Všechny následné import/zkompiluje se stejnou konfigurací vypadat přesně tak. Pokaždé, když provedete aktualizaci vašeho balíčku a potřebujete vydat do produkčního prostředí proveďte tento krok, až se ujistíte, že je správný konfigurační soubor – včetně novou verzi balíčku. Tady je konfigurační soubor a prostředí PowerShell:

ISVBoxConfig.ps1:

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
            DisplayGroup = 'IIS Incoming Traffic'
            State        = 'Enabled'
            Access       = 'Allow'
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

Tyto kroky výsledkem novou konfiguraci uzel s názvem "ISVBoxConfig.isvbox" umístěných na tomto serveru. Název konfigurace uzlu je vytvořený jako "configurationName.nodeName".

## <a name="step-5-creating-and-maintaining-package-metadata"></a>Krok 5: Vytvoření a údržbu metadata balíčků

Pro každý balíček, který vložíte do úložiště balíčků budete potřebovat soubor nuspec, které aplikaci popisuje.
Tento soubor nuspec musí být zkompilovány a uloženy na serveru služby NuGet. Tento proces je popsán [tady](http://docs.nuget.org/create/creating-and-publishing-a-package). Webu MyGet.org slouží jako NuGet server. Prodávejte této služby, ale mají starter skladovou Položku, která je zdarma. Na NuGet.org, najdete pokyny k instalaci vlastní server NuGet pro své privátní balíčky.

## <a name="step-6-tying-it-all-together"></a>Krok 6: Propojí to všechno dohromady

Pokaždé, když na verzi předá dotazů a odpovědí a schválení pro nasazení, balíček se vytvoří soubor nuspec a nupkg aktualizovat a nasadit NuGet server. Kromě toho konfigurace (kroku 4 výše) musí být aktualizovány souhlas s nové číslo verze. Musí být odeslán do serveru vyžádané replikace a zkompilovány.
Od této chvíle je to na virtuální počítače, které jsou závislé na tuto konfiguraci k vyžádání aktualizace a nainstalujte ji. Každá z těchto aktualizací jsou jednoduché – stačí na řádek nebo dvě z prostředí PowerShell. V případě Azure DevOps některé z nich jsou zapouzdřeny v úlohách sestavení, které je možné zřetězit v sestavení. To [článku](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery) obsahuje další podrobnosti. To [úložiště GitHub se vzorovými](https://github.com/Microsoft/vso-agent-tasks) podrobnosti o různých dostupných úloh.

## <a name="notes"></a>Poznámky

Tento příklad použití začíná u virtuálního počítače z obecného image Windows serveru 2012 R2 z Galerie Azure. Můžete spustit žádné uložené Image a pak doladit odtud s konfigurací DSC.
Změna konfigurace, která je vloženými do image je však mnohem obtížnější než dynamicky aktualizuje se konfigurace pomocí DSC.

Není nutné tuto techniku použít se vaše virtuální počítače pomocí šablony Resource Manageru a rozšíření virtuálních počítačů. A vaše virtuální počítače nemusí být v Azure a být pod správou disku CD. Vše, co je potřeba je nainstalovat Chocolatey a LCM nakonfigurovaný na virtuálním počítači, aby věděl, kde je serveru vyžádané replikace.

Samozřejmě když aktualizujete balíček ve virtuálním počítači, který je v produkčním prostředí, budete muset provést tento virtuální počítač ze smyčky, při instalaci aktualizace. Tento postup se může výrazně lišit. Například v případě virtuálních počítačů za služby Azure Load Balancer můžete přidat vlastní sondy. Při aktualizaci virtuálního počítače, jste koncový bod vrátit 400. Úprava nezbytné změny může být v rámci konfigurace, jak můžete změnit, přejděte zpět na vrácení 200 po dokončení aktualizace.

Úplný zdrojový pro tento příklad použití je v [tento projekt sady Visual Studio](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) na Githubu.

## <a name="related-articles"></a>Související články
* [Přehled Azure Automation DSC](automation-dsc-overview.md)
* [Rutiny Azure Automation DSC](https://docs.microsoft.com/powershell/module/azurerm.automation#automation)
* [Připojování počítačů pro správu pomocí Azure Automation DSC](automation-dsc-onboarding.md)

## <a name="next-steps"></a>Další postup

- Přehled najdete v tématu [konfigurace stavu služby Azure Automation](automation-dsc-overview.md)
- Abyste mohli začít, najdete v článku [Začínáme s Azure Automation stavu konfigurace](automation-dsc-getting-started.md)
- Další informace o kompilaci konfigurace DSC, takže můžete je přiřadit k cílové uzly, naleznete v tématu [kompilace konfigurací v konfiguraci stavu služby Azure Automation](automation-dsc-compile.md)
- Reference k rutinám Powershellu najdete v části [rutiny Azure Automation stavu konfigurace](/powershell/module/azurerm.automation/#automation)
- Informace o cenách najdete v tématu [ceny konfigurace stavu služby Azure Automation](https://azure.microsoft.com/pricing/details/automation/)
- Příklad použití Azure Automation stav konfigurace v kanálu průběžného nasazování najdete v tématu [nepřetržité nasazení pomocí Azure Automation konfigurace stavu a Chocolatey](automation-dsc-cd-chocolatey.md)