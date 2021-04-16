---
title: Rychlý Start – vytvoření privátní zóny DNS Azure pomocí Azure PowerShell
description: V tomto rychlém startu se dozvíte, jak vytvořit a spravovat první privátní zónu a záznam DNS pomocí Azure PowerShell.
services: dns
author: rohinkoul
ms.author: rohink
ms.date: 10/20/2020
ms.topic: quickstart
ms.service: dns
ms.custom:
- mode-api
ms.openlocfilehash: 321cd9c9f85b7f1dc2540992d9db0aaefa1b9b62
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535475"
---
# <a name="quickstart-create-an-azure-private-dns-zone-using-azure-powershell"></a>Rychlý Start: Vytvoření privátní zóny DNS Azure pomocí Azure PowerShell

Tento článek vás provede kroky k vytvoření první privátní zóny a záznamu DNS pomocí Azure PowerShellu.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

K hostování záznamů DNS pro konkrétní doménu se používá zóna DNS. Pokud chcete začít hostovat svou doménu v DNS Azure, musíte vytvořit zónu DNS pro daný název domény. Všechny záznamy DNS pro vaši doménu se pak vytvoří v této zóně DNS. Když chcete publikovat privátní zónu DNS do vaší virtuální sítě, zadáte seznam virtuálních sítí, které mají povolené překládání záznamů v rámci zóny.  Ty se nazývají *propojené* virtuální sítě. Pokud je povolena automatická registrace, Azure DNS aktualizuje také záznamy zón při každém vytvoření virtuálního počítače, změní jeho IP adresu nebo se odstraní.

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

Pokud budete chtít, můžete tento rychlý Start dokončit pomocí [Azure CLI](private-dns-getstarted-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

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

Pokud chcete vytvořit zónu jenom pro překlad IP adres (bez automatické registrace názvu hostitele), můžete `-EnableRegistration` parametr vynechat.

### <a name="list-dns-private-zones"></a>Výpis privátních zón DNS

Vynecháním názvu zóny v rutině `Get-AzPrivateDnsZone` můžete zobrazit výčet všech zón ve skupině prostředků. Tato operace vrátí pole objektů zón.

```azurepowershell
$zones = Get-AzPrivateDnsZone -ResourceGroupName MyAzureResourceGroup
$zones
```

Vynecháním názvu zóny i názvu skupiny prostředků v rutině `Get-AzPrivateDnsZone` můžete zobrazit výčet všech zón v předplatném Azure.

```azurepowershell
$zones = Get-AzPrivateDnsZone
$zones
```

## <a name="create-the-test-virtual-machines"></a>Vytvoření testovacích virtuálních počítačů

Teď vytvořte dva virtuální počítače, abyste mohli privátní zónu DNS otestovat:

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

Dokončení může několik minut trvat.

## <a name="create-an-additional-dns-record"></a>Vytvoření dalšího záznamu DNS

Sady záznamů vytvoříte pomocí rutiny `New-AzPrivateDnsRecordSet`. Následující příklad vytvoří záznam s relativní názvem **DB** v zóně DNS **Private.contoso.com** ve skupině prostředků **MyAzureResourceGroup**. Plně kvalifikovaný název sady záznamů je **DB.Private.contoso.com**. Typ záznamu je A, IP adresa je 10.2.0.4 a hodnota TTL je 3600 sekund.

```azurepowershell
New-AzPrivateDnsRecordSet -Name db -RecordType A -ZoneName private.contoso.com `
   -ResourceGroupName MyAzureResourceGroup -Ttl 3600 `
   -PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address "10.2.0.4")
```

### <a name="view-dns-records"></a>Zobrazení záznamů DNS

K výpisu záznamů DNS ve vaší zóně použijte následující příkaz:

```azurepowershell
Get-AzPrivateDnsRecordSet -ZoneName private.contoso.com -ResourceGroupName MyAzureResourceGroup
```

## <a name="test-the-private-zone"></a>Testování privátní zóny

Teď můžete testovat překlad IP adres pro privátní zónu **Private.contoso.com** .

### <a name="configure-vms-to-allow-inbound-icmp"></a>Konfigurace virtuálních počítačů pro povolení příchozích přenosů ICMP

Překlad adres můžete otestovat pomocí příkazu ping. Za tím účelem nakonfigurujte bránu firewall na obou virtuálních počítačích tak, aby povolovala příchozí pakety ICMP.

1. Připojte se k počítači myVM01 a otevřete okno Windows PowerShellu s oprávněními správce.
2. Spusťte následující příkaz:

   ```powershell
   New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
   ```

Totéž zopakujte pro virtuální počítač myVM02.

### <a name="ping-the-vms-by-name"></a>Odeslání příkazu ping na virtuální počítače podle názvu

1. Z příkazového řádku ve Windows PowerShellu virtuálního počítače myVM02 odešlete příkaz ping do virtuálního počítače myVM01 a použijte v něm automaticky zaregistrovaný název hostitele:

   ```
   ping myVM01.private.contoso.com
   ```

   Zobrazený výstup by měl vypadat nějak takto:

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

2. Teď odešlete příkaz ping na název **db**, který jste předtím vytvořili:

   ```
   ping db.private.contoso.com
   ```

   Zobrazený výstup by měl vypadat nějak takto:

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

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků **MyAzureResourceGroup** a odstraňte prostředky vytvořené v tomto článku.

```azurepowershell
Remove-AzResourceGroup -Name MyAzureResourceGroup
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Scénáře privátních zón Azure DNS](private-dns-scenarios.md)
