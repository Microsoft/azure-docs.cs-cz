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
ms.date: 05/18/2020
ms.author: mnayak
ms.openlocfilehash: 21d9f318ef18b7ffb49a95ce495c09f1fa46ec1a
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96491311"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-the-azure-portal"></a>Konfigurace předvolby směrování pro veřejnou IP adresu pomocí Azure Portal

V tomto článku se dozvíte, jak nakonfigurovat [Předvolby směrování](https://docs.microsoft.com/azure/virtual-network/routing-preference-overview) prostřednictvím sítě poskytovatele internetových služeb (možnost **Internet** ) pro veřejnou IP adresu. Po vytvoření veřejné IP adresy ji můžete přidružit k následujícím prostředkům Azure pro příchozí a odchozí provoz do Internetu:

* Virtuální počítač
* Škálovací sada virtuálních počítačů
* Azure Kubernetes Service (AKS)
* Internetový nástroj pro vyrovnávání zatížení
* Application Gateway
* Azure Firewall

Ve výchozím nastavení je předvolby směrování pro veřejnou IP adresu nastavená na globální síť Microsoft pro všechny služby Azure a může být přidružená k jakékoli službě Azure.

> [!IMPORTANT]
> Předvolby směrování jsou momentálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pokud ještě nemáte předplatné Azure, vytvořte si teď [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-public-ip-address-with-a-routing-preference"></a>Vytvoření veřejné IP adresy s prioritou směrování
1. Přihlaste se k [portálu Azure Portal](https://preview.portal.azure.com/).
2. Vyberte **Vytvořit prostředek**. 
3. Do vyhledávacího pole zadejte *veřejnou IP adresu*.
3. Ve výsledcích hledání vyberte **Veřejná IP adresa**. Potom na stránce **Veřejná IP adresa** vyberte **vytvořit**.
3. V možnosti **Předvolby směrování** vyberte **Internet**.

      ![Vytvoření veřejné IP adresy](./media/routing-preference-portal/pip-new.png)

    > [!NOTE]
    > Veřejné IP adresy se vytvářejí s IPv4 nebo IPv6 adresou. Předvolby směrování ale v současné době podporují jenom protokol IPV4.

Výše vytvořenou veřejnou IP adresu můžete přidružit k virtuálnímu počítači s [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) . Použijte oddíl CLI na stránce kurzu: [přidružte veřejnou IP adresu k virtuálnímu počítači](associate-public-ip-address-vm.md#azure-cli) a PŘIDRUŽTE veřejnou IP adresu k vašemu virtuálnímu počítači. Můžete také přidružit veřejnou IP adresu vytvořenou výše s [Azure Load Balancer](../load-balancer/load-balancer-overview.md), a to tak, že ji přiřadíte ke konfiguraci **front-endu** nástroje pro vyrovnávání zatížení. Veřejná IP adresa slouží jako virtuální IP adresa (VIP) s vyrovnáváním zatížení.

## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [veřejné IP adrese s upřednostněním směrování](routing-preference-overview.md).
- [Nakonfigurujte předvolby směrování pro virtuální počítač](tutorial-routing-preference-virtual-machine-portal.md).
- [Nakonfigurujte předvolby směrování pro veřejnou IP adresu pomocí PowerShellu](routing-preference-powershell.md).
- Přečtěte si další informace o [veřejných IP adresách](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) v Azure.
- Přečtěte si další informace o všech [nastaveních veřejné IP adresy](virtual-network-public-ip-address.md#create-a-public-ip-address).
