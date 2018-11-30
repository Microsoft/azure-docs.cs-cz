---
title: Konfigurace monitorování nástrojů se službou Azure App Service v Linuxu a Javě APM
description: Zjistěte, jak odesílat protokoly a metriky pro vaše aplikace Java běžící na App Service pro Linux poskytovatelům nástrojů NewRelic a aplikace Dynamics APM
services: app-service\web
author: rloutlaw
manager: angerobe
ms.service: app-service-web
ms.workload: web
ms.topic: article
ms.date: 11/27/2018
ms.author: astay;routlaw
ms.openlocfilehash: 06ae71fea1b85a74d87588d2635038c64c8540cc
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52500275"
---
# <a name="how-to-application-performance-monitoring-tools-with-java-apps-on-azure-app-service-on-linux"></a>Postupy: Funkce Application performance monitoring nástroje s aplikacemi v Javě ve službě Azure App Service v Linuxu

Tento článek ukazuje, jak připojit aplikace v Javě nasazené ve službě Azure App Service v Linuxu pomocí nástrojů NewRelic a AppDynamics pro monitorování výkonu aplikací (APM) platformy.

## <a name="configure-new-relic"></a>Nakonfigurujte nástroj společnosti New Relic
1. Vytvořit účet NewRelic na [NewRelic.com](https://newrelic.com/signup)
1. Stáhněte agenta Java z nástrojů NewRelic, bude mít název souboru, který je podobný `newrelic-java-x.x.x.zip`.
1. Zkopírování klíče licence, ho budete potřebovat ke konfiguraci agenta později.
1. [Připojte se přes SSH vaší instance služby App Service](/azure/app-service/containers/app-service-linux-ssh-support) a vytvořte nový adresář `/home/site/wwwroot/apm`. 
1. Rozbalené soubory agenta NewRelic Java nahrát do adresáře v rámci `/home/site/wwwroot/apm`. Soubory pro agenta by měla být v `/home/site/wwwroot/apm/newrelic`.
1. Upravte soubor YAML na `/home/site/wwwroot/apm/newrelic/newrelic.yml` a nahraďte hodnotu zástupného symbolu licence vlastní licenční klíč.
1. Na webu Azure Portal přejděte do vaší aplikace ve službě App Service a vytvořit nové nastavení aplikace.
    - Pokud vaše aplikace používá **Java SE**, vytvořte proměnnou prostředí s názvem `JAVA_OPTS` s hodnotou `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Pokud používáte **Tomcat**, vytvořte proměnnou prostředí s názvem `CATALINA_OPTS` s hodnotou `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Pokud už máte proměnnou prostředí pro `JAVA_OPTS` nebo `CATALINA_OPTS`, připojte `javaagent` možnost na konec aktuální hodnotu.

## <a name="configure-appdynamics"></a>Konfigurace AppDynamics
1. Vytvoření účtu AppDynamics na [AppDynamics.com](https://www.appdynamics.com/community/register/)
1. Stáhněte agenta Java z webu AppDynamics, názvem souboru bude podobný `AppServerAgent-x.x.x.xxxxx.zip`
1. [Připojte se přes SSH vaší instance služby App Service](/azure/app-service/containers/app-service-linux-ssh-support) a vytvořte nový adresář `/home/site/wwwroot/apm`. 
1. Nahrát soubory agenta Java do adresáře v rámci `/home/site/wwwroot/apm`. Soubory pro agenta by měla být v `/home/site/wwwroot/apm/appdynamics`.
1. Profilace na webu Azure portal, přejděte do vaší aplikace ve službě App Service a vytvořit nové nastavení aplikace.
    - Pokud používáte **Java SE**, vytvořte proměnnou prostředí s názvem `JAVA_OPTS` s hodnotou `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<YOUR_SITE_NAME>` kde `<YOUR_SITE_NAME>` je název vaší služby App Service.
    - Pokud používáte **Tomcat**, vytvořte proměnnou prostředí s názvem `CATALINA_OPTS` s hodnotou `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<YOUR_SITE_NAME>` kde `<YOUR_SITE_NAME>` je název vaší služby App Service.
