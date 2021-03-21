---
title: Vytvoření hostitele bastionu pomocí Azure PowerShell | Microsoft Docs
description: V tomto článku se dozvíte, jak vytvořit hostitele Azure bastionu.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 10/14/2020
ms.author: cherylmc
ms.openlocfilehash: ba2716613a0e950cbae5c65add410ac8a8b38955
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92077721"
---
# <a name="create-an-azure-bastion-host-using-azure-powershell"></a>Vytvoření hostitele služby Azure Bastion pomocí Azure PowerShellu

V tomto článku se dozvíte, jak vytvořit hostitele Azure bastionu pomocí prostředí PowerShell. Jakmile ve své virtuální síti zřídíte službu Azure bastionu, bezproblémové prostředí RDP/SSH je dostupné všem virtuálním počítačům ve stejné virtuální síti. Nasazení Azure bastionu je vázané na virtuální síť, ne pro předplatné/účet nebo virtuální počítač.

Volitelně můžete vytvořit hostitele Azure bastionu pomocí [Azure Portal](./tutorial-create-host-portal.md).

## <a name="prerequisites"></a>Předpoklady

Ověřte, že máte předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si aktivovat [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Vytvoření hostitele bastionu

Tato část vám pomůže vytvořit nový prostředek Azure bastionu pomocí Azure PowerShell.

1. Vytvořte virtuální síť a podsíť Azure bastionu. Podsíť Azure bastionu musíte vytvořit pomocí hodnoty Name **AzureBastionSubnet**. Tato hodnota umožňuje službě Azure zjistit, do které podsítě nasadit prostředky bastionu. To se liší od podsítě brány. Je nutné použít podsíť s alespoň/27 nebo větší podsítí (/27,/26 atd.). Vytvořte **AzureBastionSubnet** bez směrovacích tabulek nebo delegování. Pokud používáte skupiny zabezpečení sítě na **AzureBastionSubnet**, přečtěte si článek [práce s skupin zabezpečení sítě](bastion-nsg.md) .

   ```azurepowershell-interactive
   $subnetName = "AzureBastionSubnet"
   $subnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
   $vnet = New-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myBastionRG" -Location "westeurope" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
   ```

2. Pro tuto podsíť vytvořte veřejnou IP adresu. Veřejná IP adresa je veřejná IP adresa, na které se bastionu prostředek k připojení RDP/SSH (přes port 443). Veřejná IP adresa musí být ve stejné oblasti jako prostředek bastionu, který vytváříte.

   ```azurepowershell-interactive
   $publicip = New-AzPublicIpAddress -ResourceGroupName "myBastionRG" -name "myPublicIP" -location "westeurope" -AllocationMethod Static -Sku Standard
   ```

3. Vytvořte nový prostředek Azure bastionu v AzureBastionSubnet vaší virtuální sítě. Vytváření a nasazování prostředků bastionu trvá přibližně 5 minut.

   ```azurepowershell-interactive
   $bastion = New-AzBastion -ResourceGroupName "myBastionRG" -Name "myBastion" -PublicIpAddress $publicip -VirtualNetwork $vnet
   ```

## <a name="next-steps"></a>Další kroky

* Další informace najdete v tématu [bastionu – Nejčastější dotazy](bastion-faq.md) .
* Pokud chcete používat skupiny zabezpečení sítě s podsítí Azure bastionu, přečtěte si téma [práce s skupin zabezpečení sítě](bastion-nsg.md).
