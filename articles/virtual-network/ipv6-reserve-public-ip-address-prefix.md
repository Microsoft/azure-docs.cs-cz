---
title: Rezervace veřejných adres IPv6 a rozsahů adres ve virtuální síti Azure
titlesuffix: Azure Virtual Network
description: Přečtěte si, jak rezervovat veřejné adresy IPv6 a rozsahy adres ve virtuální síti Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/15/2019
ms.author: kumud
ms.openlocfilehash: 76d1ba2717ac3c8ac8e86687ef1754a8790f3e4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72595075"
---
# <a name="reserve-public-ipv6-address-prefix"></a>Rezervovat veřejnou předponu adresy IPv6
IPv6 pro virtuální síť Azure (VNet) umožňuje hostovat aplikace v Azure pomocí připojení IPv6 a IPv4 v rámci virtuální sítě i do a z Internetu. Kromě rezervace jednotlivých adres IPv6 můžete pro své použití rezervovat souvislé rozsahy adres Azure IPv6 (označované jako IP prefix). Tento článek popisuje, jak vytvořit IPv6 veřejné IP adresy a rozsahy adres pomocí Azure PowerShell a CLI.

> [!Important]
> IPv6 pro virtuální síť Azure je aktuálně ve verzi Public Preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované nebo můžou mít omezené možnosti. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-single-reserved-ipv6-public-ip"></a>Vytvoření jedné vyhrazené veřejné IP adresy IPv6

### <a name="using-azure-powershell"></a>Použití Azure Powershell

Můžete vytvořit jednu vyhrazenou (statickou) veřejnou IP adresu IPv6 pomocí Azure PowerShellu s [new-azPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) takto:

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

 Můžete vytvořit jednu vyhrazenou (statickou) veřejnou IP adresu IPv6 Veřejné IP adresy Azure CLI s [az sítě public-ip vytvořit](/cli/azure/network/public-ip) takto:
  
```azurecli
 az network public-ip create \
 --name dsPublicIP_v6 \
 --resource-group UpgradeInPlace_CLI_RG1 \
 --location WestUS \
 --sku Standard  \
 --allocation-method static  \
 --version IPv6
```

## <a name="create-a-reserved-ipv6-prefix-range"></a>Vytvoření vyhrazené předpony IPv6 (rozsah)

Chcete-li rezervovat předponu IPv6, přidejte do stejného příkazu, který se používá k vytváření předpon IPv4, rodinu IP adres protokolu IPv6. Následující příkazy vytvoří předponu o velikosti /125 ( 8 adres IPv6).  

### <a name="using-azure-powershell"></a>Použití Azure Powershell

Veřejnou adresu IPv6 můžete vytvořit pomocí azure cli s [az sítě public-ip vytvořit](/powershell/module/az.network/new-azpublicipprefix) takto:
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

Veřejnou adresu IPv6 můžete pomocí azure cli vytvořit následujícím způsobem:

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

 Statickou veřejnou IP adresu IPv6 vytvoříte `-PublicIpPrefix` z vyhrazené předpony přidáním argumentu při vytváření veřejné IP adresy pomocí Azure PowerShellu. Následující příklad předpokládá, že předpona byla vytvořena a uložena v proměnné Prostředí PowerShell s názvem: *$myOwnIPv6Prefix*.

```azurepowershell:  
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
- Další informace o [předponě adresy IPv6](ipv6-public-ip-address-prefix.md).
- Další informace o [adresách IPv6](ipv6-overview.md).
