---
title: Úvod do jarního cloudu Azure
description: Seznamte se s funkcemi a výhodami Azure jarního cloudu pro nasazení a správu aplikací v jazyce Java pružiny v Azure.
author: bmitchell287
ms.service: spring-cloud
ms.topic: overview
ms.date: 12/02/2020
ms.author: brendm
ms.custom: devx-track-java, contperf-fy21q2
customer intent: As an Azure Cloud user, I want to deploy, run, and monitor Spring Boot microservices.
ms.openlocfilehash: a92e535cb1edeb0eeaa285e442b4b24766e20ead
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104879255"
---
# <a name="what-is-azure-spring-cloud"></a>Co je Azure Spring Cloud?

Jarní cloud Azure usnadňuje nasazení aplikací mikroslužeb s pružinou v systému Azure bez jakýchkoli změn kódu.  Služba spravuje infrastrukturu jarních cloudových aplikací, aby se vývojáři mohli soustředit na svůj kód.  Služba jarní Cloud v Azure poskytuje správu životního cyklu pomocí komplexního monitorování a diagnostiky, správy konfigurací, zjišťování služeb, integrace CI/CD, modrého zeleného nasazení a dalších.

## <a name="why-use-azure-spring-cloud"></a>Proč používat jarní cloud Azure?

Nasazení aplikací do Azure Pramenitého cloudu má spoustu výhod.  Další možnosti:
* Efektivně Migrujte stávající jarní aplikace a spravujte škálování a náklady v cloudu.
* Modernizovat aplikace pomocí jarních vzorů cloudu, abyste vylepšili flexibilitu a rychlost doručování.
* Spouštějte Java v cloudovém měřítku a vyšší využití bez komplikované infrastruktury.
* Rychle vyvíjet a nasazovat bez závislostí kontejneru
* Sledujte provozní úlohy efektivně a snadno.

Jarní cloud Azure podporuje aplikace v jazyce Java pro [spouštění](https://spring.io/projects/spring-boot) a ASP.NET Core [Steeltoe](https://steeltoe.io/) . Podpora Steeltoe se v tuto chvíli nabízí jako verze Public Preview. Nabídky verze Public Preview vám umožní experimentovat s novými funkcemi před jejich oficiální verzí. Funkce a služby verze Public Preview nejsou určeny pro produkční použití. Další informace najdete v tématu [Nejčastější dotazy](https://azure.microsoft.com/support/faq/) nebo soubor a [support Request](../azure-portal/supportability/how-to-create-azure-support-request.md).

## <a name="service-overview"></a>Přehled služby

V rámci ekosystému Azure umožňuje Azure jaře Cloud snadnou vazbu na další služby Azure, včetně úložiště, databází, monitorování a dalších.  

  ![Přehled služby jarní Cloud v Azure](media/spring-cloud-principles/azure-spring-cloud-overview.png)

* Jarní cloud Azure je plně spravovaná služba pro aplikace pro jaře Boot, která vám umožní zaměřit se na vytváření a spouštění aplikací bez starostí se správou infrastruktury.

* Jednoduše nasaďte jar nebo kód a v Azure jaře Cloud budou vaše aplikace automaticky nasazovat s využitím pružinové služby a integrovaného životního cyklu aplikace.

* Monitorování je jednoduché. Po nasazení můžete monitorovat výkon aplikace, opravovat chyby a rychle vylepšit aplikace. 

* Úplná integrace ekosystémů a služeb Azure.

* Azure pružinový Cloud je připravený pro plně spravovanou infrastrukturu, vestavěnou správu životního cyklu a snadné monitorování.

## <a name="documentation-overview"></a>Přehled dokumentace
Tato dokumentace obsahuje oddíly, které vysvětlují, jak začít a využívat jarní cloudové služby Azure.

* Začínáme
    * [Spuštění první aplikace](spring-cloud-quickstart.md)
    * [Zřízení služby jarního cloudu Azure](spring-cloud-quickstart-provision-service-instance.md)
    * [Nastavení konfiguračního serveru]()
    * [Sestavování a nasazování aplikací](spring-cloud-quickstart-deploy-apps.md)
    * [Použití metrik a trasování protokolů](spring-cloud-quickstart-logs-metrics-tracing.md)
* Postupy
    * [Vývoj](how-to-prepare-app-deployment.md): Příprava existující aplikace pružiny v jazyce Java pro nasazení do jarního cloudu Azure. Pokud jsou správně nakonfigurovány, poskytuje Azure jarní Cloud robustní služby pro monitorování, škálování a aktualizace aplikací Java pro jarní Cloud.
    * [Nasazení](spring-cloud-howto-staging-environment.md): jak nastavit pracovní nasazení pomocí modelu nasazení Blue-zelený ve jarním cloudu Azure. Modré/zelené nasazení je vzor průběžného doručování Azure DevOps, který spoléhá na zachování živé stávající (modré) verze, zatímco probíhá nasazení nové (zelené) verze.
    * [Konfigurace aplikací](spring-cloud-howto-start-stop-delete.md): spouštění, zastavování a odstraňování aplikací Azure pro jarní Cloud Pomocí Azure Portal nebo rozhraní příkazového řádku Azure můžete změnit stav aplikace v jaře cloudu Azure.
    * [Škálování](spring-cloud-howto-scale-manual.md): škálování jakékoli aplikace mikroslužeb pomocí řídicího panelu Azure jarního cloudu v Azure Portal nebo pomocí nastavení automatického škálování. Veřejné IP adresy jsou k dispozici pro komunikaci s externími prostředky, jako jsou databáze, úložiště a trezory klíčů.
    * [Monitorujte aplikace](spring-cloud-howto-distributed-tracing.md): nástroje pro distribuované trasování, abyste mohli snadno ladit a monitorovat složité problémy. Jarní cloud Azure integruje jarní Cloud Sleuth s využitím Azure Application Insights. Tato integrace poskytuje výkonnou schopnost distribuované vektorizace z Azure Portal.
    * [Zabezpečené aplikace](spring-cloud-howto-enable-system-assigned-managed-identity.md): prostředky Azure poskytují automaticky spravovanou identitu v Azure Active Directory. Tuto identitu můžete použít k ověření pro libovolnou službu, která podporuje ověřování Azure AD, a to bez nutnosti přihlašovacích údajů ve vašem kódu.
    * [Integrace s jinými službami Azure](spring-cloud-howto-bind-cosmos.md): místo ruční konfigurace aplikací pro spouštění pružiny můžete automaticky svázat vybrané služby Azure s vašimi aplikacemi, například vytvořit vazbu aplikace k databázi Azure Cosmos DB.
    * [Automatizace](spring-cloud-howto-cicd.md): nástroje pro průběžnou integraci a průběžné doručování vám umožní rychle nasadit aktualizace stávajících aplikací s minimálním úsilím a rizikem. Azure DevOps pomáhá organizovat a řídit tyto klíčové úlohy. 
    * [Řešení potíží](spring-cloud-howto-self-diagnose-solve.md): Diagnostika Azure jaře cloudu poskytuje interaktivní možnosti, které vám pomůžou při řešení potíží s aplikacemi. Není nutná žádná konfigurace. Když najdete problémy, diagnostika cloudu Azure vyhledá problémy a provede vás s informacemi, které vám pomohou vyřešit problémy.
    * [Migrace](/azure/developer/java/migration/migrate-spring-boot-to-azure-spring-cloud): jak migrovat existující jarní cloudová aplikace nebo aplikace pro spouštění pružiny do provozu v Azure jaře cloudu.

 Informace o tom, jak začít, najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Rychlý Start jarního cloudu](spring-cloud-quickstart.md)

Ukázky jsou k dispozici na GitHubu: [ukázky Azure pro jarní Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/).