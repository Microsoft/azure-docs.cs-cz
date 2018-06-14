---
title: Přehled síťové architektury ve službě App Service Environment
description: Přehled architektury ofApp topologie sítě služby prostředí.
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
ms.openlocfilehash: 3362a55524da42914681db06b8d2c0da8df773d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23836823"
---
# <a name="network-architecture-overview-of-app-service-environments"></a>Přehled síťové architektury ve službě App Service Environment
## <a name="introduction"></a>Úvod
Prostředí App Service se vytváří vždy v rámci podsítí [virtuální sítě] [ virtualnetwork] -aplikace běžící ve službě App Service Environment může komunikovat s privátní koncové body umístěné v rámci stejné virtuální topologie sítě.  Vzhledem k tomu, že zákazníci můžou zamknout součástí infrastruktury svojí virtuální sítě, je důležité si uvědomit, typy síťovými toky komunikace, které se služby App Service Environment.

## <a name="general-network-flow"></a>Tok obecné sítě
Používá-li aplikaci služby prostředí řízení, veřejné virtuální adresy IP (VIP) pro aplikace, všechny příchozí přenos dorazí na tuto veřejnou virtuální IP.  To zahrnuje přenosů dat HTTP a HTTPS pro aplikace, a také ostatní přenosy FTP, funkce vzdáleného ladění a operace správy Azure.  Úplný seznam určité porty (požadované a volitelné), které jsou k dispozici na veřejných virtuálních IP adres najdete v článku na [řízení příchozí provoz] [ controllinginboundtraffic] do služby App Service Environment. 

Služby App Service Environment také podporují spuštěné aplikace, které jsou vázány jenom na interní adresu virtuální sítě, také označuje jako adresu ILB (nástroj pro vyrovnávání zatížení pro vnitřní).  Na ILB povoleno App Service Environment, HTTP a HTTPS provozy pro aplikace a také vzdáleného ladění volání dorazí na adrese ILB.  U většiny běžných konfigurací s ILB App Service Environment přenosy FTP/FTPS také dorazí na adrese ILB.  Operace správy Azure bude stále procházet na porty 454/455 veřejná virtuální IP adresu z ILB, ale povoleno App Service Environment.

Následující diagram ukazuje přehled různých příchozí a odchozí síťovými toky pro služby App Service Environment, kde aplikace je vázána na veřejné virtuální IP adresy:

![Obecné síťovými toky][GeneralNetworkFlows]

Služby App Service Environment může komunikovat s celou řadu koncové body privátní zákazníka.  Aplikace běžící v App Service Environment může například připojení k databázi servery spuštěné v virtuální počítače IaaS ve stejné virtuální síťové topologie.

> [!IMPORTANT]
> Vyhledávání v diagramu sítě, jsou v jiné podsíti ze služby App Service Environment nasazené "Ostatní výpočetní prostředky". Nasazení prostředků ve stejné podsíti s App Service Environment bude blokovat připojení z App Service Environment na tyto prostředky (s výjimkou směrování konkrétní intra-App Service Environment). Nasaďte do jiné podsítě místo toho (ve stejné virtuální síti). Služba App Service Environment pak bude moct připojit. Není potřeba žádná další konfigurace.
> 
> 

Služby App Service Environment také komunikovat s databáze Sql a Azure Storage prostředky potřebné pro správu a provozování služby App Service Environment.  Některé prostředky Sql a úložiště, které služby App Service Environment komunikuje se nacházejí ve stejné oblasti jako služba App Service Environment, zatímco jiné jsou umístěny v vzdálené oblastech Azure.  Odchozí připojení k Internetu je proto vždy vyžaduje pro správné fungování služby App Service Environment. 

Vzhledem k tomu, že služby App Service Environment je nasazený na podsíť, skupiny zabezpečení sítě můžete použít k řízení příchozí provoz do podsítě.  Podrobnosti o tom, jak řídit příchozí přenosy do služby App Service Environment, naleznete v následujících [článku][controllinginboundtraffic].

Podrobné informace o tom, jak povolit odchozí připojení k Internetu z služby App Service Environment, najdete v následujícím článku o práci s [Express Route][ExpressRoute].  Ve stejný přístup popsané v článku platí při práci s připojení Site-to-Site a použití vynuceného tunelování.

## <a name="outbound-network-addresses"></a>Odchozí síťové adresy
Pokud služby App Service Environment provede odchozí volání, je vždy přidružen odchozí volání IP adresu.  Konkrétní IP adresu, která se používá závisí na tom, jestli se nachází v rámci virtuální síťové topologie, nebo mimo virtuální síťové topologie volané koncový bod.

Pokud je koncovým bodem volané **mimo** topologie virtuální sítě, je odchozí adresu (neboli odchozí adres NAT), která se používá veřejné VIP App Service Environment.  Tato adresa naleznete v portálu uživatelské rozhraní App Service Environment v okně Vlastnosti.

![Odchozí IP adresu][OutboundIPAddress]

Tuto adresu můžete také určit pro ASEs, které mají pouze veřejné VIP vytváření aplikace v App Service Environment, a pak provedením *nslookup* na adrese aplikace. Výsledná IP adresa je veřejná virtuální IP adresu, jak odchozí adres NAT App Service Environment.

Pokud je koncovým bodem volané **uvnitř** o virtuální síťové topologie, adresu odchozí volání aplikace bude na interní IP adresu jednotlivých výpočetních prostředků, ve kterém aplikace běží.  Ale není trvalé mapování virtuální sítě interní IP adresy pro aplikace.  Aplikace lze pohybovat mezi různé výpočetní prostředky a fondu k dispozici výpočetních prostředků ve službě App Service Environment, můžete změnit z důvodu operace škálování.

Ale vzhledem k tomu, že služby App Service Environment se vždy nachází v rámci jedné podsítě, že je zaručeno, že na interní IP adresu výpočtový prostředek spuštění aplikace bude vždy ležet v rozsahu CIDR podsítě.  V důsledku toho-když podrobných seznamů řízení přístupu nebo skupiny zabezpečení sítě se používají k zabezpečení přístupu k další koncové body v rámci virtuální sítě, rozsahu podsítě obsahující App Service Environment musí mít udělen přístup.

Následující diagram znázorňuje tyto koncepty podrobněji:

![Odchozí síťové adresy][OutboundNetworkAddresses]

V diagramu:

* Vzhledem k tomu, že veřejný VIP App Service Environment je 192.23.1.2, který je odchozí adresa IP použitá při volání koncové body "Internet".
* Rozsah CIDR obsahující podsítě pro App Service Environment je 10.0.1.0/26.  Další koncové body v rámci stejné infrastruktury virtuální sítě se zobrazí volání z aplikace jako pocházející z někde v tomto rozsahu adres.

## <a name="calls-between-app-service-environments"></a>Volání mezi služby App Service Environment
Složitější situace může nastat, pokud nasadíte více prostředí App Service ve stejné virtuální síti a odchozí volání z jednoho App Service Environment do jiné App Service Environment.  Tyto typy křížová volání bude také považována za "Internet" volání služby App Service Environment.

Následující diagram ukazuje příklad Vrstvená architektura aplikace na jeden App Service Environment (např.) "Přední dveře" webové aplikace) na (třeba interní aplikace API back-end není by měla být přístupný z Internetu) druhý App Service Environment volání aplikace. 

![Volání mezi služby App Service Environment][CallsBetweenAppServiceEnvironments] 

V předchozím příkladu má služba App Service Environment "App Service Environment jeden" 192.23.1.2 odchozí IP adresu.  Pokud aplikace spuštěná na tomto App Service Environment budou díky odchozí volání do aplikace druhý App Service Environment ("App Service Environment dvě") a systémem umístěný ve stejné virtuální síti, odchozí volání se považuje za volání "Internet".  V důsledku síťový provoz přicházejících na druhé stránce App Service Environment zobrazí jako pocházející z 192.23.1.2 (tj. není podsítě rozsah adres první App Service Environment).

I když volání mezi různé prostředí App Service jsou považovány za "Internet" volání, pokud obě prostředí App Service jsou umístěny ve stejné oblasti Azure, síťový provoz zůstanou v místní síti Azure a není fyzicky toku přes veřejného Internetu.  V důsledku použít skupinu zabezpečení sítě v podsíti, druhý App Service Environment a Povolit jenom příchozí volání z první App Service Environment (jehož odchozí IP adresa je 192.23.1.2), čímž zajišťuje zabezpečenou komunikaci mezi aplikace Prostředí služby.

## <a name="additional-links-and-information"></a>Další odkazy a informace
Podrobnosti o příchozí porty používané prostředí App Service a použití skupin zabezpečení sítě k řízení příchozího provozu je k dispozici [sem][controllinginboundtraffic].

Podrobnosti o použití uživatele definované trasy udělit přístup k prostředí App Service Internetu je k dispozici v tomto [článku][ExpressRoute]. 

<!-- LINKS -->
[virtualnetwork]: http://azure.microsoft.com/services/virtual-network/
[controllinginboundtraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[ExpressRoute]:  app-service-app-service-environment-network-configuration-expressroute.md

<!-- IMAGES -->
[GeneralNetworkFlows]: ./media/app-service-app-service-environment-network-architecture-overview/NetworkOverview-1.png
[OutboundIPAddress]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundIPAddress-1.png
[OutboundNetworkAddresses]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundNetworkAddresses-1.png
[CallsBetweenAppServiceEnvironments]: ./media/app-service-app-service-environment-network-architecture-overview/CallsBetweenEnvironments-1.png

