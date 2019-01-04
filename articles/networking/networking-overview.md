---
title: Sítě Azure | Dokumentace Microsoftu
description: Přečtěte si o Azure síťové služby a funkce.
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
ms.openlocfilehash: f21d92dabfcfbe51cf8135388a1ab489c20593a4
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53537541"
---
# <a name="azure-networking"></a>Sítě Azure

Azure nabízí širokou škálu možností sítě, které lze použít společně nebo samostatně. Získáte po kliknutí na následující klíčové funkce pro další informace o nich:
- [Připojení mezi prostředky Azure](#connectivity): Připojení prostředků Azure společně v zabezpečené a privátní virtuální sítě v cloudu.
- [Připojení k Internetu](#internet-connectivity): Z prostředků Azure a komunikují přes Internet.
- [Místní připojení](#on-premises-connectivity): Připojení místní sítě k prostředkům Azure pomocí virtuální privátní sítě (VPN) přes Internet nebo prostřednictvím vyhrazené připojení k Azure.
- [Směr provozu a vyrovnávání zatížení](#load-balancing): Vyrovnávání zatížení provozu na servery ve stejném umístění a směrovat přenos dat na servery v různých umístěních.
- [Zabezpečení](#security): Filtrování síťového provozu mezi podsítěmi síť nebo jednotlivé virtuální počítače (VM).
- [Směrování](#routing): Použít výchozí směrování nebo plně řídit směrování mezi Azure a místní prostředky.
- [Možnosti správy](#manageability): Monitorování a Správa síťových prostředků Azure.
- [Nasazení a konfigurace nástroje](#tools): Webový portál nebo nástrojů příkazového řádku pro různé platformy použijte k nasazení a konfiguraci síťových prostředků.

## <a name="Connectivity"></a>Připojení mezi prostředky Azure

Prostředky Azure, jako jsou Virtual Machines, Cloud Services, Škálovací sady virtuálních počítačů a Azure App Service Environment může komunikovat soukromě mezi sebou prostřednictvím služby Azure Virtual Network (VNet). Virtuální síť je logickou izolaci cloudu Azure, které jsou vyhrazené pro váš [předplatné](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fnetworking%2ftoc.json). Můžete implementovat několik virtuálních sítí v každém předplatném Azure a Azure [oblasti](https://azure.microsoft.com/regions). Každá virtuální síť je izolovaná od jiných virtuálních sítí. Pro každou virtuální síť můžete:

- Zadání vlastního adresního prostoru privátních IP adres pomocí veřejných a privátních (RFC 1918) adres. Azure přiřazuje prostředkům připojené k virtuální síti privátní IP adresu z adresního prostoru, který přiřadíte.
- Virtuální síť segmentovat do jedné nebo několika podsítí a přidělení části adresního prostoru virtuální sítě ke každé podsíti.
- Použít překlad názvů poskytovaných Azure nebo zadat vlastní server DNS pro použití podle prostředků, které jsou připojené k virtuální síti.

Další informace o službě Azure Virtual Network, [Přehled virtuálních sítí](../virtual-network/virtual-networks-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) článku. Propojení virtuálních sítí mezi sebou, povolení prostředkům připojeným k jedné virtuální sítě ve virtuálních sítích komunikovat mezi sebou. Propojení virtuálních sítí mezi sebou můžete použít jednu nebo obě z následujících možností:

- **Vytvoření partnerského vztahu:** Umožňuje prostředkům připojeným k jiné virtuální sítě Azure v rámci stejné oblasti Azure ke komunikaci mezi sebou. Na šířku pásma a latencí mezi virtuálními sítěmi je stejný jako v případě, že prostředky, které byly připojené ke stejné virtuální síti. Další informace o partnerských vztazích najdete v článku [přehled partnerských vztahů virtuálních sítí](../virtual-network/virtual-network-peering-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) článku.
- **Brána sítě VPN:** Umožňuje prostředkům připojeným k jiné virtuální sítě Azure v různých oblastech Azure pro komunikaci mezi sebou. Provoz mezi virtuálními sítěmi probíhá prostřednictvím služby Azure VPN Gateway. Šířka pásma mezi virtuálními sítěmi je omezená na šířku pásma brány. Další informace o propojení virtuálních sítí pomocí služby VPN Gateway najdete v článku [konfigurace připojení typu VNet-to-VNet mezi oblastmi](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) článku.

## <a name="internet-connectivity"></a>Připojení k Internetu

Všechny prostředky Azure, které jsou připojené k virtuální síti mají ve výchozím nastavení odchozího připojení k Internetu. Privátní IP adresu prostředku je zdrojové síťové adresy přeložit (SNAT) na veřejnou IP adresu infrastrukturou Azure. Další informace o odchozích připojení k Internetu, přečtěte si [Principy odchozích připojení v Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fnetworking%2ftoc.json) článku.

Ke komunikaci příchozí k prostředkům Azure z Internetu nebo k Internetu bez SNAT odchozí komunikaci, zdroj musí přiřadit veřejnou IP adresu. Další informace o veřejné IP adresy, [veřejné IP adresy](../virtual-network/virtual-network-public-ip-address.md?toc=%2fazure%2fnetworking%2ftoc.json) článku.

## <a name="on-premises-connectivity"></a>Místní připojení

Je můžete zabezpečený přístup k prostředkům ve vaší virtuální síti přes připojení VPN nebo privátní připojení s přímým přístupem. K posílání síťového provozu mezi virtuální sítí Azure a vaší místní síti, musíte vytvořit bránu virtuální sítě. Nakonfigurujete nastavení pro bránu vytvořit typ připojení, které chcete, síť VPN nebo ExpressRoute.

Vaše místní sítě můžete připojit k virtuální síti pomocí jakékoli kombinace následujících možností:

**Point-to-site (VPN přes SSTP)**

Následující obrázek znázorňuje samostatný bod pro připojení k serveru mezi více počítačů a virtuální sítě:

![Point-to-Site](./media/networking-overview/point-to-site.png)

Toto připojení mezi do jednoho počítače a virtuální sítě. Tento typ připojení je skvělý, pokud teprve začínáte s Azure, nebo pro vývojáře, protože nevyžaduje téměř nebo vůbec žádné změny vaší stávající sítě. Je také vhodné, když se připojujete ze vzdáleného umístění, jako je například z místa konání konference nebo home. Připojení point-to-site jsou často svázány s připojením site-to-site prostřednictvím stejné brány virtuální sítě. Připojení používá protokol SSTP poskytnout šifrovanou komunikaci prostřednictvím Internetu mezi počítačem a virtuální síť. Latence pro sítě VPN point-to-site nepředvídatelné, protože provoz prochází Internetem.

**Site-to-site (tunel VPN IPsec/IKE)**

![Site-to-Site](./media/networking-overview/site-to-site.png)

Toto připojení mezi místní zařízení VPN a službou Azure VPN Gateway. Tento typ připojení povoluje místnímu prostředku, který autorizaci pro přístup k virtuální síti. Připojení je protokolu IPSec/IKE VPN, poskytující šifrovanou komunikaci prostřednictvím Internetu mezi místním zařízením a bránou Azure VPN. Několik místních serverů můžete připojit ke stejné bráně VPN. Místní zařízení VPN v každé lokalitě musí mít externě určených pro veřejnou IP adresu, která není za zařízením NAT. Latence pro připojení site-to-site nepředvídatelné, protože provoz prochází Internetem.

**ExpressRoute (vyhrazené soukromé připojení)**

![ExpressRoute](./media/networking-overview/expressroute.png)

Tento typ připojení se naváže mezi vaší sítí a Azure prostřednictvím partnera ExpressRoute. Toto připojení je soukromé. Přenosy neprocházejí přes Internet. Latence pro připojení ExpressRoute je předvídatelné, od provozu nepodporuje procházení Internetu. ExpressRoute můžete kombinovat pomocí připojení site-to-site.

Další informace o všech předchozích možností připojení, přečtěte si [diagramy topologie připojení](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fnetworking%2ftoc.json) článku.

## <a name="load-balancing"></a>Směr provozu a vyrovnávání zatížení

Microsoft Azure poskytuje několik služeb pro správu, jak se distribuuje síťový provoz s vyrovnáváním zatížení. Můžete použít libovolnou z následujících funkcí, samostatně nebo společně:

**Vyrovnávání zatížení DNS**

Služba Azure Traffic Manager poskytuje Vyrovnávání zatížení globálního DNS. Traffic Manager jsou reaguje na klienty s IP adresou v dobrém stavu koncového bodu, na základě jedné z následujících metod směrování:
- **Geografické:** Klienti jsou přesměrováni na konkrétní koncové body (Azure, externí nebo vnořené) na základě, na jaké geografické umístění jejich dotaz DNS pochází z. Tato metoda umožňuje scénáře, kde je důležité vědět, geografické oblasti klienta a směrování je podle něj. Mezi příklady patří vyhovující pověření suverenita dat, lokalizace obsahu & uživatelské prostředí a měření přenosů z různých oblastí.
- **Výkon:** IP adresa, který je vrácen do klienta je "nejbližší" do klienta. 'Co nejblíže' koncový bod není nutně co nejblíž koncovým měřený podle geografické vzdálenosti. Místo toho tato metoda určuje nejbližší koncového bodu, na základě měření latence sítě. Traffic Manager udržuje tabulku Internet latence sledovat dobu odezvy mezi rozsahy IP adres a každé datové centrum Azure.
- **Priorita:** Provoz se směřuje do primárního koncového bodu (nejvyšší priorita). Pokud primární koncový bod není k dispozici, Traffic Manager směruje provoz do druhé koncového bodu. Pokud obě primární a sekundární koncových bodů nejsou k dispozici, provoz směrován na třetí a tak dále. Dostupnost koncového bodu závisí na nakonfigurovaných stavu (povoleno nebo zakázáno) a monitorování probíhající koncových bodů.
- **Vážené kruhové dotazování:** Pro každý požadavek Traffic Manager náhodně vybere koncový bod k dispozici. Pravděpodobnost Výběr koncového bodu podle váhy přiřazené všechny dostupné koncové body. Použitím stejnou váhou pro všechny koncové body výsledky v distribuci i provoz. Pomocí vyšší nebo nižší váhu na konkrétním koncovým bodům způsobí, že tyto koncové body mají více nebo méně často vrátit v odpovědi DNS.

Následující obrázek ukazuje požadavek pro webovou aplikaci přesměrováni na koncový bod webové aplikace. Koncové body mohou být také další služby Azure, jako jsou virtuální počítače a cloudové služby.

![Traffic Manager](./media/networking-overview/traffic-manager.png)

Klient se připojí přímo do tohoto koncového bodu. Azure Traffic Manager zjistí, že koncový bod není v pořádku a klienty přesměruje na koncový bod jiné, v pořádku. Další informace o Traffic Manageru, najdete [Azure Traffic Manager – Přehled](../traffic-manager/traffic-manager-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) článku.

**Vyrovnávání zatížení aplikace**

Služba Azure Application Gateway poskytuje kontroler doručování aplikací (ADC) jako službu. Služba Application Gateway nabízí různé vrstvy 7 (HTTP/HTTPS) možnostmi Vyrovnávání zatížení pro vaše aplikace, včetně brány firewall webových aplikací k ochraně vašich webových aplikací před ohrožením zabezpečení a zneužitím. Služba Application Gateway umožňuje optimalizovat produktivitu webové farmy tím, že se ukončování protokolu SSL náročnou na procesor, ke službě application gateway. 

Další možnosti přesměrování vrstvy 7 obsahovat kruhové dotazování na distribuci příchozích přenosů, spřažení relace na základě souborů cookie, směrování na základě cesty URL a možnost hostování několika webů za jedné službě application gateway. Application Gateway lze nakonfigurovat jako brány přístupem k Internetu, pouze interní brány nebo kombinaci obojího. Služba Application Gateway je Azure plně spravované, škálovatelné a vysoce dostupné. Nabízí celou řadu možností diagnostiky a protokolování, které zlepšují správu. Další informace o službě Application Gateway, přečtěte si [Přehled služby Application Gateway](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fnetworking%2ftoc.json) článku.

Následující obrázek ukazuje adresu URL, – směrování na základě cest pomocí služby Application Gateway:

![Application Gateway](./media/networking-overview/application-gateway.png)

**Vyrovnávání zatížení sítě**

Nástroje Azure Load Balancer poskytuje vysoce výkonné, s nízkou latencí 4 vrstvy Vyrovnávání zatížení pro všechny protokoly UDP a TCP. Spravuje příchozí a odchozí připojení. Veřejné a vnitřní s vyrovnáváním zatížení koncové body můžete nakonfigurovat. Můžete definovat pravidla pro namapování příchozí připojení do back endového fondu cílů s použitím TCP nebo HTTP zjišťování stavu možností pro správu dostupnost služeb. Další informace o nástroji Load Balancer, [Load Balancer – přehled](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) článku.

Následující obrázek znázorňuje vícevrstvou aplikaci přístupem k Internetu, které využívá oba nástroje pro vyrovnávání zatížení externí a interní:

![Nástroj pro vyrovnávání zatížení](./media/networking-overview/load-balancer.png)

## <a name="security"></a>Zabezpečení

Můžete filtrovat provoz do a z prostředků Azure pomocí následujících možností:

- **Síť:** Můžete implementovat skupiny zabezpečení sítě Azure (Nsg) pro filtrování příchozího a odchozího provozu do prostředků Azure. Každá skupina NSG obsahuje jedno nebo více příchozích a odchozích pravidel. Každé pravidlo určuje zdrojové IP adresy, cílové IP adresy, portu a protokolu, který se bude filtrovat provoz s. Skupiny zabezpečení sítě můžete použít pro jednotlivé podsítě a jednotlivým virtuálním počítačům. Další informace o skupinách Nsg najdete v článku [přehled skupin zabezpečení sítě](../virtual-network/security-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) článku.
- **Aplikace:** Pomocí služby Application Gateway s firewallem webových aplikací dokáže ochránit vaše webové aplikace před ohroženími zabezpečení a zneužitím. Běžné příklady jsou SQL útoky prostřednictvím injektáže, skriptování mezi weby a chybně vytvořená záhlaví. Služba Application gateway filtruje provoz a zastaví dosažení vašich webových serverů. Budete moct nakonfigurovat pravidla má povoleno. Poskytuje možnost konfigurovat zásady vyjednávání SSL umožňuje to zakázání určitých zásad. Další informace o firewallu webových aplikací najdete v článku [firewallu webových aplikací](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) článku.

Pokud potřebujete možnost sítě Azure nebude poskytovat nebo chcete použít síť aplikací, které používáte v místním, můžete implementovat produkty ve virtuálních počítačích a připojte se k virtuální síti. [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances) obsahuje několik různých virtuálních počítačů předem nakonfigurovaným programem síťové aplikace mohou právě používáte. Tato předem nakonfigurovaných virtuálních počítačů se obvykle označují jako síťová virtuální zařízení (NVA). Síťová virtuální zařízení jsou k dispozici s aplikací, jako jsou brány firewall a optimalizace sítě WAN.

## <a name="routing"></a>Směrování

Azure vytvoří výchozí směrovací tabulky, které umožňují prostředkům připojeným k žádné podsíti v libovolné virtuální síti komunikovat mezi sebou. Můžete implementovat jednu nebo obě z následujících typů trasy přepisují výchozí trasy, které Azure vytváří:
- **Uživatelem definované:** Nemůžete vytvářet vlastní směrovací tabulky s trasami tento ovládací prvek, kde provoz se směruje do pro každou podsíť. Další informace o trasách definovaných uživatelem najdete v článku [Trasy definované uživatelem](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **Border gateway protocol (BGP):** Je-li propojit vaši virtuální síť k místní síti pomocí Azure VPN Gateway nebo ExpressRoute, můžete rozšířit trasy protokolu BGP pro vaše virtuální sítě. BGP je standardní směrovací protokol, na internetu běžně používaný k výměně informací o směrování a dostupnosti mezi dvěma nebo více sítěmi. Při použití v rámci virtuální sítě Azure, umožňuje Azure VPN Gateway a vaše místní zařízení VPN, nazývají partnerské uzly protokolu BGP nebo okolí, výměnu "tras" informujících obě brány o dostupnosti a dosažitelnosti předpon, chcete-li přejít protokolu BGP prostřednictvím těmito bránami nebo trasami, které jsou zahrnuté. BGP také umožňuje směrování přenosu mezi více sítěmi pomocí šíření tras, které brána s protokolem BGP zjistí od jednoho partnerského uzlu protokolu BGP pro všechny další partnerské uzly protokolu BGP. Další informace o protokolu BGP, najdete v článku [protokolu BGP se službou Azure VPN Gateway přehled](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) článku.

## <a name="manageability"></a>Možnosti správy

Azure poskytuje následující nástroje pro monitorování a Správa sítě:
- **Protokoly aktivit:** Všechny prostředky Azure mají protokoly aktivit, které poskytují informace o operacích provedených místo, stav operací a kdo operaci zahájil. Další informace o protokolech aktivit, [protokoly aktivit přehled](../azure-monitor/platform/activity-logs-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) článku.
- **Diagnostické protokoly:** Pravidelné a spontánních události jsou vytvořené síťové prostředky a protokolovány v účtech Azure storage, odeslaná do centra událostí Azure nebo odeslané do služby Azure Log Analytics. Diagnostické protokoly poskytují přehled o stavu prostředku. Diagnostické protokoly jsou k dispozici pro nástroj pro vyrovnávání zatížení (internetový), skupin zabezpečení sítě, tras a služba Application Gateway. Další informace o diagnostických protokolech najdete v článku [přehled diagnostických protokolů](../azure-monitor/platform/diagnostic-logs-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) článku.
- **Metriky:** Metriky jsou měření výkonu a čítače shromažďují za časové období na prostředky. Metriky lze použít k aktivaci výstrahy na základě prahových hodnot. Metriky jsou teď dostupné ve službě Application Gateway. Další informace o metrikách, [přehled metriky](../monitoring-and-diagnostics/monitoring-overview-metrics.md?toc=%2fazure%2fnetworking%2ftoc.json) článku.
- **Řešení potíží:** Informace o odstraňování potíží je přístupný přímo na webu Azure portal. Informace pomohou diagnostikovat běžné problémy se službou ExpressRoute, VPN Gateway, Application Gateway, protokoly zabezpečení sítě, tras, DNS, nástroj pro vyrovnávání zatížení a Traffic Manager.
- **Řízení přístupu na základě role (RBAC):** Řídíte, kdo může vytvářet a spravovat síťové prostředky s řízením přístupu na základě rolí (RBAC). Další informace o RBAC pro čtení [vám umožní začít RBAC](../role-based-access-control/overview.md?toc=%2fazure%2fnetworking%2ftoc.json) článku. 
- **Zachycení paketů:** Služba Azure Network Watcher nabízí možnost spustit zachytávání paketů na virtuálním počítači prostřednictvím rozšíření ve virtuálním počítači. Tato funkce je dostupná pro virtuální počítače Windows a Linux. Další informace o zachytávání paketů, [přehled zachytávání paketů](../network-watcher/network-watcher-packet-capture-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) článku.
- **Ověření toků protokolu IP:** Network Watcher umožňuje ověření IP toků mezi Virtuálním počítači Azure a vzdáleného prostředku k určení, zda pakety jsou povolené nebo zakázané. Tato funkce poskytuje správcům možnost rychle diagnostikovat problémy s připojením. Další informace o tom, jak ověření IP toků, přečtěte si [ověření toku protokolu IP přehled](../network-watcher/network-watcher-ip-flow-verify-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) článku.
- **Řešení potíží s připojením VPN:** Možnost Poradce při potížích s VPN služby Network Watcher umožňuje dotazování připojení nebo brány a ověřit stav prostředků. Další informace o řešení potíží s připojením VPN najdete v článku [řešení potíží s přehled připojení k síti VPN](../network-watcher/network-watcher-troubleshoot-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) článku.
- **Zobrazení topologie sítě:** Zobrazte grafická reprezentace síťovým prostředkům ve virtuální síti pomocí služby Network Watcher. Další informace o zobrazení topologie sítě, [přehled topologie](../network-watcher/network-watcher-topology-overview.md?toc=%2fazure%2fnetworking%2ftoc.json) článku.

## <a name="tools"></a>Nástroje pro nasazení a konfigurace

Můžete nasadit a nakonfigurovat síťovými prostředky Azure s žádným z následujících nástrojů:

- **Azure portal:** Grafické uživatelské rozhraní, na kterém běží v prohlížeči. Otevřete web [Azure Portal](http://portal.azure.com).
- **Azure PowerShell:** Nástroje příkazového řádku pro správu Azure z počítačů s Windows. Další informace o Azure Powershellu najdete [Přehled prostředí Azure PowerShell](/powershell/azure/overview?view=azurermps-3.8.0?toc=%2fazure%2fnetworking%2ftoc.json) článku.
- **Rozhraní příkazového řádku Azure (CLI):** Nástroje příkazového řádku pro správu Azure z počítačů systému Linux, macOS nebo Windows. Další informace o rozhraní příkazového řádku Azure pro čtení [přehled Azure CLI](/cli/azure/get-started-with-azure-cli?toc=%2fazure%2fnetworking%2ftoc.json) článku.
- **Šablony Azure Resource Manageru:** Soubor (ve formátu JSON), která definuje infrastrukturu a konfiguraci řešení Azure. Pomocí šablony můžete řešení opakovaně nasadit v průběhu životního cyklu a mít přitom jistotu, že se prostředky nasadí konzistentně. Další informace o vytváření šablon najdete v článku [osvědčené postupy pro vytváření šablon](../azure-resource-manager/resource-manager-template-best-practices.md?toc=%2fazure%2fnetworking%2ftoc.json) článku. Šablony můžete nasadit pomocí webu Azure portal, rozhraní příkazového řádku nebo Powershellu. Začínáme se šablonami hned, nasaďte jednu z mnoha předem nakonfigurovaných šablon v [šablony pro rychlý start Azure](https://azure.microsoft.com/resources/templates/?term=network) knihovny. 

## <a name="pricing"></a>Ceny

Některé síťové služby Azure mají poplatek, zatímco ostatní jsou zdarma. Zobrazení [virtuální síť](https://azure.microsoft.com/pricing/details/virtual-network), [VPN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway), [Application Gateway](https://azure.microsoft.com/pricing/details/application-gateway/), [nástroje pro vyrovnávání zatížení](https://azure.microsoft.com/pricing/details/load-balancer), [Network Watcher](https://azure.microsoft.com/pricing/details/network-watcher), [DNS](https://azure.microsoft.com/pricing/details/dns), [Traffic Manageru](https://azure.microsoft.com/pricing/details/traffic-manager) a [ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute) pro další informace o stránkách s cenami.

## <a name="next-steps"></a>Další postup

- Vytvoření první virtuální sítě a připojení několika virtuálními počítači, pomocí kroků v [vytvoření první virtuální sítě](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) článku.
- Připojení počítače k virtuální síti pomocí kroků v [konfigurace připojení typu point-to-site](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) článku.
- Internetový provoz na veřejné servery Vyrovnávání zatížení pomocí kroků v [vytvořit nástroj pro vyrovnávání zatížení přístupem k Internetu](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) článku.
