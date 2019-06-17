---
title: Virtuální sítě WAN globální tranzitní síť architektura služby Azure | Dokumentace Microsoftu
description: Další informace o globální přenosu Síťová architektura pro virtuální sítě WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 05/20/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand global transit network architecture as it relates to Virtual WAN.
ms.openlocfilehash: 114d11f98c6181a03f5ce52527b5e2efea468c42
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65965984"
---
# <a name="global-transit-network-architecture-and-virtual-wan"></a>Architektura globálního přenosu sítě a virtuální sítě WAN

Architektura globálního přenosu sítě přijali podnikům umožňuje konsolidovat, připojení a řízení zaměřené na cloud moderní podnikové IT zázemí. V moderních podnikových zaměřené na cloud síťový provoz nemusí být backhauled k Ústředí. Globální tranzitní síť architektura je založená na známé koncepty sítě i nových konceptů, které jsou jedinečné pro cloud a cloudové architektury.

![Architektura](./media/virtual-wan-global-transit-network-architecture/architecture2.png)

**Obrázek 1: Globální tranzitní síť s virtuální sítě WAN**

Moderní podniky vyžadují všudypřítomná spojení mezi hyper distribuované aplikace, data a uživatelé v cloudu i lokálně. Azure virtuální sítě WAN umožňuje Síťová architektura globálního přenosu tím, že připojení všudypřítomná, any-to-any mezi globálně distribuovanou sadu virtuálních sítí, webů, aplikací a uživatelů. Azure virtuální síť WAN je služba spravovaná Microsoftem. Síťové součásti, které tato služba se skládá z jsou hostované a spravované microsoftem. Další informace o virtuální sítě WAN, najdete v článku [virtuální sítě WAN přehled](virtual-wan-about.md) článku.

V Azure virtuální sítě WAN architektuře oblasti Azure, které slouží jako rozbočovače, ke kterým můžete zvolit připojení větve. Po připojení se větve, můžete využít páteřní síť Azure k vytvoření větve to-VNet a volitelně větve do větve připojení.

Můžete vytvořit virtuální síť WAN vytvoření jedné virtuální sítě WAN centra v oblasti, která má největší počet paprsků (větví, virtuálních sítích, které uživatelé) a následným připojením paprsků, které jsou v jiných oblastech k rozbočovači. Případně pokud paprsky jsou geograficky distribuované, můžete také vytvořit instanci místní centra a propojení rozbočovače. Všechny jsou součástí stejné virtuální síť WAN jsou rozbočovače, ale můžou být spojené s jinou místní zásady.

## <a name="hub"></a>Střed a paprsek přenosu

Architektura globálního přenosu sítě je založená na modelu classic připojení střed a paprsek kde hostované v cloudu síť "centra" umožňuje tranzitivní připojení mezi koncovými body, které mohou být distribuovány do různých typů "paprsky".
  
V tomto modelu může být jednoho paprsku:

* Virtuální síť (Vnet)
* Fyzické pobočky
* Vzdálený uživatel
* Internet

![hvězdicové diagram globální přenosu](./media/virtual-wan-global-transit-network-architecture/architecture.png)

**Obrázek 2: Střed a paprsek**

Obrázek 2 znázorňuje logické zobrazení a pomocí globální sítě, ve kterém geograficky distribuovaných uživatelů, fyzických serverů a virtuálních sítí jsou propojeny prostřednictvím sítě Centrum hostované v cloudu. Tato architektura umožňuje připojení k logické jedním segmentem směrováním provozu mezi koncových bodech sítě. Paprsky jsou připojené k rozbočovači podle různých služeb Azure, sítě například ExpressRoute nebo site-to-síť VPN typu site fyzické větví, partnerský vztah virtuální sítě a sítě VPN point-to-site pro vzdálené uživatele.

## <a name="crossregion"></a>Připojení mezi různými oblastmi

Pro organizace cloudovou stopou obvykle následuje nároky na fyzické. Většina podniků přístup do cloudu z oblasti nejblíž k jejich fyzické síti a uživatelů. Jeden z klíčů objekty zabezpečení architektury globální sítě je chcete povolit připojení mezi různými oblastmi mezi koncových bodů a síťové entity. Cloudovou stopou může zahrnovat více oblastí. To znamená, že provoz z větve, která je připojená ke cloudu v jedné oblasti můžete oslovit jiné větve nebo virtuální sítě v jiné oblasti pomocí připojení k rozbočovači centra, která je aktuálně ve verzi preview.

## <a name="any"></a>Any-to-any připojení

Architektura globálního přenosu sítě umožňuje *any-to-any připojení* přes rozbočovač centrální síti. Tato architektura eliminuje nebo snižuje potřebu Vícecestná nebo částečné síť připojení modely, které jsou mnohem složitější a vytvářet a spravovat. Kromě toho směrování ovládací prvek v hub-and-spoke vs. síť sítě je snazší konfigurovat a udržovat.

Any-to-any připojení v rámci globální architektury umožňuje podnikům s globálně distribuovanou uživateli, větví, datových center, virtuální sítě a aplikace se připojit k sobě navzájem prostřednictvím centra přenosu. Průchod centra funguje jako globální přenosu systému.

![provoz cesty](./media/virtual-wan-global-transit-network-architecture/trafficpath.png)

**Obrázek 3: Cesty virtuálního provozu v síti WAN**

Azure virtuální sítě WAN podporuje následující cesty připojení globální přenosu. Písmena v závorkách se mapují na obr. 3.

* Větev to-VNet (a)  
* Větve do větve (b)
* Vzdálený uživatel to-VNet (c)
* Vzdálené uživatele na větve (d)
* Připojení typu VNet-to-VNet pomocí partnerského vztahu virtuálních sítí (e)
* ExpressRoute globálním dosahem 

### <a name="branchvnet"></a>Větev to-VNet

Větev to-VNet je primární cesta podporuje Azure virtuální sítě WAN. Tuto cestu můžete připojení pobočky k Azure IAAS firemních procesů, které jsou nasazené ve virtuálních sítích Azure. Větví může být připojen k virtuální síť WAN přes ExpressRoute nebo VPN typu site-to-site. Tranzit provoz do virtuální sítě, které jsou připojené k virtuální síti WAN hubs přes připojení typu VNet.

### <a name="branchbranch"></a>Větve do větve

Větví může být připojen k centra Azure virtuální sítě WAN, pomocí okruhy ExpressRoute a připojení VPN typu site-to-site. Větve můžete připojit k centru virtuální sítě WAN, který je v oblast nejbližší větev.

Tato možnost umožňuje podnikům využívat páteřní síť Azure pro připojení větví. Nicméně i když tato funkce je k dispozici, měli byste zvážit výhody propojení větví prostřednictvím Azure virtuální sítě WAN anebo privátní síť WAN.

### <a name="usertovnet"></a>Vzdálený uživatel to-VNet

Můžete povolit s přímým přístupem, zabezpečený vzdálený přístup k Azure pomocí připojení point-to-site od vzdáleného uživatele klienta na virtuální síť WAN. Vzdálení uživatelé organizace už žádné hairpin do cloudu s využitím firemní sítě VPN.

### <a name="usertobranch"></a>Vzdálený uživatel větve

Cesta vzdáleného uživatele větve umožňuje vzdáleným uživatelům, kteří používají připojení point-to-site k místní úlohy přístup ke službě Azure a aplikací přes cloud. Tato cesta zajišťuje flexibilitu vzdáleným uživatelům přístup k úlohám, které jsou nasazené v Azure a místní. Podniky můžete povolit službu centrální založené na cloudu zabezpečený vzdálený přístup v Azure virtuální sítě WAN.

### <a name="vnetvnet"></a>Připojení typu VNet-to-VNet přenosu pomocí VNet peering

Propojení virtuálních sítí mezi sebou za účelem podpory vícevrstvých aplikací, které jsou implementovány mezi několika virtuálními sítěmi, pomocí VNet peering. V tuto chvíli nepodporuje scénáři přenosu připojení typu VNet-to-VNet přes Azure virtuální sítě WAN, ale je na plány Azure do budoucna. Propojení virtuálních sítí pomocí VNet Peering je doporučená řešení pro virtuální sítě, které musejí být připojení k sobě navzájem. [Průchod branou](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) (v rámci partnerského vztahu virtuální sítě) se nevyžaduje pro virtuální sítě WAN vzhledem k tomu, že virtuální sítě WAN automaticky umožňuje průchod bránou.

### <a name="globalreach"></a>ExpressRoute globálním dosahem

ExpressRoute je privátní a odolná proti selháním způsob, jak připojit místní sítě ke cloudu Microsoftu. Globální dosah ExpressRoute je funkce doplňku pro ExpressRoute. Okruhy ExpressRoute tvořit privátní sítě mezi místními sítěmi můžete propojit s globální dosah. Větve, které jsou připojené k Azure virtuální sítě WAN pomocí ExpressRoute vyžadují ExpressRoute globální dosah komunikovat mezi sebou.

V tomto modelu každou větev, který je připojený k virtuální síti WAN hubu pomocí ExpressRoute můžete připojit k virtuálním sítím pomocí cesty větev to-VNet. Větve do větve provoz nebude přenosu centra, protože globální dosah ExpressRoute umožňuje více optimální cesta přes sítě WAN Azure.

## <a name="security"></a>Zabezpečení a zásad řízení

Virtuální síť centra propojení a potenciálně vidí všechny provoz. Může být místo pro hostitele centrální síťové funkce a služby, jako je takový cloudu směrování, síťové zásady a zabezpečení a řízení přístupu na Internet.

## <a name="next-steps"></a>Další postup

Vytvoření připojení pomocí virtuální sítě WAN.

* [Připojení Site-to-site pomocí virtuální sítě WAN](virtual-wan-site-to-site-portal.md)
* [Připojení point-to-site pomocí virtuální sítě WAN](virtual-wan-point-to-site-portal.md)
* [Připojení ExpressRoute využívající virtuální sítě WAN](virtual-wan-expressroute-portal.md)
