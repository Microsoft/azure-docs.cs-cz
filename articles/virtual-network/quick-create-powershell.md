---
title: Vytvoření virtuální sítě – Rychlý start – Azure PowerShell | Microsoft Docs
description: V tomto rychlém startu zjistíte, jak vytvořit virtuální síť pomocí webu Azure Portal. Virtuální síť umožňuje prostředkům Azure, jako jsou virtuální počítače, komunikovat soukromě mezi sebou a s internetem.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/09/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: 3d4c8e130f96c1b89247fe0c092363c33032ec3d
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="quickstart-create-a-virtual-network-using-powershell"></a>Rychlý start: Vytvoření virtuální sítě pomocí PowerShellu

Virtuální síť umožňuje prostředkům Azure, jako jsou virtuální počítače, komunikovat soukromě mezi sebou a s internetem. V tomto rychlém startu zjistíte, jak vytvořit virtuální síť. Po vytvoření virtuální sítě do ní nasadíte dva virtuální počítače. Pak se k jednomu virtuálnímu počítači připojíte z internetu a navážete soukromou komunikaci s druhým virtuálním počítačem.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte použít modul AzureRM PowerShell verze 5.4.1 nebo novější. Nainstalovanou verzi zjistíte spuštěním rutiny ` Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Login-AzureRmAccount` pro vytvoření připojení k Azure.

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Než budete moct vytvořit virtuální síť, musíte vytvořit skupinu prostředků, která bude virtuální síť obsahovat. Vytvořte skupinu prostředků pomocí rutiny [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

Vytvořte virtuální síť pomocí rutiny [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). Následující příklad vytvoří výchozí virtuální síť *myVirtualNetwork* v umístění *EastUS*:

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Prostředky Azure se nasadí do podsítě v rámci virtuální sítě, takže musíte vytvořit podsíť. Vytvořte konfiguraci podsítě pomocí rutiny [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). 

```azurepowershell-interactive
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name default `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Zapište konfiguraci podsítě do virtuální sítě pomocí rutiny [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), která v rámci virtuální sítě vytvoří podsíť:

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvořte ve virtuální síti dva virtuální počítače:

### <a name="create-the-first-vm"></a>Vytvoření prvního virtuálního počítače

Vytvořte virtuální počítač pomocí rutiny [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Při spuštění následujícího příkazu se zobrazí výzva k zadání přihlašovacích údajů. Hodnoty, které zadáte, se nakonfigurují jako uživatelské jméno a heslo pro virtuální počítač. Pomocí možnosti `-AsJob` se virtuální počítač vytvoří na pozadí, takže můžete pokračovat k dalšímu kroku.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "default" `
    -Name "myVm1" `
    -AsJob
```

Vrátí se podobný výstup jako v následujícím příkladu a Azure zahájí vytváření virtuálního počítače na pozadí.

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command                  
--     ----            -------------   -----         -----------     --------             -------                  
1      Long Running... AzureLongRun... Running       True            localhost            New-AzureRmVM     
```

### <a name="create-the-second-vm"></a>Vytvoření druhého virtuálního počítače 

Zadejte následující příkaz:

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "default" `
  -Name "myVm2"
```

Vytvoření virtuálního počítače trvá několik minut. Nepokračujte dalším krokem, dokud se předchozí příkaz neprovede a do PowerShellu se nevrátí výstup.

## <a name="connect-to-a-vm-from-the-internet"></a>Připojení k virtuálnímu počítači z internetu

Pomocí rutiny [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) získejte veřejnou IP adresu virtuálního počítače. Následující příklad vrátí veřejnou IP adresu virtuálního počítače *myVm1*:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

V následujícím příkazu nahraďte `<publicIpAddress>` veřejnou IP adresou vrácenou předchozím příkazem a pak ho zadejte: 

```
mstsc /v:<publicIpAddress>
```

Vytvoří se soubor protokolu RDP (.rdp) a stáhne se na váš počítač. Otevřete stažený soubor .rdp. Pokud se zobrazí výzva, vyberte **Připojit**. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače. Možná bude nutné vybrat **Další možnosti** a pak **Použít jiný účet** a zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače. Vyberte **OK**. Během procesu přihlášení se může zobrazit upozornění certifikátu. Pokud se toto upozornění zobrazí, vyberte **Ano** nebo **Pokračovat** a pokračujte v připojování.

## <a name="communicate-between-vms"></a>Komunikace mezi virtuálními počítači

V PowerShellu na virtuálním počítači *myVm1* zadejte `ping myvm2`. Příkaz ping selže, protože využívá protokol ICMP (Internet Control Message Protocol), který ve výchozím nastavení nemá povolený průchod bránou Windows Firewall.

Pokud chcete virtuálnímu počítači *myVm2* povolit otestování virtuálního počítače *myVm1* pomocí příkazu ping v pozdějším kroku, zadejte v PowerShellu následující příkaz, který povolí příchozí provoz protokolu ICMP přes bránu Windows Firewall:

```powershell
New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
```

Ukončete připojení ke vzdálené ploše virtuálního počítače *myVm1*. 

Zopakujte kroky uvedené v části [Připojení k virtuálnímu počítači z internetu](#connect-to-a-vm-from-the-internet), ale připojte se k virtuálnímu počítači *myVm2*. 

Na příkazovém řádku na virtuálním počítači *myVm2* zadejte `ping myvm1`.

Obdržíte odpovědi z virtuálního počítače *myVm1*, protože jste v předchozím kroku povolili průchod protokolu ICMP bránou Windows Firewall na virtuálním počítači *myVm1*.

Ukončete připojení ke vzdálené ploše virtuálního počítače *myVm2*.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků, které obsahuje, použít rutinu [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup):

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili výchozí virtuální síť a dva virtuální počítače. K jednomu virtuálnímu počítači jste se připojili z internetu a navázali jste soukromou komunikaci mezi tímto a druhým virtuálním počítačem. Další informace o nastavení virtuální sítě najdete v tématu [Správa virtuální sítě](manage-virtual-network.md). 

Ve výchozím nastavení Azure umožňuje neomezenou soukromou komunikaci mezi virtuálními počítači, ale u virtuálních počítačů s Windows povoluje pouze příchozí připojení ke vzdálené ploše z internetu. Informace o povolení nebo zakázání různých typů síťové komunikace do a z virtuálních počítačů najdete v dalším kurzu [Filtrování síťového provozu](tutorial-filter-network-traffic.md).
