---
title: Zabezpečení sítě pro Azure Relay
description: Tento článek popisuje, jak pomocí Azure Relay používat pravidla brány firewall protokolu IP a soukromé koncové body.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 3aa3ffd119f65ec5181b0c382472cc4ef3c8bac4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91263721"
---
# <a name="network-security-for-azure-relay"></a>Zabezpečení sítě pro Azure Relay 
Tento článek popisuje, jak používat následující funkce zabezpečení pro Azure Relay: 

- Pravidla brány firewall protokolu IP (Preview)
- Soukromé koncové body (Preview)

> [!NOTE]
> Azure Relay nepodporuje koncové body síťové služby. 


## <a name="ip-firewall"></a>Brána firewall protokolu IP 
Ve výchozím nastavení jsou obory názvů Relay přístupné z Internetu, pokud požadavek přichází s platným ověřováním a autorizací. Pomocí brány firewall protokolu IP je můžete omezit na další jenom na sadu IPv4 adres nebo rozsahů IPv4 adres v zápisu [CIDR (bez třídy) (směrování Inter-Domain)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

Tato funkce je užitečná ve scénářích, ve kterých Azure Relay by měly být dostupné jenom z určitých dobře známých lokalit. Pravidla brány firewall umožňují konfigurovat pravidla pro příjem provozu pocházejících z konkrétních IPv4 adres. Pokud například používáte předávání pomocí [Azure Express Route](../expressroute/expressroute-faqs.md#supported-services), můžete vytvořit **pravidlo brány firewall** , které umožní provoz jenom z místních IP adres infrastruktury. 

Pravidla brány firewall protokolu IP se používají na úrovni oboru názvů přenosu. Proto se pravidla vztahují na všechna připojení z klientů pomocí libovolného podporovaného protokolu. Všechny pokusy o připojení z IP adresy, které neodpovídají povolenému pravidlu IP v oboru názvů přenosu, se odmítnou jako neautorizované. Odpověď nezmiňuje pravidlo protokolu IP. Pravidla filtru IP se aplikují v pořadí a první pravidlo, které odpovídá IP adrese, určuje akci přijmout nebo odmítnout.

Další informace najdete v tématu [Postup konfigurace brány firewall protokolu IP pro obor názvů přenosu](ip-firewall-virtual-networks.md) .

## <a name="private-endpoints"></a>Soukromé koncové body

**Služba privátního propojení** Azure umožňuje přístup ke službám Azure (například Azure Relay, Azure Service Bus, Azure Event Hubs, Azure Storage a Azure Cosmos DB) a hostovaným zákaznickým a partnerským službám Azure prostřednictvím privátního koncového bodu ve vaší virtuální síti. Další informace najdete v tématu [co je to Azure Private Link (Preview)?](../private-link/private-link-overview.md)

**Privátní koncový bod** je síťové rozhraní, které umožňuje, aby vaše úlohy spuštěné ve virtuální síti připojovaly soukromě a bezpečně ke službě, která má **prostředek privátního propojení** (například obor názvů přenosu). Privátní koncový bod používá privátní IP adresu z vaší virtuální sítě a efektivně ho přinášejí do vaší virtuální sítě. Veškerý provoz do služby se dá směrovat prostřednictvím privátního koncového bodu, takže se nevyžadují žádné brány, zařízení NAT, ExpressRoute, připojení VPN ani veřejné IP adresy. Přenosy mezi vaší virtuální sítí a službou procházejí přes páteřní síť Microsoftu, která eliminuje expozici veřejného Internetu. Úroveň členitosti můžete v řízení přístupu poskytnout tím, že povolíte připojení ke konkrétním oborům názvů Azure Relay.

> [!NOTE]
> Tato funkce je aktuálně ve **verzi Preview**. 

Další informace najdete v tématu [Konfigurace privátních koncových bodů](private-link-service.md) .


## <a name="next-steps"></a>Další kroky
Viz následující články:

- [Postup konfigurace brány firewall protokolu IP](ip-firewall-virtual-networks.md)
- [Postup konfigurace privátních koncových bodů](private-link-service.md)
