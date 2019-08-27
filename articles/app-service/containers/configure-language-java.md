---
title: Konfigurace aplikací pro Linux Java – Azure App Service | Microsoft Docs
description: Naučte se konfigurovat aplikace Java spuštěné v Azure App Service v systému Linux.
keywords: Azure App Service, Web App, Linux, OSS, Java, Java EE, JEE, JavaEE
services: app-service
author: bmitchell287
manager: douge
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 06/26/2019
ms.author: brendm
ms.custom: seodec18
ms.openlocfilehash: f0cbb8d19d2a7d60fdfd3c10a8c9914ffa79e0a3
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034909"
---
# <a name="configure-a-linux-java-app-for-azure-app-service"></a>Konfigurace aplikace pro Linux Java pro Azure App Service

Azure App Service v systému Linux umožňuje vývojářům v jazyce Java rychle sestavit, nasadit a škálovat zabalené webové aplikace v jazyce Java Standard (SE) na plně spravovanou službu se systémem Linux. Nasaďte aplikace pomocí modulů plug-in Maven z příkazového řádku nebo v editorech, jako je IntelliJ, zatmění nebo Visual Studio Code.

Tato příručka poskytuje klíčové koncepty a pokyny pro vývojáře v jazyce Java, kteří používají integrovaný kontejner Linux v nástroji App Service. Pokud jste Azure App Service nikdy nepoužili, postupujte nejprve podle kurzu [Java Starter](quickstart-java.md) a [Java with PostgreSQL](tutorial-java-enterprise-postgresql-app.md) .

## <a name="deploying-your-app"></a>Nasazení aplikace

K nasazení souborů. jar i. War můžete použít [modul plug-in Maven pro Azure App Service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) . Nasazení s oblíbenými mikroprostředími se podporuje taky [Azure Toolkit for IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij) nebo [Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse).

V opačném případě vaše metoda nasazení bude záviset na typu archivu:

- K nasazení souborů. War do Tomcat použijte `/api/wardeploy/` koncový bod k odeslání souboru archivu. Další informace o tomto rozhraní API najdete v [této dokumentaci](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file).
- Chcete-li nasadit soubory. jar na obrázcích Java se systémem, `/api/zipdeploy/` použijte koncový bod webu Kudu. Další informace o tomto rozhraní API najdete v [této dokumentaci](https://docs.microsoft.com/azure/app-service/deploy-zip#rest).

Nesaďte své. War nebo. jar pomocí FTP. Nástroj FTP je určen pro nahrávání spouštěcích skriptů, závislostí nebo jiných souborů modulu runtime. Nejedná se o optimální volbu pro nasazování webových aplikací.

## <a name="logging-and-debugging-apps"></a>Protokolování a ladění aplikací

Sestavy výkonu, vizualizace provozu a checkups stavu jsou k dispozici pro každou aplikaci prostřednictvím Azure Portal. Další informace najdete v tématu [Přehled diagnostiky Azure App Service](../overview-diagnostics.md).

### <a name="ssh-console-access"></a>Přístup ke konzole SSH

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>Streamování diagnostických protokolů

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

Další informace najdete v tématu [streamování protokolů pomocí Azure CLI](../troubleshoot-diagnostic-logs.md#streaming-with-azure-cli).

### <a name="app-logging"></a>Protokolování aplikace

Povolte [protokolování aplikací](../troubleshoot-diagnostic-logs.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#enablediag) prostřednictvím Azure Portal nebo pomocí [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) a nakonfigurujte App Service tak, aby se do místního systému souborů nebo do služby Azure Blob Storage napsaly standardní a standardní chybové proudy konzolové aplikace. Protokolování do místní instance systému souborů App Service je po konfiguraci zakázáno 12 hodin. Pokud budete potřebovat delší dobu uchování, nakonfigurujte aplikaci tak, aby zapisovala výstup do kontejneru úložiště objektů BLOB. Protokoly aplikací Java a Tomcat najdete v adresáři */Home/LogFiles/Application/* .

Pokud vaše aplikace používá pro trasování [Logback](https://logback.qos.ch/) nebo [log4j](https://logging.apache.org/log4j) , můžete tato trasování přepošlete pro účely revize do Azure Application Insights pomocí pokynů pro konfiguraci protokolovacího rozhraní v tématu [prozkoumávání protokolů trasování Java v Application Insights ](/azure/application-insights/app-insights-java-trace-logs).

### <a name="troubleshooting-tools"></a>Nástroje pro řešení potíží

Integrované image Java jsou založené na operačním systému [Alpine Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) . K instalaci všech nástrojů nebo příkazů pro řešení potíží použijte správce balíčků.`apk`

### <a name="flight-recorder"></a>Záznam letu

Všechny image pro Linux v jazyce Java v App Service mají nainstalovaný Zuluový záznam o letu, abyste se mohli snadno připojit k JVM a spustit záznam profileru nebo vygenerovat výpis haldy.

#### <a name="timed-recording"></a>Časový záznam vypršel

Chcete-li začít, spusťte SSH do svého App Service a `jcmd` spusťte příkaz, který zobrazí seznam všech spuštěných procesů Java. Kromě samotné jcmd by se měla zobrazit vaše aplikace Java spuštěná s IDENTIFIKAČNÍm číslem procesu (PID).

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

Spuštěním následujícího příkazu zahajte záznam JVM o 30 sekund. Tím se profil JVM a vytvoří soubor JFR s názvem *jfr_example. jfr* v domovském adresáři. (Nahraďte 116 číslem PID vaší aplikace v jazyce Java.)

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

Během intervalu 30 sekund můžete ověřit, že záznam probíhá spuštěním `jcmd 116 JFR.check`. Zobrazí se všechny nahrávky pro daný proces Java.

#### <a name="continuous-recording"></a>Průběžný záznam

Pomocí Zulu letu můžete průběžně profilovat svoji aplikaci Java s minimálním dopadem na výkon modulu runtime ([zdroj](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf)). Uděláte to tak, že spustíte následující příkaz Azure CLI, který vytvoří nastavení aplikace s názvem JAVA_OPTS s nezbytnou konfigurací. Obsah nastavení aplikace JAVA_OPTS se před zahájením aplikace předává `java` do příkazu.

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

Po zahájení nahrávání můžete data o nahrávání kdykoli vypsat pomocí `JFR.dump` příkazu.

```shell
jcmd <pid> JFR.dump name=continuous_recording filename="/home/recording1.jfr"
```

Další informace najdete v referenčních informacích k [příkazům Jcmd](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190).

### <a name="analyzing-recordings"></a>Analýza záznamů

Použijte [FTPS](../deploy-ftp.md) ke stažení souboru jfr do místního počítače. Chcete-li analyzovat soubor JFR, Stáhněte a nainstalujte [řízení Zulu](https://www.azul.com/products/zulu-mission-control/). Pokyny k řízení Zulu najdete v [dokumentaci k Azul](https://docs.azul.com/zmc/) a v pokynech k [instalaci](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control).

## <a name="customization-and-tuning"></a>Přizpůsobení a ladění

Azure App Service pro Linux podporuje vyladění a přizpůsobení prostřednictvím Azure Portal a CLI. Projděte si následující články s konfigurací webové aplikace specifické pro jazyk Java:

- [Konfigurovat nastavení aplikace](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)
- [Nastavení vlastní domény](../app-service-web-tutorial-custom-domain.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Povolit SSL](../app-service-web-tutorial-custom-ssl.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Přidat síť CDN](../../cdn/cdn-add-to-web-app.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Konfigurace webu Kudu](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>Nastavení možností modulu Java Runtime

Pokud chcete nastavit přidělenou paměť nebo jiné možnosti JVM runtime v prostředích Tomcat a Java se systémem, vytvořte [nastavení aplikace](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) s názvem `JAVA_OPTS` s možnostmi. App Service Linux předá toto nastavení jako proměnnou prostředí modulu runtime Java při spuštění.

V Azure Portal v části **nastavení aplikace** pro webovou aplikaci vytvořte nové nastavení aplikace s názvem `JAVA_OPTS` , které obsahuje další `-Xms512m -Xmx1204m`nastavení, například.

Pokud chcete nakonfigurovat nastavení aplikace z modulu plug-in Maven, přidejte do části modul plug-in Azure značky nastavení/hodnoty. Následující příklad nastaví konkrétní minimální a maximální velikost haldy Java:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

Vývojáři, kteří spouštějí jednu aplikaci s jedním slotem nasazení v plánu App Service, mohou použít následující možnosti:

- Instance B1 a S1:`-Xms1024m -Xmx1024m`
- Instance B2 a S2:`-Xms3072m -Xmx3072m`
- Instance B3 a S3:`-Xms6144m -Xmx6144m`

Při ladění nastavení haldy aplikace zkontrolujte podrobnosti plánu App Service a vezměte v úvahu více aplikací a slot nasazení potřebuje k vyhledání optimálního přidělení paměti.

Pokud nasazujete aplikaci JAR, měla by být pojmenována *App. jar* , aby předdefinovaná image mohla správně identifikovat vaši aplikaci. (Modul plug-in Maven Toto přejmenování provede automaticky.) Pokud si svůj JAR nepřejete přejmenovat na *App. jar*, můžete nahrát skript prostředí pomocí příkazu, abyste mohli svůj jar spustit. Pak vložte úplnou cestu k tomuto skriptu do textového pole [spouštěcí soubor](app-service-linux-faq.md#built-in-images) v části konfigurace portálu. Spouštěcí skript neběží z adresáře, do kterého je umístěný. Proto vždy používejte absolutní cesty k odkazování na soubory ve spouštěcím skriptu (například: `java -jar /home/myapp/myapp.jar`).

### <a name="turn-on-web-sockets"></a>Zapnout webové sokety

Zapněte podporu pro webové sokety v Azure Portal v **nastavení aplikace** pro danou aplikaci. Aby se nastavení projevilo, bude nutné aplikaci restartovat.

Zapněte podporu webového soketu pomocí rozhraní příkazového řádku Azure pomocí následujícího příkazu:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --web-sockets-enabled true
```

Pak restartujte aplikaci:

```azurecli-interactive
az webapp stop --name <app-name> --resource-group <resource-group-name>
az webapp start --name <app-name> --resource-group <resource-group-name>
```

### <a name="set-default-character-encoding"></a>Nastavit výchozí kódování znaků

V Azure Portal v části **nastavení aplikace** pro webovou aplikaci vytvořte nové nastavení aplikace s názvem `JAVA_OPTS` s hodnotou. `-Dfile.encoding=UTF-8`

Případně můžete nakonfigurovat nastavení aplikace pomocí modulu plug-in App Service Maven. Přidejte značky název a hodnota nastavení do konfigurace modulu plug-in:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

### <a name="adjust-startup-timeout"></a>Upravit časový limit spuštění

Pokud je vaše aplikace Java zvláště velká, měli byste zvýšit časový limit spuštění. Chcete-li to provést, vytvořte nastavení `WEBSITES_CONTAINER_START_TIME_LIMIT` aplikace a nastavte ji na počet sekund, po které App Service čekat před vypršením časového limitu. Maximální hodnota je `1800` sekund.

### <a name="pre-compile-jsp-files"></a>Předem kompilovat soubory JSP

Chcete-li zlepšit výkon aplikací Tomcat, můžete před nasazením do App Service zkompilovat své soubory JSP. Můžete použít [modul plug-in Maven](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) , který poskytuje Apache Sling, nebo použít tento [soubor sestavení ANT](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation).

## <a name="secure-applications"></a>Zabezpečené aplikace

Aplikace Java běžící v App Service pro Linux mají stejnou sadu osvědčených [postupů zabezpečení](/azure/security/security-paas-applications-using-app-services) jako u jiných aplikací.

### <a name="authenticate-users-easy-auth"></a>Ověřování uživatelů (snadné ověřování)

Pomocí možnosti **ověřování a autorizace** nastavte ověřování aplikací v Azure Portal. Odtud můžete povolit ověřování pomocí Azure Active Directory nebo přes sociální přihlášení, jako je Facebook, Google nebo GitHub. Konfigurace Azure Portal funguje pouze při konfiguraci jednoho poskytovatele ověřování. Další informace najdete v tématu [Konfigurace aplikace App Service pro použití Azure Active Directory přihlášení](../configure-authentication-provider-aad.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) a souvisejících článků pro jiné poskytovatele identity. Pokud potřebujete povolit více poskytovatelů přihlašování, postupujte podle pokynů v článku [přizpůsobení App Serviceho ověřování](../app-service-authentication-how-to.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) .

#### <a name="tomcat-and-wildfly"></a>Tomcat a WildFly

Vaše aplikace Tomcat nebo WildFly může získat přístup k deklaracím uživatele přímo z servlet přetypováním objektu zabezpečení na objekt mapy. Objekt mapy bude mapovat jednotlivé typy deklarací na kolekci deklarací pro daný typ. V následujícím `request` kódu je `HttpServletRequest`instance.

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

Nyní můžete zkontrolovat `Map` objekt pro všechny konkrétní deklarace identity. Například následující fragment kódu projde všechny typy deklarací a vytiskne obsah každé kolekce.

```java
for (Object key : map.keySet()) {
        Object value = map.get(key);
        if (value != null && value instanceof Collection {
            Collection claims = (Collection) value;
            for (Object claim : claims) {
                System.out.println(claims);
            }
        }
    }
```

Pokud chcete uživatele podepsat, použijte `/.auth/ext/logout` cestu. Chcete-li provést další akce, přečtěte si dokumentaci k [App Service ověřování a používání autorizace](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to). K dispozici je také oficiální dokumentace k [rozhraní Tomcat HttpServletRequest](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) a jeho metodám. V závislosti na konfiguraci App Service jsou také vycházející následující metody servlet:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

Chcete-li tuto funkci zakázat, vytvořte nastavení aplikace `WEBSITE_AUTH_SKIP_PRINCIPAL` s názvem s `1`hodnotou. Chcete-li zakázat všechny filtry servlet přidané App Service, vytvořte nastavení s `WEBSITE_SKIP_FILTERS` názvem s `1`hodnotou.

#### <a name="spring-boot"></a>Spring Boot

Vývojáři pružinového spouštění můžou pomocí [Azure Active Directory pružiny Boot Starter](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) zabezpečit aplikace pomocí známých poznámek a rozhraní API pro zabezpečení pružiny. Nezapomeňte zvětšit maximální velikost hlavičky v souboru *Application. Properties* . Navrhneme hodnotu `16384`.

### <a name="configure-tlsssl"></a>Konfigurace TLS/SSL

Podle pokynů v tématu [vytvoření vazby existujícího vlastního certifikátu SSL](../app-service-web-tutorial-custom-ssl.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) Nahrajte stávající certifikát SSL a navažte ho k názvu domény vaší aplikace. Ve výchozím nastavení bude vaše aplikace pořád umožňovat připojení HTTP – postupujte podle kroků v tomto kurzu, abyste vynutili SSL a TLS.

### <a name="use-keyvault-references"></a>Použití odkazů na Trezor klíčů

[Azure webtrezor](../../key-vault/key-vault-overview.md) poskytuje centralizovanou správu tajných kódů pomocí zásad přístupu a historie auditu. Do trezoru klíčů můžete ukládat tajné kódy (například hesla nebo připojovací řetězce) a přistupovat k nim ve vaší aplikaci pomocí proměnných prostředí.

Nejdřív postupujte podle pokynů pro [udělení přístupu aplikace Key Vault](../app-service-key-vault-references.md#granting-your-app-access-to-key-vault) a [Vytvoření odkazu trezoru klíčů pro váš tajný klíč v nastavení aplikace](../app-service-key-vault-references.md#reference-syntax). Můžete ověřit, že se odkaz na tajný kód přeloží vytištěním proměnné prostředí a vzdáleným přístupem k App Service terminálu.

Pro vložení těchto tajných kódů do konfiguračního souboru jarní nebo Tomcat použijte syntaxi injektáže proměnné prostředí (`${MY_ENV_VAR}`). V případě konfiguračních souborů pružiny se podívejte na tuto [](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)dokumentaci k externě nakonfigurovaným konfiguracím.

## <a name="configure-apm-platforms"></a>Konfigurace platforem APM

V této části se dozvíte, jak připojit aplikace Java nasazené na Azure App Service v systému Linux pomocí platforem APM (Application Performance Monitoring) pro NewRelic a AppDynamics.

### <a name="configure-new-relic"></a>Konfigurovat nové Relic

1. Vytvoření účtu NewRelic na [NewRelic.com](https://newrelic.com/signup)
2. Stáhněte si agenta Java ze NewRelic, bude mít název souboru podobný řetězci *NewRelic-Java-x. x. x. zip*.
3. Zkopírujte licenční klíč, budete ho potřebovat ke konfiguraci agenta později.
4. [Do své instance App Service](app-service-linux-ssh-support.md) a vytvořte nový adresář */Home/site/wwwroot/APM*.
5. Nahrajte nebalené soubory NewRelic Java do adresáře pod */Home/site/wwwroot/APM*. Soubory pro vašeho agenta by měly být v */Home/site/wwwroot/APM/NewRelic*.
6. Upravte soubor YAML na */Home/site/wwwroot/APM/NewRelic/NewRelic.yml* a nahraďte hodnotu licence zástupného symbolu vlastním licenčním klíčem.
7. V Azure Portal přejděte do aplikace v App Service a vytvořte nové nastavení aplikace.
    - Pokud vaše aplikace používá **Java se**, vytvořte proměnnou prostředí s názvem `JAVA_OPTS` s hodnotou. `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`
    - Pokud používáte **Tomcat**, vytvořte proměnnou prostředí s názvem `CATALINA_OPTS` s hodnotou. `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`
    - Pokud používáte **WildFly**, přečtěte si novou dokumentaci k Relic [](https://docs.newrelic.com/docs/agents/java-agent/additional-installation/wildfly-version-11-installation-java) , kde najdete pokyny k instalaci agenta Java a konfiguraci JBoss.

### <a name="configure-appdynamics"></a>Konfigurace AppDynamics

1. Vytvoření účtu AppDynamics na [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Stáhněte si agenta Java z webu AppDynamics, název souboru bude podobný jako *AppServerAgent-x. x. x. xxxxx. zip*
3. [Do své instance App Service](app-service-linux-ssh-support.md) a vytvořte nový adresář */Home/site/wwwroot/APM*.
4. Nahrajte soubory agenta Java do adresáře pod */Home/site/wwwroot/APM*. Soubory pro vašeho agenta by měly být v */Home/site/wwwroot/APM/AppDynamics*.
5. V Azure Portal přejděte do aplikace v App Service a vytvořte nové nastavení aplikace.
    - Pokud používáte **Java se**systémem, vytvořte proměnnou prostředí s názvem `JAVA_OPTS` s hodnotou `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` , kde `<app-name>` je váš App Service název.
    - Pokud používáte **Tomcat**, vytvořte proměnnou prostředí s názvem `CATALINA_OPTS` s hodnotou `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` , kde `<app-name>` je váš App Service název.
    - Pokud používáte **WildFly**, přečtěte si dokumentaci k AppDynamics [](https://docs.appdynamics.com/display/PRO45/JBoss+and+Wildfly+Startup+Settings) , kde najdete pokyny k instalaci agenta Java a konfiguraci JBoss.

>  Pokud již máte proměnnou prostředí pro `JAVA_OPTS` nebo `CATALINA_OPTS`, přidejte `-javaagent:/...` možnost na konec aktuální hodnoty.

## <a name="configure-jar-applications"></a>Konfigurovat aplikace JAR

### <a name="starting-jar-apps"></a>Spouštění aplikací JAR

Ve výchozím nastavení App Service očekává, že vaše aplikace JAR bude pojmenována *App. jar*. Pokud tento název obsahuje, bude automaticky spuštěn. Pro uživatele Maven můžete název jar nastavit tak, že zahrnete `<finalName>app</finalName>` `<build>` do části souboru *pom. XML*. To [samé můžete provést v Gradle](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.bundling.Jar.html#org.gradle.api.tasks.bundling.Jar:archiveFileName) nastavením `archiveFileName` vlastnosti.

Pokud chcete pro svůj JAR použít jiný název, musíte zadat také [spouštěcí příkaz](app-service-linux-faq.md#built-in-images) , který SPUSTÍ soubor JAR. Například, `java -jar my-jar-app.jar`. Hodnotu pro spouštěcí příkaz můžete nastavit na portálu v části Konfigurace > Obecné nastavení nebo pomocí nastavení aplikace s názvem `STARTUP_COMMAND`.

### <a name="server-port"></a>Port serveru

App Service Linux směruje příchozí požadavky na port 80, takže vaše aplikace by měla naslouchat i na portu 80. To můžete provést v konfiguraci aplikace (jako je například soubor *aplikace. Properties* v jaře) nebo v příkazu pro spuštění (například `java -jar spring-app.jar --server.port=80`). Podívejte se prosím na následující dokumentaci pro běžné architektury Java:

- [Jarní spuštění](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-properties-and-configuration.html#howto-use-short-command-line-arguments)
- [SparkJava](http://sparkjava.com/documentation#embedded-web-server)
- [Micronaut](https://docs.micronaut.io/latest/guide/index.html#runningSpecificPort)
- [Rozhraní Play](https://www.playframework.com/documentation/2.6.x/ConfiguringHttps#Configuring-HTTPS)
- [Vertx](https://vertx.io/docs/vertx-core/java/#_start_the_server_listening)
- [Quarkus](https://quarkus.io/guides/application-configuration-guide)

## <a name="data-sources"></a>Zdroje dat

### <a name="tomcat"></a>Tomcat

Tyto pokyny platí pro všechna databázová připojení. Zástupné symboly budete muset vyplnit pomocí názvu třídy ovladače zvolené databáze a souboru JAR. Poskytuje se tabulka s názvy tříd a stahování ovladačů pro běžné databáze.

| Databáze   | Název třídy ovladače                             | Ovladač JDBC                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Stáhnout](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Stáhnout](https://dev.mysql.com/downloads/connector/j/) (Vyberte možnost nezávislé na platformě) |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Stáhnout](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#available-downloads-of-jdbc-driver-for-sql-server)                                                           |

Chcete-li nakonfigurovat Tomcat pro použití JDBC (Java Database Connectivity) nebo rozhraní Java Persistence (JPA), nejprve `CATALINA_OPTS` upravte proměnnou prostředí, která je čtena nástrojem Tomcat při spuštění. Nastavte tyto hodnoty pomocí nastavení aplikace v [modulu plug-in App Service Maven](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Nebo nastavte proměnné prostředí na stránce nastavení **konfigurační** > **aplikace** v Azure Portal.

Dále určete, zda má být zdroj dat k dispozici pro jednu aplikaci nebo pro všechny aplikace běžící na Tomcat servlet.

#### <a name="application-level-data-sources"></a>Zdroje dat na úrovni aplikace

1. Vytvořte soubor *Context. XML* v adresáři *META-INF/* Directory vašeho projektu. Pokud neexistuje, vytvořte *soubor META-INF nebo* s adresářem.

2. V *kontextu. XML*přidejte `Context` element, který bude propojit zdroj dat s adresou JNDI. `driverClassName` Zástupný symbol nahraďte názvem třídy vašeho ovladače z tabulky výše.

    ```xml
    <Context>
        <Resource
            name="jdbc/dbconnection"
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}"
            password="${connURL}"
        />
    </Context>
    ```

3. Aktualizujte *Web. XML* vaší aplikace tak, aby používal zdroj dat ve vaší aplikaci.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>Sdílené prostředky na úrovni serveru

1. Zkopírujte obsah */usr/local/Tomcat/conf* do */home/tomcat/conf* na instanci App Service Linux pomocí SSH, pokud ještě nemáte konfiguraci.

    ```bash
    mkdir -p /home/tomcat
    cp -a /usr/local/tomcat/conf /home/tomcat/conf
    ```

2. Přidejte kontextový prvek do *souboru Server. XML* v rámci `<Server>` elementu.

    ```xml
    <Server>
    ...
    <Context>
        <Resource
            name="jdbc/dbconnection"
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}"
            password="${connURL}"
        />
    </Context>
    ...
    </Server>
    ```

3. Aktualizujte *Web. XML* vaší aplikace tak, aby používal zdroj dat ve vaší aplikaci.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="finalize-configuration"></a>Dokončit konfiguraci

Nakonec umístěte jar ovladače do cesty pro Tomcat a restartujte App Service.

1. Zajistěte, aby byly soubory ovladače JDBC k dispozici pro classloader Tomcat, a to jejich umístěním do adresáře */Home/Tomcat/lib* . (Pokud tento adresář ještě neexistuje, vytvořte ho.) Chcete-li odeslat tyto soubory do instance App Service, proveďte následující kroky:

    1. V [Cloud Shell](https://shell.azure.com)nainstalujte rozšíření WebApp:

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. Spuštěním následujícího příkazu rozhraní příkazového řádku vytvořte tunel SSH z místního systému, který chcete App Service:

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. Připojte se k místnímu tunelovém portu pomocí klienta SFTP a nahrajte soubory do složky */Home/Tomcat/lib* .

    Alternativně můžete k nahrání ovladače JDBC použít klienta FTP. [Při získávání přihlašovacích údajů k FTP](../deploy-configure-credentials.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)postupujte podle těchto pokynů.

2. Pokud jste vytvořili zdroj dat na úrovni serveru, restartujte aplikaci App Service Linux. Tomcat se obnoví `CATALINA_BASE` `/home/tomcat` a použije aktualizovanou konfiguraci.

### <a name="spring-boot"></a>Spring Boot

Pro připojení ke zdrojům dat v aplikacích pro pružinové spouštění doporučujeme vytvořit připojovací řetězce a vložit je do souboru *. Properties* .

1. V části "konfigurace" na stránce App Service nastavte název řetězce, vložte připojovací řetězec JDBC do pole hodnota a nastavte typ na Custom (vlastní). Volitelně můžete nastavit tento připojovací řetězec jako nastavení slotu.

    Tento připojovací řetězec je pro naši aplikaci přístupný jako proměnná prostředí s názvem `CUSTOMCONNSTR_<your-string-name>`. Například připojovací řetězec, který jsme vytvořili výše, bude pojmenován `CUSTOMCONNSTR_exampledb`.

2. V souboru *Application. Properties* , odkazujte na tento připojovací řetězec s názvem proměnné prostředí. V našem příkladu bychom použili následující.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

Další informace o tomto tématu najdete v [dokumentaci ke jarnímu spuštění pro přístup k datům a k](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html) externě nakonfigurovaným [konfiguracím](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html) .

## <a name="configure-java-ee-wildfly"></a>Konfigurace jazyka Java EE (WildFly)

> [!NOTE]
> Java Enterprise Edition v systému App Service Linux je momentálně ve verzi Preview. Tento zásobník se nedoporučuje pro práci na produkčním prostředí.

Azure App Service v systému Linux umožňuje vývojářům v jazyce Java sestavovat, nasazovat a škálovat aplikace Java Enterprise (Java EE) na plně spravovanou službu se systémem Linux.  Základní běhové prostředí Java Enterprise je open source aplikační server [WildFly](https://wildfly.org/) .

Tato část obsahuje následující pododdíly:

- [Škálování pomocí App Service](#scale-with-app-service)
- [Přizpůsobení konfigurace aplikačního serveru](#customize-application-server-configuration)
- [Nainstalovat moduly a závislosti](#install-modules-and-dependencies)
- [Konfigurace zdrojů dat](#configure-data-sources)
- [Povolit poskytovatele zasílání zpráv](#enable-messaging-providers)

### <a name="scale-with-app-service"></a>Škálování pomocí App Service

Aplikační server WildFly běžící v App Service v systému Linux běží v samostatném režimu, nikoli v konfiguraci domény. Při horizontálním navýšení kapacity App Service plánu je každá instance WildFly nakonfigurovaná jako samostatný server.

Škálujte aplikaci vertikálně nebo vodorovně pomocí [pravidel škálování](../../monitoring-and-diagnostics/monitoring-autoscale-get-started.md) a [zvyšte počet instancí](../manage-scale-up.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

### <a name="customize-application-server-configuration"></a>Přizpůsobení konfigurace aplikačního serveru

Instance webových aplikací jsou bezstavové, takže každá nově spuštěná instance musí být při spuštění nakonfigurovaná tak, aby podporovala konfiguraci WildFly, kterou vyžaduje aplikace.
Můžete napsat spouštěcí skript bash, který volá rozhraní příkazového řádku WildFly na:

- Nastavení zdrojů dat
- Konfigurovat poskytovatele zasílání zpráv
- Přidejte další moduly a závislosti do konfigurace serveru WildFly.

Skript se spustí, když je WildFly v provozu, ale před spuštěním aplikace. Skript by měl používat rozhraní příkazového [řádku JBoss](https://docs.jboss.org/author/display/WFLY/Command+Line+Interface) s názvem z */opt/JBoss/WildFly/bin/JBoss-CLI.sh* ke konfiguraci aplikačního serveru s jakoukoli konfigurací nebo změnami potřebnými po spuštění serveru.

Nepoužívejte interaktivní režim rozhraní příkazového řádku ke konfiguraci WildFly. Místo toho můžete pomocí `--file` příkazu zadat skript příkazů JBoss CLI, například:

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

Pomocí protokolu FTP nahrajte spouštěcí skript do umístění v App Service instanci v adresáři */Home* , například */Home/site/Deployments/Tools*. Další informace najdete v tématu [nasazení aplikace pro Azure App Service pomocí FTP/S](https://docs.microsoft.com/azure/app-service/deploy-ftp).

Do pole **spouštěcí skript** v Azure Portal nastavte umístění spouštěcího skriptu Shell, například */Home/site/Deployments/Tools/Your-Startup-Script.sh*.

Zadejte [Nastavení](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) aplikace v konfiguraci aplikace a předejte proměnné prostředí pro použití ve skriptu. Nastavení aplikace udržuje připojovací řetězce a další tajné kódy, které jsou potřeba ke konfiguraci vaší aplikace z řízení verze.

### <a name="install-modules-and-dependencies"></a>Nainstalovat moduly a závislosti

Chcete-li nainstalovat moduly a jejich závislosti do WildFly classpath prostřednictvím rozhraní příkazového řádku JBoss, budete muset vytvořit následující soubory ve svém vlastním adresáři. Některé moduly a závislosti můžou potřebovat další konfiguraci, jako je JNDI názvů nebo jiná konfigurace specifická pro rozhraní API, takže tento seznam je minimální, co budete muset nakonfigurovat na závislost ve většině případů.

- [Popisovač modulu XML](https://jboss-modules.github.io/jboss-modules/manual/#descriptors). Tento soubor XML definuje název, atributy a závislosti vašeho modulu. Tento [ukázkový soubor Module. XML](https://access.redhat.com/documentation/en-us/jboss_enterprise_application_platform/6/html/administration_and_configuration_guide/example_postgresql_xa_datasource) definuje modul Postgres, jeho závislost JDBC souborů JAR a vyžaduje další závislosti modulů.
- Všechny nezbytné závislosti souborů JAR pro váš modul.
- Skript s příkazy rozhraní příkazového řádku JBoss pro konfiguraci nového modulu. Tento soubor bude obsahovat příkazy, které mají být spuštěny rozhraním příkazového řádku JBoss, aby bylo možné nakonfigurovat server tak, aby používal závislost. Dokumentaci k příkazům pro přidání modulů, zdrojů dat a poskytovatelů zasílání zpráv najdete v [tomto dokumentu](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli).
- Spouštěcí skript bash, který volá rozhraní příkazového řádku JBoss a spustí skript v předchozím kroku. Tento soubor se spustí, když se vaše instance App Service restartuje nebo když se během škálování na více instancí zřídí nové instance. Tento spouštěcí skript je místo, kde můžete pro svou aplikaci provádět jakékoli další konfigurace, protože příkazy JBoss jsou předány do JBoss CLI. Tento soubor může být minimálně jediným příkazem k předání skriptu příkazu JBoss CLI do JBoss CLI:

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

Jakmile budete mít soubory a obsah pro svůj modul, postupujte podle následujících kroků a přidejte modul do aplikačního serveru WildFly.

1. Pomocí protokolu FTP nahrajte soubory do umístění v App Service instanci v adresáři */Home* , jako je například */Home/site/Deployments/Tools*. Další informace najdete v tématu [nasazení aplikace pro Azure App Service pomocí FTP/S](../deploy-ftp.md).
2. Na stránce**Obecné nastavení** **Konfigurace** > v Azure Portal nastavte pole **spouštěcí skript** do umístění skriptu Startup Shell, například */Home/site/Deployments/Tools/Startup.sh*.
3. Kliknutím na tlačítko **restartovat** v části **Přehled** na portálu nebo pomocí Azure CLI Restartujte instanci App Service.

### <a name="configure-data-sources"></a>Konfigurace zdrojů dat

Pokud chcete nakonfigurovat WildFly/JBoss pro přístup ke zdroji dat, použijte obecný proces uvedený výše v části "nainstalovat moduly a závislosti". V následující části najdete konkrétní informace o tomto procesu pro zdroje dat PostgreSQL, MySQL a SQL Server.

V této části se předpokládá, že už máte aplikaci, instanci App Service a instanci služby Azure Database Service. Níže uvedené pokyny odkazují na název App Service, jeho skupinu prostředků a informace o připojení k databázi. Tyto informace najdete na Azure Portal.

Pokud raději projdete celý proces od začátku s použitím ukázkové aplikace, přečtěte si téma [kurz: Sestavte webovou aplikaci Java EE a Postgres v](tutorial-java-enterprise-postgresql-app.md)Azure.

Následující kroky vysvětlují požadavky na připojení stávajících App Service a databáze.

1. Stáhněte si ovladač JDBC pro [PostgreSQL](https://jdbc.postgresql.org/download.html), [MySQL](https://dev.mysql.com/downloads/connector/j/)nebo [SQL Server](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server). Rozbalte stažený archiv a získejte soubor Driver. jar.

2. Vytvořte soubor s názvem jako *Module. XML* a přidejte následující kód. Zástupný symbol (včetně lomených závorek `org.postgres` ) nahraďte pro PostgreSQL `com.mysql` , pro MySQL nebo `com.microsoft` pro SQL Server. `<module name>` Nahraďte `<JDBC .jar file path>` názvem souboru. jar z předchozího kroku, včetně úplné cesty k umístění, kam umístíte soubor do instance App Service. Může to být jakékoli umístění v adresáři */Home* .

    ```xml
    <?xml version="1.0" ?>
    <module xmlns="urn:jboss:module:1.1" name="<module name>">
        <resources>
           <resource-root path="<JDBC .jar file path>" />
        </resources>
        <dependencies>
            <module name="javax.api"/>
            <module name="javax.transaction.api"/>
        </dependencies>
    </module>
    ```

3. Vytvořte soubor s názvem, jako je *DataSource-Commands. CLI* a přidejte následující kód. Nahraďte `<JDBC .jar file path>` hodnotou, kterou jste použili v předchozím kroku. Nahraďte `<module file path>` názvem souboru a App Service cestou z předchozího kroku, například */Home/Module.XML*.

    **PostgreSQL**

    ```console
    module add --name=org.postgres --resources=<JDBC .jar file path> --module-xml=<module file path>

    /subsystem=datasources/jdbc-driver=postgres:add(driver-name=postgres,driver-module-name=org.postgres,driver-class-name=org.postgresql.Driver,driver-xa-datasource-class-name=org.postgresql.xa.PGXADataSource)

    data-source add --name=postgresDS --driver-name=postgres --jndi-name=java:jboss/datasources/postgresDS --connection-url=$DATABASE_CONNECTION_URL --user-name=$DATABASE_SERVER_ADMIN_FULL_NAME --password=$DATABASE_SERVER_ADMIN_PASSWORD --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=org.postgresql.Driver --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLExceptionSorter --jta=true --use-java-context=true --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLValidConnectionChecker

    reload --use-current-server-config=true
    ```

    **MySQL**

    ```console
    module add --name=com.mysql --resources=<JDBC .jar file path> --module-xml=<module file path>

    /subsystem=datasources/jdbc-driver=mysql:add(driver-name=mysql,driver-module-name=com.mysql,driver-class-name=com.mysql.cj.jdbc.Driver)

    data-source add --name=mysqlDS --jndi-name=java:jboss/datasources/mysqlDS --connection-url=$DATABASE_CONNECTION_URL --driver-name=mysql --user-name=$DATABASE_SERVER_ADMIN_FULL_NAME --password=$DATABASE_SERVER_ADMIN_PASSWORD --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=com.mysql.cj.jdbc.Driver --jta=true --use-java-context=true --exception-sorter-class-name=com.mysql.cj.jdbc.integration.jboss.ExtendedMysqlExceptionSorter

    reload --use-current-server-config=true
    ```

    **SQL Server**

    ```console
    module add --name=com.microsoft --resources=<JDBC .jar file path> --module-xml=<module file path>

    /subsystem=datasources/jdbc-driver=sqlserver:add(driver-name=sqlserver,driver-module-name=com.microsoft,driver-class-name=com.microsoft.sqlserver.jdbc.SQLServerDriver,driver-datasource-class-name=com.microsoft.sqlserver.jdbc.SQLServerDataSource)

    data-source add --name=sqlDS --jndi-name=java:jboss/datasources/sqlDS --driver-name=sqlserver --connection-url=$DATABASE_CONNECTION_URL --validate-on-match=true --background-validation=false --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.mssql.MSSQLValidConnectionChecker --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.mssql.MSSQLExceptionSorter

    reload --use-current-server-config=true
    ```

    Tento soubor se spouští spouštěcím skriptem popsaným v dalším kroku. Nainstaluje ovladač JDBC jako modul WildFly, vytvoří odpovídající zdroj dat WildFly a znovu načte server, aby se projevily změny.

4. Vytvořte soubor s názvem jako *Startup.sh* a přidejte následující kód. Nahraďte `<JBoss CLI script>` názvem souboru, který jste vytvořili v předchozím kroku. Nezapomeňte uvést úplnou cestu k umístění, kam umístíte soubor do instance App Service, například */Home/DataSource-Commands.CLI*.

    ```bash
    #!/usr/bin/env bash
    /opt/jboss/wildfly/bin/jboss-cli.sh -c --file=<JBoss CLI script>
    ```

5. Pomocí FTP nahrajte soubor JDBC. jar, soubor XML modulu, skript rozhraní příkazového řádku JBoss a spouštěcí skript do instance App Service. Tyto soubory vložte do umístění, které jste zadali v předchozích krocích, například */Home*. Další informace o FTP najdete v tématu [nasazení aplikace pro Azure App Service pomocí protokolu FTP/S](https://docs.microsoft.com/azure/app-service/deploy-ftp).

6. Pomocí rozhraní příkazového řádku Azure můžete přidat nastavení do App Service, která obsahují informace o připojení k databázi. Hodnoty `<resource group>` a`<webapp name>` nahraďte hodnotami, které App Service používá. `<database server name>`Nahraďte `<database name>`,, a`<admin password>` s informacemi o připojení k databázi. `<admin name>` Z Azure Portal můžete získat informace o App Service a databázi.

    **PostgreSQL:**

    ```bash
    az webapp config appsettings set \
        --resource-group <resource group> \
        --name <webapp name> \
        --settings \
            DATABASE_CONNECTION_URL=jdbc:postgresql://<database server name>:5432/<database name>?ssl=true \
            DATABASE_SERVER_ADMIN_FULL_NAME=<admin name> \
            DATABASE_SERVER_ADMIN_PASSWORD=<admin password>
    ```

    **MySQL:**

    ```bash
    az webapp config appsettings set \
        --resource-group <resource group> \
        --name <webapp name> \
        --settings \
            DATABASE_CONNECTION_URL=jdbc:mysql://<database server name>:3306/<database name>?ssl=true\&useLegacyDatetimeCode=false\&serverTimezone=GMT \
            DATABASE_SERVER_ADMIN_FULL_NAME=<admin name> \
            DATABASE_SERVER_ADMIN_PASSWORD=<admin password>
    ```

    **SQL Server:**

    ```bash
    az webapp config appsettings set \
        --resource-group <resource group> \
        --name <webapp name> \
        --settings \
            DATABASE_CONNECTION_URL=jdbc:sqlserver://<database server name>:1433;database=<database name>;user=<admin name>;password=<admin password>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
    ```

    Hodnoty DATABASE_CONNECTION_URL se pro každý databázový server liší a liší se od hodnot v Azure Portal. Zde uvedené formáty adres URL (a ve výše uvedených fragmentech) jsou vyžadovány pro použití v WildFly:

    * **PostgreSQL:** `jdbc:postgresql://<database server name>:5432/<database name>?ssl=true`
    * **MySQL:** `jdbc:mysql://<database server name>:3306/<database name>?ssl=true\&useLegacyDatetimeCode=false\&serverTimezone=GMT`
    * **SQL Server:** `jdbc:sqlserver://<database server name>:1433;database=<database name>;user=<admin name>;password=<admin password>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;`

7. V Azure Portal přejděte na App Service a Najděte stránku**Obecná nastavení** **Konfigurace** > . Do pole **spouštěcí skript** nastavte název a umístění spouštěcího skriptu, například */Home/Startup.sh*.

Při příštím restartování App Service spustí spouštěcí skript a provede kroky potřebné ke konfiguraci. K otestování, jestli tato konfigurace proběhne správně, můžete ke svému App Service přistupovat pomocí SSH a potom spustit spouštěcí skript sami z bash výzvy. Můžete také prostudovat protokoly App Service. Další informace o těchto možnostech najdete v tématu [protokolování a ladění aplikací](#logging-and-debugging-apps).

V dalším kroku budete muset aktualizovat konfiguraci WildFly pro vaši aplikaci a znovu ji nasadit. Použijte k tomu následující postup:

1. Otevřete soubor *Src/Main/Resources/META-INF/Persistence. XML* pro vaši aplikaci a `<jta-data-source>` vyhledejte element. Nahraďte jeho obsah, jak je znázorněno zde:

    **PostgreSQL**

    ```xml
    <jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
    ```

    **MySQL**

    ```xml
    <jta-data-source>java:jboss/datasources/mysqlDS</jta-data-source>
    ```

    **SQL Server**

    ```xml
    <jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
    ```

2. Znovu sestavte a znovu nasaďte aplikaci pomocí následujícího příkazu na příkazovém řádku bash:

    ```bash
    mvn package -DskipTests azure-webapp:deploy
    ```

3. Restartujte instanci App Service tak, že stisknete tlačítko **restartovat** v části **Přehled** Azure Portal nebo pomocí Azure CLI.

Vaše instance App Service je teď nakonfigurovaná pro přístup k databázi.

Další informace o konfiguraci připojení databáze pomocí WildFly najdete v tématu [PostgreSQL](https://developer.jboss.org/blogs/amartin-blog/2012/02/08/how-to-set-up-a-postgresql-jdbc-driver-on-jboss-7), [MySQL](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#Using_other_Databases-Using_MySQL_as_the_Default_DataSource)nebo [SQL Server](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#d0e3898).

### <a name="enable-messaging-providers"></a>Povolit poskytovatele zasílání zpráv

Postup povolení fazolových bobů v rámci zprávy pomocí Service Bus jako mechanismu zasílání zpráv:

1. Použijte [knihovnu pro zasílání zpráv Apache QPID JMS](https://qpid.apache.org/proton/index.html). Zahrňte tuto závislost do souboru pom. XML (nebo jiného souboru sestavení) pro aplikaci.

2. Vytvoření [prostředků Service Bus](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp). Vytvoří obor názvů Azure Service Bus a zařadí do fronty v rámci tohoto oboru názvů a zásadu sdíleného přístupu s funkcemi pro odesílání a příjem.

3. Předejte klíč zásad sdíleného přístupu do kódu buď pomocí adresy URL, a to tak, že zadáte primární klíč zásady, nebo [použijete sadu Service Bus SDK](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp#setup-jndi-context-and-configure-the-connectionfactory).

4. Postupujte podle kroků uvedených v části instalace modulů a závislostí s popisovačem XML modulu, závislostmi jar, příkazy rozhraní příkazového řádku JBoss a spouštěcím skriptem pro poskytovatele JMS. Kromě těchto čtyř souborů budete také muset vytvořit soubor XML, který definuje název JNDI pro frontu JMS a téma. Podívejte se na [Toto úložiště](https://github.com/JasonFreeberg/widlfly-server-configs/tree/master/appconfig) pro referenční konfigurační soubory.

## <a name="use-redis-as-a-session-cache-with-tomcat"></a>Použití Redis jako mezipaměti relace s Tomcat

Tomcat můžete nakonfigurovat tak, aby používala externí úložiště relací, jako je například [Azure cache pro Redis](/azure/azure-cache-for-redis/). To umožňuje zachovat stav uživatelské relace (například data nákupního košíku), když se uživatel přenese do jiné instance aplikace, například když dojde k automatickému škálování, restartování nebo převzetí služeb při selhání.

Pokud chcete používat Tomcat s Redis, musíte aplikaci nakonfigurovat tak, aby používala implementaci [PersistentManager](http://tomcat.apache.org/tomcat-8.5-doc/config/manager.html) . Následující kroky vysvětlují tento proces pomocí [správce relací Pivot: Redis-Store](https://github.com/pivotalsoftware/session-managers/tree/master/redis-store) jako příklad.

1. Otevřete terminál bash a použijte `export <variable>=<value>` k nastavení všech těchto proměnných prostředí.

    | Proměnná                 | Value                                                                      |
    |--------------------------|----------------------------------------------------------------------------|
    | RESOURCEGROUP_NAME       | Název skupiny prostředků, která obsahuje vaši instanci App Service.       |
    | WEBAPP_NAME              | Název vaší App Service instance.                                     |
    | WEBAPP_PLAN_NAME         | Název vašeho plánu App Service                                          |
    | OBLAST                   | Název oblasti, ve které je vaše aplikace hostovaná.                           |
    | REDIS_CACHE_NAME         | Název vaší instance Azure cache pro instanci Redis                           |
    | REDIS_PORT               | Port SSL, na kterém Redis Cache naslouchá.                             |
    | REDIS_PASSWORD           | Primární přístupový klíč vaší instance.                                  |
    | REDIS_SESSION_KEY_PREFIX | Hodnota, kterou určíte k identifikaci klíčů relací, které pocházejí z vaší aplikace. |

    Informace o názvu, portu a přístupu k informacím o Azure Portal najdete v částech **vlastnosti** nebo **přístupové klíče** vaší instance služby.

2. Vytvořte nebo aktualizujte soubor *Src/Main/WebApp/META-INF* vaší aplikace s následujícím obsahem:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <Context path="">
        <!-- Specify Redis Store -->
        <Valve className="com.gopivotal.manager.SessionFlushValve" />
        <Manager className="org.apache.catalina.session.PersistentManager">
            <Store className="com.gopivotal.manager.redis.RedisStore"
                   connectionPoolSize="20"
                   host="${REDIS_CACHE_NAME}.redis.cache.windows.net"
                   port="${REDIS_PORT}"
                   password="${REDIS_PASSWORD}"
                   sessionKeyPrefix="${REDIS_SESSION_KEY_PREFIX}"
                   timeout="2000"
            />
        </Manager>
    </Context>
    ```

    Tento soubor určuje a nakonfiguruje implementaci správce relací pro vaši aplikaci. Používá proměnné prostředí, které jste nastavili v předchozím kroku, abyste zachovali informace o svém účtu ze zdrojových souborů.

3. Pomocí FTP nahrajte soubor JAR správce relací do instance App Service a umístěte ho do adresáře */Home/Tomcat/lib* . Další informace najdete v tématu [nasazení aplikace pro Azure App Service pomocí FTP/S](https://docs.microsoft.com/azure/app-service/deploy-ftp).

4. Zakažte [soubor cookie spřažení relace](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) pro vaši instanci App Service. To můžete provést z Azure Portal tak, že přejdete do aplikace a pak nakonfigurujete nastavení **konfigurace > obecná nastavení > spřažení ARR** na **off**. Alternativně můžete použít následující příkaz:

    ```azurecli
    az webapp update -g <resource group> -n <webapp name> --client-affinity-enabled false
    ```

    Ve výchozím nastavení App Service použijí soubory cookie spřažení relace, aby bylo zajištěno, že požadavky klientů se stávajícími relacemi budou směrovány do stejné instance aplikace. Toto výchozí chování nevyžaduje žádnou konfiguraci, ale nemůže zachovat stav uživatelské relace, když se vaše instance aplikace restartuje nebo když se provoz přesměruje na jinou instanci. Když [zakážete existující konfiguraci spřažení instance ARR](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) pro vypnutí směrování založeného na souborech cookie relace, umožníte, aby nakonfigurované úložiště relace fungovalo bez rušivého vlivu.

5. Přejděte do části **vlastnosti** instance App Service a najděte **Další odchozí IP adresy**. Tyto možnosti označují všechny možné odchozí IP adresy pro vaši aplikaci. Zkopírujte je pro použití v dalším kroku.

6. Pro každou IP adresu vytvořte v mezipaměti Azure pro instanci Redis pravidlo brány firewall. To můžete provést na Azure Portal z části **Brána firewall** vaší instance Redis. Zadejte jedinečný název pro každé pravidlo a nastavte **Počáteční IP adresu** a hodnoty KONCOVých **IP** adres na stejnou IP adresu.

7. Přejděte do části **Rozšířená nastavení** instance Redis a nastavte možnost **povoluje přístup pouze přes protokol SSL** . Tím umožníte, aby vaše instance App Service komunikovala s mezipamětí Redis prostřednictvím infrastruktury Azure.

8. Aktualizujte konfiguraci v souboru *pom. XML* vaší aplikace tak, aby odkazovala na informace o účtu Redis. `azure-webapp-maven-plugin` Tento soubor používá proměnné prostředí, které jste předtím nastavili, aby byly informace o svém účtu ze zdrojových souborů.

    V případě potřeby přejděte `1.7.0` na aktuální verzi [modulu plug-in Maven pro Azure App Service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme).

    ```xml
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.7.0</version>
        <configuration>

            <!-- Web App information -->
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appServicePlanName>${WEBAPP_PLAN_NAME}-${REGION}</appServicePlanName>
            <appName>${WEBAPP_NAME}-${REGION}</appName>
            <region>${REGION}</region>
            <linuxRuntime>tomcat 9.0-jre8</linuxRuntime>

            <appSettings>
                <property>
                    <name>REDIS_CACHE_NAME</name>
                    <value>${REDIS_CACHE_NAME}</value>
                </property>
                <property>
                    <name>REDIS_PORT</name>
                    <value>${REDIS_PORT}</value>
                </property>
                <property>
                    <name>REDIS_PASSWORD</name>
                    <value>${REDIS_PASSWORD}</value>
                </property>
                <property>
                    <name>REDIS_SESSION_KEY_PREFIX</name>
                    <value>${REDIS_SESSION_KEY_PREFIX}</value>
                </property>
                <property>
                    <name>JAVA_OPTS</name>
                    <value>-Xms2048m -Xmx2048m -DREDIS_CACHE_NAME=${REDIS_CACHE_NAME} -DREDIS_PORT=${REDIS_PORT} -DREDIS_PASSWORD=${REDIS_PASSWORD} IS_SESSION_KEY_PREFIX=${REDIS_SESSION_KEY_PREFIX}</value>
                </property>

            </appSettings>

        </configuration>
    </plugin>
    ```

9. Znovu sestavte a nasaďte aplikaci.

    ```bash
    mvn package
    mvn azure-webapp:deploy
    ```

Vaše aplikace teď bude používat Redis Cache pro správu relací.

Ukázku, kterou můžete použít k otestování těchto pokynů, najdete v tématu [škálování-Stateful-Java-Web-App-on-Azure](https://github.com/Azure-Samples/scaling-stateful-java-web-app-on-azure) úložiště na GitHubu.

## <a name="docker-containers"></a>Kontejnery Dockeru

Pokud chcete ve svých kontejnerech používat Zulu JDK s podporou Azure, nezapomeňte si vyžádat a použít předem připravené image, jak je uvedeno na [stránce pro stažení podporované Azul Zulu Enterprise for Azure](https://www.azul.com/downloads/azure-only/zulu/) , nebo `Dockerfile` použijte příklady z [úložiště Microsoft Java GitHub. ](https://github.com/Microsoft/java/tree/master/docker).

## <a name="statement-of-support"></a>Prohlášení o podpoře

### <a name="runtime-availability"></a>Běhová dostupnost

App Service pro Linux podporuje dva moduly runtime pro spravované hostování webových aplikací Java:

- [Kontejner servlet Tomcat](https://tomcat.apache.org/) pro spuštěné aplikace zabalené jako soubory webového archivu (War). Podporované verze jsou 8,5 a 9,0.
- Běhové prostředí Java SE spouští pro aplikace zabalené jako soubory v archivu Java (JAR). Podporované verze jsou Java 8 a 11.

### <a name="jdk-versions-and-maintenance"></a>Verze a údržba JDK

Azul Zulu Enterprise Builds of OpenJDK jsou špičkovou distribucí do produkčního prostředí OpenJDK pro Azure a Azure Stack zajištěné systémy Microsoftu a Azul. Obsahují všechny komponenty pro vytváření a spouštění aplikací Java SE. JDK můžete nainstalovat z [instalace Java JDK](https://aka.ms/azure-jdks).

Podporované sady JDK se na čtvrtletní bázi automaticky opravují v lednu, dubnu, červenci a říjnu každého roku.

### <a name="security-updates"></a>Aktualizace zabezpečení

Opravy a opravy pro hlavní slabá místa zabezpečení budou vydány, jakmile budou dostupné ze systémů Azul. "Hlavní" ohrožení zabezpečení je definováno základním skóre 9,0 nebo vyšším v [systému NIST Common zranitelnost Standard, verze 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Vyřazení a vyřazení z provozu

Pokud bude vyřazení podporované běhové prostředí Java, budou se vývojáři Azure, kteří používají modul runtime, předávat oznámení o zastaralosti alespoň šest měsíců před vyřazením modulu runtime.

## <a name="next-steps"></a>Další postup

Na webu [Azure pro vývojáře v jazyce Java](/java/azure/) najdete informace o rychlých startech, kurzech a referenční dokumentaci Java pro Azure.

Obecné otázky týkající se použití App Service pro Linux, které nejsou specifické pro vývoj v jazyce Java, jsou zodpovězeny v tématu [Nejčastější dotazy k App Service Linux](app-service-linux-faq.md).
