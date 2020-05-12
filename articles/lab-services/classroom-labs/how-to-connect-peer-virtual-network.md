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
ms.date: 03/31/2020
ms.author: spelluru
ms.openlocfilehash: 8d8f2c747a4bc0ab2119c92e61188e3c57f2b212
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2020
ms.locfileid: "83118358"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>Připojte síť testovacího prostředí k partnerské virtuální síti v Azure Lab Services

Tento článek poskytuje informace o partnerském vztahu vaší sítě Labs s jinou sítí.

## <a name="overview"></a>Přehled

Partnerské vztahy virtuálních sítí umožňují bezproblémové připojení virtuálních sítí Azure. Po navázání partnerského vztahu se virtuální sítě pro účely připojení jeví jako jedna síť. Přenos dat mezi virtuálními počítači v partnerských virtuálních sítích je směrován přes páteřní infrastrukturu Microsoftu, podobně jako provoz se směruje mezi virtuálními počítači ve stejné virtuální síti, a to pouze pomocí privátních IP adres. Další informace najdete v tématu [partnerský vztah virtuálních sítí](../../virtual-network/virtual-network-peering-overview.md).

V některých scénářích může být nutné připojit síť testovacího prostředí k partnerské virtuální síti, včetně následujících:

- Virtuální počítače v testovacím prostředí mají software, který se připojuje k místním licenčním serverům, aby získal licenci.
- Virtuální počítače v testovacím prostředí potřebují přístup k datovým sadám (nebo jakýmkoli jiným souborům) na sdílených síťových složkách univerzity.

Některé místní sítě jsou připojené k Azure Virtual Network buď prostřednictvím [ExpressRoute](../../expressroute/expressroute-introduction.md) , nebo [Virtual Network bránou](../../vpn-gateway/vpn-gateway-about-vpngateways.md). Tyto služby musí být nastaveny mimo Azure Lab Services. Další informace o připojení místní sítě k Azure pomocí ExpressRoute najdete v tématu [ExpressRoute Overview](../../expressroute/expressroute-introduction.md). V případě místního připojení pomocí Virtual Network brány musí být brána, zadaná virtuální síť a účet testovacího prostředí všechny ve stejné oblasti.

> [!NOTE]
> Při vytváření Virtual Network Azure, která bude mít partnerský vztah s účtem testovacího prostředí, je důležité pochopit, jakým způsobem má oblast virtuální sítě vliv na vytvoření učebn Labs.  Další informace najdete v části příručky pro správce na [regions\locations](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regionslocations).

## <a name="configure-at-the-time-of-lab-account-creation"></a>Konfigurace v době vytváření účtu testovacího prostředí

Během vytváření nového [účtu testovacího prostředí](tutorial-setup-lab-account.md)můžete vybrat existující virtuální síť, která se zobrazí v rozevíracím seznamu **partnerské virtuální sítě** na kartě **Upřesnit** .  V seznamu se zobrazí jenom virtuální sítě ve stejné oblasti jako účet testovacího prostředí. Vybraná virtuální síť je připojená k laboratořím vytvořeným v rámci účtu testovacího prostředí.  Všechny virtuální počítače v laboratořích, které se vytvoří po provedení této změny, budou mít přístup k prostředkům v partnerské virtuální síti.

![Vybrat virtuální síť pro partnerský uzel](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

### <a name="address-range"></a>Rozsah adres

K dispozici je také možnost zadat **Rozsah adres** pro virtuální počítače pro cvičení.  Vlastnost **Rozsah adres** se vztahuje jenom na to, jestli je pro testovací prostředí povolená **Partnerská virtuální síť** .  Pokud je zadaný rozsah adres, vytvoří se v tomto rozsahu adres všechny virtuální počítače v testovacím prostředí pod účtem testovacího prostředí. Rozsah adres by měl být v zápisu CIDR (např. 10.20.0.0/20) a nesmí se překrývat s žádnými existujícími rozsahy adres.  Když zadáváte rozsah adres, je důležité uvažovat o počtu *laboratoří* , které budou vytvořeny, a poskytnout rozsah adres, který se má přizpůsobit. Testovací služby předpokládají maximálně 512 virtuálních počítačů na testovací prostředí.  Například rozsah IP adres s '/23 ' může vytvořit pouze jedno testovací prostředí.  Rozsah s '/21 ' umožní vytvořit čtyři laboratoře.

Pokud není zadaný **Rozsah adres** , služba testovacího prostředí použije výchozí rozsah adres, který mu předává Azure při vytváření virtuální sítě pro partnerský vztah s vaší virtuální sítí.  Rozsah je často podobný 10. x. 0,0/16.  To může vést k překrytí rozsahu IP adres, proto nezapomeňte buď zadat rozsah adres v nastavení testovacího prostředí, nebo zkontrolovat rozsah adres vaší virtuální sítě, na kterou se navázat partnerský vztah.

## <a name="configure-after-the-lab-is-created"></a>Konfigurace po vytvoření testovacího prostředí

Pokud jste v době vytváření účtu testovacího prostředí nevytvořili rovnocennou síť, můžete tuto vlastnost Povolit na kartě **Konfigurace Labs** stránky **účtu testovacího prostředí** . Změny provedené v tomto nastavení platí pouze pro laboratoře, které byly vytvořeny po provedení změny. Jak vidíte na obrázku, můžete povolit nebo zakázat **partnerský virtuální síť** pro laboratoře v účtu testovacího prostředí.

![Povolení nebo zakázání partnerského vztahu VNet po vytvoření testovacího prostředí](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png)

Když vyberete virtuální síť pro pole **rovnocenná virtuální síť** , možnost **Povolení testovacího prostředí pro výběr umístění testovacího** prostředí je zakázaná. To je proto, že laboratoře v účtu testovacího prostředí musí být ve stejné oblasti jako účet testovacího prostředí, aby se připojovaly k prostředkům v partnerské virtuální síti.

> [!IMPORTANT]
> Nastavení partnerské virtuální sítě se vztahuje pouze na laboratoře vytvořené po provedení změny, nikoli na stávající cvičení.

## <a name="next-steps"></a>Další kroky

Viz následující články:

- [Umožnění výběru umístění testovacího prostředí pro jeho tvůrce](allow-lab-creator-pick-lab-location.md)
- [Připojení Galerie sdílených imagí k testovacímu prostředí](how-to-attach-detach-shared-image-gallery.md)
- [Přidat uživatele jako vlastníka testovacího prostředí](how-to-add-user-lab-owner.md)
- [Zobrazit nastavení brány firewall pro testovací prostředí](how-to-configure-firewall-settings.md)
- [Konfigurace dalších nastavení pro testovací prostředí](how-to-configure-lab-accounts.md)
