---
title: Síťové funkce
description: Přečtěte si o funkcích sítě v Azure App Service a seznamte se s funkcemi, které potřebujete pro zabezpečení nebo jiné funkce.
author: ccompy
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.topic: article
ms.date: 10/18/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 730f26039db0f5441563ac7bf5d6b0ab536cbcd2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99593125"
---
# <a name="app-service-networking-features"></a>Funkce App Service sítě

Aplikace můžete nasadit v Azure App Service několika způsoby. Ve výchozím nastavení jsou aplikace hostované v App Service dostupné přímo přes Internet a můžou se dostat jenom k koncovým bodům hostovaným v Internetu. Ale u mnoha aplikací je potřeba řídit příchozí a odchozí síťový provoz. V App Service je k dispozici několik funkcí, které vám pomůžou tyto požadavky splnit. Výzva má za úkol vědět, která funkce se má použít k vyřešení daného problému. Tento článek vám pomůže určit, která funkce se má použít, na základě některých příkladů případů použití.

Existují dva hlavní typy nasazení pro Azure App Service: 
- Veřejná služba víceklientské architektury je hostitelem App Service plánů v cenových jednotkách Free, Shared, Basic, Standard, Premium, PremiumV2 a PremiumV3. 
- Jeden tenant App Service Environment (pomocného modulu) hostuje izolované SKU App Service plány přímo ve vaší virtuální síti Azure. 

Funkce, které použijete, budou záviset na tom, jestli jste ve víceklientské službě nebo v pomocném mechanismu. 

## <a name="multitenant-app-service-networking-features"></a>Funkce víceklientské App Service sítě 

Azure App Service je distribuovaný systém. Role, které zpracovávají příchozí požadavky HTTP nebo HTTPS, se nazývají *front-endy*. Role, které hostují úlohy zákazníka, se nazývají *pracovní procesy*. Všechny role ve App Service nasazení existují ve víceklientské síti. Vzhledem k tomu, že existuje mnoho různých zákazníků ve stejné App Service jednotce škálování, nemůžete připojit síť App Service přímo do vaší sítě. 

Místo připojení sítí potřebujete funkce pro zpracování různých aspektů komunikace s aplikacemi. Funkce, které zpracovávají požadavky *na* vaši aplikaci, se nedají použít k řešení problémů při volání *z* vaší aplikace. Podobně funkce, které řeší problémy pro volání z vaší aplikace, se nedají použít k řešení problémů s vaší aplikací.  

| Příchozí funkce | Odchozí funkce |
|---------------------|-------------------|
| Adresa přiřazená aplikacím | Hybridní připojení |
| Omezení přístupu | Brána – požadovaná integrace virtuální sítě |
| Koncové body služby | Integrace virtuální sítě |
| Privátní koncové body ||

Kromě popsaných výjimek můžete všechny tyto funkce použít dohromady. Můžete kombinovat funkce a vyřešit problémy.

## <a name="use-cases-and-features"></a>Případy použití a funkce

U každého daného případu použití může být několik způsobů, jak problém vyřešit. Výběr nejlepší funkce někdy nabývá mimo případ použití. Následující případy příchozího použití ukazují, jak používat funkce App Service sítě k řešení problémů s řízením provozu, který do vaší aplikace směřuje:
 
| Případ příchozího použití | Funkce |
|---------------------|-------------------|
| Podpora protokolu SSL založeného na protokolu IP pro vaši aplikaci | Adresa přiřazená aplikacím |
| Podpora nesdílené vyhrazené příchozí adresy pro vaši aplikaci | Adresa přiřazená aplikacím |
| Omezení přístupu k aplikaci ze sady dobře definovaných adres | Omezení přístupu |
| Omezení přístupu k aplikaci z prostředků ve virtuální síti | Koncové body služby </br> INTERNÍHO NÁSTROJE POMOCNÉHO MECHANISMU </br> Privátní koncové body |
| Vystavení aplikace na privátní IP adrese ve vaší virtuální síti | INTERNÍHO NÁSTROJE POMOCNÉHO MECHANISMU </br> Privátní koncové body </br> Privátní IP adresa pro příchozí provoz na instanci Application Gateway s koncovými body služby |
| Ochrana aplikace pomocí firewallu webových aplikací (WAF) | Application Gateway a interního nástroje pomocného mechanismu </br> Application Gateway s privátními koncovými body </br> Application Gateway s koncovými body služby </br> Přední dvířka Azure s omezeními přístupu |
| Vyrovnávání zatížení aplikací v různých oblastech | Přední dvířka Azure s omezeními přístupu | 
| Vyrovnávat zatížení provozu ve stejné oblasti | [Application Gateway s koncovými body služby][appgwserviceendpoints] | 

Následující případy odchozího použití ukazují, jak používat funkce App Service sítě k řešení požadavků na odchozí přístup k vaší aplikaci:

| Případ odchozího použití | Funkce |
|---------------------|-------------------|
| Přístup k prostředkům ve virtuální síti Azure ve stejné oblasti | Integrace virtuální sítě </br> ASE |
| Přístup k prostředkům ve virtuální síti Azure v jiné oblasti | Brána – požadovaná integrace virtuální sítě </br> Pomocného mechanismu pro vytváření a virtuální sítě |
| Přístup k prostředkům zabezpečeným pomocí koncových bodů služby | Integrace virtuální sítě </br> ASE |
| Přístup k prostředkům v privátní síti, která není připojená k Azure | Hybridní připojení |
| Přístup k prostředkům přes okruhy Azure ExpressRoute | Integrace virtuální sítě </br> ASE | 
| Zabezpečení odchozího provozu z vaší webové aplikace | Integrace virtuální sítě a skupiny zabezpečení sítě </br> ASE | 
| Směrování odchozího provozu z vaší webové aplikace | Integrace virtuální sítě a směrovací tabulky </br> ASE | 


### <a name="default-networking-behavior"></a>Výchozí chování sítě

Jednotky škálování Azure App Service podporují mnoho zákazníků v každém nasazení. Bezplatné a sdílené SKU plánuje hostování zákaznických úloh u víceklientské pracovní procesy. Plány Basic a vyšší plánuje hostování zákaznických úloh, které jsou vyhrazeny pouze jednomu App Service plánu. Pokud máte plán Standard App Service, všechny aplikace v tomto plánu se spustí na stejném pracovním procesu. Při horizontálním navýšení kapacity pracovního procesu se všechny aplikace v tomto plánu App Service replikují na nového pracovního procesu pro každou instanci v plánu App Service. 

#### <a name="outbound-addresses"></a>Odchozí adresy

Pracovní virtuální počítače jsou v rámci App Service plánů rozdělené podle velkých částí. Všechny plány Free, Shared, Basic, Standard a Premium používají stejný typ virtuálního počítače Worker. Plán PremiumV2 používá jiný typ virtuálního počítače. PremiumV3 používá ještě jiný typ virtuálního počítače. Když změníte rodinu virtuálních počítačů, získáte jinou sadu odchozích adres. Pokud budete škálovat z úrovně Standard na PremiumV2, budou se vaše odchozí adresy měnit. Pokud budete škálovat z PremiumV2 na PremiumV3, budou se vaše odchozí adresy měnit. V některých starších jednotkách škálování se příchozí i odchozí adresy změní při škálování z úrovně Standard na PremiumV2. 

Pro odchozí hovory se používá několik adres. Odchozí adresy, které vaše aplikace používá k odchozím voláním, jsou uvedené ve vlastnostech vaší aplikace. Tyto adresy sdílí všechny aplikace běžící na stejné rodině virtuálních počítačů pracovních procesů v nasazení App Service. Pokud chcete zobrazit všechny adresy, které může vaše aplikace používat v jednotce škálování, je k dispozici vlastnost s názvem `possibleOutboundAddresses` , která bude seznam zobrazovat. 

![Snímek obrazovky, který zobrazuje vlastnosti aplikace](media/networking-features/app-properties.png)

App Service má počet koncových bodů, které se používají ke správě služby.  Tyto adresy jsou publikovány v samostatném dokumentu a také ve `AppServiceManagement` značce služby IP. Tato `AppServiceManagement` značka se používá jenom v App Service prostředích, kde je potřeba tento provoz umožňovat. Příchozí adresy App Service jsou sledovány ve `AppService` značce služby IP. Neexistuje žádná značka služby IP, která obsahuje odchozí adresy, které používá App Service. 

![Diagram, který zobrazuje App Service příchozích a odchozích přenosů.](media/networking-features/default-behavior.png)

### <a name="app-assigned-address"></a>Adresa přiřazená aplikacím 

Funkce adresy přiřazené aplikacím je offshoot funkce protokolu SSL založené na protokolu IP. K němu přistupujete nastavením SSL s vaší aplikací. Tuto funkci můžete použít pro volání SSL na základě IP adresy. Můžete ji také použít k tomu, abyste aplikaci přizpůsobili, že má jenom adresu. 

![Diagram, který znázorňuje adresu přiřazenou aplikacím.](media/networking-features/app-assigned-address.png)

Když použijete adresu přiřazenou aplikacím, váš provoz dál projde stejné role front-endu, které zpracovávají veškerý příchozí provoz do App Service jednotky škálování. Adresa přiřazená vaší aplikaci ale používá pouze vaše aplikace. Případy použití pro tuto funkci:

* Podporuje pro vaši aplikaci protokol SSL založený na protokolu IP.
* Nastavte vyhrazenou adresu pro vaši aplikaci, která není sdílená.

Informace o nastavení adresy v aplikaci najdete v tématu [Přidání certifikátu TLS/SSL v Azure App Service][appassignedaddress]. 

### <a name="access-restrictions"></a>Omezení přístupu 

Omezení přístupu umožňují filtrovat *příchozí* požadavky. Akce filtrování probíhá na front-end rolích, které jsou od rolí pracovních procesů, kde běží vaše aplikace, v nadřazeném umístění. Vzhledem k tomu, že role front-endu jsou z pracovních procesů nadřazené, můžete si představit omezení přístupu jako ochranu na úrovni sítě pro vaše aplikace. 

Tato funkce umožňuje vytvořit seznam pravidel pro povolení a odepření, která se vyhodnocují v pořadí podle priority. Je podobný funkci skupiny zabezpečení sítě (NSG) v sítích Azure. Tuto funkci můžete použít v pomocném mechanismu nebo ve službě víceklientské služby. Když ho použijete s interního nástroje pomocným mechanismem nebo soukromým koncovým bodem, můžete omezit přístup z privátních bloků adres.
> [!NOTE]
> Pro každou aplikaci je možné nakonfigurovat až 512 pravidel omezení přístupu. 

![Diagram, který znázorňuje omezení přístupu.](media/networking-features/access-restrictions.png)

#### <a name="ip-based-access-restriction-rules"></a>Pravidla omezení přístupu podle IP adresy

Funkce omezení přístupu na základě IP adresy pomáhá při omezení IP adres, které se dají použít k přístupu k vaší aplikaci. Jsou podporovány adresy IPv4 i IPv6. Některé případy použití pro tuto funkci:
* Omezte přístup k aplikaci ze sady dobře definovaných adres. 
* Omezte přístup k provozu přes externí službu Vyrovnávání zatížení nebo jiná síťová zařízení se známými výstupními IP adresami. 

Informace o tom, jak tuto funkci povolit, najdete v tématu [Konfigurace omezení přístupu][iprestrictions].

> [!NOTE]
> Pravidla omezení přístupu podle IP adresy zpracovávají rozsahy adres virtuální sítě jenom v případě, že je vaše aplikace v App Service Environment. Pokud je vaše aplikace ve víceklientské službě, je potřeba pomocí [koncových bodů služby](../virtual-network/virtual-network-service-endpoints-overview.md) omezit provoz na výběr podsítí ve vaší virtuální síti.

#### <a name="access-restriction-rules-based-on-service-endpoints"></a>Pravidla omezení přístupu na základě koncových bodů služby 

Koncové body služby umožňují Uzamknout *příchozí* přístup k aplikaci, takže zdrojová adresa musí pocházet ze sady podsítí, které vyberete. Tato funkce funguje společně s omezeními přístupu k IP adresám. Koncové body služby nejsou kompatibilní se vzdáleným laděním. Pokud chcete používat vzdálené ladění s vaší aplikací, klient se nemůže nacházet v podsíti s povolenými koncovými body služby. Proces nastavení koncových bodů služby je podobný procesu nastavení omezení přístupu k IP adresám. Můžete vytvořit seznam povolených a zakázaných pravidel přístupu, který obsahuje veřejné adresy a podsítě ve vašich virtuálních sítích. 

Některé případy použití pro tuto funkci:

* Nastavte Aplikační bránu s aplikací pro uzamknutí příchozího provozu do vaší aplikace.
* Omezte přístup k vaší aplikaci na prostředky ve vaší virtuální síti. Tyto prostředky můžou zahrnovat virtuální počítače, služby ASE nebo i další aplikace, které používají integraci virtuální sítě. 

![Diagram, který znázorňuje použití koncových bodů služby s Application Gateway.](media/networking-features/service-endpoints-appgw.png)

Další informace o konfiguraci koncových bodů služby s vaší aplikací najdete v tématu [omezení přístupu Azure App Service][serviceendpoints].
#### <a name="access-restriction-rules-based-on-service-tags-preview"></a>Pravidla omezení přístupu na základě značek služby (Preview)
[Značky služeb Azure][servicetags] jsou dobře definované sady IP adres pro služby Azure. Značky služeb seskupují rozsahy IP adres používané v různých službách Azure a často jsou také dále vymezeny na konkrétní oblasti. Díky tomu můžete filtrovat *příchozí* provoz z konkrétních služeb Azure. 

Úplný seznam značek a další informace najdete v odkazu na značku služby výše. Informace o tom, jak tuto funkci povolit, najdete v tématu [Konfigurace omezení přístupu][iprestrictions].
#### <a name="http-header-filtering-for-access-restriction-rules-preview"></a>Filtrování hlaviček protokolu HTTP pro pravidla omezení přístupu (Preview)
Pro každé pravidlo omezení přístupu můžete přidat další filtrování hlaviček protokolu HTTP. To vám umožní podrobněji zkontrolovat příchozí požadavek a filtr na základě konkrétních hodnot hlaviček protokolu HTTP. Každé záhlaví může mít až 8 hodnot na jedno pravidlo. V tuto chvíli se podporuje následující seznam hlaviček protokolu http: 
* X-předané – pro
* X-předávaný-Host
* X – Azure – FDID
* X-FD – HealthProbe

K některým případům použití pro filtrování hlaviček protokolu HTTP patří:
* Omezení přístupu k provozu z proxy serverů předávajících název hostitele
* Omezení přístupu ke konkrétní instanci front-FDID pro Azure pomocí pravidla značek služby a omezením X-Azure-Header
### <a name="private-endpoint"></a>Privátní koncový bod

Privátní koncový bod je síťové rozhraní, které vám prostřednictvím privátního propojení Azure připojuje soukromě a bezpečně ke své webové aplikaci. Privátní koncový bod používá privátní IP adresu z vaší virtuální sítě a efektivně tak přináší webovou aplikaci do vaší virtuální sítě. Tato funkce je určena pouze pro *příchozí* toky do vaší webové aplikace.
Další informace najdete v tématu [použití privátních koncových bodů pro webovou aplikaci Azure][privateendpoints].

Některé případy použití pro tuto funkci:

* Omezte přístup k vaší aplikaci z prostředků ve virtuální síti. 
* Zveřejněte svou aplikaci na privátní IP adresu ve vaší virtuální síti. 
* Chraňte svoji aplikaci pomocí WAF.

Soukromé koncové body brání v exfiltrace dat, protože jediná věc, kterou můžete kontaktovat přes soukromý koncový bod, je aplikace, se kterou je nakonfigurovaná. 
 
### <a name="hybrid-connections"></a>Hybridní připojení

App Service Hybrid Connections umožňuje vašim aplikacím provádět *odchozí* volání do zadaných koncových bodů TCP. Koncový bod může být místní, ve virtuální síti nebo kdekoli, kde umožňuje odchozí provoz do Azure na portu 443. K použití této funkce je potřeba nainstalovat přenosového agenta s názvem Správce hybridního připojení na Windows Serveru 2012 nebo novějším. Správce hybridního připojení musí být schopné kontaktovat Azure Relay na portu 443. Správce hybridního připojení můžete stáhnout z uživatelského rozhraní App Service Hybrid Connections na portálu. 

![Diagram znázorňující tok Hybrid Connections sítě.](media/networking-features/hybrid-connections.png)

App Service Hybrid Connections je postaven na funkci Azure Relay Hybrid Connections. App Service používá specializovanou formu funkce, která podporuje pouze odchozí volání z vaší aplikace na hostitele a port TCP. Tento hostitel a port je potřeba přeložit jenom na hostiteli, na kterém je nainstalovaná Správce hybridního připojení. 

Když aplikace v App Service provede vyhledávání DNS na hostiteli a portu definovaném v hybridním připojení, přenos se automaticky přesměruje, aby procházel z hybridního připojení a z Správce hybridního připojení. Další informace najdete v tématu [App Service Hybrid Connections][hybridconn].

Tato funkce se běžně používá k těmto akcím:

* Přístup k prostředkům v privátních sítích, které nejsou připojené k Azure pomocí sítě VPN nebo ExpressRoute.
* Podpora migrace místních aplikací na App Service bez nutnosti přesunout podpůrné databáze.  
* Poskytněte přístup s vyšším zabezpečením na jednoho hostitele a port na hybridní připojení. Většina síťových funkcí otevřela přístup k síti. S Hybrid Connections můžete kontaktovat jenom jednoho hostitele a port.
* Pokrytí scénářů, na které se nevztahuje jiné metody odchozího připojení.
* Udělejte vývoj v App Service způsobem, který umožňuje aplikacím snadno používat místní prostředky. 

Vzhledem k tomu, že tato funkce umožňuje přístup k místním prostředkům bez příchozího otvoru brány firewall, je oblíbená pro vývojáře. Ostatní funkce pro odchozí App Service sítě souvisejí s Azure Virtual Network. Hybrid Connections nezávisí na přechodu přes virtuální síť. Dá se použít k širší škále síťových potřeb. 

Všimněte si, že App Service Hybrid Connections nezáleží na tom, co se vám právě dělá. Takže ho můžete použít pro přístup k databázi, webové službě nebo libovolnému soketu TCP v rámci sálového počítače. Tato funkce v podstatě odpojování paketů TCP. 

Hybrid Connections je oblíbená pro vývoj, ale používá se také v produkčních aplikacích. Je skvělé pro přístup k webové službě nebo databázi, ale není vhodná pro situace, které zahrnují vytváření mnoha připojení. 

### <a name="gateway-required-vnet-integration"></a>Brána – požadovaná integrace virtuální sítě 

Brána – požadovaná App Service Integration VNet umožňuje vaší aplikaci vytvářet *odchozí* požadavky do virtuální sítě Azure. Funkce funguje připojením hostitele, na kterém je aplikace spuštěná, do Virtual Network brány ve vaší virtuální síti pomocí sítě VPN typu Point-to-site. Když nakonfigurujete funkci, aplikace získá jednu z adres Point-to-site přiřazených k jednotlivým instancím. Tato funkce umožňuje přístup k prostředkům v klasických nebo Azure Resource Manager virtuálních sítích v libovolné oblasti. 

![Diagram, který znázorňuje bránu. integrace virtuální sítě je povinná.](media/networking-features/gw-vnet-integration.png)

Tato funkce řeší potíže s přístupem k prostředkům v jiných virtuálních sítích. Dá se použít i k připojení přes virtuální síť k jiným virtuálním sítím nebo místnímu prostředí. Nefunguje s ExpressRoutemi virtuálními sítěmi, ale funguje s sítěmi propojenými s VPN typu Site-to-site. Tato funkce se obvykle nedoporučuje používat z aplikace v App Service Environment (pomocného mechanismu), protože je už ve vaší virtuální síti. Případy použití pro tuto funkci:

* Přístup k prostředkům privátních IP adres ve virtuálních sítích Azure. 
* Přístup k prostředkům v místním prostředí, pokud existuje síť VPN typu Site-to-site. 
* Přístup k prostředkům v partnerských virtuálních sítích. 

Když je tato funkce povolená, bude aplikace používat server DNS, se kterým je nakonfigurovaná cílová virtuální síť. Další informace o této funkci najdete v tématu [App Service Integration VNet][vnetintegrationp2s]. 

### <a name="vnet-integration"></a>Integrace virtuální sítě

Brána – vyžadovaná integrace virtuální sítě je užitečná, ale neřeší problém přístupu k prostředkům napříč ExpressRoute. Aby bylo možné dosáhnout mezi připojeními ExpressRoute, je potřeba zajistit, aby aplikace mohly volat služby zabezpečené koncovým bodem služby. Tyto potřeby mohou splňovat i další funkce integrace virtuální sítě. 

Nová funkce integrace virtuální sítě umožňuje umístit back-end aplikaci do podsítě ve Správce prostředků virtuální síti ve stejné oblasti jako vaše aplikace. Tato funkce není k dispozici z App Service Environment, která je již ve virtuální síti. Případy použití pro tuto funkci:

* Přístup k prostředkům v Správce prostředků virtuálních sítích ve stejné oblasti.
* Přístup k prostředkům, které jsou zabezpečené pomocí koncových bodů služby. 
* Přístup k prostředkům, které jsou přístupné přes ExpressRoute nebo připojení VPN.
* Usnadňuje zabezpečení všech odchozích přenosů. 
* Vynutit tunelování všech odchozích přenosů. 

![Diagram, který znázorňuje integraci virtuální sítě.](media/networking-features/vnet-integration.png)

Další informace najdete v tématu [App Service Integration VNet][vnetintegration].

### <a name="app-service-environment"></a>App Service Environment 

App Service Environment (pomocným mechanismem) je nasazení Azure App Service jednoho tenanta, které běží ve vaší virtuální síti. Některé případy, jako je tato funkce:

* Přístup k prostředkům ve virtuální síti.
* Přístup k prostředkům napříč ExpressRoute.
* Zpřístupněte své aplikace pomocí soukromé adresy ve vaší virtuální síti. 
* Přístup k prostředkům napříč koncovými body služby. 

Pomocí pomocného mechanismu služeb nemusíte používat funkce, jako je integrace virtuální sítě nebo koncové body služby, protože služba pomocného mechanismu už je ve vaší virtuální síti. Pokud chcete získat přístup k prostředkům, jako je SQL nebo Azure Storage prostřednictvím koncových bodů služby, povolte koncové body služby v podsíti pomocného mechanismu. Pokud chcete získat přístup k prostředkům ve virtuální síti, nemusíte provádět žádnou další konfiguraci. Pokud chcete získat přístup k prostředkům v rámci ExpressRoute, už jste ve virtuální síti, a nemusíte nic konfigurovat na pomocném panelu nebo v aplikacích. 

Vzhledem k tomu, že aplikace v pomocném seznamu interního nástroje můžou být vystavené na privátní IP adrese, můžete snadno přidat zařízení WAF a zpřístupnit tak jenom aplikace, které chcete používat na internetu, a zajistit zabezpečení REST. Tato funkce může usnadnit vývoj vícevrstvých aplikací. 

Některé věci nejsou v současnosti možné z víceklientské služby, ale jsou možné z pomocného mechanismu. Tady je několik příkladů:

* Zpřístupněte své aplikace na privátní IP adrese.
* Zabezpečte veškerý odchozí provoz pomocí ovládacích prvků sítě, které nejsou součástí vaší aplikace.
* Hostování aplikací ve službě s jednou tenantů 
* Škálovat můžete až na mnoho dalších instancí, než je možné ve víceklientské službě. 
* Načtěte klientské certifikáty privátní certifikační autority pro použití vašimi aplikacemi s koncovými body zabezpečenými soukromou certifikační autoritou.
* Vynutit TLS 1,1 napříč všemi aplikacemi hostovanými v systému bez možnosti jejich zakázání na úrovni aplikace. 
* Poskytněte vyhrazenou výstupní adresu pro všechny aplikace ve službě pomocného mechanismu, které nejsou sdílené se zákazníky. 

![Diagram, který znázorňuje pomocného mechanismu řízení ve virtuální síti.](media/networking-features/app-service-environment.png)

Pomocného nástroje poskytuje nejlepší příběh kolem izolovaného a vyhrazeného hostování aplikací, ale zahrnuje i některé problémy se správou. Před použitím funkčního pomocného mechanismu můžete zvážit několik věcí:
 
 * Správce se spouští ve virtuální síti, ale má závislosti mimo virtuální síť. Tyto závislosti musí být povoleny. Další informace najdete v tématu [požadavky na síť pro App Service Environment][networkinfo].
 * Služba pomocného mechanismu se nebude škálovat hned jako víceklientské služby. Místo reaktivního škálování je potřeba odhadnout požadavky na škálování. 
 * Pomocného objektu pro čtení má vyšší náklady na front-end. Chcete-li získat z vašeho pomocného mechanismu it maximum, měli byste naplánovat velký počet úloh do jednoho pomocného mechanismu, ale nepoužívat ho pro malé úsilí.
 * Aplikace v pomocném okně nemůžou selektivně omezit přístup k některým aplikacím v pomocném formuláři a ne ostatním.
 * Pomocného nástroje je v podsíti a všechna síťová pravidla se vztahují na veškerý provoz do a z tohoto pomocného mechanismu. Pokud chcete přiřadit pravidla příchozího přenosu jenom pro jednu aplikaci, použijte omezení přístupu. 

## <a name="combining-features"></a>Kombinování funkcí 

Funkce zjištěné pro víceklientské služby se dají použít společně k řešení složitějších případů použití. Dva z častých případů použití jsou zde popsány, ale jsou zde pouze příklady. Tím, že pochopíte jednotlivé funkce, můžete splnit skoro všechny požadavky vaší systémové architektury.

### <a name="place-an-app-into-a-virtual-network"></a>Umístění aplikace do virtuální sítě

Je možné, že si zajímáte, jak aplikaci umístit do virtuální sítě. Pokud svou aplikaci vložíte do virtuální sítě, koncové body příchozího a odchozího připojení aplikace jsou v rámci virtuální sítě. Nejlepším způsobem, jak tento problém vyřešit, je pomocného mechanismu pro obnovení. Díky kombinování funkcí ale můžete v rámci víceklientské služby vyhovět většině vašich potřeb. Například můžete hostovat jenom intranetové aplikace s privátními příchozími a odchozími adresami:

* Vytvoření aplikační brány s privátními příchozími a odchozími adresami.
* Zabezpečení příchozího provozu do aplikace pomocí koncových bodů služby. 
* Používání nové funkce integrace virtuální sítě, takže back-end aplikace je ve vaší virtuální síti. 

Tento styl nasazení vám neposkytne vyhrazenou adresu pro odchozí přenosy na Internet nebo možnost Uzamknout veškerý odchozí provoz z vaší aplikace. Poskytne vám tak spoustu toho, co byste jinak získali pomocí pomocného mechanismu. 

### <a name="create-multitier-applications"></a>Vytváření vícevrstvých aplikací

Vícevrstvá aplikace je aplikace, ve které je back-endové aplikace API dostupné jenom z front-endové úrovně. Existují dva způsoby, jak vytvořit vícevrstvou aplikaci. Jak začít pomocí integrace virtuální sítě připojit front-end webovou aplikaci k podsíti ve virtuální síti. Tím umožníte, aby webová aplikace prováděla volání do vaší virtuální sítě. Jakmile je aplikace front-end připojená k virtuální síti, musíte se rozhodnout, jak uzamknout přístup k vaší aplikaci API. Další možnosti:

* Hostování front-endu i aplikace API ve stejném interního nástroje pomocném programu pro čtení a zpřístupnění aplikace front-end pro Internet pomocí aplikační brány.
* Hostování front-endu ve víceklientské službě a back-endu v interního nástroje pomocném modulu pro obnovení.
* Hostování front-endu i aplikace API ve víceklientské službě.

Pokud hostuje front-end i aplikaci API pro vícevrstvou aplikaci, můžete:

- Vystavení aplikace API pomocí privátních koncových bodů ve vaší virtuální síti:

  ![Diagram, který znázorňuje použití privátních koncových bodů v aplikaci se dvěma vrstvami.](media/networking-features/multi-tier-app-private-endpoint.png)

- Pomocí koncových bodů služby zajistěte, aby příchozí provoz do vaší aplikace API byl dostupný jenom z podsítě, kterou používá webová aplikace front-end:

  ![Diagram, který znázorňuje použití koncových bodů služby k zajištění zabezpečení aplikace.](media/networking-features/multi-tier-app.png)

Tady je několik důležitých informací, které vám pomohou rozhodnout, kterou metodu použít:

* Při použití koncových bodů služby stačí pouze zabezpečit provoz do vaší aplikace API do podsítě Integration. To pomáhá zabezpečit aplikaci API, ale můžete i nadále mít exfiltrace data z front-endové aplikace do jiných aplikací ve službě App Service.
* Při použití soukromých koncových bodů máte při hraní dvě podsítě, což zvyšuje složitost. Soukromý koncový bod je také prostředkem nejvyšší úrovně a přidává režijní náklady na správu. Výhodou použití privátních koncových bodů je, že nemáte možnost exfiltrace dat. 

Obě metody budou fungovat s více front-endy. V malém měřítku je použití koncových bodů služby jednodušší, protože jednoduše povolíte koncové body služby pro aplikaci API v klientské podsíti front-end Integration. Když přidáváte další aplikace front-end, je nutné upravit každou aplikaci API tak, aby zahrnovala koncové body služby s podsítí integrace. Pokud používáte soukromé koncové body, je složitější, ale po nastavení privátního koncového bodu nemusíte nic měnit v aplikacích API. 

### <a name="line-of-business-applications"></a>Obchodní aplikace

Obchodní aplikace (LOB) jsou interní aplikace, které nejsou běžně přístupné pro přístup z Internetu. Tyto aplikace jsou volány v rámci podnikových sítí, kde může být přístup striktně řízen. Pokud používáte interního nástroje pomocného uživatele, můžete snadno hostovat své obchodní aplikace. Pokud používáte víceklientské služby, můžete buď použít soukromé koncové body, nebo použít koncové body služby kombinované s aplikační bránou. Existují dva důvody, proč použít Aplikační bránu s koncovými body služby místo použití privátních koncových bodů:
* Pro obchodní aplikace potřebujete WAF ochranu.
* Chcete vyrovnávat zatížení s několika instancemi obchodních aplikací.

Pokud ani jeden z těchto požadavků neplatí, je lepší používat soukromé koncové body. Díky soukromým koncovým bodům dostupným v App Service můžete své aplikace zveřejnit na soukromých adresách ve vaší virtuální síti. Soukromý koncový bod, který umístíte do virtuální sítě, je možné dosáhnout napříč ExpressRoute a připojeními VPN. 

Konfigurace privátních koncových bodů zveřejňuje vaše aplikace na privátní adrese, ale budete muset nakonfigurovat DNS, aby se tato adresa dostala z místního prostředí. Aby tato konfigurace fungovala, musíte předávat Azure DNS privátní zóna, která obsahuje vaše soukromé koncové body na místní servery DNS. Azure DNS privátní zóny nepodporují předávání zóny, ale pro tento účel můžete podporovat předávání zóny pomocí serveru DNS. Šablona [služby pro dodávání DNS](https://azure.microsoft.com/resources/templates/301-dns-forwarder/) usnadňuje přeposílání Azure DNS privátní zóny na místní servery DNS.

## <a name="app-service-ports"></a>Porty App Service

Pokud provedete kontrolu App Service, najdete několik portů, které jsou vystaveny pro příchozí připojení. Neexistuje žádný způsob, jak zablokovat ani řídit přístup k těmto portům ve víceklientské službě. Tady je seznam vystavených portů:

| Použití | Port nebo porty |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  Správa | 454, 455 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  Vzdálené ladění sady Visual Studio  |  4020, 4022, 4024 |
|  Služba Nasazení webu | 8172 |
|  Použití infrastruktury | 7654, 1221 |

<!--Links-->
[appassignedaddress]: ./configure-ssl-certificate.md
[iprestrictions]: ./app-service-ip-restrictions.md
[serviceendpoints]: ./app-service-ip-restrictions.md
[hybridconn]: ./app-service-hybrid-connections.md
[vnetintegrationp2s]: ./web-sites-integrate-with-vnet.md
[vnetintegration]: ./web-sites-integrate-with-vnet.md
[networkinfo]: ./environment/network-info.md
[appgwserviceendpoints]: ./networking/app-gateway-with-service-endpoints.md
[privateendpoints]: ./networking/private-endpoint.md
[servicetags]: ../virtual-network/service-tags-overview.md