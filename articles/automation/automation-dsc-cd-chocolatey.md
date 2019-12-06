---
title: Azure Automation průběžného nasazování konfigurace stavu pomocí čokolády
description: DevOps průběžné nasazování pomocí konfigurace stavu Azure Automation, DSC a správce balíčků pro čokolády.  Příklad s úplnou šablonou Správce prostředků JSON a zdrojem PowerShellu
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 08/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ddbf652c35c4f1504e3253838a983fd0f6039401
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850359"
---
# <a name="usage-example-continuous-deployment-to-virtual-machines-using-automation-state-configuration-and-chocolatey"></a>Příklad použití: průběžné nasazování do Virtual Machines pomocí konfigurace stavu služby Automation a čokolády

Na DevOps světě máte spoustu nástrojů, které vám pomůžou s různými body v kanálu průběžné integrace. Konfigurace stavu Azure Automation je dobrým novým doplňkem k možnostem, které mohou týmy DevOps využívat. Tento článek ukazuje nastavení průběžného nasazování (CD) pro počítač se systémem Windows. Technika snadno rozšíříte tak, aby obsahovala tolik počítačů s Windows, kolik je potřeba v roli (například web), a odtud i na další role.

![Průběžné nasazování pro virtuální počítače s IaaS](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## <a name="at-a-high-level"></a>Na vysoké úrovni

V tuto chvíli probíhá trochu, ale naštěstí se dá rozdělit do dvou hlavních procesů:

- Zápis a testování kódu a následné vytváření a publikování instalačních balíčků pro hlavní a dílčí verze systému.
- Vytváření a správa virtuálních počítačů, které budou instalovat a spouštět kód v balíčcích.  

Jakmile budou oba tyto základní procesy zavedeny, je to krátký krok k automatické aktualizaci balíčku běžícího na konkrétním virtuálním počítači při vytváření a nasazování nových verzí.

## <a name="component-overview"></a>Přehled komponenty

Správce balíčků, jako je [apt-get](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) , je v systému Linux World dobře známý, ale ne tak daleko ve Windows světě.
Tato věc je taková a Scott Hanselman [blog](https://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) na [téma je skvělý](https://chocolatey.org/) Úvod. V kostce umožňuje čokolády instalovat balíčky z centrálního úložiště balíčků do systému Windows pomocí příkazového řádku. Můžete vytvářet a spravovat vlastní úložiště a čokolády můžou instalovat balíčky z libovolného počtu úložišť, která určíte.

Požadovaná konfigurace stavu (DSC) ([Přehled](/powershell/scripting/dsc/overview/overview)) je nástroj PowerShell, který umožňuje deklarovat konfiguraci, kterou chcete pro počítač. Například můžete říct, že chci nainstalovanou čokoláda, chci nainstalovanou službu IIS, chci mít otevřený port 80, chci nainstalovanou verzi 1.0.0 webu. " Místní Configuration Manager DSC implementuje tuto konfiguraci. Server vyžádané replikace DSC obsahuje úložiště konfigurací pro vaše počítače. LCM v každém počítači pravidelně kontroluje, jestli jeho konfigurace odpovídá uložené konfiguraci. Může buď nahlásit stav, nebo se pokusit převést počítač zpět na zarovnání s uloženou konfigurací. Uloženou konfiguraci můžete upravit na serveru vyžádané replikace a způsobit tak, že počítač nebo sada počítačů vstoupí do souladu se změněnou konfigurací.

Azure Automation je spravovaná služba v Microsoft Azure, která umožňuje automatizovat různé úlohy pomocí runbooků, uzlů, přihlašovacích údajů, prostředků a prostředků, jako jsou plány a globální proměnné.
Konfigurace stavu Azure Automation rozšiřuje tuto možnost automatizace tak, aby zahrnovala nástroje PowerShell DSC. Tady je skvělý [Přehled](automation-dsc-overview.md).

Prostředek DSC je modul kódu, který má konkrétní možnosti, jako je Správa sítě, Active Directory nebo SQL Server. Čokoládový prostředek DSC ví, jak získat přístup k serveru NuGet (mimo jiné), stahovat balíčky, instalovat balíčky a tak dále. V [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title)existuje mnoho dalších prostředků DSC.
Tyto moduly se nainstalují do konfigurace stavu Azure Automation vyžádaného serveru (vámi), takže je můžou použít vaše konfigurace.

Šablony Správce prostředků poskytují deklarativní způsob generování infrastruktury – například sítě, podsítě, zabezpečení sítě a směrování, nástroje pro vyrovnávání zatížení, síťové karty, virtuální počítače atd. Zde je [článek](../azure-resource-manager/resource-manager-deployment-model.md) , který porovnává model nasazení Správce prostředků (deklarativní) s modelem nasazení Azure Service Management (ASM nebo Classic) (imperativně) a popisuje základní poskytovatele prostředků, výpočetní výkon, úložiště a síť.

Jednou z klíčových funkcí šablony Správce prostředků je její schopnost nainstalovat rozšíření virtuálního počítače do virtuálního počítače, protože je zřízené. Rozšíření virtuálních počítačů má konkrétní možnosti, jako je například spuštění vlastního skriptu, instalace antivirového softwaru nebo spuštění konfiguračního skriptu DSC. Existuje mnoho dalších typů rozšíření virtuálních počítačů.

## <a name="quick-trip-around-the-diagram"></a>Rychlá cesta kolem diagramu

Počínaje horním, napíšete kód, sestavíte a otestujete a pak vytvoříte instalační balíček.
Čokoláda může zpracovávat různé typy instalačních balíčků, jako jsou MSI, MSU, ZIP. A máte celou sílu prostředí PowerShell, abyste mohli provádět vlastní instalaci, pokud Chocolateys nativní možnosti. Vložte balíček na místo, které je dostupné – úložiště balíčků. Tento příklad použití používá veřejnou složku v účtu služby Azure Blob Storage, ale může to být kdekoli. Čokoláda funguje nativně se servery NuGet a několik dalších pro správu metadat balíčku. [Tento článek](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) popisuje možnosti. Tento příklad použití používá NuGet. Nuspec je metadata o vašich balíčcích. Nuspec je "kompilované" do NuPkg a uložen na serveru NuGet. Když vaše konfigurace požádá o balíček podle názvu a odkazuje na server NuGet, provedený prostředek DSC (nyní na virtuálním počítači) balíček provede a nainstaluje ho za vás. Můžete si také vyžádat konkrétní verzi balíčku.

V levé dolní části obrázku je Azure Resource Manager šablona. V tomto příkladu je rozšířením virtuálního počítače zaregistrované virtuální počítač pomocí serveru pro vyžádání konfigurace stavu Azure Automation (tj. serveru pull) jako uzel. Konfigurace je uložena na serveru pro vyžádání obsahu.
Ve skutečnosti je uložen dvakrát: jednou jako prostý text a po zkompilování jako soubor MOF (pro ty, které tyto věci znají). Na portálu je MOF "konfigurace uzlu" (na rozdíl od pouhého "konfigurace"). Je to artefakt, který je přidružen k uzlu, takže uzel bude znát jeho konfiguraci. Níže uvedené podrobnosti ukazují, jak přiřadit konfiguraci uzlu k uzlu.

Předpokládá se, že jste už bit prováděli v horní části, nebo na maximum. Vytvoření nuspec, jeho kompilace a uložení na serveru NuGet je malé věci. A už spravujete virtuální počítače. Provedení dalšího kroku průběžného nasazování vyžaduje nastavování serveru pro vyžádání obsahu (jednou), registrace uzlů (jednou) a vytvoření a uložení konfigurace (zpočátku). Poté, co jsou balíčky upgradovány a nasazeny do úložiště, aktualizujte konfiguraci a konfiguraci uzlu na vyžádaném serveru (podle potřeby opakujte).

Pokud nezačínáte šablonou Správce prostředků, je to také OK. K dispozici jsou rutiny prostředí PowerShell, které vám pomůžou zaregistrovat virtuální počítače se serverem Pull a všemi ostatními. Další podrobnosti najdete v tomto článku: [zprovoznění počítačů pro správu podle konfigurace stavu Azure Automation](automation-dsc-onboarding.md).

## <a name="step-1-setting-up-the-pull-server-and-automation-account"></a>Krok 1: nastavení serveru vyžádané replikace a účtu Automation

Na příkazovém řádku ověřeného (`Connect-AzureRmAccount`) PowerShellu: (může trvat několik minut, než se nastavil Server pro vyžádání obsahu)

```azurepowershell-interactive
New-AzureRmResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
New-AzureRmAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT
```

Svůj účet Automation můžete vložit do kterékoli z následujících oblastí (neboli umístění): Východní USA 2, Střed USA – jih, US Gov – Virginie, Západní Evropa, jihovýchodní Asie, Japonsko – východ, Střed Indie a Austrálie – jihovýchod, Kanada – střed, Severní Evropa.

## <a name="step-2-vm-extension-tweaks-to-the-resource-manager-template"></a>Krok 2: vylepšení rozšíření virtuálních počítačů na šablonu Správce prostředků

Podrobnosti o registraci virtuálních počítačů (pomocí rozšíření virtuálního počítače prostředí PowerShell DSC) poskytované v této [šabloně Azure pro rychlý Start](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver).
Tento krok zaregistruje nový virtuální počítač se serverem Pull v seznamu uzlů konfigurace stavu. Součástí této registrace je určení konfigurace uzlu, která se má použít na uzel. Tato konfigurace uzlu nemusí na serveru pro vyžádání obsahu existovat, takže je to v pořádku, pokud se jedná o první krok v kroku 4. Ale tady v kroku 2 se musíte rozhodnout, že jste určili název uzlu a název konfigurace. V tomto příkladu použití je uzel "isvbox" a konfigurace je "ISVBoxConfig". Proto je název konfigurace uzlu (který se má zadat v souboru DeploymentTemplate. JSON) "ISVBoxConfig. isvbox".

## <a name="step-3-adding-required-dsc-resources-to-the-pull-server"></a>Krok 3: přidání požadovaných prostředků DSC na server vyžádané replikace

Galerie prostředí PowerShell se instrumentuje pro instalaci prostředků DSC do účtu Azure Automation.
Přejděte k požadovanému prostředku a klikněte na tlačítko nasadit do Azure Automation.

![Příklad Galerie prostředí PowerShell](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

Další postup, který jste nedávno přidali na portál Azure Portal, vám umožní vyžádat si nové moduly nebo aktualizovat existující moduly. Klikněte na prostředek účtu Automation, dlaždice Assety a nakonec na dlaždici moduly. Ikona procházet galerii vám umožní zobrazit seznam modulů v galerii, přejít k podrobnostem a nakonec je importovat do svého účtu Automation. Jedná se o skvělý způsob, jak uchovávat moduly v aktuálním stavu v čase. Funkce Import ale kontroluje závislosti s ostatními moduly, aby se zajistilo, že se nic nestane synchronizované.

Nebo, existuje ruční přístup. Struktura složek modulu integrace PowerShellu pro počítač s Windows se trochu liší od struktury složek, kterou očekává Azure Automation.
K tomu je potřeba mít na vaší straně trochu vylepšení. Není to ale těžké a u každého prostředku se provádí jenom jednou (pokud ho nechcete v budoucnu upgradovat). Další informace o vytváření modulů integrace PowerShellu najdete v tomto článku: [vytváření modulů integrace pro Azure Automation](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/)

- Nainstalujte modul, který budete potřebovat na pracovní stanici, takto:
  - Instalace [rozhraní Windows Management Framework, verze 5](https://aka.ms/wmf5latest) (není nutné pro Windows 10)
  - `Install-Module –Name MODULE-NAME` < – předaný modul z Galerie prostředí PowerShell
- Zkopírujte složku modulu z `c:\Program Files\WindowsPowerShell\Modules\MODULE-NAME` do dočasné složky.
- Odstranění ukázek a dokumentace z hlavní složky
- Hlavní složka zip, pojmenování souboru ZIP přesně stejné jako složka 
- Soubor ZIP vložte do dosažitelného umístění HTTP, jako je například BLOB Storage v účtu Azure Storage.
- Spusťte tento PowerShell:

  ```powershell
  New-AzureRmAutomationModule `
    -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -Name MODULE-NAME –ContentLink 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
  ```

Zahrnutý příklad provádí tyto kroky pro cChoco a xNetworking. Další informace najdete v [poznámkách](#notes) ke speciálnímu zpracování pro cChoco.

## <a name="step-4-adding-the-node-configuration-to-the-pull-server"></a>Krok 4: Přidání konfigurace uzlu na server vyžádané replikace

Při prvním importu konfigurace do vyžádaného serveru a zkompilování není nic speciální. Všechny následné importy a kompilace stejné konfigurace vypadají přesně stejně. Pokaždé, když balíček aktualizujete a potřebujete ho nabízet do produkčního prostředí, provedete tento krok až po ověření správnosti konfiguračního souboru – včetně nové verze balíčku. Tady je konfigurační soubor a PowerShell:

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

Výsledkem těchto kroků je nová konfigurace uzlu s názvem "ISVBoxConfig. isvbox", která se umístí na server vyžádané replikace. Název konfigurace uzlu je sestaven jako "configurationName. Node".

## <a name="step-5-creating-and-maintaining-package-metadata"></a>Krok 5: vytváření a údržba metadat balíčku

Pro každý balíček, který vložíte do úložiště balíčků, potřebujete nuspec, který ho popisuje.
Tento nuspec musí být zkompilován a uložen na vašem serveru NuGet. Tento proces je popsán [zde](https://docs.nuget.org/create/creating-and-publishing-a-package). MyGet.org můžete použít jako server NuGet. Prodávají tuto službu, ale mají bezplatnou SKU Starter. V NuGet.org najdete pokyny k instalaci vlastního serveru NuGet pro vaše soukromé balíčky.

## <a name="step-6-tying-it-all-together"></a>Krok 6: Propojte všechno dohromady

Pokaždé, když verze projde QA a je schválená pro nasazení, vytvoří se balíček, nuspec a nupkg se aktualizuje a nasadí na server NuGet. Kromě toho musí být konfigurace (krok 4 výše) aktualizována tak, aby souhlasila s novým číslem verze. Musí se odeslat na server vyžádané replikace a zkompilovat ho.
Od tohoto okamžiku se nachází na virtuálních počítačích, které na této konfiguraci závisí, aby se aktualizace vyčetla a nainstalovala. Každá z těchto aktualizací je jednoduchá – jenom čára nebo dva prostředí PowerShell. V případě Azure DevOps jsou některé z nich zapouzdřeny v úlohách sestavení, které lze zřetězit společně v sestavení. V tomto [článku](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery) najdete další podrobnosti. Toto [úložiště GitHub](https://github.com/Microsoft/vso-agent-tasks) podrobně popisuje různé dostupné úlohy sestavení.

## <a name="notes"></a>Poznámky

Tento příklad použití začíná virtuálním počítačem z obecné image Windows Serveru 2012 R2 z Galerie Azure. Můžete začít z libovolné uložené image a pak je upravit pomocí konfigurace DSC.
Změna konfigurace, která je vloženými na image, je ale mnohem obtížnější než dynamická aktualizace konfigurace pomocí DSC.

K použití této techniky u virtuálních počítačů nemusíte používat šablonu Správce prostředků a rozšíření virtuálního počítače. A vaše virtuální počítače nemusí být v Azure, aby byly v rámci správy disků CD. To je nezbytné v případě, že je tato čokoláda nainstalována a pracuje na virtuálním počítači, aby znala, kde je server vyžádané replikace.

Samozřejmě když aktualizujete balíček na virtuálním počítači, který je v produkčním prostředí, budete muset při instalaci aktualizace tento virtuální počítač vyřídit mimo rotaci. To se značně liší. Například s virtuálním počítačem za Azure Load Balancer můžete přidat vlastní test. Když aktualizujete virtuální počítač, vrátí koncový bod testu 400. K tomu, aby bylo možné tuto změnu provést, se může v konfiguraci nacházet v rámci konfigurace, protože je možné je upravit tak, aby vracela 200 po dokončení aktualizace.

Úplný zdroj pro tento příklad použití je v [tomto projektu sady Visual Studio](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) na GitHubu.

## <a name="related-articles"></a>Související články
* [Přehled Azure Automation DSC](automation-dsc-overview.md)
* [Azure Automation rutin DSC](https://docs.microsoft.com/powershell/module/azurerm.automation#automation)
* [Připojování počítačů pro správu pomocí Azure Automation DSC](automation-dsc-onboarding.md)

## <a name="next-steps"></a>Další kroky

- Přehled najdete v tématu [Konfigurace stavu Azure Automation](automation-dsc-overview.md) .
- Informace o tom, jak začít, najdete v tématu [Začínáme s konfigurací stavu Azure Automation](automation-dsc-getting-started.md) .
- Další informace o kompilaci konfigurací DSC, abyste je mohli přiřadit cílovým uzlům, najdete v tématu [kompilace konfigurací v konfiguraci stavu Azure Automation](automation-dsc-compile.md)
- Referenční informace k rutinám PowerShellu najdete v tématu [rutiny konfigurace stavu Azure Automation](/powershell/module/azurerm.automation/#automation) .
- Informace o cenách najdete v tématu [Azure Automation ceny konfigurace stavu](https://azure.microsoft.com/pricing/details/automation/) .
- Příklad použití konfigurace stavu Azure Automation v kanálu průběžného nasazování najdete v tématu [průběžné nasazování pomocí Azure Automation konfigurace stavu a čokolády](automation-dsc-cd-chocolatey.md) .
