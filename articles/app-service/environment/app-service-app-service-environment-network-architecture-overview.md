---
title: Přehled architektury sítě služby App Service Environment – Azure
description: Přehled architektury sítě topologie ofApp prostředí Service.
services: app-service
documentationcenter: ''
author: stefsch
manager: erikre
editor: ''
ms.assetid: 13d03a37-1fe2-4e3e-9d57-46dfb330ba52
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 0d7d4af46e54ad89e0d084cb15af13e56115e996
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53274159"
---
# <a name="network-architecture-overview-of-app-service-environments"></a>Přehled síťové architektury ve službě App Service Environment
## <a name="introduction"></a>Úvod
App Service Environment se vždy vytvoří v určité podsíti [virtuální sítě] [ virtualnetwork] – aplikace, které běží ve službě App Service Environment může komunikovat s privátní koncové body umístěné ve stejném virtuálním topologie sítě.  Vzhledem k tomu, že zákazníci můžou uzamknout částmi infrastruktury virtuální sítě, je důležité porozumět typům sítě komunikačních toků, které mohou u služby App Service Environment.

## <a name="general-network-flow"></a>Tok sítě obecné
Pokud App Service Environment (ASE) používá veřejnou virtuální IP adresu (VIP) pro aplikace, všechny příchozí přenos dorazí na veřejných virtuálních IP adres.  To zahrnuje přenosy HTTP i HTTPS pro aplikace, stejně jako ostatní přenosy FTP, funkce vzdáleného ladění a operace správy Azure.  Úplný seznam určité porty (požadované a volitelné), které jsou k dispozici na veřejných virtuálních IP adres najdete v článku na [řízení příchozího provozu] [ controllinginboundtraffic] do služby App Service Environment. 

Prostředí App Service také podporují spuštěné aplikace, které jsou vázány pouze na interní adresu virtuální sítě, také označuje jako adresu ILB (interního nástroje load balancer).  Na ILB povolený provoz služby ASE, HTTP a HTTPS pro aplikace, stejně jako volání vzdálené ladění, přicházejí na adrese ILB.  Pro nejběžnější konfigurace služba ASE s ILB také FTP/FTPS provoz dorazí na adrese ILB.  Operace správy Azure budou stále směrovat na portech 454 a 455 na veřejných virtuálních IP adres ILB ale povolit služby ASE.

Následující diagram znázorňuje přehled různých příchozí a odchozí síťovou toků pro službu App Service Environment, ve kterém je aplikace vázána na veřejnou virtuální IP adresu:

![Obecné síťovými toky][GeneralNetworkFlows]

Služby App Service Environment může komunikovat s širokou škálu koncové body privátní zákazníka.  Aplikace spuštěné ve službě App Service Environment můžete například připojení k databázi, server (servery) běžící na virtuálních počítačích IaaS ve stejné virtuální síťové topologie.

> [!IMPORTANT]
> Síťový diagram podíváme, "Další výpočetní prostředky" jsou nasazené v jiné podsíti, ze služby App Service Environment. Nasazení prostředků ve stejné podsíti službou ASE bude blokovat připojení ze služby ASE k těmto prostředkům (s výjimkou služby ASE zvláštní uvnitř směrování). Nasazení do jiné podsítě místo toho (ve stejné virtuální síti). Služba App Service Environment pak bude moct připojit. Není potřeba žádná další konfigurace.
> 
> 

Služby App Service Environment také komunikovat s Sql DB a Azure Storage prostředky potřebné pro správu a provozování služby App Service Environment.  Některé z prostředků Sql a službu Storage, které komunikuje se službou App Service Environment jsou umístěné ve stejné oblasti jako služba App Service Environment, zatímco jiné jsou umístěny ve vzdálené oblasti Azure.  Odchozí připojení k Internetu je proto vždy požadované pro službu App Service Environment fungovala správně. 

Od služby App Service Environment se nasazuje do podsítě, skupiny zabezpečení sítě je možné řídit příchozí provoz do podsítě.  Podrobné informace o tom, jak řízení příchozího provozu do služby App Service Environment najdete na následující [článku][controllinginboundtraffic].

Podrobnosti o tom, jak povolit odchozí připojení k Internetu ze služby App Service Environment najdete v následujícím článku o práci s [Express Route][ExpressRoute].  Stejným způsobem popsaným v článku platí při práci s připojením Site-to-Site a použití vynuceného tunelování.

## <a name="outbound-network-addresses"></a>Odchozí síťové adresy
Služby App Service Environment provádí odchozích volání, IP adresa je vždy přidružený odchozích volání.  Konkrétní IP adresu, která se používá závisí na tom, jestli se nachází v rámci topologie virtuální sítě nebo mimo virtuální síťové topologie koncový bod volaný.

Pokud je koncový bod volaný **mimo** topologie virtuální sítě je odchozí adresy (označuje se také jako odchozí adres NAT), který se používá veřejných virtuálních IP adres služby App Service Environment.  Tuto adresu najdete v portálu uživatelského rozhraní pro App Service Environment v okně vlastností.

![Odchozí IP adresa][OutboundIPAddress]

Pro služby ase, které mají pouze veřejných virtuálních IP adres tak, že vytvoříte aplikaci v App Service Environment a následnému provedením můžete také určit tuto adresu *nslookup* na adresu aplikace. Výsledná adresa IP je veřejných virtuálních IP adres, i odchozí adresy NAT App Service Environment.

Pokud je koncový bod volaný **uvnitř** topologie virtuální sítě, odchozí adresy volající aplikace bude interní IP adresa jednotlivé výpočetních prostředků, ve kterém aplikace běží.  Ale není trvalé mapování z interních IP adres virtuální sítě do aplikací.  Aplikace se můžu pohybovat mezi různými výpočetními prostředky a fond k dispozici výpočetní prostředky ve službě App Service Environment může změnit v důsledku operace škálování.

Ale protože služby App Service Environment je vždy umístěny v podsíti, zaručuje, že interní IP adresa výpočetní prostředek, je aplikace spuštěná bude vždy ležet v rozsahu CIDR podsítě.  V důsledku toho při podrobných seznamů ACL nebo skupiny zabezpečení sítě se používají k zabezpečení přístupu k jiné koncové body v rámci virtuální sítě, rozsahu podsítě obsahující služby App Service Environment je potřeba udělit přístup.

Následující diagram znázorňuje tyto koncepty podrobněji:

![Odchozí síťové adresy][OutboundNetworkAddresses]

V diagramu:

* Protože veřejných virtuálních IP adres služby App Service Environment je 192.23.1.2, který je odchozí IP adresa používá při volání do koncových bodů "Internet".
* Rozsah CIDR obsahující podsítě pro App Service Environment je 10.0.1.0/26.  Další koncové body v rámci stejné virtuální síti infrastruktury uvidí volání z aplikace jako pocházející z někde v rámci tohoto rozsahu adres.

## <a name="calls-between-app-service-environments"></a>Volání mezi služby App Service Environment
Složitější scénáře může dojít, pokud nasadíte více App Service Environment ve stejné virtuální síti a odchozích volání z jedné služby App Service Environment do jiného prostředí App Service.  Tyto typy pro různé služby App Service Environment volání, budou také považovány za volání "Internet".

Následující diagram ukazuje příklad vícevrstvé architektury s aplikacemi na jeden App Service Environment (např.) "Předních dveří" webové aplikace) volání aplikace (třeba interní aplikace back endové rozhraní API nejsou určeny byla přístupná z Internetu) druhý App Service Environment. 

![Volání mezi služby App Service Environment][CallsBetweenAppServiceEnvironments] 

Ve výše uvedeném příkladu má služba App Service Environment "ASE jeden" odchozí IP adresu 192.23.1.2.  Pokud aplikace spuštěná na tomto App Service Environment, využívá odchozí volání na aplikaci spuštěnou v prostředí druhý služby App Service Environment ("služby ASE obou") umístěn ve stejné virtuální síti, odchozích volání budou považovány za volání rozhraní "Internet".  V důsledku síťový provoz přicházející v druhém služby App Service Environment se zobrazí jako pocházející z 192.23.1.2 (tedy ne rozsahu adres podsítě první App Service Environment).

I když volání mezi různé App Service Environment jsou považovány za volání "Internet", pokud obě služby App Service Environment jsou umístěny ve stejné oblasti Azure, síťový provoz zůstanou v místní síti Azure a nebudou fyzicky flow přes veřejný Internet.  Díky tomu můžete použít skupinu zabezpečení sítě v podsíti druhý App Service Environment a Povolit jenom příchozích volání od první služby App Service Environment (jehož odchozí IP adresa je 192.23.1.2), tedy zajistit zabezpečenou komunikaci mezi aplikací Služba prostředí.

## <a name="additional-links-and-information"></a>Další odkazy a informace
Podrobnosti o příchozí porty, které používá služba App Service Environment, a k řízení příchozího provozu pomocí skupin zabezpečení sítě je k dispozici [tady][controllinginboundtraffic].

Podrobnosti o použití uživatele definované trasy a udělit odchozí internetový přístup k App Service Environment je k dispozici v tomto [článku][ExpressRoute]. 

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/services/virtual-network/
[controllinginboundtraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[ExpressRoute]:  app-service-app-service-environment-network-configuration-expressroute.md

<!-- IMAGES -->
[GeneralNetworkFlows]: ./media/app-service-app-service-environment-network-architecture-overview/NetworkOverview-1.png
[OutboundIPAddress]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundIPAddress-1.png
[OutboundNetworkAddresses]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundNetworkAddresses-1.png
[CallsBetweenAppServiceEnvironments]: ./media/app-service-app-service-environment-network-architecture-overview/CallsBetweenEnvironments-1.png

