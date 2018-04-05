---
title: Vytvoření virtuální sítě Azure – prostředí PowerShell | Microsoft Docs
description: Rychle se Naučte se vytvořit virtuální síť pomocí prostředí PowerShell. Virtuální síť umožňuje prostředky Azure, jako jsou virtuální počítače, pro soukromě komunikaci mezi sebou a s Internetem.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: ''
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/09/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: fe171000f83c27f23972569b93e351340f4426ad
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="create-a-virtual-network-using-powershell"></a>Vytvoření virtuální sítě pomocí prostředí PowerShell

Virtuální síť umožňuje prostředky Azure, jako jsou virtuální počítače (VM), soukromě komunikaci mezi sebou a s Internetem. V tomto článku zjistěte, jak vytvořit virtuální síť. Po vytvoření virtuální sítě, můžete nasadit dva virtuální počítače do virtuální sítě. Potom připojení jeden virtuální počítač z Internetu a soukromě komunikaci mezi dvěma virtuálními počítači.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-powershell.md)]

Pokud si zvolíte instalaci a použití prostředí PowerShell místně, v tomto článku vyžaduje prostředí AzureRM PowerShell verze modulu 5.4.1 nebo novější. Chcete-li najít nainstalovanou verzi, spusťte ` Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Login-AzureRmAccount` pro vytvoření připojení k Azure.

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Než bude možné vytvořit virtuální síť, musíte vytvořit skupinu prostředků tak, aby obsahovala virtuální sítě. Vytvořte skupinu prostředků s [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

Vytvořte virtuální síť pomocí rutiny [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). Následující příklad vytvoří výchozí virtuální síť s názvem *myVirtualNetwork* v *EastUS* umístění:

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Prostředky Azure se nasadí do podsítí v rámci virtuální sítě, takže je třeba vytvořit podsíť. Vytvoření konfigurací podsítě s [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). 

```azurepowershell-interactive
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name default `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Zapsat konfiguraci podsítě virtuální sítě s [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), která vytvoří podsíť virtuální sítě:

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvořte dva virtuální počítače ve virtuální síti:

### <a name="create-the-first-vm"></a>Vytvoření první virtuální počítač

Vytvoření virtuálního počítače s [nové AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Když spustíte příkaz, který následuje, zobrazí se výzva k zadání pověření. Hodnoty, které zadáte jsou nakonfigurovány jako uživatelské jméno a heslo pro virtuální počítač. `-AsJob` Možnost vytvoří virtuální počítač na pozadí, aby mohl pokračovat k dalšímu kroku.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "default" `
    -Name "myVm1" `
    -AsJob
```

Výstup podobné výstupu v následujícím příkladu se vrátí, a Azure spustí vytváření virtuálního počítače na pozadí.

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command                  
--     ----            -------------   -----         -----------     --------             -------                  
1      Long Running... AzureLongRun... Running       True            localhost            New-AzureRmVM     
```

### <a name="create-the-second-vm"></a>Vytvořit druhý virtuální počítač 

Zadejte následující příkaz:

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "default" `
  -Name "myVm2"
```

Virtuální počítač trvá několik minut pro vytvoření. Dalším krokem nepokračujte, dokud se předchozí příkaz provede a výstup se vrací do prostředí PowerShell.

## <a name="connect-to-a-vm-from-the-internet"></a>Připojení k virtuálnímu počítači z Internetu

Použití [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) vrátit veřejnou IP adresu virtuálního počítače. Následující příklad vrací veřejnou IP adresu *myVm1* virtuálních počítačů:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Nahraďte `<publicIpAddress>` v následující příkaz, s veřejnou IP adresu vrácená z předchozí příkaz a potom zadejte následující příkaz: 

```
mstsc /v:<publicIpAddress>
```

Soubor Remote Desktop Protocol (.rdp) je vytvořen a stažena do počítače. Otevřete soubor stažený rdp. Po zobrazení výzvy vyberte **Connect**. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače. Je nutné vybrat **další možnosti**, pak **použít jiný účet**, zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače. Vyberte **OK**. Během procesu přihlášení se může zobrazit upozornění certifikátu. Pokud se zobrazí upozornění, vyberte **Ano** nebo **pokračovat**, chcete-li pokračovat s připojením.

## <a name="communicate-privately-between-vms"></a>Soukromě komunikaci mezi virtuálními počítači

Z prostředí PowerShell na *myVm1* virtuální počítač, zadejte `ping myvm2`. Příkaz ping nezdaří, protože ping používá protokol (ICMP) a ICMP není přes bránu Windows firewall povolena ve výchozím nastavení.

Chcete-li povolit *Můjvp2* na příkaz ping *myVm1* v pozdější fázi, zadejte následující příkaz z prostředí PowerShell, což umožňuje ICMP příchozí komunikace přes bránu Windows firewall:

```powershell
New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
```

Zavřete připojení ke vzdálené ploše *myVm1*. 

Proveďte kroky v [připojení k virtuálnímu počítači z Internetu](#connect-to-a-vm-from-the-internet) znovu, ale připojení k *Můjvp2*. 

Z příkazového řádku na *Můjvp2* virtuální počítač, zadejte `ping myvm1`.

Obdržíte odpovědi z *myVm1*, protože povolené ICMP přes bránu Windows firewall na *myVm1* virtuálních počítačů v předchozím kroku.

Zavřete připojení ke vzdálené ploše *Můjvp2*.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již nepotřebujete, můžete použít [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) odebrat skupinu prostředků a všechny prostředky, které obsahuje:

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste vytvořili výchozí virtuální sítě a dva virtuální počítače. Připojený k jeden virtuální počítač z Internetu a soukromě přenášená mezi virtuálního počítače a jiným virtuálním Počítačem. Další informace o nastavení virtuální sítě najdete v tématu [spravovat virtuální sítě](manage-virtual-network.md). 

Ve výchozím nastavení Azure umožňuje neomezený privátní komunikaci mezi virtuálními počítači, ale umožňuje pouze příchozí připojení ke vzdálené ploše na virtuálních počítačích Windows z Internetu. Postup povolení nebo zakázání různé typy síťové komunikace do a z virtuálních počítačů, přechodu na v dalším kurzu.

> [!div class="nextstepaction"]
> [Filtrování provozu sítě přenosů](tutorial-filter-network-traffic.md)
