---
title: Úvod do ase v1
description: Přečtěte si o funkcích prostředí služby App Service v1. Tento dokument je k dispozici pouze pro zákazníky, kteří používají starší verze v1 ASE.
author: stefsch
ms.assetid: 78e6d4f5-da46-4eb5-a632-b5fdc17d2394
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: f655b7793bfbb5bbeddfc2f1f8e7bc973dabeb4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687358"
---
# <a name="introduction-to-app-service-environment-v1"></a>Úvod do prostředí služby App Service v1

> [!NOTE]
> Tento článek je o prostředí služby App Service v1.  K dispozici je novější verze prostředí služby App Service, která se snadněji používá a běží na výkonnější infrastruktuře. Další informace o nové verzi začněte [s úvodem do prostředí služby App Service](intro.md).

## <a name="overview"></a>Přehled

Prostředí služby App Service Environment je možnost plánu [služby Premium][PremiumTier] [služby Azure App Service,](../overview.md) která poskytuje plně izolované a vyhrazené prostředí pro bezpečné spouštění aplikací Služby Azure App Service ve velkém měřítku, včetně webových aplikací, mobilních aplikací a aplikací API.  

Prostředí služby App Service jsou ideální pro úlohy aplikací, které vyžadují:

* Velmi vysoké měřítko
* Izolace a zabezpečený přístup k síti

Zákazníci můžou vytvořit více prostředí služby App Service v rámci jedné oblasti Azure i ve více oblastech Azure.  Díky tomu jsou prostředí služby App Service ideální pro horizontální škálování úrovní aplikací bez stavu na podporu vysokých úloh RPS.

Prostředí služby App Service jsou izolované na spouštění pouze aplikací jednoho zákazníka a jsou vždy nasazeny do virtuální sítě.  Zákazníci mají jemně odstupňovanou kontrolu nad příchozím i odchozím síťovým provozem aplikací a aplikace mohou navázat vysokorychlostní zabezpečená připojení přes virtuální sítě k místním podnikovým prostředkům.

Přehled toho, jak prostředí služby App Service umožňují vysoký a zabezpečený přístup k síti, najdete v tématu [AzureCon Deep Dive][AzureConDeepDive] on App Service Environments!

Podrobné informace o horizontálním škálování pomocí více prostředí služby App Service najdete v článku o nastavení [geograficky distribuované aplikace .][GeodistributedAppFootprint]

Chcete-li zjistit, jak byla nakonfigurována architektura zabezpečení zobrazená v azurecondeep dive, přečtěte si článek o implementaci [vrstvené architektury zabezpečení](app-service-app-service-environment-layered-security.md) s prostředím služby App Service.

Aplikace spuštěné v prostředích služby App Service mohou mít přístup bránou pomocí upstream zařízení, jako jsou brány firewall webových aplikací (WAF).  Článek o [konfiguraci WAF pro prostředí služby App Service popisuje](app-service-app-service-environment-web-application-firewall.md) tento scénář.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="dedicated-compute-resources"></a>Vyhrazené výpočetní prostředky

Všechny výpočetní prostředky v prostředí služby App Service jsou vyhrazeny výhradně pro jedno předplatné a prostředí služby App Service lze nakonfigurovat s až padesát (50) výpočetníprostředky pro výhradní použití v jedné aplikaci.

Prostředí služby App Service se skládá z fondu výpočetních prostředků front-endu a také z jednoho až tří fondů výpočetních prostředků pracovního procesu.

Front-endový fond obsahuje výpočetní prostředky odpovědné za ukončení SSL a také automatické vyrovnávání zatížení požadavků aplikací v prostředí služby App Service.

Každý fond pracovních prostředků obsahuje výpočetní prostředky přidělené [na plány služby App Service][AppServicePlan], které zase obsahují jednu nebo více aplikací Azure App Service.  Vzhledem k tomu, že může být až tři různé fondy pracovních procesů v prostředí služby App Service, máte možnost zvolit různé výpočetní prostředky pro každý fond pracovních procesů.  

To například umožňuje vytvořit jeden fond pracovních prostředků s méně výkonnými výpočetními prostředky pro plány služby App Service určené pro vývoj nebo testování aplikací.  Druhý (nebo dokonce třetí) fond pracovních sil by mohl používat výkonnější výpočetní prostředky určené pro plány služby App Service, které spouštějí produkční aplikace.

Další podrobnosti o množství výpočetních prostředků, které jsou k dispozici pro front-endové a pracovní fondy, najdete v [tématu Jak nakonfigurovat prostředí služby App Service][HowToConfigureanAppServiceEnvironment].  

Podrobnosti o dostupných velikostech výpočetních prostředků podporovaných v prostředí služby App Service najděte na stránce [Ceny služby App Service][AppServicePricing] a projděte si dostupné možnosti prostředí služby App Service v cenové úrovni Premium.

## <a name="virtual-network-support"></a>Podpora virtuálních sítí

Prostředí služby App Service lze vytvořit **ve** virtuální síti Azure Resource Manager **nebo** ve virtuální síti modelu klasického nasazení ([další informace o virtuálních sítích][MoreInfoOnVirtualNetworks]).  Vzhledem k tomu, že prostředí služby App Service vždy existuje ve virtuální síti a přesněji v rámci podsítě virtuální sítě, můžete využít funkce zabezpečení virtuálních sítí k řízení příchozí i odchozí síťové komunikace.  

Prostředí služby App Service může být buď internetčelí s veřejnou IP adresou nebo vnitřní čelí pouze Azure Internal Load Balancer (ILB) adresu.

[Skupiny zabezpečení sítě][NetworkSecurityGroups] můžete použít k omezení příchozí síťové komunikace do podsítě, kde se nachází prostředí služby App Service.  To umožňuje spouštět aplikace za upstream zařízení a služeb, jako jsou brány firewall webových aplikací a poskytovatelé síťových SaaS.

Aplikace také často potřebují přístup k firemním prostředkům, jako jsou třeba interní databáze a webové služby.  Běžným přístupem je zpřístupnit tyto koncové body pouze internímu síťovému provozu, který probíhá v rámci virtuální sítě Azure.  Jakmile se prostředí služby App Service připojí ke stejné virtuální síti jako interní služby, aplikace spuštěné v prostředí k nim budou mít přístup, včetně koncových bodů dostupných prostřednictvím připojení [Site-to-Site][SiteToSite] a [Azure ExpressRoute.][ExpressRoute]

Další podrobnosti o tom, jak prostředí služby App Service fungují s virtuálními sítěmi a místními sítěmi, naleznete v následujících článcích o [síťové architektuře][NetworkArchitectureOverview], [řízení příchozího provozu][ControllingInboundTraffic]a [bezpečném připojení k back-endům][SecurelyConnectingToBackends]. 

## <a name="getting-started"></a>Začínáme

Pokud chcete začít s prostředím služby App Service, přečtěte si informace [o tom, jak vytvořit prostředí služby App Service.][HowToCreateAnAppServiceEnvironment]

Přehled síťové architektury prostředí služby App Service najdete v článku [Přehled síťové architektury.][NetworkArchitectureOverview]

Podrobnosti o používání prostředí služby App Service s ExpressRoute najdete v následujícím článku o [prostředíexpresních tras a služeb aplikací][NetworkConfigDetailsForExpressRoute].

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[PremiumTier]: https://azure.microsoft.com/pricing/details/app-service/
[MoreInfoOnVirtualNetworks]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePlan]: ../overview-hosting-plans.md
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