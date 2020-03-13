---
title: Správa modulů v Azure Automation
description: Tento článek popisuje, jak spravovat moduly v Azure Automation
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e300bc0f29808215673407d21b65fe329e50ad45
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79278334"
---
# <a name="manage-modules-in-azure-automation"></a>Správa modulů v Azure Automation

Azure Automation poskytuje možnost importovat moduly PowerShellu do účtu Automation, který budou používat Runbooky založené na PowerShellu. Tyto moduly můžou být vlastní moduly, které jste vytvořili, z Galerie prostředí PowerShell, nebo moduly AzureRM a AZ pro Azure. Při vytváření účtu Automation se ve výchozím nastavení importují některé moduly.

## <a name="import-modules"></a>Import modulů

Existuje několik způsobů, jak můžete importovat modul do svého účtu Automation. V následujících částech jsou uvedeny různé způsoby importu modulu.

> [!NOTE]
> Maximální cesta k souboru v modulu, který se má použít v Azure Automation je 140 znaků. Žádná cesta přes 140 znaků nebude možné do relace PowerShellu importovat pomocí `Import-Module`.

### <a name="powershell"></a>PowerShell

K importu modulu do svého účtu Automation můžete použít [New-AzureRmAutomationModule](/powershell/module/azurerm.automation/new-azurermautomationmodule) . Rutina převezme adresu URL k balíčku zip modulu.

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

Stejnou rutinu můžete použít také k importu modulu z Galerie prostředí PowerShell přímo. Ujistěte se, že název **modulu** a **ModuleVersion** z [Galerie prostředí PowerShell](https://www.powershellgallery.com).

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="azure-portal"></a>Azure Portal

V Azure Portal přejděte do svého účtu Automation a v části **sdílené prostředky**vyberte **moduly** . Klikněte na **+ Přidat modul**. Vyberte soubor s **příponou. zip** , který obsahuje váš modul, a kliknutím na tlačítko **OK** začněte importovat proces.

### <a name="powershell-gallery"></a>Galerie prostředí PowerShell

Moduly z Galerie prostředí PowerShell můžete buď importovat z [Galerie prostředí PowerShell](https://www.powershellgallery.com) přímo, nebo z vašeho účtu Automation.

Pokud chcete modul naimportovat z Galerie prostředí PowerShell, navštivte https://www.powershellgallery.com a vyhledejte modul, který chcete naimportovat. Klikněte na **nasadit a Azure Automation** na kartě **Azure Automation** v části **Možnosti instalace**. Tato akce otevře Azure Portal. Na stránce **Import** vyberte svůj účet Automation a klikněte na **OK**.

![Importovat modul Galerie prostředí PowerShell](../media/modules/powershell-gallery.png)

Moduly můžete také importovat z Galerie prostředí PowerShell přímo z vašeho účtu Automation. Ve svém účtu Automation v části **sdílené prostředky**vyberte **moduly** . Na stránce moduly klikněte na **Procházet galerii**a potom vyhledejte Galerie prostředí PowerShell pro modul. Vyberte modul, který chcete importovat, a klikněte na **importovat**. Na stránce **Import** spusťte proces importu kliknutím na tlačítko **OK** .

![Galerie prostředí PowerShell Import z Azure Portal](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>Odstranit moduly

Pokud máte s modulem problémy nebo potřebujete přejít zpět k předchozí verzi modulu, můžete ho odstranit z účtu Automation. Původní verzi [výchozích modulů](#default-modules) , které se importují při vytváření účtu Automation, nemůžete odstranit. Pokud je modul, který chcete odstranit, novější verze jednoho z nainstalovaných [výchozích modulů](#default-modules) , vrátí se zpět k verzi nainstalované s vaším účtem Automation. V opačném případě budou odebrány všechny moduly, které odstraníte z účtu Automation.

### <a name="azure-portal"></a>Azure Portal

V Azure Portal přejděte do svého účtu Automation a v části **sdílené prostředky**vyberte **moduly** . Vyberte modul, který chcete odebrat. Na stránce **modul** vyberte **Odstranit**. Pokud je tento modul jedním z [výchozích modulů](#default-modules), vrátí se zpět na verzi, která byla přítomna při vytvoření účtu Automation.

### <a name="powershell"></a>PowerShell

Pokud chcete odebrat modul pomocí PowerShellu, spusťte následující příkaz:

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="internal-cmdlets"></a>Interní rutiny

Níže je uveden seznam rutin v interním `Orchestrator.AssetManagement.Cmdlets` modulu, který je importován do každého účtu Automation. Tyto rutiny jsou dostupné ve vašich sadách Runbook a konfiguracích DSC a umožňují interakci s prostředky v rámci vašeho účtu Automation. Kromě toho interní rutiny umožňují načíst tajné kódy z šifrovaných hodnot **proměnných** , **přihlašovacích údajů**a šifrovaných polí **připojení** . Rutiny Azure PowerShell nemůžou tyto tajné klíče načíst. Tyto rutiny nevyžadují při jejich používání implicitní připojení k Azure, jako je například použití účtu Spustit jako k ověření v Azure.

>[!NOTE]
>Tyto interní rutiny jsou k dispozici v Hybrid Runbook Worker Windows, nejsou k dispozici v Hybrid Runbook Worker pro Linux. Použijte odpovídající moduly [AzureRM. Automation](https://docs.microsoft.com/powershell/module/AzureRM.Automation/?view=azurermps-6.13.0) nebo [AZ](../az-modules.md) pro Runbooky běžící přímo na počítači nebo u prostředků ve vašem prostředí. 
>

|Název|Popis|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Spustit – AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

## <a name="add-a-connection-type-to-your-module"></a>Přidání typu připojení do modulu

Můžete zadat vlastní [Typ připojení](../automation-connections.md) , který můžete použít v účtu Automation, a to tak, že do svého modulu přidáte volitelný soubor. Tento soubor je soubor metadat, který určuje typ připojení Azure Automation, který se má používat s rutinami modulu ve vašem účtu Automation. Abyste to dosáhli, musíte nejdřív zjistit, jak vytvořit modul prostředí PowerShell. Další informace o vytváření modulů najdete v tématu [Jak napsat modul skriptu PowerShellu](/powershell/scripting/developer/module/how-to-write-a-powershell-script-module).

![Použití vlastního připojení v Azure Portal](../media/modules/connection-create-new.png)

Chcete-li přidat typ připojení Azure Automation, modul musí obsahovat soubor s názvem `<ModuleName>-Automation.json`, který určuje vlastnosti typu připojení. Soubor JSON se umístí do složky modulu komprimovaného souboru. zip. Tento soubor obsahuje pole připojení, která jsou nutná pro připojení k systému nebo službě, kterou modul představuje. Konfigurace končí vytvořením typu připojení v Azure Automation. Pomocí tohoto souboru můžete pro typ připojení modulu nastavit názvy polí, typy a to, jestli mají být pole zašifrovaná nebo volitelná. Následující příklad je šablonou ve formátu JSON, který definuje uživatelské jméno a vlastnost hesla:

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

## <a name="module-best-practices"></a>Doporučené postupy pro modul

Moduly PowerShellu se dají importovat do Azure Automation, aby byly rutiny dostupné pro použití v rámci runbooků a jejich prostředků DSC, které jsou k dispozici pro použití v rámci konfigurací DSC. Na pozadí aplikace Azure Automation ukládá tyto moduly a v době spuštění úlohy Runbooku a úlohy kompilace DSC je načte do Azure Automationch izolovaných prostorů, kde jsou spouštěny Runbooky a konfigurace DSC se zkompiluje. Všechny prostředky DSC v modulech se také automaticky umístí na server vyžádané replikace Automatizace DSC. Můžou je vyžádat počítače při použití konfigurací DSC.

Při vytváření modulu PowerShellu pro použití v Azure Automation doporučujeme vzít v úvahu následující:

* Nezahrnujte složku verze v rámci balíčku. zip.  Tento problém je méně důležitý pro Runbooky, ale způsobí problém se službou konfigurace stavu.  Azure Automation vytvoří složku verze automaticky, když je modul distribuován do uzlů spravovaných DSC a pokud existuje složka verze, skončíte se dvěma instancemi.  Příklad struktury složek pro modul DSC:

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

* Ke každé rutině v modulu přidejte i stručný obsah, popis a pomocný identifikátor URI. V PowerShellu můžete definovat určité informace nápovědy k rutinám, abyste uživatelům umožnili zobrazit nápovědu k jejich používání pomocí rutiny **Get-Help**. Následující příklad ukazuje, jak definovat souhrn a nápovědu URI pro v souboru modulu. psm1:

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

  Zadání těchto informací zobrazí tuto nápovědu pomocí rutiny **Get-Help** v konzole PowerShellu. Tento popis se zobrazí také v Azure Portal.

  ![Nápověda k modulu integrace](../media/modules/module-activity-description.png)

* Pokud se modul připojuje k externí službě, měl by obsahovat [Typ připojení](#add-a-connection-type-to-your-module). Každá rutina v modulu musí být schopná přijmout objekt připojení (instanci takového typu připojení) jako parametr. Uživatelé mapují parametry assetu připojení k odpovídajícím parametrům rutiny pokaždé, když volají rutinu. Na základě výše uvedeného příkladu Runbooku používá ukázkový prostředek připojení contoso s názvem ContosoConnection pro přístup k prostředkům společnosti Contoso a vrácení dat z externí služby.

  V následujícím příkladu jsou pole mapována na vlastnosti uživatelského jména a hesla objektu `PSCredential` a poté předány rutině.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  Jednodušší a lepší způsob, jak získat přístup k tomuto chování, je předání objektu připojení přímo do rutiny:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  Chování jako v předchozím příkladu pro rutiny můžete povolit tak, že jim umožníte přijmout objekt připojení přímo jako parametr místo pouze polí připojení pro parametry. Obvykle chcete nastavit sadu parametrů pro každý, aby uživatel, který nepoužívá Azure Automation, mohl volat rutiny bez vytvoření zatřiďovací tabulky, která by sloužila jako objekt připojení. Sada parametrů `UserAccount`slouží k předání vlastností pole připojení. `ConnectionObject` umožňuje předat připojení rovnou uživateli.

* Definujte typ výstupu pro všechny rutiny v modulu. Definování typu výstupu rutiny umožňuje technologii IntelliSense, aby vám v době návrhu pomohla zjistit výstupní vlastnosti rutiny, které použijete při vytváření obsahu. To je užitečné hlavně během grafického vytváření Runbooku služby Automation, kde znalostní báze pro čas návrhu je klíč pro snadné uživatelské prostředí s vaším modulem.

Přidejte `[OutputType([<MyOutputType>])]`, kde je MyOutputType platným typem. Další informace o elementu OutputType naleznete v tématu [About Functions OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). Následující kód je příkladem přidání `OutputType` do rutiny:

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

* Nastavte všechny rutiny v modulu jako bezstavové. Více úloh sady Runbook lze současně spustit ve stejné doméně aplikace a v rámci stejného procesu a izolovaného prostoru. Pokud se na těchto úrovních sdílí nějaký stav, úlohy můžou mít vliv na sebe navzájem. Toto chování může vést k občasnému a obtížnému diagnostikování problémů.  Následuje příklad špatného postupu.

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

* Modul by měl být plně obsažený v balíčku s příkazem xcopy. Azure Automation moduly jsou distribuovány do izolovaných prostorů automatizace, když Runbooky musí být spuštěny. Moduly potřebují pracovat nezávisle na hostiteli, na kterém běží. Měli byste být schopni vytvořit a přesunout balíček modulu a při importu do prostředí PowerShell jiného hostitele fungovat jako normální. Aby k tomu mohlo dojít, modul by neměl záviset na žádných souborech mimo složku modulu. Tato složka je složka, která se při importu modulu do Azure Automation vrátí do režimu zip. Modul by neměl být závislý na žádném z jedinečných nastavení registru na hostiteli, jako jsou nastavení, která jsou nastavená při instalaci produktu. Všechny soubory v modulu by měly mít cestu kratší než 140 znaků. Jakékoli cesty více než 140 znaků způsobí problémy s importem sady Runbook. Pokud tento osvědčený postup nesledujete, modul nebude možné použít v Azure Automation.  

* Pokud odkazujete na [prostředí Azure PowerShell AZ](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) modules in, ujistěte se, že neodkazujete na `AzureRM`. Modul `Az` nelze použít ve spojení s `AzureRM` moduly. `Az` jsou podporovány v sadách Runbook, ale nejsou importovány ve výchozím nastavení. Další informace o `Az`ch modulech a požadavcích, které je potřeba vzít v úvahu, najdete v tématu [AZ Module Support in Azure Automation](../az-modules.md).

## <a name="default-modules"></a>Výchozí moduly

Následující tabulka uvádí moduly, které jsou importovány ve výchozím nastavení při vytvoření účtu Automation. Moduly uvedené níže můžou mít naimportované novější verze, ale původní verzi nejde z účtu Automation odebrat, i když odstraníte novější verzi.

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
| GPRegistryPolicyParser | 0,2 |
| Microsoft.PowerShell.Core | 0 |
| Microsoft.PowerShell.Diagnostics |  |
| Microsoft.PowerShell.Management |  |
| Microsoft.PowerShell.Security |  |
| Microsoft.PowerShell.Utility |  |
| Microsoft. WSMan. Management |  |
| Orchestrator.AssetManagement.Cmdlets | 1 |
| PSDscResources | 2.9.0.0 |
| SecurityPolicyDsc | 2.1.0.0 |
| StateConfigCompositeResources | 1 |
| xDSCDomainjoin | 1.1 |
| xPowerShellExecutionPolicy | 1.1.0.0 |
| xRemoteDesktopAdmin | 1.1.0.0 |

## <a name="next-steps"></a>Další kroky

* Další informace o vytváření modulů PowerShellu najdete v článku [Psaní modulu Windows PowerShellu](/powershell/scripting/developer/windows-powershell)
