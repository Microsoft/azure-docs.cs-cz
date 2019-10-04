---
title: Rychlý Start – vytvoření privátní zóny DNS Azure pomocí Azure CLI
description: V tomto rychlém startu vytvoříte a otestujete privátní zónu a záznam DNS v Azure DNS. Toto je podrobný Průvodce vytvořením a správou první privátní zóny DNS a záznamu pomocí Azure CLI.
services: dns
author: vhorne
ms.service: dns
ms.topic: quickstart
ms.date: 10/05/2019
ms.author: victorh
ms.openlocfilehash: 6f5f2263736e1317ca40e6c27a909be2aa106270
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71960432"
---
# <a name="quickstart-create-an-azure-private-dns-zone-using-the-azure-cli"></a>Rychlý Start: Vytvoření privátní zóny DNS Azure pomocí Azure CLI

Tento rychlý Start vás provede kroky k vytvoření první privátní zóny DNS a záznamu pomocí Azure CLI.

Zóna DNS se používá k hostování záznamů DNS pro konkrétní doménu. Pokud chcete začít hostovat vaši doménu v Azure DNS, musíte pro tento název domény vytvořit zónu DNS. Každý záznam DNS pro vaši doménu se pak vytvoří v této zóně DNS. Pokud chcete publikovat privátní zónu DNS do vaší virtuální sítě, zadáte seznam virtuálních sítí, které mají povolené řešení záznamů v rámci zóny.  Ty se nazývají *propojené* virtuální sítě. Pokud je povolena automatická registrace, Azure DNS aktualizuje také záznamy zón při každém vytvoření virtuálního počítače, změní jeho IP adresu nebo se odstraní.

V tomto rychlém startu se dozvíte, jak:

> [!div class="checklist"]
> * Vytvoření privátní zóny DNS
> * Vytvoření testovacích virtuálních počítačů
> * Vytvoření dalšího záznamu DNS
> * Testování privátní zóny

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

Pokud budete chtít, můžete tento rychlý Start dokončit pomocí [Azure PowerShell](private-dns-getstarted-powershell.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resource-group"></a>Vytvoření skupiny prostředků

Nejdřív vytvořte skupinu prostředků, která bude obsahovat zónu DNS: 

```azurecli
az group create --name MyAzureResourceGroup --location "East US"
```

## <a name="create-a-private-dns-zone"></a>Vytvoření privátní zóny DNS

Následující příklad vytvoří virtuální síť s názvem **myAzureVNet**. Potom ve skupině prostředků **MyAzureResourceGroup** vytvoří zónu DNS s názvem **Private.contoso.com** , propojí zónu DNS s virtuální sítí **MyAzureVnet** a povolí automatickou registraci.

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

Pokud chcete vytvořit zónu jenom pro překlad IP adres (bez automatické registrace názvu hostitele), můžete použít parametr `-e false`.

### <a name="list-dns-private-zones"></a>Výpis privátních zón DNS

Chcete-li vytvořit výčet zón DNS, použijte `az network private-dns zone list`. Nápovědu najdete v tématu `az network dns zone list --help`.

Zadáním skupiny prostředků vypíšete jenom zóny v rámci skupiny prostředků:

```azurecli
az network private-dns zone list \
  -g MyAzureResourceGroup
```

Vynechání skupiny prostředků vypíše seznam všech zón v předplatném:

```azurecli
az network private-dns zone list 
```

## <a name="create-the-test-virtual-machines"></a>Vytvoření testovacích virtuálních počítačů

Teď vytvořte dva virtuální počítače, abyste mohli testovat svoji privátní zónu DNS:

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

Dokončení této akce bude trvat několik minut.

## <a name="create-an-additional-dns-record"></a>Vytvoření dalšího záznamu DNS

K vytvoření záznamu DNS použijte příkaz `az network private-dns record-set [record type] add-record`. Nápovědu k přidávání záznamů najdete například v tématu `az network private-dns record-set A add-record --help`.

 Následující příklad vytvoří záznam s relativní názvem **DB** v zóně DNS **Private.contoso.com**ve skupině prostředků **MyAzureResourceGroup**. Plně kvalifikovaný název sady záznamů je **DB.Private.contoso.com**. Typ záznamu je A s IP adresou "10.2.0.4".

```azurecli
az network private-dns record-set a add-record \
  -g MyAzureResourceGroup \
  -z private.contoso.com \
  -n db \
  -a 10.2.0.4
```

### <a name="view-dns-records"></a>Zobrazení záznamů DNS

Chcete-li zobrazit seznam záznamů DNS v zóně, spusťte příkaz:

```azurecli
az network private-dns record-set list \
  -g MyAzureResourceGroup \
  -z private.contoso.com
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
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="delete-all-resources"></a>Odstranit všechny prostředky

Pokud už je nepotřebujete, odstraňte skupinu prostředků **MyAzureResourceGroup** a odstraňte prostředky vytvořené v rámci tohoto rychlého startu.

```azurecli
az group delete --name MyAzureResourceGroup
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Azure DNS Private Zones scénáře](private-dns-scenarios.md)

