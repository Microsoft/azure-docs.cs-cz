---
title: Průvodce odstraňováním potíží pro Azure Spring Cloud | Dokumenty společnosti Microsoft
description: Průvodce odstraňováním potíží pro Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: troubleshooting
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: 5dcdb03a6d4ec4f448108dbd771a44f362aa7f20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277579"
---
# <a name="troubleshoot-common-azure-spring-cloud-issues"></a>Řešení běžných problémů s Azure Spring Cloud

Tento článek obsahuje pokyny pro řešení potíží s vývojem Azure Spring Cloud. Další informace najdete v [tématu Nejčastější dotazy k Azure Spring Cloud](spring-cloud-faq.md).

## <a name="availability-performance-and-application-issues"></a>Problémy s dostupností, výkonem a aplikacemi

### <a name="my-application-cant-start-for-example-the-endpoint-cant-be-connected-or-it-returns-a-502-after-a-few-retries"></a>Moje aplikace nelze spustit (například koncový bod nelze připojit, nebo vrátí 502 po několika opakování)

Exportujte protokoly do Azure Log Analytics. Tabulka pro protokoly jarních aplikací se jmenuje *AppPlatformLogsforSpring*. Další informace najdete v [tématu Analýza protokolů a metrik s nastavením diagnostiky](diagnostic-services.md).

V protokolech se může zobrazit následující chybová zpráva:

> "org.springframework.context.ApplicationContextException: Nelze spustit webový server"

Zpráva označuje jeden ze dvou pravděpodobných problémů: 
* Jedna z fazolí nebo jedna z jeho závislostí chybí.
* Jedna z vlastností objektu bean chybí nebo je neplatná. V tomto případě "java.lang.IllegalArgumentException" se pravděpodobně zobrazí.

Vazby služby může také způsobit selhání spuštění aplikace. Chcete-li dotaz protokoly, použijte klíčová slova, které souvisejí s vázané služby. Předpokládejme například, že vaše aplikace má vazbu na instanci MySQL, která je nastavena na místní systémový čas. Pokud se aplikace nespustí, může se v protokolu zobrazit následující chybová zpráva:

> "java.sql.SQLException: Hodnota časového pásma serveru Koordinovaný světový čas není rozpoznána nebo představuje více než jedno časové pásmo."

Chcete-li tuto chybu `server parameters` opravit, přejděte na instanci MySQL a změňte hodnotu `time_zone` ze *systému* na *+0:00*.


### <a name="my-application-crashes-or-throws-an-unexpected-error"></a>Aplikace se chybově ukončí nebo vyvolá neočekávanou chybu

Při ladění dojde k chybě aplikace, začněte kontrolou stavu spuštění a stavu zjišťování aplikace. Chcete-li tak učinit, přejděte na _správu aplikací_ na webu Azure Portal a ujistěte se, že stavy všech aplikací jsou _spuštěné_ a _UP_.

* Pokud je stav _Spuštěno,_ ale stav zjišťování není _UP_, přejděte do části [Moje aplikace nemůže být registrována.](#my-application-cant-be-registered)

* Pokud je stav zjišťování _UP_, přejděte na metriky a zkontrolujte stav aplikace. Zkontrolujte následující metriky:


  - `TomcatErrorCount`(_tomcat.global.error_): Zde se počítají všechny výjimky jarní aplikace. Pokud je toto číslo velké, přejděte na Azure Log Analytics a zkontrolujte protokoly aplikací.

  - `AppMemoryMax`(_jvm.memory.max_): Maximální množství paměti dostupné pro aplikaci. Částka může být nedefinovaná nebo se může v průběhu času měnit, pokud je definována. Pokud je definována, množství použité a potvrzené paměti je vždy menší nebo rovno max. Přidělení paměti však může `OutOfMemoryError` selhat se zprávou, pokud se přidělení pokusí zvýšit použitou paměť, která *se používá > potvrzena*, i když *je použita <= max* je stále true. V takovém případě se pokuste zvýšit maximální velikost `-Xmx` haldy pomocí parametru.

  - `AppMemoryUsed`(_jvm.memory.used):_ Velikost paměti v bajtů, která je aktuálně používáaplikace. Pro normální zatížení Java aplikace, tato metrická řada tvoří *sawtooth* vzor, kde využití paměti neustále zvyšuje a snižuje v malých krocích a náhle klesne hodně, a pak se vzorek opakuje. Tato metrika řady dochází z důvodu uvolňování paměti uvnitř virtuálního počítače Java, kde kolekce akce představují kapky na vzor pilový zub.
    
    Tato metrika je důležitá pro identifikaci problémů s pamětí, například:
    * Exploze paměti na samém začátku.
    * Přidělení přepětí paměti pro konkrétní cestu logiky.
    * Postupné úniky paměti.

  Další informace naleznete v [tématu Metriky](spring-cloud-concept-metrics.md).

Další informace o Azure Log Analytics najdete [v tématu Začínáme s Analýzou protokolů v Azure Monitoru](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).

### <a name="my-application-experiences-high-cpu-usage-or-high-memory-usage"></a>V aplikaci dochází k vysokému využití procesoru nebo paměti

Pokud vaše aplikace se zkušenosti vysoké využití procesoru nebo paměti, jedna ze dvou věcí je pravda:
* Všechny instance aplikace zaznamenat vysoké využití procesoru nebo paměti.
* Některé instance aplikace zaznamenat vysoké využití procesoru nebo paměti.

Chcete-li zjistit, která situace se použije, postupujte takto:

1. Přejděte na **Metriky**a vyberte procento využití **procesoru služby** nebo **použitou paměť služby**.
2. Přidejte **filtr App=,** který určuje, kterou aplikaci chcete monitorovat.
3. Metriky rozdělte podle **instance**.

Pokud *všechny instance* dochází k vysoké využití procesoru nebo paměti, je třeba buď horizontální navýšení kapacity aplikace nebo vertikálně navýšit kapacitu procesoru nebo využití paměti. Další informace najdete [v tématu Kurz: Škálování aplikace v Azure Spring Cloud](spring-cloud-tutorial-scale-manual.md).

Pokud *některé instance* dochází k vysoké využití procesoru nebo paměti, zkontrolujte stav instance a její stav zjišťování.

Další informace najdete [v tématu Metriky pro Azure Spring Cloud](spring-cloud-concept-metrics.md).

Pokud jsou všechny instance v provozu, přejděte na Azure Log Analytics dotaz ovat protokoly aplikací a zkontrolujte logiku kódu. To vám pomůže zjistit, zda některý z nich může ovlivnit dělení škálování. Další informace naleznete v [tématu Analýza protokolů a metrik s nastavením diagnostiky](diagnostic-services.md).

Další informace o Azure Log Analytics najdete [v tématu Začínáme s Analýzou protokolů v Azure Monitoru](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal). Dotaz na protokoly pomocí [dotazovacího jazyka Kusto](https://docs.microsoft.com/azure/kusto/query/).

### <a name="checklist-for-deploying-your-spring-application-to-azure-spring-cloud"></a>Kontrolní seznam pro nasazení jarní aplikace do Azure Spring Cloud

Před nástupem do aplikace se ujistěte, že splňuje následující kritéria:

* Aplikace může běžet místně se zadanou verzí java runtime.
* Konfigurace prostředí (CPU/RAM/Instance) splňuje minimální požadavek nastavený poskytovatelem aplikace.
* Položky konfigurace mají své očekávané hodnoty. Další informace naleznete [v tématu Config Server](spring-cloud-tutorial-config-server.md).
* Proměnné prostředí mají své očekávané hodnoty.
* Parametry JVM mají své očekávané hodnoty.
* Doporučujeme zakázat nebo odebrat vložené služby _config serveru_ a _jarního registru služby_ z balíčku aplikace.
* Pokud se nějaké prostředky Azure mají vázat přes _vazbu služby_, ujistěte se, že jsou cílové prostředky v provozu.

## <a name="configuration-and-management"></a>Konfigurace a správa

### <a name="i-encountered-a-problem-with-creating-an-azure-spring-cloud-service-instance"></a>Došlo k problému s vytvořením instance služby Azure Spring Cloud

Když nastavíte instanci služby Azure Spring Cloud pomocí portálu Azure, Azure Spring Cloud provede ověření za vás.

Ale pokud se pokusíte nastavit instanci služby Azure Spring Cloud pomocí [azure cli](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) nebo [šablony Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/), ověřte, že:

* Předplatné je aktivní.
* Umístění je [podporované](spring-cloud-faq.md) Službou Azure Spring Cloud.
* Skupina prostředků pro instanci je již vytvořena.
* Název prostředku odpovídá pravidlu pojmenování. Musí obsahovat pouze malá písmena, číslice a pomlčky. Prvním znakem musí být písmeno. Posledním znakem musí být písmeno nebo číslice. Hodnota musí obsahovat 2 až 32 znaků.

Pokud chcete nastavit instanci služby Azure Spring Cloud pomocí šablony Správce prostředků, nejprve se podívejte na [Principy struktury a syntaxe šablon Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates).

Název instance služby Azure Spring Cloud se použije pro vyžádání `azureapps.io`názvu subdomény v části , takže nastavení se nezdaří, pokud je název v konfliktu s existující. Další podrobnosti můžete najít v protokolech aktivit.

### <a name="i-cant-deploy-a-jar-package"></a>Nelze nasadit balíček JAR

Soubor archivu Java (JAR)/zdrojový balíček nelze nahrát pomocí portálu Azure nebo šablony Správce prostředků.

Při nasazení balíčku aplikace pomocí [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli), Azure CLI pravidelně dotazuje průběh nasazení a nakonec zobrazí výsledek nasazení.

Pokud dojde k přerušení dotazování, stále můžete načíst protokoly nasazení pomocí následujícího příkazu:

`az spring-cloud app show-deploy-log -n <app-name>`

Ujistěte se, že je aplikace zabalena ve správném [spustitelném formátu JAR](https://docs.spring.io/spring-boot/docs/current/reference/html/executable-jar.html). Pokud není správně zabalen, zobrazí se chybová zpráva podobná následující:

> "Chyba: Neplatný nebo poškozený jarfile /jar/38bc8ea1-a6bb-4736-8e93-e8f3b52c8714"

### <a name="i-cant-deploy-a-source-package"></a>Nelze nasadit zdrojový balíček

Balíček JAR/source nelze nahrát pomocí portálu Azure nebo šablony Správce prostředků.

Při nasazení balíčku aplikace pomocí [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli), Azure CLI pravidelně dotazuje průběh nasazení a nakonec zobrazí výsledek nasazení.

Pokud dojde k přerušení dotazování, stále můžete načíst protokoly sestavení a nasazení pomocí následujícího příkazu:

`az spring-cloud app show-deploy-log -n <app-name>`

Všimněte si však, že jedna instance služby Azure Spring Cloud může aktivovat pouze jednu úlohu sestavení pro jeden zdrojový balíček najednou. Další informace najdete [v tématu Nasazení aplikace](spring-cloud-quickstart-launch-app-portal.md) a Nastavení [pracovního prostředí v Azure Spring Cloud](spring-cloud-howto-staging-environment.md).

### <a name="my-application-cant-be-registered"></a>Moje přihláška nemůže být registrována

Ve většině případů k této situaci dochází, když *požadované závislosti* a *zjišťování služby* nejsou správně nakonfigurovány v souboru objektového modelu projektu (POM). Jakmile je nakonfigurován, vestavěný koncový bod serveru registru služeb je vložen jako proměnná prostředí s vaší aplikací. Aplikace se pak zaregistrují na serveru registru služeb a zjišťují další závislé mikroslužby.

Počkejte alespoň dvě minuty, než nově registrovaná instance začne přijímat provoz.

Pokud migrujete existující řešení založené na jarním cloudu do Azure, ujistěte se, že vaše instance ad-hoc _Service Registry_ a _Config Server_ jsou odebrány (nebo zakázány), aby nedošlo ke konfliktu se spravovanými instancemi poskytovanými službou Azure Spring Cloud.

Můžete také zkontrolovat protokoly klientů _registru služeb_ v Azure Log Analytics. Další informace naleznete v [tématu Analýza protokolů a metrik s nastavením diagnostiky.](diagnostic-services.md)

Další informace o Azure Log Analytics najdete [v tématu Začínáme s Analýzou protokolů v Azure Monitoru](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal). Dotaz na protokoly pomocí [dotazovacího jazyka Kusto](https://docs.microsoft.com/azure/kusto/query/).

### <a name="i-want-to-inspect-my-applications-environment-variables"></a>Chci zkontrolovat proměnné prostředí aplikace

Proměnné prostředí informují o rámci Azure Spring Cloud a zajišťují, že Azure chápe, kde a jak nakonfigurovat služby, které tvoří vaši aplikaci. Zajištění správné proměnné prostředí je nezbytným prvním krokem při řešení potenciálních problémů.  Koncový bod aktuátoru pružinového startu můžete použít ke kontrole proměnných prostředí.  

> [!WARNING]
> Tento postup zpřístupňuje proměnné prostředí pomocí koncového bodu testu.  Nepokračujte, pokud je koncový bod testu veřejně přístupný nebo pokud jste aplikaci přiřadili název domény.

1. Přejděte do části `https://<your application test endpoint>/actuator/health` (Soubor > Nový > Jiné).  
    - Odpověď podobná `{"status":"UP"}` označuje, že koncový bod byl povolen.
    - Pokud je odpověď záporná, zahrňte do souboru *POM.xml* následující závislost:

        ```xml
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
            </dependency>
        ```

1. Pokud je povolen koncový bod akceptoru jarního spuštění, přejděte na portál Azure a vyhledejte konfigurační stránku vaší aplikace.  Přidejte proměnnou prostředí `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE` s `*` názvem a hodnotou . 

1. Restartujte aplikaci.

1. Přejděte `https://<your application test endpoint>/actuator/env` na a zkontrolujte odpověď.  Mělo by to vypadat takto:

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

Vyhledejte podřízený uzel `systemEnvironment`s názvem .  Tento uzel obsahuje proměnné prostředí aplikace.

> [!IMPORTANT]
> Nezapomeňte zvrátit expozici proměnných prostředí před zpřístupněním aplikace veřejnosti.  Přejděte na portál Azure, vyhledejte konfigurační stránku `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE`aplikace a odstraňte tuto proměnnou prostředí: .

### <a name="i-cant-find-metrics-or-logs-for-my-application"></a>Nemohu najít metriky nebo protokoly pro svou aplikaci

Přejděte na **správu aplikací** a ujistěte se, že stavy aplikací jsou _spuštěné_ a _UP_.

Pokud vidíte metriky z _JVM,_ ale žádné metriky z `spring-boot-actuator` _Tomcat_, zkontrolujte, zda je závislost povolena v balíčku aplikace a zda se úspěšně spustí.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

Pokud vaše protokoly aplikací lze archivovat do účtu úložiště, ale není odeslána do Azure Log Analytics, zkontrolujte, zda jste [nastavili pracovní prostor správně](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace). Pokud používáte bezplatnou úroveň Azure Log Analytics, všimněte si, že [bezplatná úroveň neposkytuje smlouvu o úrovni služeb (SLA).](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_3/)
