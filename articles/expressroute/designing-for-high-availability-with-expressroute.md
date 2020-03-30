---
title: 'Azure ExpressRoute: Návrh vysoké dostupnosti'
description: Tato stránka obsahuje doporučení architektury pro vysokou dostupnost při používání Azure ExpressRoute.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 06/28/2019
ms.author: rambala
ms.openlocfilehash: 4c3c6ae5fbdd91e6e44438be7fef2a3a91564a34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74076678"
---
# <a name="designing-for-high-availability-with-expressroute"></a>Návrh vysoké dostupnosti pomocí ExpressRoute

ExpressRoute je navržen pro vysokou dostupnost poskytovat operátor třídy privátní síť připojení k prostředkům společnosti Microsoft. Jinými slovy neexistuje žádný jediný bod selhání v cestě ExpressRoute v rámci sítě Společnosti Microsoft. Chcete-li maximalizovat dostupnost, zákazník a segment poskytovatele služeb okruhu ExpressRoute by měl být také navržen pro vysokou dostupnost. V tomto článku nejprve podívejme na aspekty síťové architektury pro vytváření robustní připojení k síti pomocí ExpressRoute, pak se podívejme na funkce jemného doladění, které vám pomohou zlepšit vysokou dostupnost okruhu ExpressRoute.


## <a name="architecture-considerations"></a>Aspekty architektury

Následující obrázek znázorňuje doporučený způsob připojení pomocí okruhu ExpressRoute pro maximalizaci dostupnosti okruhu ExpressRoute.

 [![1]][1 1 1 1.]

Pro vysokou dostupnost je nezbytné zachovat redundanci okruhu ExpressRoute v celé síti koncového konce. Jinými slovy je třeba zachovat redundanci v rámci místní sítě a nemělo by ohrozit redundanci v rámci sítě poskytovatele služeb. Zachování redundance na minimum znamená vyhnout se jednomu bodu selhání sítě. S redundantní napájení a chlazení pro síťová zařízení dále zlepší vysokou dostupnost.

### <a name="first-mile-physical-layer-design-considerations"></a>Aspekty návrhu fyzické vrstvy první míle

 Pokud ukončíte primární i sekundární připojení okruhů ExpressRoute na stejném zařízení customer premises (CPE), ohrožujete vysokou dostupnost v rámci místní sítě. Pokud navíc nakonfigurujete primární i sekundární připojení prostřednictvím stejného portu CPE (buď ukončením dvou připojení v rámci různých dílčích rozhraní, nebo sloučením dvou připojení v rámci partnerské sítě), vynucujete partnera vysokou dostupnost i v jejich síťovém segmentu. Tento kompromis je znázorněn na následujícím obrázku.

[![2]][2 2]

Na druhou stranu pokud ukončíte primární a sekundární připojení okruhů ExpressRoute v různých geografických umístěních, může být ohrožení výkonu připojení k síti. Pokud je provoz aktivně vyrovnán v rámci primárního a sekundárního připojení, která jsou ukončena v různých geografických umístěních, potenciální podstatný rozdíl v latenci sítě mezi těmito dvěma cestami by vedl k neoptimální síti Výkon. 

Geograficky redundantní návrh, naleznete [v tématu Návrh pro zotavení po havárii s ExpressRoute][DR].

### <a name="active-active-connections"></a>Aktivní a aktivní připojení

Síť společnosti Microsoft je nakonfigurována pro provoz primárních a sekundárních připojení obvodů ExpressRoute v aktivním aktivním režimu. Však prostřednictvím inzerování trasy můžete vynutit redundantní připojení okruhu ExpressRoute pracovat v režimu aktivní pasivní. Inzerce konkrétnější trasy a BGP AS cesta prepending jsou běžné techniky používané k tomu, aby jedna cesta přednost před druhou.

Chcete-li zlepšit vysokou dostupnost, doporučujeme provozovat obě připojení okruhu ExpressRoute v aktivním aktivním režimu. Pokud necháte připojení pracovat v aktivním aktivním režimu, bude síť Společnosti Microsoft vyrovnává provoz napříč připojeními na základě toku.

Spuštění primárních a sekundárních připojení okruhu ExpressRoute v režimu aktivní pasivní čelí riziku selhání obou připojení po selhání v aktivní cestě. Běžnými příčinami selhání při přepnutí jsou nedostatečná aktivní správa pasivního připojení a pasivní připojení inzerující zastaralé trasy.

Alternativně spuštění primární a sekundární připojení okruhu ExpressRoute v aktivním aktivním režimu, výsledkem je pouze asi polovina toků selhání a získání přesměrovány, po selhání připojení ExpressRoute. Aktivní aktivní režim tedy výrazně pomůže zlepšit střední dobu obnovení (MTTR).

### <a name="nat-for-microsoft-peering"></a>NAT pro partnerský vztah Microsoftu 

Partnerský vztah společnosti Microsoft je určen pro komunikaci mezi veřejnými koncovými body. Místní privátní koncové body jsou obvykle síťové adresy přeložené (NATed) s veřejnou IP adresou v síti zákazníků nebo partnerů před tím, než komunikují prostředpou partnerského vztahu Microsoftu. Za předpokladu, že používáte primární i sekundární připojení v aktivním aktivním režimu, kde a jak má nat vliv na to, jak rychle se obnovíte po selhání v jednom z připojení ExpressRoute. Na následujícím obrázku jsou znázorněny dvě různé možnosti například:

[![3]][3]

V možnosti 1 nat se použije po rozdělení provozu mezi primární a sekundární připojení ExpressRoute. Chcete-li splnit požadavky na stav na NAT, nezávislé nat fondy se používají mezi primární a sekundární zařízení tak, aby návratový provoz dorazí do stejné horečné zařízení, přes které tok odchozí.

V možnosti 2 se používá společný fond NAT před rozdělením provozu mezi primární a sekundární připojení ExpressRoute. Je důležité rozlišovat, že společný fond NAT před rozdělením provozu neznamená zavedení jediného bodu selhání, čímž by byla ohrožena vysoká dostupnost.

S možností 1, po selhání připojení ExpressRoute, schopnost dosáhnout odpovídající fond NAT je přerušena. Proto všechny přerušené toky musí být znovu stanoveny tcp nebo aplikační vrstvy po odpovídající časový rozsah okna. Pokud některý z fondů NAT se používají k frontendu některého z místních serverů a pokud odpovídající připojení se nezdaří, místní servery nelze dosáhnout z Azure, dokud připojení je pevná.

Vzhledem k tomu, že s možností 2 je nat dosažitelný i po selhání primárního nebo sekundárního připojení. Proto samotná síťová vrstva může přesměrovat pakety a pomoci rychlejší obnovení po selhání. 

> [!NOTE]
> Pokud použijete možnost NAT 1 (nezávislé fondy NAT pro primární a sekundární připojení ExpressRoute) a namapujete port IP adresy z jednoho z fondu NAT na místní server, server nebude dostupný prostřednictvím okruhu ExpressRoute, pokud odpovídající odpovídající připojení se nezdaří.
> 

## <a name="fine-tuning-features-for-private-peering"></a>Funkce jemného doladění pro soukromý partnerský vztah

V této části si můžeme přečíst volitelné (v závislosti na nasazení Azure a citlivosti na MTTR), které pomáhají zlepšit vysokou dostupnost okruhu ExpressRoute. Konkrétně pojďme zkontrolovat zónu na vědomí nasazení brány virtuální sítě ExpressRoute a obousměrné detekce předávání (BFD).

### <a name="availability-zone-aware-expressroute-virtual-network-gateways"></a>Brány virtuálnísítě ExpressRoute podporující zónu dostupnosti

Zóna dostupnosti v oblasti Azure je kombinací domény selhání a aktualizační domény. Pokud se rozhodnete pro zónově redundantní nasazení Azure IaaS, můžete také nakonfigurovat zóny redundantní virtuální síťové brány, které ukončí ExpressRoute privátní partnerský vztah. Další informace najdete v [tématu O zónově redundantních privátních síťových branách v zónách dostupnosti Azure][zone redundant vgw]. Pokud chcete nakonfigurovat zónově redundantní bránu virtuální sítě, přečtěte si, že [v zónách dostupnosti Azure vytvoříte zónovou virtuální síť ovou bránu s redundantní zónou][conf zone redundant vgw].

### <a name="improving-failure-detection-time"></a>Zlepšení doby detekce selhání

ExpressRoute podporuje BFD přes soukromý partnerský vztah. BFD snižuje dobu zjišťování v síti vrstvy 2 mezi Microsoft Enterprise Edge (MSEEs) a jejich sousedy Protokolu BGP na místní straně z přibližně 3 minut (výchozí) na méně než sekundu. Rychlá doba detekce selhání pomáhá urychlit obnovení selhání. Další informace naleznete v [tématu Konfigurace BFD přes ExpressRoute][BFD].

## <a name="next-steps"></a>Další kroky

V tomto článku jsme diskutovali o tom, jak navrhnout vysokou dostupnost připojení okruhu ExpressRoute. Bod partnerského vztahu okruhu ExpressRoute je připnutý k zeměpisné poloze, a proto může být ovlivněn autickým selháním, které má vliv na celé umístění. 

Návrh aspekty k vytvoření geograficky redundantní síťové připojení k páteřní síti společnosti Microsoft, které vydrží katastrofické selhání, které mají vliv na celou oblast, naleznete [v tématu Návrh zotavení po havárii s ExpressRoute soukromého partnerského vztahu][DR].

<!--Image References-->
[1]: ./media/designing-for-high-availability-with-expressroute/exr-reco.png "Doporučený způsob připojení pomocí ExpressRoute"
[2]: ./media/designing-for-high-availability-with-expressroute/suboptimal-lastmile-connectivity.png "Neoptimální konektivita poslední míle"
[3]: ./media/designing-for-high-availability-with-expressroute/nat-options.png "možnosti NAT"


<!--Link References-->
[zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways
[conf zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/create-zone-redundant-vnet-gateway
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[BFD]: https://docs.microsoft.com/azure/expressroute/expressroute-bfd
[DR]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering




