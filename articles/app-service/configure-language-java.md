---
title: Konfigurace aplikací Java
description: Naučte se konfigurovat aplikace Java, aby běžely na Azure App Service. Tento článek ukazuje nejběžnější konfigurační úlohy.
keywords: Azure App Service, Web App, Windows, OSS, Java, Tomcat, JBoss
author: jasonfreeberg
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18, devx-track-java, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 2e77d76ddae540a311655eca36c53b23c418f5e3
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92744141"
---
# <a name="configure-a-java-app-for-azure-app-service"></a>Konfigurace aplikace Java pro Azure App Service

Azure App Service umožňuje vývojářům Java rychle sestavovat, nasazovat a škálovat webové aplikace v jazyce Java SE, Tomcat a JBoss v plně spravované službě. Nasaďte aplikace pomocí modulů plug-in Maven, z příkazového řádku nebo v editorech, jako je IntelliJ, zatmění nebo Visual Studio Code.

Tato příručka poskytuje klíčové koncepty a pokyny pro vývojáře v jazyce Java, které používají App Service. Pokud jste nikdy Azure App Service nepoužili, měli byste si nejdřív projít modulem [rychlý Start Java](quickstart-java.md) . Obecné otázky týkající se použití App Service, které nejsou specifické pro vývoj v jazyce Java, jsou zodpovězeny v části [Nejčastější dotazy k App Service](faq-configuration-and-management.md).

## <a name="deploying-your-app"></a>Nasazení aplikace

K nasazení souborů. War nebo. jar můžete použít [modul plug-in webové aplikace Azure pro Maven](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) . Nasazení s oblíbenými mikroprostředími se podporuje taky [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/) nebo [Azure Toolkit for Eclipse](/azure/developer/java/toolkit-for-eclipse).

V opačném případě vaše metoda nasazení bude záviset na typu archivu:

### <a name="java-se"></a>Java SE

Pokud chcete nasadit soubory. jar do Java SE, použijte `/api/zipdeploy/` koncový bod webu Kudu. Další informace o tomto rozhraní API najdete v [této dokumentaci](./deploy-zip.md#rest).

### <a name="tomcat"></a>Tomcat

K nasazení souborů. War do Tomcat použijte `/api/wardeploy/` koncový bod k odeslání souboru archivu. Další informace o tomto rozhraní API najdete v [této dokumentaci](./deploy-zip.md#deploy-war-file).

::: zone pivot="platform-linux"

### <a name="jboss-eap"></a>JBoss EAP

K nasazení souborů. War do JBoss použijte `/api/wardeploy/` koncový bod k odeslání souboru archivu. Další informace o tomto rozhraní API najdete v [této dokumentaci](./deploy-zip.md#deploy-war-file).

K nasazení souborů. ušních souborů [použijte FTP](deploy-ftp.md).

::: zone-end

Nesaďte své. War nebo. jar pomocí FTP. Nástroj FTP je určen pro nahrávání spouštěcích skriptů, závislostí nebo jiných souborů modulu runtime. Nejedná se o optimální volbu pro nasazování webových aplikací.

## <a name="logging-and-debugging-apps"></a>Protokolování a ladění aplikací

Sestavy výkonu, vizualizace provozu a checkups stavu jsou k dispozici pro každou aplikaci prostřednictvím Azure Portal. Další informace najdete v tématu [Přehled diagnostiky Azure App Service](overview-diagnostics.md).

### <a name="stream-diagnostic-logs"></a>Streamování diagnostických protokolů

::: zone pivot="platform-windows"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end
::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

::: zone-end

Další informace najdete v tématu [protokoly streamování v Cloud Shell](troubleshoot-diagnostic-logs.md#in-cloud-shell).

### <a name="ssh-console-access"></a>Přístup ke konzole SSH

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

::: zone pivot="platform-linux"

### <a name="troubleshooting-tools"></a>Nástroje pro řešení potíží

Integrované image Java jsou založené na operačním systému [Alpine Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) . `apk`K instalaci všech nástrojů nebo příkazů pro řešení potíží použijte Správce balíčků.

::: zone-end

### <a name="flight-recorder"></a>Záznam letu

Všechny běhové moduly Java v App Service s využitím JVMsu Azul se dodávají do zapisovače letu Zulu. Tuto možnost můžete použít k zaznamenávání událostí JVM, System a Application a odstraňování problémů v aplikacích Java.

::: zone pivot="platform-windows"

#### <a name="timed-recording"></a>Časový záznam vypršel

Chcete-li získat časový limit záznamu, budete potřebovat identifikátor PID (ID procesu) aplikace Java. Pokud chcete najít PID, otevřete prohlížeč na webu SCM vaší webové aplikace na adrese https://<název-Site-Name>. scm.azurewebsites.net/ProcessExplorer/. Tato stránka zobrazuje spuštěné procesy ve vaší webové aplikaci. Vyhledejte v tabulce proces s názvem "Java" a zkopírujte odpovídající identifikátor PID (ID procesu).

Dále otevřete **konzolu ladění** na horním panelu nástrojů webu SCM a spusťte následující příkaz. Nahraďte `<pid>` ID procesu, které jste zkopírovali dříve. Tento příkaz spustí 30 sekundový záznam aplikace Java a vygeneruje soubor s názvem `timed_recording_example.jfr` v `D:\home` adresáři.

```
jcmd <pid> JFR.start name=TimedRecording settings=profile duration=30s filename="D:\home\timed_recording_example.JFR"
```

::: zone-end
::: zone pivot="platform-linux"

Do vašeho App Service spusťte příkaz SSH a spuštěním `jcmd` příkazu zobrazte seznam všech spuštěných procesů Java. Kromě samotné jcmd by se měla zobrazit vaše aplikace Java spuštěná s IDENTIFIKAČNÍm číslem procesu (PID).

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

::: zone-end

#### <a name="analyze-jfr-files"></a>Analyzovat `.jfr` soubory

Použijte [FTPS](deploy-ftp.md) ke stažení souboru jfr do místního počítače. Chcete-li analyzovat soubor JFR, Stáhněte a nainstalujte [řízení Zulu](https://www.azul.com/products/zulu-mission-control/). Pokyny k řízení Zulu najdete v [dokumentaci k Azul](https://docs.azul.com/zmc/) a v [pokynech k instalaci](/java/azure/jdk/java-jdk-flight-recorder-and-mission-control).

### <a name="app-logging"></a>Protokolování aplikace

::: zone pivot="platform-windows"

Povolte [protokolování aplikací](troubleshoot-diagnostic-logs.md#enable-application-logging-windows) prostřednictvím Azure Portal nebo pomocí [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) a nakonfigurujte App Service tak, aby se do místního systému souborů nebo do služby Azure Blob Storage napsaly standardní a standardní chybové proudy konzolové aplikace. Protokolování do místní instance systému souborů App Service je po konfiguraci zakázáno 12 hodin. Pokud budete potřebovat delší dobu uchování, nakonfigurujte aplikaci tak, aby zapisovala výstup do kontejneru úložiště objektů BLOB. Protokoly aplikací Java a Tomcat najdete v adresáři */Home/LogFiles/Application/* .

::: zone-end
::: zone pivot="platform-linux"

Povolte [protokolování aplikací](troubleshoot-diagnostic-logs.md#enable-application-logging-linuxcontainer) prostřednictvím Azure Portal nebo pomocí [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) a nakonfigurujte App Service tak, aby se do místního systému souborů nebo do služby Azure Blob Storage napsaly standardní a standardní chybové proudy konzolové aplikace. Pokud budete potřebovat delší dobu uchování, nakonfigurujte aplikaci tak, aby zapisovala výstup do kontejneru úložiště objektů BLOB. Protokoly aplikací Java a Tomcat najdete v adresáři */Home/LogFiles/Application/* .

Protokolování Azure Blob Storage pro App Services na bázi Linux se dá nakonfigurovat jenom pomocí [Azure monitor (Preview)](./troubleshoot-diagnostic-logs.md#send-logs-to-azure-monitor-preview) . 

::: zone-end

Pokud vaše aplikace používá pro trasování [Logback](https://logback.qos.ch/) nebo [log4j](https://logging.apache.org/log4j) , můžete tyto trasování přepošlete pro účely revize do Azure Application Insights pomocí pokynů pro konfiguraci protokolovacího rozhraní v tématu [prozkoumat protokoly trasování Java v Application Insights](../azure-monitor/app/java-trace-logs.md).

## <a name="customization-and-tuning"></a>Přizpůsobení a ladění

Azure App Service pro Linux podporuje vyladění a přizpůsobení prostřednictvím Azure Portal a CLI. Projděte si následující články s konfigurací webové aplikace specifické pro jazyk Java:

- [Konfigurace nastavení aplikace](configure-common.md#configure-app-settings)
- [Nastavení vlastní domény](app-service-web-tutorial-custom-domain.md)
- [Konfigurace vazeb SSL](configure-ssl-bindings.md)
- [Přidat síť CDN](../cdn/cdn-add-to-web-app.md)
- [Konfigurace webu Kudu](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)


### <a name="set-java-runtime-options"></a>Nastavení možností modulu Java Runtime

Pokud chcete nastavit přidělenou paměť nebo jiné možnosti modulu runtime JVM, vytvořte [nastavení aplikace](configure-common.md#configure-app-settings) s názvem `JAVA_OPTS` s možnostmi. App Service předá toto nastavení jako proměnnou prostředí modulu runtime Java při spuštění.

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

- Instance B1 a S1: `-Xms1024m -Xmx1024m`
- Instance B2 a S2: `-Xms3072m -Xmx3072m`
- Instance B3 a S3: `-Xms6144m -Xmx6144m`

Při ladění nastavení haldy aplikace zkontrolujte podrobnosti plánu App Service a vezměte v úvahu více aplikací a slot nasazení potřebuje k vyhledání optimálního přidělení paměti.

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

### <a name="pre-compile-jsp-files"></a>Předem kompilovat soubory JSP

Chcete-li zlepšit výkon aplikací Tomcat, můžete před nasazením do App Service zkompilovat své soubory JSP. Můžete použít [modul plug-in Maven](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) , který poskytuje Apache Sling, nebo použít tento [soubor sestavení ANT](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation).

## <a name="secure-applications"></a>Zabezpečené aplikace

Aplikace Java běžící v App Service mají stejnou sadu [osvědčených postupů zabezpečení](../security/fundamentals/paas-applications-using-app-services.md) jako u jiných aplikací.

### <a name="authenticate-users-easy-auth"></a>Ověřování uživatelů (snadné ověřování)

Pomocí možnosti **ověřování a autorizace** nastavte ověřování aplikací v Azure Portal. Odtud můžete povolit ověřování pomocí Azure Active Directory nebo přes sociální přihlášení, jako je Facebook, Google nebo GitHub. Konfigurace Azure Portal funguje pouze při konfiguraci jednoho poskytovatele ověřování. Další informace najdete v tématu [Konfigurace aplikace App Service pro použití Azure Active Directory přihlášení](configure-authentication-provider-aad.md) a souvisejících článků pro jiné poskytovatele identity. Pokud potřebujete povolit více poskytovatelů přihlašování, postupujte podle pokynů v článku [přizpůsobení App Serviceho ověřování](app-service-authentication-how-to.md) .

#### <a name="java-se"></a>Java SE

Vývojáři pružinového spouštění můžou pomocí [Azure Active Directory pružiny Boot Starter](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory) zabezpečit aplikace pomocí známých poznámek a rozhraní API pro zabezpečení pružiny. Nezapomeňte zvětšit maximální velikost hlavičky v souboru *Application. Properties* . Navrhneme hodnotu `16384` .

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

Pokud chcete uživatele podepsat, použijte `/.auth/ext/logout` cestu. Chcete-li provést další akce, přečtěte si dokumentaci k [App Service ověřování a používání autorizace](./app-service-authentication-how-to.md). K dispozici je také oficiální dokumentace k [rozhraní Tomcat HttpServletRequest](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) a jeho metodám. V závislosti na konfiguraci App Service jsou také vycházející následující metody servlet:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

Chcete-li tuto funkci zakázat, vytvořte nastavení aplikace s názvem `WEBSITE_AUTH_SKIP_PRINCIPAL` s hodnotou `1` . Chcete-li zakázat všechny filtry servlet přidané App Service, vytvořte nastavení s názvem `WEBSITE_SKIP_FILTERS` s hodnotou `1` .

### <a name="configure-tlsssl"></a>Konfigurace TLS/SSL

Podle pokynů v části [zabezpečení vlastního názvu DNS s vazbou SSL v Azure App Service](configure-ssl-bindings.md) Nahrajte stávající certifikát SSL a vytvořte jeho vazbu s názvem domény vaší aplikace. Ve výchozím nastavení bude vaše aplikace pořád umožňovat připojení HTTP – postupujte podle kroků v tomto kurzu, abyste vynutili SSL a TLS.

### <a name="use-keyvault-references"></a>Použití odkazů na Trezor klíčů

[Azure webtrezor](../key-vault/general/overview.md) poskytuje centralizovanou správu tajných kódů pomocí zásad přístupu a historie auditu. Do trezoru klíčů můžete ukládat tajné kódy (například hesla nebo připojovací řetězce) a přistupovat k nim ve vaší aplikaci pomocí proměnných prostředí.

Nejdřív postupujte podle pokynů pro [udělení přístupu aplikace Key Vault](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) a [Vytvoření odkazu trezoru klíčů pro váš tajný klíč v nastavení aplikace](app-service-key-vault-references.md#reference-syntax). Můžete ověřit, že se odkaz na tajný kód přeloží vytištěním proměnné prostředí a vzdáleným přístupem k App Service terminálu.

Pro vložení těchto tajných kódů do konfiguračního souboru jarní nebo Tomcat použijte syntaxi injektáže proměnné prostředí ( `${MY_ENV_VAR}` ). V případě konfiguračních souborů pružiny se podívejte na tuto dokumentaci k [externě nakonfigurovaným konfiguracím](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).

::: zone pivot="platform-linux"

### <a name="use-the-java-key-store"></a>Použití úložiště klíčů Java

Ve výchozím nastavení se všechny veřejné nebo privátní certifikáty [nahrané do App Service Linux](configure-ssl-certificate.md) načtou do příslušných úložišť klíčů Java jako kontejner se spustí. Po nahrání certifikátu bude potřeba restartovat App Service, aby se načetla do úložiště klíčů Java. Veřejné certifikáty jsou načteny do úložiště klíčů na portálu `$JAVA_HOME/jre/lib/security/cacerts` a soukromé certifikáty jsou uloženy v `$JAVA_HOME/lib/security/client.jks` .

Pro šifrování připojení JDBC k certifikátům v úložišti klíčů Java může být potřeba další konfigurace. Další informace najdete v dokumentaci pro zvolený ovladač JDBC.

- [PostgreSQL](https://jdbc.postgresql.org/documentation/head/ssl-client.html)
- [SQL Server](/sql/connect/jdbc/connecting-with-ssl-encryption)
- [MySQL](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-reference-using-ssl.html)
- [MongoDB](https://mongodb.github.io/mongo-java-driver/3.4/driver/tutorials/ssl/)
- [Cassandra](https://docs.datastax.com/en/developer/java-driver/4.3/)

#### <a name="initialize-the-java-key-store"></a>Inicializace úložiště klíčů Java

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

Nástroj Java Key můžete interagovat nebo ladit [otevřením připojení SSH](configure-linux-open-ssh-session.md) k vašemu App Service a spuštěním příkazu `keytool` . Seznam příkazů najdete v [dokumentaci k nástroji Key](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) . Další informace o rozhraní API úložiště klíčů najdete [v oficiální dokumentaci](https://docs.oracle.com/javase/8/docs/api/java/security/KeyStore.html).

::: zone-end

## <a name="configure-apm-platforms"></a>Konfigurace platforem APM

V této části se dozvíte, jak připojit aplikace Java nasazené na Azure App Service v systému Linux pomocí platforem APM (Application Performance Monitoring) pro NewRelic a AppDynamics.

### <a name="configure-new-relic"></a>Konfigurovat nové Relic

::: zone pivot="platform-windows"

1. Vytvoření účtu NewRelic na [NewRelic.com](https://newrelic.com/signup)
2. Stáhněte si agenta Java ze NewRelic, bude mít název souboru podobný *newrelic-java-x.x.x.zip* .
3. Zkopírujte licenční klíč, budete ho potřebovat ke konfiguraci agenta později.
4. [Do své instance App Service](configure-linux-open-ssh-session.md) a vytvořte nový adresář */Home/site/wwwroot/APM* .
5. Nahrajte nebalené soubory NewRelic Java do adresáře pod */Home/site/wwwroot/APM* . Soubory pro vašeho agenta by měly být v */Home/site/wwwroot/APM/NewRelic* .
6. Upravte soubor YAML na */Home/site/wwwroot/APM/NewRelic/NewRelic.yml* a nahraďte hodnotu licence zástupného symbolu vlastním licenčním klíčem.
7. V Azure Portal přejděte do aplikace v App Service a vytvořte nové nastavení aplikace.

    - Pro aplikace **Java se** Vytvořte proměnnou prostředí s názvem `JAVA_OPTS` s hodnotou `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .
    - Pro **Tomcat** Vytvořte proměnnou prostředí s názvem `CATALINA_OPTS` s hodnotou `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .

::: zone-end
::: zone pivot="platform-linux"

1. Vytvoření účtu NewRelic na [NewRelic.com](https://newrelic.com/signup)
2. Stáhněte si agenta Java ze NewRelic, bude mít název souboru podobný *newrelic-java-x.x.x.zip* .
3. Zkopírujte licenční klíč, budete ho potřebovat ke konfiguraci agenta později.
4. [Do své instance App Service](configure-linux-open-ssh-session.md) a vytvořte nový adresář */Home/site/wwwroot/APM* .
5. Nahrajte nebalené soubory NewRelic Java do adresáře pod */Home/site/wwwroot/APM* . Soubory pro vašeho agenta by měly být v */Home/site/wwwroot/APM/NewRelic* .
6. Upravte soubor YAML na */Home/site/wwwroot/APM/NewRelic/NewRelic.yml* a nahraďte hodnotu licence zástupného symbolu vlastním licenčním klíčem.
7. V Azure Portal přejděte do aplikace v App Service a vytvořte nové nastavení aplikace.
   
    - Pro aplikace **Java se** Vytvořte proměnnou prostředí s názvem `JAVA_OPTS` s hodnotou `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .
    - Pro **Tomcat** Vytvořte proměnnou prostředí s názvem `CATALINA_OPTS` s hodnotou `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .

::: zone-end

>  Pokud již máte proměnnou prostředí pro `JAVA_OPTS` nebo `CATALINA_OPTS` , přidejte `-javaagent:/...` možnost na konec aktuální hodnoty.

### <a name="configure-appdynamics"></a>Konfigurace AppDynamics

::: zone pivot="platform-windows"

1. Vytvoření účtu AppDynamics na [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Stáhněte si agenta Java z webu AppDynamics, název souboru bude podobný *AppServerAgent-x.x.x.xxxxx.zip*
3. Pomocí [konzoly Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console) vytvořte nový adresář */Home/site/wwwroot/APM* .
4. Nahrajte soubory agenta Java do adresáře pod */Home/site/wwwroot/APM* . Soubory pro vašeho agenta by měly být v */Home/site/wwwroot/APM/AppDynamics* .
5. V Azure Portal přejděte do aplikace v App Service a vytvořte nové nastavení aplikace.

   - Pro aplikace **Java se** Vytvořte proměnnou prostředí s názvem `JAVA_OPTS` s hodnotou `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` , kde `<app-name>` je váš App Service název.
   - Pro aplikace **Tomcat** Vytvořte proměnnou prostředí s názvem `CATALINA_OPTS` s hodnotou `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` , kde `<app-name>` je váš App Service název.

::: zone-end
::: zone pivot="platform-linux"

1. Vytvoření účtu AppDynamics na [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Stáhněte si agenta Java z webu AppDynamics, název souboru bude podobný *AppServerAgent-x.x.x.xxxxx.zip*
3. [Do své instance App Service](configure-linux-open-ssh-session.md) a vytvořte nový adresář */Home/site/wwwroot/APM* .
4. Nahrajte soubory agenta Java do adresáře pod */Home/site/wwwroot/APM* . Soubory pro vašeho agenta by měly být v */Home/site/wwwroot/APM/AppDynamics* .
5. V Azure Portal přejděte do aplikace v App Service a vytvořte nové nastavení aplikace.

   - Pro aplikace **Java se** Vytvořte proměnnou prostředí s názvem `JAVA_OPTS` s hodnotou `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` , kde `<app-name>` je váš App Service název.
   - Pro aplikace **Tomcat** Vytvořte proměnnou prostředí s názvem `CATALINA_OPTS` s hodnotou `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` , kde `<app-name>` je váš App Service název.

::: zone-end

> [!NOTE]
>  Pokud již máte proměnnou prostředí pro `JAVA_OPTS` nebo `CATALINA_OPTS` , přidejte `-javaagent:/...` možnost na konec aktuální hodnoty.

## <a name="configure-data-sources"></a>Konfigurace zdrojů dat

### <a name="java-se"></a>Java SE

Pro připojení ke zdrojům dat v aplikacích pro pružinové spouštění doporučujeme vytvořit připojovací řetězce a vložit je do souboru *. Properties* .

1. V části "konfigurace" na stránce App Service nastavte název řetězce, vložte připojovací řetězec JDBC do pole hodnota a nastavte typ na Custom (vlastní). Volitelně můžete nastavit tento připojovací řetězec jako nastavení slotu.

    Tento připojovací řetězec je pro naši aplikaci přístupný jako proměnná prostředí s názvem `CUSTOMCONNSTR_<your-string-name>` . Například připojovací řetězec, který jsme vytvořili výše, bude pojmenován `CUSTOMCONNSTR_exampledb` .

2. V souboru *Application. Properties* , odkazujte na tento připojovací řetězec s názvem proměnné prostředí. V našem příkladu bychom použili následující.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

Další informace o tomto tématu najdete v [dokumentaci ke jarnímu spuštění pro přístup k datům a k](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html) [externě nakonfigurovaným konfiguracím](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html) .

::: zone pivot="platform-windows"

### <a name="tomcat"></a>Tomcat

Tyto pokyny platí pro všechna databázová připojení. Zástupné symboly budete muset vyplnit pomocí názvu třídy ovladače zvolené databáze a souboru JAR. Poskytuje se tabulka s názvy tříd a stahování ovladačů pro běžné databáze.

| Databáze   | Název třídy ovladače                             | Ovladač JDBC                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Stáhnout](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Stáhnout](https://dev.mysql.com/downloads/connector/j/) (vyberte "nezávislé na platformě") |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Stáhnout](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#download)                                                           |

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

2. V *context.xml* přidejte `Context` prvek pro propojení zdroje dat s adresou JNDI. `driverClassName`Zástupný symbol nahraďte názvem třídy vašeho ovladače z tabulky výše.

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

#### <a name="finalize-configuration"></a>Dokončit konfiguraci

Nakonec umístíme jar ovladače do cesty pro Tomcat a restartujete App Service. Zajistěte, aby byly soubory ovladače JDBC k dispozici pro classloader Tomcat, a to jejich umístěním do adresáře */Home/Tomcat/lib* . (Pokud tento adresář ještě neexistuje, vytvořte ho.) Chcete-li odeslat tyto soubory do instance App Service, proveďte následující kroky:

1. V [Cloud Shell](https://shell.azure.com)nainstalujte rozšíření WebApp:

    ```azurecli-interactive
    az extension add -–name webapp
    ```

2. Spuštěním následujícího příkazu rozhraní příkazového řádku vytvořte tunel SSH z místního systému, který chcete App Service:

    ```azurecli-interactive
    az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
    ```

3. Připojte se k místnímu tunelovém portu pomocí klienta SFTP a nahrajte soubory do složky */Home/Tomcat/lib* .

Alternativně můžete k nahrání ovladače JDBC použít klienta FTP. [Při získávání přihlašovacích údajů k FTP](deploy-configure-credentials.md)postupujte podle těchto pokynů.

---

::: zone-end
::: zone pivot="platform-linux"

### <a name="tomcat"></a>Tomcat

Tyto pokyny platí pro všechna databázová připojení. Zástupné symboly budete muset vyplnit pomocí názvu třídy ovladače zvolené databáze a souboru JAR. Poskytuje se tabulka s názvy tříd a stahování ovladačů pro běžné databáze.

| Databáze   | Název třídy ovladače                             | Ovladač JDBC                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Stáhnout](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Stáhnout](https://dev.mysql.com/downloads/connector/j/) (vyberte "nezávislé na platformě") |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Stáhnout](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#download)                                                           |

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

2. V *context.xml* přidejte `Context` prvek pro propojení zdroje dat s adresou JNDI. `driverClassName`Zástupný symbol nahraďte názvem třídy vašeho ovladače z tabulky výše.

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

Přidání sdíleného zdroje dat na úrovni serveru bude vyžadovat, abyste upravili server.xml Tomcat. Nejdřív nahrajte [spouštěcí skript](faq-app-service-linux.md#built-in-images) a nastavte cestu ke skriptu v příkazu **Konfigurace**  >  **při spuštění** . Spouštěcí skript můžete nahrát pomocí [FTP](deploy-ftp.md).

Spouštěcí skript vytvoří [transformaci XSL](https://www.w3schools.com/xml/xsl_intro.asp) do souboru server.xml a výstup výsledného souboru XML do `/usr/local/tomcat/conf/server.xml` . Spouštěcí skript by měl nainstalovat libxslt přes APK. Soubor XSL a spouštěcí skript je možné nahrávat prostřednictvím FTP. Níže je příklad spouštěcího skriptu.

```sh
# Install libxslt. Also copy the transform file to /home/tomcat/conf/
apk add --update libxslt

# Usage: xsltproc --output output.xml style.xsl input.xml
xsltproc --output /home/tomcat/conf/server.xml /home/tomcat/conf/transform.xsl /usr/local/tomcat/conf/server.xml
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

    Alternativně můžete k nahrání ovladače JDBC použít klienta FTP. [Při získávání přihlašovacích údajů k FTP](deploy-configure-credentials.md)postupujte podle těchto pokynů.

2. Pokud jste vytvořili zdroj dat na úrovni serveru, restartujte aplikaci App Service Linux. Tomcat se obnoví `CATALINA_BASE` `/home/tomcat` a použije aktualizovanou konfiguraci.

### <a name="jboss-eap"></a>JBoss EAP

Existují tři základní kroky při [registraci zdroje dat pomocí protokolu JBoss EAP](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.0/html/configuration_guide/datasource_management): nahrání ovladače JDBC, přidání ovladače JDBC jako modulu a registrace modulu. App Service je Bezstavová hostitelská služba, takže konfigurační příkazy pro přidání a registraci modulu zdroje dat musí být spouštěny pomocí skriptů a aplikovány při spuštění kontejneru.

1. Získejte ovladač JDBC vaší databáze. 
2. Vytvořte soubor definice modulu XML pro ovladač JDBC. Níže zobrazený příklad je definice modulu pro PostgreSQL.

    ```xml
    <?xml version="1.0" ?>
    <module xmlns="urn:jboss:module:1.1" name="org.postgres">
        <resources>
        <!-- ***** IMPORTANT : REPLACE THIS PLACEHOLDER *******-->
        <resource-root path="/home/site/deployments/tools/postgresql-42.2.12.jar" />
        </resources>
        <dependencies>
            <module name="javax.api"/>
            <module name="javax.transaction.api"/>
        </dependencies>
    </module>
    ```

1. Příkazy rozhraní příkazového řádku JBoss umístěte do souboru s názvem `jboss-cli-commands.cli` . Příkazy JBoss musí přidat modul a zaregistrovat ho jako zdroj dat. Následující příklad ukazuje příkazy rozhraní příkazového řádku JBoss pro PostgreSQL.

    ```bash
    #!/usr/bin/env bash
    module add --name=org.postgres --resources=/home/site/deployments/tools/postgresql-42.2.12.jar --module-xml=/home/site/deployments/tools/postgres-module.xml

    /subsystem=datasources/jdbc-driver=postgres:add(driver-name="postgres",driver-module-name="org.postgres",driver-class-name=org.postgresql.Driver,driver-xa-datasource-class-name=org.postgresql.xa.PGXADataSource)

    data-source add --name=postgresDS --driver-name=postgres --jndi-name=java:jboss/datasources/postgresDS --connection-url=${POSTGRES_CONNECTION_URL,env.POSTGRES_CONNECTION_URL:jdbc:postgresql://db:5432/postgres} --user-name=${POSTGRES_SERVER_ADMIN_FULL_NAME,env.POSTGRES_SERVER_ADMIN_FULL_NAME:postgres} --password=${POSTGRES_SERVER_ADMIN_PASSWORD,env.POSTGRES_SERVER_ADMIN_PASSWORD:example} --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=org.postgresql.Driver --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLExceptionSorter --jta=true --use-java-context=true --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLValidConnectionChecker
    ```

1. Vytvořte spouštěcí skript, `startup_script.sh` který bude volat příkazy rozhraní příkazového řádku JBoss. Následující příklad ukazuje, jak zavolat `jboss-cli-commands.cli` . Později budete configre App Service ke spuštění tohoto skriptu při spuštění kontejneru. 

    ```bash
    $JBOSS_HOME/bin/jboss-cli.sh --connect --file=/home/site/deployments/tools/jboss-cli-commands.cli
    ```

1. Pomocí klienta FTP dle vašeho výběru nahrajte ovladač JDBC,, `jboss-cli-commands.cli` `startup_script.sh` a definice modulu do nástroje `/site/deployments/tools/` .
2. Nakonfigurujte, aby se váš web spouštěl `startup_script.sh` při spuštění kontejneru. Na webu Azure Portal přejděte do části **Konfigurace**  >  **Obecné nastavení**  >  **spouštěcí příkaz** . Nastavte pole spouštěcí příkaz na `/home/site/deployments/tools/startup_script.sh` . **Uložte** změny.

Pokud chcete potvrdit, že se zdroj dat přidal do serveru JBoss, připojte se ke svému WebApp a spusťte příkaz SSH `$JBOSS_HOME/bin/jboss-cli.sh --connect` . Po připojení k JBoss spusťte `/subsystem=datasources:read-resource` a vytiskněte seznam zdrojů dat.

::: zone-end

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

## <a name="choosing-a-java-runtime-version"></a>Výběr běhové verze Java

App Service umožňuje uživatelům zvolit hlavní verzi JVM, jako je Java 8 nebo Java 11, jakož i podverze, jako je například 1.8.0 _232 nebo 11.0.5. Také můžete zvolit, aby se podverze automaticky aktualizovala, protože nové podverze budou k dispozici. Ve většině případů by měly být v produkčních lokalitách používány připnuté menší verze JVM. Tím zabráníte výpadkům unnanticipated při automatické aktualizaci dílčí verze.

Pokud se rozhodnete k vedlejší verzi připnout, budete muset pravidelně aktualizovat podverze JVM v lokalitě. Chcete-li zajistit, aby vaše aplikace běžela v novější podverzi, vytvořte přípravný slot a zvyšte podverzi v přípravném webu. Jakmile ověříte, že je aplikace správně spuštěná na nové dílčí verzi, můžete vyměnit pracovní a produkční sloty.

## <a name="jboss-eap-hardware-options"></a>Hardwarové možnosti JBoss protokolu EAP

Protokol EAP JBoss je k dispozici pouze v možnostech hardwaru Premium a Isolated. Zákazníci, kteří vytvořili web JBoss EAP na úrovni Free, Shared, Basic nebo Standard ve verzi Public Preview, by měli škálovat až na úroveň Premium nebo izolovanou, aby nedocházelo k neočekávanému chování.

## <a name="java-runtime-statement-of-support"></a>Příkaz Java Runtime pro podporu

### <a name="jdk-versions-and-maintenance"></a>Verze a údržba JDK

Podporovaná sada Java Development Kit (JDK) pro Azure je zajištěná [Zulu](https://www.azul.com/downloads/azure-only/zulu/) prostřednictvím [systémů Azul](https://www.azul.com/). Azul Zulu Enterprise Builds of OpenJDK jsou špičkovou distribucí do produkčního prostředí OpenJDK pro Azure a Azure Stack zajištěné systémy Microsoftu a Azul. Obsahuje všechny komponenty pro vytváření a spouštění aplikací Java SE. JDK můžete nainstalovat z [instalace Java JDK](https://aka.ms/azure-jdks).

Hlavní aktualizace verze se budou poskytovat prostřednictvím nových možností modulu runtime v Azure App Service. Zákazníci aktualizují tyto novější verze Java tím, že nakonfigurují nasazení App Service a zodpovídá za testování a zajištění významné aktualizace, které vyhovují jejich potřebám.

Podporované sady JDK se na čtvrtletní bázi automaticky opravují v lednu, dubnu, červenci a říjnu každého roku. Další informace o jazyce Java v Azure najdete v [tomto dokumentu podpory](/azure/developer/java/fundamentals/java-jdk-long-term-support).

### <a name="security-updates"></a>Aktualizace zabezpečení

Opravy a opravy pro hlavní slabá místa zabezpečení budou vydány, jakmile budou dostupné ze systémů Azul. "Hlavní" ohrožení zabezpečení je definováno základním skóre 9,0 nebo vyšším v [systému NIST Common zranitelnost Standard, verze 2](https://nvd.nist.gov/vuln-metrics/cvss).

Tomcat 8,0 dosáhl [konce životnosti (konce řádku) až do 30. září 2018](https://tomcat.apache.org/tomcat-80-eol.html). I když je modul runtime na Azure App Service stále k dispozici, Azure nebude používat aktualizace zabezpečení Tomcat 8,0. Pokud je to možné, migrujte své aplikace na Tomcat 8,5 nebo 9,0. V Azure App Service jsou k dispozici obě Tomcat 8,5 a 9,0. Další informace najdete v [oficiální lokalitě Tomcat](https://tomcat.apache.org/whichversion.html) . 

### <a name="deprecation-and-retirement"></a>Vyřazení a vyřazení z provozu

Pokud bude vyřazení podporované běhové prostředí Java, budou se vývojáři Azure, kteří používají modul runtime, předávat oznámení o zastaralosti alespoň šest měsíců před vyřazením modulu runtime.


### <a name="local-development"></a>Místní vývoj

Vývojáři mohou stáhnout provozní edici Azul Zulu Enterprise JDK pro místní vývoj z [webu pro stažení Azul](https://www.azul.com/downloads/azure-only/zulu/).

### <a name="development-support"></a>Vývojová podpora

Podpora produktů pro [JDK Zulu s podporou Azure Azul](https://www.azul.com/downloads/azure-only/zulu/) je dostupná prostřednictvím Microsoftu při vývoji pro Azure nebo [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) s [kvalifikovaným plánem podpory Azure](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Další kroky

Na webu [Azure pro vývojáře v jazyce Java](/java/azure/) najdete informace o rychlých startech, kurzech a referenční dokumentaci Java pro Azure.

Obecné otázky týkající se použití App Service pro Linux, které nejsou specifické pro vývoj v jazyce Java, jsou zodpovězeny v tématu [Nejčastější dotazy k App Service Linux](faq-app-service-linux.md).
