---
title: Geograficky distribuované škálování v prostředí App Service Environments
description: Zjistěte, jak vodorovně škálování aplikace pomocí Traffic Manageru a prostředí App Service geo rozdělení.
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
ms.openlocfilehash: 21f747239e565aba79a84c8e946a71e306b64968
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23836830"
---
# <a name="geo-distributed-scale-with-app-service-environments"></a>Geograficky distribuované škálování v prostředí App Service Environments
## <a name="overview"></a>Přehled
Scénáře aplikací, které vyžadují velmi vysoký škálování může být vyšší než výpočetní kapacitu prostředků, k dispozici pro jedno nasazení aplikace.  Hlasování aplikace, sportovní události a události televizní Zábava jsou všechny příklady scénářů, které vyžadují velmi velkém rozsahu. Vodorovně škálování aplikace s více nasazení aplikací pro zpracování extrémně zatížení požadavky prováděné v jedné oblasti, a také v oblastech, můžete splnit požadavky na velkém rozsahu.

Služby App Service Environment jsou ideální platformu pro horizontální škálování.  Jednou služby App Service Environment configuration se vybral podporující rychlost známé požadavků, vývojáři můžete nasadit další prostředí App Service způsobem "soubor cookie ořezávání" aby bylo možné zatížení ve špičce požadovanou kapacitou.

Předpokládejme například, že byl testován aplikace spuštěné v konfiguraci služby App Service Environment, zpracování 20 tisíc požadavků za sekundu (RPS).  Pokud je kapacita zatížení ve špičce požadované 100 tisíc RPS, pak pět (5) prostředí App Service lze vytvořit a nakonfigurovat tak, aby Ujistěte se, že aplikace dokáže zvládat maximální předpokládané zatížení.

Vzhledem k tomu, že zákazníci obvykle získat přístup k aplikacím pomocí vlastní (nebo jednoduché) domény, vývojáři potřebovat způsob, jak k distribuci požadavků aplikace ve všech instancí služby App Service Environment.  Skvělý způsob, jak se má vyřešit pomocí vlastní domény [profilu Azure Traffic Manageru][AzureTrafficManagerProfile].  Profil služby Traffic Manager lze nakonfigurovat tak, aby odkazoval na všechny jednotlivé prostředí App Service.  Traffic Manager automaticky zpracuje distribuce zákazníkům napříč všemi prostředí App Service podle nastavení v profilu Správce provozu služby Vyrovnávání zatížení.  Tento postup funguje bez ohledu na to, jestli všechny prostředí App Service jsou nasazené v jedné oblasti Azure, nebo nasazeným po celém světě nad několika oblastmi Azure.

Navíc vzhledem k tomu, že zákazníkům přístup k aplikacím prostřednictvím domény jednoduché, zákazníci neberou počet prostředí App Service spuštěna aplikace.  V důsledku vývojáři můžete rychle a snadno přidat a odebrat, podle zjištěnou přenosové zatížení prostředí App Service.

Následující koncepční diagram znázorňuje aplikace horizontálně škálovat na více systémů mezi tři prostředí App Service v jedné oblasti.

![Konceptuální architektura][ConceptualArchitecture] 

Zbývající část tohoto tématu vás provede kroky s nastavením Distribuovaná topologie pro ukázkové aplikace pomocí několika prostředí App Service.

## <a name="planning-the-topology"></a>Plánování topologie
Před vytvořením na nároky distribuované aplikace, pomáhá má několik částí informace předem.

* **Vlastní doména pro aplikaci:** co je název vlastní domény, který zákazníky bude používat pro přístup k aplikaci?  Pro ukázkovou aplikaci vlastní název domény je *www.scalableasedemo.com*
* **Doménu Traffic Manageru:** název domény musí být zvolena při vytváření [profilu Azure Traffic Manageru][AzureTrafficManagerProfile].  Tento název bude sloučen s *trafficmanager.net* příponu zaregistrovat položku domény, který je spravován nástrojem Traffic Manager.  Pro ukázkovou aplikaci, je zvolený název *škálovatelné App Service Environment ukázku*.  Výsledkem je název úplné domény, který je spravován nástrojem Traffic Manager *škálovatelné App Service Environment demo.trafficmanager.net*.
* **Strategie pro škálování aplikací nároky:** budou nároky na aplikace distribuována mezi více prostředí App Service v jedné oblasti?  Více oblastí?  Kombinaci a match obou přístupů?  Rozhodnutí by měla být založena na očekávání, kde budou pocházet provozu zákazníka a také jak dobře můžete škálovat zbytek aplikace podpůrná infrastruktura back-end.  Například s 100 % bezstavové aplikace, aplikace je možné massively rozšířit pomocí kombinace více prostředí App Service na oblast Azure, násobenou prostředí App Service nasadit nad několika oblastmi Azure.  S 15 + veřejných oblastí Azure můžete vybrat z skutečně zákazníci mohou vytvářet nároky celém světě flexibilně škálovatelné aplikace.  Ukázková aplikace používá pro tento článek byly vytvořeny tři prostředí App Service v jedné oblasti Azure (Jižní střední USA).
* **Zásady vytváření názvů pro prostředí App Service:** každý App Service Environment vyžaduje jedinečný název.  Nad rámec jednoho nebo dvou prostředí App Service je vhodné mít zásady vytváření názvů k identifikaci jednotlivých App Service Environment.  Pro ukázkovou aplikaci byl použit jednoduché zásady vytváření názvů.  Názvy tři prostředí App Service jsou *fe1ase*, *fe2ase*, a *fe3ase*.
* **Zásady vytváření názvů pro aplikace:** vzhledem k tomu, že budou nasazeny více instancí aplikace, název se vyžaduje pro každou instanci aplikace nasazené.  Jeden málo známé, ale je užitečná funkce prostředí App Service je, že stejný název aplikace lze použít v rámci více prostředí App Service.  Vzhledem k tomu, že každý App Service Environment má příponu domény jedinečný, vývojáři můžete znovu použít přesný stejný název aplikace v každém prostředí.  Vývojář může mít třeba aplikace s názvem následujícím způsobem: *myapp.foo1.p.azurewebsites.net*, *myapp.foo2.p.azurewebsites.net*, *myapp.foo3.p.azurewebsites.net*atd.  Pro ukázkovou aplikaci, když každá instance aplikace také musí mít jedinečný název.  Názvy instancí aplikace používá jsou *webfrontend1*, *webfrontend2*, a *webfrontend3*.

## <a name="setting-up-the-traffic-manager-profile"></a>Nastavení profilu Traffic Manageru
Po několik instancí aplikace nasazené na několika prostředí App Service, může být registrováno s nástrojem Traffic Manager instance jednotlivých aplikací.  Pro ukázkovou aplikaci Traffic Manager profil je potřeba pro *škálovatelné App Service Environment demo.trafficmanager.net* zákazníků, může směrovat do jakéhokoli z následující instance nasazené aplikace:

* **webfrontend1.fe1ase.p.azurewebsites.NET:** instanci ukázková aplikace nasazené na první App Service Environment.
* **webfrontend2.fe2ase.p.azurewebsites.NET:** instanci ukázková aplikace nasazené na druhý App Service Environment.
* **webfrontend3.fe3ase.p.azurewebsites.NET:** instanci ukázková aplikace nasazené na třetí App Service Environment.

Nejjednodušší způsob, jak zaregistrovat několik Azure App Service koncových bodů, ve všech spuštěných **stejné** oblast Azure, je pomocí prostředí Powershell [podpory Azure Resource Manager Traffic Manager] [ ARMTrafficManager].  

Prvním krokem je vytvoření profilu Azure Traffic Manager.  Následující kód ukazuje, jak byl profil vytvořen pro ukázkovou aplikaci:

    $profile = New-AzureTrafficManagerProfile –Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

Všimněte si jak *RelativeDnsName* parametr byl nastavený *škálovatelné App Service Environment ukázku*.  Jedná se jak názvu domény *škálovatelné App Service Environment demo.trafficmanager.net* je vytvořena a přidružena profil Traffic Manageru.

*TrafficRoutingMethod* parametr definuje zásady Traffic Manager použije k určení způsobu rozloženy zákazníka zatížení k dispozici koncové body pro vyrovnávání zatížení.  V tomto příkladu *vážená* jste vybrali metodu.  Tato akce způsobí žádostem zákazníků se rozloženy koncové body zaregistrovanou aplikaci založené na relativní váhu přidružené každý koncový bod. 

S profilem vytvořen každá instance aplikace přidat do profilu jako nativní koncového bodu Azure.  Následující kód načte odkaz na každý front-endu webové aplikace a pak přidá každou aplikaci jako koncový bod Traffic Manager prostřednictvím *TargetResourceId* parametr.

    $webapp1 = Get-AzureRMWebApp -Name webfrontend1
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend1 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled –Weight 10

    $webapp2 = Get-AzureRMWebApp -Name webfrontend2
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend2 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled –Weight 10

    $webapp3 = Get-AzureRMWebApp -Name webfrontend3
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend3 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp3.Id –EndpointStatus Enabled –Weight 10

    Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

Všimněte si, jak je jednoho volání *přidat AzureTrafficManagerEndpointConfig* pro každou instanci jednotlivých aplikací.  *TargetResourceId* parametr v každém příkazu prostředí Powershell odkazuje jedna z instancí tři nasazené aplikace.  Profil služby Traffic Manager se zatížení rozloženy všechny tři koncové body, které jsou zaregistrované v profilu.

Všechny tři koncových bodů pro používají stejnou hodnotu (10) *váhy* parametr.  Výsledkem rozprostření žádostem zákazníků Traffic Manager napříč všemi instancemi tři aplikace relativně rovnoměrně. 

## <a name="pointing-the-apps-custom-domain-at-the-traffic-manager-domain"></a>Odkazující vlastní domény aplikace na doménu Traffic Manageru
V posledním kroku potřeby je tak, aby odkazovalo vlastní domény aplikace na doménu Traffic Manageru.  Ukázková aplikace to znamená, odkazující *www.scalableasedemo.com* v *škálovatelné App Service Environment demo.trafficmanager.net*.  Tento krok je potřeba dokončit u registrátora domény, který spravuje vlastní doménu.  

Pomocí nástrojů pro správu vašeho registrátora domény zaznamenává záznam CNAME musí být vytvořen, která odkazuje vlastní domény na doménu Traffic Manageru.  Následující obrázek ukazuje příklad tuto konfiguraci CNAME, které bude vypadat takto:

![CNAME pro vlastní doménu.][CNAMEforCustomDomain] 

I když nejsou zahrnuté v tomto tématu, mějte na paměti, že každá instance jednotlivých aplikací musí mít vlastní doména se také zaregistrována.  Jinak Pokud požadavek je pro instanci aplikace a aplikace nemá vlastní domény zaregistrována aplikace, se požadavek nezdaří.  

V tomto příkladu se vlastní doména je *www.scalableasedemo.com*, a každá instance aplikace má vlastní domény s ním spojená.

![Vlastní doména][CustomDomain] 

Rekapitulace registrace vlastní domény s aplikacemi Azure App Service, najdete v následujícím článku na [registrace vlastní domény][RegisterCustomDomain].

## <a name="trying-out-the-distributed-topology"></a>Vyzkoušení topologie distribuované
Konečný výsledek konfiguraci Traffic Manager a DNS se žádostí o *www.scalableasedemo.com* bude procházet následující sekvenci:

1. Prohlížeč nebo zařízení bude vyhledávání DNS *www.scalableasedemo.com*
2. Záznam CNAME u registrátora domény způsobí, že vyhledávání DNS přesměrovat do Azure Traffic Manageru.
3. Vyhledávání DNS se provádí *škálovatelné App Service Environment demo.trafficmanager.net* na jednom ze serverů Azure DNS Traffic Manager.
4. Podle zásad vyrovnávání zatížení ( *TrafficRoutingMethod* parametr dříve použitý při vytváření profil služby Traffic Manager), bude Traffic Manager vyberte jeden z nakonfigurované koncové body a vrátí plně kvalifikovaný název domény tohoto koncového bodu na prohlížeč nebo zařízení.
5. Vzhledem k tomu, že plně kvalifikovaný název koncového bodu je adresa Url instance aplikace spuštěná na služby App Service Environment, prohlížeč nebo zařízení požádá server Microsoft Azure DNS přeložit plně kvalifikovaný název domény na IP adresu. 
6. Prohlížeče nebo zařízení, odešle požadavek HTTP/S na IP adresu.  
7. Žádost bude přicházejí na jednu z instancí aplikace spuštěn v jednom z prostředí App Service.

Následující obrázek konzoly ukazuje vyhledávání DNS pro vlastní doménu ukázkové aplikace úspěšně řešení do instance aplikace spuštěná v jednom ze tří prostředí Service ukázkové aplikace (v tomto případě druhý ze tří prostředí App Service):

![Vyhledávání DNS][DNSLookup] 

## <a name="additional-links-and-information"></a>Další odkazy a informace
Dokumentace v prostředí Powershell [podpory Azure Resource Manager Traffic Manager][ARMTrafficManager].  

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
