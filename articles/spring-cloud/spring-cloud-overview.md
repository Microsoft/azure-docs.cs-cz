---
title: Úvod do jarního cloudu Azure
description: Seznamte se s funkcemi a výhodami Azure jarního cloudu pro nasazení a správu aplikací v jazyce Java pružiny v Azure.
author: jpconnock
ms.service: spring-cloud
ms.topic: overview
ms.date: 11/4/2019
ms.author: jeconnoc
ms.openlocfilehash: 5b5b5d9eba0e643357054cfd1b0c514a159282a1
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73646802"
---
# <a name="what-is-azure-spring-cloud"></a>Co je Azure Spring Cloud?

Jarní cloud Azure usnadňuje nasazení mikroslužeb založených na jarních aplikacích do Azure s nulovými změnami kódu.  Jarní cloud Azure umožňuje vývojářům soustředit se na svůj kód tím, že spravuje životní cyklus jarních cloudových aplikací.  Jarní Cloud poskytuje správu životního cyklu pomocí komplexního monitorování a diagnostiky, správy konfigurací, zjišťování služeb, integrace CI/CD, modrého zeleného nasazení a dalších.

V rámci ekosystému Azure umožňuje Azure jaře Cloud snadnou vazbu na další služby Azure, včetně úložiště, databází, monitorování a dalších.

Jarní cloud Azure se teď nabízí jako verze Public Preview. Nabídky veřejné verze Preview umožňují zákazníkům experimentovat s novými funkcemi před jejich oficiální verzí.  Funkce a služby verze Public Preview nejsou určeny pro produkční použití.  Další informace o podpoře v rámci verzí Preview najdete v souboru [support Request](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request).

Začněte tím, že dokončíte rychlý Start jarního cloudu pomocí rozhraní příkazového [řádku Azure CLI](spring-cloud-quickstart-launch-app-cli.md), [Azure Portal](spring-cloud-quickstart-launch-app-portal.md)nebo [Maven](spring-cloud-quickstart-launch-app-maven.md).

## <a name="application-configuration"></a>Konfigurace aplikace

### <a name="spring-cloud-config-server"></a>Jarní cloudový konfigurační server

Azure jarní cloudový konfigurační server poskytuje v distribuovaném systému externou konfiguraci s podporou serveru i na straně klienta.  Konfigurační server poskytuje centrální umístění pro správu vlastností aplikace napříč všemi prostředími.  Další informace najdete v referenčních informacích k [serveru jarní Cloud config server](https://spring.io/projects/spring-cloud-config.md) a dokončení kurzu.

### <a name="enable-bluegreen-deployments"></a>Povolit modrá/zelená nasazení

Azure pružinový Cloud podporuje nasazení v různých a zelených prostředích, a to za účelem vydávání a aktualizace kódu v produkčním prostředí.  Využití tohoto modelu správy změn umožňuje vývojářům implementovat v případě potřeby funkce a změny kódu s jistotou okamžitého použití.  Azure umožňuje vývojářům soustředit se na psaní kódu tím, že spravuje více produkčních prostředí a usnadňuje aktualizaci nebo vrácení změn kódu bez přerušení aplikace.  Další informace o přípravných prostředích a o Blue-zelených nasazeních najdete v tomto [článku s postupem](spring-cloud-howto-staging-environment.md).

### <a name="automate-cicd-pipelines"></a>Automatizace kanálů CI/CD

Azure jaře Cloud poskytuje integraci se službou Azure DevOps pomocí Azure CLI.  Pomocí Azure DevOps můžete automatizovat integraci kódu a nasazení do vaší aplikace pružiny.  Další informace najdete v tomto [článku](spring-cloud-howto-cicd.md).

### <a name="scale-your-application"></a>Škálování aplikace

Jarní cloud Azure vám umožňuje snadno škálovat mikroslužby na řídicím panelu cloudu Azure na jaře.  Počet vCPU a velikost paměti dostupné pro vaše mikroslužby je možné škálovat nahoru nebo dolů, aby vyhovovaly vašim požadavkům.  Změna měřítka se projeví během několika sekund a nevyžaduje změny kódu nebo opětovné nasazení.  Pokud se chcete dozvědět víc, dokončete tento [kurz](spring-cloud-tutorial-scale-manual.md).

## <a name="application-monitoring"></a>Monitorování aplikací

### <a name="monitor-your-application-using-distributed-tracing-and-azure-app-insights"></a>Monitorování aplikace pomocí distribuovaného trasování a Azure App Insights

Nástroje distribuované distribuce jarního cloudu umožňují vývojářům ladit a monitorovat složitá propojení mezi mikroslužbami v jejich aplikaci.  Díky integraci [jarních cloudových Sleuth](https://spring.io/projects/spring-cloud-sleuth) s [Application Insights](../azure-monitor/insights/insights-overview.md)Azure poskytuje Azure výkonnou schopnost distribuované vektorizace přímo z Azure Portal.  Pokud se chcete dozvědět víc, dokončete tento [kurz](spring-cloud-tutorial-distributed-tracing.md).

## <a name="next-steps"></a>Další kroky

- [Spuštění vaší jarní cloudové aplikace z CLI](spring-cloud-quickstart-launch-app-cli.md)
