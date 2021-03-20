---
title: Koncepce a požadavky zabezpečení sítě v Azure | Microsoft Docs
description: Tento článek obsahuje základní Vysvětlení konceptů a požadavků zabezpečení základní sítě a informace o tom, co Azure nabízí v každé z těchto oblastí.
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
ms.openlocfilehash: 4fb4981925593a22ed65df9d3ed0da5f45062f23
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100578935"
---
# <a name="azure-network-security-overview"></a>Přehled zabezpečení sítě v Azure

Zabezpečení sítě je možné definovat jako proces ochrany prostředků před neoprávněným přístupem nebo útokem pomocí ovládacích prvků pro síťový provoz. Cílem je zajistit, aby byl povolen pouze legitimní provoz. Azure zahrnuje robustní síťovou infrastrukturu pro podporu požadavků vaší aplikace a připojení služby. Mezi prostředky umístěnými v Azure, mezi místními a hostovanými prostředky Azure a a z Internetu a Azure se může připojit k síti.

Tento článek popisuje některé z možností, které Azure nabízí v oblasti zabezpečení sítě. Můžete se dozvědět:

* Sítě Azure
* Řízení přístupu k síti
* Azure Firewall
* Zabezpečený vzdálený přístup a připojení mezi místními sítěmi
* Dostupnost
* Překlad adres
* Architektura hraniční sítě (DMZ)
* Azure DDoS Protection
* Azure Front Door
* Traffic Manager
* Monitorování a detekce hrozeb

## <a name="azure-networking"></a>Sítě Azure

Azure vyžaduje, aby se virtuální počítače připojovaly k Virtual Network Azure. Virtuální síť je logická konstrukce, která je postavená na fyzických prostředcích infrastruktury sítě Azure. Každá virtuální síť je izolovaná od všech ostatních virtuálních sítí. To pomáhá zajistit, že síťový provoz ve vašich nasazeních není přístupný ostatním zákazníkům Azure.

Další informace:

* [Přehled virtuálních sítí](../../virtual-network/virtual-networks-overview.md)

## <a name="network-access-control"></a>Řízení přístupu k síti

Řízení přístupu k síti je výsledkem omezení připojení k určitým zařízením nebo podsítím v rámci virtuální sítě a z nich. Cílem řízení přístupu k síti je omezit přístup k virtuálním počítačům a službám na schválené uživatele a zařízení. Ovládací prvky přístupu jsou založené na rozhodnutích o povolení nebo zamítnutí připojení k virtuálnímu počítači nebo službě.

Azure podporuje několik typů řízení přístupu k síti, například:

* Řízení vrstvy sítě
* Řízení směrování a vynucené tunelování
* Zařízení zabezpečení virtuální sítě

### <a name="network-layer-control"></a>Řízení vrstvy sítě

Jakékoli zabezpečené nasazení vyžaduje určitou míru řízení přístupu k síti. Cílem řízení přístupu k síti je omezit komunikaci virtuálních počítačů s potřebnými systémy. Ostatní pokusy o komunikaci jsou blokované.

> [!NOTE]
> Brány firewall úložiště jsou uvedené v článku [Přehled zabezpečení služby Azure Storage](../../storage/blobs/security-recommendations.md) .

#### <a name="network-security-rules-nsgs"></a>Pravidla zabezpečení sítě (skupin zabezpečení sítě)

Pokud potřebujete základní řízení přístupu na úrovni sítě (na základě IP adresy a protokolů TCP nebo UDP), můžete použít skupiny zabezpečení sítě (skupin zabezpečení sítě). NSG je základní a stavová brána firewall pro filtrování paketů a umožňuje řídit přístup na základě [5 řazených kolekcí členů](https://www.techopedia.com/definition/28190/5-tuple). Skupin zabezpečení sítě zahrnují funkce pro zjednodušení správy a omezení pravděpodobnosti chyb konfigurace:

* **Rozšířená pravidla zabezpečení** zjednodušují definici pravidla NSG a umožňují vytvářet složitá pravidla, ale nemusíte vytvářet více jednoduchých pravidel, abyste dosáhli stejného výsledku.
* **Značky služby** jsou vytvořené Microsoftem štítky, které představují skupinu IP adres. Dynamicky se aktualizují tak, aby zahrnovaly rozsahy IP adres, které splňují podmínky definující zahrnutí do popisku. Pokud například chcete vytvořit pravidlo, které bude platit pro všechny služby Azure Storage na východní oblasti, můžete použít Storage. EastUS
* **Skupiny zabezpečení aplikací** umožňují nasadit prostředky do skupin aplikací a řídit přístup k těmto prostředkům vytvořením pravidel, která používají tyto skupiny aplikací. Například pokud máte nasazené webservers do skupiny aplikací webservers, můžete vytvořit pravidlo, které 443 povolí NSG provoz z Internetu do všech systémů ve skupině aplikací webservers.

Skupin zabezpečení sítě neposkytuje kontrolu aplikační vrstvy ani ověřování řízení přístupu.

Další informace:

* [Network Security Groups (Skupiny zabezpečení sítě)](../../virtual-network/network-security-groups-overview.md)

#### <a name="asc-just-in-time-vm-access"></a>Přístup k virtuálnímu počítači v čase ASC

[Azure Security Center](../../security-center/security-center-introduction.md) může spravovat skupin zabezpečení sítě na virtuálních počítačích a uzamknout přístup k virtuálnímu počítači, dokud uživatel s příslušnou rolí Azure na řízení přístupu na základě role [Azure RBAC](../../role-based-access-control/overview.md) nepožaduje přístup. Po úspěšném ověření uživatele ASC provede úpravy skupin zabezpečení sítě k povolení přístupu k vybraným portům po určenou dobu. Po vypršení časového limitu se skupin zabezpečení sítě obnoví na předchozí zabezpečený stav.

Další informace:

* [Přístup k Azure Security Center jenom v čase](../../security-center/security-center-just-in-time.md)

#### <a name="service-endpoints"></a>Koncové body služby

Koncové body služby představují jiný způsob, jak použít kontrolu nad přenosem dat. Komunikaci s podporovanými službami můžete omezit jenom na virtuální sítě přes přímé připojení. Provoz z vaší virtuální sítě do zadané služby Azure zůstane v páteřní síti Microsoft Azure.  

Další informace:

* [Koncové body služby](../../virtual-network/virtual-network-service-endpoints-overview.md#secure-azure-services-to-virtual-networks)

### <a name="route-control-and-forced-tunneling"></a>Řízení směrování a vynucené tunelování

Možnost řídit chování směrování ve virtuálních sítích je kritická. Pokud je směrování nesprávně nakonfigurované, můžou se aplikace a služby hostované ve vašem virtuálním počítači připojit k neautorizovaným zařízením, včetně systémů vlastněných a provozovaných potenciálními útočníky.

Síť Azure podporuje možnost přizpůsobení chování směrování pro síťový provoz ve vašich virtuálních sítích. To umožňuje změnit výchozí položky směrovací tabulky ve vaší virtuální síti. Řízení chování směrování vám pomůže zajistit, že veškerý provoz z určitého zařízení nebo skupiny zařízení zadá nebo opustí virtuální síť prostřednictvím konkrétního umístění.

Můžete mít například zařízení zabezpečení virtuální sítě ve vaší virtuální síti. Chcete se ujistit, že veškerý provoz do a z vaší virtuální sítě projde virtuálním zařízením zabezpečení. To můžete provést tak, že nakonfigurujete [trasy definované uživatelem](../../virtual-network/virtual-networks-udr-overview.md) (udr) v Azure.

[Vynucené tunelování](https://www.petri.com/azure-forced-tunneling) je mechanismus, který můžete použít k zajištění toho, aby vaše služby neumožňovaly iniciovat připojení k zařízením v Internetu. Všimněte si, že se liší od přijetí příchozích připojení a jejich reakce na ně. Webové servery front-end musí odpovídat na požadavky od hostitelů v Internetu, takže internetový internetový provoz je na tyto webové servery povolený, a webové servery můžou reagovat.

To, co nechcete povolit, je front-end webový server, který iniciuje odchozí požadavek. Takové žádosti mohou představovat bezpečnostní riziko, protože tato připojení lze použít ke stažení malwaru. I když chcete, aby tyto servery front-end zahájily odchozí požadavky na Internet, můžete vynutit, aby procházely prostřednictvím místních webových proxy serverů. Díky tomu můžete využít filtrování a protokolování adres URL.

Místo toho byste měli používat vynucené tunelování k tomu, aby se zabránilo. Když povolíte vynucené tunelování, všechna připojení k Internetu se vynutí prostřednictvím místní brány. Můžete nakonfigurovat vynucené tunelování tím, že využijete výhod udr.

Další informace:

* [Co jsou trasy definované uživatelem a předávání IP](../../virtual-network/virtual-networks-udr-overview.md)

### <a name="virtual-network-security-appliances"></a>Zařízení zabezpečení virtuální sítě

I když skupin zabezpečení sítě, udr a vynucené tunelování poskytují úroveň zabezpečení v síťové a transportní vrstvě [modelu OSI](https://en.wikipedia.org/wiki/OSI_model), možná budete chtít povolit zabezpečení i na vyšších úrovních než v síti.

Vaše požadavky na zabezpečení můžou například zahrnovat:

* Ověřování a autorizace před povolením přístupu k aplikaci
* Zjišťování vniknutí a reakce na neoprávněné vniknutí
* Kontrola aplikační vrstvy pro protokoly vysoké úrovně
* Filtrování adres URL
* Antivirová ochrana a antimalware na úrovni sítě
* Ochrana proti robotům
* Řízení přístupu k aplikaci
* Další ochrana DDoS (nad ochranou DDoS poskytovanou samotnými prostředky infrastruktury Azure)

K těmto rozšířeným funkcím zabezpečení sítě můžete přistupovat pomocí partnerského řešení Azure. Nejaktuálnější řešení zabezpečení pro Azure Partner Network najdete na [Azure Marketplace](https://azure.microsoft.com/marketplace/)a vyhledáte "zabezpečení" a "zabezpečení sítě".

## <a name="azure-firewall"></a>Azure Firewall

Azure Firewall je spravovaná cloudová služba síťového zabezpečení, která chrání vaše prostředky ve virtuálních sítích Azure. Jde o plně stavovou bránu firewall poskytovanou jako služba s integrovanou vysokou dostupností a neomezenou cloudovou škálovatelností. Mezi tyto funkce patří:

* Vysoká dostupnost
* Škálovatelnost cloudu
* Pravidla filtrování plně kvalifikovaných názvů domén aplikací
* Pravidla filtrování síťového provozu

Další informace:

* [Přehled Azure Firewall](../../firewall/overview.md)

## <a name="secure-remote-access-and-cross-premises-connectivity"></a>Zabezpečený vzdálený přístup a připojení mezi místními sítěmi

Instalaci, konfiguraci a správu prostředků Azure je potřeba provést vzdáleně. Kromě toho můžete chtít nasadit [hybridní řešení IT](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) , která mají komponenty v místním prostředí a ve veřejném cloudu Azure. Tyto scénáře vyžadují zabezpečený vzdálený přístup.

Sítě Azure podporují následující scénáře zabezpečeného vzdáleného přístupu:

* Připojení jednotlivých pracovních stanic k virtuální síti
* Připojení místní sítě k virtuální síti pomocí sítě VPN
* Připojení místní sítě k virtuální síti s vyhrazeným propojením WAN
* Vzájemná propojení virtuálních sítí

### <a name="connect-individual-workstations-to-a-virtual-network"></a>Připojení jednotlivých pracovních stanic k virtuální síti

Pro správu virtuálních počítačů a služeb v Azure můžete chtít povolit jednotlivé vývojáře nebo provozní pracovníky. Řekněme například, že potřebujete přístup k virtuálnímu počítači ve virtuální síti. Zásady zabezpečení ale nepovolují vzdálený přístup RDP nebo SSH k jednotlivým virtuálním počítačům. V takovém případě můžete použít připojení [VPN typu Point-to-site](../../vpn-gateway/point-to-site-about.md) .

Připojení VPN typu Point-to-site umožňuje nastavit privátní a zabezpečené připojení mezi uživatelem a virtuální sítí. Po navázání připojení VPN může uživatel na virtuálním počítači ve virtuální síti připojit protokol RDP nebo SSH přes připojení k síti VPN. (Předpokládá se, že se uživatel může ověřit a má oprávnění.) SÍŤ VPN typu Point-to-site podporuje:

* Protokol SSTP (Secure Socket Tunneling Protocol), což je proprietární protokol VPN založený na protokolu SSL. Řešení SSL VPN může proniknout do brány firewall, protože většina bran firewall otevírá port TCP 443, který používá protokol TLS/SSL. SSTP se podporuje jenom na zařízeních s Windows. Azure podporuje všechny verze Windows, které mají protokol SSTP (Windows 7 a novější).

* IKEv2 VPN, řešení IPsec VPN založené na standardech. IKEv2 VPN je možné použít k připojení ze zařízení se systémem Mac (OSX verze 10.11 a vyšší).

* [OpenVPN](https://azure.microsoft.com/updates/openvpn-support-for-azure-vpn-gateways/)

Další informace:

* [Konfigurace připojení typu Point-to-site k virtuální síti pomocí PowerShellu](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-vpn"></a>Připojení místní sítě k virtuální síti pomocí sítě VPN

Je možné, že budete chtít připojit celou podnikovou síť nebo její části k virtuální síti. To je běžné ve scénářích hybridního IT oddělení, kde organizace [šíří své místní datacentrum do Azure](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84). V mnoha případech organizace hostují části služby v Azure a místní součásti. To může například udělat, když řešení zahrnuje webové servery front-end v Azure a back-endové databáze v místním prostředí. Tyto typy připojení mezi různými místy také usnadňují správu prostředků v Azure s větším zabezpečením a umožňují scénáře, jako je rozšíření řadičů domény služby Active Directory do Azure.

Jedním ze způsobů, jak toho dosáhnout, je použít [síť VPN typu Site-to-site](https://www.techopedia.com/definition/30747/site-to-site-vpn). Rozdíl mezi sítí VPN typu Site-to-site a sítí VPN typu Point-to-site spočívá v tom, že druhá zařízení připojuje k virtuální síti jedno zařízení. Síť Site-to-Site VPN připojuje celou síť (například místní síť) k virtuální síti. Sítě VPN typu Site-to-site k virtuální síti používají vysoce zabezpečený protokol VPN tunelového režimu IPsec.

Další informace:

* [Vytvoření virtuální sítě Správce prostředků s připojením VPN typu Site-to-site pomocí Azure Portal](../../vpn-gateway/tutorial-site-to-site-portal.md)
* [Informace o službě VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-dedicated-wan-link"></a>Připojení místní sítě k virtuální síti s vyhrazeným propojením WAN

Připojení VPN typu Point-to-site a Site-to-site jsou platná pro povolení připojení mezi různými místy. Některé organizace je však považují za následující nevýhody:

* Připojení VPN přesouvá data přes Internet. To zveřejňuje tato připojení k potenciálním problémům se zabezpečením, které se týkají přesouvání dat přes veřejnou síť. Kromě toho není možné zaručit spolehlivost a dostupnost připojení k Internetu.
* Připojení VPN k virtuálním sítím nemusí mít šířku pásma pro některé aplikace a účely, protože mají maximální velikost od přibližně 200 MB/s.

Organizace, které potřebují nejvyšší úroveň zabezpečení a dostupnosti pro připojení mezi různými místy, obvykle používají vyhrazené odkazy sítě WAN pro připojení ke vzdáleným webům. Azure poskytuje možnost použít vyhrazené propojení WAN, které můžete použít k připojení místní sítě k virtuální síti. Tuto možnost umožňují globální dosah Azure ExpressRoute, Express Route Direct a Express Route.

Další informace:

* [Technický přehled ExpressRoute](../../expressroute/expressroute-introduction.md)
* [ExpressRoute Direct](../../expressroute/expressroute-erdirect-about.md)
* [Globální dosah Express Route](../../expressroute/expressroute-global-reach.md)

### <a name="connect-virtual-networks-to-each-other"></a>Vzájemná propojení virtuálních sítí

Pro vaše nasazení je možné použít spoustu virtuálních sítí. K dispozici jsou různé důvody, proč to může udělat. Může se stát, že budete chtít zjednodušit správu, nebo budete chtít zvýšené zabezpečení. Bez ohledu na motivaci pro vložení prostředků do různých virtuálních sítí může nastat situace, kdy chcete, aby se prostředky v každé z těchto sítí připojovaly k sobě.

Jednou z možností je, že služby v jedné virtuální síti se připojují ke službám v jiné virtuální síti – "opakováním" prostřednictvím Internetu. Připojení se spouští v jedné virtuální síti, prochází přes Internet a pak se vrátí do cílové virtuální sítě. Tato možnost zveřejňuje připojení k problémům se zabezpečením, které jsou součástí jakékoli internetové komunikace.

Lepší možností může být vytvoření sítě VPN typu Site-to-site, která se připojuje mezi dvěma virtuálními sítěmi. Tato metoda používá stejný protokol [tunelového režimu IPSec](/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) jako připojení VPN typu Site-to-site uvedená výše.

Výhodou tohoto přístupu je, že připojení VPN se naváže přes síťové prostředky infrastruktury Azure místo připojení přes Internet. V porovnání s sítěmi VPN typu Site-to-site, které se připojují přes Internet, získáte další vrstvu zabezpečení.

Další informace:

* [Konfigurace připojení typu VNet-to-VNet pomocí Azure Resource Manager a PowerShellu](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

Dalším způsobem připojení virtuálních sítí je vytvoření  [partnerského vztahu](../../virtual-network/virtual-network-peering-overview.md)virtuální sítě. Tato funkce umožňuje propojit dvě sítě Azure, aby komunikace mezi nimi byla přes páteřní infrastrukturu Microsoftu, aniž by bylo možné přejít přes Internet. Partnerský vztah virtuálních sítí může propojit dvě virtuální sítě v rámci stejné oblasti nebo dvou virtuální sítě napříč oblastmi Azure. Skupin zabezpečení sítě se dá použít k omezení připojení mezi různými podsítěmi nebo systémy.

## <a name="availability"></a>Dostupnost

Dostupnost je klíčovou součástí jakéhokoli programu zabezpečení. Pokud vaši uživatelé a systémy nemají přístup k tomu, co potřebují k přístupu přes síť, může být služba považována za ohroženou. Azure obsahuje síťové technologie, které podporují následující mechanismy vysoké dostupnosti:

* Vyrovnávání zatížení založené na protokolu HTTP
* Vyrovnávání zatížení na úrovni sítě
* Globální vyrovnávání zatížení

Vyrovnávání zatížení je mechanismus navržený tak, aby bylo možné rovnoměrně rozdělovat připojení mezi více zařízení. Cílem vyrovnávání zatížení jsou:

* Pro zvýšení dostupnosti. Při vyrovnávání zatížení připojení mezi různými zařízeními může být jedno nebo více zařízení nedostupné bez narušení služby. Služby běžící ve zbývajících online zařízeních mohou nadále obsluhovat obsah služby.
* Pro zvýšení výkonu. Když vyrovnáváte zatížení připojení napříč více zařízeními, nemusíte mít jedno zařízení zpracovávat veškeré zpracování. Místo toho jsou požadavky na zpracování a paměť pro obsluhu obsahu rozloženy mezi několik zařízení.

### <a name="http-based-load-balancing"></a>Vyrovnávání zatížení založené na protokolu HTTP

Organizace, na kterých běží webové služby, často chtějí před těmito webovými službami používat nástroj pro vyrovnávání zatížení založený na protokolu HTTP. To pomáhá zajistit odpovídající úroveň výkonu a vysokou dostupnost. Tradiční nástroje pro vyrovnávání zatížení založené na síti spoléhají na protokoly sítě a transportní vrstvy. Nástroje pro vyrovnávání zatížení založené na protokolu HTTP na druhé straně se rozhodnou na základě charakteristik protokolu HTTP.

Azure Application Gateway poskytuje vyrovnávání zatížení na základě protokolu HTTP pro vaše webové služby. Application Gateway podporuje:

* Spřažení relace na základě souborů cookie. Tato možnost zajistí, že připojení navázaná na jeden ze serverů za tímto nástrojem pro vyrovnávání zatížení zůstanou beze změny mezi klientem a serverem. Tím se zajistí stabilita transakcí.
* Přesměrování zpracování TLS. Když se klient připojí pomocí nástroje pro vyrovnávání zatížení, tato relace se zašifruje pomocí protokolu HTTPS (TLS). Aby bylo možné zvýšit výkon, můžete použít protokol HTTP (nešifrovaný) pro připojení mezi nástrojem pro vyrovnávání zatížení a webovým serverem za nástrojem pro vyrovnávání zatížení. To se označuje jako "přesměrování zpracování TLS", protože webové servery za nástrojem pro vyrovnávání zatížení nezpůsobí režii procesoru související se šifrováním. Webové servery proto mohou rychleji vycházet žádosti o služby.
* Směrování obsahu na základě adresy URL. Tato funkce umožňuje nástroji pro vyrovnávání zatížení rozhodnout, kam se mají dodávat připojení na základě cílové adresy URL. Díky tomu je větší flexibilita než řešení, která na základě IP adres provádí rozhodnutí o vyrovnávání zatížení.

Další informace:

* [Přehled služby Application Gateway](../../application-gateway/overview.md)

### <a name="network-level-load-balancing"></a>Vyrovnávání zatížení na úrovni sítě

Na rozdíl od vyrovnávání zatížení založeného na protokolu HTTP umožňuje vyrovnávání zatížení na úrovni sítě rozhodnutí založená na číslech IP adresy a portu (TCP nebo UDP).
Výhody vyrovnávání zatížení sítě v Azure můžete získat pomocí Azure Load Balancer. Mezi klíčové charakteristiky Load Balancer patří:

* Vyrovnávání zatížení na úrovni sítě založené na IP adrese a číslech portů.
* Podpora jakéhokoli protokolu vrstvy aplikace.
* Načtěte si rovnováhu mezi virtuálními počítači Azure a instancemi rolí cloudových služeb.
* Dá se použít pro internetovou (externí vyrovnávání zatížení) i pro aplikace a virtuální počítače, které nejsou v Internetu (interní vyrovnávání zatížení).
* Monitorování koncového bodu, které slouží k určení, jestli některé ze služeb za nástroj pro vyrovnávání zatížení nebudou dostupné.

Další informace:

* [Internetový nástroj pro vyrovnávání zatížení mezi několika virtuálními počítači nebo službami](../../load-balancer/load-balancer-overview.md)
* [Interní přehled nástroje pro vyrovnávání zatížení](../../load-balancer/load-balancer-overview.md)

### <a name="global-load-balancing"></a>Globální vyrovnávání zatížení

Některé organizace chtějí nejvyšší možnou úroveň dostupnosti. Jedním ze způsobů, jak dosáhnout tohoto cíle, je hostovat aplikace v globálně distribuovaných datových centrech. Když je aplikace hostována v datových centrech umístěných na celém světě, je možné, že celá geopolitická oblast nebude k dispozici a stále je aplikace spuštěná.

Tato strategie vyrovnávání zatížení může také přinést výhody výkonu. Požadavky na službu můžete směrovat do datového centra, které je nejblíže zařízení, které tuto žádost vytváří.

V Azure můžete získat výhody globálního vyrovnávání zatížení s využitím Azure Traffic Manager.

Další informace:

* [Co je Traffic Manager?](../../traffic-manager/traffic-manager-overview.md)

## <a name="name-resolution"></a>Překlad adres

Překlad názvů je kritická funkce pro všechny služby, které hostuje v Azure. Z hlediska zabezpečení může zabezpečení funkce překladu IP adres vést k přesměrování požadavků od vašich webů na web útočníka. Zabezpečené rozlišení názvů je požadavek pro všechny služby hostované v cloudu.

Existují dva typy překladu IP adres, které potřebujete:

* Interní překlad názvů. Tato služba je používána službami ve vašich virtuálních sítích, v místních sítích nebo v obou. Názvy používané k internímu překladu názvů nejsou přístupné přes Internet. Pro zajištění optimálního zabezpečení je důležité, aby vaše interní schéma překladu názvů nebylo přístupné pro externí uživatele.
* Překlad externích názvů. Používají je lidé a zařízení mimo vaše místní sítě a virtuální sítě. Jedná se o názvy, které jsou viditelné pro Internet a slouží k přímému připojení k vašim cloudovým službám.

V případě překladu interních názvů máte dvě možnosti:

* Server DNS virtuální sítě. Při vytváření nové virtuální sítě se vytvoří server DNS. Tento server DNS může přeložit názvy počítačů umístěných v této virtuální síti. Tento server DNS není možné konfigurovat, je spravovaný správcem prostředků infrastruktury Azure a může vám tak přispět k zabezpečení řešení překladu názvů.
* Přineste si vlastní server DNS. Máte možnost umístit si vlastní server DNS, který zvolíte ve své virtuální síti. Tento server DNS může být integrovaným serverem DNS služby Active Directory nebo vyhrazeným řešením serveru DNS poskytovaným partnerem Azure, který můžete získat z Azure Marketplace.

Další informace:

* [Přehled virtuálních sítí](../../virtual-network/virtual-networks-overview.md)
* [Správa serverů DNS používaných virtuální sítí](../../virtual-network/manage-virtual-network.md#change-dns-servers)

Pro překlad externích názvů máte dvě možnosti:

* Hostování vlastního externího serveru DNS v místním prostředí.
* Hostování vlastního externího serveru DNS u poskytovatele služeb

Mnoho velkých organizací hostuje vlastní servery DNS místně. Můžou to udělat, protože mají síťové znalosti a globální přítomnost.

Ve většině případů je lepší hostovat služby překladu názvů DNS s poskytovatelem služeb. Tito poskytovatelé služeb mají znalosti sítě a globální přítomnost, aby zajistili vysokou dostupnost služeb překladu názvů. Dostupnost je pro služby DNS zásadní, protože pokud vaše služby překladu IP adres selžou, nebude mít nikdo přístup k vašim internetovým službám.

Azure poskytuje vysoce dostupné a vysoce výkonné externí řešení DNS ve formě Azure DNS. Toto řešení překladu externích názvů využívá celosvětovou infrastrukturu Azure DNS. Umožňuje hostovat vaši doménu v Azure pomocí stejných přihlašovacích údajů, rozhraní API, nástrojů a fakturace jako jiné služby Azure. V rámci Azure zahrnuje také silné ovládací prvky zabezpečení, které jsou součástí platformy.

Další informace:

* [Přehled Azure DNS](../../dns/dns-overview.md)
* [Azure DNS privátní zóny](../../dns/private-dns-overview.md) vám umožní nakonfigurovat privátní názvy DNS pro prostředky Azure namísto automaticky přiřazených názvů bez nutnosti přidat vlastní řešení DNS.

## <a name="perimeter-network-architecture"></a>Architektura hraniční sítě

Řada velkých organizací používá hraniční sítě k segmentaci svých sítí a vytváří zónu vyrovnávací paměti mezi Internetem a jejich službami. Hraniční část sítě se považuje za zónu s nízkým zabezpečením a v tomto segmentu sítě se neumísťují žádné prostředky s vysokou hodnotou. Obvykle se zobrazí zařízení zabezpečení sítě, která mají síťové rozhraní v segmentu hraniční sítě. Jiné síťové rozhraní je připojeno k síti s virtuálními počítači a službami, které přijímají příchozí připojení z Internetu.

Hraniční sítě můžete navrhovat mnoha různými způsoby. Rozhodnutí o nasazení hraniční sítě a následný typ hraniční sítě, která se má použít, pokud se rozhodnete použít jeden, závisí na požadavcích na zabezpečení sítě.

Další informace:

* [Microsoft Cloud služeb a zabezpečení sítě](network-best-practices.md)

## <a name="azure-ddos-protection"></a>Azure DDoS Protection

Distribuované útoky na dostupnost služeb (DDoS) patří k největším hrozbám pro dostupnost a zabezpečení, se kterými se musejí zákazníci přesouvající aplikace do cloudu vyrovnávat. Útok DDoS se pokusí vyčerpat prostředky aplikace, takže aplikace nebude k dispozici oprávněným uživatelům. Útoky DDoS můžou cílit na jakýkoli koncový bod, který je veřejně dostupný přes internet.
Společnost Microsoft poskytuje jako součást platformy Azure DDoS Protection označované jako **Basic** . K dispozici je zdarma a zahrnuje nepřetržité monitorování a zmírnění běžných útoků na úrovni sítě v reálném čase. Kromě ochrany zahrnuté v DDoS Protection **Basic** můžete povolit **standardní** možnost. Mezi DDoS Protection standardní funkce patří:

* **Integrace nativní platformy:** Nativně integrovaná do Azure. Zahrnuje konfiguraci prostřednictvím Azure Portal. DDoS Protection Standard rozumí vašim prostředkům a konfiguraci prostředků.
* **Zapnout ochranu klíče:** Zjednodušená konfigurace okamžitě chrání všechny prostředky ve virtuální síti, jakmile je povolená DDoS Protection Standard. Není nutná žádná definice ani zásah uživatele. DDoS Protection Standard okamžitě a automaticky zmírnit útok, jakmile se zjistí.
* **Monitorování nepřetržitého provozu:** Vaše vzory přenosů aplikací jsou sledovány 24 hodin denně, 7 dní v týdnu a hledají ukazatele DDoSch útoků. Při překročení zásad ochrany dochází ke zmírnění rizik.
* **Sestavy o zmírnění útoků** Sestavy zmírnění útoků používají agregovaná data toku sítě a poskytují podrobné informace o útocích cílících na vaše prostředky.
* **Protokoly o omezeních toků útoků na útoky** Protokoly o omezeních toků útoků na útoky umožňují kontrolovat vyřazený provoz, předávané přenosy a další data útoku téměř v reálném čase během útoku na aktivní DDoS.
* **Adaptivní ladění:** Inteligentní profilace provozu zjišťuje provoz vaší aplikace v průběhu času a vybere a aktualizuje profil, který je nejvhodnější pro vaši službu. Profil se v průběhu času upraví jako změny provozu. Ochrana vrstvy 3 ke vrstvě 7: poskytuje úplnou ochranu zásobníku DDoS při použití s bránou firewall webových aplikací.
* **Rozsáhlá škála rizik:** U více než 60 různých typů útoků se dá zmírnit s globální kapacitou, která se bude chránit před největším známými DDoS útoky.
* **Metriky útoku:** Souhrnná metrika z každého útoku je přístupná prostřednictvím Azure Monitor.
* **Výstrahy útoku:** Výstrahy se dají nakonfigurovat na začátku a na konci útoku a přes dobu trvání útoku pomocí integrované metriky útoku. Výstrahy se integrují do svého provozního softwaru, jako je Microsoft Azure monitorování protokolů, Splunk, Azure Storage, e-mailu a Azure Portal.
* **Záruka nákladů:**  Kredity služeb pro přenos dat a škálování aplikací pro dokumentované DDoS útoky.
* **DDoS rychle reagovat** Zákazníci s DDoS Protection Standard teď mají při aktivním útoku přístup k týmu Rapid Response. DRR může pomáhat s vyšetřováním útoků, vlastními riziky při útoku a analýzou po útoku.


Další informace:

* [Přehled ochrany DDOS](../../ddos-protection/ddos-protection-overview.md)

## <a name="azure-front-door"></a>Azure Front Door

Služba front-dveří Azure umožňuje definovat, spravovat a monitorovat globální směrování webového provozu. Optimalizuje směrování provozu pro zajištění nejlepšího výkonu a vysoké dostupnosti. Azure Front Door umožňuje vytvořit vlastní pravidla firewallu webových aplikací pro řízení přístupu za účelem ochrany úloh HTTP/HTTPS před zneužitím na základě klientských IP adres, kódů zemí a parametrů HTTP. Kromě toho vám i přední dvířka umožňují vytvořit si pravidla omezující omezení pro vytvoření škodlivého provozu robota, včetně snižování zátěže TLS a požadavků na protokol HTTP/HTTPS, zpracování aplikační vrstvy.

Samotná platforma Front Door je chráněná službou Azure DDoS Protection Basic. Z důvodu další ochrany je možné ve virtuálních sítích povolit službu Azure DDoS Protection Standard a pomocí automatického ladění a zmírnění chránit prostředky před útoky na vrstvě sítě (TCP/UDP). Přední dvířka jsou reverzní proxy vrstvy 7, ale umožňuje průchod webovým serverem přes back-end servery a blokování dalších typů provozu ve výchozím nastavení.

Další informace:

* Další informace o celé sadě možností front-end pro Azure najdete v článku Přehled služby [front-end pro Azure](../../frontdoor/front-door-overview.md) .

## <a name="azure-traffic-manager"></a>Azure Traffic Manager

Azure Traffic Manager je nástroj pro vyrovnávání zatížení provozu na základě DNS, který umožňuje optimálně distribuovat provoz do služeb napříč globálními oblastmi Azure při zajištění vysoké dostupnosti a rychlosti odezvy. Traffic Manager pomocí DNS směruje požadavky klientů do nejvhodnějšího koncového bodu služby v závislosti na metodě směrování provozu a stavu koncových bodů. Koncový bod je jakákoli internetová služba hostovaná v rámci nebo mimo Azure. Traffic Manager monitoruje koncové body a nesměruje provoz do žádných koncových bodů, které nejsou k dispozici.

Další informace:

* [Přehled služby Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md)

## <a name="monitoring-and-threat-detection"></a>Monitorování a detekce hrozeb

Azure poskytuje možnosti, které vám pomůžou v této klíčové oblasti s včasnou detekcí, monitorováním a shromažďováním a kontrolou síťového provozu.

### <a name="azure-network-watcher"></a>Azure Network Watcher

Azure Network Watcher vám může pomoct při odstraňování potíží a poskytuje celou novou sadu nástrojů, které vám pomůžou s identifikací problémů se zabezpečením.

[Zobrazení skupiny zabezpečení](../../network-watcher/network-watcher-security-group-view-overview.md) pomáhá s dodržováním zásad auditování a zabezpečení Virtual Machines. Pomocí této funkce můžete provádět programové audity a porovnat základní zásady definované vaší organizací s platnými pravidly pro každý z vašich virtuálních počítačů. To vám může přispět k identifikaci posunu konfigurace.

[Zachycení paketů](../../network-watcher/network-watcher-packet-capture-overview.md) umožňuje zachytit síťový provoz do a z virtuálního počítače. Můžete shromažďovat statistiky sítě a řešit problémy s aplikacemi, které mohou být neužitečné při vyšetřování neoprávněných vniknutí do sítě. Tuto funkci můžete také použít spolu s Azure Functions ke spuštění síťových zachycení v reakci na konkrétní výstrahy Azure.

Další informace o Network Watcher a o tom, jak začít s testováním některých funkcí v laboratoři, najdete v tématu [Přehled monitorování sledování sítě Azure](../../network-watcher/network-watcher-monitoring-overview.md).

> [!NOTE]
> Nejaktuálnější oznámení o dostupnosti a stavu této služby najdete na [stránce s aktualizacemi Azure](https://azure.microsoft.com/updates/?product=network-watcher).

### <a name="azure-security-center"></a>Azure Security Center

Azure Security Center pomáhá předcházet hrozbám, zjišťovat je a reagovat na ně a poskytuje lepší přehled o zabezpečení prostředků Azure a kontrolu nad nimi. Poskytuje integrované monitorování zabezpečení a správu zásad v rámci předplatných Azure, pomáhá detekovat hrozby, které by jinak neinformovaly a fungují s velkou sadou řešení zabezpečení.

Security Center vám pomůže optimalizovat a monitorovat zabezpečení sítě pomocí:

* Poskytnutí doporučení zabezpečení sítě.
* Monitorování stavu konfigurace zabezpečení sítě.
* Upozornění na síťové hrozby na úrovni koncového bodu i sítě.

Další informace:

* [Úvod do Azure Security Center](../../security-center/security-center-introduction.md)

### <a name="virtual-network-tap"></a>Virtual Network klepněte na

KLEPNUTÍ na virtuální síť Azure (terminálový přístupový bod) umožňuje nepřetržitě streamovat síťový provoz virtuálního počítače do nástroje pro shromažďování síťových paketů nebo pro analýzu. Sběrač nebo nástroj pro analýzu poskytuje síťový partner pro síťové virtuální zařízení. K agregaci provozu z více síťových rozhraní ve stejných nebo různých předplatných můžete použít stejnou virtuální síť. klepněte na prostředek.

Další informace:

* [Naslouchací zařízení virtuální sítě](../../virtual-network/virtual-network-tap-overview.md)

### <a name="logging"></a>protokolování

Protokolování na úrovni sítě je klíčovou funkcí pro jakýkoli scénář zabezpečení sítě. V Azure můžete protokolovat informace získané pro skupin zabezpečení sítě a získat informace o protokolování na úrovni sítě. Pomocí protokolování NSG získáte informace z těchto údajů:

* [Protokoly aktivit](../../azure-monitor/essentials/platform-logs-overview.md). Pomocí těchto protokolů můžete zobrazit všechny operace odeslané do předplatných Azure. Tyto protokoly jsou ve výchozím nastavení povolené a dají se použít v rámci Azure Portal. Dříve se nazývaly jako audit nebo provozní protokoly.
* Protokoly událostí. Tyto protokoly obsahují informace o tom, jaká pravidla NSG byla použita.
* Protokoly čítače. Tyto protokoly vám umožní zjistit, kolikrát se každé pravidlo NSG použilo pro odepření nebo povolení provozu.

K zobrazení a analýze těchto protokolů můžete použít také [Microsoft Power BI](https://powerbi.microsoft.com/what-is-power-bi/), výkonný nástroj pro vizualizaci dat.
Další informace:

* [Protokoly Azure Monitor pro skupiny zabezpečení sítě (skupin zabezpečení sítě)](../../virtual-network/virtual-network-nsg-manage-log.md)