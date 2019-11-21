---
title: Quickstart - Create an Azure private DNS zone using the Azure CLI
description: In this quickstart, you create and test a private DNS zone and record in Azure DNS. Pomocí tohoto podrobného průvodce můžete vytvořit a spravovat první privátní zónu a záznam DNS pomocí Azure CLI.
services: dns
author: asudbring
ms.service: dns
ms.topic: quickstart
ms.date: 10/05/2019
ms.author: allensu
ms.openlocfilehash: 3c580eebd19eb2f2028768059140731fa24f4664
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74210926"
---
# <a name="quickstart-create-an-azure-private-dns-zone-using-the-azure-cli"></a>Quickstart: Create an Azure private DNS zone using the Azure CLI

This quickstart walks you through the steps to create your first private DNS zone and record using the Azure CLI.

Zóna DNS se používá k hostování záznamů DNS pro konkrétní doménu. Pokud chcete začít hostovat svou doménu v DNS Azure, musíte vytvořit zónu DNS pro daný název domény. Všechny záznamy DNS pro vaši doménu se pak vytvoří v této zóně DNS. Když chcete publikovat privátní zónu DNS do virtuální sítě, zadáte seznam virtuálních sítí, které mají povoleno překládat záznamy v rámci této zóny.  These are called *linked* virtual networks. When autoregistration is enabled, Azure DNS also updates the zone records whenever a virtual machine is created, changes its' IP address, or is deleted.

V tomto rychlém startu se naučíte:

> [!div class="checklist"]
> * Create a private DNS zone
> * Vytvoření testovacích virtuálních počítačů
> * Vytvoření dalšího záznamu DNS
> * Testování privátní zóny

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

If you prefer, you can complete this quickstart using [Azure PowerShell](private-dns-getstarted-powershell.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resource-group"></a>Vytvoření skupiny prostředků

Nejdřív vytvořte skupinu prostředků, která bude obsahovat zónu DNS: 

```azurecli
az group create --name MyAzureResourceGroup --location "East US"
```

## <a name="create-a-private-dns-zone"></a>Create a private DNS zone

The following example creates a virtual network named **myAzureVNet**. Then it creates a DNS zone named **private.contoso.com** in the **MyAzureResourceGroup** resource group, links the DNS zone to the **MyAzureVnet** virtual network, and enables automatic registration.

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

If you want to create a zone just for name resolution (no automatic hostname registration), you could use the `-e false` parameter.

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

 The following example creates a record with the relative name **db** in the DNS Zone **private.contoso.com**, in resource group **MyAzureResourceGroup**. The fully qualified name of the record set is **db.private.contoso.com**. Typ záznamu je A a IP adresa je 10.2.0.4.

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

Now you can test the name resolution for your **private.contoso.com** private zone.

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

When no longer needed, delete the **MyAzureResourceGroup** resource group to delete the resources created in this quickstart.

```azurecli
az group delete --name MyAzureResourceGroup
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Azure DNS Private Zones scenarios](private-dns-scenarios.md)

