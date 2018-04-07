---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 04/05/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 66ff1e2e02728e05cb0aeedce90de1882a8804ce
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
Nejčastější dotazy týkající se propojení VNet-to-VNet se vztahují k připojení služby VPN Gateway. Pokud hledáte informace o VNet Peering, přečtěte si téma [Partnerské vztahy virtuálních sítí](../articles/virtual-network/virtual-network-peering-overview.md).

### <a name="does-azure-charge-for-traffic-between-vnets"></a>Účtuje se v Azure provoz mezi virtuálními sítěmi?

Při použití připojení brány VPN je provoz mezi virtuálními sítěmi v rámci stejné oblasti bezplatný v obou směrech. V případě různých oblastí je výchozí přenos VNet-to-VNet zpoplatněn jako odchozí přenos dat mezi virtuálními sítěmi podle zdrojových oblastí. Podrobnosti najdete na [stránce s cenami služby VPN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway/). Pokud virtuální sítě propojujete pomocí partnerského vztahu virtuálních sítí místo služby VPN Gateway, podívejte se na [stránku s cenami služby Virtual Network](https://azure.microsoft.com/pricing/details/virtual-network/).

### <a name="does-vnet-to-vnet-traffic-travel-across-the-internet"></a>Prochází provoz VNet-to-VNet přes internet?

Ne. Provoz VNet-to-VNet se přenáší prostřednictvím páteřní struktury systému Microsoft Azure, nikoli po internetu.

### <a name="can-i-establish-a-vnet-to-vnet-connection-across-aad-tenants"></a>Může vytvořit připojení VNet-to-VNet mezi klienty AAD?

Ano, připojení VNet-to-VNet pomocí Azure VPN Gateway fungovat na všech klientů AAD.

### <a name="is-vnet-to-vnet-traffic-secure"></a>Je provoz VNet-to-VNet bezpečný?

Ano, je chráněn šifrováním s použitím protokolu IPsec/IKE.

### <a name="do-i-need-a-vpn-device-to-connect-vnets-together"></a>Je k propojení virtuálních sítí potřeba zařízení VPN?

Ne. Propojení více virtuálních sítí Azure nevyžaduje žádná zařízení VPN, pokud není vyžadována možnost připojení mezi různými místy.

### <a name="do-my-vnets-need-to-be-in-the-same-region"></a>Je nutné, aby virtuální sítě byly ve stejné oblasti?

Ne. Virtuální sítě se můžou nacházet ve stejné oblasti (umístění) Azure nebo v různých oblastech.

### <a name="if-the-vnets-are-not-in-the-same-subscription-do-the-subscriptions-need-to-be-associated-with-the-same-ad-tenant"></a>Pokud virtuální sítě nejsou ve stejném předplatném, musí být přidružené ke stejnému tenantovi AD?

Ne.

### <a name="can-i-use-vnet-to-vnet-to-connect-virtual-networks-in-separate-azure-instances"></a>Je možné použít VNet-to-VNet k propojení virtuálních sítí v samostatných instancích Azure? 

Ne. VNet-to-VNet podporuje propojování virtuálních sítí v rámci stejné instance Azure. Například není možné vytvořit propojení mezi veřejnou instancí Azure a instancí Azure v oblastech Čína, Německo nebo USA (Gov). Pro tyto scénáře zvažte použití připojení VPN typu Site-to-Site.

### <a name="can-i-use-vnet-to-vnet-along-with-multi-site-connections"></a>Je možné použít VNet-to-VNet společně s připojením propojujícím víc serverů?

Ano. Možnost připojení k virtuální síti je možné využívat současně se sítěmi VPN s více servery.

### <a name="how-many-on-premises-sites-and-virtual-networks-can-one-virtual-network-connect-to"></a>Ke kolika místních serverům a virtuálním sítím se může připojit jedna virtuální síť?

Viz tabulka [Požadavky na bránu](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#requirements).

### <a name="can-i-use-vnet-to-vnet-to-connect-vms-or-cloud-services-outside-of-a-vnet"></a>Je možné použít VNet-to-VNet k propojení virtuálních počítačů nebo cloudových služeb mimo virtuální síť?

Ne. Propojení VNet-to-VNet podporují propojování virtuálních sítí. Nepodporuje propojování virtuálních počítačů ani cloudových služeb mimo virtuální síť.

### <a name="can-a-cloud-service-or-a-load-balancing-endpoint-span-vnets"></a>Může cloudová služba nebo koncový bod vyrovnávání zatížení pracovat nad více virtuálními sítěmi?

Ne. Cloudová služba ani koncový bod vyrovnávání zatížení nemůžou pracovat nad více virtuálními sítěmi ani v případě, že jsou propojeny.

### <a name="can-i-used-a-policybased-vpn-type-for-vnet-to-vnet-or-multi-site-connections"></a>Je možné použít typ sítě VPN PolicyBased pro připojení VNet-to-VNet nebo Multi-Site?

Ne. Připojení VNet-to-VNet a Multi-Site vyžadují brány VPN Azure s typy sítě VPN RouteBased (dříve nazývané dynamické směrování).

### <a name="can-i-connect-a-vnet-with-a-routebased-vpn-type-to-another-vnet-with-a-policybased-vpn-type"></a>Je možné připojit virtuální síť typu RouteBased k jiné virtuální síti typu PolicyBased?

Ne, obě virtuální sítě MUSÍ používat sítě VPN založené na směrování (dříve nazývané dynamické směrování).

### <a name="do-vpn-tunnels-share-bandwidth"></a>Sdílejí tunely VPN šířku pásma?

Ano. Všechna tunelová propojení sítí VPN v rámci virtuální sítě sdílejí v bráně VPN Azure šířku pásma, která je k dispozici, a stejnou smlouvu SLA pro dostupnost brány VPN v Azure.

### <a name="are-redundant-tunnels-supported"></a>Jsou podporovány redundantní tunely?

Redundantní tunely mezi párem virtuálních sítí jsou podporovány, pokud je jedna brána virtuální sítě nakonfigurována jako aktivní-aktivní.

### <a name="can-i-have-overlapping-address-spaces-for-vnet-to-vnet-configurations"></a>Můžou se překrývat adresní prostory pro konfigurace VNet-to-VNet?

Ne. Není možné, aby se rozsahy IP adres překrývaly.

### <a name="can-there-be-overlapping-address-spaces-among-connected-virtual-networks-and-on-premises-local-sites"></a>Můžou se překrývat adresní prostory mezi propojenými virtuálními sítěmi a místními servery?

Ne. Není možné, aby se rozsahy IP adres překrývaly.



