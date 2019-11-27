---
title: Konfigurace serverů do požadovaného stavu a správa odchylek s využitím Azure Automation
description: Kurz – Správa konfigurací serveru s konfigurací stavu Azure Automation
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
manager: carmonm
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: 72e5018dc1212e57dc190c05cc54158d37ca7fe1
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231500"
---
# <a name="configure-servers-to-a-desired-state-and-manage-drift"></a>Konfigurace serverů do požadovaného stavu a Správa posunu

Konfigurace stavu Azure Automation umožňuje zadat konfigurace pro vaše servery a zajistit, aby tyto servery byly v zadaném stavu v průběhu času.

> [!div class="checklist"]
> - Připojit virtuální počítač, který se má spravovat pomocí Azure Automation DSC
> - Nahrajte konfiguraci do Azure Automation
> - Kompilace konfigurace do konfigurace uzlu
> - Přiřazení konfigurace uzlu spravovanému uzlu
> - Zkontroluje stav dodržování předpisů spravovaného uzlu.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

- Účet Azure Automation. Pokyny k vytvoření účtu Azure Automation Spustit jako najdete v tématu [Účet Spustit jako pro Azure](automation-sec-configure-azure-runas-account.md).
- Azure Resource Manager virtuální počítač (ne Classic) se systémem Windows Server 2008 R2 nebo novějším. Pokyny k vytvoření virtuálního počítače najdete v tématu [Vytvoření vašeho prvního virtuálního počítače s Windows na webu Azure Portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md).
- Azure PowerShell modul verze 3,6 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).
- Znalost konfigurace požadovaného stavu (DSC). Informace o DSC najdete v tématu [Přehled konfigurace požadovaného stavu prostředí Windows PowerShell](/powershell/scripting/dsc/overview/overview) .

## <a name="log-in-to-azure"></a>Přihlaste se k Azure.

Přihlaste se k předplatnému Azure pomocí příkazu `Connect-AzureRmAccount` a postupujte podle pokynů na obrazovce.

```powershell
Connect-AzureRmAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Vytvoření a nahrání konfigurace pro Azure Automation

V tomto kurzu použijeme jednoduchou konfiguraci DSC, která zajistí, že se na virtuálním počítači nainstaluje služba IIS.

Informace o konfiguracích DSC najdete v tématu [Konfigurace DSC](/powershell/scripting/dsc/configurations/configurations).

V textovém editoru zadejte následující a soubor místně uložte jako `TestConfig.ps1`.

```powershell
configuration TestConfig {
   Node WebServer {
      WindowsFeature IIS {
         Ensure               = 'Present'
         Name                 = 'Web-Server'
         IncludeAllSubFeature = $true
      }
   }
}
```

> [!NOTE]
> V pokročilejších scénářích, kdy potřebujete importovat více modulů, které poskytují prostředky DSC, se ujistěte, že každý modul obsahuje jedinečný `Import-DscResource` řádek v konfiguraci.

Voláním rutiny `Import-AzureRmAutomationDscConfiguration` nahrajte konfiguraci do svého účtu Automation:

```powershell
 Import-AzureRmAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Kompilace konfigurace do konfigurace uzlu

Konfigurace DSC musí být zkompilována do konfigurace uzlu před tím, než může být přiřazena k uzlu.

Informace o kompilaci konfigurací najdete v tématu [Konfigurace DSC](/powershell/scripting/dsc/configurations/configurations).

Voláním rutiny `Start-AzureRmAutomationDscCompilationJob` zkompilujte konfiguraci `TestConfig` do konfigurace uzlu:

```powershell
Start-AzureRmAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

Tím se ve vašem účtu Automation vytvoří konfigurace uzlu s názvem `TestConfig.WebServer`.

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>Registrace virtuálního počítače, který se má spravovat pomocí konfigurace stavu

Konfiguraci stavu Azure Automation můžete použít ke správě virtuálních počítačů Azure (klasických i Správce prostředků), místních virtuálních počítačů, počítačů se systémem Linux, virtuálních počítačů s AWS a místních fyzických počítačů. V tomto tématu se zabýváme registrací jenom Azure Resource Manager virtuálních počítačů. Informace o registraci jiných typů počítačů najdete v tématu věnovaném [připojování počítačů ke správě podle konfigurace stavu Azure Automation](automation-dsc-onboarding.md).

Voláním rutiny `Register-AzureRmAutomationDscNode` zaregistrujete virtuální počítač s konfigurací stavu Azure Automation.

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

Zaregistruje zadaný virtuální počítač jako spravovaný uzel v konfiguraci stavu.

### <a name="specify-configuration-mode-settings"></a>Zadat nastavení režimu konfigurace

Když zaregistrujete virtuální počítač jako spravovaný uzel, můžete také zadat vlastnosti konfigurace. Můžete například určit, že se má stav počítače použít jenom jednou (DSC se nepokusí použít konfiguraci po počáteční kontrole) zadáním `ApplyOnly` jako hodnoty vlastnosti **ConfigurationMode** :

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

Můžete také určit, jak často DSC kontroluje stav konfigurace pomocí vlastnosti **ConfigurationModeFrequencyMins** :

```powershell
# Run a DSC check every 60 minutes
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

Další informace o nastavení vlastností konfigurace pro spravovaný uzel naleznete v tématu [Register-AzureRmAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode).

Další informace o nastavení konfigurace DSC najdete v tématu [Konfigurace místní Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig).

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Přiřazení konfigurace uzlu spravovanému uzlu

Nyní můžeme konfiguraci zkompilovaného uzlu přiřadit k virtuálnímu počítači, který chceme nakonfigurovat.

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -NodeId $node.Id
```

Tím se konfigurace uzlu s názvem `TestConfig.WebServer` přiřadí zaregistrovanému uzlu DSC s názvem `DscVm`.
Ve výchozím nastavení má uzel DSC kontrolu kompatibility s konfigurací uzlu každých 30 minut.
Informace o tom, jak změnit interval kontroly dodržování předpisů, najdete v tématu [Konfigurace místní Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig).

## <a name="working-with-partial-configurations"></a>Práce s částečnými konfiguracemi

Konfigurace stavu Azure Automation podporuje použití [částečných konfigurací](/powershell/scripting/dsc/pull-server/partialconfigs).
V tomto scénáři je DSC nakonfigurované pro správu více konfigurací nezávisle a každá konfigurace se načte z Azure Automation.
K uzlu na účet Automation se ale dá přiřadit jenom jedna konfigurace.
To znamená, že pokud pro uzel používáte dvě konfigurace, budete potřebovat dva účty Automation.

Podrobnosti o tom, jak registrovat částečnou konfiguraci ze služby vyžádané replikace, najdete v dokumentaci pro [částečné konfigurace](https://docs.microsoft.com/powershell/scripting/dsc/pull-server/partialconfigs#partial-configurations-in-pull-mode).

Další informace o tom, jak můžou týmy spolupracovat na spolupráci se servery, které používají konfiguraci jako kód, najdete [v tématu Principy role DSC v kanálu CI/CD](/powershell/scripting/dsc/overview/authoringadvanced).

## <a name="check-the-compliance-status-of-a-managed-node"></a>Zkontroluje stav dodržování předpisů spravovaného uzlu.

Můžete získat sestavy o stavu dodržování předpisů spravovaného uzlu voláním rutiny `Get-AzureRmAutomationDscNodeReport`:

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzureRmAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="removing-nodes-from-service"></a>Odebírají se uzly ze služby.

Když přidáte uzel do konfigurace stavu Azure Automation, nastavení v místní Configuration Manager jsou nastavena k registraci u služby a konfigurace přijetí a požadované moduly pro konfiguraci počítače.
Pokud se rozhodnete odebrat uzel ze služby, můžete tak učinit buď pomocí Azure Portal, nebo pomocí rutin AZ.

> [!NOTE]
> Zrušení registrace uzlu ze služby nastaví pouze nastavení místního Configuration Manager, aby se uzel již nepřipojoval k této službě.
> To neplatí pro konfiguraci, která je aktuálně použita pro uzel.
> Pokud chcete odebrat aktuální konfiguraci, použijte [PowerShell](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/remove-dscconfigurationdocument?view=powershell-5.1) nebo odstraňte místní konfigurační soubor (Toto je jediná možnost pro uzly Linux).

### <a name="azure-portal"></a>portál Azure

V Azure Automation klikněte v obsahu na **Konfigurace stavu (DSC)** .
Dalším kliknutím na **uzly** zobrazíte seznam uzlů, které jsou zaregistrované ve službě.
Klikněte na název uzlu, který chcete odebrat.
V zobrazení uzlu, které se otevře, klikněte na **zrušit registraci**.

### <a name="powershell"></a>PowerShell

Pokud chcete zrušit registraci uzlu ze služby konfigurace stavu Azure Automation pomocí prostředí PowerShell, postupujte podle dokumentace k rutině [Unregister-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-2.0.0).

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak začít, najdete v tématu [Začínáme s konfigurací stavu Azure Automation](automation-dsc-getting-started.md) .
- Další informace o připojování uzlů najdete v tématu věnovaném [připojování počítačů ke správě podle konfigurace stavu Azure Automation](automation-dsc-onboarding.md) .
- Další informace o kompilaci konfigurací DSC, abyste je mohli přiřadit cílovým uzlům, najdete v tématu [kompilace konfigurací v konfiguraci stavu Azure Automation](automation-dsc-compile.md)
- Referenční informace k rutinám PowerShellu najdete v tématu [rutiny konfigurace stavu Azure Automation](/powershell/module/azurerm.automation/#automation) .
- Informace o cenách najdete v tématu [Azure Automation ceny konfigurace stavu](https://azure.microsoft.com/pricing/details/automation/) .
- Příklad použití konfigurace stavu Azure Automation v kanálu průběžného nasazování najdete v tématu [průběžné nasazování pomocí Azure Automation konfigurace stavu a čokolády](automation-dsc-cd-chocolatey.md) .
