---
title: Vytvoření hostitele bašty pomocí azure CLI | Azure Bašta
description: V tomto článku se dozvíte, jak vytvořit a odstranit hostitele bašty
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: cherylmc
ms.openlocfilehash: 9e216bf2bb8e2e605723256f0511b9bdb63083b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337568"
---
# <a name="create-an-azure-bastion-host-using-azure-cli"></a>Vytvoření hostitele Azure Bastion pomocí azure cli

Tento článek ukazuje, jak vytvořit hostitele Azure Bastion pomocí Azure CLI. Jakmile zřídíte službu Azure Bastion ve vaší virtuální síti, bezproblémové prostředí RDP/SSH je k dispozici všem virtuálním počítačům ve stejné virtuální síti. Nasazení Azure Bastion se používá pro virtuální síť, ne na předplatné nebo účet nebo virtuální počítač.

Volitelně můžete vytvořit hostitele Azure Bastion pomocí [portálu Azure](bastion-create-host-portal.md)nebo pomocí [Azure PowerShellu](bastion-create-host-powershell.md).

## <a name="before-you-begin"></a>Než začnete

Ověřte, že máte předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si aktivovat [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [cloudshell cli](../../includes/vpn-gateway-cloud-shell-cli.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Vytvoření hostitele bašty

Tato část vám pomůže vytvořit nový prostředek Azure Bastion pomocí Azure CLI.

1. Vytvořte virtuální síť a podsíť Azure Bastion. Podsíť Azure Bastion musíte vytvořit pomocí hodnoty názvu **AzureBastionSubnet**. Tato hodnota umožňuje Azure vědět, které podsítě nasadit prostředky Bastion. Tose liší od podsítě Brány. Je nutné použít podsíť alespoň /27 nebo větší podsítě (/27, /26 a tak dále). Vytvořte **AzureBastionSubnet** bez všech směrovacích tabulek nebo delegací. Pokud používáte skupiny zabezpečení sítě na **AzureBastionSubnet**, naleznete v článku [Práce s skupinami zabezpečení sítě.](bastion-nsg.md)

   ```azurecli-interactive
   az network vnet create -g MyResourceGroup -n MyVnet --address-prefix 10.0.0.0/16 --subnet-name AzureBastionSubnet  --subnet-prefix 10.0.0.0/24
   ```

2. Vytvořte veřejnou IP adresu pro Azure Bastion. Veřejná IP adresa je veřejná IP adresa prostředku Bastion, ke kterému bude přístup k RDP/SSH (přes port 443). Veřejná IP adresa musí být ve stejné oblasti jako prostředek bastionu, který vytváříte.

   ```azurecli-interactive
   az network public-ip create -g MyResourceGroup -n MyIp
   ```

3. Vytvořte nový prostředek Azure Bastion v AzureBastionSubnet vaší virtuální sítě. Vytvoření a nasazení prostředku bastionu trvá přibližně 5 minut.

   ```azurecli-interactive
   az network bastion create --name $name--public-ip-address $publicip--resource-group $RgName --vnet-name $VNetName --location $location
                           
   ```

## <a name="next-steps"></a>Další kroky

* Další informace našlápejte na časté dotazy k [baště.](bastion-faq.md)

* Informace o použití skupin zabezpečení sítě s podsítí Azure Bastion najdete v [tématu Práce se skupinami zabezpečení sítě](bastion-nsg.md).
