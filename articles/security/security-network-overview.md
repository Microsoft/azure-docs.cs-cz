---
title: Síťové požadavky v oblasti Azure a koncepty zabezpečení | Dokumentace Microsoftu
description: Tento článek obsahuje základní informace o základní koncepty zabezpečení sítě a požadavky a informace o Azure nabízí v každé z těchto oblastí.
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: TomSh
ms.assetid: bedf411a-0781-47b9-9742-d524cf3dbfc1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/29/2018
ms.author: terrylan
ms.openlocfilehash: 30a7eea751a9fbfa0ee95a7edb039a13860be7f8
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56115466"
---
# <a name="azure-network-security-overview"></a>Přehled zabezpečení sítě Azure

Zabezpečení sítě může být definován jako proces ochrany prostředků před neoprávněným přístupem nebo útok s použitím ovládacích prvků pro síťový provoz. Cílem je zajistit, že je povolený jenom legitimní provoz. Azure obsahuje robustní síťovou infrastrukturu pro podporu aplikací a požadavky na připojení služby. Připojení k síti je možné mezi prostředky umístěné v Azure, mezi místním a hostované v Azure prostředky a do a z Internetu a z Azure.

Tento článek popisuje některé z možností, které Azure nabízí v oblasti zabezpečení sítě. Informace o:

* Sítě Azure
* Řízení přístupu k síti
* Brána Azure Firewall
* Zabezpečené připojení vzdáleného přístupu a mezi různými místy
* Dostupnost
* Překlad adres
* Architektura hraniční síti (DMZ)
* Azure DDoS Protection
* Branou Azure
* Traffic Manager
* Sledování a detekci hrozeb

## <a name="azure-networking"></a>Sítě Azure

Azure vyžaduje virtuální počítače připojit ke službě Azure Virtual Network. Virtuální síť je logická konstrukce postavené na Azure síťových prostředcích infrastruktury. Každá virtuální síť je izolovaná od všech ostatních virtuálních sítí. To pomáhá zajistit, že síťový provoz v nasazeních není dostupný ostatním zákazníkům Azure.

Další informace:

* [Přehled služby Virtual network](../virtual-network/virtual-networks-overview.md)

## <a name="network-access-control"></a>Řízení přístupu k síti

Řízení přístupu k síti je v rámci omezení připojení do a z konkrétních zařízení nebo podsítí ve virtuální síti. Cílem řízení přístupu k síti je omezit přístup k vašim virtuálním počítačům a službám na schválené uživatele a zařízení. Řízení přístupu jsou založeny na rozhodnutí, která chcete povolit nebo zakázat připojení do a z virtuálního počítače nebo služby.

Azure podporuje několik typů řízení přístupu k síti, jako například:

* Vrstva síťového ovládacího prvku
* Ovládací prvek směrování a vynucené tunelování
* Virtuální síťová zabezpečovací zařízení

### <a name="network-layer-control"></a>Vrstva síťového ovládacího prvku

Jakékoli zabezpečené nasazení vyžaduje určitou míru řízení přístupu k síti. Cílem řízení přístupu k síti je omezit komunikaci virtuálních počítačů s nezbytné systémy. Další pokusy o komunikaci jsou zablokované.

>[!NOTE]
Úložiště brány firewall jsou popsané v [Přehled zabezpečení služby Azure storage](security-storage-overview.md) článku

#### <a name="network-security-rules-nsgs"></a>Pravidla zabezpečení sítě (Nsg)

Pokud potřebujete řízení přístupu na úrovni základní sítě (na základě IP adresy a protokoly TCP nebo UDP), můžete použít skupiny zabezpečení sítě (Nsg). Skupina zabezpečení sítě je basic, stavová, brány firewall pro filtrování paketů a umožňuje vám k řízení přístupu na základě [5 řazené kolekce členů](https://www.techopedia.com/definition/28190/5-tuple). Skupiny Nsg patří funkce, které zjednodušuje správu a snížilo riziko chyby konfigurace:

* **Rozšířená pravidla zabezpečení** zjednodušují definici pravidla skupiny zabezpečení sítě a umožňují vytvářet komplexní pravidla, spíše než by bylo nutné vytvořit několik jednoduchých pravidel k dosažení stejného výsledku.
* **Značky služeb** vytvářejí Microsoft popisky, které představují skupiny IP adres. Dynamicky aktualizují zahrnout rozsahy IP adres, které splňují podmínky, které definují zahrnutí v popisku. Například pokud chcete vytvořit pravidlo, které se vztahuje na všechna úložiště Azure v oblasti Východ můžete Storage.EastUS
* **Skupiny zabezpečení aplikací** vám umožní nasadit prostředky do skupiny aplikací a řízení přístupu k těmto prostředkům vytvořením pravidel, která používá tyto skupiny aplikací. Například pokud máte nasazení do skupiny aplikací "Webové servery" webové servery můžete vytvořit pravidla, která se vztahuje skupiny zabezpečení sítě povoluje provoz 443 z Internetu do všech systémů ve skupině aplikací "Webové servery".

Skupiny Nsg se neposkytuje kontroly vrstvy aplikace nebo ověřený řízení přístupu.

Další informace:

* [Skupiny zabezpečení sítě](../virtual-network/security-overview.md)

#### <a name="asc-just-in-time-vm-access"></a>ASC dočasný přístup virtuálních počítačů v

[Azure security center](../security-center/security-center-intro.md) můžete spravovat skupiny zabezpečení sítě na virtuálních počítačích a uzamčení přístupu k virtuálnímu počítači až do uživatele s ovládacím prvkem odpovídající přístup na základě rolí [RBAC](../role-based-access-control/overview.md) oprávnění požádá o přístup. Když se uživatel úspěšně provede autorizované ASC změny skupiny Nsg k povolení přístupu k vybrané porty po dobu uvedenou. Když čas vyprší platnost skupiny zabezpečení sítě budou obnoveny do jejich předchozího stavu zabezpečené.

Další informace:

* [Azure Security Center dočasný přístup v](../security-center/security-center-just-in-time.md)

#### <a name="service-endpoints"></a>Koncové body služby

Koncové body služby se jiný způsob, jak aplikovat řídit provoz. Komunikace s podporovaných služeb můžete omezit na jen virtuální sítě přes přímé připojení. Provoz z vaší virtuální sítě do zadané služby Azure zůstává v páteřní síti Microsoft Azure.  

Další informace:

* [Koncové body služby](../virtual-network/virtual-network-service-endpoints-overview.md#securing-azure-services-to-virtual-networks)

### <a name="route-control-and-forced-tunneling"></a>Ovládací prvek směrování a vynucené tunelování

Možnost řízení chování směrování ve virtuálních sítích je důležité. Pokud směrování není správně nakonfigurovaná, aplikace a služby hostované ve vašem virtuálním počítači může připojit k Neautorizovaná zařízení, včetně systémů vlastněné a provozované společností potenciální útočníky.

Sítě Azure podporuje schopnost přizpůsobit chování směrování síťového provozu ve virtuálních sítích. To umožňuje změnit výchozí směrování položky tabulky ve službě virtual network. Řízení chování směrování vám pomůže zajistit, že veškerý provoz z určitého zařízení nebo skupině zařízení přejde do nebo ji opustí vaši virtuální síť prostřednictvím určitého umístění.

Například může mít zařízení pro zabezpečení virtuální sítě ve vaší virtuální síti. Chcete, aby se zajistilo, že veškerý provoz do a z vaší virtuální sítě prochází skrz virtuální zabezpečení zařízení. Můžete to provést konfigurací [trasy definované uživatelem](../virtual-network/virtual-networks-udr-overview.md) (udr) v Azure.

[Vynucené tunelování](https://www.petri.com/azure-forced-tunneling) virtuálních sítí je mechanismus, který můžete použít k zajištění, že vaše služby nejsou povoleny k navázání připojení k zařízení na Internetu. Všimněte si, že se liší od přijímat příchozí připojení a pak reagovat na ně. Front-endové webové servery musí reagovat na žádosti z Internetu hostitelů a proto síti internet opensourcového vizualizačního provoz je povolený příchozí pro tyto webové servery a webové servery můžou reagovat.

Co nechcete povolit je front-end webový server iniciovat odchozí požadavek. Požadavky, může představovat bezpečnostní riziko, protože tato připojení je možné stáhnout malware. I v případě, že chcete tyto servery front-end iniciovat odchozí požadavky na Internetu, můžete chtít vynutit je na vaše místní webové proxy servery. To umožňuje využít výhod adresu URL, filtrování a protokolování.

Místo toho byste k tomu použít vynucené tunelování. Když povolíte vynucené tunelování, vložení je vynuceno všechna připojení k Internetu prostřednictvím místní brány. Můžete nakonfigurovat vynucené tunelování s využitím trasy definované uživatelem.

Další informace:

* [Co jsou trasy definované uživatelem a předávání IP](../virtual-network/virtual-networks-udr-overview.md)

### <a name="virtual-network-security-appliances"></a>Virtuální síťová zabezpečovací zařízení

Skupiny zabezpečení sítě, trasy definované uživatelem a vynuceném tunelování nabízejí úroveň zabezpečení na síť a přenosové vrstvy [modelu OSI](https://en.wikipedia.org/wiki/OSI_model), můžete také povolit zabezpečení na úrovni vyšší než v síti.

Vaše požadavky na zabezpečení mohou například zahrnovat:

* Ověřování a autorizace před povolením přístupu do vaší aplikace
* Zjišťování neoprávněných vniknutí a neoprávněného vniknutí odpovědi
* Kontrola vrstvy aplikace pro základní protokoly
* Filtrování adres URL
* Síťové úrovni antivirového a antimalwarového
* Anti-bot ochrany
* Řízení přístupu aplikace
* Další ochranu před útoky DDoS (nad DDoS protection poskytuje prostředky infrastruktury Azure, samotný)

Tyto funkce zabezpečení rozšířeného sítě můžete přistupovat pomocí Azure partnerského řešení. Najdete nejaktuálnější sítě partnerů Azure řešení zabezpečení přechodem [Azure Marketplace](https://azure.microsoft.com/marketplace/)a vyhledáním "zabezpečení" a "zabezpečení sítě."

## <a name="azure-firewall"></a>Brána Azure Firewall

Azure Firewall je spravovaná cloudová služba síťového zabezpečení, která chrání vaše prostředky ve virtuálních sítích Azure. Jde o plně stavovou bránu firewall poskytovanou jako služba s integrovanou vysokou dostupností a neomezenou cloudovou škálovatelností. Některé funkce:

* Vysoká dostupnost
* Škálovatelnosti cloudu
* Pravidla filtrování plně kvalifikovaných názvů domén aplikací
* Pravidla filtrování síťového provozu

Další informace:

* [Přehled brány Firewall služby Azure](../firewall/overview.md)

## <a name="secure-remote-access-and-cross-premises-connectivity"></a>Zabezpečené připojení vzdáleného přístupu a mezi různými místy

Nastavení, konfiguraci a správu vašich prostředků Azure je nutné provést vzdáleně. Kromě toho, kterou chcete nasadit [hybridní IT](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) řešení, která mají komponenty místní a ve veřejném cloudu Azure. Tyto scénáře vyžadují zabezpečený vzdálený přístup.

Sítě Azure podporuje následující scénáře zabezpečený vzdálený přístup:

* Jednotlivé pracovní stanice se připojit k virtuální síti
* Připojení místní sítě k virtuální síti pomocí sítě VPN
* Připojení místní sítě k virtuální síti s vyhrazenou propojení WAN
* Propojení virtuálních sítí mezi sebou

### <a name="connect-individual-workstations-to-a-virtual-network"></a>Jednotlivé pracovní stanice se připojit k virtuální síti

Můžete chtít povolit samostatné vývojáře nebo pracovníci operace pro správu virtuálních počítačů a služeb v Azure. Řekněme například, že potřebujete přístup k virtuálnímu počítači ve virtuální síti. Ale zásady zabezpečení protokolu RDP nebo SSH vzdáleného přístupu k jednotlivým virtuální počítačům nepovoluje. V takovém případě můžete použít [point-to-site VPN](../vpn-gateway/point-to-site-about.md) připojení.

Připojení point-to-site VPN umožňuje nastavit soukromé a bezpečné připojení mezi uživatelem a virtuální sítě. Po vytvoření připojení VPN, uživatel může pomocí protokolu RDP nebo SSH přes propojení sítě VPN do libovolného virtuálního počítače ve virtuální síti. (Předpokladem je, že uživatel může ověřit a autorizaci.) Podporuje sítě Point-to-site VPN:

* Zabezpečte SSTP Socket Tunneling Protocol (), speciální protokol VPN založený na protokolu SSL. Řešení typu VPN protokolu SSL umožňuje pronikat branami firewall, protože většina bran firewall otevírá port TCP 443, který používá protokol SSL. SSTP je podporována pouze na zařízeních s Windows. Azure podporuje všechny verze Windows, které mají SSTP (Windows 7 a novější).

* IKEv2 VPN, řešení IPsec VPN založené na standardech. IKEv2 VPN je možné použít k připojení ze zařízení se systémem Mac (OSX verze 10.11 a vyšší).

* [OpenVPN](https://azure.microsoft.com/updates/openvpn-support-for-azure-vpn-gateways/)

Další informace:

* [Konfigurace připojení typu point-to-site k virtuální síti pomocí Powershellu](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-vpn"></a>Připojení místní sítě k virtuální síti pomocí sítě VPN

Můžete chtít připojit celý podnikové síti nebo jeho části, k virtuální síti. To je běžné v hybridním IT scénáře, kdy organizace [rozšířit jejich místní datacentra do Azure](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84). V mnoha případech hostovat organizace části služby v Azure a částí místní. Například může dělají, když řešení obsahuje front-end webové servery v Azure a místní databáze back-end. Tyto typy připojení "více míst" také nastavit správu Azure, které jsou umístěné prostředky lepší zabezpečení a aktivovat scénáře jako rozšíření řadiče domény služby Active Directory do Azure.

Jeden ze způsobů, jak provádět jde použít [site-to-site VPN](https://www.techopedia.com/definition/30747/site-to-site-vpn). Rozdíl mezi VPN typu site-to-site a point-to-site VPN je, že druhá možnost připojí jedno zařízení k virtuální síti. Síť site-to-site VPN připojuje k celé síti (například vaší místní sítě) k virtuální síti. VPN typu Site-to-site k virtuální síti pomocí vysoce zabezpečené režimu tunelového propojení IPsec protokol VPN.

Další informace:

* [Vytvořte virtuální síť Resource Manageru pomocí připojení VPN site-to-site pomocí webu Azure portal](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Plánování a návrh pro bránu VPN](../vpn-gateway/vpn-gateway-plan-design.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-dedicated-wan-link"></a>Připojení místní sítě k virtuální síti s vyhrazenou propojení WAN

Připojení VPN typu Point-to-site a site-to-site jsou platné pro povolení připojení mezi místními sítěmi. Ale některé organizace zvažte tak, aby měly těchto nevýhod:

* Připojení VPN typu přesuňte data přes internet. Tato třída zveřejňuje tato připojení na potenciální problémy se zabezpečením související s přesunem dat přes veřejnou síť. Kromě toho nemůže být zaručena spolehlivosti a dostupnosti pro připojení k Internetu.
* Připojení k síti VPN do virtuální sítě, nemusí mít šířky pásma u některých aplikací a účely, jako jsou mimo maximální na přibližně 200 MB/s.

Organizace, které obvykle potřebují nejvyšší úroveň zabezpečení a dostupnosti pro jejich připojení mezi různými místy pomocí vyhrazené linky WAN pro připojení k vzdálené lokality. Azure poskytuje možnost používat vyhrazené sítě WAN odkaz, který můžete použít k připojení místní sítě k virtuální síti. Azure ExpressRoute, globální dosah s přímým přístupem a Express route Express route povolit.

Další informace:

* [Technický přehled ExpressRoute](../expressroute/expressroute-introduction.md)
* [ExpressRoute s přímým přístupem](../expressroute/expressroute-erdirect-about.md)
* [Express route globálním dosahem](..//expressroute/expressroute-global-reach.md)

### <a name="connect-virtual-networks-to-each-other"></a>Propojení virtuálních sítí mezi sebou

Je možné použít řadu virtuálních sítí pro vaše nasazení. Existují různé důvody, proč to můžete udělat. Chcete zjednodušit správu, nebo může být vhodné zvýšit zabezpečení. Bez ohledu na to motivace pro uvedení prostředků v různých virtuálních sítích může nastat situace, kdy má být prostředky ve všech sítích se mezi sebou připojit.

Jednou z možností je pro služby v jedné virtuální sítě pro připojení ke službám v jiné virtuální síti s "opakování ve smyčce zpět" prostřednictvím Internetu. Připojení se spouští v jedné virtuální sítě prochází Internetu a potom se vrátí zpět do cílové virtuální sítě. Tato možnost poskytuje připojení k řešení problémů zabezpečení vyplývajících z libovolné internetové komunikaci.

Může být vhodnější vytvořit sítě site-to-site VPN, která se připojuje mezi dvěma virtuálními sítěmi. Tato metoda používá stejný [režimu tunelového propojení IPSec](https://technet.microsoft.com/library/cc786385.aspx) protokol jako připojení VPN site-to-site mezi různými místy uvedených výše.

Výhodou tohoto přístupu je, že připojení k síti VPN se navazuje prostřednictvím prostředků infrastruktury sítě Azure, namísto připojení přes internet. To poskytuje další vrstvu zabezpečení, ve srovnání s VPN typu site-to-site, která se připojují přes internet.

Další informace:

* [Konfigurace připojení typu VNet-to-VNet pomocí Azure Resource Manageru a Powershellu](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

Dalším způsobem, jak propojit virtuální sítě je [VNET peering](../virtual-network/virtual-network-peering-overview.md). Tato funkce umožňuje propojit dvě sítě Azure tak, aby komunikace mezi nimi se stane přes páteřní infrastrukturu Microsoftu, bez něj uvedením přes Internet. Partnerský vztah virtuální sítě můžete připojit dvěma virtuálními sítěmi ve stejné oblasti nebo dvěma virtuálními sítěmi v různých oblastech Azure. Skupiny zabezpečení sítě je možné omezit připojení mezi různé podsítě nebo systémy.

## <a name="availability"></a>Dostupnost

Dostupnost je klíčovou součástí každého programu zabezpečení. Pokud uživatelům a systémům nelze získat přístup k to, co chtějí získat přístup přes síť, službu lze považovat za dojde k ohrožení bezpečnosti. Azure nabízí síťové technologie, které podporují vysokou dostupnost následujících mechanismů:

* Vyrovnávání zatížení založený na protokolu HTTP
* Úrovně služby Vyrovnávání zatížení sítě
* Globální Vyrovnávání zatížení

Vyrovnávání zatížení je mechanismus, který je navržený tak, aby rovnoměrně rozmístit připojení mezi více zařízení. Cíle služby Vyrovnávání zatížení jsou:

* Chcete-li zvýšit dostupnost. Při načítání zůstatek připojení mezi různými zařízeními, jeden nebo více zařízení nemusí být k dispozici bez narušení služby. Služby spuštěné na zbývající online zařízení můžete dál obsluhovat obsah ze služby.
* Chcete-li zvýšit výkon. Při načítání zůstatek připojení mezi různými zařízeními jedno zařízení nemusí zpracovávat veškeré zpracování. Místo toho paměti a zpracování požadavků pro poskytování obsahu je rozdělena mezi více zařízení.

### <a name="http-based-load-balancing"></a>Vyrovnávání zatížení založený na protokolu HTTP

Organizace, které se spouštějí webové služby často žádoucí mít Vyrovnávání zatížení založený na HTTP před těchto webových služeb. To pomáhá zajistit odpovídající úroveň výkonu a vysoké dostupnosti. Nástroje pro vyrovnávání zatížení tradiční, založené na síti využívají síť a přenosu protokolů vrstvy. Nástroje pro vyrovnávání zatížení založený na protokolu HTTP, na druhé straně rozhodování na základě charakteristik protokolu HTTP.

Azure Application Gateway zajišťuje pro vaše webové služby Vyrovnávání zatížení založený na protokolu HTTP. Služba Application Gateway podporuje:

* Spřažení relace na základě souborů cookie. Tato funkce zajišťuje, že připojení na jeden ze serverů za tento nástroj pro vyrovnávání zatížení zůstane beze změny mezi klientem a serverem. Tím se zajistí stabilitu transakcí.
* Přesměrování zpracování SSL. Pokud se klient připojí pomocí nástroje pro vyrovnávání zatížení, relace se šifrují pomocí protokolu HTTPS (SSL). Za účelem zvýšení výkonu, ale můžete použít protokol HTTP (nešifrované) pro připojení mezi nástroje pro vyrovnávání zatížení a webový server za nástrojem pro vyrovnávání zatížení. To se označuje jako "Přesměrování zpracování SSL," protože webových serverů za nástrojem pro vyrovnávání zatížení není prostředí procesoru režie spojená s šifrováním. Webové servery může proto žádosti o služby rychleji.
* Adresa URL obsahu směrování na základě. Tato funkce umožňuje Vyrovnávání zatížení pro rozhodování o tam, kde na dopředné připojení založené na cílové adrese URL. To zajišťuje mnohem větší flexibilitu než řešení, která usnadňují načíst vyrovnávání rozhodování na základě IP adres.

Další informace:

* [Přehled služby Application Gateway](../application-gateway/application-gateway-introduction.md)

### <a name="network-level-load-balancing"></a>Úrovně služby Vyrovnávání zatížení sítě

Na rozdíl od založené na protokolu HTTP služby Vyrovnávání zatížení díky Vyrovnávání zatížení úrovně rozhodnutí založené na IP adresu a port (TCP nebo UDP) čísla.
Můžete získat výhody úrovně Vyrovnávání zatížení sítě v Azure pomocí Azure Load Balancer. Zahrnují několik klíčových charakteristik, nástroje pro vyrovnávání zatížení:

* Vyrovnávání zatížení úrovně sítě podle IP adresy a čísla portů.
* Podpora pro libovolný protokol aplikační vrstvy.
* Vyrovnává zatížení na virtuálních počítačích Azure a instance role cloudových služeb.
* Lze použít pro přístupem k Internetu (externího Vyrovnávání zatížení) i bez Internetu směřující virtuálních počítačů a aplikací (interní Vyrovnávání zatížení).
* Monitorování koncových bodů, které se používá k určení, pokud žádné služby za nástrojem pro vyrovnávání zatížení se staly není k dispozici.

Další informace:

* [Nástroj pro vyrovnávání zatížení přístupem k Internetu mezi několika virtuálními počítači nebo službami](../load-balancer/load-balancer-internet-overview.md)
* [Interní služby load balancer – přehled](../load-balancer/load-balancer-internal-overview.md)

### <a name="global-load-balancing"></a>Globální Vyrovnávání zatížení

Některé organizace má nejvyšší úroveň dostupnosti je to možné. Jedním ze způsobů dosažení tohoto cíle je k hostování aplikací v globálně distribuovaných datacentrech. Když je aplikace hostovaná v datových centrech rozmístěných po celém světě, je to možné celé geopolitické oblasti není dostupná, a ještě další aplikace a spuštěna.

Tato strategie Vyrovnávání zatížení můžete také k výraznému výkonu. Může směrovat požadavky na službu do datového centra, která je nejblíže zařízení, která odeslala žádost.

V Azure můžete získat výhody globální služby Vyrovnávání zatížení pomocí Azure Traffic Manageru.

Další informace:

* [Co je Traffic Manager?](../traffic-manager/traffic-manager-overview.md)

## <a name="name-resolution"></a>Překlad adres

Překlad názvů je důležité funkce pro všechny služby, které můžete hostovat v Azure. Z hlediska zabezpečení může způsobit ohrožení zabezpečení funkce překladu názvu útočník přesměrování požadavků z vašich serverů do lokality útočníka. Zabezpečené překlad představuje požadavek pro všechny služby hostované v cloudu.

Existují dva typy překladu adres, které je potřeba vyhovět:

* Interní překlad adres. Používá se pomocí služby na virtuálními sítěmi a místními sítěmi. Názvy použité pro interní překlad adres nejsou přístupné přes internet. Z důvodu optimálního zabezpečení je důležité, že schéma interní název řešení není dostupná pro externí uživatele.
* Externí název řešení. Používá se pomocí lidmi a zařízeními mimo vaší místní sítí a virtuální sítě. Jedná se o názvy, které jsou viditelné na Internetu a slouží k přímé připojení k vaší cloudové služby.

Pro interní překlad adres máte dvě možnosti:

* Server DNS virtuální sítě. Při vytváření nové virtuální sítě, DNS server se vytvoří za vás. Tento server DNS dokáže přeložit názvy počítačů umístěných ve virtuální síti. Tento server DNS není konfigurovatelné, spravuje správce prostředků infrastruktury Azure a proto vám může pomoct zabezpečit vaše řešení název řešení.
* Používání vlastního serveru DNS. Máte možnost uvedení DNS serveru podle vlastního výběru ve vaší virtuální síti. Tento server DNS může být, že služby Active Directory integrovali se DNS server, nebo na vyhrazené řešení server DNS poskytovaný partner Azure, které lze získat z webu Azure Marketplace.

Další informace:

* [Přehled služby Virtual network](../virtual-network/virtual-networks-overview.md)
* [Správa serverů DNS používaných virtuální sítě](../virtual-network/manage-virtual-network.md#change-dns-servers)

Pro externí překlad máte dvě možnosti:

* Hostujte vlastní externí DNS místní server.
* Hostování externím serverem DNS u poskytovatele služeb.

Mnoho organizací hostovat vlastní DNS servery místně. Protože mají síťové odborných znalostí a globální přítomnosti Uděláte to tak to dělají.

Ve většině případů je lepší pro hostování vaší služby překladu názvů DNS u poskytovatele služeb. Poskytovatelé služeb mají sítě odbornost a globální přítomnosti zajistit velmi vysokou dostupnost služeb název řešení. Dostupnost je nezbytné služby DNS, protože při selhání vaší služby rozlišení názvů nikdo se nebude moct kontaktovat vaše internetové služby.

Azure vám poskytne vysoce dostupné a vysoce výkonné externí řešení DNS ve formě Azure DNS. Toto řešení externí název řešení využívá výhod infrastruktury po celém světě Azure DNS. Umožňuje hostování domény v Azure, pomocí stejných přihlašovacích údajů, rozhraní API, nástrojů a fakturačních podmínek jako u ostatních služeb Azure. V rámci Azure se také dědí ovládací prvky silné zabezpečení integrované v platformě.

Další informace:

* [Přehled služby Azure DNS](../dns/dns-overview.md)
* [Privátní zóny Azure DNS](../dns/private-dns-overview.md) můžete konfigurovat privátní názvy DNS pro prostředky Azure, nikoli automaticky přiřazená názvy, aniž by bylo nutné přidat vlastního řešení DNS.

## <a name="perimeter-network-architecture"></a>Architektura hraniční sítě

Mnoho organizací použití hraničních sítích k segmentaci svých sítí a vytvořit zónu vyrovnávací paměť mezi Internetem a jejich služby. Hraniční části sítě se považuje za zónu s nízkou úrovní zabezpečení a žádné cenné prostředky jsou umístěné v daném segmentu sítě. Obvykle se zobrazí zařízení pro zabezpečení sítě, které mají síťové rozhraní v segmentu hraniční sítě. Jiné síťové rozhraní je připojen k síti, která obsahuje virtuální počítače a služby, které přijímají příchozí připojení z Internetu.

Můžete navrhnout hraniční sítě v několika různými způsoby. Rozhodnutí o nasazení do hraniční sítě a jaký druh hraniční sítě má použít, pokud se rozhodnete použít jednu, závisí na požadavcích zabezpečení sítě.

Další informace:

* [Cloudové služby společnosti Microsoft a zabezpečení sítě](../best-practices-network-security.md)

## <a name="azure-ddos-protection"></a>Azure DDoS Protection

Distribuované útoky na dostupnost služeb (DDoS) patří k největším hrozbám pro dostupnost a zabezpečení, se kterými se musejí zákazníci přesouvající aplikace do cloudu vyrovnávat. S útoky DDoS pokusí vyčerpání aplikační prostředky, čímž aplikaci není k dispozici pro oprávněné uživatele. Útoky DDoS můžou cílit na jakýkoli koncový bod, který je veřejně dostupný přes internet.
Microsoft poskytuje ochranu před útoky DDoS říká **základní** jako součást platformy Azure. Toto je zdarma a zahrnuje vždy na monitorování a v reálném čase ke zmírnění běžných útoků úrovně sítě. Kromě ochrany před útoky DDoS protection je součástí **základní** můžete povolit **standardní** možnost. DDoS Protection standardní funkce patří:

* **Integrace nativní platformy:** Nativně integrováno do Azure. Zahrnuje konfiguraci prostřednictvím webu Azure portal. Standardní před útoky DDoS Protection rozumí vašim prostředkům a konfiguraci prostředků.
* **Ochrana na klíč:** Zjednodušená konfigurace okamžitě chrání všechny prostředky ve virtuální síti jako standardní před útoky DDoS Protection je povolená. Vyžaduje se žádná definice zásahů nebo uživatele. Standardní před útoky DDoS Protection okamžitě a automaticky zmírní útok, jakmile se detekuje.
* **Monitorování se vždy provozu:** Vzory provozu vaší aplikace jsou monitorovány 24 hodin denně, 7 dní v týdnu, hledá indikátory útoky DDoS. Omezení rizik provádí při překročení zásady ochrany.
* **Sestavy omezení rizik útoků** sestavy omezení rizik útoků pomocí agregované sítě toku dat poskytují podrobné informace o útocích určenou pro vaše prostředky.
* **Protokoly toku omezení rizik útoků** protokoly toku omezení rizik útoků ke kontrole zhoršení provozu povolit přesměrovaný přenos a další data útoku v téměř reálném čase během aktivního útoku DDoS.
* **Adaptivní ladění:** Inteligentní přenos profilace učí o provozu vaší aplikace v průběhu času a vybere a aktualizuje profil, který je nejvhodnější pro vaši službu. Profil, který upravuje provoz mění v průběhu času. Vrstvy 3 ochrany vrstvy 7: Poskytuje ochranu před útoky DDoS plnohodnotných, při použití s firewallem webových aplikací.
* **Škálování rozsáhlé omezení rizik:** Více než 60 útoku různé typy můžete řešit s využitím globální kapacitu, pro ochranu před největší známé útoky DDoS.
* **Útok metriky:** Souhrnná metriky z každého útoku jsou přístupné prostřednictvím služby Azure Monitor.
* **Útok výstrahy:** Oznámení se dají konfigurovat na spouštění a zastavování útoků a na dobu trvání útok, pomocí integrované útoku metrik. Upozornění integrovat do provozní softwaru, například Microsoft Azure Log Analytics, Splunk, Azure Storage, e-mailu a na webu Azure portal.
* **Se zárukou nákladů:**  Přenos dat a aplikací horizontální navýšení kapacity kredity pro dokument útoky DDoS.
* **Před útoky DDoS rychlou odezvou** zákazníci s edicí Standard před útoky DDoS Protection teď mají přístup k týmu Rapid Response během aktivního útoku. Obsluhy DRR může pomoct s vyšetřování útoku, vlastní zmírnění rizik během útoku a analýzy po útoku.


Další informace:

* [Přehled služby Endpoint protection před útoky DDOS](../virtual-network/ddos-protection-overview.md)

## <a name="azure-front-door"></a>Branou Azure

Služba Azure branou vám umožňuje definovat, spravovat a monitorovat globální směrování webových přenosů. Optimalizuje směrování vašeho provozu pro zajištění nejlepšího výkonu a vysoké dostupnosti. Azure Front Door umožňuje vytvořit vlastní pravidla firewallu webových aplikací pro řízení přístupu za účelem ochrany úloh HTTP/HTTPS před zneužitím na základě klientských IP adres, kódů zemí a parametrů HTTP. Kromě toho branou také umožňuje vytvořit pravidla, která provozem škodlivých robotů zocelenou tady, snižování zátěže protokolu SSL a požadavku na HTTP/HTTPS, zpracování aplikační vrstvu.

Přední dveře platformě, jako takové je chráněn Azure DDoS Protection na úrovni Basic. Z důvodu další ochrany je možné ve virtuálních sítích povolit službu Azure DDoS Protection Standard a pomocí automatického ladění a zmírnění chránit prostředky před útoky na vrstvě sítě (TCP/UDP). Reverzní proxy server vrstvy 7 je vstupní branou, umožňuje pouze webového provozu předávání zpátky end servery a jiné typy přenosů blokovat ve výchozím nastavení.

Další informace:

* Další informace o celé sady Azure přední dveře možnosti můžete zkontrolovat [branou Azure – přehled](../frontdoor/front-door-overview.md)

## <a name="azure-traffic-manager"></a>Azure Traffic Manageru

Azure Traffic Manager je nástroj pro vyrovnávání zatížení provozu na základě DNS, který umožňuje optimálně distribuovat provoz do služeb napříč globálními oblastmi Azure při zajištění vysoké dostupnosti a rychlosti odezvy. Traffic Manager pomocí DNS směruje požadavky klientů do nejvhodnějšího koncového bodu služby v závislosti na metodě směrování provozu a stavu koncových bodů. Koncový bod je jakákoli internetová služba hostovaná v rámci nebo mimo Azure. Traffic manager monitoruje koncové body a není směrovat provoz do žádné koncové body, které jsou k dispozici.

Další informace:

* [Přehled Azure Traffic Manageru](../traffic-manager/traffic-manager-overview.md)

## <a name="monitoring-and-threat-detection"></a>Sledování a detekci hrozeb

Azure nabízí možnosti, které vám pomůžou v této oblasti klíče s včasnou detekci, monitorování a shromažďování a kontrola síťový provoz.

### <a name="azure-network-watcher"></a>Azure Network Watcher

Azure Network Watcher vám můžou pomoct vyřešit a poskytuje zcela novou sadu nástroje, které pomohou s identifikací problémy se zabezpečením.

[Zobrazení skupiny zabezpečení](../network-watcher/network-watcher-security-group-view-overview.md) pomáhá při zabezpečení a auditování dodržování předpisů virtuálních počítačů. Pomocí této funkce lze provádět prostřednictvím kódu programu audity, porovnání směrný plán zásad definovaných vaší organizací na efektivní pravidla pro každou z vašich virtuálních počítačů. To může pomoct identifikovat případné odchylky konfigurace.

[Zachytávání paketů](../network-watcher/network-watcher-packet-capture-overview.md) umožňuje zaznamenat síťový provoz do a z virtuálního počítače. Můžete shromažďovat statistiky sítě a řešení potíží s aplikací, které mohou být neocenitelnou pomocí při šetření neoprávněného vniknutí sítě. Tato funkce společně s Azure Functions můžete použít také spustit Zachytávání síťových v reakci na konkrétní upozornění Azure.

Další informace o Network Watcheru a jak spustit některé funkce testování ve cvičeních, naleznete v tématu [Přehled monitorování Azure network watcheru](../network-watcher/network-watcher-monitoring-overview.md).

>[!NOTE]
Nejaktuálnější oznámení o dostupnosti a stavu této služby najdete [stránku aktualizace Azure](https://azure.microsoft.com/updates/?product=network-watcher).

### <a name="azure-security-center"></a>Azure Security Center

Azure Security Center pomáhá zabránit, detekci a reakce na ně a nabízí zvýšenou viditelnost a kontrolu nad, zabezpečení vašich prostředků Azure. Poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných Azure, pomáhá zjišťovat hrozby, které jinak nevšimli a spolupracuje s rozsáhlou sadou řešení zabezpečení.

Security Center pomáhá optimalizaci a monitorování zabezpečení sítě podle:

* Poskytování doporučení zabezpečení sítě.
* Monitorování stavu konfigurace zabezpečení sítě.
* Upozorní vás k síti na základě hrozeb, i na úrovni koncového bodu a sítě.

Další informace:

* [Úvod do Azure Security Center](../security-center/security-center-intro.md)

### <a name="virtual-network-tap"></a>VTAP (Virtual Network TAP)

Virtuální síť Azure TAP (terminál přístupový bod) vám umožní průběžně stream vašeho virtuálního počítače síťový provoz do síťových paketů kolekcí nebo analytics nástroj. Kolekce nebo nástroj pro analýzu poskytuje partner síťové virtuální zařízení. Můžete použít stejné virtuální síti TAP prostředku k souhrnným přenosům z několika síťovými rozhraními v jednom nebo několika předplatných.

Další informace:

* [Klepněte na virtuální síť](../virtual-network/virtual-network-tap-overview.md)

### <a name="logging"></a>Protokolování

Protokolování na úrovni sítě je klíčová funkce pro jakýkoli scénář zabezpečení sítě. V Azure můžete protokolovat informace získané pro skupiny Nsg k síťové úroveň protokolování informací. Pomocí protokolování NSG, získat informace z:

* [Protokoly aktivit](../azure-monitor/platform/activity-logs-overview.md). Pomocí těchto protokolů můžete zobrazit všechny operace odeslání pro vaše předplatná Azure. Tyto protokoly jsou ve výchozím nastavení povolené a můžou používat na webu Azure portal. Dříve se používalo označení auditu nebo provozní protokoly.
* Protokoly událostí. Tyto protokoly poskytuje informace o tom, jaká pravidla skupiny zabezpečení sítě se používají.
* Čítač protokoly. Tyto protokoly umožňují zjistit, kolikrát se každé pravidlo skupiny zabezpečení sítě byla použita pro odepření nebo povolení provozu.

Můžete také použít [Microsoft Power BI](https://powerbi.microsoft.com/what-is-power-bi/), nástroj pro výkonné datové vizualizace, můžete zobrazit a analyzovat tyto protokoly.
Další informace:

* [Log Analytics pro skupiny zabezpečení sítě (Nsg)](../virtual-network/virtual-network-nsg-manage-log.md)
