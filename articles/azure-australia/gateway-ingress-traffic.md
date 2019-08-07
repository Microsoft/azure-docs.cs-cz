---
title: Řízení příchozího provozu v Azure Austrálie
description: Příručka pro řízení provozu příchozích dat v Azure Austrálie, aby splňovala požadavky australské vlády na zabezpečení internetových bran
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 3885b9fa346047a50a49c7b2f9b96b6a8f95e51f
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779355"
---
# <a name="controlling-ingress-traffic-in-azure-australia"></a>Řízení příchozího provozu v Azure Austrálie

Základním prvkem zabezpečení systémů ICT je řízení síťového provozu. Provoz by měl být omezený jenom na to, aby systém mohl fungovat tak, aby snížil riziko ohrožení zabezpečení.

Tato příručka obsahuje podrobné informace o tom, jak příchozí (příchozí) přenos dat funguje v rámci Azure, a doporučení pro implementaci ovládacích prvků zabezpečení sítě pro systém připojený k Internetu.

Ovládací prvky sítě se zarovnají s centrem ACSC (Austrálie Internet Security Center) a záměrem ACSC (ISM) pro zabezpečení informací.

## <a name="requirements"></a>Požadavky

Celkové požadavky na zabezpečení pro systémy Společenství jsou definované v rámci ISM. Pro pomoc subjektům Společenství při implementaci zabezpečení sítě acsc zveřejnil [acsc ochranu: Implementace segmentace a oddělení](https://www.acsc.gov.au/publications/protect/network_segmentation_segregation.htm)sítě a pomoc s zabezpečením systémů v cloudových prostředích acsc zveřejnila [zabezpečení cloud computingu pro klienty](https://www.acsc.gov.au/publications/protect/Cloud_Computing_Security_for_Tenants.pdf).

Tyto příručky popisují kontext implementace zabezpečení sítě a řízení provozu a zahrnují praktická doporučení pro návrh a konfiguraci sítě.

Příručka Microsoft [Cloud Computing Security for tenantes Microsoft Azure](https://aka.ms/au-irap) Guide na stránce Austrálie portálu pro vztahy důvěryhodnosti služby zvýrazňuje konkrétní technologie Microsoftu, které vám umožní splnit Rady v publikacích acsc.

Následující klíčové požadavky, které jsou uvedené v publikacích ACSC, jsou důležité pro řízení příchozího provozu v Azure:

|Popis|Source|
|---|---|
|**Implementujte segmentaci sítě a oddělení, například n-vrstvou architekturu, pomocí bran firewall na hostiteli a řízení přístupu k síti CSP, abyste omezili příchozí a odchozí připojení k síti virtuálních počítačů jenom na požadované porty a protokoly.**| _Cloud Computing pro klienty_|
|**Implementujte dostatečně velkou šířku pásma, nízkou latenci a spolehlivé připojení k síti** mezi klientem (včetně vzdálených uživatelů tenanta) a cloudovou službou, aby splňovala požadavky na dostupnost tenanta.  | _Cloud Computing pro klienty_|
|**Použijte technologie více než jenom síťové vrstvy**. Každý hostitel a síť by měly být rozdělené a oddělené, pokud je to možné, na nejnižší úrovni, která může být prakticky spravovaná. Ve většině případů se segmentace a oddělení aplikují z vrstvy datové vazby až do a včetně vrstvy aplikace. v citlivých prostředích ale může být vhodná fyzická izolace. Míry založené na hostiteli a síti by se měly nasadit doplňkovým způsobem a být centrálně monitorovány. Použití brány firewall nebo bezpečnostního zařízení jako jediného bezpečnostního opatření není dostatečné. |_ACSC chránit: Implementace segmentace a oddělení sítě_|
|**Používejte zásady s nejnižšími oprávněními a potřebnou Ki k znát**. Pokud hostitel, služba nebo síť nemusí komunikovat s jiným hostitelem, službou nebo sítí, neměl by být povolený. Pokud musí hostitel, služba nebo síť komunikovat jenom s jiným hostitelem, službou nebo sítí pomocí konkrétních portů nebo protokolů, měly by být zakázané všechny ostatní porty nebo protokoly. Přijetí těchto zásad napříč sítí doplní minimalizaci uživatelských oprávnění a významně zvýší celkové stav zabezpečení prostředí. |_ACSC chránit: Implementace segmentace a oddělení sítě_|
|**Samostatné hostitele a sítě založené na jejich citlivosti nebo nekritickém provozu pro obchodní operace**. Oddělení je možné dosáhnout použitím různých hardwarových nebo platforem v závislosti na různých klasifikacích zabezpečení, domén zabezpečení nebo požadavcích na dostupnost a integritu pro určité hostitele nebo sítě. Konkrétně samostatné sítě pro správu a zvažte fyzicky izolaci sítí pro vzdálenou správu pro citlivá prostředí. |_ACSC chránit: Implementace segmentace a oddělení sítě_|
|**Identifikujte, ověřují a schvalujte přístup všem entitám ke všem ostatním entitám**. Všichni uživatelé, hostitelé a služby by měli mít přístup omezený jenom na jiné uživatele, hostitele a služby, které jsou nutné k tomu, aby vyjmenovaly příslušné povinnosti nebo funkce. Všechny starší nebo místní služby, které obcházejí nebo downgradují sílu totožnosti, ověřování a služby autorizace, by měly být zakázány a jejich použití by mělo být pečlivě sledováno. |_ACSC chránit: Implementace segmentace a oddělení sítě_|
|**Implementujte možnost Povolit výpis síťových přenosů místo seznamu odepření**. Místo odepření přístupu ke známému špatnému síťovému provozu (třeba blokování konkrétní adresy nebo služby) povolte přístup jenom pro známý dobrý síťový provoz (tzn. který je identifikovaný, ověřený a autorizovaný). Seznam povolených výsledků má vynikající zásady zabezpečení, které je možné vyhodnotit, a významně zlepšuje kapacitu organizace pro detekci a vyhodnocení potenciálních vniknutí do sítě. |_ACSC chránit: Implementace segmentace a oddělení sítě_|
|

Tento článek poskytuje informace a doporučení týkající se těchto požadavků pro systémy nasazené v Azure s využitím infrastruktury jako služby (IaaS) a platformy jako služby (PaaS). Měli byste si také přečíst článek o [řízení odchozího provozu v Austrálii Azure](gateway-egress-traffic.md) , abyste plně pochopili řízení síťového provozu v rámci Azure.

## <a name="architecture"></a>Architektura

Pokud se chystáte o návrh nebo implementaci řízení síťového zabezpečení a přenosů dat do provozu, musíte nejdřív pochopit, jak funguje příchozí přenos dat v rámci Azure napříč IaaS i PaaS. V této části najdete přehled možných vstupních bodů, ve kterých by síťový provoz mohl dosáhnout prostředku hostovaného v Azure, a kontrol zabezpečení, které jsou k dispozici pro omezení a řízení provozu. Každá z těchto součástí je podrobně popsána ve zbývajících částech tohoto průvodce.

### <a name="architecture-components"></a>Komponenty architektury

Diagram architektury zobrazený tady znázorňuje možné cesty, které může síťový provoz trvat pro připojení ke službě hostované v Azure. Tyto součásti jsou rozdělené do Azure, IaaS příchozího přenosu dat PaaS a řízení zabezpečení v závislosti na funkci, kterou poskytují pro přenos příchozího přenosu dat.

![Architektura](media/ingress-traffic.png)

### <a name="azure-components"></a>Součásti Azure

|Komponenta | Popis|
|---|---|
|**DDoS Protection** | Služba DDoS (Distributed DOS) se pokouší vyčerpat prostředky aplikace, takže aplikace nebude k dispozici oprávněným uživatelům. Útoky DDoS můžou cílit na jakýkoli koncový bod, který je veřejně dostupný přes internet. Azure zahrnuje DDoS Protection automaticky prostřednictvím platformy Azure a poskytuje další funkce pro zmírnění rizik, které je možné povolit pro konkrétní aplikace a podrobnější kontrolu.|
| **Traffic Manager** | Azure Traffic Manager je nástroj pro vyrovnávání zatížení založený na systému DNS (Domain Name System), který může pro optimální distribuci provozu do služeb napříč oblastmi Azure a současně poskytovat vysokou dostupnost a rychlost odezvy. Traffic Manager používá DNS k přímému směrování požadavků klientů na nejvhodnější koncový bod na základě metody směrování provozu a stavu koncových bodů.|
| **ExpressRoute** | ExpressRoute je vyhrazené síťové připojení pro využívání cloudových služeb Microsoftu. Je zajištěna prostřednictvím poskytovatele připojení a nabízí spolehlivější, rychlejší rychlost, nižší latenci a vyšší zabezpečení než typická připojení přes Internet. Okruh ExpressRoute představuje logické propojení mezi místní infrastrukturou a cloudovou službou Microsoftu prostřednictvím poskytovatele připojení.|
| **Soukromý partnerský vztah ExpressRoute** | Soukromý partnerský vztah ExpressRoute je propojení mezi místním prostředím a privátními virtuálními sítěmi Azure. Privátní partnerské vztahy umožňují přístup ke službám Azure, jako je Virtual Machines, které jsou nasazeny v rámci virtuální sítě. Prostředky a virtuální sítě, ke kterým se přihlíží prostřednictvím privátního partnerského vztahu, se považují za rozšíření základní sítě organizace. Privátní partnerské vztahy poskytují obousměrné propojení mezi místní sítí a virtuálními sítěmi Azure pomocí privátních IP adres.|
| **Partnerský vztah Microsoftu ExpressRoute** | Partnerský vztah Microsoftu ExpressRoute je propojení mezi místním prostředím a veřejnými službami Microsoftu a Azure. To zahrnuje připojení k Office 365, Dynamics 365 a Azure PaaS Services. Partnerský vztah se zřizuje přes veřejné IP adresy, které vlastní organizace nebo poskytovatel připojení. Ve výchozím nastavení nejsou přístupné žádné služby prostřednictvím partnerského vztahu Microsoftu ExpressRoute a organizace musí souhlasit s požadovanými službami. Tento proces potom poskytuje připojení ke stejným koncovým bodům, které jsou k dispozici na internetu.|
|

### <a name="iaas-ingress-components"></a>IaaS součásti pro příchozí přenosy dat

|Komponenta | Popis|
|---|---|
|**Síťové rozhraní** | Síťové rozhraní je prostředek, který existuje v Azure. Je připojený k virtuálnímu počítači a přiřadí privátní IP adresu, která není připojená k Internetu z podsítě, ke které je přidružená. Tato IP adresa je dynamicky nebo staticky přiřazena prostřednictvím Azure Resource Manager.|
|**Podsíť** | Podsíť je rozsah IP adres, který se vytvoří v rámci virtuální sítě. V rámci sítě VNet se dá vytvořit víc podsítí pro segmentaci sítě.|
| **Virtual Network (VNet)** | Virtuální síť je základní prostředek v Azure, který poskytuje platformu a hranici pro nasazování prostředků a povolení komunikace. Virtuální síť existuje v oblasti Azure a definuje adresní prostor IP adres a hranice směrování pro integrované prostředky virtuální sítě, jako je například Virtual Machines.|
| **Partnerské vztahy virtuálních sítí** | VNet peering je možnost konfigurace Azure, která umožňuje přímou komunikaci mezi dvěma virtuální sítě bez nutnosti brány Virtual Network. Po navázání partnerského vztahu může dva virtuální sítě komunikovat přímo a další konfigurace může řídit použití Virtual Network bran a dalších možností přenosu.|
| **Veřejná IP adresa** | Veřejná IP adresa je prostředek, který si vyhrazuje jednu z veřejných a internetových IP adres vlastněných společností Microsoft ze zadané oblasti pro použití v rámci virtuální sítě. Dá se přidružit ke konkrétnímu síťovému rozhraní, které umožňuje přístup k prostředkům ze systémů Internet, ExpressRoute a PaaS.|
| **ExpressRoute bránu** | ExpressRoute Gateway je objekt v Virtual Network zajišťuje připojení a směrování z Virtual Network do místních sítí přes privátní partnerský vztah na okruhu ExpressRoute.|
| **VPN Gateway** | VPN Gateway je objekt v Virtual Network, který poskytuje šifrované tunelové propojení z Virtual Network do externí sítě. Šifrovaný tunel může být typu Site-to-site pro obousměrnou komunikaci s místním prostředím, jinou virtuální sítí nebo cloudovým prostředím pro komunikaci s jedním koncovým bodem.|
| **Integrace virtuální sítě PaaS** | Mnoho PaaSch funkcí se dá nasadit do, nebo je integrovat s Virtual Network. Některé možnosti PaaS můžou být plně integrované s virtuální sítí a přístupné jenom přes privátní IP adresy. Ostatní, například Azure Load Balancer a Azure Application Gateway, můžou mít externí rozhraní s veřejnou IP adresou a interním rozhraním, které v rámci virtuální sítě obsahuje privátní IP adresu. V této instanci může provoz do Virtual Network příchozí prostřednictvím schopnosti PaaS.|
|

### <a name="paas-ingress-components"></a>PaaS součásti pro příchozí přenosy dat

|Komponenta | Popis|
|---|---|
|**Název hostitele** | Funkce Azure PaaS se identifikují jedinečným veřejným názvem hostitele, který je přiřazený při vytvoření prostředku. Tento název hostitele se pak zaregistruje do veřejné domény DNS, kde se dá přeložit na veřejnou IP adresu.|
|**Veřejná IP adresa** | Pokud není nasazená v konfiguraci integrované virtuální sítě, k funkcím Azure PaaS se dostanete prostřednictvím veřejné IP adresy, která je směrovatelný pro Internet. Tato adresa může být vyhrazená pro konkrétní prostředky, třeba pro veřejné Load Balancer, nebo může být přidružená k určité funkci, která má sdílený vstupní bod pro více instancí, jako je například Storage nebo SQL. Tato veřejná IP adresa je k dispozici z Internetu, ExpressRoute nebo z veřejných IP adres IaaS prostřednictvím páteřní sítě Azure.|
|**Koncové body služby** | Koncové body služby poskytují přímé privátní připojení z Virtual Network k určité funkci PaaS. Koncové body služby, které jsou dostupné jenom pro podmnožinu funkcí PaaS, poskytují zvýšený výkon a zabezpečení pro prostředky ve virtuální síti, která přistupuje k PaaS.|
|

### <a name="security-controls"></a>Ovládací prvky zabezpečení

|Komponenta | Popis|
|---|---|
|**Skupiny zabezpečení sítě (skupin zabezpečení sítě)** | Skupin zabezpečení sítě řízení provozu do virtuálních síťových prostředků v Azure a mimo ně. Skupin zabezpečení sítě použít pravidla pro přenosová data, která jsou povolená nebo zakázaná, což zahrnuje provoz v rámci Azure a mezi Azure a externími sítěmi, jako je místní nebo Internet. Skupin zabezpečení sítě se aplikují na podsítě v rámci virtuální sítě nebo na jednotlivá síťová rozhraní.|
|**PaaS firewall** | Celá řada PaaSch funkcí, jako je úložiště a SQL, má předem vytvořenou bránu firewall pro řízení příchozího síťového provozu pro konkrétní prostředek. Pravidla lze vytvořit pro povolení nebo odepření připojení z konkrétních IP adres nebo virtuálních sítí.|
|**PaaS ověřování a Access Control** | V rámci vrstveného přístupu k zabezpečení poskytují možnosti PaaS více mechanismů pro ověřování uživatelů a řízení oprávnění a přístupu.|
|**Azure Policy** | Azure Policy je služba v Azure pro vytváření, přiřazování a správu zásad. Tyto zásady používají pravidla pro řízení typů prostředků, které se dají nasadit, a konfigurace těchto prostředků. Zásady je možné použít k vymáhání dodržování předpisů tím, že zabráníte nasazení prostředků, pokud nesplňují požadavky nebo je lze použít ke sledování sestav o stavu dodržování předpisů.|
|

## <a name="general-guidance"></a>Obecné pokyny

Pro návrh a vytváření zabezpečených řešení v rámci Azure je důležité pochopit a řídit síťový provoz tak, aby mohlo probíhat pouze identifikovaná a oprávněná komunikace. Účelem těchto pokynů a specifických pokynů k součástem v dalších částech je popsání nástrojů a služeb, které je možné využít k použití zásad uvedených v _acsc ochraně: Implementace segmentace sítě a oddělení_ napříč úlohami Azure. To zahrnuje podrobné informace o tom, jak vytvořit virtuální architekturu pro zabezpečení prostředků, když není možné použít stejné tradiční fyzické a síťové ovládací prvky, které jsou možné v místním prostředí.

### <a name="specific-focus-areas"></a>Konkrétní oblasti zaměření

* Omezení počtu vstupních bodů na virtuální sítě
* Omezení počtu veřejných IP adres
* Zvažte použití sítě centrálního a paprskového návrhu pro virtuální sítě, jak je popsáno v dokumentaci k virtuálním VDC (Microsoft Virtual data Center).
* Používání produktů s integrovanými možnostmi zabezpečení pro příchozí připojení z Internetu (například Application Gateway, brána API, síťová virtuální zařízení)
* Omezení komunikačních toků, aby PaaS možnosti jenom na ty, které jsou nezbytné pro funkčnost systému
* Nasazení PaaS v konfiguraci integrované virtuální sítě pro zvýšení oddělení a řízení
* Nakonfigurujte systémy na používání šifrovacích mechanismů v souladu s pokyny pro spotřebitele ACSC a ISM.
* Kromě tradičních ovládacích prvků sítě používejte ochranu na základě identity, jako je ověřování a řízení přístupu na základě rolí.
* Implementace ExpressRoute pro připojení k místním sítím
* Implementace sítí VPN pro správu provozu a integrace s externími sítěmi
* Využití Azure Policy k omezení oblastí a prostředků jenom na ty, které jsou nezbytné pro funkčnost systému
* Využití Azure Policy k vymáhání základní konfigurace zabezpečení pro prostředky přístupné z Internetu

### <a name="additional-resources"></a>Další zdroje

|Resource | Odkaz|
|---|---|
|Australské dokumenty a předpisy týkající se dodržování předpisů podle zásad, včetně pokynů pro spotřebitele|[https://aka.ms/au-irap](https://aka.ms/au-irap)|
|Virtuální datové centrum Azure|[https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter)|
|Segmentace sítě ACSC|[https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)|
|ACSC cloudové zabezpečení pro klienty| [https://acsc.gov.au/publications/protect/cloud-security-tenants.htm](https://acsc.gov.au/publications/protect/cloud-security-tenants.htm)|
|Ruční zabezpečení informací ACSC|[https://acsc.gov.au/infosec/ism/index.htm](https://acsc.gov.au/infosec/ism/index.htm)|

## <a name="component-guidance"></a>Doprovodné materiály k komponentám

V této části najdete další doprovodné materiály týkající se jednotlivých komponent, které se vztahují k provozu příchozího přenosu dat na systémy nasazené v Azure. Každá část popisuje záměr konkrétní komponenty s odkazy na dokumentaci a konfigurační příručky, které lze použít pro pomoc s aktivitami návrhu a sestavování.

## <a name="azure"></a>Azure

Veškerá komunikace s prostředky v rámci Azure projde prostřednictvím síťové infrastruktury udržované Microsoftem, která poskytuje funkce připojení a zabezpečení. Společnost Microsoft automaticky zavedla řadu ochran pro ochranu platformy Azure a síťové infrastruktury a další funkce jsou k dispozici jako služby v rámci Azure pro řízení síťového provozu a navázání segmentace sítě a oddělení.

### <a name="ddos-protection"></a>DDoS Protection

Prostředky přístupné z Internetu jsou náchylné k DDoS útokům. V zájmu ochrany proti těmto útokům poskytuje Azure DDoS ochrany na úrovni Basic a úrovně Standard.

Úroveň Basic je automaticky povolená jako součást platformy Azure, včetně monitorování nepřetržitého provozu a zmírnění běžných útoků na úrovni sítě, a zajišťuje tak stejnou obranu, kterou využívá online služby Microsoftu. Celá škála globální sítě Azure se dá použít k distribuci a zmírnění provozu útoku napříč oblastmi. Ochrana je poskytována pro veřejné IP adresy IPv4 a IPv6.

Standard poskytuje další funkce pro zmírnění rizik oproti základní úrovni služeb, které jsou vyladěné konkrétně na prostředky Azure Virtual Network. Zásady ochrany jsou vyladěny prostřednictvím vyhrazeného monitorování provozu a algoritmů strojového učení. Ochrana je k dispozici pro veřejné IP adresy Azure IPv4.

|Resource|Odkaz|
|---|---|
|Přehled Azure DDoS Protection|[https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)|
|Osvědčené postupy pro Azure DDoS|[https://docs.microsoft.com/azure/security/fundamentals/ddos-best-practices](https://docs.microsoft.com/azure/security/fundamentals/ddos-best-practices)|
|Správa DDoS Protection|[https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)|
|

### <a name="traffic-manager"></a>Traffic Manager

Traffic Manager se používá ke správě příchozího provozu tím, že řídí, které koncové body aplikace přijímají připojení. Aby bylo možné chránit před ztrátou dostupnosti systémů nebo aplikací kvůli útokům prostřednictvím sítě Internet nebo obnovení služeb po ohrožení zabezpečení systému, lze Traffic Manager použít k přesměrování provozu na funkční a dostupné instance aplikace.

|Resource|Odkaz|
|---|---|
|Traffic Manager – přehled | [https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview)|
|Zotavení po havárii pomocí Azure DNS a Průvodce Traffic Manager | [https://docs.microsoft.com/azure/networking/disaster-recovery-dns-traffic-manager](https://docs.microsoft.com/azure/networking/disaster-recovery-dns-traffic-manager)|
|

### <a name="expressroute"></a>ExpressRoute

ExpressRoute se dá použít k vytvoření soukromé cesty z místního prostředí do systémů hostovaných v Azure. Toto připojení může poskytovat větší spolehlivost a zaručený výkon díky lepší ochraně osobních údajů pro síťovou komunikaci. Expresní trasa umožňuje subjektům služby řízení přístupu řídit příchozí provoz z místního prostředí a definovat vyhrazené adresy specifické pro organizaci, které se mají použít pro příchozí pravidla brány firewall a seznamy řízení přístupu.

|Resource | Odkaz|
|---|---|
|Přehled ExpressRoute | [https://docs.microsoft.com/azure/expressroute/](https://docs.microsoft.com/azure/expressroute/)|
|Modely připojení ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-connectivity-models](https://docs.microsoft.com/azure/expressroute/expressroute-connectivity-models)|
|

### <a name="expressroute-private-peering"></a>Soukromý partnerský vztah ExpressRoute

Privátní partnerské vztahy poskytují mechanismus pro rozšíření místního prostředí do Azure pomocí jenom privátních IP adres. To umožňuje entitám služby Společenství integrovat virtuální sítě Azure a rozsahy adres se stávajícími místními systémy a službami. Privátní partnerské vztahy poskytují jistotu, že komunikace mezi ExpressRoute je jenom pro virtuální sítě autorizované organizací. Pokud používáte privátní partnerské vztahy, entity Společenství musí implementovat síťová virtuální zařízení (síťové virtuální zařízení) místo Azure VPN Gateway, aby se navázala zabezpečená komunikace VPN s vašimi místními sítěmi, jak to vyžadují doprovodné materiály pro ACSC spotřebitele.

|Resource | Odkaz|
|---|---|
|Přehled privátního partnerského vztahu ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#routingdomains](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#routingdomains)|
|Průvodce vytvářením privátních partnerských vztahů ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager#private](https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager#private)|
|

### <a name="expressroute-microsoft-peering"></a>Partnerský vztah Microsoftu ExpressRoute

Partnerský vztah Microsoftu poskytuje vysokorychlostní připojení k veřejným službám Microsoftu s nízkou latencí, aniž by bylo nutné procházet internetem. To zajišťuje vyšší spolehlivost, výkon a ochranu osobních údajů pro připojení. Pomocí filtrů směrování můžou entity Společenství omezit komunikaci jenom na oblasti Azure, které vyžadují, ale zahrnují služby hostované jinými organizacemi a můžou vyžadovat další možnosti filtrování nebo kontroly mezi místní prostředí a Microsoft.

Entity Společenství můžou používat vyhrazené veřejné IP adresy zřízené prostřednictvím vztahu partnerských vztahů k jednoznačné identifikaci místního prostředí pro použití v bránách firewall a seznamech řízení přístupu v rámci možností PaaS.

Jako alternativu můžou entity Společenství používat ExpressRoute peering jako Underlay síť pro vytváření připojení VPN prostřednictvím Azure VPN Gateway. V tomto modelu neexistuje žádná aktivní komunikace z interní místní sítě až po veřejné služby Azure přes ExpressRoute, ale zabezpečené připojení prostřednictvím privátních virtuálních sítí se dosahuje v souladu s pokyny pro spotřebitele ACSC.

|Resource | Odkaz|
|---|---|
|Přehled partnerského vztahu Microsoftu ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#routingdomains](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#routingdomains)|
|Průvodce ExpressRoutem partnerským vztahem Microsoftu | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager#msft](https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager#msft)|
|

## <a name="iaas-ingress"></a>IaaS příchozí přenosy

V této části najdete pokyny pro komponentu pro řízení provozu příchozího přenosu dat IaaS komponent. IaaS zahrnuje Virtual Machines a další výpočetní prostředky, které se dají nasadit a spravovat v rámci Virtual Network v Azure. Aby se provoz dostal do systémů nasazených pomocí IaaS, musí mít vstupní bod do Virtual Network, který se dá vytvořit prostřednictvím veřejné IP adresy, Virtual Network brány nebo vztahu partnerských vztahů Virtual Network.

### <a name="network-interface"></a>Síťové rozhraní

Síťová rozhraní jsou vstupními body pro veškerý provoz do virtuálního počítače. Síťová rozhraní umožňují konfiguraci adresování IP a dají se použít k použití skupin zabezpečení sítě nebo pro směrování provozu přes síťové virtuální zařízení. Síťová rozhraní pro Virtual Machines by se měla naplánovat a správně nakonfigurovat tak, aby se zarovnaly celkovému segmentaci sítě a cílům oddělení.

|Resource | Odkaz|
|---|---|
|Vytvoření, změna nebo odstranění síťového rozhraní | [https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface)|
|Adresování IP síťového rozhraní | [https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses)|
|

### <a name="subnet"></a>Subnet

Podsítě jsou zásadní součástí pro segmentaci sítě a oddělení v rámci Azure. Podsítě lze použít podobně k zajištění oddělení mezi systémy. Skupin zabezpečení sítě se dá použít na podsítě, aby se omezila komunikace příchozích dat jenom na ty, které jsou nezbytné pro funkčnost systému. Podsítě lze použít jako zdrojové i cílové adresy pro pravidla brány firewall a seznamy řízení přístupu a je možné je nakonfigurovat pro koncové body služby, aby poskytovaly možnosti připojení k PaaS funkcím.

|Resource | Odkaz|
|---|---|
|Přidání, změna nebo odstranění podsítě virtuální sítě | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)|
|

### <a name="virtual-network-vnet"></a>Virtuální síť (VNet)

Virtuální sítě je jedním ze základních stavebních bloků sítě v Azure. Virtuální sítě definují adresní prostor IP adres a hranici směrování, které se mají používat v různých systémech. Virtuální sítě jsou rozdělené do podsítí a všechny podsítě v rámci Virtual Network mají vzájemnou přímou trasu sítě. Pomocí Virtual Network bran (ExpressRoute nebo VPN) se systémy v rámci Virtual Network dají zpřístupnit místním i externím prostředím. Porozumění virtuálním sítím a přidruženým parametrům konfigurace a směrování je zásadní pro porozumění a řízení síťového provozu příchozího přenosu dat.

|Resource | Odkaz|
|---|---|
|Přehled virtuálních sítí | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)|
|Průvodce plánováním virtuálních sítí | [https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)|
Vytvoření Virtual Network rychlý Start | [https://docs.microsoft.com/azure/virtual-network/quick-create-portal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)|
|

### <a name="vnet-peering"></a>Partnerský vztah virtuální sítě

Partnerský vztah virtuálních sítí slouží k zajištění přímé komunikační cesty mezi dvěma virtuálními sítěmi. Po navázání partnerského vztahu hostitelé v jednom Virtual Network mají vysokorychlostní cestu k směrování přímo na hostitele v jiném Virtual Network. Skupin zabezpečení sítě se pořád vztahuje na provoz jako na normální a rozšířené parametry konfigurace, které je možné použít k definování, jestli je povolená komunikace prostřednictvím Virtual Network Branch nebo jiných externích systémech.

|Resource | Odkaz|
|---|---|
|Virtual Network – přehled partnerských vztahů |  [https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)|
|Vytvoření, změna nebo odstranění partnerského vztahu virtuální sítě | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering)|
|

### <a name="public-ip-on-vnet"></a>Veřejná IP adresa ve virtuální síti

Veřejné IP adresy slouží k poskytnutí komunikační cesty ke službám nasazeným ve Virtual Network. Entity Společenství by měly naplánovat pečlivé přidělování veřejných IP adres a přiřazovat je pouze k prostředkům, kde je platný požadavek. Obecně platí, že veřejné IP adresy by měly být přiděleny prostředkům s integrovanými možnostmi zabezpečení, jako jsou Application Gateway nebo síťová virtuální zařízení, které poskytují zabezpečený a řízený veřejný vstupní bod pro Virtual Network.

|Resource | Odkaz|
|---|---|
|Přehled Veřejné IP adresy | [https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses)|
|Vytvoření, změna nebo odstranění veřejné IP adresy | [https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)|
|

### <a name="expressroute-gateway"></a>ExpressRoute bránu

Brány ExpressRoute poskytují příchozí bod z místního prostředí a měly by se nasazovat tak, aby splňovaly požadavky na zabezpečení, dostupnost, finanční a výkon. ExpressRoute brány poskytují definovanou šířku pásma sítě a účtují náklady na využití po nasazení. Virtuální sítě můžou mít jenom jednu bránu ExpressRoute, ale dá se připojit k několika okruhům ExpressRoute a dá se využít několika virtuálními sítěmi prostřednictvím partnerského vztahu virtuálních sítí, což umožňuje více virtuálním sítím sdílet šířku pásma a konektivitu. Za účelem konfigurace směrování mezi místními prostředími a virtuálními sítěmi pomocí bran ExpressRoute zajistěte koncové připojení pomocí známých, kontrolovaných síťových vstupních bodů. Entity služby spotřební sítě, které používají bránu ExpressRoute, musí také nasadit síťová virtuální zařízení, aby se navázalo připojení VPN k místnímu prostředí pro zajištění souladu s pokyny pro spotřebitele ACSC.

|Resource | Odkaz|
|---|---|
|Přehled brány ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways)|
|Konfigurace brány virtuální sítě pro ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager)|
|

### <a name="vpn-gateway"></a>VPN Gateway

Azure VPN Gateway poskytuje vstupní síťový bod z externí sítě pro zajištění připojení mezi lokalitami nebo Point-to-site. Služby VPN Gateway poskytují definovanou šířku pásma sítě a účtují náklady na využití po nasazení. Entity Společenství, které využívají VPN Gateway, by měly zajistit, aby byly nakonfigurované v souladu s pokyny pro spotřebitele ACSC. Virtuální sítě můžou mít jenom jeden VPN Gateway, ale dá se nakonfigurovat s více tunely a dá se využít několika virtuálními sítěmi prostřednictvím partnerského vztahu virtuálních sítí, což umožňuje více virtuálním sítím sdílet šířku pásma a konektivitu. Brány VPN se můžou navázat přes Internet nebo přes ExpressRoute prostřednictvím partnerského vztahu Microsoftu.

|Resource | Odkaz|
|---|---|
|Přehled VPN Gateway | [https://docs.microsoft.com/azure/vpn-gateway/](https://docs.microsoft.com/azure/vpn-gateway/)|
|Plánování a návrh pro VPN Gateway | [https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)|
|Konfigurace VPN Gateway pro státní úřady Austrálie|[Vyžaduje se konfigurace protokolu IPSEC pro státní úřady Austrálie.](vpn-gateway.md)|
|

### <a name="paas-vnet-integration"></a>Integrace virtuální sítě PaaS

Využití PaaS může poskytovat lepší funkce a dostupnost a snižovat režijní náklady na správu, ale musí být správně zabezpečené. Aby bylo možné zvýšit kontrolu, vyhovět segmentům sítě nebo poskytnout zabezpečený vstupní bod pro aplikace a služby, může být mnoho možností PaaS integrováno s Virtual Network.

Aby bylo možné poskytnout zabezpečený vstupní bod, mohou být PaaS možnosti, jako je například Application Gateway, konfigurovány pomocí externího, veřejného a interního rozhraní pro komunikaci s aplikačními službami. To brání nutnosti konfigurovat aplikační servery s veřejnými IP adresami a zpřístupnit je externím sítím.

Aby bylo možné používat PaaS jako integrovanou součást architektury systému nebo aplikace, společnost Microsoft nabízí více mechanismů nasazení PaaS do Virtual Network. Metodologie nasazení omezuje příchozí přístup z externích sítí, jako je Internet, a zajišťuje připojení a integraci s interními systémy a aplikacemi. Mezi příklady patří App Service prostředí, spravované instance SQL a další.

|Resource | Odkaz|
|---|---|
|Integrace virtuální sítě pro služby Azure | [https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)|
|Integrujte svoji aplikaci pomocí Průvodce postupy pro Azure Virtual Network | [https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)|
|

## <a name="paas-ingress"></a>PaaS příchozí přenosy

PaaS možnosti poskytují možnosti pro lepší možnosti a zjednodušenou správu, ale představují složitosti při adresování požadavků pro segmentaci a oddělení sítě. Funkce PaaS jsou obvykle nakonfigurované s veřejnými IP adresami a jsou přístupné z Internetu.  Při sestavování systémů využívajících možnosti PaaS byste měli dbát na to, aby identifikovaly všechny potřebné komunikační toky mezi součástmi v rámci pravidel zabezpečení systému a sítě, které jsou vytvořené tak, aby umožňovaly jenom tuto komunikaci. V rámci důkladného přístupu k zabezpečení by měly být možnosti PaaS nakonfigurovány pomocí šifrování, ověřování a odpovídajících řízení přístupu a oprávnění.  

### <a name="hostname"></a>Název hostitele

Funkce PaaS se jednoznačně identifikují pomocí názvů hostitelů, aby bylo možné na stejné veřejné IP adrese hostovat víc instancí stejné služby. Při vytváření prostředků a jejich existenci do domén DNS vlastněných společností Microsoft jsou zadány jedinečné názvy hostitelů. Konkrétní názvy hostitelů u autorizovaných služeb lze použít v nástrojích zabezpečení s možnostmi filtrování na úrovni aplikace. Některé služby je také možné nakonfigurovat s vlastními doménami podle potřeby.

|Resource | Odkaz|
|---|---|
|Mnoho veřejných oborů názvů používaných službami Azure se dá získat prostřednictvím PowerShellu spuštěním příkazu Get-AzureRMEnvironment. | [https://docs.microsoft.com/powershell/module/azurerm.profile/get-azurermenvironment](https://docs.microsoft.com/powershell/module/azurerm.profile/get-azurermenvironment)|
|Konfigurace vlastního názvu domény pro cloudovou službu Azure | App Services a jiné můžou mít vlastní domény.[https://docs.microsoft.com/azure/cloud-services/cloud-services-custom-domain-name-portal](https://docs.microsoft.com/azure/cloud-services/cloud-services-custom-domain-name-portal)|
|

### <a name="public-ip-for-paas"></a>Veřejná IP adresa pro PaaS

Veřejné IP adresy pro PaaS možnosti jsou přidělovány na základě oblasti, kde je služba hostovaná nebo nasazená. Pro sestavování vhodných pravidel zabezpečení sítě a topologie směrování pro vytváření segmentace sítě a oddělení pokrývání Azure Virtual Networks, PaaS a ExpressRoute a připojení k Internetu je porozumění přidělování veřejných IP adres a oblastí požadovanou. Azure přiděluje IP adresy z fondu přiděleného každé oblasti Azure. Společnost Microsoft zpřístupňuje adresy používané v každé oblasti ke stažení, která se aktualizuje pravidelným a kontrolovaným způsobem. Služby, které jsou k dispozici v každé oblasti, se často mění, protože jsou vydávány nové služby nebo jsou služby nasazené v širším rozsahu. Entity Společenství by si měly tyto materiály pravidelně kontrolovat a můžou využít automatizaci k údržbě systémů podle potřeby. Konkrétní IP adresy pro některé služby hostované v jednotlivých oblastech lze získat kontaktováním podpory společnosti Microsoft.

|Resource | Odkaz|
|---|---|
|Microsoft Azure rozsahy IP adres datového centra | [https://www.microsoft.com/download/details.aspx?id=41653](https://www.microsoft.com/download/details.aspx?id=41653)|
|Služby Azure na oblast | [https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional, Austrálie – střed, Austrálie – střed – 2, Austrálie – východ, Austrálie – jihovýchod & Products = vše](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast&products=all)|
|

### <a name="service-endpoints"></a>Koncové body služby

Koncové body služby Virtual Network poskytují vysokorychlostní privátní síťové připojení pro podsítě v rámci Virtual Network a využívají konkrétní možnosti PaaS. Pro kompletní segmentaci sítě a oddělení schopností PaaS musí být schopnost PaaS nakonfigurovaná tak, aby přijímala připojení jenom z nezbytných virtuálních sítí. Ne všechny funkce PaaS podporují kombinaci pravidel brány firewall, která obsahují koncové body služby a tradiční pravidla IP adresy, proto byste měli dbát na to, abyste pochopili tok komunikací vyžadovaný pro funkčnost aplikace a správu. aby implementace těchto ovládacích prvků zabezpečení neovlivnila dostupnost služby.

|Resource | Odkaz|
|---|---|
|Přehled koncových bodů služby | [https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)
|Kurz |[https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources](https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources)|
|

## <a name="security"></a>Zabezpečení

Implementaci segmentace sítě a ovládacích prvků oddělení na IaaS a PaaS se dosahují prostřednictvím zabezpečení samotných možností a implementací řízených komunikačních cest ze systémů, které budou komunikovat s podporu.

Návrh a sestavování řešení v Azure je proces vytváření logických architektur pro pochopení, řízení a monitorování síťových prostředků v celé přítomnosti Azure. Tato Logická architektura je software definovaný v rámci platformy Azure a přebírá místo fyzické síťové topologie, která je implementovaná v tradičních síťových prostředích.

Logická architektura, která je vytvořena, musí poskytovat funkcionalitu nutnou pro použitelnost, ale musí také poskytovat viditelnost a kontrolu potřebnou pro zabezpečení a integritu.

Dosažení tohoto výsledku vychází z implementace nezbytných nástrojů segmentace sítě a oddělení, ale také při ochraně a vynucování síťové topologie a implementace těchto nástrojů.

Informace, které jsou uvedené v tomto průvodci, se dají použít k identifikaci zdrojů příchozího provozu, které je potřeba povolit, a způsobů, jak se dá provoz dále řídit nebo omezit.

### <a name="network-security-groups-nsgs"></a>Skupiny zabezpečení sítě (skupin zabezpečení sítě)

Skupin zabezpečení sítě slouží k určení příchozího a odchozího provozu povoleného pro podsíť nebo konkrétní síťové rozhraní. Při konfiguraci skupin zabezpečení sítě by entity Společenství měly používat přístup na seznam povolených, kde jsou pravidla nakonfigurovaná tak, aby povolovala potřebný provoz s výchozím pravidlem, aby se odepřel veškerý provoz, který se neshoduje s konkrétním příkazem permit. Při plánování a konfiguraci skupin zabezpečení sítě musíte vzít v potaz, aby se zajistilo správné zaznamenávání všech potřebných příchozích a odchozích přenosů. To zahrnuje identifikaci a porozumění všem rozsahům privátních IP adres využívaným v rámci virtuálních sítí Azure a místním prostředí a specifické služby Microsoftu, jako jsou Azure Load Balancer a požadavky na správu PaaS. Jednotlivci, kteří jsou součástí návrhu a implementace skupin zabezpečení sítě, by měli také pochopit použití značek služeb a skupin zabezpečení aplikací pro vytváření podrobných pravidel zabezpečení pro přesnější, služby a aplikace.

|Resource | Odkaz|
|---|---|
|Přehled zabezpečení sítě | [https://docs.microsoft.com/azure/virtual-network/security-overview](https://docs.microsoft.com/azure/virtual-network/security-overview)
|Vytvoření, změna nebo odstranění skupiny zabezpečení sítě | [https://docs.microsoft.com/azure/virtual-network/manage-network-security-group](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)|
|

## <a name="paas-firewall"></a>PaaS firewall

Brána firewall PaaS je schopnost řízení přístupu k síti, kterou je možné použít na určité služby PaaS. Umožňuje filtrování IP adres nebo filtrování z konkrétních virtuálních sítí, které se mají nakonfigurovat tak, aby se omezil příchozí přenos na konkrétní instanci PaaS. Pro funkce PaaS, které zahrnují bránu firewall, by se měly nakonfigurovat zásady řízení přístupu k síti tak, aby povolovaly jenom nezbytný příchozí provoz na základě požadavků na aplikace.  

|Resource | Odkaz|
|---|---|
|Azure SQL Database a SQL Data Warehouse pravidla brány firewall protokolu IP | [https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)|
|Zabezpečení sítě úložiště | [https://docs.microsoft.com/azure/storage/common/storage-network-security](https://docs.microsoft.com/azure/storage/common/storage-network-security)|
|

## <a name="paas-authentication-and-access-control"></a>PaaS ověřování a řízení přístupu

V závislosti na schopnosti PaaS a jejím účelu použití síťových ovládacích prvků k omezení přístupu možná nebude možné nebo praktické. V rámci vrstveného modelu zabezpečení pro PaaS poskytuje Azure nejrůznější mechanismy ověřování a řízení přístupu pro omezení přístupu ke službě, a to i v případě, že je síťový provoz povolený. Mezi typické mechanismy ověřování pro PaaS možnosti patří Azure Active Directory, ověřování na úrovni aplikace a sdílené klíče nebo přístup k podpisům. Po bezpečné identifikaci uživatele lze role použít k řízení akcí, které může uživatel provádět. Tyto nástroje lze využít jako alternativu nebo jako bezplatný postup pro omezení přístupu ke službám.

|Resource | Odkaz|
|---|---|
|Řízení a udělení přístupu k databázi SQL Database a SQL Data Warehouse | [https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins)|
|Autorizace pro služby Azure Storage Services | [https://docs.microsoft.com/rest/api/storageservices/authorization-for-the-Azure-Storage-Services](https://docs.microsoft.com/rest/api/storageservices/authorization-for-the-Azure-Storage-Services)|
|

## <a name="azure-policy"></a>Azure Policy

Azure Policy je klíčovou součástí pro vynucování a udržování integrity logické architektury prostředí Azure. Vzhledem k nejrůznějším službám a cestám síťového provozu příchozího přenosu dat prostřednictvím služeb Azure je důležité, aby entity Společenství věděli o prostředcích, které existují ve svém prostředí, a dostupných síťových vstupních bodech. Aby se zajistilo, že v prostředí Azure nebudou vytvořeny neautorizované vstupní body sítě, měly by entity Společenství využít Azure Policy k řízení typů prostředků, které se dají nasadit, a jejich konfigurace. Mezi praktické příklady patří omezení prostředků jenom na ty, které jsou povolené a schválené pro použití, vynucování šifrování HTTPS v úložišti a vyžadování skupin zabezpečení sítě k přidání do podsítí.

|Resource | Odkaz|
|---|---|
|Přehled Azure Policy | [https://docs.microsoft.com/azure/governance/policy/overview](https://docs.microsoft.com/azure/governance/policy/overview)|
|Ukázkové zásady povolených typů prostředků | [https://docs.microsoft.com/azure/governance/policy/samples/allowed-resource-types](https://docs.microsoft.com/azure/governance/policy/samples/allowed-resource-types)
|Ujistěte se, že máte ukázkové zásady účtu úložiště HTTPS|[https://docs.microsoft.com/azure/governance/policy/samples/ensure-https-storage-account](https://docs.microsoft.com/azure/governance/policy/samples/ensure-https-storage-account)_
|Vynucení NSG pro ukázkové zásady podsítě| [https://docs.microsoft.com/azure/governance/policy/samples/nsg-on-subnet](https://docs.microsoft.com/azure/governance/policy/samples/nsg-on-subnet)|
|

## <a name="next-steps"></a>Další kroky

Podrobnosti o správě toků provozu z prostředí Azure do jiných sítí pomocí komponent brány v Azure najdete v článku [Správa a řízení odchozích přenosů brány](gateway-egress-traffic.md) .
