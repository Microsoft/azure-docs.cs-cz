---
title: Nejčastější dotazy týkající se Azure jarního cloudu | Microsoft Docs
description: Tento článek obsahuje odpovědi na nejčastější dotazy týkající se Azure jarního cloudu.
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: f034cd07b481f9d72cb3f753b30e1779bf672ac2
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491932"
---
# <a name="azure-spring-cloud-faq"></a>Nejčastější dotazy k jarnímu cloudu Azure

Tento článek obsahuje odpovědi na nejčastější dotazy týkající se Azure jarního cloudu.

## <a name="general"></a>Obecné

### <a name="why-azure-spring-cloud"></a>Proč Azure jaře Cloud?

Jarní cloud Azure poskytuje platformu jako službu (PaaS) pro aplikace pro jarní cloudové vývojáře. Azure jaře Cloud spravuje infrastrukturu vaší aplikace, takže se můžete soustředit na kód aplikace a obchodní logiku. K základním funkcím integrovaným v Azure jaře cloudu patří Eureka, config server, server registru služby, služba Pivoted Build Service, nasazení s modrou zelenou a další. Tato služba také umožňuje vývojářům navazovat aplikace s jinými službami Azure, jako jsou Azure Cosmos DB, Azure Database for MySQL a Azure cache pro Redis.

Služba Azure jaře Cloud vylepšuje prostředí diagnostiky aplikací pro vývojáře a operátory integrací Azure Monitor, Application Insights a Log Analytics.

### <a name="how-secure-is-azure-spring-cloud"></a>Jak zabezpečená je Azure jarní Cloud?

Zabezpečení a ochrana osobních údajů jsou v rámci nejdůležitějších priorit pro zákazníky Azure a Azure pro jarní Cloud. Azure pomáhá zajistit, aby měli přístup k datům, protokolům a konfiguracím aplikace, a to díky bezpečnému šifrování všech těchto dat. 

* Instance služby ve jarním cloudu Azure jsou od sebe vzájemně izolované.
* Jarní cloud Azure poskytuje kompletní protokol TLS/SSL a správu certifikátů.
* Důležité opravy zabezpečení pro OpenJDK a jarní cloudové moduly runtime se v co nejkratší době aplikují na Azure jaře Cloud.

### <a name="in-which-regions-is-azure-spring-cloud-available"></a>Ve kterých oblastech je k dispozici Azure pružinový Cloud?

Východní USA, Východní USA 2, Střed USA, Střed USA – jih, Západní USA 2, Západní Evropa, Severní Evropa, Velká Británie – jih, jihovýchodní Asie a Austrálie – východ.

### <a name="is-any-customer-data-stored-outside-of-the-specified-region"></a>Jsou všechna zákaznická data uložená mimo určenou oblast?

Azure jarní Cloud je regionální služba. Veškerá zákaznická data v Azure jaře cloudu se ukládají do několika oblastí v rámci stejné geografické oblasti zadané oblasti pro zajištění redundance. Další informace o geografických a geografických oblastech najdete v tématu [zasídlí dat v Azure](https://azure.microsoft.com/global-infrastructure/data-residency/).

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Jaká jsou známá omezení Azure jarního cloudu?

U jarního cloudu Azure máte Tato známá omezení:
    
* `spring.application.name` bude přepsáno názvem aplikace, který se používá k vytvoření jednotlivých aplikací.
* `server.port` Výchozí hodnota je port 1025. Pokud se použije jiná hodnota, přepíše se. Respektujte prosím toto nastavení a v kódu nespecifikujte port serveru.
* Šablony Azure Portal a Azure Resource Manager nepodporují nahrávání balíčků aplikací. Balíčky aplikací můžete nahrát jenom nasazením aplikace přes rozhraní příkazového řádku Azure CLI.

### <a name="what-pricing-tiers-are-available"></a>Jaké cenové úrovně jsou k dispozici? 
Který z nich mám použít a jaká jsou omezení v rámci jednotlivých vrstev?
* Azure jaře Cloud nabízí dvě cenové úrovně: Basic a Standard. Úroveň Basic je zaměřená na vývoj a testování a vyzkoušení služby Azure Pramenitého cloudu. Úroveň Standard je optimalizovaná tak, aby spouštěla provozní provoz pro obecné účely. Omezení a porovnání na úrovni funkcí najdete v tématu [Podrobnosti o cenách jarního cloudu Azure](https://azure.microsoft.com/pricing/details/spring-cloud/) .

### <a name="how-can-i-provide-feedback-and-report-issues"></a>Jak můžu sdělit svůj názor a ohlásit problémy?

Pokud narazíte na nějaké problémy se službou Azure Pramenitého cloudu, vytvořte [žádost o podporu Azure](../azure-portal/supportability/how-to-create-azure-support-request.md). Pokud chcete odeslat žádost o funkci nebo poskytnout zpětnou vazbu, přečtěte si [názory na Azure](https://feedback.azure.com/forums/34192--general-feedback).

## <a name="development"></a>Vývoj

### <a name="i-am-a-spring-cloud-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-an-azure-spring-cloud-application"></a>Jsem pružinový vývojář pro Cloud, ale novinka v Azure. Jak mám nejrychlejší způsob, jak se mě naučit vyvíjet cloudovou aplikaci Azure na jaře?

Nejrychlejší způsob, jak začít s jarním cloudem v Azure, najdete podle pokynů v tématu [rychlý Start: spuštění aplikace pro jarní Cloud v Azure pomocí Azure Portal](spring-cloud-quickstart.md).

::: zone pivot="programming-language-java"
### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Jaký běhový modul Java podporuje Azure jaře Cloud?

Azure jarní Cloud podporuje jazyky Java 8 a 11. Viz [Java Runtime a verze operačních systémů](#java-runtime-and-os-versions)
::: zone-end

### <a name="where-can-i-view-my-spring-cloud-application-logs-and-metrics"></a>Kde můžu zobrazit protokoly a metriky cloudových aplikací pro jaře?

Metriky najdete na kartě Přehled aplikace a na kartě [Azure monitor](../azure-monitor/platform/data-platform-metrics.md#metrics-explorer) .

Azure jaře Cloud podporuje Exportování protokolů a metriky aplikace na jaře cloudu do Azure Storage, EventHub a [Log Analytics](../azure-monitor/platform/data-platform-logs.md). Název tabulky v Log Analytics je *AppPlatformLogsforSpring*. Další informace o tom, jak ho povolit, najdete v tématu [diagnostické služby](diagnostic-services.md).

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Podporuje Azure jaře Cloud distribuované trasování?

Yes. Další informace najdete v tématu [kurz: použití distribuovaného trasování u jarního cloudu Azure](spring-cloud-tutorial-distributed-tracing.md).

::: zone pivot="programming-language-java"
### <a name="what-resource-types-does-service-binding-support"></a>Jaké typy prostředků podporuje Service Binding?

V současné době jsou podporovány tři služby:
* Azure Cosmos DB
* Azure Database for MySQL
* Mezipaměť Azure pro Redis.
::: zone-end

### <a name="can-i-view-add-or-move-persistent-volumes-from-inside-my-applications"></a>Můžu v rámci svých aplikací zobrazit, přidat nebo přesunout trvalé svazky?

Yes.

### <a name="when-i-deletemove-an-azure-spring-cloud-service-instance-will-its-extension-resources-be-deletedmoved-as-well"></a>Po odstranění nebo přesunutí instance služby Azure jaře Cloud Service se její prostředky pro rozšíření odstranily/přesunuly i?

Závisí na logice poskytovatelů prostředků, které vlastní prostředky rozšíření. Prostředky rozšíření `Microsoft.AppPlatform` instance nepatří do stejného oboru názvů, takže se chování liší podle poskytovatele prostředků. Například operace delete/Move nebude přenesena do prostředků **nastavení diagnostiky** . Pokud se vytvoří nová instance cloudové cloudové služby Azure se stejným ID prostředku jako Odstraněná, nebo pokud se předchozí instance cloudového cloudu Azure přesune zpátky, předchozí zdroje **nastavení diagnostiky** ji dál rozšiřují.

Nastavení diagnostiky jarního cloudu můžete odstranit pomocí Azure CLI:

```azurecli
 az monitor diagnostic-settings delete --name $diagnosticSettingName --resource $azureSpringCloudResourceId
```

::: zone pivot="programming-language-java"
## <a name="java-runtime-and-os-versions"></a>Běhové prostředí Java a verze operačních systémů

### <a name="which-versions-of-java-runtime-are-supported-in-azure-spring-cloud"></a>Které verze Java Runtime jsou podporovány v Azure jaře cloudu?

Azure jarní Cloud podporuje verze Java LTS s nejnovějšími sestaveními, které jsou v současnosti 2020, Java 8 a Java 11 podporovány. Přečtěte si téma [instalace JDK pro Azure a Azure Stack](/azure/developer/java/fundamentals/java-jdk-install)

### <a name="who-built-these-java-runtimes"></a>Kdo tyto moduly Runtime Java vytvořil?

Systémy Azul. Sestavení sady Azul Zulu JDK for Azure – Enterprise Edition je bezplatná multiplatformní distribuce sady OpenJDK pro produkční prostředí pro Azure a Azure Stack s podporou Microsoft a Azul Systems. Obsahuje všechny komponenty pro vytváření a spouštění aplikací Java SE.

### <a name="how-often-will-java-runtimes-get-updated"></a>Jak často se budou běhové moduly Java dostanou aktualizovat?

LTS a MTS JDK Release mají v případě potřeby čtvrtletní aktualizace zabezpečení, opravy chyb a kritické aktualizace a opravy pro vzdálenou správu. Tato podpora zahrnuje i nové porty pro jazyky Java 7 a 8 aktualizací zabezpečení a opravy chyb hlášené v novějších verzích Java, jako je Java 11.

### <a name="how-long-will-java-8-and-java-11-lts-versions-be-supported"></a>Jak dlouho budou podporované verze Java 8 a Java 11 LTS?

Viz [Dlouhodobá podpora jazyka Java pro Azure a Azure Stack](/azure/developer/java/fundamentals/java-jdk-long-term-support).

* Jazyk Java 8 LTS bude podporován do prosince 2030.
* Java 11 LTS se bude podporovat až do září 2027.

### <a name="how-can-i-download-a-supported-java-runtime-for-local-development"></a>Jak můžu stáhnout podporovaný běhový modul Java pro místní vývoj?

Přečtěte si téma [instalace JDK pro Azure a Azure Stack](/azure/developer/java/fundamentals/java-jdk-install).

### <a name="what-is-the-retire-policy-for-older-java-runtimes"></a>Jaké jsou zásady vyřazení pro starší běhové moduly Java?

Veřejné oznámení se odešle do 12 měsíců před vyřazením původní verze modulu runtime. Po dobu 12 měsíců budete chtít migrovat na novější verzi.

* Správci předplatného obdrží e-mail s oznámením, že vyřadíme verzi Java.
* Informace o vyřazení budou zveřejněny v dokumentaci.

### <a name="how-can-i-get-support-for-issues-at-the-java-runtime-level"></a>Jak získám podporu pro problémy na úrovni běhového prostředí Java?

Můžete otevřít lístek podpory s podporou Azure.  Podívejte [se, jak vytvořit žádost o podporu Azure](../azure-portal/supportability/how-to-create-azure-support-request.md).

### <a name="what-is-the-operation-system-to-run-my-apps"></a>K čemu má operační systém běžet moje aplikace?

Použije se nejnovější verze Ubuntu LTS, aktuálně [Ubuntu 20,04 LTS (kontaktní fossa)](https://releases.ubuntu.com/focal/) je výchozí operační systém.

### <a name="how-often-are-os-security-patches-applied"></a>Jak často se používají opravy zabezpečení operačního systému?

Opravy zabezpečení použitelné pro jarní cloudy Azure jsou v produkčním prostředí zavedeny na základě měsíčního nasazení.
Důležité opravy zabezpečení (CVE skóre >= 9) použitelné pro jarní cloudy Azure jsou nasazeny co nejdříve.
::: zone-end

## <a name="deployment"></a>Nasazení

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Podporuje nasazení s modrou zeleným cloudem Azure?
Yes. Další informace najdete v tématu [Nastavení přípravného prostředí](spring-cloud-howto-staging-environment.md).

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>Můžu získat přístup k Kubernetes, abyste mohli manipulovat s kontejnery aplikací?

Ne.  Azure jaře Cloud vyabstrakce vývojáře z základní architektury, což vám umožní soustředit se na kód aplikace a obchodní logiku.

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Podporuje Azure jaře Cloud vytváření kontejnerů ze zdroje?

Yes. Další informace najdete v tématu [spuštění vaší jarní cloudové aplikace ze zdrojového kódu](spring-cloud-quickstart.md).

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Podporuje Azure jaře Cloud automatické škálování v instancích aplikací?

Yes.  Další informace najdete v tématu [Nastavení automatického škálování](spring-cloud-tutorial-setup-autoscale.md).

::: zone pivot="programming-language-java"
### <a name="what-are-the-best-practices-for-migrating-existing-spring-cloud-microservices-to-azure-spring-cloud"></a>Jaké jsou osvědčené postupy pro migraci stávajících mikroslužeb pro jarní Cloud do Azure jaře cloudu?

Při migraci stávajících mikroslužeb pro jarní Cloud do jarního cloudu Azure je vhodné sledovat následující osvědčené postupy:
* Všechny závislosti aplikací je potřeba vyřešit.
* Připravte si položky konfigurace, proměnné prostředí a parametry JVM, abyste je mohli porovnat s nasazením v Azure jaře cloudu.
* Pokud chcete použít vazbu služby, Projděte si služby Azure a ujistěte se, že jste nastavili příslušná přístupová oprávnění.
* Doporučujeme odebrat nebo zakázat všechny vložené služby, které mohou být v konfliktu se službami, které jsou spravovány službou Azure jaře Cloud, jako je naše služba zjišťování služeb, konfigurační server a tak dále.
* Doporučujeme používat oficiální a stabilní Pivotické knihovny. Neoficiální, beta nebo rozvětvené verze kontingenčních knihoven Pivoter nemají podporu smlouvy o úrovni služeb (SLA).

Po migraci monitorujte metriky procesoru/paměti RAM a síťový provoz, abyste měli jistotu, že jsou instance aplikace vhodně škálovatelné.
::: zone-end

::: zone pivot="programming-language-csharp"
## <a name="net-core-versions"></a>Verze .NET Core

### <a name="which-net-core-versions-are-supported"></a>Které verze .NET Core jsou podporované?

.NET Core 3,1 a novější verze.

### <a name="how-long-will-net-core-31-be-supported"></a>Jak dlouho bude podporováno rozhraní .NET Core 3,1?

Až do 3. prosince 2022. Viz [zásady podpory .NET Core](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).
::: zone-end


## <a name="troubleshooting"></a>Odstraňování potíží

### <a name="what-are-the-impacts-of-service-registry-rarely-unavailable"></a>Jaké dopady služby Service registry nejsou zřídka dostupné?

V některých případech se může zobrazit několik chyb, jako např. 
```
RetryableEurekaHttpClient: Request execution failure with status code 401; retrying on another server if available
```
z vašich protokolů aplikací. Tento problém zavedla architektura pružiny s velmi nízkou mírou kvůli nestabilní síti nebo jiným problémům se sítí. 

Nemělo by to mít žádný dopad na činnost koncového uživatele, protože klient Eureka má na starosti i zásady opakování. Je možné, že bude považována za jednu přechodnou chybu a bude bezpečně přeskočena.

Tuto část vylepšíme a vyhnete se této chybě aplikacím uživatelů v krátké budoucnosti.


## <a name="next-steps"></a>Další kroky

Pokud máte další otázky, přečtěte si [příručku k odstraňování potíží se službou Azure pružiny Cloud](spring-cloud-troubleshoot.md).
