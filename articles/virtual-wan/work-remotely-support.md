---
title: Virtuální WAN Azure a vzdálená práce
description: Tato stránka popisuje, jak můžete využít Azure Virtual WAN k povolení vzdálené práce kvůli pandemii COVID-19.
services: virtual-wan
author: reyandap
ms.service: virtual-wan
ms.topic: article
ms.date: 03/22/2020
ms.author: cherylmc
ms.openlocfilehash: ce212b5da90906966025674b58884d0e2f5bb064
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337132"
---
# <a name="azure-virtual-wan-and-supporting-remote-work"></a>Virtuální WAN Azure a podpora vzdálené práce

>[!NOTE]
>Tento článek popisuje, jak můžete využít Azure Virtual WAN, Azure, Microsoft network a ekosystém partnerů Azure k vzdálené práci a zmírnění problémů se sítí, kterým čelíte kvůli krizi COVID-19.
>

Snažíte se poskytovat připojení pro vzdálené uživatele?
Najednou vidíte potřebu podporovat nárůst uživatelů nad rámec toho, co jste plánovali?
Potřebujete, aby se uživatel mohl připojit z domova a nejen získat přístup ke cloudu, ale také se k němu dostat místně?
Potřebujete, aby vzdálení uživatelé mohli oslovit prostředky za privátní sítě WAN?
Potřebujete, aby uživatelé měli přístup k prostředkům v rámci cloudu, aniž by museli nastavovat připojení mezi oblastmi?
Vzhledem k tomu, že tato globální pandemie vytváří kolem nás bezprecedentní změny, je tým Azure Virtual WAN tu, abyste uspokojili vaše potřeby připojení.

Azure Virtual WAN je síťová služba, která spojuje mnoho funkcí sítě, zabezpečení a směrování a poskytuje jediné provozní rozhraní. Mezi tyto funkce patří připojení pobočky (prostřednictvím automatizace připojení ze zařízení Virtual WAN Partner, jako je SD-WAN nebo VPN CPE), připojení VPN site-to-site, připojení k síti Remote User VPN (Point-to-site), privátní (ExpressRoute), Připojení v rámci cloudu (přenositelné připojení pro virtuální sítě), propojení VPN ExpressRoute, směrování, brána firewall Azure, šifrování pro privátní připojení atd. Nemusíte mít všechny tyto případy použití začít používat virtuální WAN. Můžete začít pouze s jedním případem použití a upravit síť podle toho, jak se vyvíjí.

![Diagram virtuální sítě WAN](./media/virtual-wan-about/virtualwan1.png)

Nyní hovoří meo vzdálených uživatelů, umožňuje podívat se na to, co potřebujete, aby si vaše síť a provoz:

## <a name="set-up-remote-user-connectivity"></a><a name="connectivity"></a>Nastavení vzdáleného připojení uživatelů

Ke svým prostředkům v Azure se můžete připojit přes připojení IPsec/IKE (IKEv2) nebo OpenVPN. Tento typ připojení vyžaduje konfiguraci klienta VPN pro vzdáleného uživatele. Tento klient může být [klient Azure VPN](https://go.microsoft.com/fwlink/?linkid=2117554) nebo Klient OpenVPN nebo jakýkoli klient, který podporuje IKEv2. Další informace naleznete [v tématu Vytvoření připojení bodu k webu](virtual-wan-point-to-site-portal.md).

## <a name="connectivity-from-the-remote-user-to-on-premises"></a><a name="remote user connectivity"></a>Připojení od vzdáleného uživatele k místnímu

Máte zde dvě možnosti:

* Nastavte připojení site-to-site s libovolným existujícím zařízením VPN. Když připojíte zařízení VPN IPsec k centru Azure Virtual WAN, je automatické propojení mezi vpn uživatele bodu na místo (vzdálený uživatel) a vpn site-to-site. Další informace o tom, jak nastavit síť VPN mezi lokalitami z místního zařízení VPN do virtuální sítě Azure, najdete v [tématu Vytvoření připojení mezi lokalitami pomocí virtuální sítě WAN](virtual-wan-site-to-site-portal.md).

* Připojte okruh ExpressRoute k rozbočovači Virtuální WAN. Připojení okruhu ExpressRoute vyžaduje nasazení brány ExpressRoute ve virtuální síti WAN. Jakmile jste nasadili jeden, propojení mezi Point-to-site User VPN a ExpressRoute uživatele je automatické. Informace o vytvoření připojení ExpressRoute naleznete [v tématu Vytvoření připojení ExpressRoute pomocí programu Virtual WAN](virtual-wan-expressroute-portal.md). K připojení k virtuální síti Azure WAN můžete použít existující okruh ExpressRoute.

## <a name="existing-basic-virtual-wan-customer"></a><a name="basic vWAN"></a>Stávající základní zákazník virtuální sítě WAN

Základní virtuální síť WAN poskytuje pouze síť VPN mezi lokalitami. Aby se vzdálení uživatelé mohli připojit, budete muset upgradovat virtuální wan na standardní virtuální wan. Postup upgradu virtuální sítě WAN naleznete [v tématu Upgrade virtuální sítě WAN ze základní na standardní](upgrade-virtual-wan.md)

## <a name="additional-information"></a><a name="other considerations"></a>Další informace

Virtuální wan podporuje jedno rozbočovače pro oblast/umístění. Informace o poloze naleznete v článku [Virtuální partneři wan a umístění.](virtual-wan-locations-partners.md) Každý rozbočovač podporuje až 10 000 vzdálených uživatelských připojení, 1 000 větev připojení, čtyři okruhy ExpressRoute a až 500 připojení virtuální sítě. Při rozšiřování vzdálených uživatelů, pokud máte nějaké dotazy, neváhejte vyhledat pomoc azurevirtualwan@microsoft.comzasláním e-mailu na adresu . Pokud potřebujete technickou podporu, nezapomeňte otevřít lístek podpory z portálu Azure a nápověda bude na cestě.

## <a name="faq"></a><a name="faq"></a>Nejčastější dotazy

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>Další kroky

Další informace o virtuální masce WAN naleznete v [tématu Přehled virtuální sítě WAN](virtual-wan-about.md)