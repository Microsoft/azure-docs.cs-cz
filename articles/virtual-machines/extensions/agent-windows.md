---
title: Přehled agenta virtuálního počítače Azure | Microsoft Docs
description: Přehled agenta virtuálního počítače Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: axayjo
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: 0a1f212e-053e-4a39-9910-8d622959f594
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/20/2019
ms.author: akjosh
ms.openlocfilehash: 7c163dd48e53a3116d58cb94988f2822ddede5e5
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/20/2019
ms.locfileid: "71169125"
---
# <a name="azure-virtual-machine-agent-overview"></a>Přehled agenta virtuálního počítače Azure
Agent virtuálního počítače Microsoft Azure (agent virtuálního počítače) je zabezpečený a odlehčený proces, který spravuje interakci virtuálních počítačů s řadičem prostředků infrastruktury Azure. Agent virtuálního počítače má primární roli při povolování a provádění rozšíření virtuálních počítačů Azure. Rozšíření virtuálních počítačů umožňují konfiguraci po nasazení virtuálního počítače, jako je instalace a konfigurace softwaru. Rozšíření virtuálních počítačů také umožňují funkce pro obnovení, jako je resetování hesla pro správu virtuálního počítače. Bez agenta virtuálního počítače Azure nejde spustit rozšíření virtuálních počítačů.

Tento článek podrobně popisuje instalaci, detekci a odebrání agenta virtuálních počítačů Azure.

## <a name="install-the-vm-agent"></a>Instalace agenta virtuálního počítače

### <a name="azure-marketplace-image"></a>Obrázek Azure Marketplace

Agent virtuálního počítače Azure se ve výchozím nastavení instaluje na libovolný virtuální počítač s Windows nasazený z bitové kopie Azure Marketplace. Když nasadíte Azure Marketplace image z portálu, PowerShellu, rozhraní příkazového řádku nebo šablony Azure Resource Manager, nainstaluje se taky agent virtuálního počítače Azure.

Balíček agenta hosta systému Windows je rozdělen do dvou částí:

- Agent zřizování (PA)
- Agent hosta systému Windows (křídlo)

Pokud chcete spustit virtuální počítač, musíte mít na virtuálním počítači nainstalovanou službu PA, ale není potřeba ji instalovat. V době nasazení virtuálního počítače můžete vybrat možnost neinstalovat křídlo. Následující příklad ukazuje, jak vybrat možnost *provisionVmAgent* pomocí šablony Azure Resource Manager:

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

Pokud nemáte nainstalované agenty, nemůžete použít některé služby Azure, například Azure Backup nebo zabezpečení Azure. Tyto služby vyžadují instalaci rozšíření. Pokud jste virtuální počítač nasadili bez křídla, můžete nainstalovat nejnovější verzi agenta později.

### <a name="manual-installation"></a>Ruční instalace
Agenta virtuálního počítače s Windows je možné ručně nainstalovat pomocí balíčku Instalační služby systému Windows. Ruční instalace může být nutná při vytváření vlastní image virtuálního počítače, která je nasazena do Azure. Chcete-li ručně nainstalovat agenta virtuálního počítače s Windows, [Stáhněte si instalační program agenta virtuálního počítače](https://go.microsoft.com/fwlink/?LinkID=394789). Agent virtuálního počítače je podporovaný v systému Windows Server 2008 R2 a novějším.

Agenta virtuálního počítače můžete nainstalovat dvojitým kliknutím na soubor Instalační služby systému Windows. Pro automatizovanou nebo bezobslužnou instalaci agenta virtuálního počítače spusťte následující příkaz:

```cmd
msiexec.exe /i WindowsAzureVmAgent.2.7.1198.778.rd_art_stable.160617-1120.fre /quiet
```

### <a name="prerequisites"></a>Požadavky
Aby bylo možné spustit agenta virtuálního počítače s Windows, musí být v rozhraní .NET Framework 4,0 alespoň Windows Server 2008 R2 (64-bitů).

## <a name="detect-the-vm-agent"></a>Zjištění agenta virtuálního počítače

### <a name="powershell"></a>PowerShell

Modul Azure Resource Manager PowerShellu se dá použít k načtení informací o virtuálních počítačích Azure. Pokud chcete zobrazit informace o virtuálním počítači, jako je stav zřizování agenta virtuálního počítače Azure, použijte [příkaz Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm):

```powershell
Get-AzVM
```

Následující zhuštěný příklad výstupu ukazuje vlastnost *ProvisionVMAgent* vnořenou v *OSProfile*. Tato vlastnost slouží k určení, jestli je na virtuálním počítači nasazený agent virtuálního počítače:

```powershell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : myUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

Pomocí následujícího skriptu můžete vracet stručný seznam názvů virtuálních počítačů a stav agenta virtuálního počítače:

```powershell
$vms = Get-AzVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>Ruční zjišťování

Když se přihlásíte k virtuálnímu počítači s Windows, můžete ke kontrole spuštěných procesů použít Správce úloh. Pokud chcete zkontrolovat agenta virtuálního počítače Azure, otevřete Správce úloh, klikněte na kartu *Podrobnosti* a vyhledejte název procesu **WindowsAzureGuestAgent. exe**. Přítomnost tohoto procesu indikuje, že je agent virtuálního počítače nainstalovaný.


## <a name="upgrade-the-vm-agent"></a>Upgrade agenta virtuálního počítače
Agent virtuálního počítače Azure pro Windows se upgraduje automaticky. Když se do Azure nasadí nové virtuální počítače, dostanou nejnovějšího agenta virtuálního počítače při zřizování virtuálních počítačů. Vlastní image virtuálních počítačů by se měly aktualizovat ručně, aby se při vytváření image zahrnul nový agent virtuálního počítače.


## <a name="next-steps"></a>Další kroky
Další informace o rozšíření virtuálních počítačů najdete v tématu [Přehled rozšíření a funkcí virtuálních počítačů Azure](overview.md).
