---
title: Vytvoření virtuální sítě – rychlý Start – Azure PowerShell
titlesuffix: Azure Virtual Network
description: V tomto rychlém startu vytvoříte virtuální síť pomocí Azure Portal. Virtuální síť umožňuje prostředkům Azure komunikovat mezi sebou a s internetem.
author: KumudD
ms.service: virtual-network
ms.topic: quickstart
ms.date: 03/06/2021
ms.author: kumud
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7ee10327ab95a3e66e5592593ae72d6e5cd8d606
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060597"
---
# <a name="quickstart-create-a-virtual-network-using-powershell"></a>Rychlý start: Vytvoření virtuální sítě pomocí PowerShellu

Virtuální síť umožňuje prostředkům Azure, jako jsou virtuální počítače, komunikovat soukromě mezi sebou a s internetem. 

V tomto rychlém startu zjistíte, jak vytvořit virtuální síť. Po vytvoření virtuální sítě do ní nasadíte dva virtuální počítače. Pak se k virtuálním počítačům připojíte z Internetu a soukromě komunikujte přes virtuální síť.

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure PowerShell lokálně nainstalované nebo Azure Cloud Shell

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte použít modul Azure PowerShell verze 5.4.1 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps). Pokud používáte prostředí PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="create-a-resource-group-and-a-virtual-network"></a>Vytvoření skupiny prostředků a virtuální sítě

K dispozici je několik kroků, pomocí kterých si můžete projít, abyste získali nakonfigurovanou skupinu prostředků a virtuální síť.

### <a name="create-the-resource-group"></a>Vytvoření skupiny prostředků

Než budete moct vytvořit virtuální síť, musíte vytvořit skupinu prostředků, která bude hostovat virtuální síť. Vytvořte skupinu prostředků pomocí [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). Tento příklad vytvoří skupinu prostředků s názvem **CreateVNetQS-RG** v umístění **Eastus** :

```azurepowershell-interactive
$rg = @{
    Name = 'CreateVNetQS-rg'
    Location = 'EastUS'
}
New-AzResourceGroup @rg
```

### <a name="create-the-virtual-network"></a>Vytvoření virtuální sítě

Vytvořte virtuální síť pomocí [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Tento příklad vytvoří výchozí virtuální síť s názvem **myVNet** v umístění **EastUS** :

```azurepowershell-interactive
$vnet = @{
    Name = 'myVNet'
    ResourceGroupName = 'CreateVNetQS-rg'
    Location = 'EastUS'
    AddressPrefix = '10.0.0.0/16'    
}
$virtualNetwork = New-AzVirtualNetwork @vnet
```

### <a name="add-a-subnet"></a>Přidání podsítě

Azure nasadí prostředky do podsítě v rámci virtuální sítě, takže je potřeba vytvořit podsíť. Vytvořte konfiguraci podsítě s názvem **Default** pomocí [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig):

```azurepowershell-interactive
$subnet = @{
    Name = 'default'
    VirtualNetwork = $virtualNetwork
    AddressPrefix = '10.0.0.0/24'
}
$subnetConfig = Add-AzVirtualNetworkSubnetConfig @subnet
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
$vm1 = @{
    ResourceGroupName = 'CreateVNetQS-rg'
    Location = 'EastUS'
    Name = 'myVM1'
    VirtualNetworkName = 'myVNet'
    SubnetName = 'default'
}
New-AzVM @vm1 -AsJob
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
$vm2 = @{
    ResourceGroupName = 'CreateVNetQS-rg'
    Location = 'EastUS'
    Name = 'myVM2'
    VirtualNetworkName = 'myVNet'
    SubnetName = 'default'
}
New-AzVM @vm2
```

Budete muset vytvořit další uživatele a heslo. K vytvoření virtuálního počítače trvá Azure několik minut.

> [!IMPORTANT]
> Nepokračujte dalším krokem až do dokončení Azure.  Zjistíte, že je to hotové, když vrátí výstup do PowerShellu.

## <a name="connect-to-a-vm-from-the-internet"></a>Připojení k virtuálnímu počítači z internetu

K získání veřejné IP adresy virtuálního počítače použijte [příkaz Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress).

Tento příklad vrátí veřejnou IP adresu virtuálního počítače **myVm1** :

```azurepowershell-interactive
$ip = @{
    Name = 'myVM1'
    ResourceGroupName = 'CreateVNetQS-rg'
}
Get-AzPublicIpAddress @ip | select IpAddress
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
    > Možná budete muset vybrat **Další volby**  >  **použít jiný účet** a zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače.

1. Vyberte **OK**.

1. Může se zobrazit upozornění certifikátu. Pokud ano, vyberte **Ano** nebo **pokračovat**.

## <a name="communicate-between-vms"></a>Komunikace mezi virtuálními počítači

1. Ve vzdálené ploše **myVm1** otevřete PowerShell.

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

1. Pokud chcete, aby se v pozdějším kroku **myVm2** příkaz Test **myVm1** , zadejte tento příkaz:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Tento příkaz umožňuje příchozí připojení ICMP přes bránu Windows Firewall.

1. Ukončete připojení ke vzdálené ploše virtuálního počítače **myVm1**.

1. Opakujte kroky v části [připojení k virtuálnímu počítači z Internetu](#connect-to-a-vm-from-the-internet). Tentokrát se připojte k **myVm2**.

1. Na příkazovém řádku na virtuálním počítači **myVm2** zadejte `ping myvm1`.

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

    Obdržíte odpovědi z virtuálního počítače **myVm1**, protože jste v předchozím kroku povolili průchod protokolu ICMP bránou Windows Firewall na virtuálním počítači **myVm1**.

1. Ukončete připojení ke vzdálené ploše virtuálního počítače **myVm2**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až skončíte s virtuální sítí a virtuálními počítači, odeberte skupinu prostředků a všechny prostředky, které obsahuje, pomocí [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) :

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'CreateVNetQS-rg' -Force
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu: 

* Vytvořili jste výchozí virtuální síť a dva virtuální počítače. 
* Připojili jste se k jednomu virtuálnímu počítači z Internetu a komunikovali soukromě mezi těmito dvěma virtuálními počítači.

Privátní komunikace mezi virtuálními počítači není ve virtuální síti omezená. 

V dalším článku se dozvíte víc o konfiguraci různých typů síťových komunikací virtuálních počítačů:
> [!div class="nextstepaction"]
> [Filtrování provozu sítě](tutorial-filter-network-traffic.md)
