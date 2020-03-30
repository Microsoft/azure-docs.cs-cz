---
title: 'Architektura: Architektura globální tranzitní sítě'
titleSuffix: Azure Virtual WAN
description: Informace o architektuře globální tranzitní sítě pro virtuální síť WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 02/06/2020
ms.author: cherylmc
ms.openlocfilehash: 17d0e678008c76da32f20562aa795e83e49c80e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064967"
---
# <a name="global-transit-network-architecture-and-virtual-wan"></a>Architektura globální tranzitní sítě a virtuální síť WAN

Moderní podniky vyžadují všudypřítomné připojení mezi hyperdistribuovanými aplikacemi, daty a uživateli v cloudu i v místním prostředí. Globální architektura tranzitních sítí je přijímána podniky za účelem konsolidace, připojení a řízení moderní globální it stopy zaměřené na cloud.

Architektura globální tranzitní sítě je založena na klasickém modelu připojení pro rozbočovač a paprsky, kde centrum hostované v cloudu umožňuje přenosité připojení mezi koncovými body, které mohou být distribuovány mezi různými typy "paprsků".

V tomto modelu může být paprsek:
* Virtuální síť (virtuální sítě)
* Fyzická pobočka webu
* Vzdálený uživatel
* Internet

![rozbočovač a mluvil](./media/virtual-wan-global-transit-network-architecture/figure1.png)

**Obrázek 1: Globální tranzitní rozbočovač a paprsková síť**

Obrázek 1 znázorňuje logické zobrazení globální tranzitní sítě, kde jsou geograficky distribuovaní uživatelé, fyzické lokality a virtuální sítě propojeny prostřednictvím síťového rozbočovače hostovaného v cloudu. Tato architektura umožňuje logické připojení jednoho směrování mezi koncovými body sítě.

## <a name="global-transit-network-with-virtual-wan"></a><a name="globalnetworktransit"></a>Globální tranzitní síť s virtuální sítí WAN

Azure Virtual WAN je cloudová síťová služba spravovaná společností Microsoft. Všechny síťové součásti, ze kterých se tato služba skládá, jsou hostovány a spravovány společností Microsoft. Další informace o virtuální paměti WAN naleznete v článku [Přehled virtuální sítě WAN.](virtual-wan-about.md)

Azure Virtual WAN umožňuje architekturu globální tranzitní sítě tím, že umožňuje všudypřítomné připojení libovolné mezi globálně distribuovanými sadami cloudových úloh ve virtuálních sítích, pobočkách, aplikacích SaaS a PaaS a uživateli.

![Informace o službě Azure Virtual WAN](./media/virtual-wan-global-transit-network-architecture/figure2.png)

**Obrázek 2: Globální tranzitní síť a virtuální síť WAN**

V architektuře Virtuální wan Azure virtuální WAN rozbočovače jsou zřízeny v oblastech Azure, ke kterému můžete připojit větve, virtuální sítě a vzdálené uživatele. Fyzické pobočky lokality jsou připojeny k rozbočovači Pomocí Premium ExpressRoute nebo site-to site-VPN, virtuální sítě jsou připojené k rozbočovači pomocí připojení virtuální sítě a vzdálení uživatelé se mohou přímo připojit k rozbočovači pomocí uživatelské VPN (VPN s bodem na lokalitu). Virtuální síť WAN také podporuje připojení virtuální sítě mezi oblastmi, kde virtuální síť v jedné oblasti může být připojena k virtuálnímu rozbočovači WAN v jiné oblasti.

Virtuální síť WAN můžete vytvořit vytvořením jednoho virtuálního rozbočovače WAN v oblasti, které má největší počet paprsků (větve, virtuální sítě, uživatelé), a potom připojením paprsků, které jsou v jiných oblastech, k rozbočovači. To je dobrá volba, když je podniková stopa většinou v jedné oblasti s několika vzdálenými paprsky.  
  
## <a name="hub-to-hub-connectivity"></a><a name="hubtohub"></a>Připojení rozbočovače k rozbočovači

Nároky na cloud pro podniky mohou přesáhnou více cloudových oblastí a přístup ke cloudu z oblasti, která je nejblíže jejich fyzickému webu a uživatelům, je optimální (latence). Jedním z klíčových principů architektury globální tranzitní sítě je umožnit připojení mezi oblastmi mezi všemi koncovými body sítě v cloudu a místní síti. To znamená, že provoz z větve, která je připojena ke cloudu v jedné oblasti, může dosáhnout jiné větve nebo virtuální sítě v jiné oblasti pomocí připojení mezi centry povolené [ho Azure Global Network](https://azure.microsoft.com/global-infrastructure/global-network/).

![mezioblastní](./media/virtual-wan-global-transit-network-architecture/figure3.png)

**Obrázek 3: Virtuální připojení wan mezi oblastmi**

Pokud je v jedné virtuální síti WAN povoleno více rozbočovačů, jsou rozbočovače automaticky propojeny prostřednictvím propojení mezi rozbočovači, což umožňuje globální připojení mezi větvemi a virtuálními sítěmi, které jsou distribuovány ve více oblastech. 

Centra, která jsou součástí stejné virtuální sítě WAN, mohou být navíc přidružena k různým zásadám místního přístupu a zabezpečení. Další informace naleznete v [tématu Zabezpečení a ovládací prvek zásad](#security) dále v tomto článku.

## <a name="any-to-any-connectivity"></a><a name="anytoany"></a>Připojení libovolného k libovolnému

Architektura globální tranzitní sítě umožňuje připojení libovolného připojení prostřednictvím virtuálních rozbočovačů WAN. Tato architektura eliminuje nebo snižuje potřebu úplné sítě nebo částečné sítě připojení mezi paprsky, které jsou složitější vytvářet a udržovat. Kromě toho je snazší konfigurovat a udržovat řízení směrování v sítích hub-and-spoke vs. mesh.

Jakékoli připojení (v kontextu globální architektury) umožňuje podniku s globálně distribuovanými uživateli, pobočkami, datovými centry, virtuálními sítěmi a aplikacemi, aby se vzájemně připojovali prostřednictvím rozbočovačů "transit". Azure Virtual WAN funguje jako globální tranzitní systém.

![žádné na jakékoli](./media/virtual-wan-global-transit-network-architecture/figure4.png)

**Obrázek 4: Virtuální trasy provozu WAN**

Azure Virtual WAN podporuje následující globální tranzitní cesty připojení. Písmena v závorce mapovat na obrázek 4.

* Pobočka-virtuální síť (a)
* Od větve k větvi (b)
  * Globální dosah ExpressRoute a virtuální wan
* Vzdálená síť uživatele k virtuální síti (c)
* Vzdálená uživatelská větev (d)
* Virtuální síť k virtuální síti (e)
* Od větve k rozbočovači-k-větvi (f)
* Od větve k rozbočovači k virtuální síti (g)
* Virtuální síť k rozbočovači k virtuální síti (h)

### <a name="branch-to-vnet-a-and-branch-to-vnet-cross-region-g"></a>Mezioblast mezi větvemi a virtuální sítí (a) a mezioborové sítě a mezioborové sítě (g)

Pobočka virtuální sítě je primární cesta podporovaná virtuální sítí Azure WAN. Tato cesta umožňuje připojit pobočky k podnikovým úlohám Azure IAAS, které se nasazují ve virtuálních sítích Azure. Pobočky lze připojit k virtuální síti WAN prostřednictvím ExpressRoute nebo VPN site-to-site. Přenosy přenosy do virtuálních sítí, které jsou připojené k virtuální matná rozbočovače WAN prostřednictvím připojení virtuální sítě. Explicitní [přenos brány](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) není vyžadován pro virtuální síť WAN, protože virtuální síť WAN automaticky umožňuje přechod brány do pobočkové lokality. Viz článek [o virtuálních partnerech WAN](virtual-wan-configure-automation-providers.md) o připojení CPE SD-WAN k virtuální síti WAN.

### <a name="expressroute-global-reach-and-virtual-wan"></a>Globální dosah ExpressRoute a virtuální wan

ExpressRoute je soukromý a odolný způsob připojení místních sítí k Microsoft Cloudu. Virtuální síť WAN podporuje připojení okruhů Express Route. Připojení pobočky k virtuální síti WAN s expresní trasou vyžaduje 1) prémiový okruh 2) okruh, který bude v umístění s povoleným globálním dosahem.

ExpressRoute Global Reach je doplňková funkce pro ExpressRoute. Pomocí globálního dosahu můžete propojit okruhy ExpressRoute a vytvořit tak privátní síť mezi místními sítěmi. Větve, které jsou připojené k Azure Virtual WAN pomocí ExpressRoute vyžadují ExpressRoute Globální dosah komunikovat mezi sebou.

V tomto modelu se každá větev, která je připojena k virtuálnímu rozbočovači WAN pomocí ExpressRoute, může připojit k virtuálním sítím pomocí cesty mezi větvemi na virtuální síť. Provoz mezi větvemi nebude projíždět rozbočovačem, protože Globální dosah ExpressRoute umožňuje přesnější cestu přes Azure WAN.

### <a name="branch-to-branch-b-and-branch-to-branch-cross-region-f"></a>Mezioblastní oblast mezi větvemi a větvemi (f)

Pobočky lze připojit k virtuálnímu rozbočovači Azure pomocí okruhů ExpressRoute nebo připojení VPN mezi lokalitami. Větve můžete připojit k virtuálnímu rozbočovači WAN, které je v oblasti, která je nejblíže větvi.

Tato možnost umožňuje podnikům využít páteřní sítě Azure k připojení větví. I když je tato funkce k dispozici, měli byste zvážit výhody připojení větví přes Azure Virtual WAN vs. pomocí privátní sítě WAN.  

### <a name="remote-user-to-vnet-c"></a>Vzdálená síť uživatele k virtuální síti (c)

Můžete povolit přímý, zabezpečený vzdálený přístup k Azure pomocí připojení point-to-site ze vzdáleného klienta uživatele do virtuální sítě WAN. Vzdálení podnikoví uživatelé již nemusí používat firemní VPN.

### <a name="remote-user-to-branch-d"></a>Vzdálená uživatelská větev (d)

Vzdálená cesta mezi uživateli a větvemi umožňuje vzdáleným uživatelům, kteří používají připojení bodu k webu k místním úlohám a aplikacím přístupu k síti Azure, a to prostřednictvím cloudu. Tato cesta poskytuje vzdáleným uživatelům flexibilitu pro přístup k úlohám, které jsou nasazené v Azure i místně. Podniky můžou ve virtuální mase Azure wan povolit centrální cloudovou službu zabezpečeného vzdáleného přístupu.

### <a name="vnet-to-vnet-transit-e-and-vnet-to-vnet-cross-region-h"></a>Tranzit virtuální sítě na virtuální síť (e) a mezioblastmezioblastmi virtuální sítě na virtuální síť (h)

Přenos virtuální sítě na virtuální síť umožňuje virtuálním sítím vzájemně se připojovat, aby mohly propojit vícevrstvé aplikace, které jsou implementované ve více virtuálních sítích. Volitelně můžete připojit virtuální sítě k sobě prostřednictvím partnerského vztahu virtuální sítě a to může být vhodné pro některé scénáře, kde není nutné tranzit přes rozbočovač VWAN.

## <a name="security-and-policy-control"></a><a name="security"></a>Zabezpečení a kontrola zásad

Centra Azure Virtual WAN propojují všechny koncové body sítě v rámci hybridní sítě a potenciálně vidí veškerý přenos v tranzitní síti. Virtuální rozbočovače WAN lze převést na zabezpečené virtuální rozbočovače nasazením brány Azure Firewall do rozbočovačů VWAN, aby bylo možné provádět cloudové zabezpečení, přístup a řízení zásad. Orchestraci azure firewallů ve virtuálních rozbočovačích WAN může provádět Správce azure firewall.

[Azure Firewall Manager](https://go.microsoft.com/fwlink/?linkid=2107683) poskytuje funkce pro správu a škálování zabezpečení pro globální tranzitní sítě. Azure Firewall Manager poskytuje možnost centrálně spravovat směrování, globální správu zásad, pokročilé služby zabezpečení Internetu prostřednictvím třetích stran spolu s Azure Firewall.

![zabezpečené virtuální rozbočovač pomocí Azure Firewall](./media/virtual-wan-global-transit-network-architecture/figure5.png)

**Obrázek 5: Zabezpečené virtuální centrum pomocí Azure Firewall**

Azure Firewall do virtuální sítě WAN podporuje následující globální cesty zabezpečeného tranzitního připojení. Písmena v závorce mapovat na obrázek 5.

* Zabezpečený tranzit virtuální sítě k virtuální síti (e)
* Služba zabezpečení Virtuální síť k Internetu nebo služba zabezpečení jiného výrobce (i)
* Služba zabezpečení mezi větvemi a internetem nebo službou zabezpečení jiného výrobce (j)

### <a name="vnet-to-vnet-secured-transit-e"></a>Zabezpečený tranzit virtuální sítě k virtuální síti (e)

Zabezpečený přenos virtuální sítě na virtuální síť umožňuje virtuálním sítím vzájemně se připojovat přes Bránu Azure firewall ve virtuálním rozbočovači WAN.

### <a name="vnet-to-internet-or-third-party-security-service-i"></a>Služba zabezpečení Virtuální síť k Internetu nebo služba zabezpečení jiného výrobce (i)

Zabezpečený přenos virtuální sítě na Internet nebo zabezpečený přenos od jiných výrobců umožňuje virtuálním sítím připojit se k internetu nebo k podporovaným službám zabezpečení třetích stran prostřednictvím brány Azure Firewall ve virtuálním rozbočovači WAN.

### <a name="branch-to-internet-or-third-party-security-service-j"></a>Služba zabezpečení mezi větvemi a internetem nebo službou zabezpečení jiného výrobce (j)
Zabezpečovací přenos mezi větvemi a internetem umožňuje pobočkám připojit se k Internetu nebo k podporovaným službám zabezpečení třetích stran prostřednictvím brány Azure Firewall ve virtuálním centru WAN.

## <a name="next-steps"></a>Další kroky

Vytvořte připojení pomocí virtuální sítě WAN a nasazení brány Azure Firewall v rozbočovačích VWAN.

* [Připojení mezi lokalitami pomocí virtuální sítě WAN](virtual-wan-site-to-site-portal.md)
* [Připojení ExpressRoute pomocí virtuální sítě WAN](virtual-wan-expressroute-portal.md)
* [Správce Azure Firewall pro nasazení Azure FW ve VWAN](https://go.microsoft.com/fwlink/?linkid=2107683)
