---
title: Rychlý Start – vytvoření privátní zóny DNS Azure pomocí Azure PowerShell
description: V tomto článku vytvoříte a otestujete privátní zónu a záznam DNS v Azure DNS. Toto je podrobný Průvodce vytvořením a správou první privátní zóny DNS a záznamu pomocí Azure PowerShell.
services: dns
author: vhorne
ms.service: dns
ms.topic: quickstart
ms.date: 10/05/2019
ms.author: victorh
ms.openlocfilehash: fb39042e53795057a3404ba1e8cb5903188966f7
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71960446"
---
# <a name="quickstart-create-an-azure-private-dns-zone-using-azure-powershell"></a>Rychlý Start: Vytvoření privátní zóny DNS Azure pomocí Azure PowerShell

Tento článek vás provede kroky k vytvoření první privátní zóny DNS a záznamu pomocí Azure PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Zóna DNS se používá k hostování záznamů DNS pro konkrétní doménu. Pokud chcete začít hostovat vaši doménu v Azure DNS, musíte pro tento název domény vytvořit zónu DNS. Každý záznam DNS pro vaši doménu se pak vytvoří v této zóně DNS. Pokud chcete publikovat privátní zónu DNS do vaší virtuální sítě, zadáte seznam virtuálních sítí, které mají povolené řešení záznamů v rámci zóny.  Ty se nazývají *propojené* virtuální sítě. Pokud je povolena automatická registrace, Azure DNS aktualizuje také záznamy zón při každém vytvoření virtuálního počítače, změní jeho IP adresu nebo se odstraní.

V tomto článku se dozvíte, jak:

> [!div class="checklist"]
> * Vytvoření privátní zóny DNS
> * Vytvoření testovacích virtuálních počítačů
> * Vytvoření dalšího záznamu DNS
> * Testování privátní zóny

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

Pokud budete chtít, můžete tento rychlý Start dokončit pomocí [Azure CLI](private-dns-getstarted-cli.md).

## <a name="create-the-resource-group"></a>Vytvoření skupiny prostředků

Nejdřív vytvořte skupinu prostředků, která bude obsahovat zónu DNS: 

```azurepowershell
New-AzResourceGroup -name MyAzureResourceGroup -location "eastus"
```

## <a name="create-a-private-dns-zone"></a>Vytvoření privátní zóny DNS

Zóna DNS se vytvoří pomocí rutiny `New-AzPrivateDnsZone`.

Následující příklad vytvoří virtuální síť s názvem **myAzureVNet**. Potom ve skupině prostředků **MyAzureResourceGroup** vytvoří zónu DNS s názvem **Private.contoso.com** , propojí zónu DNS s virtuální sítí **MyAzureVnet** a povolí automatickou registraci.

```azurepowershell
Install-Module -Name Az.PrivateDns -force

$backendSubnet = New-AzVirtualNetworkSubnetConfig -Name backendSubnet -AddressPrefix "10.2.0.0/24"
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName MyAzureResourceGroup `
  -Location eastus `
  -Name myAzureVNet `
  -AddressPrefix 10.2.0.0/16 `
  -Subnet $backendSubnet

$zone = New-AzPrivateDnsZone -Name private.contoso.com -ResourceGroupName MyAzureResourceGroup

$link = New-AzPrivateDnsVirtualNetworkLink -ZoneName private.contoso.com `
  -ResourceGroupName MyAzureResourceGroup -Name "mylink" `
  -VirtualNetworkId $vnet.id -EnableRegistration
```

Pokud chcete vytvořit zónu jenom pro překlad IP adres (bez automatické registrace názvu hostitele), můžete parametr `-EnableRegistration` vynechat.

### <a name="list-dns-private-zones"></a>Výpis privátních zón DNS

Vynecháte-li název zóny z `Get-AzPrivateDnsZone`, můžete vytvořit výčet všech zón ve skupině prostředků. Tato operace vrátí pole objektů zóny.

```azurepowershell
$zones = Get-AzPrivateDnsZone -ResourceGroupName MyAzureResourceGroup
$zones
```

Vynecháním názvu zóny a názvu skupiny prostředků z `Get-AzPrivateDnsZone` můžete vytvořit výčet všech zón v rámci předplatného Azure.

```azurepowershell
$zones = Get-AzPrivateDnsZone
$zones
```

## <a name="create-the-test-virtual-machines"></a>Vytvoření testovacích virtuálních počítačů

Teď vytvořte dva virtuální počítače, abyste mohli testovat svoji privátní zónu DNS:

```azurepowershell
New-AzVm `
    -ResourceGroupName "myAzureResourceGroup" `
    -Name "myVM01" `
    -Location "East US" `
    -subnetname backendSubnet `
    -VirtualNetworkName "myAzureVnet" `
    -addressprefix 10.2.0.0/24 `
    -OpenPorts 3389

New-AzVm `
    -ResourceGroupName "myAzureResourceGroup" `
    -Name "myVM02" `
    -Location "East US" `
    -subnetname backendSubnet `
    -VirtualNetworkName "myAzureVnet" `
    -addressprefix 10.2.0.0/24 `
    -OpenPorts 3389
```

Dokončení této akce bude trvat několik minut.

## <a name="create-an-additional-dns-record"></a>Vytvoření dalšího záznamu DNS

Sady záznamů vytvoříte pomocí rutiny `New-AzPrivateDnsRecordSet`. Následující příklad vytvoří záznam s relativní názvem **DB** v zóně DNS **Private.contoso.com**ve skupině prostředků **MyAzureResourceGroup**. Plně kvalifikovaný název sady záznamů je **DB.Private.contoso.com**. Typ záznamu je A, IP adresa je 10.2.0.4 a hodnota TTL je 3600 sekund.

```azurepowershell
New-AzPrivateDnsRecordSet -Name db -RecordType A -ZoneName private.contoso.com `
   -ResourceGroupName MyAzureResourceGroup -Ttl 3600 `
   -PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address "10.2.0.4")
```

### <a name="view-dns-records"></a>Zobrazení záznamů DNS

Chcete-li zobrazit seznam záznamů DNS v zóně, spusťte příkaz:

```azurepowershell
Get-AzPrivateDnsRecordSet -ZoneName private.contoso.com -ResourceGroupName MyAzureResourceGroup
```

## <a name="test-the-private-zone"></a>Testování privátní zóny

Teď můžete testovat překlad IP adres pro privátní zónu **Private.contoso.com** .

### <a name="configure-vms-to-allow-inbound-icmp"></a>Konfigurace virtuálních počítačů pro povolení příchozího protokolu ICMP

K otestování překladu názvů můžete použít příkaz příkazu. Proto nakonfigurujte bránu firewall na obou virtuálních počítačích tak, aby povolovala příchozí pakety protokolu ICMP.

1. Připojte se k myVM01 a otevřete okno Windows PowerShellu s oprávněními správce.
2. Spusťte následující příkaz:

   ```powershell
   New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
   ```

Opakujte pro myVM02.

### <a name="ping-the-vms-by-name"></a>Pomocí příkazů otestujete virtuální počítače podle názvu

1. Z příkazového řádku myVM02 prostředí Windows PowerShell otestujte pomocí automaticky registrovaného názvu hostitele příkaz myVM01.

   ```
   ping myVM01.private.contoso.com
   ```

   Měl by se zobrazit výstup, který vypadá nějak takto:

   ```
   PS C:\> ping myvm01.private.contoso.com

   Pinging myvm01.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time=1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 1ms, Average = 0ms
   PS C:\>
   ```

2. Nyní otestujte název **databáze** , který jste vytvořili dříve:

   ```
   ping db.private.contoso.com
   ```

   Měl by se zobrazit výstup, který vypadá nějak takto:

   ```
   PS C:\> ping db.private.contoso.com

   Pinging db.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milliseconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="delete-all-resources"></a>Odstranit všechny prostředky

Pokud už je nepotřebujete, odstraňte skupinu prostředků **MyAzureResourceGroup** a odstraňte prostředky vytvořené v tomto článku.

```azurepowershell
Remove-AzResourceGroup -Name MyAzureResourceGroup
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Azure DNS Private Zones scénáře](private-dns-scenarios.md)
