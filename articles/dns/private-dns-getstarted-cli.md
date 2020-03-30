---
title: Úvodní příručka – vytvoření privátní zóny DNS Azure pomocí příkazového příkazového příkazu Azure
description: V tomto rychlém startu vytvoříte a otestujete privátní zónu DNS a záznam v Azure DNS. Pomocí tohoto podrobného průvodce můžete vytvořit a spravovat první privátní zónu a záznam DNS pomocí Azure CLI.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: quickstart
ms.date: 10/05/2019
ms.author: rohink
ms.openlocfilehash: 34a9858ffbec2360a347b8c765d53551fd2d9fc0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "76935426"
---
# <a name="quickstart-create-an-azure-private-dns-zone-using-the-azure-cli"></a>Úvodní příručka: Vytvoření privátní zóny DNS Azure pomocí azure cli

Tento rychlý start vás provede kroky k vytvoření první privátní zóny DNS a záznamu pomocí azure CLI.

K hostování záznamů DNS pro konkrétní doménu se používá zóna DNS. Pokud chcete začít hostovat svou doménu v DNS Azure, musíte vytvořit zónu DNS pro daný název domény. Všechny záznamy DNS pro vaši doménu se pak vytvoří v této zóně DNS. Když chcete publikovat privátní zónu DNS do virtuální sítě, zadáte seznam virtuálních sítí, které mají povoleno překládat záznamy v rámci této zóny.  Tyto sítě se nazývají *propojené* virtuální sítě. Pokud je povolena automatická registrace, Azure DNS také aktualizuje záznamy zóny při každém vytvoření virtuálního počítače, změní jeho IP adresu nebo se odstraní.

V tomto rychlém startu se naučíte:

> [!div class="checklist"]
> * Vytvoření privátní zóny DNS
> * Vytvoření testovacích virtuálních počítačů
> * Vytvoření dalšího záznamu DNS
> * Testování privátní zóny

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

Pokud chcete, můžete tento rychlý start dokončit pomocí [Azure PowerShellu](private-dns-getstarted-powershell.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resource-group"></a>Vytvoření skupiny prostředků

Nejdřív vytvořte skupinu prostředků, která bude obsahovat zónu DNS: 

```azurecli
az group create --name MyAzureResourceGroup --location "East US"
```

## <a name="create-a-private-dns-zone"></a>Vytvoření privátní zóny DNS

Následující příklad vytvoří virtuální síť s názvem **myAzureVNet**. Potom vytvoří zónu DNS s názvem **private.contoso.com** ve skupině prostředků **MyAzureResourceGroup,** propojí zónu DNS s virtuální sítí **MyAzureVnet** a povolí automatickou registraci.

```azurecli
az network vnet create \
  --name myAzureVNet \
  --resource-group MyAzureResourceGroup \
  --location eastus \
  --address-prefix 10.2.0.0/16 \
  --subnet-name backendSubnet \
  --subnet-prefixes 10.2.0.0/24

az network private-dns zone create -g MyAzureResourceGroup \
   -n private.contoso.com

az network private-dns link vnet create -g MyAzureResourceGroup -n MyDNSLink \
   -z private.contoso.com -v myAzureVNet -e true
```

Pokud chcete vytvořit zónu pouze pro překlad názvů (bez automatické `-e false` registrace názvu hostitele), můžete použít parametr.

### <a name="list-dns-private-zones"></a>Výpis privátních zón DNS

Pokud chcete zobrazit výčet zón DNS, použijte příkaz `az network private-dns zone list`. Nápovědu získáte příkazem `az network dns zone list --help`.

Zadáním skupiny prostředků můžete vypsat pouze zóny v rámci této skupiny prostředků:

```azurecli
az network private-dns zone list \
  -g MyAzureResourceGroup
```

Pokud skupinu prostředků vynecháte, vypíší se všechny zóny v předplatném:

```azurecli
az network private-dns zone list 
```

## <a name="create-the-test-virtual-machines"></a>Vytvoření testovacích virtuálních počítačů

Teď vytvořte dva virtuální počítače, abyste mohli privátní zónu DNS otestovat:

```azurecli
az vm create \
 -n myVM01 \
 --admin-username AzureAdmin \
 -g MyAzureResourceGroup \
 -l eastus \
 --subnet backendSubnet \
 --vnet-name myAzureVnet \
 --nsg NSG01 \
 --nsg-rule RDP \
 --image win2016datacenter

az vm create \
 -n myVM02 \
 --admin-username AzureAdmin \
 -g MyAzureResourceGroup \
 -l eastus \
 --subnet backendSubnet \
 --vnet-name myAzureVnet \
 --nsg NSG01 \
 --nsg-rule RDP \
 --image win2016datacenter
```

Dokončení tohoto procesu může několik minut trvat.

## <a name="create-an-additional-dns-record"></a>Vytvoření dalšího záznamu DNS

K vytvoření záznamu DNS použijte příkaz `az network private-dns record-set [record type] add-record`. Například nápovědu k přidání záznamů A získáte příkazem `az network private-dns record-set A add-record --help`.

 Následující příklad vytvoří záznam s relativním názvem **db** v **zóně**DNS private.contoso.com ve skupině prostředků **MyAzureResourceGroup**. Plně kvalifikovaný název sady záznamů je **db.private.contoso.com**. Typ záznamu je A a IP adresa je 10.2.0.4.

```azurecli
az network private-dns record-set a add-record \
  -g MyAzureResourceGroup \
  -z private.contoso.com \
  -n db \
  -a 10.2.0.4
```

### <a name="view-dns-records"></a>Zobrazení záznamů DNS

K výpisu záznamů DNS ve vaší zóně použijte následující příkaz:

```azurecli
az network private-dns record-set list \
  -g MyAzureResourceGroup \
  -z private.contoso.com
```

## <a name="test-the-private-zone"></a>Testování privátní zóny

Nyní můžete otestovat překlad názvů pro **private.contoso.com** soukromé zóny.

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

   Zobrazený výstup by měl vypadat zhruba takto:

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

   Zobrazený výstup by měl vypadat zhruba takto:

   ```
   PS C:\> ping db.private.contoso.com

   Pinging db.private.contoso.com [10.2.0.4] with 32 bytes of data:
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

Pokud už není potřeba, odstraňte skupinu prostředků **MyAzureResourceGroup** a odstraňte prostředky vytvořené v tomto rychlém startu.

```azurecli
az group delete --name MyAzureResourceGroup
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Scénáře pro Azure DNS Private Zones](private-dns-scenarios.md)

