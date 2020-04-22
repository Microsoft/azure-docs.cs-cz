---
title: Konfigurace serverů do požadovaného stavu a správa odchylek s využitím Azure Automation
description: Kurz – správa konfigurací serverů pomocí konfigurace stavu automatizace Azure
services: automation
ms.subservice: dsc
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: a02c664ddf0802ad5ac306f98de14b7c0d5d7271
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678707"
---
# <a name="configure-servers-to-a-desired-state-and-manage-drift"></a>Konfigurace serverů do požadovaného stavu a správa driftu

Konfigurace stavu automatizace Azure umožňuje zadat konfigurace pro vaše servery a zajistit, aby tyto servery jsou v zadaném stavu v průběhu času.

> [!div class="checklist"]
> - Na palubě virtuálního počítače, který má spravovat Azure Automation DSC
> - Nahrání konfigurace do Azure Automation
> - Kompilace konfigurace do konfigurace uzlu
> - Přiřazení konfigurace uzlu spravovanému uzlu
> - Kontrola stavu dodržování předpisů spravovaného uzlu

Pro účely tohoto kurzu používáme jednoduchou [konfiguraci DSC,](/powershell/scripting/dsc/configurations/configurations) která zajišťuje, že služba IIS je nainstalovaná na virtuálním počítači.

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci modulu AZ na pracovníka hybridní sady Runbook najdete [v tématu Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). U vašeho účtu Automation můžete aktualizovat moduly na nejnovější verzi pomocí [funkce Jak aktualizovat moduly Azure PowerShellu v Azure Automation](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

- Účet Azure Automation. Pokyny k vytvoření účtu Azure Automation Spustit jako najdete v tématu [Účet Spustit jako pro Azure](automation-sec-configure-azure-runas-account.md).
- Virtuální počítač Azure Resource Manager (ne klasický) se systémem Windows Server 2008 R2 nebo novějším. Pokyny k vytvoření virtuálního počítače najdete v tématu [Vytvoření prvního virtuálního počítače s Windows na webu Azure Portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md).
- Modul Azure PowerShell verze 3.6 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).
- Znalost konfigurace požadovaného stavu (DSC). Informace o nástroji DSC naleznete v tématu [Windows PowerShell Přehled konfigurace požadovaného stavu](/powershell/scripting/dsc/overview/overview).

## <a name="support-for-partial-configurations"></a>Podpora částečných konfigurací

Konfigurace stavu automatizace Azure podporuje použití [částečných konfigurací](/powershell/scripting/dsc/pull-server/partialconfigs). V tomto scénáři DSC je nakonfigurován pro správu více konfigurací nezávisle a každá konfigurace se načte z Azure Automation. K uzlu na účet automatizace však lze přiřadit pouze jednu konfiguraci. To znamená, že pokud používáte dvě konfigurace pro uzel, budete potřebovat dva účty automatizace.

Podrobnosti o tom, jak zaregistrovat částečnou konfiguraci ze [služby vyžádat,](https://docs.microsoft.com/powershell/scripting/dsc/pull-server/partialconfigs#partial-configurations-in-pull-mode)naleznete v dokumentaci k částečné konfigurace .

Další informace o tom, jak mohou týmy spolupracovat na spolupráci při správě serverů pomocí konfigurace jako kódu, naleznete [v tématu Principy role DSC v kanálu CI/CD](/powershell/scripting/dsc/overview/authoringadvanced).

## <a name="log-in-to-azure"></a>Přihlaste se k Azure.

Přihlaste se k předplatnému Azure pomocí rutiny [Connect-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0) a postupujte podle pokynů na obrazovce.

```powershell
Connect-AzAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Vytvoření a nahrání konfigurace do Azure Automation


Do textového editoru zadejte následující text a uložte jej místně jako **TestConfig.ps1**.

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
> V pokročilejších scénářích, kde vyžadujete import více modulů, které poskytují prostředky DSC, ujistěte se, že každý modul má v konfiguraci jedinečný `Import-DscResource` řádek.

Chcete-li odeslat konfiguraci do účtu Automation, zavolejte rutinu [Import-AzAutomationDscConfiguration.](https://docs.microsoft.com/powershell/module/Az.Automation/Import-AzAutomationDscConfiguration?view=azps-3.7.0)

```powershell
 Import-AzAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Kompilace konfigurace do konfigurace uzlu

Konfigurace DSC musí být zkompilován do konfigurace uzlu před jeho přiřazením k uzlu. Viz [Konfigurace DSC](/powershell/scripting/dsc/configurations/configurations).

Volání [Rutina Start-AzAutomationDscCompilationJob](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationDscCompilationJob?view=azps-3.7.0) zkompilujte `TestConfig` konfiguraci do `TestConfig.WebServer` konfigurace uzlu pojmenované v účtu Automation.

```powershell
Start-AzAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>Registrace virtuálního počítače, který má být spravován pomocí konfigurace státu

Konfigurace stavu Azure Automation můžete použít ke správě virtuálních počítačů Azure (Classic i Resource Manager), místních virtuálních počítačů, počítačů s Linuxem, virtuálních počítačů AWS a místních fyzických počítačů. V tomto tématu se zabýváme registrací jenom virtuálních počítačích Azure Resource Manager. Informace o registraci jiných typů počítačů najdete [v tématu Onboarding machines for management by Azure Automation State Configuration](automation-dsc-onboarding.md).

Volání [Register-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationDscNode?view=azps-3.7.0) rutina zaregistrovat váš virtuální počítač s Azure Automation state Configuration jako spravovaný uzel. 

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

### <a name="specify-configuration-mode-settings"></a>Určení nastavení konfiguračního režimu

Rutina [Register-AzAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode) slouží k registraci virtuálního počítače jako spravovaného uzlu a určení vlastností konfigurace. Můžete například určit, že stav stroje má být použit pouze `ApplyOnly` jednou zadáním `ConfigurationMode` hodnoty vlastnosti. Konfigurace stavu se nepokouší použít konfiguraci po počáteční kontrole.

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

Můžete také určit, jak často DSC kontroluje `ConfigurationModeFrequencyMins` stav konfigurace pomocí vlastnosti. Další informace o nastavení konfigurace dsc naleznete [v tématu Konfigurace správce místní konfigurace](/powershell/scripting/dsc/managing-nodes/metaConfig).

```powershell
# Run a DSC check every 60 minutes
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Přiřazení konfigurace uzlu spravovanému uzlu

Teď můžeme přiřadit konfiguraci kompilovaného uzlu virtuálnímu virtuálnímu počítače, který chceme nakonfigurovat.

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -NodeId $node.Id
```

Tím přiřadíte konfiguraci `TestConfig.WebServer` uzlu s názvem registrovanému uzlu `DscVm`DSC . Ve výchozím nastavení je uzel DSC kontrolován z důvodu dodržování konfigurace uzlu každých 30 minut. Informace o změně intervalu kontroly dodržování předpisů naleznete [v tématu Konfigurace nástroje Local Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig).

## <a name="check-the-compliance-status-of-a-managed-node"></a>Kontrola stavu dodržování předpisů spravovaného uzlu

Můžete získat sestavy o stavu dodržování předpisů spravovaného uzlu pomocí [rutiny Get-AzAutomationDscNodeReport.](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationDscNodeReport?view=azps-3.7.0)

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="remove-nodes-from-service"></a>Odebrání uzlů ze služby

Když přidáte uzel do konfigurace stavu automatizace Azure, nastavení v místním konfiguračním nástroji jsou nastavena na registraci s konfigurací služby a vyžádat konfigurace a požadované moduly pro konfiguraci počítače.
Pokud se rozhodnete odebrat uzel ze služby, můžete tak učinit pomocí portálu Azure nebo rutiny AZ.

> [!NOTE]
> Zrušení registrace uzlu ze služby pouze nastaví nastavení správce místní konfigurace, takže uzel se již nepřipojuje ke službě.
> To nemá vliv na konfiguraci, která je aktuálně použita na uzel.
> Chcete-li odebrat aktuální konfiguraci, použijte [prostředí PowerShell](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/remove-dscconfigurationdocument?view=powershell-5.1) nebo odstraňte místní konfigurační soubor (toto je jediná možnost pro linuxové uzly).

### <a name="azure-portal"></a>portál Azure

Z Azure Automation klikněte na **konfiguraci stavu (DSC)** v obsahu.
Dalším klepnutím na položku **Uzly** zobrazíte seznam uzlů, které jsou registrovány ve službě.
Klikněte na název uzlu, který chcete odstranit.
V zobrazení Uzel, které se otevře, klepněte na tlačítko **Zrušit registraci**.

### <a name="powershell"></a>PowerShell

Chcete-li zrušit registraci uzlu ze služby Azure Automation State Configuration pomocí prostředí PowerShell, postupujte podle dokumentace k rutině [Unregister-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-2.0.0).

## <a name="next-steps"></a>Další kroky

- Další informace najdete [v tématu Začínáme s azure automation state configuration](automation-dsc-getting-started.md).
- Další informace o tom, jak napalubě uzly, najdete [v tématu onboarding počítače pro správu pomocí konfigurace stavu automatizace Azure](automation-dsc-onboarding.md).
- Další informace o kompilaci konfigurací DSC, abyste je mohli přiřadit k cílovým uzlům, najdete [v tématu Kompilace konfigurací v konfiguraci stavu automatizace Azure](automation-dsc-compile.md).
- Odkaz na rutinu prostředí PowerShell najdete [v tématu Rutiny konfigurace stavu azure automatizace](/powershell/module/azurerm.automation/#automation).
- Informace o cenách najdete v [tématu Ceny konfigurace stavu Azure Automation](https://azure.microsoft.com/pricing/details/automation/).
- Příklad použití konfigurace stavu azure automatizace v kanálu průběžného nasazení najdete v [tématu Průběžné nasazení pomocí konfigurace stavu automatizace Azure a Chocolatey](automation-dsc-cd-chocolatey.md)
