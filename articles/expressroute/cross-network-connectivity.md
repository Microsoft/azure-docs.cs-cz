---
title: Připojení Azure mezi sítěmi
description: Tato stránka popisuje scénář aplikace pro připojení k síti napříč sítěmi a řešení založené na síťových funkcích Azure.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 04/03/2019
ms.author: rambala
ms.openlocfilehash: 48ec26cc98310dfeb61aa17018c940b431cfbcee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644252"
---
# <a name="cross-network-connectivity"></a>Možnosti připojení mezi sítěmi

Společnost Fabrikam Inc. má velkou fyzickou přítomnost a nasazení Azure ve východní části USA. Společnost Fabrikam má back-endové připojení mezi místními nasazeními a nasazením Azure prostřednictvím služby ExpressRoute. Podobně contoso ltd. má přítomnost a nasazení Azure v západní USA. Contoso má back-end připojení mezi jeho místní nasazení a Azure nasazení přes ExpressRoute.  

Společnost Fabrikam Inc. kupuje společnost Contoso Ltd. Po fúzi chce společnost Fabrikam propojit sítě. Následující obrázek znázorňuje scénář:

 [![1]][1 1 1 1.]

Přerušované šipky uprostřed výše uvedeného obrázku označují požadované síťové propojení. Konkrétně existují tři typy křížených připojení žádoucí: 1) Fabrikam a Contoso Virtuální sítě cross connect, 2) Cross regionální místní a virtuální sítě mezi připojení (to znamená připojení Fabrikam místní sítě contoso virtuální sítě a připojení Contoso místní sítě na síť Fabrikam) a 3) Společnosti Fabrikam a Contoso v místní síti cross connect. 

V následující tabulce je uvedena směrovací tabulka soukromého partnerského vztahu ExpressRoute společnosti Contoso Ltd. před fúzí.

[![2]][2 2]

V následující tabulce jsou uvedeny efektivní trasy virtuálního virtuálního soudu v předplatném Contoso před fúzí. Podle tabulky si virtuální počítač na virtuální síti uvědomuje adresní prostor virtuální sítě a místní síť Contoso, kromě výchozích. 

[![4]][4.]

V následující tabulce je uvedena směrovací tabulka soukromého partnerského vztahu ExpressRoute společnosti Fabrikam Inc. před fúzí.

[![3]][3 3]

V následující tabulce jsou uvedeny efektivní trasy virtuálního virtuálního soudu v předplatném společnosti Fabrikam před fúzí. Podle tabulky si virtuální počítač na virtuální síti uvědomuje adresní prostor virtuální sítě a místní síť Fabrikam, kromě výchozích.

[![5]][5 5.]

V tomto článku si projdeme krok za krokem a prodiskutujeme, jak dosáhnout požadovaných křížových připojení pomocí následujících síťových funkcí Azure:

* [Partnerský vztah virtuální sítě][Virtual network peering] 
* [Připojení virtuální sítě ExpressRoute][connection]
* [Globální dosah][Global Reach] 

## <a name="cross-connecting-vnets"></a>Křížové připojení virtuálních sítí

Partnerský vztah virtuální sítě (partnerský vztah virtuální sítě) poskytuje nejoptimálnější a nejlepší síťový výkon při připojování dvou virtuálních sítí. Partnerský vztah virtuální sítě podporuje partnerský vztah dvou virtuálních sítí v rámci stejné oblasti Azure (běžně se nazývá partnerský vztah virtuální sítě) a ve dvou různých oblastech Azure (běžně nazývané globální partnerský vztah virtuální sítě). 

Pojďme nakonfigurovat globální partnerský vztah virtuální sítě mezi virtuálními sítěmi v předplatných Contoso a Fabrikam Azure. Jak vytvořit partnerský vztah virtuální sítě mezi dvěma virtuálními sítěmi, najdete v článku [Vytvoření partnerského vztahu virtuální sítě.][Configure VNet peering]

Následující obrázek znázorňuje síťovou architekturu po konfiguraci globálního partnerského vztahu virtuální sítě.

[![6]][6]

V následující tabulce jsou uvedeny trasy známé virtuálnímu virtuálnímu ms předplaceného sondy Contoso. Věnujte pozornost poslednímu vstupu do tabulky. Tato položka je výsledkem křížového propojení virtuálních sítí.

[![7]][7]

V následující tabulce jsou uvedeny trasy známé virtuálnímu virtuálnímu ms předplaceného předplatného společnosti Fabrikam. Věnujte pozornost poslednímu vstupu do tabulky. Tato položka je výsledkem křížového propojení virtuálních sítí.

[![8]][8]

Partnerský vztah virtuální sítě přímo propojuje dvě virtuální sítě (viz ve výše uvedených dvou tabulkách nejsou žádné další směrování pro položku *VNetGlobalPeering)*

## <a name="cross-connecting-vnets-to-the-on-premises-networks"></a>Křížení připojení virtuálních sítí k místním sítím

Můžeme připojit okruh ExpressRoute k více virtuálním sítím. Viz [Omezení předplatného a služeb][Subscription limits] pro maximální počet virtuálních sítí, které lze připojit k okruhu ExpressRoute. 

Pojďme připojit okruh Fabrikam ExpressRoute k virtuální síti předplatného Contoso a podobně okruhu Contoso ExpressRoute k virtuální síti předplatného Fabrikam, abychom umožnili křížové připojení mezi virtuálními sítěmi a místními sítěmi. Chcete-li připojit virtuální síť k okruhu ExpressRoute v jiném předplatném, musíme vytvořit a použít autorizaci.  Viz článek: [Připojení virtuální sítě k okruhu ExpressRoute][Connect-ER-VNet].

Následující obrázek znázorňuje síťovou architekturu po konfiguraci připojení ExpressRoute cross k virtuálním sítím.

[![9]][9]

V následující tabulce je uvedena směrovací tabulka soukromého partnerského vztahu ExpressRoute společnosti Contoso Ltd. po křížovém připojení virtuálních sítí k místním sítím prostřednictvím služby ExpressRoute. Podívejte se, že směrovací tabulka obsahuje trasy, které patří do obou virtuálních sítí.

[![10]][10]

V následující tabulce je uvedena směrovací tabulka soukromého partnerského vztahu ExpressRoute společnosti Fabrikam Inc. po křížovém připojení virtuálních sítí k místním sítím prostřednictvím služby ExpressRoute. Podívejte se, že směrovací tabulka obsahuje trasy, které patří do obou virtuálních sítí.

[![11]][11]

V následující tabulce jsou uvedeny trasy známé virtuálnímu virtuálnímu ms předplaceného sondy Contoso. Věnujte pozornost položkám *brány virtuální sítě* v tabulce. Virtuální počítač vidí trasy pro obě místní sítě.

[![12]][12]

V následující tabulce jsou uvedeny trasy známé virtuálnímu virtuálnímu ms předplaceného předplatného společnosti Fabrikam. Věnujte pozornost položkám *brány virtuální sítě* v tabulce. Virtuální počítač vidí trasy pro obě místní sítě.

[![13]][13]

>[!NOTE]
>V předplatných Fabrikam nebo Contoso můžete mít také virtuální sítě pro paprsky do příslušné virtuální sítě hubu (návrh rozbočovače a paprsku není znázorněno v diagramech architektury v tomto článku). Křížová připojení mezi bránami virtuální sítě rozbočovače na ExpressRoute také umožní komunikaci mezi východními a západními rozbočovači a paprsky.
>

## <a name="cross-connecting-on-premises-networks"></a>Křížové připojení místních sítí

ExpressRoute Global Reach poskytuje připojení mezi místními sítěmi, které jsou připojeny k různým okruhům ExpressRoute. Nakonfigurujeme globální dosah mezi okruhy Contoso a Fabrikam ExpressRoute. Vzhledem k tomu, že okruhy ExpressRoute jsou v různých předplatných, musíme vytvořit a použít autorizaci. Podrobné pokyny k [tématu Konfigurace globálního dosahu ExpressRoute.][Configure Global Reach]

Následující obrázek znázorňuje síťovou architekturu po konfiguraci globálního dosahu.

[![14]][14]

V následující tabulce je uvedena směrovací tabulka soukromého partnerského vztahu ExpressRoute společnosti Contoso Ltd. po konfiguraci globálního dosahu. Podívejte se, že směrovací tabulka obsahuje trasy, které patří do obou místních sítí. 

[![15]][15]

V následující tabulce je uvedena směrovací tabulka soukromého partnerského vztahu ExpressRoute společnosti Fabrikam Inc. po konfiguraci globálního dosahu. Podívejte se, že směrovací tabulka obsahuje trasy, které patří do obou místních sítí.

[![16]][16]

## <a name="next-steps"></a>Další kroky

Další otázky týkající se virtuální sítě a partnerského vztahu virtuální [sítě][VNet-FAQ]najdete v tématu nejčastější dotazy k virtuální síti. Další otázky týkající se ExpressRoute a připojení k virtuální síti najdete v nejčastějších dotazech k [ExpressRoute.][ER-FAQ]

Globální dosah se zavádí podle jednotlivých zemí nebo oblastí. Informace o tom, jestli je globální dosah dostupný v požadovaných zemích nebo oblastech, najdete v [tématu ExpressRoute Global Reach][Global Reach].

<!--Image References-->
[1]: ./media/cross-network-connectivity/premergerscenario.png "Scénář aplikace"
[2]: ./media/cross-network-connectivity/contosoexr-rt-premerger.png "Trasová tabulka Contoso ExpressRoute před fúzí"
[3]: ./media/cross-network-connectivity/fabrikamexr-rt-premerger.png "Trasová tabulka Fabrikam ExpressRoute před fúzí"
[4]: ./media/cross-network-connectivity/contosovm-routes-premerger.png "trasy virtuálních podniků Contoso před fúzí"
[5]: ./media/cross-network-connectivity/fabrikamvm-routes-premerger.png "tras virtuálních podniků Společnosti Fabrikam před fúzí"
[6]: ./media/cross-network-connectivity/vnet-peering.png "Architektura po partnerské síti virtuální sítě"
[7]: ./media/cross-network-connectivity/contosovm-routes-peering.png "tras virtuálního virtuálního vztahu Contoso po partnerské síti virtuální sítě"
[8]: ./media/cross-network-connectivity/fabrikamvm-routes-peering.png "tras virtuálních cest v síti Fabrikam po partnerské síti virtuální sítě"
[9]: ./media/cross-network-connectivity/exr-x-connect.png "Architektura po křížovém spojení ExpressRoutes"
[10]: ./media/cross-network-connectivity/contosoexr-rt-xconnect.png "Contoso ExpressRoute trasační tabulka po křížovém připojení ExR a virtuálních sítí"
[11]: ./media/cross-network-connectivity/fabrikamexr-rt-xconnect.png "Trasová tabulka Fabrikam ExpressRoute po křížovém připojení ExR a virtuálních sítí"
[12]: ./media/cross-network-connectivity/contosovm-routes-xconnect.png "tras virtuálního virtuálního zařízení Contoso po vzájemném připojení ExR a virtuálních sítí"
[13]: ./media/cross-network-connectivity/fabrikamvm-routes-xconnect.png "tras virtuálního virtuálního zařízení Společnosti Fabrikam po vzájemném připojení ExR a virtuálních sítí"
[14]: ./media/cross-network-connectivity/globalreach.png "Architektura po konfiguraci globálního dosahu"
[15]: ./media/cross-network-connectivity/contosoexr-rt-gr.png "Trasovací tabulka Contoso ExpressRoute po globálním dosahu"
[16]: ./media/cross-network-connectivity/fabrikamexr-rt-gr.png "Trasová tabulka Fabrikam ExpressRoute po globálním dosahu"

<!--Link References-->
[Virtual network peering]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview
[connection]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-global-reach
[Configure VNet peering]: https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[Subscription limits]: https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits
[Connect-ER-VNet]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[ER-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[VNet-FAQ]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq