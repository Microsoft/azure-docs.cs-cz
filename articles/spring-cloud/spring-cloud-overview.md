---
title: Úvod do Azure Spring Cloud
description: Seznamte se s funkcemi a výhodami Azure Spring Cloud pro nasazení a správu aplikací Java Spring v Azure.
author: bmitchell287
ms.service: spring-cloud
ms.topic: overview
ms.date: 11/4/2019
ms.author: brendm
ms.openlocfilehash: 4426044b3608be0ded378f4f56cbec6bc1948d75
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78273258"
---
# <a name="what-is-azure-spring-cloud"></a>Co je Azure Spring Cloud?

Azure Spring Cloud usnadňuje nasazení aplikací mikroslužeb založených na jarním spuštění do Azure s nulovými změnami kódu.  Azure Spring Cloud spravuje životní cyklus aplikací Spring Cloud, takže se vývojáři můžou soustředit na svůj kód.  Spring Cloud poskytuje správu životního cyklu pomocí komplexního monitorování a diagnostiky, správy konfigurace, zjišťování služeb, integrace CI/CD, modrozelených nasazení a dalších.

Azure Spring Cloud jako součást ekosystému Azure umožňuje snadné vázání na další služby Azure, včetně úložiště, databází, monitorování a dalších.

Azure Spring Cloud se momentálně nabízí jako veřejná verze Preview. Nabídky ve verzi Public Preview umožňují zákazníkům experimentovat s novými funkcemi před jejich oficiálním vydáním.  Funkce a služby veřejné verze Preview nejsou určeny pro produkční použití.  Další informace o podpoře během náhledů najdete v [nejčastějších dotazech](https://azure.microsoft.com/support/faq/) nebo najdete [žádost o podporu,](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) kde se dozvíte další informace.

Začněte tím, že dokončíte rychlý start spring cloudu pomocí [azure cli](spring-cloud-quickstart-launch-app-cli.md), [portálu Azure](spring-cloud-quickstart-launch-app-portal.md)nebo [Maven](spring-cloud-quickstart-launch-app-maven.md).

Další ukázky jsou k dispozici na [GitHubu: Ukázky Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).

## <a name="application-configuration"></a>Konfigurace aplikace

### <a name="spring-cloud-config-server"></a>Konfigurační server spring cloudu

Azure Spring Cloud Config Server poskytuje externalizovanou konfiguraci v distribuovaném systému s podporou serveru i klienta.  Config Server poskytuje centrální umístění pro správu vlastností aplikace ve všech prostředích.  Další informace naleznete v [referenční příručce spring cloudconfig server](https://spring.io/projects/spring-cloud-config.md) a dokončete kurz.

### <a name="enable-bluegreen-deployments"></a>Povolení modrých/zelených nasazení

Azure Spring Cloud podporuje modrozelená nasazení pro uvolnění a aktualizaci kódu do produkčních prostředí.  Využití tohoto vzoru správy změn umožňuje vývojářům implementovat funkce a změny kódu se zabezpečením okamžité záložní v případě potřeby.  Azure umožňuje vývojářům soustředit se na psaní kódu správou více produkčních prostředí a usnadňuje aktualizaci nebo vrácení změn kódu bez přerušení aplikace.  Další informace o pracovních prostředích a modrozelených nasazeních naleznete v tomto [článku s návody](spring-cloud-howto-staging-environment.md).

### <a name="automate-cicd-pipelines"></a>Automatizace kanálů CI/CD

Azure Spring Cloud poskytuje integraci s Azure DevOps pomocí azure cli.  Pomocí Azure DevOps můžete automatizovat integraci kódu a nasazení do vaší jarní aplikace.  Další informace naleznete v tomto [článku](spring-cloud-howto-cicd.md).

### <a name="scale-your-application"></a>Škálování aplikace

Azure Spring Cloud umožňuje snadno škálovat mikroslužby na řídicím panelu Azure Spring Cloud.  Počet virtuálních procesorů a množství paměti dostupné pro vaše mikroslužby lze škálovat nahoru nebo dolů tak, aby vyhovovalvašim požadavkům.  Škálování se projeví během několika sekund a nevyžaduje změny kódu nebo opětovné nasazení.  Chcete-li se dozvědět více, dokončete tento [kurz](spring-cloud-tutorial-scale-manual.md).

## <a name="application-monitoring"></a>Monitorování aplikace

### <a name="monitor-your-application-using-distributed-tracing-and-azure-app-insights"></a>Sledování aplikace pomocí distribuovaného trasování a Azure App Insights

Distribuované nástroje pro trasování Spring Cloud umožňují vývojářům ladit a monitorovat komplexní propojení mezi mikroslužbami v aplikaci.  Díky integraci [spring cloudového detektiva](https://spring.io/projects/spring-cloud-sleuth) s [Azure Application Insights](../azure-monitor/insights/insights-overview.md)poskytuje Azure výkonné možnosti distribuovaného trasování přímo z portálu Azure.  Chcete-li se dozvědět více, dokončete tento [kurz](spring-cloud-tutorial-distributed-tracing.md).

## <a name="next-steps"></a>Další kroky

- [Spuštění aplikace Spring Cloud z cli](spring-cloud-quickstart-launch-app-cli.md)
