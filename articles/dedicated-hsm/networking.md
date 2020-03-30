---
title: Aspekty vytváření sítí – azure dedicated hsm | Dokumenty společnosti Microsoft
description: Přehled aspekty sítě platné pro nasazení azure dedicated hsm
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
ms.openlocfilehash: 044930c9df7b54515b9b66426a6b05aa9517a3a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70881284"
---
# <a name="azure-dedicated-hsm-networking"></a>Azure Vyhrazené sítě hardwarového zabezpečení

Azure Dedicated HSM vyžaduje vysoce zabezpečené síťové prostředí. To platí bez ohledu na to, zda je z cloudu Azure zpět do it prostředí zákazníka (místně), pomocí distribuovaných aplikací nebo pro scénáře s vysokou dostupností. Azure Networking poskytuje to a existují čtyři odlišné oblasti, které je třeba řešit.

- Vytváření zařízení hsm uvnitř virtuální sítě (Virtuální síť) v Azure
- Připojení místních cloudových prostředků pro konfiguraci a správu zařízení hsm
- Vytváření a propojování virtuálních sítí pro propojení aplikačních prostředků a zařízení hsm
- Propojení virtuálních sítí napříč regiony pro vzájemnou komunikaci a také umožnění scénářů s vysokou dostupností

## <a name="virtual-network-for-your-dedicated-hsms"></a>Virtuální síť pro vyhrazené moduly hardwarového zabezpečení

Vyhrazené moduly hardwarového zabezpečení jsou integrovány do virtuální sítě a umístěny ve vlastní privátní síti zákazníků v Azure. To umožňuje přístup k zařízením z virtuálních počítačů nebo výpočetních prostředků ve virtuální síti.  
Další informace o integraci služeb Azure do virtuální sítě a funkcí, které poskytuje, najdete v tématu [Dokumentace ke virtuální síti pro služby Azure.](../virtual-network/virtual-network-for-azure-services.md)

### <a name="virtual-networks"></a>Virtuální sítě

Před zřízením vyhrazené hojné hojného modulu hardwaru zařízení zákazníci budou muset nejprve vytvořit virtuální síť v Azure nebo použít tu, která již existuje v předplatném zákazníků. Virtuální síť definuje obvod zabezpečení pro vyhrazené zařízení hardwarového zabezpečení. Další informace o vytváření virtuálních sítí naleznete v [dokumentaci k virtuální síti](../virtual-network/virtual-networks-overview.md).

### <a name="subnets"></a>Podsítě

Podsítě segmentvirtuální síť do samostatných adresprostorů použitelných prostředky Azure, které umístíte v nich. Vyhrazené moduly hardwarového zabezpečení jsou nasazeny do podsítě ve virtuální síti. Každé vyhrazené zařízení hardwarového zabezpečení, které je nasazeno v podsíti zákazníka, obdrží z této podsítě privátní IP adresu. Podsíť, ve které je zařízení hardwarového zabezpečení nasazeno, musí být explicitně delegována na službu: Microsoft.HardwareSecurityModules/dedicatedHSMs. To uděluje určitá oprávnění službě HSM pro nasazení do podsítě. Delegování na vyhrazené moduly hardwarového zabezpečení ukládá určitá omezení zásad pro podsíť. Skupiny zabezpečení sítě (NSG) a uživatelem definované trasy (UDR) nejsou v delegovaných podsítích aktuálně podporovány. V důsledku toho po delegování podsítě na vyhrazené moduly hardwarového zabezpečení, lze použít pouze k nasazení prostředků modulu hardwarového zabezpečení. Nasazení jiných prostředků zákazníka do podsítě se nezdaří.


### <a name="expressroute-gateway"></a>Brána ExpressRoute

Požadavkem aktuální architektury je konfigurace brány ER v podsíti zákazníků, kde musí být umístěno zařízení s modulu hesm, aby bylo možné zařízení modulu zabezpečení do Azure. Tuto bránu ER nelze využít pro připojení místních umístění k zákazníkům zařízení hsm zákazníků v Azure.

## <a name="connecting-your-on-premises-it-to-azure"></a>Propojení místního IT s Azure

Při vytváření cloudových prostředků je typickým požadavkem pro privátní připojení zpět k místním it prostředkům. V případě vyhrazeného modulu hardwarového zabezpečení to bude převážně pro klientský software modulu hardwarového zabezpečení pro konfiguraci zařízení hardwarového zabezpečení a také pro činnosti, jako jsou zálohování a vytažení protokolů z modulů hardwarového zabezpečení pro analýzu. Klíčovým bodem rozhodnutí je zde povaha připojení, protože existují možnosti.  Nejflexibilnější možností je síť VPN site-to-site, protože pravděpodobně bude existovat více místních prostředků, které vyžadují zabezpečenou komunikaci s prostředky (včetně modulů zabezpečení) v cloudu Azure. To bude vyžadovat, aby organizace zákazníka měla zařízení VPN pro usnadnění připojení. Připojení VPN z bodu na místní místo lze použít, pokud existuje pouze jeden koncový bod v místním prostředí, například jedna pracovní stanice pro správu.
Další informace o možnostech připojení naleznete v tématu [možnosti plánování brány VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable).

> [!NOTE]
> V tuto chvíli ExpressRoute není možnost pro připojení k místním prostředkům. Je třeba také poznamenat, že brána ExpressRoute používaná výše, není pro připojení k místní infrastruktuře.

### <a name="point-to-site-vpn"></a>Point-to-site VPN

Virtuální privátní síť point-to-site je nejjednodušší formou zabezpečeného připojení k jedinému koncovému bodu v místním prostředí. To může být relevantní, pokud máte v úmyslu mít jenom jednu pracovní stanici pro správu pro vyhrazené moduly hardwarového zabezpečení založené na Azure.

### <a name="site-to-site-vpn"></a>Site-to-site VPN

Virtuální privátní síť site-to-site umožňuje zabezpečenou komunikaci mezi vyhrazenými moduly hardwarového zabezpečení založenými na Azure a místními IT. Důvodem k tomu je mít záložní zařízení pro místní server zabezpečení a potřebují připojení mezi těmito dvěma pro spuštění zálohy.

## <a name="connecting-virtual-networks"></a>Připojení virtuálních sítí

Typická architektura nasazení pro vyhrazený modul hardwarového zabezpečení bude začínat s jedinou virtuální sítí a odpovídající podsítí, ve které jsou vytvořena a zřízena zařízení hardwarového zabezpečení. V rámci stejné oblasti může být další virtuální sítě a podsítě pro součásti aplikace, které by využívaly vyhrazený modul hardwarového zabezpečení. K povolení komunikace v těchto sítích používáme partnerský vztah virtuální sítě.

### <a name="virtual-network-peering"></a>Partnerské vztahy virtuálních sítí

Pokud existuje více virtuálních sítí v rámci oblasti, které potřebují přístup k navzájem prostředky, partnerský vztah virtuální sítě lze vytvořit zabezpečené komunikační kanály mezi nimi.  Partnerský vztah virtuální sítě poskytuje nejen zabezpečenou komunikaci, ale také zajišťuje připojení s nízkou latencí a vysokou šířkou pásma mezi prostředky v Azure.

![partnerský vztah v síti](media/networking/peering.png)

## <a name="connecting-across-azure-regions"></a>Připojení napříč oblastmi Azure

Zařízení s hsm mají možnost prostřednictvím softwarových knihoven přesměrovat provoz na alternativní hsm. Přesměrování provozu je užitečné v případě, že zařízení selžou nebo dojde ke ztrátě přístupu k zařízení. Scénáře selhání na regionální úrovni lze zmírnit nasazením hsm v jiných oblastech a povolením komunikace mezi virtuálními sítěmi napříč oblastmi.

### <a name="cross-region-ha-using-vpn-gateway"></a>Ha pro návlašovou oblast s bránou VPN

U globálně distribuovaných aplikací nebo pro scénáře převzetí služeb při selhání s vysokou dostupností je nutné připojit virtuální sítě napříč oblastmi. Díky vyhrazenému modulu hardwarového zabezpečení Azure lze dosáhnout vysoké dostupnosti pomocí brány VPN, která poskytuje zabezpečené tunelové propojení mezi dvěma virtuálními sítěmi. Další informace o připojeních v síti Vnet-to-Vnet pomocí brány VPN najdete v článku s názvem [Co je brána VPN?](../vpn-gateway/vpn-gateway-about-vpngateways.md#V2V)

> [!NOTE]
> Globální partnerský vztah virtuální sítě není k dispozici ve scénářích připojení mezi oblastmi s vyhrazenými moduly hardwarového zabezpečení v tuto chvíli a místo toho by měla být použita brána VPN. 

![globální virtuální síť](media/networking/global-vnet.png)

## <a name="next-steps"></a>Další kroky

- [Nejčastější dotazy](faq.md)
- [Možnosti podpory](supportability.md)
- [Vysoká dostupnost](high-availability.md)
- [Fyzické zabezpečení](physical-security.md)
- [Sledování](monitoring.md)
- [Architektura nasazení](deployment-architecture.md)