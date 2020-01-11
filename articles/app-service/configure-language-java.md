---
title: Konfigurace aplikací pro Windows Java
description: Naučte se konfigurovat aplikace Java tak, aby běžely na instancích virtuálních počítačů s Windows v Azure App Service. Tento článek ukazuje nejběžnější konfigurační úlohy.
keywords: Azure App Service, Web App, Windows, OSS, Java
author: jasonfreeberg
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: 9ee989a079366a470d086a8b931685a6c1dbc757
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75889351"
---
# <a name="configure-a-windows-java-app-for-azure-app-service"></a>Konfigurace aplikace pro Windows Java pro Azure App Service

Azure App Service umožňuje vývojářům v jazyce Java rychle sestavovat, nasazovat a škálovat webové aplikace v Tomcat na plně spravované službě založené na Windows. Nasaďte aplikace pomocí modulů plug-in Maven z příkazového řádku nebo v editorech, jako je IntelliJ, zatmění nebo Visual Studio Code.

Tato příručka poskytuje klíčové koncepty a pokyny pro vývojáře v jazyce Java, které využívají v App Service. Pokud jste nikdy Azure App Service nepoužili, měli byste si nejdřív projít modulem [rychlý Start Java](app-service-web-get-started-java.md) . Obecné otázky týkající se použití App Service, které nejsou specifické pro vývoj v jazyce Java, jsou zodpovězeny v tématu [Nejčastější dotazy k App Service Windows](faq-configuration-and-management.md).

## <a name="deploying-your-app"></a>Nasazení aplikace

K nasazení souborů. War můžete použít [modul plug-in Maven pro Azure App Service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) . Nasazení s oblíbenými mikroprostředími se podporuje taky [Azure Toolkit for IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij) nebo [Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse).

V opačném případě vaše metoda nasazení bude záviset na typu archivu:

- K nasazení souborů. War do Tomcat použijte koncový bod `/api/wardeploy/` k odeslání souboru archivu. Další informace o tomto rozhraní API najdete v [této dokumentaci](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file).

Nesaďte svůj. War pomocí FTP. Nástroj FTP je určen pro nahrávání spouštěcích skriptů, závislostí nebo jiných souborů modulu runtime. Nejedná se o optimální volbu pro nasazování webových aplikací.

## <a name="logging-and-debugging-apps"></a>Protokolování a ladění aplikací

Sestavy výkonu, vizualizace provozu a checkups stavu jsou k dispozici pro každou aplikaci prostřednictvím Azure Portal. Další informace najdete v tématu [Přehled diagnostiky Azure App Service](overview-diagnostics.md).

### <a name="stream-diagnostic-logs"></a>Streamování diagnostických protokolů

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

Další informace najdete v tématu [protokoly streamování v Cloud Shell](troubleshoot-diagnostic-logs.md#in-cloud-shell).

### <a name="app-logging"></a>Protokolování aplikace

Povolte [protokolování aplikací](troubleshoot-diagnostic-logs.md#enable-application-logging-windows) prostřednictvím Azure Portal nebo pomocí [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) a nakonfigurujte App Service tak, aby se do místního systému souborů nebo do služby Azure Blob Storage napsaly standardní a standardní chybové proudy konzolové aplikace. Protokolování do místní instance systému souborů App Service je po konfiguraci zakázáno 12 hodin. Pokud budete potřebovat delší dobu uchování, nakonfigurujte aplikaci tak, aby zapisovala výstup do kontejneru úložiště objektů BLOB. Protokoly aplikací Java a Tomcat najdete v adresáři */Logfiles/Application/* .

Pokud vaše aplikace používá pro trasování [Logback](https://logback.qos.ch/) nebo [log4j](https://logging.apache.org/log4j) , můžete tyto trasování přepošlete pro účely revize do Azure Application Insights pomocí pokynů pro konfiguraci protokolovacího rozhraní v tématu [prozkoumat protokoly trasování Java v Application Insights](/azure/application-insights/app-insights-java-trace-logs).


## <a name="customization-and-tuning"></a>Přizpůsobení a ladění

Azure App Service podporuje vyladění a přizpůsobení prostřednictvím Azure Portal a CLI. Projděte si následující články s konfigurací webové aplikace specifické pro jazyk Java:

- [Konfigurovat nastavení aplikace](configure-common.md#configure-app-settings)
- [Nastavení vlastní domény](app-service-web-tutorial-custom-domain.md)
- [Konfigurace vazeb SSL](configure-ssl-bindings.md)
- [Přidat síť CDN](../cdn/cdn-add-to-web-app.md)
- [Konfigurace webu Kudu](https://github.com/projectkudu/kudu/wiki/Configurable-settings)

### <a name="set-java-runtime-options"></a>Nastavení možností modulu Java Runtime

Pokud chcete nastavit přidělenou paměť nebo jiné možnosti modulu runtime JVM, vytvořte [nastavení aplikace](configure-common.md#configure-app-settings) s názvem `JAVA_OPTS` s možnostmi. App Service předá toto nastavení jako proměnnou prostředí modulu runtime Java při spuštění.

V Azure Portal v části **nastavení aplikace** pro webovou aplikaci vytvořte nové nastavení aplikace s názvem `JAVA_OPTS`, které obsahuje další nastavení, jako je například `-Xms512m -Xmx1204m`.

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
- Instance K3 a S3: `-Xms6144m -Xmx6144m`

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

V Azure Portal v části **nastavení aplikace** pro webovou aplikaci vytvořte nové nastavení aplikace s názvem `JAVA_OPTS` s hodnotou `-Dfile.encoding=UTF-8`.

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

Aplikace Java běžící v App Service mají stejnou sadu [osvědčených postupů zabezpečení](/azure/security/security-paas-applications-using-app-services) jako u jiných aplikací.

### <a name="authenticate-users-easy-auth"></a>Ověřování uživatelů (snadné ověřování)

Pomocí možnosti **ověřování a autorizace** nastavte ověřování aplikací v Azure Portal. Odtud můžete povolit ověřování pomocí Azure Active Directory nebo přes sociální přihlášení, jako je Facebook, Google nebo GitHub. Konfigurace Azure Portal funguje pouze při konfiguraci jednoho poskytovatele ověřování. Další informace najdete v tématu [Konfigurace aplikace App Service pro použití Azure Active Directory přihlášení](configure-authentication-provider-aad.md) a souvisejících článků pro jiné poskytovatele identity. Pokud potřebujete povolit více poskytovatelů přihlašování, postupujte podle pokynů v článku [přizpůsobení App Serviceho ověřování](app-service-authentication-how-to.md) .

#### <a name="tomcat-and-wildfly"></a>Tomcat a WildFly

Vaše aplikace Tomcat nebo WildFly může získat přístup k deklaracím uživatele přímo z servlet přetypováním objektu zabezpečení na objekt mapy. Objekt mapy bude mapovat jednotlivé typy deklarací na kolekci deklarací pro daný typ. V následujícím kódu `request` je instance `HttpServletRequest`.

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

Nyní můžete zkontrolovat objekt `Map` pro všechny konkrétní deklarace identity. Například následující fragment kódu projde všechny typy deklarací a vytiskne obsah každé kolekce.

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

Chcete-li tuto funkci zakázat, vytvořte nastavení aplikace s názvem `WEBSITE_AUTH_SKIP_PRINCIPAL` s hodnotou `1`. Pokud chcete zakázat všechny filtry servlet přidané pomocí App Service, vytvořte nastavení s názvem `WEBSITE_SKIP_FILTERS` s hodnotou `1`.

### <a name="configure-tlsssl"></a>Konfigurace TLS/SSL

Podle pokynů v části [zabezpečení vlastního názvu DNS s vazbou SSL v Azure App Service](configure-ssl-bindings.md) Nahrajte stávající certifikát SSL a vytvořte jeho vazbu s názvem domény vaší aplikace. Ve výchozím nastavení bude vaše aplikace pořád umožňovat připojení HTTP – postupujte podle kroků v tomto kurzu, abyste vynutili SSL a TLS.

### <a name="use-keyvault-references"></a>Použití odkazů na Trezor klíčů

[Azure webtrezor](../key-vault/key-vault-overview.md) poskytuje centralizovanou správu tajných kódů pomocí zásad přístupu a historie auditu. Do trezoru klíčů můžete ukládat tajné kódy (například hesla nebo připojovací řetězce) a přistupovat k nim ve vaší aplikaci pomocí proměnných prostředí.

Nejdřív postupujte podle pokynů pro [udělení přístupu aplikace Key Vault](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) a [Vytvoření odkazu trezoru klíčů pro váš tajný klíč v nastavení aplikace](app-service-key-vault-references.md#reference-syntax). Můžete ověřit, že se odkaz na tajný kód přeloží vytištěním proměnné prostředí a vzdáleným přístupem k App Service terminálu.

Pro vložení těchto tajných kódů do konfiguračního souboru jarní nebo Tomcat použijte syntaxi vkládání proměnné prostředí (`${MY_ENV_VAR}`). V případě konfiguračních souborů pružiny se podívejte na tuto dokumentaci k [externě nakonfigurovaným konfiguracím](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).


## <a name="configure-apm-platforms"></a>Konfigurace platforem APM

V této části se dozvíte, jak připojit aplikace Java nasazené na Azure App Service v systému Linux pomocí platforem APM (Application Performance Monitoring) pro NewRelic a AppDynamics.

### <a name="configure-new-relic"></a>Konfigurovat nové Relic

1. Vytvoření nového účtu Relic na [NewRelic.com](https://newrelic.com/signup)
2. Stáhněte si agenta Java ze NewRelic, bude mít název souboru podobný řetězci *NewRelic-Java-x. x. x. zip*.
3. Zkopírujte licenční klíč, budete ho potřebovat ke konfiguraci agenta později.
4. Pomocí [konzoly Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console) vytvořte nový adresář */Home/site/wwwroot/APM*.
5. Nahrajte nezabalené nové soubory agenta Java Relic do adresáře pod */Home/site/wwwroot/APM*. Soubory pro vašeho agenta by měly být v */Home/site/wwwroot/APM/NewRelic*.
6. Upravte soubor YAML na */Home/site/wwwroot/APM/NewRelic/NewRelic.yml* a nahraďte hodnotu licence zástupného symbolu vlastním licenčním klíčem.
7. V Azure Portal přejděte do aplikace v App Service a vytvořte nové nastavení aplikace.
    - Pokud vaše aplikace používá **Java se**, vytvořte proměnnou prostředí s názvem `JAVA_OPTS` s hodnotou `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Pokud používáte **Tomcat**, vytvořte proměnnou prostředí s názvem `CATALINA_OPTS` s hodnotou `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.

### <a name="configure-appdynamics"></a>Konfigurace AppDynamics

1. Vytvoření účtu AppDynamics na [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Stáhněte si agenta Java z webu AppDynamics, název souboru bude podobný jako *AppServerAgent-x. x. x. xxxxx. zip*
3. Pomocí [konzoly Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console) vytvořte nový adresář */Home/site/wwwroot/APM*.
4. Nahrajte soubory agenta Java do adresáře pod */Home/site/wwwroot/APM*. Soubory pro vašeho agenta by měly být v */Home/site/wwwroot/APM/AppDynamics*.
5. V Azure Portal přejděte do aplikace v App Service a vytvořte nové nastavení aplikace.
    - Pokud používáte **Java se**systémem, vytvořte proměnnou prostředí s názvem `JAVA_OPTS` s hodnotou `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` kde `<app-name>` je váš App Service název.
    - Pokud používáte **Tomcat**, vytvořte proměnnou prostředí s názvem `CATALINA_OPTS` s hodnotou `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` kde `<app-name>` je váš App Service název.

>  Pokud již máte proměnnou prostředí pro `JAVA_OPTS` nebo `CATALINA_OPTS`, přidejte možnost `-javaagent:/...` na konec aktuální hodnoty.

## <a name="data-sources"></a>Zdroje dat

### <a name="tomcat"></a>Tomcat

Tyto pokyny platí pro všechna databázová připojení. Zástupné symboly budete muset vyplnit pomocí názvu třídy ovladače zvolené databáze a souboru JAR. Poskytuje se tabulka s názvy tříd a stahování ovladačů pro běžné databáze.

| databáze   | Název třídy ovladače                             | Ovladač JDBC                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Stáhnout](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Stáhnout](https://dev.mysql.com/downloads/connector/j/) (vyberte "nezávislé na platformě") |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Stáhnout](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#available-downloads-of-jdbc-driver-for-sql-server)                                                           |

Chcete-li nakonfigurovat Tomcat pro použití JDBC (Java Database Connectivity) nebo rozhraní Java trvalá rozhraní API (JPA), nejprve Přizpůsobte proměnnou prostředí `CATALINA_OPTS`, která je čtena nástrojem Tomcat při spuštění. Nastavte tyto hodnoty pomocí nastavení aplikace v [modulu plug-in App Service Maven](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Nebo nastavte proměnné prostředí na stránce nastavení > konfigurace **aplikace** v Azure Portal.

Dále určete, zda má být zdroj dat k dispozici pro jednu aplikaci nebo pro všechny aplikace běžící na Tomcat servlet.

#### <a name="application-level-data-sources"></a>Zdroje dat na úrovni aplikace

1. Vytvořte soubor *Context. XML* v adresáři *META-INF/* Directory vašeho projektu. Pokud neexistuje, vytvořte *soubor META-INF nebo* s adresářem.

2. V *kontextu. XML*přidejte `Context` element pro propojení zdroje dat s adresou JNDI. Zástupný text `driverClassName` nahraďte názvem třídy vašeho ovladače z tabulky výše.

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

## <a name="configuring-tomcat"></a>Konfigurace Tomcat

Pokud chcete upravit `server.xml` nebo jiné konfigurační soubory Tomcat, nejdřív si poznamenejte hlavní verzi Tomcat na portálu.

1. Najděte domovský adresář Tomcat pro vaši verzi spuštěním příkazu `env`. Vyhledejte proměnnou prostředí, která začíná na `AZURE_TOMCAT`a odpovídá vaší hlavní verzi. Například `AZURE_TOMCAT85_HOME` odkazuje na adresář Tomcat pro Tomcat 8,5.
1. Po identifikaci domovského adresáře Tomcat pro vaši verzi zkopírujte konfigurační adresář do `D:\home`. Pokud má například `AZURE_TOMCAT85_HOME` hodnotu `D:\Program Files (x86)\apache-tomcat-8.5.37`, nová cesta kopírovaného adresáře bude `D:\home\apache-tomcat-8.5.37`.

Nakonec restartujte App Service. Vaše nasazení by mělo přejít na `D:\home\site\wwwroot\webapps` stejně jako dřív.

## <a name="java-runtime-statement-of-support"></a>Příkaz Java Runtime pro podporu

### <a name="jdk-versions-and-maintenance"></a>Verze a údržba JDK

Podporovaná sada Java Development Kit (JDK) pro Azure je zajištěná [Zulu](https://www.azul.com/downloads/azure-only/zulu/) prostřednictvím [systémů Azul](https://www.azul.com/).

Hlavní aktualizace verze se poskytnou prostřednictvím nových možností modulu runtime v Azure App Service pro Windows. Zákazníci aktualizují tyto novější verze Java tím, že nakonfigurují nasazení App Service a zodpovídá za testování a zajištění významné aktualizace, které vyhovují jejich potřebám.

Podporované sady JDK se na čtvrtletní bázi automaticky opravují v lednu, dubnu, červenci a říjnu každého roku. Další informace o jazyce Java v Azure najdete v [tomto dokumentu podpory](https://docs.microsoft.com/azure/java/jdk/).

### <a name="security-updates"></a>Aktualizace zabezpečení

Opravy a opravy pro hlavní slabá místa zabezpečení budou vydány, jakmile budou dostupné ze systémů Azul. "Hlavní" ohrožení zabezpečení je definováno základním skóre 9,0 nebo vyšším v [systému NIST Common zranitelnost Standard, verze 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Vyřazení a vyřazení z provozu

Pokud bude vyřazení podporované běhové prostředí Java, budou se vývojáři Azure, kteří používají modul runtime, předávat oznámení o zastaralosti alespoň šest měsíců před vyřazením modulu runtime.

### <a name="local-development"></a>Místní vývoj

Vývojáři mohou stáhnout provozní edici Azul Zulu Enterprise JDK pro místní vývoj z [webu pro stažení Azul](https://www.azul.com/downloads/azure-only/zulu/).

### <a name="development-support"></a>Vývojová podpora

Podpora produktů pro [JDK Zulu s podporou Azure Azul](https://www.azul.com/downloads/azure-only/zulu/) je dostupná prostřednictvím Microsoftu při vývoji pro Azure nebo [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) s [kvalifikovaným plánem podpory Azure](https://azure.microsoft.com/support/plans/).

### <a name="runtime-support"></a>Podpora modulu CLR

Vývojáři mohou [otevřít problém](/azure/azure-portal/supportability/how-to-create-azure-support-request) s Azul Zulu sady JDK prostřednictvím podpory Azure, pokud mají [kvalifikovaný plán podpory](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Další kroky

Toto téma poskytuje příkaz Java Runtime, který podporuje Azure App Service ve Windows.

- Další informace o hostování webových aplikací pomocí Azure App Service naleznete v tématu [App Service Overview](overview.md).
- Informace o vývoji Java v prostředí Azure najdete v tématu [Azure for Java Dev Center](https://docs.microsoft.com/java/azure/?view=azure-java-stable).
