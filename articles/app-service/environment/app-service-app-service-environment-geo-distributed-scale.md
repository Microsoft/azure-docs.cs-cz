---
title: Geograficky distribuované škálování pomocí služby App Service Environment – Azure
description: Zjistěte, jak horizontálně škálovat aplikace s využitím geografické distribuce pomocí Traffic Manageru a služby App Service Environment.
services: app-service
documentationcenter: ''
author: stefsch
manager: erikre
editor: ''
ms.assetid: c1b05ca8-3703-4d87-a9ae-819d741787fb
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: aa9eb0b624df29f6fb86402c06436ed7349fa662
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53273863"
---
# <a name="geo-distributed-scale-with-app-service-environments"></a>Geograficky distribuované škálování v prostředí App Service Environments
## <a name="overview"></a>Přehled
Scénáře aplikací, které vyžadují velmi vysokou škálovatelností může překročit kapacitu prostředků výpočetní prostředky k dispozici pro jedno nasazení aplikace.  Hlasovací aplikace, během sportovní události a události televizní Zábava jsou všechny příklady scénářů, které vyžadují velmi vysokou škálovatelností. Vodorovně horizontální navýšení kapacity aplikace s více nasazení aplikací pro zpracování požadavků na extrémní zatížení prováděných v rámci jedné oblasti i napříč oblastmi, mohou být splněny požadavky na vysokou škálovatelností.

Služby App Service Environment jsou ideální platformu pro horizontální navýšení kapacity.  Jednou služby App Service Environment se vybral konfigurace, který podporuje frekvence známé požadavků, vývojáři můžete nasadit další služby App Service Environment způsobem "ořezávání soubor cookie" aby bylo možné kapacitu zatížení požadovaného ve špičce.

Předpokládejme například, že aplikaci spuštěnou v konfiguraci služby App Service Environment je testovaná pro zpracování 20 tisíc požadavků za sekundu (předávajících stran).  Pokud je kapacita zatížení požadovaného ve špičce 100 tisíc RPS, pak pět (5) služby App Service Environment lze vytvořit a nakonfigurovat tak, aby Ujistěte se, že aplikace dokáže zpracovat očekávané maximální zatížení.

Vzhledem k tomu, že zákazníci obvykle přístup k aplikacím pomocí domény vlastní (nebo vlastní), vývojáři potřebují způsob, jak distribuovat požadavky na aplikaci ve všech instancích služby App Service Environment.  Chcete-li vyřešit pomocí vlastní domény je skvělý způsob, jak to provést [profilu Azure Traffic Manageru][AzureTrafficManagerProfile].  Profil služby Traffic Manager je možné nakonfigurovat tak, aby odkazoval na všechny jednotlivé App Service Environment.  Traffic Manager automaticky zajistí distribuci zákazníkům ve všech prostředích App Service podle nastavení v profilu služby Traffic Manager Vyrovnávání zatížení.  Tento přístup funguje bez ohledu na to, zda všechny App Service Environment jsou nasazené v jedné oblasti Azure, nebo nasazeným ve víc oblastech Azure po celém světě.

Navíc vzhledem k tomu, že zákazníkům přístup k aplikacím prostřednictvím individuální doména, zákazníci mají vědět o počet prostředí App Service je aplikace spuštěná.  Vývojáři díky tomu můžete rychle a snadno přidat a odebrat, na základě vypozorovaného provoz zatížení služby App Service Environment.

Následující koncepční diagram znázorňuje aplikaci horizontálně škálovat napříč tři služby App Service Environment v jedné oblasti.

![Konceptuální architektura][ConceptualArchitecture] 

Zbývající část tohoto tématu vás provede jednotlivými kroky nastavení distributed topologii pro ukázkovou aplikaci pomocí více App Service Environment.

## <a name="planning-the-topology"></a>Plánování topologie
Před sestavením si nároky distribuované aplikace, umožňuje mít několik částí informace předem.

* **Vlastní doména aplikace:**  Co je vlastní název domény, který zákazníci budou používat pro přístup k aplikaci?  Pro ukázkovou aplikaci vlastní název domény je *www.scalableasedemo.com*
* **Doménu Traffic Manageru:**  Název domény je potřeba zvolit při vytváření [profilu Azure Traffic Manageru][AzureTrafficManagerProfile].  Tento název se zkombinuje s *trafficmanager.net* příponu je třeba zaregistrovat položku domény, který je spravovaný nástrojem Traffic Manager.  Ukázkové aplikace je název zvoleném *škálovatelné služby ase ukázka*.  Díky tomu úplný název domény, který je spravovaný nástrojem Traffic Manager je *škálovatelné služby ase demo.trafficmanager.net*.
* **Strategie pro škálování app nároky:**  Bude nároky na aplikaci distribuovat napříč více App Service Environment v jedné oblasti?  Více oblastech?  -Kombinovat oba přístupy poskytují?  Rozhodnutí by podle očekávání, kde budou pocházet provozu zákazníka a také jak můžete škálovat zbývající aplikace podporu back-end infrastrukturu.  Například se 100 % bezstavové aplikace, aplikace je možné masivně škálovat pomocí kombinace více App Service Environment v jedné oblasti Azure, vynásobený nasazení ve víc oblastech Azure App Service Environment.  Pomocí 15 + veřejných oblastech Azure si můžete vybrat z zákazníků, kteří vytvářejí skutečně nároky na celém světě vysoce škálovatelné aplikace.  Pro ukázková aplikace používá pro účely tohoto článku byly vytvořeny tři App Service Environment v jedné oblasti Azure (střed USA – jih).
* **Zásady vytváření názvů pro App Service Environment:**  Každá služba App Service Environment vyžaduje jedinečný název.  Nad rámec jednu nebo dvě služby App Service Environment je vhodné používat takové názvy vám pomůže identifikovat každou službu App Service Environment.  Ukázkové aplikace byl použit jednoduché zásady vytváření názvů.  Názvy tři App Service Environment jsou *fe1ase*, *fe2ase*, a *fe3ase*.
* **Zásady vytváření názvů pro aplikace:**  Protože více instancí aplikace se nasadí, název je potřeba pro každou instanci nasazené aplikace.  Jeden malý známé, ale příliš pohodlné funkce App Service Environment je, že stejný název aplikace lze použít v rámci více App Service Environment.  Protože každá služba App Service Environment má příponu domény jedinečný, vývojáři můžete znovu použít přesně stejný název aplikace v každém prostředí.  Například vývojář může mít aplikace s názvem následujícím způsobem: *myapp.foo1.p.azurewebsites.net*, *myapp.foo2.p.azurewebsites.net*, *myapp.foo3.p.azurewebsites.net*atd.  Ukázkové aplikace i když každá instance aplikace má také jedinečný název.  Názvy instancí aplikace používá jsou *webfrontend1*, *webfrontend2*, a *webfrontend3*.

## <a name="setting-up-the-traffic-manager-profile"></a>Nastavení profilu Traffic Manageru
Jakmile více instancí aplikace jsou nasazené v několika prostředích App Service, instance jednotlivých aplikací lze registrovat pomocí Traffic Manageru.  Ukázkové aplikace Traffic Manager je potřeba profil pro *škálovatelné služby ase demo.trafficmanager.net* zákazníků, který může směrovat na některý z následujících případech nasazené aplikace:

* **webfrontend1.fe1ase.p.azurewebsites.NET:**  Instance ukázkové aplikace nasazené na první služby App Service Environment.
* **webfrontend2.fe2ase.p.azurewebsites.NET:**  Instance ukázkové aplikace nasazené na druhý App Service Environment.
* **webfrontend3.fe3ase.p.azurewebsites.NET:**  Instance ukázkové aplikace nasazené na třetí App Service Environment.

Nejjednodušší způsob, jak zaregistrovat více služby Azure App Service koncových bodů, všechny spuštěné v **stejné** oblast Azure, je pomocí Powershellu [podpora Azure Resource Manageru Traffic Manageru] [ ARMTrafficManager].  

Prvním krokem je vytvoření profilu Azure Traffic Manageru.  Následující kód ukazuje, jak se profil vytvořil pro ukázkovou aplikaci:

    $profile = New-AzureTrafficManagerProfile –Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

Všimněte si, že jak *RelativeDnsName* parametr byl nastavený na *škálovatelné služby ase ukázka*.  Jedná se jak název domény *škálovatelné služby ase demo.trafficmanager.net* se vytvoří a přidružení k profilu Traffic Manageru.

*Trafficroutingmethod funkce* parametr definuje zásady Traffic Manager použije k určení způsobu vytížení od zákazníka rozloženy všechny dostupné koncové body pro vyrovnávání zatížení.  V tomto příkladu *vážená* jste vybrali metodu.  Výsledkem bude požadavky zákazníků se pak rozdělí mezi všechny koncové body registrované aplikace založené na relativní váhy přidružené každý koncový bod. 

S profil, který vytvořili každá instance aplikace přidá do profilu jako nativní koncový bod Azure.  Následující kód načte odkaz na každé front-endu webové aplikace a pak přidá jednotlivých aplikací jako koncových bodů Traffic Manageru prostřednictvím *TargetResourceId* parametru.

    $webapp1 = Get-AzureRMWebApp -Name webfrontend1
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend1 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled –Weight 10

    $webapp2 = Get-AzureRMWebApp -Name webfrontend2
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend2 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled –Weight 10

    $webapp3 = Get-AzureRMWebApp -Name webfrontend3
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend3 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp3.Id –EndpointStatus Enabled –Weight 10

    Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

Všimněte si, jak je jedno volání *přidat AzureTrafficManagerEndpointConfig* u každé instance jednotlivých aplikací.  *TargetResourceId* parametr v každém z prostředí Powershell odkazuje na některé z instancí tří nasazené aplikace.  Profil služby Traffic Manager se šíří zatížení mezi všechny tři koncové body, které jsou zaregistrované v profilu.

Všechny tři koncové body používat stejnou hodnotu (10) *váha* parametru.  Výsledkem rozprostření požadavky zákazníků z Traffic Manageru napříč všemi instancemi tři aplikace relativně rovnoměrně. 

## <a name="pointing-the-apps-custom-domain-at-the-traffic-manager-domain"></a>Odkazuje aplikaci vlastní domény na doménu Traffic Manageru
V posledním kroku potřeby se tak, aby odkazoval vlastní doménu aplikace na doménu Traffic Manageru.  Ukázkové aplikace to znamená, že odkazující *www.scalableasedemo.com* na *škálovatelné služby ase demo.trafficmanager.net*.  Tento krok je potřeba dokončit u registrátora domény, který spravuje vlastní doménu.  

Pomocí nástrojů pro správu vašeho registrátora domény záznam CNAME zaznamenává musí být vytvořen, která odkazuje vlastní domény na doménu Traffic Manageru.  Následující obrázek ukazuje příklad vypadá tato konfigurace CNAME:

![Záznam CNAME pro vlastní doménu][CNAMEforCustomDomain] 

I když nejsou zahrnuta v tomto tématu, mějte na paměti, že každá instance jednotlivých aplikací musí mít vlastní doménu v něm zaregistrovaný poskytovatel také.  Jinak pokud žádost o zajišťuje instance aplikace a aplikace nemá žádné vlastní domény zaregistrovaný s aplikací, požadavek selže.  

V tomto příkladu je vlastní doména *www.scalableasedemo.com*, a každá instance aplikace má vlastní domény s ním spojená.

![Vlastní doména][CustomDomain] 

Rekapitulace registrace vlastní domény s aplikacemi Azure App Service, najdete v následujícím článku na [registrace vlastní domény][RegisterCustomDomain].

## <a name="trying-out-the-distributed-topology"></a>Vyzkoušejte si Distributed topologii
Konečný výsledek konfigurace Traffic Manageru a DNS je, že žádosti pro *www.scalableasedemo.com* budou směrovat přes následující posloupnost:

1. Prohlížeč nebo zařízení provede vyhledávání DNS *www.scalableasedemo.com*
2. Záznam CNAME u registrátora domény způsobí, že vyhledávání DNS přesměrováni do Azure Traffic Manageru.
3. Vyhledávání DNS se provádí *škálovatelné služby ase demo.trafficmanager.net* proti jeden ze serverů DNS Azure Traffic Manageru.
4. Na základě zásada vyrovnávání zatížení ( *trafficroutingmethod funkce* parametr použili dříve při vytváření profilu služby Traffic Manager), bude vyberte jednu z nakonfigurované koncové body a vrátí plně kvalifikovaný název domény tohoto koncového bodu do Traffic Manageru prohlížeč nebo zařízení.
5. Protože je plně kvalifikovaný název koncového bodu adresy Url instance aplikace běžící na App Service Environment, prohlížeč nebo zařízení požádá server Microsoft Azure DNS vyřešit plně kvalifikovaný název domény na IP adresu. 
6. Prohlížeč nebo zařízení pošle požadavek HTTP/S na IP adresu.  
7. Žádost se přejít na jednu z instancí aplikace běží v některém prostředí App Service.

Konzoly obrázek níže ukazuje vyhledání DNS pro vlastní doménu ukázkovou aplikaci úspěšně překládá na instanci aplikace běžící na jednom z aktualizace tři ukázkové App Service Environment (v tomto případě druhý ze tří App Service Environment):

![Vyhledávání DNS][DNSLookup] 

## <a name="additional-links-and-information"></a>Další odkazy a informace
Dokumentaci k Powershellu [podpora Azure Resource Manageru Traffic Manageru][ARMTrafficManager].  

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[AzureTrafficManagerProfile]: ../../traffic-manager/traffic-manager-manage-profiles.md
[ARMTrafficManager]: ../../traffic-manager/traffic-manager-powershell-arm.md
[RegisterCustomDomain]: ../app-service-web-tutorial-custom-domain.md


<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-geo-distributed-scale/ConceptualArchitecture-1.png
[CNAMEforCustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CNAMECustomDomain-1.png
[DNSLookup]:  ./media/app-service-app-service-environment-geo-distributed-scale/DNSLookup-1.png
[CustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CustomDomain-1.png 
