---
title: Nakonfigurujte jak možnosti předvolby směrování pro virtuální počítač – Azure Portal
description: Naučte se, jak povolit obě možnosti směrování obou možností.
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
ms.openlocfilehash: d9bb00077b1d96d13e15861cac477f913a002030
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101679309"
---
# <a name="configure-both-routing-preference-options-for-a-virtual-machine"></a>Nakonfigurovat obě možnosti předvolby směrování pro virtuální počítač

V tomto článku se dozvíte, jak nakonfigurovat možnosti [Předvolby směrování](./routing-preference-overview.md) (Internet a globální síť Microsoft) pro virtuální počítač. To se dosahuje pomocí dvou rozhraní virtuální sítě, jednoho síťového rozhraní s veřejnou IP adresou, která je směrována přes globální síť Microsoft a druhá s veřejnou IP adresou směrování prostřednictvím sítě poskytovatele internetových služeb.

## <a name="prerequisites"></a>Předpoklady

Vytvořte virtuální počítač s veřejnou IP adresou podle pokynů popsaných v tématu [Vytvoření virtuálního počítače se statickou veřejnou IP adresou pomocí Azure Portal](./virtual-network-deploy-static-pip-arm-portal.md) pomocí Azure Portal. Výchozí volba předvolby směrování pro veřejnou IP adresu je prostřednictvím **globální sítě Microsoft**. Jakmile budete mít vytvořený virtuální počítač s veřejnou IP adresou, přidejte do virtuálního počítače druhou veřejnou IP adresu, která bude směrovat provoz prostřednictvím tranzitní sítě poskytovatele internetových služeb pomocí upřednostňované předvolby směrování nakonfigurované jako **Internet**.

## <a name="create-a-public-ip-address-with-a-routing-preference-choice-internet"></a>Vytvoření veřejné IP adresy s volbou předvolby směrování Internet
1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
2. Vyberte **Vytvořit prostředek**. 
3. Do vyhledávacího pole zadejte *veřejnou IP adresu*.
4. Ve výsledcích hledání vyberte **Veřejná IP adresa**. Potom na stránce **Veřejná IP adresa** vyberte **vytvořit**.
5. V možnosti **Předvolby směrování** vyberte **Internet**.

      ![Vytvoření veřejné IP adresy](./media/routing-preference-portal/public-ip-new.png)

    > [!NOTE]
    > Veřejné IP adresy se vytvářejí s IPv4 nebo IPv6 adresou. Předvolby směrování ale v současné době podporují jenom protokol IPV4.

## <a name="create-a-network-interface-and-associate-the-public-ip"></a>Vytvořte síťové rozhraní a přidružte veřejnou IP adresu.

1. Vytvořte [síťové rozhraní](./routing-preference-overview.md) pomocí Azure Portal s výchozími nastaveními. 
1. [Přidružte veřejnou IP adresu k síťovému rozhraní](./associate-public-ip-address-vm.md) vytvořenému v předchozí části. Zobrazení IP adresy může trvat několik sekund. Zobrazte veřejnou IP adresu přiřazenou ke konfiguraci protokolu IP, jak je znázorněno níže:

    ![Přidružit veřejnou IP adresu](./media/routing-preference-mixed-network-adapter-portal/public-ip.png)

## <a name="attach-network-interface-to-the-vm"></a>Připojení síťového rozhraní k virtuálnímu počítači

1. [Připojte síťové rozhraní vytvořené v předchozí části k virtuálnímu počítači](./virtual-network-network-interface-vm.md).
2. Teď si můžete zobrazit dvě rozhraní virtuální sítě přidružená k virtuálnímu počítači, jednu s veřejnou IP adresou, která je směrována přes globální síť Microsoft, a druhá směrována prostřednictvím sítě poskytovatele internetových služeb, jak je znázorněno níže:  ![ připojení síťového rozhraní k virtuálnímu počítači.](./media/routing-preference-mixed-network-adapter-portal/mixed-network-adapter.png) 

## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [veřejné IP adrese s upřednostněním směrování](routing-preference-overview.md).
- [Nakonfigurujte předvolby směrování pro virtuální počítač](tutorial-routing-preference-virtual-machine-portal.md).
- [Nakonfigurujte předvolby směrování pro veřejnou IP adresu pomocí PowerShellu](routing-preference-powershell.md).
- Přečtěte si další informace o [veřejných IP adresách](./public-ip-addresses.md#public-ip-addresses) v Azure.
- Přečtěte si další informace o všech [nastaveních veřejné IP adresy](virtual-network-public-ip-address.md#create-a-public-ip-address).