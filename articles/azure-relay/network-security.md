---
title: Zabezpečení sítě pro Azure Relay
description: Tento článek popisuje, jak nakonfigurovat přístup z privátních koncových bodů.
services: service-bus-relay
author: spelluru
ms.service: service-bus-relay
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: spelluru
ms.openlocfilehash: d0a0d03680877ae78f5af939f8d47e4e426abadb
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211011"
---
# <a name="network-security-for-azure-relay"></a>Zabezpečení sítě pro Azure Relay 
Tento článek popisuje, jak používat následující funkce zabezpečení pro Azure Relay: 

- Pravidla brány firewall protokolu IP (Preview)
- Soukromé koncové body (Preview)

> [!NOTE]
> Azure Relay nepodporuje koncové body síťové služby. 


## <a name="ip-firewall"></a>Brána firewall protokolu IP 
Ve výchozím nastavení jsou obory názvů Relay přístupné z Internetu, pokud požadavek přichází s platným ověřováním a autorizací. Pomocí brány firewall protokolu IP je můžete omezit na více než jenom na sadu IPv4 adres nebo rozsahů IPv4 adres v [CIDR (směrování mezi doménami bez třídy)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

Tato funkce je užitečná ve scénářích, ve kterých Azure Relay by měly být dostupné jenom z určitých dobře známých lokalit. Pravidla brány firewall umožňují konfigurovat pravidla pro příjem provozu pocházejících z konkrétních IPv4 adres. Pokud například používáte předávání pomocí [Azure Express Route](/azure/expressroute/expressroute-faqs#supported-services), můžete vytvořit **pravidlo brány firewall** , které umožní provoz jenom z místních IP adres infrastruktury. 

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