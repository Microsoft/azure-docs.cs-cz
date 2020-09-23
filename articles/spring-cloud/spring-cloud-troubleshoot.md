---
title: Průvodce odstraňováním potíží pro jarní cloud Azure | Microsoft Docs
description: Průvodce odstraňováním potíží pro jarní cloud Azure
author: bmitchell287
ms.service: spring-cloud
ms.topic: troubleshooting
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: d3094a8cca317e53dd3b8bc8e9b32b956c89a376
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90904192"
---
# <a name="troubleshoot-common-azure-spring-cloud-issues"></a>Řešení běžných problémů s jarním cloudem v Azure

Tento článek poskytuje pokyny pro řešení potíží s vývojem ve službě Azure jaře Cloud. Další informace najdete v tématu [Nejčastější dotazy k Azure jaře cloudu](spring-cloud-faq.md).

## <a name="availability-performance-and-application-issues"></a>Problémy s dostupností, výkonem a aplikacemi

::: zone pivot="programming-language-java"
### <a name="my-application-cant-start-for-example-the-endpoint-cant-be-connected-or-it-returns-a-502-after-a-few-retries"></a>Moje aplikace se nemůže spustit (například koncový bod nelze připojit, nebo po několika opakovaných pokusech vrátí 502).

Exportujte protokoly do Azure Log Analytics. Tabulka pro protokoly aplikace pružiny má název *AppPlatformLogsforSpring*. Další informace najdete v tématu [Analýza protokolů a metrik pomocí nastavení diagnostiky](diagnostic-services.md).

V protokolech se může zobrazit následující chybová zpráva:

> "org. springframework. Context. ApplicationContextException: nelze spustit webový server"

Zpráva indikuje jeden ze dvou pravděpodobných problémů: 
* Chybí jedna z bobů nebo jedna z jejích závislostí.
* Jedna z vlastností objektu bean chybí nebo je neplatná. V tomto případě se pravděpodobně zobrazí "Java. lang. IllegalArgumentException".

Vazby služby můžou také způsobit chyby spuštění aplikace. K dotazování protokolů použijte klíčová slova, která se vztahují k vázaným službám. Předpokládejme například, že vaše aplikace má vazbu na instanci MySQL, která je nastavená na místní systémový čas. Pokud se aplikace nespustí, může se v protokolu zobrazit tato chybová zpráva:

> "Java. SQL. SQLException: hodnota časového pásma serveru ' koordinovaný světový čas ' není rozpoznána nebo představuje více než jedno časové pásmo."

Tuto chybu opravíte tak, že přejdete na `server parameters` instanci MySQL a změníte `time_zone` hodnotu ze *systému* na *+ 0:00*.


### <a name="my-application-crashes-or-throws-an-unexpected-error"></a>Aplikace se chybově ukončí nebo vyvolá neočekávanou chybu

Při ladění selhání aplikace Začněte kontrolou stavu spuštění a stavu zjišťování aplikace. Provedete to tak, že přejdete do _správy aplikací_ v Azure Portal a zajistěte, aby byly _spuštěné_ a _aktuální_stavy všech aplikací.

* Pokud je stav _spuštěný_ _, ale stav zjišťování není_zapnutý, můžete přejít do části [Moje aplikace se nedá zaregistrovat](#my-application-cant-be-registered) .

* Pokud je stav _zjišťování zapnutý, můžete_přejít na metriky a ověřit stav aplikace. Zkontrolujte následující metriky:


  - `TomcatErrorCount` (_Tomcat. Global. Error_): zde se počítají všechny výjimky aplikace pružiny. Pokud je toto číslo velké, vyhledejte v protokolech aplikací Azure Log Analytics.

  - `AppMemoryMax` (_JVM. Memory. Max_): maximální množství paměti, které aplikace je k dispozici. Velikost může být nedefinovaná nebo se může v průběhu času změnit, pokud je definována. Pokud je tato hodnota definována, je velikost použité a potvrzené paměti vždy menší než nebo rovna hodnotě max. Přidělení paměti může ale selhat se `OutOfMemoryError` zprávou, pokud se přidělení pokusy o zvýšení využité paměti, jako je například využívané *> potvrzené*, i když *použití <= Max* stále platí. V takové situaci zkuste zvýšit maximální velikost haldy pomocí `-Xmx` parametru.

  - `AppMemoryUsed` (_JVM. Memory .Ed_): množství paměti v bajtech, které aplikace aktuálně používá. V případě normálního zatížení aplikace v jazyce Java Tato metrika tvoří model *vede pilovému efektu* , ve kterém se využití paměti neustále zvětšuje a klesá v malých přírůstcích a náhle se neuvolní a pak se vzor opakuje. Tato řada metrik je způsobena uvolňováním paměti uvnitř virtuálního počítače Java, kde akce shromažďování reprezentují vede pilovému efektu vzor.
    
    Tato metrika je důležitá k identifikaci potíží s pamětí, například:
    * Nárůst paměti na začátku.
    * Přidělení paměti přepětí pro konkrétní logickou cestu.
    * Postupný únik paměti.
  Další informace najdete v tématu [metriky](spring-cloud-concept-metrics.md).
  
* Pokud se aplikace nespustí, ověřte, zda má aplikace platné parametry JVM. Pokud je paměť JVM nastavena příliš vysoká, může se v protokolech zobrazit následující chybová zpráva:

  >"požadovaná paměť 2728741K je větší než 2000M k dispozici pro přidělení"



Další informace o Azure Log Analytics najdete v tématu [Začínáme s Log Analytics v Azure monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).
::: zone-end

### <a name="my-application-experiences-high-cpu-usage-or-high-memory-usage"></a>V aplikaci dochází k vysokému využití procesoru nebo paměti

Pokud vaše aplikace pracuje s vysokým využitím procesoru nebo paměti, je splněna jedna z těchto dvou věcí:
* U všech instancí aplikace dochází k vysokému využití procesoru nebo paměti.
* U některých instancí aplikace dochází k vysokému využití procesoru nebo paměti.

Chcete-li zjistit, jaké situace platí, postupujte následovně:

1. Přejít na **metriky**a pak vyberte buď **procento využití procesoru služby** , nebo **využitá paměť služby**.
2. Přidáním aplikace **=** filtr určete, kterou aplikaci chcete monitorovat.
3. Rozdělte metriky podle **instance**.

Pokud u *všech instancí* dochází k vysokému využití procesoru nebo paměti, je potřeba buď škálovat aplikaci, nebo ŠKÁLOVAT využití procesoru nebo paměti. Další informace najdete v tématu [kurz: škálování aplikace ve jarním cloudu Azure](spring-cloud-tutorial-scale-manual.md).

Pokud u *některých instancí* dochází k vysokému využití procesoru nebo paměti, Projděte si stav instance a její stav zjišťování.

Další informace najdete v tématu [metriky pro jarní cloud Azure](spring-cloud-concept-metrics.md).

Pokud jsou všechny instance v provozu, můžete přejít na Azure Log Analytics a dotazovat se na protokoly aplikací a zkontrolovat logiku kódu. To vám pomůže zjistit, jestli některý z nich může ovlivnit dělení stupnice. Další informace najdete v tématu [Analýza protokolů a metrik pomocí nastavení diagnostiky](diagnostic-services.md).

Další informace o Azure Log Analytics najdete v tématu [Začínáme s Log Analytics v Azure monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal). Dotazování protokolů pomocí [dotazovacího jazyka Kusto](https://docs.microsoft.com/azure/kusto/query/)

::: zone pivot="programming-language-java"
### <a name="checklist-for-deploying-your-spring-application-to-azure-spring-cloud"></a>Kontrolní seznam pro nasazení aplikace pružiny do jarního cloudu Azure

Před zprovozněním aplikace se ujistěte, že splňuje následující kritéria:

* Aplikace může běžet místně se zadanou verzí Java Runtime.
* Konfigurace prostředí (CPU/RAM/instance) splňuje minimální požadavky nastavené poskytovatelem aplikace.
* Položky konfigurace mají své očekávané hodnoty. Další informace najdete v tématu [konfigurační server](spring-cloud-tutorial-config-server.md).
* Proměnné prostředí mají své očekávané hodnoty.
* Parametry JVM mají své očekávané hodnoty.
* Doporučujeme, abyste z balíčku aplikace zapnuli nebo odebrali integrovaný _konfigurační server_ a služby _registru služby pružiny_ .
* Pokud se nějaké prostředky Azure mají vázat přes _vazbu služby_, ujistěte se, že jsou cílové prostředky v provozu.
::: zone-end

## <a name="configuration-and-management"></a>Konfigurace a správa

### <a name="i-encountered-a-problem-with-creating-an-azure-spring-cloud-service-instance"></a>Došlo k potížím s vytvořením instance služby jarní cloudové služby Azure

Při nastavování instance služby jarní cloudu Azure pomocí Azure Portal provede vám ověření za vás Azure jaře Cloud.

Ale pokud se pokusíte nastavit instanci služby jarní cloudovou službu Azure pomocí [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) nebo [šablony Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/), ověřte, že:

* Předplatné je aktivní.
* Toto umístění [podporuje](spring-cloud-faq.md) služba Azure jaře Cloud.
* Skupina prostředků pro instanci je už vytvořená.
* Název prostředku odpovídá pravidlu pojmenování. Musí obsahovat jenom malá písmena, číslice a spojovníky. Prvním znakem musí být písmeno. Posledním znakem musí být písmeno nebo číslo. Hodnota musí být v rozmezí 2 až 32 znaků.

Pokud chcete nastavit instanci služby jarní cloudovou službu Azure pomocí šablony Správce prostředků, nejdřív se seznamte s [pochopením struktury a syntaxe šablon Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates).

Název instance služby jarní cloudová služba Azure se použije pro vyžádání názvu subdomény v rámci `azureapps.io` , takže pokud je název v konfliktu s existujícím názvem, instalace selže. Další podrobnosti najdete v protokolech aktivit.

::: zone pivot="programming-language-java"
### <a name="i-cant-deploy-a-net-core-app"></a>Nemůžu nasadit aplikaci .NET Core

Soubor *. zip* pro aplikaci .NET Core Steeltoe nemůžete nahrát pomocí Azure Portal nebo šablony Správce prostředků.

Když nasadíte balíček aplikace pomocí rozhraní příkazového [řádku Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli), rozhraní příkazového řádku Azure se pravidelně dotazuje průběh nasazení a na konci se zobrazí výsledek nasazení.

Ujistěte se, že je vaše aplikace zabalená ve správném formátu *. zip* . Pokud není zabaleno správně, proces se zablokuje nebo se zobrazí chybová zpráva.
::: zone-end

::: zone pivot="programming-language-java"
### <a name="i-cant-deploy-a-jar-package"></a>Nemůžu nasadit balíček JAR

Balíček/source archivu Java (JAR) nemůžete nahrát pomocí šablony Azure Portal nebo Správce prostředků.

Když nasadíte balíček aplikace pomocí rozhraní příkazového [řádku Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli), rozhraní příkazového řádku Azure se pravidelně dotazuje průběh nasazení a na konci se zobrazí výsledek nasazení.

Pokud dojde k přerušení dotazování, stále můžete načíst protokoly nasazení pomocí následujícího příkazu:

`az spring-cloud app show-deploy-log -n <app-name>`

Ujistěte se, že je vaše aplikace zabalená ve správném [spustitelném formátu jar](https://docs.spring.io/spring-boot/docs/current/reference/html/executable-jar.html). Pokud není zabaleno správně, zobrazí se chybová zpráva podobná následující:

> "Chyba: neplatný nebo poškozený jarfile/jar/38bc8ea1-a6bb-4736-8e93-e8f3b52c8714"

### <a name="i-cant-deploy-a-source-package"></a>Nejde nasadit zdrojový balíček

Nejde nahrát balíček JAR/source pomocí Azure Portal nebo šablony Správce prostředků.

Když nasadíte balíček aplikace pomocí rozhraní příkazového [řádku Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli), rozhraní příkazového řádku Azure se pravidelně dotazuje průběh nasazení a na konci se zobrazí výsledek nasazení.

Pokud dojde k přerušení dotazování, stále můžete načíst protokoly sestavení a nasazení pomocí následujícího příkazu:

`az spring-cloud app show-deploy-log -n <app-name>`

Upozorňujeme však, že jedna instance služby jarní cloudová služba Azure může najednou aktivovat pouze jednu úlohu sestavení pro jeden zdrojový balíček. Další informace najdete v tématu [nasazení aplikace](spring-cloud-quickstart.md) a [Nastavení přípravného prostředí ve jarním cloudu Azure](spring-cloud-howto-staging-environment.md).

### <a name="my-application-cant-be-registered"></a>Moje aplikace se nedá zaregistrovat.

Ve většině případů k této situaci dochází v případě, že *požadované závislosti* a *zjišťování služby* nejsou správně nakonfigurovány v souboru Project Object Model (pom). Po nakonfigurování se integrovaný koncový bod serveru registru služby vloží do vaší aplikace jako proměnná prostředí. Aplikace se pak zaregistrují společně se serverem registru služby a zjišťují se další závislé mikroslužby.

Počkejte alespoň dvě minuty, než nově zaregistrovaná instance začne přijímat přenosy.

Pokud migrujete existující jarní cloudové řešení do Azure, ujistěte se, že se odeberou instance _služby_ ad hoc a instance _konfiguračního serveru_ (nebo jsou zakázané), aby nedocházelo ke konfliktům se spravovanými instancemi, které poskytuje Azure jarní Cloud.

Můžete také ověřit protokoly klienta _registru služby_ v Azure Log Analytics. Další informace najdete v tématu [Analýza protokolů a metrik pomocí nastavení diagnostiky](diagnostic-services.md) .

Další informace o Azure Log Analytics najdete v tématu [Začínáme s Log Analytics v Azure monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal). Dotazování protokolů pomocí [dotazovacího jazyka Kusto](https://docs.microsoft.com/azure/kusto/query/)

### <a name="i-want-to-inspect-my-applications-environment-variables"></a>Chci zkontrolovat proměnné prostředí moje aplikace

Proměnné prostředí informují cloudové cloudové rozhraní Azure, které zajišťuje, kde Azure chápe, kde a jak nakonfigurovat služby, které tvoří vaši aplikaci. Zajištění správnosti proměnných prostředí je nezbytným prvním krokem při řešení potenciálních problémů.  Ke kontrole proměnných prostředí můžete použít koncový bod pružinového spouštěcího pohánějícího.  

> [!WARNING]
> Tento postup zpřístupňuje proměnné prostředí pomocí testovacího koncového bodu.  Nepokračujte, pokud je váš koncový bod testu veřejně přístupný nebo pokud jste k aplikaci přiřadili název domény.

1. Přejděte na `https://<your application test endpoint>/actuator/health`.  
    - Podobná odpověď `{"status":"UP"}` označuje, že byl koncový bod povolen.
    - Pokud je odpověď záporná, zahrňte do souboru *POM.xml* následující závislost:

        ```xml
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
            </dependency>
        ```

1. V případě povoleného koncového bodu pohánějícího spouštěcího modulu je potřeba přejít na Azure Portal a vyhledat stránku konfigurace aplikace.  Přidejte proměnnou prostředí s názvem `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE` a hodnotou `*` . 

1. Restartujte aplikaci.

1. Přejít na `https://<your application test endpoint>/actuator/env` odpověď a zkontrolovat odpověď.  Měl by vypadat takto:

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

Vyhledejte podřízený uzel s názvem `systemEnvironment` .  Tento uzel obsahuje proměnné prostředí vaší aplikace.

> [!IMPORTANT]
> Nezapomeňte zrušit vystavení proměnných prostředí před tím, než aplikaci zpřístupníte veřejnosti.  Přejít na Azure Portal, vyhledat stránku konfigurace aplikace a odstranit tuto proměnnou  `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE` prostředí:

### <a name="i-cant-find-metrics-or-logs-for-my-application"></a>Nemůžu najít metriky nebo protokoly pro moji aplikaci

Pokud chcete zajistit, aby byly stavy aplikace _spuštěné_ a _aktuální_, otevřete **správu aplikací** .

Zkontrolujte, jestli je v balíčku aplikace povolená možnost počasí _JMX_ . Tato funkce se dá povolit s vlastností konfigurace `spring.jmx.enabled=true` .  

Zkontrolujte `spring-boot-actuator` , zda je závislost povolena v balíčku aplikace a zda je úspěšně spuštěná.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

Pokud se protokoly aplikací můžou archivovat do účtu úložiště, ale neodesílají se do Azure Log Analytics, zkontrolujte, jestli jste [pracovní prostor správně nastavili](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace). Pokud používáte bezplatnou úroveň Azure Log Analytics, mějte na paměti, že úroveň [Free neposkytuje smlouvu o úrovni služeb (SLA)](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_3/).
::: zone-end

## <a name="next-steps"></a>Další kroky

* [Jak provést samočinnou diagnostiku a řešení problémů v Azure jaře cloudu](spring-cloud-howto-self-diagnose-solve.md)
