---
title: Úvod do služby App Service Environment v1 – Azure
description: Další informace o funkci App Service Environment v1, která poskytuje zabezpečené, připojené k virtuální síti, vyhrazené škálovací jednotky pro všechny vaše aplikace.
services: app-service
documentationcenter: ''
author: stefsch
manager: erikre
editor: ''
ms.assetid: 78e6d4f5-da46-4eb5-a632-b5fdc17d2394
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 5a06e2577468baa6f3e46f838db11f1925ec0e1d
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53271602"
---
# <a name="introduction-to-app-service-environment-v1"></a>Úvod do služby App Service Environment v1

> [!NOTE]
> Tento článek je o App Service Environment v1.  Existuje novější verze služby App Service Environment, která se snadněji používá a běží na výkonnější infrastruktuře. Další informace o nové verzi spuštění s [Úvod do služby App Service Environment](intro.md).
> 

## <a name="overview"></a>Přehled
Služby App Service Environment je [Premium] [ PremiumTier] služeb možnost plánu [služby Azure App Service](../app-service-web-overview.md) , která poskytuje plně izolované a vyhrazené prostředí pro zabezpečené spouštění Aplikace Azure App Service ve velkém měřítku, včetně Web Apps, Mobile Apps a API Apps.  

Služby App Service Environment jsou ideální pro úlohy aplikací vyžadující:

* Velmi velké měřítko
* Izolace a bezpečný přístup k síti

Zákazníci tak můžou vytvářet více prostředí App Service v rámci jedné oblasti Azure, stejně jako ve víc oblastech Azure.  Díky tomu je prostředí App Service ideální pro horizontální škálování bez stavu aplikačních vrstev vysokou náročných úloh RPS.

Služby App Service Environment jsou izolované ke spouštění jenom aplikace jednoho zákazníka a vždycky jsou nasazené do virtuální sítě.  Zákazníci mají podrobnou kontrolu nad síťovými přenosy v obou vstupní a výstupní aplikace a aplikace můžete vytvářet vysokorychlostní zabezpečená připojení prostřednictvím virtuálních sítí a místním podnikovým prostředkům.

Přehled toho, jak služby App Service Environment povolit vysokou škálovatelností a zabezpečený přístup k síti, najdete v článku [konference Azurecon] [ AzureConDeepDive] v prostředích App Service!

Podrobně nyní na horizontální škálování, použití více App Service Environment najdete v článku o tom, jak nastavení [nároky na geografické distribuce aplikace][GeodistributedAppFootprint].

Konfiguraci architektury zabezpečení v konference Azurecon najdete v článku o implementaci [vrstvy architektury zabezpečení](app-service-app-service-environment-layered-security.md) s App Service Environment.

Aplikace běžící na App Service Environment může mít přístup chráněný branami upstreamovými zařízeními, jako jsou brány firewall webových aplikací (WAF).  V článku věnovaném [konfigurace brány WAF pro služby App Service Environment](app-service-app-service-environment-web-application-firewall.md) popisuje tento scénář. 

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="dedicated-compute-resources"></a>Vyhrazené výpočetní prostředky
Všechny výpočetní prostředky ve službě App Service Environment je vyhrazená jenom pro jedno předplatné a služby App Service Environment se dá nakonfigurovat s až 50 (50) výpočetních prostředků pro výhradní použití jedné aplikace.

Služby App Service Environment se skládá z front-endu výpočty fondu zdrojů, jakož i fondy prostředků výpočetní jedné do tří pracovních procesů. 

Front-endový fond obsahuje výpočetních prostředků za ukončení protokolu SSL jako a automatické vyrovnávání zatížení požadavků aplikací ve službě App Service Environment. 

Každý fond pracovních procesů obsahuje výpočetní prostředky přidělené [plány služby App Service][AppServicePlan], které pak obsahovat jednu nebo víc aplikací Azure App Service.  Vzhledem k tomu, že ve službě App Service Environment může být až tři fondy jinému pracovnímu procesu, budete mít volnost ve výběru různých výpočetních prostředků pro každý fond pracovních procesů.  

Například to umožňuje vytvořit jeden fond pracovních procesů s menším výpočetní prostředky pro plány služby App Service určený pro vývojové nebo testovací aplikace.  Fond pracovních procesů druhý (nebo dokonce třetí) použít výkonnější výpočetní prostředky, které jsou určené pro plány služby App Service spouštění produkčních aplikací.

Další podrobnosti o množství výpočetních prostředků dostupných pro fondy front-endu a pracovního procesu najdete v tématu [konfigurace služby App Service Environment][HowToConfigureanAppServiceEnvironment].  

Podrobné informace o dostupných výpočetních prostředků velikosti podporované ve službě App Service Environment [App Service – ceny] [ AppServicePricing] stránce a projděte si dostupné možnosti pro App Service Environment v Cenovou úroveň Premium.

## <a name="virtual-network-support"></a>Podpora virtuální sítě
Služby App Service Environment je možné vytvořit v **buď** virtuální sítí Azure Resource Manageru **nebo** virtuální síť modelu nasazení classic ([Další informace o virtuálních sítích] [MoreInfoOnVirtualNetworks]).  Protože služba App Service Environment vždy existuje ve virtuální síti a přesněji v rámci podsítě virtuální sítě, můžete využívat funkce zabezpečení virtuální sítě k řízení i příchozí a odchozí síťovou komunikaci.  

Služby App Service Environment může být buď internetového s použitím veřejné IP adresy nebo interní směřuje pouze adresy Azure nástroje pro vyrovnávání interní zatížení (ILB).

Můžete použít [skupiny zabezpečení sítě] [ NetworkSecurityGroups] omezit příchozí síťovou komunikaci na podsíť, ve které se nachází služby App Service Environment.  To umožňuje spouštět aplikace za upstreamovými zařízeními a službami, jako jsou brány firewall webových aplikací a síťoví poskytovatelé SaaS.

Aplikace také často potřebují přístup k firemním prostředkům, jako jsou třeba interní databáze a webové služby.  Běžným přístupem je, aby tyto koncové body k dispozici pouze pro interní síťový provoz směřující do služby Azure virtual network.  Jakmile App Service Environment je připojený ke stejné virtuální síti jako interní služby, aplikace běžící v prostředí můžete k nim přístup, včetně koncových bodů, které jsou dostupné prostřednictvím [Site-to-Site] [ SiteToSite] a [Azure ExpressRoute] [ ExpressRoute] připojení.

Pro další informace o fungování služby App Service Environment s virtuálními sítěmi a místními sítěmi najdete v následujících článcích na [Síťová architektura][NetworkArchitectureOverview], [řízení příchozí Provoz][ControllingInboundTraffic], a [zabezpečené připojení k back-EndY][SecurelyConnectingToBackends]. 

## <a name="getting-started"></a>Začínáme
Začínáme s App Service Environment najdete v článku [jak k vytvoření služby App Service Environment][HowToCreateAnAppServiceEnvironment]

Přehled architektury sítě služby App Service Environment, najdete v článku [přehled architektury sítě] [ NetworkArchitectureOverview] článku.

Podrobnosti o používání služby App Service Environment se službou ExpressRoute, najdete v následujícím článku na [Express Route a App Service Environment][NetworkConfigDetailsForExpressRoute].

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[PremiumTier]: https://azure.microsoft.com/pricing/details/app-service/
[MoreInfoOnVirtualNetworks]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePlan]: ../azure-web-sites-web-hosting-plans-in-depth-overview.md
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[LogicApps]: https://azure.microsoft.com/documentation/articles/app-service-logic-what-are-logic-apps/
[AzureConDeepDive]:  https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/
[GeodistributedAppFootprint]:  app-service-app-service-environment-geo-distributed-scale.md
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-multi-site
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/
[HowToConfigureanAppServiceEnvironment]:  app-service-web-configure-an-app-service-environment.md
[ControllingInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SecurelyConnectingToBackends]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NetworkArchitectureOverview]:  app-service-app-service-environment-network-architecture-overview.md
[NetworkConfigDetailsForExpressRoute]:  app-service-app-service-environment-network-configuration-expressroute.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 

<!-- IMAGES -->


