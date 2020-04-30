---
title: Monitorování aplikací Java spuštěných v jakémkoli prostředí – Azure Monitor Application Insights
description: Monitorování výkonu aplikací pro aplikace Java spuštěné v jakémkoli prostředí se samostatným agentem Java bez instrumentace aplikace Distribuované trasování a mapa aplikací
ms.topic: conceptual
ms.date: 04/16/2020
ms.openlocfilehash: 08a83fbc05276808b62a0391a5c4217cc09f6d00
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81641872"
---
# <a name="configuring-jvm-args-java-standalone-agent-for-azure-monitor-application-insights"></a>Konfigurace samostatného agenta JVM args Java pro Azure Monitor Application Insights



## <a name="azure-environments"></a>Prostředí Azure

Nakonfigurujte [App Services](https://docs.microsoft.com/azure/app-service/configure-language-java#set-java-runtime-options).

## <a name="spring-boot"></a>Spring Boot

Přidejte ARG `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` JVM někam do `-jar <myapp.jar>`, například:

```
java -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar -jar <myapp.jar>
```

> [!NOTE]
> Argumenty umístěné po `-jar <myapp.jar>` jsou předány do aplikace jako argumenty programu.


## <a name="tomcat-8-linux"></a>Tomcat 8 (Linux)

### <a name="tomcat-installed-via-apt-get-or-yum"></a>Tomcat nainstalované prostřednictvím `apt-get` nebo`yum`

Pokud jste nainstalovali Tomcat přes `apt-get` nebo `yum`, měli byste mít soubor `/etc/tomcat8/tomcat8.conf`.  Přidejte tento řádek na konec tohoto souboru:

```
JAVA_OPTS="$JAVA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

### <a name="tomcat-installed-via-download-and-unzip"></a>Tomcat nainstalované prostřednictvím stažení a rozbalení

Pokud jste nainstalovali Tomcat prostřednictvím stažení a dekomprimovat [https://tomcat.apache.org](https://tomcat.apache.org)z nástroje, měli byste mít soubor `<tomcat>/bin/catalina.sh`.  Vytvořte nový soubor ve stejném adresáři s názvem `<tomcat>/bin/setenv.sh` s následujícím obsahem:

```
CATALINA_OPTS="$CATALINA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

Pokud soubor `<tomcat>/bin/setenv.sh` již existuje, upravte ho a přidejte `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` do. `CATALINA_OPTS`


## <a name="tomcat-8-windows"></a>Tomcat 8 (Windows)

### <a name="running-tomcat-from-the-command-line"></a>Spuštění Tomcat z příkazového řádku

Vyhledejte soubor `<tomcat>/bin/catalina.bat`.  Vytvořte nový soubor ve stejném adresáři s názvem `<tomcat>/bin/setenv.bat` s následujícím obsahem:

```
set CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```

Citace nejsou nutné, ale pokud je chcete zahrnout, správné umístění je:

```
set "CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

Pokud soubor `<tomcat>/bin/setenv.bat` již existuje, stačí ho upravit a přidat `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` do `CATALINA_OPTS`nástroje.

### <a name="running-tomcat-as-a-windows-service"></a>Spuštění Tomcat jako služby systému Windows

Vyhledejte soubor `<tomcat>/bin/tomcat8w.exe`.  Spusťte tento spustitelný soubor a `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` přidejte na `Java Options` `Java` kartu pod.


## <a name="jboss-eap-7"></a>JBoss EAP 7

### <a name="standalone-server"></a>Samostatný server

Přidat `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` do existující `JAVA_OPTS` proměnné prostředí v souboru `JBOSS_HOME/bin/standalone.conf` (Linux) nebo `JBOSS_HOME/bin/standalone.conf.bat` (Windows):

```java    ...
    JAVA_OPTS="<b>-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar</b> -Xms1303m -Xmx1303m ..."
    ...
```

### <a name="domain-server"></a>Doménový server

Přidat `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` k existujícímu `jvm-options` v `JBOSS_HOME/domain/configuration/host.xml`:

```xml
...
<jvms>
    <jvm name="default">
        <heap size="64m" max-size="256m"/>
        <jvm-options>
            <option value="-server"/>
            <!--Add Java agent jar file here-->
            <option value="-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"/>
            <option value="-XX:MetaspaceSize=96m"/>
            <option value="-XX:MaxMetaspaceSize=256m"/>
        </jvm-options>
    </jvm>
</jvms>
...
```

Pokud na jednom hostiteli spouštíte víc spravovaných serverů, budete muset pro každý z `applicationinsights.agent.id` `system-properties` nich `server`přidat:

```xml
...
<servers>
    <server name="server-one" group="main-server-group">
        <!--Edit system properties for server-one-->
        <system-properties> 
            <property name="applicationinsights.agent.id" value="..."/>
        </system-properties>
    </server>
    <server name="server-two" group="main-server-group">
        <socket-bindings port-offset="150"/>
        <!--Edit system properties for server-two-->
        <system-properties>
            <property name="applicationinsights.agent.id" value="..."/> 
        </system-properties>
    </server>
</servers>
...
```

Zadaná `applicationinsights.agent.id` hodnota musí být jedinečná. Slouží k vytvoření podadresáře v adresáři ApplicationInsights, protože každý proces JVM potřebuje svůj vlastní místní soubor ApplicationInsights config a místní soubor protokolu ApplicationInsights. Také při vytváření sestav do centrálního sběrače `applicationinsights.properties` soubor sdílí více spravovaných serverů, a proto `applicationinsights.agent.id` je potřeba k přepsání `agent.id` nastavení v tomto sdíleném souboru. `applicationinsights.agent.rollup.id`v `system-properties` případě, že je potřeba přepsat `agent.rollup.id` nastavení na spravovaný server, je možné ho podobně zadat na serveru.


## <a name="jetty-9"></a>Jetty 9

Přidat tyto řádky do`start.ini`

```
--exec
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```


## <a name="payara-5"></a>Payara 5

Přidat `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` k existujícímu `jvm-options` v `glassfish/domains/domain1/config/domain.xml`:

```xml
...
<java-config ...>
    <!--Edit the JVM options here-->
    <jvm-options>
        -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar>
    </jvm-options>
        ...
</java-config>
...
```

## <a name="websphere-8"></a>WebSphere 8

Otevřete konzolu pro správu přejděte na **servery > WebSphere aplikační servery > aplikační servery**, vyberte příslušné aplikační servery a klikněte na: 

```
Java and Process Management > Process definition >  Java Virtual Machine
```
V části Obecné argumenty JVM přidejte následující:
```
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```
Potom uložte a znovu spusťte aplikační server.


## <a name="openliberty-18"></a>OpenLiberty 18

Vytvořte nový soubor `jvm.options` v adresáři serveru (například `<openliberty>/usr/servers/defaultServer`) a přidejte tento řádek:
```
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```
