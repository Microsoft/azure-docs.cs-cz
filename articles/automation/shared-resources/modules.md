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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278334"
---
# <a name="manage-modules-in-azure-automation"></a>Správa modulů v Azure Automation

Azure Automation poskytuje možnost importovat moduly PowerShellu do vašeho účtu Automation, který budou používat runbooky založené na Prostředí PowerShell. Tyto moduly můžou být vlastní moduly, které jste vytvořili, z Galerie Prostředí PowerShell nebo z modulů AzureRM a Az pro Azure. Při vytváření účtu automatizace jsou některé moduly importovány ve výchozím nastavení.

## <a name="import-modules"></a>Import modulů

Existuje několik způsobů, jak můžete importovat modul do účtu automatizace. Následující části ukazují různé způsoby importu modulu.

> [!NOTE]
> Maximální cesta k souboru v modulu, který se má použít v Azure Automation, je 140 znaků. Libovolnou cestu přes 140 znaků nebude možné importovat do `Import-Module`relace prostředí PowerShell s .

### <a name="powershell"></a>PowerShell

[Modul New-AzureRmAutomationModule](/powershell/module/azurerm.automation/new-azurermautomationmodule) můžete použít k importu modulu do účtu automatizace. Rutina přebírá adresu URL do balíčku zip modulu.

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

Stejnou rutinu můžete také použít k přímému importu modulu z Galerie prostředí PowerShell. Ujistěte se, že uchopit **ModuleName** a **ModuleVersion** z [Galerie prostředí PowerShell](https://www.powershellgallery.com).

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="azure-portal"></a>portál Azure

Na webu Azure Portal přejděte na svůj účet automation a v části **Sdílené prostředky**vyberte **Moduly** . Klepněte na tlačítko **+ Přidat modul**. Vyberte soubor **ZIP,** který obsahuje váš modul, a klepnutím na **tlačítko Ok** začněte importovat proces.

### <a name="powershell-gallery"></a>Galerie prostředí PowerShell

Moduly z galerie PowerShellu lze importovat přímo z [Galerie prostředí PowerShell](https://www.powershellgallery.com) nebo z vašeho účtu automation.

Chcete-li importovat modul z Galerie https://www.powershellgallery.com prostředí PowerShell, přejděte na modul, který chcete importovat, a vyhledejte jej. Klikněte **na Nasadit do Azure Automation** na kartě Azure **Automation** v části **Možnosti instalace**. Tato akce otevře portál Azure. Na stránce **Import** vyberte účet automatizace a klepněte na tlačítko **OK**.

![Modul importu Galerie prostředí PowerShell](../media/modules/powershell-gallery.png)

Moduly můžete také importovat z Galerie prostředí PowerShell přímo z účtu automatizace. V účtu Automation vyberte **moduly v** části **Sdílené prostředky**. Na stránce modulů klikněte na **Procházet galerii**a potom vyhledejte modul v Galerii prostředí PowerShell. Vyberte modul, který chcete importovat, a klepněte na **tlačítko Importovat**. Na stránce **Import** klikněte na **OK** a spusťte proces importu.

![Import Galerie PowerShellu z webu Azure Portal](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>Odstranit moduly

Pokud máte problémy s modulem nebo potřebujete vrátit zpět k předchozí verzi modulu, můžete jej odstranit z účtu automation. Původní verzi [výchozích modulů,](#default-modules) které jsou importovány při vytváření účtu automatizace, nelze odstranit. Pokud je modul, který chcete odstranit, novější verzí jednoho z [nainstalovaných výchozích modulů,](#default-modules) vrátí se zpět k verzi, která byla nainstalována s vaším účtem Automation. V opačném případě budou odebrány všechny moduly, které odstraníte ze svého účtu Automation.

### <a name="azure-portal"></a>portál Azure

Na webu Azure Portal přejděte na svůj účet automation a v části **Sdílené prostředky**vyberte **Moduly** . Vyberte modul, který chcete odebrat. Na stránce **Modul** vyberte **Odstranit**. Pokud je tento modul jedním z [výchozích modulů](#default-modules), bude vrácen zpět na verzi, která byla přítomna při vytvoření účtu automatizace.

### <a name="powershell"></a>PowerShell

Chcete-li odebrat modul pomocí prostředí PowerShell, spusťte následující příkaz:

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="internal-cmdlets"></a>Vnitřní rutiny

Následuje seznam rutin v interním `Orchestrator.AssetManagement.Cmdlets` modulu, který je importován do každého účtu automatizace. Tyto rutiny jsou přístupné ve vašich runbookech a konfiguracích DSC a umožňují interakci s vašimi prostředky v rámci vašeho účtu Automation. Interní rutiny navíc umožňují načítat tajné klíče z šifrovaných **hodnot proměnných,** **pověření**a šifrovaných polí **připojení.** Rutiny prostředí Azure PowerShell nejsou schopny načíst tyto tajné klíče. Tyto rutiny nevyžadují, abyste se při jejich používání implicitně připojovali k Azure, jako je například použití účtu Run As k ověření do Azure.

>[!NOTE]
>Tyto interní rutiny jsou k dispozici na Windows Hybrid Runbook Worker, nejsou k dispozici na Linux Hybridní Runbook Worker. Použijte odpovídající moduly [AzureRM.Automation](https://docs.microsoft.com/powershell/module/AzureRM.Automation/?view=azurermps-6.13.0) nebo [Az](../az-modules.md) pro runbooky spuštěné přímo v počítači nebo proti prostředkům ve vašem prostředí. 
>

|Name (Název)|Popis|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Čekání-AutomatizaceÚloha|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

## <a name="add-a-connection-type-to-your-module"></a>Přidání typu připojení do modulu

Přidáním volitelného souboru do modulu můžete zadat vlastní [typ připojení,](../automation-connections.md) který můžete použít ve svém účtu Automation. Tento soubor je soubor metadat určující typ připojení Azure Automation, který se má použít s rutinami modulu ve vašem účtu Automation. Chcete-li toho dosáhnout, musíte nejprve vědět, jak vytvořit modul prostředí PowerShell. Další informace o vytváření modulů naleznete v [tématu Jak napsat modul skriptu prostředí PowerShell](/powershell/scripting/developer/module/how-to-write-a-powershell-script-module).

![Použití vlastního připojení na webu Azure Portal](../media/modules/connection-create-new.png)

Chcete-li přidat typ připojení Azure Automation, musí `<ModuleName>-Automation.json` modul obsahovat soubor s názvem, který určuje vlastnosti typu připojení. Soubor json je umístěn do složky modulu komprimovaného souboru ZIP. Tento soubor obsahuje pole připojení, které je nutné pro připojení k systému nebo službě, kterou modul představuje. Konfigurace skončí vytvoření typu připojení v Azure Automation. Pomocí tohoto souboru můžete nastavit názvy polí, typy a zda mají být pole šifrována nebo volitelná pro typ připojení modulu. Následující příklad je šablona ve formátu souboru json, která definuje vlastnost uživatelského jména a hesla:

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

## <a name="module-best-practices"></a>Doporučené postupy modulu

Moduly Prostředí PowerShell lze importovat do Azure Automation, aby jejich rutiny byly k dispozici pro použití v sadách Runbook a jejich prostředky DSC, které jsou k dispozici pro použití v konfiguracích DSC. Na pozadí Azure Automation ukládá tyto moduly a při spuštění úlohy runbooku a dsc kompilace úlohy, načte je do karantény azure automatizace, kde runbooky spustit a konfigurace DSC kompilovat. Všechny prostředky DSC v modulech jsou také automaticky umístěny na serveru pro vyžádat automatizaci DSC. Mohou být taženy stroji při použití konfigurací DSC.

Při vytváření modulu PowerShellu pro použití v Azure Automation doporučujeme zvážit následující:

* Nezahrnovat složku verze do balíčku ZIP.  Tento problém je méně znepokojující pro sady Runbook, ale způsobí problém se službou konfigurace stavu.  Azure Automation vytvoří složku verze automaticky při distribuci modulu do uzlů spravovaných DSC a pokud existuje složka verze, skončíte se dvěma instancemi.  Příklad struktury složek pro modul DSC:

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

* Ke každé rutině v modulu přidejte i stručný obsah, popis a pomocný identifikátor URI. V PowerShellu můžete definovat určité informace nápovědy k rutinám, abyste uživatelům umožnili zobrazit nápovědu k jejich používání pomocí rutiny **Get-Help**. Následující příklad ukazuje, jak definovat synopse a nápovědu URI pro v souboru modulu PSM1:

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

  Poskytnutí těchto informací ukazuje tuto nápovědu pomocí rutiny **Get-Help** v konzole PowerShell. Tento popis se taky zobrazí na webu Azure Portal.

  ![Nápověda k modulu integrace](../media/modules/module-activity-description.png)

* Pokud se modul připojí k externí službě, měl by obsahovat [typ připojení](#add-a-connection-type-to-your-module). Každá rutina v modulu musí být schopná přijmout objekt připojení (instanci takového typu připojení) jako parametr. Uživatelé mapují parametry datového zdroje připojení na odpovídající parametry rutiny pokaždé, když volají rutinu. Na základě výše uvedeného příkladu runbooku používá příklad prostředku připojení Contoso s názvem ContosoConnection pro přístup k prostředkům Contoso a vrácení dat z externí služby.

  V následujícím příkladu jsou pole mapována na vlastnosti `PSCredential` UserName a Password objektu a poté předána rutině.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  Jednodušší a lepší způsob, jak přistupovat k tomuto chování je přímo předávání objektu připojení k rutině:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  Chování můžete povolit jako předchozí příklad rutiny tím, že jim umožníte přijmout objekt připojení přímo jako parametr, namísto pouze pole připojení pro parametry. Obvykle chcete nastavit parametr pro každý, takže uživatel, který nepoužívá Azure Automation můžete volat vaše rutiny bez vytvoření hashtable fungovat jako objekt připojení. Sada `UserAccount`parametrů se používá k předání vlastností pole připojení. `ConnectionObject`umožňuje projít spojení přímo skrz.

* Definujte typ výstupu pro všechny rutiny v modulu. Definování typu výstupu rutiny umožňuje technologii IntelliSense, aby vám v době návrhu pomohla zjistit výstupní vlastnosti rutiny, které použijete při vytváření obsahu. Je to užitečné zejména při vytváření grafiky runbooku automatizace, kde znalost času návrhu je klíčem ke snadnému uživatelskému prostředí s modulem.

Přidejte, `[OutputType([<MyOutputType>])]` kde MyOutputType je platný typ. Další informace o OutputType, naleznete v [tématu O funkcích OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). Následující kód je příkladem `OutputType` přidání do rutiny:

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

* Nastavte všechny rutiny v modulu jako bezstavové. Více úloh runbooku lze současně spustit ve stejné AppDomain a stejný proces a izolovaného prostoru. Pokud je na těchto úrovních sdílen nějaký stav, úlohy se mohou vzájemně ovlivňovat. Toto chování může vést k přerušované a těžko diagnostikovat problémy.  Následuje příklad špatného postupu.

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

* Modul by měl být plně obsažen v balení, které lze kopírovat. Moduly Azure Automation se distribuují do karantén y automatizace, když je potřeba je spustit. Moduly musí pracovat nezávisle na hostiteli, na který běží. Měli byste být schopni Zip nahoru a přesunout balíček modulu a mít to fungovat jako normální při importu do prostředí powershellu jiného hostitele. Aby k tomu došlo, modul by neměl záviset na žádné soubory mimo složku modulu. Tato složka je složka, která získá zip up při importu modulu do Azure Automation. Modul by také neměl záviset na žádné jedinečné nastavení registru na hostiteli, jako jsou nastavení nastavená při instalaci produktu. Všechny soubory v modulu by měly mít cestu kratší než 140 znaků. Všechny cesty nad 140 znaků způsobí problémy s importem sady Runbook. Pokud tento osvědčený postup není dodržena, modul nebude použitelné v Azure Automation.  

* Pokud odkazujete na [moduly Azure Powershell Az](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) ve vašem `AzureRM`modulu, ujistěte se, že neodkazujete také na . Modul `Az` nelze použít ve spojení s `AzureRM` moduly. `Az`je podporován v sadách Runbook, ale ve výchozím nastavení se neimportuje. Další informace `Az` o modulech a aspektech, které je třeba vzít v úvahu, najdete v tématu [podpora modulů Az v Azure Automation](../az-modules.md).

## <a name="default-modules"></a>Výchozí moduly

V následující tabulce jsou uvedeny moduly, které jsou importovány ve výchozím nastavení při vytvoření účtu automatizace. Níže uvedené moduly mohou importovat novější verze, ale původní verzi nelze odebrat z účtu Automation, i když odstraníte novější verzi.

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

## <a name="next-steps"></a>Další kroky

* Další informace o vytváření modulů PowerShellu najdete v článku [Psaní modulu Windows PowerShellu](/powershell/scripting/developer/windows-powershell)
