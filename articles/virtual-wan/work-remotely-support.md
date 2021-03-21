---
title: Azure Virtual WAN a vzdálená práce
description: Tato stránka popisuje, jak můžete využít Azure Virtual WAN, aby bylo možné vzdáleně pracovat v důsledku COVID-19 PANDEMIC.
services: virtual-wan
author: reyandap
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/22/2020
ms.author: cherylmc
ms.openlocfilehash: fc048f3da3156f5e17cfa32479b834b7320a60a2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96023482"
---
# <a name="azure-virtual-wan-and-supporting-remote-work"></a>Virtuální síť WAN Azure a podpora vzdálené práce

>[!NOTE]
>Tento článek popisuje, jak můžete využít Azure Virtual WAN, Azure, síť Microsoftu a partnerský ekosystém Azure k tomu, aby fungovaly vzdáleně a zmírnily problémy se sítí, ke kterým jste se přiCOVIDi, z důvodu krize v 19.
>

Používáte kódování pro poskytování připojení pro vzdálené uživatele?
Náhle vidíte nutnost podporovat přepětí uživatelů nad rámec toho, co jste naplánovali?
Potřebujete uživatele, aby se připojili z domova a nedostali jenom přístup do cloudu, ale mohli se taky dostat do místního prostředí?
Potřebujete, aby vaši vzdálení uživatelé měli přístup k prostředkům připojeným k privátní síti WAN?
Potřebujete, aby uživatelé měli přístup k prostředkům v cloudu, aniž byste museli nastavovat připojení mezi oblastmi?
Vzhledem k tomu, že tento globální PANDEMIC vytvoří v USA nebývalé změny, tým Azure Virtual WAN je tady, abyste mohli vytvářet požadavky na připojení.

Azure Virtual WAN je síťová služba, která přináší více funkcí směrování, zabezpečení a směrování, aby poskytovaly jediné provozní rozhraní. Mezi tyto funkce patří připojení k síti (prostřednictvím automatizace připojení z virtuálních zařízení WAN, jako jsou SD-WAN nebo VPN CPE), připojení VPN typu Site-to-site, vzdálené připojení VPN (Point-to-site), privátní připojení (ExpressRoute), konektivita v rámci cloudového připojení (přenosné připojení k virtuálním sítím), VPN ExpressRoute vzájemné propojení, směrování, Azure firewall, šifrování pro privátní Nemusíte mít všechny tyto případy použití, abyste mohli začít používat virtuální síť WAN. Můžete začít jenom s jedním případem použití a upravit síť při jejich vývojovém prostředí.

![Diagram virtuální sítě WAN](./media/virtual-wan-about/virtualwan1.png)

Teď mluví o vzdálených uživatelích, takže se můžete podívat na to, co potřebujete k tomu, abyste měli svoji síť v provozu a běželi:

## <a name="set-up-remote-user-connectivity"></a><a name="connectivity"></a>Nastavení připojení vzdáleného uživatele

K prostředkům v Azure se můžete připojit přes připojení IPsec/IKE (IKEv2) nebo OpenVPN. Tento typ připojení vyžaduje, aby byl klient VPN nakonfigurovaný pro vzdáleného uživatele. Tímto klientem může být klient [Azure VPN](https://go.microsoft.com/fwlink/?linkid=2117554) nebo klient OpenVPN nebo jakýkoli klient, který podporuje IKEv2. Další informace najdete v tématu [vytvoření připojení typu Point-to-site](virtual-wan-point-to-site-portal.md).

## <a name="connectivity-from-the-remote-user-to-on-premises"></a><a name="remote user connectivity"></a>Připojení od vzdáleného uživatele k místnímu uživateli

Tady máte dvě možnosti:

* Nastavte připojení typu Site-to-site k jakémukoli existujícímu zařízení VPN. Když připojíte zařízení VPN s protokolem IPsec ke službě Azure Virtual WAN hub, vzájemné propojení mezi uživatelským rozhraním VPN typu Point-to-Site (vzdálený uživatel) a VPN typu Site-to-site je automatický. Další informace o tom, jak nastavit síť VPN typu Site-to-Site z místního zařízení VPN na Azure Virtual WAN, najdete v tématu [vytvoření připojení typu Site-to-site pomocí virtuální sítě WAN](virtual-wan-site-to-site-portal.md).

* Připojte okruh ExpressRoute k virtuálnímu centru WAN. Připojení okruhu ExpressRoute vyžaduje nasazení brány ExpressRoute ve virtuální síti WAN. Jakmile nasadíte jednu, vzájemné propojení mezi uživatelem VPN typu Point-to-site a uživatelem ExpressRoute je automatický. Pokud chcete vytvořit připojení ExpressRoute, přečtěte si téma [vytvoření připojení ExpressRoute pomocí virtuální sítě WAN](virtual-wan-expressroute-portal.md). Existující okruh ExpressRoute můžete použít pro připojení k virtuální síti WAN Azure.

## <a name="existing-basic-virtual-wan-customer"></a><a name="basic vWAN"></a>Stávající základní zákazník Virtual WAN

Základní virtuální síť WAN poskytuje pouze síť Site-to-Site VPN. Aby se vzdálení uživatelé mohli připojit, musíte upgradovat virtuální síť WAN na standard Virtual WAN. Postup upgradu virtuální sítě WAN najdete v tématu [upgrade virtuální sítě WAN z úrovně Basic na standard](upgrade-virtual-wan.md) .

## <a name="additional-information"></a><a name="other considerations"></a>Další informace

Virtuální síť WAN podporuje jedno rozbočovače na oblast nebo umístění. Informace o umístění naleznete v článku [virtuální partneři a umístění sítě WAN](virtual-wan-locations-partners.md) . Každé centrum podporuje až 10 000 vzdálených uživatelských připojení, 1 000 připojení k síti, čtyři okruhy ExpressRoute a až 500 Virtual Network připojení. Při horizontálním navýšení kapacity, pokud máte nějaké dotazy, se váhají na to, že vám pošle e-mail odesláním e-mailu azurevirtualwan@microsoft.com . Pokud potřebujete technickou podporu, otevřete lístek podpory z Azure Portal a pomůže vám to.

## <a name="faq"></a><a name="faq"></a>Časté otázky

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>Další kroky

Další informace o virtuální síti WAN najdete v tématu [Virtual WAN – přehled](virtual-wan-about.md)