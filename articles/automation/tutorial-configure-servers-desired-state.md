---
title: Konfigurace serverů do požadovaného stavu a správa odchylek s využitím Azure Automation
description: Kurz – správa konfigurací serverů pomocí konfigurace stavu automatizace Azure
services: automation
ms.subservice: dsc
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: 9e2f04f59a56be6c516eb90de45fdf7327673086
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75416593"
---
# <a name="configure-servers-to-a-desired-state-and-manage-drift"></a>Konfigurace serverů do požadovaného stavu a správa driftu

Konfigurace stavu automatizace Azure umožňuje zadat konfigurace pro vaše servery a zajistit, aby tyto servery jsou v zadaném stavu v průběhu času.

> [!div class="checklist"]
> - Na palubě virtuálního počítače, který má spravovat Azure Automation DSC
> - Nahrání konfigurace do Azure Automation
> - Kompilace konfigurace do konfigurace uzlu
> - Přiřazení konfigurace uzlu spravovanému uzlu
> - Kontrola stavu dodržování předpisů spravovaného uzlu

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

- Účet Azure Automation. Pokyny k vytvoření účtu Azure Automation Spustit jako najdete v tématu [Účet Spustit jako pro Azure](automation-sec-configure-azure-runas-account.md).
- Virtuální počítač Azure Resource Manager (ne klasický) se systémem Windows Server 2008 R2 nebo novějším. Pokyny k vytvoření virtuálního počítače najdete v tématu [Vytvoření vašeho prvního virtuálního počítače s Windows na webu Azure Portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md).
- Modul Azure PowerShell verze 3.6 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).
- Znalost konfigurace požadovaného stavu (DSC). Informace o dsc, naleznete [v tématu Windows PowerShell požadovaného stavu konfigurace Přehled](/powershell/scripting/dsc/overview/overview)

## <a name="log-in-to-azure"></a>Přihlaste se k Azure.

Přihlaste se k předplatnému Azure pomocí příkazu `Connect-AzureRmAccount` a postupujte podle pokynů na obrazovce.

```powershell
Connect-AzureRmAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Vytvoření a nahrání konfigurace do Azure Automation

Pro účely tohoto kurzu použijeme jednoduchou konfiguraci DSC, která zajistí, že služba IIS je nainstalovaná na virtuálním počítači.

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
> V pokročilejších scénářích, kde vyžadujete import více modulů, které poskytují prostředky DSC, ujistěte se, že každý modul má v konfiguraci jedinečný `Import-DscResource` řádek.

Chcete-li `Import-AzureRmAutomationDscConfiguration` odeslat konfiguraci do účtu Automation, zavolejte na rutinu:

```powershell
 Import-AzureRmAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Kompilace konfigurace do konfigurace uzlu

Konfigurace DSC musí být zkompilován do konfigurace uzlu před jeho přiřazením k uzlu.

Informace o kompilaci konfigurací naleznete v [tématu Konfigurace DSC](/powershell/scripting/dsc/configurations/configurations).

Volání `Start-AzureRmAutomationDscCompilationJob` rutiny zkompilovat `TestConfig` konfiguraci do konfigurace uzlu:

```powershell
Start-AzureRmAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

Tím se vytvoří konfigurace `TestConfig.WebServer` uzlu s názvem v účtu automatizace.

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>Registrace virtuálního počítače, který má být spravován pomocí konfigurace státu

Konfigurace stavu Azure Automation můžete použít ke správě virtuálních počítačů Azure (Classic i Resource Manager), místních virtuálních počítačů, počítačů s Linuxem, virtuálních počítačů AWS a místních fyzických počítačů. V tomto tématu se zabýváme registrací jenom virtuálních počítačích Azure Resource Manager. Informace o registraci jiných typů počítačů najdete [v tématu Onboarding machines for management by Azure Automation State Configuration](automation-dsc-onboarding.md).

Volání `Register-AzureRmAutomationDscNode` rutiny zaregistrovat virtuální počítač s Azure Automation state Configuration.

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

Tím se zaregistruje zadaný virtuální počítače jako spravovaný uzel v konfiguraci stavu.

### <a name="specify-configuration-mode-settings"></a>Určení nastavení konfiguračního režimu

Když zaregistrujete virtuální ho jako spravovaný uzel, můžete také zadat vlastnosti konfigurace. Můžete například určit, že stav počítače má být použit pouze jednou (DSC se nepokusí použít konfiguraci po počáteční kontrole) zadáním `ApplyOnly` jako hodnota **vlastnostconfigurationMode:**

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

Můžete také určit, jak často DSC kontroluje stav konfigurace pomocí **vlastnosti ConfigurationModeFrequencyMins:**

```powershell
# Run a DSC check every 60 minutes
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

Další informace o nastavení vlastností konfigurace spravovaného uzlu naleznete v [tématu Register-AzureRmAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode).

Další informace o nastavení konfigurace dsc naleznete [v tématu Konfigurace správce místní konfigurace](/powershell/scripting/dsc/managing-nodes/metaConfig).

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Přiřazení konfigurace uzlu spravovanému uzlu

Teď můžeme přiřadit konfiguraci kompilovaného uzlu virtuálnímu virtuálnímu počítače, který chceme nakonfigurovat.

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -NodeId $node.Id
```

Tím přiřadíte konfiguraci `TestConfig.WebServer` uzlu s názvem `DscVm`registrovaného uzlu DSC .
Ve výchozím nastavení je uzel DSC kontrolován z důvodu dodržování konfigurace uzlu každých 30 minut.
Informace o změně intervalu kontroly dodržování předpisů naleznete [v tématu Konfigurace nástroje Local Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig).

## <a name="working-with-partial-configurations"></a>Práce s částečnými konfiguracemi

Konfigurace stavu automatizace Azure podporuje použití [částečných konfigurací](/powershell/scripting/dsc/pull-server/partialconfigs).
V tomto scénáři DSC je nakonfigurován pro správu více konfigurací nezávisle a každá konfigurace se načte z Azure Automation.
K uzlu na účet automatizace však lze přiřadit pouze jednu konfiguraci.
To znamená, že pokud používáte dvě konfigurace pro uzel, budete potřebovat dva účty automatizace.

Podrobnosti o tom, jak zaregistrovat částečnou konfiguraci ze služby vyžádat, naleznete v dokumentaci k [částečným konfiguracím](https://docs.microsoft.com/powershell/scripting/dsc/pull-server/partialconfigs#partial-configurations-in-pull-mode).

Další informace o tom, jak mohou týmy spolupracovat na spolupráci při správě serverů pomocí konfigurace jako kódu, naleznete [v tématu Principy role DSC v kanálu CI/CD](/powershell/scripting/dsc/overview/authoringadvanced).

## <a name="check-the-compliance-status-of-a-managed-node"></a>Kontrola stavu dodržování předpisů spravovaného uzlu

Sestavy o stavu dodržování předpisů spravovaného uzlu můžete `Get-AzureRmAutomationDscNodeReport` získat voláním rutiny:

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzureRmAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="removing-nodes-from-service"></a>Odebrání uzlů ze služby

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

- Další informace najdete v tématu [Začínáme s konfigurací stavu automatizace Azure](automation-dsc-getting-started.md)
- Informace o tom, jak napalubě uzlů, najdete [v tématu Onboarding ové počítače pro správu pomocí konfigurace stavu automatizace Azure](automation-dsc-onboarding.md)
- Další informace o kompilaci konfigurací DSC, abyste je mohli přiřadit k cílovým uzlům, najdete [v tématu Kompilace konfigurací v konfiguraci stavu automatizace Azure](automation-dsc-compile.md)
- Informace o odkazu na rutinu prostředí PowerShell najdete v tématu [Rutiny konfigurace stavu automatizace Azure](/powershell/module/azurerm.automation/#automation)
- Informace o cenách najdete v [tématu Ceny konfigurace stavu Azure Automation](https://azure.microsoft.com/pricing/details/automation/)
- Příklad použití konfigurace stavu azure automatizace v kanálu průběžného nasazení najdete v [tématu Průběžné nasazení pomocí konfigurace stavu automatizace Azure a Chocolatey](automation-dsc-cd-chocolatey.md)
