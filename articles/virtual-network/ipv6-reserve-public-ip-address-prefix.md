---
title: Rezervace veřejných adres IPv6 a rozsahů adres ve virtuální síti Azure
titlesuffix: Azure Virtual Network
description: Přečtěte si, jak rezervovat veřejné adresy IPv6 a rozsahy adres ve službě Azure Virtual Network.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: ac79e1eb5c4f7448dc17804cd8aac3cba582497e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88509939"
---
# <a name="reserve-public-ipv6-address-prefix"></a>Rezervovat předponu veřejné adresy IPv6
Protokol IPv6 pro Azure Virtual Network (VNet) umožňuje hostovat aplikace v Azure s připojením IPv6 a IPv4 jak v rámci virtuální sítě, tak i z Internetu. Kromě rezervace individuálních adres IPv6 můžete pro své použití rezervovat souvislé rozsahy adres IPv6 pro Azure (označované jako předpona IP adresy). V tomto článku se dozvíte, jak vytvořit veřejné IP adresy a rozsahy adres IPv6 pomocí Azure PowerShell a CLI.


## <a name="create-a-single-reserved-ipv6-public-ip"></a>Vytvoření jediné rezervované veřejné IP adresy IPv6

### <a name="using-azure-powershell"></a>Použití Azure Powershell

Jednu rezervovanou (statickou) veřejnou IP adresu IPv6 můžete vytvořit pomocí Azure PowerShell pomocí příkazu [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) následujícím způsobem:

```azurepowershell
 $myOwnIPv6Address = New-AzPublicIpAddress `
 -name PIPv6_WestUS `
 -ResourceGroup MyRG `
 -Location "West US" `
 -Sku Standard `
 -allocationMethod static `
 -IpAddressVersion IPv6
 ```

### <a name="using-azure-cli"></a>Použití Azure CLI

 Pomocí příkazu AZ Network Public-IP můžete vytvořit jednu rezervovanou (statickou) veřejnou IP adresu typu Azure CLI pomocí příkazu [AZ Network Public-IP Create](/cli/azure/network/public-ip) takto:

```azurecli
 az network public-ip create \
 --name dsPublicIP_v6 \
 --resource-group UpgradeInPlace_CLI_RG1 \
 --location WestUS \
 --sku Standard  \
 --allocation-method static  \
 --version IPv6
```

## <a name="create-a-reserved-ipv6-prefix-range"></a>Vytvoření rezervované předpony IPv6 (rozsah)

K rezervaci předpony IPv6 přidejte rodinu IP adres IPv6 do stejného příkazu, který se používá pro vytváření předpon IPv4. Následující příkazy vytvoří prefix velikosti/125 (8 IPv6 adres).

### <a name="using-azure-powershell"></a>Použití Azure Powershell

Veřejnou adresu IPv6 můžete vytvořit pomocí Azure CLI pomocí příkazu [AZ Network Public-IP Create](/powershell/module/az.network/new-azpublicipprefix) takto:
```azurepowershell
 $myOwnIPv6Prefix = New-AzPublicIpPrefix `
 -name IPv6PrefixWestUS `
 -ResourceGroupName MyRG `
 -Location "West US" `
 -Sku Standard `
 -IpAddressVersion IPv6 `
 -PrefixLength 125
```

### <a name="using-azure-cli"></a>Použití Azure CLI

Veřejnou adresu IPv6 můžete vytvořit pomocí Azure CLI následujícím způsobem:

```azurecli
az network public-ip prefix create \
--name IPv6PrefixWestUS \
--resource-group MyRG \
--location WestUS \
--version IPv6 \
--length 125
```

## <a name="allocate-a-public-ip-address-from-a-reserved-ipv6-prefix"></a>Přidělení veřejné IP adresy z rezervované předpony IPv6

### <a name="using-azure-powershell"></a>Použití Azure Powershell

 Můžete vytvořit statickou veřejnou IP adresu IPv6 z rezervované předpony přidáním `-PublicIpPrefix` argumentu při vytváření veřejné IP adresy pomocí Azure PowerShell. Následující příklad předpokládá, že byla vytvořena předpona a uložena do proměnné prostředí PowerShell s názvem: *$MyOwnIPv 6prefix*.

```azurepowershell
 $MyIPv6PublicIPFromMyReservedPrefix = New-AzPublicIpAddress \
 -name PIPv6_fromPrefix `
 -ResourceGroup DsStdLb04 `
 -Location "West Central US" `
 -Sku Standard `
 -allocationMethod static `
 -IpAddressVersion IPv6 `
 -PublicIpPrefix $myOwnIPv6Prefix
```

### <a name="using-azure-cli"></a>Použití Azure CLI

Následující příklad předpokládá, že předpona byla vytvořena a uložena v proměnné CLI s názvem: *IPv6PrefixWestUS*.

```azurecli
az network public-ip create \
--name dsPublicIP_v6 \
--resource-group UpgradeInPlace_CLI_RG1 \
--location WestUS \
--sku Standard \
--allocation-method static \
--version IPv6 \
--public-ip-prefix  IPv6PrefixWestUS
```

## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [předponě adresy IPv6](ipv6-public-ip-address-prefix.md).
- Přečtěte si další informace o [adresách IPv6](ipv6-overview.md).
