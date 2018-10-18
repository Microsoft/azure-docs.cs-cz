---
title: Důvěryhodná připojení k Internetu doprovodné materiály k Azure
description: Důvěryhodné pokyny k připojení k Internetu pro služby Azure a SaaS
services: security
author: dlapiduz
ms.assetid: 09511e03-a862-4443-81ac-ede815bdaf25
ms.service: security
ms.topic: article
ms.date: 06/20/2018
ms.author: dlap
ms.openlocfilehash: d52785dd7569560f4b6986080b14723762537ec8
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49388303"
---
# <a name="trusted-internet-connections-guidance"></a>Pokyny k důvěryhodné připojení k Internetu

Tento článek popisuje, jak instituce státní správy pomocí Microsoft Azure Government můžete dosáhnout souladu se standardem iniciativy důvěryhodné internetové připojení (PIŠKVORKY). Tento článek popisuje, jak využívat předplatné Azure Government prostřednictvím Azure infrastruktury jako služba (IaaS) a platformy Azure jako služba (PaaS) nabídek.

## <a name="trusted-internet-connections-overview"></a>Přehled důvěryhodných připojení k Internetu

Účelem PIŠKVORKY iniciativy je k optimalizaci a standardizovat zabezpečení jednotlivých externí připojení, které se používají orgány statní. Zásady je uvedeno v [Office pro správu a rozpočtu (OMB) protokolu M-08-05](https://georgewbush-whitehouse.archives.gov/omb/memoranda/fy2008/m08-05.pdf).

V listopadu 2007 navázat OMB PIŠKVORKY program zlepšit zabezpečení perimetru federální sítě a reakce na incidenty funkce. PIŠKVORKY slouží k analýze síťových přenosů všechny příchozí a odchozí .gov k identifikaci konkrétní podpisy a dat na základě vzoru. PIŠKVORKY získává anomálií chování, jako je aktivita botnetu. Instituce jsou vyžadováno konsolidovat svoje externí síťové připojení a směrovat všechny přenosy přes zařízení odhalování a prevenci neoprávněných vniknutí známá jako EINSTEIN. Zařízení se hostují v omezeném počtu koncových bodů sítě, které jsou označovány jako _důvěryhodné připojení k Internetu_.

Cílem PIŠKVORKY je pro instituce vědět:
- Kdo je v síti (oprávnění nebo neoprávněný přístup)?
- Když přistupuje vlastní síť a proč?
- Které prostředky jsou přístupné?

Všechny externí připojení agentura musí aktuálně směrovat přes PIŠKVORKY OMB schválené. Federální úřady mají povinnost účast v programu PIŠKVORKY jako poskytovatel přístup PIŠKVORKY (TICAP) nebo ve smluvní služby s jednou z hlavních vrstvy 1 poskytovatelé služeb. Tito poskytovatelé jsou označovány jako poskytovatelé spravované důvěryhodné Internet Protocol služby (MTIPS). PIŠKVORKY zahrnuje povinná pro chod firem, které provádí agency a MTIPS zprostředkovatele. V aktuální verzi PIŠKVORKY zjišťování neoprávněných vniknutí EINSTEIN verze 2 a EINSTEIN verze 3 accelerated (3A) nasazují se na každý TICAP a MTIPS zařízení pro prevenci neoprávněných vniknutí. Agentura naváže "protokolu o porozumění" se oddělení z vnitřní zabezpečení (DHS) k nasazení funkce EINSTEIN federální systémy.

Jako součást její odpovědnosti, aby síť .gov vyžaduje DHS informační kanály nezpracovaná data agentury net toku dat pro korelaci incidentů organizace Federální a provádění analýz s využitím specializované nástroje. DHS směrovačů poskytuje schopnost shromažďovat IP síťového provozu, protože zadá nebo ukončí rozhraní. Správci sítě můžete analyzovat data net toku určit zdroj a cíl provozu, třída služeb, a tak dále. NET toku dat je považován za "bez obsahu dat" podobný záhlaví, Zdrojová IP adresa, cílová IP adresa a tak dále. Bez obsahu dat umožňuje DHS Další informace o obsahu: kdo dělal co a jak dlouho.

Iniciativa zaměřená také obsahuje zásady zabezpečení, pokyny a architektur, které předpokládají na místní infrastrukturu. Jako instituce státní správy přesunout do cloudu, abyste dosáhli úspory nákladů, provozní efektivitu a inovace, implementace požadavky PIŠKVORKY může zpomalit síťový provoz. Díky tomu je omezena rychlostí a agilitou u vlády, které můžou uživatelé přístup k datům založené na cloudu.

## <a name="azure-networking-options"></a>Možnosti sítě Azure

Existují tři hlavní možnosti pro připojení ke službám Azure:

- Přímé připojení k Internetu: připojení ke službám Azure přímo prostřednictvím otevřené připojení k Internetu. Média a připojení jsou veřejné. Aplikace a šifrování na úrovni přenosu se pak mohli spoléhat na zajištění ochrany osobních údajů. Šířka pásma je omezená lokality připojení k Internetu. Více než jeden aktivní zprostředkovatel slouží k zajištění odolnosti proti chybám.
- Virtuální privátní sítě (VPN): připojení ke službě Azure virtual network soukromě s použitím brány VPN.
Médium je veřejné, protože prochází lokality standardní připojení k Internetu, ale připojení je v tunelové připojení k zajištění ochrany osobních údajů. Šířka pásma je omezená, v závislosti na zařízení VPN a zvolenou konfiguraci. Připojení point-to-site Azure jsou obvykle omezené na 100 MB/s a připojení site-to-site jsou omezené na 1,25 GB/s.
- Azure ExpressRoute: ExpressRoute je přímé připojení ke službám Microsoftu. Vzhledem k tomu, že připojení je prostřednictvím izolované Fibre channel, kterou může být připojení veřejných nebo privátních v závislosti na konfiguraci, která se používá. Šířka pásma je obvykle omezen na maximálně 10 GB/s.

Existuje několik způsobů pro splnění požadavků PIŠKVORKY příloha H (důležité informace o cloudu), jak je uvedeno v ministerstva vnitřní bezpečnosti společnosti, "Důvěryhodné internetové připojení (PIŠKVORKY) referenční architektury dokument, verze 2.0." V tomto článku najdete pokyny k DHS PIŠKVORKY se označuje jako **PIŠKVORKY 2.0**.

Povolit připojení z **ministerstvo nebo úřad USA (D/A)** Azure nebo Office 365, aniž by směrování provozu přes PIŠKVORKY D/A D/A musí používat šifrovaného tunelu nebo vyhrazené připojení k poskytovateli cloudové služby (CSP). Služby CSP můžete zajistit, že se připojení k D/A cloudovým prostředím nenabízí do veřejného Internetu přístup pracovníci s přímým přístupem agentura.

Office 365 je kompatibilní s H příloha PIŠKVORKY 2.0 s použitím obou ExpressRoute s [Microsoft Peering](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#expressroute-routing-domains) povolená nebo připojení k Internetu, který šifruje všechny přenosy pomocí protokolu TLS 1.2. D/A koncovým uživatelům v síti D/A můžete připojit prostřednictvím jejich agentura síť a infrastrukturu PIŠKVORKY prostřednictvím Internetu. Všechny vzdálený internetový přístup k Office 365 se zablokuje a směruje prostřednictvím. D/A můžete také připojit k Office 365 přes připojení ExpressRoute s Microsoft Peering (typ veřejného partnerského vztahu) povolena.  

Pouze Azure druhá možnost (VPN) a třetí možnost (ExpressRoute) můžete splňovat tyto požadavky při jejich použití v souvislosti se službami, které omezují přístup k Internetu.

![Microsoft důvěryhodné připojení k Internetu (PIŠKVORKY)](media/tic-diagram-a.png)

## <a name="azure-infrastructure-as-a-service-offerings"></a>Infrastruktury Azure jako nabídek služeb

Dodržování předpisů se zásadami HRÁČI s využitím Azure IaaS je poměrně jednoduché, protože Azure zákazníci spravovat svoje vlastní směrování virtuální sítě.

Hlavní nutnost pomoct zajistit dodržování předpisů s PIŠKVORKY referenční architekturu je Ujistěte se, že vaše virtuální síť je rozšířením privátní sítě D/A. Bude _privátní_ rozšíření, tato zásada vyžaduje žádný provoz nechte síti s výjimkou přes místní PIŠKVORKY síťové připojení. Tento proces se označuje jako _vynucené tunelování_. PIŠKVORKY dodržování předpisů procesu bude veškerý provoz z jakéhokoli systému v prostředí CSP prostřednictvím brány místní v síti vaší organizace na Internetu prostřednictvím PIŠKVORKY.

Dodržování předpisů Azure IaaS PIŠKVORKY je rozdělena na dva hlavní kroky:

- Krok 1: konfigurace.
- Krok 2: auditování.

### <a name="azure-iaas-tic-compliance-configuration"></a>Dodržování předpisů Azure IaaS PIŠKVORKY: Konfigurace

Ke konfiguraci CLS PIŠKVORKY architektury s Azure, musíte nejprve zakázat přímý přístup k Internetu pro vaši virtuální síť a pak vynucují internetový provoz přes místní síť.

#### <a name="prevent-direct-internet-access"></a>Zakázat přímý přístup k Internetu

Sítě Azure IaaS se provádí prostřednictvím virtuální sítě, které se skládají z podsítě, ke kterým jsou přidružené řadiče síťového rozhraní (NIC) virtuálních počítačů.

Nejjednodušší scénář, abyste podpořili dodržování PIŠKVORKY je, aby zajistil, že virtuální počítač nebo kolekci virtuálních počítačů, nemůžou připojit k externím prostředkům. Zajištění odpojení od externího sítí s použitím skupin zabezpečení sítě (Nsg). Pomocí skupin Nsg k řízení provozu na jeden nebo více síťových adaptérů nebo podsítě ve virtuální síti. Skupina zabezpečení sítě obsahuje pravidla pro řízení přístupu, která povolují nebo zakazují provoz na základě směru přenosu, protokolu, zdrojové adresy a portu a cílové adresy a portu. Pravidla NSG můžete kdykoli změnit a změny se použijí na všechny související instance. Další informace o tom, jak vytvořit skupinu zabezpečení sítě najdete v tématu [filtrování provozu sítě s použitím skupiny zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-create-nsg-arm-pportal).

#### <a name="force-internet-traffic-through-an-on-premises-network"></a>Vynucují internetový provoz přes místní síť

Azure automaticky vytvoří systémové trasy a přiřadí je ke každé podsíti ve virtuální síti. Nejde vytvořit nebo odstranit systémové trasy, ale můžete přepsat některé z nich vlastní trasy. Azure vytvoří výchozí systémové trasy pro každou podsíť. Azure Přidá volitelné výchozí trasy do konkrétních podsítí, nebo do všech podsítí, pokud použijete konkrétní možnosti Azure. Tento typ směrování zajistí:
- Provoz, který je určený ve virtuální síti probíhá v rámci virtuální sítě.
- Určený IANA privátních adresních prostorů, jako jsou 10.0.0.0/8 se zahodí, pokud jsou zahrnuté v adresním prostoru virtuální sítě.
- "Poslední" směrování 0.0.0.0/0 na koncový bod virtuální sítě internet.

![PIŠKVORKY vynucené tunelování](media/tic-diagram-c.png)

Veškerý provoz, které se zasílají virtuální sítě je potřeba směrovat přes místní připojení, a ujistěte se, že veškerý provoz prochází skrz D/A PIŠKVORKY. Vlastní trasy můžete vytvořit tak, že vytvoříte trasy definované uživatelem, nebo výměnou tras protokolu BGP (Border Gateway) mezi bránu místní sítě a službou Azure VPN gateway. Další informace o trasách definovaných uživatelem najdete v tématu [směrování provozu virtuální sítě: uživatelem definované trasy](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined). Další informace o protokolu BGP najdete v tématu [směrování provozu virtuální sítě: Border Gateway Protocol](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol).

#### <a name="add-user-defined-routes"></a>Přidání trasy definované uživatelem

Pokud používáte bránu virtuální sítě založené na směrování, můžete vynutit tunelové propojení v Azure. Přidání trasy definované uživatelem (UDR) v, který nastaví 0.0.0.0/0 provoz směrovat **dalšího segmentu směrování** brány virtuální sítě. Azure upřednostňuje trasy definované uživatelem přes trasy definované uživatelem systému. Je všechny nevirtuální síťový provoz směrován do brány virtuální sítě, které potom směrovat provoz do místní. Po definování uživatelem definovaná TRASA přidružte trasa s existující podsítí nebo nové podsítě v rámci všechny virtuální sítě v prostředí Azure.

![uživatelem definované trasy a PIŠKVORKY](media/tic-diagram-d.png)

#### <a name="use-the-border-gateway-protocol"></a>Použití protokolu Border Gateway Protocol

Pokud používáte ExpressRoute nebo brány virtuální sítě s povoleným protokolem BGP, protokol BGP je upřednostňovaný postup pro inzerování tras. Protokol BGP trasy inzerované 0.0.0.0/0 brány virtuální sítě ExpressRoute a s ohledem na BGP Ujistěte se, že výchozí trasa vztahuje na všechny podsítě v rámci virtuální sítě.

### <a name="azure-iaas-tic-compliance-auditing"></a>Dodržování předpisů Azure IaaS PIŠKVORKY: auditování

Azure nabízí několik způsobů, jak auditovat PIŠKVORKY dodržování předpisů.

#### <a name="view-effective-routes"></a>Zobrazení efektivních tras

Potvrďte, že se výchozí trasy šíří pozorováním _efektivní trasy_ pro konkrétní virtuální počítač, konkrétní síťové rozhraní nebo tabulky uživatelem definovaná trasa v [na webu Azure portal](https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-portal#diagnose-using-azure-portal) nebo v [ Prostředí Azure PowerShell](https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-powershell#diagnose-using-powershell). **Efektivní trasy** zobrazit relevantní uživatelem definované trasy protokolu BGP Inzerovat trasy a systémové trasy, které se vztahují k příslušné entitě, jak je znázorněno na následujícím obrázku:

![Efektivní trasy](media/tic-screen-1.png)

> [!NOTE]
> Efektivní trasy pro síťové rozhraní, nelze zobrazit, pokud síťového adaptéru je přidružený k běžícímu virtuálnímu počítači.

#### <a name="use-azure-network-watcher"></a>Azure Network Watcher můžete využít

Azure Network Watcher nabízí několik nástrojů, které auditování dodržování PIŠKVORKY. Další informace najdete v tématu [tento přehled o službě Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).

##### <a name="capture-network-security-group-flow-logs"></a>Zachycení protokoly toků skupin zabezpečení sítě 

Network Watcher můžete využít k zachycení síťové protokoly toku, který indikuje metadata, která obklopuje provozu IP. Protokoly toku network obsahovat zdrojové a cílové adresy cíle a další data. Tato data můžete použít pomocí protokolů z brány virtuální sítě, místní hraniční zařízení nebo PIŠKVORKY, monitorování, že veškerý provoz směruje na místě. 

## <a name="azure-platform-as-a-service-offerings"></a>Platformu Azure jako nabídek služeb

Služby Azure PaaS, jako je Azure Storage, jsou přístupné prostřednictvím adresy URL dostupné na Internetu. Každý, kdo má schválené přihlašovací údaje přístup k prostředku, jako je například účet úložiště, z libovolného místa a bez procházení PIŠKVORKY. Z tohoto důvodu mnoho státní správu nesprávně závěru, že nejsou kompatibilní se zásadami PIŠKVORKY služeb Azure PaaS. Mnoho služeb Azure PaaS, která může být PIŠKVORKY zásadám. Služba je kompatibilní s Když architektura je stejný jako prostředí vyhovující PIŠKVORKY IaaS ([jak už bylo popsáno](https://docs.microsoft.com/azure/security/compliance/compliance-tic#azure-infrastructure-as-a-service-offerings)) a služba je připojen ke službě Azure virtual network.

Pokud služby Azure PaaS jsou integrované s virtuální sítí, služba přístupný soukromě tuto virtuální síť. Můžete použít vlastní směrování 0.0.0.0/0 prostřednictvím uživatelem definované trasy a protokolu BGP. Vlastní směrování se zajistí, že směruje veškerý provoz směřující na internet k procházení PIŠKVORKY místní. Integrace služeb Azure, do virtuálních sítí s použitím následujících vzorů:

- **Nasazení vyhrazenou instanci služby**: rostoucí počet služeb PaaS, je možné nasadit jako vyhrazené instance s koncovými body virtuální připojeného k síti. V režimu "Izolované" Povolit koncový bod sítě pro omezené k virtuální síti můžete nasadit službu App Service Environment pro PowerApps. Hostujte řadou služeb Azure PaaS, jako je Azure Web Apps, Azure API Management a Azure Functions můžete službě App Service Environment.
- **Používání koncových bodů služby virtuální sítě**: rostoucí počet služeb PaaS povolit možnost přesunout jejich koncový bod na virtuální síti privátní IP adresu místo veřejné adresy.

Služby, které podporují nasazení vyhrazených instancích do virtuální sítě nebo pomocí koncových bodů služby od května 2018, jsou uvedeny v následujících tabulkách.

> [!Note]
> Stav dostupnosti odpovídá komerčně dostupné služby Azure. Stav dostupnosti služeb Azure ve službě Azure Government čeká na vyřízení.

### <a name="support-for-service-endpoints"></a>Podpora pro koncové body služby

|Služba                        |Dostupnost      |
|-------------------------------|------------------|
|Azure Key Vault                | Ve verzi Private preview  |
|Azure Cosmos DB                | Ve verzi Private preview  |
|Identity služby              | Ve verzi Private preview  |
|Azure Data Lake                | Ve verzi Private preview  |
|Azure Database for PostgreSQL  | Ve verzi Private preview  |
|Azure Database for MySQL       | Ve verzi Private preview  |
|Azure SQL Data Warehouse       | Veřejná verze Preview   |
|Azure SQL Database             | Obecné dostupnosti (GA) |
|Azure Storage                  | GA               |

### <a name="support-for-virtual-network-injection"></a>Podpora pro vkládání virtuální sítě

|Služba                               |Dostupnost      |
|--------------------------------------|------------------|
|Azure SQL Database Managed Instance   | Veřejná verze Preview   |
|Azure Kubernetes Service (AKS)        | Veřejná verze Preview   |
|Azure Service Fabric                  | GA               |
|Azure API Management                  | GA               |
|Azure Active Directory                | GA               |
|Azure Batch                           | GA               |
|App Service Environment               | GA               |
|Azure Redis Cache                     | GA               |
|Azure HDInsight                       | GA               |
|Škálovací sada virtuálních počítačů             | GA               |
|Azure Cloud Services                  | GA               |


### <a name="virtual-network-integration-details"></a>Podrobnosti integrace virtuální sítě

Následující diagram znázorňuje tok obecné sítě pro přístup ke službám PaaS. Přístup je zřejmé z virtuální sítě vkládání a tunelové propojení služeb virtuální sítě. Další informace o síťové služby brány virtuální sítě a značky služeb najdete v tématu [sítě a skupin zabezpečení aplikací: značky služeb](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

![Možnosti připojení PaaS PIŠKVORKY](media/tic-diagram-e.png)

1. Soukromé připojení k Azure pomocí ExpressRoute. Privátní partnerský vztah ExpressRoute se vynucené tunelování se používá k vynucení všechny přenosy virtuální sítě zákazníků prostřednictvím ExpressRoute a zpět na místní. Microsoft Peering není povinné.
2. Azure VPN Gateway, při použití ve spojení s ExpressRoute a Microsoft Peering můžete překryv začátku do konce šifrování protokolem IPSec mezi virtuální sítí zákazníka a na místní hraniční síti. 
3. Síťové připojení k virtuální síti zákazníka je řídit ho jde pomocí skupin zabezpečení sítě, které umožňují zákazníkům povolení/zákazu založené na protokolu IP, portu a protokolu.
4. Virtual network zákazníka rozšíří ve službě PaaS tak, že vytvoříte koncový bod služby pro služby zákazníka.
5. Koncový bod služby PaaS neoprávněnou **výchozí Zamítnout vše** a k povolení přístupu jenom z uvedených podsítí ve virtuální síti zákazníka. Výchozí hodnota zakazuje také zahrnuje připojení, které pocházejí z Internetu.
6. Další služby Azure, které potřebují přístup k prostředkům ve virtuální síti zákazníka by měla být:  
   - Nasadit přímo do virtuální sítě.
   - Selektivně povolené, na základě pokynů v příslušné službě Azure.

#### <a name="option-a-deploy-a-dedicated-instance-of-a-service-virtual-network-injection"></a>Odpověď: možnost Nasadit vyhrazenou instanci služby (vkládání virtuální sítě)

Vkládání virtuální sítě umožňuje zákazníkům nasadit selektivně vyhrazené instance dané Azure služby, jako je HDInsight, do své vlastní virtuální sítě. Instance služby, které jsou nasazené do vyhrazenou podsíť ve virtuální síti zákazníka. Vkládání virtuální sítě umožňuje přístup k prostředkům služby prostřednictvím Internetu jiných směrovatelné adres. Místní instance používat ExpressRoute nebo VPN typu site-to-site pro přímý přístup k instance služby prostřednictvím adresní prostor virtuální sítě, místo otevření brány firewall do veřejného Internetu adresního prostoru. Když vyhrazenou instanci je připojen na koncový bod, můžete použít stejné strategie jako u IaaS PIŠKVORKY dodržování předpisů. Výchozí směrování zajistí, že internetový provoz se přesměruje na bránu virtuální sítě, která je vázána pro místní. Dále můžete řídit příchozí a odchozí přístup pomocí skupin zabezpečení sítě pro danou podsíť.

![Přehled služby Virtual network vkládání](media/tic-diagram-f.png)

#### <a name="option-b-use-virtual-network-service-endpoints-service-tunnel"></a>Možnost B: použijte koncových bodech virtuální sítě (tunelové propojení služby)

Růst počtu víceklientské služby Azure nabízejí "koncových bodů služby." Koncové body služby je alternativní metoda pro integraci k virtuálním sítím Azure. Koncové body služeb virtuální sítě rozšiřují prostoru IP adres vaší virtuální sítě a její identitu vaší virtuální sítě ke službě přes přímé připojení. Provoz z virtuální sítě do služby Azure vždy zůstává v páteřní síti Azure. 

Po povolení koncového bodu služby pro službu omezit připojení služby k této virtuální síti pomocí zásad určeného službou. Kontroly přístupu se vynucují na platformě podle služeb Azure. Přístup k prostředku uzamčené je povolen pouze v případě požadavek pochází z povolených virtuální síť nebo podsíť, nebo z dvou IP adres, který slouží k identifikaci vaší místní provoz, pokud používáte ExpressRoute. Pomocí této metody můžete efektivně zabránit odchozího/příchozího provozu z přímo ukončení služby PaaS.

![Přehled koncových bodů služeb](media/tic-diagram-g.png)

## <a name="cloud-native-tools-for-network-situational-awareness"></a>Nástroje pro nativní pro cloud pro povědomí o situaci sítě

Azure poskytuje nástroje pro nativní pro cloud pomáhá zajistit, abyste měli povědomí o situaci, který je potřeba pochopit tok provozu tak vaší sítě. Nástroje se nevyžadují pro dodržování předpisů se zásadami PIŠKVORKY. Nástroje může významně zlepšit vaše možnosti zabezpečení.

### <a name="azure-policy"></a>Azure Policy

[Služba Azure Policy](https://azure.microsoft.com/services/azure-policy/) je služba Azure, který vaše organizace poskytuje lepší možnosti auditování a vynucování dodržování předpisů iniciativy. Azure Policy je aktuálně k dispozici ve verzi public preview služby Azure, které jsou komerčně dostupný. Služba Azure Policy ještě není k dispozici ve službě Azure Government. Zákazníkům naplánovat a otestovat jejich pravidla zásad Azure teď pro zajištění budoucí kompatibility PIŠKVORKY. 

Služba Azure Policy je cílená na úrovni předplatného. Tato služba poskytuje centralizovanou rozhraní, kde můžete provádět úlohy dodržování předpisů, včetně:
- Správa iniciativy
- Konfigurace definice zásad
- Audit dodržování předpisů
- Vynucování dodržování předpisů
- Správa výjimek

Spolu s mnoha integrované definice správci můžou určit vlastní definice vlastní s využitím jednoduchých šablon JSON. Společnost Microsoft doporučuje stanovení auditování přes vynucení, kde je to možné.

Následující ukázkové zásady je možné pro scénáře PIŠKVORKY dodržování předpisů:

|Zásada  |Ukázkový scénář  |Šablona  |
|---------|---------|---------|
|Vynuťte tabulky trasy definované uživatelem. | Ujistěte se, že výchozí trasu na všechny virtuální sítě odkazuje na bránou virtuální sítě schválené pro směrování na místní.    | Začínáme s tímto [šablony](https://docs.microsoft.com/azure/azure-policy/scripts/no-user-def-route-table). |
|Auditovat, jestli Network Watcher není povolená pro oblast.  | Ujistěte se sledovací proces sítě je povolena pro všechny oblasti.  | Začínáme s tímto [šablony](https://docs.microsoft.com/azure/azure-policy/scripts/net-watch-not-enabled). |
|Skupina zabezpečení sítě x v každé podsíti.  | Ujistěte se, že skupina zabezpečení sítě (nebo sadu schválených skupin zabezpečení sítě) s blokovány přenosy z Internetu používá do všech podsítí v každé virtuální síti. | Začínáme s tímto [šablony](https://docs.microsoft.com/azure/azure-policy/scripts/nsg-on-subnet). |
|Skupina zabezpečení sítě x na každý síťový adaptér | Ujistěte se, že skupina zabezpečení sítě s přenosy z Internetu blokované platí pro všechny síťové adaptéry u všech virtuálních počítačů. | Začínáme s tímto [šablony](https://docs.microsoft.com/azure/azure-policy/scripts/nsg-on-nic). |
|Použití schválené virtuální sítě pro síťová rozhraní virtuálního počítače.  | Ujistěte se, že jsou všechny síťové adaptéry na schválené virtuální sítě. | Začínáme s tímto [šablony](https://docs.microsoft.com/azure/azure-policy/scripts/use-approved-vnet-vm-nics). |
|Povolená umístění. | Ujistěte se, že všechny prostředky se nasadí do oblastí s kompatibilní virtuální sítě a konfigurace Network Watcheru.  | Začínáme s tímto [šablony](https://docs.microsoft.com/azure/azure-policy/scripts/allowed-locs). |
|Nepovolené typy prostředků, jako například **veřejných IP adresách protokolu**. | Zakázat nasazení typy prostředků, které nemají plán dodržování předpisů. Pomocí těchto zásad můžete zakázat nasazení prostředky veřejné IP adresy. Pravidla skupiny zabezpečení sítě můžete využít efektivně blokovat příchozí internetovou komunikaci, brání použití veřejných IP adres dále snižuje útoku.   | Začínáme s tímto [šablony](https://docs.microsoft.com/azure/azure-policy/scripts/not-allowed-res-type).  |

### <a name="network-watcher-traffic-analytics"></a>Network Watcher analýzy provozu

Network Watcher [traffic analytics](https://azure.microsoft.com/blog/traffic-analytics-in-preview/) využívají data protokolů toku a další protokoly poskytnout podrobný přehled síťový provoz. Data jsou užitečná pro auditování dodržování předpisů PIŠKVORKY a k identifikaci míst. Podrobný řídicí panel můžete použít na virtuální počítače, které komunikují s Internetem a získat podrobný přehled směrování PIŠKVORKY rychle obrazovku.

![Analýza provozu](media/tic-traffic-analytics-1.png)

Použití **geografické mapy** rychle zjistit, o fyzické cíle internetového provozu pro virtuální počítače. Můžete identifikovat a posouzení podezřelých míst nebo vzor změny:

![Geografické mapy](media/tic-traffic-analytics-2.png)

Použití **virtuální topologie sítě** k rychlému průzkumu existující virtuální sítě:

![Mapa topologie sítě](media/tic-traffic-analytics-3.png)

### <a name="network-watcher-next-hop-tests"></a>Network Watcher další směrování testy

Sítě v oblasti, které jsou monitorovány pomocí Network Watcher můžete provádět testy dalšího segmentu směrování. Na webu Azure Portal můžete zadat zdroj a cíl pro tok sítě vzorku pro Network Watcher k vyřešení cíl dalšího segmentu. Tento test spouštět virtuální počítače a ukázka internetových adres k zajištění cíl dalšího segmentu je Brána virtuální sítě očekávané.

![Další směrování testy](media/tic-network-watcher.png)

## <a name="conclusions"></a>Závěry

Můžete snadno nakonfigurovat přístup pro Microsoft Azure, Office 365 a Dynamics 365 umožňující v souladu s pokyny k PIŠKVORKY 2.0 příloha H, jako písemné a definovaná. května 2018. Společnost Microsoft si uvědomuje, že pokyny PIŠKVORKY se může změnit. Přejeme Microsoft pomáhá zákazníkům při vydání nové pokyny podle pokynů včas.

## <a name="appendix-trusted-internet-connections-patterns-for-common-workloads"></a>Dodatek: Důvěryhodné způsoby připojení k Internetu pro běžné úlohy

| Kategorie | Úloha | IaaS | Vyhrazené PaaS / virtuální sítě vkládání  | Koncové body služby  |
|---------|---------|---------|---------|--------|
| Compute | Azure virtual machines s Linuxem | Ano | | |
| Compute | Virtuální počítače Azure Windows | Ano | | |
| Compute | Škálovací sady virtuálních počítačů | Ano | | |
| Compute | Azure Functions | | App Service Environment | |
| Web a mobilní zařízení | Interní webové aplikace | | App Service Environment| |
| Web a mobilní zařízení | Interní mobilních aplikací | | App Service Environment | |
| Web a mobilní zařízení | Aplikace rozhraní API | | App Service Environment | |
| Containers | Azure Container Service | | | Ano |
| Containers | Azure Kubernetes Service (AKS) \* | | | Ano |
| Databáze | Azure SQL Database | | Spravovaná Instance Azure SQL Database \* | Azure SQL |
| Databáze | Azure Database for MySQL | | | Ano |
| Databáze | Azure Database for PostgreSQL | | | Ano |
| Databáze | Azure SQL Data Warehouse | | | Ano |
| Databáze | Azure Cosmos DB | | | Ano |
| Databáze | Azure Redis Cache | | Ano | |
| Úložiště | Azure Blob Storage | Ano | | |
| Úložiště | Soubory Azure | Ano | | |
| Úložiště | Azure Queue storage | Ano | | |
| Úložiště | Azure Table Storage | Ano | | |
| Úložiště | Azure Disk storage | Ano | | |

\* Chcete-li verzi Public preview ve službě Azure Government. května 2018.
