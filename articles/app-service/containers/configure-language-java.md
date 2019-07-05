---
title: Konfigurace aplikací v Javě v Linuxu – Azure App Service | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat aplikace v Javě spuštěné v Azure App Service v Linuxu.
keywords: služby Azure app service, web Apps, linux, oss, java, platformě java EE určený dží, javaee
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
ms.openlocfilehash: 51ca597208b582e95fd305886dcf163744825eee
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509642"
---
# <a name="configure-a-linux-java-app-for-azure-app-service"></a>Konfigurace aplikace v Javě v Linuxu pro Azure App Service

Azure App Service v Linuxu umožňuje vývojářům rychle sestavit, nasadit a škálovat své Tomcat v jazyce Java nebo Java Standard Edition (SE) zabalené webové aplikace na plně spravované služby založené na Linuxu. Nasazujte aplikace s moduly plug-in Maven z příkazového řádku nebo v editoru, jako je IntelliJ, Eclipse nebo Visual Studio Code.

Tato příručka obsahuje klíčové koncepty a pokyny pro vývojáře v Javě, které používají integrované kontejneru Linuxu ve službě App Service. Pokud jste nikdy použili službu Azure App Service, postupujte podle [Java quickstart](quickstart-java.md) a [Java s PostgreSQL kurzu](tutorial-java-enterprise-postgresql-app.md) první.

## <a name="deploying-your-app"></a>Nasazení vaší aplikace

Můžete použít [modul plug-in Maven pro Azure App Service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) nasadit soubory .jar a .war. Nasazení s oblíbenými prostředími IDE také podporuje [sady Azure Toolkit pro IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij) nebo [sady Azure Toolkit pro Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse).

V opačném případě metodě nasazení bude záviset na vašich typ archivu:

- Chcete-li nasadit .war soubory na Tomcat, použijte `/api/wardeploy/` koncový bod účtovat souboru archivu. Další informace o tomto rozhraní API najdete v tématu [této dokumentace](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file).
- K nasazení souborů .jar v Java SE Image, použijte `/api/zipdeploy/` koncový bod webu kudu. Další informace o tomto rozhraní API najdete v tématu [této dokumentace](https://docs.microsoft.com/azure/app-service/deploy-zip#rest).

Nenasazujte .war nebo .jar pomocí FTP. Nástroje serveru FTP slouží k nahrávání spouštěcí skripty, závislosti nebo jiné soubory modulu runtime. To není ideální volbou pro nasazování webových aplikací.

## <a name="logging-and-debugging-apps"></a>Protokolování a ladění aplikací

Sestavy o výkonu, provoz vizualizace a kontroly stavu jsou k dispozici pro každou aplikaci na webu Azure portal. Další informace najdete v tématu [Přehled diagnostiky služby Azure App Service](../overview-diagnostics.md).

### <a name="ssh-console-access"></a>Přístup ke konzole SSH

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>Streamování diagnostických protokolů

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

Další informace najdete v tématu [streamování protokolů pomocí Azure CLI](../troubleshoot-diagnostic-logs.md#streaming-with-azure-cli).

### <a name="app-logging"></a>Protokolování aplikace

Povolit [protokolování aplikací](../troubleshoot-diagnostic-logs.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#enablediag) prostřednictvím webu Azure portal nebo [rozhraní příkazového řádku Azure](/cli/azure/webapp/log#az-webapp-log-config) ke konfiguraci služby App Service, zaznamená se do místní výstup standardní konzole vaší aplikace a datových proudů chyba standardní konzole systém souborů nebo úložiště objektů Blob v Azure. Instance je zakázaná 12 hodin po dokončení konfigurace protokolování do místního systému souborů služby App Service. Pokud budete potřebovat delší dobu uchování, nakonfigurujte aplikaci zapisovat výstup do kontejneru úložiště objektů Blob. Protokoly aplikací Java a Tomcat najdete v */home/LogFiles/aplikace/* adresáře.

Pokud vaše aplikace používá [Logback](https://logback.qos.ch/) nebo [Log4j](https://logging.apache.org/log4j) pro trasování, můžete předat dál trasování ke kontrole do služby Azure Application Insights pomocí pokyny ke konfiguraci protokolování framework v [Protokoly trasování prozkoumejte Java ve službě Application Insights](/azure/application-insights/app-insights-java-trace-logs).

### <a name="troubleshooting-tools"></a>Řešení potíží s nástroji

Integrované Image Java jsou založeny na [Alpine Linuxu](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) operačního systému. Použití `apk` Správce balíčků pro instalaci řešení potíží nástroje nebo příkazy.

### <a name="flight-recorder"></a>Nahrávání letu

Všechny Image Linuxu, Javě ve službě App Service mají nahrávání letu Zulu nainstalované, takže můžete snadno připojit k JVM a spustit profiler záznam nebo vygenerovat výpis haldy paměti.

#### <a name="timed-recording"></a>Vypršel časový limit nahrávání

K získání začít, připojte se přes SSH služby App Service a spustit `jcmd` příkazu zobrazte seznam všechny spuštěné procesy Java. Kromě jcmd samotného měli byste vidět aplikace Java spuštěná s číslem ID procesu (pid).

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

Spusťte následující příkaz Spustit záznam 30 sekundách JVM. Tím profil JVM a vytvořte soubor JFR *jfr_example.jfr* v domovském adresáři. (Nahradit 116 pid aplikace v jazyce Java.)

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

Během 30 druhý interval může ověřit záznam probíhat spuštěním `jcmd 116 JFR.check`. Tím se zobrazí všechny záznamy pro daný proces Javy.

#### <a name="continuous-recording"></a>Průběžné záznam

Nahrávání letu Zulu můžete průběžně profilu aplikace v jazyce Java s minimálním dopadem na výkon modulu runtime ([zdroj](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf)). Uděláte to tak, spusťte následující příkaz rozhraní příkazového řádku Azure k vytvoření nastavení aplikace s názvem JAVA_OPTS s nezbytné konfigurace. Obsah JAVA_OPTS nastavení aplikace, které jsou předány `java` příkaz při spuštění aplikace.

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

Další informace najdete v tématu [Jcmd informace o příkazech](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190).

### <a name="analyzing-recordings"></a>Analýza záznamy

Použití [FTPS](../deploy-ftp.md) JFR soubor stáhnout do svého místního počítače. Pokud chcete analyzovat soubor JFR, stáhněte a nainstalujte [Zulu střediskem](https://www.azul.com/products/zulu-mission-control/). Řídicí středisko Zulu, v tématu [Azul dokumentaci](https://docs.azul.com/zmc/) a [pokyny k instalaci](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control).

## <a name="customization-and-tuning"></a>Přizpůsobení a optimalizace

Azure App Service pro Linux podporuje pole vyladění a přizpůsobení prostřednictvím webu Azure portal a rozhraní příkazového řádku. Projděte si následující články konfigurace Javy konkrétní webové aplikace:

- [Konfigurace nastavení aplikace](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)
- [Nastavení vlastní domény](../app-service-web-tutorial-custom-domain.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Povolení protokolu SSL](../app-service-web-tutorial-custom-ssl.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Přidání sítě CDN](../../cdn/cdn-add-to-web-app.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Konfigurace webu kudu](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>Nastavte možnosti modulu runtime Java

Pokud chcete nastavit přidělené paměti nebo jiné možnosti modulu runtime JVM do Tomcat a Java SE prostředí, vytvořte [nastavení aplikace, které](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) s názvem `JAVA_OPTS` s možnostmi. App Service Linux předá toto nastavení jako proměnnou prostředí Java runtime při spuštění.

Na webu Azure Portal v části **nastavení aplikace** pro webovou aplikaci, vytvořte nové nastavení aplikace s názvem `JAVA_OPTS` , který obsahuje další nastavení, jako například `-Xms512m -Xmx1204m`.

Pokud chcete nakonfigurovat nastavení aplikace, které z modulu plug-in Maven, add v sekci modul plug-in Azure nastavení/hodnota značek. Následující příklad nastaví konkrétní minimální a maximální velikost haldy Java:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

S jednou aplikací jeden nasazovací slot v jejich plán služby App Service mohou vývojáři tyto možnosti:

- Instance B1 a S1: `-Xms1024m -Xmx1024m`
- Instance B2 a S2: `-Xms3072m -Xmx3072m`
- Instance B3 a S3: `-Xms6144m -Xmx6144m`

Při nastavení haldy ladění aplikace, projděte si podrobnosti vašeho plánu služby App Service a vezměte v úvahu více aplikací a slot nasazení je potřeba najít optimální přidělení paměti.

Pokud nasazujete aplikaci JAR, by měly být pojmenovány *app.jar* tak, aby integrované image můžete správně identifikují vaši aplikaci. (Modul plug-in Maven nemá tomto přejmenování automaticky.) Pokud nechcete, aby přejmenovat váš soubor JAR pro *app.jar*, můžete nahrát skript prostředí pomocí příkazu Spustit váš soubor JAR. Vložte úplnou cestu pro tento skript [spouštěcí soubor](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-faq#startup-file) textového pole v části konfigurace na portálu.

### <a name="turn-on-web-sockets"></a>Zapnout webové sokety

Zapněte podporu pro webové sokety na webu Azure Portal v **nastavení aplikace** pro aplikaci. Budete muset restartovat aplikaci pro nastavení projevilo.

Zapnutí podpory webových soketů pomocí Azure CLI pomocí následujícího příkazu:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --web-sockets-enabled true
```

Potom restartujte aplikaci:

```azurecli-interactive
az webapp stop --name <app-name> --resource-group <resource-group-name>
az webapp start --name <app-name> --resource-group <resource-group-name>
```

### <a name="set-default-character-encoding"></a>Nastavit výchozí kódování znaků

Na webu Azure Portal v části **nastavení aplikace** pro webovou aplikaci, vytvořte nové nastavení aplikace s názvem `JAVA_OPTS` s hodnotou `-Dfile.encoding=UTF-8`.

Alternativně můžete nakonfigurovat nastavení aplikace pomocí modulu plug-in App Service Maven. Přidáte nastavení názvu a hodnoty značek v konfiguraci modulu plug-in:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

### <a name="adjust-startup-timeout"></a>Nastavení časového limitu pro spuštění

Pokud vaše aplikace v Javě je zejména velkých, měli byste zvýšit časový limit spuštění. Vytvořit nastavení aplikace, `WEBSITES_CONTAINER_START_TIME_LIMIT` a nastavte ho na počet sekund, po které služby App Service má čekat před vypršením časového limitu. Maximální hodnota je `1800` sekund.

## <a name="secure-applications"></a>Zabezpečení aplikací

Aplikace Java spuštěná ve službě App Service pro Linux mají stejnou sadu [osvědčené postupy zabezpečení](/azure/security/security-paas-applications-using-app-services) jako ostatní aplikace.

### <a name="authenticate-users"></a>Ověřování uživatelů

Nastavení ověřování aplikace na webu Azure Portal s **ověřování a autorizace** možnost. Odtud můžete povolit ověřování pomocí Azure Active Directory nebo přihlašování přes sociální sítě jako je Facebook, Google nebo GitHub. Konfigurace Azure portal funguje pouze v případě konfigurace ověřování jednotným poskytovatele. Další informace najdete v tématu [konfigurace aplikace App Service pro použití přihlášení Azure Active Directory](../configure-authentication-provider-aad.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) a související články pro jiné zprostředkovatele identity. Pokud je potřeba povolit více poskytovatelů přihlásit, postupujte podle pokynů [přizpůsobit ověřování pomocí služby App Service](../app-service-authentication-how-to.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) článku.

#### <a name="tomcat"></a>Tomcat

Tomcat aplikaci může přístup k danému uživateli deklarace identity přímo z servlet Tomcat přetypováním objektu zabezpečení objektu do objektu Map. Objekt Map se namapuje každý typ deklarace identity ke kolekci pro daný typ deklarace identity. V níže uvedený kód `request` je instance `HttpServletRequest`.

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

Teď si můžete prohlédnout `Map` objekt pro jakékoli konkrétní deklarace identity. Například následující fragment kódu Iteruje přes všechny typy deklarací identity a vytiskne obsah každé z kolekcí.

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

Odhlásit uživatele a provádět další akce, naleznete v dokumentaci na [použití ověřování pomocí služby App Service a autorizaci](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to). K dispozici je také oficiální dokumentaci na Tomcat [HttpServletRequest rozhraní](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) a její metody. Následující servlet, které metody jsou také HYDRATOVANÝ podle vaší konfigurace služby App Service:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

Chcete-li tuto funkci zakázat, vytvořte nastavení aplikace s názvem `WEBSITE_AUTH_SKIP_PRINCIPAL` s hodnotou `1`. Pokud chcete zakázat všechny filtry servlet přidané ve službě App Service, vytvořte nastavení s názvem `WEBSITE_SKIP_FILTERS` s hodnotou `1`.

#### <a name="spring-boot"></a>Spring Boot

Aplikace Spring Boot vývojáři mohou použít [Azure Active Directory Spring Boot starter](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) k zabezpečení aplikace pomocí známých poznámky k zabezpečení Spring a rozhraní API. Je potřeba ke zvětšení maximální záhlaví ve vaší *souboru application.properties* souboru. Doporučujeme, aby hodnota `16384`.

### <a name="configure-tlsssl"></a>Konfigurace TLS/SSL

Postupujte podle pokynů [vytvoření vazby existujícího vlastního certifikátu SSL](../app-service-web-tutorial-custom-ssl.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) nahrát stávající certifikát SSL a navázat jej na název domény vaší aplikace. Ve výchozím nastavení vaše aplikace stále umožňuje HTTP připojení – postupujte podle konkrétních kroků v kurzu k vynucení šifrování protokolu SSL a TLS.

### <a name="use-keyvault-references"></a>Odkazy na použití trezoru klíčů

[Azure KeyVault](../../key-vault/key-vault-overview.md) nabízí centralizovanou správu tajných kódů pomocí historie zásad a auditování přístupu. Můžete ukládat tajné klíče (např. hesla nebo připojovací řetězce) v Key Vaultu a přístup k těchto tajných kódů v aplikaci prostřednictvím proměnných prostředí.

Nejprve, postupujte podle pokynů pro [uděluje přístup k vaší aplikaci do služby Key Vault](../app-service-key-vault-references.md#granting-your-app-access-to-key-vault) a [odkazu služby KeyVault pro váš tajný klíč v nastavení aplikace](../app-service-key-vault-references.md#reference-syntax). Můžete ověřit, že odkaz přeloží na tajný kód s tiskem proměnnou prostředí při vzdáleném přístupu k Terminálové službě App Service.

Vložení těchto tajných kódů v konfiguračním souboru Spring nebo Tomcat, použijte syntaxi vkládání proměnné prostředí (`${MY_ENV_VAR}`). Spring konfigurační soubory, najdete v tématu této dokumentace na [externalized konfigurace](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).

## <a name="configure-apm-platforms"></a>Konfigurovat platformy APM

Tato část ukazuje, jak připojit aplikace v Javě nasazené ve službě Azure App Service v Linuxu pomocí nástrojů NewRelic a AppDynamics pro monitorování výkonu aplikací (APM) platformy.

[Nakonfigurujte nástroj společnosti New Relic](#configure-new-relic)
[nakonfigurovat AppDynamics](#configure-appdynamics)

### <a name="configure-new-relic"></a>Nakonfigurujte nástroj společnosti New Relic

1. Vytvořit účet NewRelic na [NewRelic.com](https://newrelic.com/signup)
2. Stáhněte agenta Java z nástrojů NewRelic, bude mít název souboru, který je podobný *newrelic-java-x.x.x.zip*.
3. Zkopírování klíče licence, ho budete potřebovat ke konfiguraci agenta později.
4. [Připojte se přes SSH vaší instance služby App Service](app-service-linux-ssh-support.md) a vytvořte nový adresář */home/site/wwwroot/apm*.
5. Rozbalené soubory agenta NewRelic Java nahrát do adresáře v rámci */home/site/wwwroot/apm*. Soubory pro agenta by měla být v */home/site/wwwroot/apm/newrelic*.
6. Upravte soubor YAML na */home/site/wwwroot/apm/newrelic/newrelic.yml* a nahraďte hodnotu zástupného symbolu licence vlastní licenční klíč.
7. Na webu Azure Portal přejděte do vaší aplikace ve službě App Service a vytvořit nové nastavení aplikace.
    - Pokud vaše aplikace používá **Java SE**, vytvořte proměnnou prostředí s názvem `JAVA_OPTS` s hodnotou `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Pokud používáte **Tomcat**, vytvořte proměnnou prostředí s názvem `CATALINA_OPTS` s hodnotou `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Pokud používáte **WildFly**, naleznete v dokumentaci k špičkové funkce New Relicu [tady](https://docs.newrelic.com/docs/agents/java-agent/additional-installation/wildfly-version-11-installation-java) pokyny týkající se instalace agenta Java a JBoss konfigurace.
    - Pokud už máte proměnnou prostředí pro `JAVA_OPTS` nebo `CATALINA_OPTS`, připojte `javaagent` možnost na konec aktuální hodnotu.

### <a name="configure-appdynamics"></a>Konfigurace AppDynamics

1. Vytvoření účtu AppDynamics na [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Stáhněte agenta Java z webu AppDynamics, názvem souboru bude podobný *AppServerAgent x.x.x.xxxxx.zip*
3. [Připojte se přes SSH vaší instance služby App Service](app-service-linux-ssh-support.md) a vytvořte nový adresář */home/site/wwwroot/apm*.
4. Nahrát soubory agenta Java do adresáře v rámci */home/site/wwwroot/apm*. Soubory pro agenta by měla být v */home/site/wwwroot/apm/appdynamics*.
5. Na webu Azure Portal přejděte do vaší aplikace ve službě App Service a vytvořit nové nastavení aplikace.
    - Pokud používáte **Java SE**, vytvořte proměnnou prostředí s názvem `JAVA_OPTS` s hodnotou `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` kde `<app-name>` je název vaší služby App Service.
    - Pokud používáte **Tomcat**, vytvořte proměnnou prostředí s názvem `CATALINA_OPTS` s hodnotou `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` kde `<app-name>` je název vaší služby App Service.
    - Pokud používáte **WildFly**, naleznete v dokumentaci k AppDynamics [tady](https://docs.appdynamics.com/display/PRO45/JBoss+and+Wildfly+Startup+Settings) pokyny týkající se instalace agenta Java a JBoss konfigurace.

## <a name="configure-jar-applications"></a>Konfigurovat soubor JAR aplikace

### <a name="starting-jar-apps"></a>Spouští se soubor JAR aplikace

Ve výchozím nastavení, služby App Service očekává, že soubor JAR aplikaci s názvem *app.jar*. Pokud má tento název, se automaticky spustí. Pro uživatele Maven, můžete nastavit název JAR zahrnutím `<finalName>app</finalName>` v `<build>` část vaší *pom.xml*. [Můžete provést totéž v Gradle](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.bundling.Jar.html#org.gradle.api.tasks.bundling.Jar:archiveFileName) nastavením `archiveFileName` vlastnost.

Pokud chcete použít jiný název pro váš soubor JAR, je rovněž nutné poskytnout [spouštěcí příkaz](app-service-linux-faq.md#built-in-images) , který se spustí váš soubor JAR. Například, `java -jar my-jar-app.jar`. Můžete nastavit hodnotu pro spuštění příkazu na portálu v části Konfigurace > Obecné nastavení, nebo pomocí nastavení aplikace s názvem `STARTUP_COMMAND`.

### <a name="server-port"></a>Port serveru

App Service Linux směruje příchozí žádosti na port 80, takže vaše aplikace naslouchat požadavkům na portu 80 a. Můžete to provést v konfiguraci vaší aplikace (jako je například pro Spring *souboru application.properties* souboru), nebo v po spuštění příkazu (například `java -jar spring-app.jar --server.port=80`). Podrobnosti najdete v následující dokumentaci pro běžné rozhraní Java:

- [Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-properties-and-configuration.html#howto-use-short-command-line-arguments)
- [SparkJava](http://sparkjava.com/documentation#embedded-web-server)
- [Micronaut](https://docs.micronaut.io/latest/guide/index.html#runningSpecificPort)
- [Přehrát Framework](https://www.playframework.com/documentation/2.6.x/ConfiguringHttps#Configuring-HTTPS)
- [Vertx](https://vertx.io/docs/vertx-core/java/#_start_the_server_listening)
- [Quarkus](https://quarkus.io/guides/application-configuration-guide)

## <a name="data-sources"></a>Zdroje dat

### <a name="tomcat"></a>Tomcat

Tyto pokyny platí pro všechna připojení k databázi. Je potřeba vyplnit zástupné symboly s názvem třídy ovladač vybrané databáze a soubor JAR. K dispozici je také tabulka s názvy tříd a soubory ke stažení ovladače pro běžné databáze.

| Databáze   | Název třídy ovladače                             | Ovladač JDBC                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Stáhnout](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Stáhněte si](https://dev.mysql.com/downloads/connector/j/) (vyberte "Nezávislé na platformě") |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Stáhnout](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#available-downloads-of-jdbc-driver-for-sql-server)                                                           |

Pokud chcete nakonfigurovat Tomcat používat připojení k databázi Java (JDBC) nebo rozhraní API trvalost Java (JPA), nejprve přizpůsobit `CATALINA_OPTS` proměnnou prostředí, která přečte v Tomcatu při spuštění. Nastavte tyto hodnoty pomocí nastavení aplikace v [modulu plug-in App Service Maven](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Nebo nastavte proměnné prostředí **konfigurace** > **nastavení aplikace** stránky na webu Azure Portal.

Dále určete, pokud zdroj dat by měla být k dispozici pro jednu aplikaci nebo pro všechny aplikace spuštěné na Tomcat servlet.

#### <a name="application-level-data-sources"></a>Zdroje dat na úrovni aplikace

1. Vytvoření *context.xml* soubor *META-INF /* adresáře vašeho projektu. Vytvořte *META-INF /* adresáře, pokud neexistuje.

2. V *context.xml*, přidejte `Context` prvek, který chcete propojit zdroje dat na adresu JNDI. Nahradit `driverClassName` zástupný symbol s názvem třídy ve vašem ovladači z výše uvedené tabulky.

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

3. Aktualizace vaší aplikace *soubor web.xml* budou používat zdroj dat ve vaší aplikaci.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>Sdílené prostředky na úrovni serveru

1. Zkopírujte obsah */usr/local/tomcat/conf* do */home/tomcat/conf* ve vaší App Service pro Linux instance pomocí protokolu SSH, pokud konfigurace existuje ještě nemáte.

    ```bash
    mkdir -p /home/tomcat
    cp -a /usr/local/tomcat/conf /home/tomcat/conf
    ```

2. Přidat element kontextu v vaše *server.xml* v rámci `<Server>` elementu.

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

3. Aktualizace vaší aplikace *soubor web.xml* budou používat zdroj dat ve vaší aplikaci.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="finalize-configuration"></a>Dokončení konfigurace

A konečně umístěte JAR ovladač v cestě Tomcat a restartujte službu App Service.

1. Zajistěte, aby byly soubory ovladačů JDBC k dispozici pro Tomcat classloader tak, že je */home/tomcat/lib* adresáře. (Pokud ještě neexistuje, vytvořte tento adresář.) Pokud chcete nahrát tyto soubory k vaší instanci služby App Service, postupujte následovně:

    1. V [Cloud Shell](https://shell.azure.com), nainstalovat rozšíření webové aplikace:

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. Spusťte následující příkaz rozhraní příkazového řádku k vytvoření tunelu SSH z místního systému do služby App Service:

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. Připojení na místní port tunelového propojení s vašeho klienta protokolu SFTP a odesílání souborů do */home/tomcat/lib* složky.

    Alternativně můžete klienta FTP k nahrání ovladač JDBC. Postupujte podle těchto [pokyny k získání údajů FTP](../deploy-configure-credentials.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

2. Pokud jste nevytvořili zdroj dat na úrovni serveru, restartujte aplikaci App Service pro Linux. Tomcat resetuje `CATALINA_HOME` k `/home/tomcat/conf` a použít aktualizovanou konfiguraci.

### <a name="spring-boot"></a>Spring Boot

Pro připojení ke zdrojům dat aplikace Spring Boot, doporučujeme vytvoření připojovací řetězce a vkládá je do vašeho *souboru application.properties* souboru.

1. V oddílu "Konfigurace" stránka služby App Service nastavte název pro řetězec, vložte připojovací řetězec JDBC do pole hodnota a nastavit zadáním "Vlastní". Tento připojovací řetězec můžete volitelně nastavit jako nastavení slotu.

    Tento připojovací řetězec je přístupné pro naši aplikaci jako proměnné prostředí s názvem `CUSTOMCONNSTR_<your-string-name>`. Například připojovací řetězec, který jsme vytvořili výše, bude mít název `CUSTOMCONNSTR_exampledb`.

2. Ve vaší *souboru application.properties* souboru, odkazují na tento připojovací řetězec s názvem proměnné prostředí. V našem příkladu jsme byste použili následující.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

Podrobnosti najdete [Spring Boot dokumentaci o přístupu k datům](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html) a [externalized konfigurace](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html) Další informace o tomto tématu.

## <a name="configure-java-ee-wildfly"></a>Konfigurace jazyka Java EE (WildFly)

> [!NOTE]
> Platformu Java Enterprise Edition v App Service pro Linux je aktuálně ve verzi Preview. Tento stack je **není** doporučuje pro produkční přístupem k práci. informace o našich zásobníků Java SE a Tomcat.

Azure App Service v Linuxu umožňuje vývojářům vytvářet, nasazovat a škálovat aplikace Java Enterprise (Java EE) na plně spravované služby založené na Linuxu v jazyce Java.  Základní běhové prostředí Java Enterprise je open source [WildFly](https://wildfly.org/) aplikačního serveru.

Tato část obsahuje následující témata:

- [Škálování pomocí služby App Service](#scale-with-app-service)
- [Vlastní nastavení konfigurace aplikačního serveru](#customize-application-server-configuration)
- [Instalace modulů a závislostí](#install-modules-and-dependencies)
- [Konfigurace zdroje dat](#configure-data-sources)
- [Povolení zprostředkovatelé zasílání zpráv](#enable-messaging-providers)
- [Konfigurovat ukládání do mezipaměti relace správy](#configure-session-management-caching)

### <a name="scale-with-app-service"></a>Škálování pomocí služby App Service

WildFly aplikačního serveru ve službě App Service v Linuxu spuštěnou běží v samostatném režimu, není v konfiguraci domény. Při horizontálním navýšením kapacity plánu služby App Service, každá instance WildFly je nakonfigurovaná jako samostatný server.

Vertikálně nebo horizontálně škálovat aplikaci s [škálování pravidla](../../monitoring-and-diagnostics/monitoring-autoscale-get-started.md) a [zvýšit počet vašich instancí](../web-sites-scale.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

### <a name="customize-application-server-configuration"></a>Vlastní nastavení konfigurace aplikačního serveru

Instance webové aplikace jsou bezstavové, takže každá nová instance spustit musí být nakonfigurovaná na spuštění WildFly konfigurace vyžadované aplikací.
Můžete napsat po spuštění skriptu Bash pro volání rozhraní příkazového řádku WildFly pro:

- Nastavení zdroje dat
- Konfigurace zprostředkovatelů zasílání zpráv
- Přidejte do konfigurace serveru WildFly jiných modulů a závislosti.

Skript se spustí při WildFly je vytvořená a spuštěná, ale před spuštěním aplikace. Skript by měl používat [JBOSS CLI](https://docs.jboss.org/author/display/WFLY/Command+Line+Interface) volat z */opt/jboss/wildfly/bin/jboss-cli.sh* se konfigurace nebo po spuštění serveru potřebné změny konfigurace aplikačního serveru.

Nepoužívejte ke konfiguraci WildFly interaktivní režim rozhraní příkazového řádku. Místo toho můžete poskytnout skript příkazů rozhraní příkazového řádku JBoss pomocí `--file` příkazu, například:

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

Pomocí funkce FTP k nahrání spouštěcí skript do umístění ve vaší instanci služby App Service v rámci vaší */home* adresář, jako například */home/site/deployments/tools*. Další informace najdete v tématu [nasazení aplikace do Azure App Service pomocí FTP/S](https://docs.microsoft.com/azure/app-service/deploy-ftp).

Nastavte **spouštěcí skript** pole na webu Azure Portal do umístění při spuštění skriptu prostředí, například */home/site/deployments/tools/your-startup-script.sh*.

Zadat [nastavení aplikace](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) v konfiguraci aplikace a zajistěte tak předání proměnných prostředí pro použití skriptu. Nastavení aplikace udržovat připojovací řetězce a další tajné klíče potřebné ke konfiguraci vaší aplikace ze správy verzí.

### <a name="install-modules-and-dependencies"></a>Instalace modulů a závislostí

Instalace modulů a jejich závislosti do cesty pro třídy WildFly prostřednictvím rozhraní příkazového řádku JBoss, musíte vytvořit následující soubory v jejich vlastní adresáře. Některé moduly a závislosti může být nutné další konfigurace, jako je například JNDI pojmenování nebo jiná konfigurace specifické pro rozhraní API, takže tento seznam je minimální sada budete potřebovat ke konfiguraci závislostí ve většině případů.

- [Popisovač modulu XML](https://jboss-modules.github.io/jboss-modules/manual/#descriptors). Tento soubor XML definuje název, atributy a závislosti modulu. To [ukázkový soubor module.xml](https://access.redhat.com/documentation/en-us/jboss_enterprise_application_platform/6/html/administration_and_configuration_guide/example_postgresql_xa_datasource) definuje modulu Postgres, jeho závislost JDBC souboru JAR a další závislosti modulu, které vyžaduje.
- Všechny nezbytné JAR souboru závislosti pro modul.
- Skript se vaše příkazy rozhraní příkazového řádku JBoss konfigurace nového modulu. Tento soubor bude obsahovat vaše příkazy mají být provedeny pomocí rozhraní příkazového řádku JBoss a konfigurace serveru pro použití závislost. Dokumentace ke službě na příkazy pro přidání modulů, zdroje dat a zprostředkovatelé zasílání zpráv, najdete v tématu [tento dokument](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli).
- Bash spouštěcí skript pro volání rozhraní příkazového řádku JBoss a spusťte tento skript v předchozím kroku. Tento soubor se spustí při restartování vaší instance služby App Service, nebo když nových instancí se zřizují během Škálováním. Tento skript po spuštění je, kde můžete provádět další konfigurace pro vaši aplikaci předaly JBoss příkazy rozhraní příkazového řádku JBoss. Minimálně tento soubor může být jediným příkazem k předání vašeho skriptu příkazu rozhraní příkazového řádku JBoss JBoss rozhraní příkazového řádku:

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

Jakmile budete mít, soubory a obsah pro modul, postupujte podle kroků níže přidejte modul WildFly aplikačního serveru.

1. Pomocí funkce FTP k nahrání souborů do umístění ve vaší instanci služby App Service v rámci vaší */home* adresář, jako například */home/site/deployments/tools*. Další informace najdete v tématu [nasazení aplikace do Azure App Service pomocí FTP/S](../deploy-ftp.md).
2. V **konfigurace** > **obecné nastavení** stránky na webu Azure portal, nastavte **spouštěcí skript** pole do umístění vašeho prostředí spouštěcí skript pro Příklad */home/site/deployments/tools/startup.sh*.
3. Stisknutím kombinace kláves restartování vaší instance služby App Service **restartovat** tlačítko **přehled** oddílu na portálu nebo pomocí rozhraní příkazového řádku Azure.

### <a name="configure-data-sources"></a>Konfigurace zdroje dat

Ke konfiguraci WildFly/JBoss pro přístup ke zdroji dat, je použít obecný postup uvedených výše v části "Instalace modulů a závislosti". V následujících částech najdete konkrétní podrobnosti o tomto procesu pro datové zdroje pro PostgreSQL, MySQL a SQL Server.

V této části se předpokládá, že již máte aplikaci, instance služby App Service a instanci služby Azure Database. Podle následujících pokynů odkazovat na název vaší služby App Service, jeho skupina prostředků a vaše informace o připojení databáze. Můžete najít tyto informace na portálu Azure portal.

Pokud chcete absolvovat celý proces od začátku pomocí ukázkové aplikace, najdete v článku [kurzu: Vytvoření webové aplikace v Azure platformě Java EE určený a Postgres](tutorial-java-enterprise-postgresql-app.md).

Následující postup vysvětluje požadavky pro připojení stávající službu App Service a databázi.

1. Stáhnout ovladač JDBC pro [PostgreSQL](https://jdbc.postgresql.org/download.html), [MySQL](https://dev.mysql.com/downloads/connector/j/), nebo [systému SQL Server](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server). Rozbalte stažený archiv získat ovladač soubor .jar.

2. Vytvořte soubor s názvem, jako je *module.xml* a přidejte následující kód. Nahradit `<module name>` zástupný text (včetně ostrých závorek) `org.postgres` pro PostgreSQL, `com.mysql` pro MySQL, nebo `com.microsoft` pro SQL Server. Nahraďte `<JDBC .jar file path>` s názvem souboru .jar v předchozím kroku, včetně úplnou cestu k umístění umístíte ho ve vaší instanci služby App Service. To může být libovolného umístění v rámci */home* adresáře.

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

3. Vytvořte soubor s názvem, jako je *datasource commands.cli* a přidejte následující kód. Nahraďte `<JDBC .jar file path>` s hodnotou, které jste použili v předchozím kroku. Nahraďte `<module file path>` s názvem souboru a cestou služby App Service v předchozím kroku, například */home/module.xml*.

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

    Tento soubor je spuštěn při spuštění skriptu je popsáno v dalším kroku. Nainstaluje ovladač JDBC jako modul WildFly, vytvoří odpovídající zdroj dat WildFly a znovu načte serveru a ujistěte se, že změny se projeví.

4. Vytvořte soubor s názvem, jako je *startup.sh* a přidejte následující kód. Nahraďte `<JBoss CLI script>` s názvem souboru, který jste vytvořili v předchozím kroku. Nezapomeňte zahrnout úplnou cestu k umístění umístíte soubor v instanci služby App Service, třeba */home/datasource-commands.cli*.

    ```bash
    #!/usr/bin/env bash
    /opt/jboss/wildfly/bin/jboss-cli.sh -c --file=<JBoss CLI script>
    ```

5. Nahrát soubor .jar s JDBC, soubor XML modulu, skript rozhraní příkazového řádku JBoss a spouštěcí skript k vaší instanci služby App Service pomocí FTP. Tyto soubory do umístění, které jste zadali v předchozím postupu, jako například */home*. Další informace o protokolu FTP, naleznete v tématu [nasazení aplikace do Azure App Service pomocí FTP/S](https://docs.microsoft.com/azure/app-service/deploy-ftp).

6. Chcete-li přidat nastavení do služby App Service, která obsahovat vaše informace o připojení databáze pomocí Azure CLI. Nahraďte `<resource group>` a `<webapp name>` hodnotami, které používá služby App Service. Nahraďte `<database server name>`, `<database name>`, `<admin name>`, a `<admin password>` se vaše informace o připojení databáze. Informace o vaší služby App Service a databáze můžete získat z webu Azure portal.

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

    DATABASE_CONNECTION_URL hodnoty se liší pro každý server databáze a jiné než hodnoty na webu Azure portal. Jsou požadovány pro použití podle WildFly formátech adres URL je znázorněno zde (a fragmenty kódu výše):

    * **PostgreSQL:** `jdbc:postgresql://<database server name>:5432/<database name>?ssl=true`
    * **MySQL:** `jdbc:mysql://<database server name>:3306/<database name>?ssl=true\&useLegacyDatetimeCode=false\&serverTimezone=GMT`
    * **SQL Server:** `jdbc:sqlserver://<database server name>:1433;database=<database name>;user=<admin name>;password=<admin password>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;`

7. Na webu Azure Portal, přejděte do služby App Service a najděte **konfigurace** > **obecné nastavení** stránky. Nastavte **spouštěcí skript** pole pro název a umístění spuštění skriptu, například */home/startup.sh*.

Při příštím restartování služby App Service, bude se spouštěcí skript spouštěl a proveďte kroky nezbytné konfigurace. K otestování, zda tato konfigurace vyskytuje správně, můžete přístup k vaší službě App Service pomocí SSH a pak se spouštěcí skript spouštěl sami z příkazového řádku prostředí Bash. Můžete také zkontrolovat protokoly služby App Service. Další informace o těchto možnostech najdete v tématu [protokolování a ladění aplikací](#logging-and-debugging-apps).

V dalším kroku je potřeba aktualizovat konfiguraci WildFly pro vaši aplikaci a znovu ji nasadíte. Použijte k tomu následující postup:

1. Otevřít *src/main/resources/META-INF/persistence.xml* souboru pro vaši aplikaci a najít `<jta-data-source>` elementu. Nahraďte jeho obsah, jak je znázorněno zde:

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

2. Znovu sestavte a opětovně Nasaďte aplikaci pomocí následujícího příkazu na příkazovém řádku Bash:

    ```bash
    mvn package -DskipTests azure-webapp:deploy
    ```

3. Stisknutím klávesy restartování vaší instance služby App Service **restartovat** tlačítko **přehled** části webu Azure Portal nebo pomocí rozhraní příkazového řádku Azure.

Instance vaší služby App Service je nyní nakonfigurováno pro přístup k databázi.

Další informace o tom, jak nakonfigurovat připojení k databázi s WildFly, naleznete v tématu [PostgreSQL](https://developer.jboss.org/blogs/amartin-blog/2012/02/08/how-to-set-up-a-postgresql-jdbc-driver-on-jboss-7), [MySQL](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#Using_other_Databases-Using_MySQL_as_the_Default_DataSource), nebo [systému SQL Server](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#d0e3898).

### <a name="enable-messaging-providers"></a>Povolení zprostředkovatelé zasílání zpráv

Pokud chcete povolit zpráva řízené lusků používat jako mechanismus zasílání zpráv Service Bus:

1. Použití [knihovna zpráv Apache QPId JMS](https://qpid.apache.org/proton/index.html). Patří této závislosti ve vašich pom.xml (nebo jiný soubor sestavení) pro aplikaci.

2. Vytvoření [prostředky služby Service Bus](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp). Vytvoření oboru názvů služby Azure Service Bus a fronty v rámci daného oboru názvů a zásady sdíleného přístupu s odesílání a přijímání možnosti.

3. Předat klíč zásad sdíleného přístupu ke kódu, buď pomocí kódování URL primární klíč zásady nebo [pomocí sady SDK služby Service Bus](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp#setup-jndi-context-and-configure-the-connectionfactory).

4. Postupujte podle kroků uvedených v části Instalace modulů a závislosti s modul XML popisovač, .jar závislosti, příkazů rozhraní příkazového řádku JBoss a spouštěcí skript pro JMS zprostředkovatele. Kromě čtyři soubory je také potřeba vytvořit soubor XML, který definuje název JNDI pro JMS frontu a téma. Zobrazit [toto úložiště](https://github.com/JasonFreeberg/widlfly-server-configs/tree/master/appconfig) pro odkaz na konfigurační soubory.

### <a name="configure-session-management-caching"></a>Konfigurovat ukládání do mezipaměti relace správy

App Service v Linuxu ve výchozím nastavení bude používat soubory cookie spřažení relace k zajištění, že požadavky klientů s existující relací jsou směrovány stejnou instanci vaší aplikace. Toto výchozí chování není nutné nic konfigurovat, ale má určitá omezení:

- Pokud instance aplikace je restartován nebo kapacitu vertikálně snížit, stav relace uživatele na serveru aplikace se ztratí.
- Pokud aplikace nastavení časového limitu relace dlouhé nebo pevný počet uživatelů, může trvat nějakou dobu došlo ke ztrátě schopnosti nové instance pro příjem zatížení, protože pouze nové relace se budou směrovat na nově spuštěná instance.

Můžete nakonfigurovat WildFly použít jako úložiště externí relace [mezipaměti Azure Redis](/azure/azure-cache-for-redis/). Budete muset [zakázat existující Instance spřažení směrování žádostí na aplikace](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) konfigurace vypnout relace na základě souboru cookie směrování a povolit nakonfigurované úložiště relace WildFly pracovat i bez rušení.

## <a name="docker-containers"></a>Kontejnery Dockeru

Pokud chcete použít sadu JDK Zulu podporovaných systémem Azure v kontejnerech, ujistěte se, že k vyžádání a použijte předem sestavené Image, jak je uvedeno v [podporované Azul Zulu Enterprise pro stránku pro stažení Azure](https://www.azul.com/downloads/azure-only/zulu/) nebo použijte `Dockerfile` příklady z [Úložiště Microsoft Java GitHub](https://github.com/Microsoft/java/tree/master/docker).

## <a name="statement-of-support"></a>Prohlášení k podpoře

### <a name="runtime-availability"></a>Dostupnost modulu runtime

App Service pro Linux podporuje dva moduly runtime pro spravované hostování webových aplikací v Javě:

- [Servletový kontejner Tomcat](https://tomcat.apache.org/) ke spuštění zabalené aplikace jako webové souborech archivu (WAR). Podporované verze jsou 8.5 a 9.0.
- Běhové prostředí Java SE pro provoz aplikací s podobu Java archivní soubory (JAR). Podporovány jsou verze Javy 8 a 11.

### <a name="jdk-versions-and-maintenance"></a>JDK verze a údržba

Azul Zulu Enterprise sestavení OpenJDK jsou kombinace OpenJDK pro Azure a Azure Stackem zajišťovanou Microsoftem a Azul Systems bezplatného, multiplatformní a připravené pro produkční prostředí. Obsahují všechny komponenty pro vytváření a spouštění aplikací Java SE. Můžete nainstalovat sadu JDK od [Java JDK instalace](https://aka.ms/azure-jdks).

Podporované JDK jsou automaticky opravit čtvrtletně v lednu, dubnu, červenci a říjnu každý rok.

### <a name="security-updates"></a>Aktualizace zabezpečení

Opravy chyb a opravy pro chyby zabezpečení hlavní vydá Jakmile budou k dispozici od Azul Systems. "Hlavní" ohrožení zabezpečení je definována základní skóre 9.0 nebo vyšší na [NIST běžné ohrožení zabezpečení hodnocení systému, verze 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Vyřazení a vyřazení z provozu

Pokud podporovaného modulu runtime Java se vyřadí z provozu, vývojáře v Azure pomocí ovlivněné runtime dostanou oznámení o zastarání aspoň šest měsíců před modul runtime byl vyřazen z provozu.

## <a name="next-steps"></a>Další postup

Přejděte [Azure pro vývojáře v Javě](/java/azure/) centra a najít rychlí průvodci Azure, kurzy a referenční dokumentace jazyka Java.

Obecné dotazy ohledně používání služby App Service pro Linux, které nejsou specifické pro Java development v odpovědi [nejčastější dotazy týkající se App Service Linux](app-service-linux-faq.md).
