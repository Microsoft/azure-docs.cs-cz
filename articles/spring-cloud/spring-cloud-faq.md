---
title: Nejčastější dotazy týkající se Azure Spring Cloud | Dokumenty společnosti Microsoft
description: Tento článek odpovídá na nejčastější dotazy týkající se Azure Spring Cloud.
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: brendm
ms.openlocfilehash: 62623bcadb35f21117ddc2601195e34598c2dff5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298757"
---
# <a name="azure-spring-cloud-faq"></a>Nejčastější dotazy k Azure Spring Cloud

Tento článek odpovídá na nejčastější dotazy týkající se Azure Spring Cloud. 

## <a name="general"></a>Obecné

### <a name="why-azure-spring-cloud"></a>Proč Azure Spring Cloud?

Azure Spring Cloud poskytuje platformu jako službu (PaaS) pro vývojáře Spring Cloud. Azure Spring Cloud spravuje infrastrukturu aplikací, takže se můžete soustředit na kód aplikace a obchodní logiku. Mezi základní funkce integrované do Azure Spring Cloud patří Eureka, Config Server, Service Registry Server, Pivotal Build Service, Blue-green nasazení a další. Tato služba také umožňuje vývojářům svázat své aplikace s jinými službami Azure, jako je Azure Cosmos DB, Azure Database for MySQL a Azure Cache for Redis.

Azure Spring Cloud vylepšuje prostředí diagnostiky aplikací pro vývojáře a operátory integrací Azure Monitor, Application Insights a Log Analytics.

### <a name="what-service-plans-does-azure-spring-cloud-offer"></a>Jaké plány služeb Azure Spring Cloud nabízí?

Azure Spring Cloud nabízí jeden plán služeb během období náhledu.  Nasazení Spring Cloud obsahuje 16 jader virtuálních procesorů a 32 gigabajtů (GB) paměti.  Horní mez pro každou instanci mikroslužeb v rámci nasazení je 4 jádra virtuálního procesoru s 8 GB paměti.

Prostředek | Částka
------- | -------
Instance aplikací na jarní aplikaci | 20
Celkový počet instancí aplikací na instanci služby Azure Spring Cloud | 500
Instance služeb Azure Spring Cloud na oblast na předplatné | 10
Trvalé svazky | 10 x 50 gbajtů

\*_Chcete-li zvýšit limit, otevřete [lístek podpory](https://azure.microsoft.com/support/faq/)._

Další informace najdete v [tématu nejčastější dotazy k podpoře Azure](https://azure.microsoft.com/support/faq/).

### <a name="how-secure-is-azure-spring-cloud"></a>Jak bezpečné je Azure Spring Cloud?

Zabezpečení a ochrana osobních údajů patří mezi hlavní priority pro zákazníky Azure a Azure Spring Cloud. Azure pomáhá zajistit, aby k datům aplikací, protokolům nebo konfiguracím měli přístup jenom zákazníci, a to díky bezpečnému šifrování všech těchto dat. Všechny instance služeb v Azure Spring Cloud jsou izolované od sebe navzájem.

Azure Spring Cloud poskytuje kompletní TLS/SSL a správu certifikátů.

Kritické opravy zabezpečení pro běhy OpenJDK a Spring Cloud se v Azure Spring Cloud uplatní co nejdříve.

### <a name="in-which-regions-is-azure-spring-cloud-available"></a>Ve kterých oblastech je Azure Spring Cloud k dispozici?

Východní USA, Západní USA 2, Západní Evropa a jihovýchodní Asie.

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Jaká jsou známá omezení Azure Spring Cloudu?

Během předběžné verze má Azure Spring Cloud následující známá omezení:

* `spring.application.name`bude přepsán názvem aplikace, který se používá k vytvoření jednotlivých aplikací.
* `server.port`není v konfiguračním souboru z úložiště Git povoleno. Přidání do konfiguračního souboru pravděpodobně způsobí, že vaše aplikace nebude dostupná z jiných aplikací nebo z internetu.
* Šablony portálu Azure a Azure Resource Managernenepodporují odesílání balíčků aplikací. Balíčky aplikací můžete nahrát jenom nasazením aplikace prostřednictvím příkazového příkazového příkazu k webu Azure.
* Další informace o omezeních kvót najdete v tématu [Jaké plány služeb Azure Spring Cloud nabízí?](#what-service-plans-does-azure-spring-cloud-offer).

### <a name="how-can-i-provide-feedback-and-report-issues"></a>Jak mohu poskytnout zpětnou vazbu a nahlásit problémy?

Pokud narazíte na nějaké problémy s Azure Spring Cloud, vytvořte [žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Pokud chcete odeslat žádost o funkci nebo poskytnout zpětnou vazbu, přejděte na [Azure Feedback](https://feedback.azure.com/forums/34192--general-feedback).

## <a name="development"></a>Vývoj

### <a name="i-am-a-spring-cloud-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-an-azure-spring-cloud-application"></a>Jsem vývojář Spring Cloud, ale v Azure jsem nový. Jaký je pro mě nejrychlejší způsob, jak se naučit vyvíjet aplikaci Azure Spring Cloud?

Nejrychlejší způsob, jak začít s Azure Spring Cloud, najdete v pokynech na [úvodním panelu: Spusťte aplikaci Azure Spring Cloud pomocí portálu Azure .](spring-cloud-quickstart-launch-app-portal.md)

### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Jaký java runtime Azure Spring Cloud podporuje?

Azure Spring Cloud podporuje Javu 8 a 11.

### <a name="where-can-i-view-my-spring-cloud-application-logs-and-metrics"></a>Kde si mohu prohlédnout protokoly a metriky aplikací Spring Cloud?

Metriky najdete na kartě Přehled aplikací a na kartě [Azure Monitor.](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics)

Azure Spring Cloud podporuje export protokolů a metrik aplikací Spring Cloud do Azure Storage, EventHubu a [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries). Název tabulky v Log Analytics je *AppPlatformLogsforSpring*. Informace o tom, jak ji povolit, naleznete [v tématu Diagnostické služby](diagnostic-services.md).

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Podporuje Azure Spring Cloud distribuované trasování?

Ano. Další informace najdete [v tématu Kurz: Použití distribuované trasování s Azure Spring Cloud](spring-cloud-tutorial-distributed-tracing.md).

### <a name="what-resource-types-does-service-binding-support"></a>Jaké typy prostředků podporuje vazby služby?

Momentálně jsou podporované tři služby: Azure Cosmos DB, Azure Database for MySQL a Azure Cache for Redis.

### <a name="can-i-view-add-or-move-persistent-volumes-from-inside-my-applications"></a>Je možné zobrazit, přidat nebo přesunout trvalé svazky z aplikací?

Ano.

## <a name="deployment"></a>Nasazení

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Podporuje Azure Spring Cloud modrozelené nasazení?
Ano. Další informace naleznete v [tématu Nastavení pracovního prostředí](spring-cloud-howto-staging-environment.md).

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>Mohu přistupovat k Kubernetes manipulovat s kontejnery aplikace?

Ne.  Azure Spring Cloud abstrahuje vývojáře z podkladové architektury, což vám umožní soustředit se na kód aplikace a obchodní logiku.

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Podporuje Azure Spring Cloud vytváření kontejnerů ze zdroje?

Ano. Další informace najdete [v tématu Spuštění aplikace Spring Cloud ze zdrojového kódu](spring-cloud-launch-from-source.md).

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Podporuje Azure Spring Cloud automatické škálování v instancích aplikací?

Ne.

### <a name="what-are-the-best-practices-for-migrating-existing-spring-cloud-microservices-to-azure-spring-cloud"></a>Jaké jsou doporučené postupy pro migraci stávajících mikroslužeb Spring Cloud do Azure Spring Cloud?

Při migraci stávajících mikroslužeb Spring Cloud do Azure Spring Cloud je vhodné dodržovat následující osvědčené postupy:
* Je třeba vyřešit všechny závislosti aplikací.
* Připravte si položky konfigurace, proměnné prostředí a parametry JVM, abyste je mohli porovnat s nasazením v Azure Spring Cloudu.
* Pokud chcete použít service binding, projděte si služby Azure a ujistěte se, že jste nastavili příslušná přístupová oprávnění.
* Doporučujeme odebrat nebo zakázat všechny vložené služby, které by mohly být v konfliktu se službami, které jsou spravované službou Azure Spring Cloud, jako je naše služba Zjišťování služeb, konfigurační server a tak dále.
* Doporučujeme používat oficiální, stabilní knihovny Pivotal Spring. Neoficiální, beta nebo rozpůlené verze knihoven Pivotal Spring nemají žádnou podporu smlouvy o úrovni služeb (SLA).

Po migraci sledujte metriky procesoru/ram a síťový provoz, abyste zajistili, že instance aplikace budou odpovídajícím způsobem škálovány.

## <a name="next-steps"></a>Další kroky

Pokud máte další otázky, přečtěte si [průvodce řešením potíží s Azure Spring Cloud](spring-cloud-troubleshoot.md).
