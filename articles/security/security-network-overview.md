---
title: Síťové koncepty zabezpečení a požadavky v Azure | Microsoft Docs
description: Tento článek obsahuje základní informace o základní koncepty zabezpečení sítě a požadavky a informace na Azure nabízí v každé z těchto oblastí.
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TomSh
ms.assetid: bedf411a-0781-47b9-9742-d524cf3dbfc1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: 843ff1adddcb4c1165ead3f484ad74a503410852
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
---
# <a name="azure-network-security-overview"></a>Přehled zabezpečení sítě Azure
Azure obsahuje robustní síťové infrastruktury pro podporu aplikací a služeb požadavky na připojení. Připojení k síti je možné mezi prostředky, které jsou umístěné v Azure, mezi místními a Azure hostovaným prostředkům a do a z Internetu a Azure.

Cílem tohoto článku je vysvětlit, co Azure nabízí v oblasti zabezpečení sítě. Kromě základních vysvětlení o základní koncepty zabezpečení sítě a požadavky se dozvíte:

* Síť Azure
* Řízení přístupu k síti
* Zabezpečení vzdáleného přístupu a mezi různými místy připojení
* Dostupnost
* Překlad adres
* Architektura hraniční sítě (DMZ)
* Monitorování a zjišťování hrozeb

## <a name="azure-networking"></a>Síť Azure
Třeba virtuální počítače připojení k síti. Pro podporu tohoto požadavku, Azure vyžaduje virtuální počítače k připojení k virtuální síti Azure. Virtuální síť je logická konstrukce postavená na Azure síťových prostředcích infrastruktury. Každé logické virtuální síti je izolovaná od všech ostatních virtuálních sítí. To pomáhá zajistit, že síťový provoz v nasazeních není dostupný ostatním zákazníkům Azure.

Další informace:

* [Přehled virtuálních sítí](../virtual-network/virtual-networks-overview.md)


## <a name="network-access-control"></a>Řízení přístupu k síti
Řízení přístupu k síti je v rámci omezení připojení do a z konkrétní zařízení nebo podsítě virtuální sítě. Cílem řízení přístupu k síti je omezit přístup k virtuálním počítačům a službám schválení uživatelé a zařízení. Řízení přístupu jsou založené na rozhodnutí, která chcete povolit nebo Odepřít připojení do a z virtuálního počítače nebo služby.

Azure podporuje několik typů řízení přístupu k síti, jako například:

* Vrstva síťového ovládacího prvku
* Směrování řízení a vynuceného tunelování
* Virtuální síť zabezpečovací zařízení

### <a name="network-layer-control"></a>Vrstva síťového ovládacího prvku
Žádné zabezpečení nasazení vyžaduje určitou míru řízení přístupu k síti. Cílem řízení přístupu k síti je omezit komunikaci nezbytných systémů virtuálního počítače. Další pokusy o komunikaci jsou blokovány.

Pokud potřebujete řízení úrovně přístupu k základní síti (na základě IP adresy a protokoly TCP nebo UDP), můžete použít skupiny zabezpečení sítě (Nsg). Skupina NSG je basic, stateful, paket filtrování brány firewall a umožňuje vám k řízení přístupu na základě [5 řazené kolekce členů](https://www.techopedia.com/definition/28190/5-tuple). Skupiny Nsg neposkytují kontroly vrstvy aplikací nebo ověřený řízení přístupu.

Další informace:

* [Skupiny zabezpečení sítě](../virtual-network/virtual-networks-nsg.md)

### <a name="route-control-and-forced-tunneling"></a>Směrování řízení a vynuceného tunelování
Možnost řídit chování směrování na virtuálních sítí je důležité. Pokud směrování není správně nakonfigurovaná, aplikace a služby hostované na virtuálním počítači se může připojit k neoprávněným zařízení, včetně systémů vlastníte a která je provozována společností potenciálním útočníkům.

Azure sítě podporuje přizpůsobit chování směrování síťového provozu ve virtuálních sítí. Umožňuje změnit výchozí směrování položky tabulky ve virtuální síti. Řízení chování směrování díky tomu můžete zajistit, aby veškerý provoz z určitého zařízení nebo skupinu zařízení zadá nebo opustí vaši virtuální síť pomocí konkrétního umístění.

Například můžete mít zabezpečení zařízení virtuální sítě ve vaší virtuální síti. Chcete zajistit, aby veškerý provoz do a z vaší virtuální sítě prochází této virtuální zabezpečení zařízení. To provedete tak, že nakonfigurujete [trasy definované uživatelem](../virtual-network/virtual-networks-udr-overview.md) (udr) v Azure.

[Vynuceného tunelování](https://www.petri.com/azure-forced-tunneling) mechanismus, můžete zajistit, že vaše služby nejsou povolené k navázání připojení k zařízení na Internetu. Všimněte si, že se to neliší od přijímat příchozí připojení a pak reagovat na ně. Webových serverů front-end potřebovat reagovat na požadavky z hostitelů v Internetu, a proto internet Source provoz je povolený příchozí na tyto webové servery a webové servery jsou povoleny reagovat.

Co nechcete povolit je front-end webový server zahájíte výstupního požadavku. Takových požadavků, může představovat bezpečnostní riziko, protože tato připojení lze stáhnout malware. I když chcete tyto servery front-end zahájíte odchozí požadavky na Internetu, můžete vynutit, aby projít vaše místní webové proxy servery. To umožňuje využít výhod adresu URL filtrování a protokolování.

Místo toho by chcete předejít pomocí vynucené tunelování. Když povolíte vynucené tunelování, vynuceně přesunuty všechna připojení k Internetu prostřednictvím místní brána. Můžete konfigurovat vynucené tunelování využitím udr.

Další informace:

* [Co jsou trasy definované uživatelem a předávání IP](../virtual-network/virtual-networks-udr-overview.md)

### <a name="virtual-network-security-appliances"></a>Virtuální síť zabezpečovací zařízení
Zatímco skupiny Nsg, udr a vynucené tunelování poskytují úroveň zabezpečení na síť a transportní vrstvy [OSI model](https://en.wikipedia.org/wiki/OSI_model), můžete také povolit zabezpečení na úrovni vyšší, než v síti.

Například požadavky na zabezpečení mohou zahrnovat:

* Ověřování a autorizace před povolením přístupu k aplikaci
* Zjišťování neoprávněných vniknutí a narušení odpovědi
* Kontroly vrstvy aplikace pro protokoly vysoké úrovně
* Filtrování adres URL
* Sítě úrovně antivirového a antimalwarového
* Ochrana proti robota
* Řízení přístupu aplikace
* Další ochrana proti útoku DDoS (nad ochrana proti útoku DDoS, poskytuje prostředky infrastruktury Azure sám sebe)

Tyto funkce Rozšířené sítě zabezpečení lze využívat pomocí služby Azure partnerských řešení. Můžete najít nejaktuálnější sítě Azure partnera řešení zabezpečení navštivte stránky [Azure Marketplace](https://azure.microsoft.com/marketplace/)a při vyhledání "zabezpečení" a "zabezpečení sítě."

## <a name="secure-remote-access-and-cross-premises-connectivity"></a>Zabezpečení vzdáleného přístupu a mezi různými místy připojení
Instalační program, konfiguraci a správu vašich prostředků Azure provést vzdáleně. Kromě toho může chtít nasadit [hybridního IT](http://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) řešení, které mají součásti na místě a ve veřejném cloudu Azure. Tyto scénáře vyžadují zabezpečený vzdálený přístup.

Síť Azure podporuje následující scénáře zabezpečený vzdálený přístup:

* Jednotlivé pracovní stanice se připojit k virtuální síti.
* Připojit k virtuální síti prostřednictvím sítě VPN v místní síti
* Připojit k virtuální síti s vyhrazenou propojení WAN do místní sítě
* Připojit k sobě navzájem virtuální sítě

### <a name="connect-individual-workstations-to-a-virtual-network"></a>Jednotlivé pracovní stanice se připojit k virtuální síti.
Můžete chtít povolit jednotlivé vývojáři nebo pracovníci oddělení provozu ke správě virtuálních počítačů a služeb v Azure. Řekněme například, že potřebujete přístup k virtuálnímu počítači na virtuální síť. Ale vaše zásady zabezpečení protokolu RDP nebo SSH vzdáleného přístupu k jednotlivým virtuální počítačům nepovoluje. V takovém případě můžete použít připojení point-to-site VPN.

Připojení VPN typu point-to-site používá [SSTP VPN](https://technet.microsoft.com/library/cc731352.aspx) protokol, který vám umožní nastavit privátní a bezpečné připojení mezi uživatelem a virtuální sítě. Po vytvoření připojení VPN, uživatel může protokolu RDP nebo SSH přes propojení sítě VPN do jakéhokoli virtuálního počítače ve virtuální síti. (Předpokladem je, že uživatel může ověřit je a oprávnění.)

Další informace:

* [Konfigurace připojení typu point-to-site k virtuální síti pomocí prostředí PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-vpn"></a>Připojit k virtuální síti prostřednictvím sítě VPN v místní síti
Můžete chtít připojit celý podnikové sítě nebo části, k virtuální síti. To je běžné v IT hybridní scénáře, kde organizace [rozšířit jejich místní datacentra do Azure](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84). V mnoha případech organizace hostovat části služby ve službě Azure a částí na místě. Například se může učinit při řešení v Azure obsahuje webových serverů front-end a back-end databáze místně. Tyto typy připojení "mezi různými místy" rovněž usnadňují správu Azure, které jsou umístěné prostředky víc zabezpečit a povolit scénáře, jako je například rozšíření řadiče domény služby Active Directory do Azure.

Jeden ze způsobů, jak provést jde použít [site-to-site VPN](https://www.techopedia.com/definition/30747/site-to-site-vpn). Rozdíl mezi VPN site-to-site a point-to-site VPN je, že k tomu připojí jedno zařízení k virtuální síti. Síť site-to-site VPN připojí k celé síti (například síti na pracovišti) k virtuální síti. Site-to-site VPN do virtuální sítě pomocí vysoce zabezpečených režimu tunelového připojení IPsec protokol VPN.

Další informace:

* [Vytvoření virtuální sítě Resource Manager s připojením VPN typu site-to-site pomocí portálu Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Plánování a návrh pro bránu VPN](../vpn-gateway/vpn-gateway-plan-design.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-dedicated-wan-link"></a>Připojit k virtuální síti s vyhrazenou propojení WAN do místní sítě
Připojení k síti VPN Point-to-site a site-to-site jsou platné pro povolení připojení mezi různými místy. Ale některé organizace zvažte mají mít tyto nevýhody:

* Připojení k síti VPN přesun dat přes internet. To zpřístupňuje tato připojení potenciální potíže se zabezpečením zabývajících se přesouvání dat přes veřejnou síť. Kromě toho nemůže být zaručit spolehlivost a dostupnost pro připojení k Internetu.
* Připojení k síti VPN do virtuální sítě nemusí mít šířku pásma pro některé aplikace a účely, jakmile jsou maximální limit přibližně 200 MB/s.

Organizace, které potřebují nejvyšší úroveň zabezpečení a dostupnosti pro jejich připojení mezi různými místy obvykle slouží k připojení ke vzdáleným lokalitám vyhrazených propojení WAN. Azure poskytuje možnost používat vyhrazené propojení WAN, který slouží k propojení vaší místní sítě k virtuální síti. To umožní Azure ExpressRoute.

Další informace:

* [Technický přehled ExpressRoute](../expressroute/expressroute-introduction.md)

### <a name="connect-virtual-networks-to-each-other"></a>Připojit k sobě navzájem virtuální sítě
Je možné použít mnoho virtuální sítě pro vaše nasazení. Existují různé důvody, proč to můžete udělat. Můžete chtít zjednodušit správu, nebo můžete chtít zvýšení zabezpečení. Bez ohledu na to motivace pro vložení prostředky v různých virtuálních sítích může být časy, kdy chcete prostředky v každé sítě pro vzájemné propojení.

Jednou z možností je pro služby na jednu virtuální síť pro připojení ke službám v jiné virtuální síti, s "zpět ve smyčce" prostřednictvím Internetu. Připojení se spustí na jednu virtuální síť, přejde prostřednictvím Internetu a pak zpátky na cílové virtuální sítě. Tato možnost udává připojení k vyplývajících z libovolné internetové komunikaci problémy se zabezpečením.

Chcete-li vytvořit site-to-site VPN, který se připojuje mezi dvěma virtuálními sítěmi může být lepší volbou. Tato metoda používá stejný [režimu tunelového připojení IPsec](https://technet.microsoft.com/library/cc786385.aspx) protokol jako připojení site-to-site VPN mezi různými místy, uvedených výše.

Výhodou tohoto přístupu je, že připojení VPN je vytvořeno přes Azure síťových prostředcích infrastruktury místo připojení přes internet. To poskytuje další vrstvu zabezpečení, ve srovnání s site-to-site VPN, které se připojují přes internet.

Další informace:

* [Nakonfigurujte připojení VNet-to-VNet s použitím Azure Resource Manageru a prostředí PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

## <a name="availability"></a>Dostupnost
Dostupnost je klíčovou součástí žádné program zabezpečení. Pokud uživatelé a systémy přístup potřebují přístup přes síť, službu lze považovat za narušení. Azure nabízí síťové technologie, které podporují vysokou dostupnost následujících mechanismů:

* Vyrovnávání zatížení založené na protokolu HTTP
* Úrovně služby Vyrovnávání zatížení sítě
* Globální Vyrovnávání zatížení

Vyrovnávání zatížení je mechanismus navržený tak, aby rovnoměrně rozmístit připojení mezi různými zařízeními. Cílem Vyrovnávání zatížení je:

* Pokud chcete zvýšit dostupnost. Při načítání vyrovnávání připojení mezi různými zařízeními, jeden nebo více zařízení, můžete k dispozici bez narušení služby. Se služby spuštěné na zbývající online zařízení můžete nadále poskytovat obsah ze služby.
* Pokud chcete zvýšit výkon. Při načítání vyrovnávání připojení mezi různými zařízeními, jedno zařízení nemá pro zpracování všech procesů. Místo toho zpracování a paměti požadavky pro poskytování obsahu je rozdělena mezi různými zařízeními.

### <a name="http-based-load-balancing"></a>Vyrovnávání zatížení založené na protokolu HTTP
Organizace, které používají webových služeb, často chcete mít k nástroji pro vyrovnávání zatížení založené na protokolu HTTP před tyto webové služby. To pomáhá zajistit odpovídající úroveň výkonu a vysokou dostupnost. Nástroje pro vyrovnávání zatížení tradiční, založené na síti spoléhají na síť a přenosu protokolů vrstvy. Nástroje pro vyrovnávání zatížení založené na protokolu HTTP, na druhé straně rozhodnout na základě charakteristik protokolu HTTP.

Služba Azure Application Gateway poskytuje pro vaše webové služby pro vyrovnávání zatížení založené na protokolu HTTP. Aplikační brána podporuje:

* Spřažení na základě souboru cookie relace. Tato funkce zajišťuje, že připojení do některého ze serverů za tento nástroj pro vyrovnávání zatížení zůstává beze změn mezi klientem a serverem. Tím se zajistí stabilitu transakce.
* Přesměrování zpracování SSL. Pokud se klient připojí s nástrojem pro vyrovnávání zatížení, že je relace šifrována pomocí protokolu HTTPS (SSL). Ale chcete-li zvýšit výkon, můžete protokol HTTP (bez šifrování) pro připojení mezi nástroje pro vyrovnávání zatížení a webový server za nástrojem pro vyrovnávání zatížení. To se označuje jako "Přesměrování zpracování SSL," protože webové servery za nástrojem pro vyrovnávání zatížení nemáte prostředí spojené s šifrování nároky na procesor. Webové servery může proto žádosti o služby rychleji.
* Na základě adresy URL obsahu směrování. Tato funkce umožňuje Vyrovnávání zatížení při rozhodování o tam, kde k dopředného připojení podle cílové adresy URL. To poskytuje flexibilnější než u řešení, které načíst vyrovnávání rozhodnutí, která na základě IP adres.

Další informace:

* [Přehled brány aplikace](../application-gateway/application-gateway-introduction.md)

### <a name="network-level-load-balancing"></a>Úrovně služby Vyrovnávání zatížení sítě
Na rozdíl od založené na protokolu HTTP Vyrovnávání zatížení vyrovnávání zatížení úrovně sítě rozhoduje založené na IP adresu a port (TCP nebo UDP) čísla.
Můžete získat výhody v podobě úrovně služby Vyrovnávání zatížení sítě v Azure pomocí vyrovnávání zatížení Azure. Některé klíčové vlastnosti služby Vyrovnávání zatížení patří:

* Vyrovnávání zatížení úrovně sítě podle IP adresy a portu čísla.
* Podpora pro protokol vrstvy jakékoli aplikace.
* Vyrovnává zatížení na virtuálních počítačích Azure a cloudových služeb instancí rolí.
* Lze použít pro internetové (Vyrovnávání zatížení externí) i bez Internetu facing (interní Vyrovnávání zatížení) aplikace a virtuální počítače.
* Koncový bod monitorování, která se používá k určení, pokud některé z služeb za nástrojem pro vyrovnávání zatížení mají k dispozici.

Další informace:

* [Nástroj pro vyrovnávání zatížení internetového mezi několik virtuálních počítačů nebo služeb](../load-balancer/load-balancer-internet-overview.md)
* [Přehled nástroje pro vyrovnávání zatížení interní](../load-balancer/load-balancer-internal-overview.md)

### <a name="global-load-balancing"></a>Globální Vyrovnávání zatížení
Některé organizace mají nejvyšší úroveň dostupnosti možné. Jedním ze způsobů dosažení tohoto cíle je hostování aplikací v globálně distribuované datových centrech. Když aplikace hostovaný v datových centrech umístěný v celém světě, je možné pro celý geopolitické oblasti k dispozici, a ještě aplikace a spuštěná.

Tato strategie Vyrovnávání zatížení přispět také výhody výkonu. Můžete směrovat požadavky pro službu do datového centra, které je nejblíže zařízení, která odeslala žádost.

V Azure můžete získat výhody v podobě globální Vyrovnávání zatížení pomocí Azure Traffic Manager.

Další informace:

* [Co je Traffic Manager?](../traffic-manager/traffic-manager-overview.md)


## <a name="name-resolution"></a>Překlad adres
Překlad je důležité funkce pro všechny služby, které je hostovat v Azure. Z hlediska zabezpečení může způsobit ohrožení zabezpečení funkce název řešení útočník přesměrování požadavků z vaší lokality k lokalitě útočníka. Zabezpečené překlad je požadavek pro všechny hostované cloudové služby.

Existují dva typy překlad názvů, potřebujete adresu:

* Interní překlad adres. Používá se službami na virtuálních sítí, místní sítě nebo obojí. Názvy používaných pro interní překlad adres nejsou dostupné přes internet. Pro optimální zabezpečení je důležité, aby vaše interní název řešení schéma není přístupná pro externí uživatele.
* Externí název řešení. Používá se tak, že lidé a zařízení mimo vaší místní sítí a virtuální sítě. Toto jsou názvy, které jsou viditelné na Internetu a slouží k přímé připojení k vaší cloudové služby.

Pro interní překlad adres máte dvě možnosti:

* Server DNS virtuální sítě. Když vytvoříte nové virtuální sítě, se vytvoří DNS server. Tento server DNS může překládat názvy počítačů v této virtuální síti. Tento server DNS se nedá konfigurovat, spravuje správce prostředků infrastruktury Azure a proto můžete pomáhá vám zabezpečit vaše řešení rozlišení názvu.
* Přidání serveru DNS. Máte možnost uvedení server DNS podle vlastní volby ve vaší virtuální síti. Tento server DNS může být, že integraci služby Active Directory DNS server, nebo vyhrazený řešení, server DNS poskytovaný Azure partnera, který můžete získat z Azure Marketplace.

Další informace:

* [Přehled virtuálních sítí](../virtual-network/virtual-networks-overview.md)
* [Správa serverů DNS používaných virtuální sítě](../virtual-network/manage-virtual-network.md#change-dns-servers)

Externí překladu IP adres máte dvě možnosti:

* Hostitel vlastní externí DNS místní server.
* Hostování externím serverem DNS s poskytovatelem služeb.

Mnoho velkých organizacích hostovat vlastní DNS servery místně. Vzhledem k tomu, že mají síťové znalosti a globální přítomnosti Uděláte to tak to dělají.

Ve většině případů je lepší pro hostování vaší služby překladu názvů DNS s poskytovatelem služeb. Poskytovatelé služeb mají znalosti sítě a globální přítomnosti zajistit velmi vysoká dostupnost pro vaše služby rozlišení názvů. Dostupnost je nezbytné pro služby DNS, protože pokud vaše služby rozlišení názvů selže, nikdo se nebude moci dosáhnout vaší internetové služby.

Azure poskytuje k s vysokou dostupností a původce externí DNS řešení ve formě Azure DNS. Toto řešení externí název řešení využívá infrastrukturu Azure DNS po celém světě. Umožňuje hostování vaší domény ve službě Azure pomocí stejných přihlašovacích údajů, rozhraní API, nástrojů a fakturace jako jinými službami Azure. V rámci Azure je rovněž dědí silné zabezpečení ovládacích prvků, součástí platformy.

Další informace:

* [Přehled Azure DNS](../dns/dns-overview.md)

## <a name="perimeter-network-architecture"></a>Architektura hraniční sítě
Mnoho velkých organizacích segmentovat sítí pomocí hraniční sítě a vytvořit zónu vyrovnávací paměti mezi Internetu a službám. Část hraniční sítě se považuje za zóny s nízkou úrovní zabezpečení a žádné prostředky vysoké hodnoty jsou umístěny v tomto segmentu sítě. Obvykle se zobrazí sítě zabezpečovací zařízení, které mají síťové rozhraní v segmentu hraniční sítě. Jiné síťové rozhraní je připojený k síti, která obsahuje virtuální počítače a služby, které přijímají příchozí připojení z Internetu.

Hraniční sítě můžete vytvářet v několika různými způsoby. Rozhodnutí o nasazení hraniční síti a jaký typ hraniční sítě má použít, pokud se rozhodnete použít jednu, závisí na požadavcích zabezpečení sítě.

Další informace:

* [Cloudové služby společnosti Microsoft a zabezpečení sítě](../best-practices-network-security.md)


## <a name="monitoring-and-threat-detection"></a>Monitorování a zjišťování hrozeb

Azure poskytuje funkce, které vám pomohou v této oblasti klíče s časná zjišťování, monitorování a shromažďování a kontrola přenos v síti.

### <a name="azure-network-watcher"></a>Sledovací proces sítě Azure
Azure sledovací proces sítě vám může pomoct vyřešit a poskytuje sadu nástrojů pomoct s identifikací problémy se zabezpečením zcela nový.

[Zobrazení skupiny zabezpečení ](../network-watcher/network-watcher-security-group-view-overview.md) pomáhá s kontrolou auditování a zabezpečení virtuálních počítačů. Pomocí této funkce lze provádět programový audity, porovnávání standardních hodnot zásad definovaných ve vaší organizaci, aby efektivní pravidla pro jednotlivé virtuální počítače. Můžete identifikovat všechny odlišily konfigurace.

[Zachytáváním paketů](../network-watcher/network-watcher-packet-capture-overview.md) umožňuje zaznamenat síťový provoz do a z virtuálního počítače. Můžete shromáždit statistiku sítě a problémů s aplikací, které mohou být neocenitelnou pomocí při šetření vniknutí sítě. Tato funkce společně s Azure Functions můžete taky spustit síťových přenosů v reakci na konkrétní Azure výstrahy.

Další informace o sledovací proces sítě a jak zahájit testování některé funkce ve vašem prostředí najdete v tématu [sledovací proces sítě Azure Přehled monitorování](../network-watcher/network-watcher-monitoring-overview.md).

>[!NOTE]
Nejaktuálnější oznámení o dostupnosti a stav této služby, zkontrolujte [Azure aktualizace stránky](https://azure.microsoft.com/updates/?product=network-watcher).

### <a name="azure-security-center"></a>Azure Security Center
Azure Security Center pomáhá zabránit, zjistit a reagovat na hrozby a poskytuje že vyšší přehled a kontrolu nad, zabezpečení vašich prostředků Azure. Poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných Azure, pomáhá zjišťovat hrozby, kterých byste jinak nevšimli a spolupracuje s velké sady řešení zabezpečení.

Security Center pomáhá optimalizovat a monitorování zabezpečení sítě podle:

* Poskytuje doporučení zabezpečení sítě.
* Monitorování stavu konfigurace zabezpečení sítě.
* Výstrahy můžete k síti na základě hrozeb, i na úrovni koncového bodu a sítě.

Další informace:

* [Úvod do Azure Security Center](../security-center/security-center-intro.md)


### <a name="logging"></a>Protokolování
Protokolování na úrovni sítě je klíčovou funkcí pro každý scénář zabezpečení sítě. V Azure můžete protokolovat informace získané pro skupiny Nsg k získání sítě úroveň protokolování informací. S protokolováním NSG můžete získat informace z:

* [Protokoly aktivity](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md). Tyto protokoly slouží k zobrazení všech operací odeslána předplatné Azure. Tyto protokoly jsou ve výchozím nastavení povolená a je možné v rámci portálu Azure. Byly dřív označovala jako auditu nebo provozní protokoly.
* Protokoly událostí. Tyto protokoly poskytují informace o jaké pravidla NSG byly použity.
* Čítač protokoly. Tyto protokoly umožňují vědět, kolikrát každého pravidla NSG bylo použito pro odepřít nebo povolení provozu.

Můžete také použít [Microsoft Power BI](https://powerbi.microsoft.com/what-is-power-bi/), nástroj pro vizualizaci dat výkonné, můžete zobrazit a analyzovat tyto protokoly.

Další informace:

* [Analýzy protokolů pro skupinu zabezpečení sítě (Nsg)](../virtual-network/virtual-network-nsg-manage-log.md)
