---
title: Úvod do jarního cloudu Azure
description: Seznamte se s funkcemi a výhodami Azure jarního cloudu pro nasazení a správu aplikací v jazyce Java pružiny v Azure.
author: bmitchell287
ms.service: spring-cloud
ms.topic: overview
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: b03bedd9abbe59ed3bc1b0ec1439aa27af57e6da
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91758893"
---
# <a name="what-is-azure-spring-cloud"></a>Co je Azure Spring Cloud?

Jarní cloud Azure usnadňuje nasazení mikroslužeb založených na jarních aplikacích do Azure s nulovými změnami kódu.  Jarní cloud Azure spravuje infrastrukturu jarních cloudových aplikací, takže se vývojáři můžou soustředit na svůj kód.  Jarní Cloud poskytuje správu životního cyklu pomocí komplexního monitorování a diagnostiky, správy konfigurací, zjišťování služeb, integrace CI/CD, modrého zeleného nasazení a dalších.

Jarní cloud Azure podporuje aplikace v jazyce Java pro [spouštění](https://spring.io/projects/spring-boot) a ASP.NET Core [Steeltoe](https://steeltoe.io/) . Podpora Steeltoe se v tuto chvíli nabízí jako verze Public Preview. Nabídky verze Public Preview vám umožní experimentovat s novými funkcemi před jejich oficiální verzí.  Funkce a služby verze Public Preview nejsou určeny pro produkční použití.  Další informace najdete v tématu [Nejčastější dotazy](https://azure.microsoft.com/support/faq/) nebo soubor a [support Request](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

V rámci ekosystému Azure umožňuje Azure jaře Cloud snadnou vazbu na další služby Azure, včetně úložiště, databází, monitorování a dalších.

Tento Úvod popisuje následující možnosti Azure jarního cloudu:

* Konfigurační server
* Modrá/zelená nasazení
* Škálování aplikace
* Integrace s Azure DevOps
* Monitorování aplikace

## <a name="spring-cloud-config-server"></a>Jarní cloudový konfigurační server

Azure jarní cloudový konfigurační server poskytuje v distribuovaném systému externou konfiguraci s podporou serveru i na straně klienta.  Azure jarní cloudový konfigurační server je centrální umístění pro správu vlastností aplikace napříč všemi prostředími. Další informace najdete v tématu [odkazování na server pro konfiguraci jarního cloudu](https://spring.io/projects/spring-cloud-config). 

## <a name="bluegreen-deployments"></a>Modrá/zelená nasazení

Azure pružinový Cloud podporuje nasazení v různých a zelených prostředích, a to za účelem vydávání a aktualizace kódu v produkčním prostředí.  Tento model správy změn umožňuje vývojářům implementovat v případě potřeby funkce a změny kódu s jistotou okamžitého použití.  Vývojáři se můžou soustředit na psaní kódu s více provozními prostředími, aby aktualizovali nebo vrátili změny kódu beze přerušení aplikace.  Další informace o přípravných prostředích a o Blue-zelených nasazeních najdete v tomto [článku s postupem](spring-cloud-howto-staging-environment.md).

## <a name="cicd-pipeline-automation"></a>Automatizace kanálů CI/CD

Azure jaře Cloud poskytuje integraci se službou Azure DevOps pomocí Azure CLI.  Pomocí Azure DevOps můžete automatizovat integraci kódu a nasazení do vaší aplikace pružiny.  Další informace najdete v tomto [článku](spring-cloud-howto-cicd.md).

## <a name="application-scaling"></a>Škálování aplikace

Jarní cloud Azure vám umožňuje snadno škálovat mikroslužby na řídicím panelu cloudu Azure na jaře.  Počet vCPU a velikost paměti dostupné pro vaše mikroslužby je možné škálovat nahoru nebo dolů, aby vyhovovaly vašim požadavkům.  Změna měřítka se projeví během několika sekund a nevyžaduje změny kódu nebo opětovné nasazení.  Pokud se chcete dozvědět víc, dokončete tento [kurz](spring-cloud-tutorial-scale-manual.md).

## <a name="application-monitoring"></a>Monitorování aplikace

Nástroje distribuované distribuce jarního cloudu umožňují vývojářům ladit a monitorovat složitá propojení mezi mikroslužbami v aplikaci.  Díky integraci [jarních cloudových Sleuth](https://spring.io/projects/spring-cloud-sleuth) s [Application Insights](../azure-monitor/insights/insights-overview.md)Azure poskytuje Azure výkonnou schopnost distribuované vektorizace přímo z Azure Portal.  Pokud se chcete dozvědět víc, dokončete tento [kurz](spring-cloud-tutorial-distributed-tracing.md).

## <a name="next-steps"></a>Další kroky

Začněte tím, že dokončíte [rychlý Start jarního cloudu](spring-cloud-quickstart.md) .

Ukázky jsou k dispozici na GitHubu: [ukázky Azure pro jarní Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/).
