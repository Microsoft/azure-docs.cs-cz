---
title: Monitorování aplikací java spuštěných v jakémkoli prostředí – Azure Monitor Application Insights
description: Monitorování výkonu aplikací pro java aplikace spuštěné v libovolném prostředí se samostatným agentem Java bez instrumentace aplikace. Distribuované trasování a mapa aplikací.
ms.topic: conceptual
ms.date: 04/16/2020
ms.openlocfilehash: 08a83fbc05276808b62a0391a5c4217cc09f6d00
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641872"
---
# <a name="configuring-jvm-args-java-standalone-agent-for-azure-monitor-application-insights"></a>Konfigurace samostatného agenta Jazyka Java pro Azure Monitor Application Insights



## <a name="azure-environments"></a>Prostředí Azure

Konfigurace [služby App Services](https://docs.microsoft.com/azure/app-service/configure-language-java#set-java-runtime-options).

## <a name="spring-boot"></a>Spring Boot

Přidejte JVM `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` arg `-jar <myapp.jar>`někde před , například:

```
java -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar -jar <myapp.jar>
```

> [!NOTE]
> Args umístěny po `-jar <myapp.jar>` jsou předány do aplikace jako program args.


## <a name="tomcat-8-linux"></a>Tomcat 8 (Linux)

### <a name="tomcat-installed-via-apt-get-or-yum"></a>Tomcat nainstalován `apt-get` přes nebo`yum`

Pokud jste nainstalovali `apt-get` `yum`Tomcat přes nebo `/etc/tomcat8/tomcat8.conf`, pak byste měli mít soubor .  Přidejte tento řádek na konec tohoto souboru:

```
JAVA_OPTS="$JAVA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

### <a name="tomcat-installed-via-download-and-unzip"></a>Tomcat nainstalován přes download a rozbalit

Pokud jste nainstalovali Tomcat přes [https://tomcat.apache.org](https://tomcat.apache.org)download a rozbalit `<tomcat>/bin/catalina.sh`z , pak byste měli mít soubor .  Vytvořte nový soubor ve `<tomcat>/bin/setenv.sh` stejném adresáři s následujícím obsahem:

```
CATALINA_OPTS="$CATALINA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

Pokud soubor `<tomcat>/bin/setenv.sh` již existuje, upravte jej `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` `CATALINA_OPTS`a přidejte do aplikace .


## <a name="tomcat-8-windows"></a>Tomcat 8 (Windows)

### <a name="running-tomcat-from-the-command-line"></a>Spuštění kocoura z příkazového řádku

Vyhledejte `<tomcat>/bin/catalina.bat`soubor .  Vytvořte nový soubor ve `<tomcat>/bin/setenv.bat` stejném adresáři s následujícím obsahem:

```
set CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```

Citace nejsou nutné, ale pokud je chcete zahrnout, správné umístění je:

```
set "CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

Pokud soubor `<tomcat>/bin/setenv.bat` již existuje, stačí tento `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` soubor `CATALINA_OPTS`upravit a přidat do aplikace .

### <a name="running-tomcat-as-a-windows-service"></a>Spuštění aplikace Tomcat jako služby systému Windows

Vyhledejte `<tomcat>/bin/tomcat8w.exe`soubor .  Spusťte tento `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` spustitelný `Java Options` soubor `Java` a přidejte na kartu pod.


## <a name="jboss-eap-7"></a>JBoss EAP 7

### <a name="standalone-server"></a>Samostatný server

Přidat `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` do `JAVA_OPTS` existující proměnné prostředí `JBOSS_HOME/bin/standalone.conf` v souboru (Linux) nebo `JBOSS_HOME/bin/standalone.conf.bat` (Windows):

```java    ...
    JAVA_OPTS="<b>-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar</b> -Xms1303m -Xmx1303m ..."
    ...
```

### <a name="domain-server"></a>Doménový server

Přidat `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` k `jvm-options` existujícímu v `JBOSS_HOME/domain/configuration/host.xml`:

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

Pokud používáte více spravovaných serverů na jednom `applicationinsights.agent.id` hostiteli, budete muset přidat k `system-properties` pro každý `server`:

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

Zadaná `applicationinsights.agent.id` hodnota musí být jedinečná. Používá se k vytvoření podadresáře pod adresářem applicationinsights, protože každý proces JVM potřebuje vlastní konfiguraci místních applicationinsights a soubor protokolu local applicationinsights. Také pokud vykazování centrální `applicationinsights.properties` kolektoru, soubor je sdílen více `applicationinsights.agent.id` spravovaných `agent.id` serverů, a proto zadaný je potřeba přepsat nastavení v tomto sdíleném souboru. `applicationinsights.agent.rollup.id`podobně lze zadat na serveru, `system-properties` pokud potřebujete přepsat `agent.rollup.id` nastavení na spravovaný server.


## <a name="jetty-9"></a>Molo 9

Přidejte tyto řádky do`start.ini`

```
--exec
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```


## <a name="payara-5"></a>Payara 5

Přidat `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` k `jvm-options` existujícímu v `glassfish/domains/domain1/config/domain.xml`:

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

Open Management Console přejít na **servery > aplikační servery WebSphere > aplikační servery**, zvolte příslušné aplikační servery a klikněte na: 

```
Java and Process Management > Process definition >  Java Virtual Machine
```
V "Obecné Argumenty JVM" přidejte následující:
```
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```
Poté uložte a restartujte aplikační server.


## <a name="openliberty-18"></a>OpenLiberty 18

Vytvořte nový `jvm.options` soubor v adresáři `<openliberty>/usr/servers/defaultServer`serveru (například ) a přidejte tento řádek:
```
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```
