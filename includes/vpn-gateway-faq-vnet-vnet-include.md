---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/03/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 72ddd0b6cd6c3e12417d3698c403f89312b531f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67174828"
---
Nejčastější dotazy virtuální sítě k virtuální síti platí pro připojení brány VPN. Informace o partnerské síti virtuální sítě najdete v [tématu partnerský vztah virtuální sítě](../articles/virtual-network/virtual-network-peering-overview.md).

### <a name="does-azure-charge-for-traffic-between-vnets"></a>Účtuje se v Azure provoz mezi virtuálními sítěmi?

Provoz virtuální sítě na virtuální síť ve stejné oblasti je zdarma pro oba směry při použití připojení brány VPN. Přenosy odchozího přenosu dat mezi oblastmi virtuální sítě na virtuální síť se účtují sazby za odchozí přenos dat mezi virtuálními sítěmi na základě zdrojových oblastí. Další informace naleznete na [stránce s cenami služby VPN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway/). Pokud připojujete virtuální sítě pomocí partnerského vztahu virtuální sítě místo brány VPN, přečtěte si část [Ceny virtuální sítě](https://azure.microsoft.com/pricing/details/virtual-network/).

### <a name="does-vnet-to-vnet-traffic-travel-across-the-internet"></a>Je provoz virtuální sítě na virtuální síť přes internet?

Ne. Provoz virtuální sítě na virtuální síť se pohybuje přes páteřní síť Microsoft Azure, ne přes internet.

### <a name="can-i-establish-a-vnet-to-vnet-connection-across-azure-active-directory-aad-tenants"></a>Můžu navázat připojení virtuální sítě k virtuální síti mezi klienty Azure Active Directory (AAD)?

Ano, připojení virtuální sítě k virtuální síti, která používají brány Azure VPN, fungují napříč klienty AAD.

### <a name="is-vnet-to-vnet-traffic-secure"></a>Je provoz VNet-to-VNet bezpečný?

Ano, je chráněn šifrováním IPsec/IKE.

### <a name="do-i-need-a-vpn-device-to-connect-vnets-together"></a>Je k propojení virtuálních sítí potřeba zařízení VPN?

Ne. Propojení více virtuálních sítí Azure nevyžaduje žádná zařízení VPN, pokud není vyžadována možnost připojení mezi různými místy.

### <a name="do-my-vnets-need-to-be-in-the-same-region"></a>Je nutné, aby virtuální sítě byly ve stejné oblasti?

Ne. Virtuální sítě se můžou nacházet ve stejné oblasti (umístění) Azure nebo v různých oblastech.

### <a name="if-the-vnets-arent-in-the-same-subscription-do-the-subscriptions-need-to-be-associated-with-the-same-active-directory-tenant"></a>Pokud virtuální sítě nejsou ve stejném předplatném, musí být předplatná přidružena ke stejnému klientovi služby Active Directory?

Ne.

### <a name="can-i-use-vnet-to-vnet-to-connect-virtual-networks-in-separate-azure-instances"></a>Je možné použít VNet-to-VNet k propojení virtuálních sítí v samostatných instancích Azure? 

Ne. VNet-to-VNet podporuje propojování virtuálních sítí v rámci stejné instance Azure. Například nelze vytvořit připojení mezi globální Azure a čínské/německé/americké vládní instance Azure. Zvažte použití připojení VPN mezi lokalitami pro tyto scénáře.

### <a name="can-i-use-vnet-to-vnet-along-with-multi-site-connections"></a>Je možné použít VNet-to-VNet společně s připojením propojujícím víc serverů?

Ano. Možnost připojení k virtuální síti je možné využívat současně se sítěmi VPN s více servery.

### <a name="how-many-on-premises-sites-and-virtual-networks-can-one-virtual-network-connect-to"></a>Ke kolika místních serverům a virtuálním sítím se může připojit jedna virtuální síť?

Podívejte se na tabulku [požadavků brány.](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#requirements)

### <a name="can-i-use-vnet-to-vnet-to-connect-vms-or-cloud-services-outside-of-a-vnet"></a>Je možné použít VNet-to-VNet k propojení virtuálních počítačů nebo cloudových služeb mimo virtuální síť?

Ne. Propojení VNet-to-VNet podporují propojování virtuálních sítí. Nepodporuje připojení virtuálních počítačů nebo cloudových služeb, které nejsou ve virtuální síti.

### <a name="can-a-cloud-service-or-a-load-balancing-endpoint-span-vnets"></a>Může cloudová služba nebo koncového bodu vyrovnávání zatížení span virtuální sítě?

Ne. Cloudová služba nebo koncový bod vyrovnávání zatížení se nemůže rozprostírat napříč virtuálními sítěmi, i když jsou propojené dohromady.

### <a name="can-i-use-a-policybased-vpn-type-for-vnet-to-vnet-or-multi-site-connections"></a>Můžu použít typ VPN založený na zásadách pro připojení virtuální sítě k virtuální síti nebo připojení s více lokalitami?

Ne. Připojení virtuální sítě k virtuální síti a připojení s více lokalitami vyžadují brány Azure VPN s typy VPN RouteBased (dříve nazývané dynamické směrování).

### <a name="can-i-connect-a-vnet-with-a-routebased-vpn-type-to-another-vnet-with-a-policybased-vpn-type"></a>Je možné připojit virtuální síť typu RouteBased k jiné virtuální síti typu PolicyBased?

Ne, obě virtuální sítě musí používat sítě VIRTUÁLNÍ SÍTĚ založené na směrování (dříve nazývané dynamické směrování).

### <a name="do-vpn-tunnels-share-bandwidth"></a>Sdílejí tunely VPN šířku pásma?

Ano. Všechna tunelová propojení sítí VPN v rámci virtuální sítě sdílejí v bráně VPN Azure šířku pásma, která je k dispozici, a stejnou smlouvu SLA pro dostupnost brány VPN v Azure.

### <a name="are-redundant-tunnels-supported"></a>Jsou podporovány redundantní tunely?

Redundantní tunely mezi párem virtuálních sítí jsou podporovány, pokud je jedna brána virtuální sítě nakonfigurována jako aktivní-aktivní.

### <a name="can-i-have-overlapping-address-spaces-for-vnet-to-vnet-configurations"></a>Můžou se překrývat adresní prostory pro konfigurace VNet-to-VNet?

Ne. Není možné, aby se rozsahy IP adres překrývaly.

### <a name="can-there-be-overlapping-address-spaces-among-connected-virtual-networks-and-on-premises-local-sites"></a>Můžou se překrývat adresní prostory mezi propojenými virtuálními sítěmi a místními servery?

Ne. Není možné, aby se rozsahy IP adres překrývaly.



