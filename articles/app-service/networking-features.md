---
title: Sítě pro funkce nasazení – Azure App Service | Dokumentace Microsoftu
description: Jak používat síťové funkce různé služby App Service
author: ccompy
manager: stefsch
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 05/28/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 666430a11fb95871eb601b2a38eb7b97ad16119f
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66498946"
---
# <a name="app-service-networking-features"></a>Funkce sítě služby App Service

Aplikace ve službě Azure App Service můžete nasadit několika různými způsoby. Ve výchozím nastavení služba aplikace hostované aplikace jsou přímo přístup k Internetu a můžete pouze přístup internet hostované koncové body. Mnoho zákazníků však potřeba aplikace řídit příchozí a odchozí síťový provoz. K dispozici několik funkcí ve službě App Service splňovat tyto požadavky. Na výzvu je vědět, jaké funkce by měla sloužit k vyřešení daného problému. Tento dokument je určený pomáhá zákazníkům určit, jaké funkce by měla sloužit založené na případy použití.

Existují dva typy nasazení pro službu Azure App Service. Je víceklientská služba public, který je hostitelem plány služby App Service Free, Shared, Basic, Standard, Premium a Premiumv2 ceny skladových položek. Pak je jednoho tenanta aplikaci App Service Environment, který je hostitelem izolované SKU plány služby App Service přímo ve službě Azure Virtual Network (VNet). Závisí na funkce, které použijete, pokud jste ve službě více tenantů nebo ve službě ASE. 

## <a name="multi-tenant-app-service-networking-features"></a>Funkce sítě více tenanty služby App Service 

Azure App Service je distribuovaný systém. Role, které zpracovávají příchozí požadavky HTTP/HTTPS, se nazývají front endů. Role, které jsou hostiteli úloh zákazníka, se nazývají pracovní procesy. Všechny role v nasazení služby App Service existuje v síti pro více tenantů. Existuje mnoho různých zákazníků ve stejné jednotce škálování služby App Service, nelze sítě služby App Service připojit přímo k síti. Místo připojování sítě, potřebujeme funkce pro zpracování různých aspektů komunikaci aplikací. Funkce, které zpracovávají požadavky na vaši aplikaci nelze použít k řešení problémů při volání z vaší aplikace. Funkce, které řeší problémy pro volání z vaší aplikace, nelze použít k řešení problémů do vaší aplikace.  

| Příchozí funkce | Odchozí funkce |
|---------------------|-------------------|
| Aplikace přiřazena adresa | Hybridní připojení |
| Omezení přístupu | Brána vyžaduje integrace virtuální sítě |
| Koncové body služeb | Integrace virtuální sítě (preview) |

Pokud není uvedeno jinak, všechny funkce lze použít společně. Můžete kombinovat funkce k vyřešení různých problémů.

## <a name="use-case-and-features"></a>Případ použití a funkce

Pro případ použití daného může být několik způsobů, jak problém vyřešit.  Správné funkce je někdy z nějakého důvodu nad rámec jen samotné případ použití. Tyto případy použití příchozí navrhnout jak řešit problémy z hlediska řízení provoz směřující do vaší aplikace pomocí síťové funkce služby App Service. 
 
| Příchozí případy použití | Funkce |
|---------------------|-------------------|
| Podpora protokolu SSL na základě IP adresy, musí mít vaše aplikace | aplikace přiřazena adresa |
| Sdílí, ne vyhrazenou příchozí adresy pro vaši aplikaci | aplikace přiřazena adresa |
| Omezení přístupu k aplikaci ze sady dobře definovaných adres | Omezení přístupu |
| Zveřejnit své aplikace na privátních IP adres ve virtuální síti | SLUŽBA ASE S ILB </br> Application Gateway s koncovými body služby |
| Omezení přístupu k aplikaci z prostředků ve virtuální síti | Koncové body služeb </br> SLUŽBA ASE S ILB |
| Zveřejnit aplikaci na privátní IP adresu ve virtuální síti | SLUŽBA ASE S ILB </br> privátní IP adresu pro příchozí ve službě Application Gateway s koncovými body služby |
| Ochrana Moje aplikace s WAF | Služba Application Gateway a služba ASE s ILB </br> Application Gateway s koncovými body služby </br> Azure prahu díky omezení přístupu |
| Vyrovnávání zatížení provozu na Moje aplikace v různých oblastech | Azure prahu díky omezení přístupu | 
| Vyrovnávání zatížení provozu ve stejné oblasti | Application Gateway s koncovými body služby | 

Tyto případy použití odchozí navrhnout použití síťové funkce služby App Service k řešení požadavky na odchozích přístup pro vaši aplikaci. 

| Případy použití odchozí | Funkce |
|---------------------|-------------------|
| Přístup k prostředkům ve službě Azure Virtual Network ve stejné oblasti | Integrace virtuální sítě </br> SLUŽBA ASE |
| Přístup k prostředkům ve službě Azure Virtual Network v jiné oblasti | Brána vyžaduje integrace virtuální sítě </br> Služba ASE a partnerský vztah virtuální sítě |
| Přístup k prostředkům zabezpečené pomocí koncových bodů služby | Integrace virtuální sítě </br> SLUŽBA ASE |
| Přístup k prostředkům v privátní síti bez připojení k Azure | Hybridní připojení |
| Přístup k prostředkům napříč okruhy ExpressRoute | Integrace virtuální sítě (zatím omezeny adresy RFC 1918) </br> SLUŽBA ASE | 


### <a name="default-networking-behavior"></a>Výchozí chování sítě

Jednotky škálování Azure App Service podporují mnoho zákazníků v každém nasazení. Plány Free a SKU sdílené hostování úloh zákazníka v pracovních procesech více tenantů. Na úrovni Basic a vyšších úloh zákazníka hostitele plány, které jsou vyhrazené jenom jeden plán služby App Service (ASP). Pokud jste měli plán služby Standard aplikace, pak všechny aplikace v tomto plánu poběží ve stejné pracovní proces. Pokud horizontálním navýšením kapacity pracovního procesu, pak všechny aplikace v prostředí ASP se replikovat na nový pracovní proces pro všechny instance ve vaší ASP. Pracovní procesy, které se používají pro Premiumv2 se liší od zaměstnanců používá pro jiné plány. Každé nasazení služby App Service má jednu IP adresu, která se používá pro veškerý příchozí provoz do aplikací v tomto nasazení služby App Service. Existuje ale kdekoli od 4 do 11. používá se pro uskutečňování volání odchozí adresy. Tyto adresy jsou sdíleny ve všech aplikacích v tomto nasazení služby App Service. Odchozí adresy se liší v závislosti na typech jinému pracovnímu procesu. To znamená, že se liší od adresy používané pro odchozí volání z používalo Premiumv2 adresy používané službou Free, Shared, Basic, Standard a Premium používalo. Když se podíváte ve vlastnostech pro vaši aplikaci, uvidíte příchozí a odchozí adresy, které se používají ve vaší aplikaci. Pokud je potřeba zamknout závislost s seznamu ACL portu IP, použijte possibleOutboundAddresses. 

![Vlastnosti aplikace](media/networking-features/app-properties.png)

App Service má několik koncových bodů, které se používají ke správě služby.  Tyto adresy jsou publikovány v samostatných dokumentu a také jsou ve značce služby AppServiceManagement IP. AppServiceManagement značka se používá jenom s App Service Environment (ASE) Pokud potřebujete tyto přenosy. App Service, které příchozích adres jsou sledovány v značka služby AppService IP. Neexistuje žádná značka služby IP adresy, která obsahuje výstupní adresy používané službou App Service. 

![Příchozí a odchozí diagram služby App Service](media/networking-features/default-behavior.png)

### <a name="app-assigned-address"></a>Aplikace přiřazena adresa 

Přiřazenou adresu funkce aplikace je offshoot funkce založené na protokolu IP SSL a přístupu k nastavení protokolu SSL s vaší aplikací. Tuto funkci můžete použít pro volání založené na protokolu IP SSL, ale je také možné poskytnout adresu, která je jenom vaše aplikace. 

![Aplikace přiřazena adresa diagramu](media/networking-features/app-assigned-address.png)

Při použití aplikace přiřazena adresa provoz prochází stále stejné role front-endu, které zpracovávají veškerý příchozí provoz do jednotky škálování služby App Service. Pouze adres, který je přiřazen do vaší aplikace, ale používá vaši aplikaci. Případy použití pro tuto funkci je:

* Podpora protokolu SSL na základě IP adresy, musí mít vaše aplikace
* Nastavení vyhrazené adresy pro vaši aplikaci, kterou nebude sdílet s cokoliv jiného

Můžete informace o nastavení adresu vaší aplikace v rámci tohoto kurzu na [SSL na základě konfigurace IP adresy][appassignedaddress]. 

### <a name="access-restrictions"></a>Omezení přístupu 

Funkce umožňuje omezení přístupu, filtrovat **příchozí** žádosti na základě vzniku IP adresy. Akce filtrování probíhá na front-endu role, které jsou v rolích pracovního procesu nadřazeného kde vaše aplikace běží. Protože jsou front-endu role nadřazeného od pracovníků, možnost omezení přístupu lze považovat za úroveň ochrany sítě pro vaše aplikace. Tato funkce vám umožní vytvářet seznam povolit a zakázat bloky adres, které jsou vyhodnocovány v pořadí podle priority. Je to podobné funkce Skupina zabezpečení sítě (NSG), která existuje v síti Azure.  Tato funkce slouží ve službě ASE nebo službou s více tenanty. Při použití s ILB ASE, můžete omezit přístup z bloků privátních adres.

![Omezení přístupu](media/networking-features/access-restrictions.png)

Omezení přístupu funkce vám pomůže v situacích, ve které chcete omezit IP adresy, které můžou být použité k dosažení vaší aplikace. Mezi použití jsou případy pro tuto funkci:

* Omezení přístupu k aplikaci ze sady dobře definovaných adres 
* Omezení přístupu k přes něj procházejí služby Vyrovnávání zatížení, jako je Azure branou. Pokud chcete zamezit příchozí provoz do Azure branou, vytvořit pravidla, která umožňují provoz z 147.243.0.0/16 a 2a01:111:2050:: / 44. 

![Omezení přístupu s branou](media/networking-features/access-restrictions-afd.png)

Pokud chcete zamezit přístup k vaší aplikaci tak, aby ji pouze dosažitelný z prostředků ve službě Azure Virtual Network (VNet), potřebujete statickou veřejnou adresu, na cokoli, co je zdrojem ve vaší virtuální síti. Pokud prostředky nemají veřejnou adresu, by měl místo toho použít funkci koncových bodů služby. Zjistěte, jak povolit tuto funkci v tomto kurzu na [konfigurace omezení přístupu][iprestrictions].

### <a name="service-endpoints"></a>Koncové body služby

Koncové body služby umožňuje uzamknout **příchozí** přístup do vaší aplikace tak, aby zdrojové adresy musí pocházet ze sady podsítě, které jste vybrali. Tato funkce funguje ve spojení s omezením přístupu IP. Koncové body služby se nastavují v stejné prostředí pro uživatele jako omezení IP adres přístup. Můžete vytvářet povolit nebo zamítnout seznam pravidel přístupu, který zahrnuje veřejných adres a podsítí ve virtuálních sítích. Tato funkce podporuje scénáře, jako:

![Koncové body služby](media/networking-features/service-endpoints.png)

* Nastavení služby Application Gateway s vaší aplikací, aby zamezit příchozímu provozu do vaší aplikace
* Testricting přístup k vaší aplikaci k prostředkům ve vaší virtuální síti. To může zahrnovat virtuální počítače, služby ase nebo dokonce i další aplikace používající integrace virtuální sítě 

![Koncové body služby pomocí služby application gateway](media/networking-features/service-endpoints-appgw.png)

Další informace o konfiguraci koncových bodů služby s vaší aplikací v tomto kurzu na [omezení přístupu konfigurace koncového bodu služby][serviceendpoints]
 
### <a name="hybrid-connections"></a>Hybridní připojení

App Service Hybrid Connections umožňuje aplikacím provádět **odchozí** volání do zadaných koncových bodů TCP. Koncový bod může být v místním prostředí, ve virtuální síti nebo kdekoli, který povoluje odchozí provoz do Azure na portu 443. Tato funkce vyžaduje instalaci sady přenosový agent volat hybridní připojení správce (HCM) pro Windows Server 2012 nebo novější hostitele. HCM musí být schopný připojit Azure Relay na portu 443. HCM si můžete stáhnout z rozhraní App Service hybridní připojení na portálu. 

![Tok sítě hybridní připojení](media/networking-features/hybrid-connections.png)

Funkce App Service Hybrid Connections je založená na funkci Azure Relay Hybrid Connections. Služba App Service používá specializovaná forma funkci, která podporuje pouze vytváření odchozích volání z vaší aplikace TCP hostitele a port. Tohoto hostitele a port stačí překládat na hostitele, kde je nainstalován HCM. Když aplikace ve službě App Service, provádí vyhledání DNS u hostitele a port, které jsou definovány v hybridní připojení, provoz automaticky přesměruje přejít přes hybridní připojení a navýšení kapacity správce hybridního připojení. Další informace o funkci hybridní připojení, přečtěte si dokumentaci na [App Service Hybrid Connections][hybridconn]

Tato funkce se obvykle používá pro:

* Přístup k prostředkům v privátní sítě, které nejsou připojené k Azure pomocí VPN nebo ExpressRoute
* Podpora lift and shift místních aplikací do služby App Service bez nutnosti přesunout také podpůrné databáze  
* Bezpečně poskytnout přístup na jednom hostiteli a portu za hybridní připojení. Většina síťových funkcí otevřete přístup k síti a s hybridními připojeními máte jenom jednoho hostitele a port, který můžete oslovit.
* Scénáře, které nejsou pokryty jinými metodami odchozí připojení
* Provádět vývoj ve službě App Service, kde aplikace můžete snadno využít místní prostředky 

Protože tato funkce umožňuje přístup k místním prostředkům bez brány firewall pro příchozí riziko, obvykle se s vývojáři. Další odchozí služby App Service síťové funkce jsou velmi virtuální sítě Azure související. Hybridní připojení nemá závislost na prostřednictvím virtuální sítě a slouží pro širší sítě, které potřebujete. Je důležité si uvědomit, že funkce App Service Hybrid Connections pro vás nebo vědět, co právě děláte dojde k jeho zvýraznění. To je Řekněme, že se použije pro přístup k databázi, webové služby nebo libovolného soket TCP na sálového počítače. Tato funkce v podstatě tunelových propojení pakety protokolu TCP. 

Zatímco hybridní připojení se používá pro vývoj, používá se také také řadu provozních aplikací. Je skvělé pro přístup k webové služby nebo databáze, ale není vhodný pro situace zahrnující skvělé mnoho připojení vytváří. 

### <a name="gateway-required-vnet-integration"></a>Brána vyžaduje integrace virtuální sítě 

Brána vyžaduje funkci integrace virtuální sítě služby App umožňuje aplikaci provést **odchozí** žádostí do služby Azure Virtual Network. Tato funkce funguje tak, že připojení hostitele, které vaše aplikace běží bránu virtuální sítě ve virtuální síti pomocí sítě VPN point-to-site. Když konfigurujete funkci, vaše aplikace obdržela adres point-to-site, přiřazeny ke každé instanci. Tato funkce umožňuje přístup k prostředkům v modelu Classic nebo Resource Manageru virtuálních sítí v libovolné oblasti. 

![Brána vyžaduje integrace virtuální sítě](media/networking-features/gw-vnet-integration.png)

Tato funkce se řeší problém přístupu k prostředkům v jiných virtuálních sítí a slouží i k připojení přes virtuální síť k jiné virtuální sítě nebo dokonce místní. Nefunguje s ExpressRoute připojené virtuální sítě, ale nemá pomocí sítě Site-to-site VPN propojených sítí. Je obvykle nevhodný pro použití této funkce z aplikace do App Service Environment (ASE), protože služba ASE se už ve vaší virtuální síti. Případy použití, které řeší tato funkce je:

* Přístup k prostředkům na privátních IP adres ve vaší virtuální sítě Azure 
* Přístup k prostředkům v místním Pokud je VPN typu site-to-site 
* Přístup k prostředkům v partnerské virtuální sítě 

Pokud je tato funkce povolena, vaše aplikace bude používat server DNS, který má nakonfigurovanou cílové virtuální sítě. Další informace o této funkci v dokumentaci k [integrace virtuální sítě služby App][vnetintegrationp2s]. 

### <a name="vnet-integration"></a>Integrace virtuální sítě

Brána vyžaduje funkci integrace virtuální sítě je velmi užitečný, ale stále nevyřeší přístup k prostředkům přes ExpressRoute. Nad museli kontaktovat prostřednictvím připojení ExpressRoute, je potřeba pro aplikace, abyste mohli provádět volání do koncového bodu služby službám zabezpečeným přes službu. Vyřešit obě tyto další požadavky, se přidal další funkce integrace virtuální sítě. Novou funkci integrace virtuální sítě můžete umístit na back-endu vaší aplikace do podsítě ve virtuální síti správce prostředků ve stejné oblasti. Tato funkce není k dispozici v App Service Environment, který už je ve virtuální síti. Tato funkce umožňuje:

* Přístup k prostředkům v Resource Manageru virtuálních sítích ve stejné oblasti
* Přístup k prostředkům, které jsou zabezpečené pomocí koncových bodů služby 
* Přístup k prostředkům, které jsou přístupné přes připojení VPN nebo ExpressRoute

![Integrace virtuální sítě](media/networking-features/vnet-integration.png)

Tato funkce je ve verzi preview a neměl by se používat pro produkční úlohy. Další informace o této funkci, přečtěte si dokumentaci na [integrace virtuální sítě služby App][vnetintegration].

## <a name="app-service-environment"></a>App Service Environment 

App Service Environment (ASE) je nasazení jednoho tenanta služby Azure App Service, která se spouští ve vaší virtuální síti. Služba ASE umožňuje případy použití, jako například:

* Přístup k prostředkům ve vaší virtuální síti
* Přístup k prostředkům přes ExpressRoute
* Vystavení vašich aplikací s privátní adresou ve vaší virtuální sítě 
* Přístup k prostředkům napříč koncovými body služby 

Pomocí služby ASE není nutné používat funkce, jako je integrace virtuální sítě nebo koncové body služby, protože služba ASE je už ve vaší virtuální síti. Pokud chcete pro přístup k prostředkům, jako je SQL nebo úložiště přes koncové body služby povolte koncové body služby v podsíti služby ASE. Pokud chcete získat přístup k prostředkům ve virtuální síti, není nutná žádná další konfigurace.  Pokud chcete získat přístup k prostředkům přes ExpressRoute, jsou už ve virtuální síti a není nutné nic konfigurovat službu ASE nebo aplikace uvnitř. 

Protože aplikace ve službě ASE s ILB, může být odhalen na privátní IP adresu, můžete snadno přidat zařízení WAF ke zveřejnění stejně jako aplikace mají k Internetu a zabezpečit rest. To slouží k snadný vývoj vícevrstvých aplikací. 

Existuje několik věcí, které ještě nejsou z víceklientské služby, které jsou ze služby ASE. Vezměte v úvahu zejména věci, jako je:

* Zveřejnit své aplikace na privátní IP adresy
* Zabezpečení veškerý odchozí provoz s ovládacími prvky sítě, které nejsou součástí vaší aplikace 
* Využijte možnost hostovat aplikace ve službě jednoho tenanta 
* Vertikálně navýšit kapacitu na více instancí než je možné ve víceklientské službě 
* Načíst privátní certifikační Autority klientské certifikáty pro použití ve vašich aplikací s privátní certifikační Autority zabezpečené koncové body 
* Vynucení protokolu TLS 1.1 ve všech aplikacích hostovaných v systému bez jakékoli možnost vypnutí na úrovni aplikace 
* Zadejte vyhrazené adresy odchozí všech aplikací ve vaší službě ASE, který není sdílený s žádný zákazník 

![Služba ASE ve virtuální síti](media/networking-features/app-service-environment.png)

Služby ASE poskytuje nejlepší scénář kolem hostování aplikací izolované a vyhrazené ale součástí některých výzev v oblasti správy. Některé věci k uvážení před použitím provozní prostředí ASE jsou:
 
 * Služba ASE běží v rámci vaší virtuální sítě, ale má závislosti mimo virtuální síť. Tyto závislosti musí být povoleno. Další informace najdete v [důležité informace o sítích pro službu App Service Environment][networkinfo]
 * Služba ASE okamžitě škálování jako služba pro více tenantů. Budete muset předvídat potřeby škálování, nikoli reaktivně škálování. 
 * Služba ASE má vyšší ještě před zahájením náklady s ním spojená. Pokud chcete naplno využít vaše služba ASE, vám by měl plánujete uvedení mnoho úloh do jedné služby ASE a nenechat používanou small úsilí
 * Aplikace ve službě ASE nelze omezit přístup k některé aplikace ve službě ASE a jiné ne.
 * Služba ASE je v podsíti a libovolné sítě pravidla platí pro veškerý provoz do a z této službě ASE. Pokud chcete přiřadit pravidla pro příchozí provoz jenom jedna aplikace, použijte omezení přístupu. 

## <a name="combining-features"></a>Kombinování funkcí 

Funkce, které jste si poznamenali pro víceklientské služby lze použít společně k řešení komplikovanějších případech použití. Dvě běžné případy použití jsou popsány zde ale jsou jenom příklady. Když porozumíte tomu, co dělat různé funkce, řešit téměř všechny architektury systému, které potřebujete.

### <a name="inject-app-into-a-vnet"></a>Vložení aplikace do virtuální sítě

Je běžné požadavek o tom, jak vložit vaše aplikace ve virtuální síti. Uvedení vaší aplikace do virtuální sítě znamená, že příchozí a odchozí koncové body pro aplikace v rámci virtuální sítě. Služby ASE nabízí nejlepší řešení pro tento problém vyřešit, ale můžete získat většinu toho, co je potřeba s ve víceklientské službě díky kombinaci funkcí. Například může hostovat pouze internetových aplikací s privátní příchozí a odchozí adresy podle:

* Vytvoření služby Application Gateway s privátní adresou příchozí a odchozí
* Zabezpečení příchozího provozu do vaší aplikace s koncovými body služby 
* Použít nové integrace virtuální sítě, takže back-endu vaší aplikace je ve vaší virtuální síti 

Tento styl nasazení by poskytují vyhrazených adres pro odchozí provoz do Internetu nebo vám tak umožnili zamezit veškerý odchozí provoz z vaší aplikace.  Tento styl nasazení by vám poskytlo velmi toho, co by jinak pouze s ASE. 

### <a name="create-multi-tier-applications"></a>Vytvoření vícevrstvé aplikace

Vícevrstvé aplikace je aplikace, kde aplikace back-endového rozhraní API je přístupný pouze z úroveň front-endu. Chcete-li vytvořit vícevrstvou aplikaci, můžete:

* Použití integrace virtuální sítě k podsíti ve virtuální síti připojit back-end vaší front-endové webové aplikace
* Zabezpečený příchozí provoz do vaší aplikace API k pouze pocházející z podsítě používané ve front-endové webové aplikace pomocí koncových bodů služby

![vícevrstvé aplikace](media/networking-features/multi-tier-app.png)

Můžete mít více front-endových aplikací používat stejná rozhraní API aplikace pomocí integrace virtuální sítě z jiných front-endové aplikace a koncové body služby z aplikace API s využitím podsítě.  

<!--Links-->
[appassignedaddress]: https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl#bind-your-ssl-certificate
[iprestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[serviceendpoints]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[hybridconn]: https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections
[vnetintegrationp2s]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[vnetintegration]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[networkinfo]: https://docs.microsoft.com/azure/app-service/environment/network-info
