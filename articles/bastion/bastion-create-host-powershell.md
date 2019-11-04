---
title: Vytvoření hostitele bastionu pomocí Azure PowerShellu | Microsoft Docs
description: V tomto článku se dozvíte, jak vytvořit hostitele Azure bastionu.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: cherylmc
ms.openlocfilehash: 5fea9c9a99250c8abe26f953b1c6632b561a6013
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518044"
---
# <a name="create-an-azure-bastion-host-using-azure-powershell"></a>Vytvoření hostitele Azure bastionu pomocí Azure PowerShell

V tomto článku se dozvíte, jak vytvořit hostitele Azure bastionu. Po zřízení služby Azure bastionu ve vaší virtuální síti bude bezproblémové prostředí RDP/SSH dostupné pro všechny vaše virtuální počítače ve stejné virtuální síti. Toto nasazení je na jednu virtuální síť, ne na předplatné/účet nebo virtuální počítač.

## <a name="before-you-begin"></a>Než začnete

Ověřte, že máte předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si aktivovat [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="createhost"></a>Vytvoření hostitele bastionu

Tato část vám pomůže vytvořit nový prostředek Azure bastionu pomocí Azure PowerShell.

1. Vytvořte virtuální síť a podsíť Azure bastionu. Podsíť Azure bastionu musíte vytvořit pomocí hodnoty Name **AzureBastionSubnet**. Tato hodnota umožňuje službě Azure zjistit, do které podsítě nasadit prostředky bastionu. To se liší od podsítě brány. Je nutné použít podsíť alespoň a/27 nebo větší podsíť (/27,/26 atd.). Vytvořte **AzureBastionSubnet** bez směrovacích tabulek nebo delegování. Pokud používáte skupiny zabezpečení sítě na **AzureBastionSubnet**, přečtěte si téma [práce s skupin zabezpečení sítě](bastion-nsg.md).

   ```azurepowershell-interactive
   $subnetName = "AzureBastionSubnet"
   $subnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
   $vnet = New-AzVirtualNetwork -Name "myVnet" -ResourceGroupName " myBastionRG " -Location "westeurope" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
   ```

2. Vytvořte veřejnou IP adresu pro Azure bastionu. Veřejná IP adresa je veřejná IP adresa, na které se bastionu prostředek k připojení RDP/SSH (přes port 443). Veřejná IP adresa musí být ve stejné oblasti jako prostředek bastionu, který vytváříte.

   ```azurepowershell-interactive
   $publicip = New-AzPublicIpAddress -ResourceGroupName "myBastionRG" -name "myPublicIP" -location "westeurope" -AllocationMethod Dynamic -Sku Standard
   ```

3. Vytvořte nový prostředek Azure bastionu v AzureBastionSubnet vaší virtuální sítě. Vytvoření a nasazení prostředku bastionu trvá přibližně 5 minut.

   ```azurepowershell-interactive
   $bastion = New-AzBastion -ResourceGroupName " myBastionRG " -Name "myBastion" -PublicIpAddress $publicip -VirtualNetwork $vnet
   ```

## <a name="next-steps"></a>Další kroky

Přečtěte si [Nejčastější dotazy k bastionu](bastion-faq.md).