---
title: Správa modulů v Azure Automation
description: Tento článek popisuje, jak spravovat moduly v Azure Automation.
services: automation
ms.service: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 859eea66d10e07a3503e33166bc77c8a97577acd
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548935"
---
# <a name="manage-modules-in-azure-automation"></a>Správa modulů v Azure Automation

Azure Automation umožňuje import modulů PowerShellu do vašeho účtu Automation používat runbooky založené na PowerShellu. Tyto moduly můžou být vlastní moduly, které jste vytvořili, moduly z Galerie PowerShellu nebo moduly AzureRM a Az pro Azure. Při vytváření účtu automatizace jsou některé moduly importovány ve výchozím nastavení.

## <a name="import-modules"></a>Import modulů

Existuje několik způsobů, jak můžete importovat modul do účtu automatizace. Následující části ukazují různé způsoby importu modulu.

> [!NOTE]
> Maximální velikost cesty pro soubor v modulu používaném v Azure Automation je 140 znaků. Automatizace nemůže importovat soubor s velikostí cesty přes 140 `Import-Module`znaků do relace prostředí PowerShell s .

### <a name="powershell"></a>PowerShell

Rutinu [New-AzureRmAutomationModule](/powershell/module/azurerm.automation/new-azurermautomationmodule) můžete použít k importu modulu do účtu automatizace. Rutina přebírá adresu URL balíčku module .zip.

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

Stejnou rutinu můžete také použít k přímému importu modulu z Galerie prostředí PowerShell. Ujistěte se, že chytit `ModuleName` a `ModuleVersion` z [PowerShell Galerie](https://www.powershellgallery.com).

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="azure-portal"></a>portál Azure

Import modulu na webu Azure Portal:

1. Přejděte na svůj účet Automation.
2. V části **Sdílené prostředky** **vyberte Moduly** .
3. Klepněte **na tlačítko Přidat modul**. 
4. Vyberte soubor **ZIP,** který obsahuje váš modul.
5. Klepnutím na **tlačítko OK** zahájíte import proces.

### <a name="powershell-gallery"></a>Galerie prostředí PowerShell

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

## <a name="delete-modules"></a>Odstranit moduly

Pokud máte problémy s modulem nebo potřebujete vrátit zpět k předchozí verzi modulu, můžete jej odstranit z účtu Automation. Původní verze [výchozích modulů,](#default-modules) které jsou importovány při vytváření účtu automatizace, nelze odstranit. Pokud modul odstranit je novější verze jednoho z [výchozích modulů](#default-modules), vrátí se zpět do verze, která byla nainstalována s účtem Automation. V opačném případě bude odebrán veškerý modul, který odstraníte z účtu Automation.

### <a name="azure-portal"></a>portál Azure

Odebrání modulu na webu Azure Portal:

1. Přejděte na účet Automation a v části **Sdílené prostředky**vyberte **Moduly** . 
2. Vyberte modul, který chcete odebrat. 
3. Na stránce **Modul** vyberte **Odstranit**. Pokud je tento modul jedním z [výchozích modulů](#default-modules), vrátí se zpět do verze, která existovala při vytvoření účtu Automatizace.

### <a name="powershell"></a>PowerShell

Chcete-li odebrat modul pomocí prostředí PowerShell, spusťte následující příkaz:

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="internal-cmdlets"></a>Vnitřní rutiny

V následující tabulce jsou uvedeny `Orchestrator.AssetManagement.Cmdlets` rutiny v interním modulu, který je importován do každého účtu automatizace. Tyto rutiny jsou přístupné ve vašich runbookech a konfiguracích DSC a umožňují interakci s prostředky v rámci vašeho účtu Automation. Interní rutiny navíc umožňují načítat tajné klíče z šifrovaných proměnných, pověření a šifrovaných připojení. Rutiny prostředí Azure PowerShell nejsou schopny načíst tyto tajné klíče. Tyto rutiny nevyžadují, abyste se při jejich použití implicitně připojovali k Azure, jako když se k ověření v Azure používáte účet Run As.

>[!NOTE]
>Tyto interní rutiny jsou k dispozici na Windows Hybrid Runbook Worker, ale ne na Linux hybridní Runbook Worker. Použijte odpovídající rutiny modulu [AzureRM.Automation](https://docs.microsoft.com/powershell/module/AzureRM.Automation/?view=azurermps-6.13.0) nebo [Az](../az-modules.md) pro sady Runbook, které běží přímo v počítači nebo proti prostředkům ve vašem prostředí. 

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

Přidáním volitelného souboru metadat do modulu můžete zadat vlastní [typ připojení,](../automation-connections.md) který se použije ve vašem účtu Automation. Tento soubor určuje typ připojení Azure Automation, který se má použít s rutinami modulu v účtu Automation. Chcete-li toho dosáhnout, musíte nejprve vědět, jak vytvořit modul prostředí PowerShell. Přečtěte [si informace o tom, jak napsat modul powershellového skriptu](/powershell/scripting/developer/module/how-to-write-a-powershell-script-module).

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

## <a name="module-best-practices"></a>Doporučené postupy modulu

Moduly Prostředí PowerShell můžete importovat do Azure Automation, aby jejich rutiny byly dostupné v rámci runbooků a jejich prostředků DSC dostupných v rámci konfigurací DSC. V zákulisí Azure Automation ukládá tyto moduly. V době spuštění úlohy sady Runbook a kompilace DSC je automatizace načte do karantén y Azure Automation, kde se spouštějí runbooky a kompilují konfigurace DSC. Všechny prostředky DSC v modulech jsou také automaticky umístěny na serveru pro vyžádat automatizaci DSC. Stroje je mohou vytáhnout při použití konfigurací DSC.

Při vytváření modulu PowerShellu pro použití v Azure Automation doporučujeme zvážit následující:

* Nezahrnovat složku verze do balíčku **ZIP.**  Tento problém je méně znepokojující pro sady Runbook, ale způsobit problém se službou konfigurace stavu. Azure Automation vytvoří složku verze automaticky při distribuci modulu do uzlů spravovaných DSC. Pokud složka verze existuje, skončíte se dvěma instancemi. Zde je příklad struktury složek pro modul DSC:

```powershell
myModule
  - DSCResources
    - myResourceFolder
      myResourceModule.psm1
      myResourceSchema.mof
  myModuleManifest.psd1
```

* Ke každé rutině v modulu přidejte i stručný obsah, popis a pomocný identifikátor URI. V prostředí PowerShell můžete definovat informace nápovědy `Get-Help` pro rutiny pomocí rutiny. Následující příklad ukazuje, jak definovat synopse a pomoci uri v souboru modulu **PSM1:**

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

* Pokud se modul připojuje k externí službě, definujte [typ připojení](#add-a-connection-type-to-your-module). Každá rutina v modulu by měla jako parametr přijmout objekt připojení (instanci tohoto typu připojení). Uživatelé mapují parametry datového zdroje připojení na odpovídající parametry rutiny pokaždé, když volají rutinu. Na základě výše uvedeného příkladu runbooku používá `ContosoConnection` příklad prostředku připojení Contoso volaný pro přístup k prostředkům Contoso a vrácení dat z externí služby.

  V následujícím příkladu jsou pole mapována na vlastnosti `UserName` a `Password` objektu `PSCredential` a poté předána rutině.

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

  Můžete povolit podobné chování pro vaše rutiny tím, že jim umožní přijmout objekt připojení přímo jako parametr, namísto pouze pole připojení pro parametry. Obvykle chcete nastavit parametr pro každý, takže uživatel, který nepoužívá Azure Automation můžete volat vaše rutiny bez vytvoření hashtable fungovat jako objekt připojení. Sada `UserAccount`parametrů se používá k předání vlastností pole připojení. `ConnectionObject`umožňuje projít spojení přímo skrz.

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

* Modul by měl být plně obsažen v balení, které lze kopírovat. Moduly Azure Automation se distribuují do karantén y automatizace, když je potřeba je spustit. Moduly musí pracovat nezávisle na hostiteli, na který běží. Měli byste být schopni zip a přesunout balíček modulu a mít to fungovat jako normální při importu do prostředí powershellu jiného hostitele. Aby k tomu došlo, modul by neměl záviset na žádné soubory mimo složku modulu, který je zazipovaný při importu modulu do Azure Automation. Modul by také neměl záviset na žádné jedinečné nastavení registru na hostiteli, jako jsou nastavení nastavená při instalaci produktu. Všechny soubory v modulu by měly mít cestu kratší než 140 znaků. Všechny cesty nad 140 znaků způsobují problémy s importem sady Runbook. Pokud tento osvědčený postup nedodržujete, modul není použitelný v Azure Automation.  

* Pokud odkazujete na [modul Azure PowerShell Az](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) ve vašem modulu, ujistěte se, že neodkazujete také na `AzureRM`. Modul nelze použít `Az` ve spojení s `AzureRM` modulem. `Az`je podporován v sadách Runbook, ale ve výchozím nastavení není importován. Další informace `Az` o modulu a důležité informace, které je třeba vzít v úvahu, najdete v tématu [podpora modulu Az v Azure Automation](../az-modules.md).

## <a name="default-modules"></a>Výchozí moduly

V následující tabulce jsou uvedeny moduly importované ve výchozím nastavení při vytvoření účtu automatizace. Automatizace můžete importovat novější verze těchto modulů. Původní verzi však nelze odebrat z účtu Automation, i když odstraníte novější verzi.

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

* Další informace o vytváření modulů PowerShellu najdete [v tématu Zápis modulu Prostředí Windows PowerShell](/powershell/scripting/developer/windows-powershell).
