---
title: Konfigurace aplikací v Javě v Linuxu – Azure App Service | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat aplikace v Javě spuštěné v Azure App Service v Linuxu.
keywords: azure app service, web app, linux, oss, java
services: app-service
author: rloutlaw
manager: angerobe
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 03/28/2019
ms.author: routlaw
ms.custom: seodec18
ms.openlocfilehash: b659c076974b0659c645c9b6460e458dfac8974a
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2019
ms.locfileid: "59551229"
---
# <a name="configure-a-linux-java-app-for-azure-app-service"></a>Konfigurace aplikace v Javě v Linuxu pro Azure App Service

Azure App Service v Linuxu umožňuje vývojářům rychle sestavit, nasadit a škálovat své Tomcat v jazyce Java nebo Java Standard Edition (SE) zabalené webové aplikace na plně spravované služby založené na Linuxu. Nasazujte aplikace s moduly plug-in Maven z příkazového řádku nebo v editoru, jako je IntelliJ, Eclipse nebo Visual Studio Code.

Tato příručka obsahuje klíčové koncepty a pokyny pro vývojáře v Javě, které používají integrované kontejneru Linuxu ve službě App Service. Pokud jste nikdy použili službu Azure App Service, postupujte podle [Java quickstart](quickstart-java.md) a [Java s PostgreSQL kurzu](tutorial-java-enterprise-postgresql-app.md) první.

## <a name="logging-and-debugging-apps"></a>Protokolování a ladění aplikací

Sestavy o výkonu, provoz vizualizace a kontroly stavu jsou k dispozici pro každou aplikaci na webu Azure portal. Další informace najdete v tématu [Přehled diagnostiky služby Azure App Service](../overview-diagnostics.md).

### <a name="ssh-console-access"></a>Přístup ke konzole SSH

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>Streamování diagnostických protokolů

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

Další informace najdete v tématu [streamování protokolů pomocí Azure CLI](../troubleshoot-diagnostic-logs.md#streaming-with-azure-cli).

### <a name="app-logging"></a>Protokolování aplikace

Povolit [protokolování aplikací](../troubleshoot-diagnostic-logs.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#enablediag) prostřednictvím webu Azure portal nebo [rozhraní příkazového řádku Azure](/cli/azure/webapp/log#az-webapp-log-config) ke konfiguraci služby App Service, zaznamená se do místní výstup standardní konzole vaší aplikace a datových proudů chyba standardní konzole systém souborů nebo úložiště objektů Blob v Azure. Instance je zakázaná 12 hodin po dokončení konfigurace protokolování do místního systému souborů služby App Service. Pokud budete potřebovat delší dobu uchování, nakonfigurujte aplikaci zapisovat výstup do kontejneru úložiště objektů Blob.

Pokud vaše aplikace používá [Logback](https://logback.qos.ch/) nebo [Log4j](https://logging.apache.org/log4j) pro trasování, můžete předat dál trasování ke kontrole do služby Azure Application Insights pomocí pokyny ke konfiguraci protokolování framework v [Protokoly trasování prozkoumejte Java ve službě Application Insights](/azure/application-insights/app-insights-java-trace-logs).

## <a name="customization-and-tuning"></a>Přizpůsobení a optimalizace

Azure App Service pro Linux podporuje pole vyladění a přizpůsobení prostřednictvím webu Azure portal a rozhraní příkazového řádku. Projděte si následující články konfigurace Javy konkrétní webové aplikace:

- [Konfigurace nastavení služby App Service](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Nastavení vlastní domény](../app-service-web-tutorial-custom-domain.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Povolení protokolu SSL](../app-service-web-tutorial-custom-ssl.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Přidání sítě CDN](../../cdn/cdn-add-to-web-app.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)

### <a name="set-java-runtime-options"></a>Nastavte možnosti modulu runtime Java

V prostředí Tomcat i Java SE nastavit přidělené paměti nebo jiné možnosti modulu runtime JVM, nastavte JAVA_OPTS, jak je znázorněno níže jako [nastavení aplikace](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings). App Service Linux předá toto nastavení jako proměnnou prostředí Java runtime při spuštění.

Na webu Azure Portal v části **nastavení aplikace** pro webovou aplikaci, vytvořte nové nastavení aplikace s názvem `JAVA_OPTS` , který obsahuje další nastavení, jako například `$JAVA_OPTS -Xms512m -Xmx1204m`.

Pokud chcete nakonfigurovat nastavení aplikace, které z modulu plug-in Azure App Service Linux Maven, add v sekci modul plug-in Azure nastavení/hodnota značek. Následující příklad nastaví konkrétní minimální a maximální velikost haldy Java:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>$JAVA_OPTS -Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

S jednou aplikací jeden nasazovací slot v jejich plán služby App Service mohou vývojáři tyto možnosti:

- Instance B1 a S1:-Xms1024m-Xmx1024m
- Instance B2 a S2:-Xms3072m-Xmx3072m
- Instance B3 a S3:-Xms6144m-Xmx6144m


Při nastavení haldy ladění aplikace, projděte si podrobnosti vašeho plánu služby App Service a vezměte v úvahu více aplikací a slot nasazení je potřeba najít optimální přidělení paměti.

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

Na webu Azure Portal v části **nastavení aplikace** pro webovou aplikaci, vytvořte nové nastavení aplikace s názvem `JAVA_OPTS` s hodnotou `$JAVA_OPTS -Dfile.encoding=UTF-8`.

Alternativně můžete nakonfigurovat nastavení aplikace pomocí modulu plug-in App Service Maven. Přidáte nastavení názvu a hodnoty značek v konfiguraci modulu plug-in:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>$JAVA_OPTS -Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

## <a name="secure-applications"></a>Zabezpečené aplikace

Aplikace Java spuštěná ve službě App Service pro Linux mají stejnou sadu [osvědčené postupy zabezpečení](/azure/security/security-paas-applications-using-app-services) jako ostatní aplikace. 

### <a name="authenticate-users"></a>Ověřování uživatelů

Nastavení ověřování aplikace na webu Azure Portal s **ověřování a autorizace** možnost. Odtud můžete povolit ověřování pomocí Azure Active Directory nebo přihlašování přes sociální sítě jako je Facebook, Google nebo GitHub. Konfigurace Azure portal funguje pouze v případě konfigurace ověřování jednotným poskytovatele. Další informace najdete v tématu [konfigurace aplikace App Service pro použití přihlášení Azure Active Directory](../configure-authentication-provider-aad.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) a související články pro jiné zprostředkovatele identity.

Pokud je potřeba povolit více poskytovatelů přihlásit, postupujte podle pokynů [přizpůsobit ověřování pomocí služby App Service](../app-service-authentication-how-to.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) článku.

 Aplikace Spring Boot vývojáři mohou použít [Azure Active Directory Spring Boot starter](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) k zabezpečení aplikace pomocí známých poznámky k zabezpečení Spring a rozhraní API.

### <a name="configure-tlsssl"></a>Konfigurace TLS/SSL

Postupujte podle pokynů [vytvoření vazby existujícího vlastního certifikátu SSL](../app-service-web-tutorial-custom-ssl.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) nahrát stávající certifikát SSL a navázat jej na název domény vaší aplikace. Ve výchozím nastavení vaše aplikace stále umožňuje HTTP připojení – postupujte podle konkrétních kroků v kurzu k vynucení šifrování protokolu SSL a TLS.

## <a name="configure-apm-platforms"></a>Konfigurovat platformy APM

Tato část ukazuje, jak připojit aplikace v Javě nasazené ve službě Azure App Service v Linuxu pomocí nástrojů NewRelic a AppDynamics pro monitorování výkonu aplikací (APM) platformy.

[Nakonfigurujte nástroj společnosti New Relic](#configure-new-relic)
[nakonfigurovat AppDynamics](#configure-appdynamics)

### <a name="configure-new-relic"></a>Nakonfigurujte nástroj společnosti New Relic

1. Vytvořit účet NewRelic na [NewRelic.com](https://newrelic.com/signup)
2. Stáhněte agenta Java z nástrojů NewRelic, bude mít název souboru, který je podobný `newrelic-java-x.x.x.zip`.
3. Zkopírování klíče licence, ho budete potřebovat ke konfiguraci agenta později.
4. [Připojte se přes SSH vaší instance služby App Service](app-service-linux-ssh-support.md) a vytvořte nový adresář `/home/site/wwwroot/apm`.
5. Rozbalené soubory agenta NewRelic Java nahrát do adresáře v rámci `/home/site/wwwroot/apm`. Soubory pro agenta by měla být v `/home/site/wwwroot/apm/newrelic`.
6. Upravte soubor YAML na `/home/site/wwwroot/apm/newrelic/newrelic.yml` a nahraďte hodnotu zástupného symbolu licence vlastní licenční klíč.
7. Na webu Azure Portal přejděte do vaší aplikace ve službě App Service a vytvořit nové nastavení aplikace.
    - Pokud vaše aplikace používá **Java SE**, vytvořte proměnnou prostředí s názvem `JAVA_OPTS` s hodnotou `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Pokud používáte **Tomcat**, vytvořte proměnnou prostředí s názvem `CATALINA_OPTS` s hodnotou `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Pokud používáte **WildFly**, naleznete v dokumentaci k špičkové funkce New Relicu [tady](https://docs.newrelic.com/docs/agents/java-agent/additional-installation/wildfly-version-11-installation-java) pokyny týkající se instalace agenta Java a JBoss konfigurace.
    - Pokud už máte proměnnou prostředí pro `JAVA_OPTS` nebo `CATALINA_OPTS`, připojte `javaagent` možnost na konec aktuální hodnotu.

### <a name="configure-appdynamics"></a>Konfigurace AppDynamics

1. Vytvoření účtu AppDynamics na [AppDynamics.com](https://www.appdynamics.com/community/register/)
1. Stáhněte agenta Java z webu AppDynamics, názvem souboru bude podobný `AppServerAgent-x.x.x.xxxxx.zip`
1. [Připojte se přes SSH vaší instance služby App Service](app-service-linux-ssh-support.md) a vytvořte nový adresář `/home/site/wwwroot/apm`.
1. Nahrát soubory agenta Java do adresáře v rámci `/home/site/wwwroot/apm`. Soubory pro agenta by měla být v `/home/site/wwwroot/apm/appdynamics`.
1. Na webu Azure Portal přejděte do vaší aplikace ve službě App Service a vytvořit nové nastavení aplikace.
    - Pokud používáte **Java SE**, vytvořte proměnnou prostředí s názvem `JAVA_OPTS` s hodnotou `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` kde `<app-name>` je název vaší služby App Service.
    - Pokud používáte **Tomcat**, vytvořte proměnnou prostředí s názvem `CATALINA_OPTS` s hodnotou `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` kde `<app-name>` je název vaší služby App Service.
    - Pokud používáte **WildFly**, naleznete v dokumentaci k AppDynamics [tady](https://docs.appdynamics.com/display/PRO45/JBoss+and+Wildfly+Startup+Settings) pokyny týkající se instalace agenta Java a JBoss konfigurace.

## <a name="configure-tomcat"></a>Konfigurace Tomcat

### <a name="connect-to-data-sources"></a>Připojení ke zdrojům dat

>[!NOTE]
> Pokud vaše aplikace používá Spring Framework nebo Spring Boot, můžete nastavit informace o připojení databáze pro Spring Data JPA jako proměnné prostředí [ve vlastnosti souboru aplikace]. Pak pomocí [nastavení aplikace](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings) definovat tyto hodnoty pro vaši aplikaci v portálu Azure portal nebo rozhraní příkazového řádku.

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

Nebo nastavte proměnné prostředí v okně "Nastavení aplikace" na webu Azure Portal.

Dále určete, pokud zdroj dat by měla být k dispozici pro jednu aplikaci nebo pro všechny aplikace spuštěné na Tomcat servlet.

#### <a name="application-level-data-sources"></a>Zdroje dat na úrovni aplikace

1. Vytvoření `context.xml` soubor `META-INF/` adresáře vašeho projektu. Vytvořte `META-INF/` adresáře, pokud neexistuje.

2. V `context.xml`, přidejte `Context` prvek, který chcete propojit zdroje dat na adresu JNDI. Nahradit `driverClassName` zástupný symbol s názvem třídy ve vašem ovladači z výše uvedené tabulky.

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

3. Aktualizace vaší aplikace `web.xml` budou používat zdroj dat ve vaší aplikaci.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>Sdílené prostředky na úrovni serveru

1. Zkopírujte obsah `/usr/local/tomcat/conf` do `/home/tomcat/conf` ve vaší App Service pro Linux instance pomocí protokolu SSH, pokud konfigurace existuje ještě nemáte.
    ```
    mkdir -p /home/tomcat
    cp -a /usr/local/tomcat/conf /home/tomcat/conf
    ```

2. Přidat element kontextu v vaše `server.xml` v rámci `<Server>` elementu.

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

3. Aktualizace vaší aplikace `web.xml` budou používat zdroj dat ve vaší aplikaci.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="finalize-configuration"></a>Dokončení konfigurace

A konečně umístěte JAR ovladač v cestě Tomcat a restartujte službu App Service.

1. Zajistěte, aby byly soubory ovladačů JDBC k dispozici pro Tomcat classloader tak, že je `/home/tomcat/lib` adresáře. (Pokud ještě neexistuje, vytvořte tento adresář.) Pokud chcete nahrát tyto soubory k vaší instanci služby App Service, postupujte následovně:
    1. V [Cloud Shell](https://shell.azure.com), nainstalovat rozšíření webové aplikace:

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. Spusťte následující příkaz rozhraní příkazového řádku k vytvoření tunelu SSH z místního systému do služby App Service:

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. Připojení na místní port tunelového propojení s vašeho klienta protokolu SFTP a odesílání souborů do `/home/tomcat/lib` složky.

    Alternativně můžete klienta FTP k nahrání ovladač JDBC. Postupujte podle těchto [pokyny k získání údajů FTP](../deploy-configure-credentials.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

2. Pokud jste nevytvořili zdroj dat na úrovni serveru, restartujte aplikaci App Service pro Linux. Tomcat resetuje `CATALINA_HOME` k `/home/tomcat/conf` a použít aktualizovanou konfiguraci.

## <a name="configure-wildfly-server"></a>Konfigurace serveru WildFly

[Škálování pomocí služby App Service](#scale-with-app-service)
[vlastní nastavení konfigurace aplikačního serveru](#customize-application-server-configuration)
[moduly a závislosti](#modules-and-dependencies)
[dat zdroje](#data-sources)
[povolit zasílání zpráv poskytovatelé](#enable-messaging-providers)
[konfigurovat ukládání do mezipaměti relace správy](#configure-session-management-caching)

### <a name="scale-with-app-service"></a>Škálování pomocí služby App Service

WildFly aplikačního serveru ve službě App Service v Linuxu spuštěnou běží v samostatném režimu, není v konfiguraci domény. Při horizontálním navýšením kapacity plánu služby App Service, každá instance WildFly je nakonfigurovaná jako samostatný server.

 Vertikálně nebo horizontálně škálovat aplikaci s [škálování pravidla](../../monitoring-and-diagnostics/monitoring-autoscale-get-started.md) a [zvýšit počet vašich instancí](../web-sites-scale.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

### <a name="customize-application-server-configuration"></a>Vlastní nastavení konfigurace aplikačního serveru

Instance webové aplikace jsou bezstavové, takže každá nová instance spustit musí být nakonfigurovaná na spuštění Wildfly konfigurace vyžadované aplikací.
Můžete napsat po spuštění skriptu Bash pro volání rozhraní příkazového řádku WildFly pro:

- Nastavení zdroje dat
- Konfigurace zprostředkovatelů zasílání zpráv
- Přidejte do konfigurace serveru Wildfly jiných modulů a závislosti.

 Skript se spustí při Wildfly je vytvořená a spuštěná, ale před spuštěním aplikace. Skript by měl používat [JBOSS CLI](https://docs.jboss.org/author/display/WFLY/Command+Line+Interface) volat z `/opt/jboss/wildfly/bin/jboss-cli.sh` se konfigurace nebo po spuštění serveru potřebné změny konfigurace aplikačního serveru.

Nepoužívejte ke konfiguraci Wildfly interaktivní režim rozhraní příkazového řádku. Místo toho můžete poskytnout skript příkazů rozhraní příkazového řádku JBoss pomocí `--file` příkazu, například:

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

Po spuštění skriptu nahrajte `/home/site/deployments/tools` ve vaší instanci služby App Service. Zobrazit [tento dokument](../deploy-configure-credentials.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#userscope) postup získání svoje přihlašovací údaje serveru FTP.

Nastavte **spouštěcí skript** pole na webu Azure Portal do umístění při spuštění skriptu prostředí, například `/home/site/deployments/tools/your-startup-script.sh`.

Zadat [nastavení aplikace](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings) v konfiguraci aplikace a zajistěte tak předání proměnných prostředí pro použití skriptu. Nastavení aplikace udržovat připojovací řetězce a další tajné klíče potřebné ke konfiguraci vaší aplikace ze správy verzí.

### <a name="modules-and-dependencies"></a>Moduly a závislosti

Instalace modulů a jejich závislosti do cesty pro třídy Wildfly prostřednictvím rozhraní příkazového řádku JBoss, musíte vytvořit následující soubory v jejich vlastní adresáře. Některé moduly a závislosti může být nutné další konfigurace, jako je například JNDI pojmenování nebo jiná konfigurace specifické pro rozhraní API, takže tento seznam je minimální sada budete potřebovat ke konfiguraci závislostí ve většině případů.

- [Popisovač modulu XML](https://jboss-modules.github.io/jboss-modules/manual/#descriptors). Tento soubor XML definuje název, atributy a závislosti modulu. To [ukázkový soubor module.xml](https://access.redhat.com/documentation/en-us/jboss_enterprise_application_platform/6/html/administration_and_configuration_guide/example_postgresql_xa_datasource) definuje modulu Postgres, jeho závislost JDBC souboru JAR a další závislosti modulu, které vyžaduje.
- Všechny nezbytné JAR souboru závislosti pro modul.
- Skript se vaše příkazy rozhraní příkazového řádku JBoss konfigurace nového modulu. Tento soubor bude obsahovat vaše příkazy mají být provedeny pomocí rozhraní příkazového řádku JBoss a konfigurace serveru pro použití závislost. Dokumentace ke službě na příkazy pro přidání modulů, zdroje dat a zprostředkovatelé zasílání zpráv, najdete v tématu [tento dokument](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli).
- Bash spouštěcí skript pro volání rozhraní příkazového řádku JBoss a spusťte tento skript v předchozím kroku. Tento soubor se spustí při restartování vaší instance služby App Service, nebo když nových instancí se zřizují během Škálováním. Tento skript po spuštění je, kde můžete provádět další konfigurace pro vaši aplikaci předaly JBoss příkazy rozhraní příkazového řádku JBoss. Minimálně tento soubor může být jediným příkazem k předání vašeho skriptu příkazu rozhraní příkazového řádku JBoss JBoss rozhraní příkazového řádku:

```bash
`/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli`
```

Jakmile budete mít, soubory a obsah pro modul, postupujte podle kroků níže přidejte modul Wildfly aplikačního serveru.

1. Soubory na serveru FTP `/home/site/deployments/tools` ve vaší instanci služby App Service. Najdete v tomto dokumentu pokyny na získání svoje přihlašovací údaje serveru FTP.
2. V okně Nastavení aplikace na webu Azure portal, nastavte pole "Při spuštění skriptu" do umístění při spuštění skriptu prostředí, například `/home/site/deployments/tools/your-startup-script.sh` .
3. Stisknutím kombinace kláves restartování vaší instance služby App Service **restartovat** tlačítko **přehled** oddílu na portálu nebo pomocí rozhraní příkazového řádku Azure.

### <a name="data-sources"></a>Zdroje dat

Pokud chcete nakonfigurovat Wildfly pro připojení ke zdroji dat, opakujte stejný postup uvedených výše v části Instalace modulů a závislosti. Můžete postupovat podle stejných kroků pro libovolnou službu Azure Database.

1. Stáhněte ovladač JDBC pro vaši databázi charakter. Pro usnadnění práce uvádíme ovladače [Postgres](https://jdbc.postgresql.org/download.html) a [MySQL](https://dev.mysql.com/downloads/connector/j/). Rozbalte stahování se získat soubor .jar.
2. Postupujte podle kroků obrysu v "Moduly a závislosti" k vytvoření a nahrání popisovač modul XML, skript rozhraní příkazového řádku JBoss, spouštěcí skript a závislost .jar JDBC.

Další informace o konfiguraci Wildfly s [PostgreSQL](https://developer.jboss.org/blogs/amartin-blog/2012/02/08/how-to-set-up-a-postgresql-jdbc-driver-on-jboss-7) , [MySQL](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#Using_other_Databases-Using_MySQL_as_the_Default_DataSource), a [SQL Database](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#d0e3898) je k dispozici. Tyto přizpůsobené pokyny spolu s zobecněný přístup výše můžete použít k přidání definice zdrojů dat na server.

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

Můžete nakonfigurovat Wildfly použít jako úložiště externí relace [mezipaměti Azure Redis](/azure/azure-cache-for-redis/). Budete muset [zakázat existující Instance spřažení směrování žádostí na aplikace](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) konfigurace vypnout relace na základě souboru cookie směrování a povolit nakonfigurované úložiště relace Wildfly pracovat i bez rušení.

## <a name="docker-containers"></a>Kontejnery Dockeru

Pokud chcete použít sadu JDK Zulu podporovaných systémem Azure v kontejnerech, ujistěte se, že k vyžádání a použijte předem sestavené Image, jak je uvedeno v [podporované Azul Zulu Enterprise pro stránku pro stažení Azure](https://www.azul.com/downloads/azure-only/zulu/) nebo použijte `Dockerfile` příklady z [Úložiště Microsoft Java GitHub](https://github.com/Microsoft/java/tree/master/docker).

## <a name="statement-of-support"></a>Prohlášení k podpoře

### <a name="runtime-availability"></a>Dostupnost modulu runtime

App Service pro Linux podporuje dva moduly runtime pro spravované hostování webových aplikací v Javě:

- [Servletový kontejner Tomcat](https://tomcat.apache.org/) ke spuštění zabalené aplikace jako webové souborech archivu (WAR). Podporované verze jsou 8.5 a 9.0.
- Běhové prostředí Java SE pro provoz aplikací s podobu Java archivní soubory (JAR). Podporovány jsou verze Javy 8 a 11.

### <a name="jdk-versions-and-maintenance"></a>JDK verze a údržba

Azure podporovaná Java Development Kit (JDK) je [Zulu](https://www.azul.com/downloads/azure-only/zulu/) zajišťována [Azul Systems](https://www.azul.com/).

Hlavní verze aktualizace bude poskytovat prostřednictvím nové možnosti modulu runtime ve službě Azure App Service pro Linux. Zákazníci, aktualizujte na tyto novější verze Javy podle konfigurace nasazení služby App Service a jsou zodpovědné za testování a zajištění hlavní aktualizace vyhovují jejich potřebám.

Podporované JDK jsou automaticky opravit čtvrtletně v lednu, dubnu, červenci a říjnu každý rok.

### <a name="security-updates"></a>Aktualizace zabezpečení

Opravy chyb a opravy pro chyby zabezpečení hlavní vydá Jakmile budou k dispozici od Azul Systems. "Hlavní" ohrožení zabezpečení je definována základní skóre 9.0 nebo vyšší na [NIST běžné ohrožení zabezpečení hodnocení systému, verze 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Vyřazení a vyřazení z provozu

Pokud podporovaného modulu runtime Java se vyřadí z provozu, vývojáře v Azure pomocí ovlivněné runtime dostanou oznámení o zastarání aspoň šest měsíců před modul runtime byl vyřazen z provozu.

### <a name="local-development"></a>Místní vývoj

Vývojáři mohou stáhnout produkční verzi z Azul Zulu Enterprise sadu JDK pro místní vývoj z [serveru pro stahování společnosti Azul](https://www.azul.com/downloads/azure-only/zulu/).

### <a name="development-support"></a>Podpora vývoje

Odborná pomoc pro [podporovaných systémem Azure Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/) je k dispozici prostřednictvím při vývoji pro Azure nebo [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) s [kvalifikovaný plán podpory Azure](https://azure.microsoft.com/support/plans/).

### <a name="runtime-support"></a>Podpora modulu CLR

Mohou vývojáři [otevřete problém](/azure/azure-supportability/how-to-create-azure-support-request) s JDK Azul Zulu prostřednictvím podpory Azure, pokud mají [plán podpory kvalifikovaný](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Další postup

Přejděte [Azure pro vývojáře v Javě](/java/azure/) centra a najít rychlí průvodci Azure, kurzy a referenční dokumentace jazyka Java.

Obecné dotazy ohledně používání služby App Service pro Linux, které nejsou specifické pro Java development v odpovědi [nejčastější dotazy týkající se App Service Linux](app-service-linux-faq.md).