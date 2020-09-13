---
title: Vytvoření hostitele bastionu pomocí Azure CLI | Bastionu Azure
description: V tomto článku se dozvíte, jak vytvořit a odstranit hostitele bastionu.
services: bastion
author: mialdrid
ms.service: bastion
ms.topic: how-to
ms.date: 03/26/2020
ms.author: mialdrid
ms.openlocfilehash: 8ee90d80230f9115946525ede325e874e98e358e
ms.sourcegitcommit: 70ee014d1706e903b7d1e346ba866f5e08b22761
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2020
ms.locfileid: "90024328"
---
# <a name="create-an-azure-bastion-host-using-azure-cli"></a>Vytvoření hostitele Azure bastionu pomocí Azure CLI

V tomto článku se dozvíte, jak vytvořit hostitele Azure bastionu pomocí Azure CLI. Jakmile ve své virtuální síti zřídíte službu Azure bastionu, bezproblémové prostředí RDP/SSH je dostupné všem virtuálním počítačům ve stejné virtuální síti. Nasazení Azure bastionu je vázané na virtuální síť, ne pro předplatné/účet nebo virtuální počítač.

Volitelně můžete vytvořit hostitele Azure bastionu pomocí [Azure Portal](bastion-create-host-portal.md)nebo pomocí [Azure PowerShell](bastion-create-host-powershell.md).

## <a name="before-you-begin"></a>Než začnete

Ověřte, že máte předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si aktivovat [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [cloudshell cli](../../includes/vpn-gateway-cloud-shell-cli.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Vytvoření hostitele bastionu

Tato část vám pomůže vytvořit nový prostředek Azure bastionu pomocí Azure CLI.

> [!NOTE]
> Jak je znázorněno v příkladech, použijte `--location` parametr `--resource-group` pro každý příkaz k zajištění toho, aby se prostředky nasadily dohromady.

1. Vytvořte virtuální síť a podsíť Azure bastionu. Podsíť Azure bastionu musíte vytvořit pomocí hodnoty Name **AzureBastionSubnet**. Tato hodnota umožňuje službě Azure zjistit, do které podsítě nasadit prostředky bastionu. To se liší od podsítě brány. Je nutné použít podsíť s alespoň/27 nebo větší podsítí (/27,/26 atd.). Vytvořte **AzureBastionSubnet** bez směrovacích tabulek nebo delegování. Pokud používáte skupiny zabezpečení sítě na **AzureBastionSubnet**, přečtěte si článek [práce s skupin zabezpečení sítě](bastion-nsg.md) .

   ```azurecli-interactive
   az network vnet create --resource-group MyResourceGroup --name MyVnet --address-prefix 10.0.0.0/16 --subnet-name AzureBastionSubnet --subnet-prefix 10.0.0.0/24 --location northeurope
   ```

2. Vytvořte veřejnou IP adresu pro Azure bastionu. Veřejná IP adresa je veřejná IP adresa, na které se bastionu prostředek k připojení RDP/SSH (přes port 443). Veřejná IP adresa musí být ve stejné oblasti jako prostředek bastionu, který vytváříte.

   ```azurecli-interactive
   az network public-ip create --resource-group MyResourceGroup --name MyIp --sku Standard --location northeurope
   ```

3. Vytvořte nový prostředek Azure bastionu v AzureBastionSubnet vaší virtuální sítě. Vytváření a nasazování prostředků bastionu trvá přibližně 5 minut.

   ```azurecli-interactive
   az network bastion create --name MyBastion --public-ip-address MyIp --resource-group MyResourceGroup --vnet-name MyVnet --location northeurope
   ```

## <a name="next-steps"></a>Další kroky

* Další informace najdete v tématu [bastionu – Nejčastější dotazy](bastion-faq.md) .

* Pokud chcete používat skupiny zabezpečení sítě s podsítí Azure bastionu, přečtěte si téma [práce s skupin zabezpečení sítě](bastion-nsg.md).
