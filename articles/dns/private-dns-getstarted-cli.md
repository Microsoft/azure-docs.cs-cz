---
title: Vytvoření privátní zóny Azure DNS pomocí Azure CLI
description: V tomto kurzu vytvoříte a otestujete privátní zónu a záznam DNS v Azure DNS. Pomocí tohoto podrobného průvodce můžete vytvořit a spravovat první privátní zónu a záznam DNS pomocí Azure CLI.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 7/25/2018
ms.author: victorh
ms.openlocfilehash: 5559e2fc9b9cce95bd7d5d02a64d134e5eaa03be
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56100611"
---
# <a name="create-an-azure-dns-private-zone-using-the-azure-cli"></a>Vytvoření privátní zóny Azure DNS pomocí Azure CLI

Tento kurz vás provede kroky k vytvoření první privátní zóny a záznamu DNS pomocí Azure CLI.

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

K hostování záznamů DNS v určité doméně se používá zóna DNS. Pokud chcete začít hostovat svou doménu v DNS Azure, musíte vytvořit zónu DNS pro daný název domény. Všechny záznamy DNS pro vaši doménu se pak vytvoří v této zóně DNS. Když chcete publikovat privátní zónu DNS do virtuální sítě, zadáte seznam virtuálních sítí, které mají povoleno překládat záznamy v rámci této zóny.  Ty označujeme jako *virtuální sítě pro překlad*. Můžete také zadat virtuální síť, pro kterou bude Azure DNS uchovávat záznamy názvů hostitelů při každém vytvoření virtuálního počítače, změně jeho IP adresy nebo jeho odstranění.  Tu označujeme jako *registrační virtuální síť*.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření privátní zóny DNS
> * Vytvoření testovacích virtuálních počítačů
> * Vytvoření dalšího záznamu DNS
> * Testování privátní zóny

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

Pokud chcete, můžete k dokončení tohoto kurzu použít [Azure PowerShell](private-dns-getstarted-powershell.md).


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resource-group"></a>Vytvoření skupiny prostředků

Nejdřív vytvořte skupinu prostředků, která bude obsahovat zónu DNS: 

```azurecli
az group create --name MyAzureResourceGroup --location "East US"
```

## <a name="create-a-dns-private-zone"></a>Vytvoření privátní zóny DNS

Zónu DNS vytvoříte pomocí příkazu `az network dns zone create` s parametrem **ZoneType** s hodnotou *Private*. Následující příklad vytvoří zónu DNS **contoso.local** ve skupině prostředků **MyAzureResourceGroup** a zpřístupní tuto zónu DNS pro virtuální síť **MyAzureVnet**.

Pokud parametr **ZoneType** vynecháte, zóna se vytvoří jako veřejná zóna. Pokud potřebujete vytvořit privátní zónu, je tento parametr povinný.

```azurecli
az network vnet create \
  --name myAzureVNet \
  --resource-group MyAzureResourceGroup \
  --location eastus \
  --address-prefix 10.2.0.0/16 \
  --subnet-name backendSubnet \
  --subnet-prefixes 10.2.0.0/24

az network dns zone create -g MyAzureResourceGroup \
   -n contoso.local \
  --zone-type Private \
  --registration-vnets myAzureVNet
```

Kdybyste chtěli vytvořit zónu jenom pro překlad adres (bez automatického vytváření názvu hostitele), mohli byste místo parametru *registration-vnets* použít parametr *resolution-vnets*.

> [!NOTE]
> Automaticky vytvořené záznamy názvu hostitele se vám nezobrazí. Později ale provedete testování, abyste si ověřili, že existují.

### <a name="list-dns-private-zones"></a>Výpis privátních zón DNS

Pokud chcete zobrazit výčet zón DNS, použijte příkaz `az network dns zone list`. Nápovědu získáte příkazem `az network dns zone list --help`.

Zadáním skupiny prostředků můžete vypsat pouze zóny v rámci této skupiny prostředků:

```azurecli
az network dns zone list \
  --resource-group MyAzureResourceGroup
```

Pokud skupinu prostředků vynecháte, vypíší se všechny zóny v předplatném:

```azurecli
az network dns zone list 
```

## <a name="create-the-test-virtual-machines"></a>Vytvoření testovacích virtuálních počítačů

Teď vytvořte dva virtuální počítače, abyste mohli privátní zónu DNS otestovat:

```azurecli
az vm create \
 -n myVM01 \
 --admin-username test-user \
 -g MyAzureResourceGroup \
 -l eastus \
 --subnet backendSubnet \
 --vnet-name myAzureVnet \
 --image win2016datacenter

az vm create \
 -n myVM02 \
 --admin-username test-user \
 -g MyAzureResourceGroup \
 -l eastus \
 --subnet backendSubnet \
 --vnet-name myAzureVnet \
 --image win2016datacenter
```

Dokončení tohoto procesu může několik minut trvat.

## <a name="create-an-additional-dns-record"></a>Vytvoření dalšího záznamu DNS

K vytvoření záznamu DNS použijte příkaz `az network dns record-set [record type] add-record`. Například nápovědu k přidání záznamů A získáte příkazem `azure network dns record-set A add-record --help`.

 Následující příklad vytvoří záznam s relativním názvem **db** v zóně DNS **contoso.local** ve skupině prostředků **MyAzureResourceGroup**. Plně kvalifikovaný název sady záznamů je **db.contoso.local**. Typ záznamu je A a IP adresa je 10.2.0.4.

```azurecli
az network dns record-set a add-record \
  -g MyAzureResourceGroup \
  -z contoso.local \
  -n db \
  -a 10.2.0.4
```

### <a name="view-dns-records"></a>Zobrazení záznamů DNS

K výpisu záznamů DNS ve vaší zóně použijte následující příkaz:

```azurecli
az network dns record-set list \
  -g MyAzureResourceGroup \
  -z contoso.local
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

```azurecli
az group delete --name MyAzureResourceGroup
```

## <a name="next-steps"></a>Další postup

V tomto kurzu jste nasadili privátní zónu DNS, vytvořili záznam DNS nasadila a zónu otestovali.
Teď se můžete o privátních zónách DNS dozvědět podrobnější informace.

> [!div class="nextstepaction"]
> [Použití Azure DNS pro privátní domény](private-dns-overview.md)
