---
title: Úvod
description: Přečtěte si, jak Azure App Service prostředí vám pomůžou škálovat, zabezpečit a optimalizovat aplikace v plně izolovaném a vyhrazeném prostředí.
author: ccompy
ms.assetid: 3c7eaefa-1850-4643-8540-428e8982b7cb
ms.topic: overview
ms.date: 04/19/2018
ms.author: ccompy
ms.custom: mvc, seodec18
ms.openlocfilehash: 6fff19498e9ca70991d3190165df70a48136f502
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92631134"
---
# <a name="introduction-to-the-app-service-environments"></a>Úvod do služby App Service Environment #
## <a name="overview"></a>Přehled ##

Azure App Service Environment je funkce služby Azure App Service, která poskytuje plně izolované a vyhrazené prostředí pro bezpečné spouštění aplikací Azure App Service ve velkém měřítku. Tato funkce umožňuje hostovat vaše:

* Webové aplikace pro Windows
* Webové aplikace pro Linux 
* Kontejnery Dockeru
* Mobilní aplikace
* Functions

Služby App Service Environment (ASE) jsou vhodné pro úlohy aplikací, které mají tyto požadavky:

* Velmi velké měřítko
* Izolace a bezpečný přístup k síti
* Využití velkého množství paměti

Zákazníci můžou vytvořit víc služeb ASE v jedné oblasti Azure nebo v několika oblastech Azure. Díky této flexibilitě je služby ASE ideální pro horizontální škálování bezstavových aplikačních vrstev v rámci podpory úloh vysoké požadavků za sekundu (RPS).

Služby ASE hostitelské aplikace jenom od jednoho zákazníka a tak učinit v jednom ze svých virtuální sítě. Zákazníci mají podrobnou kontrolu nad příchozími i odchozími přenosy v síti aplikací. Aplikace můžou prostřednictvím sítí VPN vytvářet vysokorychlostní zabezpečená připojení k místním firemním prostředkům.

* Služby ASE mají vlastní cenovou úroveň – přečtěte si, jak [izolovaná nabídka](https://channel9.msdn.com/Shows/Azure-Friday/Security-and-Horsepower-with-App-Service-The-New-Isolated-Offering?term=app%20service%20environment) pomáhá zajistit hyperškálovatelnost a zabezpečení.
* Služby [App Service Environments v2](https://channel9.msdn.com/Blogs/Azure/Azure-Application-Service-Environments-v2-Private-PaaS-Environments-in-the-Cloud?term=app%20service%20environment) poskytují rámec pro zabezpečení aplikací v podsíti vaší sítě a také vaše vlastní nasazení služby Azure App Service.
* K horizontálnímu škálování se dá použít víc služeb ASE. Další informace najdete v článku [Nastavení náročnosti geografické distribuce aplikace](app-service-app-service-environment-geo-distributed-scale.md).
* Služby ASE se dají použít ke konfiguraci architektury zabezpečení, jak je patrné z podrobných informací z konference AzureCon. Informace o způsobu konfigurace architektury zabezpečení v podrobných informacích z konference AzureCon najdete v [článku o implementaci vrstvené architektury zabezpečení](app-service-app-service-environment-layered-security.md) se službami App Service Environment.
* U aplikací využívajících služby ASE mají je přístup chráněný branami v podobě upstreamovými zařízeními, jako jsou brány firewall webových aplikací (WAF). Další informace najdete v článku [Firewall webových aplikací (WAF)][AppGW].
* Prostředí App Service lze nasadit do Zóny dostupnosti (AZ) pomocí připnutí zóny.  Další podrobnosti najdete v tématu [podpora Zóny dostupnosti App Service Environment][ASEAZ] .

## <a name="dedicated-environment"></a>Vyhrazené prostředí ##

Služba ASE je vyhrazená jenom pro jedno předplatné a může být hostitelem 100 instancí plánů služby App Service. Tento rozsah může zahrnovat 100 instancí v rámci jednoho plánu služby App Service, ale i 100 plánů služby App Service s jednou instancí a veškeré další kombinace.

Služba ASE se skládá z front-endů a pracovních procesů. Front-endy zodpovídají za ukončení protokolu HTTP/HTTPS a automatické vyrovnávání zatížení požadavků aplikací ve službě ASE. Front-endy se přidávají automaticky při horizontálním škálování plánů služby App Service ve službě ASE.

Pracovní procesy jsou role, které jsou hostiteli zákaznických aplikací. Pracovní procesy jsou dostupné ve třech pevných velikostech:

* Jeden virtuální procesor / 3,5 GB paměti RAM
* Dva virtuální procesory / 7 GB paměti RAM
* Čtyři virtuální procesory / 14 GB paměti RAM

Zákazníci nemusí spravovat front-endy ani pracovní procesy. Veškerá infrastruktura se přidává automaticky s tím, jak zákazníci horizontálně navyšují kapacitu svých plánů služby App Service. Při vytváření nebo škálování plánů služby App Service ve službě ASE se podle potřeby přidává nebo odebírá nutná infrastruktura.

Za službu ASE se účtuje pevný měsíční poplatek za infrastrukturu, který se nemění podle velikosti služby ASE. Kromě toho se účtuje poplatek za každý virtuální procesor v plánu služby App Service. Všechny aplikace hostované ve službě ASE jsou ve skladové položce s izolovanou cenou. Informace o cenách pro služby ASE najdete na stránce [Ceny služeb App Service][Pricing], která obsahuje také dostupné možnosti služeb ASE.

## <a name="virtual-network-support"></a>Podpora virtuální sítě ##

Funkce pomocného mechanismu je nasazení Azure App Service přímo do Azure Resource Manager virtuální sítě zákazníka. Další informace o virtuálních sítí Azure najdete v článku [Virtuální sítě Azure – nejčastější dotazy](../../virtual-network/virtual-networks-faq.md). Služba ASE vždycky existuje ve virtuální síti, přesněji řečeno v určité podsíti virtuální sítě. Pomocí funkcí zabezpečení virtuálních sítí můžete řídit příchozí a odchozí síťovou komunikaci svých aplikací.

Služba ASE může být obrácená buď na internet a mít veřejnou IP adresu, nebo do interního prostředí a mít jenom adresu interního nástroje pro vyrovnávání zatížení Azure.

[Skupiny zabezpečení sítě][NSGs] omezují příchozí síťovou komunikaci na podsíť, ve které se nachází služba ASE. Pomocí skupin zabezpečení sítě můžete spouštět aplikace za upstreamovými zařízeními a službami, jako jsou brány WAF a síťoví poskytovatelé SaaS.

Aplikace také často potřebují přístup k firemním prostředkům, jako jsou třeba interní databáze a webové služby. Pokud nasadíte službu ASE ve virtuální síti, která má připojení VPN k místní síti, aplikace v této službě ASE můžou získat přístup k místním prostředkům. Tato možnost platí bez ohledu na to, jestli se jedná o síť VPN typu [site-to-site](../../vpn-gateway/vpn-gateway-multi-site.md), nebo o síť VPN [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/).

Další informace o tom, jak služby ASE fungují s virtuálními sítěmi a místními sítěmi, najdete v článku [Aspekty sítí služby App Service Environment][ASENetwork].

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Application-Service-Environments-v2-Private-PaaS-Environments-in-the-Cloud/player]

## <a name="app-service-environment-v1"></a>App Service Environment v1 ##

Služba App Service Environment má dvě verze: ASEv1 a ASEv2. Předchozí informace se týkaly verze ASEv2. V této části jsou uvedené rozdíly mezi verzemi ASEv1 a ASEv2. 

Ve verzi ASEv1 je potřeba spravovat všechny prostředky ručně. To se týká front-endů, pracovních procesů a IP adres používaných pro zabezpečení SSL na základě protokolu IP. Před horizontálním navýšením kapacity vašeho plánu služby App Service je potřeba nejdřív horizontálně navýšit kapacitu fondu pracovních procesů, který má být jeho hostitelem.

Verze ASEv1 používá jiný cenový model než verze ASEv2. Ve verzi ASEv1 se platí za každý přidělený virtuální procesor. To zahrnuje i virtuální procesory používané pro front-endy nebo pracovní procesy, které nejsou hostiteli žádných úloh. Ve verzi ASEv1 je výchozí maximální velikost služby ASE celkem 55 hostitelů. To zahrnuje pracovní procesy i front-endy. Jedna z výhod verze ASEv1 spočívá v tom, že se dá nasadit do klasické virtuální sítě i do virtuální sítě Resource Manager. Další informace o verzi ASEv1 najdete v článku [Úvod do služby App Service Environment verze 1][ASEv1Intro].

<!--Links-->
[App Service Environments v2]: https://channel9.msdn.com/Blogs/Azure/Azure-Application-Service-Environments-v2-Private-PaaS-Environments-in-the-Cloud?term=app%20service%20environment
[Isolated offering]: https://channel9.msdn.com/Shows/Azure-Friday/Security-and-Horsepower-with-App-Service-The-New-Isolated-Offering?term=app%20service%20environment
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[webapps]: ../overview.md
[mobileapps]: /previous-versions/azure/app-service-mobile/app-service-mobile-value-prop
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[ASEAZ]: https://azure.github.io/AppService/2019/12/12/App-Service-Environment-Support-for-Availability-Zones.html