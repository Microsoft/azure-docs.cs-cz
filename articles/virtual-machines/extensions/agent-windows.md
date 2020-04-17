---
title: Přehled agenta virtuálního počítače Azure
description: Přehled agenta virtuálního počítače Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: mimckitt
manager: gwallace
tags: azure-resource-manager
ms.assetid: 0a1f212e-053e-4a39-9910-8d622959f594
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/20/2019
ms.author: akjosh
ms.openlocfilehash: f29a20ddeb93ec3d4aa98bbcb36f50456b543667
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81452566"
---
# <a name="azure-virtual-machine-agent-overview"></a>Přehled agenta virtuálního počítače Azure
Microsoft Azure Virtual Machine Agent (VM Agent) je bezpečný, lehký proces, který spravuje interakci virtuálních strojů (VM) s řadičem Infrastruktury Azure. Agent virtuálního počítače má primární roli při povolení a provádění rozšíření virtuálních počítačů Azure. Rozšíření virtuálních počítačů umožňují konfiguraci virtuálního počítače po nasazení, jako je instalace a konfigurace softwaru. Rozšíření virtuálních počítače také umožňují funkce obnovení, jako je resetování hesla pro správu virtuálního počítače. Bez agenta virtuálního počítače Azure nelze spouštět rozšíření virtuálních počítače.

Tento článek podrobně popisuje instalaci a detekci agenta virtuálních strojů Azure.

## <a name="install-the-vm-agent"></a>Instalace agenta virtuálního soudu

### <a name="azure-marketplace-image"></a>Image Azure Marketplace

Agent virtuálních zařízení Azure se ve výchozím nastavení instaluje na libovolném virtuálním počítači s Windows nasazeným z image Azure Marketplace. Když nasadíte image Azure Marketplace z portálu, PowerShellu, rozhraní Command Line nebo šablony Azure Resource Manager, nahraje se taky agent virtuálního počítače Azure.

Balíček agenta hosta systému Windows je rozdělen na dvě části:

- Zprostředkovatel zřizování (PA)
- Agent pro hosty systému Windows (WinGA)

Chcete-li spustit virtuální počítač, musíte mít pa nainstalovaný na virtuálním počítači, ale WinGA nemusí být nainstalován. V době nasazení virtuálního aplikace můžete vybrat, že se nemá instalovat WinGA. Následující příklad ukazuje, jak vybrat možnost *provisionVmAgent* pomocí šablony Azure Resource Manager:

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

Pokud nemáte nainstalované agenty, nemůžete používat některé služby Azure, jako je Azure Backup nebo Azure Security. Tyto služby vyžadují rozšíření, které má být nainstalováno. Pokud jste nasadili virtuální ho disponiifikovat bez WinGA, můžete nainstalovat nejnovější verzi agenta později.

### <a name="manual-installation"></a>Ruční instalace
Agenta virtuálního zařízení systému Windows lze nainstalovat ručně pomocí instalačního balíčku systému Windows. Ruční instalace může být nezbytná při vytváření vlastní image virtuálního počítače, která se nasadí do Azure. Chcete-li agenta virtuálního zařízení pro Windows nainstalovat ručně, [stáhněte si instalační program agenta virtuálního zařízení](https://go.microsoft.com/fwlink/?LinkID=394789). Agent virtuálního počítače je podporovaný v systému Windows Server 2008 R2 a novějším.

> [!NOTE]
> Je důležité aktualizovat Možnost AllowExtensionOperations po ruční instalaci VMAgent na virtuální počítač, který byl nasazen z image bez ProvisionVMAgent povolit.

```powershell
$vm.OSProfile.AllowExtensionOperations = $true
$vm | Update-AzVM
```

### <a name="prerequisites"></a>Požadavky
- Agent virtuálního připojení systému Windows potřebuje ke spuštění alespoň systém Windows Server 2008 R2 (64 bitů) pomocí rozhraní .Net Framework 4.0. Viz [Podpora minimální verze pro agenty virtuálních strojů v Azure](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)

- Ujistěte se, že váš virtuální počítač má přístup k IP adrese 168.63.129.16. Další informace naleznete [v tématu Co je IP adresa 168.63.129.16](https://docs.microsoft.com/azure/virtual-network/what-is-ip-address-168-63-129-16).

## <a name="detect-the-vm-agent"></a>Detekce agenta virtuálního soudu

### <a name="powershell"></a>PowerShell

Modul PowerShell Azure Resource Manager umíte načíst informace o virtuálních počítačích Azure. Chcete-li zobrazit informace o virtuálním počítači, jako je stav zřizování pro agenta virtuálního počítače Azure, použijte [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm):

```powershell
Get-AzVM
```

Následující výstup zkráceného příkladu ukazuje vlastnost *ProvisionVMAgent* vnořenou uvnitř *osprofile*. Tuto vlastnost můžete použít k určení, pokud agent virtuálního zařízení byl nasazený do virtuálního virtuálního zařízení:

```powershell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : myUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

Následující skript lze použít k vrácení stručného seznamu názvů virtuálních počítačů a stavu agenta virtuálního počítačů:

```powershell
$vms = Get-AzVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>Ruční detekce

Při přihlášení k virtuálnímu virtuálnímu provozu systému Windows lze správce úloh použít ke kontrole spuštěných procesů. Pokud chcete vyhledat agenta virtuálního počítače Azure, otevřete Správce úloh, klikněte na kartu *Podrobnosti* a vyhledejte název procesu **WindowsAzureGuestAgent.exe**. Přítomnost tohoto procesu označuje, že je nainstalován agent virtuálního zařízení.


## <a name="upgrade-the-vm-agent"></a>Upgrade agenta virtuálního softwaru
Agent virtuálního počítače Azure pro Windows se automaticky upgraduje. Jako nové virtuální počítače se nasazují do Azure, obdrží nejnovější agent virtuálního počítače v době zřizování virtuálních počítačů. Vlastní image virtuálních počítačových společností by měly být ručně aktualizovány tak, aby zahrnovaly nového agenta virtuálního počítače v době vytvoření bitové kopie.

## <a name="windows-guest-agent-automatic-logs-collection"></a>Kolekce automatických protokolů agenta hosta systému Windows
Agent hosta systému Windows má funkci automatického shromažďování některých protokolů. Tato funkce je řadič procesu CollectGuestLogs.exe. Existuje pro cloudové služby PaaS i virtuální počítače IaaS a jeho cílem je rychle & automaticky shromažďovat některé diagnostické protokoly z virtuálního počítače – aby je bylo možné použít pro offline analýzu. Shromážděné protokoly jsou protokoly událostí, protokoly operačního es, protokoly Azure a některé klíče registru. Vytvoří soubor ZIP, který se přenese do hostitele virtuálního soudu. Tento soubor ZIP pak můžete prohlížet inženýrské týmy a odborníci podpory, aby prošetřili problémy na žádost zákazníka, který vlastní virtuální počítač.

## <a name="next-steps"></a>Další kroky
Další informace o rozšířeních virtuálních virtuálních zařízení najdete v [tématu Rozšíření virtuálních zařízení Azure a přehled funkcí](overview.md).
