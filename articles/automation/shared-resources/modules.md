---
title: Spravovat moduly ve službě Azure Automation
description: Tento článek popisuje, jak spravovat moduly ve službě Azure Automation
services: automation
ms.service: automation
ms.subservice: shared-resources
author: georgewallace
ms.author: gwallace
ms.date: 03/13/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: fa7f5d3fb38eb1dbca51dec9b73dca3c998436aa
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2019
ms.locfileid: "57909094"
---
# <a name="manage-modules-in-azure-automation"></a>Spravovat moduly ve službě Azure Automation

Azure Automation poskytuje možnost importovat další moduly Powershellu do účtu Automation pro sady runbook na základě prostředí PowerShell. Tyto moduly je možné vlastní moduly, které jste vytvořili z Galerie prostředí PowerShell nebo moduly AzureRM a Az pro Azure.

## <a name="import-modules"></a>Import modulů

Existuje více způsobů, můžete importovat modul do vašeho účtu Automation. Následující části ukazují různé způsoby, jak importovat modul.

> [!NOTE]
> Maximální počet cestu k souboru v modulu, který se má použít ve službě Azure Automation je 140 znaků. Než 140 znaků libovolnou cestu nebude možné importovat do relace prostředí PowerShell s `Import-Module`.

### <a name="powershell"></a>PowerShell

Můžete použít [New-AzureRmAutomationModule](/powershell/module/azurerm.automation/new-azurermautomationmodule) postup importování modulu do účtu Automation. Rutina přebírá adresu url do modulu zip balíčku.

```azurepowershell-interactive
New-AzureRmAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

### <a name="azure-portal"></a>portál Azure

Na webu Azure Portal, přejděte do účtu Automation a vyberte **moduly** pod **sdílené prostředky**. Klikněte na tlačítko **+ přidat modul**. Vyberte **ZIP** soubor, který obsahuje modul a klikněte na tlačítko **Ok** spusťte import procesu.

### <a name="powershell-gallery"></a>Galerie prostředí PowerShell

Moduly z Galerie prostředí PowerShell může být buď importovaná z [Galerie prostředí PowerShell](https://www.powershellgallery.com) přímo nebo z vašeho účtu Automation.

Postup importování modulu z Galerie prostředí PowerShell, přejděte na https://www.powershellgallery.com a vyhledejte modul, který chcete importovat. Klikněte na tlačítko **nasadit do Azure Automation** na **Azure Automation** kartu **možnosti instalace**. Tato akce otevře Azure Portal. Na **Import** stránky, vyberte svůj účet Automation a klikněte na tlačítko **OK**.

![Import modulu Galerie prostředí PowerShell](../media/modules/powershell-gallery.png)

Můžete také importovat další moduly z Galerie prostředí PowerShell přímo z vašeho účtu Automation. Ve vašem účtu Automation vyberte **moduly** pod **sdílené prostředky**. Klikněte na stránce modulů **procházet galerii**. Otevře **procházet galerii** stránky. Na této stránce můžete prohledat galerii prostředí PowerShell pro modul. Vyberte modul, který chcete importovat a klikněte na tlačítko **importovat**. Na **importovat** klikněte na **OK** spusťte proces importu.

![Galerie prostředí PowerShell importovat z webu Azure portal](../media/modules/gallery-azure-portal.png)

## <a name="internal-cmdlets"></a>Interní rutiny

Tady je seznam rutin v interní `Orchestrator.AssetManagement.Cmdlets` modul, který je importován každý účet služby Automation. Tyto rutiny jsou dostupné v runboocích a konfiguracích DSC a bylo možné interagovat s vašimi prostředky v rámci účtu Automation. Kromě toho interní rutiny umožňují načíst tajné kódy z šifrované **proměnné** hodnoty, **přihlašovací údaje**a šifrované **připojení** pole. Rutiny prostředí Azure PowerShell, nebudou se moct k načtení těchto tajných kódů. Tyto rutiny nevyžadují implicitně připojení k Azure při jejich používání. To je užitečné pro scénáře, ve kterém jste připojení, jako je například účet Spustit jako, který je třeba použít pro ověření do Azure.

|Název|Popis|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

## <a name="add-a-connection-type-to-your-module"></a>Přidat typ připojení modulu

Můžete zadat vlastní [typ připojení](../automation-connections.md) pro použití ve vašem účtu Automation přidáním volitelný soubor do modulu. Tento soubor je soubor metadat určujících typ připojení Azure Automation pro použití s rutinami modulu v účtu Automation. Jak tohoto dosáhnout musí nejdřív vědět, jak vytvořit modul Powershellu. Další informace o vytváření modulu najdete v tématu [jak napsat modul skriptu Powershellu](/powershell/developer/module/how-to-write-a-powershell-script-module).

![Použít vlastní připojení na webu Azure Portal](../media/modules/connection-create-new.png)

Chcete-li přidat typ připojení Azure Automation, modulu musí obsahovat soubor s názvem `<ModuleName>-Automation.json` , který určuje vlastnosti typu připojení. Toto je soubor json, který je umístěn ve složce modulu vašeho komprimovaného souboru .zip. Tento soubor obsahuje pole, která je požadována pro připojení k systému nebo služby, které modul představuje připojení. Tato konfigurace končí vytvoření typu připojení ve službě Azure Automation. Použití tohoto souboru můžete nastavit názvy polí, typy, a určuje, zda mají být pole šifrovaná nebo volitelná pro typ připojení modulu. Následující příklad uvádíme šablonu ve formátu json, který definuje vlastnost uživatelské jméno a heslo:

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

## <a name="module-best-practices"></a>Osvědčené postupy modulu

Moduly Powershellu můžete importovat do Azure Automation k jejich rutiny zpřístupnili pro použití v rámci sady runbook a jejich prostředků DSC k dispozici pro použití v rámci konfigurací DSC. Na pozadí Azure Automation tyto moduly ukládá a v úlohy runbooku a čas spuštění úlohy kompilace DSC je načte do karantény Azure Automation, kde spuštění sady runbook a kompilaci konfigurace DSC. Všechny prostředky DSC v modulech se také automaticky umístí na serveru vyžádané replikace automatizace DSC. Si je mohly vyžádat počítače při jejich použít konfigurace DSC.

Doporučujeme, abyste že při vytváření modulu Powershellu pro použití ve službě Azure Automation se zvažte následující:

* Ke každé rutině v modulu přidejte i stručný obsah, popis a pomocný identifikátor URI. V PowerShellu můžete definovat určité informace nápovědy k rutinám, abyste uživatelům umožnili zobrazit nápovědu k jejich používání pomocí rutiny **Get-Help**. Následující příklad ukazuje, jak definovat stručný a pomocný identifikátor URI pro v souboru .psm1 modul:

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

  Zadání těchto informací zobrazí tuto nápovědu pomocí **Get-Help** rutiny v konzole prostředí PowerShell. Tento popis se také zobrazí na webu Azure Portal.

  ![Nápověda k modulu integrace](../media/modules/module-activity-description.png)

* Pokud se modul připojuje k externí služby, měl by obsahovat [typ připojení](#add-a-connection-type-to-your-module). Každá rutina v modulu musí být schopná přijmout objekt připojení (instanci takového typu připojení) jako parametr. To umožňuje uživatelům mapovat parametry prostředku připojení na odpovídající parametry rutiny při každém volání rutiny. Založený na výše uvedený příklad runbooku používá asset připojení Contoso příklad volá ContosoConnection přístup k prostředkům společnosti Contoso a vrátit data z externí služby.

  V následujícím příkladu jsou pole mapovány na vlastnosti uživatelského jména a hesla `PSCredential` objekt a potom předá do rutiny.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

  Jednodušším a lepším způsobem, abyste toto chování je předat objekt připojení přímo do rutiny:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

  Chování podobně jako v předchozím příkladu můžete svým rutinám povolit neboť jim umožňuje přijímat objekt připojení přímo jako parametr místo přijímáním polí připojení pro parametry. Obvykle byste měli u každé sady parametrů tak, aby uživatel bez použití služby Azure Automation mohl vaše rutiny volat bez vytváření zatřiďovací tabulku tak, aby fungoval jako objekt připojení. Sada parametrů `UserAccount`, slouží k předávání vlastností polí připojení. `ConnectionObject` umožňuje předat připojení přímo prostřednictvím.

* Definujte výstupní typ pro všechny rutiny v modulu. Definování typu výstupu rutiny umožňuje technologii IntelliSense, aby vám v době návrhu pomohla zjistit výstupní vlastnosti rutiny, které použijete při vytváření obsahu. To se hodí zejména při automatizaci vytváření grafického obsahu runbooku, kde je znalost doby návrhu klíčová pro snadnou práci s modulem.

  Toho lze dosáhnout tak, že přidáte `[OutputType([<MyOutputType>])]` kde MyOutputType je platného typu. Další informace o OutputType najdete v tématu [o OutputTypeAttribute funkce](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). Následující kód představuje příklad přidání `OutputType` do rutiny:

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

  Toto chování je podobné funkci "našeptávání" výstupu rutiny v prostředí PowerShell ISE bez nutnosti jeho spuštění.

  ![POSH IntelliSense](../media/modules/automation-posh-ise-intellisense.png)

* Nastavte všechny rutiny v modulu jako bezstavové. Více úloh sady runbook může současně spustit v téže doméně AppDomain stejného procesu a izolovaného prostoru. Pokud dojde k některému ze stavů sdílet i na těchto úrovních, může ovlivnit úlohy mezi sebou. Toto chování může vést k přerušovaným a těžko diagnostikovat problémy.  Následuje příklad špatného postupu.

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

* V modulu musí být plně obsažený v příkazu xcopy u balíčku. Moduly Azure Automation se distribuují do sandboxy Automation při runbooky potřebují spustit. Moduly musí pracovat nezávisle, které běží na hostiteli. By měl být schopen Zip nahoru a přesunout balíček modulu a fungovat jako obvykle po importování do jiného hostitele prostředí PowerShell. V pořadí, které se provedou nesmí být modul závislý na žádném souboru mimo složku modulu. Tato složka je složka, kterou zazipujete při importu modulu do služby Azure Automation. Modul by neměl závisí také na žádném jedinečném nastavení registrů na hostiteli, jako je nastavena tato nastavení při instalaci produktu. Všechny soubory v modulu musí mít méně než 140 znaků cesty. Všechny cesty, než 140 znaků způsobí problémů při importu runbooku. Pokud tento osvědčený postup nedodržíte, modul nebude možné použít ve službě Azure Automation.  

* Pokud odkazující na [moduly Azure Powershellu Az](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) v modulu, ujistěte se také nejsou odkazující na `AzureRM`. `Az` Modulu nelze použít ve spojení s `AzureRM` moduly. `Az` je podporován v sadách runbook, ale nejsou ve výchozím nastavení importován. Další informace o `Az` moduly a důležité informace o vzít v úvahu, najdete v části [Az podpora modulu ve službě Azure Automation](../az-modules.md).

## <a name="next-steps"></a>Další postup

* Další informace o vytváření modulů PowerShellu najdete v článku [Psaní modulu Windows PowerShellu](https://msdn.microsoft.com/library/dd878310%28v=vs.85%29.aspx)