---
title: Připojení k síti typu peer v Azure Lab Services | Microsoft Docs
description: Přečtěte si, jak propojit síť testovacího prostředí s jinou sítí jako s partnerským vztahem. Připojte například svou místní školní nebo univerzitní síť k virtuální síti testovacího prostředí v Azure.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2019
ms.author: spelluru
ms.openlocfilehash: d3f6acef7491a07f94eec0b2c3b2f3bcd9c01a33
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74701673"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>Připojte síť testovacího prostředí k partnerské virtuální síti v Azure Lab Services 
Tento článek poskytuje informace o partnerském vztahu vaší sítě Labs s jinou sítí. 

## <a name="overview"></a>Přehled
Partnerské vztahy virtuálních sítí umožňují bezproblémové připojení virtuálních sítí Azure. Po navázání partnerského vztahu se virtuální sítě pro účely připojení jeví jako jedna síť. Přenos dat mezi virtuálními počítači v partnerských virtuálních sítích je směrován přes páteřní infrastrukturu Microsoftu, podobně jako provoz se směruje mezi virtuálními počítači ve stejné virtuální síti, a to pouze pomocí privátních IP adres. Další informace najdete v tématu [partnerský vztah virtuálních sítí](../../virtual-network/virtual-network-peering-overview.md).

V některých scénářích může být nutné připojit síť testovacího prostředí k partnerské virtuální síti, včetně následujících:

- Virtuální počítače v testovacím prostředí mají software, který se připojuje k místním licenčním serverům, aby získal licenci.
- Virtuální počítače v testovacím prostředí potřebují přístup k datovým sadám (nebo jakýmkoli jiným souborům) na sdílených síťových složkách univerzity. 

Některé místní sítě jsou připojené k Azure Virtual Network buď prostřednictvím [ExpressRoute](../../expressroute/expressroute-introduction.md) , nebo [Virtual Network bránou](../../vpn-gateway/vpn-gateway-about-vpngateways.md). Tyto služby musí být nastaveny mimo Azure Lab Services. Další informace o připojení místní sítě k Azure pomocí ExpressRoute najdete v tématu [ExpressRoute Overview](../../expressroute/expressroute-introduction.md). V případě místního připojení pomocí Virtual Network brány musí být brána, zadaná virtuální síť a účet testovacího prostředí všechny ve stejné oblasti.

## <a name="configure-at-the-time-of-lab-account-creation"></a>Konfigurace v době vytváření účtu testovacího prostředí
Během vytváření nového účtu testovacího prostředí můžete vybrat existující virtuální síť, která se zobrazí v rozevíracím seznamu **partnerských virtuálních sítí** . Vybraná virtuální síť je připojená k laboratořím vytvořeným v rámci účtu testovacího prostředí. Všechny virtuální počítače v laboratořích, které se vytvoří po provedení této změny, budou mít přístup k prostředkům v partnerské virtuální síti. 

![Vybrat virtuální síť pro partnerský uzel](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

> [!NOTE]
> Podrobné pokyny pro vytvoření účtu testovacího prostředí najdete v tématu [Nastavení účtu testovacího prostředí](tutorial-setup-lab-account.md) .


## <a name="configure-after-the-lab-is-created"></a>Konfigurace po vytvoření testovacího prostředí
Pokud jste v době vytváření účtu testovacího prostředí nevytvořili rovnocennou síť, můžete tuto vlastnost Povolit na kartě **Konfigurace Labs** stránky **účtu testovacího prostředí** . Změny provedené v tomto nastavení platí pouze pro laboratoře, které byly vytvořeny po provedení změny. Jak vidíte na obrázku, můžete povolit nebo zakázat **partnerský virtuální síť** pro laboratoře v účtu testovacího prostředí. 

![Povolení nebo zakázání partnerského vztahu VNet po vytvoření testovacího prostředí](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png) 

Když vyberete virtuální síť pro pole **rovnocenná virtuální síť** , možnost **Povolení testovacího prostředí pro výběr umístění testovacího** prostředí je zakázaná. Je to proto, že laboratoře v účtu testovacího prostředí musí být ve stejné oblasti jako účet testovacího prostředí, aby se připojovaly k prostředkům v partnerské virtuální síti. 

> [!IMPORTANT]
> Tato změna nastavení se vztahuje pouze na laboratoře vytvořené po provedení změny, nikoli na stávající cvičení. 


## <a name="next-steps"></a>Další kroky
Viz následující články:

- [Účty testovacího prostředí se vytvářejí a spravují jako správce.](how-to-manage-lab-accounts.md)
- [Jako vlastník testovacího prostředí vytvářet a spravovat cvičení](how-to-manage-classroom-labs.md)
- [Jako vlastník testovacího prostředí, nastavení a publikování šablon](how-to-create-manage-template.md)
- [Jako uživatel testovacího prostředí, Access učeben Labs](how-to-use-classroom-lab.md)

