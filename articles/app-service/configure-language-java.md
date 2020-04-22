---
title: Konfigurace aplikací pro Windows Java
description: Přečtěte si, jak nakonfigurovat aplikace Java tak, aby se spouštěla na instancích virtuálních aplikací Windows ve službě Azure App Service. Tento článek ukazuje nejběžnější úlohy konfigurace.
keywords: azure app service, webová aplikace, windows, oss, java
author: jasonfreeberg
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: cebe5564767ee345c1aea96b6ac54b9398c3e9a3
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733033"
---
# <a name="configure-a-windows-java-app-for-azure-app-service"></a>Konfigurace aplikace Windows Java pro azure app service

Azure App Service umožňuje vývojářům jazyka Java rychle vytvářet, nasazovat a škálovat své webové aplikace Tomcat na plně spravované službě založené na Windows. Nasazujte aplikace pomocí modulů plug-in Maven z příkazového řádku nebo v editorech, jako je IntelliJ, Eclipse nebo Visual Studio Code.

Tato příručka obsahuje klíčové koncepty a pokyny pro vývojáře Javy, kteří používají službu App Service. Pokud jste službu Azure App Service nikdy nepoužívali, měli byste si nejprve přečíst [rychlý start v jazyce Java.](app-service-web-get-started-java.md) Obecné otázky týkající se používání služby App Service, které nejsou specifické pro vývoj jazyka Java, jsou zodpovězeny v [nejčastějších dotazech ke službě App Service pro Windows](faq-configuration-and-management.md).

## <a name="deploying-your-app"></a>Nasazení aplikace

K nasazení souborů .war můžete použít [modul plug-in Azure Web App pro Maven.](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) Nasazení s oblíbenými inežy je taky podporované pomocí [sady Azure Toolkit for IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij) nebo [Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse).

V opačném případě bude metoda nasazení záviset na typu archivu:

- Chcete-li nasadit soubory .war `/api/wardeploy/` do aplikace Tomcat, použijte koncový bod k odeslání archivního souboru. Další informace o tomto rozhraní API naleznete v [této dokumentaci](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file).
- Chcete-li nasadit soubory .jar `/api/zipdeploy/` do jazyka Java SE, použijte koncový bod webu Kudu. Další informace o tomto rozhraní API naleznete v [této dokumentaci](https://docs.microsoft.com/azure/app-service/deploy-zip#rest).

Nenasazujte svou .war pomocí FTP. Nástroj FTP je určen k nahrávání spouštěcích skriptů, závislostí nebo jiných souborů runtime. Není to optimální volba pro nasazení webových aplikací.

## <a name="logging-and-debugging-apps"></a>Protokolování a ladění aplikací

Sestavy výkonu, vizualizace provozu a kontroly stavu jsou k dispozici pro každou aplikaci prostřednictvím portálu Azure. Další informace najdete v [tématu Přehled diagnostiky služby Azure App Service](overview-diagnostics.md).

### <a name="use-flight-recorder"></a>Použití letového zapisovače

Všechny java runtimes na App Service pomocí Azul JVMs jsou dodávány s Zulu Flight Recorder. Můžete použít k záznamu událostí na úrovni JVM, systému a Javy ke sledování chování a řešení problémů v aplikacích Java.

Chcete-li provést časovaný záznam, budete potřebovat PID (Process ID) aplikace Java. Chcete-li najít PID, otevřete prohlížeč webu SCM vaší webové aplikace na adrese https://<název vašeho webu>.scm.azurewebsites.net/ProcessExplorer/. Na této stránce jsou zobrazeny spuštěné procesy ve webové aplikaci. Vyhledejte proces s názvem "java" v tabulce a zkopírujte odpovídající PID (ID procesu).

Dále otevřete **konzolu ladění** v horním panelu nástrojů webu SCM a spusťte následující příkaz. Nahraďte `<pid>` ID procesu, které jste zkopírovali dříve. Tento příkaz spustí 30 sekund profiler nahrávání aplikace Java `timed_recording_example.jfr` a `D:\home` generovat soubor pojmenovaný v adresáři.

```
jcmd <pid> JFR.start name=TimedRecording settings=profile duration=30s filename="D:\home\timed_recording_example.JFR"
```

Další informace naleznete v [odkazu na příkaz Jcmd](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190).

#### <a name="analyze-jfr-files"></a>Analýza `.jfr` souborů

Pomocí [ftps](deploy-ftp.md) stáhnout soubor JFR do místního počítače. Chcete-li analyzovat soubor JFR, stáhněte a nainstalujte [Zulu Mission Control](https://www.azul.com/products/zulu-mission-control/). Pokyny k řízení mise Zulu naleznete v [dokumentaci azul](https://docs.azul.com/zmc/) a [pokyny k instalaci](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control).

### <a name="stream-diagnostic-logs"></a>Streamování diagnostických protokolů

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

Další informace najdete [v tématu Stream protokoly v prostředí Cloud Shell](troubleshoot-diagnostic-logs.md#in-cloud-shell).

### <a name="app-logging"></a>Protokolování aplikací

Povolte [protokolování aplikací](troubleshoot-diagnostic-logs.md#enable-application-logging-windows) prostřednictvím portálu Azure nebo [rozhraní příkazového příkazu Azure](/cli/azure/webapp/log#az-webapp-log-config) a nakonfigurujte službu App Service tak, aby zapisovala standardní výstup konzoly vaší aplikace a standardní datové proudy chyb konzoly do místního souborového systému nebo úložiště objektů blob Azure. Protokolování k místní instanci souborového systému služby App Service je zakázáno 12 hodin po konfiguraci. Pokud potřebujete delší uchovávání informací, nakonfigurujte aplikaci pro zápis výstupu do kontejneru úložiště objektů Blob. Vaše java a tomcat app logy najdete v adresáři */LogFiles/Application/.*

Pokud vaše aplikace používá [Logback](https://logback.qos.ch/) nebo [Log4j](https://logging.apache.org/log4j) pro trasování, můžete tyto trasování předat ke kontrole do Azure Application Insights pomocí pokynů ke konfiguraci architektury protokolování v [prozkoumat protokoly trasování java v Application Insights](/azure/application-insights/app-insights-java-trace-logs).


## <a name="customization-and-tuning"></a>Přizpůsobení a ladění

Azure App Service podporuje mimo pole ladění a přizpůsobení prostřednictvím portálu Azure a rozhraní příkazového příkazového příkazového příkazu. V následujících článcích naleznete konfiguraci webové aplikace, která není specifická pro Jazykovou javu:

- [Konfigurace nastavení aplikace](configure-common.md#configure-app-settings)
- [Nastavení vlastní domény](app-service-web-tutorial-custom-domain.md)
- [Konfigurace vazeb TLS](configure-ssl-bindings.md)
- [Přidání sítě CDN](../cdn/cdn-add-to-web-app.md)
- [Konfigurace webu Kudu](https://github.com/projectkudu/kudu/wiki/Configurable-settings)

### <a name="set-java-runtime-options"></a>Nastavení možností runtime v jazyce Java

Chcete-li nastavit přidělenou paměť nebo jiné možnosti běhu `JAVA_OPTS` JVM, vytvořte nastavení [aplikace](configure-common.md#configure-app-settings) s názvem s možnostmi. Služba App Service předá toto nastavení jako proměnnou prostředí prostředí prostředí za běhu Java při spuštění.

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

### <a name="pre-compile-jsp-files"></a>Předkompilace souborů JSP

Chcete-li zlepšit výkon aplikací Tomcat, můžete zkompilovat soubory JSP před nasazením do služby App Service. Můžete použít [plugin Maven](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) poskytované Apache Sling, nebo pomocí tohoto [ant sestavení souboru](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation).

## <a name="secure-applications"></a>Zabezpečené aplikace

Java aplikace spuštěné ve službě App Service mají stejnou sadu [doporučených postupů zabezpečení](/azure/security/security-paas-applications-using-app-services) jako ostatní aplikace.

### <a name="authenticate-users-easy-auth"></a>Ověření uživatelů (Jednoduché ověření)

Nastavte ověřování aplikací na webu Azure Portal pomocí možnosti **Ověřování a autorizace.** Odtud můžete povolit ověřování pomocí Azure Active Directory nebo sociální přihlášení, jako je Facebook, Google nebo GitHub. Konfigurace portálu Azure funguje jenom při konfiguraci jednoho zprostředkovatele ověřování. Další informace [najdete v tématu Konfigurace aplikace App Service tak, aby používala přihlášení služby Azure Active Directory,](configure-authentication-provider-aad.md) a související články pro jiné poskytovatele identit. Pokud potřebujete povolit více poskytovatelů přihlášení, postupujte podle pokynů v článku [ověřování vlastní služby App Service.](app-service-authentication-how-to.md)

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

### <a name="configure-tlsssl"></a>Konfigurace protokolu TLS/SSL

Postupujte podle pokynů v [secure vlastní název DNS s vazbou TLS ve službě Azure App Service](configure-ssl-bindings.md) nahrát existující certifikát TLS/SSL a svázat jej s názvem domény vaší aplikace. Ve výchozím nastavení aplikace bude stále povolit připojení HTTP postupujte podle konkrétních kroků v kurzu k vynucení SSL a TLS.

### <a name="use-keyvault-references"></a>Použití odkazů KeyVault

[Azure KeyVault](../key-vault/general/overview.md) poskytuje centralizovanou správu tajných klíčů se zásadami přístupu a historií auditu. Můžete ukládat tajné klíče (například hesla nebo připojovací řetězce) v KeyVault a přístup k těmto tajným kódům ve vaší aplikaci prostřednictvím proměnných prostředí.

Nejprve postupujte podle pokynů pro [udělení přístupu aplikace k trezoru klíčů](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) a [vytvoření odkazu keyvault na váš tajný klíč v nastavení aplikace](app-service-key-vault-references.md#reference-syntax). Můžete ověřit, že odkaz řeší na tajný klíč vytištěním proměnné prostředí při vzdáleném přístupu k terminálu služby App Service.

Chcete-li tyto tajné klíče vložit do konfiguračního`${MY_ENV_VAR}`souboru Spring nebo Tomcat, použijte syntaxi proměnné vstřikování prostředí ( ). Konfigurační soubory pružinnaleznete v této dokumentaci o [externalizovaných konfiguracích](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).


## <a name="configure-apm-platforms"></a>Konfigurace platforem APM

Tato část ukazuje, jak připojit java aplikace nasazené ve službě Azure App Service na Linuxu s platformami pro sledování výkonu aplikací NewRelic a AppDynamics (APM).

### <a name="configure-new-relic"></a>Konfigurace nové relikvie

1. Vytvořte si nový účet Relic v [NewRelic.com](https://newrelic.com/signup)
2. Stáhněte si agenta Java z NewRelic, bude mít název souboru podobný *newrelic-java-x.x.x.zip*.
3. Zkopírujte licenční klíč, budete ho potřebovat k pozdější konfiguraci agenta.
4. Pomocí [konzoly Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console) vytvořte nový adresář */home/site/wwwroot/apm*.
5. Nahrajte rozbalené nové soubory agentů Relic Java do adresáře pod */home/site/wwwroot/apm*. Soubory pro vašeho agenta by měly být v */home/site/wwwroot/apm/newrelic*.
6. Upravte soubor YAML na adrese */home/site/wwwroot/apm/newrelic/newrelic.yml* a nahraďte zástupnou hodnotu licence vlastním licenčním klíčem.
7. Na webu Azure Portal vyhledejte svou aplikaci ve službě App Service a vytvořte nové nastavení aplikace.
    - Pokud vaše aplikace používá **Java SE**, `JAVA_OPTS` vytvořte `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`proměnnou prostředí s názvem s hodnotou .
    - Pokud používáte **Tomcat**, vytvořte `CATALINA_OPTS` proměnnou `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`prostředí s názvem s hodnotou .

### <a name="configure-appdynamics"></a>Konfigurace aplikace AppDynamics

1. Vytvoření účtu AppDynamics na [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Stáhněte si agenta Java z webu AppDynamics, název souboru bude podobný *AppServerAgent-x.x.x.xxxxx.zip*
3. Pomocí [konzoly Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console) vytvořte nový adresář */home/site/wwwroot/apm*.
4. Nahrajte soubory agentů Java do adresáře pod */home/site/wwwroot/apm*. Soubory pro vašeho agenta by měly být v */home/site/wwwroot/apm/appdynamics*.
5. Na webu Azure Portal vyhledejte svou aplikaci ve službě App Service a vytvořte nové nastavení aplikace.
    - Pokud používáte **Java SE**, vytvořte `JAVA_OPTS` proměnnou `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` `<app-name>` prostředí s názvem s hodnotou, kde je název služby App Service.
    - Pokud používáte **Tomcat**, vytvořte `CATALINA_OPTS` proměnnou `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` prostředí `<app-name>` s názvem s hodnotou, kde je název služby App Service.

>  Pokud již máte proměnnou `JAVA_OPTS` `CATALINA_OPTS`prostředí pro `-javaagent:/...` nebo , připojte možnost na konec aktuální hodnoty.

## <a name="data-sources"></a>Zdroje dat

### <a name="tomcat"></a>Tomcat

Tyto pokyny platí pro všechna připojení databáze. Zástupné symboly budete muset vyplnit názvem třídy ovladače vybrané databáze a souborem JAR. Za předpokladu, je tabulka s názvy tříd a ovladače ke stažení pro běžné databáze.

| databáze   | Název třídy ovladače                             | Ovladač JDBC                                                                      |
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

#### <a name="finalize-configuration"></a>Dokončení konfigurace

Nakonec umístíme jar ovladače do cesty třídy Tomcat a restartujeme službu App Service. Ujistěte se, že soubory ovladačů JDBC jsou k dispozici pro classloader Tomcat jejich umístěním do adresáře */home/tomcat/lib.* (Vytvořte tento adresář, pokud ještě neexistuje.) Chcete-li tyto soubory nahrát do instance služby App Service, proveďte následující kroky:

1. V [prostředí Cloud Shell](https://shell.azure.com)nainstalujte rozšíření webapp:

    ```azurecli-interactive
    az extension add -–name webapp
    ```

2. Spusťte následující příkaz příkazu příkazu příkazu cli a vytvořte tunel SSH z místního systému do služby App Service:

    ```azurecli-interactive
    az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
    ```

3. Připojte se k místnímu portu tunelového propojení s klientem SFTP a nahrajte soubory do složky */home/tomcat/lib.*

Alternativně můžete použít klienta FTP k nahrání ovladače JDBC. Postupujte podle těchto [pokynů pro získání pověření FTP](deploy-configure-credentials.md).

## <a name="configuring-tomcat"></a>Konfigurace Kocoura

Chcete-li upravit `server.xml` soubory Tomcat nebo jiné konfigurační soubory, poznamenejte si nejprve hlavní verzi aplikace Tomcat na portálu.

1. Pomocí `env` příkazu najděte domovský adresář Tomcat pro vaši verzi. Vyhledejte proměnnou prostředí, `AZURE_TOMCAT`která začíná a odpovídá hlavní verzi. Odkazuje například `AZURE_TOMCAT85_HOME` na adresář Tomcat pro Tomcat 8.5.
1. Jakmile jste identifikovali domovský adresář Tomcat pro vaši `D:\home`verzi, zkopírujte konfigurační adresář do aplikace . Například pokud `AZURE_TOMCAT85_HOME` má hodnotu `D:\Program Files (x86)\apache-tomcat-8.5.37`, nová cesta zkopírovaného adresáře by `D:\home\apache-tomcat-8.5.37`.

Nakonec restartujte službu App Service. Vaše nasazení by `D:\home\site\wwwroot\webapps` měla přejít stejně jako dříve.

## <a name="configure-java-se"></a>Konfigurace jazyka Java SE

Při spuštění . JAR aplikace na Java `server.port` SE v systému Windows, je předán jako možnost příkazového řádku, jak vaše aplikace začíná. Port HTTP můžete ručně přeložit z proměnné `HTTP_PLATFORM_PORT`prostředí . Hodnota této proměnné prostředí bude port HTTP, na který by měla aplikace naslouchat. 

## <a name="java-runtime-statement-of-support"></a>Java runtime prohlášení o podpoře

### <a name="jdk-versions-and-maintenance"></a>JDK verze a údržba

Podporovaná java development kit (JDK) podporovaná Sadou Azure je [Zulu,](https://www.azul.com/downloads/azure-only/zulu/) která je poskytována prostřednictvím [azul systems](https://www.azul.com/).

Hlavní aktualizace verzí budou k dispozici prostřednictvím nových možností runtime ve službě Azure App Service pro Windows. Zákazníci aktualizují tyto novější verze javy konfigurací jejich nasazení služby App Service a jsou zodpovědní za testování a zajištění hlavní aktualizace splňuje jejich potřeby.

Podporované sady JDK jsou automaticky čtvrtletně opraveny v lednu, dubnu, červenci a říjnu každého roku. Další informace o Javě v Azure najdete v [tomto dokumentu podpory](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-long-term-support).

### <a name="security-updates"></a>Aktualizace zabezpečení

Opravy a opravy hlavních bezpečnostních chyb budou vydány, jakmile budou k dispozici od společnosti Azul Systems. "Hlavní" chyba zabezpečení je definována základním skóre 9,0 nebo vyšším v [systému společného systému hodnocení chyb zabezpečení NIST verze 2](https://nvd.nist.gov/cvss.cfm).

Tomcat 8.0 dosáhl [konce života (EOL) od září 30, 2018](https://tomcat.apache.org/tomcat-80-eol.html). Zatímco runtime je stále dostupná ve službě Azure App Service, Azure nebude používat aktualizace zabezpečení pro Tomcat 8.0. Pokud je to možné, migrujte aplikace na Tomcat 8.5 nebo 9.0. Tomcat 8.5 a 9.0 jsou k dispozici ve službě Azure App Service. Další informace naleznete na [oficiálních stránkách společnosti Tomcat.](https://tomcat.apache.org/whichversion.html) 

### <a name="deprecation-and-retirement"></a>Odsazení a odchod do důchodu

Pokud bude podporovaný modul java runtime vyřazen, vývojáři Azure pomocí ovlivněného modulu runtime budou mít oznámení o vyřazení nejméně šest měsíců před vyřazením modulu runtime.

### <a name="local-development"></a>Místní vývoj

Vývojáři si mohou stáhnout Production Edition Azul Zulu Enterprise JDK pro místní rozvoj z [webu pro stahování Azul](https://www.azul.com/downloads/azure-only/zulu/).

### <a name="development-support"></a>Rozvojová podpora

Odborná podpora pro [Azul Zulu JDK podporovaná Azure](https://www.azul.com/downloads/azure-only/zulu/) je dostupná prostřednictvím Microsoftu při vývoji pro Azure nebo [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) s [kvalifikovaným plánem podpory Azure](https://azure.microsoft.com/support/plans/).

### <a name="runtime-support"></a>Podpora runtime

Vývojáři mohou [otevřít problém](/azure/azure-portal/supportability/how-to-create-azure-support-request) s JDK Azul Zulu prostřednictvím podpory Azure, pokud mají kvalifikovaný [plán podpory](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Další kroky

Toto téma obsahuje prohlášení java runtime o podpoře služby Azure App Service ve Windows.

- Další informace o hostování webových aplikací pomocí služby Azure App Service najdete v [tématu Přehled služby App Service](overview.md).
- Informace o vývoji javy ve službě Azure najdete v [tématu Azure for Java Dev Center](https://docs.microsoft.com/java/azure/?view=azure-java-stable).
