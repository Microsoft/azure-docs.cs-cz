---
title: Připojení k síti peer ve službě Azure Lab Services | Dokumentace Microsoftu
description: Zjistěte, jak připojit síť testovacího prostředí s jinou sítí jako sdílené. Třeba může připojte vaší školy nebo univerzity v místní síti pomocí testovacího prostředí v Azure virtual network.
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
ms.date: 03/28/2019
ms.author: spelluru
ms.openlocfilehash: 465352af52cbc84773e52782233065b3000921e7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60695363"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>Připojit síť testovacího prostředí s partnerská virtuální síť v Azure Lab Services 
Tento článek obsahuje informace o partnerských vztazích labs sítě s jinou sítí. 

## <a name="overview"></a>Přehled
Partnerský vztah virtuálních sít umožňuje bezproblémové propojení virtuálních sítí Azure. Po navázání partnerského vztahu se virtuální sítě pro účely připojení jeví jako jedna síť. Provoz mezi virtuálními počítači v partnerských virtuálních sítích je směrován přes páteřní infrastrukturu Microsoftu, stejně jako mezi virtuálními počítači ve stejné virtuální síti pomocí soukromých IP adres pouze prochází. Další informace najdete v tématu [partnerský vztah virtuálních sítí](../../virtual-network/virtual-network-peering-overview.md).

Budete muset připojit síť testovacího prostředí s partnerská virtuální síť v některých scénářích, včetně následujících:

- Software, který se připojuje k místním serverům licence pro získání licence mají virtuální počítače v testovacím prostředí
- Virtuální počítače v testovacím prostředí potřebovat přístup k datovým sadám (nebo jiné soubory) ve sdílených síťových složkách na university. 

Určité místní sítě jsou buď připojené k virtuální síti Azure přes [ExpressRoute](../../expressroute/expressroute-introduction.md) nebo [brány virtuální sítě](../../vpn-gateway/vpn-gateway-about-vpngateways.md). Tyto služby musíte nastavit mimo Azure Lab Services. Další informace o připojení místní sítě k Azure pomocí ExpressRoute, najdete v článku [přehled ExpressRoute]) (.. /expressroute/expressroute-Introduction.MD). Pro místní připojení pomocí virtuální síťová brána, brány, zadaná virtuální síť a účet testovacího prostředí musí být ve stejné oblasti.


## <a name="configure-at-the-time-of-lab-account-creation"></a>Konfigurace v době vytvoření účtu služby testovacího prostředí
Při vytvoření nového účtu testovacího prostředí, můžete si vybrat existující virtuální síť, která zobrazuje **partnerská virtuální síť** rozevíracího seznamu. Vybrané virtuální síti je connected(peered) k testovacím prostředím vytvořené v rámci účtu testovacího prostředí. Všechny virtuální počítače v testovacích prostředí, které jsou vytvořeny po k provedení této změny bude obsahovat přístup k prostředkům v partnerské virtuální síti. 

![Vyberte virtuální síť na vytvoření partnerského vztahu](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

> [!NOTE]
> Podrobné pokyny pro vytvoření účtu testovacího prostředí, najdete v části [nastavení účtu testovacího prostředí](tutorial-setup-lab-account.md)


## <a name="configure-after-the-lab-is-created"></a>Nakonfigurovat po vytvoření testovacího prostředí
Stejnou vlastnost můžete povolit z **Labs konfigurace** karty **účet testovacího prostředí** stránky, pokud jste nenastavili sítě peer v době vytvoření účtu služby testovacího prostředí. Změny provedené v toto nastavení platí jenom pro testovací prostředí, které jsou vytvořeny po provedení změny.

![Povolení nebo zakázání VNet peering po vytvoření testovacího prostředí](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png) 

Jak je vidět na obrázku, můžete povolit nebo zakázat **partnerská virtuální síť** pro laboratorní cvičení v účtu testovacího prostředí. 

> [!IMPORTANT]
> Tato změna nastavení platí jenom pro testovací prostředí, které jsou vytvořeny po provedení změny, ne na existující testovací prostředí. 



## <a name="next-steps"></a>Další postup
Viz následující články:

- [Jako správce vytvářet a spravovat účty pro testovací prostředí](how-to-manage-lab-accounts.md)
- [Jako vlastník testovacího prostředí vytvoření a správa testovacích prostředí](how-to-manage-classroom-labs.md)
- [Jako vlastník testovacího prostředí nastavení a publikování šablon](how-to-create-manage-template.md)
- [Jako uživatel, který testovací prostředí přístup k testovacím prostředím v učebnách](how-to-use-classroom-lab.md)

