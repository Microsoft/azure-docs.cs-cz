---
title: Vytvoření virtuální sítě – rychlý Start – Azure PowerShell
titlesuffix: Azure Virtual Network
description: V tomto rychlém startu vytvoříte virtuální síť pomocí Azure Portal. Virtuální síť umožňuje prostředkům Azure komunikovat mezi sebou a s internetem.
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
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 93e459df96d444e71f4b6a15668f80e9d77db5fd
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "89077870"
---
# <a name="quickstart-create-a-virtual-network-using-powershell"></a>Rychlý start: Vytvoření virtuální sítě pomocí PowerShellu

Virtuální síť umožňuje prostředkům Azure, jako jsou virtuální počítače, komunikovat soukromě mezi sebou a s internetem. V tomto rychlém startu zjistíte, jak vytvořit virtuální síť. Po vytvoření virtuální sítě do ní nasadíte dva virtuální počítače. Pak se k virtuálním počítačům připojíte z Internetu a soukromě komunikujte přes virtuální síť.

## <a name="prerequisites"></a>Předpoklady
Pokud ještě nemáte předplatné Azure, vytvořte si teď [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně místo toho, musíte použít Azure PowerShell modulu verze 1.0.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Informace o instalaci a upgradu najdete v tématu [install Azure PowerShell Module](/powershell/azure/install-az-ps) .

Nakonec, pokud používáte PowerShell místně, budete také muset spustit `Connect-AzAccount` . Tento příkaz vytvoří připojení k Azure.

## <a name="create-a-resource-group-and-a-virtual-network"></a>Vytvoření skupiny prostředků a virtuální sítě

K dispozici je několik kroků, pomocí kterých si můžete projít, abyste získali nakonfigurovanou skupinu prostředků a virtuální síť.

### <a name="create-the-resource-group"></a>Vytvoření skupiny prostředků

Než budete moct vytvořit virtuální síť, musíte vytvořit skupinu prostředků, která bude hostovat virtuální síť. Vytvořte skupinu prostředků pomocí [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). Tento příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *eastus* :

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

### <a name="create-the-virtual-network"></a>Vytvoření virtuální sítě

Vytvořte virtuální síť pomocí [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Tento příklad vytvoří výchozí virtuální síť s názvem *myVirtualNetwork* v umístění *EastUS* :

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Přidání podsítě

Azure nasadí prostředky do podsítě v rámci virtuální sítě, takže je potřeba vytvořit podsíť. Vytvořte konfiguraci podsítě s názvem *Default* pomocí [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig):

```azurepowershell-interactive
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name default `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

### <a name="associate-the-subnet-to-the-virtual-network"></a>Přidružte podsíť k virtuální síti.

Konfiguraci podsítě můžete zapsat do virtuální sítě pomocí [set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork). Tento příkaz vytvoří podsíť:

```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvořte ve virtuální síti dva virtuální počítače.

### <a name="create-the-first-vm"></a>Vytvoření prvního virtuálního počítače

Vytvořte první virtuální počítač pomocí [New-AzVM](/powershell/module/az.compute/new-azvm). Po spuštění dalšího příkazu budete vyzváni k zadání přihlašovacích údajů. Zadejte uživatelské jméno a heslo pro virtuální počítač:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "default" `
    -Name "myVm1" `
    -AsJob
```

`-AsJob`Možnost vytvoří virtuální počítač na pozadí. Můžete pokračovat k dalšímu kroku.

Když Azure začne vytvářet virtuální počítač na pozadí, získáte něco podobného:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

### <a name="create-the-second-vm"></a>Vytvoření druhého virtuálního počítače

Vytvořte druhý virtuální počítač pomocí tohoto příkazu:

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "default" `
  -Name "myVm2"
```

Budete muset vytvořit další uživatele a heslo. K vytvoření virtuálního počítače trvá Azure několik minut.

> [!IMPORTANT]
> Nepokračujte dalším krokem až do dokončení Azure.  Zjistíte, že je to hotové, když vrátí výstup do PowerShellu.

## <a name="connect-to-a-vm-from-the-internet"></a>Připojení k virtuálnímu počítači z internetu

K vrácení veřejné IP adresy virtuálního počítače použijte [příkaz Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) . Tento příklad vrátí veřejnou IP adresu virtuálního počítače *myVm1* :

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Otevřete příkazový řádek na místním počítači. Spusťte příkaz `mstsc`. Nahraďte `<publicIpAddress>` veřejnou IP adresou vrácenou z posledního kroku:

> [!NOTE]
> Pokud jste tyto příkazy spustili z příkazového řádku PowerShellu v místním počítači a používáte modul AZ PowerShell verze 1,0 nebo novější, můžete v tomto rozhraní pokračovat.

```cmd
mstsc /v:<publicIpAddress>
```
1. Pokud se zobrazí výzva, vyberte **Připojit**.

1. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.

    > [!NOTE]
    > Možná budete muset vybrat **Další volby**  >  **použít jiný účet**a zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače.

1. Vyberte **OK**.

1. Může se zobrazit upozornění certifikátu. Pokud ano, vyberte **Ano** nebo **pokračovat**.

## <a name="communicate-between-vms"></a>Komunikace mezi virtuálními počítači

1. Ve vzdálené ploše *myVm1*otevřete PowerShell.

1. Zadejte `ping myVm2`.

    Jako tuto akci získáte něco podobného:

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

    Tento příkaz se nezdařil, protože používá protokol ICMP (Internet Control Message Protocol). Ve výchozím nastavení se protokol ICMP nepovoluje přes bránu Windows Firewall.

1. Pokud chcete, aby se v pozdějším kroku *myVm2* příkaz Test *myVm1* , zadejte tento příkaz:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Tento příkaz umožňuje příchozí připojení ICMP přes bránu Windows Firewall.

1. Ukončete připojení ke vzdálené ploše virtuálního počítače *myVm1*.

1. Opakujte kroky v části [připojení k virtuálnímu počítači z Internetu](#connect-to-a-vm-from-the-internet). Tentokrát se připojte k *myVm2*.

1. Na příkazovém řádku na virtuálním počítači *myVm2* zadejte `ping myvm1`.

    Jako tuto akci získáte něco podobného:

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

Až skončíte s virtuální sítí a virtuálními počítači, odeberte skupinu prostředků a všechny prostředky, které obsahuje, pomocí [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) :

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili výchozí virtuální síť a dva virtuální počítače. Připojili jste se k jednomu virtuálnímu počítači z Internetu a komunikovali soukromě mezi těmito dvěma virtuálními počítači.
Azure umožňuje neomezenou soukromou komunikaci mezi virtuálními počítači. Azure ve výchozím nastavení umožňuje pouze příchozí připojení vzdálené plochy k virtuálním počítačům s Windows z Internetu. V dalším článku se dozvíte víc o konfiguraci různých typů síťových komunikací virtuálních počítačů:
> [!div class="nextstepaction"]
> [Filtrování síťového provozu](tutorial-filter-network-traffic.md)
