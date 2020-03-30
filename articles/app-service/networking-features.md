---
title: Síťové funkce
description: Přečtěte si o síťových funkcích ve službě Azure App Service a o funkcích, které potřebujete pro potřeby sítě pro zabezpečení nebo funkčnost.
author: ccompy
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.topic: article
ms.date: 03/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: bc3d35830d833c0223a400140c53e583d2f6ed37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475421"
---
# <a name="app-service-networking-features"></a>Síťové funkce služby App Service

Aplikace ve službě Azure App Service lze nasadit několika způsoby. Ve výchozím nastavení jsou hostované aplikace Služby App Service přímo přístupné internetu a mohou se dostat pouze k koncovým bodům hostovaným na internetu. Mnoho zákaznických aplikací však musí řídit příchozí a odchozí síťový provoz. Existuje několik funkcí, které jsou k dispozici ve službě App Service k uspokojení těchto potřeb. Úkolem je vědět, jaká funkce by měla být použita k vyřešení daného problému. Tento dokument je určen k pomoci zákazníkům určit, jaké funkce by měly být použity na základě některých příkladů případů použití.

Existují dva primární typy nasazení pro službu Azure App Service. K dispozici je veřejná služba s více tenanty, která hostuje plány služby App Service v cenových virtuálních prodejích Free, Shared, Basic, Standard, Premium a Premiumv2. Pak je tu jeden klient App Service Environment (ASE), který hostuje izolované plány služby SKU App Service přímo ve vaší virtuální síti Azure (VNet). Funkce, které používáte se bude lišit, pokud jste ve službě více klientů nebo ve službě ASE. 

## <a name="multi-tenant-app-service-networking-features"></a>Síťové funkce služby App Service pro více klientů 

Služba Azure App Service je distribuovaný systém. Role, které zpracovávají příchozí požadavky HTTP/HTTPS, se nazývají front-endy. Role, které jsou hostiteli zatížení zákazníka se nazývají pracovníci. Všechny role v nasazení služby App Service existují v síti s více tenanty. Vzhledem k tomu, že ve stejné jednotce škálování služby App Service existuje mnoho různých zákazníků, nelze síť služby App Service připojit přímo k síti. Namísto připojení sítí potřebujeme funkce pro zpracování různých aspektů aplikační komunikace. Funkce, které zpracovávají požadavky na vaši aplikaci, nelze použít k řešení problémů při volání z vaší aplikace. Stejně tak funkce, které řeší problémy s voláním z vaší aplikace, nelze použít k řešení problémů s vaší aplikací.  

| Příchozí funkce | Odchozí funkce |
|---------------------|-------------------|
| Adresa přiřazená aplikaci | Hybridní připojení |
| Omezení přístupu | Brána vyžaduje integraci virtuální sítě |
| Koncové body služby | Integrace virtuální sítě |

Není-li uvedeno jinak, všechny funkce lze použít společně. Můžete kombinovat funkce k vyřešení různých problémů.

## <a name="use-case-and-features"></a>Případ použití a funkce

Pro daný případ použití může být několik způsobů, jak problém vyřešit.  Správná funkce pro použití je někdy z důvodů, které přesahují pouze samotný případ použití. Následující případy příchozího použití naznačují, jak používat síťové funkce služby App Service k řešení problémů týkajících se řízení provozu do vaší aplikace. 
 
| Příchozí případy použití | Funkce |
|---------------------|-------------------|
| Podpora ip-založené SSL potřeby pro vaši aplikaci | přiřazená adresa aplikace |
| Nesdílená vyhrazená příchozí adresa pro vaši aplikaci | přiřazená adresa aplikace |
| Omezení přístupu k aplikaci ze sady přesně definovaných adres | Omezení přístupu |
| Omezení přístupu k mé aplikaci z prostředků ve virtuální síti | Koncové body služby </br> ILB ASE </br> Soukromý koncový bod (náhled) |
| Vystavit moji aplikaci na privátní IP ve virtuální síti | ILB ASE </br> privátní IP adresa pro příchozí na aplikační bránu s koncovými body služby </br> Koncový bod služby (náhled) |
| Ochrana aplikace pomocí WAF | Aplikační brána + ASE ILB </br> Application Gateway s koncovými body služby </br> Azure Front Door s omezením přístupu |
| Provoz vyvažuje zatížení aplikací v různých oblastech | Azure Front Door s omezením přístupu | 
| Provoz vyvažuje zatížení ve stejné oblasti | [Application Gateway s koncovými body služby][appgwserviceendpoints] | 

Následující případy odchozího použití naznačují, jak pomocí síťových funkcí služby App Service vyřešit potřeby odchozího přístupu pro vaši aplikaci. 

| Odchozí případy použití | Funkce |
|---------------------|-------------------|
| Přístup k prostředkům ve virtuální síti Azure ve stejné oblasti | Integrace virtuální sítě </br> Ase |
| Přístup k prostředkům ve virtuální síti Azure v jiné oblasti | Brána vyžaduje integraci virtuální sítě </br> Partnerský vztah ase a virtuální sítě |
| Přístup k prostředkům zabezpečeným koncovými body služby | Integrace virtuální sítě </br> Ase |
| Přístup k prostředkům v privátní síti, která není připojená k Azure | Hybridní připojení |
| Přístup k prostředkům napříč okruhy ExpressRoute | Integrace virtuální sítě </br> Ase | 
| Zabezpečení odchozího provozu z webové aplikace | Skupiny pro integraci virtuální sítě a zabezpečení sítě </br> Ase | 
| Směrování odchozího provozu z webové aplikace | Integrace virtuální sítě a tabulky směrování </br> Ase | 


### <a name="default-networking-behavior"></a>Výchozí síťové chování

Jednotky škálování služby Azure App Service podporují mnoho zákazníků v každém nasazení. Plány Free and Shared SKU hostují úlohy zákazníků u víceklientských pracovníků. Základní a vyšší plány jsou hostiteli úloh zákazníků, které jsou vyhrazeny pouze pro jeden plán služby App Service (ASP). Pokud jste měli standardní plán služby App Service, budou všechny aplikace v tomto plánu spuštěny na stejném pracovníkovi. Pokud navštováte kapacitu pracovníka, budou všechny aplikace v tomto asp replikovány na nového pracovníka pro každou instanci ve vašem asp. Pracovníci, které se používají pro Premiumv2 se liší od pracovníků používaných pro ostatní plány. Každé nasazení služby App Service má jednu IP adresu, která se používá pro všechny příchozí provoz do aplikací v tomto nasazení služby App Service. Existují však kdekoli od 4 do 11 adres používaných pro odchozí hovory. Tyto adresy jsou sdíleny všemi aplikacemi v tomto nasazení služby App Service. Odchozí adresy se liší v závislosti na různých typech pracovních pracovníků. To znamená, že adresy používané bezplatnými, sdílenými, základními, standardními a prémiovými aps se liší od adres používaných pro odchozí volání z programů AsP Premiumv2. Pokud se podíváte do vlastností aplikace, uvidíte příchozí a odchozí adresy, které vaše aplikace používá. Pokud potřebujete uzamknout závislost pomocí protokolu ACL protokolu IP, použijte možné odchozí adresy. 

![Vlastnosti aplikace](media/networking-features/app-properties.png)

Služba App Service má řadu koncových bodů, které se používají ke správě služby.  Tyto adresy jsou publikovány v samostatném dokumentu a jsou také v servisní mj. Značka AppServiceManagement se používá pouze s prostředím služby App Service (ASE), kde je třeba povolit takový provoz. Příchozí adresy služby App Service jsou sledovány v servisní značce IP služby AppService. Neexistuje žádná značka služby IP, která obsahuje odchozí adresy používané službou App Service. 

![Příchozí a odchozí diagram služby App Service](media/networking-features/default-behavior.png)

### <a name="app-assigned-address"></a>Adresa přiřazená aplikaci 

Funkce přiřazené adresy aplikace je odnož funkce SSL založené na PROTOKOLU IP a je přístupná nastavením protokolu SSL pomocí aplikace. Tuto funkci lze použít pro volání SSL založené na protokolu IP, ale lze ji také použít k poskytnutí adresy, kterou má pouze aplikace. 

![Diagram adres přiřazený aplikaci](media/networking-features/app-assigned-address.png)

Když použijete adresu přiřazenou aplikaci, váš provoz stále prochází stejnými front-endovými rolemi, které zpracovávají všechny příchozí přenosy do jednotky škálování služby App Service. Adresa, která je přiřazená k vaší aplikaci, však používá jenom vaše aplikace. Případy použití této funkce jsou následující:

* Podpora ip-založené SSL potřeby pro vaši aplikaci
* Nastavení vyhrazené adresy pro vaši aplikaci, která není sdílena s ničím jiným

Můžete se dozvědět, jak nastavit adresu v aplikaci s kurzem konfigurace [IP založené SSL][appassignedaddress]. 

### <a name="access-restrictions"></a>Omezení přístupu 

Funkce Omezení přístupu umožňuje filtrovat **příchozí** požadavky na základě adresy IP původu. Akce filtrování probíhá na front-endrole, které jsou proti proudu od role pracovního procesu, kde jsou spuštěny vaše aplikace. Vzhledem k tomu, že role front-endu jsou před řazené od pracovníků, lze funkci Omezení přístupu považovat za ochranu na úrovni sítě pro vaše aplikace. Tato funkce umožňuje vytvořit seznam povolit a odepřít bloky adres, které jsou vyhodnocovány v pořadí podle priority. Je podobná funkci Skupiny zabezpečení sítě (NSG), která existuje v Azure Networking.  Tuto funkci můžete použít ve službě ASE nebo ve službě s více tenanty. Při použití se službou ASE ILB můžete omezit přístup ze soukromých adresových bloků.

![Omezení přístupu](media/networking-features/access-restrictions.png)

Funkce Omezení přístupu pomáhá ve scénářích, kde chcete omezit IP adresy, které se dá použít k přístupu k vaší aplikaci. Mezi případy použití této funkce patří:

* Omezení přístupu k aplikaci ze sady přesně definovaných adres 
* Omezte přístup k přístupu prostřednictvím služby vyrovnávání zatížení, jako jsou například Azure Front Door. Pokud jste chtěli uzamknout příchozí provoz na Azure Front Door, vytvořte pravidla pro povolení provozu od 147.243.0.0/16 a 2a01:111:2050::/44. 

![Omezení přístupu s předními dveřmi](media/networking-features/access-restrictions-afd.png)

Pokud chcete uzamknout přístup k vaší aplikaci tak, aby byla přístupná jenom z prostředků ve vaší virtuální síti Azure, potřebujete statickou veřejnou adresu na bez ohledu na váš zdroj ve vaší virtuální síti. Pokud prostředky nemají veřejnou adresu, měli byste místo toho použít funkci Koncové body služby. Naučte se, jak tuto funkci povolit pomocí kurzu [konfigurace omezení přístupu][iprestrictions].

### <a name="service-endpoints"></a>Koncové body služby

Koncové body služby umožňuje uzamknout **příchozí** přístup k vaší aplikaci tak, aby zdrojová adresa musí pocházet ze sady podsítí, které vyberete. Tato funkce funguje ve spojení s omezeními přístupu k protokolu IP. Koncové body služby jsou nastaveny ve stejném uživatelském prostředí jako omezení přístupu k IP. Můžete vytvořit seznam povolených a odepřených pravidel přístupu, který obsahuje veřejné adresy i podsítě ve virtuálních sítích. Tato funkce podporuje scénáře, jako jsou:

![koncové body služby](media/networking-features/service-endpoints.png)

* Nastavení aplikační brány pomocí aplikace pro uzamčení příchozího provozu do aplikace
* Omezení přístupu k aplikaci na prostředky ve virtuální síti. To může zahrnovat virtuální chod, ASEs nebo dokonce i jiné aplikace, které používají integraci virtuální sítě 

![koncové body služby s aplikační bránou](media/networking-features/service-endpoints-appgw.png)

Další informace o konfiguraci koncových bodů služby pomocí aplikace najdete v kurzu [o konfiguraci omezení přístupu ke koncovému bodu služby][serviceendpoints]

### <a name="private-endpoint-preview"></a>Soukromý koncový bod (náhled)

Private Endpoint je síťové rozhraní, které vás soukromě a bezpečně propojuje s webovou aplikací pomocí Azure Private Link. Private Endpoint používá privátní IP adresu z vaší virtuální sítě, efektivně přináší webovou aplikaci do virtuální sítě. Tato funkce je **jenom** pro příchozí toky do webové aplikace.
[Použití privátních koncových bodů pro Azure Web App (preview)][privateendpoints]
 
### <a name="hybrid-connections"></a>Hybridní připojení

Hybridní připojení služby App Service umožňuje vašim aplikacím provádět **odchozí** volání do určených koncových bodů TCP. Koncový bod může být místní, ve virtuální síti nebo kdekoli, který umožňuje odchozí provoz do Azure na portu 443. Tato funkce vyžaduje instalaci přenosového agenta nazývaného Hybrid Connection Manager (HCM) na hostiteli systému Windows Server 2012 nebo novějším hostiteli. HCM musí být možné dosáhnout Azure Relay na portu 443. HCM lze stáhnout z rozhraní App Service Hybridní připojení na portálu. 

![Tok sítě hybridních připojení](media/networking-features/hybrid-connections.png)

Funkce Hybridní připojení služby App Service je postavená na funkci hybridních připojení azure relay. Služba App Service používá specializovanou formu funkce, která podporuje jenom odchozí volání z vaší aplikace na hostitele tcp a port. Tento hostitel a port je třeba vyřešit pouze na hostiteli, kde je nainstalován HCM. Když aplikace ve službě App Service provede vyhledávání DNS na hostiteli a portu definovaném v hybridním připojení, provoz se automaticky přesměruje na hybridní připojení a ven z Nástroje pro hybridní připojení. Další informace o hybridních připojeních najdete v dokumentaci k [hybridním připojením služby App Service.][hybridconn]

Tato funkce se běžně používá k:

* Přístup k prostředkům v privátních sítích, které nejsou připojené k Azure pomocí VPN nebo ExpressRoute
* Podpora výtahu a přesunu místních aplikací do služby App Service, aniž byste museli také přesouvat podpůrné databáze  
* Bezpečně poskytněte přístup k jednomu hostiteli a portu na hybridní připojení. Většina síťových funkcí má otevřený přístup k síti a s hybridními připojeními máte pouze jednoho hostitele a port, ke kterým se dostanete.
* Zahrnují scénáře, které nejsou pokryty jinými metodami odchozího připojení
* Provádění vývoje ve službě App Service, kde aplikace mohou snadno využívat místní prostředky 

Vzhledem k tomu, že tato funkce umožňuje přístup k místním prostředkům bez vstupní díry brány firewall, je u vývojářů oblíbená. Ostatní odchozí app service síťové funkce jsou velmi související s virtuální sítí Azure. Hybridní připojení nemá závislost na procházení virtuální sítě a lze použít pro širší škálu potřeb sítě. Je důležité si uvědomit, že funkce Hybridní připojení služby App Service se nestará nebo víte, co děláte nad ním. To znamená, že jej můžete použít pro přístup k databázi, webové službě nebo libovolnému soketu TCP na sálovém počítači. Funkce v podstatě tunelové propojení pakety TCP. 

Zatímco hybridní připojení je populární pro vývoj, to je také používáno v mnoha výrobních aplikacích stejně. Je to skvělé pro přístup k webové službě nebo databázi, ale není vhodné pro situace zahrnující vytváření mnoha připojení. 

### <a name="gateway-required-vnet-integration"></a>Brána vyžaduje integraci virtuální sítě 

Funkce integrace virtuální sítě služby App Service umožňuje vaší aplikaci vytvářet **odchozí** požadavky do virtuální sítě Azure. Tato funkce funguje tak, že připojíte hostitele, na kterého vaše aplikace běží, k bráně virtuální sítě ve virtuální síti pomocí vpn s bodem na místo. Při konfiguraci funkce aplikace získá jednu z adres point-to-site přiřazených každé instanci. Tato funkce umožňuje přístup k prostředkům v klasických virtuálních sítích nebo virtuálních sítích Správce prostředků v libovolné oblasti. 

![Brána vyžaduje integraci virtuální sítě](media/networking-features/gw-vnet-integration.png)

Tato funkce řeší problém přístupu k prostředkům v jiných virtuálních sítích a lze dokonce použít k připojení prostřednictvím virtuální sítě k jiným virtuálním sítím nebo dokonce k místním. Nefunguje s připojenými virtuálními sítěmi ExpressRoute, ale s připojenými sítěmi VPN site-to-site. Obvykle není vhodné používat tuto funkci z aplikace v prostředí služby App Service (ASE), protože služba ASE je již ve vaší virtuální síti. Případy použití, které tato funkce řeší, jsou:

* Přístup k prostředkům na privátních IP službách ve virtuálních sítích Azure 
* Přístup k místním prostředkům, pokud existuje síť VPN site-to-site 
* Přístup k prostředkům v partnerských virtuálních sítích 

Pokud je tato funkce povolená, bude vaše aplikace používat server DNS, se kterým je cílová virtuální síť nakonfigurovaná. Další informace o této funkci najdete v dokumentaci k [integraci virtuální sítě služby App Service][vnetintegrationp2s]. 

### <a name="vnet-integration"></a>Integrace virtuální sítě

Funkce integrace virtuální sítě je velmi užitečná, ale stále neřeší přístup k prostředkům přes ExpressRoute. Kromě nutnosti dosáhnout přes expressroute připojení, je potřeba pro aplikace, aby mohli volat do služby zabezpečené koncového bodu služby. Chcete-li vyřešit oba tyto další potřeby, byla přidána další funkce integrace virtuální sítě. Nová funkce integrace virtuální sítě umožňuje umístit back-end vaší aplikace do podsítě ve virtuální síti Správce prostředků ve stejné oblasti. Tato funkce není dostupná v prostředí služby App Service, které už je ve virtuální síti. Tato funkce umožňuje:

* Přístup k prostředkům ve virtuálních sítích Správce prostředků ve stejné oblasti
* Přístup k prostředkům, které jsou zabezpečené koncovými body služby 
* Přístup k prostředkům, které jsou přístupné přes expressroute nebo připojení VPN
* Zabezpečení veškerého odchozího provozu 
* Vynutit tunelování všech odchozích přenosů. 

![Integrace virtuální sítě](media/networking-features/vnet-integration.png)

Další informace o této funkci najdete v dokumentech na [app service virtuální sítě integrace][vnetintegration].

## <a name="app-service-environment"></a>App Service Environment 

Prostředí služby App Service (ASE) je jednoklientské nasazení služby Azure App Service, která běží ve vaší virtuální síti. ASE umožňuje případy použití, jako jsou:

* Přístup k prostředkům ve virtuální síti
* Přístup k prostředkům přes ExpressRoute
* Vystavit své aplikace se soukromou adresou ve virtuální síti 
* Přístup k prostředkům napříč koncovými body služby 

Se službou ASE není nutné používat funkce, jako je integrace virtuální sítě nebo koncové body služby, protože služba ASE je již ve vaší virtuální síti. Pokud chcete získat přístup k prostředkům, jako je SQL nebo Storage přes koncové body služby, povolte koncové body služby v podsíti služby ASE. Pokud chcete získat přístup k prostředkům ve virtuální síti, není vyžadována žádná další konfigurace.  Pokud chcete získat přístup k prostředkům přes ExpressRoute, jste už ve virtuální síti a není nutné nic konfigurovat na ase nebo aplikace uvnitř něj. 

Vzhledem k tomu, že aplikace ve službě ASE ILB mohou být vystaveny na privátní IP adrese, můžete snadno přidat zařízení WAF, abyste odhalili pouze aplikace, které chcete na internetu, a zbytek udrželv bezpečí. To je vhodné pro snadný vývoj vícevrstvých aplikací. 

Existují některé věci, které ještě nejsou možné ze služby více klientů, které jsou ze služby ASE. Patří mezi ně věci jako:

* Vystavit své aplikace na privátní IP adresu
* Zabezpečení veškerého odchozího provozu pomocí síťových ovládacích prvků, které nejsou součástí vaší aplikace 
* Hostování aplikací v jediné klientské službě 
* Škálování až na mnohem více instancí, než je možné ve službě s více klienty 
* Načtení privátních klientských certifikátů certifikační autority pro použití aplikacemi pomocí privátních koncových bodů zabezpečených certifikační autoritou 
* Vynutit TLS 1.1 ve všech aplikacích hostovaných v systému bez možnosti zakázat na úrovni aplikace 
* Zadejte vyhrazenou odchozí adresu pro všechny aplikace ve službě ASE, které nejsou sdíleny s žádnými zákazníky. 

![ASE ve virtuální síti](media/networking-features/app-service-environment.png)

ASE poskytuje nejlepší příběh kolem izolovaného a vyhrazeného hostingu aplikací, ale přichází s některými výzvami v oblasti správy. Některé věci, které je třeba zvážit před použitím operační služby ASE, jsou:
 
 * ASE běží uvnitř vaší virtuální sítě, ale má závislosti mimo virtuální síť. Tyto závislosti musí být povoleny. Další informace o [aspektech sítě pro prostředí služby App Service][networkinfo]
 * Služba ASE není okamžitě škálovat jako víceklientské služby. Je třeba předvídat škálování potřeby spíše než reaktivně škálování. 
 * ASE má vyšší počáteční náklady s ním spojené. Chcete-li získat co nejvíce z vaší ase, měli byste plánovat na uvedení mnoho úloh do jedné ase spíše než mít používá pro malé úsilí
 * Aplikace ve službě ASE nemůže omezit přístup k některým aplikacím ve službě ASE a ne k jiným.
 * Služba ASE je v podsíti a všechna pravidla sítě platí pro veškerý provoz do a z této služby ASE. Pokud chcete přiřadit pravidla příchozího provozu jenom pro jednu aplikaci, použijte omezení přístupu. 

## <a name="combining-features"></a>Kombinování funkcí 

Funkce uvedené pro službu více klientů lze společně použít k řešení složitějších případů použití. Dva z více běžných případů použití jsou popsány zde, ale jsou to jen příklady. Pochopením toho, co jednotlivé funkce dělají, můžete vyřešit téměř všechny potřeby architektury systému.

### <a name="inject-app-into-a-vnet"></a>Vložení aplikace do virtuální sítě

Běžným požadavkem je, jak umístit aplikaci do virtuální sítě. Vložení aplikace do virtuální sítě znamená, že příchozí a odchozí koncové body pro aplikaci jsou v rámci virtuální sítě. Služba ASE poskytuje nejlepší řešení k vyřešení tohoto problému, ale můžete získat většinu toho, co je potřeba ve službě s více tenanty kombinací funkcí. Intranetu můžete například hostovat pouze aplikace v síti intranet s privátními příchozími a odchozími adresami:

* Vytvoření aplikační brány s privátní příchozí a odchozí adresou
* Zabezpečení příchozího provozu do aplikace pomocí koncových bodů služby 
* Použijte novou integraci virtuální sítě, aby byl back-end vaší aplikace ve vaší virtuální síti. 

Tento styl nasazení by vám nedal vyhrazenou adresu pro odchozí provoz na internetu nebo vám nedal možnost uzamknout veškerý odchozí provoz z vaší aplikace.  Tento styl nasazení by vám hodně z toho, co byste jinak získat pouze se službou ASE. 

### <a name="create-multi-tier-applications"></a>Vytváření vícevrstvých aplikací

Vícevrstvá aplikace je aplikace, kde back-endové aplikace rozhraní API lze přistupovat pouze z front-endové vrstvy. Chcete-li vytvořit vícevrstvou aplikaci, můžete:

* Použití integrace virtuální sítě k připojení back-endu vaší webové aplikace front-end u podsítě ve virtuální síti
* Koncové body služby slouží k zabezpečení příchozího provozu do aplikace API pouze z podsítě používané vaší webovou aplikací front-end

![vícevrstvá aplikace](media/networking-features/multi-tier-app.png)

Můžete mít více front-endaplikace používat stejnou aplikaci rozhraní API pomocí integrace virtuální sítě z jiných front-endových aplikací a koncových bodů služby z aplikace rozhraní API s jejich podsítěmi.  

<!--Links-->
[appassignedaddress]: https://docs.microsoft.com/azure/app-service/configure-ssl-certificate
[iprestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[serviceendpoints]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[hybridconn]: https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections
[vnetintegrationp2s]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[vnetintegration]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[networkinfo]: https://docs.microsoft.com/azure/app-service/environment/network-info
[appgwserviceendpoints]: https://docs.microsoft.com/azure/app-service/networking/app-gateway-with-service-endpoints
[privateendpoints]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
