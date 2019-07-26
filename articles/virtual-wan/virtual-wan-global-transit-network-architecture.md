---
title: Azure Virtual WAN – architektura globální přenosové sítě | Microsoft Docs
description: Další informace o architektuře globální přenosové sítě pro virtuální síť WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 07/23/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand global transit network architecture as it relates to Virtual WAN.
ms.openlocfilehash: 2376c77ecc328788c842e045aafb618cbad39b0e
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68421425"
---
# <a name="global-transit-network-architecture-and-virtual-wan"></a>Globální přenosová architektura sítě a virtuální síť WAN

Podniková architektura pro cloudovou architekturu se přijímá v rámci konsolidace, připojení a řízení moderního podnikání zaměřeného na Cloud. V moderním cloudově orientovaném podniku nemusí být síťový provoz vysídelý. Architektura globální přenosové sítě je založená na známých konceptech sítě a nových konceptech, které jsou jedinečné pro cloudové a cloudové architektury.

![Architektura](./media/virtual-wan-global-transit-network-architecture/architecture2.png)

**Obrázek 1: Globální tranzitní síť s virtuální sítí WAN**

Moderní podniky vyžadují připojení všudypřítomný mezi aplikacemi, daty a uživateli distribuovanými na Hyper-v cloudu a místním prostředím. Azure Virtual WAN umožňuje globální přenosovou architekturu sítě tím, že umožňuje všudypřítomný připojení mezi globálně distribuovanými sadami virtuální sítě, weby, aplikacemi a uživateli. Azure Virtual WAN je služba spravovaná Microsoftem. Všechny síťové součásti, ze kterých se tato služba skládá, je hostována a spravována společností Microsoft. Další informace o virtuální síti WAN najdete v článku [Přehled virtuálních sítí WAN](virtual-wan-about.md) .

V architektuře Azure Virtual WAN oblasti Azure slouží jako rozbočovače, na které se můžete rozhodnout připojit své větve. Po připojení větví můžete využít páteřní síť Azure k navázání připojení mezi virtuálními sítěmi a případně připojením mezi větvemi.

Virtuální síť WAN můžete vytvořit tak, že vytvoříte jedno virtuální centrum sítě WAN v oblasti s největším počtem paprsků (větví, virtuální sítě, uživatelů) a potom propojíte paprsky, které jsou v jiných oblastech, do centra. Případně, pokud jsou paprsky geograficky distribuované, můžete také vytvářet instance regionálních center a propojit centra. Všechna centra jsou součástí stejné virtuální sítě WAN, ale můžou být přidružená k různým oblastním zásadám.

## <a name="hub"></a>Přenos hub a paprsků

Architektura globální přenosové sítě je založená na klasickém modelu připojení hub a hvězdicové sítě, ve kterém cloudová hostovaná síť "centrum" umožňuje přenosné připojení mezi koncovými body, které mohou být distribuovány napříč různými typy "paprsků".
  
V tomto modelu může být paprsek:

* Virtuální síť (virtuální sítě)
* Lokalita fyzické pobočky
* Vzdálený uživatel
* Internet

![Diagram globálního přenosu centra a paprsků](./media/virtual-wan-global-transit-network-architecture/architecture.png)

**Obrázek 2: Střed a paprskový**

Obrázek 2 ukazuje logické zobrazení globální sítě, ve které jsou geograficky rozmístěné uživatele, fyzické lokality a virtuální sítě propojeny prostřednictvím síťového centra hostovaného v cloudu. Tato architektura umožňuje v rámci logického připojení mezi koncovými body sítě logický přenos s jedním směrováním. Paprsky jsou připojené k centru pomocí různých síťových služeb Azure, jako jsou ExpressRoute nebo site-to-VPN pro fyzické větve, VNet peering pro virtuální sítě a VPN typu Point-to-site pro vzdálené uživatele.

## <a name="crossregion"></a>Připojení mezi oblastmi

V případě podniku se cloudové nároky typicky řídí fyzickými nároky. Většina podniků přistupuje ke cloudu z oblasti, která je nejblíže jejich fyzické lokalitě a uživatelům. Jedním z klíčových objektů globální síťové architektury je povolit připojení mezi oblastmi mezi síťovými entitami a koncovými body. Cloudové nároky mohou zahrnovat více oblastí. To znamená, že provoz z větve, která je připojená ke cloudu v jedné oblasti, může oslovit jinou větev nebo virtuální síť v jiné oblasti pomocí připojení typu hvězdicové centrum, které je aktuálně v našem plánu.

## <a name="any"></a>Jakékoli připojení

Globální přenosová architektura sítě umožňuje *jakékoli připojení* prostřednictvím centrálního síťového rozbočovače. Tato architektura eliminuje nebo omezuje nutnost vytvoření celé sítě nebo částečně propojených modelů sítě, které jsou složitější pro sestavování a údržbu. Kromě toho je snazší konfigurovat a udržovat řízení směrování v sítích s rozbočovačem a s mřížkou.

Jakékoli připojení, v kontextu globální architektury, umožňuje podnik s globálně distribuovanými uživateli, pobočkami, datacentry, virtuální sítě a aplikacemi, aby se vzájemně připojovali prostřednictvím centra přenosů. Centrum přenosů funguje jako globální tranzitní systém.

![cesty provozu](./media/virtual-wan-global-transit-network-architecture/trafficpath.png)

**Obrázek 3: Cesty provozu virtuální sítě WAN**

Azure Virtual WAN podporuje následující cesty pro připojení globálního přenosu. Písmena v závorkách se mapují na obrázek 3.

* Větev do virtuální sítě (a)  
* Větev do větve (b)
* Vzdálený uživatel – virtuální síť (c)
* Vzdálený uživatel-do větve (d)
* VNet-to-VNet pomocí partnerského vztahu virtuální sítě (e)
* ExpressRoute Global Reach 

### <a name="branchvnet"></a>Větev do VNet

Větev-to-VNet je primární cesta podporovaná službou Azure Virtual WAN. Tato cesta umožňuje připojit větve k podnikovým úlohám Azure IAAS nasazeným v Azure virtuální sítě. Větve se dají připojit k virtuální síti WAN přes ExpressRoute nebo VPN typu Site-to-site. Přenos dat do virtuální sítě, která jsou připojená k virtuálním rozbočovačům sítě WAN prostřednictvím připojení virtuální sítě.

### <a name="branchbranch"></a>Větev do větve

Větve se dají připojit ke službě Azure Virtual WAN pomocí okruhů ExpressRoute a připojení VPN typu Site-to-site. Větve můžete připojit k virtuálnímu rozbočovači WAN, který je v oblasti nejbližší k této větvi.

Tato možnost umožňuje podnikům využít páteřní síť Azure pro připojení větví. I když je tato možnost k dispozici, měli byste zvážit výhody propojení větví přes Azure Virtual WAN vs. pomocí privátní sítě WAN.

### <a name="usertovnet"></a>Vzdálený uživatel-síť VNet

Můžete povolit přímý a zabezpečený vzdálený přístup k Azure pomocí připojení typu Point-to-site od vzdáleného uživatele klienta k virtuální síti WAN. Podnikoví vzdálení uživatelé už nemusí hairpin do cloudu pomocí podnikové sítě VPN.

### <a name="usertobranch"></a>Vzdálený uživatel – větev

Cesta vzdáleného uživatele k větvi umožňuje vzdáleným uživatelům, kteří používají připojení Point-to-site k Azure, přistupovat k místním úlohám a aplikacím prostřednictvím přenosu v cloudu. Tato cesta dává vzdáleným uživatelům flexibilitu při přístupu k úlohám, které jsou nasazené v Azure i v místním prostředí. Podniky můžou povolit centrální cloudovou službu vzdáleného přístupu založenou na Azure Virtual WAN.

### <a name="vnetvnet"></a>Tranzitní síť VNet-to-VNet pomocí partnerského vztahu virtuální sítě

K vzájemnému propojení virtuální sítě, aby bylo možné podporovat vícevrstvé aplikace, které jsou implementované napříč více virtuální sítě, použijte partnerský vztah virtuálních sítí. Scénář přenosu typu VNet-to-VNet prostřednictvím Azure Virtual WAN se v současnosti nepodporuje, ale je k dispozici v plánu Azure. Připojení virtuální sítě prostřednictvím partnerského vztahu virtuálních sítí je doporučené řešení pro virtuální sítě, které je potřeba vzájemně propojit. [Přenos brány](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) (v kontextu partnerského vztahu virtuálních sítí) se virtuální síť WAN nevyžaduje, protože virtuální síť WAN automaticky povoluje přenos brány.

### <a name="globalreach"></a>ExpressRoute Global Reach

ExpressRoute je soukromý a odolný způsob, jak připojit vaše místní sítě k Microsoft Cloud. ExpressRoute Global Reach je doplňková funkce pro ExpressRoute. Pomocí Global Reach můžete propojit okruhy ExpressRoute dohromady a vytvořit tak soukromou síť mezi místními sítěmi. Větve, které jsou připojené k Azure Virtual WAN pomocí ExpressRoute, vyžadují vzájemnou komunikaci ExpressRoute Global Reach.

V tomto modelu se může každá větev, která je připojená k virtuálnímu rozbočovači WAN pomocí ExpressRoute, připojit k virtuální sítě pomocí cesty k virtuální síti. Provoz z větve do větve nebude přenášet do služby hub, protože ExpressRoute Global Reach umožňuje lepší cestu přes Azure WAN.

## <a name="security"></a>Zabezpečení a řízení zásad

Rozbočovač virtuální sítě se vzájemně připojuje a potenciálně vidí veškerý tranzitní provoz. Může to být místo pro hostování funkcí a služeb centrální sítě, jako je například směrování cloudu, síťové zásady a zabezpečení a řízení přístupu k Internetu.

## <a name="next-steps"></a>Další postup

Vytvořte připojení pomocí virtuální sítě WAN.

* [Připojení typu Site-to-site pomocí virtuální sítě WAN](virtual-wan-site-to-site-portal.md)
* [Připojení ExpressRoute pomocí virtuální sítě WAN](virtual-wan-expressroute-portal.md)
