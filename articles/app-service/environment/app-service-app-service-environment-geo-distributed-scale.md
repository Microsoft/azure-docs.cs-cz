---
title: Geografické distribuované měřítko
description: Naučte se škálovat aplikace vodorovně pomocí geografické distribuce pomocí Traffic Manageru a prostředí služby App Service.
author: stefsch
ms.assetid: c1b05ca8-3703-4d87-a9ae-819d741787fb
ms.topic: article
ms.date: 09/07/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 7ab04e23b838f2dfd39b73476db7492947d62e6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74688820"
---
# <a name="geo-distributed-scale-with-app-service-environments"></a>Geograficky distribuované škálování v prostředí App Service Environments
## <a name="overview"></a>Přehled

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Scénáře aplikací, které vyžadují velmi vysoké škálování může překročit kapacitu výpočetních prostředků, které jsou k dispozici pro jedno nasazení aplikace.  Hlasovací aplikace, sportovní události a televizní zábavní události jsou všechny příklady scénářů, které vyžadují extrémně vysoké měřítko. Požadavky na vysoké škálování lze splnit horizontálním škálováním aplikací, přičemž více nasazení aplikací se provádí v rámci jedné oblasti i napříč oblastmi, aby bylo možné zpracovat požadavky na extrémní zatížení.

App Service Environments are an ideal platform for horizontal scale out.  Jakmile je vybrána konfigurace prostředí služby App Service, která podporuje známou míru požadavků, vývojáři mohou nasadit další prostředí služby App Service způsobem "cookie cutter" k dosažení požadované kapacity špičkového zatížení.

Předpokládejme například, že aplikace spuštěná v konfiguraci prostředí služby App Service byla testována pro zpracování požadavků o 20 tisíc za sekundu (RPS).  Pokud je požadovaná kapacita zatížení ve špičce 100 KRP, lze vytvořit a nakonfigurovat pět (5) prostředí služby App Service, aby bylo zajištěno, že aplikace zvládne maximální předpokládané zatížení.

Vzhledem k tomu, že zákazníci obvykle přistupují k aplikacím pomocí vlastní (nebo marité) domény, vývojáři potřebují způsob, jak distribuovat požadavky aplikací napříč všemi instancemi prostředí Služby App Service.  Skvělý způsob, jak toho dosáhnout, je vyřešit vlastní doménu pomocí [profilu Azure Traffic Manager][AzureTrafficManagerProfile].  Profil Traffic Manageru lze nakonfigurovat tak, aby ukazoval na všechna jednotlivá prostředí služby App Service.  Traffic Manager bude automaticky zpracovávat distribuci zákazníků ve všech prostředích služby App Service na základě nastavení vyrovnávání zatížení v profilu Traffic Manager.  Tento přístup funguje bez ohledu na to, jestli jsou všechna prostředí služby App Service nasazená v jedné oblasti Azure nebo nasazená po celém světě ve více oblastech Azure.

Navíc vzhledem k tomu, že zákazníci přistupují k aplikacím prostřednictvím domény marnosti, zákazníci si nejsou vědomi počtu prostředí služby App Service, která spouštějí aplikaci.  V důsledku toho mohou vývojáři rychle a snadno přidávat a odebírat prostředí služby App Service na základě pozorovaného zatížení provozu.

Koncepční diagram níže znázorňuje aplikace horizontálně škálované ve třech prostředích služby App Service v rámci jedné oblasti.

![Koncepční architektura][ConceptualArchitecture] 

Zbývající část tohoto tématu provede kroky spojené s nastavením distribuované topologie pro ukázkovou aplikaci pomocí více prostředí služby App Service.

## <a name="planning-the-topology"></a>Plánování topologie
Před vytvořením distribuované aplikace stopy, pomáhá mít několik kusů informací dopředu.

* **Vlastní doména aplikace:**  Jaký je vlastní název domény, který zákazníci použijí pro přístup k aplikaci?  U ukázkové aplikace je vlastní název domény`www.scalableasedemo.com`
* **Doména Traffic Manageru:**  Při vytváření profilu Azure Traffic [Manageru][AzureTrafficManagerProfile]je třeba zvolit název domény .  Tento název bude kombinován s *příponou trafficmanager.net* pro registraci položky domény spravované traffic managerem.  Pro ukázkovou aplikaci je zvolený název *škálovatelný-ase-demo*.  V důsledku toho je úplný název domény, který je spravován Traffic *Manager, scalable-ase-demo.trafficmanager.net*.
* **Strategie pro škálování nároku na aplikaci:**  Bude nároky na aplikaci distribuovány ve více prostředích služby App Service v jedné oblasti?  Více oblastí?  Mix-a-zápas obou přístupů?  Rozhodnutí by mělo být založeno na očekáváních, odkud bude provoz zákazníků pocházet, a také na tom, jak dobře se může škálovat zbytek podpůrné back-endové infrastruktury aplikace.  Například s 100% bezstavovou aplikací, aplikace může být masivně škálovat pomocí kombinace více prostředí služby App Service na oblast Azure, vynásobené prostředí služby App Service nasazené ve více oblastech Azure.  Díky více než 15 veřejným oblastem Azure, ze kterých si můžete vybrat, můžou zákazníci skutečně vytvořit celosvětové nároky na aplikace v hyperškálování.  Pro ukázkovou aplikaci použitou pro tento článek byly vytvořeny tři prostředí služby App Service v jedné oblasti Azure (jižní centrální USA).
* **Konvence pojmenování prostředí služby App Service:**  Každé prostředí služby App Service vyžaduje jedinečný název.  Kromě jednoho nebo dvou prostředí služby App Service je užitečné mít konvenci pojmenování, která vám pomůže identifikovat každé prostředí služby App Service.  Pro ukázkovou aplikaci byla použita jednoduchá konvence pojmenování.  Názvy tří prostředí služby App Service jsou *fe1ase*, *fe2ase*a *fe3ase*.
* **Konvence pojmenování aplikací:**  Vzhledem k tomu, že se nasadí více instancí aplikace, je potřeba název pro každou instanci nasazené aplikace.  Jeden málo známý, ale velmi pohodlné funkce prostředí služby App Service je, že stejný název aplikace lze použít v rámci více prostředí služby App Service.  Vzhledem k tomu, že každé prostředí služby App Service má jedinečnou příponu domény, vývojáři se mohou rozhodnout znovu použít přesně stejný název aplikace v každém prostředí.  Například vývojář může mít aplikace s názvem takto: *myapp.foo1.p.azurewebsites.net*, *myapp.foo2.p.azurewebsites.net*, *myapp.foo3.p.azurewebsites.net*atd.  Pro ukázkovou aplikaci, i když každá instance aplikace má také jedinečný název.  Názvy instancí aplikace jsou *webfrontend1*, *webfrontend2*a *webfrontend3*.

## <a name="setting-up-the-traffic-manager-profile"></a>Nastavení profilu traffic managera
Po nasazení více instancí aplikace ve více prostředích služby App Service lze jednotlivé instance aplikací zaregistrovat pomocí Traffic Manageru.  Pro ukázkovou aplikaci je profil Traffic Manageru potřebný pro *scalable-ase-demo.trafficmanager.net,* které můžou zákazníky směrovat do některé z následujících nasazených instancí aplikace:

* **webfrontend1.fe1ase.p.azurewebsites.net:**  Instance ukázkové aplikace nasazené v prvním prostředí služby App Service.
* **webfrontend2.fe2ase.p.azurewebsites.net:**  Instance ukázkové aplikace nasazené v druhém prostředí služby App Service.
* **webfrontend3.fe3ase.p.azurewebsites.net:**  Instance ukázkové aplikace nasazené ve třetím prostředí služby App Service.

Nejjednodušší způsob, jak zaregistrovat více koncových bodů Služby Azure App Service, všechny spuštěné ve **stejné** oblasti Azure, je s podporou PowershellAzure [Resource Manager Traffic Manager][ARMTrafficManager].  

Prvním krokem je vytvoření profilu Azure Traffic Manager.  Níže uvedený kód ukazuje, jak byl profil vytvořen pro ukázkovou aplikaci:

    $profile = New-AzureTrafficManagerProfile –Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

Všimněte si, jak *relativeDnsName* parametr byl nastaven na *škálovatelné-ase-demo*.  Takto je vytvořen název domény *scalable-ase-demo.trafficmanager.net* a přidružený k profilu Traffic Manageru.

*TrafficRoutingMethod Parametr* definuje zásady vyrovnávání zatížení Traffic Manager použije k určení, jak rozložit zatížení zákazníka mezi všechny dostupné koncové body.  V tomto příkladu byla *zvolena metoda Vážená.*  To bude mít za následek požadavky zákazníků jsou rozloženy do všech koncových bodů registrované aplikace na základě relativní váhy spojené s každým koncovým bodem. 

Při vytvoření profilu se každá instance aplikace přidá do profilu jako nativní koncový bod Azure.  Níže uvedený kód načte odkaz na každou front-endovou webovou aplikaci a pak přidá každou aplikaci jako koncový bod Traffic Manageru prostřednictvím parametru *TargetResourceId.*

    $webapp1 = Get-AzWebApp -Name webfrontend1
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend1 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled –Weight 10

    $webapp2 = Get-AzWebApp -Name webfrontend2
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend2 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled –Weight 10

    $webapp3 = Get-AzWebApp -Name webfrontend3
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend3 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp3.Id –EndpointStatus Enabled –Weight 10

    Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

Všimněte si, jak existuje jedno volání *Add-AzureTrafficManagerEndpointConfig* pro každou instanci aplikace.  Parametr *TargetResourceId* v každém příkazu Powershellu odkazuje na jednu ze tří nasazených instancí aplikace.  Profil Traffic Manager se rozloží zatížení mezi všechny tři koncové body registrované v profilu.

Všechny tři koncové body používají stejnou hodnotu (10) pro parametr *Hmotnost.*  Výsledkem je, že Traffic Manager rozloží požadavky zákazníků na všechny tři instance aplikace relativně rovnoměrně. 

## <a name="pointing-the-apps-custom-domain-at-the-traffic-manager-domain"></a>Nasměrování vlastní domény aplikace na doménu Traffic Manageru
Posledním krokem je nasměrovat vlastní doménu aplikace do domény Traffic Manageru.  Pro ukázkovou aplikaci `www.scalableasedemo.com` `scalable-ase-demo.trafficmanager.net`to znamená ukazovat na .  Tento krok je třeba dokončit s registrátorem domény, který spravuje vlastní doménu.  

Pomocí nástrojů pro správu domény registrátora je třeba vytvořit záznamy CNAME, které nasměrují vlastní doménu na doménu Traffic Manager.  Následující obrázek ukazuje příklad toho, jak tato konfigurace CNAME vypadá:

![CNAME pro vlastní doménu][CNAMEforCustomDomain] 

I když není zahrnuta v tomto tématu, nezapomeňte, že každá instance jednotlivé aplikace musí mít vlastní doménu zaregistrovanou s ním také.  V opačném případě, pokud požadavek provede do instance aplikace a aplikace nemá vlastní doménu registrovanou v aplikaci, požadavek se nezdaří.  

V tomto příkladu `www.scalableasedemo.com`je vlastní doména a každá instance aplikace má vlastní doménu přidruženou k ní.

![Vlastní doména][CustomDomain] 

Rekapitulaci registrace vlastní domény pomocí aplikací Azure App Service najdete v následujícím článku o [registraci vlastních domén][RegisterCustomDomain].

## <a name="trying-out-the-distributed-topology"></a>Vyzkoušení distribuované topologie
Konečným výsledkem traffic manageru a konfigurace DNS `www.scalableasedemo.com` je, že požadavky pro budou protékat následujícím pořadím:

1. Prohlížeč nebo zařízení provede vyhledávání DNS`www.scalableasedemo.com`
2. Položka CNAME na registrátoru domény způsobí, že vyhledávání DNS přesměrováno na Azure Traffic Manager.
3. Vyhledávání DNS je pro *scalable-ase-demo.trafficmanager.net* proti jednomu ze serverů DNS Azure Traffic Manager.
4. Na základě zásad vyrovnávání zatížení (parametr *TrafficRoutingMethod* použitý dříve při vytváření profilu Traffic Manager) traffic manager vybere jeden z nakonfigurovaných koncových bodů a vrátí hlavní název doménového bodu tohoto koncového bodu prohlížeči nebo zařízení.
5. Vzhledem k tomu, že hlavní název domény koncového bodu je adresa URL instance aplikace spuštěné v prostředí služby App Service, prohlížeč nebo zařízení požádá server Microsoft Azure DNS o překlad hlavního názvového práva na IP adresu. 
6. Prohlížeč nebo zařízení odešle požadavek HTTP/S na adresu IP.  
7. Požadavek dorazí na jednu z instancí aplikace spuštěné v jednom z prostředí služby App Service.

Obrázek konzole níže ukazuje vyhledávání DNS pro vlastní doménu ukázkové aplikace, která se úspěšně vyřešila s instancí aplikace spuštěnou v jednom ze tří ukázkových prostředí služby App Service (v tomto případě druhé ze tří prostředí služby App Service):

![Vyhledávání DNS][DNSLookup] 

## <a name="additional-links-and-information"></a>Další odkazy a informace
Dokumentace k podpoře Správce [prostředků Azure Azure][ARMTrafficManager].  

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
