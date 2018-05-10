---
title: Přehled agenta virtuálního počítače Azure | Microsoft Docs
description: Přehled agenta virtuálního počítače Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: danielsollondon
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 0a1f212e-053e-4a39-9910-8d622959f594
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: danis
ms.openlocfilehash: fb29f0f931715b8a6ba5b4528294eb61ef5762c8
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="azure-virtual-machine-agent-overview"></a>Přehled služby Azure agenta virtuálního počítače
Agent virtuálního počítače Microsoft Azure (VM Agent) je zabezpečený, lightweight proces, který spravuje interakci virtuální počítač (VM) s Kontroleru prostředků infrastruktury Azure. Agent virtuálního počítače má primární roli v povolení a spuštění rozšíření virtuálního počítače Azure. Rozšíření virtuálních počítačů povolit konfiguraci po nasazení virtuálního počítače, jako je instalace a konfigurace softwaru. Rozšíření virtuálního počítače také povolit obnovení funkce jako je resetování hesla pro správu virtuálního počítače. Bez agenta virtuálního počítače Azure nelze spustit, rozšíření virtuálního počítače.

Tento článek podrobné informace o instalaci, zjištění a odebrání agenta virtuálního počítače Azure.

## <a name="install-the-vm-agent"></a>Nainstalujte agenta virtuálního počítače

### <a name="azure-marketplace-image"></a>Obrázek pro Azure Marketplace.

Agent virtuálního počítače Azure je nainstalována ve výchozím nastavení pro všechny Windows virtuální počítač nasazen z bitové kopie Azure Marketplace. Při nasazení image Azure Marketplace z portálu, prostředí PowerShell, rozhraní příkazového řádku nebo šablonu Azure Resource Manager, je také nainstalován Agent virtuálního počítače Azure.

Balíček Windows hostovaného agenta je rozděleno do dvou částí:

- Agentem zřizování (PA)
- Agent hosta systému Windows (WinGA)

Ke spuštění virtuálního počítače musí mít PA nainstalovaný na Virtuálním počítači, ale WinGA nemusí být nainstalován. Na virtuální počítač nasadit čas, můžete vybrat nechcete instalovat WinGA. Následující příklad ukazuje, jak vybrat *provisionVmAgent* možnost pomocí šablony Azure Resource Manager:

```json
"resources": [{
"name": "[parameters('virtualMachineName')]",
"type": "Microsoft.Compute/virtualMachines",
"apiVersion": "2016-04-30-preview",
"location": "[parameters('location')]",
"dependsOn": ["[concat('Microsoft.Network/networkInterfaces/', parameters('networkInterfaceName'))]"],
"properties": {
    "osProfile": {
    "computerName": "[parameters('virtualMachineName')]",
    "adminUsername": "[parameters('adminUsername')]",
    "adminPassword": "[parameters('adminPassword')]",
    "windowsConfiguration": {
        "provisionVmAgent": "false"
}
```

Pokud jste agenty nainstalovat, nebudete moct používat některé služby Azure, například zálohování Azure a zabezpečení Azure. Tyto služby vyžadují instalaci rozšíření. Pokud jste nasadili virtuální počítač bez WinGA, můžete nainstalovat nejnovější verzi agenta později.

### <a name="manual-installation"></a>Ruční instalace
S balíčkem Instalační služby systému Windows lze ručně nainstalovat agenta virtuálního počítače s Windows. Ruční instalace může být nutné, když vytvoříte vlastní image virtuálního počítače, který je nasazen do Azure. Při ruční instalaci agenta virtuálního počítače Windows, [stáhnout instalační program agenta virtuálního počítače](http://go.microsoft.com/fwlink/?LinkID=394789).

Dvojitým kliknutím na soubor Instalační služby systému Windows můžete nainstalovat agenta virtuálního počítače. Pro automatizované nebo bezobslužné instalace agenta virtuálního počítače spusťte následující příkaz:

```cmd
msiexec.exe /i WindowsAzureVmAgent.2.7.1198.778.rd_art_stable.160617-1120.fre /quiet
```

## <a name="detect-the-vm-agent"></a>Zjištění agenta virtuálního počítače

### <a name="powershell"></a>PowerShell

Modul Powershellu pro Azure Resource Manager lze použít k načtení informací o virtuálních počítačích Azure. Pokud chcete zobrazit informace o používání virtuálních počítačů, jako je například stav zřizování pro agenta virtuálního počítače Azure, použijte [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm):

""powershell"Get-AzureRmVM
```

The following condensed example output shows the the *ProvisionVMAgent* property nested inside *OSProfile*. This property can be used to determine if the VM agent has been deployed to the VM:

```PowerShell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : myUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

Následující skript lze použít k vrácení stručným seznamu názvů virtuálních počítačů a stav agenta virtuálního počítače:

```PowerShell
$vms = Get-AzureRmVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>Ruční detekce
Při přihlášení k virtuálnímu počítači Windows Azure, Správce úloh můžete použít k prozkoumání spuštěných procesů. Chcete-li vyhledat agenta virtuálního počítače Azure, otevřete Správce úloh, klikněte na *podrobnosti* kartě a vyhledejte název procesu **WindowsAzureGuestAgent.exe**. Přítomnost tento proces naznačuje, že je nainstalovaný agent virtuálního počítače.


## <a name="upgrade-the-vm-agent"></a>Upgrade agenta virtuálního počítače
Virtuální počítač agenta k Azure pro systém Windows automaticky upgradován. Jako nové virtuální počítače nasazované do Azure, obdrží nejnovější agenta virtuálního počítače během zřizování virtuálních počítačů. Vlastní Image virtuálních počítačů by měl ručně aktualizovalo se o nový agent virtuálního počítače v okamžiku vytvoření bitové kopie.


## <a name="next-steps"></a>Další postup
Další informace o rozšíření virtuálního počítače najdete v tématu [virtuální počítač Azure rozšíření a funkce přehled](overview.md).