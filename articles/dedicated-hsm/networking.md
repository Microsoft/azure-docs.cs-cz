---
title: Vyhrazené modulu hardwarového zabezpečení sítě | Dokumentace Microsoftu
description: Azure vyhrazené HSM nabízí možnosti úložiště klíčů v Azure, která splňují standard FIPS 140-2 Level 3 certifikační
services: dedicated-hsm
author: barclayn
manager: mbaldwin
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: barclayn
ms.openlocfilehash: a711fbfe262ea3f9e12e0a4b4f5bf73243a6c726
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52678964"
---
# <a name="azure-dedicated-hsm-networking"></a>Sítě Azure vyhrazené HSM

Azure vyhrazené modulu hardwarového zabezpečení vyžaduje vysoce zabezpečených síťových prostředí. Toto je hodnota true Určuje, zda je z Azure cloudu zpět do zákazníka IT prostředí (místní), pomocí distribuovaných aplikací nebo scénáře vysoké dostupnosti. Sítě Azure poskytují to a existují čtyři různé oblasti, které je potřeba řešit.

- Vytvoření modulu hardwarového zabezpečení zařízení ve vaší virtuální síti (VNet) v Azure
- Připojení místních prostředků cloudu ke konfiguraci a správě zařízení HSM
- Vytvoření a připojení virtuální sítě pro připojení mezi prostředky aplikace a zařízení HSM
- Propojení virtuálních sítí v oblasti pro komunikaci mezi a také aby se povolily scénáře vysoké dostupnosti

## <a name="virtual-network-for-your-dedicated-hsms"></a>Virtuální sítě pro vyhrazené modulů hardwarového zabezpečení

Dedikovaných modulů hardwarového zabezpečení jsou integrované do virtuální sítě a umístěné ve vlastní privátní síti zákazníků v Azure. To umožňuje přístup k zařízení z virtuálních počítačů nebo výpočetních prostředků ve virtuální síti.  
Další informace o integraci služby Azure do virtuální sítě a nabízí funkce, najdete v části [virtuální síť pro služby Azure](../virtual-network/virtual-network-for-azure-services.md) dokumentaci.

### <a name="virtual-networks"></a>Virtuální sítě

Před zřizováním zařízení vyhrazené HSM, zákazníci budete muset vytvořit virtuální síť v Azure nebo použijte takový, který již existuje v rámci předplatného pro zákazníky. Virtuální síť definuje bezpečnostní hraniční sítě pro zařízení vyhrazené HSM. Další informace o vytváření virtuálních sítí najdete v tématu [dokumentace ke službě virtual network](../virtual-network/virtual-networks-overview.md).

### <a name="subnets"></a>Podsítě

Podsítě virtuální sítě rozdělit do samostatné adresní prostory použitelné Azure prostředky, které umístíte do nich. Dedikovaných modulů hardwarového zabezpečení se nasazuje do podsítě ve virtuální síti. Každý vyhrazený modulu hardwarového zabezpečení zařízení, který je nasazený v podsíti zákazníka zobrazí privátní IP adresu z této podsítě. Podsítě, ve kterém je nasazen zařízení HSM musí být explicitně delegovat na službu: Microsoft.HardwareSecurityModules/dedicatedHSMs. Tím udělíte určitá oprávnění k modulu hardwarového zabezpečení služby pro nasazení do podsítě. Delegování vyhrazené moduly hardwarového zabezpečení má určitá omezení zásad v podsíti. Skupiny zabezpečení sítě (Nsg) a trasy definované uživatelem (udr) nejsou aktuálně podporované ve delegované podsítě. V důsledku toho Jakmile podsítě se deleguje na dedikovaných modulů hardwarového zabezpečení, ho jde použít jenom k nasazení prostředků modulu hardwarového zabezpečení. Nasazení z dalších prostředků zákazníků do podsítě se nezdaří.


### <a name="expressroute-gateway"></a>ExpressRoute gateway

Požadavek na aktuální architektuře je konfigurace ER brány v podsíti zákazníkům ve kterém zařízení s HSM musí být umístěny se povolit integraci modulu hardwarového zabezpečení zařízení do Azure. Tato brána ER nedají využít pro připojení k zařízení HSM zákazníků v Azure v místním umístění.

## <a name="connecting-your-on-premises-it-to-azure"></a>Připojení místní IT do Azure

Když vytváříte cloudové prostředky, je typické požadavek soukromého připojení zpět k místní prostředky IT. V případě vyhrazeného HSM bude převážně pro klientský software modulu hardwarového zabezpečení můžete nakonfigurovat zařízení HSM a také pro aktivity, jako je zálohování a potáhnete prstem protokoly z modulů hardwarového zabezpečení pro analýzu. Bod klíčová rozhodnutí je povaze připojení, protože jsou k dispozici možnosti.  Jak budou pravděpodobně více místních prostředků, které vyžadují bezpečnou komunikaci s prostředky (včetně moduly hardwarového zabezpečení) v cloudu Azure, je nejflexibilnějším možnost Site-to-Site VPN. To vyžaduje organizace zákazníka k zařízení VPN k usnadnění připojení. Připojení k síti VPN Point-to-Site lze použít, pokud existuje pouze jeden koncový bod v místním například pracovní stanice jednotné správy.
Další informace o možnostech připojení najdete v tématu [VPN Gateway možnosti plánování](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable).

>[!NOTE]
V tuto chvíli ExpressRoute není možné zvolit pro připojení k místním prostředkům. Je také potřeba poznamenat, že brána ExpressRoute používá jak je popsáno výše, není pro připojení k místní infrastrukturu.

### <a name="point-to-site-vpn"></a>Point-to-Site VPN

Virtuální privátní sítě point-to-site je nejjednodušší forma zabezpečené připojení k jeden koncový bod v místním prostředí. To může být relevantní, pokud máte v úmyslu mít jenom pracovní stanici jednotné správy pro založené na Azure dedikovaných modulů hardwarového zabezpečení.

### <a name="site-to-site-vpn"></a>Site-to-Site VPN

Umožňuje virtuální privátní síť site-to-site pro zabezpečenou komunikaci mezi založené na Azure vyhrazené moduly hardwarového zabezpečení ani místní IT. Důvod k tomu dochází k danému zálohování zařízení pro místní modul hardwarového zabezpečení a nutnosti připojení mezi těmito dvěma pro spuštění zálohování.

## <a name="connecting-virtual-networks"></a>Propojení virtuálních sítí

Typické nasazení architektury pro vyhrazené HSM budou začínat jedné virtuální sítě a odpovídající podsítě, ve kterém zařízení HSM vytvoření a zřízení. V rámci stejné oblasti může také dojít k další virtuální sítě a podsítě pro součásti aplikace, které by pomocí modulu hardwarového zabezpečení Dedicated. Pokud chcete povolit komunikaci mezi těmito sítěmi, používáme partnerské vztahy virtuálních sítí.

### <a name="virtual-network-peering"></a>Partnerské vztahy virtuálních sítí

Po několika virtuálními sítěmi v rámci oblasti, které potřebují přístup k prostředkům druhé strany se partnerský vztah virtuální sítě je možné vytvořit zabezpečený komunikační kanály mezi nimi.  Partnerský vztah virtuální sítě poskytuje nejen zabezpečenou komunikaci, ale také zajišťuje, aby s nízkou latencí a velkou šířkou pásma připojení mezi prostředky v Azure.

![Vnet peering](media/networking/peering.png)

## <a name="connecting-across-azure-regions"></a>Připojení napříč oblastmi Azure

Zařízením hardwarového zabezpečení mají možnost, prostřednictvím knihovny softwaru pro přesměrování přenosu dat na alternativní modulu hardwarového zabezpečení. Přesměrování provozu je užitečné, pokud selhání zařízení nebo ztráty přístupu k zařízení. Selhání v oblasti úrovně scénáře dají zmírnit nasazením modulů hardwarového zabezpečení v jiných oblastech a povolení komunikace mezi virtuálními sítěmi napříč oblastmi.

### <a name="cross-region-ha-using-vpn-gateway"></a>Pro různé oblasti vysokou dostupnost pomocí VPN gateway

U globálně distribuovaných aplikací nebo scénáře vysoké dostupnosti regionální převzetí služeb při selhání se vyžaduje k propojení virtuálních sítí mezi oblastmi. Pomocí vyhrazené modulu hardwarového zabezpečení Azure můžete dosáhnout vysoké dostupnosti s použitím brány VPN, která poskytuje zabezpečené tunelové propojení mezi dvěma virtuálními sítěmi. Další informace o připojení Vnet-to-Vnet pomocí VPN Gateway najdete v článku s názvem [co je VPN Gateway?](../vpn-gateway/vpn-gateway-about-vpngateways.md#V2V)

>[!NOTE]
Globální partnerský vztah virtuální sítě není k dispozici připojení mezi různými oblastmi, scénáře s moduly hardwarového zabezpečení vyhrazené v této době a VPN gateway by měl místo toho použít. 

![globální vnet](media/networking/global-vnet.png)

## <a name="next-steps"></a>Další postup

- [Nejčastější dotazy](faq.md)
- [Možnosti podpory](supportability.md)
- [Vysoká dostupnost](high-availability.md)
- [Fyzické zabezpečení](physical-security.md)
- [Monitorování](monitoring.md)
- [Architektura nasazení](deployment-architecture.md)