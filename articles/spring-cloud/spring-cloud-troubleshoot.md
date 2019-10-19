---
title: Průvodce odstraňováním potíží pro jarní cloud Azure | Microsoft Docs
description: Průvodce odstraňováním potíží pro jarní cloud Azure
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: v-vasuke
ms.openlocfilehash: ee51841046962a6896b4c16e651f85ff761a69fc
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2019
ms.locfileid: "72592482"
---
# <a name="troubleshooting-guide-for-common-problems"></a>Průvodce odstraňováním potíží pro běžné problémy

Tento článek popisuje některé běžné problémy a postup řešení potíží pro vývojáře pracující v rámci jarního cloudu Azure. Doporučujeme také Přečtěte si [článek nejčastějších dotazů](spring-cloud-faq.md).

## <a name="availability-performance-and-application-issues"></a>Problémy s dostupností, výkonem a aplikacemi

### <a name="my-application-cannot-start-for-example-the-endpoint-cannot-be-connected-or-returns-502-after-few-retries"></a>Moji aplikaci nelze spustit (například koncový bod nelze připojit nebo vrátí 502 po několika opakovaných pokusech).

Exportujte protokoly do _Azure Log Analytics_. Tabulka pro protokoly aplikace Spring má název `AppPlatformLogsforSpring`. Pokud se chcete dozvědět víc, navštivte prosím téma [Analýza protokolů a metrik s nastavením diagnostiky](diagnostic-services.md) .

Hledání následující chyby v protokolech indikuje jeden ze dvou pravděpodobných problémů:

`org.springframework.context.ApplicationContextException: Unable to start web server`

* Chybí jedna z bobů nebo jedna z jejích závislostí.
* Jedna z vlastností objektu bean chybí nebo je neplatná. V tomto případě se pravděpodobně zobrazí `java.lang.IllegalArgumentException`.

Vazby služby můžou také způsobit chyby spuštění aplikace. K dotazování protokolů použijte klíčová slova související s propojenými službami.  Předpokládejme například, že vaše aplikace má vazbu na instanci MySQL nastavenou na místní systémový čas. Pokud se aplikace nespustí, může se v protokolu vycházet z následujících chyb:

`java.sql.SQLException: The server time zone value 'Coordinated Universal Time' is unrecognized or represents more than one time zone.`

Pokud chcete tuto chybu opravit, přejděte do `server parameters` instance MySql a změňte `time_zone` z `SYSTEM` na `+0:00`.


### <a name="my-application-crashes-or-throws-an-unexpected-error"></a>Aplikace se chybově ukončí nebo vyvolá neočekávanou chybu

Při ladění selhání aplikace Začněte kontrolou stavu spuštění a stavu zjišťování aplikace. Pokud chcete zajistit, aby všechny aplikace _běžely a běžely_ _, otevřete_v Azure Portal _správu aplikací_ .

* Pokud je stav _spuštěno_ , ale stav zjišťování _není zapnuto_, nelze přejít do [složky aplikace nelze registrovat](#my-application-cannot-be-registered).

* Pokud je stav _zjišťování zapnutý, můžete_přejít na _metriky_ a ověřit stav aplikace. Zkontrolujte následující metriky:


  - `TomcatErrorCount` (_Tomcat. Global. Error_): zde budou započítány všechny výjimky aplikace pružiny. Pokud je toto číslo velké, vyhledejte v protokolech aplikací _Azure Log Analytics_ .

  - `AppMemoryMax` (_JVM. Memory. Max_): maximální množství paměti, které aplikace je k dispozici. V případě definování může být nedefinovaná nebo v čase měnit. Velikost využité a potvrzené paměti bude vždy nižší nebo stejná jako maximální velikost paměti, pokud je definovaná. Při pokusu o zvětšení využité paměti tak, že velikost využité paměti bude vyšší než velikost potvrzené paměti, však může přidělení paměti selhat s chybou `OutOfMemoryError`, a to i v případě, že platí, že velikost využité paměti je menší nebo rovna maximální velikosti paměti. V takovém případě zkuste pomocí parametru `-Xmx` zvětšit maximální velikost haldy.

  - `AppMemoryUsed` (_JVM. Memory .Ed_): množství paměti v bajtech, které aplikace aktuálně používá. V případě aplikace Java s normálním zatížením se řada této metriky zobrazí ve tvaru zubů pily, kdy se využití paměti plynule zvyšuje a snižuje po malých přírůstcích, pak najednou prudce klesne a tento vzor se opakuje. Důvodem je uvolňování paměti ve virtuálním počítači Java, kde akce shromažďování reprezentují na ' Sawteeth '.
    Tato metrika je důležitá pro identifikaci problémů s pamětí, například: * rozpad paměti na začátku * přidělení paměti přetečení pro konkrétní logickou cestu * postupný únik paměti

  Další podrobnosti najdete v [metrikách](spring-cloud-concept-metrics.md).

Pokud chcete začít s _Azure Log Analytics_, navštivte [Tento článek Začínáme](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) .

### <a name="my-application-experiences-high-cpu-usage-or-high-memory-usage"></a>V aplikaci dochází k vysokému využití procesoru nebo paměti

Pokud vaše aplikace pracuje s vysokým využitím procesoru nebo paměti, je pravdivá jedna z těchto dvou věcí:
* U všech instancí aplikace dochází k vysokému využití CPU nebo paměti nebo
* U některých instancí aplikace dochází k vysokému využití procesoru nebo paměti.

Pokud chcete potvrdit, která situace je,

1. Přejděte do části _Metriky_ a vyberte `Service CPU Usage Percentage` nebo `Service Memory Used`.
2. Přidejte filtr `App=` a zadejte, kterou aplikaci chcete monitorovat, a
3. Rozdělte metriky o `Instance`.

Pokud u všech instancí dochází k vysokému zatížení procesoru nebo paměti, je potřeba buď škálovat aplikaci, nebo škálovat procesor nebo paměť. Další podrobnosti najdete na stránce [škálování aplikací](spring-cloud-tutorial-scale-manual.md) .

Pokud u některých instancí dochází k vysokému zatížení procesoru nebo paměti, ověřte stav instance a její stav zjišťování.

Další podrobnosti najdete v [metrikách](spring-cloud-concept-metrics.md).

Pokud jsou všechny instance v provozu, přejděte do _Azure Log Analytics_ a Dotazujte se na protokoly aplikací a Prohlédněte si logiku kódu, abyste viděli, jestli některý z nich může ovlivnit dělení stupnice. Další podrobnosti najdete v informacích o [analýze protokolů a metrik s nastavením diagnostiky](diagnostic-services.md).

Pokud chcete začít s _Azure Log Analytics_, navštivte [Tento článek Začínáme](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) . Dotazování protokolů pomocí [dotazovacího jazyka Kusto](https://docs.microsoft.com/azure/kusto/query/)

### <a name="checklist-before-onboarding-your-spring-application-to-azure-spring-cloud"></a>Kontrolní seznam před zprovozněním aplikace pružiny do jarního cloudu Azure

* Aplikace může běžet místně se zadanou verzí Java Runtime.
* Konfigurace prostředí (CPU/RAM/instance) splňuje minimální požadavky nastavené poskytovatelem aplikace.
* Položky konfigurace mají očekávané hodnoty. Další informace najdete v tématu [konfigurační server](spring-cloud-tutorial-config-server.md).
* Proměnné prostředí mají očekávané hodnoty.
* Parametry JVM mají očekávané hodnoty.
* Doporučujeme, abyste v balíčku aplikace zakázali nebo odebrali službu vloženého _konfiguračního serveru_ a služby _registru jarní služby_ .
* Pokud se nějaké prostředky Azure mají vázat přes _vazbu služby_, ujistěte se, že jsou cílové prostředky v provozu.

## <a name="configuration-and-management"></a>Konfigurace a správa

### <a name="i-encountered-a-problem-creating-an-azure-spring-cloud-service-instance"></a>Došlo k potížím při vytváření instance služby jarní cloudové služby Azure

Při pokusu o zřízení instance služby _jarní cloudové služby Azure_ prostřednictvím portálu vám Azure jaře Cloud provede ověření za vás.

Pokud se ale pokusíte zřídit instanci služby _jarní cloudovou službu Azure_ pomocí [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) nebo [šablony Správce prostředků](https://docs.microsoft.com/azure/azure-resource-manager/), ověřte:

* Předplatné je aktivní.
* Toto umístění [podporuje](spring-cloud-faq.md) služba _Azure jaře Cloud_.
* Skupina prostředků pro instanci je už vytvořená.
* Název prostředku odpovídá pravidlu pojmenování. (Může obsahovat jenom malá písmena, číslice a spojovníky. Prvním znakem musí být písmeno. Posledním znakem musí být písmeno nebo číslice. Hodnota musí být dlouhá 2 až 32 znaků.)

Pokud se pokusíte zřídit instanci služby _jarní cloudovou službu Azure_ pomocí šablony Správce prostředků, navštivte https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates a zkontrolujte syntaxi šablony.

Název instance služby _jarní cloudová služba Azure_ se použije pro vyžádání názvu subdomény v rámci `azureapps.io`, takže pokud je název v konfliktu s existujícím názvem, nepodaří se mu. Další podrobnosti najdete v protokolech aktivit.

### <a name="i-cannot-deploy-a-jar-package"></a>Nejde nasadit balíček JAR

JAR/zdrojový balíček nemůžete nahrát přes portál nebo šablonu Správce prostředků.

Při nasazení balíčku aplikace pomocí rozhraní příkazového [řádku Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)se bude pravidelně dotazovat na průběh nasazení a na konci se zobrazí výsledek nasazení.

Pokud dojde k přerušení dotazování, stále můžete načíst protokoly nasazení pomocí následujícího příkazu:

`az spring-cloud app show-deploy-log -n <app-name>`

Ujistěte se, že je vaše aplikace zabalená ve správném [spustitelném formátu jar](https://docs.spring.io/spring-boot/docs/current/reference/html/executable-jar.html). Pokud ne, zobrazí se podobná chyba:

`Error: Invalid or corrupt jarfile /jar/38bc8ea1-a6bb-4736-8e93-e8f3b52c8714`

### <a name="i-cannot-deploy-a-source-package"></a>Nejde nasadit zdrojový balíček

JAR/zdrojový balíček nemůžete nahrát přes portál nebo šablonu Správce prostředků.

Pokud balíček aplikace nasadíte prostřednictvím [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli), bude se pravidelně dotazovat na průběh nasazení a nakonec se zobrazí výsledek nasazení.

Pokud dojde k přerušení dotazování, stále můžete načíst protokoly sestavení a nasazení pomocí následujícího příkazu:

`az spring-cloud app show-deploy-log -n <app-name>`

Upozorňujeme však, že jedna instance služby _jarní cloudová služba Azure_ může najednou aktivovat jenom jednu úlohu sestavení pro jeden zdrojový balíček. Další podrobnosti najdete v tématu nasazení průvodce [aplikací](spring-cloud-quickstart-launch-app-portal.md) a [přípravného prostředí](spring-cloud-howto-staging-environment.md).

### <a name="my-application-cannot-be-registered"></a>Aplikaci nejde zaregistrovat

Ve většině případů k tomu dojde v případě, že požadované závislosti nebo zjišťování služeb nejsou správně nakonfigurovány v souboru POM. Po nakonfigurování se integrovaný koncový bod serveru registru služby vloží do vaší aplikace jako proměnná prostředí. Aplikace se pak budou registrovat se serverem registru služby a zjišťovat další závislé mikroslužby.

Počkejte alespoň 2 minuty, než nově zaregistrovaná instance začne přijímat přenosy.

Pokud migrujete stávající řešení založené na jarním cloudu do Azure, ujistěte se, že se odeberou _služby_ ad hoc a instance _konfiguračního serveru_ (nebo jsou zakázané), aby se zabránilo konfliktům se spravovanými instancemi, které poskytuje _Azure jarní Cloud._ .

Můžete také ověřit protokoly klienta _registru služby_ v _Azure Log Analytics_. Další podrobnosti najdete v informacích o [analýze protokolů a metrik s nastavením diagnostiky](diagnostic-services.md) .

Pokud chcete začít s _Azure Log Analytics_, navštivte [Tento článek Začínáme](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) . Dotazování protokolů pomocí [dotazovacího jazyka Kusto](https://docs.microsoft.com/azure/kusto/query/)

### <a name="i-want-to-inspect-my-applications-environment-variables"></a>Chci zkontrolovat proměnné prostředí moje aplikace

Proměnné prostředí informují cloudové cloudové rozhraní Azure, které zajišťuje, kde Azure chápe, kde a jak nakonfigurovat služby, které tvoří vaši aplikaci.  Zajištění správnosti proměnných prostředí je nezbytným prvním krokem při řešení potenciálních problémů.  Ke kontrole proměnných prostředí můžete použít koncový bod pružinového spouštěcího pohánějícího.  

> [!WARNING]
> Tento postup zpřístupňuje proměnné prostředí pomocí testovacího koncového bodu.  Nepokračujte, pokud je váš koncový bod testu veřejně přístupný nebo pokud jste k aplikaci přiřadili název domény.

1. Přejděte na tuto adresu URL: `https://<your application test endpoint>/actuator/health`.  
    - Odpověď podobná `{"status":"UP"}` indikuje, že byl koncový bod povolen.
    - Pokud je odpověď záporná, zahrňte do `POM.xml` následující závislost:

        ```xml
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
            </dependency>
        ```

1. S povoleným koncovým bodem pohánějícího spouštěcího bodu je potřeba přejít na Azure Portal a najít stránku konfigurace aplikace.  Přidejte proměnnou prostředí s názvem `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE' and the value ` *. 

1. Restartujte aplikaci.

1. Přejděte na `https://<the test endpoint of your app>/actuator/env` a zkontrolujte odpověď.  Mělo by to vypadat takto:

    ```json
    {
        "activeProfiles": [],
        "propertySources": {,
            "name": "server.ports",
            "properties": {
                "local.server.port": {
                    "value": 1025
                }
            }
        }
    }
    ```

Vyhledejte podřízený uzel s názvem `systemEnvironment`.  Tento uzel obsahuje proměnné prostředí vaší aplikace.

> [!IMPORTANT]
> Nezapomeňte zrušit vystavení proměnných prostředí před tím, než aplikaci zpřístupníte veřejnosti.  Přejít na Azure Portal, Najděte stránku konfigurace aplikace a odstraňte tuto proměnnou prostředí: `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE`.

### <a name="i-cannot-find-metrics-or-logs-for-my-application"></a>Nemůžu najít metriky nebo protokoly mojí aplikace

Pokud chcete _zajistit, aby_byla aplikace _spuštěná_ , klikněte na _Správa aplikací_ .

Pokud vidíte metriky z _JVM_ , ale žádné metriky od _Tomcat_, zkontrolujte, jestli je v balíčku aplikace povolená závislost `spring-boot-actuator` a úspěšně se spustí.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

Pokud je možné archivovat protokoly vaší aplikace v účtu úložiště, ale není možné je odesílat do _Azure Log Analytics_, zkontrolujte, jestli jste [správně nastavili svůj pracovní prostor](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace). Pokud používáte bezplatnou úroveň _Azure Log Analytics_, mějte na paměti, že [úroveň Free neposkytuje smlouvu SLA](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_3/).