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
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "67174828"
---
Nejčastější dotazy k VNet-to-VNet se vztahují na připojení brány VPN Gateway. Informace o partnerském vztahu virtuálních sítí najdete v tématu věnovaném [partnerským vztahům virtuální sítě](../articles/virtual-network/virtual-network-peering-overview.md).

### <a name="does-azure-charge-for-traffic-between-vnets"></a>Účtuje se v Azure provoz mezi virtuálními sítěmi?

Provoz VNet-to-VNet v rámci stejné oblasti je v obou směrech zdarma pro použití připojení brány VPN. Odchozí přenosy mezi oblastmi VNet-to-VNet se účtují podle sazeb za odchozí přenos dat mezi virtuálními sítěmi podle zdrojových oblastí. Další informace najdete na [stránce s cenami VPN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway/). Pokud připojujete virtuální sítě pomocí partnerského vztahu virtuálních sítí namísto brány VPN Gateway, přečtěte si téma [ceny služby Virtual Network](https://azure.microsoft.com/pricing/details/virtual-network/).

### <a name="does-vnet-to-vnet-traffic-travel-across-the-internet"></a>Prochází provoz mezi virtuálními sítěmi přes Internet?

No. Provoz VNet-to-VNet se přenáší přes Microsoft Azure páteřní síť, nikoli na Internet.

### <a name="can-i-establish-a-vnet-to-vnet-connection-across-azure-active-directory-aad-tenants"></a>Můžu navázat připojení typu VNet-to-VNet mezi klienty Azure Active Directory (AAD)?

Ano, připojení typu VNet-to-VNet, která používají brány Azure VPN Gateway, fungují napříč klienty AAD.

### <a name="is-vnet-to-vnet-traffic-secure"></a>Je provoz VNet-to-VNet bezpečný?

Ano, je chráněn šifrováním pomocí protokolu IPsec/IKE.

### <a name="do-i-need-a-vpn-device-to-connect-vnets-together"></a>Je k propojení virtuálních sítí potřeba zařízení VPN?

No. Propojení více virtuálních sítí Azure nevyžaduje žádná zařízení VPN, pokud není vyžadována možnost připojení mezi různými místy.

### <a name="do-my-vnets-need-to-be-in-the-same-region"></a>Je nutné, aby virtuální sítě byly ve stejné oblasti?

No. Virtuální sítě se můžou nacházet ve stejné oblasti (umístění) Azure nebo v různých oblastech.

### <a name="if-the-vnets-arent-in-the-same-subscription-do-the-subscriptions-need-to-be-associated-with-the-same-active-directory-tenant"></a>Pokud virtuální sítě nejsou ve stejném předplatném, musí být předplatné přidruženo ke stejnému tenantovi Active Directory?

No.

### <a name="can-i-use-vnet-to-vnet-to-connect-virtual-networks-in-separate-azure-instances"></a>Je možné použít VNet-to-VNet k propojení virtuálních sítí v samostatných instancích Azure? 

No. VNet-to-VNet podporuje propojování virtuálních sítí v rámci stejné instance Azure. Například nemůžete vytvořit připojení mezi globálními instancemi Azure a čínskou/německý/vládou USA. Pro tyto scénáře zvažte použití připojení VPN typu Site-to-site.

### <a name="can-i-use-vnet-to-vnet-along-with-multi-site-connections"></a>Je možné použít VNet-to-VNet společně s připojením propojujícím víc serverů?

Ano. Možnost připojení k virtuální síti je možné využívat současně se sítěmi VPN s více servery.

### <a name="how-many-on-premises-sites-and-virtual-networks-can-one-virtual-network-connect-to"></a>Ke kolika místních serverům a virtuálním sítím se může připojit jedna virtuální síť?

Podívejte se na tabulku [požadavky na bránu](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#requirements) .

### <a name="can-i-use-vnet-to-vnet-to-connect-vms-or-cloud-services-outside-of-a-vnet"></a>Je možné použít VNet-to-VNet k propojení virtuálních počítačů nebo cloudových služeb mimo virtuální síť?

No. Propojení VNet-to-VNet podporují propojování virtuálních sítí. Nepodporuje propojování virtuálních počítačů ani cloudových služeb, které nejsou ve virtuální síti.

### <a name="can-a-cloud-service-or-a-load-balancing-endpoint-span-vnets"></a>Může se virtuální sítě cloudová služba nebo koncový bod vyrovnávání zatížení?

No. Cloudová služba nebo koncový bod pro vyrovnávání zatížení nemůžou být napříč virtuálními sítěmi, i když jsou připojené dohromady.

### <a name="can-i-use-a-policybased-vpn-type-for-vnet-to-vnet-or-multi-site-connections"></a>Můžu použít typ sítě VPN PolicyBased pro připojení typu VNet-to-VNet nebo Multi-Site?

No. Připojení typu VNet-to-VNet a Multi-Site vyžadují brány VPN Azure s typy sítě VPN RouteBased (dříve nazývané dynamické směrování).

### <a name="can-i-connect-a-vnet-with-a-routebased-vpn-type-to-another-vnet-with-a-policybased-vpn-type"></a>Je možné připojit virtuální síť typu RouteBased k jiné virtuální síti typu PolicyBased?

Ne, obě virtuální sítě musí používat sítě VPN založené na směrování (dříve nazývané dynamické směrování).

### <a name="do-vpn-tunnels-share-bandwidth"></a>Sdílejí tunely VPN šířku pásma?

Ano. Všechna tunelová propojení sítí VPN v rámci virtuální sítě sdílejí v bráně VPN Azure šířku pásma, která je k dispozici, a stejnou smlouvu SLA pro dostupnost brány VPN v Azure.

### <a name="are-redundant-tunnels-supported"></a>Jsou podporovány redundantní tunely?

Redundantní tunely mezi párem virtuálních sítí jsou podporovány, pokud je jedna brána virtuální sítě nakonfigurována jako aktivní-aktivní.

### <a name="can-i-have-overlapping-address-spaces-for-vnet-to-vnet-configurations"></a>Můžou se překrývat adresní prostory pro konfigurace VNet-to-VNet?

No. Není možné, aby se rozsahy IP adres překrývaly.

### <a name="can-there-be-overlapping-address-spaces-among-connected-virtual-networks-and-on-premises-local-sites"></a>Můžou se překrývat adresní prostory mezi propojenými virtuálními sítěmi a místními servery?

No. Není možné, aby se rozsahy IP adres překrývaly.



