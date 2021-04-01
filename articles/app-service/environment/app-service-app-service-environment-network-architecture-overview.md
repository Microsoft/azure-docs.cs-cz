---
title: Architektura sítě v1
description: Přehled architektury síťové topologie App Service prostředí. Tento dokument je k dispozici pouze pro zákazníky, kteří používají starší pomocného uživatele v1.
author: stefsch
ms.assetid: 13d03a37-1fe2-4e3e-9d57-46dfb330ba52
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: b1b866f3be789c59eea38c5c22b5557d557440be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "84701809"
---
# <a name="network-architecture-overview-of-app-service-environments"></a>Přehled architektury sítě App Service prostředí
App Service prostředí se vždycky vytvářejí v rámci podsítě [virtuální sítě][virtualnetwork] – aplikace běžící v App Service Environment můžou komunikovat s privátními koncovými body umístěnými ve stejné topologii virtuální sítě.  Vzhledem k tomu, že zákazníci můžou uzamknout části své infrastruktury virtuální sítě, je důležité pochopit typy toků síťové komunikace, ke kterým dochází s App Service Environment.

## <a name="general-network-flow"></a>Obecný tok sítě
Když App Service Environment (pomocného mechanismu) používá veřejnou virtuální IP adresu (VIP) pro aplikace, veškerý příchozí provoz přijde na tuto veřejnou virtuální IP adresu.  To zahrnuje přenos HTTP a HTTPS pro aplikace a další přenosy pro FTP, funkce vzdáleného ladění a operace správy Azure.  Úplný seznam konkrétních portů (požadovaných i volitelných), které jsou k dispozici na veřejné VIP, najdete v článku [řízení příchozího provozu][controllinginboundtraffic] do App Service Environment. 

Prostředí App Service také podporují spouštění aplikací, které jsou vázány pouze na interní adresy virtuální sítě, označované také jako interního nástroje (interní nástroj pro vyrovnávání zatížení).  V interního nástroje s povoleným MECHANISMem řízení a přenos HTTP a HTTPS pro aplikace, stejně jako volání vzdáleného ladění, dorazí na adresu interního nástroje.  Pro většinu běžných konfigurací interního nástroje pomocného mechanismu přenosu dat FTP/FTPS se taky dorazí na adresu interního nástroje.  Operace správy Azure budou ale pořád přesměrované do portů 454/455 ve veřejné virtuální IP adrese pro pomocného programu interního nástroje.

Následující diagram ukazuje přehled různých příchozích a odchozích síťových toků pro App Service Environment, kde jsou aplikace vázané na veřejnou virtuální IP adresu:

![Obecné síťové toky][GeneralNetworkFlows]

App Service Environment může komunikovat s nejrůznějšími koncovými body privátního zákazníka.  Například aplikace spuštěné v App Service Environment se můžou připojit k databázovým serverům běžícím na virtuálních počítačích s IaaS ve stejné topologii virtuální sítě.

> [!IMPORTANT]
> V diagramu sítě jsou "jiné výpočetní prostředky" nasazeny v jiné podsíti než App Service Environment. Nasazení prostředků ve stejné podsíti s pomocným mechanismem pomocného programu zablokuje připojení z pomocného mechanismu pro tyto prostředky (s výjimkou konkrétního směrování uvnitř-služby Místo toho se nasaďte do jiné podsítě (ve stejné virtuální síti). App Service Environment se pak budou moci připojit. Není nutná žádná další konfigurace.
> 
> 

App Service prostředí také komunikují s SQL DB a Azure Storage prostředky nezbytnými pro správu a provoz App Service Environment.  Některé prostředky SQL a úložiště, se kterými App Service Environment komunikuje, se nacházejí ve stejné oblasti jako App Service Environment, zatímco jiné se nacházejí ve vzdálených oblastech Azure.  V důsledku toho je odchozí připojení k Internetu vždy vyžadováno, aby App Service Environment správně fungovalo. 

Vzhledem k tomu, že je App Service Environment nasazen v podsíti, lze použít skupiny zabezpečení sítě k řízení příchozího provozu do podsítě.  Podrobnosti o tom, jak řídit příchozí provoz do App Service Environment, najdete v následujícím [článku][controllinginboundtraffic].

Podrobnosti o tom, jak povolíte odchozí připojení k Internetu z App Service Environment, najdete v následujícím článku o práci se službou [Express Route][ExpressRoute].  Stejný postup, který je popsaný v článku, platí při práci s připojením typu Site-to-site a pomocí vynuceného tunelování.

## <a name="outbound-network-addresses"></a>Odchozí síťové adresy
Když App Service Environment provede odchozí volání, IP adresa je vždycky přidružená k odchozím voláním.  Konkrétní IP adresa, která se použije, závisí na tom, jestli je koncový bod umístěný v topologii virtuální sítě nebo mimo topologii virtuální sítě.

Pokud je volaný koncový bod **mimo** virtuální síťovou topologii, pak odchozí adresa (neboli adresa ODCHOZÍHO překladu adres), která se používá, je veřejná VIP App Service Environment.  Tuto adresu najdete v uživatelském rozhraní portálu pro App Service Environment v okně Vlastnosti.

![Odchozí IP adresa][OutboundIPAddress]

Tato adresa se dá určit taky pro služby ASE, které mají jenom veřejnou virtuální IP adresu, a to tak, že vytvoříte aplikaci v App Service Environment a pak na adrese aplikace provedete nástroj *nslookup* . Výsledná IP adresa je veřejná VIP i adresa App Service Environment odchozího překladu adres (NAT).

Pokud je koncový bod volaný **uvnitř** topologie virtuální sítě, bude odchozí adresa volající aplikace interní IP adresou jednotlivého výpočetního prostředku, na kterém běží aplikace.  Nejedná se však o trvalé mapování interních IP adres virtuální sítě na aplikace.  Aplikace se můžou pohybovat v různých výpočetních prostředcích a fond dostupných výpočetních prostředků v App Service Environment se může změnit v důsledku operací škálování.

Vzhledem k tomu, že se v rámci podsítě vždy používá App Service Environment, je zaručeno, že interní IP adresa výpočetního prostředku, na kterém běží aplikace, bude vždycky spadat do rozsahu CIDR podsítě.  V důsledku toho se při použití podrobných seznamů ACL nebo skupin zabezpečení sítě pro zabezpečení přístupu k jiným koncovým bodům v rámci virtuální sítě musí udělit přístup k rozsahu podsítě, který obsahuje App Service Environment.

Následující diagram podrobněji znázorňuje tyto koncepty:

![Odchozí síťové adresy][OutboundNetworkAddresses]

Ve výše uvedeném diagramu:

* Vzhledem k tomu, že veřejná VIP App Service Environment je 192.23.1.2, jedná se o odchozí IP adresu, která se používá při volání koncových bodů "Internet".
* Rozsah CIDR obsahující podsítě pro App Service Environment je 10.0.1.0/26.  Další koncové body v rámci stejné infrastruktury virtuální sítě uvidí v rámci tohoto rozsahu adres volání z aplikace, která pocházejí z někde někam.

## <a name="calls-between-app-service-environments"></a>Volání mezi prostředími App Service
Složitější scénář může nastat, pokud nasadíte více App Service prostředí ve stejné virtuální síti a odchozí volání z jednoho App Service Environment na jiný App Service Environment.  Tyto typy volání vzájemného App Service Environment budou také považovány za volání "Internet".

Následující diagram znázorňuje příklad vrstvené architektury s aplikacemi na jednom App Service Environment (např. "webové aplikace front-endu"), které volají aplikace na druhém App Service Environment (např. interní back-endové aplikace API, které nejsou určené k přístupu z Internetu). 

![Volání mezi prostředími App Service][CallsBetweenAppServiceEnvironments] 

V příkladu výše App Service Environment "pomocnému programu One" obsahuje odchozí IP adresu 192.23.1.2.  Pokud aplikace běžící na tomto App Service Environment provede odchozí volání aplikace běžící na druhém App Service Environment ("pomocném pomocným způsobem") umístěném ve stejné virtuální síti, odchozí volání se bude považovat za volání "Internet".  V důsledku toho se síťový provoz, který se dorazí na druhý App Service Environment, bude zobrazovat jako z 192.23.1.2 (to znamená, že se nejedná o rozsah adres podsítě prvního App Service Environment).

I když se volání mezi různými App Service prostředí považují za volání "Internet", pokud jsou obě prostředí App Service umístěná ve stejné oblasti Azure, síťový provoz zůstane v regionální síti Azure a nebude fyzicky přenášet přes veřejný Internet.  V důsledku toho můžete použít skupinu zabezpečení sítě v podsíti druhé App Service Environment, aby povolovala pouze příchozí hovory z prvního App Service Environment (jejichž odchozí IP adresa je 192.23.1.2), což zajišťuje zabezpečenou komunikaci mezi App Service prostředími.

## <a name="additional-links-and-information"></a>Další odkazy a informace
Podrobnosti o příchozích portech používaných App Service prostředích a používání skupin zabezpečení sítě k řízení příchozího provozu jsou k dispozici [zde][controllinginboundtraffic].

Podrobnosti o používání uživatelem definovaných tras pro udělení odchozího internetového přístupu k App Service prostředí je k dispozici v tomto [článku][ExpressRoute]. 

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/services/virtual-network/
[controllinginboundtraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[ExpressRoute]:  app-service-app-service-environment-network-configuration-expressroute.md

<!-- IMAGES -->
[GeneralNetworkFlows]: ./media/app-service-app-service-environment-network-architecture-overview/NetworkOverview-1.png
[OutboundIPAddress]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundIPAddress-1.png
[OutboundNetworkAddresses]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundNetworkAddresses-1.png
[CallsBetweenAppServiceEnvironments]: ./media/app-service-app-service-environment-network-architecture-overview/CallsBetweenEnvironments-1.png

