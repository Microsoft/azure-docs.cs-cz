---
title: "Sítě koncepty zabezpečení požadavky v Azure | Microsoft Docs"
description: " Tento článek snadno pochopit, co je Microsoft Azure nabízí v oblasti zabezpečení sítě. Poskytujeme základní vysvětlení základní koncepty zabezpečení sítě a informace a požadavky na Azure má nabízet v každé z těchto oblastí. "
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
ms.openlocfilehash: e477ce9a5f9f35d88a6faeb68ee4e0403f64c030
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="azure-network-security-overview"></a>Přehled zabezpečení sítě Azure
Microsoft Azure obsahuje robustní síťové infrastruktury pro podporu aplikací a služeb požadavky na připojení. Připojení k síti je možné mezi prostředky, které jsou umístěné v Azure, mezi místními a Azure hostovaným prostředkům a do a z Internetu a Azure.

Cílem tohoto článku je snazší pochopit, co je Microsoft Azure nabízí v oblasti zabezpečení sítě. Zde jsou základní vysvětlení požadavky a základní koncepty zabezpečení sítě. Poskytujeme také můžete informace toho, co má Azure nabízí v každé z těchto oblastí, jakož i odkazy, abyste získali lepší představu o zajímavé oblasti.

V tomto článku Přehled zabezpečení sítě Azure se zaměřuje na tyto oblasti:

* Síť Azure
* Řízení přístupu k síti
* Zabezpečení vzdáleného přístupu a mezi různými místy připojení
* Dostupnost
* Překlad adres
* Architektura hraniční sítě
* Monitorování a zjišťování hrozeb


## <a name="azure-networking"></a>Sítě Azure
Třeba virtuální počítače připojení k síti. Pro podporu tohoto požadavku, Azure vyžaduje virtuální počítače k připojení k virtuální síti Azure. Virtuální síť Azure je logická konstrukce postavená na Azure síťových prostředcích infrastruktury. Každé logické síti Azure Virtual Network je izolovaná od všech ostatních Azure virtuálních sítí. To pomáhá zajistit, že síťový provoz v nasazeních není dostupný ostatním zákazníkům Microsoft Azure.

Další informace:

* [Přehled služby Virtual Network](../virtual-network/virtual-networks-overview.md)


## <a name="network-access-control"></a>Řízení přístupu k síti
Řízení přístupu k síti je v rámci omezení připojení do a z konkrétní zařízení nebo podsítí ve virtuální síti Azure. Cílem řízení přístupu k síti je omezit přístup k virtuálním počítačům a službám schválení uživatelé a zařízení. Řízení přístupu jsou založené na povolit nebo odepřít rozhodnutí pro připojení do a z virtuálního počítače nebo služby.

Azure podporuje několik typů řízení přístupu k síti, jako:

* Vrstva síťového ovládacího prvku
* Směrování řízení a vynuceného tunelování
* Virtuální síť zabezpečovací zařízení

### <a name="network-layer-control"></a>Vrstva síťového ovládacího prvku
Žádné zabezpečení nasazení vyžaduje určitou míru řízení přístupu k síti. Cílem řízení přístupu k síti je omezit komunikaci se virtuální počítač pro potřeby systémy a že jsou zablokované další pokusy o komunikaci.

Pokud potřebujete řízení úrovně přístupu k základní síti (na základě IP adresy a protokoly TCP nebo UDP), můžete použít skupiny zabezpečení sítě. Skupina zabezpečení sítě (NSG) je základní stavových paketů filtrování brány firewall a umožňuje vám k řízení přístupu na základě [5 řazené kolekce členů](https://www.techopedia.com/definition/28190/5-tuple). Skupiny Nsg neposkytují kontroly vrstvy aplikací nebo ověřený řízení přístupu.

Další informace:

* [Skupiny zabezpečení sítě](../virtual-network/virtual-networks-nsg.md)

### <a name="route-control-and-forced-tunneling"></a>Řízení směrování a vynucené tunelování
Možnost řídit chování směrování na virtuálních sítí Azure je kritických síťových funkce řízení zabezpečení a přístup. Pokud směrování není správně nakonfigurovaná, aplikace a služby hostované na virtuálním počítači může připojit k neoprávněným zařízení, včetně systémů vlastníte a která je provozována společností potenciálním útočníkům.

Azure sítě podporuje přizpůsobit chování směrování síťového provozu ve virtuálních sítí Azure. Umožňuje změnit výchozí směrovací tabulka položky ve virtuální síti Azure. Řízení chování směrování díky tomu můžete zajistit, aby veškerý provoz z určitého zařízení nebo skupinu zařízení zadá nebo opustí vaši virtuální síť pomocí konkrétního umístění.

Například můžete mít zabezpečení zařízení virtuální sítě ve vaší virtuální síti Azure. Chcete zajistit, aby veškerý provoz do a z Azure Virtual Network prochází této virtuální zabezpečení zařízení. To provedete tak, že nakonfigurujete [trasy definované uživatelem](../virtual-network/virtual-networks-udr-overview.md) v Azure.

[Vynuceného tunelování](https://www.petri.com/azure-forced-tunneling) mechanismus, můžete zajistit, že vaše služby nejsou povolené k navázání připojení k zařízení na Internetu. Všimněte si, že se to neliší od přijímat příchozí připojení a pak reagovat na ně. Webových serverů front-end potřebovat reagovat na požadavky z hostitelů v Internetu, a proto Internet Source provoz je povolený příchozí na tyto webové servery a webové servery jsou povoleny reagovat.

Co nechcete povolit je front-end webový server zahájíte výstupního požadavku. Tyto žádosti může představovat bezpečnostní riziko, protože tato připojení může stáhnout malware. I když chcete tyto servery front-end zahájíte odchozí požadavky na Internetu, můžete vynutit, aby projít vaše místní webové proxy servery, takže můžete využít výhod adresu URL pro filtrování a protokolování.

Místo toho by chcete předejít pomocí vynucené tunelování. Když povolíte vynucené tunelování, vynuceně přesunuty všechna připojení k Internetu prostřednictvím místní brána. Můžete konfigurovat vynucené tunelování využitím trasy definované uživatelem.

Další informace:

* [Co jsou trasy definované uživatelem a předávání IP](../virtual-network/virtual-networks-udr-overview.md)

### <a name="virtual-network-security-appliances"></a>Virtuální síť zabezpečovací zařízení
Zatímco skupin zabezpečení sítě, trasy definované uživatelem a vynucené tunelování poskytují úroveň zabezpečení na síť a transportní vrstvy [OSI model](https://en.wikipedia.org/wiki/OSI_model), může nastat situace, když chcete povolit zabezpečení na úrovni vyšší, než síť.

Například požadavky na zabezpečení mohou zahrnovat:

* Ověřování a autorizace před povolením přístupu k aplikaci
* Zjišťování neoprávněných vniknutí a narušení odpovědi
* Kontroly vrstvy aplikace pro protokoly vysoké úrovně
* Filtrování adres URL
* Sítě úrovně antivirového a antimalwarového
* Ochrana proti robota
* Řízení přístupu aplikace
* Další ochrana proti útoku DDoS (nad DDoS ochrany poskytuje prostředky infrastruktury Azure sám sebe)

Tyto funkce Rozšířené sítě zabezpečení lze využívat pomocí služby Azure partnerských řešení. Můžete najít nejaktuálnější sítě Azure partnera řešení zabezpečení navštivte stránky [Azure Marketplace](https://azure.microsoft.com/marketplace/) a při vyhledání "zabezpečení" a "zabezpečení sítě."

## <a name="secure-remote-access-and-cross-premises-connectivity"></a>Zabezpečený vzdálený přístup a připojení mezi více místy
Instalační program, konfiguraci a správu vašich prostředků Azure provést vzdáleně. Kromě toho můžete chtít nasadit [hybridního IT](http://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) řešení, které mají součásti na místě a ve veřejném cloudu Azure. Tyto scénáře vyžadují zabezpečený vzdálený přístup.

Síť Azure podporuje následující scénáře zabezpečený vzdálený přístup:

* Připojit k virtuální síti Azure jednotlivé pracovní stanice
* Připojit k virtuální síti Azure prostřednictvím sítě VPN v místní síti
* Připojit k virtuální síti Azure s vyhrazenou propojení WAN do místní sítě
* Připojit k sobě navzájem virtuální sítě Azure

### <a name="connect-individual-workstations-to-an-azure-virtual-network"></a>Připojit k virtuální sítě Azure jednotlivé pracovní stanice
Může nastat situace, když chcete povolit jednotlivé vývojáře nebo operations pracovníky ke správě virtuálních počítačů a služeb v Azure. Například můžete potřebovat přístup k virtuálnímu počítači na virtuální síť Azure a vaše zásady zabezpečení protokolu RDP nebo SSH vzdáleného přístupu k jednotlivým virtuální počítačům nepovoluje. V takovém případě můžete použít připojení point-to-site VPN.

Připojení VPN typu point-to-site používá [SSTP VPN](https://technet.microsoft.com/library/cc731352.aspx) protokol, který vám umožní nastavit privátní a bezpečné připojení mezi uživatelem a ve virtuální síti Azure. Po navázání připojení VPN, uživatel bude moci protokolu RDP nebo SSH přes propojení sítě VPN do jakéhokoli virtuálního počítače ve virtuální síti Azure (za předpokladu, že uživatel může ověřit je a oprávnění).

Další informace:

* [Konfigurace připojení typu Point-to-Site k virtuální síti pomocí prostředí PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="connect-your-on-premises-network-to-an-azure-virtual-network-with-a-vpn"></a>Připojit k virtuální sítě Azure prostřednictvím sítě VPN v místní síti
Můžete pro celý podnikové sítě nebo části, připojení k virtuální síti Azure. To je běžné v hybridní IT scénáře kde společnosti [rozšířit jejich místní datacentra do Azure](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84). V mnoha případech bude společností hostitelem součástí služby v Azure a částí místně, například při řešení zahrnuje front-end webové servery v Azure a back-end databáze na místě. Tyto typy připojení "mezi různými místy" rovněž usnadňují správu Azure, které jsou umístěné prostředky víc zabezpečit a povolit scénáře, jako je například rozšíření řadiče domény služby Active Directory do Azure.

Jeden ze způsobů, jak provést jde použít [site-to-site VPN](https://www.techopedia.com/definition/30747/site-to-site-vpn). Rozdíl mezi VPN site-to-site a point-to-site VPN je, že point-to-site VPN připojí jedno zařízení k virtuální síti Azure, zatímco site-to-site VPN připojí k celé síti (například síti na pracovišti) k virtuální síti Azure. Sítě VPN Site-to-site k virtuální síti Azure pomocí vysoce zabezpečených režimu tunelového připojení IPsec protokol VPN.

Další informace:

* [Vytvoření virtuální sítě Resource Manager s připojením VPN typu site-to-site pomocí portálu Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Plánování a návrh pro bránu VPN](../vpn-gateway/vpn-gateway-plan-design.md)

### <a name="connect-your-on-premises-network-to-an-azure-virtual-network-with-a-dedicated-wan-link"></a>Připojit k virtuální sítě Azure s vyhrazenou propojení WAN do místní sítě
Připojení k síti VPN Point-to-site a site-to-site jsou platné pro povolení připojení mezi různými místy. Ale některé organizace zvažte mají mít tyto nevýhody:

* Připojení k síti VPN přes Internet přesun dat – to zpřístupňuje tato připojení potenciální potíže se zabezpečením zabývajících se přesouvání dat přes veřejnou síť. Kromě toho nemůže být zaručit spolehlivost a dostupnost pro připojení k Internetu.
* Připojení VPN k virtuálním sítím Azure lze považovat za, že šířka pásma omezená pro některé aplikace a účely, jakmile jsou maximální limit přibližně 200 MB/s.

Organizace, které potřebují nejvyšší úroveň zabezpečení a dostupnosti pro jejich připojení mezi různými místy obvykle slouží k připojení ke vzdáleným lokalitám vyhrazených propojení WAN. Azure poskytuje možnost používat vyhrazené propojení WAN, který slouží k propojení vaší místní sítě k virtuální síti Azure. Tato možnost je povolena prostřednictvím Azure ExpressRoute.

Další informace:

* [Technický přehled ExpressRoute](../expressroute/expressroute-introduction.md)

### <a name="connect-azure-virtual-networks-to-each-other"></a>Připojit k sobě navzájem virtuální sítě Azure
Je možné, budete moci použít mnoho virtuálních sítí Azure pro vaše nasazení. Existuje mnoho důvodů, proč to můžete udělat. Jedním z důvodů, proč může být ke zjednodušení správy; z bezpečnostních důvodů může být jiné. Bez ohledu na to, motivace nebo důvody uvedení prostředky v různých virtuálních sítích Azure může nastat situace, kdy chcete prostředky v každé sítě pro vzájemné propojení.

Jednou z možností by pro služby na jednu virtuální síť Azure pro připojení ke službám na jinou virtuální sítí Azure s "zpět ve smyčce" prostřednictvím Internetu. Připojení by spustit na jednu virtuální síť Azure, přejděte přes Internet a pak se vraťte k cílové síti Azure Virtual Network. Tato možnost udává připojení k vyplývajících a všechny internetové problémy se zabezpečením.

Lepší volbou může být pro vytvoření služby Azure virtuální sítě Azure virtuální sítě site-to-site VPN. Tato Azure virtuální sítě Azure virtuální sítě site-to-site VPN používá stejnou [režimu tunelového připojení IPsec](https://technet.microsoft.com/library/cc786385.aspx) protokol jako připojení site-to-site VPN mezi různými místy, uvedených výše.

Výhodou použití služby Azure virtuální sítě Azure virtuální sítě site-to-site VPN je, že připojení VPN je vytvořeno přes Azure síťových prostředcích infrastruktury místo připojení přes Internet. To poskytuje další vrstvu zabezpečení ve srovnání s site-to-site VPN, které se připojují přes Internet.

Další informace:

* [Nakonfigurujte připojení VNet-to-VNet s použitím Azure Resource Manageru a prostředí PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

## <a name="availability"></a>Dostupnost
Dostupnost je klíčovou součástí žádné program zabezpečení. Pokud uživatelé a systémy přístup potřebují přístup přes síť, službu lze považovat za narušení. Azure nabízí síťové technologie, které podporují vysokou dostupnost následujících mechanismů:

* Vyrovnávání zatížení založené na protokolu HTTP
* Úrovně služby Vyrovnávání zatížení sítě
* Globální Vyrovnávání zatížení

Vyrovnávání zatížení je mechanismus navržený tak, aby rovnoměrně rozmístit připojení mezi různými zařízeními. Cílem Vyrovnávání zatížení je:

* Zvýšit dostupnost – když zavedete vyrovnávání připojení mezi různými zařízeními, jeden nebo více zařízení, můžete k dispozici a se služby spuštěné na zbývající online zařízení můžete pokračovat v práci s obsahem ze služby
* Zvýšení výkonu – při načítání vyrovnávání připojení mezi různými zařízeními, jedno zařízení nemá provést podle procesoru. Místo toho zpracování a paměti požadavky pro poskytování obsahu je rozdělena mezi různými zařízeními.

### <a name="http-based-load-balancing"></a>Vyrovnávání zatížení založené na protokolu HTTP
Organizace, které používají webových služeb, často chcete mít k nástroji pro vyrovnávání zatížení založené na protokolu HTTP před těchto webových služeb pro Nápověda zajistěte odpovídající úrovně výkonu a vysokou dostupnost. Nástroje pro vyrovnávání zatížení obvykle založené na síti, na rozdíl od rozhodnutí podle založené na protokolu HTTP pro vyrovnávání zatížení nástroje pro vyrovnávání zatížení jsou na základě charakteristik protokolu HTTP, nikoli na vrstvě protokoly sítě a přenosu.

Abyste si mohli pro vaše webové služby pro vyrovnávání zatížení založené na protokolu HTTP, Azure poskytuje služby Azure Application Gateway. Podporuje Azure Application Gateway:

* Založené na protokolu HTTP Vyrovnávání zatížení – rozhodnutí o vyrovnávání zatížení jsou provedená na základě vlastnosti speciální s protokolem HTTP
* Spřažení na základě souboru cookie relace – tato funkce je zajištěno, že připojení do některého ze serverů za tento nástroj pro vyrovnávání zatížení zůstává beze změn mezi klientem a serverem. Díky tomu se budou stabilitu transakce.
* Přesměrování zpracování SSL – když se naváže připojení k klienta s nástrojem pro vyrovnávání zatížení, aby relace mezi klientem a nástroje pro vyrovnávání zatížení jsou šifrována pomocí HTTPS (SSL nebo) protokolu. Chcete-li zvýšit výkon, máte ale možnost připojení mezi nástroje pro vyrovnávání zatížení a webový server za použití nástroje pro vyrovnávání zatížení protokolu HTTP (bez šifrování). To se označuje jako "Přesměrování zpracování SSL" protože webové servery za nástrojem pro vyrovnávání zatížení nemáte prostředí spojené s šifrování nároky na procesor a proto byste měli mít k žádosti o služby rychleji.
* Adresa URL obsahu směrování podle – tato funkce umožňuje Vyrovnávání zatížení při rozhodování o přijatých na předávání připojení podle cílové adresy URL. To poskytuje flexibilnější než u řešení, které načíst vyrovnávání rozhodnutí, která na základě IP adres.

Další informace:

* [Přehled brány aplikace](../application-gateway/application-gateway-introduction.md)

### <a name="network-level-load-balancing"></a>Úrovně služby Vyrovnávání zatížení sítě
Na rozdíl od služby Vyrovnávání zatížení založené na protokolu HTTP, vyrovnávání úrovně zatížení sítě umožňuje rozhodnutí Vyrovnávání zatížení založené na IP adresu a port (TCP nebo UDP) čísla.
Můžete získat výhody v podobě úrovně služby Vyrovnávání zatížení sítě v Azure pomocí vyrovnávání zatížení Azure. Některé klíčové vlastnosti služby Vyrovnávání zatížení Azure patří:

* Vyrovnávání zatížení úrovně sítě podle IP adresy a portu čísla
* Podpora pro protokol vrstvy jakékoli aplikace
* Vyrovnává zatížení na virtuálních počítačích Azure a cloudových služeb instance rolí
* Lze použít pro internetové (Vyrovnávání zatížení externí) i bez Internetu facing (interní Vyrovnávání zatížení) aplikace a virtuální počítače
* Koncový bod monitorování, která se používá k určení, pokud některé z služeb za nástrojem pro vyrovnávání zatížení mají k dispozici

Další informace:

* [Nástroj pro vyrovnávání zatížení z internetu mezi několika virtuálními počítači nebo službami](../load-balancer/load-balancer-internet-overview.md)
* [Přehled nástroje pro vyrovnávání zatížení interní](../load-balancer/load-balancer-internal-overview.md)

### <a name="global-load-balancing"></a>Globální Vyrovnávání zatížení
Některé organizace bude chtít nejvyšší úroveň dostupnosti možné. Jedním ze způsobů dosažení tohoto cíle je hostování aplikací v globálně distribuované datových centrech. Když aplikace hostovaný v datových centrech umístěný v celém světě, je možné pro celý geopolitické oblasti, aby se k dispozici a stále dostupné aplikace a spuštěná.

Kromě dostupnosti výhody, které dostanete tak, že hostování aplikací v datových centrech globálně distribuované také můžete získat výhody výkonu. Tyto výhody výkonu lze získat pomocí mechanismus, který přesměruje požadavky pro službu do datového centra, které je nejblíže zařízení, která odeslala žádost.

Vyrovnávání zatížení globální vám může poskytnout obě tyto výhody. V Azure můžete získat výhody v podobě globální Vyrovnávání zatížení pomocí Azure Traffic Manager.

Další informace:

* [Co je Traffic Manager?](../traffic-manager/traffic-manager-overview.md)


## <a name="name-resolution"></a>Překlad názvů
Překlad je důležité funkce pro všechny služby, které je hostovat v Azure. Z hlediska zabezpečení může způsobit ohrožení zabezpečení funkce název řešení útočník přesměrování požadavků z vaší lokality k lokalitě útočníka. Zabezpečené překlad je požadavek pro všechny hostované cloudové služby.

Existují dva typy překlad názvů, potřebujete adresu:

* Interní překlad adres – interní překlad adres se používá služby v Azure Virtual Network, místní sítě nebo obojí. Názvy používaných pro interní překlad adres nejsou dostupné přes Internet. Pro optimální zabezpečení je důležité, aby vaše interní název řešení schéma není přístupná pro externí uživatele.
* Externí název řešení – externí překlad názvů používá uživatele a zařízení mimo místní a virtuální sítí Azure. Toto jsou názvy, které jsou viditelné na Internet a slouží k přímé připojení k vaší cloudové služby.

Pro interní překlad adres máte dvě možnosti:

* Serveru DNS virtuální sítě Azure – při vytváření nové virtuální sítě Azure, server DNS se vytvoří pro vás. Tento server DNS může překládat názvy počítačů, které jsou umístěné ve virtuální síti Azure. Tento server DNS není Konfigurovatelný a spravuje správce prostředků infrastruktury Azure, a tím řešení zabezpečeného název řešení.
* Přineste vlastní server DNS – máte možnost uvedení server DNS podle vlastní volby ve vaší virtuální síti Azure. Tento server DNS může být, že integraci služby Active Directory DNS server, nebo vyhrazený řešení, server DNS poskytovaný Azure partnera, který můžete získat z Azure Marketplace.

Další informace:

* [Přehled služby Virtual Network](../virtual-network/virtual-networks-overview.md)
* [Správa serverů DNS používaných virtuální síť (VNet)](../virtual-network/manage-virtual-network.md#change-dns-servers)

Pro externí překlad DNS máte dvě možnosti:

* Hostování vlastní externí DNS místního serveru
* Hostování externím serverem DNS s poskytovatelem služeb

Mnoho velkých organizacích bude hostovat vlastní DNS servery místně. Vzhledem k tomu, že mají síťové znalosti a globální přítomnosti Uděláte to tak to dělají.

Ve většině případů je lepší pro hostování vaší služby překladu názvů DNS s poskytovatelem služeb. Poskytovatelé služeb mají znalosti sítě a globální přítomnosti zajistit velmi vysoká dostupnost pro vaše služby rozlišení názvů. Dostupnost je nezbytné pro služby DNS, protože pokud vaše služby rozlišení názvů selže, nikdo se nebude moci dosáhnout vaší internetové služby.

Azure poskytuje s vysokou dostupností a původce externí DNS řešení ve formě Azure DNS. Toto řešení externí název řešení využívá infrastrukturu Azure DNS po celém světě. Umožňuje hostování vaší doméně v Azure pomocí stejných přihlašovacích údajů, rozhraní API, nástrojů a fakturace jako jinými službami Azure. V rámci Azure je rovněž dědí silné zabezpečení ovládacích prvků, součástí platformy.

Další informace:

* [Přehled Azure DNS](../dns/dns-overview.md)

## <a name="dmz-architecture"></a>Architektura hraniční sítě
Mnoho organizací enterprise používá zóny DMZ segmentovat sítí vytvořit zónu vyrovnávací paměti mezi Internetu a službám. Část DMZ sítě se považuje za zóny s nízkou úrovní zabezpečení a žádné prostředky vysoké hodnoty jsou umístěny v tomto segmentu sítě. Obvykle se zobrazí sítě zabezpečovací zařízení, které mají síťové rozhraní na segmentu DMZ a jiné síťové rozhraní, které jsou připojené k síti, která má virtuální počítače a služby, které přijímají příchozí připojení z Internetu.

Existuje mnoho variant DMZ návrhu a rozhodnutí o nasazení DMZ, a pak DMZ má použít, pokud se rozhodnete použít jednu, jaký typ je založena na požadavcích zabezpečení sítě.

Další informace:

* [Cloudové služby společnosti Microsoft a zabezpečení sítě](../best-practices-network-security.md)


## <a name="monitoring-and-threat-detection"></a>Monitorování a zjišťování hrozeb

Azure poskytuje funkce, které vám pomohou v této oblasti klíče s časná zjišťování, monitorování a schopnost shromažďovat a zkontrolujte síťový provoz.

### <a name="azure-network-watcher"></a>Sledovací proces sítě Azure
Azure sledovací proces sítě obsahuje velké množství funkcí, které pomáhají při řešení problémů a také poskytují zcela nový sadu nástrojů pomoct s identifikací problémy se zabezpečením.

[Zobrazení skupiny zabezpečení ](/network-watcher/network-watcher-security-group-view-overview.md) pomáhá s kontrolou auditování a zabezpečení virtuálních počítačů a slouží k provádění programový audity porovnávání standardních hodnot zásad definovaných ve vaší organizaci, aby efektivní pravidla pro jednotlivé virtuální počítače. Můžete identifikovat všechny odlišily konfigurace.

[Zachytáváním paketů](/network-watcher/network-watcher-packet-capture-overview.md) umožňuje zaznamenat síťový provoz do a z virtuálního počítače. Kromě toho, tedy zvýšení tím, že se můžete shromáždit statistiku sítě a problémů v aplikaci mohou být neocenitelnou pomocí při šetření sítě vniknutí zachytáváním paketů. Tato funkce společně s Azure Functions můžete také použít ke spuštění síťových přenosů v reakci na konkrétní Azure výstrahy.

Další informace o sledovací proces sítě Azure a jak zahájit testování některé z funkcí, které ve vaší laboratoře podívejte se na [sledovací proces sítě Azure Přehled monitorování](/network-watcher/network-watcher-monitoring-overview.md)

>[!NOTE]
Azure sledovací proces sítě je stále ve verzi public preview, takže se nemusí mít stejnou úroveň dostupnost a spolehlivost jako verze služby, které jsou obecné dostupnosti. Některé funkce nemusí být podporována, může mít omezené možnosti a nemusí být k dispozici ve všech Azure umístění. Nejaktuálnější oznámení o dostupnosti a stav této služby, zkontrolujte [Azure aktualizace stránky](https://azure.microsoft.com/updates/?product=network-watcher)

### <a name="azure-security-center"></a>Azure Security Center
Security Center pomáhá zabránit, zjistit a reagovat na hrozby a poskytuje že vyšší přehled a kontrolu nad, zabezpečení vašich prostředků Azure. Poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných Azure, pomáhá zjišťovat hrozby, kterých byste jinak nevšimli a spolupracuje s velké sady řešení zabezpečení.

Azure Security Center pomáhá optimalizovat a monitorování zabezpečení sítě podle:

* Poskytuje doporučení zabezpečení sítě
* Monitorování stavu konfigurace zabezpečení sítě
* Výstrahy upozorňující na základě hrozeb pro síť i na úrovni koncového bodu a sítě

Další informace:

* [Úvod do Azure Security Center](../security-center/security-center-intro.md)


### <a name="logging"></a>Protokolování
Protokolování na úrovni sítě je klíčovou funkcí pro každý scénář zabezpečení sítě. V Azure můžete protokolovat informace získané pro skupiny zabezpečení sítě získat protokolování informací o úrovni sítě. S protokolováním NSG můžete získat informace z:

* [Protokoly aktivity](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) – tyto protokoly jsou používány k zobrazení všech operací odeslána předplatné Azure. Tyto protokoly jsou ve výchozím nastavení povolená a je možné v rámci portálu Azure. Byly dřív označovala jako "Protokoly auditu" nebo "Operační protokoly".
* Protokoly událostí – tyto protokoly poskytují informace o jaké pravidla NSG byly použity.
* Čítač protokoly – tyto protokoly umožňují vědět, kolikrát každého pravidla NSG bylo použito pro odepřít nebo povolení provozu.

Můžete také použít [Microsoft Power BI](https://powerbi.microsoft.com/what-is-power-bi/), nástroj pro vizualizaci dat výkonné, můžete zobrazit a analyzovat tyto protokoly.

Další informace:

* [Analýzy protokolů pro skupinu zabezpečení sítě (Nsg)](../virtual-network/virtual-network-nsg-manage-log.md)
