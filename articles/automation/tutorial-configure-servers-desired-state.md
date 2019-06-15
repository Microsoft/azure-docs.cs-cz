---
title: Konfigurace serverů do požadovaného stavu a správa odchylek s využitím Azure Automation
description: Kurz – Správa konfigurace serveru pomocí Azure Automation State Configuration
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
manager: carmonm
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: 83a65be50a3cec9cea47682ab5e207bd4ad9e984
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67072561"
---
# <a name="configure-servers-to-a-desired-state-and-manage-drift"></a>Konfigurace serverů do požadovaného stavu a správa odchylek

Konfigurace stavu Azure Automation umožňuje zadat konfiguraci pro servery a ujistěte se, že tyto servery jsou v zadaném stavu v čase.

> [!div class="checklist"]
> - Připojení virtuálního počítače jej lze spravovat pomocí Azure Automation DSC
> - Odeslat konfiguraci služby Azure Automation
> - Kompilace konfigurace do konfigurace uzlu
> - Přiřazení konfigurace uzlu spravovaných uzlů
> - Kontrola stavu dodržování předpisů spravovaných uzlů

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

- Účet Azure Automation. Pokyny k vytvoření účtu Azure Automation Spustit jako najdete v tématu [Účet Spustit jako pro Azure](automation-sec-configure-azure-runas-account.md).
- Virtuální počítač Azure Resource Manageru (ne Classic) s Windows serverem 2008 R2 nebo novější. Pokyny k vytvoření virtuálního počítače najdete v tématu [Vytvoření vašeho prvního virtuálního počítače s Windows na webu Azure Portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md).
- Azure modul PowerShell verze 3.6 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).
- Znalost Desired State Configuration (DSC). Informace o DSC najdete v tématu [Přehled služby Windows Powershellu Desired State Configuration](https://docs.microsoft.com/powershell/dsc/overview)

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k předplatnému Azure pomocí příkazu `Connect-AzureRmAccount` a postupujte podle pokynů na obrazovce.

```powershell
Connect-AzureRmAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Vytvoření a nahrání konfigurace Azure Automation.

V tomto kurzu použijeme jednoduchou konfiguraci DSC, která zajišťuje, že je služba IIS nainstalovaná na virtuálním počítači.

Informace o konfiguracích DSC najdete v tématu [Konfigurace DSC](/powershell/dsc/configurations).

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

Volání `Import-AzureRmAutomationDscConfiguration` rutinu k odeslání konfigurace do účtu služby Automation:

```powershell
 Import-AzureRmAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Kompilace konfigurace do konfigurace uzlu

Konfigurace DSC musí být zkompilovány do konfigurace uzlu předtím, než může být přiřazena k uzlu.

Informace o kompilaci konfigurace najdete v tématu [konfigurací DSC](/powershell/dsc/configurations).

Volání `Start-AzureRmAutomationDscCompilationJob` rutiny pro kompilaci `TestConfig` konfigurace do konfigurace uzlu:

```powershell
Start-AzureRmAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

Tím se vytvoří konfigurace uzlu s názvem `TestConfig.WebServer` ve vašem účtu Automation.

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>Registraci virtuálního počítače jej lze spravovat pomocí konfigurace stavu

Konfigurace stavu služby Azure Automation můžete použít ke správě virtuálních počítačů Azure (Classic i Resource Manager), místních virtuálních počítačů, počítačů Linux, virtuální počítače AWS a místních fyzických počítačů. V tomto tématu se budeme zabývat jak zaregistrovat pouze virtuální počítače Azure Resource Manageru. Informace o registraci jiných druhů počítačů najdete v tématu [připojování počítačů pro správu Azure Automation stavu konfigurace](automation-dsc-onboarding.md).

Volání `Register-AzureRmAutomationDscNode` rutiny k registraci vašeho virtuálního počítače pomocí Azure Automation State Configuration.

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

To zaregistruje zadaný virtuální počítač jako spravovaných uzlů do konfigurace stavu.

### <a name="specify-configuration-mode-settings"></a>Nastavení režimu konfigurace

Při registraci virtuálního počítače jako spravovaných uzlů můžete také zadat vlastnosti konfigurace. Například můžete určit, že stav počítače se použijí jenom jednou (DSC se nebude pokoušet použít konfiguraci po počáteční kontroly) tak, že zadáte `ApplyOnly` jako hodnotu **ConfigurationMode** vlastnost :

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

Můžete také určit, jak často zkontroluje stav konfigurace pomocí DSC **ConfigurationModeFrequencyMins** vlastnost:

```powershell
# Run a DSC check every 60 minutes
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

Další informace o nastavení vlastností konfigurace spravovaných uzlů najdete v tématu [Register-AzureRmAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode).

Další informace o nastavení konfigurace DSC najdete v tématu [konfigurace Local Configuration Manageru](/powershell/dsc/metaconfig).

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Přiřazení konfigurace uzlu spravovaných uzlů

Nyní jsme můžete přiřadit konfiguraci uzlu kompilované chceme, aby konfigurace virtuálního počítače.

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -NodeId $node.Id
```

Toto přiřadí konfigurace uzlu s názvem `TestConfig.WebServer` k registrovaným uzlem DSC, s názvem `DscVm`.
Ve výchozím nastavení je uzel DSC kontroluje dodržování předpisů s konfigurací uzlu každých 30 minut.
Informace o tom, jak změnit interval kontroly dodržování předpisů najdete v tématu [konfigurace Local Configuration Manageru](/PowerShell/DSC/metaConfig).

## <a name="check-the-compliance-status-of-a-managed-node"></a>Kontrola stavu dodržování předpisů spravovaných uzlů

Sestavy o stavu dodržování předpisů ze spravovaných uzlů můžete získat voláním `Get-AzureRmAutomationDscNodeReport` rutiny:

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzureRmAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="removing-nodes-from-service"></a>Odebrání uzlů ze služby

Při přidání uzlu do konfigurace stavu služby Azure Automation, jsou nastavení v Local Configuration Manageru nastavená zaregistrovat u služby a o přijetí změn konfigurace a požadované moduly pro konfiguraci počítače.
Pokud se rozhodnete k odebrání uzlu ze služby, lze provést pomocí webu Azure portal nebo rutin Az.

> [!NOTE]
> Zrušení registrace uzlu ze služby pouze nastaví Local Configuration Manageru tak, že uzel už je připojení ke službě.
> To vliv nemá odpovídající konfiguraci, která je aktuálně použité k uzlu.
> Chcete-li odebrat aktuální konfiguraci, použijte [Powershellu](https://docs.microsoft.com/en-us/powershell/module/psdesiredstateconfiguration/remove-dscconfigurationdocument?view=powershell-5.1) nebo odstranit místní konfigurační soubor (to je jedinou možností pro uzly s Linuxem).

### <a name="azure-portal"></a>portál Azure

Ve službě Azure Automation, klikněte na **konfigurace stavu (DSC)** v obsahu.
Klepnutím na tlačítko **uzly** zobrazíte seznam uzlů, které jsou registrované ve službě.
Klikněte na název uzlu, na který chcete odebrat.
V zobrazení uzlů, které se otevře, klikněte na tlačítko **Unregister**.

### <a name="powershell"></a>PowerShell

Zrušení registrace uzlu ze služby konfigurace stavu služby Azure Automation pomocí prostředí PowerShell, postupujte podle dokumentace pro rutinu [Unregister-AzAutomationDscNode](https://docs.microsoft.com/en-us/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-2.0.0).

## <a name="next-steps"></a>Další postup

- Abyste mohli začít, najdete v článku [Začínáme s Azure Automation stavu konfigurace](automation-dsc-getting-started.md)
- Další informace jak připojit uzlů najdete v článku [připojování počítačů pro správu podle konfigurace stavu služby Azure Automation](automation-dsc-onboarding.md)
- Další informace o kompilaci konfigurace DSC, takže můžete je přiřadit k cílové uzly, naleznete v tématu [kompilace konfigurací v konfiguraci stavu služby Azure Automation](automation-dsc-compile.md)
- Reference k rutinám Powershellu najdete v části [rutiny Azure Automation stavu konfigurace](/powershell/module/azurerm.automation/#automation)
- Informace o cenách najdete v tématu [ceny konfigurace stavu služby Azure Automation](https://azure.microsoft.com/pricing/details/automation/)
- Příklad použití Azure Automation stav konfigurace v kanálu průběžného nasazování najdete v tématu [nepřetržité nasazení pomocí Azure Automation konfigurace stavu a Chocolatey](automation-dsc-cd-chocolatey.md)
