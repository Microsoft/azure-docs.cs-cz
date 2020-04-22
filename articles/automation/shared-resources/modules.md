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
ms.openlocfilehash: 2a567b156c3a7e0c48e2c2f8db2d268e0eb3307c
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770948"
---
# <a name="manage-modules-in-azure-automation"></a>Správa modulů ve službě Azure Automation

Azure Automation umožňuje importovat moduly PowerShellu pro povolení rutin v sadách Runbook a DSC v konfiguracích DSC. Mezi moduly používané v Azure Automation patří:

* [Azure PowerShell Az.Automation](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)
* [Azure PowerShell AzureRM.Automation](https://docs.microsoft.com/powershell/module/azurerm.automation/?view=azurermps-6.13.0)
* Interní `Orchestrator.AssetManagement.Cmdlets` modul pro agenta Log Analytics pro Windows
* [AzureAutomationAuthoringToolkit](https://www.powershellgallery.com/packages/AzureAutomationAuthoringToolkit/0.2.3.9)
* Další moduly Prostředí PowerShell
* Vlastní moduly, které vytvoříte 

Při vytváření účtu automatizace Azure Automation importuje některé moduly ve výchozím nastavení. Viz [Výchozí moduly](#default-modules).

Když Azure Automation spustí úlohy kompilace runbooku a DSC, načte moduly do karantén, kde mohou spustit sady Runbook a konfigurace DSC mohou zkompilovat. Automatizace také automaticky umístí všechny prostředky DSC v modulech na serveru pro vyžádat DSC. Počítače můžete vytáhnout prostředky při použití konfigurace DSC.

>[!NOTE]
>Nezapomeňte importovat pouze moduly, které vaše sady Runbook a Konfigurace DSC skutečně potřebují. V žádném případě neimportujte souhrnný modul, například Az.Automation.

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci modulu AZ na pracovníka hybridní sady Runbook najdete [v tématu Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). U vašeho účtu Automation můžete aktualizovat moduly na nejnovější verzi pomocí [funkce Jak aktualizovat moduly Azure PowerShellu v Azure Automation](../automation-update-azure-modules.md).

## <a name="default-modules"></a>Výchozí moduly

V následující tabulce jsou uvedeny moduly, které Azure Automation importuje ve výchozím nastavení při vytváření účtu Automatizace. Automatizace můžete importovat novější verze těchto modulů. Původní verzi však nelze odebrat z účtu Automation, i když odstraníte novější verzi. Všimněte si, že tyto výchozí moduly obsahují několik modulů AzureRM. 

> [!NOTE]
> Nedoporučujeme měnit moduly a runbooky v účtech Automatizace, které obsahují žádná řešení. 

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
| Microsoft.PowerShell.Core | 0 |
| Microsoft.PowerShell.Diagnostika |  |
| Microsoft.PowerShell.Management |  |
| Microsoft.PowerShell.Security |  |
| Microsoft.PowerShell.Utility |  |
| Microsoft.Wsman.Management |  |
| Orchestrator.AssetManagement.Rutiny | 1 |
| PSDscZdroje | 2.9.0.0 |
| Bezpečnostní politikaDsc | 2.1.0.0 |
| StavConfigCompositeResources | 1 |
| xDSCDomainjoin | 1.1 |
| xPowerShellExecutionPolicy | 1.1.0.0 |
| xRemoteDesktopAdmin | 1.1.0.0 |


Moduly Az.Automation jsou upřednostňovány v konfiguracích runbooků a DSC. Azure Automation však neimportuje souhrnný modul Az automaticky do žádné ho nové nebo existující účty automatizace. Další informace o práci s těmito moduly naleznete [v tématu Migrace na moduly Az](#migrating-to-az-modules).

## <a name="internal-cmdlets"></a>Vnitřní rutiny

V následující tabulce jsou definovány interní rutiny podporované modulem. `Orchestrator.AssetManagement.Cmdlets` Použijte je v sadách Runbook a DSC konfigurace pro interakci s prostředky automatizace v rámci účtu automatizace. Rutiny jsou navrženy tak, aby načítaly tajné klíče z šifrovaných proměnných, pověření a šifrovaných připojení. 

> [!NOTE]
> Rutiny prostředí Azure PowerShell nelze získat tajné klíče prostředků, které můžete načíst interní rutiny. 

|Název|Popis|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Čekání-AutomatizaceÚloha|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

Tyto interní rutiny jsou k dispozici na Windows Hybrid Runbook Worker, ale ne na Linux hybridní Runbook Worker. Jsou provedeny z orchestrator izolovaného prostoru, který je používán hybridní pracovník.  Jejich použití nevyžaduje implicitní připojení k Azure, jako při použití účtu Spustit jako pro ověřování.

Místo použití interních rutin použijte rutiny Az nebo AzureRM pro sady Runbook a konfigurace spuštěné přímo v počítači nebo proti prostředkům ve vašem prostředí. V těchto případech se musíte implicitně připojit k Azure při použití rutin, jako když používáte účet Spustit jako k ověření azure. 

## <a name="modules-supporting-get-automationpscredential"></a>Moduly podporující get-automationPSCredential

Pro místní vývoj pomocí Azure Automation Authoring `Get-AutomationPSCredential` Toolkit je rutina součástí sestavení [AzureAutomationAuthoringToolkit](https://www.powershellgallery.com/packages/AzureAutomationAuthoringToolkit/0.2.3.9). Pro Azure práci s kontextem automatizace `Orchestrator.AssetManagement.Cmdlets`rutina je v . Další informace o používání přihlašovacích údajů v Azure Automation najdete [v tématu Prostředky pověření v Azure Automation](credentials.md).

## <a name="migrating-to-az-modules"></a>Migrace na moduly Az

Při používání modulů Az v Azure Automation je třeba vzít v úvahu několik věcí:

* Řešení vyšší úrovně ve vašem účtu Automation mohou používat sady Runbook a moduly. Proto úpravy runbooků nebo upgradovacích modulů mohou potenciálně způsobit problémy s vašimi řešeními. Před importem modulů Az byste měli pečlivě otestovat všechny sady Runbook a řešení v samostatném účtu Automation. 

* Jakékoli změny modulů mohou negativně ovlivnit [řešení Start/Stop VM během mimo pracovní dobu](../automation-solution-vm-management.md). 

* Import modulu Az do účtu Automation automaticky neimportuje modul v relaci prostředí PowerShell, který sady Runbook používají. Moduly se importují do relace prostředí PowerShell v následujících situacích:

    * Když runbook vyvolá rutinu z modulu
    * Když runbook importuje modul `Import-Module` explicitně s rutinou
    * Když runbook importuje jiný závislý modul

Po dokončení migrace modulů se nepokoušejte spustit runbooky pomocí modulů AzureRM na účtu Automation. Doporučujeme také neimportovat nebo aktualizovat moduly AzureRM na účtu. Zvažte migrovaný účet na Az.Automation a pracujte pouze s moduly Az. 

>[!IMPORTANT]
>Když vytvoříte nový účet Automation, Azure Automation nainstaluje moduly AzureRM ve výchozím nastavení. Stále můžete aktualizovat výukové runbooky s rutinami AzureRM. Tyto sady Runbook byste však neměli spouštět.

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>Zastavení a zrušení plánování všech runbooků, které používají moduly AzureRM

Chcete-li zajistit, že nespustíte žádné existující sady Runbook, které používají moduly AzureRM, zastavte a odplánujte všechny ovlivněné sady Runbook. Můžete vidět, jaké plány existují a které plány odebrat spuštěním kódu podobného tomuto příkladu:

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

Je důležité zkontrolovat každý plán zvlášť, abyste se ujistili, že ho můžete v budoucnu přeplánovat pro vaše sady Runbook, pokud je to nutné.

### <a name="import-the-az-modules"></a>Import modulů Az

V této části se dozvíte, jak importovat moduly Az na webu Azure Portal. Nezapomeňte importovat pouze moduly Az, které potřebujete, nikoli celý modul Az.Automation. Vzhledem k [tomu, že Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) je závislost pro ostatní moduly Az, nezapomeňte importovat tento modul před ostatními.

1. V části **Sdílené prostředky**vyberte v části Sdílené prostředky **položku Moduly** . 
2. Kliknutím na **Procházet galerii** otevřete stránku Galerie procházení.  
3. Na vyhledávacím panelu zadejte název `Az.Accounts`modulu, například . 
4. Na stránce Modul prostředí PowerShell klikněte na **Importovat** modul do účtu Automation.

    ![Import modulů do účtu Automatizace](../media/modules/import-module.png)

Tento proces importu lze provést také prostřednictvím [Galerie prostředí PowerShell](https://www.powershellgallery.com) vyhledáním modulu pro import. Jakmile modul najdete, vyberte ho, zvolte kartu **Azure Automation** a klikněte na **Nasadit do Azure Automation**.

![Import modulů přímo z galerie](../media/modules/import-gallery.png)

### <a name="test-your-runbooks"></a>Otestujte své runbooky

Po importu modulů Az do účtu Automation můžete začít upravovat runbooky a používat nové moduly. Většina rutin má stejné názvy jako pro moduly AzureRM, s tím rozdílem, že předpona AzureRM (nebo AzureRm) byla změněna na Az. Seznam modulů, které nedodržují tuto konvenci pojmenování, naleznete v [seznamu výjimek](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

Jedním ze způsobů, jak otestovat změnu runbooku pro `Enable-AzureRmAlias -Scope Process` použití nových rutin, je použití na začátku runbooku. Přidáním tohoto příkazu do runbooku může být skript spuštěn beze změn.

## <a name="authoring-modules"></a>Vývojové moduly

Doporučujeme postupovat podle důležité informace v této části při vytváření modulu Prostředí PowerShell pro použití v Azure Automation.

### <a name="references-to-azurerm-and-az"></a>Odkazy na AzureRM a Az

Pokud odkazujete na moduly Az ve vašem modulu, ujistěte se, že neodkazujete také na moduly AzureRM. Nelze použít obě sady modulů současně. 

### <a name="version-folder"></a>Složka verze

Nezahrnujte složku verze do balíčku **ZIP** pro váš modul.  Tento problém je méně znepokojující pro sady Runbook, ale způsobit problém se službou konfigurace stavu (DSC). Azure Automation vytvoří složku verze automaticky při distribuci modulu do uzlů spravovaných DSC. Pokud složka verze existuje, skončíte se dvěma instancemi. Zde je příklad struktury složek pro modul DSC:

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

### <a name="help-information"></a>Informace nápovědy

Zahrňte přehled, popis a nápovědu URI pro každou rutinu v modulu. V prostředí PowerShell můžete definovat informace nápovědy `Get-Help` pro rutiny pomocí rutiny. Následující příklad ukazuje, jak definovat synopse a pomoci uri v souboru modulu **PSM1.**

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

  Poskytnutí těchto informací zobrazuje `Get-Help` text nápovědy prostřednictvím rutiny v konzole PowerShell. Tento text se také zobrazí na webu Azure Portal.

  ![Nápověda k modulu integrace](../media/modules/module-activity-description.png)

### <a name="connection-type"></a>Typ připojení

Pokud se modul připojuje k externí službě, definujte [typ připojení](#adding-a-connection-type-to-your-module). Každá rutina v modulu by měla jako parametr přijmout objekt připojení (instanci tohoto typu připojení). Uživatelé mapují parametry datového zdroje připojení na odpovídající parametry rutiny pokaždé, když volají rutinu. Následující příklad runbooku založený na příkladu v předchozí části používá `ContosoConnection` prostředek připojení Contoso volaný pro přístup k prostředkům Contoso a vrácení dat z externí služby. V tomto příkladu jsou pole `UserName` mapována na vlastnosti a `Password` objektu `PSCredential` a poté předána rutině.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  Jednodušší a lepší způsob, jak přistupovat k tomuto chování je přímým předáním objektu připojení k rutině:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  Můžete povolit podobné chování pro vaše rutiny tím, že jim umožní přijmout objekt připojení přímo jako parametr, namísto pouze pole připojení pro parametry. Obvykle chcete nastavit parametr pro každý, takže uživatel, který nepoužívá Azure Automation můžete volat vaše rutiny bez vytvoření hashtable fungovat jako objekt připojení. Sada `UserAccount` parametrů se používá k předání vlastností pole připojení. `ConnectionObject`umožňuje projít spojení přímo skrz.

### <a name="output-type"></a>Typ výstupu

Definujte typ výstupu pro všechny rutiny v modulu. Definování typu výstupu pro rutinu umožňuje technologie IntelliSense v době návrhu určit výstupní vlastnosti rutiny během vytváření. Tento postup je užitečný zejména při vytváření grafických runbooků, pro které jsou znalosti v době návrhu klíčem ke snadnému uživatelskému prostředí s modulem.

Přidejte, `[OutputType([<MyOutputType>])]` kde `MyOutputType` je platný typ. Další informace `OutputType`naleznete v [tématu About Functions OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). Následující kód je příkladem `OutputType` přidání do rutiny:

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

  Toto chování je podobné funkci "typ dopředu" výstupu rutiny v prostředí PowerShell ISE bez nutnosti spouštět.

  ![POSH IntelliSense](../media/modules/automation-posh-ise-intellisense.png)

### <a name="cmdlet-state"></a>Stav rutiny

Proveďte všechny rutiny v modulu bezstavové. Více úloh runbooku může současně `AppDomain` spouštět ve stejném a stejném procesu a izolovaném prostoru. Pokud je na těchto úrovních sdílen nějaký stav, úlohy se mohou vzájemně ovlivňovat. Toto chování může vést k občasné a těžko diagnostikovat problémy. Zde je příklad toho, co nedělat:

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

Ujistěte se, že modul je plně obsažen v balíčku, který lze kopírovat pomocí xcopy. Moduly Azure Automation se distribuují do karantén y automatizace při spuštění runbooků. Moduly musí pracovat nezávisle na hostiteli, který je spouští. 

Měli byste být schopni zip a přesunout balíček modulu a mít to fungovat jako normální při importu do prostředí powershellu jiného hostitele. Aby k tomu došlo, ujistěte se, že modul není závislá na žádné soubory mimo složku modulu, který je zazipovaný při importu modulu do Azure Automation. 

Modul by neměl záviset na žádné jedinečné nastavení registru na hostiteli. Příkladem jsou nastavení, která jsou provedena při instalaci produktu. 

### <a name="module-file-paths"></a>Cesty k souborům modulu

Ujistěte se, že všechny soubory v modulu mají cesty s méně než 140 znaků. Všechny cesty delší než 140 znaků způsobují problémy s importem runbooků. Azure Automation nemůže importovat soubor s velikostí cesty přes 140 `Import-Module`znaků do relace PowerShellu s .

## <a name="importing-modules"></a>Import modulů

Tato část definuje několik způsobů, jak můžete importovat modul do účtu automatizace. 

### <a name="import-modules-in-azure-portal"></a>Import modulů na webu Azure Portal

Import modulu na webu Azure Portal:

1. Přejděte na svůj účet Automation.
2. V části **Sdílené prostředky** **vyberte Moduly** .
3. Klepněte **na tlačítko Přidat modul**. 
4. Vyberte soubor **ZIP,** který obsahuje váš modul.
5. Klepnutím na **tlačítko OK** zahájíte import proces.

### <a name="import-modules-using-powershell"></a>Import modulů pomocí PowerShellu

Rutinu [New-AzAutomationModule](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationmodule?view=azps-3.7.0) můžete použít k importu modulu do účtu automation. Rutina přebírá adresu URL balíčku module .zip.

```azurepowershell-interactive
New-AzAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

Stejnou rutinu můžete také použít k přímému importu modulu z Galerie prostředí PowerShell. Ujistěte se, že chytit `ModuleName` a `ModuleVersion` z [PowerShell Galerie](https://www.powershellgallery.com).

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="import-modules-from-powershell-gallery"></a>Import modulů z Galerie prostředí PowerShell

Moduly [PowerShell Gallery](https://www.powershellgallery.com) můžete importovat buď přímo z galerie, nebo z účtu Automation.

Import modulu přímo z Galerie prostředí PowerShell:

1. Přejděte https://www.powershellgallery.com na soubor a vyhledejte modul, který chcete importovat.
2. Klikněte **na Nasadit do Azure Automation** na kartě Azure **Automation** v části **Možnosti instalace**. Tato akce otevře portál Azure. 
3. Na stránce Import vyberte účet Automatizace a klepněte na tlačítko **OK**.

![Modul importu Galerie prostředí PowerShell](../media/modules/powershell-gallery.png)

Import modulu PowerShell Gallery přímo z účtu Automation:

1. V části **Sdílené prostředky** **vyberte Moduly** . 
2. Na stránce Moduly klikněte na **Galerie procházení**a vyhledejte v galerii modul. 
3. Vyberte modul, který chcete importovat, a klepněte na **tlačítko Importovat**. 
4. Na stránce Import klikněte na **OK** a spusťte proces importu.

![Import Galerie PowerShellu z webu Azure Portal](../media/modules/gallery-azure-portal.png)

## <a name="deleting-modules"></a>Odstranění modulů

Pokud máte problémy s modulem nebo potřebujete vrátit zpět k předchozí verzi modulu, můžete jej odstranit z účtu Automation. Původní verze [výchozích modulů,](#default-modules) které jsou importovány při vytváření účtu automatizace, nelze odstranit. Pokud modul odstranit je novější verze jednoho z [výchozích modulů](#default-modules), vrátí se zpět do verze, která byla nainstalována s účtem Automation. V opačném případě bude odebrán veškerý modul, který odstraníte z účtu Automation.

### <a name="delete-modules-in-azure-portal"></a>Odstranění modulů na webu Azure Portal

Odebrání modulu na webu Azure Portal:

1. Přejděte na účet Automation a v části **Sdílené prostředky**vyberte **Moduly** . 
2. Vyberte modul, který chcete odebrat. 
3. Na stránce **Modul** vyberte **Odstranit**. Pokud je tento modul jedním z [výchozích modulů](#default-modules), vrátí se zpět do verze, která existovala při vytvoření účtu Automatizace.

### <a name="delete-modules-using-powershell"></a>Odstranění modulů pomocí Prostředí PowerShell

Chcete-li odebrat modul pomocí prostředí PowerShell, spusťte následující příkaz:

```azurepowershell-interactive
Remove-AzAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="adding-a-connection-type-to-your-module"></a>Přidání typu připojení do modulu

Přidáním volitelného souboru metadat do modulu můžete zadat vlastní [typ připojení,](../automation-connections.md) který se použije ve vašem účtu Automation. Tento soubor určuje typ připojení Azure Automation, který se má použít s rutinami modulu v účtu Automation. Další informace o vytváření modulu PowerShellu najdete v [tématu Jak napsat modul PowerShell Script .](https://docs.microsoft.com/powershell/scripting/developer/module/how-to-write-a-powershell-script-module?view=powershell-7)

![Použití vlastního připojení na webu Azure Portal](../media/modules/connection-create-new.png)

Soubor určující vlastnosti typu připojení se nazývá ** &lt;ModuleName&gt;-Automation.json** a nachází se ve složce modulu komprimovaného souboru **ZIP.** Tento soubor obsahuje pole připojení, které jsou nutné pro připojení k systému nebo službě, kterou modul představuje. Konfigurace umožňuje vytvoření typu připojení v Azure Automation. Pomocí tohoto souboru můžete nastavit názvy polí, typy a zda jsou pole šifrována nebo volitelná pro typ připojení modulu. Následující příklad je šablona ve formátu **souboru JSON,** která definuje vlastnosti uživatelského jména a hesla:

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

* Další informace o používání modulů Azure PowerShellu najdete [v tématu Začínáme s Azure PowerShellem](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azps-3.7.0).
* Další informace o vytváření modulů PowerShellu najdete [v tématu Zápis modulu Prostředí Windows PowerShell](https://docs.microsoft.com/powershell/scripting/developer/module/writing-a-windows-powershell-module?view=powershell-7).
