---
title: Kurz – použití distribuovaného trasování pomocí Azure Spring Cloud
description: Tento kurz ukazuje, jak používat distribuované trasování Spring Cloudu prostřednictvím Přehledů aplikací Azure
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: brendm
ms.openlocfilehash: 0815aa084462d1b829d64cd7c5d6fa7cebf534fc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78273215"
---
# <a name="use-distributed-tracing-with-azure-spring-cloud"></a>Použití distribuovaného trasování pomocí Azure Spring Cloud

Díky distribuovaným nástrojům pro trasování v Azure Spring Cloudu můžete snadno ladit a monitorovat složité problémy. Azure Spring Cloud integruje [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) s [Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). Tato integrace poskytuje výkonné možnosti distribuovaného trasování z portálu Azure.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Povolte distribuované trasování na webu Azure Portal.
> * Přidejte do své aplikace sleuth Spring Cloud Sleuth.
> * Zobrazení map závislostí pro vaše aplikace mikroslužeb.
> * Vyhledávání trasovacích dat pomocí různých filtrů.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete službu Azure Spring Cloud, která je již zřízená a spuštěná. Dokončete [rychlý start při nasazování aplikace prostřednictvím azure cli](spring-cloud-quickstart-launch-app-cli.md) pro zřízení a spuštění služby Azure Spring Cloud.
    
## <a name="add-dependencies"></a>Přidání závislostí

1. Do souboru application.properties přidejte následující řádek:

   ```xml
   spring.zipkin.sender.type = web
   ```

   Po této změně může odesílatel Zipkin odeslat na web.

1. Tento krok přeskočte, pokud jste se [řídili naším průvodcem přípravou aplikace Azure Spring Cloud](spring-cloud-tutorial-prepare-app-deployment.md). V opačném případě přejděte do místního vývojového prostředí a upravte soubor pom.xml tak, aby zahrnoval následující závislost Spring Cloud Sleuth:

    ```xml
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-sleuth</artifactId>
                <version>${spring-cloud-sleuth.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-sleuth</artifactId>
        </dependency>
    </dependencies>
    ```

1. Sestavte a nasaďte znovu pro svou službu Azure Spring Cloud tak, aby odrážela tyto změny.

## <a name="modify-the-sample-rate"></a>Změna vzorkovací frekvence

Rychlost, jakou se shromažďuje telemetrická data, můžete změnit úpravou vzorkovací frekvence. Chcete-li například vzorkovat polovinu tak často, otevřete soubor application.properties a změňte následující řádek:

```xml
spring.sleuth.sampler.probability=0.5
```

Pokud jste již vytvořili a nasadili aplikaci, můžete upravit vzorkovací frekvenci. Udělejte to tak, že přidáte předchozí řádek jako proměnnou prostředí v azure cli nebo na webu Azure Portal.

## <a name="enable-application-insights"></a>Povolení Application Insights

1. Přejděte na stránku služby Azure Spring Cloud na webu Azure Portal.
1. Na stránce **Sledování** vyberte **Položku Distribuované trasování**.
1. Vyberte **Upravit nastavení,** chcete-li upravit nebo přidat nové nastavení.
1. Vytvořte nový dotaz Application Insights nebo vyberte existující dotaz.
1. Zvolte kategorii protokolování, kterou chcete sledovat, a určete dobu uchování ve dnech.
1. Vyberte **Použít,** chcete-li použít nové vektorizace.

## <a name="view-the-application-map"></a>Zobrazit mapu aplikace

Vraťte se na stránku **Distribuované trasování** a vyberte **Zobrazit mapu aplikace**. Zkontrolujte vizuální reprezentaci aplikace a nastavení monitorování. Informace o použití mapy aplikace naleznete v [tématu Mapa aplikace: Třídění distribuovaných aplikací](https://docs.microsoft.com/azure/azure-monitor/app/app-map).

## <a name="use-search"></a>Použití vyhledávání

Pomocí funkce vyhledávání můžete dotazovat na jiné specifické položky telemetrie. Na stránce **Distribuované trasování** vyberte **Hledat**. Další informace o použití funkce vyhledávání naleznete v [tématu Použití funkce Hledání v přehledech aplikací](https://docs.microsoft.com/azure/azure-monitor/app/diagnostic-search).

## <a name="use-application-insights"></a>Použití přehledů aplikací

Application Insights poskytuje kromě mapy aplikací a funkce vyhledávání také možnosti monitorování. Vyhledejte název aplikace na webu Azure Portal a otevřete stránku Application Insights, kde najdete informace o monitorování. Další pokyny k použití těchto nástrojů najdete v [dotazech protokolu Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language).

## <a name="disable-application-insights"></a>Zakázat přehledy aplikací

1. Přejděte na stránku služby Azure Spring Cloud na webu Azure Portal.
1. Při **sledování**vyberte **možnost Distribuované trasování**.
1. Výběrem **možnosti Zakázat** zakážete přehledy aplikací.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak povolit a pochopit distribuované trasování v Azure Spring Cloud. Chcete-li se dozvědět, jak svázat aplikaci s databází Azure Cosmos DB, pokračujte dalším kurzem.

> [!div class="nextstepaction"]
> [Zjistěte, jak vytvořit vazbu na databázi Azure Cosmos DB](spring-cloud-tutorial-bind-cosmos.md)
