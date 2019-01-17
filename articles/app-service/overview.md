---
title: Přehled služby App Service – Azure | Dokumentace Microsoftu
description: Zjistěte, jak služba Azure App Service pomáhá nasazovat, hostovat a používat webové aplikace
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: 94af2caf-a2ec-4415-a097-f60694b860b3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 01/04/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 5f3fed9255b86696b0e71c70adcd7ff23c0573d9
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54352440"
---
# <a name="app-service-overview"></a>Přehled App Service

*Azure App Service* je služba pro hostování webových aplikací, rozhraní REST API a mobilní back-EndY. Můžete vyvíjet ve svém oblíbeném jazyce, ať už jde o .NET, .NET Core, Javu, Ruby, Node.js, PHP nebo Python. Aplikace spouštění a škálování s lehkostí a elegancí na Windows i linuxem prostředí. Prostředí založené na Linuxu najdete v části App Service na [App Service v Linuxu](containers/app-service-linux-intro.md). 

App Service nejen přidá do vaší aplikace, jako je zabezpečení, Vyrovnávání zatížení, automatické škálování, výkon Microsoft Azure a automatizované správy. Můžete také využívat její možnosti DevOps, jako jsou průběžné nasazování z Azure DevOps, GitHubu, Docker Hubu a dalších zdrojů, správa balíčků, přípravná prostředí, vlastní doména a certifikáty SSL. 

Se službou App Service platíte jenom za výpočetní prostředky Azure, které využijete. Určuje výpočetní prostředky, které používáte _plán služby App Service_ spouštějte svoje aplikace na. Další informace najdete v tématu [přehled plánů služby Azure App Service](overview-hosting-plans.md).

## <a name="why-use-app-service"></a>Proč používat App Service?

Tady jsou některé klíčové funkce služby App Service:

* **Více jazyků a rozhraní** – App Service zahrnuje prvotřídní podporu jazyků ASP.NET, ASP.NET Core, Javy, Ruby, Node.js, PHP nebo Python. Můžete také spouštět [PowerShell a další skripty nebo spustitelné soubory](webjobs-create.md) jako služby na pozadí.
* **Optimalizace DevOps** – Můžete nastavit [kontinuální integraci a nasazování](deploy-continuous-deployment.md) pomocí služeb Azure DevOps, GitHub, BitBucket, Docker Hub nebo Azure Container Registry. Aktualizace lze podporovat prostřednictvím [testovacího a přípravného prostředí](deploy-staging-slots.md). Spravujte aplikace ve službě App Service pomocí prostředí [Azure PowerShell](/powershell/azureps-cmdlets-docs) nebo [multiplatformního rozhraní příkazového řádku (CLI)](/cli/azure/install-azure-cli).
* **Globální škálování s vysokou dostupností:** Můžete ručně i automaticky škálovat pro účely [vertikálního](web-sites-scale.md) nebo [horizontálního](../monitoring-and-diagnostics/insights-how-to-scale.md) navýšení kapacity. Aplikace je možné hostovat kdekoli v globální infrastruktuře datacenter Microsoftu a smlouva [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) pro App Service zaručuje vysokou dostupnost.
* **Připojení k platformám SaaS a místním datům** – Můžete si vybírat z více než 50 [konektorů](../connectors/apis-list.md) pro podnikové systémy (například SAP), služby SaaS (například Salesforce) a internetové služby (například Facebook). Získejte přístup k místním datům pomocí [hybridních připojení](app-service-hybrid-connections.md) a [virtuálních sítí Azure](web-sites-integrate-with-vnet.md).
* **Zabezpečení a dodržování předpisů** – Služba App Service je [kompatibilní se standardy ISO, SOC a PCI](https://www.microsoft.com/en-us/trustcenter). Uživatele můžete ověřovat pomocí služby [Azure Active Directory](configure-authentication-provider-aad.md) nebo přes přihlášení prostřednictvím sociální sítě ([Google](configure-authentication-provider-google.md), [Facebook](configure-authentication-provider-facebook.md), [Twitter](configure-authentication-provider-twitter.md) a [Microsoft](configure-authentication-provider-microsoft.md)). Můžete vytvořit [omezení IP adres](app-service-ip-restrictions.md) a [spravovat identity služby](overview-managed-identity.md).
* **Šablony aplikací** – Vyberte si z rozsáhlého seznamu šablon aplikací na webu [Azure Marketplace](https://azure.microsoft.com/marketplace/), například pro systémy WordPress, Joomla nebo Drupal.
* **Integrace sady Visual Studio** – Vyhrazené nástroje v sadě Visual Studio zjednodušují práci při vytváření, nasazování a ladění.
* **Rozhraní API a mobilní funkce** – App Service poskytuje podporu CORS klíč pro scénáře rozhraní RESTful API a zjednodušuje scénáře mobilních aplikací tím, že ověřování, offline synchronizací dat, nabízená oznámení a další.
* **Kód bez serveru** – Můžete spouštět fragment kódu nebo skript na vyžádání, aniž byste museli explicitně zřizovat nebo spravovat infrastrukturu, a platíte jenom za výpočetní čas, který váš kód skutečně využije (viz [Azure Functions](/azure/azure-functions/)).

Kromě služby App Service nabízí Azure další služby, které lze použít pro hostování webů a webových aplikací. App Service pro většinu scénářů je nejlepší volbou.  Pro architekturu mikroslužeb zvažte [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric). Pokud potřebujete větší kontrolu nad virtuálními počítači, na kterých se kód spouští, zvažte službu [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/). Příklady typických aplikačních scénářů, k nimž můžete použít službu Web Apps, naleznete v tématu [Srovnání Azure App Service, Virtual Machines, Service Fabric a Cloud Services](overview-compare.md).

## <a name="next-steps"></a>Další postup

Vytvořte svou první aplikaci.

> [!div class="nextstepaction"]
> [Jádro ASP.NET](app-service-web-get-started-dotnet.md)

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
> [Python (v Linuxu)](containers/quickstart-python.md)

> [!div class="nextstepaction"]
> [HTML](app-service-web-get-started-html.md)
