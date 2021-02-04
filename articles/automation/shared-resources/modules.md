---
title: Správa modulů ve službě Azure Automation
description: Tento článek popisuje, jak pomocí modulů PowerShellu povolit rutiny v sadách Runbook a prostředcích DSC v konfiguracích DSC.
services: automation
ms.subservice: shared-capabilities
ms.date: 02/01/2021
ms.topic: conceptual
ms.openlocfilehash: a784127cfd6019629f1c2714d0f36850406c3b9d
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2021
ms.locfileid: "99548763"
---
# <a name="manage-modules-in-azure-automation"></a>Správa modulů ve službě Azure Automation

Azure Automation využívá řadu modulů PowerShellu k povolení rutin v sadách Runbook a prostředky DSC v konfiguracích DSC. Mezi podporované moduly patří:

* [Azure PowerShell AZ. Automation](/powershell/azure/new-azureps-module-az).
* [Azure PowerShell AzureRM. Automation](/powershell/module/azurerm.automation/).
* Jiné moduly prostředí PowerShell.
* Interní `Orchestrator.AssetManagement.Cmdlets` modul.
* Moduly Python 2.
* Vlastní moduly, které vytvoříte.

Když vytvoříte účet Automation, Azure Automation ve výchozím nastavení importují některé moduly. Viz [výchozí moduly](#default-modules).

## <a name="sandboxes"></a>Sandboxy

Když automatizace spustí úlohy kompilace sady Runbook a DSC, načte moduly do izolovaných prostorů, kde lze Runbooky spustit a konfigurace DSC mohou být zkompilovány. Automatizace také automaticky umístí do modulů všechny prostředky DSC v modulech na serveru vyžádané replikace DSC. Počítače mohou vyžádat prostředky při použití konfigurací DSC.

>[!NOTE]
>Nezapomeňte importovat pouze moduly, které vyžaduje vaše Runbooky a konfigurace DSC. Nedoporučujeme importovat kořenový modul AZ. Obsahuje mnoho dalších modulů, které nemusí být potřeba, což může způsobit problémy s výkonem. Místo toho importujte jednotlivé moduly, jako AZ. Compute.

Cloud izolovaného prostoru (sandbox) podporuje maximálně 48 systémových volání a omezuje všechna ostatní volání z bezpečnostních důvodů. Jiné funkce, jako je Správa přihlašovacích údajů a některé sítě, nejsou v izolovaném prostoru cloudu podporované.

Vzhledem k počtu zahrnutých modulů a rutin je obtížné zjistit, které rutiny budou provádět Nepodporovaná volání. Obecně jsme zjistili problémy s rutinami, které vyžadují zvýšený přístup, vyžadují přihlašovací údaje jako parametr nebo rutiny související se sítí. V izolovaném prostoru (sandbox) nejsou podporované žádné rutiny, které provádějí úplné síťové operace zásobníku, včetně [Connect-AipService](/powershell/module/aipservice/connect-aipservice) z modulu AipService PowerShellu a rutiny [Resolve-DnsName](/powershell/module/dnsclient/resolve-dnsname) z modulu DNSClient.

Toto jsou známá omezení pro izolovaný prostor (sandbox). Doporučeným řešením je nasazení [Hybrid Runbook Worker](../automation-hybrid-runbook-worker.md) nebo použití [Azure Functions](../../azure-functions/functions-overview.md).

## <a name="default-modules"></a>Výchozí moduly

V následující tabulce jsou uvedeny moduly, které Azure Automation při vytváření účtu Automation standardně naimportovány. Automatizace může importovat novější verze těchto modulů. Nemůžete ale odebrat původní verzi z účtu Automation, a to ani v případě, že odstraníte novější verzi. Všimněte si, že tyto výchozí moduly obsahují několik AzureRM modulů.

Výchozí moduly se označují také jako globální moduly. V Azure Portal bude vlastnost **globálního modulu** **true** při zobrazení modulu, který byl importován při vytvoření účtu.

![Snímek vlastnosti globálního modulu na webu Azure Portal](../media/modules/automation-global-modules.png)

Automatizace neimportuje kořenový klíč AZ Module automaticky do všech nových nebo existujících účtů Automation. Další informace o práci s těmito moduly najdete v tématu [migrace na az modules](#migrate-to-az-modules).

> [!NOTE]
> Nedoporučujeme měnit moduly a runbooky v účtech Automation používaných pro nasazení funkce [Start/Stop VMS during off-hours](../automation-solution-vm-management.md) .

|Název modulu|Verze|
|---|---|
| AuditPolicyDsc | 1.1.0.0 |
| Azure | 1.0.3 |
| Azure.Storage | 1.0.3 |
| AzureRM.Automation | 1.0.3 |
| AzureRM.Compute | 1.2.1 |
| AzureRM.Profile | 1.0.3 |
| AzureRM.Resources | 1.0.3 |
| AzureRM.Sql | 1.0.3 |
| AzureRM.Storage | 1.0.3 |
| ComputerManagementDsc | 5.0.0.0 |
| GPRegistryPolicyParser | 0.2 |
| Microsoft. PowerShell. Core | 0 |
| Microsoft. PowerShell. Diagnostics |  |
| Microsoft. PowerShell. Management |  |
| Microsoft. PowerShell. Security |  |
| Microsoft.PowerShell.Utility |  |
| Microsoft. WSMan. Management |  |
| Orchestrator. AssetManagement. rutiny | 1 |
| PSDscResources | 2.9.0.0 |
| SecurityPolicyDsc | 2.1.0.0 |
| StateConfigCompositeResources | 1 |
| xDSCDomainjoin | 1.1 |
| xPowerShellExecutionPolicy | 1.1.0.0 |
| xRemoteDesktopAdmin | 1.1.0.0 |

## <a name="az-modules"></a>AZ modules

V případě AZ. Automation mají většina rutin stejné názvy jako ty, které se používají pro moduly AzureRM, s tím rozdílem, že `AzureRM` předpona byla změněna na `Az` . Seznam AZ modules, který nedodržuje tuto zásadu vytváření názvů, najdete v [seznamu výjimek](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

## <a name="internal-cmdlets"></a>Interní rutiny

Azure Automation podporuje interní `Orchestrator.AssetManagement.Cmdlets` modul pro Log Analytics agenta pro Windows, který je ve výchozím nastavení nainstalovaný. Následující tabulka definuje interní rutiny. Tyto rutiny jsou navržené tak, aby se používaly Azure PowerShell rutiny pro interakci se sdílenými prostředky. Můžou načítat tajné kódy z šifrovaných proměnných, přihlašovacích údajů a šifrovaných připojení.

>[!NOTE]
>Interní rutiny jsou dostupné, jenom když spouštíte Runbooky v prostředí Azure izolovaného prostoru (sandbox) nebo ve Windows Hybrid Runbook Worker. 

|Název|Description|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

Všimněte si, že se interní rutiny liší v pojmenování pomocí rutin AZ a AzureRM. Interní názvy rutin neobsahují slova jako `Azure` nebo `Az` v podstatném jméně, ale použijte slovo `Automation` . Při provádění Runbooku v izolovaném prostoru Azure nebo na Hybrid Runbook Worker Windows doporučujeme použít rutiny AZ nebo AzureRM. Vyžadují míň parametrů a běží v kontextu úlohy, která je už spuštěná.

Pomocí rutin AZ nebo AzureRM můžete manipulovat s prostředky Automation mimo kontext Runbooku. 

## <a name="python-modules"></a>Moduly Pythonu

Můžete vytvořit Runbooky v Pythonu 2 v Azure Automation. Informace o modulu Pythonu najdete [v tématu Správa balíčků Python 2 v Azure Automation](../python-packages.md).

## <a name="custom-modules"></a>Vlastní moduly

Azure Automation podporuje vlastní moduly PowerShellu, které vytvoříte pro použití s vašimi Runbooky a konfiguracemi DSC. Jedním z typů vlastního modulu je integrační modul, který volitelně obsahuje soubor metadat, který umožňuje definovat vlastní funkce pro rutiny modulu. Příklad použití integračního modulu je k dispozici v části [Přidání typu připojení](../automation-connections.md#add-a-connection-type).

Azure Automation může importovat vlastní modul, aby byly dostupné jeho rutiny. Na pozadí ukládá modul a používá ho v izolovaném prostoru Azure, stejně jako jiné moduly.

## <a name="migrate-to-az-modules"></a>Migrace na az moduls

V této části se dozvíte, jak migrovat do modulů AZ modules in Automation. Další informace najdete v tématu [migrace Azure PowerShell z AzureRM na az](/powershell/azure/migrate-from-azurerm-to-az).

Nedoporučujeme spouštět moduly AzureRM a AZ modules ve stejném účtu Automation. Pokud jste si jisti, že chcete migrovat z AzureRM na AZ, je nejlepší provést úplné dokončení migrace. Automatizace často znovu používá sandboxy v rámci účtu Automation, aby se uložily při spuštění. Pokud neprovedete úplnou migraci modulu, můžete spustit úlohu, která používá pouze AzureRM moduly, a pak spustit jinou úlohu, která používá pouze příkaz AZ Modules. Izolovaný prostor (sandbox) brzy selže a zobrazí se chyba oznamující, že moduly nejsou kompatibilní. Tato situace vede k náhodnému výskytu chyb pro konkrétní Runbook nebo konfiguraci.

>[!NOTE]
>Když vytvoříte nový účet Automation i po migraci na az modules, Služba Automation nainstaluje ve výchozím nastavení moduly AzureRM. Můžete přesto aktualizovat Runbooky kurzů pomocí rutin AzureRM. Nicméně byste tyto Runbooky neměli spouštět.

### <a name="test-your-runbooks-and-dsc-configurations-prior-to-module-migration"></a>Otestujte své Runbooky a konfigurace DSC před migrací modulu

Před migrací na moduly AZ modules nezapomeňte všechny Runbooky a konfigurace DSC pečlivě otestovat v samostatném účtu Automation. 

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>Zastavit a zrušit plán pro všechny Runbooky, které používají moduly AzureRM

Abyste se ujistili, že nespouštíte žádné existující Runbooky nebo konfigurace DSC, které používají moduly AzureRM, musíte zastavit a zrušit plán všech ovlivněných runbooků a konfigurací. Nejdřív se ujistěte, že každé z těchto sad Runbook nebo konfigurace DSC a jejich plány se samostatně kontrolují, aby v případě potřeby bylo možné položku v budoucnu znovu naplánovat.

Až budete připraveni k odebrání plánů, můžete použít rutinu Azure Portal nebo rutinu [Remove-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) . Viz [Odebrat plán](schedules.md#remove-a-schedule).

### <a name="remove-azurerm-modules"></a>Odebrat moduly AzureRM

Moduly AzureRM je možné odebrat předtím, než naimportujete moduly AZ. Nicméně, pokud to uděláte, můžete přerušit synchronizaci správy zdrojového kódu a způsobit, že všechny skripty, u kterých stále naplánujete selhání. Pokud se rozhodnete moduly odebrat, přečtěte si téma [Uninstall AzureRM](/powershell/azure/migrate-from-azurerm-to-az#uninstall-azurerm).

### <a name="import-az-modules"></a>Import AZ modules

Import modulu AZ Module do účtu Automation neprovede automatický import modulu do relace prostředí PowerShell, kterou používají Runbooky. Moduly se importují do relace PowerShellu v následujících situacích:

* Když sada Runbook vyvolá rutinu z modulu.
* Když sada Runbook importuje modul explicitně pomocí rutiny [Import-Module](/powershell/module/microsoft.powershell.core/import-module) .
* Když sada Runbook importuje jiný závislý modul.

Do Azure Portal můžete importovat moduly AZ Modules. Nezapomeňte importovat jenom moduly AZ, které potřebujete, a ne celý modul AZ. Automation. Vzhledem k tomu, že [AZ. Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) je závislost pro ostatní moduly AZ, nezapomeňte tento modul naimportovat před všemi ostatními.

1. V účtu Automation v části **sdílené prostředky** vyberte **moduly**.
2. Vyberte **Procházet galerii**.  
3. Na panelu hledání zadejte název modulu (například `Az.Accounts` ).
4. Na stránce modul prostředí PowerShell vyberte **importovat** a importujte modul do svého účtu Automation.

    ![Snímek obrazovky importování modulů do účtu Automation](../media/modules/import-module.png)

Tento import můžete provést také prostřednictvím [Galerie prostředí PowerShell](https://www.powershellgallery.com), a to tak, že vyhledáte modul, který chcete importovat. Když najdete modul, vyberte ho a klikněte na kartu **Azure Automation** . Vyberte **nasadit do Azure Automation**.

![Snímek obrazovky s importem modulů přímo z Galerie prostředí PowerShell](../media/modules/import-gallery.png)

### <a name="test-your-runbooks"></a>Testování runbooků

Po importu modulů AZ modules do účtu Automation můžete začít upravovat své Runbooky a konfigurace DSC pro použití nových modulů. Jedním ze způsobů, jak otestovat změnu sady Runbook, aby používala nové rutiny, je použití `Enable-AzureRmAlias -Scope Process` příkazu na začátku Runbooku. Přidáním tohoto příkazu do Runbooku se skript dá spustit beze změn.

## <a name="author-modules"></a>Vytváření modulů

Při vytváření vlastního modulu PowerShellu pro použití v Azure Automation doporučujeme postupovat podle pokynů v této části. Pokud chcete připravit modul pro import, musíte vytvořit alespoň soubor. dll. psd1,. psm1 nebo modul PowerShell modulu **. dll** se stejným názvem jako složka modulu. Pak zazálohujte složku modulu tak, aby ji Azure Automation mohli importovat jako jeden soubor. Balíček **. zip** by měl mít stejný název jako složka s uvedeným modulem.

Další informace o vytváření modulu PowerShellu najdete v tématu [Postup zápisu modulu skriptu PowerShellu](/powershell/scripting/developer/module/how-to-write-a-powershell-script-module).

### <a name="version-folder"></a>Složka verze

Správa verzí na souběžných modulech PowerShellu umožňuje používat v PowerShellu více než jednu verzi modulu. To může být užitečné, pokud máte starší skripty, které byly testovány a pracují pouze s určitou verzí modulu prostředí PowerShell, ale jiné skripty vyžadují novější verzi stejného modulu PowerShellu.

Pokud chcete vytvořit moduly PowerShellu tak, aby obsahovaly víc verzí, vytvořte složku modulu a pak pro každou verzi modulu, kterou chcete použít, vytvořte složku v této složce modulu. V následujícím příkladu modul s názvem *TestModule* poskytuje dvě verze, 1.0.0 a 2.0.0.

```dos
TestModule
   1.0.0
   2.0.0
```

V každé ze složek verze zkopírujte soubory PowerShell. psm1,. psd1 nebo modul PowerShellu **. dll** , které tvoří modul, do příslušné verze složky. Zazálohujte složku modulu tak, aby ji Azure Automation mohl importovat jako jeden soubor. zip. I když Automation zobrazuje jenom nejvyšší verzi importovaného modulu, pokud balíček obsahuje souběžné verze modulu, jsou všechny dostupné pro použití ve vašich sadách Runbook nebo konfiguracích DSC.  

Služba Automation sice podporuje moduly, které obsahují souběžné verze v rámci stejného balíčku, ale nepodporuje používání více verzí modulu napříč importy balíčků modulů. Například importujete **modul a**, který obsahuje verze 1 a 2 do svého účtu Automation. Později aktualizujete **modul a** tak, aby obsahoval verze 3 a 4. při importu do svého účtu Automation jsou v rámci všech runbooků nebo konfigurací DSC použitelné jenom verze 3 a 4. Pokud požadujete, aby byly k dispozici všechny verze – 1, 2, 3 a 4, soubor. zip, který váš import má, bude obsahovat verze 1, 2, 3 a 4.

Pokud budete používat různé verze stejného modulu mezi sadami Runbook, měli byste vždy deklarovat verzi, kterou chcete použít v sadě Runbook, pomocí `Import-Module` rutiny a zahrnout parametr `-RequiredVersion <version>` . I v případě, že je verze, kterou chcete použít, nejnovější verze. Důvodem je to, že úlohy sady Runbook mohou běžet ve stejném izolovaném prostoru. Pokud izolovaný prostor (sandbox) už explicitně načetl modul určitého čísla verze, protože k tomu se říká předchozí úloha v tomto izolovaném prostoru (sandbox), nenačte budoucí úlohy v tomto izolovaném prostoru automaticky nejnovější verzi tohoto modulu. Je to proto, že některá verze je již načtena v izolovaném prostoru (sandbox).

V případě prostředku DSC zadejte konkrétní verzi pomocí následujícího příkazu:

```powershell
Import-DscResource -ModuleName <ModuleName> -ModuleVersion <version>
```

### <a name="help-information"></a>Informace o nápovědě

Pro každou rutinu v modulu uveďte stručný popis, popis a identifikátor URI pro nápovědu. V PowerShellu můžete pomocí rutiny definovat informace o nápovědě k rutinám `Get-Help` . Následující příklad ukazuje, jak definovat stručný a s identifikátorem URI v souboru modulu **. psm1** .

  ```powershell
  <#
       .SYNOPSIS
        Gets a Contoso User account
  #>
  function Get-ContosoUser {
  [CmdletBinding](DefaultParameterSetName='UseConnectionObject', `
  HelpUri='https://www.contoso.com/docs/information')]
  [OutputType([String])]
  param(
     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $UserName,

     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $Password,

     [Parameter(ParameterSetName='ConnectionObject', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [Hashtable]
     $Connection
  )

  switch ($PSCmdlet.ParameterSetName) {
     "UserAccount" {
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $UserName, $Password
        Connect-Contoso -Credential $cred
     }
     "ConnectionObject" {
        Connect-Contoso -Connection $Connection
    }
  }
  }
  ```

  Zadání těchto informací zobrazí text v nápovědě pomocí `Get-Help` rutiny v konzole PowerShellu. Tento text je také zobrazen v Azure Portal.

  ![Obrazovka pomocníka s modulem integrace](../media/modules/module-activity-description.png)

### <a name="connection-type"></a>Typ připojení

Pokud se modul připojuje k externí službě, Definujte typ připojení pomocí [vlastního modulu Integration](#custom-modules). Každá rutina v modulu by měla jako parametr přijmout instanci tohoto typu připojení (objekt připojení). Uživatelé mapují parametry assetu připojení k odpovídajícím parametrům rutiny pokaždé, když volají rutinu.

![Použití vlastního připojení v Azure Portal](../media/modules/connection-create-new.png)

Následující příklad Runbooku používá prostředek připojení contoso, `ContosoConnection` který se volá pro přístup k prostředkům společnosti Contoso a vrácení dat z externí služby. V tomto příkladu jsou pole mapována na `UserName` `Password` vlastnosti a `PSCredential` objektu a poté předány rutině.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

Jednodušší a lepší způsob, jak získat přístup k tomuto chování, je přímé předání objektu připojení do rutiny:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

Podobné chování rutin můžete povolit tím, že jim umožníte přijmout objekt připojení přímo jako parametr místo pouze polí připojení pro parametry. Obvykle chcete sadu parametrů pro každý, aby uživatel, který nepoužívá automatizaci, mohl volat vaše rutiny bez vytvoření zatřiďovací tabulky, která by sloužila jako objekt připojení. Sada parametrů `UserAccount` se používá k předání vlastností pole připojení. `ConnectionObject` umožňuje předat připojení rovnou uživateli.

### <a name="output-type"></a>Typ výstupu

Definujte typ výstupu pro všechny rutiny v modulu. Definování typu výstupu pro rutinu umožňuje technologii IntelliSense v době návrhu, která vám pomůže určit výstupní vlastnosti rutiny během vytváření obsahu. Tento postup je obzvlášť užitečný při vytváření grafického Runbooku, pro který je důležité, aby byl v rámci vašeho modulu snadný zážitek v době návrhu.

Přidat `[OutputType([<MyOutputType>])]` , kde `MyOutputType` je platný typ. Další informace o najdete `OutputType` v tématu [About Functions OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). Následující kód je příkladem přidání `OutputType` do rutiny:

  ```powershell
  function Get-ContosoUser {
  [OutputType([String])]
  param(
     [string]
     $Parameter1
  )
  # <script location here>
  }
  ```

  ![Snímek obrazovky s typem výstupu grafického Runbooku](../media/modules/runbook-graphical-module-output-type.png)

  Toto chování je podobné funkci "typ dopředu" výstupu rutiny v prostředí integrační služby prostředí PowerShell, aniž byste je museli spouštět.

  ![Snímek obrazovky POSH IntelliSense](../media/modules/automation-posh-ise-intellisense.png)

### <a name="cmdlet-state"></a>Stav rutiny

Zpřístupněte všechny rutiny v modulu bez stavu. Více úloh sady Runbook lze současně spustit ve stejném `AppDomain` a stejném procesu a v izolovaném prostoru. Pokud se na těchto úrovních sdílí nějaký stav, úlohy můžou mít vliv na sebe navzájem. Toto chování může vést k občasnému a obtížnému diagnostikování problémů. Tady je příklad toho, co dělat není:

  ```powershell
  $globalNum = 0
  function Set-GlobalNum {
     param(
         [int] $num
     )

     $globalNum = $num
  }
  function Get-GlobalNumTimesTwo {
     $output = $globalNum * 2

     $output
  }
  ```

### <a name="module-dependency"></a>Závislost modulu

Zajistěte, aby byl modul plně obsažený v balíčku, který se dá zkopírovat pomocí příkazu Xcopy. Moduly automatizace jsou distribuovány do izolovaných prostorů automatizace při spuštění sad Runbook. Moduly musí fungovat nezávisle na hostiteli, který je spouští.

Měli byste být schopni vytvořit a přesunout balíček modulu a při importu do prostředí PowerShell jiného hostitele fungovat jako normální. Aby k tomu mohlo dojít, zajistěte, aby modul nezávisí na žádných souborech mimo složku modulu, který je při importu modulu do automatizace.

Váš modul by neměl záviset na žádném z jedinečných nastavení registru na hostiteli. Příklady jsou nastavení, která se provádějí při instalaci produktu.

### <a name="module-file-paths"></a>Cesty k souborům modulu

Ujistěte se, že všechny soubory v modulu mají cesty s méně než 140 znaky. Jakékoli cesty delší než 140 znaků v délce způsobují problémy s importem runbooků. Automatizace nemůže importovat soubor s velikostí cesty více než 140 znaků do relace PowerShellu `Import-Module` .

## <a name="import-modules"></a>Import modulů

Tato část definuje několik způsobů, jak můžete importovat modul do svého účtu Automation.

### <a name="import-modules-in-the-azure-portal"></a>Importovat moduly v Azure Portal

Import modulu v Azure Portal:

1. Přejít na svůj účet Automation.
2. V části **sdílené prostředky** vyberte **moduly**.
3. Vyberte **Přidat modul**.
4. Vyberte soubor **. zip** , který obsahuje váš modul.
5. Kliknutím na **tlačítko OK** začněte importovat proces.

### <a name="import-modules-by-using-powershell"></a>Import modulů pomocí PowerShellu

K importu modulu do svého účtu Automation můžete použít rutinu [New-AzAutomationModule](/powershell/module/az.automation/new-azautomationmodule) . Rutina převezme adresu URL pro balíček Module. zip.

```azurepowershell-interactive
New-AzAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

Stejnou rutinu můžete použít také k importu modulu z Galerie prostředí PowerShell přímo. Nezapomeňte vzít `ModuleName` a `ModuleVersion` z [Galerie prostředí PowerShell](https://www.powershellgallery.com).

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="import-modules-from-the-powershell-gallery"></a>Importovat moduly z Galerie prostředí PowerShell

[Galerie prostředí PowerShell](https://www.powershellgallery.com) moduly můžete importovat buď přímo z Galerie, nebo z vašeho účtu Automation.

Import modulu přímo z Galerie prostředí PowerShell:

1. Vyhledejte https://www.powershellgallery.com modul, který chcete importovat, a vyhledejte ho.
2. V části **Možnosti instalace** na kartě **Azure Automation** vyberte **nasadit a Azure Automation**. Tato akce otevře Azure Portal. 
3. Na stránce Import vyberte svůj účet Automation a vyberte **OK**.

![Snímek obrazovky modulu Galerie prostředí PowerShell Import](../media/modules/powershell-gallery.png)

Import modulu Galerie prostředí PowerShell přímo z účtu Automation:

1. V části **sdílené prostředky** vyberte **moduly**. 
2. Vyberte **Procházet galerii** a potom vyhledejte v galerii modul. 
3. Vyberte modul, který chcete importovat, a vyberte **importovat**. 
4. Výběrem **OK** spusťte proces importu.

![Snímek obrazovky importující modul Galerie prostředí PowerShell z Azure Portal](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>Odstranit moduly

Pokud máte problémy s modulem nebo se chcete vrátit k předchozí verzi modulu, můžete ho odstranit z účtu Automation. Původní verze [výchozích modulů](#default-modules) , které se importují při vytváření účtu Automation, nemůžete odstranit. Pokud je modul, který chcete odstranit, novější verze jednoho z [výchozích modulů](#default-modules), vrátí se zpět k verzi nainstalované s vaším účtem Automation. V opačném případě se odstraní kterýkoli modul, který odstraníte z účtu Automation.

### <a name="delete-modules-in-the-azure-portal"></a>Odstranit moduly v Azure Portal

Odebrání modulu v Azure Portal:

1. Přejít na svůj účet Automation. V části **sdílené prostředky** vyberte **moduly**.
2. Vyberte modul, který chcete odebrat.
3. Na stránce modul vyberte **Odstranit**. Pokud je tento modul jedním z [výchozích modulů](#default-modules), vrátí se na verzi, která existovala při vytvoření účtu Automation.

### <a name="delete-modules-by-using-powershell"></a>Odstranění modulů pomocí PowerShellu

Pokud chcete odebrat modul pomocí PowerShellu, spusťte následující příkaz:

```azurepowershell-interactive
Remove-AzAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="next-steps"></a>Další kroky

* Další informace o použití Azure PowerShellch modulů najdete v tématu [Začínáme s Azure PowerShell](/powershell/azure/get-started-azureps).

* Další informace o vytváření modulů PowerShellu najdete v tématu vytvoření [modulu Windows PowerShellu](/powershell/scripting/developer/module/writing-a-windows-powershell-module).
