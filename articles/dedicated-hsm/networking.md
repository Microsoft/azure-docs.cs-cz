---
title: Požadavky na síť – vyhrazený modul HARDWAROVÉho zabezpečení Azure | Microsoft Docs
description: Přehled důležitých informací o sítích týkajících se vyhrazených nasazení HSM v Azure
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: mbaldwin
ms.openlocfilehash: 3764b261b491c660da16d7989be20742fead1fbf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91359150"
---
# <a name="azure-dedicated-hsm-networking"></a>Vyhrazené sítě HSM v Azure

Vyhrazený modul HARDWAROVÉho zabezpečení Azure vyžaduje vysoce zabezpečené síťové prostředí. To platí bez ohledu na to, jestli se z cloudu Azure zpátky do IT prostředí (místní), pomocí distribuovaných aplikací nebo scénářů s vysokou dostupností. Sítě Azure tuto službu nabízí a existují čtyři různé oblasti, které je potřeba řešit.

- Vytváření zařízení HSM v rámci vaší Virtual Network (VNet) v Azure
- Připojení místních prostředků ke cloudovým prostředkům pro konfiguraci a správu zařízení HSM
- Vytváření a propojení virtuálních sítí pro připojení prostředků aplikací a zařízení HSM
- Propojení virtuálních sítí v různých oblastech pro vzájemnou komunikaci a také pro zajištění scénářů s vysokou dostupností

## <a name="virtual-network-for-your-dedicated-hsms"></a>Virtuální síť pro vyhrazené HSM

Vyhrazené HSM jsou integrované do Virtual Network a umisťují se do vlastní privátní sítě Customers v Azure. To umožňuje přístup k zařízením z virtuálních počítačů nebo výpočetních prostředků ve virtuální síti.  
Další informace o integraci služeb Azure do virtuální sítě a funkcí, které poskytuje, najdete v dokumentaci ke [službám Virtual Network pro Azure](../virtual-network/virtual-network-for-azure-services.md) .

### <a name="virtual-networks"></a>Virtuální sítě

Než zřídíte vyhrazené zařízení HSM, zákazníci nejdřív budou muset vytvořit Virtual Network v Azure nebo použít jiný, který už existuje v předplatném pro zákazníky. Virtuální síť definuje hraniční zabezpečení pro vyhrazené zařízení HSM. Další informace o vytváření virtuálních sítí najdete v tématu [dokumentace k virtuální síti](../virtual-network/virtual-networks-overview.md).

### <a name="subnets"></a>Podsítě

Podsítě segmentují virtuální síť do samostatných adresních prostorů použitelných prostředky Azure, které do nich umístíte. Vyhrazené HSM se nasadí do podsítě ve virtuální síti. Každé vyhrazené zařízení HSM, které je nasazeno v podsíti zákazníka, obdrží privátní IP adresu z této podsítě. Podsíť, ve které je nasazené zařízení HSM, musí být explicitně delegovaná na službu: Microsoft. HardwareSecurityModules/dedicatedHSMs. Tím je uděleno určité oprávnění ke službě HSM pro nasazení do podsítě. Delegování na vyhrazené HSM ukládá určitá omezení zásad v podsíti. V delegovaných podsítích nejsou aktuálně podporovány skupiny zabezpečení sítě (skupin zabezpečení sítě) a trasy User-Defined (udr). V důsledku toho se po delegování podsítě na vyhrazené HSM dá použít jenom k nasazení prostředků HSM. Nasazení jakýchkoli dalších zákaznických prostředků do podsítě se nezdaří.


### <a name="expressroute-gateway"></a>ExpressRoute bránu

Požadavek aktuální architektury je konfigurací brány ER v podsíti zákazníků, kde je nutné umístit zařízení HSM, aby bylo možné povolit integraci zařízení HSM do Azure. Tuto bránu ER nejde využít k připojení místních umístění k zařízením HSM pro zákazníky v Azure.

## <a name="connecting-your-on-premises-it-to-azure"></a>Připojení místního prostředí k Azure

Při vytváření cloudových prostředků je obvyklým požadavkem pro privátní připojení zpátky k místním IT prostředkům. V případě vyhrazeného modulu HARDWAROVÉho zabezpečení (HSM) bude mít klientský software HSM konfiguraci zařízení HSM a také aktivity, jako jsou například zálohy a protokoly o přijetí změn z HSM k analýze. Klíčový bod rozhodování je povaha připojení, protože existují možnosti.  Nejpružnější možností je síť VPN typu Site-to-site, protože se bude pravděpodobně jednat o více místních prostředků, které vyžadují zabezpečenou komunikaci s prostředky (včetně HSM) v cloudu Azure. Tato akce vyžaduje, aby organizace zákazníka měla k usnadnění připojení zařízení VPN. Připojení VPN typu Point-to-site lze použít, pokud je k dispozici pouze jeden místní koncový bod, například jedna pracovní stanice pro správu.
Další informace o možnostech připojení najdete v tématu [VPN Gateway možností plánování](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable).

> [!NOTE]
> V současné době ExpressRoute není možnost připojení k místním prostředkům. Měla by se také poznamenat, že brána ExpressRoute použitá jak je popsaná výše, není pro připojení k místní infrastruktuře.

### <a name="point-to-site-vpn"></a>SÍŤ VPN typu Point-to-site

Virtuální privátní síť typu Point-to-site je nejjednodušší forma zabezpečeného připojení k jednomu koncovému bodu v místním prostředí. To může být důležité, pokud máte v úmyslu mít pro vyhrazené HSM založené na Azure jenom jednu pracovní stanici pro správu.

### <a name="site-to-site-vpn"></a>Site-to-site VPN

Virtuální privátní síť typu Site-to-site umožňuje zabezpečenou komunikaci mezi vyhrazenými HSM založenými na Azure a místním prostředím. Důvodem je, že se jedná o zálohovací zařízení pro místní modul hardwarového zabezpečení (HSM), které vyžaduje připojení mezi dvěma pro spuštění zálohování.

## <a name="connecting-virtual-networks"></a>Připojení virtuálních sítí

Typická architektura nasazení pro vyhrazený modul HARDWAROVÉho zabezpečení spustí jednu virtuální síť a odpovídající podsíť, ve které se vytvoří a zřídí zařízení HSM. V rámci stejné oblasti může být pro součásti aplikací, které by používaly vyhrazený modul HARDWAROVÉho zabezpečení, také další virtuální sítě a podsítě. Pokud chcete povolit komunikaci mezi těmito sítěmi, používáme Virtual Network partnerský vztah.

### <a name="virtual-network-peering"></a>Peering virtuálních sítí

Pokud je v oblasti více virtuálních sítí, které potřebují přístup k prostředkům dalších zdrojů, můžete k vytváření zabezpečených komunikačních kanálů mezi nimi použít Virtual Network partnerský vztah.  Partnerský vztah virtuálních sítí poskytuje nejen zabezpečenou komunikaci, ale taky zajišťuje připojení s nízkou latencí a velkou šířkou pásma mezi prostředky v Azure.

![partnerský vztah k síti](media/networking/peering.png)

## <a name="connecting-across-azure-regions"></a>Připojení přes oblasti Azure

Zařízení HSM mají schopnost přes knihovny softwaru přesměrovat provoz do alternativního HSM. Přesměrování provozu je užitečné v případě, že dojde ke ztrátě zařízení nebo k výpadku zařízení. Scénáře selhání na regionální úrovni je možné zmírnit nasazením HSM do jiných oblastí a povolením komunikace mezi virtuálními sítěmi v různých oblastech.

### <a name="cross-region-ha-using-vpn-gateway"></a>HA v různých oblastech pomocí brány VPN

U globálně distribuovaných aplikací nebo pro regionální scénáře převzetí služeb při selhání s vysokou dostupností je potřeba propojit virtuální sítě napříč různými oblastmi. S vyhrazeným modulem HARDWAROVÉho zabezpečení Azure je možné dosáhnout vysoké dostupnosti pomocí VPN Gateway, která poskytuje zabezpečené tunelové propojení mezi oběma virtuálními sítěmi. Další informace o připojeních VNet-to-VNet pomocí VPN Gateway naleznete v článku s názvem [co je VPN Gateway?](../vpn-gateway/design.md#V2V)

> [!NOTE]
> Globální partnerské vztahy virtuálních sítí nejsou v současné době k dispozici ve scénářích připojení pro různé oblasti s vyhrazeným HSM a měli byste místo toho použít službu VPN Gateway. 

![Diagram znázorňuje dvě oblasti, které jsou propojeny dvěma branami V P N. Každá oblast obsahuje partnerské virtuální sítě.](media/networking/global-vnet.png)

## <a name="next-steps"></a>Další kroky

- [Nejčastější dotazy](faq.md)
- [Možnosti podpory](supportability.md)
- [Vysoká dostupnost](high-availability.md)
- [Fyzické zabezpečení](physical-security.md)
- [Monitorování](monitoring.md)
- [Architektura nasazení](deployment-architecture.md)