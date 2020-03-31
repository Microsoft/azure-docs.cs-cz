---
title: Připojení k partnerské síti ve službě Azure Lab Services | Dokumenty společnosti Microsoft
description: Přečtěte si, jak připojit testovací síť k jiné síti jako druhé straně. Můžete například připojit místní školní/univerzitní síť s virtuální sítí Lab v Azure.
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
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: d2115b1dc7e9f3150e44eb5ee9417e88ebeaa279
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80370832"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>Propojení sítě testovacího prostředí s virtuální virtuální sítí druhé strany ve službě Azure Lab Services 
Tento článek obsahuje informace o partnerské síti testovacího prostředí s jinou sítí. 

## <a name="overview"></a>Přehled
Partnerský vztah virtuální sítě umožňuje bezproblémově propojit virtuální sítě Azure. Po navázání partnerského vztahu se virtuální sítě pro účely připojení jeví jako jedna síť. Provoz mezi virtuálními počítači v partnerských virtuálních sítích je směrován přes páteřní infrastrukturu Microsoftu, podobně jako je provoz směrován mezi virtuálními počítači ve stejné virtuální síti, pouze přes privátní IP adresy. Další informace naleznete v [tématu Partnerský vztah virtuální sítě](../../virtual-network/virtual-network-peering-overview.md).

V některých scénářích, včetně následujících, může být nutné připojit síť testovacího prostředí k partnerské virtuální síti:

- Virtuální počítače v testovacím prostředí mají software, který se připojuje k místním licenčním serverům za účelem získání licence.
- Virtuální počítače v testovacím prostředí potřebují přístup k datovým souborům (nebo jiným souborům) ve sdílených složkách sítě univerzity. 

Některé místní sítě jsou připojené k virtuální síti Azure buď prostřednictvím [ExpressRoute](../../expressroute/expressroute-introduction.md) nebo [brány virtuální sítě](../../vpn-gateway/vpn-gateway-about-vpngateways.md). Tyto služby musí být nastaveny mimo Azure Lab Services. Další informace o připojení místní sítě k Azure pomocí ExpressRoute najdete v tématu [Přehled ExpressRoute](../../expressroute/expressroute-introduction.md). Pro místní připojení pomocí brány virtuální sítě, brány, zadané virtuální sítě a účtu testovacího prostředí musí být všechny ve stejné oblasti.

> [!NOTE]
> Při vytváření virtuální sítě Azure, která bude partnerský vztah s účtem testovacího prostředí, je důležité pochopit, jak oblast virtuální sítě ovlivňuje, kde se vytvářejí testovací prostředí učebny.  Další informace naleznete v části průvodce správcem [v oblastech\umístěních](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regions-or-locations).

## <a name="configure-at-the-time-of-lab-account-creation"></a>Konfigurovat v době vytvoření účtu testovacího prostředí
Během vytváření nového účtu testovacího prostředí můžete vybrat existující virtuální síť, která se zobrazí v rozevíracím seznamu **virtuální sítě Peer** na kartě **Upřesnit.** Vybraná virtuální síť je připojená (partnerský vztah) k testovacím prostředím vytvořeným pod účtem testovacího prostředí. Všechny virtuální počítače v testovacích prostředích, které jsou vytvořeny po provedení této změny bude mít přístup k prostředkům v partnerské virtuální sítě. 

![Výběr virtuální sítě pro druhou položku](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

> [!NOTE]
> Podrobné pokyny pro vytvoření účtu testovacího prostředí najdete v [tématu Nastavení účtu testovacího prostředí.](tutorial-setup-lab-account.md)


## <a name="configure-after-the-lab-is-created"></a>Konfigurovat po vytvoření testovacího prostředí
Stejnou vlastnost lze povolit z karty **konfigurace labs** na stránce **Účet testovacího prostředí,** pokud jste v době vytvoření účtu testovacího prostředí nenastavili partnerskou síť. Změna provedená v tomto nastavení platí pouze pro testovací prostředí, které jsou vytvořeny po změně. Jak můžete vidět na bitové kopii, můžete povolit nebo zakázat **peer virtuální síť** pro testovací prostředí v účtu testovacího prostředí. 

![Povolení nebo zakázání partnerského vztahu virtuální sítě po vytvoření testovacího prostředí](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png) 

Když vyberete virtuální síť pro pole **virtuální sítě Peer,** možnost **Povolit autorovi testovacího prostředí k výběru umístění testovacího prostředí** je zakázána. Je to proto, že testovací prostředí v účtu testovacího prostředí musí být ve stejné oblasti jako účet testovacího prostředí pro jejich připojení k prostředkům ve virtuální síti druhé strany. 

> [!IMPORTANT]
> Tato změna nastavení platí pouze pro testovací prostředí, které jsou vytvořeny po provedené změny, nikoli pro existující testovací prostředí. 


## <a name="next-steps"></a>Další kroky
Viz následující články:

- [Umožnění výběru umístění testovacího prostředí pro jeho tvůrce](allow-lab-creator-pick-lab-location.md)
- [Připojení sdílené galerie obrázků k testovacímu prostředí](how-to-attach-detach-shared-image-gallery.md)
- [Přidání uživatele jako vlastníka testovacího prostředí](how-to-add-user-lab-owner.md)
- [Zobrazení nastavení brány firewall pro testovací prostředí](how-to-configure-firewall-settings.md)
- [Konfigurace dalších nastavení testovacího prostředí](how-to-configure-lab-accounts.md)