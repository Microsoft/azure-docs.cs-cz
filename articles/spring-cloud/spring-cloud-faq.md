---
title: Nejčastější dotazy týkající se Azure jarního cloudu | Microsoft Docs
description: Tento článek obsahuje odpovědi na nejčastější dotazy týkající se Azure jarního cloudu.
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: jeconnoc
ms.openlocfilehash: 6835132192040b4b943b156fb78ae1547522be0c
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229250"
---
# <a name="azure-spring-cloud-faq"></a>Nejčastější dotazy k jarnímu cloudu Azure

Tento článek obsahuje odpovědi na nejčastější dotazy týkající se Azure jarního cloudu. 

## <a name="general"></a>Obecné

### <a name="why-azure-spring-cloud"></a>Proč Azure jaře Cloud?

Jarní cloud Azure poskytuje platformu jako službu (PaaS) pro aplikace pro jarní cloudové vývojáře. Azure jaře Cloud spravuje infrastrukturu vaší aplikace, takže se můžete soustředit na kód aplikace a obchodní logiku. K základním funkcím integrovaným v Azure jaře cloudu patří Eureka, config server, server registru služby, služba Pivoted Build Service, nasazení s modrou zelenou a další. Tato služba také umožňuje vývojářům navazovat aplikace s jinými službami Azure, jako jsou Azure Cosmos DB, Azure Database for MySQL a Azure cache pro Redis.

Služba Azure jaře Cloud vylepšuje prostředí diagnostiky aplikací pro vývojáře a operátory integrací Azure Monitor, Application Insights a Log Analytics.

### <a name="what-service-plans-does-azure-spring-cloud-offer"></a>Jaké plány služeb nabízí Azure jaře Cloud?

Azure jaře Cloud nabízí v období Preview jeden plán služeb.  Nasazení jarního cloudu obsahuje 16 vCPU jader a 32 gigabajtů (GB) paměti.  Horní mez pro každou instanci mikroslužeb v rámci nasazení je 4 vCPU jádra s 8 GB paměti.

Prostředek | Částka
------- | -------
Instance aplikace na jarní aplikaci | 20
Celkový počet instancí aplikace na jednu instanci cloudové služby Azure na jaře | 500
Instance služby Azure jaře Cloud Service na oblast na předplatné | 10
Trvalé svazky | 10 × 50 GBytes

\* _pro zvýšení limitu otevřete [lístek podpory](https://azure.microsoft.com/support/faq/)._

Další informace najdete v tématu [Nejčastější dotazy k podpoře Azure](https://azure.microsoft.com/support/faq/).

### <a name="how-secure-is-azure-spring-cloud"></a>Jak zabezpečená je Azure jarní Cloud?

Zabezpečení a ochrana osobních údajů jsou v rámci nejdůležitějších priorit pro zákazníky Azure a Azure pro jarní Cloud. Azure pomáhá zajistit, aby měli přístup k datům, protokolům a konfiguracím aplikace, a to díky bezpečnému šifrování všech těchto dat. Všechny instance služby ve jarním cloudu Azure jsou od sebe vzájemně izolované.

Azure jaře Cloud poskytuje kompletní protokol SSL a správu certifikátů.

Důležité opravy zabezpečení pro OpenJDK a jarní cloudové moduly runtime se v co nejkratší době aplikují na Azure jaře Cloud.

### <a name="in-which-regions-is-azure-spring-cloud-available"></a>Ve kterých oblastech je k dispozici Azure pružinový Cloud?

Východní USA, Západní USA 2, Západní Evropa a jihovýchodní Asie.

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Jaká jsou známá omezení Azure jarního cloudu?

Při vydání verze Preview má Azure jarní Cloud následující známá omezení:

* `spring.application.name` přepíše název aplikace, který se používá k vytvoření jednotlivých aplikací.
* v konfiguračním souboru z úložiště Git není povolená `server.port`. Přidání do konfiguračního souboru bude nejspíš způsobit, že aplikace nebude dostupná z jiných aplikací nebo z Internetu.
* Šablony Azure Portal a Azure Resource Manager nepodporují nahrávání balíčků aplikací. Balíčky aplikací můžete nahrát jenom nasazením aplikace přes rozhraní příkazového řádku Azure CLI.
* Další informace o omezeních kvóty najdete v tématu [Jaké plány služeb nabízí Azure jaře Cloud](#what-service-plans-does-azure-spring-cloud-offer).

### <a name="how-can-i-provide-feedback-and-report-issues"></a>Jak můžu sdělit svůj názor a ohlásit problémy?

Pokud narazíte na nějaké problémy se službou Azure Pramenitého cloudu, vytvořte [žádost o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Pokud chcete odeslat žádost o funkci nebo poskytnout zpětnou vazbu, přečtěte si [názory na Azure](https://feedback.azure.com/forums/34192--general-feedback).

## <a name="development"></a>Vývoj

### <a name="i-am-a-spring-cloud-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-an-azure-spring-cloud-application"></a>Jsem pružinový vývojář pro Cloud, ale novinka v Azure. Jak mám nejrychlejší způsob, jak se mě naučit vyvíjet cloudovou aplikaci Azure na jaře?

Nejrychlejší způsob, jak začít s jarním cloudem v Azure, najdete podle pokynů v tématu [rychlý Start: spuštění aplikace pro jarní Cloud v Azure pomocí Azure Portal](spring-cloud-quickstart-launch-app-portal.md).

### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Jaký běhový modul Java podporuje Azure jaře Cloud?

Azure jarní Cloud podporuje jazyky Java 8 a 11.

### <a name="where-can-i-view-my-spring-cloud-application-logs-and-metrics"></a>Kde můžu zobrazit protokoly a metriky cloudových aplikací pro jaře?

Metriky najdete na kartě Přehled aplikace a na kartě [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics) .

Azure jaře Cloud podporuje Exportování protokolů a metriky aplikace na jaře cloudu do Azure Storage, EventHub a [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries). Název tabulky v Log Analytics je *AppPlatformLogsforSpring*. Další informace o tom, jak ho povolit, najdete v tématu [diagnostické služby](diagnostic-services.md).

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Podporuje Azure jaře Cloud distribuované trasování?

Ano. Další informace najdete v tématu [kurz: použití distribuovaného trasování u jarního cloudu Azure](spring-cloud-tutorial-distributed-tracing.md).

### <a name="what-resource-types-does-service-binding-support"></a>Jaké typy prostředků podporuje Service Binding?

V současné době jsou podporovány tři služby: Azure Cosmos DB, Azure Database for MySQL a Azure cache pro Redis.

### <a name="can-i-view-add-or-move-persistent-volumes-from-inside-my-applications"></a>Můžu v rámci svých aplikací zobrazit, přidat nebo přesunout trvalé svazky?

Ano.

## <a name="deployment"></a>Nasazení

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Podporuje nasazení s modrou zeleným cloudem Azure?
Ano. Další informace najdete v tématu [Nastavení přípravného prostředí](spring-cloud-howto-staging-environment.md).

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>Můžu získat přístup k Kubernetes, abyste mohli manipulovat s kontejnery aplikací?

Ne.  Azure jaře Cloud vyabstrakce vývojáře z základní architektury, což vám umožní soustředit se na kód aplikace a obchodní logiku.

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Podporuje Azure jaře Cloud vytváření kontejnerů ze zdroje?

Ano. Další informace najdete v tématu [spuštění vaší jarní cloudové aplikace ze zdrojového kódu](spring-cloud-launch-from-source.md).

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Podporuje Azure jaře Cloud automatické škálování v instancích aplikací?

Ne.

### <a name="what-are-the-best-practices-for-migrating-existing-spring-cloud-microservices-to-azure-spring-cloud"></a>Jaké jsou osvědčené postupy pro migraci stávajících mikroslužeb pro jarní Cloud do Azure jaře cloudu?

Při migraci stávajících mikroslužeb pro jarní Cloud do jarního cloudu Azure je vhodné sledovat následující osvědčené postupy:
* Všechny závislosti aplikací je potřeba vyřešit.
* Připravte si položky konfigurace, proměnné prostředí a parametry JVM, abyste je mohli porovnat s nasazením v Azure jaře cloudu.
* Pokud chcete použít vazbu služby, Projděte si služby Azure a ujistěte se, že jste nastavili příslušná přístupová oprávnění.
* Doporučujeme odebrat nebo zakázat všechny vložené služby, které mohou být v konfliktu se službami, které jsou spravovány službou Azure jaře Cloud, jako je naše služba zjišťování služeb, konfigurační server a tak dále.
* Doporučujeme používat oficiální a stabilní Pivotické knihovny. Neoficiální, beta nebo rozvětvené verze kontingenčních knihoven Pivoter nemají podporu smlouvy o úrovni služeb (SLA).

Po migraci monitorujte metriky procesoru/paměti RAM a síťový provoz, abyste měli jistotu, že jsou instance aplikace vhodně škálovatelné.

## <a name="next-steps"></a>Další kroky

Pokud máte další otázky, přečtěte si [příručku k odstraňování potíží se službou Azure pružiny Cloud](spring-cloud-troubleshoot.md).
