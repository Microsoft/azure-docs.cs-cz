---
title: 'Azure ExpressRoute: návrh pro vysokou dostupnost'
description: Tato stránka poskytuje doporučení pro architekturu pro vysokou dostupnost při používání Azure ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 06/28/2019
ms.author: duau
ms.openlocfilehash: d6e63ad5781b0b8d6f46aee1cb88373721bb9dfc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89397895"
---
# <a name="designing-for-high-availability-with-expressroute"></a>Návrh pro zajištění vysoké dostupnosti pomocí ExpressRoute

ExpressRoute je navržená tak, aby poskytovala vysokou dostupnost k zajištění privátní síťové připojení k prostředkům Microsoftu. Jinými slovy, v ExpressRoute cestě v síti Microsoftu neexistuje jediný bod selhání. Pro maximalizaci dostupnosti by měl být pro zákazníky a segmenty poskytovatele služeb vašeho okruhu ExpressRoute také navržený pro vysokou dostupnost. V tomto článku se nejdřív podívejme na požadavky na architekturu sítě při vytváření robustního síťového připojení pomocí ExpressRoute a potom se podíváme na funkce s vyladěním, které vám pomůžou zlepšit vysokou dostupnost vašeho okruhu ExpressRoute.

>[!NOTE]
>Koncepty popsané v tomto článku platí stejně, když je v rámci virtuální sítě WAN nebo mimo ni vytvořen okruh ExpressRoute.
>

## <a name="architecture-considerations"></a>Požadavky architektury

Následující obrázek znázorňuje doporučený způsob připojení pomocí okruhu ExpressRoute pro maximalizaci dostupnosti okruhu ExpressRoute.

 [![1]][1]

Pro zajištění vysoké dostupnosti je nezbytné zachovat redundanci okruhu ExpressRoute napříč koncovou sítí. Jinými slovy, je třeba zachovat redundanci v rámci vaší místní sítě a neměli byste v síti poskytovatele služeb mít redundanci. Udržování redundance na minimum zahrnuje zamezení selhání v jednom bodě sítě. Díky redundantnímu napájení a chlazení síťových zařízení bude lépe vylepšit vysokou dostupnost.

### <a name="first-mile-physical-layer-design-considerations"></a>Faktory návrhu fyzické vrstvy první km

 Pokud ukončíte primární i sekundární připojení okruhu ExpressRoute na stejné zákaznické zařízení (CPE), dojde k narušení vysoké dostupnosti v rámci vaší místní sítě. Kromě toho, pokud nakonfigurujete primární i sekundární připojení přes stejný port CPE (buď ukončením těchto dvou připojení v různých podrozhraních, nebo sloučením dvou připojení v rámci partnerské sítě), vynutíte, aby partner nastavil vysokou dostupnost v jejich segmentu sítě také. Toto ohrožení je znázorněno na následujícím obrázku.

[![2]][2]

Na druhé straně, pokud zrušíte primární a sekundární připojení okruhu ExpressRoute v různých geografických umístěních, může dojít k narušení výkonu sítě. Pokud je provoz aktivně vyrovnaný mezi primárním a sekundárním připojením, která se ukončí v různých geografických umístěních, může dojít k výraznému rozdílu v latenci sítě mezi dvěma cestami, což by způsobilo zhoršený výkon sítě. 

Informace o geograficky redundantním návrhu najdete v tématu [navrhování pro zotavení po havárii pomocí ExpressRoute][DR].

### <a name="active-active-connections"></a>Aktivní – aktivní připojení

Síť Microsoft je nakonfigurována pro provoz primárních a sekundárních připojení okruhů ExpressRoute v režimu aktivní-aktivní. Nicméně prostřednictvím inzerce tras můžete vynutit redundantní připojení okruhu ExpressRoute, aby fungovala v režimu aktivní-pasivní. Vytváření inzerce konkrétnějších tras a protokolu BGP jako nedokončené cesty jsou běžné techniky, pomocí kterých je možné nastavit jednu cestu předností nad druhou.

Pro zlepšení vysoké dostupnosti doporučujeme provozovat připojení okruhu ExpressRoute v režimu aktivní-aktivní. Pokud povolíte připojení v režimu aktivní-aktivní, bude síť Microsoftu vyrovnávat zatížení napříč připojeními na základě jednotlivých toků.

Při spuštění primárního a sekundárního připojení okruhu ExpressRoute v aktivním pasivním režimu čelí riziko, že připojení selže po selhání v aktivní cestě. Běžnými příčinami selhání při přepínání je neexistence aktivní správy pasivního připojení a pasivní připojení, které inzeruje zastaralé trasy.

Další možností je spuštění primárního a sekundárního připojení okruhu ExpressRoute v režimu aktivní-aktivní. výsledkem bude jenom zhruba polovinu selhání toků a jejich přesměrování, a to po selhání připojení ExpressRoute. Proto režim aktivní – aktivní bude významně pomáhat zlepšit střední dobu obnovení (MTTR).

### <a name="nat-for-microsoft-peering"></a>Překlad adres (NAT) pro partnerský vztah Microsoftu 

Partnerský vztah Microsoftu je určený pro komunikaci mezi veřejnými koncovými body. To znamená, že místní privátní koncové body jsou přeložené ze sítě (NATed) s veřejnou IP adresou v síti zákazníka nebo partnera předtím, než budou komunikovat s partnerským vztahem Microsoftu. Za předpokladu, že používáte primární i sekundární připojení v režimu aktivní-aktivní, kde a jak vaše NAT má vliv na to, jak rychle obnovujete po selhání jednoho z ExpressRoute připojení. Na následujícím obrázku jsou znázorněny dvě různé možnosti překladu adres (NAT):

[![1]][3]

V možnosti 1 se překlad adres (NAT) použije po rozdělení provozu mezi primárním a sekundárním připojením ExpressRoute. Aby bylo možné splnit stavové požadavky překladu adres (NAT), používají se nezávislé fondy NAT mezi primárním a sekundárním zařízením, takže návratová přenosová data přicházejí do stejného hraničního zařízení, přes které tok vzdává výstup.

V možnosti 2 se k rozdělení provozu mezi primárním a sekundárním připojením ExpressRoute používá společný fond NAT. Je důležité rozlišovat, že společný fond překladu adres (NAT) před rozdělením provozu neznamená velký počet selhání, což by ohrozilo vysokou dostupnost.

U možnosti 1 se po selhání připojení ExpressRoute schopnost spojit s odpovídajícím fondem překladu adres (NAT). Proto musí být všechny přerušené toky znovu navázány pomocí protokolu TCP nebo aplikační vrstvy, a to za odpovídajícím časovým limitem okna. Pokud se některý z fondů NAT používá pro front-endové servery a v případě, že by odpovídající připojení nebylo úspěšné, nejdou z Azure získat místní servery, dokud nebude připojení vyřešené.

V případě možnosti 2 je překlad adres (NAT) dosažitelný i po selhání primárního nebo sekundárního připojení. Proto může tato síťová vrstva znovu směrovat pakety a urychlit obnovení po selhání. 

> [!NOTE]
> Pokud použijete možnost NAT 1 (nezávislé fondy NAT pro primární a sekundární připojení ExpressRoute) a namapujete port IP adresy z jednoho z fondů překladu adres na místní server, server nebude dostupný přes okruh ExpressRoute, pokud se odpovídající připojení nezdaří.
> 

## <a name="fine-tuning-features-for-private-peering"></a>Vyladitelné funkce pro privátní partnerské vztahy

V této části si můžeme předejít volitelné (v závislosti na vašem nasazení Azure a MTTR) funkce, které pomáhají zlepšit vysokou dostupnost vašeho okruhu ExpressRoute. Konkrétně se podívejme na nasazení bran virtuální sítě ExpressRoute s ohledem na zóny a na detekci obousměrného přesměrování (BFD).

### <a name="availability-zone-aware-expressroute-virtual-network-gateways"></a>Brány virtuální sítě podporující zóny dostupnosti ExpressRoute

Zóna dostupnosti v oblasti Azure je kombinací domény selhání a aktualizační domény. Pokud se rozhodnete pro nasazení služby Azure IaaS v zóně redundantní, můžete taky chtít nakonfigurovat brány redundantní virtuální sítě zóny, které budou koncovým partnerským vztahem ExpressRoute ukončovat. Další informace najdete v tématu [informace o branách virtuální sítě redundantních v zóně v zóny dostupnosti Azure][zone redundant vgw]. Postup konfigurace brány redundantní virtuální sítě v zóně najdete [v tématu Vytvoření brány redundantní virtuální sítě v zóny dostupnosti Azure][conf zone redundant vgw].

### <a name="improving-failure-detection-time"></a>Zlepšení doby detekce selhání

ExpressRoute podporuje BFD prostřednictvím privátního partnerského vztahu. BFD snižuje dobu detekce selhání během sítě vrstvy 2 mezi sítí Microsoft Enterprise Edge (směrovači msee) a sousedními sousedi protokolu BGP na místní straně od přibližně 3 minut (výchozí) po méně než druhé. Čas rychlého zjištění selhání pomáhá při hastening obnovení. Další informace najdete v tématu [Configuring BFD over ExpressRoute][BFD].

## <a name="next-steps"></a>Další kroky

V tomto článku jsme probrali, jak navrhnout vysokou dostupnost připojení okruhu ExpressRoute. Bod partnerského vztahu okruhu ExpressRoute je připnutý k geografickému umístění, a proto by mohl být ovlivněn závažnou chybou, která ovlivňuje celé umístění. 

Pokyny k návrhu pro sestavení geograficky redundantního síťového připojení k páteřní síti Microsoftu, která může odolat závažným chybám, které ovlivňují celou oblast, najdete v tématu [navrhování pro zotavení po havárii s privátním partnerským vztahem ExpressRoute][DR].

<!--Image References-->
[1]: ./media/designing-for-high-availability-with-expressroute/exr-reco.png "doporučený způsob připojení pomocí ExpressRoute"
[2]: ./media/designing-for-high-availability-with-expressroute/suboptimal-lastmile-connectivity.png "neoptimální připojení poslední míle"
[3]: ./media/designing-for-high-availability-with-expressroute/nat-options.png "Možnosti NAT"


<!--Link References-->
[zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways
[conf zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/create-zone-redundant-vnet-gateway
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[BFD]: https://docs.microsoft.com/azure/expressroute/expressroute-bfd
[DR]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering




