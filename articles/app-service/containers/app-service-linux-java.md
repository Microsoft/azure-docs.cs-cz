---
title: Příručka pro vývojáře Java pro službu App Service v Linuxu – Azure | Dokumentace Microsoftu
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
ms.date: 12/10/2018
ms.author: routlaw
ms.custom: seodec18
ms.openlocfilehash: d6e8d943d14cfddc260ba502e724543c6dc9cf4f
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56110332"
---
# <a name="java-developers-guide-for-app-service-on-linux"></a>Příručka pro vývojáře Java pro službu App Service v Linuxu

Azure App Service v Linuxu umožňuje vývojářům rychle sestavit, nasadit a škálovat své Tomcat v jazyce Java nebo Java Standard Edition (SE) zabalené webové aplikace na plně spravované služby založené na Linuxu. Nasazujte aplikace s moduly plug-in Maven z příkazového řádku nebo v editoru, jako je IntelliJ, Eclipse nebo Visual Studio Code.

Tato příručka obsahuje klíčové koncepty a pokyny pro vývojáře v Javě pomocí ve službě App Service pro Linux. Pokud jste nikdy použili službu Azure App Service pro Linux, přečtěte si [Java quickstart](quickstart-java.md) první. Obecné dotazy ohledně používání služby App Service pro Linux, které nejsou specifické pro Java development v odpovědi [nejčastější dotazy týkající se App Service Linux](app-service-linux-faq.md).

## <a name="deploying-your-app"></a>Nasazení vaší aplikace

Modul plug-in Maven můžete použít k nasazení souborů .jar a .war. Podrobnosti najdete na [této dokumentace](https://docs.microsoft.com/en-us/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme?view=azure-java-stable) Další informace o modulu plug-in Maven. 

Pokud nepoužíváte Maven, metodě nasazení bude záviset na vašich typ archivu:

- Chcete-li nasadit .war soubory na Tomcat, použijte `/api/wardeploy/` koncový bod účtovat souboru archivu. Další informace o tomto rozhraní API najdete v tématu [této dokumentace](https://docs.microsoft.com/en-us/azure/app-service/deploy-zip#deploy-war-file).
- K nasazení souborů .jar v Java SE Image, použijte `/api/zipdeploy/` koncový bod webu kudu. Další informace o tomto rozhraní API najdete v tématu [této dokumentace](https://docs.microsoft.com/en-us/azure/app-service/deploy-zip#rest).

Nenasazujte .war nebo .jar pomocí FTP. Nástroje serveru FTP slouží k nahrávání spouštěcí skripty, závislosti nebo jiné soubory modulu runtime. To není ideální volbou pro nasazování webových aplikací.

## <a name="logging-and-debugging-apps"></a>Protokolování a ladění aplikací

Sestavy o výkonu, provoz vizualizace a kontroly stavu jsou k dispozici pro každou aplikaci na webu Azure portal. Zobrazit [Přehled diagnostiky služby Azure App Service](/azure/app-service/overview-diagnostics) Další informace o tom, jak přistupovat a používat tyto diagnostické nástroje.

## <a name="application-performance-monitoring"></a>Funkce Application performance monitoring

Zobrazit [funkce Application performance monitoring nástroje s aplikacemi v Javě ve službě Azure App Service v Linuxu](how-to-java-apm-monitoring.md) pro podrobné pokyny ke konfiguraci New Relic a AppDynamics s Java aplikace spuštěné ve službě App Service v Linuxu.

### <a name="ssh-console-access"></a>Přístup ke konzole SSH 

Připojení SSH k prostředí Linuxu spuštění vaší aplikace je k dispozici. Zobrazit [podpora SSH pro Azure App Service v Linuxu](/azure/app-service/containers/app-service-linux-ssh-support) úplné pokyny pro připojení k systému Linux přes webový prohlížeč nebo místním terminálu.

### <a name="streaming-logs"></a>Protokoly streamování

Pro rychlé ladění a řešení problémů, můžete streamování protokolů do konzoly pomocí Azure CLI. Konfigurace rozhraní příkazového řádku s `az webapp log config` povolení protokolování:

```azurecli-interactive
az webapp log config --name ${WEBAPP_NAME} \
 --resource-group ${RESOURCEGROUP_NAME} \
 --web-server-logging filesystem
```

Potom streamování protokolů k pomocí konzoly `az webapp log tail`:

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```

Další informace najdete v tématu [streamování protokolů pomocí Azure CLI](../troubleshoot-diagnostic-logs.md#streaming-with-azure-cli).

### <a name="app-logging"></a>Protokolování aplikace

Povolit [protokolování aplikací](/azure/app-service/troubleshoot-diagnostic-logs#enablediag) prostřednictvím webu Azure portal nebo [rozhraní příkazového řádku Azure](/cli/azure/webapp/log#az-webapp-log-config) ke konfiguraci služby App Service, zaznamená se do místní výstup standardní konzole vaší aplikace a datových proudů chyba standardní konzole systém souborů nebo úložiště objektů Blob v Azure. Instance je zakázaná 12 hodin po dokončení konfigurace protokolování do místního systému souborů služby App Service. Pokud budete potřebovat delší dobu uchování, nakonfigurujte aplikaci zapisovat výstup do kontejneru úložiště objektů Blob.

Pokud vaše aplikace používá [Logback](https://logback.qos.ch/) nebo [Log4j](https://logging.apache.org/log4j) pro trasování, můžete předat dál trasování ke kontrole do služby Azure Application Insights pomocí pokyny ke konfiguraci protokolování framework v [Protokoly trasování prozkoumejte Java ve službě Application Insights](/azure/application-insights/app-insights-java-trace-logs). 

## <a name="customization-and-tuning"></a>Přizpůsobení a optimalizace

Azure App Service pro Linux podporuje pole vyladění a přizpůsobení prostřednictvím webu Azure Portal a rozhraní příkazového řádku. Projděte si následující články konfigurace bez Java konkrétní webové aplikace:

- [Konfigurace nastavení služby App Service](/azure/app-service/web-sites-configure?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Nastavení vlastní domény](/azure/app-service/app-service-web-tutorial-custom-domain?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Povolení protokolu SSL](/azure/app-service/app-service-web-tutorial-custom-ssl?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Přidání sítě CDN](/azure/cdn/cdn-add-to-web-app?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)

### <a name="set-java-runtime-options"></a>Nastavte možnosti modulu runtime Java

V prostředí Tomcat i Java SE nastavit přidělené paměti nebo jiné možnosti modulu runtime JVM, nastavte JAVA_OPTS, jak je znázorněno níže jako [nastavení aplikace](/azure/app-service/web-sites-configure#app-settings). App Service Linux předá toto nastavení jako proměnnou prostředí Java runtime při spuštění.

Na webu Azure Portal v části **nastavení aplikace** pro webovou aplikaci, vytvořte nové nastavení aplikace s názvem `JAVA_OPTS` , který obsahuje další nastavení, jako například `$JAVA_OPTS -Xms512m -Xmx1204m`.

Pokud chcete nakonfigurovat nastavení aplikace, které z modulu plug-in Azure App Service Linux Maven, add v sekci modul plug-in Azure nastavení/hodnota značek. Následující příklad nastaví konkrétní minimální a maximální heapsize Java:

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
az webapp config set -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME} --web-sockets-enabled true 
```

Potom restartujte aplikaci:

```azurecli-interactive
az webapp stop -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME} 
az webapp start -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME}  
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

Nastavení ověřování aplikace na webu Azure Portal s **ověřování a autorizace** možnost. Odtud můžete povolit ověřování pomocí Azure Active Directory nebo přihlašování přes sociální sítě jako je Facebook, Google nebo GitHub. Konfigurace Azure portal funguje pouze v případě konfigurace ověřování jednotným poskytovatele.  Další informace najdete v tématu [konfigurace aplikace App Service pro použití přihlášení Azure Active Directory](/azure/app-service/configure-authentication-provider-aad) a související články pro jiné zprostředkovatele identity.

Pokud je potřeba povolit více poskytovatelů přihlásit, postupujte podle pokynů [přizpůsobit ověřování pomocí služby App Service](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to) článku.

Aplikace Spring Boot vývojáři mohou použít [Azure Active Directory Spring Boot starter](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) k zabezpečení aplikace pomocí známých poznámky k zabezpečení Spring a rozhraní API. Je potřeba ke zvětšení maximální záhlaví ve vaší `application.properties` souboru. Doporučujeme, aby hodnota `16384`. 

### <a name="configure-tlsssl"></a>Konfigurace TLS/SSL

Postupujte podle pokynů [vytvoření vazby existujícího vlastního certifikátu SSL](/azure/app-service/app-service-web-tutorial-custom-ssl) nahrát stávající certifikát SSL a navázat jej na název domény vaší aplikace. Ve výchozím nastavení vaše aplikace stále umožňuje HTTP připojení – postupujte podle konkrétních kroků v kurzu k vynucení šifrování protokolu SSL a TLS.

## <a name="tomcat"></a>Tomcat 

### <a name="connecting-to-data-sources"></a>Připojení ke zdrojům dat

>[!NOTE]
> Pokud vaše aplikace používá Spring Framework nebo Spring Boot, můžete nastavit informace o připojení databáze pro Spring Data JPA jako proměnné prostředí [ve vlastnosti souboru aplikace]. Pak pomocí [nastavení aplikace](/azure/app-service/web-sites-configure#app-settings) definovat tyto hodnoty pro vaši aplikaci v portálu Azure portal nebo rozhraní příkazového řádku.

Tyto pokyny platí pro všechna připojení k databázi. Je potřeba vyplnit zástupné symboly s názvem třídy ovladač vybrané databáze a soubor JAR. K dispozici je také tabulka s názvy tříd a soubory ke stažení ovladače pro běžné databáze.

| Databáze   | Název třídy ovladače                             | Ovladač JDBC                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Drvier`                        | [Stáhnout](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Stáhněte si](https://dev.mysql.com/downloads/connector/j/) (vyberte "Nezávislé na platformě") |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Stáhnout](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#available-downloads-of-jdbc-driver-for-sql-server)                                                           |

Pokud chcete nakonfigurovat Tomcat používat připojení k databázi Java (JDBC) nebo rozhraní API trvalost Java (JPA), nejprve přizpůsobit `CATALINA_OPTS` proměnné prostředí, který je určen pro čtení pomocí Tomcat na začátku si. Nastavte tyto hodnoty pomocí nastavení aplikace v [modulu plug-in App Service Maven](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

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

#### <a name="for-application-level-data-sources"></a>U zdrojů dat na úrovni aplikace: 

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

#### <a name="for-shared-server-level-resources"></a>Sdílené prostředky na úrovni serveru:

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

#### <a name="finally-place-the-driver-jars-in-the-tomcat-classpath-and-restart-your-app-service"></a>Nakonec umístit JAR ovladač v cestě Tomcat a potom restartujte službu App Service: 

1. Zajistěte, aby byly soubory ovladačů JDBC k dispozici pro Tomcat classloader tak, že je `/home/tomcat/lib` adresáře. (Pokud ještě neexistuje, vytvořte tento adresář.) Pokud chcete nahrát tyto soubory k vaší instanci služby App Service, postupujte následovně:  
    1. Instalace rozšíření webpp služby Azure App Service:

      ```azurecli-interactive
      az extension add –name webapp
      ```

    2. Spusťte následující příkaz rozhraní příkazového řádku a vytvořte tunel SSH z místního systému do služby App Service:

      ```azurecli-interactive
      az webapp remote-connection create –g [resource group] -n [app name] -p [local port to open]
      ```

    3. Připojení na místní port tunelového propojení s vašeho klienta protokolu SFTP a odesílání souborů do `/home/tomcat/lib` složky.

    Alternativně můžete klienta FTP k nahrání ovladač JDBC. Postupujte podle těchto [pokyny k získání údajů FTP](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials).

2. Pokud jste nevytvořili zdroj dat na úrovni serveru, restartujte aplikaci App Service pro Linux. Tomcat resetuje `CATALINA_HOME` k `/home/tomcat/conf` a použít aktualizovanou konfiguraci.

## <a name="docker-containers"></a>Kontejnery Dockeru

Pokud chcete použít sadu JDK Zulu podporovaných systémem Azure v kontejnerech, ujistěte se, že k vyžádání a použijte předem sestavené Image, jak je uvedeno v [podporované Azul Zulu Enterprise pro stránku pro stažení Azure](https://www.azul.com/downloads/azure-only/zulu/) nebo použijte `Dockerfile` příklady z [Úložiště Microsoft Java GitHub](https://github.com/Microsoft/java/tree/master/docker).

## <a name="runtime-availability-and-statement-of-support"></a>Dostupnost a modulu runtime prohlášení k podpoře

App Service pro Linux podporuje dva moduly runtime pro spravované hostování webových aplikací v Javě:

- [Servletový kontejner Tomcat](https://tomcat.apache.org/) ke spuštění zabalené aplikace jako webové souborech archivu (WAR). Podporované verze jsou 8.5 a 9.0.
- Běhové prostředí Java SE pro provoz aplikací s podobu Java archivní soubory (JAR). Jedinou podporovanou hlavní verzí je Java 8.

## <a name="java-runtime-statement-of-support"></a>Java runtime prohlášení k podpoře 

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
