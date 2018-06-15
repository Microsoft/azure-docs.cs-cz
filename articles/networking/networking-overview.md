---
title: Síť Azure | Microsoft Docs
description: Další informace o Azure síťové služby a možnosti.
services: networking
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/19/2017
ms.author: jdial
ms.openlocfilehash: 2e5862e99b3e883554b42341d2c1dbe9d8b8ec72
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34366976"
---
# <a name="azure-networking"></a>Síť Azure

Azure poskytuje celou řadu možností sítě, které lze použít společně nebo samostatně. Získáte po kliknutí na následující klíčové funkce, které další informace o nich:
- [Připojení mezi prostředky Azure](#connectivity): prostředky Azure připojit společně v zabezpečené, privátní virtuální sítě v cloudu.
- [Připojení k Internetu](#internet-connectivity): komunikují přes Internet do a z prostředků Azure.
- [Místní připojení](#on-premises-connectivity): připojení k místní síti prostředky Azure prostřednictvím virtuální privátní sítě (VPN) přes Internet nebo prostřednictvím vyhrazené připojení k Azure.
- [Načíst vyrovnávání a provoz směrem](#load-balancing): Vyrovnávání zatížení přenosů do serverů ve stejném umístění a přímé provoz na servery v různých umístěních.
- [Zabezpečení](#security): filtrování síťového provozu mezi podsítě v síti nebo jednotlivé virtuální počítače (VM).
- [Směrování](#routing): použijte výchozí směrování nebo plnou kontrolu nad směrování mezi vaší Azure a místní prostředky.
- [Možnosti správy](#manageability): monitorování a správě síťových prostředků Azure.
- [Nástroje pro nasazení a konfigurace](#tools): použijte webový portál nebo nástroje příkazového řádku a platformy k nasazení a konfiguraci síťových prostředků.

## <a name="Connectivity"></a>Připojení mezi prostředky Azure

Prostředky Azure jako virtuální počítače, cloudové služby, sady škálování virtuálních počítačů a prostředí Azure App Service můžete vzájemně komunikovat soukromě přes virtuální síť Azure (VNet). Virtuální síť je to logická izolace cloudu Azure vyhrazeného pro vaše [předplatné](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fnetworking%2ftoc.json). Můžete implementovat více virtuálních sítí v rámci každé předplatné Azure a Azure [oblast](https://azure.microsoft.com/regions). Každý virtuální sítě je izolovaná od jiných virtuálních sítí. Pro každý virtuální síť můžete:

- Zadání vlastního adresního prostoru privátních IP adres pomocí veřejných a privátních (RFC 1918) adres. Prostředky Azure přiřadí připojen k virtuální síti privátní IP adresy z adresního prostoru, který přiřadíte.
- Segment sítě VNet do jedné nebo více podsítí a přidělovat část adresní prostor virtuální sítě pro každou podsíť.
- Můžete použít Azure překlad nebo zadejte vlastní server DNS pro použití prostředky, které jsou připojené k virtuální síti.

Další informace o službě Azure Virtual Network, najdete [Přehled virtuálních sítí](../virtual-network/virtual-networks-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) článku. Virtuální sítě můžete připojit k sobě navzájem, povolení prostředky připojenými k buď virtuální sítě spolu vzájemně komunikovat virtuální sítě. Propojení virtuálních sítí k sobě navzájem, můžete použít jednu nebo obě z následujících možností:

- **Partnerský vztah:** umožňuje prostředky připojenými k jiné sítě Azure Vnet v rámci stejné oblasti Azure pro komunikaci mezi sebou. Šířka pásma a čekací doba mezi virtuální sítě je stejný, jako kdyby prostředky byly připojené ke stejné síti VNet. Další informace o partnerském vztahu, najdete [partnerského vztahu Přehled virtuálních sítí](../virtual-network/virtual-network-peering-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) článku.
- **Brána sítě VPN:** umožňuje prostředky připojenými k jiné sítě Azure Vnet v rámci různých oblastech Azure pro komunikaci mezi sebou. Provoz mezi virtuálními sítěmi toky prostřednictvím služby Azure VPN Gateway. Šířka pásma mezi virtuálními sítěmi je omezená na šířku pásma brány. Další informace týkající se propojení virtuálních sítí s brána sítě VPN, najdete [konfigurace připojení typu VNet-to-VNet v oblastech](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) článku.

## <a name="internet-connectivity"></a>Připojení k Internetu

Všechny prostředky Azure, které jsou připojené k virtuální síti mají ve výchozím nastavení odchozí připojení k Internetu. Privátní IP adresu prostředku je zdrojová síťová adresa přeložit (překládat pomocí SNAT) na veřejnou IP adresu pomocí infrastruktury Azure. Další informace o odchozí připojení k Internetu, přečtěte si [pochopení odchozí připojení v Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fnetworking%2ftoc.json) článku.

Pro komunikaci příchozí prostředky Azure z Internetu, nebo k Internetu bez překládat pomocí SNAT odchozí komunikaci, musí prostředek přiřazenou veřejnou adresu IP. Další informace o veřejné IP adresy, přečtěte si [veřejné IP adresy](../virtual-network/virtual-network-public-ip-address.md?toc=%2fazure%2fnetworking%2ftoc.json) článku.

## <a name="on-premises-connectivity"></a>Místní připojení

Prostředky ve vaší virtuální síti získat přístup bezpečně prostřednictvím připojení k síti VPN nebo přímé privátní připojení. K posílání síťového provozu mezi virtuální sítě Azure a v místní síti, musíte vytvořit bránu virtuální sítě. Můžete nakonfigurovat nastavení pro bránu vytvořit typ připojení, které chcete, VPN nebo ExpressRoute.

Do místní sítě lze připojit k virtuální síti pomocí libovolnou kombinaci těchto možností:

**Point-to-site (VPN prostřednictvím protokolu SSTP)**

Následující obrázek znázorňuje samostatný bod připojení site mezi více počítačů a virtuální sítě:

![Point-to-Site](./media/networking-overview/point-to-site.png)

Toto připojení mezi jednoho počítače a virtuální sítě. Tento typ připojení je skvělý, pokud teprve začínáte s Azure, nebo pro vývojáře, protože nevyžaduje téměř nebo vůbec žádné změny vaší stávající sítě. Je také vhodné, pokud se připojujete ze vzdáleného umístění, jako je například z konference nebo domovský. Připojení point-to-site jsou často vázány pomocí připojení site-to-site pomocí stejné brány virtuální sítě. Připojení k poskytování šifrovanou komunikaci prostřednictvím Internetu mezi počítačem a virtuální sítě používá protokol SSTP. Latence pro síť VPN point-to-site nepředvídatelným, protože provoz prochází přes Internet.

**Site-to-site (protokolu IPsec/IKE tunelového připojení sítě VPN)**

![Site-to-Site](./media/networking-overview/site-to-site.png)

Toto připojení mezi místní zařízení VPN a služby Azure VPN Gateway. Tento typ připojení umožňuje místnímu prostředku, který autorizaci pro přístup k virtuální síti. Připojení je VPN pomocí protokolu IPSec/IKE, která poskytuje šifrovanou komunikaci prostřednictvím Internetu mezi místní zařízení a brána Azure VPN. Více místními servery můžete připojit ke stejné bráně VPN. Místní zařízení VPN v každé lokalitě musí mít externě směřujících veřejnou IP adresu, která není za adres (NAT) Latence pro připojení site-to-site nepředvídatelným, protože provoz prochází přes Internet.

**ExpressRoute (vyhrazené soukromé připojení)**

![ExpressRoute](./media/networking-overview/expressroute.png)

Tento typ připojení je navázat mezi vaší sítí a Azure prostřednictvím partnerem ExpressRoute. Toto připojení je soukromé. Provoz neprochází Internetu. Latence pro připojení typu ExpressRoute je předvídatelný, vzhledem k tomu, že provoz není procházení Internetu. ExpressRoute je možné kombinovat s připojení site-to-site.

Další informace o všech předchozích možností připojení, přečtěte si [diagramy topologie připojení](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fnetworking%2ftoc.json) článku.

## <a name="load-balancing"></a>Načíst vyrovnávání a provoz směrem

Microsoft Azure poskytuje několik služeb pro správu, jak je distribuován síťový provoz a skupinu s vyrovnáváním zatížení. Samostatně nebo společně můžete následující možnosti:

**Vyrovnávání zatížení DNS**

Služba Azure Traffic Manager poskytuje globální DNS Vyrovnávání zatížení. Správce provozu odpoví na klienty s IP adresou v pořádku koncového bodu, na základě jedné z následujících metod směrování:
- **Zeměpisná:** klienti jsou směrovaní ke konkrétním koncovým bodům (Azure, externí nebo vnořená) na základě, na které zeměpisné umístění svého dotazu DNS pochází z. Tato metoda umožňuje scénáře, kde je důležité zároveň budete vědět, geografické oblasti klienta a směrování je podle něj. Mezi příklady patří soulad s pověřeními suverenity dat, lokalizace obsah & uživatelské prostředí a měření provoz z různých oblastech.
- **Výkon:** adresu IP, který je vrácen do klienta je "nejblíže" klienta. 'Nejbližší' koncový bod není nutně nejbližší měřený podle zeměpisného vzdálenost. Místo toho tato metoda určuje nejbližší koncový bod měřením latence sítě. Správce provozu udržuje tabulky latence Internetu ke sledování doby odezvy mezi rozsahů IP adres a každé datové centrum Azure.
- **Priorita:** přenášená na primární koncový bod (nejvyšší priorita). Pokud primární koncový bod není k dispozici, Traffic Manager směrovat provoz na druhý. Pokud obě primární a sekundární koncových bodů nejsou k dispozici, provoz přejde na třetí a tak dále. Dostupnost koncového bodu je založena na nakonfigurované stav (povolit nebo zakázat) a monitorování probíhající koncového bodu.
- **Vážené kruhové dotazování:** pro každý požadavek Traffic Manager náhodně vybere koncový bod k dispozici. Pravděpodobnost výběr koncový bod podle váhy přiřazené všechny koncové body k dispozici. Používá stejnou váhou mezi všechny výsledky koncové body rozdělení i provoz. Pomocí vyšší nebo nižší váhu na konkrétním koncovým bodům způsobí, že těchto koncových bodů, který se má vrátit více nebo méně často v odpovědi DNS.

Následující obrázek znázorňuje žádost pro webovou aplikaci přesměrován na koncový bod webové aplikace. Koncové body může být také jinými službami Azure, jako je například virtuální počítače a cloudové služby.

![Traffic Manager](./media/networking-overview/traffic-manager.png)

Připojení klienta přímo do tohoto koncového bodu. Azure Traffic Manager zjistí, že koncový bod není v pořádku a pak přesměruje klienty do různých koncového bodu v pořádku. Další informace o Traffic Manager, najdete [přehled Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) článku.

**Vyrovnávání zatížení aplikace**

Služba Azure Application Gateway poskytuje aplikace doručení řadiče (ADC) jako služba. Aplikační brána nabízí různé vrstvy 7 (HTTP či HTTPS) služby Vyrovnávání zatížení pro vaše aplikace, včetně brány firewall webových aplikací k ochraně vašich webových aplikací z ohrožení zabezpečení a zneužití. Aplikační brána taky umožňuje optimalizovat webové farmy produktivitu přesměrováním ukončení protokolu SSL náročná na prostředky procesoru aplikační brány. 

Další možnosti směrování vrstvy 7 zahrnují kruhového dotazování distribuce příchozí provoz, spřažení na základě souboru cookie relace, na základě cestu směrování adres URL a umožňuje hostování více webů za bránu jednu aplikaci. Application Gateway můžete nakonfigurovat jako bránu internetové brány, bránu pouze interní nebo obojí. Application Gateway je plně Azure spravované, škálovatelné a vysoce dostupné. Nabízí celou řadu možností diagnostiky a protokolování, které zlepšují správu. Další informace o Application Gateway, přečtěte si [Application Gateway přehled](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fnetworking%2ftoc.json) článku.

Následující obrázek znázorňuje adresu URL, cesta založené na směrování s aplikační brány:

![Application Gateway](./media/networking-overview/application-gateway.png)

**Vyrovnávání zatížení sítě**

Vyrovnávání zatížení Azure poskytuje vysoce výkonné, nízkou latencí 4 vrstvy Vyrovnávání zatížení pro všechny protokoly UDP a TCP. Spravuje příchozí a odchozí připojení. Můžete nakonfigurovat veřejný a interní Vyrovnávání zatížení sítě koncové body. Můžete definovat pravidla pro mapování příchozí připojení na fond back-end cíle pomocí TCP a HTTP zjišťování stavu možností pro správu dostupnost služeb. Další informace o vyrovnávání zatížení, přečtěte si [přehled nástroje pro vyrovnávání zatížení](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) článku.

Následující obrázek znázorňuje internetového vícevrstvé aplikace, která využívá i nástroje pro vyrovnávání zatížení externí i interní:

![Nástroj pro vyrovnávání zatížení](./media/networking-overview/load-balancer.png)

## <a name="security"></a>Zabezpečení

Můžete filtrovat provoz do a z prostředků Azure pomocí následujících možností:

- **Síť:** můžete implementovat skupiny zabezpečení Azure sítě (Nsg) pro filtrování příchozích a odchozích přenosů prostředky Azure. Každá skupina NSG obsahuje jedno nebo více pravidel příchozí a odchozí. Každé pravidlo určuje zdrojové IP adresy, cílové IP adresy, portu a protokolu, který je provoz filtrovaný pomocí. Skupiny Nsg můžete použít na jednotlivé podsítě a jednotlivé virtuální počítače. Další informace o skupinách Nsg, najdete [přehled skupin zabezpečení sítě](../virtual-network/security-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) článku.
- **Aplikace:** pomocí služby Application Gateway s brány firewall webových aplikací můžete chránit vaše webové aplikace z ohrožení zabezpečení a zneužití. Běžných příkladů jsou SQL prostřednictvím injektáže skriptování mezi weby a poškozené záhlaví. Aplikační brána filtruje tento provoz a zastaví dosáhly webových serverů. Budete moci konfigurovat pravidla, jaké má povoleno. Umožňuje konfigurovat zásady vyjednávání SSL zajišťuje určitých zásad se zakáže. Další informace o brány firewall webových aplikací, přečtěte si [brány firewall webových aplikací](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) článku.

Pokud potřebujete možnost sítě Azure není zadejte, nebo chcete použít síťových aplikací, použijte místní, můžete implementovat produkty ve virtuálních počítačích a jejich připojení k virtuální síti. [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances) obsahuje několik různých virtuálních počítačů, které jsou předem nakonfigurované s síťových aplikací může aktuálně používáte. Tyto předem nakonfigurované virtuální počítače se obvykle označují jako virtuální zařízení sítě (hodnocení chyb zabezpečení). NVAs jsou k dispozici s aplikací, jako jsou brány firewall a optimalizace sítě WAN.

## <a name="routing"></a>Směrování

Azure vytvoří výchozí směrovací tabulky, které umožňují prostředky připojenými k žádné podsíti v žádné virtuální sítě pro komunikaci mezi sebou. Můžete implementovat jednu nebo obě z následujících typů tras přepsat výchozí trasy, které vytvoří Azure:
- **Uživatelem definované:** můžete vytvořit vlastní směrovací tabulky s trasami, které tuto kontrolu, kde provoz se směruje na pro každou podsíť. Další informace o trasách definovaných uživatelem najdete v článku [Trasy definované uživatelem](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **Border gateway protocol (BGP):** Pokud virtuální sítě se připojit k místní síti pomocí připojení k Azure VPN Gateway nebo ExpressRoute, můžete rozšířit směrování BGP do vaší virtuální sítě. BGP je standardní směrovací protokol, na internetu běžně používaný k výměně informací o směrování a dostupnosti mezi dvěma nebo více sítěmi. Při použití v rámci virtuálních sítí Azure, protokol BGP umožňuje Azure VPN Gateway a vaše místní zařízení VPN, nazývají partnerské uzly protokolu BGP nebo Sousedé BGP, výměnu "tras" informujících obě brány o dostupnosti a dosažitelnosti předpon, které přejděte prostřednictvím těmito bránami nebo trasami zahrnuta. Protokol BGP můžete také povolit směrování přenosu mezi více sítěmi pomocí šíření tras, které brána s protokolem BGP zjistí od jednoho partnerského uzlu protokolu BGP ostatním partnerům BGP. Další informace o protokolu BGP najdete v tématu [protokolu BGP se službou Azure VPN Gateway přehled](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) článku.

## <a name="manageability"></a>Možnosti správy

Azure poskytuje následující nástroje pro sledování a správě sítí:
- **Protokoly aktivity:** Azure všechny prostředky mít protokoly aktivity, které poskytují informace o operacích neproběhly, stav operací a kdo inicioval operaci. Další informace o protokoly aktivity, najdete [aktivity protokoly přehled](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md?toc=%2fazure%2fnetworking%2ftoc.json) článku.
- **Diagnostické protokoly:** periodický a spontánních události jsou vytvořené síťové prostředky a protokolovány v účtech úložiště Azure, odeslané do centra událostí Azure nebo odesílání k analýze protokolů Azure. Diagnostické protokoly nabízí náhled na stav prostředku. Diagnostické protokoly jsou uvedené pro nástroj pro vyrovnávání zatížení (internetový), skupiny zabezpečení sítě, trasy a aplikační brány. Další informace o diagnostických protokolů, najdete [diagnostické protokoly přehled](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md?toc=%2fazure%2fnetworking%2ftoc.json) článku.
- **Metrika:** jsou metriky měření výkonu a čítače, které jsou shromažďovány prostřednictvím v časovém intervalu na prostředky. Metriky lze použít k aktivaci výstrahy na základě prahových hodnot. Aktuálně jsou k dispozici na aplikační brána metriky. Další informace o metriky, přečtěte si [metriky přehled](../monitoring-and-diagnostics/monitoring-overview-metrics.md?toc=%2fazure%2fnetworking%2ftoc.json) článku.
- **Řešení potíží:** informace o odstraňování potíží je přístupný přímo na portálu Azure. Informace pomohou diagnostikovat běžné problémy s ExpressRoute, brána sítě VPN, aplikační bránu, protokoly zabezpečení sítě, trasy, DNS, nástroj pro vyrovnávání zatížení a Traffic Manager.
- **Řízení přístupu na základě role (RBAC):** určovat, kdo může vytvářet a spravovat síťových prostředků pomocí řízení přístupu na základě rolí (RBAC). Další informace o RBAC načtením [začít pracovat s RBAC](../role-based-access-control/overview.md?toc=%2fazure%2fnetworking%2ftoc.json) článku. 
- **Zachytáváním paketů:** služby sledovací proces sítě Azure poskytuje možnost spustit zachytávání paketů na virtuálním počítači prostřednictvím rozšíření virtuálního počítače. Tato možnost je k dispozici pro systémy Linux a virtuální počítače Windows. Další informace o zachytáváním paketů, přečtěte si [přehled zachytávání paketů](../network-watcher/network-watcher-packet-capture-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) článku.
- **Ověřte IP toky:** sledovací proces sítě vám umožní ověřit IP toků mezi virtuální počítač Azure a vzdálený prostředek, k určení, jestli jsou pakety povolený nebo zakázaný. Tato funkce poskytuje správcům možnost rychle diagnostikovat problémy s připojením. Další informace o tom, jak ověřit IP toky, najdete [IP tok ověření přehled](../network-watcher/network-watcher-ip-flow-verify-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) článku.
- **Řešení potíží s připojení VPN:** funkce Poradce při potížích s VPN sledovací proces sítě poskytuje možnost pro dotazování připojení nebo brány a ověřit stav prostředků. Další informace o odstraňování potíží s připojení VPN, najdete [připojení VPN k řešení potíží s přehled](../network-watcher/network-watcher-troubleshoot-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) článku.
- **Zobrazení topologie sítě:** zobrazení grafické reprezentace síťové prostředky ve virtuální síti s sledovací proces sítě. Další informace o zobrazení topologie sítě, najdete [přehled topologie](../network-watcher/network-watcher-topology-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) článku.

## <a name="tools"></a>Nástroje pro nasazení a konfigurace

Můžete nasadit a nakonfigurovat Azure síťových prostředků s žádným z následujících nástrojů:

- **Portál Azure:** grafické uživatelské rozhraní, které běží v prohlížeči. Otevřete web [Azure Portal](http://portal.azure.com).
- **Azure PowerShell:** příkazového řádku nástroje pro správu Azure z počítače se systémem Windows. Další informace o prostředí Azure PowerShell načtením [Přehled prostředí Azure PowerShell](/powershell/azure/overview?view=azurermps-3.8.0?toc=%2fazure%2fnetworking%2ftoc.json) článku.
- **Rozhraní příkazového řádku Azure (CLI):** příkazového řádku nástroje pro správu Azure z počítačů systému Linux, systému macOS nebo systému Windows. Další informace o rozhraní příkazového řádku Azure načtením [přehled Azure CLI](/cli/azure/get-started-with-azure-cli?toc=%2fazure%2fnetworking%2ftoc.json) článku.
- **Šablony Azure Resource Manageru:** soubor (ve formátu JSON), která definuje infrastrukturu a konfiguraci řešení s Azure. Pomocí šablony můžete řešení opakovaně nasadit v průběhu životního cyklu a mít přitom jistotu, že se prostředky nasadí konzistentně. Další informace o vytváření šablon, přečtěte si [osvědčené postupy pro vytváření šablon](../azure-resource-manager/resource-manager-template-best-practices.md?toc=%2fazure%2fnetworking%2ftoc.json) článku. Šablony lze nasadit pomocí portálu Azure, rozhraní příkazového řádku nebo prostředí PowerShell. Pokud chcete začít s šablonami hned, nasaďte jednu z mnoha předem nakonfigurovaná šablon v [šablon Azure rychlý Start](https://azure.microsoft.com/resources/templates/?term=network) knihovny. 

## <a name="pricing"></a>Ceny

Některé síťové služby Azure mají poplatků, zatímco jiné jsou volné. Zobrazení [virtuální síť](https://azure.microsoft.com/pricing/details/virtual-network), [brány VPN](https://azure.microsoft.com/pricing/details/vpn-gateway), [Application Gateway](https://azure.microsoft.com/pricing/details/application-gateway/), [nástroj pro vyrovnávání zatížení](https://azure.microsoft.com/pricing/details/load-balancer), [sledovací proces sítě](https://azure.microsoft.com/pricing/details/network-watcher), [DNS](https://azure.microsoft.com/pricing/details/dns), [Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager) a [ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute) ceny stránky pro další informace.

## <a name="next-steps"></a>Další postup

- Vytvoření vaší první virtuální sítě a připojení několika virtuálními počítači, pomocí kroků v [vytvoření vaší první virtuální síť](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) článku.
- Připojení počítače k virtuální síti pomocí kroků v [konfigurace připojení typu point-to-site](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) článku.
- Internetové přenosy na veřejných serverech vyrovnávat zatížení pomocí kroků v [vytvořit nástroj pro vyrovnávání zatížení internetového](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) článku.
