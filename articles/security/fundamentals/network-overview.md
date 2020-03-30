---
title: Koncepce a požadavky na zabezpečení sítě v Azure | Dokumenty společnosti Microsoft
description: Tento článek obsahuje základní vysvětlení základních konceptů zabezpečení sítě a požadavky a informace o tom, co Azure nabízí v každé z těchto oblastí.
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: TomSh
ms.assetid: bedf411a-0781-47b9-9742-d524cf3dbfc1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/29/2018
ms.author: terrylan
ms.openlocfilehash: 2293618b0685fe71ae553a95797fe8bfe1fe968c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75749947"
---
# <a name="azure-network-security-overview"></a>Přehled zabezpečení sítě Azure

Zabezpečení sítě lze definovat jako proces ochrany prostředků před neoprávněným přístupem nebo útokem použitím ovládacích prvků pro síťový provoz. Cílem je zajistit, aby byl povolen pouze legitimní provoz. Azure obsahuje robustní síťovou infrastrukturu, která podporuje požadavky na připojení aplikací a služeb. Připojení k síti je možné mezi prostředky umístěnými v Azure, mezi místními a hostovanými prostředky Azure a na internet a z Internetu a Azure.

Tento článek popisuje některé možnosti, které Azure nabízí v oblasti zabezpečení sítě. Můžete se dozvědět o:

* Azure sítě
* Řízení přístupu k síti
* Brána Azure Firewall
* Bezpečný vzdálený přístup a připojení mezi místními prostory
* Dostupnost
* Překlad adres
* Architektura hraniční sítě (DMZ)
* Azure DDoS Protection
* Azure Front Door
* Traffic Manager
* Monitorování a detekce hrozeb

## <a name="azure-networking"></a>Azure sítě

Azure vyžaduje, aby se virtuální počítače připojily k virtuální síti Azure. Virtuální síť je logická konstrukce postavená na fyzické síťové struktuře Azure. Každá virtuální síť je izolovaná od všech ostatních virtuálních sítí. To pomáhá zajistit, že síťový provoz ve vašich nasazeních není přístupný ostatním zákazníkům Azure.

Další informace:

* [Přehled virtuální sítě](../../virtual-network/virtual-networks-overview.md)

## <a name="network-access-control"></a>Řízení přístupu k síti

Řízení přístupu k síti je akt omezení připojení a z konkrétních zařízení nebo podsítí v rámci virtuální sítě. Cílem řízení přístupu k síti je omezit přístup k vašim virtuálním počítačům a službám schváleným uživatelům a zařízením. Ovládací prvky přístupu jsou založeny na rozhodnutípovolit nebo odepřít připojení k a z vašeho virtuálního počítače nebo služby.

Azure podporuje několik typů řízení přístupu k síti, například:

* Řízení síťové vrstvy
* Řízení trasy a nucené tunelování
* Zařízení pro zabezpečení virtuální sítě

### <a name="network-layer-control"></a>Řízení síťové vrstvy

Jakékoli zabezpečené nasazení vyžaduje určitou míru řízení přístupu k síti. Cílem řízení přístupu k síti je omezit komunikaci virtuálních strojů na potřebné systémy. Ostatní pokusy o komunikaci jsou blokovány.

> [!NOTE]
> Brány firewall úložiště jsou zahrnuty v článku [přehled zabezpečení úložiště Azure](storage-overview.md)

#### <a name="network-security-rules-nsgs"></a>Pravidla zabezpečení sítě (NSG)

Pokud potřebujete základní řízení přístupu na úrovni sítě (na základě adresy IP a protokolů TCP nebo UDP), můžete použít skupiny zabezpečení sítě (NSG). NSG je základní, stavové, filtrování paketů firewall a umožňuje řídit přístup na základě [5-n-tice](https://www.techopedia.com/definition/28190/5-tuple). Mezi hlavní týmy zabezpečení nefunkčního systému patří funkce pro zjednodušení správy a snížení pravděpodobnosti chyb konfigurace:

* **Rozšířená pravidla zabezpečení** zjednodušují definici pravidel skupiny zabezpečení zabezpečení a umožňují vytvářet složitá pravidla, místo abyste museli vytvářet více jednoduchých pravidel, abyste dosáhli stejného výsledku.
* **Značky služeb** jsou popisky vytvořené společností Microsoft, které představují skupinu adres IP. Dynamicky se aktualizují tak, aby zahrnovaly rozsahy IP adres, které splňují podmínky, které definují zahrnutí do popisku. Například pokud chcete vytvořit pravidlo, které platí pro všechna úložiště Azure ve východní oblasti, můžete použít Storage.EastUS
* **Skupiny zabezpečení aplikací** umožňují nasazovat prostředky do skupin aplikací a řídit přístup k těmto prostředkům vytvořením pravidel, která používají tyto skupiny aplikací. Pokud máte například webové servery nasazené do skupiny aplikací "Webové servery", můžete vytvořit pravidlo, které použije skupinu zabezpečení sítě, která umožňuje přenosy 443 z Internetu do všech systémů ve skupině aplikací "Webové servery".

Skupiny nsg neposkytují kontrolu aplikační vrstvy nebo ověřené ovládací prvky přístupu.

Další informace:

* [Skupiny zabezpečení sítě](../../virtual-network/security-overview.md)

#### <a name="asc-just-in-time-vm-access"></a>ASC právě včas přístup k virtuálním montovna

[Centrum zabezpečení Azure](../../security-center/security-center-intro.md) můžete spravovat skupiny zabezpečení na virtuálních počítačích a uzamknout přístup k virtuálnímu počítači, dokud uživatel s příslušnými oprávněními [rbac](/azure/role-based-access-control/overview) řízení přístupu na základě rolí požádá o přístup. Pokud je uživatel úspěšně autorizován ASC provede změny skupin nsg povolit přístup k vybraným portům po zadanou dobu. Po uplynutí doby nsg jsou obnoveny do jejich předchozí zabezpečený stav.

Další informace:

* [Azure Security Center právě v time accessu](../../security-center/security-center-just-in-time.md)

#### <a name="service-endpoints"></a>Koncové body služby

Koncové body služby jsou dalším způsobem, jak použít kontrolu nad provozem. Můžete omezit komunikaci s podporovanými službami pouze na virtuální sítě přes přímé připojení. Přenosy z vaší virtuální sítě do zadané služby Azure zůstanou v páteřní síti Microsoft Azure.  

Další informace:

* [Koncové body služby](../../virtual-network/virtual-network-service-endpoints-overview.md#secure-azure-services-to-virtual-networks)

### <a name="route-control-and-forced-tunneling"></a>Řízení trasy a nucené tunelování

Schopnost řídit chování směrování ve virtuálních sítích je kritická. Pokud je směrování nakonfigurováno nesprávně, aplikace a služby hostované ve vašem virtuálním počítači se mohou připojit k neoprávněným zařízením, včetně systémů vlastněných a provozovaných potenciálními útočníky.

Azure networking podporuje možnost přizpůsobit chování směrování pro síťový provoz ve virtuálních sítích. To umožňuje změnit výchozí položky směrovací tabulky ve vaší virtuální síti. Řízení chování směrování vám pomůže zajistit, aby veškerý provoz z určitého zařízení nebo skupiny zařízení vstoupil do virtuální sítě nebo ji opustil přes určité umístění.

Můžete mít například zařízení pro zabezpečení virtuální sítě ve virtuální síti. Chcete se ujistit, že veškerý provoz do a z vaší virtuální sítě prochází tímto virtuálním bezpečnostním zařízením. To lze provést konfigurací [uživatelem definované trasy](../../virtual-network/virtual-networks-udr-overview.md) (UDR) v Azure.

[Vynucené tunelování](https://www.petri.com/azure-forced-tunneling) je mechanismus, který můžete použít k zajištění, že vaše služby nejsou povoleny k zahájení připojení k zařízením na internetu. Všimněte si, že se liší od přijímání příchozích připojení a potom reagovat na ně. Front-end ové webové servery musí reagovat na požadavky internetových hostitelů, a proto je povolen příchozí provoz z internetu na tyto webové servery a webové servery mohou reagovat.

Co nechcete povolit, je front-end webový server pro zahájení odchozí ho požadavku. Tyto požadavky mohou představovat bezpečnostní riziko, protože tato připojení lze použít ke stažení malwaru. I v případě, že chcete, aby tyto servery front-end iniciovaly odchozí požadavky na Internet, můžete je vynutit, aby procházely místní webové proxy servery. To umožňuje využít výhod filtrování a protokolování adres URL.

Místo toho byste chtěli použít vynucené tunelové propojení, abyste tomu zabránili. Když povolíte vynucené tunelování, všechna připojení k Internetu jsou vynucena prostřednictvím místní brány. Vynucené tunelové propojení můžete nakonfigurovat využitím udr.

Další informace:

* [Co jsou uživatelem definované trasy a předávání IP adres](../../virtual-network/virtual-networks-udr-overview.md)

### <a name="virtual-network-security-appliances"></a>Zařízení pro zabezpečení virtuální sítě

Zatímco skupiny zabezpečení sítě, udr a vynucené tunelové propojení poskytují úroveň zabezpečení v síťových a transportních vrstvách [modelu OSI](https://en.wikipedia.org/wiki/OSI_model), můžete také povolit zabezpečení na úrovních vyšších než síť.

Požadavky na zabezpečení mohou například zahrnovat:

* Ověření a autorizace před povolením přístupu k aplikaci
* Detekce vniknutí a reakce na vniknutí
* Kontrola aplikační vrstvy pro protokoly na vysoké úrovni
* Filtrování adres URL
* Antivirová ochrana na úrovni sítě a antimalwar
* Ochrana proti botům
* Řízení přístupu k aplikacím
* Další ochrana DDoS (nad ochranou DDoS poskytovanou samotnou strukturou Azure)

K těmto rozšířeným funkcím zabezpečení sítě můžete přistupovat pomocí partnerského řešení Azure. Nejnovější řešení zabezpečení sítě partnerů Azure najdete na [webu Azure Marketplace](https://azure.microsoft.com/marketplace/)a vyhledáním "zabezpečení" a "zabezpečení sítě".

## <a name="azure-firewall"></a>Brána Azure Firewall

Azure Firewall je spravovaná cloudová služba síťového zabezpečení, která chrání vaše prostředky ve virtuálních sítích Azure. Jde o plně stavovou bránu firewall poskytovanou jako služba s integrovanou vysokou dostupností a neomezenou cloudovou škálovatelností. Mezi některé funkce patří:

* Vysoká dostupnost
* Škálovatelnost cloudu
* Pravidla filtrování plně kvalifikovaných názvů domén aplikací
* Pravidla filtrování síťového provozu

Další informace:

* [Azure Firewall – přehled](/azure/firewall/overview)

## <a name="secure-remote-access-and-cross-premises-connectivity"></a>Bezpečný vzdálený přístup a připojení mezi místními prostory

Nastavení, konfigurace a správa prostředků Azure je třeba provést vzdáleně. Kromě toho můžete chtít nasadit [hybridní IT](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) řešení, která mají komponenty místní a ve veřejném cloudu Azure. Tyto scénáře vyžadují zabezpečený vzdálený přístup.

Azure networking podporuje následující scénáře zabezpečeného vzdáleného přístupu:

* Připojení jednotlivých pracovních stanic k virtuální síti
* Připojení místní sítě k virtuální síti pomocí sítě VPN
* Připojení místní sítě k virtuální síti pomocí vyhrazeného propojení WAN
* Vzájemné propojení virtuálních sítí

### <a name="connect-individual-workstations-to-a-virtual-network"></a>Připojení jednotlivých pracovních stanic k virtuální síti

Můžete chtít povolit jednotlivým vývojářům nebo provozním pracovníkům správu virtuálních počítačů a služeb v Azure. Řekněme například, že potřebujete přístup k virtuálnímu počítači ve virtuální síti. Ale vaše zásady zabezpečení neumožňuje RDP nebo SSH vzdálený přístup k jednotlivým virtuálním počítačům. V takovém případě můžete použít připojení [VPN bodu k webu.](../../vpn-gateway/point-to-site-about.md)

Připojení VPN bodu k webu umožňuje nastavit soukromé a zabezpečené připojení mezi uživatelem a virtuální sítí. Když je navázáno připojení VPN, uživatel může RDP nebo SSH přes propojení VPN do libovolného virtuálního počítače ve virtuální síti. (To předpokládá, že uživatel může ověřit a je autorizován.) Point-to-site VPN podporuje:

* Protokol SSTP (Secure Socket Tunneling Protocol) – proprietární protokol VPN založený na protokolu SSL. Řešení SSL VPN může proniknout firewally, protože většina firewallů otevírá tcp port 443, který používá SSL. SSTP je podporována pouze na zařízeních se systémem Windows. Azure podporuje všechny verze Windows, které mají SSTP (Windows 7 a novější).

* IKEv2 VPN, řešení IPsec VPN založené na standardech. IKEv2 VPN je možné použít k připojení ze zařízení se systémem Mac (OSX verze 10.11 a vyšší).

* [OpenVPN](https://azure.microsoft.com/updates/openvpn-support-for-azure-vpn-gateways/)

Další informace:

* [Konfigurace připojení z bodu na místo k virtuální síti pomocí prostředí PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-vpn"></a>Připojení místní sítě k virtuální síti pomocí sítě VPN

Můžete chtít připojit celou podnikovou síť nebo její části k virtuální síti. To je běžné v hybridních it scénářích, kde organizace [rozšiřují své místní datové centrum do Azure](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84). V mnoha případech organizace hostují části služby v Azure a části místní. Například mohou tak učinit, když řešení zahrnuje front-endové webové servery v Azure a back-endové databáze v místním prostředí. Tyto typy "mezimístních" připojení také usnadňují zabezpečení správy prostředků umístěných v Azure a umožňují scénáře, jako je rozšíření řadičů domény služby Active Directory do Azure.

Jedním ze způsobů, jak toho dosáhnout, je použití [sítě VPN site-to-site](https://www.techopedia.com/definition/30747/site-to-site-vpn). Rozdíl mezi vpn site-to-site a VPN z bodu na místo spoáž, že tato síť připojí jedno zařízení k virtuální síti. Síť VPN mezi lokalitami propojuje celou síť (například místní síť) s virtuální sítí. Vpn sítě site-to-site do virtuální sítě používají vysoce zabezpečený protokol VPN v režimu tunelového propojení IPsec.

Další informace:

* [Vytvoření virtuální sítě Správce prostředků s připojením VPN mezi lokalitami pomocí portálu Azure](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Informace o službě VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-dedicated-wan-link"></a>Připojení místní sítě k virtuální síti pomocí vyhrazeného propojení WAN

Připojení VPN z bodu na web a síť site-to-site jsou účinná pro povolení připojení mezi místními prostory. Některé organizace je však považují za následující nevýhody:

* Připojení VPN přesouvají data přes internet. Tato připojení se tak zpřístupní potenciálním problémům se zabezpečením, které se týkají přesouvání dat ve veřejné síti. Kromě toho nelze zaručit spolehlivost a dostupnost pro připojení k internetu.
* Připojení VPN k virtuálním sítím nemusí mít šířku pásma pro některé aplikace a účely, protože max se pohybuje kolem 200 Mbps.

Organizace, které potřebují nejvyšší úroveň zabezpečení a dostupnosti pro svá připojení mezi místními prostory, obvykle používají vyhrazené propojení WAN pro připojení ke vzdáleným sítím. Azure vám poskytuje možnost používat vyhrazené propojení WAN, které můžete použít k připojení místní sítě k virtuální síti. Azure ExpressRoute, Express trasy přímé a Express trasy globální dosah to umožňují.

Další informace:

* [Technický přehled ExpressRoute](../../expressroute/expressroute-introduction.md)
* [ExpressRoute přímé](../../expressroute/expressroute-erdirect-about.md)
* [Globální dosah expresní trasy](../../expressroute/expressroute-global-reach.md)

### <a name="connect-virtual-networks-to-each-other"></a>Vzájemné propojení virtuálních sítí

Pro vaše nasazení je možné použít mnoho virtuálních sítí. Existují různé důvody, proč byste to mohli udělat. Můžete chtít zjednodušit správu nebo zvýšit zabezpečení. Bez ohledu na motivaci pro umístění prostředků do různých virtuálních sítí může nastat čas, kdy chcete, aby se prostředky v každé síti vzájemně propojili.

Jednou z možností je pro služby v jedné virtuální síti pro připojení ke službám v jiné virtuální síti, "opakování zpět" přes internet. Připojení se spustí v jedné virtuální síti, přejde přes internet a pak se vrátí do cílové virtuální sítě. Tato možnost zveřejňuje připojení k problémům se zabezpečením, které jsou vlastní jakékoli internetové komunikaci.

Lepší možností může být vytvoření sítě VPN mezi lokalitami, která se připojuje mezi dvěma virtuálními sítěmi. Tato metoda používá stejný protokol [režimu tunelového propojení PROTOKOLU IPSec](https://technet.microsoft.com/library/cc786385.aspx) jako výše uvedené připojení VPN mezi místními servery.

Výhodou tohoto přístupu je, že připojení VPN je navázáno prostřednictvím síťové struktury Azure, namísto připojení přes internet. To poskytuje další vrstvu zabezpečení ve srovnání s sítěmi VIRTUÁLNÍMI SÍTĚmi site-to-site, které se připojují přes internet.

Další informace:

* [Konfigurace připojení virtuální sítě k virtuální síti pomocí Správce prostředků Azure a Prostředí PowerShell](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

Dalším způsobem, jak připojit virtuální sítě [je partnerský vztah virtuální sítě](../../virtual-network/virtual-network-peering-overview.md). Tato funkce umožňuje připojit dvě sítě Azure tak, aby komunikace mezi nimi probíhala přes páteřní infrastrukturu Microsoftu, aniž by se někdy přecšel přes Internet. Partnerský vztah virtuální sítě můžete připojit dva virtuální počítače ve stejné oblasti nebo dva virtuální počítače v rámci oblastí Azure. Skupiny nsg lze použít k omezení připojení mezi různými podsítěmi nebo systémy.

## <a name="availability"></a>Dostupnost

Dostupnost je klíčovou součástí každého programu zabezpečení. Pokud vaši uživatelé a systémy nemají přístup k tomu, co potřebují pro přístup přes síť, může být služba považována za ohroženou. Azure má síťové technologie, které podporují následující mechanismy vysoké dostupnosti:

* Vyrovnávání zatížení založené na protokolu HTTP
* Vyrovnávání zatížení na úrovni sítě
* Globální vyrovnávání zatížení

Vyrovnávání zatížení je mechanismus určený k rovnoměrnému rozložení připojení mezi více zařízeními. Cílem vyrovnávání zatížení jsou:

* Chcete-li zvýšit dostupnost. Když vyrovnáváte zatížení připojení mezi více zařízeními, jedno nebo více zařízení může být nedostupné bez ohrožení služby. Služby spuštěné na zbývajících online zařízeních mohou nadále zobrazovat obsah ze služby.
* Chcete-li zvýšit výkon. Když vyrovnáváte zatížení připojení mezi více zařízeními, jedno zařízení nemusí zpracovat veškeré zpracování. Místo toho jsou požadavky na zpracování a paměť pro zobrazování obsahu rozloženy na více zařízeních.

### <a name="http-based-load-balancing"></a>Vyrovnávání zatížení založené na protokolu HTTP

Organizace, které spouštějí webové služby, si často přejí mít před těmito webovými službami nástroj pro vyrovnávání zatížení založený na protokolu HTTP. To pomáhá zajistit odpovídající úroveň výkonu a vysokou dostupnost. Tradiční síťové vykladače zatížení spoléhají na protokoly síťových a transportních vrstev. Na druhé straně se vyvažují na základě vlastností protokolu HTTP na bázi protokolu HTTP.

Azure Application Gateway poskytuje vyrovnávání zatížení založené na protokolu HTTP pro vaše webové služby. Aplikační brána podporuje:

* Spřažení relací založené na souborech cookie. Tato funkce zajišťuje, že připojení vytvořená k jednomu ze serverů za tímto systémem vyrovnávání zatížení zůstane mezi klientem a serverem neporušená. Tím je zajištěna stabilita transakcí.
* SSL vyložení. Když se klient připojí k vykladači zatížení, je tato relace zašifrována pomocí protokolu HTTPS (SSL). Chcete-li však zvýšit výkon, můžete pomocí protokolu HTTP (nešifrovaný) připojit mezi nástroj pro vyrovnávání zatížení a webový mši za nástroj pro vyrovnávání zatížení. To se označuje jako "SSL offload", protože webové servery za nástroj pro vyrovnávání zatížení nezažívají režie procesoru spojené s šifrováním. Webové servery proto mohou požadavky na služby požadovat rychleji.
* Směrování obsahu založeného na adrese URL. Tato funkce umožňuje pro vyrovnávání zatížení, aby rozhodnutí o tom, kam předat připojení na základě cílové adresy URL. To poskytuje mnohem větší flexibilitu než řešení, která dělají rozhodnutí o vyrovnávání zatížení na základě ADRES IP.

Další informace:

* [Přehled služby Application Gateway](/azure/application-gateway/application-gateway-introduction)

### <a name="network-level-load-balancing"></a>Vyrovnávání zatížení na úrovni sítě

Na rozdíl od vyrovnávání zatížení založeného na protokolu HTTP provádí vyrovnávání zatížení na úrovni sítě rozhodnutí na základě čísel IP adresy a portu (TCP nebo UDP).
Výhody vyrovnávání zatížení na úrovni sítě v Azure můžete získat pomocí Azure Load Balancer. Některé klíčové charakteristiky balanceru zahrnují:

* Vyrovnávání zatížení na úrovni sítě na základě ip adresy a čísel portů.
* Podpora pro libovolný protokol aplikační vrstvy.
* Vyrovnávání zatížení pro virtuální počítače Azure a instance rolí cloudových služeb.
* Lze použít jak pro aplikace směřující k internetu (externí vyrovnávání zatížení), tak pro aplikace bez připojení k internetu (interní vyrovnávání zatížení) a virtuální počítače.
* Monitorování koncových bodů, které se používá k určení, pokud některé ze služeb za nástroje pro vyrovnávání zatížení byly nedostupné.

Další informace:

* [Nástroj pro vyrovnávání zatížení směřující k internetu mezi více virtuálními počítači nebo službami](/azure/load-balancer/load-balancer-internet-overview)
* [Přehled interního nástroje pro vyrovnávání zatížení](/azure/load-balancer/load-balancer-internal-overview)

### <a name="global-load-balancing"></a>Globální vyrovnávání zatížení

Některé organizace chtějí nejvyšší možnou úroveň dostupnosti. Jedním ze způsobů, jak tohoto cíle dosáhnout, je hostování aplikací v globálně distribuovaných datových centrech. Když je aplikace hostovaná v datových centrech umístěných po celém světě, je možné, že celá geopolitická oblast nebude dostupná a bude mít aplikaci stále v provozu.

Tato strategie vyrovnávání zatížení může také přinést výhody výkonu. Požadavky na službu můžete směrovat do datového centra, které je nejblíže zařízení, které podává požadavek.

V Azure můžete získat výhody globálního vyrovnávání zatížení pomocí Azure Traffic Manageru.

Další informace:

* [Co je Traffic Manager?](../../traffic-manager/traffic-manager-overview.md)

## <a name="name-resolution"></a>Překlad adres

Překlad názvů je kritická funkce pro všechny služby, které hostujete v Azure. Z hlediska zabezpečení může ohrožení zabezpečení funkce překladu názvů vést k tomu, že útočník přesměruje požadavky z vašich webů na web útočníka. Bezpečné překlad názvů je požadavek pro všechny vaše cloudové hostované služby.

Existují dva typy překladu názvů, které je třeba řešit:

* Interní překlad názvů. To se používá služby ve virtuálních sítích, místní sítě nebo obojí. Názvy používané pro interní překlad názvů nejsou přístupné přes internet. Pro optimální zabezpečení je důležité, aby vaše interní schéma překladu názvů nebylo přístupné externím uživatelům.
* Externí překlad názvů. Používají ho lidé a zařízení mimo místní sítě a virtuální sítě. Jedná se o názvy, které jsou viditelné pro internet a slouží k přímému připojení k cloudovým službám.

Pro překlad interních názvů máte dvě možnosti:

* Server DNS virtuální sítě. Při vytváření nové virtuální sítě se vytvoří server DNS za vás. Tento server DNS může přeložit názvy počítačů umístěných v této virtuální síti. Tento server DNS není konfigurovatelný, spravuje jej správce prostředků infrastruktury Azure, a proto vám může pomoci zabezpečit řešení překladu názvů.
* Přineste si vlastní DNS server. Máte možnost dát DNS server podle vlastního výběru na virtuální síti. Tento server DNS může být server DNS integrovaný službou Active Directory nebo vyhrazené řešení serveru DNS poskytované partnerem Azure, které můžete získat na Azure Marketplace.

Další informace:

* [Přehled virtuální sítě](../../virtual-network/virtual-networks-overview.md)
* [Správa serverů DNS používaných virtuální sítí](../../virtual-network/manage-virtual-network.md#change-dns-servers)

Pro externí překlad názvů máte dvě možnosti:

* Hostujte svůj vlastní externí server DNS místně.
* Hostujte svůj vlastní externí DNS server s poskytovatelem služeb.

Mnoho velkých organizací hostuje své vlastní servery DNS místně. Mohou to udělat, protože mají zkušenosti sítí a globální přítomnost, aby tak učinily.

Ve většině případů je lepší hostovat služby překladu názvů DNS u poskytovatele služeb. Tito poskytovatelé služeb mají odborné znalosti sítě a globální přítomnost, aby zajistili velmi vysokou dostupnost pro služby pro překlad názvů. Dostupnost je pro služby DNS nezbytná, protože pokud se vám nezdaří služby pro překlad názvů, nikdo se nebude moci dostat k vašim internetovým službám.

Azure vám poskytuje vysoce dostupné a vysoce výkonné externí řešení DNS ve formě Azure DNS. Toto externí řešení překladu názvů využívá celosvětovou infrastrukturu Azure DNS. Umožňuje hostovat vaši doménu v Azure pomocí stejných přihlašovacích údajů, virtuálních api, nástrojů a fakturace jako ostatní služby Azure. Jako součást Azure také zdědí silné ovládací prvky zabezpečení integrované do platformy.

Další informace:

* [Přehled Azure DNS](../../dns/dns-overview.md)
* [Privátní zóny Azure DNS](../../dns/private-dns-overview.md) umožňují konfigurovat privátní názvy DNS pro prostředky Azure, nikoli automaticky přiřazené názvy bez nutnosti přidávat vlastní řešení DNS.

## <a name="perimeter-network-architecture"></a>Architektura hraniční sítě

Mnoho velkých organizací používá hraniční sítě k segmentaci svých sítí a k vytvoření nárazníkové zóny mezi Internetem a jejich službami. Obvodová část sítě je považována za zónu s nízkým zabezpečením a v tomto segmentu sítě nejsou umístěny žádné prostředky s vysokou hodnotou. Obvykle se zobrazí zařízení zabezpečení sítě, která mají síťové rozhraní v segmentu hraniční sítě. Jiné síťové rozhraní je připojeno k síti, která má virtuální počítače a služby, které přijímají příchozí připojení z Internetu.

Obvodové sítě můžete navrhnout mnoha různými způsoby. Rozhodnutí o nasazení hraniční sítě a potom, jaký typ hraniční sítě použít, pokud se rozhodnete použít, závisí na požadavcích na zabezpečení sítě.

Další informace:

* [Cloudové služby společnosti Microsoft a zabezpečení sítě](network-best-practices.md)

## <a name="azure-ddos-protection"></a>Azure DDoS Protection

Distribuované útoky na dostupnost služeb (DDoS) patří k největším hrozbám pro dostupnost a zabezpečení, se kterými se musejí zákazníci přesouvající aplikace do cloudu vyrovnávat. Útok DDoS se pokusí vyčerpat prostředky aplikace, takže aplikace není k dispozici oprávněným uživatelům. Útoky DDoS můžou cílit na jakýkoli koncový bod, který je veřejně dostupný přes internet.
Microsoft poskytuje ochranu DDoS **známou** jako Basic jako součást platformy Azure. To je zdarma a zahrnuje vždy monitorování a zmírňování běžných útoků na úrovni sítě v reálném čase. Kromě ochrany, která je součástí ochrany DDoS **Basic,** můžete povolit možnost **Standard.** Funkce DDoS Protection Standard zahrnují:

* **Integrace nativní platformy:** Nativně integrované do Azure. Zahrnuje konfiguraci prostřednictvím portálu Azure. DDoS Protection Standard rozumí vašim prostředkům a konfiguraci prostředků.
* **Ochrana na klíč:** Zjednodušená konfigurace okamžitě chrání všechny prostředky ve virtuální síti, jakmile je povolen standard ochrany DDoS. Není vyžadován žádný zásah ani definice uživatele. DDoS Protection Standard okamžitě a automaticky zmírňuje útok, jakmile je detekován.
* **Nepřetržité sledování provozu:** Vaše aplikace provoz vzory jsou sledovány 24 hodin denně, 7 dní v týdnu, hledá ukazatele DDoS útoků. Zmírnění se provádí při překročení zásad ochrany.
* **Zprávy o zmírnění útoku** Sestavy zmírnění útoků používají agregovaná data toku sítě k poskytování podrobných informací o útocích zaměřených na vaše prostředky.
* **Protokoly toku zmírnění útoku** Protokoly toku ke zmírnění útoku umožňují zkontrolovat zrušený provoz, předávaný provoz a další data útoku téměř v reálném čase během aktivního útoku DDoS.
* **Adaptivní ladění:** Inteligentní profilování provozu se učí provoz vaší aplikace v průběhu času a vybírá a aktualizuje profil, který je nejvhodnější pro vaši službu. Profil se upravuje podle toho, jak se v průběhu času mění provoz. Ochrana vrstvy 3 až 7 vrstvy: Poskytuje ochranu ddos v plném zásobníku při použití s bránou firewall webové aplikace.
* **Rozsáhlé škálování zmírnění:** Více než 60 různých typů útoků lze zmírnit, s globální kapacitou, k ochraně proti největší známé útoky DDoS.
* **Metriky útoku:** Souhrnné metriky z každého útoku jsou přístupné prostřednictvím Azure Monitoru.
* **Upozornění na útok:** Výstrahy lze konfigurovat na začátku a zastavení útoku a po dobu trvání útoku pomocí integrovaných metrik útoku. Výstrahy se integrují do vašeho provozního softwaru, jako jsou protokoly Microsoft Azure Monitor, Splunk, Azure Storage, E-mail a portál Azure.
* **Záruka nákladů:**  Kredity služby přenosu dat a horizontálního navýšení kapacity aplikací pro zdokumentované útoky DDoS.
* **DDoS Rychlé responzivní** Zákazníci ddos protection standardu mají nyní přístup k týmu rychlé reakce během aktivního útoku. DRR může pomoci s vyšetřováním útoku, vlastními skutečnosti snižující závažnostmi rizik během analýzy útoku a po útoku.


Další informace:

* [Přehled ochrany DDOS](../../virtual-network/ddos-protection-overview.md)

## <a name="azure-front-door"></a>Azure Front Door

Služba Azure Front Door Service umožňuje definovat, spravovat a monitorovat globální směrování webového provozu. Optimalizuje směrování provozu pro nejlepší výkon a vysokou dostupnost. Azure Front Door umožňuje vytvořit vlastní pravidla firewallu webových aplikací pro řízení přístupu za účelem ochrany úloh HTTP/HTTPS před zneužitím na základě klientských IP adres, kódů zemí a parametrů HTTP. Kromě toho, Front Door také umožňuje vytvářet pravidla omezení rychlosti k boji se škodlivým provozem robota, zahrnuje ssl snižování zátěže a požadavek na HTTP / HTTPS, zpracování aplikační vrstvy.

Samotná platforma Front Door je chráněná službou Azure DDoS Protection Basic. Z důvodu další ochrany je možné ve virtuálních sítích povolit službu Azure DDoS Protection Standard a pomocí automatického ladění a zmírnění chránit prostředky před útoky na vrstvě sítě (TCP/UDP). Přední dveře je vrstva 7 reverzní proxy, to umožňuje pouze webový provoz projít do back-end servery a blokovat jiné typy provozu ve výchozím nastavení.

Další informace:

* Další informace o celé sadě funkcí dveří Azure Front door najdete v [přehledu azure front door](../../frontdoor/front-door-overview.md)

## <a name="azure-traffic-manager"></a>Správce provozu Azure

Azure Traffic Manager je nástroj pro vyrovnávání zatížení provozu na základě DNS, který umožňuje optimálně distribuovat provoz do služeb napříč globálními oblastmi Azure při zajištění vysoké dostupnosti a rychlosti odezvy. Traffic Manager pomocí DNS směruje požadavky klientů do nejvhodnějšího koncového bodu služby v závislosti na metodě směrování provozu a stavu koncových bodů. Koncový bod je jakákoli internetová služba hostovaná v rámci nebo mimo Azure. Traffic Manager monitoruje koncové body a nesměruje provoz na žádné koncové body, které nejsou k dispozici.

Další informace:

* [Azure Traffic manager – přehled](../../traffic-manager/traffic-manager-overview.md)

## <a name="monitoring-and-threat-detection"></a>Monitorování a detekce hrozeb

Azure poskytuje funkce, které vám pomohou v této klíčové oblasti s včasnou detekcí, monitorováním a shromažďováním a kontrolou síťového provozu.

### <a name="azure-network-watcher"></a>Azure Network Watcher

Azure Network Watcher vám může pomoci při řešení potíží a poskytuje zcela novou sadu nástrojů, které vám pomohou s identifikací problémů se zabezpečením.

[Zobrazení skupiny zabezpečení](../../network-watcher/network-watcher-security-group-view-overview.md) pomáhá s auditováním a dodržováním zabezpečení virtuálních počítačů. Pomocí této funkce můžete provádět programové audity a porovnávat zásady směrného plánu definované vaší organizací s efektivními pravidly pro každý virtuální počítač. To vám může pomoci identifikovat všechny konfigurace drift.

[Sběr paketů](../../network-watcher/network-watcher-packet-capture-overview.md) umožňuje zachytit síťový provoz do a z virtuálního počítače. Můžete shromažďovat statistiky sítě a řešit problémy s aplikacemi, které mohou být neocenitelné při vyšetřování narušení sítě. Tuto funkci můžete také použít společně s funkcemi Azure ke spuštění síťových zachycení v reakci na konkrétní výstrahy Azure.

Další informace o sledování sítě a jak začít testovat některé funkce ve vašich testovacích prostředích, najdete v článku [Přehled sledování sítě Azure](../../network-watcher/network-watcher-monitoring-overview.md).

> [!NOTE]
> Nejaktuálnější oznámení o dostupnosti a stavu této služby najdete na [stránce aktualizace Azure](https://azure.microsoft.com/updates/?product=network-watcher).

### <a name="azure-security-center"></a>Azure Security Center

Azure Security Center vám pomůže předcházet, zjišťovat a reagovat na hrozby a poskytuje lepší přehled o zabezpečení prostředků Azure a kontrolu nad tím, že nad vámi máte kontrolu. Poskytuje integrované monitorování zabezpečení a správu zásad v rámci vašich předplatných Azure, pomáhá detekovat hrozby, které by jinak mohly zůstat bez povšimnutí, a pracuje s rozsáhlou sadou řešení zabezpečení.

Security Center pomáhá optimalizovat a monitorovat zabezpečení sítě pomocí:

* Poskytování doporučení pro zabezpečení sítě.
* Sledování stavu konfigurace zabezpečení sítě.
* Upozornění na síťové hrozby, a to jak na úrovni koncového bodu, tak na úrovni sítě.

Další informace:

* [Úvod do Azure Security Center](../../security-center/security-center-intro.md)

### <a name="virtual-network-tap"></a>TAP virtuální sítě

Azure virtual network TAP (Terminal Access Point) umožňuje nepřetržitě streamovat provoz sítě virtuálních strojů do kolektoru síťových paketů nebo analytického nástroje. Kolektor nebo analytický nástroj je poskytován partnerem síťového virtuálního zařízení. Stejný prostředek TAP virtuální sítě můžete použít k agregaci provozu z více síťových rozhraní ve stejném nebo jiném předplatná.

Další informace:

* [Naslouchací zařízení virtuální sítě](../../virtual-network/virtual-network-tap-overview.md)

### <a name="logging"></a>protokolování

Protokolování na úrovni sítě je klíčovou funkcí pro jakýkoli scénář zabezpečení sítě. V Azure můžete protokolovat informace získané pro sítě zabezpečení sítě získat informace o protokolování na úrovni sítě. Při protokolování nsg získáte informace z:

* [Protokoly aktivit](../../azure-monitor/platform/platform-logs-overview.md). Pomocí těchto protokolů můžete zobrazit všechny operace odeslané do předplatných Azure. Tyto protokoly jsou ve výchozím nastavení povolené a lze je použít na webu Azure Portal. Dříve byly známy jako protokoly auditu nebo provozu.
* Protokoly událostí. Tyto protokoly poskytují informace o tom, jaká pravidla nsg byla použita.
* Protokoly čítačů. Tyto protokoly vám vědět, kolikrát každé pravidlo sítě sítě byla použita k odepření nebo povolení provozu.

K zobrazení a analýze těchto protokolů můžete také použít [Microsoft Power BI](https://powerbi.microsoft.com/what-is-power-bi/), výkonný nástroj pro vizualizaci dat.
Další informace:

* [Protokoly monitorování Azure pro skupiny zabezpečení sítě (NSGs)](../../virtual-network/virtual-network-nsg-manage-log.md)
