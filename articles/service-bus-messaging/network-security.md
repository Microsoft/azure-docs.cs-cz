---
title: Zabezpečení sítě pro Azure Service Bus
description: Tento článek popisuje funkce zabezpečení sítě, jako jsou značky služby, pravidla brány firewall protokolu IP, koncové body služby a soukromé koncové body.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: fb21c8beb6d48ecab04917525011cc4762c46ff3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91766390"
---
# <a name="network-security-for-azure-service-bus"></a>Zabezpečení sítě pro Azure Service Bus 
Tento článek popisuje, jak používat následující funkce zabezpečení pro Azure Service Bus: 

- Značky služeb
- Pravidla brány firewall protokolu IP
- Koncové body síťové služby
- Soukromé koncové body


## <a name="service-tags"></a>Značky služeb
Značka služby představuje skupinu předpon IP adres z dané služby Azure. Společnost Microsoft spravuje předpony adres, které jsou zahrnuté ve značce služby, a automaticky aktualizuje značku služby, protože se mění adresy. tím se minimalizuje složitost častých aktualizací pravidel zabezpečení sítě. Další informace o značkách služby najdete v tématu [Přehled značek služeb](../virtual-network/service-tags-overview.md).

Pomocí značek služeb můžete definovat řízení přístupu k síti pro [skupiny zabezpečení sítě](../virtual-network/security-overview.md#security-rules) nebo [Azure firewall](../firewall/service-tags.md). Při vytváření pravidel zabezpečení používejte značky služby místo konkrétních IP adres. Zadáním názvu značky služby (například **ServiceBus**) v příslušném *zdrojovém* nebo *cílovém* poli pravidla můžete povolit nebo odepřít provoz pro příslušnou službu.

| Značka služby | Účel | Dá se použít příchozí nebo odchozí? | Je možné je rozregionovat? | Lze použít s Azure Firewall? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **ServiceBus** | Azure Service Bus provoz, který využívá úroveň služby Premium. | Odchozí | Yes | Yes |


> [!NOTE]
> Značky služeb můžete použít jenom pro obory názvů úrovně **Premium** . Pokud používáte **standardní** obor názvů, použijte IP adresu, kterou vidíte při spuštění následujícího příkazu: `nslookup <host name for the namespace>` . Například: `nslookup contosons.servicebus.windows.net`. 

## <a name="ip-firewall"></a>Brána firewall protokolu IP 
Ve výchozím nastavení jsou Service Bus obory názvů přístupné z Internetu, pokud požadavek přichází s platným ověřováním a autorizací. Pomocí brány firewall protokolu IP je můžete omezit na další jenom na sadu IPv4 adres nebo rozsahů IPv4 adres v zápisu [CIDR (bez třídy) (směrování Inter-Domain)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

Tato funkce je užitečná ve scénářích, ve kterých Azure Service Bus by měly být dostupné jenom z určitých dobře známých lokalit. Pravidla brány firewall umožňují konfigurovat pravidla pro příjem provozu pocházejících z konkrétních IPv4 adres. Pokud například použijete Service Bus s [Azure Express Route] [Express-Route], můžete vytvořit **pravidlo brány firewall** , které umožní provoz jenom z místních IP adres infrastruktury nebo adres podnikové brány NAT. 

Pravidla brány firewall protokolu IP se používají na úrovni oboru názvů Service Bus. Proto se pravidla vztahují na všechna připojení z klientů pomocí libovolného podporovaného protokolu. Všechny pokusy o připojení z IP adresy, které neodpovídají povolenému pravidlu IP v oboru názvů Service Bus, se odmítnou jako neoprávněné. Odpověď nezmiňuje pravidlo protokolu IP. Pravidla filtru IP se aplikují v pořadí a první pravidlo, které odpovídá IP adrese, určuje akci přijmout nebo odmítnout.

Další informace najdete v tématu [Postup konfigurace brány firewall protokolu IP pro Service Bus obor názvů](service-bus-ip-filtering.md) .

## <a name="network-service-endpoints"></a>Koncové body síťové služby
Integrace Service Bus s [koncovými body služby Virtual Network (VNET)](service-bus-service-endpoints.md) umožňuje zabezpečenému přístupu k funkcím zasílání zpráv z úloh, jako jsou virtuální počítače, které jsou svázané s virtuálními sítěmi, a cestu síťového provozu, která je zabezpečená na obou koncích.

Po navázání vazby na alespoň jeden koncový bod služby virtuální sítě nebude příslušný obor názvů Service Bus nadále přijímat přenosy z odkudkoli, ale z autorizovaných virtuálních sítí. Z perspektivy virtuální sítě naváže obor názvů Service Bus na koncový bod služby a nakonfiguruje izolovaný síťový tunel z podsítě virtuální sítě do služby zasílání zpráv.

Výsledkem je privátní a izolovaný vztah mezi úlohami vázanými na podsíť a odpovídajícím oborem názvů Service Bus, a to i přes pozorovatelnou síťovou adresu koncového bodu služby zasílání zpráv ve veřejném rozsahu IP adres.

> [!IMPORTANT]
> Virtuální sítě se podporují jenom v oborech názvů Service Bus [úrovně Premium](service-bus-premium-messaging.md) .
> 
> Při použití koncových bodů služby virtuální sítě s Service Bus byste neměli povolit tyto koncové body v aplikacích, které používají obory názvů Service Bus úrovně Standard a Premium. Protože úroveň Standard nepodporuje virtuální sítě. Koncový bod je omezený jenom na obory názvů úrovně Premium.

### <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Pokročilé scénáře zabezpečení povolené integrací virtuální sítě 

Řešení, která vyžadují těsné a compartmentalized zabezpečení a kde podsítě virtuální sítě poskytují segmentaci mezi službami compartmentalized, obecně potřebují cesty komunikace mezi službami, které se nacházejí v těchto oddílech.

Veškerá okamžitá trasa IP mezi oddíly, včetně těch, které přenáší HTTPS přes protokol TCP/IP, přináší riziko zneužití chyb zabezpečení z síťové vrstvy. Služby zasílání zpráv poskytují zcela izolované komunikační cesty, ve kterých se při přechodu mezi stranami na disk zapisují i zprávy. Úlohy ve dvou různých virtuálních sítích, které jsou vázané na stejnou instanci Service Bus, mohou komunikovat efektivně a spolehlivě prostřednictvím zpráv, zatímco příslušná integrita hranice izolace sítě zůstane zachovaná.
 
To znamená, že vaše cloudová řešení citlivá na zabezpečení nezískají přístup k špičkovým spolehlivým a škálovatelným funkcím asynchronního zasílání zpráv, ale teď můžou pomocí zasílání zpráv vytvářet cesty pro komunikaci mezi oddíly zabezpečeného řešení, které jsou z vlastního hlediska bezpečnější než ty, které jsou dosažitelné pro libovolný režim komunikace peer-to-peer, včetně protokolu HTTPS a dalších protokolů soketu zabezpečen

### <a name="bind-service-bus-to-virtual-networks"></a>Vytvoření vazby Service Bus k virtuálním sítím

*Pravidla virtuální sítě* jsou funkcí zabezpečení brány firewall, která určuje, jestli Server Azure Service Bus akceptuje připojení z konkrétní podsítě virtuální sítě.

Vytvoření vazby oboru názvů Service Bus k virtuální síti je proces se dvěma kroky. Nejdřív je potřeba vytvořit **koncový bod služby Virtual Network** v podsíti Virtual Network a povolit ho pro **Microsoft. ServiceBus** , jak je vysvětleno v tématu [Přehled koncového bodu služby](service-bus-service-endpoints.md). Po přidání koncového bodu služby navážete obor názvů Service Bus s **pravidlem virtuální sítě**.

Pravidlo virtuální sítě je přidružení oboru názvů Service Bus k podsíti virtuální sítě. I když toto pravidlo existuje, budou mít všechny úlohy vázané na podsíť přístup k oboru názvů Service Bus. Service Bus sám o sobě nenavazuje odchozí připojení, nemusí získat přístup, a proto nikdy neudělí přístup k podsíti tím, že toto pravidlo povolí.

Další informace najdete v tématu [Konfigurace koncových bodů služby virtuální sítě pro obor názvů Service Bus](service-bus-service-endpoints.md) .

## <a name="private-endpoints"></a>Soukromé koncové body

Služba privátního propojení Azure vám umožňuje přístup ke službám Azure (například Azure Service Bus, Azure Storage a Azure Cosmos DB) a hostovaným zákaznickým a partnerským službám Azure prostřednictvím **privátního koncového bodu** ve vaší virtuální síti.

Privátní koncový bod je síťové rozhraní, které se připojuje soukromě a bezpečně ke službě využívající privátní propojení Azure. Privátní koncový bod používá privátní IP adresu z vaší virtuální sítě a efektivně ho přinášejí do vaší virtuální sítě. Veškerý provoz do služby se dá směrovat prostřednictvím privátního koncového bodu, takže se nevyžadují žádné brány, zařízení NAT, ExpressRoute, připojení VPN ani veřejné IP adresy. Provoz mezi vaší virtuální sítí a službou prochází přes páteřní síť Microsoftu a eliminuje rizika vystavení na veřejném internetu. Můžete se připojit k instanci prostředku Azure, která poskytuje nejvyšší úroveň členitosti v řízení přístupu.

Další informace najdete v tématu [co je privátní propojení Azure?](../private-link/private-link-overview.md)

> [!NOTE]
> Tato funkce je podporovaná s úrovní **premium** Azure Service Bus. Další informace o úrovni Premium najdete v článku [Service Bus úrovně pro zasílání zpráv na úrovni Premium a Standard](service-bus-premium-messaging.md) .


Další informace najdete v tématu [Postup konfigurace privátních koncových bodů pro obor názvů Service Bus](private-link-service.md) .


## <a name="next-steps"></a>Další kroky
Viz následující články:

- [Postup konfigurace brány firewall protokolu IP pro Service Bus obor názvů](service-bus-ip-filtering.md)
- [Postup konfigurace koncových bodů služby virtuální sítě pro obor názvů Service Bus](service-bus-service-endpoints.md)
- [Postup konfigurace privátních koncových bodů pro obor názvů Service Bus](private-link-service.md)
