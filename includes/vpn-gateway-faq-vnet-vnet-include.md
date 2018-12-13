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
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53111908"
---
Nejčastější dotazy týkající se připojení typu VNet-to-VNet se týká připojení brány VPN. Informace o metodě VNet peering najdete v tématu [partnerský vztah virtuálních sítí](../articles/virtual-network/virtual-network-peering-overview.md).

### <a name="does-azure-charge-for-traffic-between-vnets"></a>Účtuje se v Azure provoz mezi virtuálními sítěmi?

Provoz VNet-to-VNet v rámci stejné oblasti je bezplatný v obou směrech, při použití připojení brány VPN. Výchozí přenos VNet-to-VNet mezi oblastmi se účtuje s rychlosti přenosu dat odchozí sítěmi podle zdrojových oblastí. Další informace najdete v tématu [VPN Gateway stránce s cenami](https://azure.microsoft.com/pricing/details/vpn-gateway/). Pokud se připojujete vašich virtuálních sítí pomocí VNet peering místo VPN gateway, přečtěte si téma [ceny služby Virtual network](https://azure.microsoft.com/pricing/details/virtual-network/).

### <a name="does-vnet-to-vnet-traffic-travel-across-the-internet"></a>Prochází provoz VNet-to-VNet přes internet?

Ne. Provoz VNet-to-VNet se přenáší prostřednictvím páteřní síti Microsoft Azure, nikoli po Internetu.

### <a name="can-i-establish-a-vnet-to-vnet-connection-across-azure-active-directory-aad-tenants"></a>Můžete vytvořit připojení VNet-to-VNet mezi tenanty Azure Active Directory (AAD)?

Ano, připojení VNet-to-VNet pomocí Azure VPN Gateway fungovat tenantů AAD.

### <a name="is-vnet-to-vnet-traffic-secure"></a>Je provoz VNet-to-VNet bezpečný?

Ano, je chráněný šifrováním protokolu IPsec/IKE.

### <a name="do-i-need-a-vpn-device-to-connect-vnets-together"></a>Je k propojení virtuálních sítí potřeba zařízení VPN?

Ne. Propojení více virtuálních sítí Azure nevyžaduje žádná zařízení VPN, pokud není vyžadována možnost připojení mezi různými místy.

### <a name="do-my-vnets-need-to-be-in-the-same-region"></a>Je nutné, aby virtuální sítě byly ve stejné oblasti?

Ne. Virtuální sítě se můžou nacházet ve stejné oblasti (umístění) Azure nebo v různých oblastech.

### <a name="if-the-vnets-arent-in-the-same-subscription-do-the-subscriptions-need-to-be-associated-with-the-same-active-directory-tenant"></a>Pokud tyto virtuální sítě nejsou ve stejném předplatném, předplatná musí být přidružená stejnému tenantu Active Directory?

Ne.

### <a name="can-i-use-vnet-to-vnet-to-connect-virtual-networks-in-separate-azure-instances"></a>Je možné použít VNet-to-VNet k propojení virtuálních sítí v samostatných instancích Azure? 

Ne. VNet-to-VNet podporuje propojování virtuálních sítí v rámci stejné instance Azure. Například nelze vytvořit připojení mezi globální Azure a čínština/němčina/US government Azure instancí. Zvažte použití připojení VPN typu Site-to-Site pro tyto scénáře.

### <a name="can-i-use-vnet-to-vnet-along-with-multi-site-connections"></a>Je možné použít VNet-to-VNet společně s připojením propojujícím víc serverů?

Ano. Možnost připojení k virtuální síti je možné využívat současně se sítěmi VPN s více servery.

### <a name="how-many-on-premises-sites-and-virtual-networks-can-one-virtual-network-connect-to"></a>Ke kolika místních serverům a virtuálním sítím se může připojit jedna virtuální síť?

Zobrazit [požadavky na bránu](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#requirements) tabulky.

### <a name="can-i-use-vnet-to-vnet-to-connect-vms-or-cloud-services-outside-of-a-vnet"></a>Je možné použít VNet-to-VNet k propojení virtuálních počítačů nebo cloudových služeb mimo virtuální síť?

Ne. Propojení VNet-to-VNet podporují propojování virtuálních sítí. Nepodporuje propojování virtuálních počítačů nebo cloudových služeb, které nejsou ve virtuální síti.

### <a name="can-a-cloud-service-or-a-load-balancing-endpoint-span-vnets"></a>Můžete cloudovou službu nebo koncový bod Vyrovnávání zatížení pracovat nad virtuálními sítěmi?

Ne. Cloudová služba nebo koncový bod Vyrovnávání zatížení nemohou pracovat nad více virtuálními sítěmi, i v případě, že jsou propojeny.

### <a name="can-i-use-a-policybased-vpn-type-for-vnet-to-vnet-or-multi-site-connections"></a>Můžete použít síť VPN typu PolicyBased pro připojení typu VNet-to-VNet nebo multi-Site?

Ne. Připojení typu VNet-to-VNet a multi-Site vyžadují brány Azure VPN s RouteBased (dříve nazývané dynamické směrování) typy sítě VPN.

### <a name="can-i-connect-a-vnet-with-a-routebased-vpn-type-to-another-vnet-with-a-policybased-vpn-type"></a>Je možné připojit virtuální síť typu RouteBased k jiné virtuální síti typu PolicyBased?

Ne, obě virtuální sítě musí používat VPN založené na směrování (dříve nazývané dynamické směrování).

### <a name="do-vpn-tunnels-share-bandwidth"></a>Sdílejí tunely VPN šířku pásma?

Ano. Všechna tunelová propojení sítí VPN v rámci virtuální sítě sdílejí v bráně VPN Azure šířku pásma, která je k dispozici, a stejnou smlouvu SLA pro dostupnost brány VPN v Azure.

### <a name="are-redundant-tunnels-supported"></a>Jsou podporovány redundantní tunely?

Redundantní tunely mezi párem virtuálních sítí jsou podporovány, pokud je jedna brána virtuální sítě nakonfigurována jako aktivní-aktivní.

### <a name="can-i-have-overlapping-address-spaces-for-vnet-to-vnet-configurations"></a>Můžou se překrývat adresní prostory pro konfigurace VNet-to-VNet?

Ne. Není možné, aby se rozsahy IP adres překrývaly.

### <a name="can-there-be-overlapping-address-spaces-among-connected-virtual-networks-and-on-premises-local-sites"></a>Můžou se překrývat adresní prostory mezi propojenými virtuálními sítěmi a místními servery?

Ne. Není možné, aby se rozsahy IP adres překrývaly.



