---
title: Možnosti připojení mezi sítě Azure | Dokumentace Microsoftu
description: Tato stránka popisuje scénář aplikací pro různé připojení k síti a řešení založené na Azure síťových funkcí.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/03/2019
ms.author: rambala
ms.openlocfilehash: 4923c7f2048b7368af6314d5e2288216115bc3bc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60368749"
---
# <a name="cross-network-connectivity"></a>Možnosti připojení mezi sítěmi

Společnost Fabrikam, Inc. má velký fyzickou přítomnost a nasazení Azure v oblasti východní USA. Společnost Fabrikam má back-end připojení mezi místní a nasazení Azure přes ExpressRoute. Podobně Contoso Ltd. má prezentace a nasazení Azure v oblasti západní USA. Contoso má back-end připojení mezi místní a nasazení Azure přes ExpressRoute.  

Společnost Fabrikam, Inc. získá Contoso Ltd. Po spojení Fabrikam chce propojení sítí. Scénář znázorňuje následující obrázek:

 [![1]][1]

Přerušované šipky uprostřed na výše uvedeném obrázku označují požadované síťové propojení. Existují tři typy požadovaného křížové připojení: Společnost Fabrikam a Contoso virtuálních sítí pro různé připojení (1), 2) mezi místní s místními a virtuálními sítěmi mezi připojí (který je, připojení Fabrikam místní sítě k virtuální síti Contoso a propojení Contoso místní sítě k virtuální síti Fabrikam) a (3) Fabrikam a Contoso připojení mezi místní sítí. 

V následující tabulce jsou uvedeny soukromý partnerský vztah ExpressRoute ze společnosti Contoso Ltd., před spojením směrovací tabulky.

[![2]][2]

Efektivní trasy virtuální počítač v rámci předplatného Contoso, před spojením v následující tabulce. Na tabulku je seznámen adresní prostor virtuální sítě a v místní síti Contoso, kromě výchozích šablon virtuálních počítačů ve virtuální síti. 

[![4]][4]

V následující tabulce jsou uvedeny soukromý partnerský vztah na ExpressRoute ze společnosti Fabrikam Inc., před spojením směrovací tabulky.

[![3]][3]

Efektivní trasy virtuální počítač v rámci předplatného společnosti Fabrikam, před spojením v následující tabulce. Na tabulku je seznámen adresní prostor virtuální sítě a v místní síti Fabrikam, kromě výchozích šablon virtuálních počítačů ve virtuální síti.

[![5]][5]

V tomto článku Podívejme se krok za krokem a diskuzi o tom, abyste dosáhli požadovaného křížové připojení pomocí následujících tipů síť Azure:

* [Partnerský vztah virtuální sítě][Virtual network peering] 
* [Virtuální síť připojení ExpressRoute][connection]
* [Globální dosah][Global Reach] 

## <a name="cross-connecting-vnets"></a>Propojení virtuálních sítí pro různé

Partnerské vztahy virtuálních sítí (VNet peering) poskytuje nejlepší výkon sítě a nejoptimálnější při propojení dvou virtuálních sítí. Partnerský vztah virtuální sítě podporuje partnerský vztah dvou virtuálních sítí v rámci stejné oblasti Azure (označovaného jako VNet peering) a ve dvou různých oblastech Azure (označovaného jako globální VNet peering). 

Nakonfigurujme globální VNet peering mezi virtuálními sítěmi ve společnosti Contoso a Fabrikam Azure předplatných. Jak vytvořit virtuální síť vytvoření partnerského vztahu mezi dvěma virtuálními sítěmi, najdete v článku [vytvoření partnerského vztahu virtuálních sítí] [ Configure VNet peering] článku.

Následující obrázek znázorňuje síťovou architekturu po konfiguraci globální VNet peering.

[![6]][6]

Následující tabulka obsahuje trasy známé Contoso předplatné virtuálního počítače. Věnujte pozornost poslední položky v tabulce. Tato položka je výsledek pro různé propojení virtuálních sítí.

[![7]][7]

Následující tabulka uvádí známé k předplatnému virtuálních počítačů společnosti Fabrikam trasy. Věnujte pozornost poslední položky v tabulce. Tato položka je výsledek pro různé propojení virtuálních sítí.

[![8]][8]

Přímo VNet peering propojuje dvě virtuální sítě (viz neexistují žádné další segment směrování *VNetGlobalPeering* položka ve výše uvedených dvou tabulkách)

## <a name="cross-connecting-vnets-to-the-on-premises-networks"></a>Různé připojení virtuálních sítí k místním sítím

Okruh ExpressRoute můžeme připojit k více virtuálním sítím. Zobrazit [předplatné a omezení služeb] [ Subscription limits] pro maximální počet virtuálních sítí, které lze připojit k okruhu ExpressRoute. 

Umožňuje připojení k předplatnému Fabrikam virtuální sítí umožňuje křížové připojení mezi virtuálními sítěmi a místními sítěmi okruh Fabrikam ExpressRoute na Contoso předplatné virtuální sítě a podobně okruh Contoso ExpressRoute. Pro připojení virtuální sítě k okruhu ExpressRoute v jiném předplatném, potřebujeme vytvořit a používat autorizaci.  Přečtěte si článek: [Připojení virtuální sítě k okruhu ExpressRoute][Connect-ER-VNet].

Následující obrázek ukazuje architekturu sítě po dokončení konfigurace ExpressRoute křížové připojení k virtuálním sítím.

[![9]][9]

V následující tabulce jsou uvedeny směrovací tabulky soukromého partnerského vztahu z ExpressRoute Contoso Ltd. po křížové připojení virtuální sítě do místní sítě prostřednictvím ExpressRoute. Podívejte se, že směrovací tabulka obsahuje trasy, které patří do virtuální sítě.

[![10]][10]

V následující tabulce jsou uvedeny směrovací tabulky privátní partnerský vztah ExpressRoute Fabrikam Inc. po křížové připojení virtuální sítě do místní sítě prostřednictvím ExpressRoute. Podívejte se, že směrovací tabulka obsahuje trasy, které patří do virtuální sítě.

[![11]][11]

Následující tabulka obsahuje trasy známé Contoso předplatné virtuálního počítače. Věnujte pozornost *Brána virtuální sítě* položky v tabulce. Virtuální počítač se zobrazí trasy pro místní sítě.

[![12]][12]

Následující tabulka uvádí známé k předplatnému virtuálních počítačů společnosti Fabrikam trasy. Věnujte pozornost *Brána virtuální sítě* položky v tabulce. Virtuální počítač se zobrazí trasy pro místní sítě.

[![13]][13]

>[!NOTE]
>Ve společnosti Fabrikam a/nebo Contoso odběry, které je také možné virtuální sítě paprsků k centru příslušné virtuální síti (hvězdicové návrhu není ukázáno v diagramy architektury v tomto článku). Křížové spojení mezi brány virtuální sítě centra ke službě ExpressRoute vám také umožní komunikaci mezi – východ a v západní rozbočovače a paprsky.
>

## <a name="cross-connecting-on-premises-networks"></a>Připojení místních sítí pro různé

Globální dosah ExpressRoute poskytuje připojení mezi místními sítěmi, které jsou připojené k jiné okruhy ExpressRoute. Nakonfigurujme globální dosah mezi Contoso a Fabrikam ExpressRoute okruhy. Okruhy ExpressRoute jsou umístěny v různých předplatných, potřebujeme vytvořit a používat autorizaci. Zobrazit [nakonfigurovat globální dosah ExpressRoute] [ Configure Global Reach] článku Pokyny krok za krokem.

Následující obrázek znázorňuje síťovou architekturu po konfiguraci globální dosah.

[![14]][14]

V následující tabulce jsou uvedeny směrovací tabulky soukromého partnerského vztahu z ExpressRoute Contoso Ltd. Po konfiguraci globální dosah. Podívejte se, že směrovací tabulka obsahuje trasy, které patří do místní sítě. 

[![15]][15]

V následující tabulce jsou uvedeny směrovací tabulky privátní partnerský vztah ExpressRoute Fabrikam Inc., až nakonfigurujete globální dosah. Podívejte se, že směrovací tabulka obsahuje trasy, které patří do místní sítě.

[![16]][16]

## <a name="next-steps"></a>Další postup

Zobrazit [virtuální sítě – nejčastější dotazy][VNet-FAQ]pro všechny další otázky týkající se virtuální sítě a partnerský vztah virtuální sítě. Zobrazit [ExpressRoute – nejčastější dotazy] [ ER-FAQ] pro připojení k nějaké další dotazy na ExpressRoute a virtuální síti.

Globální dosah nasazení na základě jednotlivé země. Pokud globální dosah je dostupný v zemích, které mají najdete v tématu [ExpressRoute globální dosah][Global Reach].

<!--Image References-->
[1]: ./media/cross-network-connectivity/premergerscenario.png "scénář aplikace"
[2]: ./media/cross-network-connectivity/contosoexr-rt-premerger.png "Contoso ExpressRoute směrovací tabulku před spojení"
[3]: ./media/cross-network-connectivity/fabrikamexr-rt-premerger.png "Fabrikam ExpressRoute směrovací tabulku před spojení"
[4]: ./media/cross-network-connectivity/contosovm-routes-premerger.png "trasy virtuálních počítačů společnosti Contoso před spojení"
[5]: ./media/cross-network-connectivity/fabrikamvm-routes-premerger.png "trasy virtuálních počítačů společnosti Fabrikam před spojení"
[6]: ./media/cross-network-connectivity/vnet-peering.png "architektura po vytvoření partnerského vztahu virtuální sítě"
[7]: ./media/cross-network-connectivity/contosovm-routes-peering.png "trasy virtuálních počítačů společnosti Contoso po vytvoření partnerského vztahu virtuální sítě"
[8]: ./media/cross-network-connectivity/fabrikamvm-routes-peering.png "trasy virtuálních počítačů společnosti Fabrikam po vytvoření partnerského vztahu virtuální sítě"
[9]: ./media/cross-network-connectivity/exr-x-connect.png "the Architecture po ExpressRoutes přes připojení"
[10]: ./media/cross-network-connectivity/contosoexr-rt-xconnect.png "Contoso ExpressRoute směrovací tabulky po křížové připojení ExR a virtuálními sítěmi"
[11]: ./media/cross-network-connectivity/fabrikamexr-rt-xconnect.png "Fabrikam ExpressRoute směrovací tabulky po křížové připojení ExR a virtuálními sítěmi"
[12]: ./media/cross-network-connectivity/contosovm-routes-xconnect.png "trasy virtuálních počítačů společnosti Contoso za různé spojovací ExR a virtuálními sítěmi"
[13]: ./media/cross-network-connectivity/fabrikamvm-routes-xconnect.png "trasy virtuálních počítačů společnosti Fabrikam po křížové připojení ExR a virtuálními sítěmi"
[14]: ./media/cross-network-connectivity/globalreach.png "the Architecture po konfiguraci globální dosah"
[15]: ./media/cross-network-connectivity/contosoexr-rt-gr.png "Contoso ExpressRoute směrovací tabulky po globální dosah"
[16]: ./media/cross-network-connectivity/fabrikamexr-rt-gr.png "Fabrikam ExpressRoute směrovací tabulky po globální dosah"

<!--Link References-->
[Virtual network peering]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview
[connection]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-global-reach
[Configure VNet peering]: https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[Subscription limits]: https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits
[Connect-ER-VNet]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[ER-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[VNet-FAQ]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq