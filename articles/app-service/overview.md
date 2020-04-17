---
title: Přehled
description: Zjistěte, jak služba Azure App Service pomáhá nasazovat, hostovat a používat webové aplikace
ms.assetid: 94af2caf-a2ec-4415-a097-f60694b860b3
ms.topic: overview
ms.date: 01/04/2017
ms.custom: mvc, seodec18
ms.openlocfilehash: f8449c516bce3f3ab949e88808f73c7240995f99
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535634"
---
# <a name="app-service-overview"></a>Přehled služby App Service

*Azure App Service* je služba založená na protokolu HTTP pro hostování webových aplikací, rest api a mobilní back-endy. Můžete vyvíjet ve svém oblíbeném jazyce, ať už jde o .NET, .NET Core, Javu, Ruby, Node.js, PHP nebo Python. Aplikace běží a škálují s lehkostí v prostředích se systémem Windows i Linux. Informace o prostředích založených na Linuxu najdete v tématu [App Service v Linuxu](containers/app-service-linux-intro.md). 

Služba App Service do vaší aplikace nejen přidává výkon Microsoft Azure, jako je zabezpečení, vyrovnávání zatížení, automatické škálování a automatizovaná správa. Můžete také využít jeho funkce DevOps, jako je například průběžné nasazení z Azure DevOps, GitHub, Docker Hub a dalších zdrojů, správa balíčků, pracovní prostředí, vlastní domény a tls/ssl certifikáty. 

Se službou App Service platíte jenom za výpočetní prostředky Azure, které využijete. Výpočetní prostředky, které používáte, jsou určeny _plánem služby App Service,_ na kterých spouštěte aplikace. Další informace najdete v [tématu Přehled plánů služby Azure App Service](overview-hosting-plans.md).

## <a name="why-use-app-service"></a>Proč používat App Service?

Zde jsou některé klíčové funkce služby App Service:

* **Více jazyků a architektur** – Služba App Service má prvotřídní podporu pro ASP.NET, ASP.NET Core, Java, Ruby, Node.js, PHP nebo Python. Můžete také spouštět [PowerShell a další skripty nebo spustitelné soubory](webjobs-create.md) jako služby na pozadí.
* **Optimalizace DevOps** – Můžete nastavit [kontinuální integraci a nasazování](deploy-continuous-deployment.md) pomocí služeb Azure DevOps, GitHub, BitBucket, Docker Hub nebo Azure Container Registry. Aktualizace lze podporovat prostřednictvím [testovacího a přípravného prostředí](deploy-staging-slots.md). Spravujte aplikace ve službě App Service pomocí prostředí [Azure PowerShell](/powershell/azureps-cmdlets-docs) nebo [multiplatformního rozhraní příkazového řádku (CLI)](/cli/azure/install-azure-cli).
* **Globální škálování s vysokou dostupností:** Můžete ručně i automaticky škálovat pro [vertikální](manage-scale-up.md) nebo [horizontální](../monitoring-and-diagnostics/insights-how-to-scale.md) navýšení kapacity. Aplikace je možné hostovat kdekoli v globální infrastruktuře datacenter Microsoftu a smlouva [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) pro App Service zaručuje vysokou dostupnost.
* **Připojení k platformám SaaS a místním datům** – Můžete si vybírat z více než 50 [konektorů](../connectors/apis-list.md) pro podnikové systémy (například SAP), služby SaaS (například Salesforce) a internetové služby (například Facebook). Získejte přístup k místním datům pomocí [hybridních připojení](app-service-hybrid-connections.md) a [virtuálních sítí Azure](web-sites-integrate-with-vnet.md).
* **Zabezpečení a dodržování předpisů** – Služba App Service je [kompatibilní se standardy ISO, SOC a PCI](https://www.microsoft.com/en-us/trustcenter). Uživatele můžete ověřovat pomocí služby [Azure Active Directory](configure-authentication-provider-aad.md) nebo přes přihlášení prostřednictvím sociální sítě ([Google](configure-authentication-provider-google.md), [Facebook](configure-authentication-provider-facebook.md), [Twitter](configure-authentication-provider-twitter.md) a [Microsoft](configure-authentication-provider-microsoft.md)). Můžete vytvořit [omezení IP adres](app-service-ip-restrictions.md) a [spravovat identity služby](overview-managed-identity.md).
* **Šablony aplikací** – Vyberte si z rozsáhlého seznamu šablon aplikací na webu [Azure Marketplace](https://azure.microsoft.com/marketplace/), například pro systémy WordPress, Joomla nebo Drupal.
* **Integrace sady Visual Studio** – Vyhrazené nástroje v sadě Visual Studio zjednodušují práci při vytváření, nasazování a ladění.
* **ROZHRANÍ API a mobilní funkce** – služba App Service poskytuje podporu CORS na klíč pro scénáře rozhraní RESTful API a zjednodušuje scénáře mobilních aplikací povolením ověřování, synchronizace offline dat, nabízených oznámení a dalších.
* **Kód bez serveru** – Můžete spouštět fragment kódu nebo skript na vyžádání, aniž byste museli explicitně zřizovat nebo spravovat infrastrukturu, a platíte jenom za výpočetní čas, který váš kód skutečně využije (viz [Azure Functions](/azure/azure-functions/)).

Kromě služby App Service nabízí Azure další služby, které lze použít pro hostování webů a webových aplikací. Pro většinu scénářů app service je nejlepší volbou.  Pro architekturu mikroslužeb zvažte [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric). Pokud potřebujete větší kontrolu nad virtuálními počítači, na kterých se kód spouští, zvažte službu [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/). Příklady typických aplikačních scénářů, k nimž můžete použít službu Web Apps, naleznete v tématu [Srovnání Azure App Service, Virtual Machines, Service Fabric a Cloud Services](overview-compare.md).

## <a name="next-steps"></a>Další kroky

Vytvořte svou první aplikaci.

> [!div class="nextstepaction"]
> [ASP.NET Core](app-service-web-get-started-dotnet.md)

> [!div class="nextstepaction"]
> [ASP.NET](app-service-web-get-started-dotnet-framework.md)

> [!div class="nextstepaction"]
> [PHP](app-service-web-get-started-php.md)

> [!div class="nextstepaction"]
> [Ruby (v Linuxu)](containers/quickstart-ruby.md)

> [!div class="nextstepaction"]
> [Node.js](app-service-web-get-started-nodejs.md)

> [!div class="nextstepaction"]
> [Java](app-service-web-get-started-java.md)

> [!div class="nextstepaction"]
> [Python (on Linux)](containers/quickstart-python.md)

> [!div class="nextstepaction"]
> [HTML](app-service-web-get-started-html.md)
