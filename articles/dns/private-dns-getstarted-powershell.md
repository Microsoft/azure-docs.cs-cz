---
title: Kurz – Vytvoření privátní zóny Azure DNS pomocí Azure PowerShellu
description: V tomto kurzu vytvoříte a otestujete privátní zónu a záznam DNS v Azure DNS. Tento podrobný průvodce vám pomůže vytvořit a spravovat první privátní zónu a záznam DNS pomocí Azure PowerShellu.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 7/24/2018
ms.author: victorh
ms.openlocfilehash: 872227e0521bd54e6bf7fdbe3626dfca34170863
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2018
ms.locfileid: "39257721"
---
# <a name="tutorial-create-an-azure-dns-private-zone-using-azure-powershell"></a>Kurz: Vytvoření privátní zóny Azure DNS pomocí Azure PowerShellu

Tento kurz vás provede kroky k vytvoření první privátní zóny a záznamu DNS pomocí Azure PowerShellu.

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

K hostování záznamů DNS v určité doméně se používá zóna DNS. Pokud chcete začít hostovat svou doménu v DNS Azure, musíte vytvořit zónu DNS pro daný název domény. Všechny záznamy DNS pro vaši doménu se pak vytvoří v této zóně DNS. Když chcete publikovat privátní zónu DNS do virtuální sítě, zadáte seznam virtuálních sítí, které mají povoleno překládat záznamy v rámci této zóny.  Ty označujeme jako *virtuální sítě pro překlad*. Můžete také zadat virtuální síť, pro kterou bude Azure DNS uchovávat záznamy názvů hostitelů při každém vytvoření virtuálního počítače, změně jeho IP adresy nebo jeho odstranění.  Tu označujeme jako *registrační virtuální síť*.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření privátní zóny DNS
> * Vytvoření testovacích virtuálních počítačů
> * Vytvoření dalšího záznamu DNS
> * Testování privátní zóny

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

Pokud chcete, můžete tento kurz dokončit pomocí [rozhraní příkazového řádku Azure](private-dns-getstarted-cli.md).

<!--- ## Get the Preview PowerShell modules
These instructions assume you have already installed and signed in to Azure PowerShell, including ensuring you have the required modules for the Private Zone feature. -->

<!---[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)] -->

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

## <a name="create-the-resource-group"></a>Vytvoření skupiny prostředků

Nejdřív vytvořte skupinu prostředků, která bude obsahovat zónu DNS: 

```azurepowershell
New-AzureRMResourceGroup -name MyAzureResourceGroup -location "eastus"
```

## <a name="create-a-dns-private-zone"></a>Vytvoření privátní zóny DNS

Zónu DNS vytvoříte pomocí rutiny `New-AzureRmDnsZone` s parametrem **ZoneType** s hodnotou *Private*. Následující příklad vytvoří zónu DNS **contoso.local** ve skupině prostředků **MyAzureResourceGroup** a zpřístupní tuto zónu DNS pro virtuální síť **MyAzureVnet**.

Pokud parametr **ZoneType** vynecháte, zóna se vytvoří jako veřejná zóna. Pokud potřebujete vytvořit privátní zónu, je tento parametr povinný. 

```azurepowershell
$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name backendSubnet -AddressPrefix "10.2.0.0/24"
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName MyAzureResourceGroup `
  -Location eastus `
  -Name myAzureVNet `
  -AddressPrefix 10.2.0.0/16 `
  -Subnet $backendSubnet

New-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyAzureResourceGroup `
   -ZoneType Private `
   -RegistrationVirtualNetworkId @($vnet.Id)
```

Kdybyste chtěli vytvořit zónu jenom pro překlad adres (bez automatického vytváření názvu hostitele), mohli byste místo parametru *RegistrationVirtualNetworkId* použít parametr *ResolutionVirtualNetworkId*.

> [!NOTE]
> Automaticky vytvořené záznamy názvu hostitele se vám nezobrazí. Později ale provedete testování, abyste si ověřili, že existují.

### <a name="list-dns-private-zones"></a>Výpis privátních zón DNS

Vynecháním názvu zóny v rutině `Get-AzureRmDnsZone` můžete zobrazit výčet všech zón ve skupině prostředků. Tato operace vrátí pole objektů zón.

```azurepowershell
Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup
```

Vynecháním názvu zóny i názvu skupiny prostředků v rutině `Get-AzureRmDnsZone` můžete zobrazit výčet všech zón v předplatném Azure.

```azurepowershell
Get-AzureRmDnsZone
```

## <a name="create-the-test-virtual-machines"></a>Vytvoření testovacích virtuálních počítačů

Teď vytvořte dva virtuální počítače, abyste mohli privátní zónu DNS otestovat:

```azurepowershell
New-AzureRmVm `
    -ResourceGroupName "myAzureResourceGroup" `
    -Name "myVM01" `
    -Location "East US" `
    -subnetname backendSubnet `
    -VirtualNetworkName "myAzureVnet" `
    -addressprefix 10.2.0.0/24 `
    -OpenPorts 3389

New-AzureRmVm `
    -ResourceGroupName "myAzureResourceGroup" `
    -Name "myVM02" `
    -Location "East US" `
    -subnetname backendSubnet `
    -VirtualNetworkName "myAzureVnet" `
    -addressprefix 10.2.0.0/24 `
    -OpenPorts 3389
```

Dokončení tohoto procesu může několik minut trvat.

## <a name="create-an-additional-dns-record"></a>Vytvoření dalšího záznamu DNS

Sady záznamů vytvoříte pomocí rutiny `New-AzureRmDnsRecordSet`. Následující příklad vytvoří záznam s relativním názvem **db** v zóně DNS **contoso.local** ve skupině prostředků **MyAzureResourceGroup**. Plně kvalifikovaný název sady záznamů je **db.contoso.local**. Typ záznamu je A, IP adresa je 10.2.0.4 a hodnota TTL je 3600 sekund.

```azurepowershell
New-AzureRmDnsRecordSet -Name db -RecordType A -ZoneName contoso.local `
   -ResourceGroupName MyAzureResourceGroup -Ttl 3600 `
   -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "10.2.0.4")
```

### <a name="view-dns-records"></a>Zobrazení záznamů DNS

K výpisu záznamů DNS ve vaší zóně použijte následující příkaz:

```azurepowershell
Get-AzureRmDnsRecordSet -ZoneName contoso.local -ResourceGroupName MyAzureResourceGroup
```
Nezapomeňte, že automaticky se vytvořené záznamy A pro vaše dva testovací virtuální počítače se nezobrazí.

## <a name="test-the-private-zone"></a>Testování privátní zóny

Teď můžete otestovat překlad adres pro privátní zónu **contoso.local**.

### <a name="configure-vms-to-allow-inbound-icmp"></a>Konfigurace virtuálních počítačů pro povolení příchozích přenosů ICMP

Překlad adres můžete otestovat pomocí příkazu ping. Za tím účelem nakonfigurujte bránu firewall na obou virtuálních počítačích tak, aby povolovala příchozí pakety ICMP.

1. Připojte se k počítači myVM01 a otevřete okno Windows PowerShellu s oprávněními správce.
2. Spusťte následující příkaz:

   ```powershell
   New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
   ```

Totéž zopakujte pro virtuální počítač myVM02.

### <a name="ping-the-vms-by-name"></a>Odeslání příkazu ping na virtuální počítače podle názvu

1. Z příkazového řádku ve Windows PowerShellu virtuálního počítače myVM02 odešlete příkaz ping do virtuálního počítače myVM01 a použijte v něm automaticky zaregistrovaný název hostitele:
   ```
   ping myVM01.contoso.local
   ```
   Zobrazený výstup by měl vypadat zhruba takto:
   ```
   PS C:\> ping myvm01.contoso.local

   Pinging myvm01.contoso.local [10.2.0.4] with 32 bytes of data:
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
   ping db.contoso.local
   ```
   Zobrazený výstup by měl vypadat zhruba takto:
   ```
   PS C:\> ping db.contoso.local

   Pinging db.contoso.local [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="delete-all-resources"></a>Odstranění všech prostředků

Pokud už nejsou potřeba, můžete všechny prostředky vytvořené v rámci tohoto kurzu odstranit odstraněním skupiny prostředků **MyAzureResourceGroup**.

```azurepowershell
Remove-AzureRMResourceGroup -Name MyAzureResourceGroup
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nasadili privátní zónu DNS, vytvořili záznam DNS nasadila a zónu otestovali.
Teď se můžete o privátních zónách DNS dozvědět podrobnější informace.

> [!div class="nextstepaction"]
> [Použití Azure DNS pro privátní domény](private-dns-overview.md)




