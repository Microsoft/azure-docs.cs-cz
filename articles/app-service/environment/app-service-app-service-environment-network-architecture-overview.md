---
title: Síťová architektura v1
description: Architektonický přehled topologie sítě prostředí služby App Service. Tento dokument je k dispozici pouze pro zákazníky, kteří používají starší verze v1 ASE.
author: stefsch
ms.assetid: 13d03a37-1fe2-4e3e-9d57-46dfb330ba52
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: b1b866f3be789c59eea38c5c22b5557d557440be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243845"
---
# <a name="network-architecture-overview-of-app-service-environments"></a>Síťová architektura Přehled prostředí aplikačních služeb
Prostředí služby App Service se vždy vytvářejí v rámci podsítě [virtuální sítě][virtualnetwork] – aplikace spuštěné v prostředí služby App Service mohou komunikovat s privátními koncovými body umístěnými v rámci stejné topologie virtuální sítě.  Vzhledem k tomu, že zákazníci mohou uzamknout části infrastruktury virtuálních sítí, je důležité porozumět typům toků síťové komunikace, ke kterým dochází v prostředí služby App Service.

## <a name="general-network-flow"></a>Obecný tok sítě
Když prostředí služby App Service (ASE) používá pro aplikace veřejnou virtuální IP adresu (VIP), veškerý příchozí provoz dorazí na tuto veřejnou virtuální adresu VIP.  To zahrnuje provoz HTTP a HTTPS pro aplikace, stejně jako další provoz pro FTP, funkce vzdáleného ladění a operace správy Azure.  Úplný seznam konkrétních portů (povinných i volitelných), které jsou k dispozici ve veřejné virtuální ip službě, najdete v článku o [řízení příchozího provozu][controllinginboundtraffic] do prostředí služby App Service. 

Prostředí služby App Service také podporují spuštěné aplikace, které jsou vázány pouze na interní adresu virtuální sítě, označovanou také jako adresa ILB (interní nástroj pro vyrovnávání zatížení).  Na ilb povoleno Služby ASE, HTTP a HTTPS provoz pro aplikace, stejně jako vzdálené ladění volání, dorazí na adresu ILB.  U většiny běžných konfigurací ILB-ASE bude provoz FTP/FTPS docházet také na adresu ILB.  Operace správy Azure však budou stále proudit do portů 454/455 na veřejné virtuální IP službě ASE s povolenou službou ILB.

Následující diagram znázorňuje přehled různých příchozích a odchozích síťových toků pro prostředí služby App Service, kde jsou aplikace vázány na veřejnou virtuální IP adresu:

![Obecné síťové toky][GeneralNetworkFlows]

Prostředí služby App Service můžete komunikovat s různými koncovými body soukromých zákazníků.  Například aplikace spuštěné v prostředí služby App Service se mohou připojit k databázovým serverům spuštěným na virtuálních počítačích IaaS ve stejné topologii virtuální sítě.

> [!IMPORTANT]
> Při pohledu na síťový diagram "Jiné výpočetní prostředky" jsou nasazeny v jiné podsíti z prostředí služby App Service. Nasazení prostředků ve stejné podsíti se službou ASE bude blokovat připojení ze služby ASE k těmto prostředkům (s výjimkou konkrétního směrování v rámci služby ASE). Nasadit do jiné podsítě místo (ve stejné virtuální síti). Prostředí služby App Service se pak bude moci připojit. Není nutná žádná další konfigurace.
> 
> 

Prostředí služby App Service také komunikují s prostředky SQL DB a Azure Storage, které jsou nezbytné pro správu a provoz prostředí služby App Service.  Některé prostředky Sql a úložiště, se kterými prostředí služby App Service komunikuje, se nacházejí ve stejné oblasti jako prostředí služby App Service, zatímco jiné jsou umístěné ve vzdálených oblastech Azure.  V důsledku toho odchozí připojení k Internetu je vždy nutné pro prostředí služby App Service správně fungovat. 

Vzhledem k tomu, že prostředí služby App Service je nasazeno v podsíti, lze skupiny zabezpečení sítě použít k řízení příchozího provozu do podsítě.  Podrobnosti o řízení příchozího provozu do prostředí služby App Service naleznete v následujícím [článku][controllinginboundtraffic].

Podrobnosti o povolení odchozího připojení k Internetu z prostředí služby App Service naleznete v následujícím článku o práci s [express route][ExpressRoute].  Stejný přístup popsaný v článku platí při práci s připojením site-to-Site a pomocí vynuceného tunelového propojení.

## <a name="outbound-network-addresses"></a>Odchozí síťové adresy
Prostředí služby App Service uskutečňuje odchozí volání, ip adresa je vždy přidružena k odchozím voláním.  Konkrétní IP adresa, která se používá, závisí na tom, jestli je volaný koncový bod umístěn v topologii virtuální sítě nebo mimo topologii virtuální sítě.

Pokud je volána koncový bod **mimo** topologii virtuální sítě, pak odchozí adresa (aka odchozí ADRESA NAT), která se používá, je veřejná VIP prostředí služby App Service.  Tuto adresu najdete v uživatelském rozhraní portálu pro prostředí služby App Service v okně Vlastnosti.

![Odchozí IP adresa][OutboundIPAddress]

Tuto adresu lze také určit pro ases, které mají pouze veřejné VIP vytvořením aplikace v prostředí služby App Service a pak provádí *nslookup* na adresu aplikace. Výsledná ADRESA IP je veřejná virtuální ip adresa i odchozí adresa NAT prostředí služby App Service.

Pokud je volána koncový bod **uvnitř** topologie virtuální sítě, odchozí adresa volající aplikace bude interní IP adresa jednotlivých výpočetních prostředků, které aplikaci spouštějí.  Neexistuje však trvalé mapování interních IP adres virtuální sítě na aplikace.  Aplikace se můžou přesouvat mezi různými výpočetními prostředky a fond dostupných výpočetních prostředků v prostředí služby App Service se může změnit kvůli operacím škálování.

Protože prostředí služby App Service je vždy umístěn v podsíti, máte zaručeno, že interní IP adresa výpočetního prostředku spuštěného v aplikaci bude vždy v rozsahu CIDR v podsíti.  V důsledku toho při jemně odstupňované seznamy ACL nebo skupiny zabezpečení sítě se používají k zabezpečení přístupu k jiným koncovým bodům v rámci virtuální sítě, rozsah podsítí obsahující prostředí služby App Service musí být udělen přístup.

Následující diagram znázorňuje tyto pojmy podrobněji:

![Odchozí síťové adresy][OutboundNetworkAddresses]

Ve výše uvedeném diagramu:

* Vzhledem k tomu, že veřejná virtuální ip adresa prostředí služby App Service je 192.23.1.2, je to odchozí IP adresa používaná při volání koncových bodů "Internet".
* Rozsah CIDR obsahující podsítě pro prostředí služby App Service je 10.0.1.0/26.  Ostatní koncové body v rámci stejné infrastruktury virtuální sítě uvidí volání z aplikací jako pocházející z některou z tohoto rozsahu adres.

## <a name="calls-between-app-service-environments"></a>Volání mezi prostředími služby App Service
Složitější scénář může nastat, pokud nasadíte více prostředí služby App Service ve stejné virtuální síti a odchozí volání z jednoho prostředí služby App Service do jiného prostředí služby App Service.  Tyto typy volání prostředí služby cross App Service bude také považována za volání "Internet".

Následující diagram znázorňuje příklad vrstvené architektury s aplikacemi v jednom prostředí služby App Service (např. webové aplikace "Přední dveře") volajících aplikací v druhém prostředí služby App Service (např. interní aplikace rozhraní API back-end, které nejsou určeny k přístupu z Internetu). 

![Volání mezi prostředími služby App Service][CallsBetweenAppServiceEnvironments] 

Ve výše uvedeném příkladu app service prostředí "ASE One" má odchozí IP adresu 192.23.1.2.  Pokud aplikace spuštěná v tomto prostředí služby App Service provede odchozí volání do aplikace spuštěné v druhém prostředí služby App Service ("ASE Two") umístěnéve stejné virtuální síti, bude odchozí volání považováno za volání "Internet".  V důsledku toho se síťový provoz přicházející do druhého prostředí služby App Service zobrazí jako pocházející z 192.23.1.2 (tj. není rozsah adres podsítě prvního prostředí služby App Service).

I když volání mezi různými prostředími služby App Service jsou považována za "internetová" volání, když se obě prostředí služby App Service nacházejí ve stejné oblasti Azure, síťový provoz zůstane v místní síti Azure a nebude fyzicky tok přes veřejného internetu.  V důsledku toho můžete použít skupinu zabezpečení sítě v podsíti druhého prostředí Služby App Service k povolení příchozích volání pouze z prvního prostředí služby App Service (jehož odchozí IP adresa je 192.23.1.2), čímž zajistíte zabezpečenou komunikaci mezi aplikací Prostředí služeb.

## <a name="additional-links-and-information"></a>Další odkazy a informace
Podrobnosti o příchozích portech používaných prostředími služby App Service a použití skupin zabezpečení sítě k řízení příchozího provozu jsou k dispozici [zde][controllinginboundtraffic].

Podrobnosti o použití uživatelem definované trasy udělit odchozí přístup k Internetu prostředí služby App Service je k dispozici v tomto [článku][ExpressRoute]. 

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/services/virtual-network/
[controllinginboundtraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[ExpressRoute]:  app-service-app-service-environment-network-configuration-expressroute.md

<!-- IMAGES -->
[GeneralNetworkFlows]: ./media/app-service-app-service-environment-network-architecture-overview/NetworkOverview-1.png
[OutboundIPAddress]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundIPAddress-1.png
[OutboundNetworkAddresses]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundNetworkAddresses-1.png
[CallsBetweenAppServiceEnvironments]: ./media/app-service-app-service-environment-network-architecture-overview/CallsBetweenEnvironments-1.png

