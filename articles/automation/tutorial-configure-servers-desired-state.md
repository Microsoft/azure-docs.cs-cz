---
title: Konfigurace počítačů do požadovaného stavu v Azure Automation
description: V tomto článku se dozvíte, jak nakonfigurovat počítače do požadovaného stavu pomocí konfigurace stavu Azure Automation.
services: automation
ms.subservice: dsc
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: f16db3f55ebd0f09e4d7b75750fa319daf03977e
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2021
ms.locfileid: "99053563"
---
# <a name="configure-machines-to-a-desired-state"></a>Konfigurace počítačů do požadovaného stavu

Konfigurace stavu Azure Automation umožňuje zadat konfigurace pro vaše servery a zajistit, aby tyto servery byly v zadaném stavu v průběhu času.

> [!div class="checklist"]
> - Připojit virtuální počítač, který se má spravovat pomocí Azure Automation DSC
> - Nahrajte konfiguraci do Azure Automation
> - Kompilace konfigurace do konfigurace uzlu
> - Přiřazení konfigurace uzlu spravovanému uzlu
> - Zkontroluje stav dodržování předpisů spravovaného uzlu.

Pro tento kurz používáme jednoduchou [konfiguraci DSC](/powershell/scripting/dsc/configurations/configurations) , která zajišťuje, že se na virtuálním počítači nainstaluje služba IIS.

## <a name="prerequisites"></a>Požadavky

- Účet Azure Automation. Další informace o účtu Automation a jeho požadavcích najdete v tématu [Přehled ověřování účtu Automation](./automation-security-overview.md).
- Azure Resource Manager virtuální počítač (ne Classic) se systémem Windows Server 2008 R2 nebo novějším. Pokyny k vytvoření virtuálního počítače najdete v tématu [Vytvoření prvního virtuálního počítače s Windows v Azure Portal](../virtual-machines/windows/quick-create-portal.md).
- Azure PowerShell modul verze 3,6 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).
- Znalost konfigurace požadovaného stavu (DSC). Informace o DSC najdete v tématu [Přehled konfigurace požadovaného stavu Windows PowerShellu](/powershell/scripting/dsc/overview/overview).

## <a name="support-for-partial-configurations"></a>Podpora pro částečné konfigurace

Konfigurace stavu Azure Automation podporuje použití [částečných konfigurací](/powershell/scripting/dsc/pull-server/partialconfigs). V tomto scénáři je DSC nakonfigurované pro správu více konfigurací nezávisle a každá konfigurace se načte z Azure Automation. K uzlu na účet Automation se ale dá přiřadit jenom jedna konfigurace. To znamená, že pokud pro uzel používáte dvě konfigurace, budete potřebovat dva účty Automation.

Podrobnosti o tom, jak registrovat částečnou konfiguraci ze služby vyžádané replikace, najdete v dokumentaci pro [částečné konfigurace](/powershell/scripting/dsc/pull-server/partialconfigs#partial-configurations-in-pull-mode).

Další informace o tom, jak týmy můžou spolupracovat pro spolupráci se servery, které používají konfiguraci jako kód, najdete v tématu [Principy role DSC v kanálu CI/CD](/powershell/scripting/dsc/overview/authoringadvanced).

## <a name="log-in-to-azure"></a>Přihlaste se k Azure.

Přihlaste se k předplatnému Azure pomocí rutiny [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) a postupujte podle pokynů na obrazovce.

```powershell
Connect-AzAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Vytvoření a nahrání konfigurace pro Azure Automation

V textovém editoru zadejte následující příkaz a uložte ho místně jako **TestConfig.ps1**.

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
> V pokročilejších scénářích, kdy potřebujete importovat více modulů, které poskytují prostředky DSC, se ujistěte, že má každý modul jedinečný `Import-DscResource` řádek v konfiguraci.

Voláním rutiny [Import-AzAutomationDscConfiguration](/powershell/module/Az.Automation/Import-AzAutomationDscConfiguration) nahrajte konfiguraci do svého účtu Automation.

```powershell
 Import-AzAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Kompilace konfigurace do konfigurace uzlu

Konfigurace DSC musí být zkompilována do konfigurace uzlu před tím, než může být přiřazena k uzlu. Viz [Konfigurace DSC](/powershell/scripting/dsc/configurations/configurations).

Zavolejte rutinu [Start-AzAutomationDscCompilationJob](/powershell/module/Az.Automation/Start-AzAutomationDscCompilationJob) pro zkompilování `TestConfig` Konfigurace do konfigurace uzlu s názvem `TestConfig.WebServer` v účtu Automation.

```powershell
Start-AzAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>Registrace virtuálního počítače, který se má spravovat pomocí konfigurace stavu

Konfiguraci stavu Azure Automation můžete použít ke správě virtuálních počítačů Azure (klasických i Správce prostředků), místních virtuálních počítačů, počítačů se systémem Linux, virtuálních počítačů s AWS a místních fyzických počítačů. V tomto tématu se zabýváme registrací jenom Azure Resource Manager virtuálních počítačů. Informace o registraci jiných typů počítačů najdete v tématu věnovaném [připojování počítačů ke správě podle konfigurace stavu Azure Automation](automation-dsc-onboarding.md).

Voláním rutiny [Register-AzAutomationDscNode](/powershell/module/Az.Automation/Register-AzAutomationDscNode) ZAREGISTRUJETE virtuální počítač s konfigurací stavu Azure Automation jako spravovaný uzel. 

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

### <a name="specify-configuration-mode-settings"></a>Zadat nastavení režimu konfigurace

Použijte rutinu [Register-AzAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode) k registraci virtuálního počítače jako spravovaného uzlu a určení vlastností konfigurace. Například můžete určit, že stav počítače má být použit pouze jednou zadáním `ApplyOnly` hodnoty `ConfigurationMode` Vlastnosti. Konfigurace stavu se nepokusí použít konfiguraci po počáteční kontrole.

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

Můžete také určit, jak často DSC kontroluje stav konfigurace pomocí `ConfigurationModeFrequencyMins` Vlastnosti. Další informace o nastavení konfigurace DSC najdete v tématu [Konfigurace místní Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig).

```powershell
# Run a DSC check every 60 minutes
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Přiřazení konfigurace uzlu spravovanému uzlu

Nyní můžeme konfiguraci zkompilovaného uzlu přiřadit k virtuálnímu počítači, který chceme nakonfigurovat.

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -NodeId $node.Id
```

Tím se přiřadí konfigurace uzlu s názvem `TestConfig.WebServer` k zaregistrovanému uzlu DSC `DscVm` . Ve výchozím nastavení má uzel DSC kontrolu kompatibility s konfigurací uzlu každých 30 minut. Informace o tom, jak změnit interval kontroly dodržování předpisů, najdete v tématu [Konfigurace místní Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig).

## <a name="check-the-compliance-status-of-a-managed-node"></a>Zkontroluje stav dodržování předpisů spravovaného uzlu.

Pomocí rutiny [Get-AzAutomationDscNodeReport](/powershell/module/Az.Automation/Get-AzAutomationDscNodeReport) můžete získat sestavy o stavu dodržování předpisů spravovaného uzlu.

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="remove-nodes-from-service"></a>Odebrat uzly ze služby

Když přidáte uzel do konfigurace stavu Azure Automation, nastavení v místní Configuration Manager jsou nastavena k registraci u služby a konfigurace přijetí a požadované moduly pro konfiguraci počítače.
Pokud se rozhodnete odebrat uzel ze služby, můžete tak učinit buď pomocí Azure Portal, nebo pomocí rutin AZ.

> [!NOTE]
> Zrušení registrace uzlu ze služby nastaví pouze nastavení místního Configuration Manager, aby se uzel již nepřipojoval k této službě.
> To neplatí pro konfiguraci, která je aktuálně použita pro uzel.
> Pokud chcete odebrat aktuální konfiguraci, použijte [PowerShell](/powershell/module/psdesiredstateconfiguration/remove-dscconfigurationdocument) nebo odstraňte místní konfigurační soubor (Toto je jediná možnost pro uzly Linux).

### <a name="azure-portal"></a>Portál Azure Portal

V Azure Automation klikněte v obsahu na **Konfigurace stavu (DSC)** .
Dalším kliknutím na **uzly** zobrazíte seznam uzlů, které jsou zaregistrované ve službě.
Klikněte na název uzlu, který chcete odebrat.
V zobrazení uzlu, které se otevře, klikněte na **zrušit registraci**.

### <a name="powershell"></a>PowerShell

Pokud chcete zrušit registraci uzlu ze služby konfigurace stavu Azure Automation pomocí prostředí PowerShell, postupujte podle dokumentace k rutině [Unregister-AzAutomationDscNode](/powershell/module/az.automation/unregister-azautomationdscnode).

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak začít, najdete v tématu Začínáme [s konfigurací stavu Azure Automation](automation-dsc-getting-started.md).
- Další informace o povolení uzlů najdete v tématu [povolení konfigurace stavu Azure Automation](automation-dsc-onboarding.md).
- Další informace o kompilaci konfigurací DSC, abyste je mohli přiřadit cílovým uzlům, najdete v tématu [kompilace konfigurací DSC v konfiguraci stavu Azure Automation](automation-dsc-compile.md).
- Příklad použití konfigurace stavu Azure Automation v kanálu nepřetržitého nasazení najdete v tématu [Nastavení průběžného nasazování s čokoládou](automation-dsc-cd-chocolatey.md).
- Informace o cenách najdete v tématu [Azure Automation ceny konfigurace stavu](https://azure.microsoft.com/pricing/details/automation/).
- Referenční informace k rutinám PowerShellu najdete v tématu [AZ. Automation](/powershell/module/az.automation).
