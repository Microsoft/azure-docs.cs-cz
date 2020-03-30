---
title: Konfigurace aplikací Linux Java
description: Přečtěte si, jak pro vaši aplikaci nakonfigurovat předem sestavený kontejner Java. Tento článek ukazuje nejběžnější úlohy konfigurace.
keywords: azure app service, webapp, linux, oss, java, java ee, jee, javaee
author: bmitchell287
manager: barbkess
ms.devlang: java
ms.topic: article
ms.date: 11/22/2019
ms.author: brendm
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: 970701606811cbd61a9bfebe39ff82cdc91d5693
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80245833"
---
# <a name="configure-a-linux-java-app-for-azure-app-service"></a>Konfigurace linuxové aplikace v jazyce Java pro Azure App Service

Služba Azure App Service na Linuxu umožňuje vývojářům jazyka Java rychle vytvářet, nasazovat a škálovat své webové aplikace Tomcat nebo Java Standard Edition (SE) na plně spravované službě založené na Linuxu. Nasazujte aplikace pomocí modulů plug-in Maven z příkazového řádku nebo v editorech, jako je IntelliJ, Eclipse nebo Visual Studio Code.

Tato příručka obsahuje klíčové koncepty a pokyny pro vývojáře Javy, kteří používají vestavěný kontejner Linuxu ve službě App Service. Pokud jste službu Azure App Service nikdy nepoužívali, postupujte podle [rychlého startu jazyka Java](quickstart-java.md).

## <a name="deploying-your-app"></a>Nasazení aplikace

[Pomocí modulu Plug-Tma pro službu Azure App Service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) můžete nasadit soubory .jar i .war. Nasazení s oblíbenými inežy je taky podporované pomocí [sady Azure Toolkit for IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij) nebo [Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse).

V opačném případě bude metoda nasazení záviset na typu archivu:

- Chcete-li nasadit soubory .war `/api/wardeploy/` do aplikace Tomcat, použijte koncový bod k odeslání archivního souboru. Další informace o tomto rozhraní API naleznete v [této dokumentaci](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file).
- Chcete-li nasadit soubory .jar na `/api/zipdeploy/` bitové kopie Java SE, použijte koncový bod webu Kudu. Další informace o tomto rozhraní API naleznete v [této dokumentaci](https://docs.microsoft.com/azure/app-service/deploy-zip#rest).

Nenasazujte .war nebo .jar pomocí FTP. Nástroj FTP je určen k nahrávání spouštěcích skriptů, závislostí nebo jiných souborů runtime. Není to optimální volba pro nasazení webových aplikací.

## <a name="logging-and-debugging-apps"></a>Protokolování a ladění aplikací

Sestavy výkonu, vizualizace provozu a kontroly stavu jsou k dispozici pro každou aplikaci prostřednictvím portálu Azure. Další informace najdete v [tématu Přehled diagnostiky služby Azure App Service](../overview-diagnostics.md).

### <a name="ssh-console-access"></a>Přístup ke konzole SSH

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>Streamování diagnostických protokolů

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

Další informace najdete [v tématu Stream protokoly v prostředí Cloud Shell](../troubleshoot-diagnostic-logs.md#in-cloud-shell).

### <a name="app-logging"></a>Protokolování aplikací

Povolte [protokolování aplikací](../troubleshoot-diagnostic-logs.md?toc=/azure/app-service/containers/toc.json#enable-application-logging-windows) prostřednictvím portálu Azure nebo [rozhraní příkazového příkazu Azure](/cli/azure/webapp/log#az-webapp-log-config) a nakonfigurujte službu App Service tak, aby zapisovala standardní výstup konzoly vaší aplikace a standardní datové proudy chyb konzoly do místního souborového systému nebo úložiště objektů blob Azure. Protokolování k místní instanci souborového systému služby App Service je zakázáno 12 hodin po konfiguraci. Pokud potřebujete delší uchovávání informací, nakonfigurujte aplikaci pro zápis výstupu do kontejneru úložiště objektů Blob. Vaše java a tomcat app logy najdete v adresáři */home/LogFiles/Application/.*

Pokud vaše aplikace používá [Logback](https://logback.qos.ch/) nebo [Log4j](https://logging.apache.org/log4j) pro trasování, můžete tyto trasování předat ke kontrole do Azure Application Insights pomocí pokynů ke konfiguraci architektury protokolování v [prozkoumat protokoly trasování java v Application Insights](/azure/application-insights/app-insights-java-trace-logs).

### <a name="troubleshooting-tools"></a>Nástroje pro řešení potíží

Vestavěné obrázky Java jsou založeny na operačním systému [Alpine Linux.](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) Pomocí `apk` správce balíčků nainstalujte všechny nástroje nebo příkazy pro odstraňování potíží.

### <a name="flight-recorder"></a>Letový zapisovač

Všechny linuxové java obrázky v app service mají nainstalován Zulu Flight Recorder, takže se můžete snadno připojit k JVM a spustit záznam profileru nebo generovat výpis haldy.

#### <a name="timed-recording"></a>Časované nahrávání

Chcete-li začít, SSH do služby App Service a spusťte `jcmd` příkaz zobrazíte seznam všech procesů Java spuštěn. Kromě samotného jcmd byste měli vidět vaši java aplikaci spuštěnou s id procesu (pid).

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

Spusťte níže uvedený příkaz a spusťte 30sekundový záznam JVM. Tím se profilJVM a vytvořit soubor JFR s názvem *jfr_example.jfr* v domovském adresáři. (Nahraďte 116 pid vaší java aplikace.)

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

Během 30sekundového intervalu můžete ověřit, že `jcmd 116 JFR.check`nahrávání probíhá spuštěním . Zobrazí se všechny nahrávky pro daný proces Java.

#### <a name="continuous-recording"></a>Průběžné nahrávání

Můžete použít Zulu Flight Recorder nepřetržitě profilovat java aplikaci s minimálním dopadem na výkon za běhu[(zdroj).](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf) Chcete-li tak učinit, spusťte následující příkaz příkazového příkazu Azure CLI k vytvoření nastavení aplikace s názvem JAVA_OPTS s potřebnou konfigurací. Obsah JAVA_OPTS nastavení aplikace se předává příkazu `java` při spuštění aplikace.

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

Po spuštění nahrávání můžete kdykoli vypustit aktuální data `JFR.dump` záznamu pomocí příkazu.

```shell
jcmd <pid> JFR.dump name=continuous_recording filename="/home/recording1.jfr"
```

Další informace naleznete v [odkazu na příkaz Jcmd](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190).

### <a name="analyzing-recordings"></a>Analýza nahrávek

Pomocí [ftps](../deploy-ftp.md) stáhnout soubor JFR do místního počítače. Chcete-li analyzovat soubor JFR, stáhněte a nainstalujte [Zulu Mission Control](https://www.azul.com/products/zulu-mission-control/). Pokyny k řízení mise Zulu naleznete v [dokumentaci azul](https://docs.azul.com/zmc/) a [pokyny k instalaci](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control).

## <a name="customization-and-tuning"></a>Přizpůsobení a ladění

Azure App Service pro Linux podporuje mimo pole ladění a přizpůsobení prostřednictvím portálu Azure a rozhraní příkazového příkazového příkazového příkazu. V následujících článcích naleznete konfiguraci webové aplikace, která není specifická pro Jazykovou javu:

- [Konfigurace nastavení aplikace](../configure-common.md?toc=/azure/app-service/containers/toc.json#configure-app-settings)
- [Nastavení vlastní domény](../app-service-web-tutorial-custom-domain.md?toc=/azure/app-service/containers/toc.json)
- [Konfigurace vazeb SSL](../configure-ssl-bindings.md?toc=/azure/app-service/containers/toc.json)
- [Přidání sítě CDN](../../cdn/cdn-add-to-web-app.md?toc=/azure/app-service/containers/toc.json)
- [Konfigurace webu Kudu](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>Nastavení možností runtime v jazyce Java

Chcete-li nastavit přidělenou paměť nebo jiné možnosti běhu JVM v prostředí Tomcat i Java SE, vytvořte [nastavení aplikace](../configure-common.md?toc=/azure/app-service/containers/toc.json#configure-app-settings) s názvem `JAVA_OPTS` s možnostmi. App Service Linux předá toto nastavení jako proměnnou prostředí na prostředí java runtime při jeho spuštění.

Na webu Azure Portal v části **Nastavení aplikace** pro webovou aplikaci vytvořte nové nastavení aplikace s názvem, `JAVA_OPTS` které zahrnuje další nastavení, například `-Xms512m -Xmx1204m`.

Pokud chcete nakonfigurovat nastavení aplikace z modulu plug-in Maven, přidejte značky nastavení a hodnoty v části pluginu Azure. Následující příklad nastaví konkrétní minimální a maximální velikost haldy jazyka Java:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

Vývojáři, kteří ve svém plánu služby App Service spouštějí jednu aplikaci s jedním slotem pro nasazení, můžou použít následující možnosti:

- Instance B1 a S1:`-Xms1024m -Xmx1024m`
- Instance B2 a S2:`-Xms3072m -Xmx3072m`
- Instance B3 a S3:`-Xms6144m -Xmx6144m`

Při ladění nastavení haldy aplikace zkontrolujte podrobnosti plánu služby App Service a vezměte v úvahu více aplikací a slot pro nasazení potřebuje najít optimální přidělení paměti.

Pokud nasazujete aplikaci JAR, měla by být pojmenována *app.jar,* aby integrovaná bitová kopie mohla správně identifikovat vaši aplikaci. (Plugin Maven to přejmenování automaticky.) Pokud nechcete přejmenovat jar na *app.jar*, můžete nahrát skript prostředí s příkazem ke spuštění JAR. Pak úplnou cestu k tomuto skriptu vložte do textového pole [Spouštěcí soubor](app-service-linux-faq.md#built-in-images) v části Konfigurace na portálu. Spouštěcí skript se nespouští z adresáře, do kterého je umístěn. Proto ve spouštěcím skriptu vždy používejte absolutní cesty k referenčním souborům (například: `java -jar /home/myapp/myapp.jar`).

### <a name="turn-on-web-sockets"></a>Zapnutí webových soketů

Zapněte podporu webových soketů na webu Azure Portal v **nastavení aplikace** pro aplikaci. Nastavení se projeví až po restartování aplikace.

Zapněte podporu webových soketů pomocí rozhraní příkazu Azure CLI pomocí následujícího příkazu:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --web-sockets-enabled true
```

Potom aplikaci restartujte:

```azurecli-interactive
az webapp stop --name <app-name> --resource-group <resource-group-name>
az webapp start --name <app-name> --resource-group <resource-group-name>
```

### <a name="set-default-character-encoding"></a>Nastavení výchozího kódování znaků

Na webu Azure Portal v části **Nastavení aplikací** pro webovou aplikaci vytvořte nové nastavení aplikace s názvem `JAVA_OPTS` value `-Dfile.encoding=UTF-8`.

Případně můžete nakonfigurovat nastavení aplikace pomocí pluginu App Service Maven. V konfiguraci modulu plug-in přidejte značky názvu a hodnoty nastavení:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

### <a name="adjust-startup-timeout"></a>Upravit časový čas spuštění

Pokud je vaše java aplikace obzvláště velká, měli byste prodloužit časový limit spuštění. Chcete-li to provést, `WEBSITES_CONTAINER_START_TIME_LIMIT` vytvořte nastavení aplikace a nastavte jej na počet sekund, které app service by měl počkat před vypršením časového limitu. Maximální hodnota `1800` je sekund.

### <a name="pre-compile-jsp-files"></a>Předkompilace souborů JSP

Chcete-li zlepšit výkon aplikací Tomcat, můžete zkompilovat soubory JSP před nasazením do služby App Service. Můžete použít [plugin Maven](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) poskytované Apache Sling, nebo pomocí tohoto [ant sestavení souboru](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation).

## <a name="secure-applications"></a>Zabezpečené aplikace

Java aplikace spuštěné v App Service pro Linux mají stejnou sadu [osvědčených postupů zabezpečení](/azure/security/security-paas-applications-using-app-services) jako ostatní aplikace.

### <a name="authenticate-users-easy-auth"></a>Ověření uživatelů (Jednoduché ověření)

Nastavte ověřování aplikací na webu Azure Portal pomocí možnosti **Ověřování a autorizace.** Odtud můžete povolit ověřování pomocí Azure Active Directory nebo sociální přihlášení, jako je Facebook, Google nebo GitHub. Konfigurace portálu Azure funguje jenom při konfiguraci jednoho zprostředkovatele ověřování. Další informace [najdete v tématu Konfigurace aplikace App Service tak, aby používala přihlášení služby Azure Active Directory,](../configure-authentication-provider-aad.md?toc=/azure/app-service/containers/toc.json) a související články pro jiné poskytovatele identit. Pokud potřebujete povolit více poskytovatelů přihlášení, postupujte podle pokynů v článku [ověřování vlastní služby App Service.](../app-service-authentication-how-to.md?toc=/azure/app-service/containers/toc.json)

#### <a name="tomcat"></a>Tomcat

Aplikace Tomcat může přistupovat k deklaracím identity uživatele přímo z servletu přetypováním objektu Principal na objekt Map. Mapovat objekt bude mapovat každý typ deklarace identity do kolekce deklarací pro tento typ. V níže uvedeném kódu `request` `HttpServletRequest`je instancí třídy .

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

Nyní můžete zkontrolovat `Map` objekt pro konkrétní deklarace. Například následující fragment kódu promítne všechny typy deklarací a vytiskne obsah každé kolekce.

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

Chcete-li odhlásit `/.auth/ext/logout` uživatele, použijte cestu. Další akce naleznete v dokumentaci k [ověřování a autorizaci služby App Service .](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to) K dispozici je také oficiální dokumentace o [rozhraní Tomcat HttpServletRequest](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) a jeho metodách. Následující metody servlet jsou také hydratované na základě konfigurace služby App Service:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

Chcete-li tuto funkci zakázat, vytvořte nastavení aplikace s názvem `WEBSITE_AUTH_SKIP_PRINCIPAL` s hodnotou `1`. Chcete-li zakázat všechny servletové filtry `WEBSITE_SKIP_FILTERS` přidané službou App Service, vytvořte nastavení s názvem s hodnotou `1`.

#### <a name="spring-boot"></a>Spring Boot

Vývojáři jarního spouštění můžou pomocí [startéru jarního spouštění služby Azure Active Directory](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) zabezpečit aplikace pomocí známých poznámk spring security a rozhraní API. Nezapomeňte zvětšit maximální velikost záhlaví v souboru *application.properties.* Doporučujeme hodnotu `16384`.

### <a name="configure-tlsssl"></a>Konfigurace protokolu TLS/SSL

Postupujte podle pokynů v [secure vlastní název DNS s vazbou SSL ve službě Azure App Service](../configure-ssl-bindings.md?toc=/azure/app-service/containers/toc.json) nahrát existující certifikát SSL a vázat jej na název domény vaší aplikace. Ve výchozím nastavení aplikace bude stále povolit připojení HTTP postupujte podle konkrétních kroků v kurzu k vynucení SSL a TLS.

### <a name="use-keyvault-references"></a>Použití odkazů KeyVault

[Azure KeyVault](../../key-vault/key-vault-overview.md) poskytuje centralizovanou správu tajných klíčů se zásadami přístupu a historií auditu. Můžete ukládat tajné klíče (například hesla nebo připojovací řetězce) v KeyVault a přístup k těmto tajným kódům ve vaší aplikaci prostřednictvím proměnných prostředí.

Nejprve postupujte podle pokynů pro [udělení přístupu aplikace k trezoru klíčů](../app-service-key-vault-references.md#granting-your-app-access-to-key-vault) a [vytvoření odkazu keyvault na váš tajný klíč v nastavení aplikace](../app-service-key-vault-references.md#reference-syntax). Můžete ověřit, že odkaz řeší na tajný klíč vytištěním proměnné prostředí při vzdáleném přístupu k terminálu služby App Service.

Chcete-li tyto tajné klíče vložit do konfiguračního`${MY_ENV_VAR}`souboru Spring nebo Tomcat, použijte syntaxi proměnné vstřikování prostředí ( ). Konfigurační soubory pružinnaleznete v této dokumentaci o [externalizovaných konfiguracích](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).

### <a name="using-the-java-key-store"></a>Používání úložiště klíčů Java

Ve výchozím nastavení budou všechny veřejné nebo soukromé certifikáty [nahrané do App Service Linux](../configure-ssl-certificate.md) načteny do příslušných obchodů s klíči Java při spuštění kontejneru. Po nahrání certifikátu budete muset restartovat službu App Service, aby se načetla do obchodu s klíči java klíče. Veřejné certifikáty jsou načteny `$JAVA_HOME/jre/lib/security/cacerts`do úložiště klíčů v `$JAVA_HOME/lib/security/client.jks`aplikaci a soukromé certifikáty jsou uloženy v aplikaci .

Další konfigurace může být nezbytná pro šifrování připojení JDBC s certifikáty v úložišti klíčů Java. V dokumentaci k vybranému ovladači JDBC naleznete v dokumentaci.

- [PostgreSQL](https://jdbc.postgresql.org/documentation/head/ssl-client.html)
- [SQL Server](https://docs.microsoft.com/sql/connect/jdbc/connecting-with-ssl-encryption?view=sql-server-ver15)
- [Mysql](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-reference-using-ssl.html)
- [MongoDB](https://mongodb.github.io/mongo-java-driver/3.4/driver/tutorials/ssl/)
- [Cassandra](https://docs.datastax.com/en/developer/java-driver/4.3/)

#### <a name="initializing-the-java-key-store"></a>Inicializace úložiště klíčů Java

Chcete-li `import java.security.KeyStore` objekt inicializovat, načtěte soubor úložiště klíčů heslem. Výchozí heslo pro oba klíčové obchody je "changeit".

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

Certifikáty můžete načíst ručně do úložiště klíčů. Vytvořte nastavení `SKIP_JAVA_KEYSTORE_LOAD`aplikace , s `1` hodnotou zakázat App Service z načítání certifikátů do úložiště klíčů automaticky. Všechny veřejné certifikáty nahrané do služby App `/var/ssl/certs/`Service prostřednictvím portálu Azure se ukládají pod . Soukromé certifikáty jsou `/var/ssl/private/`uloženy pod .

Nástroj klíč Iva můžete interagovat nebo ladit [otevřením připojení SSH](app-service-linux-ssh-support.md) ke službě App Service a spuštěním příkazu `keytool`. Seznam příkazů naleznete v [dokumentaci ke klíčovému nástroji.](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) Další informace o rozhraní KEYStore API naleznete [v oficiální dokumentaci](https://docs.oracle.com/javase/8/docs/api/java/security/KeyStore.html).

## <a name="configure-apm-platforms"></a>Konfigurace platforem APM

Tato část ukazuje, jak připojit java aplikace nasazené ve službě Azure App Service na Linuxu s platformami pro sledování výkonu aplikací NewRelic a AppDynamics (APM).

### <a name="configure-new-relic"></a>Konfigurace nové relikvie

1. Vytvořte si účet NewRelic v [NewRelic.com](https://newrelic.com/signup)
2. Stáhněte si agenta Java z NewRelic, bude mít název souboru podobný *newrelic-java-x.x.x.zip*.
3. Zkopírujte licenční klíč, budete ho potřebovat k pozdější konfiguraci agenta.
4. [SSH do instance služby App Service](app-service-linux-ssh-support.md) a vytvořte nový adresář */home/site/wwwroot/apm*.
5. Nahrajte rozbalené soubory agentů NewRelic Java do adresáře pod */home/site/wwwroot/apm*. Soubory pro vašeho agenta by měly být v */home/site/wwwroot/apm/newrelic*.
6. Upravte soubor YAML na adrese */home/site/wwwroot/apm/newrelic/newrelic.yml* a nahraďte zástupnou hodnotu licence vlastním licenčním klíčem.
7. Na webu Azure Portal vyhledejte svou aplikaci ve službě App Service a vytvořte nové nastavení aplikace.
    - Pokud vaše aplikace používá **Java SE**, `JAVA_OPTS` vytvořte `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`proměnnou prostředí s názvem s hodnotou .
    - Pokud používáte **Tomcat**, vytvořte `CATALINA_OPTS` proměnnou `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`prostředí s názvem s hodnotou .

### <a name="configure-appdynamics"></a>Konfigurace aplikace AppDynamics

1. Vytvoření účtu AppDynamics na [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Stáhněte si agenta Java z webu AppDynamics, název souboru bude podobný *AppServerAgent-x.x.x.xxxxx.zip*
3. [SSH do instance služby App Service](app-service-linux-ssh-support.md) a vytvořte nový adresář */home/site/wwwroot/apm*.
4. Nahrajte soubory agentů Java do adresáře pod */home/site/wwwroot/apm*. Soubory pro vašeho agenta by měly být v */home/site/wwwroot/apm/appdynamics*.
5. Na webu Azure Portal vyhledejte svou aplikaci ve službě App Service a vytvořte nové nastavení aplikace.
    - Pokud používáte **Java SE**, vytvořte `JAVA_OPTS` proměnnou `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` `<app-name>` prostředí s názvem s hodnotou, kde je název služby App Service.
    - Pokud používáte **Tomcat**, vytvořte `CATALINA_OPTS` proměnnou `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` prostředí `<app-name>` s názvem s hodnotou, kde je název služby App Service.

> [!NOTE]
> Pokud již máte proměnnou `JAVA_OPTS` `CATALINA_OPTS`prostředí pro `-javaagent:/...` nebo , připojte možnost na konec aktuální hodnoty.

## <a name="configure-jar-applications"></a>Konfigurace aplikací JAR

### <a name="starting-jar-apps"></a>Spuštění aplikací JAR

Ve výchozím nastavení služba App Service očekává, že vaše aplikace JAR bude *pojmenována app.jar*. Pokud má tento název, bude spuštěn automaticky. Pro uživatele Maven můžete nastavit název `<finalName>app</finalName>` JAR `<build>` zahrnutím do části *pom.xml*. [Totéž můžete udělat v Gradle](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.bundling.Jar.html#org.gradle.api.tasks.bundling.Jar:archiveFileName) nastavením vlastnosti. `archiveFileName`

Pokud chcete použít jiný název pro jar, musíte také zadat [spouštěcí příkaz,](app-service-linux-faq.md#built-in-images) který provede soubor JAR. Například, `java -jar my-jar-app.jar`. Hodnotu příkazu po spuštění můžete nastavit na portálu, v části Obecné `STARTUP_COMMAND`nastavení konfigurace > nebo s názvem Nastavení aplikace .

### <a name="server-port"></a>Port serveru

App Service Linux směruje příchozí požadavky na port 80, takže vaše aplikace by měla poslouchat i na portu 80. Můžete to provést v konfiguraci aplikace (například soubor *application.properties* spring) nebo v `java -jar spring-app.jar --server.port=80`příkazu startup (například). Běžné architektury Java naleznete v následující dokumentaci:

- [Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-properties-and-configuration.html#howto-use-short-command-line-arguments)
- [SparkJava](http://sparkjava.com/documentation#embedded-web-server)
- [Mikronaut](https://docs.micronaut.io/latest/guide/index.html#runningSpecificPort)
- [Play Framework](https://www.playframework.com/documentation/2.6.x/ConfiguringHttps#Configuring-HTTPS)
- [Vertx](https://vertx.io/docs/vertx-core/java/#_start_the_server_listening)
- [Kvarkus](https://quarkus.io/guides/application-configuration-guide)

## <a name="data-sources"></a>Zdroje dat

### <a name="tomcat"></a>Tomcat

Tyto pokyny platí pro všechna připojení databáze. Zástupné symboly budete muset vyplnit názvem třídy ovladače vybrané databáze a souborem JAR. Za předpokladu, je tabulka s názvy tříd a ovladače ke stažení pro běžné databáze.

| Databáze   | Název třídy ovladače                             | Ovladač JDBC                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Stáhnout](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Stáhnout](https://dev.mysql.com/downloads/connector/j/) (vyberte "Nezávislé na platformě") |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Stáhnout](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#download)                                                           |

Chcete-li nakonfigurovat tomcat používat připojení k databázi Java (JDBC) `CATALINA_OPTS` nebo Java Persistence API (JPA), nejprve přizpůsobit proměnnou prostředí, která je přečtena v Tomcat při startu. Nastavte tyto hodnoty prostřednictvím nastavení aplikace v [modulu plug-in App Service Maven](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Nebo nastavte proměnné prostředí na stránce **Nastavení konfigurační** > **chod na** webu Azure Portal.

Dále určete, zda by měl být zdroj dat k dispozici pro jednu aplikaci nebo pro všechny aplikace spuštěné v servletu Tomcat.

#### <a name="application-level-data-sources"></a>Zdroje dat na úrovni aplikace

1. Vytvořte soubor *context.xml* v adresáři *META-INF/* vašeho projektu. Pokud adresář *META-INF/* neexistuje, vytvořte jej.

2. V `Context` *souboru context.xml*přidejte prvek pro propojení zdroje dat s adresou JNDI. Nahraďte `driverClassName` zástupný symbol názvem třídy řidiče z výše uvedené tabulky.

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

3. Aktualizujte *web.xml* aplikace tak, aby používal zdroj dat ve vaší aplikaci.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>Sdílené prostředky na úrovni serveru

Přidání sdíleného zdroje dat na úrovni serveru bude vyžadovat úpravu souboru.xml aplikace Tomcat. Nejprve nahrajte [spouštěcí skript](app-service-linux-faq.md#built-in-images) a nastavte cestu ke skriptu v **příkazu Configuration** > **Startup Command**. Spouštěcí skript můžete nahrát pomocí [ftp](../deploy-ftp.md).

Spouštěcí skript provede [transformaci xsl](https://www.w3schools.com/xml/xsl_intro.asp) do souboru server.xml a výsledný `/usr/local/tomcat/conf/server.xml`soubor XML bude výstupem do aplikace . Spouštěcí skript by měl nainstalovat libxslt přes apk. Váš xsl soubor a spouštěcí skript lze nahrát přes FTP. Níže je uveden příklad spouštěcího skriptu.

```sh
# Install libxslt. Also copy the transform file to /home/tomcat/conf/
apk add --update libxslt

# Usage: xsltproc --output output.xml style.xsl input.xml
xsltproc --output /home/tomcat/conf/server.xml /home/tomcat/conf/transform.xsl /usr/local/tomcat/conf/server.xml
```

Příklad xsl soubor je uveden níže. Příklad souboru xsl přidá nový uzel konektoru do souboru Tomcat server.xml.

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

#### <a name="finalize-configuration"></a>Dokončení konfigurace

Nakonec umístěte ovladače JARs v Tomcat classpath a restartujte službu App Service.

1. Ujistěte se, že soubory ovladačů JDBC jsou k dispozici pro classloader Tomcat jejich umístěním do adresáře */home/tomcat/lib.* (Vytvořte tento adresář, pokud ještě neexistuje.) Chcete-li tyto soubory nahrát do instance služby App Service, proveďte následující kroky:

    1. V [prostředí Cloud Shell](https://shell.azure.com)nainstalujte rozšíření webapp:

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. Spusťte následující příkaz příkazu příkazu příkazu cli a vytvořte tunel SSH z místního systému do služby App Service:

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. Připojte se k místnímu portu tunelového propojení s klientem SFTP a nahrajte soubory do složky */home/tomcat/lib.*

    Alternativně můžete použít klienta FTP k nahrání ovladače JDBC. Postupujte podle těchto [pokynů pro získání pověření FTP](../deploy-configure-credentials.md?toc=/azure/app-service/containers/toc.json).

2. Pokud jste vytvořili zdroj dat na úrovni serveru, restartujte aplikaci App Service Linux. Tomcat se `CATALINA_BASE` `/home/tomcat` resetuje a použije aktualizovanou konfiguraci.

### <a name="spring-boot"></a>Spring Boot

Chcete-li se připojit ke zdrojům dat v aplikacích jarního spuštění, doporučujeme vytvořit připojovací řetězce a vložit je do souboru *application.properties.*

1. V části Konfigurace na stránce Služba aplikace nastavte název řetězce, vložte připojovací řetězec JDBC do pole hodnoty a nastavte typ na "Vlastní". Volitelně můžete nastavit tento připojovací řetězec jako slot nastavení.

    Tento připojovací řetězec je pro `CUSTOMCONNSTR_<your-string-name>`naši aplikaci přístupný jako proměnná prostředí s názvem . Například připojovací řetězec, který `CUSTOMCONNSTR_exampledb`jsme vytvořili výše, bude pojmenován .

2. V souboru *application.properties* natento připojovací řetězec s názvem proměnné prostředí. Pro náš příklad bychom použili následující.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

Další informace o tomto tématu naleznete v dokumentaci k [jarnímu spuštění týkající se přístupu k datům](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html) a [externalizovaných konfigurací.](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)

## <a name="use-redis-as-a-session-cache-with-tomcat"></a>Použití Redisu jako mezipaměti relace s Tomcatem

Aplikaci Tomcat můžete nakonfigurovat tak, aby používala externí úložiště relací, například [Azure Cache for Redis](/azure/azure-cache-for-redis/). To umožňuje zachovat stav relace uživatele (například data nákupního košíku), když je uživatel přenesen do jiné instance vaší aplikace, například když dojde k automatickému škálování, restartování nebo převzetí služeb při selhání.

Chcete-li používat Tomcat s Redis, musíte nakonfigurovat aplikaci tak, aby používala implementaci [PersistentManager.](https://tomcat.apache.org/tomcat-8.5-doc/config/manager.html) Následující kroky vysvětlují tento proces pomocí [Správce klíčových relací: redis-store](https://github.com/pivotalsoftware/session-managers/tree/master/redis-store) jako příklad.

1. Otevřete terminál Bash `<variable>=<value>` a slouží k nastavení každé z následujících proměnných prostředí.

    | Proměnná                 | Hodnota                                                                      |
    |--------------------------|----------------------------------------------------------------------------|
    | RESOURCEGROUP_NAME       | Název skupiny prostředků obsahující instanci služby App Service.       |
    | WEBAPP_NAME              | Název instance služby App Service.                                     |
    | WEBAPP_PLAN_NAME         | Název plánu služby App Service.                                         |
    | REGION                   | Název oblasti, kde je vaše aplikace hostovaná.                           |
    | REDIS_CACHE_NAME         | Název instance Azure Cache for Redis.                           |
    | REDIS_PORT               | Port SSL, na který vaše mezipaměť Redis naslouchá.                             |
    | REDIS_PASSWORD           | Primární přístupový klíč pro vaši instanci.                                  |
    | REDIS_SESSION_KEY_PREFIX | Hodnota, kterou zadáte k identifikaci klíčů relace, které pocházejí z vaší aplikace. |

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

    Název, port a přístup k informacím o klíči najdete na webu Azure Portal tak, že se podíváte do částí **Vlastnosti** nebo **Klíče přístupu** v instanci služby.

2. Vytvořte nebo aktualizujte soubor *src/main/webapp/META-INF/context.xml* aplikace s následujícím obsahem:

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

    Tento soubor určuje a konfiguruje implementaci správce relací pro vaši aplikaci. Používá proměnné prostředí, které jste nastavili v předchozím kroku, aby informace o účtu z vašich zdrojových souborů.

3. Pomocí protokolu FTP nahrajte soubor JAR správce relace do instance služby App Service a umístěte jej do adresáře */home/tomcat/lib.* Další informace najdete [v tématu Nasazení aplikace do služby Azure App Service pomocí FTP/S](https://docs.microsoft.com/azure/app-service/deploy-ftp).

4. Zakažte [soubor cookie spřažení relace](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) pro instanci služby App Service. Můžete to udělat z portálu Azure tak, že přejdete do aplikace a potom **nastavením konfigurace > obecné nastavení > afinitu ARR** k **off**. Alternativně můžete použít následující příkaz:

    ```azurecli
    az webapp update -g <resource group> -n <webapp name> --client-affinity-enabled false
    ```

    Ve výchozím nastavení bude služba App Service používat soubory cookie spřažení relací, aby zajistila, že požadavky klientů s existujícími relacemi budou směrovány do stejné instance vaší aplikace. Toto výchozí chování nevyžaduje žádnou konfiguraci, ale nemůže zachovat stav relace uživatele při restartování instance aplikace nebo při přesměrování provozu do jiné instance. Pokud [zakážete existující konfiguraci afinity instance ARR,](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) aby se vypnulo směrování založené na souborech cookie relace, povolíte, aby nakonfigurované úložiště relací fungovalo bez rušení.

5. Přejděte do části **Vlastnosti** instance služby App Service a vyhledejte **další odchozí IP adresy**. Představují všechny možné odchozí IP adresy pro vaši aplikaci. Zkopírujte je pro použití v dalším kroku.

6. Pro každou IP adresu vytvořte pravidlo brány firewall v instanci Azure Cache for Redis. Můžete to udělat na portálu Azure z brány **firewall** části instance Redis. Zadejte jedinečný název pro každé pravidlo a nastavte **počáteční ip adresu** a koncové hodnoty IP **adres** na stejnou adresu IP.

7. Přejděte do části **Upřesnit nastavení** instance Redis a nastavte **povolit přístup pouze přes SSL** na **ne**. To umožňuje vaší instanci služby App Service komunikovat s vaší mezipaměti Redis prostřednictvím infrastruktury Azure.

8. Aktualizujte `azure-webapp-maven-plugin` konfiguraci v souboru *pom.xml* aplikace tak, aby odkazovala na informace o účtu Redis. Tento soubor používá dříve nastavené proměnné prostředí, aby informace o účtu zůstaly mimo zdrojové soubory.

    V případě potřeby změňte `1.7.0` na aktuální verzi [modulu plug-in Maven pro Azure App Service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme).

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

9. Znovu sestavte a znovu nasaďte aplikaci.

    ```bash
    mvn package -DskipTests azure-webapp:deploy
    ```

Vaše aplikace teď bude používat mezipaměť Redis pro správu relací.

Ukázka, kterou můžete použít k testování těchto pokynů, najdete [v měřítku stav-java-web-app-on-azure](https://github.com/Azure-Samples/scaling-stateful-java-web-app-on-azure) úložiště na GitHubu.

## <a name="docker-containers"></a>Kontejnery Dockeru

Pokud chcete ve svých kontejnerech používat sadu Zulu JDK podporovanou Azure, nezapomeňte stáhnout a použít předem sestavené bitové kopie, `Dockerfile` jak je zdokumentováno z podporované stránky pro stažení [Azul Zulu Enterprise for Azure,](https://www.azul.com/downloads/azure-only/zulu/) nebo použít příklady z úložiště Microsoft Java [GitHub](https://github.com/Microsoft/java/tree/master/docker).

## <a name="statement-of-support"></a>Prohlášení o podpoře

### <a name="runtime-availability"></a>Dostupnost runtime

App Service pro Linux podporuje dva runtime pro spravovaný hosting webových aplikací Java:

- [Kontejner Servlet Tomcat](https://tomcat.apache.org/) pro spouštění aplikací zabalených jako soubory webového archivu (WAR). Podporované verze jsou 8.5 a 9.0.
- Java SE runtime prostředí pro spouštění aplikací zabalených jako java archiv (JAR) soubory. Podporované verze jsou Java 8 a 11.

### <a name="jdk-versions-and-maintenance"></a>JDK verze a údržba

Azul Zulu Enterprise builds of OpenJDK jsou bezplatné, víceplatformové a produkční distribuce OpenJDK pro Azure a Azure Stack podporované microsoftem a Azul Systems. Obsahuje všechny komponenty pro vytváření a spouštění aplikací Java SE. JDK můžete nainstalovat z [java JDK instalace](https://aka.ms/azure-jdks).

Podporované sady JDK jsou automaticky čtvrtletně opraveny v lednu, dubnu, červenci a říjnu každého roku.

### <a name="security-updates"></a>Aktualizace zabezpečení

Opravy a opravy hlavních bezpečnostních chyb budou vydány, jakmile budou k dispozici od společnosti Azul Systems. "Hlavní" chyba zabezpečení je definována základním skóre 9,0 nebo vyšším v [systému společného systému hodnocení chyb zabezpečení NIST verze 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Odsazení a odchod do důchodu

Pokud bude podporovaný modul java runtime vyřazen, vývojáři Azure pomocí ovlivněného modulu runtime budou mít oznámení o vyřazení nejméně šest měsíců před vyřazením modulu runtime.

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Další kroky

Navštivte Centrum [vývojářů Azure pro Javu](/java/azure/) a najděte rychlou verzi, kurzy a referenční dokumentaci k Azure pro Javu.

Obecné otázky týkající se používání služby App Service pro Linux, které nejsou specifické pro vývoj Java jsou zodpovězeny v [App Service Linux FAQ](app-service-linux-faq.md).
