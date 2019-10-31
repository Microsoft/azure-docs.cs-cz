---
title: Nejčastější dotazy k Azure jaře cloudu | Microsoft Docs
description: Přečtěte si nejčastější dotazy k Azure jaře cloudu.
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: v-vasuke
ms.openlocfilehash: 12ef5586ac1e7ecf27824f95add3355451cf555a
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73163766"
---
# <a name="frequently-asked-questions"></a>Nejčastější dotazy

Tento článek popisuje časté otázky týkající se Azure jarního cloudu. 

## <a name="general"></a>Obecné

### <a name="why-azure-spring-cloud"></a>Proč Azure jaře Cloud?

Jarní cloud Azure poskytuje platformu jako službu (PaaS) pro jarní vývojáře. Azure jaře Cloud spravuje infrastrukturu vaší aplikace, takže se můžete soustředit na kód aplikace a obchodní logiku. K základním funkcím integrovaným v Azure jaře cloudu patří Eureka, config server, server registru služby, Pivotovaná sestavovací služba, nasazení Blue-zelená a další. Tato služba také umožňuje vývojářům navazovat své aplikace se službami Azure, jako je CosmosDB, MySQL a Azure cache pro Redis.

Služba Azure jaře Cloud vylepšuje prostředí diagnostiky aplikací pro vývojáře a operátory integrací Azure Monitor, Application Insights a Log Analytics.

### <a name="what-service-plans-does-azure-spring-cloud-offer"></a>Jaké plány služeb nabízí Azure jaře Cloud?

Azure jaře Cloud nabízí v období Preview jeden plán služeb.  Nasazení jarního cloudu obsahuje 16 vCPU jader a 32 GB paměti.  Horní mez pro každou instanci mikroslužeb v rámci nasazení je 4 vCPU jádra s 8 GB paměti.

Prostředek | Částka
------- | -------
Instance aplikace na jarní aplikaci | 20
Celkový počet instancí aplikace na jednu instanci cloudové služby Azure na jaře | 50 *
Instance služby Azure jaře Cloud Service na oblast na předplatné | odst
Trvalé svazky | 10 × 50 GBytes

*_otevření [lístku podpory](https://azure.microsoft.com/support/faq/) pro zvýšení limitu._

Další podrobnosti najdete v nejčastějších dotazech k [podpoře Azure](https://azure.microsoft.com/support/faq/).

### <a name="how-secure-is-azure-spring-cloud"></a>Jak zabezpečená je Azure jarní Cloud?

Zabezpečení a ochrana osobních údajů představují jednu z nejdůležitějších priorit pro zákazníky Azure a Azure pro jarní Cloud. Azure zajišťuje, aby přístup k datům, protokolům a konfiguracím aplikací pomocí bezpečného šifrování všech těchto dat měl jenom zákazník. Všechny instance služby ve jarním cloudu Azure jsou od sebe vzájemně izolované.

Azure jaře Cloud poskytuje kompletní protokol SSL a správu certifikátů.

Důležité opravy zabezpečení pro OpenJDK a jarní cloudové moduly runtime se v co nejkratší době aplikují na Azure jaře Cloud.

### <a name="which-regions-azure-spring-cloud-are-available"></a>Které oblasti je k dispozici pro Azure jaře Cloud?

Východní USA, Západní USA 2, Západní Evropa a jihovýchodní Asie.

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Jaká jsou známá omezení Azure jarního cloudu

Tady jsou známá omezení Azure jaře cloudu, když je služba ve verzi Preview.

* `spring.application.name` přepíše název aplikace, který se používá k vytvoření jednotlivých aplikací.
* `server.port` se v konfiguračním souboru z úložiště Git nepovoluje. Přidání do konfiguračního souboru bude nejspíš způsobit, že aplikace nebude dostupná z jiných aplikací nebo z Internetu.
* Šablony Azure Portal a Správce prostředků nepodporují nahrávání balíčků aplikací. Dá se to udělat jenom prostřednictvím nasazení aplikace pomocí Azure CLI.
* Omezení kvóty najdete v tématu [Jaké plány služeb nabízí Azure jaře Cloud](#what-service-plans-does-azure-spring-cloud-offer).

### <a name="how-can-i-provide-feedback-and-report-issues"></a>Jak můžu sdělit svůj názor a ohlásit problémy?

Pokud narazíte na nějaké problémy se službou Azure Pramenitého cloudu, vytvořte prosím [žádost o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). U žádostí o funkce si prosím přečtěte [názory na Azure](https://feedback.azure.com/forums/34192--general-feedback) , které vám pomůžou požádat o funkce nebo o poskytnutí zpětné vazby.

## <a name="development"></a>Vývoj

### <a name="i-am-a-spring-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-aazure-spring-cloud-application"></a>Jsem vývojář pružiny, ale novinkou v Azure, jaký je nejrychlejší způsob, jak se chci naučit vyvíjet Rezervovanéou cloudovou aplikaci pro jaře?

Nejrychlejší způsob, jak začít s jarním cloudem v Azure, je postupovat podle [tohoto](spring-cloud-quickstart-launch-app-portal.md)rychlého startu.

### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Jaký běhový modul Java podporuje Azure jaře Cloud?

Azure jarní Cloud podporuje jazyky Java 8 a 11.

### <a name="where-can-i-see-my-spring-application-logs-and-metrics"></a>Kde můžu zobrazit protokoly a metriky aplikace pružiny?

Metriky najdete na kartě Přehled aplikace a na kartě [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics) .

Azure jaře Cloud podporuje export protokolů a metriky aplikace na jaře do Azure Storage, EventHub a [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries). Název tabulky ve Log Analytics je `AppPlatformLogsforSpring`. Pokud ho chcete povolit, přečtěte si tento článek o našich [diagnostických službách](diagnostic-services.md).

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Podporuje Azure jaře Cloud distribuované trasování?

Ano, další informace najdete v [distribuovaném trasování](spring-cloud-tutorial-distributed-tracing.md) .

### <a name="what-resource-types-does-service-binding-support"></a>Jaké typy prostředků podporuje Service Binding?

V současné době jsou podporovány tři služby: Azure Cosmos DB, Azure Database for MySQL a Azure cache pro Redis.

### <a name="can-i-viewaddmove-persistent-volumes-from-inside-my-applications"></a>Můžu v rámci svých aplikací Zobrazit/Přidat nebo přesunout trvalé svazky?
Ano.

## <a name="deployment"></a>Nasazení

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Podporuje nasazení s modrou zeleným cloudem Azure?
Ano, další informace najdete v [příručce k přípravnému prostředí](spring-cloud-howto-staging-environment.md) .

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>Můžu získat přístup k Kubernetes, abyste mohli manipulovat s kontejnery aplikací?

Ne.  Azure jaře Cloud vyabstrakce vývojáře z základní architektury, což vám umožní soustředit se na kód aplikace a obchodní logiku.

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Podporuje Azure jaře Cloud vytváření kontejnerů ze zdroje?

Ano, další podrobnosti najdete [v nasazení ze zdroje](spring-cloud-launch-from-source.md) .

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Podporuje Azure jaře Cloud automatické škálování v instancích aplikací?

Ne.

### <a name="what-are-the-best-practices-for-migrating-existing-spring-microservices-to-azure-spring-cloud"></a>Jaké jsou osvědčené postupy pro migraci stávajících mikroslužeb na jaře do Azure jarního cloudu?

Před migrací stávajících mikroslužeb na jaře do Azure jarního cloudu
* Všechny závislosti aplikací je potřeba vyřešit.
* Připravte si položky konfigurace, proměnné prostředí a parametry JVM, abyste je mohli porovnat s nasazením v Azure jaře cloudu.
* Pokud chcete použít vazbu služby, Projděte si služby Azure a ujistěte se, že jste nastavili příslušná přístupová oprávnění.
* Doporučujeme odebrat nebo zakázat vložené služby, které mohou být v konfliktu se službami, které spravuje Azure jaře Cloud, jako je naše služba zjišťování služeb, konfigurační server atd.
*-* Doporučujeme používat oficiální a stabilní Pivotické knihovny. Neoficiální, beta nebo rozvětvené verze kontingenčních knihoven Pivoter nepodporují smlouvu SLA.

Po migraci monitorujte metriky CPU/RAM a síťový provoz, abyste zajistili správné škálování instancí aplikace.

## <a name="next-steps"></a>Další kroky

[Pokud máte další otázky, přečtěte si příručku Poradce při potížích](spring-cloud-troubleshoot.md).