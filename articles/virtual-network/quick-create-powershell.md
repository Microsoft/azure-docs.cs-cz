---
title: Vytvoření virtuální sítě – úvodní příručka – Azure PowerShell
titlesuffix: Azure Virtual Network
description: V tomto rychlém startu zjistíte, jak vytvořit virtuální síť pomocí webu Azure Portal. Virtuální síť umožňuje prostředky Azure, jako jsou virtuální počítače, komunikovat soukromě mezi sebou navzájem a s internetem.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
tags: azure-resource-manager
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.service: virtual-network
ms.devlang: ''
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/04/2018
ms.author: kumud
ms.openlocfilehash: 1d30b35264707c59c899cc3a224e4affa2a4696e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241441"
---
# <a name="quickstart-create-a-virtual-network-using-powershell"></a>Rychlý start: Vytvoření virtuální sítě pomocí PowerShellu

Virtuální síť umožňuje prostředky Azure, jako jsou virtuální počítače (VM), komunikovat soukromě mezi sebou a s internetem. V tomto rychlém startu zjistíte, jak vytvořit virtuální síť. Po vytvoření virtuální sítě do ní nasadíte dva virtuální počítače. Potom se připojíte k virtuálním počítačům z Internetu a soukromě komunikujete prostřednictvím virtuální sítě.

Pokud ještě nemáte předplatné Azure, vytvořte si teď [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se místo toho rozhodnete nainstalovat a použít PowerShell místně, tento rychlý start vyžaduje použití modulu Azure PowerShell verze 1.0.0 nebo novějšího. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Informace o instalaci a [upgradu najdete v tématu Instalace modulu Azure PowerShellu.](/powershell/azure/install-az-ps)

Nakonec, pokud používáte Prostředí PowerShell místně, budete také `Connect-AzAccount`muset spustit . Tento příkaz vytvoří připojení s Azure.

## <a name="create-a-resource-group-and-a-virtual-network"></a>Vytvoření skupiny prostředků a virtuální sítě

Existuje několik kroků, které musíte projít, abyste získali konfiguraci skupiny prostředků a virtuální sítě.

### <a name="create-the-resource-group"></a>Vytvoření skupiny prostředků

Před vytvořením virtuální sítě je třeba vytvořit skupinu prostředků pro hostování ve virtuální síti. Vytvořte skupinu prostředků pomocí [skupiny New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). Tento příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *eastus:*

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

### <a name="create-the-virtual-network"></a>Vytvoření virtuální sítě

Vytvořte virtuální síť s [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Tento příklad vytvoří výchozí virtuální síť s názvem *myVirtualNetwork* v umístění *EastUS:*

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Přidání podsítě

Azure nasazuje prostředky do podsítě v rámci virtuální sítě, takže je potřeba vytvořit podsíť. Vytvořte konfiguraci podsítě s názvem *Výchozí* pomocí [funkce Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig):

```azurepowershell-interactive
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name default `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

### <a name="associate-the-subnet-to-the-virtual-network"></a>Přidružení podsítě k virtuální síti

Konfiguraci podsítě můžete zapsat do virtuální sítě pomocí [programu Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork). Tento příkaz vytvoří podsíť:

```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvořte ve virtuální síti dva virtuální počítače.

### <a name="create-the-first-vm"></a>Vytvoření prvního virtuálního počítače

Vytvořte první virtuální virtuální město s [novým AzVM](/powershell/module/az.compute/new-azvm). Při spuštění dalšího příkazu budete vyzváni k zadání pověření. Zadejte uživatelské jméno a heslo pro virtuální počítače:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "default" `
    -Name "myVm1" `
    -AsJob
```

Tato `-AsJob` možnost vytvoří virtuální ho svitek na pozadí. Můžete pokračovat dalším krokem.

Když Azure začne vytvářet virtuální počítač na pozadí, dostanete něco takového zpět:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

### <a name="create-the-second-vm"></a>Vytvoření druhého virtuálního počítače

Vytvořte druhý virtuální virtuální ms pomocí tohoto příkazu:

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "default" `
  -Name "myVm2"
```

Budete muset vytvořit jiného uživatele a heslo. Azure trvá několik minut k vytvoření virtuálního počítače.

> [!IMPORTANT]
> Nepokračujte dalším krokem, dokud Azure neskončí.  Budete vědět, že se provádí, když vrátí výstup do prostředí PowerShell.

## <a name="connect-to-a-vm-from-the-internet"></a>Připojení k virtuálnímu počítači z internetu

Pomocí [get-azPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) vrátit veřejnou IP adresu virtuálního počítačů. Tento příklad vrátí veřejnou IP adresu virtuálního *montu myVm1:*

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Otevřete příkazový řádek v místním počítači. Spusťte příkaz `mstsc`. Nahradit `<publicIpAddress>` veřejnou IP adresou vrácenou z posledního kroku:

> [!NOTE]
> Pokud jste tyto příkazy spouštěli z výzvy prostředí PowerShell v místním počítači a používáte modul Az PowerShell verze 1.0 nebo novější, můžete v tomto rozhraní pokračovat.

```cmd
mstsc /v:<publicIpAddress>
```
1. Pokud se zobrazí výzva, vyberte **Připojit**.

1. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.

    > [!NOTE]
    > Možná budete muset vybrat **Další volby:** > K zadání pověření, která jste zadali při vytváření virtuálního vztahu,**můžete zadat jiný účet**.

1. Vyberte **OK**.

1. Může se zobrazit upozornění na certifikát. Pokud tak učiníte, vyberte **Ano** nebo **Pokračovat**.

## <a name="communicate-between-vms"></a>Komunikace mezi virtuálními počítači

1. Na vzdálené ploše *myVm1*otevřete prostředí PowerShell.

1. Zadejte `ping myVm2`.

    Dostanete něco takového zpět:

    ```powershell
    PS C:\Users\myVm1> ping myVm2

    Pinging myVm2.ovvzzdcazhbu5iczfvonhg2zrb.bx.internal.cloudapp.net
    Request timed out.
    Request timed out.
    Request timed out.
    Request timed out.

    Ping statistics for 10.0.0.5:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
    ```

    Příkaz ping se nezdaří, protože používá protokol ICMP (Internet Control Message Protocol). Ve výchozím nastavení není icmp povoleno prostřednictvím brány firewall systému Windows.

1. Chcete-li povolit *příkaz myVm2* ping *myVm1* v pozdějším kroku, zadejte tento příkaz:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Tento příkaz umožňuje icmp příchozí přes bránu firewall systému Windows.

1. Ukončete připojení ke vzdálené ploše virtuálního počítače *myVm1*.

1. Opakujte kroky v [části Připojení k virtuálnímu virtuálnímu virtuálnímu zařízení z internetu](#connect-to-a-vm-from-the-internet). Tentokrát se připojte k *myVm2*.

1. Na příkazovém řádku na virtuálním počítači *myVm2* zadejte `ping myvm1`.

    Dostanete něco takového zpět:

    ```cmd
    C:\windows\system32>ping myVm1

    Pinging myVm1.e5p2dibbrqtejhq04lqrusvd4g.bx.internal.cloudapp.net [10.0.0.4] with 32 bytes of data:
    Reply from 10.0.0.4: bytes=32 time=2ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128

    Ping statistics for 10.0.0.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 2ms, Average = 0ms
    ```

    Obdržíte odpovědi z virtuálního počítače *myVm1*, protože jste v předchozím kroku povolili průchod protokolu ICMP bránou Windows Firewall na virtuálním počítači *myVm1*.

1. Ukončete připojení ke vzdálené ploše virtuálního počítače *myVm2*.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení s virtuální sítí a virtuálními počítači, použijte [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) odebrat skupinu prostředků a všechny prostředky, které má:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili výchozí virtuální síť a dva virtuální počítače. K jednomu virtuálnímu počítači jste se připojili z internetu a navázali jste soukromou komunikaci mezi tímto a druhým virtuálním počítačem. Další informace o nastavení virtuální sítě najdete v tématu [Správa virtuální sítě](manage-virtual-network.md).

Azure umožňuje neomezenou privátní komunikaci mezi virtuálními počítači. Ve výchozím nastavení Azure povoluje jenom příchozí připojení ke vzdálené ploše k virtuálním počítačům s Windows z internetu. Další informace o konfiguraci různých typů síťové komunikace virtuálních počítačů najdete v kurzu [Filtrování síťového provozu.](tutorial-filter-network-traffic.md)
