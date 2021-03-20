---
title: Geografické distribuované škálování
description: Naučte se, jak horizontálně škálovat aplikace pomocí geografických distribucí pomocí prostředí Traffic Manager a App Service.
author: stefsch
ms.assetid: c1b05ca8-3703-4d87-a9ae-819d741787fb
ms.topic: article
ms.date: 09/07/2016
ms.author: stefsch
ms.custom: seodec18, references_regions
ms.openlocfilehash: 004b32118521f72c5b59ad7bab2d4e41244b85c4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "85833600"
---
# <a name="geo-distributed-scale-with-app-service-environments"></a>Geograficky distribuované škálování v prostředí App Service Environments
## <a name="overview"></a>Přehled

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Scénáře aplikací, které vyžadují velmi vysoké škálování, můžou překročit kapacitu výpočetních prostředků, která je dostupná pro jedno nasazení aplikace.  Hlasovací aplikace, sportovní události a televised Entertainment jsou všechny příklady scénářů, které vyžadují extrémně vysoké měřítko. Požadavky na vysoký rozsah můžou být splněné horizontálním škálováním aplikací. Pro zpracování extrémních požadavků na zatížení je možné v jedné oblasti a v různých oblastech vytvořit mnoho nasazení aplikace.

App Service prostředí jsou ideální platformou pro horizontální škálování. Po výběru konfigurace App Service Environment, která může podporovat známou rychlost požadavků, můžou vývojáři nasadit další App Service prostředí v "ořezávání souborů cookie", aby dosáhli požadované kapacity zatížení ve špičce.

Předpokládejme například, že aplikace spuštěná v konfiguraci App Service Environment byla testována za účelem zpracování požadavků 20 tisíc za sekundu (RPS).  Pokud je požadovaná kapacita zatížení ve špičce 100 tisíc RPS, můžete vytvořit a nakonfigurovat pět (5) App Service prostředí, aby se zajistilo, že aplikace bude moci zvládnout maximální předpokládané zatížení.

Vzhledem k tomu, že zákazníci obvykle přistupují k aplikacím pomocí vlastní (nebo individuální) domény, potřebují vývojáři způsob, jak distribuovat žádosti o aplikace napříč všemi App Service Environment instancemi.  Skvělým způsobem, jak dosáhnout tohoto cíle, je vyřešit vlastní doménu pomocí [profilu Azure Traffic Manager][AzureTrafficManagerProfile].  Profil Traffic Manager lze nakonfigurovat tak, aby ukazoval na všechna jednotlivá App Service prostředí.  Traffic Manager automaticky zpracuje distribuci zákazníků napříč všemi App Service prostředími na základě nastavení vyrovnávání zatížení v profilu Traffic Manager.  Tento přístup funguje bez ohledu na to, jestli jsou všechna App Service prostředí nasazená v jedné oblasti Azure nebo nasazená celosvětově napříč několika oblastmi Azure.

Kromě toho, vzhledem k tomu, že zákazníci přistupují k aplikacím prostřednictvím domény individuální, si zákazníci nevědomi počtu App Service prostředí, ve kterých se aplikace spouští.  V důsledku toho mohou vývojáři snadno a rychle přidávat a odebírat App Service prostředí na základě pozorovaného zatížení provozu.

Koncepční diagram níže znázorňuje aplikaci horizontálně škálované v rámci tří App Service prostředí v rámci jedné oblasti.

![Koncepční architektura][ConceptualArchitecture] 

Zbývající část tohoto tématu vás provede kroky pro nastavení distribuované topologie pro ukázkovou aplikaci s využitím více App Service prostředí.

## <a name="planning-the-topology"></a>Plánování topologie
Než začnete sestavovat kapacitu distribuovaných aplikací, pomůže vám to několik informací předem.

* **Vlastní doména pro aplikaci:**  Jaký je vlastní název domény, který budou zákazníci používat pro přístup k aplikaci?  Pro ukázkovou aplikaci je název vlastní domény `www.scalableasedemo.com` .
* **Doména Traffic Manager:** Při vytváření [profilu Traffic Manager Azure][AzureTrafficManagerProfile]vyberte název domény.  Tento název bude kombinován s příponou *trafficmanager.NET* k registraci položky domény spravované pomocí Traffic Manager.  Pro ukázkovou aplikaci je zvolený název *škálovatelný-pomocný-demo*.  Výsledkem je, že úplný název domény, který je spravovaný nástrojem Traffic Manager, je *Scalable-ASE-demo.trafficmanager.NET*.
* **Strategie škálování aplikace:**  Budou nároky na aplikaci distribuovány napříč více App Service prostředími v jedné oblasti?  Více oblastí?  Jak se vzájemně porovnává kombinace obou přístupů?  Založte rozhodnutí o očekáváních, kde budou vzdálení provozu zákazníků, a o tom, jak dobře se může škálovat i zbytek podpory back-endové infrastruktury aplikace.  Například u bezstavové aplikace s 100% se dá aplikace hromadně škálovat pomocí kombinace mnoha App Service prostředí v každé oblasti Azure vynásobená App Service prostředími nasazenými napříč mnoha oblastmi Azure.  Díky více než 15 globálním oblastem Azure, ze kterých si můžete vybrat, můžou zákazníci skutečně vytvořit škálovatelnou aplikaci na úrovni technologie Hyper-v.  Pro ukázkovou aplikaci, která se používá pro tento článek, se tři App Service prostředí vytvořila v jedné oblasti Azure (Střed USA – jih).
* **Konvence pojmenování pro prostředí App Service:**  Každý App Service Environment vyžaduje jedinečný název.  Mimo jedno nebo dvě App Service prostředí je vhodné, abyste měli zásady vytváření názvů, které vám pomůžou identifikovat jednotlivé App Service Environment.  Pro ukázkovou aplikaci se použila jednoduchá konvence pojmenování.  Názvy tří App Service prostředí jsou *fe1ase*, *fe2ase* a *fe3ase*.
* **Konvence pojmenování pro aplikace:**  Vzhledem k tomu, že bude nasazeno několik instancí aplikace, je pro každou instanci nasazené aplikace nutné zadat název.  Jednou ze známých, ale pohodlných funkcí App Service prostředí je, že stejný název aplikace můžete použít ve více App Service prostředích.  Vzhledem k tomu, že každý App Service Environment má jedinečnou příponu domény, můžou si vývojáři zvolit, že budou v každém prostředí znovu používat přesný název aplikace.  Například vývojář může mít aplikace pojmenované následujícím způsobem:  *MyApp.foo1.p.azurewebsites.NET*, *MyApp.foo2.p.azurewebsites.NET*, *MyApp.foo3.p.azurewebsites.NET* atd.  Pro ukázkovou aplikaci ale má každá instance aplikace také jedinečný název.  Používané názvy instancí aplikace jsou *webfrontend1*, *webfrontend2* a *webfrontend3*.

## <a name="setting-up-the-traffic-manager-profile"></a>Nastavení profilu Traffic Manager
Po nasazení několika instancí aplikace na více App Service prostředí se můžou jednotlivé instance aplikace registrovat v Traffic Manager.  Pro ukázkovou aplikaci je Traffic Manager profil potřebný pro *Scalable-ASE-demo.trafficmanager.NET* , který může směrovat zákazníky do kterékoli z následujících nasazených instancí aplikace:

* **webfrontend1.fe1ase.p.azurewebsites.NET:**  Instance ukázkové aplikace nasazená na prvním App Service Environment.
* **webfrontend2.fe2ase.p.azurewebsites.NET:**  Instance ukázkové aplikace nasazená v druhém App Service Environment.
* **webfrontend3.fe3ase.p.azurewebsites.NET:**  Instance ukázkové aplikace nasazené na třetí App Service Environment.

Nejjednodušší způsob, jak registrovat několik koncových bodů Azure App Service, je všechny spuštěné ve **stejné** oblasti Azure s podporou powershellu [Azure Resource Manager Traffic Manager][ARMTrafficManager].  

Prvním krokem je vytvoření profilu Azure Traffic Manager.  Následující kód ukazuje, jak byl profil vytvořen pro ukázkovou aplikaci:

```azurepowershell-interactive
$profile = New-AzureTrafficManagerProfile –Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

Všimněte si, jak byl parametr *RelativeDnsName* nastaven na *škálovatelné-pomocného – demo*.  Tento parametr způsobí vytvoření názvu domény *Scalable-ASE-demo.trafficmanager.NET* a přidružení k profilu Traffic Manager.

Parametr *TrafficRoutingMethod* definuje zásadu vyrovnávání zatížení Traffic Manager použije k určení toho, jak se má zatížení zákazníka rozprostřít ve všech dostupných koncových bodech.  V tomto příkladu byla zvolena *Vážená* metoda.  Z důvodu této volby budou žádosti o zákazníky rozdělené do všech registrovaných koncových bodů aplikace na základě relativních vah, které jsou spojené s každým koncovým bodem. 

S vytvořeným profilem se každá instance aplikace přidá do profilu jako nativní koncový bod Azure.  Následující kód načte odkaz na každou front-end webovou aplikaci. Pak přidá jednotlivé aplikace jako koncový bod Traffic Manager prostřednictvím parametru *parametrem targetresourceid* .

```azurepowershell-interactive
$webapp1 = Get-AzWebApp -Name webfrontend1
Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend1 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled –Weight 10

$webapp2 = Get-AzWebApp -Name webfrontend2
Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend2 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled –Weight 10

$webapp3 = Get-AzWebApp -Name webfrontend3
Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend3 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp3.Id –EndpointStatus Enabled –Weight 10

Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile
```

Všimněte si, jak existuje jedno volání *Add-AzureTrafficManagerEndpointConfig* pro každou jednotlivou instanci aplikace.  Parametr *parametrem targetresourceid* v každém příkazu prostředí PowerShell odkazuje na jednu ze tří nasazených instancí aplikace.  Profil Traffic Manager se rozprostře zatížení ve všech třech koncových bodech registrovaných v profilu.

U všech tří koncových bodů se pro parametr *váhy* používá stejná hodnota (10).  Výsledkem této situace je Traffic Manager, že se požadavky na zákazníky napříč všemi třemi instancemi aplikací poměrně rovnoměrně rozšíří. 

## <a name="pointing-the-apps-custom-domain-at-the-traffic-manager-domain"></a>Nasměrování vlastní domény aplikace v Traffic Manager doméně
Posledním krokem je nasměrování vlastní domény aplikace v Traffic Manager doméně.  Pro ukázkovou aplikaci ukažte `www.scalableasedemo.com` na `scalable-ase-demo.trafficmanager.net` .  Dokončete tento krok s doménovým registrátorem, který spravuje vlastní doménu.  

Pomocí nástrojů pro správu domény vašeho registrátora se musí vytvořit záznamy CNAME, které budou ukazovat na vlastní doménu v doméně Traffic Manager.  Následující obrázek ukazuje příklad toho, jak tato konfigurace záznamu CNAME vypadá takto:

![CNAME pro vlastní doménu][CNAMEforCustomDomain] 

I když se to v tomto tématu nezabývá, pamatujte, že každá instance aplikace musí mít taky zaregistrovanou vlastní doménu.  V opačném případě, pokud požadavek vytvoří instanci aplikace a aplikace nezaregistrovala vlastní doménu s aplikací, požadavek selže.

V tomto příkladu je vlastní doména `www.scalableasedemo.com` a ke každé instanci aplikace je přidružená vlastní doména.

![Vlastní doména][CustomDomain] 

Rekapitulace o registraci vlastní domény pomocí Azure App Servicech aplikací najdete v tématu [Registrace vlastních domén][RegisterCustomDomain].

## <a name="trying-out-the-distributed-topology"></a>Vyzkoušení distribuované topologie
Konečný výsledek Traffic Manager a konfigurace DNS je, že požadavky na službu `www.scalableasedemo.com` budou procházet pomocí následujícího pořadí:

1. Prohlížeč nebo zařízení provede vyhledávání DNS pro `www.scalableasedemo.com`
2. Záznam CNAME v registrátoru domény způsobí přesměrování vyhledávání DNS do Azure Traffic Manager.
3. Pro *Scalable-ASE-demo.trafficmanager.NET* se provede vyhledávání DNS na jednom ze serverů DNS Azure Traffic Manager.
4. Na základě zásad vyrovnávání zatížení uvedeného výše v parametru *TrafficRoutingMethod* Traffic Manager vybere jeden z konfigurovaných koncových bodů. Pak vrátí plně kvalifikovaný název domény tohoto koncového bodu do prohlížeče nebo zařízení.
5. Vzhledem k tomu, že plně kvalifikovaný název domény koncového bodu je adresa URL instance aplikace, která běží na App Service Environment, prohlížeč nebo zařízení požádá Microsoft Azure server DNS o překlad plně kvalifikovaného názvu domény na IP adresu. 
6. Prohlížeč nebo zařízení odešle požadavek HTTP/S na IP adresu.  
7. Požadavek se dorazí na jednu z instancí aplikace spuštěné v jednom z App Service prostředí.

Následující obrázek konzoly ukazuje vyhledání DNS pro vlastní doménu ukázkové aplikace. Úspěšně se překládá na instanci aplikace, která běží na jednom ze tří ukázkových App Service prostředí (v tomto případě druhý ze tří App Service prostředí):

![Vyhledání DNS][DNSLookup] 

## <a name="additional-links-and-information"></a>Další odkazy a informace
Dokumentace k [podpoře Azure Resource Manager][ARMTrafficManager]powershellu Traffic Manager.  

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
