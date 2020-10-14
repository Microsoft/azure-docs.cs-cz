---
title: Vytvoření hostitele bastionu pomocí Azure CLI | Bastionu Azure
description: V tomto článku se dozvíte, jak vytvořit a odstranit hostitele bastionu.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 10/13/2020
ms.author: cherylmc
ms.openlocfilehash: 851ec86feb5244ff43759a7aef2b80876dcfa734
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92018538"
---
# <a name="create-an-azure-bastion-host-using-azure-cli"></a>Vytvoření hostitele Azure bastionu pomocí Azure CLI

V tomto článku se dozvíte, jak vytvořit hostitele Azure bastionu pomocí Azure CLI. Po nasazení bastionu se můžete připojit k virtuálnímu počítači přes jeho privátní IP adresu v prohlížeči pomocí Azure Portal. Váš virtuální počítač nepotřebuje veřejnou IP adresu, dalšího klienta nebo speciální software. Nasazení Azure bastionu je vázané na virtuální síť, ne pro předplatné/účet nebo virtuální počítač. Bezproblémové prostředí RDP/SSH je k dispozici všem virtuálním počítačům ve stejné virtuální síti.

Volitelně můžete vytvořit hostitele Azure bastionu pomocí [Azure Portal](tutorial-create-host-portal.md)nebo pomocí [Azure PowerShell](bastion-create-host-powershell.md).

## <a name="before-you-begin"></a>Než začnete

Ověřte, že máte předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si aktivovat [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [Cloud Shell CLI](../../includes/vpn-gateway-cloud-shell-cli.md)]

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

* Připojte se k virtuálnímu počítači.
   * [Virtuální počítači s Linuxem](bastion-connect-vm-ssh.md)
   * [Virtuální počítač s Windows](bastion-connect-vm-rdp.md)

