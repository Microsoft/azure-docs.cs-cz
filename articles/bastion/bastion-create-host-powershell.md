---
title: Vytvoření hostitele bastionu pomocí Azure Powershellu | Dokumenty společnosti Microsoft
description: V tomto článku se dozvíte, jak vytvořit hostitele Azure Bastion
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 43d834f0c834696cd4a836466c9663fe7c31a392
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520505"
---
# <a name="create-an-azure-bastion-host-using-azure-powershell"></a>Vytvoření hostitele Azure Bastion pomocí Azure PowerShellu

Tento článek ukazuje, jak vytvořit hostitele Azure Bastion pomocí PowerShellu. Jakmile zřídíte službu Azure Bastion ve vaší virtuální síti, bezproblémové prostředí RDP/SSH je k dispozici všem virtuálním počítačům ve stejné virtuální síti. Nasazení Azure Bastion se používá pro virtuální síť, ne na předplatné nebo účet nebo virtuální počítač.

Volitelně můžete vytvořit hostitele Azure Bastion pomocí [portálu Azure](bastion-create-host-portal.md).

## <a name="before-you-begin"></a>Než začnete

Ověřte, že máte předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si aktivovat [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Vytvoření hostitele typu bašta

Tato část vám pomůže vytvořit nový prostředek Azure Bastion pomocí Azure PowerShellu.

1. Vytvořte virtuální síť a podsíť Azure Bastion. Podsíť Azure Bastion musíte vytvořit pomocí hodnoty názvu **AzureBastionSubnet**. Tato hodnota umožňuje Azure vědět, které podsítě nasadit prostředky Bastion. Tose liší od podsítě Brány. Je nutné použít podsíť alespoň /27 nebo větší podsítě (/27, /26 a tak dále). Vytvořte **AzureBastionSubnet** bez všech směrovacích tabulek nebo delegací. Pokud používáte skupiny zabezpečení sítě na **AzureBastionSubnet**, naleznete v článku [Práce s skupinami zabezpečení sítě.](bastion-nsg.md)

   ```azurepowershell-interactive
   $subnetName = "AzureBastionSubnet"
   $subnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
   $vnet = New-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myBastionRG" -Location "westeurope" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
   ```

2. Vytvořte veřejnou IP adresu pro Azure Bastion. Veřejná IP adresa je veřejná IP adresa prostředku Bastion, ke kterému bude přístup k RDP/SSH (přes port 443). Veřejná IP adresa musí být ve stejné oblasti jako prostředek bastionu, který vytváříte.

   ```azurepowershell-interactive
   $publicip = New-AzPublicIpAddress -ResourceGroupName "myBastionRG" -name "myPublicIP" -location "westeurope" -AllocationMethod Static -Sku Standard
   ```

3. Vytvořte nový prostředek Azure Bastion v AzureBastionSubnet vaší virtuální sítě. Vytvoření a nasazení prostředku bastionu trvá přibližně 5 minut.

   ```azurepowershell-interactive
   $bastion = New-AzBastion -ResourceGroupName "myBastionRG" -Name "myBastion" -PublicIpAddress $publicip -VirtualNetwork $vnet
   ```

## <a name="next-steps"></a>Další kroky

* Další informace našlápejte na časté dotazy k [baště.](bastion-faq.md)

* Informace o použití skupin zabezpečení sítě s podsítí Azure Bastion najdete v [tématu Práce se skupinami zabezpečení sítě](bastion-nsg.md).
