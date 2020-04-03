---
title: Vytvoření hostitele bašty pomocí azure CLI | Azure Bašta
description: V tomto článku se dozvíte, jak vytvořit a odstranit hostitele bašty
services: bastion
author: mialdrid
ms.service: bastion
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mialdrid
ms.openlocfilehash: f98c965ad3b776f3688a716ba28b5367a00c9119
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619223"
---
# <a name="create-an-azure-bastion-host-using-azure-cli"></a>Vytvoření hostitele Azure Bastion pomocí azure cli

Tento článek ukazuje, jak vytvořit hostitele Azure Bastion pomocí Azure CLI. Jakmile zřídíte službu Azure Bastion ve vaší virtuální síti, bezproblémové prostředí RDP/SSH je k dispozici všem virtuálním počítačům ve stejné virtuální síti. Nasazení Azure Bastion se používá pro virtuální síť, ne na předplatné nebo účet nebo virtuální počítač.

Volitelně můžete vytvořit hostitele Azure Bastion pomocí [portálu Azure](bastion-create-host-portal.md)nebo pomocí [Azure PowerShellu](bastion-create-host-powershell.md).

## <a name="before-you-begin"></a>Než začnete

Ověřte, že máte předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si aktivovat [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [cloudshell cli](../../includes/vpn-gateway-cloud-shell-cli.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Vytvoření hostitele typu bašta

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
