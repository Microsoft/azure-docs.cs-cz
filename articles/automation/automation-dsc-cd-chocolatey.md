---
title: Průběžné nasazování služby Azure Automation DSC s Chocolatey
description: DevOps průběžné nasazování pomocí Azure Automation DSC a správce Chocolatey balíčků.  Příklad s kompletní šablonou JSON ARM a zdroj PowerShell.
services: automation
ms.service: automation
ms.component: dsc
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 9c3cb5a00433a76e8cc444cc48c648cb2749a2ae
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="usage-example-continuous-deployment-to-virtual-machines-using-automation-dsc-and-chocolatey"></a>Příklad použití: Průběžné nasazování pro virtuální počítače pomocí Automation DSC a Chocolatey
Ve světě DevOps jsou celou řadu nástrojů pro pomoc s různými body v kanálu nepřetržité integrace.  Konfigurace Azure Automation žádaný stavu (DSC) je úvodní nové přidání možnosti, které můžete použít DevOps týmy.  Tento článek ukazuje nastavení nahoru průběžné nasazení (CD) pro počítač se systémem Windows.  Můžete snadno rozšířit techniku, například jako v mnoha počítače se systémem Windows v případě potřeby v roli (webový server, např.) a z ní také další rolí.

![Průběžné nasazování pro virtuální počítače IaaS](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## <a name="at-a-high-level"></a>Na vysoké úrovni
Je celkem chvilku přechodem sem, ale naštěstí možné ho rozdělit do dvou hlavních procesů: 

* Psaní kódu a jeho otestováním, pak vytváření a publikování instalační balíčky pro hlavní verze a podverze systému. 
* Vytváření a správa virtuálních počítačů, které budou nainstalovány a spouštění kódu v balíčcích.  

Jakmile jsou obě tyto klíčové procesy v místě, je krátký krok automaticky aktualizovat balíček systémem žádné konkrétní virtuální počítače, jako jsou vytvoření a nasazení nových verzí.

## <a name="component-overview"></a>– Přehled komponenty
Balíček správce například [výstižný get](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) jsou poměrně dobře známé v celém světě Linux, ale není tak většinu na světě systému Windows.  [Chocolatey](https://chocolatey.org/) je taková věc a Scott Hanselman [blog](http://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) v tomto tématu je skvělým úvod.  Stručně řečeno Chocolatey umožňuje instalovat balíčky z centrální úložiště balíčků do systému Windows pomocí příkazového řádku.  Můžete vytvořit a spravovat vlastní úložiště, a Chocolatey mohou instalovat balíčky z libovolný počet úložiště, které určíte.

Požadovaného konfigurace stavu (DSC) ([přehled](https://technet.microsoft.com/library/dn249912.aspx)) je nástroj prostředí PowerShell, který umožňuje konfiguraci, kterou chcete použít pro počítače s deklarovat.  Řekněme například, je možné, "Chci, aby byla nainstalována Chocolatey, chci, aby byla nainstalována služba IIS, má být otevřený port 80, chci, aby byla verze 1.0.0 svého webu."  DSC správce pro místní konfigurace (LCM) implementuje tuto konfiguraci. DSC pro vyžádání obsahu Server obsahuje úložiště konfigurace pro počítače. LCM na každém počítači zkontroluje pravidelně Pokud odpovídá jeho konfigurace uložené konfiguraci. Můžete buď sestavy stavu nebo pokus o přeneste počítač zpět do zarovnání uložené konfiguraci. Můžete upravit konfiguraci uložené na tomto serveru způsobí počítače nebo sada počítačů začalo zarovnání změněné konfigurace.

Služby Azure Automation je spravovaná služba ve službě Microsoft Azure, který umožňuje automatizovat různé úlohy pomocí sady runbook, uzly, přihlašovací údaje, prostředky a prostředky, jako je například plány a globální proměnné. Azure Automation DSC rozšiřuje tyto schopnosti automation DSC prostředí PowerShell nástroje.  Tady je skvělá [přehled](automation-dsc-overview.md).

Prostředek DSC je modul kódu, který má specifické možnosti, jako je například Správa sítě služby Active Directory nebo SQL Server.  Chocolatey prostředek DSC umí přístup k serveru NuGet (mimo jiné), stáhněte balíčky, instalovat balíčky a tak dále.  Existuje mnoho dalších prostředky DSC v [Galerie prostředí PowerShell](http://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title).  Tyto moduly jsou nainstalovány do Azure Automation DSC pro vyžádání obsahu serveru (které jste), je možné použít vaše konfigurace.

Šablony Resource Manageru poskytnout deklarativní způsob generování infrastruktury - věcmi, jako jsou sítě, podsítě, zabezpečení sítě a směrování, zatížení nástroje pro vyrovnávání, síťové adaptéry, virtuální počítače a tak dále.  Tady je [článku](../azure-resource-manager/resource-manager-deployment-model.md) který porovná modelu nasazení Resource Manager (deklarativní) s Azure Service Management (ASM nebo classic) nasazení modelu (imperativní) a popisuje zprostředkovatelé prostředků jádra, výpočty, úložiště a sítě.

Klíčovou vlastností šablony správce prostředků je schopnost nainstalovat rozšíření virtuálního počítače do virtuálního počítače, jako je zřízený.  Rozšíření virtuálního počítače má specifické možnosti, například při spuštění vlastního skriptu, instalaci antivirového softwaru nebo spouštění skriptu konfigurace DSC.  Existuje mnoho dalších typů rozšíření virtuálního počítače.

## <a name="quick-trip-around-the-diagram"></a>Rychlá cesta kolem diagramu
Spouštění v horní části, psaní kódu, sestavení a testování a pak vytvořit instalační balíček.  Chocolatey může zpracovávat různých typů instalačních balíčků, jako je například MSI, MSU, ZIP.  A budete mít úplnou prostředí PowerShell pro případ Chocolatey na nativní funkce nejsou poměrně až vlastní instalace.  Uvést balíček do někde dosažitelný – úložiště balíčků.  Tento příklad použití používá do veřejné složky v účtu úložiště objektů blob v Azure, ale může být kdekoli.  Chocolatey funguje nativně se servery NuGet a některých jiných pro správu metadata balíčků.  [Tento článek](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) popisuje možnosti.  Tento příklad použití používá NuGet.  Nuspec je metadata o vašich balíčků.  Nuspec jsou "zkompilovat" do na NuPkg a uloženo na serveru NuGet.  Při konfiguraci požadavky balíček podle názvu a odkazuje na NuGet server, získá balíček Chocolatey prostředek DSC (nyní ve virtuálním počítači) a nainstaluje za vás.  Také můžete vyžádat na konkrétní verzi balíčku.

V dolní části levého obrázku je šablonu Azure Resource Manager (ARM).  V tomto příkladu využití zaregistruje rozšíření virtuálního počítače virtuální počítač s Server Azure Automation DSC za (tedy vyžádání obsahu server) jako uzel.  Konfigurace je uložena na serveru vyžádané replikace.  Ve skutečnosti, uloží se dvakrát: jednou jako prostý text a jakmile zkompilován jako soubor MOF (pro ty, které vědět o takové věci.)  Na portálu je MOF "konfigurace uzlu" (na rozdíl od jednoduše "konfigurace").  Je artefaktu, který je spojen s uzlem, tak uzel věděli, její konfiguraci.  Níže uvedené podrobnosti ukazují, jak přiřadit konfigurace uzlu do uzlu.

Pravděpodobně již provádíte bit v horní části nebo většinu ho.  Soubor nuspec vytváření, kompilace a ukládání na serveru NuGet je malý věcí.  A už spravujete virtuální počítače.  Provedením dalšího kroku k průběžné nasazování vyžaduje nastavení (jednou) načítacího serveru, registrace uzly s ním (jednou) a vytváření a ukládání konfigurace došlo (původně).  Potom jako balíčky jsou upgradovány a nasazeny do úložiště aktualizujte konfiguraci a konfigurace uzlu na vyžádání serveru (opakování podle potřeby).

Pokud nejsou od verze šablony ARM, který je také OK.  Jsou navržené tak, aby vám pomůže registrovat virtuální počítače se serverem pro vyžádání obsahu a všechny ostatní rutiny prostředí PowerShell. Další podrobnosti najdete v tématu v tomto článku: [registrace počítačů pro správu Azure Automation DSC.](automation-dsc-onboarding.md)

## <a name="step-1-setting-up-the-pull-server-and-automation-account"></a>Krok 1: Vytvoření uživatelského účtu pro server a automatizace vyžádání obsahu
V příkazovém řádku prostředí PowerShell (Connect-AzureRmAccount) ověření: (může trvat několik minut vyžádání obsahu server je nastavený)

    New-AzureRmResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
    New-AzureRmAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT 

Můžete vložit účtu automation do některé z následujících oblastí (neboli umístění): východní USA 2, Jižní střední USA, Virginia nám verze pro státní správu, západní Evropa, jihovýchodní Asie, Japonsko – východ, Indie centrální a Austrálie – jihovýchod, Střední Kanada, Severní Evropa.

## <a name="step-2-vm-extension-tweaks-to-the-arm-template"></a>Krok 2: Virtuálního počítače rozšíření vylepšení do šablony ARM
Podrobnosti registrace virtuálních počítačů (pomocí rozšíření virtuálního počítače DSC prostředí PowerShell) zadaný v tomto [šablony rychlý start Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver).  Tento krok zaregistruje nový virtuální počítač se serverem pro vyžádání obsahu v seznamu uzlů DSC.  Součástí této registrace je zadání konfigurace uzlu, který bude použit na uzlu.  Tato konfigurace uzlu nemusí být ještě neexistuje na serveru vyžádané replikace, takže je OK, který krok 4 je, kde je to provést první.  Ale zde v kroku 2 je potřeba jste se rozhodli název uzlu a názvu konfigurace.  V tomto příkladu využití uzlu je 'isvbox' a 'ISVBoxConfig' je v konfiguraci.  Proto se název konfigurace uzlu (Chcete-li být zadané v DeploymentTemplate.json) 'ISVBoxConfig.isvbox'.  

## <a name="step-3-adding-required-dsc-resources-to-the-pull-server"></a>Krok 3: Přidání požadované prostředky DSC na server vyžádané replikace
Galerie prostředí PowerShell je instrumentována instalace prostředků DSC do účtu Azure Automation.  Přejděte k prostředku a klikněte na tlačítko "Nasadit do Azure Automation".

![Příklad galerie prostředí PowerShell](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

Jiné technika naposledy přidaný do portálu Azure můžete stáhnout nové moduly nebo aktualizovat existující moduly. Proklikejte se prostřednictvím prostředků účtu Automation, dlaždici prostředky a nakonec na dlaždici moduly.  Ikona procházet galerii umožňuje zobrazit seznam modulů v galerii, Rozbalit podrobnosti a nakonec naimportovat do vašeho účtu Automation. Toto je skvělý způsob, jak udržovat moduly aktuální čas od času. A funkce importu zkontroluje závislosti s zajistit, že nic získá synchronizované z ostatních modulů.

Nebo je ruční metodu.  Struktura složek modulu integrace prostředí PowerShell pro počítač se systémem Windows se mírně liší od struktura složek očekávanou Azure Automation.  To vyžaduje trochu postupně je upravujte z vaší strany.  Ale není pevný a jeho se provádí jenom jednou za prostředků (Pokud chcete upgradovat v budoucnosti.)  Další informace o vytváření moduly integrace prostředí PowerShell, najdete v tomto článku: [vytváření modulů integrace pro Azure Automation.](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/)

* Nainstalujte modul, který je nutné na pracovní stanici, následujícím způsobem:
  * Nainstalujte [Windows Management Framework, verze 5](http://aka.ms/wmf5latest) (nejsou potřeba pro Windows 10)
  * `Install-Module –Name MODULE-NAME`    < – získá modul z Galerie Powershellu 
* Zkopírujte složku modul z `c:\Program Files\WindowsPowerShell\Modules\MODULE-NAME` do dočasné složky 
* Ukázky a dokumentace odstranit z hlavní složky 
* ZIP složce hlavní názvy souboru ZIP, stejně jako složka 
* Soubor ZIP uvést do dosažitelný umístění HTTP, jako je například úložiště objektů blob v účtu úložiště Azure.
* Spusťte tuto prostředí PowerShell:
  
      New-AzureRmAutomationModule `
          -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
          -Name MODULE-NAME –ContentLink "https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip"

Zahrnuté příklad provádí tyto kroky pro cChoco a xNetworking. Najdete v článku [poznámky](#notes) pro zvláštní zpracování pro cChoco.

## <a name="step-4-adding-the-node-configuration-to-the-pull-server"></a>Krok 4: Přidání konfigurace uzlu na server vyžádané replikace
Není co speciální o prvním importovat konfiguraci na vyžádání obsahu server a kompilaci.  Všechny následné importu/zkompiluje se stejnou konfigurací vypadat přesně stejně.  Pokaždé, když vaše balíček aktualizace a muset poslat ho do produkčního prostředí proveďte tento krok, poté, co ověříte, že je konfigurační soubor správný – včetně novou verzi vašeho balíčku.  Tady je konfigurační soubor a prostředí PowerShell:

ISVBoxConfig.ps1:

    Configuration ISVBoxConfig 
    { 
        Import-DscResource -ModuleName cChoco 
        Import-DscResource -ModuleName xNetworking

        Node "isvbox" {   

            cChocoInstaller installChoco 
            { 
                InstallDir = "C:\choco" 
            }

            WindowsFeature installIIS 
            { 
                Ensure="Present" 
                Name="Web-Server" 
            }

            xFirewall WebFirewallRule 
            { 
                Direction = "Inbound" 
                Name = "Web-Server-TCP-In" 
                DisplayName = "Web Server (TCP-In)" 
                Description = "IIS allow incoming web site traffic." 
                DisplayGroup = "IIS Incoming Traffic" 
                State = "Enabled" 
                Access = "Allow" 
                Protocol = "TCP" 
                LocalPort = "80" 
                Ensure = "Present" 
            }

            cChocoPackageInstaller trivialWeb 
            {            
                Name = "trivialweb" 
                Version = "1.0.0" 
                Source = “MY-NUGET-V2-SERVER-ADDRESS” 
                DependsOn = "[cChocoInstaller]installChoco", 
                "[WindowsFeature]installIIS" 
            } 
        }    
    }

New-ConfigurationScript.ps1:

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

Tyto kroky výsledek v konfiguraci se nový uzel s názvem "ISVBoxConfig.isvbox" je uskutečnění na tomto serveru.  Název konfigurace uzlu je vytvořena jako "configurationName.nodeName".

## <a name="step-5-creating-and-maintaining-package-metadata"></a>Krok 5: Vytvoření a údržbu metadata balíčků
Pro každý balíček, který vložíte do úložiště balíčků budete potřebovat soubor nuspec, s popisem.  Tento soubor nuspec, je nutné zkompilovat a uložit na vašem serveru NuGet. Tento proces je popsán [zde](http://docs.nuget.org/create/creating-and-publishing-a-package).  MyGet.org slouží jako NuGet server.  Prodeje této služby, ale mají starter SKU, které je právě volné.  V NuGet.org najdete pokyny k instalaci NuGet server pro soukromé balíčky.

## <a name="step-6-tying-it-all-together"></a>Krok 6: Příkazů je všechny najednou
Pokaždé, když na verzi předá QA a je schválená pro nasazení, balíček je vytvořen, nuspec a nupkg aktualizován a nasazeny na server NuGet.  Kromě toho konfigurace (kroku 4 výše) musí být aktualizovány na Souhlasím s nimi nové číslo verze.  Musí být odeslány na server vyžádané replikace a zkompilovat.  Od tohoto okamžiku je to na virtuální počítače, které závisí na tato konfigurace aktualizace pro vyžádání obsahu a nainstalujte ji.  Všechny tyto aktualizace jsou jednoduché - právě řádku nebo dva prostředí PowerShell.  V případě Visual Studio Team Services některé z nich zapouzdřené v sestavení úlohy, které může být zřetězen dohromady v sestavení.  To [článku](https://www.visualstudio.com/en-us/docs/alm-devops-feature-index#continuous-delivery) obsahuje další podrobnosti.  To [úložiště GitHub](https://github.com/Microsoft/vso-agent-tasks) podrobnosti různé úlohy dostupné sestavení.

## <a name="notes"></a>Poznámky
Tento příklad použití začíná virtuální počítač z bitové kopie obecné Windows Server 2012 R2 z Galerie Azure.  Můžete spustit z jakékoli uložené image a pak upravit odtud s konfigurací DSC.  Změna konfigurace, která je zaručená do bitové kopie, je však mnohem složitější než dynamicky aktualizuje se konfigurace pomocí DSC.

Nemáte pomocí šablony ARM a rozšíření virtuálního počítače můžete použít tento postup pomocí vašich virtuálních počítačů.  A virtuální počítače nemusí být v Azure jako v části Správa disku CD.  Všechno, co je potřeba je nainstalované Chocolatey a LCM nakonfigurovaný na virtuálním počítači, takže bude vědět, kde je server pro vyžádání obsahu.  

Samozřejmě když aktualizujete balíček ve virtuálním počítači, který je v produkčním prostředí, budete muset provést tohoto virtuálního počítače mimo otočení při instalaci aktualizace.  Tento postup se výrazně liší.  Například v případě virtuálních počítačů za pro vyrovnávání zatížení Azure, můžete přidat vlastní Probe.  Při aktualizaci virtuálního počítače, mají koncový bod testu vrátit 400.  TweakUI potřeba způsobit, že tato změna může být v konfiguraci, jakou TweakUI přepnout zpět na vrácení 200 až po dokončení aktualizace.

Úplný zdrojový v tomto příkladu využití je v [tento projekt sady Visual Studio](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) na Githubu.

## <a name="related-articles"></a>Související články
* [Přehled služby Azure Automation DSC](automation-dsc-overview.md)
* [Rutiny Azure Automation DSC](https://msdn.microsoft.com/library/mt244122.aspx)
* [Registrace počítačů pro správu Azure Automation DSC.](automation-dsc-onboarding.md)

