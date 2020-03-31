---
title: Zabezpečení sítě pro Azure Service Bus
description: Tento článek popisuje funkce zabezpečení sítě, jako jsou značky služeb, pravidla brány firewall IP, koncové body služby a soukromé koncové body.
services: service-bus-messaging
documentationcenter: .net
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: aschhab
ms.openlocfilehash: 95f8c2a3b47b59bab7df909be43dacdb1f9c58f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479277"
---
# <a name="network-security-for-azure-service-bus"></a>Zabezpečení sítě pro Azure Service Bus 
Tento článek popisuje, jak používat následující funkce zabezpečení s Azure Service Bus: 

- Značky služeb
- Pravidla brány firewall IP
- Koncové body síťové služby
- Soukromé koncové body (náhled)


## <a name="service-tags"></a>Značky služeb
Značka služby představuje skupinu předpon IP adres z dané služby Azure. Společnost Microsoft spravuje předpony adres zahrnuté v servisním štítku a automaticky aktualizuje výrobní číslo, jak se adresy mění, čímž minimalizuje složitost častých aktualizací pravidel zabezpečení sítě. Další informace o značkách služeb naleznete v tématu [Service tags overview](../virtual-network/service-tags-overview.md).

Pomocí značek služeb můžete definovat ovládací prvky přístupu k síti ve [skupinách zabezpečení sítě](../virtual-network/security-overview.md#security-rules) nebo [azure firewall](../firewall/service-tags.md). Při vytváření pravidel zabezpečení používejte značky služeb místo konkrétních adres IP. Zadáním názvu značky služby (například **ServiceBus)** v příslušném *zdrojovém* nebo *cílovém* poli pravidla můžete povolit nebo odepřít provoz pro odpovídající službu.

| Značka služby | Účel | Můžete použít příchozí nebo odchozí? | Může být regionální? | Můžete použít s Azure Firewall? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **ServiceBus** | Provoz Azure Service Bus, který používá úroveň služby Premium. | Odchozí | Ano | Ano |


## <a name="ip-firewall"></a>Brána firewall protokolu IP 
Ve výchozím nastavení jsou obory názvů služby Service Bus přístupné z Internetu, pokud je požadavek dodáván s platným ověřováním a autorizací. S ip firewallem ji můžete dále omezit pouze na sadu adres IPv4 nebo rozsahy adres IPv4 v zápisu [CIDR (Classless Inter-Domain Routing).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)

Tato funkce je užitečná ve scénářích, ve kterých by azure service bus měla být přístupná jenom z určitých známých webů. Pravidla brány firewall umožňují konfigurovat pravidla pro přijímání přenosů pocházejících z konkrétních adres IPv4. Pokud například používáte službu Service Bus s [Azure Express Route][express-route], můžete vytvořit **pravidlo brány firewall,** které povolí provoz pouze z vašich místních IP adres infrastruktury nebo adres podnikové brány NAT. 

Pravidla brány firewall IP jsou použita na úrovni oboru názvů Service Bus. Proto pravidla platí pro všechna připojení z klientů pomocí libovolného podporovaného protokolu. Jakýkoli pokus o připojení z adresy IP, která neodpovídá povolenému pravidlu PROTOKOLU IP v oboru názvů service bus, je odmítnut jako neoprávněný. Odpověď neuvádí pravidlo IP. Pravidla filtru IP jsou použita v pořadí a první pravidlo, které odpovídá adrese IP, určuje akci přijmout nebo odmítnout.

Další informace naleznete v tématu [Jak nakonfigurovat bránu firewall IP pro obor názvů služby Service Bus](service-bus-ip-filtering.md)

## <a name="network-service-endpoints"></a>Koncové body síťové služby
Integrace koncových bodů služby Service Bus s [virtuální sítí (VNet)](service-bus-service-endpoints.md) umožňuje zabezpečený přístup k možnostem zasílání zpráv z úloh, jako jsou virtuální počítače, které jsou vázané na virtuální sítě, přičemž cesta síťového provozu je zabezpečena na obou koncích.

Po nakonfigurované matné síti je vázán a má být vázán alespoň na jeden koncový bod služby podsítě virtuální sítě, příslušný obor názvů Service Bus již nebude přijímat přenosy odkudkoli, ale autorizované virtuální sítě. Z hlediska virtuální sítě vazba oboru názvů Service Bus na koncový bod služby konfiguruje izolovaný síťový tunel z podsítě virtuální sítě do služby zasílání zpráv.

Výsledkem je soukromý a izolovaný vztah mezi úlohami vázanými na podsíť a příslušným oborem názvů Service Bus, a to navzdory tomu, že pozorovatelná síťová adresa koncového bodu služby zasílání zpráv je ve veřejném rozsahu IP adres.

> [!IMPORTANT]
> Virtuální sítě jsou podporovány pouze v oborech názvů service bus [úrovně Premium.](service-bus-premium-messaging.md)
> 
> Při použití koncových bodů služby virtuální sítě se službou Service Bus byste neměli povolit tyto koncové body v aplikacích, které kombinují obory názvů service bus úrovně Standard a Premium. Protože úroveň Standard nepodporuje virtuální sítě. Koncový bod je omezen pouze na obory názvů úrovně Premium.

### <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Pokročilé scénáře zabezpečení povolené integrací virtuální sítě 

Řešení, která vyžadují těsné a rozčleněné zabezpečení a kde podsítě virtuálních sítí poskytují segmentaci mezi rozčleněné služby, obecně stále potřebují komunikační cesty mezi službami, které jsou v těchto oddílech.

Jakákoli okamžitá trasa IP mezi oddíly, včetně těch, které přenášejí protokol HTTPS přes Protokol TCP/IP, s sebou nese riziko zneužití zranitelných míst ze síťové vrstvy. Služby zasílání zpráv poskytují zcela izolované komunikační cesty, kde jsou zprávy dokonce zapsány na disk při přechodu mezi stranami. Úlohy ve dvou odlišných virtuálních sítích, které jsou vázány na stejnou instanci service bus můžete komunikovat efektivně a spolehlivě prostřednictvím zpráv, zatímco příslušná integrita hranice izolace sítě je zachována.
 
To znamená, že vaše cloudová řešení citlivá na zabezpečení nejen získají přístup k špičkovým spolehlivým a škálovatelným funkcím zasílání zpráv azure, ale teď můžou používat zasílání zpráv k vytváření komunikačních cest mezi zabezpečenými komnatami řešení, které jsou ze své podstaty bezpečnější než to, čeho je dosažitelné v libovolném režimu komunikace peer-to-peer, včetně protokolu HTTPS a dalších protokolů soketu zabezpečených protokoly TLS.

### <a name="bind-service-bus-to-virtual-networks"></a>Vázat sběrnici do virtuálních sítí

*Pravidla virtuální sítě* jsou funkce zabezpečení brány firewall, která určuje, zda server Azure Service Bus přijímá připojení z určité podsítě virtuální sítě.

Vazba oboru názvů service bus do virtuální sítě je dvoustupňový proces. Nejprve je třeba vytvořit **koncový bod služby virtuální sítě** v podsíti virtuální sítě a povolit jej pro **Microsoft.ServiceBus,** jak je vysvětleno v [přehledu koncového bodu služby](service-bus-service-endpoints.md). Po přidání koncového bodu služby svážete obor názvů Service Bus s **pravidlem virtuální sítě**.

Pravidlo virtuální sítě je přidružení oboru názvů Service Bus s podsítí virtuální sítě. Zatímco pravidlo existuje, všem úlohám vázaným na podsíť je udělen přístup k oboru názvů Service Bus. Service Bus sám nikdy naváže odchozí připojení, není nutné získat přístup a proto je nikdy udělen přístup k podsíti povolením tohoto pravidla.

Další informace naleznete v tématu [Jak nakonfigurovat koncové body služby virtuální sítě pro obor názvů služby Service Bus.](service-bus-service-endpoints.md)

## <a name="private-endpoints"></a>Soukromé koncové body

Služba Azure Private Link Service umožňuje přístup ke službám Azure (například Azure Service Bus, Azure Storage a Azure Cosmos DB) a službám hostovaným zákazníkům/partnerům Azure přes **privátní koncový bod** ve vaší virtuální síti.

Privátní koncový bod je síťové rozhraní, které vás soukromě a bezpečně připojí ke službě využívající Azure Private Link. Privátní koncový bod používá privátní IP adresu z vaší virtuální sítě, efektivně uvedení služby do virtuální sítě. Veškerý provoz na službu lze směrovat přes soukromý koncový bod, takže nejsou potřeba žádné brány, zařízení NAT, připojení ExpressRoute nebo VPN nebo veřejné IP adresy. Provoz mezi vaší virtuální sítí a službou prochází přes páteřní síť Microsoftu a eliminuje rizika vystavení na veřejném internetu. Můžete se připojit k instanci prostředku Azure, což vám nejvyšší úroveň rozlišovací schopnost v řízení přístupu.

Další informace najdete v tématu [Co je privátní propojení Azure?](../private-link/private-link-overview.md)

> [!NOTE]
> Tato funkce je podporovaná s **prémiovou** úrovní Azure Service Bus. Další informace o úrovni premium najdete v článku [úrovně service bus premium a standardní zasílání zpráv.](service-bus-premium-messaging.md)
>
> Tato funkce je aktuálně ve **verzi Preview**. 


Další informace naleznete v tématu [Konfigurace privátní koncové body pro obor názvů Service Bus](private-link-service.md)


## <a name="next-steps"></a>Další kroky
Viz následující články:

- [Konfigurace brány firewall IP pro obor názvů Service Bus](service-bus-ip-filtering.md)
- [Konfigurace koncových bodů služby virtuální sítě pro obor názvů služby Service Bus](service-bus-service-endpoints.md)
- [Konfigurace privátníkoncové koncové body pro obor názvů Service Bus](private-link-service.md)
