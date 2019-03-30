---
title: Konfigurace monitorování nástroje na Linux – Azure App Service a Java APM
description: Zjistěte, jak odesílat protokoly a metriky pro vaše aplikace Java běžící na App Service pro Linux poskytovatelům nástrojů NewRelic a aplikace Dynamics APM
services: app-service\web
author: rloutlaw
manager: angerobe
ms.service: app-service-web
ms.workload: web
ms.topic: article
ms.date: 03/21/2019
ms.author: astay;routlaw
ms.custom: seodec18
ms.openlocfilehash: e6a22258266bda18c9ff79590d88e70d512f6c77
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2019
ms.locfileid: "58630142"
---
# <a name="how-to-application-performance-monitoring-tools-with-java-apps-on-azure-app-service-on-linux"></a>Postupy: Funkce Application performance monitoring nástroje s aplikacemi v Javě ve službě Azure App Service v Linuxu

Tento článek ukazuje, jak připojit aplikace v Javě nasazené ve službě Azure App Service v Linuxu pomocí nástrojů NewRelic a AppDynamics pro monitorování výkonu aplikací (APM) platformy.

## <a name="configure-new-relic"></a>Nakonfigurujte nástroj společnosti New Relic

1. Vytvořit účet NewRelic na [NewRelic.com](https://newrelic.com/signup)
2. Stáhněte agenta Java z nástrojů NewRelic, bude mít název souboru, který je podobný `newrelic-java-x.x.x.zip`.
3. Zkopírování klíče licence, ho budete potřebovat ke konfiguraci agenta později.
4. [Připojte se přes SSH vaší instance služby App Service](/azure/app-service/containers/app-service-linux-ssh-support) a vytvořte nový adresář `/home/site/wwwroot/apm`.
5. Rozbalené soubory agenta NewRelic Java nahrát do adresáře v rámci `/home/site/wwwroot/apm`. Soubory pro agenta by měla být v `/home/site/wwwroot/apm/newrelic`.
6. Upravte soubor YAML na `/home/site/wwwroot/apm/newrelic/newrelic.yml` a nahraďte hodnotu zástupného symbolu licence vlastní licenční klíč.
7. Na webu Azure Portal přejděte do vaší aplikace ve službě App Service a vytvořit nové nastavení aplikace.
    - Pokud vaše aplikace používá **Java SE**, vytvořte proměnnou prostředí s názvem `JAVA_OPTS` s hodnotou `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Pokud používáte **Tomcat**, vytvořte proměnnou prostředí s názvem `CATALINA_OPTS` s hodnotou `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Pokud používáte **WildFly**, naleznete v dokumentaci k špičkové funkce New Relicu [tady](https://docs.newrelic.com/docs/agents/java-agent/additional-installation/wildfly-version-11-installation-java) pokyny týkající se instalace agenta Java a JBoss konfigurace.
    - Pokud už máte proměnnou prostředí pro `JAVA_OPTS` nebo `CATALINA_OPTS`, připojte `javaagent` možnost na konec aktuální hodnotu.

## <a name="configure-appdynamics"></a>Konfigurace AppDynamics

1. Vytvoření účtu AppDynamics na [AppDynamics.com](https://www.appdynamics.com/community/register/)
1. Stáhněte agenta Java z webu AppDynamics, názvem souboru bude podobný `AppServerAgent-x.x.x.xxxxx.zip`
1. [Připojte se přes SSH vaší instance služby App Service](/azure/app-service/containers/app-service-linux-ssh-support) a vytvořte nový adresář `/home/site/wwwroot/apm`.
1. Nahrát soubory agenta Java do adresáře v rámci `/home/site/wwwroot/apm`. Soubory pro agenta by měla být v `/home/site/wwwroot/apm/appdynamics`.
1. Na webu Azure Portal přejděte do vaší aplikace ve službě App Service a vytvořit nové nastavení aplikace.
    - Pokud používáte **Java SE**, vytvořte proměnnou prostředí s názvem `JAVA_OPTS` s hodnotou `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<YOUR_SITE_NAME>` kde `<YOUR_SITE_NAME>` je název vaší služby App Service.
    - Pokud používáte **Tomcat**, vytvořte proměnnou prostředí s názvem `CATALINA_OPTS` s hodnotou `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<YOUR_SITE_NAME>` kde `<YOUR_SITE_NAME>` je název vaší služby App Service.
    - Pokud používáte **WildFly**, naleznete v dokumentaci k AppDynamics [tady](https://docs.appdynamics.com/display/PRO45/JBoss+and+Wildfly+Startup+Settings) pokyny týkající se instalace agenta Java a JBoss konfigurace.
