---
title: Důvěryhodné internetové připojení doprovodné materiály k Azure
description: Důvěryhodné internetové připojení pokyny pro služby Azure a SaaS
services: security
author: dlapiduz
ms.assetid: 09511e03-a862-4443-81ac-ede815bdaf25
ms.service: security
ms.topic: article
ms.date: 06/20/2018
ms.author: dlap
ms.openlocfilehash: cf24810c0aa414e751e55df163563f013c1a0081
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2018
ms.locfileid: "38969941"
---
# <a name="trusted-internet-connection-guidance"></a>Pokyny k důvěryhodné internetové připojení

## <a name="background"></a>Pozadí

Účelem této iniciativy důvěryhodné internetové připojení (PIŠKVORKY) je k optimalizaci a standardizovat zabezpečení jednotlivých externí síťové připojení aktuálně používané federální úřady USA. Zásady je uvedeno v OMB (Office pro správu a rozpočtu) [protokolu M-08-05](https://georgewbush-whitehouse.archives.gov/omb/memoranda/fy2008/m08-05.pdf).

V listopadu 2007 navázat OMB PIŠKVORKY program zlepšit zabezpečení perimetru federální sítě a reakce na incidenty funkce. PIŠKVORKY je navržená k analýze síťových přenosů všechny příchozí a odchozí .gov identifikovat konkrétní podpisy a dat na základě vzoru a odhalte anomálií chování, jako je aktivita botnetu. Instituce bylo vyžadováno konsolidovat svoje externí síťové připojení a veškerý provoz směrován přes neoprávněného vniknutí detekce a ochrany před únikem informací zařízení (označovanou jako EINSTEIN), které byly hostovány na omezený počet koncových bodů sítě (označované jako důvěryhodné Internet Připojení).

Jednoduše řečeno, cílem PIŠKVORKY je pro instituce vědět:
- Kdo je v síti (oprávnění nebo neoprávněný přístup)?
- Když je přistupováno vlastní síť a proč?
- Které prostředky jsou spuštěny?

Dnes, které všechny externí připojení agentura musí ho směrovat pomocí PIŠKVORKY OMB schválení. Federální instituce jsou nutné k účasti v programu PIŠKVORKY se jako zprostředkovatel přístup PIŠKVORKY (TICAP) nebo které smluvní služby s jednou z hlavních poskytovatelů služeb vrstvy 1 Internet označuje jako poskytovatelé spravované důvěryhodné Internet Protocol služby (MTIPS).  PIŠKVORKY zahrnuje povinná pro chod firem, které provádí ještě dnes agency a MTIPS zprostředkovatele. V aktuální verzi PIŠKVORKY zjišťování neoprávněných vniknutí EINSTEIN verze 2 a EINSTEIN verze 3 accelerated (3A) jsou nasazeny zařízení pro prevenci neoprávněných vniknutí pro každou TICAP a MTIPS a agentura vytváří protokolu o porozumění se oddělení Vnitřní zabezpečení (DHS) k nasazení funkce EINSTEIN federální systémy.

Jako součást její odpovědnosti, aby síť .gov vyžaduje DHS nezpracované datové kanály data agentury Netflow korelaci incidentů organizace Federální a provádět analýzy pomocí specializované nástroje. DHS směrovačů poskytuje schopnost shromažďovat IP síťového provozu, protože zadá nebo ukončí rozhraní. Díky analýze net toku dat, správce sítě určit věci, jako je například zdroj a cíl provozu, třídu služby atd. NET toku dat je považován za "bez obsahu dat" (například záhlaví, zdrojové IP, cílová IP adresa, atd.) a umožňuje DHS znát informace kolem obsahu; To znamená, kdo dělal co a jak dlouho.

Iniciativa zaměřená také obsahuje zásady zabezpečení, pokyny a architektur, které předpokládají na místní infrastrukturu. Jako instituce státní správy přesunout do cloudu, abyste dosáhli úspory nákladů, provozní efektivitu a inovace, implementace požadavky PIŠKVORKY se v některých případech zpomalování síťového provozu a omezení rychlosti a agility u vlády, které můžou uživatelé přístup ke svým datům založené na cloudu.

Tento článek popisuje, jak mohou státní úřady používat Microsoft Azure Government a dosáhnout tak dodržování předpisů se zásadami PIŠKVORKY napříč služby IaaS a PaaS.

## <a name="summary-of-azure-networking-options"></a>Souhrn možností sítě Azure

Při připojování ke službám Azure existují tři hlavní možnosti:

1. Přímé připojení k Internetu: připojení ke službám Azure přímo prostřednictvím otevřené připojení k Internetu. Médium je veřejná a také připojení. Aplikace a šifrování na úrovni přenosu se pak mohli spoléhat na zajištění ochrany osobních údajů. Lokality připojení k Internetu je omezena šířka pásma a víc poskytovatelů active lze použít k zajištění odolnosti proti chybám
1. Virtuální privátní síť: Připojení k službě Azure Virtual Network soukromě s použitím brány VPN.
Médium je veřejné, jako prochází lokality standardní připojení k Internetu, ale připojení se šifrují v tunelové připojení k zajištění ochrany osobních údajů. Šířka pásma je omezená, v závislosti na zařízení VPN a zvolenou konfiguraci. Připojení Azure Point-to-Site jsou obvykle omezené na 100 MB/s, zatímco připojení Site-to-Site jsou omezené na 1,25 GB/s.
1. Microsoft ExpressRoute: ExpressRoute je přímé připojení ke službám Microsoftu. Připojení je prostřednictvím izolované Fibre channel, kterou může být připojení veřejného nebo privátního podle konfigurace použít. Šířka pásma je obvykle omezen na maximálně 10 GB/s.

Existuje několik způsobů, jak požadavkům důvěryhodné internetové připojení příloha H (důležité informace o cloudu), které se nacházejí v ministerstva vnitřní bezpečnosti společnosti, "Důvěryhodné internetové připojení (PIŠKVORKY) referenční architektury dokument, verze 2.0". PIŠKVORKY DHS pokyny se v tomto dokumentu označovány jako PIŠKVORKY 2.0.

Povolit připojení z ministerstvo nebo úřad USA (D/A) do Azure nebo Office 365 bez směrování provozu přes PIŠKVORKY D/A D/A musí používat šifrovaného tunelu a/nebo vyhrazené připojení pro poskytovatele cloudových služeb (CSP). Služby CSP můžete zajistit, že nejsou do veřejného Internetu přístup pracovníci s přímým přístupem agentura nabízí připojení k D/A cloudovým prostředím.

O365 je kompatibilní s PIŠKVORKY 2.0 příloha H pomocí obou Express Route se [Microsoft Peering](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#expressroute-routing-domains) povolená nebo připojení k Internetu, který šifruje všechny přenosy pomocí protokolu TLS 1.2.  D/A koncovým uživatelům v síti D/A můžete připojit prostřednictvím jejich agentura síť a infrastrukturu PIŠKVORKY prostřednictvím Internetu. Všechny vzdálený internetový přístup k O365 je blokované a směrován přes agentura. D/A můžete také připojit k O365 přes připojení Express Route se partnerský vztah Microsoftu, což je typ veřejný partnerský vztah, povolené.  

Pouze Azure můžete možnosti 2 (VPN) a 3 (ExpressRoute) splňovat tyto požadavky, když se používají v souvislosti se službami, které omezují přístup k Internetu.

![Microsoft důvěryhodné internetové připojení (PIŠKVORKY) Diagram](media/tic-diagram-a.png)

## <a name="how-azure-infrastructure-as-a-service-offerings-can-help-with-tic-compliance"></a>Jak Azure Infrastructure as nabídky služeb vám může pomoct s PIŠKVORKY dodržování předpisů

V souladu se zásadami PIŠKVORKY pomocí IaaS je poměrně jednoduché, protože Azure zákazníci spravovat svoje vlastní směrování virtuální sítě.

Hlavní nutnost pomoct zajistit dodržování předpisů s PIŠKVORKY referenční architektury je Ujistěte se, že vaše virtuální síť (VNet) stane privátní rozšíření ministerstvo nebo úřad USA vaší sítě. Stát _privátní_ rozšíření, tato zásada vyžaduje žádný provoz přes síťové připojení PIŠKVORKY On-Premises nechte síti s výjimkou. Tento proces se označuje jako "Force tunelové propojení", který používá PIŠKVORKY dodržování předpisů, je proces směrování veškerý provoz z jakéhokoli systému v prostředí CSP přejít prostřednictvím místní brány v síti vaší organizace na Internetu prostřednictvím PIŠKVORKY.

Dodržování předpisů Azure IaaS PIŠKVORKY lze rozdělit na dva hlavní kroky:

1. Konfigurace
1. Auditování

### <a name="azure-iaas-tic-compliance-configuration"></a>Konfigurace Azure IaaS PIŠKVORKY dodržování předpisů

Pokud chcete nakonfigurovat PIŠKVORKY kompatibilní architektura s využitím Azure, budete muset nejprve zakázat přímý přístup k Internetu k virtuální síti a potom vynucují internetový provoz přes místní síť.

#### <a name="prevent-direct-internet-access"></a>Zabránit přístupu k Internetu s přímým přístupem

Sítě Azure IaaS se provádí prostřednictvím virtuálních sítí se skládá z podsítě, ke kterému jsou přidružené řadiče síťového rozhraní (NIC) virtuálních počítačů.

Nejjednodušší scénář, abyste podpořili dodržování PIŠKVORKY je, aby zajistil, že virtuální počítač nebo kolekci, nemůžou připojit k externím prostředkům. Odpojení od externího sítí si být jistí, pomocí skupin zabezpečení sítě (Nsg), které slouží k řízení provozu na jeden nebo více síťových adaptérů nebo podsítě ve virtuální síti. Skupina zabezpečení sítě obsahuje pravidla pro řízení přístupu, která povolují nebo zakazují provoz na základě směru přenosu, protokolu, zdrojové adresy a portu a cílové adresy a portu. Pravidla NSG můžete kdykoli změnit a změny se použijí na všechny související instance.  Další informace o tom, jak vytvořit skupinu zabezpečení sítě, najdete v článku [vytvoření NSG](https://docs.microsoft.com/azure/virtual-network/virtual-networks-create-nsg-arm-pportal).

#### <a name="force-internet-traffic-through-on-premises-network"></a>Vynucují internetový provoz přes místní síť

Azure automaticky vytvoří systémové trasy a přiřadí je ke každé podsíti ve virtuální síti. Systémové trasy nemůžete vytvořit ani odebrat systémové trasy, ale můžete přepsat některé z nich vlastní trasy. Azure vytvoří výchozí systémové trasy pro každou podsíť a přidá další volitelné výchozí trasy do konkrétních podsítí, nebo do všech podsítí, pokud použijete konkrétní možnosti Azure. Tato směrování zajistí, že provoz směřující ve virtuální síti probíhá v rámci virtuální sítě, IANA určené privátních adresních prostorů, jako je 10.0.0.0/8 nezahodí (Pokud není součástí adresního prostoru virtuální sítě) a "poslední" směrování 0.0.0.0/0 do koncového bodu ve virtuální síti Internet.

![PIŠKVORKY vynucené tunelování](media/tic-diagram-c.png)

Pokud chcete mít jistotu, že veškerý provoz prochází skrz PIŠKVORKY D/A, je potřeba směrovat přes připojení On-Premises všech přenosů z virtuální sítě.  Vlastní trasy můžete vytvořit buď vytváření tras definovaných uživatelem, nebo výměnou border gateway protocol (BGP) trasy mezi bránu místní sítě a brány virtuální sítě Azure. Další informace o trasách definovaných uživatelem najdete v https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined. Další informace o protokolu Border Gateway Protocol najdete také v https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol.

#### <a name="user-defined-routes"></a>Trasy definované uživatelem

Pokud používáte bránu založenou na směrování virtuální sítě, vynuceného tunelování lze provést v rámci Azure tak, že přidáte přenosem 0.0.0.0/0 nastavení uživatelem definovanou trasou (UDR) má být směrována na "Next-Hop" brány virtuální sítě. Azure upřednostňuje trasy definované uživatele přes systémové trasy definované, tak to bude mít za následek veškerý provoz virtuální síť je odeslání vaší brány virtuální sítě, které lze následně směruje na On-Premises. Po definování, musí být tato trasa definovaná uživatelem přidružené všech podsítí existující nebo nově vytvořené v rámci všech virtuálních sítí ve vašem prostředí Azure.

![uživatelem definované trasy a PIŠKVORKY](media/tic-diagram-d.png)

#### <a name="border-gateway-protocol"></a>Protokol BGP

Sítě v oblastech sledováno Network Watcher můžete provést testy "Dalšího segmentu směrování", umožňuje snadný přístup k zadávání zdrojové a cílové pro ukázkový tok sítě, pro kterou se Network Watcher rozpoznávání cíle "Dalšího segmentu směrování" založené na portálu. To můžete použít pro virtuální počítače a příklad internetové adresy tak, aby byl "dalšího segmentu směrování" ve skutečnosti brány virtuální sítě.

### <a name="azure-iaas-tic-compliance-auditing"></a>Další směrování sledovací proces sítě

Přístup k Microsoft Azure, Office 365 a Dynamics 365, dají se snadno konfigurovat umožňující v souladu s pokyny PIŠKVORKY 2.0 příloha H i písemné definované v květnu 2018.

#### <a name="effective-routes"></a>Efektivní trasy

Microsoft si je vědoma, že tyto pokyny se může změnit a bude endeavor pomáhá zákazníkům při vydání nové pokyny podle pokynů včas. Dodatek: PIŠKVORKY vzory pro běžné úlohy Vyhrazené PaaS / vkládání virtuální sítě

![prostřednictvím služby App Service Environment (ASE)](media/tic-screen-1.png)

**Interní webové aplikace

#### <a name="network-watcher"></a>Network Watcher

Interní mobilních aplikací  Azure Container Service (ACS)

##### <a name="network-security-groups-flow-logs"></a>Azure Container Service (AKS) * 

Azure SQL Database spravované Instance * *: Verzi public Preview ve službě Azure Government od. května 2018. **: Privátní verze Preview ve službě Azure Government od. května 2018. 

## <a name="how-azure-platform-as-a-service-offerings-can-help-with-tic-compliance"></a>Jak Azure Paas nabídky služeb vám může pomoct s PIŠKVORKY dodržování předpisů

Služby Azure PaaS, jako je Azure Storage jsou přístupná prostřednictvím adresy URL dostupné na Internetu. Každý, kdo má schválené přihlašovací údaje přístup k prostředku, jako je například účet úložiště, z libovolného místa a bez procházení PIŠKVORKY. Z tohoto důvodu mnoho státní správu nesprávně závěru, že nejsou kompatibilní s PIŠKVORKY zásady služby Azure PaaS. Mnoho služeb Azure PaaS ve skutečnosti může být vyhovující zásadám PIŠKVORKY pomocí stejnou architekturu jako vyhovující PIŠKVORKY IaaS prostředí je popsáno výše, pokud je možné připojit k virtuální síti (VNet). Integrace služeb Azure PaaS s virtuální sítí VNet Azure umožňuje službě soukromě přístupný z této virtuální síti a umožňuje vlastní směrování 0.0.0.0/0 uplatňovat prostřednictvím trasy definované uživatelem nebo protokolu BGP je zajistit, aby veškerý provoz směřující na Internet směrované On-Premises do Přechod PIŠKVORKY.  Některé služby Azure je možné integrovat do virtuální sítě s použitím následujících vzorů:

- **Nasazení vyhrazenou instanci služby**: rostoucí počet PaaS služby je možné nasadit jako vyhrazené instance s virtuální sítí připojených koncových bodů. Jako příklad App Service Environment (ASE) je možné nasadit v režimu "Izolovaný režim", umožní její koncový bod sítě pro omezené k virtuální síti. Hostujte řadou služeb Azure PaaS, jako jsou webové aplikace, rozhraní API a funkce může této službě ASE.
- **Koncové body služby virtuální sítě**: rostoucí počet služeb PaaS povolit možnost přesunout jejich koncový bod na virtuální síti privátní IP adresu místo veřejné adresy.

Služby, které podporují nasazení vyhrazených instancích do virtuálních sítí nebo koncovými body služby od května 2018 jsou uvedeny níže: * (dostupnosti představuje komerční Azure, Azure Government dostupnost čekající).

### <a name="service-endpoints"></a>Koncové body služeb

|Služba                   |Status            |
|--------------------------|------------------|
|Azure KeyVault            | Ve verzi Private Preview  |
|Databáze Cosmos                 | Ve verzi Private Preview  |
|Identita                  | Ve verzi Private Preview  |
|Azure Data Lake           | Ve verzi Private Preview  |
|SQL Postgress/Mysql       | Ve verzi Private Preview  |
|Azure SQL Data Warehouse  | Ve verzi Public Preview   |
|Azure SQL                 | GA               |
|Úložiště                   | GA               |

### <a name="vnet-injection"></a>Vkládání virtuální sítě

|Služba                            |Status            |
|-----------------------------------|------------------|
|Spravovaná Instance SQL               | Ve verzi Public Preview   |
|Service(AKS) kontejnerů Azure       | Ve verzi Public Preview   |
|Service Fabric                     | GA               |
|API Management                     | GA               |
|Azure Active Directory             | GA               |
|Azure Batch                        | GA               |
|SLUŽBA ASE                                | GA               |
|Redis                              | GA               |
|HDI                                | GA               |
|COMPUTE škálovací sadu virtuálních počítačů  | GA               |
|Cloudové služby COMPUTE              | GA               |

### <a name="vnet-integration-details"></a>Podrobnosti integrace virtuální sítě

Následující diagram vás provede obecný tok sítí pro přístup ke službám PaaS pomocí vkládání virtuální sítě a tunelové propojení služeb virtuální sítě.  Další informace o síťové služby brány virtuální sítě a značky služeb můžete najít zde https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags.

![Možnosti připojení PaaS PIŠKVORKY](media/tic-diagram-e.png)

1. Soukromé připojení k Azure pomocí ExpressRoute. Privátní partnerský vztah ExpressRoute se vynucené tunelování slouží k vynucení veškerý provoz virtuální sítě zákazníků zpět do místního přes ExpressRoute. Microsoft Peering se nevyžaduje.
2. Azure VPN Gateway používá ve spojení s využitím partnerského vztahu ExpressRoute Microsoft slouží k překrytí začátku do konce šifrování protokolem IPSec mezi virtuální sítí zákazníka a na místní hraniční síti. 
3. Síťové připojení k virtuální síti zákazníka je řízen pomocí skupiny zabezpečení sítě (NSG) umožňuje zákazníkům, k povolení/zákazu založené na protokolu IP, portu a protokolu.
4. Virtuální sítě zákazníků je rozšířit na službu PaaS tak, že vytvoříte koncový bod služby pro služby zákazníka.
5. Koncový bod služby PaaS neoprávněnou výchozí Zamítnout vše a pouze povolit přístup z uvedených podsítí ve virtuální síti zákazníka.  Výchozí hodnota odepřít také zahrnuje připojení z Internetu.
6. Žádné jiné služby Azure, které potřebují přístup k prostředkům v rámci virtuální sítě zákazníků by měla být:  
  a. Nasadit přímo do virtuální sítě  
  b. Povolené selektivně založené na pokyny z příslušné služby Azure.

#### <a name="option-1-dedicated-instance-vnet-injection"></a>Možnost 1: Vyhrazená Instance "Vkládání virtuální sítě"

Pomocí vkládání virtuální sítě zákazníci nasadit selektivně vyhrazené instance dané služby Azure, jako je HDInsight, do své vlastní virtuální sítě. Instance služby, které jsou nasazené do vyhrazenou podsíť ve virtuální síti zákazníka. Vkládání virtuální síť umožňuje prostředky služby byla přístupná prostřednictvím adres směrovatelný bez Internetu.  Místní instance můžete přistupovat tato instance služby prostřednictvím adresní prostor virtuální sítě přímo přes ExpressRoute nebo VPN typu Site-to-Site, místo otevření brány firewall do veřejného Internetu adresního prostoru. Pomocí vyhrazené instance připojené do koncového bodu můžete používat stejné strategie pro dodržování předpisů PIŠKVORKY IaaS, s výchozí směrování zajistit, že internetový provoz se přesměruje na bránu virtuální sítě mez pro On-Premises. Příchozí a odchozí přístup je možné dál řídit pomocí skupin zabezpečení sítě (Nsg) pro danou podsíť.

![Diagram s přehledem vkládání virtuální sítě](media/tic-diagram-f.png)

#### <a name="option-2-vnet-service-endpoints"></a>– Možnost 2: Koncové body služby virtuální sítě 

Růst počtu víceklientské služby Azure nabízejí funkce "Koncový bod služby", které se alternativní metoda pro integraci k virtuálním sítím Azure. Koncové body služby virtuální sítě rozšiřují adresní prostor vaší virtuální síti IP a identitu vaší virtuální sítě ke službě přes přímé připojení.  Provoz z virtuální sítě do služby Azure vždy zůstává v páteřní síti Azure. Po povolení koncového bodu služby pro službu, může být připojení ke službě s omezeným přístupem k této virtuální síti prostřednictvím zásad určeného službou. Kontroly přístupu se vynucují na platformě pomocí služby Azure a přístup k uzamčené prostředku je povolen pouze, pokud požadavek pochází z povolených virtuálních sítí/podsítí a/nebo dvě IP adresy sloužící k identifikaci vaší místní přenosu, pokud používáte ExpressRoute. To umožňuje efektivně zabránit příchozí/odchozí provoz z přímo ukončení služby PaaS.

![Diagram s přehledem koncové body služby](media/tic-diagram-g.png)

## <a name="using-cloud-native-tools-for-network-situational-awareness"></a>Použití nativních nástrojů cloud pro povědomí o situaci sítě

Azure poskytuje cloudové nativních nástrojů pro zajištění, že máte povědomí o musí pochopit tok provozu tak vaší sítě. Nejsou vyžadovány v souladu se zásadami PIŠKVORKY ale může významně zlepšit vaše možnosti zabezpečení.

### <a name="azure-policy"></a>Azure Policy

Služba Azure Policy (https://azure.microsoft.com/services/azure-policy/) je služba Azure, který vaše organizace poskytuje lepší možnosti auditování a vynucování dodržování předpisů iniciativy.  V současnosti ve verzi Public Preview v komerčních Cloudech Azure, ale ještě není v Microsoft Azure pro státní správu, zákazníkům kvalitní PIŠKVORKY a můžete spustit plánování testování jejich pravidla zásad pro zajištění budoucí kompatibility. Služba Azure Policy je zaměřený na úrovni předplatného a poskytuje centrální rozhraní pro správu iniciativ, definic zásad, auditování a vynucování výsledky a Správa výjimek. Kromě mnoha integrované definice zásad Azure správci můžou určit vlastní definice vlastní prostřednictvím jednoduchého json šablon. Společnost Microsoft doporučuje pro mnoho zákazníků, prioritní vyřizování auditování přes vynucení, kde je to možné.

Následující ukázkové zásady, mohou být užitečné pro scénáře PIŠKVORKY dodržování předpisů:

|Zásada  |Ukázkový scénář  |Výchozí šablony  |
|---------|---------|---------|
|Vynutit tabulky trasy definované uživatelem |     Ujistěte se, že výchozí trasu na všech bodech virtuální sítě směrem k schválené brány virtuální sítě pro směrování On Premises | https://docs.microsoft.com/azure/azure-policy/scripts/no-user-def-route-table |
|Auditovat, jestli Network Watcher není povolená pro oblast  | Ujistěte se sledovací proces sítě je povolena pro všechny oblasti  | https://docs.microsoft.com/azure/azure-policy/scripts/net-watch-not-enabled |
|NSG X ve všech podsítích  | Ujistěte se, že se použije skupiny zabezpečení sítě (nebo sadu schválených skupin zabezpečení sítě) s blokovány přenosy z Internetu do všech podsítí v každé virtuální síti | https://docs.microsoft.com/azure/azure-policy/scripts/nsg-on-subnet |
|NSG X ve všech síťových rozhraních | Ujistěte se, že skupina zabezpečení sítě s přenosy z Internetu blokované platí pro všechny síťové adaptéry pro všechny virtuální počítače. | https://docs.microsoft.com/azure/azure-policy/scripts/nsg-on-nic |
|Použití schválené virtuální sítě pro síťová rozhraní virtuálního počítače  | Ujistěte se, že jsou všechny síťové adaptéry na schválené virtuální sítě | https://docs.microsoft.com/azure/azure-policy/scripts/use-approved-vnet-vm-nics |
|Povolená umístění | Ujistěte se, že jsou všechny prostředky nasazené do oblastí s kompatibilní s konfigurací virtuální sítě a Network Watcher  | https://docs.microsoft.com/azure/azure-policy/scripts/allowed-locs |
|Nepovolené typy prostředků, jako je například veřejných IP adresách protokolu  | Zakázat nasazení typy prostředků, které nemají plán dodržování předpisů. Jako příklad tato zásada může zakázat nasazení prostředky veřejné IP adresy. Pravidla skupiny zabezpečení sítě můžete využít efektivně blokovat příchozí internetovou komunikaci, brání použití veřejných IP adres dále snižuje možný útok.    | https://docs.microsoft.com/azure/azure-policy/scripts/not-allowed-res-type  |

### <a name="azure-traffic-analyticshttpsazuremicrosoftcomen-inblogtraffic-analytics-in-preview"></a>Azure [analýzy provozu](https://azure.microsoft.com/en-in/blog/traffic-analytics-in-preview/)

Analýza provozu Azure Network Watcher spotřebovává data protokolů toku a další protokoly poskytnout podrobný přehled síťový provoz. Tato data můžou být užitečná pro auditování dodržování předpisů PIŠKVORKY a identifikaci míst. Podrobný řídicí panel je možné rychle obrazovku, která virtuálních počítačů komunikují pomocí Internetu, které by pak zadejte seznam cílené pro směrování PIŠKVORKY.

![Snímek obrazovky analýzy provozu](media/tic-traffic-analytics-1.png)

"Geografické mapy" je možné rychle identifikovat pravděpodobně fyzické cíle přenosy z Internetu pro virtuální počítače, umožňuje rozpoznávejte a Třiďte podezřelých míst nebo změny modelu.

![Snímek obrazovky analýzy provozu](media/tic-traffic-analytics-2.png)

Mapa topologie sítě lze použít k rychlému průzkumu existující virtuální sítě:

![Snímek obrazovky analýzy provozu](media/tic-traffic-analytics-3.png)

### <a name="azure-network-watcher-next-hop"></a>Další segment směrování Azure Network Watcher

Sítě v oblastech sledováno Network Watcher můžete provést testy "Dalšího segmentu směrování", umožňuje snadný přístup k zadávání zdrojové a cílové pro ukázkový tok sítě, pro kterou se Network Watcher rozpoznávání cíle "Dalšího segmentu směrování" založené na portálu. To můžete použít pro virtuální počítače a příklad internetové adresy tak, aby byl "dalšího segmentu směrování" ve skutečnosti brány virtuální sítě.

![Další směrování sledovací proces sítě](media/tic-network-watcher.png)

## <a name="conclusions"></a>Závěry

Přístup k Microsoft Azure, Office 365 a Dynamics 365, dají se snadno konfigurovat umožňující v souladu s pokyny PIŠKVORKY 2.0 příloha H i písemné definované v květnu 2018.  Microsoft si je vědoma, že tyto pokyny se může změnit a bude endeavor pomáhá zákazníkům při vydání nové pokyny podle pokynů včas.

## <a name="appendix-tic-patterns-for-common-workloads"></a>Dodatek: PIŠKVORKY vzory pro běžné úlohy

| Kategorie | Úloha | IaaS | Vyhrazené PaaS / vkládání virtuální sítě  | Koncové body služeb  |
|---------|---------|---------|---------|--------|
| Compute | Linux Virtual Machines | Ano | | |
| Compute | Windows Virtual Machines | Ano | | |
| Compute | Virtual Machine Scale Sets | Ano | | |
| Compute | Azure Functions | | prostřednictvím služby App Service Environment (ASE) | |
| Web a mobilní zařízení | Interní webové aplikace | | prostřednictvím služby App Service Environment (ASE) | |
| Web a mobilní zařízení | Interní mobilních aplikací | | prostřednictvím služby App Service Environment (ASE) | |
| Web a mobilní zařízení | API Apps | | prostřednictvím služby App Service Environment (ASE) | |
| Containers | Azure Container Service (ACS) | | | Ano |
| Containers | Azure Container Service (AKS) * | | | Ano |
| Databáze | SQL Database | | Azure SQL Database spravované Instance * | Azure SQL |
| Databáze | Azure Database for MySQL | | | Ano |
| Databáze | Azure Database for PostgreSQL | | | Ano |
| Databáze | SQL Data Warehouse | | | Ano |
| Databáze | Azure Cosmos DB | | | Ano |
| Databáze | Redis Cache | | Ano | |
| Úložiště | Objekty blob | Ano | | |
| Úložiště | Soubory | Ano | | |
| Úložiště | Fronty | Ano | | |
| Úložiště | Tabulky | Ano | | |
| Úložiště | Disky | Ano | | |

*: Verzi public Preview ve službě Azure Government od. května 2018.  
**: Privátní verze Preview ve službě Azure Government od. května 2018.

