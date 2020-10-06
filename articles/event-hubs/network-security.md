---
title: Zabezpečení sítě pro Azure Event Hubs
description: Tento článek popisuje, jak nakonfigurovat přístup z privátních koncových bodů.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: ae6cbdc8258cde9bb2da961cb452f996f0797cfe
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91767784"
---
# <a name="network-security-for-azure-event-hubs"></a>Zabezpečení sítě pro Azure Event Hubs 
Tento článek popisuje, jak používat následující funkce zabezpečení pro Azure Event Hubs: 

- Značky služeb
- Pravidla brány firewall protokolu IP
- Koncové body síťové služby
- Soukromé koncové body


## <a name="service-tags"></a>Značky služeb
Značka služby představuje skupinu předpon IP adres z dané služby Azure. Společnost Microsoft spravuje předpony adres, které jsou zahrnuté ve značce služby, a automaticky aktualizuje značku služby, protože se mění adresy. tím se minimalizuje složitost častých aktualizací pravidel zabezpečení sítě. Další informace o značkách služby najdete v tématu [Přehled značek služeb](../virtual-network/service-tags-overview.md).

Pomocí značek služeb můžete definovat řízení přístupu k síti pro [skupiny zabezpečení sítě](../virtual-network/security-overview.md#security-rules)   nebo [Azure firewall](../firewall/service-tags.md). Při vytváření pravidel zabezpečení používejte značky služby místo konkrétních IP adres. Zadáním názvu značky služby (například **EventHub**) v příslušném *zdrojovém*   nebo *cílovém*   poli pravidla můžete povolit nebo odepřít provoz pro příslušnou službu.

| Značka služby | Účel | Dá se použít příchozí nebo odchozí? | Je možné je rozregionovat? | Lze použít s Azure Firewall? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **Centrum událostí** | Event Hubs Azure. | Odchozí | Ano | Ano |


## <a name="ip-firewall"></a>Brána firewall protokolu IP 
Ve výchozím nastavení jsou Event Hubs obory názvů přístupné z Internetu, pokud požadavek přichází s platným ověřováním a autorizací. Pomocí brány firewall protokolu IP je můžete omezit na více než jenom na sadu IPv4 adres nebo rozsahů IPv4 adres v [CIDR (směrování mezi doménami bez třídy)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

Tato funkce je užitečná ve scénářích, ve kterých by měl být Azure Event Hubs dostupný jenom z určitých dobře známých lokalit. Pravidla brány firewall umožňují konfigurovat pravidla pro příjem provozu pocházejících z konkrétních IPv4 adres. Pokud například používáte Event Hubs s využitím [Azure Express Route](../expressroute/expressroute-faqs.md#supported-services), můžete vytvořit **pravidlo brány firewall** , které umožní provoz jenom z vašich místních IP adres infrastruktury. 

Pravidla brány firewall protokolu IP se používají na úrovni oboru názvů Event Hubs. Proto se pravidla vztahují na všechna připojení z klientů pomocí libovolného podporovaného protokolu. Všechny pokusy o připojení z IP adresy, které neodpovídají povolenému pravidlu IP v oboru názvů Event Hubs, se odmítnou jako neoprávněné. Odpověď nezmiňuje pravidlo protokolu IP. Pravidla filtru IP se aplikují v pořadí a první pravidlo, které odpovídá IP adrese, určuje akci přijmout nebo odmítnout.

Další informace najdete v tématu [Postup konfigurace brány firewall protokolu IP pro centrum událostí](event-hubs-ip-filtering.md) .

## <a name="network-service-endpoints"></a>Koncové body síťové služby
Integrace Event Hubs s [koncovými body služby Virtual Network (VNET)](../virtual-network/virtual-network-service-endpoints-overview.md) umožňuje zabezpečenému přístupu k funkcím zasílání zpráv z úloh, jako jsou virtuální počítače, které jsou svázané s virtuálními sítěmi, a cestu síťového provozu, která je zabezpečená na obou koncích.

Po navázání vazby na alespoň jeden koncový bod služby virtuální sítě musí příslušný obor názvů Event Hubs nadále přijímat provoz z libovolného místa, ale z nich budou ověřeny podsítě ve virtuálních sítích. Z perspektivy virtuální sítě naváže obor názvů Event Hubs na koncový bod služby a nakonfiguruje izolovaný síťový tunel z podsítě virtuální sítě do služby zasílání zpráv. 

Výsledkem je privátní a izolovaný vztah mezi úlohami vázanými na podsíť a odpovídajícím oborem názvů Event Hubs, a to i přes pozorovatelnou síťovou adresu koncového bodu služby zasílání zpráv ve veřejném rozsahu IP adres. K tomuto chování existuje výjimka. Povolení koncového bodu služby ve výchozím nastavení povolí `denyall` pravidlo v [bráně firewall protokolu IP](event-hubs-ip-filtering.md) přidružené k virtuální síti. Můžete přidat konkrétní IP adresy v bráně firewall protokolu IP a povolit tak přístup ke veřejnému koncovému bodu centra událostí. 

> [!IMPORTANT]
> Virtuální sítě jsou podporovány ve **standardních** a **vyhrazených** úrovních Event Hubs. Na úrovni **Basic** se nepodporuje.

### <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Pokročilé scénáře zabezpečení povolené integrací virtuální sítě 

Řešení, která vyžadují těsné a compartmentalized zabezpečení a kde podsítě virtuální sítě poskytují segmentaci mezi službami compartmentalized, stále potřebují komunikační cesty mezi službami, které se nacházejí v těchto oddílech.

Veškerá okamžitá trasa IP mezi oddíly, včetně těch, které přenáší HTTPS přes protokol TCP/IP, přináší riziko zneužití chyb zabezpečení z síťové vrstvy. Služby zasílání zpráv poskytují izolované komunikační cesty, ve kterých se při přechodu mezi stranami na disk zapisují i zprávy. Úlohy ve dvou různých virtuálních sítích, které jsou vázané na stejnou instanci Event Hubs, mohou komunikovat efektivně a spolehlivě prostřednictvím zpráv, zatímco příslušná integrita hranice izolace sítě zůstane zachovaná.
 
To znamená, že vaše cloudová řešení citlivá na zabezpečení nezískají přístup k špičkovým spolehlivým a škálovatelným funkcím asynchronního zasílání zpráv, ale teď můžou pomocí zasílání zpráv vytvářet cesty pro komunikaci mezi oddíly zabezpečeného řešení, které jsou z vlastního hlediska bezpečnější než ty, které jsou dosažitelné pro libovolný režim komunikace peer-to-peer, včetně protokolu HTTPS a dalších protokolů soketu zabezpečen

### <a name="bind-event-hubs-to-virtual-networks"></a>Navázání centra událostí k virtuálním sítím

**Pravidla virtuální sítě** jsou funkcí zabezpečení brány firewall, která určuje, jestli váš obor názvů Azure Event Hubs akceptuje připojení z konkrétní podsítě virtuální sítě.

Vytvoření vazby oboru názvů Event Hubs k virtuální síti je proces se dvěma kroky. Nejdřív je potřeba vytvořit **koncový bod služby virtuální sítě** v podsíti virtuální sítě a povolit ho pro **Microsoft. EventHub** , jak je vysvětleno v článku [Přehled koncového bodu služby](../virtual-network/virtual-network-service-endpoints-overview.md) . Po přidání koncového bodu služby navážete obor názvů Event Hubs s **pravidlem virtuální sítě**.

Pravidlo virtuální sítě je přidružení oboru názvů Event Hubs k podsíti virtuální sítě. I když toto pravidlo existuje, budou mít všechny úlohy vázané na podsíť přístup k oboru názvů Event Hubs. Event Hubs sám o sobě nenavazuje odchozí připojení, nemusí získat přístup, a proto nikdy neudělí přístup k podsíti tím, že toto pravidlo povolí.

Další informace najdete v tématu [Konfigurace koncových bodů služby virtuální sítě pro centrum událostí](event-hubs-service-endpoints.md) .

## <a name="private-endpoints"></a>Soukromé koncové body

[Služba privátního propojení Azure](../private-link/private-link-overview.md) umožňuje přístup ke službám Azure (například k Azure Event Hubs, Azure Storage a Azure Cosmos DB) a hostovaným zákaznickým a partnerským službám Azure prostřednictvím **privátního koncového bodu** ve vaší virtuální síti.

Privátní koncový bod je síťové rozhraní, které se připojuje soukromě a bezpečně ke službě využívající privátní propojení Azure. Privátní koncový bod používá privátní IP adresu z vaší virtuální sítě a efektivně ho přinášejí do vaší virtuální sítě. Veškerý provoz do služby se dá směrovat prostřednictvím privátního koncového bodu, takže se nevyžadují žádné brány, zařízení NAT, ExpressRoute, připojení VPN ani veřejné IP adresy. Provoz mezi vaší virtuální sítí a službou prochází přes páteřní síť Microsoftu a eliminuje rizika vystavení na veřejném internetu. Můžete se připojit k instanci prostředku Azure, která poskytuje nejvyšší úroveň členitosti v řízení přístupu.

> [!NOTE]
> Tato funkce je podporována pouze u **vyhrazené** úrovně. Další informace o vyhrazené úrovni najdete v tématu [přehled Event Hubs úrovně Dedicated](event-hubs-dedicated-overview.md). 

Další informace najdete v tématu [Konfigurace privátních koncových bodů pro centrum událostí](private-link-service.md) .


## <a name="next-steps"></a>Další kroky
Viz následující články:

- [Postup konfigurace brány firewall protokolu IP pro centrum událostí](event-hubs-ip-filtering.md)
- [Postup konfigurace koncových bodů služby virtuální sítě pro centrum událostí](event-hubs-service-endpoints.md)
- [Postup konfigurace privátních koncových bodů pro centrum událostí](private-link-service.md)
