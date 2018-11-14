---
title: Podpora jazyka Java Enterprise pro Azure App Service v Linuxu | Dokumentace Microsoftu
description: Příručka pro vývojáře k nasazování aplikací Java Enterprise Wildfly pomocí služby Azure App Service v Linuxu.
keywords: služby Azure app service, web Apps, linux, oss, java, wildfly, enterprise
services: app-service
author: rloutlaw
manager: angerobe
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 08/29/2018
ms.author: routlaw
ms.openlocfilehash: 6613def8891109e3a0ddf818111898a893a8035d
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51628962"
---
# <a name="java-enterprise-guide-for-app-service-on-linux"></a>Java podniková Příručka pro službu App Service v Linuxu

Azure App Service v Linuxu umožňuje vývojářům vytvářet, nasazovat a škálovat aplikace pro platformu Java Enterprise (JEE) na plně spravované služby založené na Linuxu v jazyce Java.  Základní běhové prostředí Java Enterprise je open source [Wildfly](http://wildfly.org/) aplikačního serveru.

Tato příručka obsahuje klíčové koncepty a pokyny pro vývojáře Java Enterprise, kteří používají ve službě App Service pro Linux. Pokud jste nikdy nasadili aplikace Java pomocí Azure App Service pro Linux, by se měl Dokončit [Java quickstart](quickstart-java.md) první. Dotazy týkající se App Service pro Linux, které nejsou specifické pro jazyk Java Enterprise v odpovědi [Příručka pro vývojáře Java](app-service-linux-java.md) a [nejčastější dotazy týkající se App Service Linux](app-service-linux-faq.md).

## <a name="scale-with-app-service"></a>Škálování pomocí služby App Service 

WildFly aplikačního serveru ve službě App Service v Linuxu spuštěnou běží v samostatném režimu, není v konfiguraci domény. 

 Vertikálně nebo horizontálně škálovat aplikaci s [škálování pravidla](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-autoscale-get-started?toc=%2Fazure%2Fapp-service%2Fcontainers%2Ftoc.json) a [zvýšit počet vašich instancí](https://docs.microsoft.com/azure/app-service/web-sites-scale?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

## <a name="customize-application-server-configuration"></a>Vlastní nastavení konfigurace aplikačního serveru

Vývojářům psát po spuštění skriptu Bash provést další konfiguraci potřebné pro svou aplikaci, jako například:

- Nastavení zdroje dat
- Konfigurace zprostředkovatelů zasílání zpráv
- Přidání jiných modulů a dependnecies ke konfiguraci serveru Wildfly.

 Skript se spustí při Wildfly je vytvořená a spuštěná, ale před spuštěním aplikace. Skript by měl používat [JBOSS CLI](https://docs.jboss.org/author/display/WFLY/Command+Line+Interface) volat z `/opt/jboss/wildfly/bin/jboss-cli.sh` se konfigurace nebo po spuštění serveru potřebné změny konfigurace aplikačního serveru. 

Nepoužívejte ke konfiguraci Wildfly interaktivní režim rozhraní příkazového řádku. Místo toho můžete poskytnout skript příkazů rozhraní příkazového řádku JBoss pomocí `--file` příkazu, například:

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

Po spuštění skriptu nahrajte `/home/site/deployments/tools` ve vaší instanci služby App Service. Zobrazit [tento dokument](/azure/app-service/app-service-deployment-credentials#userscope) postup získání svoje přihlašovací údaje serveru FTP. 

Nastavte **spouštěcí skript** pole na webu Azure Portal do umístění při spuštění skriptu prostředí, například `/home/site/deployments/tools/your-startup-script.sh`.

Použít [nastavení aplikace](/azure/app-service/web-sites-configure#application-settings) k nastavení proměnných prostředí pro použití ve skriptu. Tato nastavení jsou k dispozici po spuštění skriptu prostředí a zachovat připojovacích řetězců a dalších tajných kódů ze správy verzí.

## <a name="modules-and-dependencies"></a>Moduly a závislosti

Instalace modulů a jejich závislosti do cesty pro třídy Wildfly prostřednictvím rozhraní příkazového řádku JBoss, musíte vytvořit následující soubory v jejich vlastní adresáře.  Některé moduly a závislosti může být nutné další konfigurace, jako je například JNDI pojmenování nebo jiná konfigurace specifické pro rozhraní API, takže tento seznam je minimální sada budete potřebovat ke konfiguraci závislostí ve většině případů.

- [Popisovač modulu XML](https://jboss-modules.github.io/jboss-modules/manual/#descriptors). Tento soubor XML definuje název, atributy a závislosti modulu. To [ukázkový soubor module.xml](https://access.redhat.com/documentation/en-us/jboss_enterprise_application_platform/6/html/administration_and_configuration_guide/example_postgresql_xa_datasource) definuje modulu Postgres, jeho závislost JDBC souboru JAR a další závislosti modulu, které vyžaduje.
- Všechny nezbytné JAR souboru závislosti pro modul.
- Skript se vaše příkazy rozhraní příkazového řádku JBoss konfigurace nového modulu. Tento soubor bude obsahovat vaše příkazy mají být provedeny pomocí rozhraní příkazového řádku JBoss a konfigurace serveru pro použití závislost. Dokumentace ke službě na příkazy pro přidání modulů, zdroje dat a zprostředkovatelé zasílání zpráv, najdete v tématu [tento dokument](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli).
-  Bash spouštěcí skript pro volání rozhraní příkazového řádku JBoss a spusťte tento skript v předchozím kroku. Tento soubor se spustí při restartování vaší instance služby App Service, nebo když nových instancí se zřizují během Škálováním.  Tento skript po spuštění je, kde můžete provádět další konfigurace pro vaši aplikaci předaly JBoss příkazy rozhraní příkazového řádku JBoss. Minimálně tento soubor může být jediným příkazem k předání vašeho skriptu příkazu rozhraní příkazového řádku JBoss JBoss rozhraní příkazového řádku: 
   
```bash
`/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli` 
``` 

Jakmile budete mít, soubory a obsah pro modul, postupujte podle kroků níže přidejte modul Wildfly aplikačního serveru. 

1. Soubory na serveru FTP `/home/site/deployments/tools` ve vaší instanci služby App Service. Najdete v tomto dokumentu pokyny na získání svoje přihlašovací údaje serveru FTP. 
2. V okně Nastavení aplikace na webu Azure portal, nastavte pole "Při spuštění skriptu" do umístění při spuštění skriptu prostředí, například `/home/site/deployments/tools/your-startup-script.sh` .
3. Stisknutím kombinace kláves restartování vaší instance služby App Service **restartovat** tlačítko **přehled** oddílu na portálu nebo pomocí rozhraní příkazového řádku Azure.

## <a name="data-sources"></a>Zdroje dat

Pokud chcete nakonfigurovat Wildfly pro připojení ke zdroji dat, opakujte stejný postup uvedených výše v části Instalace modulů a závislosti. Můžete postupovat podle stejných kroků pro libovolnou službu Azure Database.

1. Stáhněte ovladač JDBC pro vaši databázi charakter. Pro usnadnění práce uvádíme ovladače [Postgres](https://jdbc.postgresql.org/download.html) a [MySQL](https://dev.mysql.com/downloads/connector/j/). Rozbalte stahování se získat soubor .jar.
2. Postupujte podle kroků obrysu v "Moduly a závislosti" k vytvoření a nahrání popisovač modul XML, skript rozhraní příkazového řádku JBoss, spouštěcí skript a závislost .jar JDBC.


Další informace o konfiguraci Wildfly s [PostgreSQL](https://developer.jboss.org/blogs/amartin-blog/2012/02/08/how-to-set-up-a-postgresql-jdbc-driver-on-jboss-7) , [MySQL](https://dev.mysql.com/doc/connector-j/5.1/connector-j-usagenotes-jboss.html), a [SQL Database](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#d0e3898) je k dispozici. Tyto přizpůsobené pokyny spolu s zobecněný přístup výše můžete použít k přidání definice zdrojů dat na server.

## <a name="messaging-providers"></a>Zprostředkovatelé zasílání zpráv

Pokud chcete povolit zpráva řízené lusků používat jako mechanismus zasílání zpráv Service Bus:

1. Použití [knihovna zpráv Apache QPId JMS](https://qpid.apache.org/proton/index.html). Patří této závislosti ve vašich pom.xml (nebo jiný soubor sestavení) pro aplikaci.

2.  Vytvoření [prostředky služby Service Bus](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp). Vytvoření oboru názvů služby Azure Service Bus a fronty v rámci daného oboru názvů a zásady sdíleného přístupu s odesílání a přijímání možnosti.

3. Předat klíč zásad sdíleného přístupu ke kódu, buď pomocí kódování URL primární klíč zásady nebo [pomocí sady SDK služby Service Bus](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp#setup-jndi-context-and-configure-the-connectionfactory).

4. Postupujte podle kroků uvedených v části Instalace modulů a závislosti s modul XML popisovač, .jar závislosti, příkazů rozhraní příkazového řádku JBoss a spouštěcí skript pro JMS zprostředkovatele. Kromě čtyři soubory je také potřeba vytvořit soubor XML, který definuje název JNDI pro JMS frontu a téma. Zobrazit [toto úložiště](https://github.com/JasonFreeberg/widlfly-server-configs/tree/master/appconfig) pro odkaz na konfigurační soubory.


## <a name="configure-session-management-caching"></a>Konfigurovat ukládání do mezipaměti relace správy

App Service v Linuxu ve výchozím nastavení bude používat soubory cookie spřažení relace k zajištění, že požadavky klientů s existující relací jsou směrovány stejnou instanci vaší aplikace. Toto výchozí chování není nutné nic konfigurovat, ale má určitá omezení:

- Pokud instance aplikace je restartován nebo kapacitu vertikálně snížit, stav relace uživatele na serveru aplikace se ztratí.
- Pokud aplikace nastavení časového limitu relace dlouhé nebo pevný počet uživatelů, může trvat nějakou dobu došlo ke ztrátě schopnosti nové instance pro příjem zatížení, protože pouze nové relace se budou směrovat na nově spuštěná instance.

Můžete nakonfigurovat Wildfly použít jako úložiště externí relace [Redis Cache](/azure/redis-cache/). Budete muset [zakázat existující Instance spřažení směrování žádostí na aplikace](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) konfigurace vypnout relace na základě souboru cookie směrování a povolit nakonfigurované úložiště relace Wildfly pracovat i bez rušení.

## <a name="enable-web-sockets"></a>Povolit webové sokety

Ve výchozím nastavení jsou povolené webové sokety ve službě App Service. Chcete-li začít pracovat s objekty Websocket ve vaší aplikaci, přečtěte si [v tomto rychlém startu](https://github.com/wildfly/quickstart/tree/master/websocket-hello).

## <a name="logs-and-troubleshooting"></a>Protokoly a řešení potíží

App Service poskytuje nástroje, které pomáhají při řešení problémů s vaší aplikací.

-   Zapnutí protokolování kliknutím **diagnostické protokoly** v levém navigačním podokně. Klikněte na tlačítko **systému souborů** nastavit období kvótu a uchování vašeho úložiště a uložte provedené změny. Můžete najít v části tyto protokoly `/home/LogFiles/`.
-   [Pomocí SSH se připojte k instanci aplikace](/app-service-linux-ssh-support) si chcete zobrazit protokoly pro spouštění aplikací.
-   Kontrola diagnostické protokoly **diagnostické protokoly** panelu portálu nebo pomocí příkazového řádku Azure: ` az webapp log tail --name <your-app-name> --resource-group <your-apps-resource-group> `
