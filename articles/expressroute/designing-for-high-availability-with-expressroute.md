---
title: Navrhování pro vysokou dostupnost s využitím Azure ExpressRoute | Dokumentace Microsoftu
description: Tato stránka obsahuje architektury doporučení pro zajištění vysoké dostupnosti při používání Azure ExpressRoute.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute
ms.topic: article
ms.workload: infrastructure-services
ms.date: 06/28/2019
ms.author: rambala
ms.openlocfilehash: 4984b30daf6170873cad9472bfed2d879af57efe
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466653"
---
# <a name="designing-for-high-availability-with-expressroute"></a>Navrhování pro vysokou dostupnost s využitím ExpressRoute

ExpressRoute je navržené pro zajištění vysoké dostupnosti a tím zajistit dopravce připojení privátní sítě na podnikové úrovni k prostředkům společnosti Microsoft. Jinými slovy neexistuje žádný jediný bod selhání v cestě ExpressRoute v rámci sítě Microsoftu. Pokud chcete maximalizovat dostupnost, zákazníka a část váš okruh ExpressRoute pro poskytovatele služby by měl také být navržen pro zajištění vysoké dostupnosti. V tomto článku, nejdřív Pojďme prozkoumat aspekty architektury sítě pro vytváření robustních síťové připojení pomocí ExpressRoute a Podívejme se na upřesnění funkce, které vám umožní zlepšit vysokou dostupnost váš okruh ExpressRoute.


## <a name="architecture-considerations"></a>Důležité informace o architektuře

Následující obrázek ukazuje doporučený postup pro připojení prostřednictvím okruhu ExpressRoute pro maximalizaci dostupnosti okruhu ExpressRoute.

 [![1]][1]

Pro zajištění vysoké dostupnosti je zásadní zachovat redundance okruhu ExpressRoute přes síť začátku do konce. Jinými slovy nemusíte udržovat redundance ve vaší místní síti a by neměl ohrozit redundance ve vaší síti poskytovatele služeb. Zachování redundance minimálně znamená, jak se vyhnout jediný bod selhání sítě. S redundantní napájení a chlazení pro síť, kterou bude zařízení dále zlepšit vysokou dostupnost.

### <a name="first-mile-physical-layer-design-considerations"></a>První aspekty návrhu pro přepočet mílí fyzickou vrstvu

 Pokud je ukončit i připojení primárních a sekundárních okruhů ExpressRoute v stejné zákazníka místní zařízení CPE (), jste omezení vysoké dostupnosti ve vaší místní síti. Kromě toho Pokud konfigurujete oba primárních a sekundárních připojení přes stejný port CPE, (nebo ukončení dvě připojení v rámci různých podrozhraní sloučením dvě připojení uvnitř sítě partnera), vynutíte partnera k ohrožení v jejich segmentu sítě také vysokou dostupnost. Toto ohrožení zabezpečení je znázorněn na následujícím obrázku.

[![2]][2]

Na druhé straně Pokud je ukončit primární a sekundární připojení okruhů ExpressRoute v různých geografických umístěních, pak vám může být tím bylo narušeno výkonu sítě za připojení. Pokud je provoz aktivně vyrovnávat zatížení napříč primární a sekundární připojení, které jsou v různých geografických umístěních byl ukončen, potenciální podstatný rozdíl v sítích s latencí mezi dvě cesty způsobí neoptimální sítě výkon. 

Aspekty návrhu geograficky redundantní, naleznete v tématu [návrhu zotavení po havárii s využitím ExpressRoute][DR].

### <a name="active-active-connections"></a>Připojení typu aktivní aktivní

MSN je nakonfigurován na provoz připojení primárních a sekundárních okruhů ExpressRoute v režimu aktivní aktivní. Prostřednictvím vaší trasy oznámení o inzerovaných programech, ale můžete vynutit redundantní připojení okruhu ExpressRoute k provozu v režimu aktivní pasivní. Inzerování konkrétnější trasy a protokolu BGP jako předřazení path je běžná technika, aby se dává přednost před druhou jednu cestu.

Pokud chcete zlepšit vysokou dostupnost, doporučuje se provoz připojení okruhu ExpressRoute v režimu aktivní aktivní. Pokud necháte připojení pracovat v režimu aktivní aktivní, MSN se zatížení můžete vyrovnávat provoz prostřednictvím připojení na základě jednotlivé toky.

Spuštění primární a sekundární připojení okruhu ExpressRoute v režimu aktivní pasivní tváře riziko připojení služeb při selhání po selhání v aktivní cesta. Nejčastější příčiny chyby při přepínání jsou chybějící aktivní správu pasivní připojení a pasivní připojení inzerování tras zastaralé.

Spuštění primární a sekundární připojení okruhu ExpressRoute v režimu aktivní aktivní, případně výsledkem přibližně půl toky služeb při selhání a získávání přesměrovány následující chybu připojení ExpressRoute. Díky tomu se režim aktivní – aktivní se výrazně zvýšit průměrný čas potřebný k obnovení (MTTR).

### <a name="nat-for-microsoft-peering"></a>NAT pro partnerský vztah Microsoftu 

Partnerský vztah Microsoftu je určená pro komunikaci mezi veřejné koncové body. Proto obvykle místní privátní koncové body jsou sítě přeložit adresy (NATed) s veřejnou IP adresu v zákazník nebo partner network před komunikují přes partnerský vztah Microsoftu. Za předpokladu, že používáte primárních a sekundárních připojení v režimu aktivní aktivní, kde a jak vám NAT má vliv na rychlost obnovení po selhání v jednom připojení ExpressRoute. Dvě různé možnosti NAT jsou znázorněné na následujícím obrázku:

[![3]][3]

V možnosti 1 NAT se použije po rozdělení přenos dat mezi primárních a sekundárních připojení ExpressRoute. Pro splnění požadavků stavové zařízení NAT, nezávislé fondy NAT se používají mezi primární a sekundární zařízení tak, že by návratový přenos dorazí na stejné hraniční zařízení, přes který egressed toku.

V možnosti 2 běžné fond NAT používá před rozdělením přenos dat mezi primárních a sekundárních připojení ExpressRoute. Je důležité rozlišovat běžné fond NAT před rozdělením provoz neznamená Úvod do jednoho místa selhání, a tím bylo narušeno vysokou dostupnost.

Možnost 1, následující chybu připojení ExpressRoute umožňuje oslovit odpovídající fond NAT se přeruší. Proto všechny toky přerušeno musí být znovu navázat buď protokol TCP nebo aplikační vrstvy následující časový limit odpovídající okno. Pokud některý z fondy NAT se používá front-endu některý z místních serverů a pokud odpovídající připojení neúspěšné, místní servery z Azure není dostupný, dokud se nevyřeší připojení.

Vzhledem k tomu možnost 2, překlad síťových adres je dostupný i po selhání primární nebo sekundární připojení. Proto samotné vrstvě sítě můžete přesměrovat paketů a pomáhají rychlejší obnovení po selhání. 

> [!NOTE]
> Pokud používáte NAT možnost 1 (nezávislé fondy NAT pro primární a sekundární připojení ExpressRoute) a mapují port IP adresu z jednoho z fondu NAT na místním serveru, server nebude dostupný přes ExpressRoute okruhu, kdy k odpovídající položce připojení se nezdaří.
> 

## <a name="fine-tuning-features-for-private-peering"></a>Upřesnění funkce pro soukromý partnerský vztah

V této části, dejte nám revize volitelné (v závislosti na nasazení vašeho řešení Azure a jak citlivá jste k MTTR) funkcí, které pomůžou vylepšit vysokou dostupnost váš okruh ExpressRoute. Konkrétně Pojďme se podívat na s ohledem na zónu nasazení brány virtuální sítě ExpressRoute a obousměrné předávání detekce (BFD).

### <a name="availability-zone-aware-expressroute-virtual-network-gateways"></a>Zóna dostupnosti clustery brány virtuální sítě ExpressRoute

Zónu dostupnosti, do oblasti Azure je kombinace doména selhání a aktualizační doména. Pokud se rozhodnete pro zónově redundantní nasazení Azure IaaS, můžete také nakonfigurovat brány zónově redundantní virtuální sítě, které ukončit privátního partnerského vztahu ExpressRoute. Další informace najdete v tématu [o branách zónově redundantní virtuálních sítí v zónách dostupnosti Azure][zone redundant vgw]. To configure zone-redundant virtual network gateway, see [Create a zone-redundant virtual network gateway in Azure Availability Zones][conf zone redundant vgw].

### <a name="improving-failure-detection-time"></a>Zlepšení čas detekce selhání

ExpressRoute podporuje BFD přes privátní partnerský vztah. BFD snižuje čas detekce selhání v síti vrstvy 2 mezi Microsoft Edge Enterprise (Msee) a jeho okolím protokolu BGP na straně místní z přibližně 3 minut (výchozí) na menší než druhý. Rychlá selhání detekováním pomáhá hastening obnovení po selhání. Další informace najdete v tématu [konfigurace BFD přes ExpressRoute][BFD].

## <a name="next-steps"></a>Další postup

V tomto článku jsme probírali jak navrhovat pro zajištění vysoké dostupnosti připojením k okruhu ExpressRoute. Jako bod partnerského vztahu okruhu ExpressRoute je připnutá k zeměpisné umístění a proto může mít vliv závažnému selhání, který ovlivňuje celou umístění. 

Aspekty návrhu pro sestavení geograficky redundantní síťové připojení k páteřní infrastrukturu Microsoftu, který dokázal zvládnout situaci katastrofických selhání, které ovlivňuje celou oblast, najdete v části [návrhu zotavení po havárii sprosoukromýpartnerskývztahExpressRoute][DR].

<!--Image References-->
[1]: ./media/designing-for-high-availability-with-expressroute/exr-reco.png "doporučuje způsob připojení pomocí ExpressRoute"
[2]: ./media/designing-for-high-availability-with-expressroute/suboptimal-lastmile-connectivity.png "Suboptimal poslední míli připojení"
[3]: ./media/designing-for-high-availability-with-expressroute/nat-options.png "možností NAT"


<!--Link References-->
[zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways
[conf zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/create-zone-redundant-vnet-gateway
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[BFD]: https://docs.microsoft.com/azure/expressroute/expressroute-bfd
[DR]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering




