---
title: Zabezpečení sítě pro centra událostí Azure
description: Tento článek popisuje, jak nakonfigurovat přístup z privátní koncové body
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: 46e6a9ecc2ed09aed1076f12c1f61a966485bdad
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422767"
---
# <a name="network-security-for-azure-event-hubs"></a>Zabezpečení sítě pro centra událostí Azure 
Tento článek popisuje, jak používat následující funkce zabezpečení s Azure Event Hubs: 

- Značky služeb
- Pravidla brány firewall IP
- Koncové body síťové služby
- Soukromé koncové body (náhled)


## <a name="service-tags"></a>Značky služeb
Značka služby představuje skupinu předpon IP adres z dané služby Azure. Společnost Microsoft spravuje předpony adres zahrnuté v servisním štítku a automaticky aktualizuje výrobní číslo, jak se adresy mění, čímž minimalizuje složitost častých aktualizací pravidel zabezpečení sítě. Další informace o značkách služeb naleznete v tématu [Service tags overview](../virtual-network/service-tags-overview.md).

Pomocí značek služeb můžete definovat ovládací prvky přístupu k síti ve [skupinách](../virtual-network/security-overview.md#security-rules) zabezpečení sítě nebo [azure firewall](../firewall/service-tags.md). Při vytváření pravidel zabezpečení používejte značky služeb místo konkrétních adres IP. Zadáním názvu značky služby (například **EventHub)** v příslušném *zdrojovém* nebo *cílovém* poli pravidla můžete povolit nebo odepřít provoz pro odpovídající službu.

| Značka služby | Účel | Můžete použít příchozí nebo odchozí? | Může být regionální? | Můžete použít s Azure Firewall? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **Centrum událostí** | Centra událostí Azure. | Odchozí | Ano | Ano |


## <a name="ip-firewall"></a>Brána firewall protokolu IP 
Ve výchozím nastavení jsou obory názvů Event Hubs přístupné z Internetu, pokud je požadavek dodáván s platným ověřováním a autorizací. S ip firewallem ji můžete dále omezit pouze na sadu adres IPv4 nebo rozsahy adres IPv4 v zápisu [CIDR (Classless Inter-Domain Routing).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)

Tato funkce je užitečná ve scénářích, ve kterých azure event huby by měly být přístupné jenom z určitých známých webů. Pravidla brány firewall umožňují konfigurovat pravidla pro přijímání přenosů pocházejících z konkrétních adres IPv4. Pokud například používáte centra událostí s [Azure Express Route](/azure/expressroute/expressroute-faqs#supported-services), můžete vytvořit pravidlo brány **firewall,** které povolí provoz pouze z vašich ip adres infrastruktury. 

Pravidla brány firewall IP se použijí na úrovni oboru názvů Event Hubs. Proto pravidla platí pro všechna připojení z klientů pomocí libovolného podporovaného protokolu. Jakýkoli pokus o připojení z adresy IP, která neodpovídá povolenému pravidlu PROTOKOLU IP v oboru názvů Event Hubs, je odmítnut jako neoprávněný. Odpověď neuvádí pravidlo IP. Pravidla filtru IP jsou použita v pořadí a první pravidlo, které odpovídá adrese IP, určuje akci přijmout nebo odmítnout.

Další informace naleznete v tématu [Jak nakonfigurovat bránu firewall IP pro centrum událostí](event-hubs-ip-filtering.md)

## <a name="network-service-endpoints"></a>Koncové body síťové služby
Integrace event hubů s [koncovými body služby Virtuální síť (Virtuální síť)](../virtual-network/virtual-network-service-endpoints-overview.md) umožňuje zabezpečený přístup k možnostem zasílání zpráv z úloh, jako jsou virtuální počítače, které jsou vázané na virtuální sítě, přičemž cesta síťového provozu je zabezpečena na obou koncích.

Po nakonfigurované matné síti na vázaný na alespoň jeden koncový bod služby podsítě virtuální sítě nepřijímá obor názvů příslušných center událostí provoz odkudkoli, ale autorizované podsítě ve virtuálních sítích. Z hlediska virtuální sítě vazba oboru názvů Event Hubs na koncový bod služby konfiguruje izolovaný síťový tunel z podsítě virtuální sítě do služby zasílání zpráv. 

Výsledkem je soukromý a izolovaný vztah mezi úlohami vázanými na podsíť a příslušným oborem názvů Event Hubs, a to navzdory tomu, že pozorovatelná síťová adresa koncového bodu služby zasílání zpráv je ve veřejném rozsahu IP adres. Existuje výjimka z tohoto chování. Povolení koncového bodu služby ve `denyall` výchozím nastavení umožňuje pravidlo v [bráně firewall IP](event-hubs-ip-filtering.md) přidružené k virtuální síti. Do brány firewall IP můžete přidat konkrétní ip adresy, které umožní přístup k veřejnému koncovému bodu Centra událostí. 

> [!IMPORTANT]
> Virtuální sítě jsou podporovány ve **standardních** a **vyhrazených** úrovních event hubů. Není podporována v **základní** vrstvě.

### <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Pokročilé scénáře zabezpečení povolené integrací virtuální sítě 

Řešení, která vyžadují těsné a rozčleněné zabezpečení a kde podsítě virtuálních sítí poskytují segmentaci mezi rozčleněné služby, stále potřebují komunikační cesty mezi službami, které jsou v těchto oddílech.

Jakákoli okamžitá trasa IP mezi oddíly, včetně těch, které přenášejí protokol HTTPS přes Protokol TCP/IP, s sebou nese riziko zneužití zranitelných míst ze síťové vrstvy. Služby zasílání zpráv poskytují izolované komunikační cesty, kde jsou zprávy dokonce zapsány na disk při přechodu mezi stranami. Úlohy ve dvou odlišných virtuálních sítích, které jsou vázány na stejnou instanci Event Hubs, mohou efektivně a spolehlivě komunikovat prostřednictvím zpráv, zatímco je zachována integrita hranice izolace sítě.
 
To znamená, že vaše cloudová řešení citlivá na zabezpečení nejen získají přístup k špičkovým spolehlivým a škálovatelným funkcím zasílání zpráv azure, ale teď můžou pomocí zasílání zpráv vytvářet komunikační cesty mezi zabezpečenými oddíly řešení, které jsou ze své podstaty bezpečnější než to, čeho je dosažitelné v jakémkoli režimu komunikace peer-to-peer, včetně protokolu HTTPS a dalších protokolů soketu zabezpečených protokoly TLS.

### <a name="bind-event-hubs-to-virtual-networks"></a>Vazba rozbočovačů událostí s virtuálními sítěmi

**Pravidla virtuální sítě** jsou funkce zabezpečení brány firewall, která řídí, zda váš obor názvů Centra událostí Azure přijímá připojení z určité podsítě virtuální sítě.

Vazba oboru názvů Event Hubs do virtuální sítě je dvoustupňový proces. Nejprve je potřeba vytvořit **koncový bod virtuální síťové služby** v podsíti virtuální sítě a povolit jej pro **Microsoft.EventHub,** jak je vysvětleno v článku [přehledu koncového bodu služby.](../virtual-network/virtual-network-service-endpoints-overview.md) Po přidání koncového bodu služby svážete obor názvů Event Hubs s **pravidlem virtuální sítě**.

Pravidlo virtuální sítě je přidružení oboru názvů Event Hubs s podsítí virtuální sítě. Zatímco pravidlo existuje, všem úlohám vázaným na podsíť je udělen přístup k oboru názvů Event Hubs. Centrum událostí sám nikdy nevytvoří odchozí připojení, není nutné získat přístup, a proto je nikdy udělen přístup k podsíti povolením tohoto pravidla.

Další informace najdete v tématu [Jak nakonfigurovat koncové body služby virtuální sítě pro centrum událostí.](event-hubs-service-endpoints.md)

## <a name="private-endpoints"></a>Soukromé koncové body

[Služba Azure Private Link](../private-link/private-link-overview.md) umožňuje přístup ke službám Azure Services (například Azure Event Hubs, Azure Storage a Azure Cosmos DB) a službám hostovaným zákazníkům/partnerům Azure přes **privátní koncový bod** ve vaší virtuální síti.

Privátní koncový bod je síťové rozhraní, které vás soukromě a bezpečně připojí ke službě využívající Azure Private Link. Privátní koncový bod používá privátní IP adresu z vaší virtuální sítě, efektivně uvedení služby do virtuální sítě. Veškerý provoz na službu lze směrovat přes soukromý koncový bod, takže nejsou potřeba žádné brány, zařízení NAT, připojení ExpressRoute nebo VPN nebo veřejné IP adresy. Provoz mezi vaší virtuální sítí a službou prochází přes páteřní síť Microsoftu a eliminuje rizika vystavení na veřejném internetu. Můžete se připojit k instanci prostředku Azure, což vám nejvyšší úroveň rozlišovací schopnost v řízení přístupu.

> [!NOTE]
> Tato funkce je podporována pouze s **vyhrazenou** vrstvou. Další informace o vyhrazené vrstvě najdete v [tématu Přehled vyhrazených center událostí](event-hubs-dedicated-overview.md). 
>
> Tato funkce je aktuálně ve **verzi Preview**. 


Další informace najdete v tématu [Konfigurace privátní koncové body pro centrum událostí](private-link-service.md)


## <a name="next-steps"></a>Další kroky
Viz následující články:

- [Konfigurace brány firewall IP pro centrum událostí](event-hubs-ip-filtering.md)
- [Konfigurace koncových bodů služby virtuální sítě pro centrum událostí](event-hubs-service-endpoints.md)
- [Konfigurace privátníkoncové koncové body pro centrum událostí](private-link-service.md)