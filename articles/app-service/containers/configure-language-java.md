---
title: Konfigurace aplikací pro Linux v jazyce Java
description: Přečtěte si, jak nakonfigurovat předem sestavený kontejner Java pro vaši aplikaci. Tento článek ukazuje nejběžnější konfigurační úlohy.
keywords: Azure App Service, Web App, Linux, OSS, Java, Java EE, JEE, JavaEE
author: bmitchell287
manager: barbkess
ms.devlang: java
ms.topic: article
ms.date: 11/22/2019
ms.author: brendm
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: 496f519ba5e4eb17060ee35ed86fba45c85336d6
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2020
ms.locfileid: "84905727"
---
# <a name="configure-a-linux-java-app-for-azure-app-service"></a>Konfigurace linuxové aplikace v jazyce Java pro Azure App Service

Azure App Service v systému Linux umožňuje vývojářům Java rychle sestavovat, nasazovat a škálovat jejich Tomcat nebo zabalené webové aplikace standardu Java Standard (SE) na plně spravovanou službu se systémem Linux. Nasaďte aplikace pomocí modulů plug-in Maven z příkazového řádku nebo v editorech, jako je IntelliJ, zatmění nebo Visual Studio Code.

Tato příručka poskytuje klíčové koncepty a pokyny pro vývojáře v jazyce Java, kteří používají integrovaný kontejner Linux v nástroji App Service. Pokud jste Azure App Service nikdy nepoužili, postupujte podle pokynů v [rychlém startu Java](quickstart-java.md).

## <a name="deploying-your-app"></a>Nasazení aplikace

K nasazení souborů. jar i. War můžete použít [modul plug-in Maven pro Azure App Service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) . Nasazení s oblíbenými mikroprostředími se podporuje taky [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/) nebo [Azure Toolkit for Eclipse](/azure/developer/java/toolkit-for-eclipse).

V opačném případě vaše metoda nasazení bude záviset na typu archivu:

- K nasazení souborů. War do Tomcat použijte `/api/wardeploy/` koncový bod k odeslání souboru archivu. Další informace o tomto rozhraní API najdete v [této dokumentaci](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file).
- Chcete-li nasadit soubory. jar na obrázcích Java SE systémem, použijte `/api/zipdeploy/` koncový bod webu Kudu. Další informace o tomto rozhraní API najdete v [této dokumentaci](https://docs.microsoft.com/azure/app-service/deploy-zip#rest).

Nesaďte své. War nebo. jar pomocí FTP. Nástroj FTP je určen pro nahrávání spouštěcích skriptů, závislostí nebo jiných souborů modulu runtime. Nejedná se o optimální volbu pro nasazování webových aplikací.

## <a name="logging-and-debugging-apps"></a>Protokolování a ladění aplikací

Sestavy výkonu, vizualizace provozu a checkups stavu jsou k dispozici pro každou aplikaci prostřednictvím Azure Portal. Další informace najdete v tématu [Přehled diagnostiky Azure App Service](../overview-diagnostics.md).

### <a name="ssh-console-access"></a>Přístup ke konzole SSH

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>Streamování diagnostických protokolů

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-linux-no-h.md)]

Další informace najdete v tématu [protokoly streamování v Cloud Shell](../troubleshoot-diagnostic-logs.md#in-cloud-shell).

### <a name="app-logging"></a>Protokolování aplikace

Povolte [protokolování aplikací](../troubleshoot-diagnostic-logs.md?toc=/azure/app-service/containers/toc.json#enable-application-logging-windows) prostřednictvím Azure Portal nebo pomocí [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) a nakonfigurujte App Service tak, aby se do místního systému souborů nebo do služby Azure Blob Storage napsaly standardní a standardní chybové proudy konzolové aplikace. Protokolování do místní instance systému souborů App Service je po konfiguraci zakázáno 12 hodin. Pokud budete potřebovat delší dobu uchování, nakonfigurujte aplikaci tak, aby zapisovala výstup do kontejneru úložiště objektů BLOB. Protokoly aplikací Java a Tomcat najdete v adresáři */Home/LogFiles/Application/* .

>[!NOTE]
>Protokolování do místního systému souborů App Service se stane neaktivním až 12 hodin se týká pouze App Services založených na systému Windows. Protokolování Azure Blob Storage pro App Services na bázi Linux se dá nakonfigurovat jenom pomocí [Azure monitor (Preview)](/azure/app-service/troubleshoot-diagnostic-logs#send-logs-to-azure-monitor-preview) . 

Pokud vaše aplikace používá pro trasování [Logback](https://logback.qos.ch/) nebo [log4j](https://logging.apache.org/log4j) , můžete tyto trasování přepošlete pro účely revize do Azure Application Insights pomocí pokynů pro konfiguraci protokolovacího rozhraní v tématu [prozkoumat protokoly trasování Java v Application Insights](/azure/application-insights/app-insights-java-trace-logs).

### <a name="troubleshooting-tools"></a>Nástroje pro řešení potíží

Integrované image Java jsou založené na operačním systému [Alpine Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) . `apk`K instalaci všech nástrojů nebo příkazů pro řešení potíží použijte Správce balíčků.

### <a name="flight-recorder"></a>Záznam letu

Všechny image pro Linux v jazyce Java v App Service mají nainstalovaný Zuluový záznam o letu, abyste se mohli snadno připojit k JVM a spustit záznam profileru nebo vygenerovat výpis haldy.

#### <a name="timed-recording"></a>Časový záznam vypršel

Chcete-li začít, spusťte SSH do svého App Service a spusťte `jcmd` příkaz, který zobrazí seznam všech spuštěných procesů Java. Kromě samotné jcmd by se měla zobrazit vaše aplikace Java spuštěná s IDENTIFIKAČNÍm číslem procesu (PID).

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

Během intervalu 30 sekund můžete ověřit, že záznam probíhá spuštěním `jcmd 116 JFR.check` . Zobrazí se všechny nahrávky pro daný proces Java.

#### <a name="continuous-recording"></a>Průběžný záznam

Pomocí Zulu letu můžete průběžně profilovat svoji aplikaci Java s minimálním dopadem na výkon modulu runtime ([zdroj](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf)). Uděláte to tak, že spustíte následující příkaz Azure CLI, který vytvoří nastavení aplikace s názvem JAVA_OPTS s nezbytnou konfigurací. Obsah nastavení aplikace JAVA_OPTS se před `java` zahájením aplikace předává do příkazu.

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

Po zahájení nahrávání můžete data o nahrávání kdykoli vypsat pomocí `JFR.dump` příkazu.

```shell
jcmd <pid> JFR.dump name=continuous_recording filename="/home/recording1.jfr"
```

Další informace najdete v referenčních informacích k [příkazům Jcmd](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190).

### <a name="analyzing-recordings"></a>Analýza záznamů

Použijte [FTPS](../deploy-ftp.md) ke stažení souboru jfr do místního počítače. Chcete-li analyzovat soubor JFR, Stáhněte a nainstalujte [řízení Zulu](https://www.azul.com/products/zulu-mission-control/). Pokyny k řízení Zulu najdete v [dokumentaci k Azul](https://docs.azul.com/zmc/) a v [pokynech k instalaci](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control).

## <a name="customization-and-tuning"></a>Přizpůsobení a ladění

Azure App Service pro Linux podporuje vyladění a přizpůsobení prostřednictvím Azure Portal a CLI. Projděte si následující články s konfigurací webové aplikace specifické pro jazyk Java:

- [Konfigurace nastavení aplikace](../configure-common.md?toc=/azure/app-service/containers/toc.json#configure-app-settings)
- [Nastavení vlastní domény](../app-service-web-tutorial-custom-domain.md?toc=/azure/app-service/containers/toc.json)
- [Konfigurace vazeb SSL](../configure-ssl-bindings.md?toc=/azure/app-service/containers/toc.json)
- [Přidat síť CDN](../../cdn/cdn-add-to-web-app.md?toc=/azure/app-service/containers/toc.json)
- [Konfigurace webu Kudu](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>Nastavení možností modulu Java Runtime

Pokud chcete nastavit přidělenou paměť nebo jiné možnosti JVM runtime v prostředích Tomcat a Java SE systémem, vytvořte [nastavení aplikace](../configure-common.md?toc=/azure/app-service/containers/toc.json#configure-app-settings) s názvem `JAVA_OPTS` s možnostmi. App Service Linux předá toto nastavení jako proměnnou prostředí modulu runtime Java při spuštění.

V Azure Portal v části **nastavení aplikace** pro webovou aplikaci vytvořte nové nastavení aplikace s názvem `JAVA_OPTS` , které obsahuje další nastavení, například `-Xms512m -Xmx1204m` .

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

Pokud nasazujete aplikaci JAR, měla by být pojmenována *App. jar* , aby předdefinovaná image mohla správně identifikovat vaši aplikaci. (Modul plug-in Maven Toto přejmenování provede automaticky.) Pokud si svůj JAR nepřejete přejmenovat na *App. jar*, můžete nahrát skript prostředí pomocí příkazu, abyste mohli svůj jar spustit. Pak úplnou cestu k tomuto skriptu vložte do textového pole [Spouštěcí soubor](app-service-linux-faq.md#built-in-images) v části Konfigurace na portálu. Spouštěcí skript se nespouští z adresáře, do kterého je umístěn. Proto ve spouštěcím skriptu vždy používejte absolutní cesty k referenčním souborům (například: `java -jar /home/myapp/myapp.jar`).

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

V Azure Portal v části **nastavení aplikace** pro webovou aplikaci vytvořte nové nastavení aplikace s názvem `JAVA_OPTS` s hodnotou `-Dfile.encoding=UTF-8` .

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

Pokud je vaše aplikace Java zvláště velká, měli byste zvýšit časový limit spuštění. Chcete-li to provést, vytvořte nastavení aplikace `WEBSITES_CONTAINER_START_TIME_LIMIT` a nastavte ji na počet sekund, po které App Service čekat před vypršením časového limitu. Maximální hodnota je `1800` sekund.

### <a name="pre-compile-jsp-files"></a>Předem kompilovat soubory JSP

Chcete-li zlepšit výkon aplikací Tomcat, můžete před nasazením do App Service zkompilovat své soubory JSP. Můžete použít [modul plug-in Maven](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) , který poskytuje Apache Sling, nebo použít tento [soubor sestavení ANT](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation).

## <a name="secure-applications"></a>Zabezpečené aplikace

Aplikace Java běžící v App Service pro Linux mají stejnou sadu [osvědčených postupů zabezpečení](/azure/security/security-paas-applications-using-app-services) jako u jiných aplikací.

### <a name="authenticate-users-easy-auth"></a>Ověřování uživatelů (snadné ověřování)

Pomocí možnosti **ověřování a autorizace** nastavte ověřování aplikací v Azure Portal. Odtud můžete povolit ověřování pomocí Azure Active Directory nebo přes sociální přihlášení, jako je Facebook, Google nebo GitHub. Konfigurace Azure Portal funguje pouze při konfiguraci jednoho poskytovatele ověřování. Další informace najdete v tématu [Konfigurace aplikace App Service pro použití Azure Active Directory přihlášení](../configure-authentication-provider-aad.md?toc=/azure/app-service/containers/toc.json) a souvisejících článků pro jiné poskytovatele identity. Pokud potřebujete povolit více poskytovatelů přihlašování, postupujte podle pokynů v článku [přizpůsobení App Serviceho ověřování](../app-service-authentication-how-to.md?toc=/azure/app-service/containers/toc.json) .

#### <a name="tomcat"></a>Tomcat

Vaše aplikace Tomcat může získat přístup k deklaracím uživatele přímo z servlet přetypování objektu zabezpečení na objekt mapy. Objekt mapy bude mapovat jednotlivé typy deklarací na kolekci deklarací pro daný typ. V následujícím kódu `request` je instance `HttpServletRequest` .

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

Chcete-li tuto funkci zakázat, vytvořte nastavení aplikace s názvem `WEBSITE_AUTH_SKIP_PRINCIPAL` s hodnotou `1` . Chcete-li zakázat všechny filtry servlet přidané App Service, vytvořte nastavení s názvem `WEBSITE_SKIP_FILTERS` s hodnotou `1` .

#### <a name="spring-boot"></a>Spring Boot

Vývojáři pružinového spouštění můžou pomocí [Azure Active Directory pružiny Boot Starter](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) zabezpečit aplikace pomocí známých poznámek a rozhraní API pro zabezpečení pružiny. Nezapomeňte zvětšit maximální velikost hlavičky v souboru *Application. Properties* . Navrhneme hodnotu `16384` .

### <a name="configure-tlsssl"></a>Konfigurace TLS/SSL

Podle pokynů v části [zabezpečení vlastního názvu DNS s vazbou SSL v Azure App Service](../configure-ssl-bindings.md?toc=/azure/app-service/containers/toc.json) Nahrajte stávající certifikát SSL a vytvořte jeho vazbu s názvem domény vaší aplikace. Ve výchozím nastavení bude vaše aplikace pořád umožňovat připojení HTTP – postupujte podle kroků v tomto kurzu, abyste vynutili SSL a TLS.

### <a name="use-keyvault-references"></a>Použití odkazů na Trezor klíčů

[Azure webtrezor](../../key-vault/general/overview.md) poskytuje centralizovanou správu tajných kódů pomocí zásad přístupu a historie auditu. Do trezoru klíčů můžete ukládat tajné kódy (například hesla nebo připojovací řetězce) a přistupovat k nim ve vaší aplikaci pomocí proměnných prostředí.

Nejdřív postupujte podle pokynů pro [udělení přístupu aplikace Key Vault](../app-service-key-vault-references.md#granting-your-app-access-to-key-vault) a [Vytvoření odkazu trezoru klíčů pro váš tajný klíč v nastavení aplikace](../app-service-key-vault-references.md#reference-syntax). Můžete ověřit, že se odkaz na tajný kód přeloží vytištěním proměnné prostředí a vzdáleným přístupem k App Service terminálu.

Pro vložení těchto tajných kódů do konfiguračního souboru jarní nebo Tomcat použijte syntaxi injektáže proměnné prostředí ( `${MY_ENV_VAR}` ). V případě konfiguračních souborů pružiny se podívejte na tuto dokumentaci k [externě nakonfigurovaným konfiguracím](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).

### <a name="using-the-java-key-store"></a>Používání úložiště klíčů Java

Ve výchozím nastavení se všechny veřejné nebo privátní certifikáty [nahrané do App Service Linux](../configure-ssl-certificate.md) načtou do příslušných úložišť klíčů Java jako kontejner se spustí. Po nahrání certifikátu bude potřeba restartovat App Service, aby se načetla do úložiště klíčů Java. Veřejné certifikáty jsou načteny do úložiště klíčů na portálu `$JAVA_HOME/jre/lib/security/cacerts` a soukromé certifikáty jsou uloženy v `$JAVA_HOME/lib/security/client.jks` .

Pro šifrování připojení JDBC k certifikátům v úložišti klíčů Java může být potřeba další konfigurace. Další informace najdete v dokumentaci pro zvolený ovladač JDBC.

- [PostgreSQL](https://jdbc.postgresql.org/documentation/head/ssl-client.html)
- [SQL Server](https://docs.microsoft.com/sql/connect/jdbc/connecting-with-ssl-encryption?view=sql-server-ver15)
- [MySQL](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-reference-using-ssl.html)
- [MongoDB](https://mongodb.github.io/mongo-java-driver/3.4/driver/tutorials/ssl/)
- [Cassandra](https://docs.datastax.com/en/developer/java-driver/4.3/)

#### <a name="initializing-the-java-key-store"></a>Inicializuje se úložiště klíčů Java.

Chcete-li inicializovat `import java.security.KeyStore` objekt, načtěte soubor úložiště klíčů pomocí hesla. Výchozí heslo pro obě úložiště klíčů je "changeit".

```java
KeyStore keyStore = KeyStore.getInstance("jks");
keyStore.load(
    new FileInputStream(System.getenv("JAVA_HOME")+"/lib/security/cacets"),
    "changeit".toCharArray());

KeyStore keyStore = KeyStore.getInstance("pkcs12");
keyStore.load(
    new FileInputStream(System.getenv("JAVA_HOME")+"/lib/security/client.jks"),
    "changeit".toCharArray());
```

#### <a name="manually-load-the-key-store"></a>Ruční načtení úložiště klíčů

Certifikáty můžete načíst ručně do úložiště klíčů. Vytvořte nastavení aplikace `SKIP_JAVA_KEYSTORE_LOAD` s hodnotou, `1` která zakáže App Service načtení certifikátů do úložiště klíčů automaticky. Všechny veřejné certifikáty nahrané do App Service přes Azure Portal jsou uložené v části `/var/ssl/certs/` . Privátní certifikáty jsou uloženy v části `/var/ssl/private/` .

Nástroj Java Key můžete interagovat nebo ladit [otevřením připojení SSH](app-service-linux-ssh-support.md) k vašemu App Service a spuštěním příkazu `keytool` . Seznam příkazů najdete v [dokumentaci k nástroji Key](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) . Další informace o rozhraní API úložiště klíčů najdete [v oficiální dokumentaci](https://docs.oracle.com/javase/8/docs/api/java/security/KeyStore.html).

## <a name="configure-apm-platforms"></a>Konfigurace platforem APM

V této části se dozvíte, jak připojit aplikace Java nasazené na Azure App Service v systému Linux pomocí platforem APM (Application Performance Monitoring) pro NewRelic a AppDynamics.

### <a name="configure-new-relic"></a>Konfigurovat nové Relic

1. Vytvoření účtu NewRelic na [NewRelic.com](https://newrelic.com/signup)
2. Stáhněte si agenta Java ze NewRelic, bude mít název souboru podobný *newrelic-java-x.x.x.zip*.
3. Zkopírujte licenční klíč, budete ho potřebovat ke konfiguraci agenta později.
4. [Do své instance App Service](app-service-linux-ssh-support.md) a vytvořte nový adresář */Home/site/wwwroot/APM*.
5. Nahrajte nebalené soubory NewRelic Java do adresáře pod */Home/site/wwwroot/APM*. Soubory pro vašeho agenta by měly být v */Home/site/wwwroot/APM/NewRelic*.
6. Upravte soubor YAML na */Home/site/wwwroot/APM/NewRelic/NewRelic.yml* a nahraďte hodnotu licence zástupného symbolu vlastním licenčním klíčem.
7. V Azure Portal přejděte do aplikace v App Service a vytvořte nové nastavení aplikace.
    - Pokud vaše aplikace používá **Java se**, vytvořte proměnnou prostředí s názvem `JAVA_OPTS` s hodnotou `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .
    - Pokud používáte **Tomcat**, vytvořte proměnnou prostředí s názvem `CATALINA_OPTS` s hodnotou `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .

### <a name="configure-appdynamics"></a>Konfigurace AppDynamics

1. Vytvoření účtu AppDynamics na [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Stáhněte si agenta Java z webu AppDynamics, název souboru bude podobný *AppServerAgent-x.x.x.xxxxx.zip*
3. [Do své instance App Service](app-service-linux-ssh-support.md) a vytvořte nový adresář */Home/site/wwwroot/APM*.
4. Nahrajte soubory agenta Java do adresáře pod */Home/site/wwwroot/APM*. Soubory pro vašeho agenta by měly být v */Home/site/wwwroot/APM/AppDynamics*.
5. V Azure Portal přejděte do aplikace v App Service a vytvořte nové nastavení aplikace.
    - Pokud používáte **Java se**systémem, vytvořte proměnnou prostředí s názvem `JAVA_OPTS` s hodnotou `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` , kde `<app-name>` je váš App Service název.
    - Pokud používáte **Tomcat**, vytvořte proměnnou prostředí s názvem `CATALINA_OPTS` s hodnotou, `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` kde `<app-name>` je váš App Service název.

> [!NOTE]
> Pokud již máte proměnnou prostředí pro `JAVA_OPTS` nebo `CATALINA_OPTS` , přidejte `-javaagent:/...` možnost na konec aktuální hodnoty.

## <a name="configure-jar-applications"></a>Konfigurovat aplikace JAR

### <a name="starting-jar-apps"></a>Spouštění aplikací JAR

Ve výchozím nastavení App Service očekává, že vaše aplikace JAR bude pojmenována *App. jar*. Pokud tento název obsahuje, bude automaticky spuštěn. Pro uživatele Maven můžete název JAR nastavit tak, že zahrnete `<finalName>app</finalName>` do `<build>` části *pom.xml*. To [samé můžete provést v Gradle](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.bundling.Jar.html#org.gradle.api.tasks.bundling.Jar:archiveFileName) nastavením `archiveFileName` Vlastnosti.

Pokud chcete pro svůj JAR použít jiný název, musíte zadat také [spouštěcí příkaz](app-service-linux-faq.md#built-in-images) , který SPUSTÍ soubor JAR. Například, `java -jar my-jar-app.jar`. Hodnotu pro spouštěcí příkaz můžete nastavit na portálu v části Konfigurace > Obecné nastavení nebo pomocí nastavení aplikace s názvem `STARTUP_COMMAND` .

### <a name="server-port"></a>Port serveru

App Service Linux směruje příchozí požadavky na port 80, takže vaše aplikace by měla naslouchat i na portu 80. To můžete provést v konfiguraci aplikace (jako je například soubor *aplikace. Properties* v jaře) nebo v příkazu pro spuštění (například `java -jar spring-app.jar --server.port=80` ). Podívejte se prosím na následující dokumentaci pro běžné architektury Java:

- [Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-properties-and-configuration.html#howto-use-short-command-line-arguments)
- [SparkJava](http://sparkjava.com/documentation#embedded-web-server)
- [Micronaut](https://docs.micronaut.io/latest/guide/index.html#runningSpecificPort)
- [Rozhraní Play](https://www.playframework.com/documentation/2.6.x/ConfiguringHttps#Configuring-HTTPS)
- [Vertx](https://vertx.io/docs/vertx-core/java/#_start_the_server_listening)
- [Quarkus](https://quarkus.io/guides/application-configuration-guide)

## <a name="data-sources"></a>Zdroje dat

### <a name="tomcat"></a>Tomcat

Tyto pokyny platí pro všechna databázová připojení. Zástupné symboly budete muset vyplnit pomocí názvu třídy ovladače zvolené databáze a souboru JAR. Poskytuje se tabulka s názvy tříd a stahování ovladačů pro běžné databáze.

| databáze   | Název třídy ovladače                             | Ovladač JDBC                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Stáhnout](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Stáhnout](https://dev.mysql.com/downloads/connector/j/) (vyberte "nezávislé na platformě") |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Stáhnout](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#download)                                                           |

Chcete-li nakonfigurovat Tomcat pro použití JDBC (Java Database Connectivity) nebo rozhraní Java Persistence (JPA), nejprve upravte `CATALINA_OPTS` proměnnou prostředí, která je čtena nástrojem Tomcat při spuštění. Nastavte tyto hodnoty pomocí nastavení aplikace v [modulu plug-in App Service Maven](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Nebo nastavte proměnné prostředí na stránce nastavení **konfigurační**  >  **aplikace** v Azure Portal.

Dále určete, zda má být zdroj dat k dispozici pro jednu aplikaci nebo pro všechny aplikace běžící na Tomcat servlet.

#### <a name="application-level-data-sources"></a>Zdroje dat na úrovni aplikace

1. Vytvořte soubor *context.xml* v adresáři *META-INF nebo* v adresáři vašeho projektu. Pokud neexistuje, vytvořte *soubor META-INF nebo* s adresářem.

2. V *context.xml*přidejte `Context` prvek pro propojení zdroje dat s adresou JNDI. `driverClassName`Zástupný symbol nahraďte názvem třídy vašeho ovladače z tabulky výše.

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

3. Aktualizujte *web.xml* vaší aplikace tak, aby používala zdroj dat ve vaší aplikaci.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>Sdílené prostředky na úrovni serveru

Přidání sdíleného zdroje dat na úrovni serveru bude vyžadovat, abyste upravili server.xml Tomcat. Nejdřív nahrajte [spouštěcí skript](app-service-linux-faq.md#built-in-images) a nastavte cestu ke skriptu v příkazu **Konfigurace**  >  **při spuštění**. Spouštěcí skript můžete nahrát pomocí [FTP](../deploy-ftp.md).

Spouštěcí skript vytvoří [transformaci XSL](https://www.w3schools.com/xml/xsl_intro.asp) do souboru server.xml a výstup výsledného souboru XML do `/usr/local/tomcat/conf/server.xml` . Spouštěcí skript by měl nainstalovat libxslt přes APK. Soubor XSL a spouštěcí skript je možné nahrávat prostřednictvím FTP. Níže je příklad spouštěcího skriptu.

```sh
# Install libxslt. Also copy the transform file to /home/tomcat/conf/
apk add --update libxslt

# Usage: xsltproc --output output.xml style.xsl input.xml
xsltproc --output /home/tomcat/conf/server.xml /home/tomcat/conf/transform.xsl /usr/local/tomcat/conf/server.xml
```

Příklad souboru XSL je uveden níže. Vzorový soubor XSL přidá nový uzel konektoru do Tomcat server.xml.

```xml
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
  <xsl:output method="xml" indent="yes"/>

  <xsl:template match="@* | node()" name="Copy">
    <xsl:copy>
      <xsl:apply-templates select="@* | node()"/>
    </xsl:copy>
  </xsl:template>

  <xsl:template match="@* | node()" mode="insertConnector">
    <xsl:call-template name="Copy" />
  </xsl:template>

  <xsl:template match="comment()[not(../Connector[@scheme = 'https']) and
                                 contains(., '&lt;Connector') and
                                 (contains(., 'scheme=&quot;https&quot;') or
                                  contains(., &quot;scheme='https'&quot;))]">
    <xsl:value-of select="." disable-output-escaping="yes" />
  </xsl:template>

  <xsl:template match="Service[not(Connector[@scheme = 'https'] or
                                   comment()[contains(., '&lt;Connector') and
                                             (contains(., 'scheme=&quot;https&quot;') or
                                              contains(., &quot;scheme='https'&quot;))]
                                  )]
                      ">
    <xsl:copy>
      <xsl:apply-templates select="@* | node()" mode="insertConnector" />
    </xsl:copy>
  </xsl:template>

  <!-- Add the new connector after the last existing Connnector if there is one -->
  <xsl:template match="Connector[last()]" mode="insertConnector">
    <xsl:call-template name="Copy" />

    <xsl:call-template name="AddConnector" />
  </xsl:template>

  <!-- ... or before the first Engine if there is no existing Connector -->
  <xsl:template match="Engine[1][not(preceding-sibling::Connector)]"
                mode="insertConnector">
    <xsl:call-template name="AddConnector" />

    <xsl:call-template name="Copy" />
  </xsl:template>

  <xsl:template name="AddConnector">
    <!-- Add new line -->
    <xsl:text>&#xa;</xsl:text>
    <!-- This is the new connector -->
    <Connector port="8443" protocol="HTTP/1.1" SSLEnabled="true" 
               maxThreads="150" scheme="https" secure="true" 
               keystroreFile="${{user.home}}/.keystore" keystorePass="changeit"
               clientAuth="false" sslProtocol="TLS" />
  </xsl:template>
  
</xsl:stylesheet>
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

    Alternativně můžete k nahrání ovladače JDBC použít klienta FTP. [Při získávání přihlašovacích údajů k FTP](../deploy-configure-credentials.md?toc=/azure/app-service/containers/toc.json)postupujte podle těchto pokynů.

2. Pokud jste vytvořili zdroj dat na úrovni serveru, restartujte aplikaci App Service Linux. Tomcat se obnoví `CATALINA_BASE` `/home/tomcat` a použije aktualizovanou konfiguraci.

### <a name="spring-boot"></a>Spring Boot

Pro připojení ke zdrojům dat v aplikacích pro pružinové spouštění doporučujeme vytvořit připojovací řetězce a vložit je do souboru *. Properties* .

1. V části "konfigurace" na stránce App Service nastavte název řetězce, vložte připojovací řetězec JDBC do pole hodnota a nastavte typ na Custom (vlastní). Volitelně můžete nastavit tento připojovací řetězec jako nastavení slotu.

    Tento připojovací řetězec je pro naši aplikaci přístupný jako proměnná prostředí s názvem `CUSTOMCONNSTR_<your-string-name>` . Například připojovací řetězec, který jsme vytvořili výše, bude pojmenován `CUSTOMCONNSTR_exampledb` .

2. V souboru *Application. Properties* , odkazujte na tento připojovací řetězec s názvem proměnné prostředí. V našem příkladu bychom použili následující.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

Další informace o tomto tématu najdete v [dokumentaci ke jarnímu spuštění pro přístup k datům a k](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html) [externě nakonfigurovaným konfiguracím](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html) .

## <a name="use-redis-as-a-session-cache-with-tomcat"></a>Použití Redis jako mezipaměti relace s Tomcat

Tomcat můžete nakonfigurovat tak, aby používala externí úložiště relací, jako je například [Azure cache pro Redis](/azure/azure-cache-for-redis/). To umožňuje zachovat stav uživatelské relace (například data nákupního košíku), když se uživatel přenese do jiné instance aplikace, například když dojde k automatickému škálování, restartování nebo převzetí služeb při selhání.

Pokud chcete používat Tomcat s Redis, musíte aplikaci nakonfigurovat tak, aby používala implementaci [PersistentManager](https://tomcat.apache.org/tomcat-8.5-doc/config/manager.html) . Následující kroky vysvětlují tento proces pomocí [správce relací Pivot: Redis-Store](https://github.com/pivotalsoftware/session-managers/tree/master/redis-store) jako příklad.

1. Otevřete terminál bash a použijte `<variable>=<value>` k nastavení všech těchto proměnných prostředí.

    | Proměnná                 | Hodnota                                                                      |
    |--------------------------|----------------------------------------------------------------------------|
    | RESOURCEGROUP_NAME       | Název skupiny prostředků, která obsahuje vaši instanci App Service.       |
    | WEBAPP_NAME              | Název vaší App Service instance.                                     |
    | WEBAPP_PLAN_NAME         | Název vašeho plánu App Service.                                         |
    | REGION                   | Název oblasti, ve které je vaše aplikace hostovaná.                           |
    | REDIS_CACHE_NAME         | Název vaší instance Azure cache pro instanci Redis                           |
    | REDIS_PORT               | Port SSL, na kterém Redis Cache naslouchá.                             |
    | REDIS_PASSWORD           | Primární přístupový klíč vaší instance.                                  |
    | REDIS_SESSION_KEY_PREFIX | Hodnota, kterou určíte k identifikaci klíčů relací, které pocházejí z vaší aplikace. |

    ```bash
    RESOURCEGROUP_NAME=<resource group>
    WEBAPP_NAME=<web app>
    WEBAPP_PLAN_NAME=<App Service plan>
    REGION=<region>
    REDIS_CACHE_NAME=<cache>
    REDIS_PORT=<port>
    REDIS_PASSWORD=<access key>
    REDIS_SESSION_KEY_PREFIX=<prefix>
    ```

    Informace o názvu, portu a přístupu k informacím o Azure Portal najdete v částech **vlastnosti** nebo **přístupové klíče** vaší instance služby.

2. Vytvořte nebo aktualizujte soubor *Src/Main/WebApp nebo meta-context.xmlINF* vaší aplikace následujícím obsahem:

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

4. Zakažte [soubor cookie spřažení relace](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) pro vaši instanci App Service. To můžete provést z Azure Portal tak, že přejdete do aplikace a pak **nakonfigurujete nastavení konfigurace > obecná nastavení > spřažení ARR** na **off**. Alternativně můžete použít následující příkaz:

    ```azurecli
    az webapp update -g <resource group> -n <webapp name> --client-affinity-enabled false
    ```

    Ve výchozím nastavení App Service použijí soubory cookie spřažení relace, aby bylo zajištěno, že požadavky klientů se stávajícími relacemi budou směrovány do stejné instance aplikace. Toto výchozí chování nevyžaduje žádnou konfiguraci, ale nemůže zachovat stav uživatelské relace, když se vaše instance aplikace restartuje nebo když se provoz přesměruje na jinou instanci. Když [zakážete existující konfiguraci spřažení instance ARR](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) pro vypnutí směrování založeného na souborech cookie relace, umožníte, aby nakonfigurované úložiště relace fungovalo bez rušivého vlivu.

5. Přejděte do části **vlastnosti** instance App Service a najděte **Další odchozí IP adresy**. Tyto možnosti označují všechny možné odchozí IP adresy pro vaši aplikaci. Zkopírujte je pro použití v dalším kroku.

6. Pro každou IP adresu vytvořte v mezipaměti Azure pro instanci Redis pravidlo brány firewall. To můžete provést na Azure Portal z části **Brána firewall** vaší instance Redis. Zadejte jedinečný název pro každé pravidlo a nastavte **Počáteční IP adresu** a hodnoty **koncových IP** adres na stejnou IP adresu.

7. Přejděte do části **Rozšířená nastavení** instance Redis a nastavte možnost **povoluje přístup pouze přes protokol SSL** **.** Tím umožníte, aby vaše instance App Service komunikovala s mezipamětí Redis prostřednictvím infrastruktury Azure.

8. Aktualizujte `azure-webapp-maven-plugin` konfiguraci v souboru *pom.xml* vaší aplikace tak, aby odkazovala na informace o účtu Redis. Tento soubor používá proměnné prostředí, které jste předtím nastavili, aby byly informace o svém účtu ze zdrojových souborů.

    V případě potřeby změňte `1.9.1` na aktuální verzi [modulu plug-in Maven pro Azure App Service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme).

    ```xml
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.9.1</version>
        <configuration>            
            <!-- Web App information -->
            <schemaVersion>v2</schemaVersion>
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appServicePlanName>${WEBAPP_PLAN_NAME}-${REGION}</appServicePlanName>
            <appName>${WEBAPP_NAME}-${REGION}</appName>
            <region>${REGION}</region>            
            <runtime>
                <os>linux</os>
                <javaVersion>jre8</javaVersion>
                <webContainer>tomcat 9.0</webContainer>
            </runtime>

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
    mvn package -DskipTests azure-webapp:deploy
    ```

Vaše aplikace teď bude používat Redis Cache pro správu relací.

Ukázku, kterou můžete použít k otestování těchto pokynů, najdete v tématu [škálování-Stateful-Java-Web-App-on-Azure](https://github.com/Azure-Samples/scaling-stateful-java-web-app-on-azure) úložiště na GitHubu.

## <a name="docker-containers"></a>Kontejnery Dockeru

Pokud chcete ve svých kontejnerech používat Zulu JDK s podporou Azure, ujistěte se, že jste si přečetli a používali předem připravené image, jak je popsáno na [stránce pro stažení podporované Azul Zulu Enterprise for Azure](https://www.azul.com/downloads/azure-only/zulu/) , nebo použijte `Dockerfile` Příklady z [úložiště Microsoft Java GitHub](https://github.com/Microsoft/java/tree/master/docker).

## <a name="statement-of-support"></a>Prohlášení o podpoře

### <a name="runtime-availability"></a>Běhová dostupnost

App Service pro Linux podporuje dva moduly runtime pro spravované hostování webových aplikací Java:

- [Kontejner servlet Tomcat](https://tomcat.apache.org/) pro spuštěné aplikace zabalené jako soubory webového archivu (War). Podporované verze jsou 8,5 a 9,0.
- Běhové prostředí Java SE spouští pro aplikace zabalené jako soubory v archivu Java (JAR). Podporované verze jsou Java 8 a 11.

### <a name="jdk-versions-and-maintenance"></a>Verze a údržba JDK

Azul Zulu Enterprise Builds of OpenJDK jsou špičkovou distribucí do produkčního prostředí OpenJDK pro Azure a Azure Stack zajištěné systémy Microsoftu a Azul. Obsahuje všechny komponenty pro vytváření a spouštění aplikací Java SE. JDK můžete nainstalovat z [instalace Java JDK](https://aka.ms/azure-jdks).

Podporované sady JDK se na čtvrtletní bázi automaticky opravují v lednu, dubnu, červenci a říjnu každého roku.

### <a name="security-updates"></a>Aktualizace zabezpečení

Opravy a opravy pro hlavní slabá místa zabezpečení budou vydány, jakmile budou dostupné ze systémů Azul. "Hlavní" ohrožení zabezpečení je definováno základním skóre 9,0 nebo vyšším v [systému NIST Common zranitelnost Standard, verze 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Vyřazení a vyřazení z provozu

Pokud bude vyřazení podporované běhové prostředí Java, budou se vývojáři Azure, kteří používají modul runtime, předávat oznámení o zastaralosti alespoň šest měsíců před vyřazením modulu runtime.

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Další kroky

Na webu [Azure pro vývojáře v jazyce Java](/java/azure/) najdete informace o rychlých startech, kurzech a referenční dokumentaci Java pro Azure.

Obecné otázky týkající se použití App Service pro Linux, které nejsou specifické pro vývoj v jazyce Java, jsou zodpovězeny v tématu [Nejčastější dotazy k App Service Linux](app-service-linux-faq.md).
