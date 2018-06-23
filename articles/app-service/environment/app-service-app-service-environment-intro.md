---
title: Úvod do aplikace služby prostředí v1
description: Další informace o funkci v1 App Service Environment, která poskytuje jednotek škálování zabezpečené, připojený k virtuální síti, vyhrazené pro spuštění všech aplikací.
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
ms.openlocfilehash: ca30818b015e95594d3b2c9861d98f24174c0aea
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2018
ms.locfileid: "36318159"
---
# <a name="introduction-to-app-service-environment-v1"></a>Úvod do aplikace služby prostředí v1

> [!NOTE]
> Tento článek je o v1 App Service Environment.  Existuje novější verze App Service Environment, který je jednodušší použít a běží na výkonnější infrastruktury. Další informace o nové verzi spuštění s [Úvod do služby App Service Environment](intro.md).
> 

## <a name="overview"></a>Přehled
Služby App Service Environment je [Premium] [ PremiumTier] služby možnost plánu [Azure App Service](../app-service-web-overview.md) poskytuje plně izolovaném a vyhrazeném prostředí pro zabezpečené spouštění Aplikace Azure App Service ve velkém rozsahu, včetně webových aplikací, mobilní aplikace a aplikace API.  

Služby App Service Environment jsou ideální pro aplikační procesy vyžadující:

* Velmi velký rozsah
* Izolace a bezpečný přístup

Zákazníci vytvářet více prostředí App Service v rámci jedné oblasti Azure, a také nad několika oblastmi Azure.  Tím je ideální pro vodorovně škálování vrstvy aplikace bez stavu na podporu vysoké zatížení RPS prostředí App Service.

Služby App Service Environment jsou izolované spouštění jenom jednoho zákazníka aplikací a vždy nasazených do virtuální sítě.  Zákazníci mají jemně odstupňovanou kontrolu nad obě aplikace příchozí a odchozí síťový provoz a aplikace může vytvořit vysokorychlostní zabezpečené připojení přes virtuální sítě k firemním prostředkům místně.

Přehled o tom, jak prostředí App Service povolit velkém rozsahu a zabezpečit přístup k síti, najdete v článku [podrobné informace AzureCon] [ AzureConDeepDive] na prostředí App Service!

Přímý informace na vodorovně škálování pomocí několika prostředí App Service najdete v článku o tom, jak instalace [geograficky distribuovaná aplikace nároků][GeodistributedAppFootprint].

Informace o tom, jak byla nakonfigurována Architektura zabezpečení uvedené v AzureCon podrobné informace, najdete v článku na implementaci [Architektura zabezpečení na základě](app-service-app-service-environment-layered-security.md) s prostředí App Service.

Aplikace běžící v prostředí App Service může mít svůj přístup závislé na nadřazený zařízení, jako jsou brány firewall systému webové aplikace (firewall webových aplikací).  V článku na [konfigurace firewall webových aplikací pro prostředí App Service](app-service-app-service-environment-web-application-firewall.md) popisuje tento scénář. 

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="dedicated-compute-resources"></a>Vyhrazený výpočetní prostředky
Všechny výpočetní prostředky ve službě App Service Environment jsou vyhrazené výhradně pro v rámci jednoho předplatného a služby App Service Environment se dá nakonfigurovat s až padesát (50) výpočetní prostředky pro výhradní použití jedné aplikace.

Služby App Service Environment se skládá z front-endu výpočetní fondu zdrojů, jakož i fondy jednu až tři pracovní výpočetních prostředků. 

Front-endu fond obsahuje výpočetní prostředky zodpovědná za ukončení protokolu SSL jako dobře automatické vyvažování zátěže požadavků aplikace v rámci služby App Service Environment. 

Každý pracovní fond obsahuje výpočetní prostředky přidělené [plány služby App Service][AppServicePlan], který naopak obsahovat jeden nebo více aplikacemi Azure App Service.  Vzhledem k tomu, že ve službě App Service Environment může být až tři fondy různých pracovních procesů, máte možnost vybrat různé výpočetní prostředky pro každý fond pracovních procesů.  

Například tímto způsobem lze vytvořit jeden fond pracovních procesů s méně výkonná výpočetní prostředky pro plány služby App Service určený pro vývoj nebo testování aplikací.  Fond pracovních procesů druhý (nebo i třetí) může použít výkonnější výpočetní prostředky, které jsou určené pro plány služby App Service spouští aplikace produkční.

Další informace o objemu výpočetní prostředky, které jsou k dispozici pro fondy front-endu a pracovního procesu, najdete v části [postup konfigurace služby App Service Environment][HowToConfigureanAppServiceEnvironment].  

Podrobné informace o dostupných výpočetních prostředků velikosti podporované ve službě App Service Environment [App Service – ceny] [ AppServicePricing] stránky a projděte si dostupné možnosti pro prostředí App Service v Cenová úroveň Premium.

## <a name="virtual-network-support"></a>Podpora virtuální sítě
Služby App Service Environment se dají vytvářet v **buď** virtuální síť Azure Resource Manager **nebo** virtuální síť modelu nasazení classic ([Další informace o virtuálních sítí] [MoreInfoOnVirtualNetworks]).  Vzhledem k tomu, že služby App Service Environment vždy existuje ve virtuální síti a přesněji v podsíti virtuální sítě, můžete využít funkce zabezpečení virtuálních sítí k řízení i příchozí a odchozí síťovou komunikaci.  

Služby App Service Environment může být buď internetové s veřejnou IP adresu, nebo interní, kterým čelí jenom adresu Azure vyrovnávání interní zatížení (ILB).

Můžete použít [skupin zabezpečení sítě] [ NetworkSecurityGroups] omezit příchozí síťovou komunikaci na podsíť, které se nachází služby App Service Environment.  To umožňuje spouštět aplikace za nadřazeného zařízení a služeb, jako jsou brány firewall webových aplikací a poskytovatelů SaaS sítě.

Aplikace také často potřebují přístup k firemním prostředkům, jako jsou třeba interní databáze a webové služby.  Běžně se provést tyto koncové body k dispozici pouze pro interní síťový provoz v rámci virtuální sítě Azure.  Jakmile služby App Service Environment se připojí ke stejné virtuální síti jako interní služby, aplikace běžící v prostředí k nim přístup, včetně koncových bodů, které jsou dostupné prostřednictvím [Site-to-Site] [ SiteToSite] a [Azure ExpressRoute] [ ExpressRoute] připojení.

Pro další informace o tom, jak prostředí App Service práci s virtuálními sítěmi a místními sítěmi najdete v následujících článcích na [síťovou architekturu][NetworkArchitectureOverview], [řízení příchozí Provoz][ControllingInboundTraffic], a [bezpečně připojování k back-EndY][SecurelyConnectingToBackends]. 

## <a name="getting-started"></a>Začínáme
Chcete-li začít pracovat s prostředí App Service, přečtěte si téma [jak k vytvoření služby App Service Environment][HowToCreateAnAppServiceEnvironment]

Přehled architektury sítě služby App Service Environment, najdete [přehled architektury sítě] [ NetworkArchitectureOverview] článku.

Podrobné informace o používání služby App Service Environment se službou ExpressRoute, najdete v následujícím článku na [Express Route a prostředí App Service][NetworkConfigDetailsForExpressRoute].

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[MoreInfoOnVirtualNetworks]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePlan]: ../azure-web-sites-web-hosting-plans-in-depth-overview.md
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[LogicApps]: http://azure.microsoft.com/documentation/articles/app-service-logic-what-are-logic-apps/
[AzureConDeepDive]:  https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/
[GeodistributedAppFootprint]:  app-service-app-service-environment-geo-distributed-scale.md
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-multi-site
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[HowToConfigureanAppServiceEnvironment]:  app-service-web-configure-an-app-service-environment.md
[ControllingInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SecurelyConnectingToBackends]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NetworkArchitectureOverview]:  app-service-app-service-environment-network-architecture-overview.md
[NetworkConfigDetailsForExpressRoute]:  app-service-app-service-environment-network-configuration-expressroute.md
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/ 

<!-- IMAGES -->


