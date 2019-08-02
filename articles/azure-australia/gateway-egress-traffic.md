---
title: Řízení odchozího provozu v Azure Austrálie
description: Klíčové prvky řízení odchozího provozu v Azure pro splnění požadavků australské vlády na zabezpečené internetové brány
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: grgale
ms.openlocfilehash: 7922846d161b7a0cbda32101b6bbb40a1741b323
ms.sourcegitcommit: 15f7b641a67f3d6cf4fb4b4c11eaee18cf335923
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68602081"
---
# <a name="controlling-egress-traffic-in-azure-australia"></a>Řízení odchozího provozu v Azure Austrálie

Základní součástí zabezpečení systémů ICT je řízení síťového provozu. Omezení komunikace pouze na provoz, který je nezbytný pro fungování systému, snižuje riziko případného ohrožení zabezpečení. Viditelnost a kontrola nad externími systémy, se kterými vaše aplikace a služby komunikuje, pomáhá odhalit napadené systémy a pokusit se o exfiltrace nebo jejich úspěšnost. Tento článek poskytuje informace o tom, jak odchozí (výstupní) Přenosová síť funguje v Azure, a poskytuje doporučení pro implementaci ovládacích prvků zabezpečení sítě pro systém připojený k Internetu, který se zarovnává s australským centrem pro Internet Security Center (ACSC). Pokyny pro spotřebitele a záměrem příručky pro informace o zabezpečení (ISM) pro ACSC.

## <a name="requirements"></a>Požadavky

Celkové požadavky na zabezpečení pro systémy Společenství jsou definované v rámci ISM. Pro pomoc subjektům Společenství při implementaci zabezpečení sítě acsc zveřejnil _acsc ochranu: Implementace segmentace a oddělení_sítě a pomoc s zabezpečením systémů v cloudových prostředích acsc zveřejnila _zabezpečení cloud computingu pro klienty_.

Dokumenty ACSC popisují kontext pro implementaci zabezpečení sítě a řízení provozu a poskytují praktická doporučení pro návrh a konfiguraci sítě.

V dokumentech ACSC se identifikovaly tyto klíčové požadavky na řízení odchozího provozu v Azure.

Popis|Source
--------------- |------------------
**Implementací segmentace a oddělení sítě**, například použijte n-vrstvou architekturu, pomocí bran firewall na hostiteli a řízení přístupu k síti omezte příchozí a odchozí připojení k síti pouze na požadované porty a protokoly.| [Cloud Computing pro klienty](https://acsc.gov.au/publications/protect/cloud-security-tenants.htm)
**Implementujte dostatečně velkou šířku pásma, nízkou latenci a spolehlivé připojení k síti** mezi klientem (včetně vzdálených uživatelů tenanta) a cloudovou službou, aby splňovala požadavky na dostupnost tenanta.  | [ACSC chránit: Implementace segmentace a oddělení sítě](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**Použijte technologie více než jenom síťové vrstvy**. Každý hostitel a síť by měly být rozdělené a oddělené, pokud je to možné, na nejnižší úrovni, která může být prakticky spravovaná. Ve většině případů to platí od vrstvy Data Link až do a včetně aplikační vrstvy; v citlivých prostředích ale může být vhodná fyzická izolace. Míry založené na hostiteli a síti by se měly nasadit doplňkovým způsobem a být centrálně monitorovány. Pouze implementace brány firewall nebo zařízení zabezpečení, protože jediné opatření zabezpečení není dostačující. |[ACSC chránit: Implementace segmentace a oddělení sítě](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**Používejte zásady s nejnižšími oprávněními a potřebnou Ki k znát**. Pokud hostitel, služba nebo síť nemusí komunikovat s jiným hostitelem, službou nebo sítí, neměla by být povolena. Pokud musí hostitel, služba nebo síť komunikovat jenom s jiným hostitelem, službou nebo sítí na konkrétním portu nebo protokolu, mělo by být omezené jenom na ty porty a protokoly. Přijetí těchto zásad napříč sítí doplní minimalizaci uživatelských oprávnění a významně zvýší celkové stav zabezpečení prostředí. |[ACSC chránit: Implementace segmentace a oddělení sítě](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**Samostatné hostitele a sítě založené na jejich citlivosti nebo nekritickém provozu pro obchodní operace**. To může zahrnovat používání různých hardwarových nebo platforem v závislosti na různých klasifikacích zabezpečení, doménách zabezpečení nebo požadavcích na dostupnost a integritu pro určité hostitele nebo sítě. Konkrétně samostatné sítě pro správu a zvažte fyzicky izolaci sítí pro vzdálenou správu pro citlivá prostředí. |[ACSC chránit: Implementace segmentace a oddělení sítě](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**Identifikujte, ověřují a schvalujte přístup všem entitám ke všem ostatním entitám**. Všichni uživatelé, hostitelé a služby by měli mít přístup ke všem ostatním uživatelům, hostitelům a službám, které jsou omezené jenom na ty, které jsou potřebné k provádění jejich určených povinností nebo funkcí. Všechny starší nebo místní služby, které obcházejí nebo downgradují sílu totožnosti, ověřování a služby autorizace, by měly být zakázány všude, kde je to možné, a jejich použití je pečlivě monitorováno. |[ACSC chránit: Implementace segmentace a oddělení sítě](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**Implementujte možnost Povolit výpis síťových přenosů místo seznamu odepření**. Povolit přístup jenom pro známý dobrý síťový provoz (identifikovaný, ověřený a autorizovaný) místo odepření přístupu ke známému špatnému síťovému provozu (třeba blokování konkrétní adresy nebo služby). Seznam povolených seznamů má za následek nadřazené zásady zabezpečení pro odepření seznamů a významně vylepšuje vaši kapacitu pro detekci a vyhodnocení potenciálních vniknutí do sítě. |[ACSC chránit: Implementace segmentace a oddělení sítě](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**Definování seznamu povolených webů s povolenými weby a blokování všech** neuvedených webů efektivně odstraní jednu z nejběžnějších technik pro doručování dat a exfiltrace, které používá nežádoucí osoba. Pokud mají uživatelé legitimní požadavky na přístup k mnoha webům nebo rychle se měnící seznam webů; měli byste zvážit náklady na takovou implementaci. Dokonce i relativně opravňující seznam povolených nabídek nabízí lepší zabezpečení, než se spoléhá na seznamy pro odepření, ani žádná omezení, a přitom stále snižují náklady na implementaci. Příkladem opravňujícího seznamu povolených souborů může být povolení celé australské domény Austrálie, to znamená *. au nebo povolení hlavních 1 000 lokalit od hodnocení webu Alexa (po filtrování domén DDNS (Dynamic Domain Name System) a jiných nevhodných domén).| [Australský úřad australské vlády Information Security Manual (ISM)](https://www.cyber.gov.au/ism)
|

Tento článek poskytuje informace a doporučení týkající se řízení síťového provozu, který opouští vaše prostředí Azure. Pokrývá systémy nasazené v Azure s využitím infrastruktury jako služby (IaaS) a platformy jako služby (PaaS).

Článek o [příchozím provozu brány](gateway-ingress-traffic.md) řeší síťový provoz, který vstupuje do prostředí Azure, a je doplňkem tohoto článku, který umožňuje úplné pokrytí řízení sítě.

## <a name="architecture"></a>Architektura

Při návrhu a implementaci zabezpečení sítě je potřeba nejdřív porozumět tomu, jak v Azure funguje přenos v provozu napříč IaaS i PaaS. Tato část poskytuje přehled o tom, jak se zpracovávají odchozí přenosy generované prostředkem hostovaným v Azure, a ovládací prvky zabezpečení, které jsou k dispozici pro omezení a řízení provozu.

### <a name="architecture-components"></a>Komponenty architektury

Diagram architektury zobrazený tady znázorňuje možné cesty, které může síťový provoz provést při ukončování systému, který je nasazený do podsítě ve virtuální síti. Provoz ve virtuální síti se spravuje a řídí na úrovni podsítě s pravidly směrování a zabezpečení, která se vztahují na prostředky obsažené v. Komponenty související s výstupními přenosy jsou rozdělené do systémů, efektivních tras, typů dalších segmentů směrování, ovládacích prvků zabezpečení a PaaSch odchozích dat.

![Architektura](media/egress-traffic.png)

### <a name="systems"></a>OSI

Systémy jsou prostředky Azure a související součásti, které generují odchozí přenosy v rámci podsítě protokolu IP, která je součástí virtuální sítě.

| Komponenta | Popis |
| --- | ---|
|Virtuální síť (VNet) | Virtuální síť je základní prostředek v Azure, který poskytuje platformu a hranici pro nasazování prostředků a povolení komunikace. Virtuální síť existuje v oblasti Azure a definuje adresní prostor IP adres a hranice směrování pro integrované prostředky virtuální sítě, jako je například Virtual Machines.|
|Subnet | Podsíť je rozsah IP adres, který se vytvoří v rámci virtuální sítě. V rámci sítě VNet se dá vytvořit víc podsítí pro segmentaci sítě.|
|Síťové rozhraní| Síťové rozhraní je prostředek, který existuje v Azure. Je připojený k virtuálnímu počítači a přiřadí privátní IP adresu, která není připojená k Internetu z podsítě, ke které je přidružená. Tato IP adresa je dynamicky nebo staticky přiřazena prostřednictvím Azure Resource Manager.|
|Veřejné IP adresy| Veřejná IP adresa je prostředek, který si vyhrazuje jednu z veřejných a internetových IP adres vlastněných společností Microsoft ze zadané oblasti pro použití v rámci virtuální sítě. Dá se přidružit ke konkrétnímu síťovému rozhraní nebo prostředku PaaS, což umožňuje, aby prostředek komunikoval s internetem, ExpressRoute a dalšími PaaS systémy.|
|

### <a name="routes"></a>Trasy

Cesta, která přebírá výstup provozu, závisí na platných trasách pro daný prostředek, což je výsledná sada tras určená kombinací tras. Projděte si všechny možné zdroje a aplikace logiky směrování Azure.

| Komponenta | Popis |
|--- | ---|
|Systémové trasy| Azure automaticky vytvoří systémové trasy a přiřadí je ke každé podsíti ve virtuální síti. Systémové trasy nelze vytvořit nebo odebrat, ale některé lze přepsat vlastními trasami. Azure vytvoří výchozí systémové trasy pro každou podsíť a přidá další volitelné výchozí trasy ke konkrétním podsítím nebo každé podsíti, pokud se využívají konkrétní možnosti Azure.|
|Koncové body služeb| Koncové body služby poskytují přímé privátní výstupní připojení z podsítě k určité PaaS schopnosti. Koncové body služby, které jsou dostupné jenom pro podmnožinu funkcí PaaS, poskytují zvýšený výkon a zabezpečení pro prostředky ve virtuální síti, která přistupuje k PaaS.|
|Směrovací tabulky| Směrovací tabulka je prostředkem v Azure, který se dá vytvořit, aby se určily uživatelsky definované trasy (udr), které můžou doplňovat nebo přepisovat systémové trasy nebo směrovat informace prostřednictvím Border Gateway Protocol. Každý UDR určuje síť, masku sítě a další segment směrování. Směrovací tabulka může být přidružená k podsíti a stejná směrovací tabulka může být přidružená k několika podsítím, ale podsíť může mít jenom jednu směrovací tabulku nebo jednu.|
|Border Gateway Protocol (BGP)| BGP je protokol směrování mezi autonomním systémem. Autonomní systém je síť nebo skupina sítí v rámci běžné správy a s běžnými zásadami směrování. Protokol BGP slouží k výměně informací o směrování mezi autonomními systémy. Protokol BGP se dá integrovat do virtuálních sítí prostřednictvím bran virtuální sítě.|
|

### <a name="next-hop-types-defined"></a>Definice typů dalších segmentů směrování

Každá trasa v rámci Azure zahrnuje rozsah sítě a přidruženou masku podsítě a další segment směrování, který určuje, jak se provoz zpracovává.

Typ dalšího segmentu směrování | Popis
---- | ----
**Virtual Network** | Směruje provoz mezi rozsahy adres v adresním prostoru virtuální sítě. Azure vytvoří trasy s předponami adres, které odpovídají jednotlivým rozsahům adres definovaným v rámci adresního prostoru virtuální sítě. Pokud je v adresním prostoru virtuální sítě definováno více rozsahů adres, Azure vytvoří pro každý rozsah adres jednotlivé trasy. Azure automaticky směruje provoz mezi podsítěmi v rámci virtuální sítě pomocí tras vytvořených pro každý rozsah adres.
**Partnerské vztahy virtuálních sítí** | Při vytvoření partnerského vztahu virtuálních sítí mezi dvěma virtuálními sítěmi se přidá trasa pro každý rozsah adres každé virtuální sítě do virtuální sítě, na kterou je partnerský vztah. Provoz se směruje mezi partnerskými virtuálními sítěmi stejným způsobem jako podsítě v rámci virtuální sítě.
**Brána virtuální sítě** | Po přidání brány virtuální sítě do virtuální sítě se přidá jedna nebo více tras s bránou virtuální sítě uvedené jako typ dalšího segmentu směrování. Zahrnuté trasy jsou ty, které jsou nakonfigurované v rámci prostředku brány místní sítě a jakékoli trasy přecházejí přes protokol BGP.
**Virtuální zařízení** | Virtuální zařízení obvykle spouští síťovou aplikaci, jako je například brána firewall. Virtuální zařízení umožňuje další zpracování provozu, jako je například filtrování, kontrola nebo překlad adres. Každá trasa s typem směrování virtuálního zařízení musí taky zadat IP adresu dalšího segmentu směrování.
**VirtualNetworkServiceEndpoint** | Veřejné IP adresy pro konkrétní službu se přidávají jako trasy k podsíti s dalším segmentem směrování VirtualNetworkServiceEndpoint, když je povolen koncový bod služby. Koncové body služby jsou povolené pro jednotlivé služby v jednotlivých podsítích v rámci virtuální sítě. Veřejné IP adresy služeb Azure se pravidelně mění. Azure při změně adres spravuje adresy ve směrovací tabulce automaticky.
**Internet** | Provoz s dalším segmentem směrování Internetu ukončí virtuální síť a automaticky se převede na veřejnou IP adresu, a to buď z dynamického fondu dostupného v přidružené oblasti Azure, nebo pomocí veřejné IP adresy nakonfigurované pro tento prostředek. Pokud je cílová adresa určena pro jednu ze služeb Azure, provoz se směruje přímo do služby přes páteřní síť Azure místo směrování provozu na Internet. Provoz mezi službami Azure neprochází přes internet, a to bez ohledu na to, ve které oblasti Azure existuje virtuální síť nebo ve které oblasti Azure je nasazená instance služby Azure.
**Žádné** | Provoz s dalším segmentem směrování None se vynechává. Azure vytvoří výchozí systémové trasy pro rezervované předpony adres, které nejsou jako typ dalšího segmentu směrování. Trasy s dalším segmentem směrování None je také možné přidat pomocí směrovacích tabulek, aby se zabránilo směrování provozu do konkrétních sítí.
|

### <a name="security-controls"></a>Ovládací prvky zabezpečení

Ovládací prvek | Popis
----- | -----
**Skupiny zabezpečení sítě (skupin zabezpečení sítě)** | Skupin zabezpečení sítě řízení provozu do virtuálních síťových prostředků v Azure a mimo ně. Skupin zabezpečení sítě použít pravidla pro přenosová data, která jsou povolená nebo zakázaná, což zahrnuje provoz v rámci Azure a mezi Azure a externími sítěmi, jako je místní nebo Internet. Skupin zabezpečení sítě se aplikují na podsítě v rámci virtuální sítě nebo na jednotlivá síťová rozhraní.
**Azure Firewall** | Azure Firewall je spravovaná cloudová služba zabezpečení sítě, která chrání prostředky virtuální sítě Azure. Jde o plně stavovou bránu firewall poskytovanou jako služba s integrovanou vysokou dostupností a neomezenou cloudovou škálovatelností. U Azure Firewall se dá nakonfigurovat tradiční pravidla filtrování sítě založená na IP adresách, protokolech a portech, ale podporuje taky filtrování založené na plně kvalifikovaném názvu domény (FQDN), značkách služby a integrované analýze hrozeb.
**Síťové virtuální zařízení (síťové virtuální zařízení)** | Síťová virtuální zařízení jsou média virtuálního počítače, která můžou poskytovat služby, zabezpečení a další funkce do Azure. Síťová virtuální zařízení podporují síťové funkce a služby ve formě virtuálních počítačů ve virtuálních sítích a nasazeních. Síťová virtuální zařízení se dá použít k řešení konkrétních požadavků, integraci s nástroji pro správu a provoz nebo pro zajištění konzistence s existujícími produkty. Azure podporuje širokou škálu síťových virtuálních zařízení jiných výrobců, mimo jiné firewally webových aplikací (WAF), firewally, brány/směrovače, řadiče pro doručování aplikací (ADC) a nástroje pro optimalizaci sítí WAN.
**Zásady koncového bodu služby (Preview)** | Zásady koncového bodu služby virtuální sítě umožňují filtrovat provoz virtuální sítě do služeb Azure a povolit pouze konkrétní prostředky služeb Azure nad koncovými body služby. Zásady koncových bodů umožňují v provozu virtuální sítě podrobně řídit přístup ke službám Azure.
**Azure Policy** | Azure Policy je služba v Azure pro vytváření, přiřazování a správu zásad. Tyto zásady používají pravidla pro řízení typů prostředků, které se dají nasadit, a konfigurace těchto prostředků. Zásady je možné použít k vymáhání dodržování předpisů tím, že zabráníte nasazení prostředků, pokud nesplňují požadavky nebo je lze použít ke sledování sestav o stavu dodržování předpisů.
|

### <a name="paas-egress"></a>Výstup PaaS

Většina prostředků PaaS negeneruje odchozí přenosy, ale buď reaguje na příchozí žádosti (například Application Gateway, úložiště, SQL Database atd.), nebo přenáší data z jiných prostředků (například Service Bus a Azure Relay). Komunikace v síti mezi PaaS prostředky, jako je například App Services do úložiště nebo databáze SQL, je řízena a obsažena v Azure a zabezpečená prostřednictvím identit a dalších ovládacích prvků konfigurace prostředků namísto segmentace sítě nebo oddělení.

Prostředky PaaS nasazené do virtuální sítě obdrží vyhrazené IP adresy a řídí se libovolnými ovládacími prvky směrování a skupin zabezpečení sítě stejným způsobem jako jiné prostředky ve virtuální síti. Prostředky PaaS, které neexistují v rámci virtuální sítě, budou využívat fond IP adres, které jsou sdíleny ve všech instancích prostředku, které jsou publikovány prostřednictvím dokumentace společnosti Microsoft nebo lze určit prostřednictvím Azure Resource Manager.

## <a name="general-guidance"></a>Obecné pokyny

Pro návrh a vytváření zabezpečených řešení v rámci Azure je důležité pochopit a řídit síťový provoz tak, aby mohlo probíhat pouze identifikovaná a oprávněná komunikace. Účelem těchto pokynů a informací o specifických součástech v dalších částech je Popis nástrojů a služeb, které je možné využít k uplatnění zásad uvedených v [nástroji acsc Protect: Implementace segmentace sítě a oddělení](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm) napříč úlohami Azure. To zahrnuje podrobné informace o tom, jak vytvořit virtuální architekturu pro zabezpečení prostředků, když není možné použít stejné tradiční fyzické a síťové ovládací prvky, které jsou možné v místním prostředí.

### <a name="guidance"></a>Doprovodné materiály

* Omezte počet výstupních bodů pro virtuální sítě.
* Přepsat výchozí systémovou trasu pro všechny podsítě, které nepotřebují přímou odchozí komunikaci do Internetu
* Návrh a implementace kompletní síťové architektury pro identifikaci a řízení všech příchozích a odchozích bodů do prostředků Azure
* Zvažte použití sítě centrálního a paprskového návrhu pro virtuální sítě, jak je popsáno v dokumentaci k virtuálním VDC (Microsoft Virtual data Center).
* Používání produktů s integrovanými možnostmi zabezpečení pro odchozí připojení k Internetu (například Azure Firewall, síťová virtuální zařízení nebo webové proxy servery)
* Omezení oprávnění konfigurace sítě pomocí ovládacích prvků identity, jako je například přístup na základě rolí, podmíněný přístup a vícefaktorové ověřování (MFA)
* Implementujte zámky, aby se zabránilo úpravám nebo mazání klíčových elementů konfigurace sítě.
* Nasazení PaaS v konfiguraci integrované virtuální sítě pro zvýšení oddělení a řízení
* Implementace ExpressRoute pro připojení k místním sítím
* Implementace sítí VPN pro integraci s externími sítěmi
* Využití Azure Policy k omezení oblastí a prostředků jenom na ty, které jsou nezbytné pro funkčnost systému
* Využití Azure Policy k vymáhání základní konfigurace zabezpečení pro prostředky
* Využití Network Watcher a Azure Monitor k protokolování, auditování a viditelnosti síťového provozu v rámci Azure

### <a name="resources"></a>Zdroje a prostředky

Položka | Odkaz
-----------| ---------
_Australské dokumenty a předpisy týkající se dodržování předpisů podle zásad, včetně pokynů pro spotřebitele_ | [https://aka.ms/au-irap](https://aka.ms/au-irap)
_Virtuální datové centrum Azure_ | [https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter)
_Segmentace sítě ACSC_ | [https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
_ACSC cloudové zabezpečení pro klienty_ | [https://acsc.gov.au/publications/protect/cloud-security-tenants.htm](https://acsc.gov.au/publications/protect/cloud-security-tenants.htm)
_Ruční zabezpečení informací ACSC_ | [https://acsc.gov.au/infosec/ism/index.htm](https://acsc.gov.au/infosec/ism/index.htm)
|

## <a name="component-guidance"></a>Doprovodné materiály k komponentám

V této části najdete další doprovodné materiály týkající se jednotlivých komponent, které jsou důležité pro přenos dat pro systémy nasazené v Azure. Každá část popisuje záměr konkrétní komponenty s odkazy na dokumentaci a konfigurační příručky, které lze použít pro pomoc s aktivitami návrhu a sestavování.

### <a name="systems-security"></a>Zabezpečení systémů

Veškerá komunikace s prostředky v rámci Azure projde prostřednictvím síťové infrastruktury udržované Microsoftem, která poskytuje funkce připojení a zabezpečení. Společnost Microsoft automaticky zavedla řadu ochran pro ochranu platformy Azure a síťové infrastruktury a další funkce jsou k dispozici jako služby v rámci Azure pro řízení síťového provozu a navázání segmentace sítě a oddělení.

### <a name="virtual-network-vnet"></a>Virtuální síť (VNet)

Virtuální sítě jsou jedním ze základních stavebních bloků sítě v Azure. Virtuální sítě definují adresní prostor IP adres a hranici směrování, které se mají používat v různých systémech. Virtuální sítě jsou rozdělené do podsítí a všechny podsítě v rámci Virtual Network mají vzájemnou přímou trasu sítě. Pomocí bran virtuální sítě (ExpressRoute nebo VPN) se systémy v rámci virtuální sítě dají integrovat s místními a externími prostředími a prostřednictvím Azure poskytnutého překladu síťových adres (NAT) a přidělování veřejných IP adres. systémy můžou Připojte se k Internetu nebo k jiným oblastem a službám Azure. Porozumění virtuálním sítím a přidruženým parametrům konfigurace a směrování je zásadní pro porozumění a řízení odchozího síťového provozu.

Virtuální sítě tvoří základní adresní prostor a hranici směrování v rámci Azure, které je možné použít jako primární stavební blok segmentace sítě a oddělení.

| Resource | Odkaz |
| --- | --- |
| *Přehled virtuálních sítí* | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) |
| *Průvodce plánováním virtuálních sítí*  | [https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm) |
| *Vytvoření Virtual Network rychlý Start* | [https://docs.microsoft.com/azure/virtual-network/quick-create-portal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)
|

### <a name="subnet"></a>Subnet

Podsítě jsou zásadní součástí pro segmentaci sítě a oddělení v rámci Azure. Podsítě lze použít k zajištění oddělení mezi systémy. Podsíť je prostředek ve virtuální síti, kde se používají skupin zabezpečení sítě, směrovací tabulky a koncové body služby. Podsítě lze použít jako zdrojové i cílové adresy pro pravidla brány firewall a seznamy řízení přístupu.

Podsítě v rámci virtuální sítě by měly být naplánované tak, aby splňovaly požadavky na úlohy a systémy. Jednotlivci, kteří se podílejí na návrhu nebo implementaci podsítí, by se měli obrátit na pokyny ACSC pro segmentaci sítě a určit tak, jak se mají systémy seskupit v rámci podsítě.

|Resource|Odkaz|
|---|---|
|*Přidání, změna nebo odstranění podsítě virtuální sítě* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)
|

### <a name="network-interface"></a>Síťové rozhraní

Síťová rozhraní jsou zdrojem veškerého odchozího provozu z virtuálního počítače. Síťová rozhraní umožňují konfiguraci adresování IP a dají se použít k použití skupin zabezpečení sítě nebo pro směrování provozu prostřednictvím síťové virtuální zařízení. Síťová rozhraní pro virtuální počítače by se měla naplánovat a správně nakonfigurovat tak, aby se zarovnala celkovému segmentaci sítě a cílům oddělení.

|Resource|Odkaz|
|---|---|
|*Vytvoření, změna nebo odstranění síťového rozhraní* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface) |
|*Adresování IP síťového rozhraní*               | [https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses)
|

### <a name="vnet-integrated-paas"></a>Integrovaná síť VNet – PaaS

PaaS může poskytovat vylepšené funkce a dostupnost a snižovat režijní náklady na správu, ale musí být správně zabezpečené. Aby bylo možné zvýšit kontrolu, vymáhat segmentaci sítě nebo zajistit zabezpečený výstupní bod pro aplikace a služby, mnoho funkcí PaaS lze integrovat s virtuální sítí.

Pomocí PaaS jako integrované součásti architektury systému nebo aplikace poskytuje společnost Microsoft několik mechanismů, jak nasadit PaaS do virtuální sítě. Metodologie nasazení může pomáhat omezit přístup a současně poskytovat připojení a integraci s interními systémy a aplikacemi. Mezi příklady patří App Service prostředí, spravované instance SQL a další.

Při nasazování PaaS do virtuální sítě, ve které byly implementovány směrování a NSG ovládací prvky, je důležité pochopit konkrétní požadavky na komunikaci prostředku, včetně přístupu pro správu ze služeb společnosti Microsoft a cesty, které provoz komunikace bude trvat při odpovídání na příchozí žádosti z těchto služeb.

| Resource  | Odkaz  |
| --- | --- |
| *Integrace virtuální sítě pro služby Azure* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services) |
| *Integrujte svoji aplikaci pomocí Průvodce postupy pro Azure Virtual Network* | [https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)
|

### <a name="public-ip"></a>Veřejná IP adresa

Veřejné IP adresy se používají při komunikaci mimo virtuální síť. To zahrnuje prostředky PaaS a jakékoli trasy s dalším segmentem směrování Internetu. Entity Společenství by měly naplánovat pečlivé přidělování veřejných IP adres a přiřazovat je pouze k prostředkům, kde je platný požadavek. Obecně platí, že veřejné IP adresy by měly být přiděleny ke kontrolovaným výstupním bodům pro virtuální síť, jako jsou Azure Firewall, VPN Gateway nebo síťová virtuální zařízení.

|Resource|Odkaz|
|---|---|
|*Přehled Veřejné IP adresy*  | [https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses) |
|*Vytvoření, změna nebo odstranění veřejné IP adresy* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)
|

## <a name="effective-routes"></a>Platné trasy

Efektivní trasy představují výslednou sadu tras určenou kombinací systémových tras, koncových bodů služby, směrovacích tabulek a protokolu BGP a aplikace logiky směrování Azure. Při odeslání odchozího provozu z podsítě vybere Azure trasu na základě cílové IP adresy pomocí algoritmu shody nejdelších předpon. Pokud několik tras obsahuje stejnou předponu adresy, Azure vybere typ trasy na základě následující priority:

1. Trasa definovaná uživatelem
2. Trasa protokolu BGP
3. Systémová trasa

Je důležité si uvědomit, že systémové trasy pro provoz související s virtuální sítí, partnerskými vztahy virtuálních sítí nebo koncovými body služby virtuální sítě jsou preferované trasy, a to i v případě, že jsou trasy protokolu BGP konkrétnější.

Jednotlivci, kteří se podílejí na návrhu nebo implementaci topologií směrování v Azure, by měli porozumět tomu, jak Azure směruje provoz a vyvíjí architekturu, která vyvažuje z nezbytných funkcí systémů s požadovaným zabezpečením a viditelností. Měli byste pečlivě naplánovat prostředí tak, aby se předešlo nadměrným zásahům a výjimkám při směrování chování, protože tím dojde ke zvýšení složitosti a řešení problémů a jejich selhání může být obtížné a časově náročné.

| Resource | Odkaz  |
| --- | --- |
| *Zobrazit efektivní trasy* | <https://docs.microsoft.com/azure/virtual-network/manage-route-table#view-effective-routes> |
|

### <a name="system-routes"></a>Systémové trasy

V případě [systémových tras](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#system-routes)by měly jednotlivci, kteří se podílejí na návrhu nebo implementaci virtuálních sítí, pochopit výchozí systémové trasy a možnosti, jak tyto trasy doplnit nebo přepsat.

### <a name="service-endpoints"></a>Koncové body služby

Povolením [koncových bodů služby](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) v podsíti získáte přímou komunikační cestu k přidruženému prostředku PaaS. To může zvýšit výkon a zabezpečení tím, že omezí dostupnou cestu komunikace jenom na tuto službu. Použití koncových bodů služby zavádí potenciální exfiltrace cestu k datům, protože výchozí konfigurace umožňuje přístup ke všem instancím služby PaaS, a ne ke konkrétním instancím, které vyžaduje aplikace nebo systém.

Entity Společenství by měly vyhodnotit riziko spojené s poskytováním přímého přístupu k PaaS prostředku, včetně pravděpodobnosti a podsekvence cesty, která se nepoužívá.

Aby se snížila potenciální rizika spojená s koncovými body služby, implementujte zásady koncového bodu služby, kde je to možné, nebo zvažte povolení koncových bodů služby v Azure Firewall nebo síťové virtuální zařízení podsíti a směrování provozu z konkrétních podsítí prostřednictvím IT můžete použít filtrování, monitorování nebo kontrolu.

|Resource|Odkaz|
|---|---|
|*Kurz: Omezení síťového přístupu k prostředkům PaaS pomocí koncových bodů služby virtuální sítě pomocí Azure Portal* |[https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources](https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources)|
|

### <a name="route-tables"></a>Tabulky směrování

Směrovací tabulky poskytují mechanismus konfigurovaný správcem pro řízení síťového provozu v rámci Azure. Směrovací tabulky je možné využít pro předávání provozu do Azure Firewall nebo síťové virtuální zařízení, připojení přímo k externím prostředkům nebo pro přepsání systémových tras Azure. Směrovací tabulky je také možné použít k tomu, aby se mohly síťové služby zjistit prostřednictvím brány virtuální sítě, která je k dispozici pro prostředky v podsíti, zakázáním šíření tras brány virtuální sítě.

|Resource|Odkaz|
|---|---|
|*Koncepce směrování – vlastní trasy* |[https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes)|
|*Kurz: Směrování síťového provozu* |[https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-portal](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-portal)|
|

### <a name="border-gateway-protocol-bgp"></a>Border Gateway Protocol (BGP)

Protokol BGP může být využíván branami virtuální sítě k dynamické výměně informací o směrování v místních nebo jiných externích sítích. Protokol BGP se vztahuje na virtuální síť, pokud je nakonfigurovaná přes bránu virtuální sítě ExpressRoute prostřednictvím privátního partnerského vztahu ExpressRoute a když je povolená na VPN Gateway Azure.

Jednotlivci, kteří se účastní návrhu nebo implementace virtuálních sítí a bran virtuální sítě v Azure, by měli čas porozumět možnostem chování a konfigurace, které jsou k dispozici pro protokol BGP v Azure.

|Resource|Odkaz|
|---|---|
|*Koncepty směrování: #A0* | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#next-hop-types-across-azure-tools](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#next-hop-types-across-azure-tools)|
|*Požadavky na směrování ExpressRoute* | [https://docs.microsoft.com/azure/expressroute/expressroute-routing](https://docs.microsoft.com/azure/expressroute/expressroute-routing)|
|*Informace o protokolu BGP s Azure VPN Gateway* |[https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview)|
|*Kurz: Konfigurace VPN typu Site-to-site přes partnerský vztah Microsoftu ExpressRoute* |[https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering](https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering)|
|

## <a name="next-hop-types"></a>Typy dalších segmentů směrování

### <a name="virtual-network"></a>Virtuální sítě

Trasy s dalším segmentem směrování Virtual Network se přidávají automaticky jako systémové trasy, ale dají se taky přidat do uživatelem definovaných tras pro směrování provozu zpátky do virtuální sítě v instancích, ve kterých se přepsala systémová trasa.

### <a name="vnet-peering"></a>Partnerské vztahy virtuálních sítí

VNet peering umožňuje komunikaci mezi dvěma různorodými virtuálními sítěmi. Konfigurace partnerského vztahu virtuálních sítí musí být povolená v každé virtuální síti, ale virtuální sítě nemusí být ve stejné oblasti, předplatném ani přidružená ke stejnému tenantovi Azure Active Directory (Azure AD).

Při konfiguraci partnerského vztahu virtuálních sítí je velmi důležité, aby se jednotliví uživatelé zapojení do návrhu nebo implementace partnerských vztahů virtuálních sítí seznámili se čtyřmi přidruženými konfiguračními parametry a jak se vztahují na každou stranu partnerského vztahu:

1. **Povolení přístupu k virtuální síti:** Pokud chcete povolit komunikaci mezi dvěma virtuálními sítěmi, vyberte **povoleno** (výchozí). Povolení komunikace mezi virtuálními sítěmi umožňuje vzájemnou komunikaci prostředků připojených k libovolné virtuální síti se stejnou šířkou pásma a latencí, jako kdyby byly připojené ke stejné virtuální síti.
2. **Povolení přesměrovaného provozu:** Zaškrtněte toto políčko, pokud chcete , aby byl provoz předaný síťovému provozu, který nepochází z virtuální sítě, do této virtuální sítě prostřednictvím partnerského vztahu. Toto nastavení je základem pro implementaci síťové topologie centra a paprsků.
3. **Povolení přenosu brány:** Zaškrtněte toto políčko, pokud chcete, aby virtuální síť s partnerským vztahem mohla využívat bránu virtuální sítě připojenou k této virtuální síti. *Povoluje se povolení přenosu brány* ve virtuální síti s prostředkem brány virtuální sítě, ale platí jenom v případě, že je v druhé virtuální síti povolená *možnost používat vzdálené brány* .
4. **Používejte vzdálené brány:** Zaškrtněte toto políčko, pokud chcete, aby provoz z této virtuální sítě procházel přes bránu virtuální sítě připojenou k virtuální síti s partnerským vztahem. *Používání vzdálených bran* je ve virtuální síti bez brány virtuální sítě povolené a platí jenom v případě, že je v druhé virtuální síti povolená možnost *Povolit přenos brány* .

|Resource|Odkaz|
|---|---|
| Koncepty: Partnerské vztahy virtuálních sítí                   | [https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) |
| Vytvoření, změna nebo odstranění partnerského vztahu virtuální sítě | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering)|
|

### <a name="virtual-network-gateway"></a>Brána virtuální sítě

Brány virtuální sítě poskytují mechanismus pro integraci virtuálních sítí s externími sítěmi, jako jsou místní prostředí, Partnerská prostředí a další cloudová nasazení. Existují dva typy brány virtuální sítě: ExpressRoute a VPN.

#### <a name="expressroute-gateway"></a>ExpressRoute bránu

Brány ExpressRoute poskytují výstupní bod z virtuální sítě do místního prostředí a měly by být nasazeny, aby splňovaly požadavky na zabezpečení, dostupnost, finanční a výkon. ExpressRoute brány poskytují definovanou šířku pásma sítě a účtují náklady na využití po nasazení. Virtuální sítě můžou mít jenom jednu bránu ExpressRoute, ale dá se připojit k několika okruhům ExpressRoute a dá se využít několika virtuálními sítěmi prostřednictvím partnerského vztahu virtuálních sítí, což umožňuje sdílení šířky pásma a připojení. Za účelem konfigurace směrování mezi místními prostředími a virtuálními sítěmi pomocí bran ExpressRoute zajistěte koncové připojení pomocí známých, kontrolovaných výstupních bodů sítě. Entity služby Řízení uživatelských služeb, které používají bránu ExpressRoute prostřednictvím privátního partnerského vztahu ExpressRoute, musí také nasadit síťové virtuální zařízení (síťové virtuální zařízení), aby se navázalo připojení VPN k místnímu prostředí pro zajištění souladu s pokyny pro spotřebitele ACSC.

Je důležité si uvědomit, že brány ExpressRoute mají omezení pro rozsahy adres, komunity a další konkrétní položky konfigurace vyměňované pomocí protokolu BGP.

| Resource  | Odkaz  |
|---|---|
| Přehled brány ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways) |
| Konfigurace brány virtuální sítě pro ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager)
|

#### <a name="vpn-gateway"></a>VPN Gateway

Azure VPN Gateway poskytuje odchozí síťové body z virtuální sítě do externí sítě pro zabezpečené připojení Site-to-site. Služby VPN Gateway poskytují definovanou šířku pásma sítě a účtují náklady na využití po nasazení. Entity Společenství, které využívají VPN Gateway, by měly zajistit, aby byly nakonfigurované v souladu s pokyny pro spotřebitele ACSC. Virtuální sítě můžou mít jenom jeden VPN Gateway, ale dá se nakonfigurovat s více tunely a dá se využít několika virtuálními sítěmi prostřednictvím partnerského vztahu virtuálních sítí, což umožňuje více virtuálním sítím sdílet šířku pásma a konektivitu. Brány VPN se můžou navázat přes Internet nebo přes ExpressRoute prostřednictvím partnerského vztahu Microsoftu.

| Resource  | Odkaz |
| --- | --- |
| Přehled VPN Gateway| [https://docs.microsoft.com/azure/vpn-gateway](https://docs.microsoft.com/azure/vpn-gateway)|
| Plánování a návrh pro VPN Gateway | [https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)|
| Azure VPN Gateway v Azure Austrálie | [Azure VPN Gateway v Azure Austrálie](vpn-gateway.md)
|

### <a name="next-hop-of-virtual-appliance"></a>Další segment směrování virtuálního zařízení

Další segmentace virtuálního zařízení nabízí možnost zpracovávat síťový provoz mimo sítě Azure a topologii směrování použitou pro virtuální sítě. Virtuální zařízení můžou použít pravidla zabezpečení, překládat adresy, navazovat sítě VPN, přenosy proxy nebo řadu dalších možností. Další segment směrování virtuálního zařízení se aplikuje prostřednictvím udr v tabulce směrování a dá se použít k přímému směrování provozu do Azure Firewall, individuálních síťové virtuální zařízení nebo Azure Load Balancer poskytování dostupnosti napříč několika síťová virtuální zařízení. Aby bylo možné používat virtuální zařízení pro směrování, musí být u přidružených síťových rozhraní povoleno předávání IP adres.

| Resource  | Odkaz |
| --- | ---|
| Koncepty směrování: Vlastní trasy | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes) |
| Povolení nebo zakázání předávání IP | [https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#enable-or-disable-ip-forwarding](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#enable-or-disable-ip-forwarding)
|

### <a name="next-hop-of-virtualnetworkserviceendpoint"></a>Další segment směrování VirtualNetworkServiceEndpoint

Trasy s typem dalšího segmentu směrování VirtualNetworkServiceEndpoint se přidávají jenom v případě, že je koncový bod služby nakonfigurovaný v podsíti a nedá se ručně nakonfigurovat pomocí směrovacích tabulek.

### <a name="next-hop-of-internet"></a>Další segment směrování Internetu

Další segment sítě Internet se používá k přístupu k prostředkům, které používají veřejnou IP adresu, což zahrnuje Internet i služby PaaS a Azure v oblastech Azure. Další segment sítě Internet nevyžaduje výchozí trasu (0.0.0.0/0) pokrývající všechny sítě, ale je možné ji použít k povolení cest směrování pro konkrétní veřejné služby. Další segmenty internetu by se měly používat k přidávání tras ke autorizovaným službám a funkcím vyžadovaným pro systémové funkce, jako jsou adresy pro správu Microsoft.

Příklady služeb, které je možné přidat pomocí dalšího segmentu směrování Internetu:

1. Služby správy klíčů pro aktivaci Windows
2. Správa App Service Environment

|Resource|Odkaz|
|---|---|
| Odchozí připojení v Azure | [https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) |
| Povolení aktivace prostřednictvím služby správy klíčů pomocí vlastních tras Azure | [https://docs.microsoft.com/bs-latn-ba/azure/virtual-machines/troubleshooting/custom-routes-enable-kms-activation](https://docs.microsoft.com/bs-latn-ba/azure/virtual-machines/troubleshooting/custom-routes-enable-kms-activation) |
| Uzamčení App Service Environment  | [https://docs.microsoft.com/azure/app-service/environment/firewall-integration](https://docs.microsoft.com/azure/app-service/environment/firewall-integration) |
|

### <a name="next-hop-of-none"></a>Další segment směrování None

Další segment směrování None se dá použít k tomu, aby se zabránilo komunikaci s určitou sítí. Na rozdíl od NSG, která určuje, jestli je přenos povolený nebo zakázaný, aby procházel dostupnou síťovou cestu, použití dalšího segmentu směrování None odebere cestu k síti úplně. Při vytváření tras s dalším segmentem směrování None byste měli dbát zvláště při jejich použití na výchozí trase 0.0.0.0/0, protože by to mohlo mít neočekávané důsledky a mohlo by dojít k potížím s řešením složitých a časově náročných problémů.

## <a name="security"></a>Zabezpečení

Implementaci segmentace sítě a ovládacích prvků oddělení na IaaS a PaaS se dosahují prostřednictvím zabezpečení samotných možností a implementací řízených komunikačních cest ze systémů, které budou komunikovat s podporu.

Návrh a sestavování řešení v Azure je proces vytváření logických architektur pro pochopení, řízení a monitorování síťových prostředků v celé přítomnosti Azure. Tato Logická architektura je software definovaný v rámci platformy Azure a přebírá místo fyzické síťové topologie, která je implementovaná v tradičních síťových prostředích.

Logická architektura, která je vytvořena, musí poskytovat funkcionalitu nutnou pro použitelnost, ale musí také poskytovat viditelnost a kontrolu potřebnou pro zabezpečení a integritu.

Dosažení tohoto výsledku vychází z implementace nezbytných nástrojů segmentace sítě a oddělení, ale také při ochraně a vynucování síťové topologie a implementace těchto nástrojů.

### <a name="network-security-groups-nsgs"></a>Skupiny zabezpečení sítě (skupin zabezpečení sítě)

Skupin zabezpečení sítě slouží k určení příchozího a odchozího provozu povoleného pro podsíť nebo konkrétní síťové rozhraní. Při konfiguraci skupin zabezpečení sítě by entity Společenství měly používat přístup na seznam povolených, kde jsou pravidla nakonfigurovaná tak, aby povolovala potřebný provoz s výchozím pravidlem, aby se odepřel veškerý provoz, který se neshoduje s konkrétním příkazem permit. Při plánování a konfiguraci skupin zabezpečení sítě je nutné dbát na to, aby byl správně zachycen veškerý potřebný příchozí a odchozí provoz. To zahrnuje identifikaci a porozumění všem rozsahům privátních IP adres využívaným v rámci virtuálních sítí a místním prostředí a specifické služby Microsoftu, jako jsou Azure Load Balancer a požadavky na správu PaaS. Jednotlivci, kteří se podílejí na navrhování a implementaci skupin zabezpečení sítě, by měli také pochopit použití značek služeb a skupin zabezpečení aplikací pro vytváření podrobných pravidel zabezpečení, služby a specifických pro aplikace.

Je důležité si uvědomit, že výchozí konfigurace pro NSG umožňuje odchozí přenosy na všechny adresy v rámci virtuální sítě a všechny veřejné IP adresy.

|Resource|Odkaz|
|---|---|
|Přehled zabezpečení sítě | [https://docs.microsoft.com/azure/virtual-network/security-overview](https://docs.microsoft.com/azure/virtual-network/security-overview)|
|Vytvoření, změna nebo odstranění skupiny zabezpečení sítě | [https://docs.microsoft.com/azure/virtual-network/manage-network-security-group](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)|
|

### <a name="azure-firewall"></a>Brána Azure Firewall

Azure Firewall lze využít k sestavení topologie sítě rozbočovače a paprsku a vynutili centralizované ovládací prvky zabezpečení sítě. Azure Firewall můžete použít ke splnění nezbytných požadavků ISM pro odchozí přenosy, a to implementací přístupu pro povolení výpisu, kde jsou povolené jenom IP adresy, protokoly, porty a plně kvalifikované názvy domény, které jsou potřeba pro systémové funkce. Entity Společenství by měly mít přístup na základě rizik, aby zjistili, jestli jsou možnosti zabezpečení poskytované Azure Firewall dostačující pro jejich požadavky. V případě scénářů, kde jsou požadovány další možnosti zabezpečení nad rámec těch, které poskytuje Azure Firewall, by měly entity Společenství zvážit implementaci síťová virtuální zařízení.

|Resource|Odkaz|
|---|---|
|*Dokumentace k Azure Firewall* | [https://docs.microsoft.com/azure/firewall](https://docs.microsoft.com/azure/firewall)|
|*Kurz: Nasazení a konfigurace Azure Firewall v hybridní síti pomocí Azure PowerShell* | [https://docs.microsoft.com/azure/firewall/tutorial-hybrid-ps](https://docs.microsoft.com/azure/firewall/tutorial-hybrid-ps)|
|

### <a name="network-virtual-appliances-nvas"></a>Síťová virtuální zařízení (síťová virtuální zařízení)

Síťová virtuální zařízení se dá použít k sestavení síťové topologie centra a paprsků, poskytování rozšířených nebo doplňkových síťových funkcí nebo se dá použít jako alternativa k síťovému mechanismu Azure pro známé a konzistentní podporu a správu pomocí místních síťových služeb. Síťová virtuální zařízení je možné nasadit tak, aby splňovala konkrétní požadavky na zabezpečení, jako je například; scénáře, kdy je potřeba mít na vědomí identitu související se síťovými přenosy, dešifrováním HTTPS, kontrolou obsahu, filtrováním nebo dalšími možnostmi zabezpečení. Síťová virtuální zařízení by se měly nasadit v konfiguraci s vysokou dostupností a jednotlivci, kteří se podílejí na návrhu nebo implementaci síťová virtuální zařízení, by se měli obrátit na příslušné dokumentaci od dodavatele, kde najdete pokyny k nasazení v Azure.

|Resource|Odkaz|
|---|---|
|*Nasazení vysoce dostupných síťových virtuálních zařízení* | [https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha)|
|

### <a name="service-endpoint-policies-preview"></a>Zásady koncového bodu služby (Preview)

Nakonfigurujte zásady koncového bodu služby na základě dostupnosti služby a posouzení rizik zabezpečení pravděpodobnosti a dopadu exfiltrace dat. Zásady koncového bodu služby by se měly považovat za Azure Storage a spravovat na základě případu pro jiné služby založené na přidruženém profilu rizika.

| Resource | Odkaz  |
| --- | --- |
| *Přehled zásad koncového bodu služby* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview) |
| *Vytvoření, změna nebo odstranění zásad koncového bodu služby pomocí Azure Portal* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-portal](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-portal)
|

### <a name="azure-policy"></a>Azure Policy

Azure Policy je klíčovou součástí pro vynucování a udržování integrity logické architektury prostředí Azure. K dispozici jsou různé služby a odchozí cesty k síťovým přenosům prostřednictvím služeb Azure. Je důležité, aby entity Společenství věděli o prostředcích, které existují ve svém prostředí, a dostupných síťových odchozích bodech. Aby se zajistilo, že v prostředí Azure nebudou vytvořeny neautorizované body odchozího přenosu dat, měly by entity Společenství používat Azure Policy k řízení typů prostředků, které se dají nasadit, a konfigurace těchto prostředků. Mezi praktické příklady patří omezení prostředků jenom na ty, které jsou povolené a schválené k použití a které vyžadují přidání skupin zabezpečení sítě do podsítí.

|Resource | Odkaz|
|---|---|
|*Přehled Azure Policy* | [https://docs.microsoft.com/azure/governance/policy/overview](https://docs.microsoft.com/azure/governance/policy/overview)|
|*Ukázkové zásady povolených typů prostředků* | [https://docs.microsoft.com/azure/governance/policy/samples/allowed-resource-types](https://docs.microsoft.com/azure/governance/policy/samples/allowed-resource-types)|
|*Vynucení NSG pro ukázkové zásady podsítě*| [https://docs.microsoft.com/azure/governance/policy/samples/nsg-on-subnet](https://docs.microsoft.com/azure/governance/policy/samples/nsg-on-subnet)|
|

## <a name="paas-egress-capabilities"></a>PaaS možnosti odchozího přenosu dat

PaaS možnosti poskytují možnosti pro zvýšení funkčnosti a zjednodušenou správu, ale představují složitosti při adresování požadavků pro segmentaci a oddělení sítě. Funkce PaaS jsou obvykle nakonfigurované s veřejnými IP adresami a jsou přístupné z Internetu.  Pokud používáte možnosti PaaS v rámci svých systémů a řešení, měli byste pečlivě určit, jestli se komunikační toky mezi součástmi a vytvořit pravidla zabezpečení sítě povolují jenom této komunikaci. V rámci důkladného přístupu k zabezpečení by měly být možnosti PaaS nakonfigurovány pomocí šifrování, ověřování a odpovídajících řízení přístupu a oprávnění.  

### <a name="public-ip-for-paas"></a>Veřejná IP adresa pro PaaS

Veřejné IP adresy pro PaaS možnosti jsou přidělovány na základě oblasti, kde je služba hostovaná nebo nasazená. Pokud chcete vytvořit příslušná pravidla zabezpečení sítě a topologii směrování pro segmentaci sítě a oddělení, které pokrývá Azure Virtual Networks, PaaS a ExpressRoute a Připojení k Internetu. Azure přiděluje IP adresy z fondu přiděleného každé oblasti Azure. Společnost Microsoft zpřístupňuje adresy používané v každé oblasti ke stažení, která se aktualizuje pravidelným a kontrolovaným způsobem. Služby, které jsou k dispozici v každé oblasti, se často mění, protože jsou vydávány nové služby nebo jsou služby nasazené v širším rozsahu. Entity Společenství by měly tyto materiály pravidelně kontrolovat a můžou pomocí automatizace spravovat systémy podle potřeby. Konkrétní IP adresy pro některé služby hostované v jednotlivých oblastech lze získat kontaktováním podpory společnosti Microsoft.

| Resource | Odkaz |
| --- | --- |
| *Rozsahy IP adres datacentra Microsoft Azure*                   | [https://www.microsoft.com/download/details.aspx?id=41653](https://www.microsoft.com/download/details.aspx?id=41653) |
| *Služby Azure na oblast*                              | [https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional, Austrálie – střed, Austrálie – střed – 2, Austrálie – východ, Austrálie – jihovýchod & Products = vše](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast&products=all) |
| *Příchozí a odchozí IP adresy v Azure App Service* | [https://docs.microsoft.com/azure/app-service/overview-inbound-outbound-ips](https://docs.microsoft.com/azure/app-service/overview-inbound-outbound-ips)
|

## <a name="next-steps"></a>Další postup

Porovnejte svou celkovou architekturu a návrh s publikovanými [chráněnými plány pro webové aplikace IaaS a PaaS](https://aka.ms/au-protected).
