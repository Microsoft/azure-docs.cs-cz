---
title: Seznámení s přidaným MECHANISMem v1
description: Přečtěte si o funkcích App Service Environment v1. Tento dokument je k dispozici pouze pro zákazníky, kteří používají starší pomocného uživatele v1.
author: stefsch
ms.assetid: 78e6d4f5-da46-4eb5-a632-b5fdc17d2394
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 878cef39ade0487c03dba83c9e3631fe3c72088f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96008616"
---
# <a name="introduction-to-app-service-environment-v1"></a>Úvod do App Service Environment v1

> [!NOTE]
> Tento článek se týká App Service Environment v1.  Existuje novější verze App Service Environment, kterou je snazší použít a která je spuštěná na výkonnější infrastruktuře. Další informace o nové verzi začíná [úvodem do App Service Environment](intro.md).

## <a name="overview"></a>Přehled

App Service Environment je plán služby [Premium][PremiumTier] [Azure App Service](../overview.md) , který poskytuje plně izolované a vyhrazené prostředí pro bezpečné spouštění Azure App Service aplikací ve velkém měřítku, včetně Web Apps, Mobile Apps a API Apps.  

App Service prostředí jsou ideální pro úlohy aplikací, které vyžadují:

* Velmi vysoké měřítko
* Izolace a zabezpečení přístupu k síti

Zákazníci můžou vytvořit několik App Servicech prostředí v rámci jedné oblasti Azure a také napříč několika oblastmi Azure.  Díky tomu jsou App Service prostředí ideální pro horizontální škálování bez stavů, a to v podpoře vysoce RPS úloh.

Prostředí App Service jsou izolovaná na spouštění pouze jednoho zákazníka a jsou vždy nasazena do virtuální sítě.  Zákazníci mají jemně odstupňovanou kontrolu nad příchozím i odchozím síťovým provozem aplikací a aplikace můžou vytvořit vysokorychlostní zabezpečená připojení přes virtuální sítě k místním podnikovým prostředkům.

Přehled toho, jak App Service prostředí umožňují zajistit vysokou škálu a zabezpečení přístupu k síti, najdete v článku [AzureCon hloubkové podrobně][AzureConDeepDive] v prostředích App Service!

Podrobné informace o horizontálním škálování pomocí více App Service prostředí najdete v článku o tom, jak nastavit zobrazení [geograficky distribuovaných aplikací][GeodistributedAppFootprint].

Pokud chcete zjistit, jak se nakonfigurovala Architektura zabezpečení zobrazená v AzureCon hloubkové podrobně, přečtěte si článek o implementaci [vrstvené architektury zabezpečení](app-service-app-service-environment-layered-security.md) s App Service prostředími.

Aplikace spuštěné v prostředích App Service můžou mít přístup gated proti nadřazenému zařízení, jako jsou například brány firewall webových aplikací (WAF).  Tento scénář popisuje článek týkající se [Konfigurace WAF pro prostředí App Service](app-service-app-service-environment-web-application-firewall.md) .

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="dedicated-compute-resources"></a>Vyhrazené výpočetní prostředky

Všechny výpočetní prostředky ve App Service Environment jsou vyhrazené výhradně pro jedno předplatné a App Service Environment se dá nakonfigurovat s až 50 (50) výpočetními prostředky pro výhradní použití jedinou aplikací.

App Service Environment se skládá z front-endového výpočetního fondu zdrojů a také jednoho až tří fondů výpočetních prostředků pracovního procesu.

Front-end fond obsahuje výpočetní prostředky zodpovědné za ukončení TLS a také automatické vyrovnávání zatížení žádostí o aplikace v rámci App Service Environment.

Každý fond pracovních procesů obsahuje výpočetní prostředky přidělené [App Servicem plánům][AppServicePlan], které zase obsahují jednu nebo více Azure App Servicech aplikací.  Vzhledem k tomu, že v App Service Environment může existovat až tři různé fondy pracovních procesů, máte flexibilitu pro výběr různých výpočetních prostředků pro každý fond pracovních procesů.  

To vám například umožní vytvořit jeden fond pracovních procesů s méně výkonnými výpočetními prostředky pro App Service plány určené pro vývojové nebo testovací aplikace.  Druhý (nebo třetí) fond pracovních procesů by mohl využívat výkonnější výpočetní prostředky určené pro App Service plány, na kterých běží provozní aplikace.

Další podrobnosti o množství výpočetních prostředků, které jsou dostupné pro front-end a fondy pracovních procesů, najdete v tématu [Postup konfigurace App Service Environment][HowToConfigureanAppServiceEnvironment].  

Podrobnosti o dostupných velikostech výpočetních prostředků podporovaných v App Service Environment najdete na stránce s [cenami App Service][AppServicePricing] a Projděte si dostupné možnosti pro App Service prostředí v cenové úrovni Premium.

## <a name="virtual-network-support"></a>Podpora Virtual Network

App Service Environment je možné vytvořit **buď** v Azure Resource Manager virtuální síti, **nebo** ve virtuální síti modelu nasazení Classic ([Další informace o virtuálních sítích][MoreInfoOnVirtualNetworks]).  Vzhledem k tomu, že App Service Environment vždy existuje ve virtuální síti a přesněji v rámci podsítě virtuální sítě, můžete využívat funkce zabezpečení virtuálních sítí k řízení příchozí i odchozí síťové komunikace.  

App Service Environment může být buď internetový, s veřejnou IP adresou, nebo interní s přístupem pouze k internímu Load Balancer (interního nástroje) Azure.

Pomocí [skupin zabezpečení sítě][NetworkSecurityGroups] můžete omezit příchozí síťovou komunikaci na podsíť, ve které se nachází App Service Environment.  To vám umožní spouštět aplikace za nadřazenými zařízeními a službami, jako jsou brány firewall webových aplikací a poskytovatelé síťových SaaS.

Aplikace také často potřebují přístup k firemním prostředkům, jako jsou třeba interní databáze a webové služby.  Běžným přístupem je zpřístupnění těchto koncových bodů pouze internímu síťovému provozu v rámci služby Azure Virtual Network.  Jakmile je App Service Environment připojen ke stejné virtuální síti jako interní služby, aplikace spuštěné v prostředí k nim budou mít přístup, včetně koncových bodů dosažitelných prostřednictvím připojení [site-to-site][SiteToSite] a [Azure ExpressRoute][ExpressRoute] .

Další informace o tom, jak App Service prostředí fungují s virtuálními sítěmi a místními sítěmi, najdete v následujících článcích o [architektuře sítě][NetworkArchitectureOverview], [řízení příchozího provozu][ControllingInboundTraffic]a [zabezpečeném připojení k back-][SecurelyConnectingToBackends]Endu. 

## <a name="getting-started"></a>Začínáme

Pokud chcete začít pracovat s App Service prostředími, přečtěte si téma [vytvoření App Service Environment][HowToCreateAnAppServiceEnvironment]

Přehled architektury App Service Environment sítě najdete v článku [Přehled architektury sítě][NetworkArchitectureOverview] .

Podrobnosti o použití App Service Environment s ExpressRoute najdete v následujícím článku na [prostředích Express Route and App Service][NetworkConfigDetailsForExpressRoute].

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[PremiumTier]: https://azure.microsoft.com/pricing/details/app-service/
[MoreInfoOnVirtualNetworks]: ../../virtual-network/virtual-networks-faq.md
[AppServicePlan]: ../overview-hosting-plans.md
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[LogicApps]: ../../logic-apps/logic-apps-overview.md
[AzureConDeepDive]:  https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/
[GeodistributedAppFootprint]:  app-service-app-service-environment-geo-distributed-scale.md
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[SiteToSite]: ../../vpn-gateway/vpn-gateway-multi-site.md
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/
[HowToConfigureanAppServiceEnvironment]:  app-service-web-configure-an-app-service-environment.md
[ControllingInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SecurelyConnectingToBackends]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NetworkArchitectureOverview]:  app-service-app-service-environment-network-architecture-overview.md
[NetworkConfigDetailsForExpressRoute]:  app-service-app-service-environment-network-configuration-expressroute.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 

<!-- IMAGES -->