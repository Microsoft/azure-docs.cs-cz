---
title: Nastavení Azure Automation průběžného nasazování s čokoládou
description: V tomto článku se dozvíte, jak nastavit průběžné nasazování s konfigurací stavu a správcem balíčků pro čokolády.
services: automation
ms.subservice: dsc
ms.date: 08/08/2018
ms.topic: conceptual
ms.custom: references_regions
ms.openlocfilehash: bb5f7b5e8214bd3b04bd7b9544ab4bc589f6c4bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98896321"
---
# <a name="set-up-continuous-deployment-with-chocolatey"></a>Nastavení průběžného nasazování s využitím Chocolatey

V DevOps světě existuje mnoho nástrojů, které vám pomůžou s různými body v kanálu průběžné integrace. [Konfigurace stavu](automation-dsc-overview.md) Azure Automation je dobrým novým doplňkem k možnostem, které mohou týmy DevOps využívat. 

Azure Automation je spravovaná služba v Microsoft Azure, která umožňuje automatizovat různé úlohy pomocí runbooků, uzlů a sdílených prostředků, jako jsou například přihlašovací údaje, plány a globální proměnné. Konfigurace stavu Azure Automation rozšiřuje tuto možnost automatizace tak, aby zahrnovala nástroje PowerShellu pro konfiguraci požadovaných stavů (DSC). Tady je skvělý [Přehled](automation-dsc-overview.md).

Tento článek ukazuje, jak nastavit průběžné nasazování (CD) pro počítač se systémem Windows. Technika snadno rozšíříte tak, aby obsahovala tolik počítačů s Windows, kolik je potřeba v roli, například web, a přejít z něj na další role.

![Průběžné nasazování pro virtuální počítače s IaaS](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## <a name="at-a-high-level"></a>Na vysoké úrovni

Tady je něco, co se vám bude líbit, ale naštěstí se dá rozdělit na dva hlavní procesy:

- Zápis a testování kódu a následné vytváření a publikování instalačních balíčků pro hlavní a dílčí verze systému.
- Vytváření a správa virtuálních počítačů, které instalují a spouštějí kód v balíčcích.  

Jakmile budou oba tyto základní procesy zavedeny, je snadné aktualizovat balíček na vašich virtuálních počítačích automaticky, protože se vytvářejí a nasazují nové verze.

## <a name="component-overview"></a>Přehled komponenty

Správce balíčků, jako je [apt-get](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) , je dobře známý na světě Linux, ale ne tak daleko ve Windows světě.
Tato věc je taková a Scott Hanselman [blog](https://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) na [téma je skvělý](https://chocolatey.org/) Úvod. V kostce umožňuje čokolády pomocí příkazového řádku k instalaci balíčků z centrálního úložiště do operačního systému Windows. Můžete vytvářet a spravovat vlastní úložiště a čokolády můžou instalovat balíčky z libovolného počtu úložišť, která určíte.

[POWERSHELL DSC](/powershell/scripting/dsc/overview/overview) je nástroj PowerShell, který umožňuje deklarovat konfiguraci, kterou chcete pro počítač. Například pokud chcete, aby se nainstalovaná čokoláda, nainstalovaná služba IIS, port 80 a 1.0.0 verze vašeho webu, implementuje místní Configuration Manager DSC tuto konfiguraci. Server vyžádané replikace DSC obsahuje úložiště konfigurací pro vaše počítače. LCM v každém počítači pravidelně kontroluje, jestli jeho konfigurace odpovídá uložené konfiguraci. Může buď nahlásit stav, nebo se pokusit převést počítač zpět na zarovnání s uloženou konfigurací. Uloženou konfiguraci můžete upravit na serveru vyžádané replikace a způsobit tak, že počítač nebo sada počítačů vstoupí do souladu se změněnou konfigurací.

Prostředek DSC je modul kódu, který má konkrétní možnosti, jako je Správa sítě, Active Directory nebo SQL Server. Čokoládový prostředek DSC ví, jak získat přístup k serveru NuGet (mimo jiné), stahovat balíčky, instalovat balíčky a tak dále. V [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title)existuje mnoho dalších prostředků DSC. Tyto moduly nainstalujete do konfigurace stavu Azure Automation vyžádaného serveru pro použití ve vašich konfiguracích.

Šablony Správce prostředků poskytují deklarativní způsob generování infrastruktury, například sítě, podsítě, zabezpečení sítě a směrování, nástroje pro vyrovnávání zatížení, síťové karty, virtuální počítače atd. Zde je [článek](../azure-resource-manager/management/deployment-models.md) , který porovnává model nasazení Správce prostředků (deklarativní) s modelem nasazení Azure Service Management (ASM nebo Classic) (imperativně). Tento článek obsahuje diskuzi o základních poskytovatelích prostředků: výpočetní prostředky, úložiště a síť.

Jednou z klíčových funkcí šablony Správce prostředků je její schopnost nainstalovat rozšíření virtuálního počítače do virtuálního počítače, protože je zřízené. Rozšíření virtuálních počítačů má konkrétní možnosti, jako je například spuštění vlastního skriptu, instalace antivirového softwaru a spuštění konfiguračního skriptu DSC. Existuje mnoho dalších typů rozšíření virtuálních počítačů.

## <a name="quick-trip-around-the-diagram"></a>Rychlá cesta kolem diagramu

Od začátku můžete napsat kód, sestavit ho, otestovat ho a pak vytvořit instalační balíček. Čokoláda může zpracovávat různé typy instalačních balíčků, jako jsou MSI, MSU, ZIP. A máte celou sílu prostředí PowerShell, abyste mohli provádět vlastní instalaci, pokud nejsou k dispozici nativní funkce čokolády. Vložte balíček na místo, které je dostupné – úložiště balíčků. Tento příklad použití používá veřejnou složku v účtu služby Azure Blob Storage, ale může to být kdekoli. Čokoláda funguje nativně se servery NuGet a několik dalších pro správu metadat balíčku. [Tento článek](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) popisuje možnosti. Příklad použití používá NuGet. Nuspec je metadata o vašich balíčcích. Informace nuspec jsou kompilovány do NuPkg a uloženy na serveru NuGet. Když vaše konfigurace požaduje balíček podle názvu a odkazuje na server NuGet, prostředek DSC ve VIRTUÁLNÍm počítači prochází balíček a nainstaluje ho. Můžete si také vyžádat konkrétní verzi balíčku.

V levém dolním rohu obrázku je Azure Resource Manager šablona. V tomto příkladu je rozšířením virtuálního počítače zaregistrované virtuální počítač pomocí serveru Pull konfigurace stavu Azure Automation jako uzel. Konfigurace je uložena na serveru vyžádané replikace dvakrát: jednou jako prostý text a po zkompilování jako soubor MOF. V Azure Portal MOF představuje konfiguraci uzlu, a to na rozdíl od jednoduché konfigurace. Je to artefakt, který je přidružen k uzlu, takže uzel bude znát jeho konfiguraci. Níže uvedené podrobnosti ukazují, jak přiřadit konfiguraci uzlu k uzlu.

Vytvoření nuspec, jeho kompilování a uložení na serveru NuGet je malé věci. A už spravujete virtuální počítače. 

Provedení dalšího kroku průběžného nasazování vyžaduje, abyste Server pro vyžádání obsahu nastavili jednou, zaregistrovali jste uzly s tím, že jednou zaregistrujete a uložíte počáteční konfiguraci na server. Když se balíčky upgradují a nasazují do úložiště, stačí podle potřeby aktualizovat konfiguraci a konfiguraci uzlů na serveru pro vyžádání obsahu.

Pokud nezačínáte šablonou Správce prostředků, je to v pořádku. K dispozici jsou příkazy prostředí PowerShell, které vám pomůžou zaregistrovat virtuální počítače pomocí serveru pro vyžádání obsahu. Další informace najdete v tématu věnovaném [připojování počítačů pro správu podle konfigurace stavu Azure Automation](automation-dsc-onboarding.md).

## <a name="about-the-usage-example"></a>O příkladu použití

Příklad použití v tomto článku začíná virtuálním počítačem z obecné image Windows Serveru 2012 R2 z Galerie Azure. Můžete začít z libovolné uložené image a pak je upravit pomocí konfigurace DSC.
Změna konfigurace, která je vloženými na image, je ale mnohem obtížnější než dynamická aktualizace konfigurace pomocí DSC.

K použití této techniky u virtuálních počítačů nemusíte používat šablonu Správce prostředků a rozšíření virtuálního počítače. A vaše virtuální počítače nemusí být v Azure, aby byly v rámci správy disků CD. To je nezbytné v případě, že je tato čokoláda nainstalována a pracuje na virtuálním počítači, aby znala, kde je server vyžádané replikace.

Když aktualizujete balíček na virtuálním počítači, který je v produkčním prostředí, musíte tento virtuální počítač při instalaci aktualizace přenést z provozu. To se značně liší. Například s virtuálním počítačem za Azure Load Balancer můžete přidat vlastní test. Když aktualizujete virtuální počítač, vrátí koncový bod testu 400. K tomu, aby bylo možné tuto změnu provést, se může v konfiguraci nacházet v rámci konfigurace, protože je možné je upravit tak, aby vracela 200 po dokončení aktualizace.

Úplný zdroj pro tento příklad použití je v [tomto projektu sady Visual Studio](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) na GitHubu.

## <a name="step-1-set-up-the-pull-server-and-automation-account"></a>Krok 1: nastavení serveru pro vyžádané replikace a účtu Automation

Na příkazovém řádku ověřeného ( `Connect-AzAccount` ) PowerShellu: (může trvat několik minut, než se nastavil Server pro vyžádání obsahu)

```azurepowershell-interactive
New-AzResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
New-AzAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT
```

Svůj účet Automation můžete vložit do kterékoli z následujících oblastí (označované taky jako umístění): Východní USA 2, Střed USA – jih, US Gov – Virginie, Západní Evropa, jihovýchodní Asie, Japonsko – východ, Střed Indie a Austrálie – jihovýchod, Kanada – střed, Severní Evropa.

## <a name="step-2-make-vm-extension-tweaks-to-the-resource-manager-template"></a>Krok 2: nastavení rozšíření virtuálních počítačů na šablonu Správce prostředků

Podrobnosti o registraci virtuálních počítačů (pomocí rozšíření virtuálního počítače prostředí PowerShell DSC) poskytované v této [šabloně Azure pro rychlý Start](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver).
Tento krok zaregistruje nový virtuální počítač se serverem Pull v seznamu uzlů konfigurace stavu. Součástí této registrace je určení konfigurace uzlu, která se má použít na uzel. Tato konfigurace uzlu nemusí na serveru pro vyžádání obsahu existovat, takže je přesně to, kde je krok 4 v první době proveden. Ale tady v kroku 2 se musíte rozhodnout, že jste určili název uzlu a název konfigurace. V tomto příkladu použití je uzel "isvbox" a konfigurace je "ISVBoxConfig". Proto je název konfigurace uzlu (který se má zadat v DeploymentTemplate.json) "ISVBoxConfig. isvbox".

## <a name="step-3-add-required-dsc-resources-to-the-pull-server"></a>Krok 3: přidání požadovaných prostředků DSC na server vyžádané replikace

Galerie prostředí PowerShell se instrumentuje pro instalaci prostředků DSC do účtu Azure Automation.
Přejděte k požadovanému prostředku a klikněte na tlačítko nasadit do Azure Automation.

![Příklad Galerie prostředí PowerShell](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

Další postup, který jste nedávno přidali do Azure Portal, vám umožní vyžádat si nové moduly nebo aktualizovat existující moduly. Klikněte na prostředek účtu Automation, dlaždice Assety a nakonec na dlaždici moduly. Ikona procházet galerii vám umožní zobrazit seznam modulů v galerii, přejít k podrobnostem a nakonec je importovat do svého účtu Automation. Jedná se o skvělý způsob, jak uchovávat moduly v aktuálním stavu v čase. Funkce Import ale kontroluje závislosti s ostatními moduly, aby se zajistilo, že se nic nestane synchronizované.

K dispozici je také ruční přístup, který se používá jenom jednou pro každý prostředek, pokud ho nechcete upgradovat později. Další informace o vytváření modulů integrace PowerShellu najdete v tématu [vytváření integračních modulů pro Azure Automation](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/).

>[!NOTE]
>Struktura složek modulu integrace PowerShellu pro počítač s Windows se trochu liší od struktury složek, kterou očekává Azure Automation. 

1. Nainstalujte [Windows Management Framework 5](https://aka.ms/wmf5latest) (není nutné pro Windows 10).

2. Nainstalujte modul Integration Module.

    ```azurepowershell-interactive
    Install-Module –Name MODULE-NAME`    <—grabs the module from the PowerShell Gallery
    ```

3. Zkopírujte složku Module z adresáře **C:\Program Files\WindowsPowerShell\Modules\MODULE-Name** do dočasné složky.

4. Odstraňte ukázky a dokumentaci z hlavní složky.

5. Hlavní složka zip, pojmenování souboru ZIP s názvem složky.

6. Soubor ZIP vložte do dosažitelného umístění HTTP, jako je například BLOB Storage v účtu Azure Storage.

7. Spusťte následující příkaz.

    ```azurepowershell-interactive
    New-AzAutomationModule `
      -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
      -Name MODULE-NAME –ContentLinkUri 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
    ```

Zahrnutý příklad implementuje tyto kroky pro cChoco a xNetworking. 

## <a name="step-4-add-the-node-configuration-to-the-pull-server"></a>Krok 4: Přidání konfigurace uzlu na server vyžádané replikace

Při prvním importu konfigurace do vyžádaného serveru a zkompilování není nic speciální. Všechna pozdější importy nebo kompilace stejné konfigurace vypadají přesně stejně. Pokaždé, když balíček aktualizujete a potřebujete ho nabízet do produkčního prostředí, provedete tento krok až po ověření správnosti konfiguračního souboru – včetně nové verze balíčku. Tady je konfigurační soubor **ISVBoxConfig.ps1**:

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

Tady je **New-ConfigurationScript.ps1** skript (upravený tak, aby používal modul AZ Module):

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

Výsledkem těchto kroků je nová konfigurace uzlu s názvem **ISVBoxConfig. isvbox** , která se umístí na server vyžádané replikace. Název konfigurace uzlu je sestaven jako `configurationName.nodeName` .

## <a name="step-5-create-and-maintain-package-metadata"></a>Krok 5: vytvoření a údržba metadat balíčku

Pro každý balíček, který vložíte do úložiště balíčků, potřebujete nuspec, který ho popisuje. Musí být zkompilován a uložen na vašem serveru NuGet. Tento proces je popsán [zde](https://docs.nuget.org/create/creating-and-publishing-a-package). 

**MyGet.org** můžete použít jako server NuGet. Tuto službu si můžete koupit, ale Thee je bezplatná SKU počátečních položek. V [balíčku NuGet](https://www.nuget.org/)najdete pokyny k instalaci vlastního serveru NuGet pro vaše soukromé balíčky.

## <a name="step-6-tie-it-all-together"></a>Krok 6: Propojte všechno dohromady.

Pokaždé, když verze projde QA a je schválená pro nasazení, vytvoří se balíček a nuspec a nupkg se aktualizují a nasadí na server NuGet. Konfigurace (krok 4) se taky musí aktualizovat tak, aby souhlasila s novým číslem verze. Musí se pak odeslat na server vyžádané replikace a zkompilovat.

Od tohoto okamžiku se nachází na virtuálních počítačích, které na této konfiguraci závisí, aby se aktualizace vyčetla a nainstalovala. Každá z těchto aktualizací je jednoduchá – jenom čára nebo dva prostředí PowerShell. V případě Azure DevOps jsou některé z nich zapouzdřeny v úlohách sestavení, které lze zřetězit společně v sestavení. V tomto [článku](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery) najdete další podrobnosti. Toto [úložiště GitHub](https://github.com/Microsoft/vso-agent-tasks) podrobně popisuje dostupné úkoly sestavení.

## <a name="related-articles"></a>Související články
* [Přehled Azure Automation DSC](automation-dsc-overview.md)
* [Připojování počítačů pro správu pomocí Azure Automation DSC](automation-dsc-onboarding.md)

## <a name="next-steps"></a>Další kroky

- Přehled najdete v tématu [Přehled konfigurace stavu Azure Automation](automation-dsc-overview.md).
- Pokud chcete začít používat tuto funkci, přečtěte si téma Začínáme [s konfigurací stavu Azure Automation](automation-dsc-getting-started.md).
- Další informace o kompilaci konfigurací DSC, abyste je mohli přiřadit cílovým uzlům, najdete v tématu [kompilace konfigurací DSC v konfiguraci stavu Azure Automation](automation-dsc-compile.md).
- Referenční informace k rutinám PowerShellu najdete v tématu [AZ. Automation](/powershell/module/az.automation).
- Informace o cenách najdete v tématu [Azure Automation ceny konfigurace stavu](https://azure.microsoft.com/pricing/details/automation/).
