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
ms.date: 11/17/2016
ms.author: danis
ms.openlocfilehash: a5da5cfb6cda940f68171e42d4512b304fc5f114
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
ms.locfileid: "30915669"
---
# <a name="azure-virtual-machine-agent-overview"></a>Přehled služby Azure agenta virtuálního počítače

Agent virtuálního počítače Microsoft Azure (VM Agent) je zabezpečené, lightweight proces, který spravuje interakci virtuálních počítačů s Kontroleru prostředků infrastruktury Azure. Agent virtuálního počítače má primární roli v povolení a spuštění rozšíření virtuálního počítače Azure. Konfigurace nasazení virtuálních počítačů, jako je instalace a konfigurace softwaru příspěvku povolení rozšíření virtuálního počítače. Rozšíření virtuálního počítače také povolit obnovení funkce jako je resetování hesla pro správu virtuálního počítače. Bez agenta virtuálního počítače Azure nelze spustit, rozšíření virtuálního počítače.

Tento dokument podrobně popisuje instalaci, zjištění a odebrání agenta virtuálního počítače Azure.

## <a name="install-the-vm-agent"></a>Nainstalujte agenta virtuálního počítače

### <a name="azure-gallery-image"></a>Obrázek v galerii Azure

Na všechny nasazené z Galerie Azure bitové kopie virtuálního počítače Windows ve výchozím nastavení je nainstalovaný Agent virtuálního počítače Azure. Při nasazování bitové kopie Galerie Azure z portálu, prostředí PowerShell, rozhraní příkazového řádku nebo šablonu Azure Resource Manager, je také nainstalován Agent virtuálního počítače Azure. 

### <a name="manual-installation"></a>Ruční instalace

Agent virtuálního počítače s Windows můžete ručně nainstalovat pomocí balíčku Instalační služby systému Windows. Ruční instalace může být nutné při vytvoření image vlastní virtuální počítač, který se nasadí v Azure. Při ruční instalaci agenta virtuálního počítače Windows, stáhněte si instalační program agenta virtuálního počítače z tohoto umístění [stáhnout agenta virtuálního počítače Azure Windows](http://go.microsoft.com/fwlink/?LinkID=394789). 

Dvojitým kliknutím na soubor Instalační služby systému windows můžete nainstalovat agenta virtuálního počítače. Pro automatizované nebo bezobslužné instalace agenta virtuálního počítače spusťte následující příkaz.

```cmd
msiexec.exe /i WindowsAzureVmAgent.2.7.1198.778.rd_art_stable.160617-1120.fre /quiet
```

## <a name="detect-the-vm-agent"></a>Zjištění agenta virtuálního počítače

### <a name="powershell"></a>PowerShell

Modul Powershellu pro Azure Resource Manager můžete využít k načtení informací o virtuálních počítačích Azure. Spuštění `Get-AzureRmVM` vrátí s bit informace včetně Stav zřizování pro agenta virtuálního počítače Azure.

```PowerShell
Get-AzureRmVM
```

Toto je pouze podmnožinu `Get-AzureRmVM` výstup. Upozornění `ProvisionVMAgent` vlastnost vnořit `OSProfile`, tato vlastnost slouží k určení, pokud agent virtuálního počítače byla nasazena do virtuálního počítače.

```PowerShell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : muUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

Následující skript lze použít k vrácení stručným seznamu názvy virtuálních počítačů a stav agenta virtuálního počítače.

```PowerShell
$vms = Get-AzureRmVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>Ruční detekce

Při přihlášení k virtuálnímu počítači Windows Azure, Správce úloh můžete použít k prozkoumání spuštěných procesů. Pokud chcete zkontrolovat pro agenta virtuálního počítače Azure, otevřete Správce úloh > klikněte na kartu s podrobnostmi a vyhledejte název procesu `WindowsAzureGuestAgent.exe`. Přítomnost tento proces naznačuje, že je nainstalovaný agent virtuálního počítače.

## <a name="upgrade-the-vm-agent"></a>Upgrade agenta virtuálního počítače

Virtuální počítač agenta k Azure pro systém Windows automaticky upgradován. Při nasazování nových virtuálních počítačů do Azure, obdrží nejnovější agenta virtuálního počítače. Vlastní Image virtuálních počítačů by měl ručně aktualizovalo se o nový agent virtuálního počítače.
