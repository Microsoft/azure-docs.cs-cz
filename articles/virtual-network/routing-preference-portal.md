---
title: Konfigurace předvolby směrování pro veřejnou IP adresu – Azure Portal
description: Naučte se vytvářet veřejné IP adresy s prioritou směrování internetových přenosů.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: mnayak
ms.openlocfilehash: f445eab65e8d2448e57bad19c52a4b72732016bb
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101672914"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-the-azure-portal"></a>Konfigurace předvolby směrování pro veřejnou IP adresu pomocí Azure Portal

V tomto článku se dozvíte, jak nakonfigurovat [Předvolby směrování](./routing-preference-overview.md) prostřednictvím sítě poskytovatele internetových služeb (možnost **Internet** ) pro veřejnou IP adresu. Po vytvoření veřejné IP adresy ji můžete přidružit k následujícím prostředkům Azure pro příchozí a odchozí provoz do Internetu:

* Virtuální počítač
* Škálovací sada virtuálních počítačů
* Azure Kubernetes Service (AKS)
* Internetový nástroj pro vyrovnávání zatížení
* Application Gateway
* Azure Firewall

Ve výchozím nastavení je předvolby směrování pro veřejnou IP adresu nastavená na globální síť Microsoft pro všechny služby Azure a může být přidružená k jakékoli službě Azure.

Pokud ještě nemáte předplatné Azure, vytvořte si teď [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-public-ip-address-with-a-routing-preference"></a>Vytvoření veřejné IP adresy s prioritou směrování
1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
2. Vyberte **Vytvořit prostředek**.
3. Do vyhledávacího pole zadejte *veřejnou IP adresu*.
3. Ve výsledcích hledání vyberte **Veřejná IP adresa**. Potom na stránce **Veřejná IP adresa** vyberte **vytvořit**.
1. V případě SKU vyberte možnost **Standard**.
1. V **předvolbách směrování** vyberte **Internet**.

      ![Vytvoření veřejné IP adresy](./media/routing-preference-portal/public-ip-new.png)
1. V části **Konfigurace IP adresy IPv4** zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Předplatné | Vyberte své předplatné.|
    | Skupina prostředků | Vyberte **vytvořit nový**, zadejte *RoutingPreferenceResourceGroup* a pak vyberte **OK**. |
    | Umístění | Vyberte **USA – východ**.|
    | Zóna dostupnosti | Ponechte výchozí hodnotu- **Zone-redundantní**. |
1. Vyberte **Vytvořit**.

    > [!NOTE]
    > Veřejné IP adresy se vytvářejí s IPv4 nebo IPv6 adresou. Předvolby směrování ale v současné době podporují jenom protokol IPV4.

Výše vytvořenou veřejnou IP adresu můžete přidružit k virtuálnímu počítači s [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) . Použijte oddíl CLI na stránce kurzu: [přidružte veřejnou IP adresu k virtuálnímu počítači](associate-public-ip-address-vm.md#azure-cli) a PŘIDRUŽTE veřejnou IP adresu k vašemu virtuálnímu počítači. Můžete také přidružit veřejnou IP adresu vytvořenou výše s [Azure Load Balancer](../load-balancer/load-balancer-overview.md), a to tak, že ji přiřadíte ke konfiguraci **front-endu** nástroje pro vyrovnávání zatížení. Veřejná IP adresa slouží jako virtuální IP adresa (VIP) s vyrovnáváním zatížení.

## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [veřejné IP adrese s upřednostněním směrování](routing-preference-overview.md).
- [Nakonfigurujte předvolby směrování pro virtuální počítač](tutorial-routing-preference-virtual-machine-portal.md).
- [Nakonfigurujte předvolby směrování pro veřejnou IP adresu pomocí PowerShellu](routing-preference-powershell.md).
- Přečtěte si další informace o [veřejných IP adresách](./public-ip-addresses.md#public-ip-addresses) v Azure.
- Přečtěte si další informace o všech [nastaveních veřejné IP adresy](virtual-network-public-ip-address.md#create-a-public-ip-address).