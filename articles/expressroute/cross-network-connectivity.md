---
title: Připojení mezi sítěmi Azure
description: Tato stránka popisuje scénář aplikace pro různé připojení k síti a řešení v závislosti na síťových funkcích Azure.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 04/03/2019
ms.author: rambala
ms.openlocfilehash: 48ec26cc98310dfeb61aa17018c940b431cfbcee
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "75644252"
---
# <a name="cross-network-connectivity"></a>Možnosti připojení mezi sítěmi

Společnost Fabrikam Inc. má rozsáhlou fyzickou přítomnost a nasazení Azure v Východní USA. Společnost Fabrikam má back-end propojení mezi místními a nasazeními Azure prostřednictvím ExpressRoute. Podobně společnost Contoso Ltd. má přítomnost a nasazení Azure v Západní USA. Společnost Contoso má back-end propojení mezi místními a nasazeními Azure prostřednictvím ExpressRoute.  

Společnost Fabrikam Inc. získá společnost Contoso Ltd. Po fúzi chce společnost Fabrikam propojit sítě. Následující obrázek znázorňuje scénář:

 [![1]][1]

Přerušované šipky uprostřed výše uvedeného obrázku označují požadovaná propojení sítě. K dispozici jsou tři typy vzájemného připojení: 1) Fabrikam a contoso virtuální sítě křížové propojení, 2) meziregionální místní a virtuální sítě křížové propojení (tj. připojení k místní síti Fabrikam k virtuální síti Contoso a připojení místní sítě společnosti Contoso k virtuální síti Fabrikam) a 3) Společnost Fabrikam a místní síť společnosti Contoso pro mezinárodní připojení. 

V následující tabulce je uvedena směrovací tabulka privátního partnerského vztahu ExpressRoute společnosti Contoso Ltd. před fúzí.

[![2]][2]

Následující tabulka ukazuje efektivní trasy virtuálního počítače v předplatném společnosti Contoso před fúzí. Na základě tabulky virtuální počítač ve virtuální síti ví o adresním prostoru virtuální sítě a místní síti společnosti Contoso, a to od výchozích. 

[![4]][4]

V následující tabulce je uvedena směrovací tabulka privátního partnerského vztahu ExpressRoute společnosti Fabrikam Inc. před fúzí.

[![3]][3]

Následující tabulka ukazuje efektivní trasy virtuálního počítače v předplatném Fabrikam před fúzí. Na základě tabulky virtuální počítač ve virtuální síti ví o adresním prostoru virtuální sítě a místní síti Fabrikam, a to od výchozích.

[![5]][5]

V tomto článku si projdeme krok za krokem a diskuzi o tom, jak dosáhnout požadovaných vzájemných připojení pomocí následujících funkcí Azure Network:

* [Partnerské vztahy virtuálních sítí][Virtual network peering] 
* [Připojení ExpressRoute virtuální sítě][connection]
* [Global Reach][Global Reach] 

## <a name="cross-connecting-vnets"></a>Vzájemné propojení virtuální sítě

Partnerský vztah virtuální sítě (VNet peering) poskytuje optimální a nejlepší výkon sítě při připojování dvou virtuálních sítí. Partnerský vztah virtuálních sítí podporuje partnerský vztah dvou virtuální sítě ve stejné oblasti Azure (obvykle se označuje jako partnerský vztah virtuálních sítí) a ve dvou různých oblastech Azure (obvykle se označuje jako globální partnerské vztahy virtuálních sítí). 

Pojďme nakonfigurovat globální partnerský vztah virtuálních sítí mezi virtuální sítě v předplatných společnosti Contoso a Fabrikam Azure. Postup vytvoření partnerského vztahu virtuální sítě mezi dvěma virtuálními sítěmi najdete v článku [vytvoření partnerského vztahu virtuální sítě][Configure VNet peering] .

Následující obrázek ukazuje architekturu sítě po konfiguraci globálního partnerského vztahu virtuální sítě.

[![6]][6]

Následující tabulka uvádí trasy známé virtuálnímu počítači předplatného společnosti Contoso. Věnujte pozornost poslednímu zadání tabulky. Tato položka je výsledkem vzájemného propojení virtuálních sítí.

[![čl]][7]

Následující tabulka uvádí trasy známé virtuálnímu počítači předplatného Fabrikam. Věnujte pozornost poslednímu zadání tabulky. Tato položka je výsledkem vzájemného propojení virtuálních sítí.

[![8]][8]

Partnerský vztah virtuálních sítí přímo propojuje dvě virtuální sítě (viz téma žádné další směrování pro záznam *VNetGlobalPeering* ve výše uvedených dvou tabulkách).

## <a name="cross-connecting-vnets-to-the-on-premises-networks"></a>Vzájemné propojení virtuální sítě s místními sítěmi

Okruh ExpressRoute můžeme propojit s několika virtuálními sítěmi. Maximální počet virtuálních sítí, které se dají připojit k okruhu ExpressRoute, najdete v tématu [omezení předplatného a služby][Subscription limits] . 

Pojďme připojit ExpressRoute okruh Fabrikam k virtuální síti předplatného společnosti Contoso a podobně contoso ExpressRoute okruh na virtuální síť předplatného společnosti Fabrikam a umožnit tak vzájemnou konektivitu mezi virtuálními sítěmi a místními sítěmi. Abychom mohli připojit virtuální síť k okruhu ExpressRoute v jiném předplatném, musíme vytvořit a použít autorizaci.  Podívejte se na článek: [připojení virtuální sítě k okruhu ExpressRoute][Connect-ER-VNet].

Následující obrázek ukazuje architekturu sítě po nakonfigurování ExpressRouteho vzájemného připojení k virtuálním sítím.

[![9]][9]

V následující tabulce je uvedena směrovací tabulka privátního partnerského vztahu ExpressRoute společnosti Contoso Ltd. po křížovém propojení virtuálních sítí s místními sítěmi prostřednictvím ExpressRoute. Podívejte se, že směrovací tabulka obsahuje trasy patřící do obou virtuálních sítí.

[![10pruhový]][10]

V následující tabulce je uvedena směrovací tabulka privátního partnerského vztahu ExpressRoute společnosti Fabrikam Inc. po křížovém propojení virtuálních sítí s místními sítěmi prostřednictvím ExpressRoute. Podívejte se, že směrovací tabulka obsahuje trasy patřící do obou virtuálních sítí.

[![odst]][11]

Následující tabulka uvádí trasy známé virtuálnímu počítači předplatného společnosti Contoso. Věnujte pozornost položkám *brány virtuální sítě* v tabulce. Virtuální počítač vidí trasy pro místní sítě.

[![12,5]][12]

Následující tabulka uvádí trasy známé virtuálnímu počítači předplatného Fabrikam. Věnujte pozornost položkám *brány virtuální sítě* v tabulce. Virtuální počítač vidí trasy pro místní sítě.

[![13,5]][13]

>[!NOTE]
>V předplatných společnosti Fabrikam a/nebo contoso můžete také paprsky virtuální sítě k příslušné virtuální síti centra (návrh centra a paprsků není znázorněn v diagramech architektury v tomto článku). Vzájemné propojení mezi branami virtuální sítě mezi rozbočovači a ExpressRoute budou také umožňovat komunikaci mezi východem a západkou rozbočovače a paprsky.
>

## <a name="cross-connecting-on-premises-networks"></a>Vzájemné propojení místních sítí

ExpressRoute Global Reach poskytuje připojení mezi místními sítěmi, které jsou připojené k různým okruhům ExpressRoute. Umožňuje nakonfigurovat Global Reach mezi ExpressRoute okruhy contoso a Fabrikam. Protože okruhy ExpressRoute jsou v různých předplatných, musíme vytvořit a použít autorizaci. Podrobné pokyny najdete v článku [konfigurace Global REACH ExpressRoute][Configure Global Reach] .

Následující obrázek ukazuje architekturu sítě po konfiguraci Global Reach.

[![čtrnáct]][14]

V následující tabulce je uvedena směrovací tabulka privátního partnerského vztahu ExpressRoute společnosti Contoso Ltd. po konfiguraci Global Reach. Podívejte se, že směrovací tabulka obsahuje trasy patřící do místních sítí. 

[![15]][15]

V následující tabulce je uvedena směrovací tabulka privátního partnerského vztahu ExpressRoute společnosti Fabrikam Inc. po konfiguraci Global Reach. Podívejte se, že směrovací tabulka obsahuje trasy patřící do místních sítí.

[![16bitovém]][16]

## <a name="next-steps"></a>Další kroky

Další otázky k virtuální síti a partnerskému vztahu VNet najdete v tématu [Nejčastější dotazy k virtuálním sítím][VNet-FAQ]. Další otázky k připojení ExpressRoute a virtuální sítě najdete v tématu [ExpressRoute – nejčastější][ER-FAQ] dotazy.

Global Reach se zavádějí do země nebo oblasti podle země nebo oblasti. Pokud chcete zjistit, jestli je Global Reach k dispozici v zemích nebo oblastech, které chcete, přečtěte si téma [ExpressRoute Global REACH][Global Reach].

<!--Image References-->
[1]: ./media/cross-network-connectivity/premergerscenario.png "scénář aplikace"
[2]: ./media/cross-network-connectivity/contosoexr-rt-premerger.png "tabulka směrování contoso ExpressRoute před fúzí"
[3]: ./media/cross-network-connectivity/fabrikamexr-rt-premerger.png. "ExpressRoute směrovací tabulka společnosti Fabrikam před fúzí"
[4]: ./media/cross-network-connectivity/contosovm-routes-premerger.png "trasy virtuálních počítačů contoso před fúzí"
[5]: ./media/cross-network-connectivity/fabrikamvm-routes-premerger.png "tras virtuálních počítačů Fabrikam před fúzí"
[6]: ./media/cross-network-connectivity/vnet-peering.png "architekturu po partnerském vztahu VNet"
[7]: ./media/cross-network-connectivity/contosovm-routes-peering.png "trasy virtuálních počítačů contoso po partnerském vztahu virtuální" sítě
[8]: ./media/cross-network-connectivity/fabrikamvm-routes-peering.png "tras virtuálních počítačů Fabrikam po partnerských sítích VNet"
[9]: ./media/cross-network-connectivity/exr-x-connect.png "Architektura po ExpressRoutes vzájemném připojení"
[10]: ./media/cross-network-connectivity/contosoexr-rt-xconnect.png "ExpressRoute směrovací tabulka společnosti Contoso po vzájemném propojení ExR a virtuální sítě"
[11]: ./media/cross-network-connectivity/fabrikamexr-rt-xconnect.png. "ExpressRoute směrovací tabulka společnosti Fabrikam po vzájemném propojení ExR a virtuální sítě"
[12]: ./media/cross-network-connectivity/contosovm-routes-xconnect.png "trasy virtuálních počítačů contoso po vzájemném propojení ExR a virtuální sítě"
[13]: ./media/cross-network-connectivity/fabrikamvm-routes-xconnect.png "trasy virtuálních počítačů Fabrikam po vzájemném propojení ExR a virtuální sítě"
[14]: ./media/cross-network-connectivity/globalreach.png "architektura po konfiguraci Global REACH"
[15]: ./media/cross-network-connectivity/contosoexr-rt-gr.png "ExpressRoute směrovací tabulka společnosti Contoso po Global REACH"
[16]: ./media/cross-network-connectivity/fabrikamexr-rt-gr.png "ExpressRoute směrování směrovací tabulky po Global REACH"

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