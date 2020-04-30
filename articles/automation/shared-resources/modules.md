---
title: Správa modulů ve službě Azure Automation
description: Tento článek popisuje, jak spravovat moduly v Azure Automation.
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d036733c023417af3ef038bb9abc278ec91e665c
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "82508951"
---
# <a name="manage-modules-in-azure-automation"></a>Správa modulů ve službě Azure Automation

Azure Automation umožňuje importovat moduly PowerShellu a povolit rutiny v sadách Runbook a prostředcích DSC v konfiguracích DSC. Moduly používané v Azure Automation zahrnují:

* [Azure PowerShell AZ. Automation](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)
* [Azure PowerShell AzureRM. Automation](https://docs.microsoft.com/powershell/module/azurerm.automation/?view=azurermps-6.13.0)
* Jiné moduly PowerShellu
* Interní `Orchestrator.AssetManagement.Cmdlets` modul
* Moduly Python 2
* Vlastní moduly, které vytvoříte 

Když vytvoříte účet Automation, Azure Automation ve výchozím nastavení importují některé moduly. Viz [výchozí moduly](#default-modules).

Když Azure Automation spustí úlohy kompilace sady Runbook a DSC, načte moduly do izolovaných prostorů, kde lze Runbooky spustit a konfigurace DSC mohou být zkompilovány. Automatizace také automaticky umístí do modulů všechny prostředky DSC v modulech na serveru vyžádané replikace DSC. Počítače mohou vyžádat prostředky při použití konfigurací DSC.

>[!NOTE]
>Nezapomeňte importovat pouze moduly, které vaše Runbooky a konfigurace DSC skutečně potřebují. Nedoporučujeme importovat kořenový modul AZ Module, protože obsahuje mnoho dalších modulů, které možná nepotřebujete, což může způsobit problémy s výkonem. Místo toho importujte jednotlivé moduly, jako AZ. Compute.

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci nástroje AZ Module Hybrid Runbook Worker najdete v tématu [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Pro váš účet Automation můžete aktualizovat moduly na nejnovější verzi pomocí [postupu aktualizace modulů Azure PowerShell v Azure Automation](../automation-update-azure-modules.md).

## <a name="default-modules"></a>Výchozí moduly

V následující tabulce jsou uvedeny moduly, které Azure Automation při vytváření účtu Automation standardně naimportovány. Automatizace může importovat novější verze těchto modulů. Nemůžete ale odebrat původní verzi z účtu Automation, a to ani v případě, že odstraníte novější verzi. Všimněte si, že tyto výchozí moduly obsahují několik AzureRM modulů. 

Azure Automation neimportuje kořenový klíč AZ Module automaticky do všech nových nebo existujících účtů Automation. Další informace o práci s těmito moduly najdete v tématu [migrace na az modules](#migrating-to-az-modules).

> [!NOTE]
> Nedoporučujeme měnit moduly a runbooky v účtech Automation, které obsahují [řešení Start/Stop VMS during off-hours v Azure Automation](../automation-solution-vm-management.md).

|Název modulu|Version|
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

## <a name="az-module-cmdlets"></a>AZ Module rutiny

Pro AZ. Automation mají většina rutin stejné názvy jako pro moduly AzureRM, s tím rozdílem, že předpona AzureRm byla změněna na AZ. Seznam AZ modules, který nedodržuje tuto zásadu vytváření názvů, najdete v tématu [seznam výjimek](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

## <a name="internal-cmdlets"></a>Interní rutiny

Následující tabulka definuje interní rutiny podporované `Orchestrator.AssetManagement.Cmdlets` modulem. Tyto rutiny můžete použít ve svých sadách Runbook a konfiguracích DSC k interakci s prostředky Azure v rámci účtu Automation. Rutiny jsou navržené tak, aby se místo rutin Azure PowerShell načetly tajné klíče z šifrovaných proměnných, přihlašovacích údajů a šifrovaných připojení. 

>[!NOTE]
>Interní rutiny jsou dostupné, jenom když spouštíte Runbooky v prostředí Azure izolovaného prostoru (sandbox) nebo ve Windows Hybrid Runbook Worker. 

|Název|Popis|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Spustit – AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Čekání – AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

Všimněte si, že se interní rutiny liší v pojmenování pomocí rutin AZ a AzureRM. Interní názvy rutin neobsahují v podstatném jméně slova jako "Azure" nebo "AZ", ale používají slovo "Automation". Při provádění Runbooku v izolovaném prostoru Azure nebo na hybridním pracovním procesu Windows doporučujeme jejich použití na používání rutin AZ nebo AzureRM. Vyžadují méně parametrů a běží v kontextu vaší již spouštěné úlohy.

Pro manipulaci s Azure Automation prostředky mimo kontext Runbooku doporučujeme použít rutiny AZ nebo AzureRM. 

## <a name="orchestratorassetmanagementcmdlets-module"></a>Orchestrator. AssetManagement. rutiny – modul

Azure Automation podporuje interní `Orchestrator.AssetManagement.Cmdlets` modul pro Log Analytics agenta pro Windows, který je ve výchozím nastavení nainstalovaný. `Get-AutomationPSCredential` Rutina v tomto modulu se běžně používá v sadách Runbook k načtení `PSCredential` objektu, který je očekáván většinou rutin PowerShellu, které pracují s přihlašovacími údaji. Další informace o použití přihlašovacích údajů v Azure Automation najdete v části [assety s přihlašovacími údaji v Azure Automation](credentials.md).

## <a name="python-modules"></a>Moduly Pythonu

Můžete vytvořit Runbooky v Pythonu 2 v Azure Automation. Informace o modulu Pythonu najdete [v tématu Správa balíčků Python 2 v Azure Automation](../python-packages.md).

## <a name="migrating-to-az-modules"></a>Migrace na az modules

### <a name="migration-considerations"></a>Posouzení migrace

Tato část obsahuje požadavky, které je potřeba vzít v úvahu při migraci do modulů AZ v Azure Automation. Viz také [migrace Azure PowerShell z AzureRM na az](https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az?view=azps-3.7.0). 

#### <a name="use-of-azurerm-modules-and-az-modules-in-the-same-automation-account"></a>Použití modulů AzureRM a AZ modules ve stejném účtu Automation

 Nedoporučujeme spouštět moduly AzureRM a AZ modules ve stejném účtu Automation. Pokud jste si jisti, že chcete migrovat z AzureRM na AZ, je nejlepší provést úplné dokončení migrace. Nejdůležitějším důvodem je, že Azure Automation často znovu používá sandboxy v rámci účtu Automation, aby se ušetřily při spuštění. Pokud neprovedete úplnou migraci modulu, můžete úlohu spustit pouze pomocí modulů AzureRM a pak spustit další úlohu pomocí pouze modulů AZ Modules. Izolovaný prostor (sandbox) brzy selže a zobrazí se závažná chyba s oznámením, že moduly nejsou kompatibilní. Tato situace vede k náhodnému výskytu chyb pro všechny konkrétní sady Runbook nebo konfigurace. 

#### <a name="import-of-az-modules-into-the-powershell-session"></a>Import AZ modules do relace PowerShellu

Import modulu AZ Module do účtu Automation neprovede automatický import modulu do relace prostředí PowerShell, kterou používají Runbooky. Moduly se importují do relace PowerShellu v následujících situacích:

* Když sada Runbook vyvolá rutinu z modulu
* Pokud sada Runbook importuje modul explicitně pomocí rutiny [Import-Module](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-7)
* Když sada Runbook importuje jiný závislý modul

#### <a name="testing-for-your-runbooks-and-dsc-configurations-prior-to-module-migration"></a>Testování pro vaše Runbooky a konfigurace DSC před migrací modulu

Před migrací na moduly AZ modules nezapomeňte všechny Runbooky a konfigurace DSC pečlivě otestovat v samostatném účtu Automation. 

#### <a name="updates-for-tutorial-runbooks"></a>Aktualizace pro Runbooky kurzů 

Když vytváříte nový účet Automation i po migraci na az modules, Azure Automation nainstaluje ve výchozím nastavení moduly AzureRM. Můžete přesto aktualizovat Runbooky kurzů pomocí rutin AzureRM. Nicméně byste tyto Runbooky neměli spouštět.

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>Zastavit a zrušit plán pro všechny Runbooky, které používají moduly AzureRM

Abyste se ujistili, že nespouštíte žádné existující Runbooky nebo konfigurace DSC, které používají moduly AzureRM, musíte zastavit a zrušit plán všech ovlivněných runbooků a konfigurací. Nejprve se ujistěte, že jste provedli kontrolu každého Runbooku nebo konfigurace DSC a jejich plánů samostatně, abyste v případě potřeby mohli položku v budoucnu znovu naplánovat. 

Až budete připraveni k odebrání plánů, můžete použít rutinu Azure Portal nebo rutinu [Remove-AzureRmAutomationSchedule](https://docs.microsoft.com/powershell/module/azurerm.automation/remove-azurermautomationschedule?view=azurermps-6.13.0) . Viz [Odebrání plánu](schedules.md#removing-a-schedule).

### <a name="remove-the-azurerm-modules"></a>Odebrat moduly AzureRM

Moduly AzureRM je možné odebrat před importem modulů AZ. Odstranění modulů AzureRM ale může přerušit synchronizaci správy zdrojového kódu a způsobit, že všechny skripty, u kterých se pořád naplánovala selhání. Pokud se rozhodnete moduly odebrat, přečtěte si téma [Uninstall AzureRM](https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az?view=azps-3.8.0#uninstall-azurerm).

### <a name="import-the-az-modules"></a>Import modulů AZ

V této části se dozvíte, jak importovat moduly az do Azure Portal. Nezapomeňte importovat jenom moduly AZ, které potřebujete, a ne celý modul AZ. Automation. Vzhledem k tomu, že [AZ. Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) je závislost pro ostatní moduly AZ, nezapomeňte tento modul naimportovat před všemi ostatními.

1. Z účtu Automation v části **sdílené prostředky**vyberte **moduly** . 
2. Kliknutím na **Procházet galerii** otevřete stránku procházet galerii.  
3. Na panelu hledání zadejte název modulu, například `Az.Accounts`. 
4. Na stránce modul prostředí PowerShell klikněte na **importovat** a importujte modul do svého účtu Automation.

    ![Importovat moduly do účtu Automation](../media/modules/import-module.png)

Tento proces importu je možné provést také pomocí [Galerie prostředí PowerShell](https://www.powershellgallery.com) vyhledáváním modulu, který se má importovat. Jakmile najdete modul, vyberte ho, zvolte kartu **Azure Automation** a klikněte na **nasadit a Azure Automation**.

![Import modulů přímo z Galerie](../media/modules/import-gallery.png)

### <a name="test-your-runbooks"></a>Testování runbooků

Po importu AZ modules do účtu Automation můžete začít upravovat své Runbooky a konfigurace DSC pro použití nových modulů. Jedním ze způsobů, jak otestovat změnu sady Runbook, aby používala nové rutiny, je `Enable-AzureRmAlias -Scope Process` použití na začátku Runbooku. Přidáním tohoto příkazu do Runbooku se skript dá spustit beze změn. 

## <a name="authoring-modules"></a>Vytváření modulů

Při vytváření modulu PowerShellu pro použití v Azure Automation doporučujeme postupovat podle pokynů v této části.

### <a name="version-folder"></a>Složka verze

Nezahrnujte složku verze do balíčku **. zip** pro váš modul.  Tento problém je méně důležitý pro Runbooky, ale způsobuje problém se službou konfigurace stavu (DSC). Azure Automation automaticky vytvoří složku verze, když je modul distribuován do uzlů spravovaných DSC. Pokud složka verze existuje, skončíte se dvěma instancemi. Tady je příklad struktury složek pro modul DSC:

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

### <a name="help-information"></a>Informace o nápovědě

Pro každou rutinu v modulu uveďte stručný popis, popis a identifikátor URI pro nápovědu. V PowerShellu můžete pomocí `Get-Help` rutiny definovat informace o nápovědě k rutinám. Následující příklad ukazuje, jak definovat stručný a s identifikátorem URI v souboru modulu **. psm1** .

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

  ![Nápověda k modulu integrace](../media/modules/module-activity-description.png)

### <a name="connection-type"></a>Typ připojení

Pokud se modul připojuje k externí službě, definujte [Typ připojení](#adding-a-connection-type-to-your-module). Každá rutina v modulu by měla jako parametr přijímat objekt připojení (instanci tohoto typu připojení). Uživatelé mapují parametry assetu připojení k odpovídajícím parametrům rutiny pokaždé, když volají rutinu. Následující příklad Runbooku vychází z příkladu v předchozí části, používá prostředek připojení contoso, který se nazývá `ContosoConnection` přístup k prostředkům společnosti Contoso a vrací data z externí služby. V tomto příkladu jsou pole mapována na vlastnosti `UserName` a `Password` `PSCredential` objektu a poté předány rutině.

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

  Podobné chování rutin můžete povolit tím, že jim umožníte přijmout objekt připojení přímo jako parametr místo pouze polí připojení pro parametry. Obvykle chcete nastavit sadu parametrů pro každý, aby uživatel, který nepoužívá Azure Automation, mohl volat rutiny bez vytvoření zatřiďovací tabulky, která by sloužila jako objekt připojení. Sada `UserAccount` parametrů se používá k předání vlastností pole připojení. `ConnectionObject`umožňuje předat připojení rovnou uživateli.

### <a name="output-type"></a>Typ výstupu

Definujte typ výstupu pro všechny rutiny v modulu. Definování typu výstupu pro rutinu umožňuje technologii IntelliSense v době návrhu, která vám pomůže určit výstupní vlastnosti rutiny během vytváření obsahu. Tento postup je obzvlášť užitečný při vytváření grafického Runbooku, pro který je důležité, aby byl v rámci vašeho modulu snadný zážitek v době návrhu.

Přidat `[OutputType([<MyOutputType>])]` , `MyOutputType` kde je platný typ. Další informace o `OutputType`najdete v tématu [About Functions OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). Následující kód je příkladem přidání `OutputType` do rutiny:

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

  ![Typ výstupu grafického runbooku](../media/modules/runbook-graphical-module-output-type.png)

  Toto chování je podobné funkci "typ dopředu" výstupu rutiny v prostředí PowerShell ISE bez nutnosti spustit.

  ![POSH IntelliSense](../media/modules/automation-posh-ise-intellisense.png)

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

Zajistěte, aby byl modul plně obsažený v balíčku, který se dá zkopírovat pomocí příkazu Xcopy. Azure Automation moduly jsou distribuovány do izolovaných prostorů automatizace při spuštění sad Runbook. Moduly musí fungovat nezávisle na hostiteli, který je spouští. 

Měli byste být schopni vytvořit a přesunout balíček modulu a při importu do prostředí PowerShell jiného hostitele fungovat jako normální. Aby k tomu docházelo, zajistěte, aby modul nezávisí na žádných souborech mimo složku modulu, který je při importu modulu do Azure Automation. 

Váš modul by neměl záviset na žádném z jedinečných nastavení registru v hostiteli. Příklady jsou nastavení, která se provádějí při instalaci produktu. 

### <a name="module-file-paths"></a>Cesty k souborům modulu

Ujistěte se, že všechny soubory v modulu mají cesty s méně než 140 znaky. Jakékoli cesty delší než 140 znaků v délce způsobují problémy při importu runbooků. Azure Automation nemůže do relace PowerShellu importovat soubor s velikostí cesty větší než 140 znaků `Import-Module`.

## <a name="importing-modules"></a>Import modulů

Tato část definuje několik způsobů, jak můžete importovat modul do svého účtu Automation. 

### <a name="import-modules-in-azure-portal"></a>Importovat moduly v Azure Portal

Import modulu v Azure Portal:

1. Přejděte do svého účtu Automation.
2. V části **sdílené prostředky**vyberte **moduly** .
3. Klikněte na **Přidat modul**. 
4. Vyberte soubor **. zip** , který obsahuje váš modul.
5. Kliknutím na tlačítko **OK** začněte importovat proces.

### <a name="import-modules-using-powershell"></a>Importování modulů pomocí PowerShellu

K importu modulu do svého účtu Automation můžete použít rutinu [New-AzAutomationModule](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationmodule?view=azps-3.7.0) . Rutina převezme adresu URL pro balíček Module. zip.

```azurepowershell-interactive
New-AzAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

Stejnou rutinu můžete použít také k importu modulu z Galerie prostředí PowerShell přímo. Nezapomeňte vzít `ModuleName` a `ModuleVersion` z [Galerie prostředí PowerShell](https://www.powershellgallery.com).

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="import-modules-from-powershell-gallery"></a>Importovat moduly z Galerie prostředí PowerShell

[Galerie prostředí PowerShell](https://www.powershellgallery.com) moduly můžete importovat buď přímo z Galerie, nebo z vašeho účtu Automation.

Import modulu přímo z Galerie prostředí PowerShell:

1. Vyhledejte modul https://www.powershellgallery.com , který chcete importovat, a vyhledejte ho.
2. Klikněte na **nasadit a Azure Automation** na kartě **Azure Automation** v části **Možnosti instalace**. Tato akce otevře Azure Portal. 
3. Na stránce Import vyberte svůj účet Automation a klikněte na **OK**.

![Importovat modul Galerie prostředí PowerShell](../media/modules/powershell-gallery.png)

Import modulu Galerie prostředí PowerShell přímo z účtu Automation:

1. V části **sdílené prostředky**vyberte **moduly** . 
2. Na stránce moduly klikněte na **Procházet galerii**a potom vyhledejte v galerii modul. 
3. Vyberte modul, který chcete importovat, a klikněte na **importovat**. 
4. Na stránce Import spusťte proces importu kliknutím na tlačítko **OK** .

![Galerie prostředí PowerShell Import z Azure Portal](../media/modules/gallery-azure-portal.png)

## <a name="deleting-modules"></a>Odstraňování modulů

Pokud máte s modulem problémy nebo potřebujete přejít zpět k předchozí verzi modulu, můžete ho odstranit z účtu Automation. Původní verze [výchozích modulů](#default-modules) , které se importují při vytváření účtu Automation, nemůžete odstranit. Pokud je modul, který chcete odstranit, novější verze jednoho z [výchozích modulů](#default-modules), vrátí se zpět k verzi nainstalované s vaším účtem Automation. V opačném případě se odstraní kterýkoli modul, který odstraníte z účtu Automation.

### <a name="delete-modules-in-azure-portal"></a>Odstranit moduly v Azure Portal

Odebrání modulu v Azure Portal:

1. Přejděte do svého účtu Automation a v části **sdílené prostředky**vyberte **moduly** . 
2. Vyberte modul, který chcete odebrat. 
3. Na stránce **modul** vyberte **Odstranit**. Pokud je tento modul jedním z [výchozích modulů](#default-modules), vrátí se na verzi, která existovala při vytvoření účtu Automation.

### <a name="delete-modules-using-powershell"></a>Odstranění modulů pomocí PowerShellu

Pokud chcete odebrat modul pomocí PowerShellu, spusťte následující příkaz:

```azurepowershell-interactive
Remove-AzAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="adding-a-connection-type-to-your-module"></a>Přidání typu připojení do modulu

Můžete zadat vlastní [Typ připojení](../automation-connections.md) , který se použije v účtu Automation, a to tak, že do modulu přidáte volitelný soubor metadat. Tento soubor určuje typ připojení Azure Automation, který se má používat s rutinami modulu ve vašem účtu Automation. Další informace o vytváření modulu PowerShellu najdete v tématu [Postup zápisu modulu skriptu PowerShellu](https://docs.microsoft.com/powershell/scripting/developer/module/how-to-write-a-powershell-script-module?view=powershell-7).

![Použití vlastního připojení v Azure Portal](../media/modules/connection-create-new.png)

Soubor, který určuje vlastnosti typu připojení, má název ** &lt;Module&gt;-Automation. JSON** , který se nachází ve složce modulu komprimovaného souboru **zip** . Tento soubor obsahuje pole připojení, která jsou nutná pro připojení k systému nebo službě, kterou modul představuje. Konfigurace umožňuje vytvořit typ připojení v Azure Automation. Pomocí tohoto souboru můžete nastavit názvy polí, typy a to, jestli jsou pole zašifrovaná nebo volitelná pro typ připojení modulu. Následující příklad je šablonou ve formátu souboru **. JSON** , který definuje vlastnosti uživatelského jména a hesla:

```json
{
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  true,
      "Name":  "Username",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  true,
      "IsOptional":  false,
      "Name":  "Password",
      "TypeName":  "System.String"
   }
   ],
   "ConnectionTypeName":  "MyModuleConnection",
   "IntegrationModuleName":  "MyModule"
}
```

## <a name="next-steps"></a>Další kroky

* Další informace o použití Azure PowerShellch modulů najdete v tématu [Začínáme s Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azps-3.7.0).
* Další informace o vytváření modulů PowerShellu najdete v tématu vytvoření [modulu Windows PowerShellu](https://docs.microsoft.com/powershell/scripting/developer/module/writing-a-windows-powershell-module?view=powershell-7).
