---
title: Kurz – použití distribuovaného trasování u jarního cloudu Azure
description: V tomto kurzu se dozvíte, jak používat distribuované trasování jarního cloudu prostřednictvím Azure Application Insights
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: brendm
ms.openlocfilehash: 0815aa084462d1b829d64cd7c5d6fa7cebf534fc
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273215"
---
# <a name="use-distributed-tracing-with-azure-spring-cloud"></a>Použití distribuovaného trasování u jarního cloudu Azure

Pomocí nástrojů pro distribuované trasování v Azure jarním cloudu můžete snadno ladit a monitorovat složité problémy. Jarní cloud Azure integruje [jarní Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) s využitím Azure [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). Tato integrace poskytuje výkonnou schopnost distribuované vektorizace z Azure Portal.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Povolit distribuované trasování v Azure Portal.
> * Přidejte do své aplikace jarní cloudový Sleuth.
> * Zobrazte mapy závislostí pro vaše aplikace mikroslužeb.
> * Vyhledávejte data trasování pomocí různých filtrů.

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto kurzu potřebujete službu pružinové cloudové služby Azure, která je už zřízená a spuštěná. Dokončete [rychlý Start při nasazení aplikace přes Azure CLI](spring-cloud-quickstart-launch-app-cli.md) a zřiďte a spusťte cloudovou službu Azure na jaře.
    
## <a name="add-dependencies"></a>Přidat závislosti

1. Do souboru Application. Properties přidejte následující řádek:

   ```xml
   spring.zipkin.sender.type = web
   ```

   Po této změně může odesílatel Zipkin odeslat na web.

1. Tento krok přeskočte, pokud jste postupovali [podle našeho průvodce pro přípravu aplikace Azure jaře Cloud](spring-cloud-tutorial-prepare-app-deployment.md). V opačném případě přejdete do svého místního vývojového prostředí a upravíte soubor pom. XML tak, aby zahrnoval následující Sleuth závislost pružinového cloudu:

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

1. Sestavte a nasaďte znovu pro službu jarní cloudovou službu Azure, aby odrážela tyto změny.

## <a name="modify-the-sample-rate"></a>Úprava vzorkovací frekvence

Můžete změnit rychlost shromažďování telemetrie úpravou vzorkovací frekvence. Například pokud chcete vzorkovat polovinu jako často, otevřete soubor Application. Properties a změňte následující řádek:

```xml
spring.sleuth.sampler.probability=0.5
```

Pokud jste už sestavili a nasadili aplikaci, můžete upravit vzorkovací frekvenci. Uděláte to tak, že přidáte předchozí řádek jako proměnnou prostředí v rozhraní příkazového řádku Azure CLI nebo Azure Portal.

## <a name="enable-application-insights"></a>Povolení Application Insights

1. V Azure Portal přejdete na stránku služby jarní cloud Azure.
1. Na stránce **monitorování** vyberte **distribuované trasování**.
1. Vyberte **Upravit nastavení** a upravte nebo přidejte nové nastavení.
1. Vytvořte nový dotaz Application Insights nebo vyberte existující.
1. Zvolte, kterou kategorii protokolování chcete monitorovat, a zadejte dobu uchování ve dnech.
1. Pokud chcete použít nové trasování, vyberte **použít** .

## <a name="view-the-application-map"></a>Zobrazit mapu aplikace

Vraťte se na stránku **distribuované trasování** a vyberte **Zobrazit mapu aplikací**. Zkontrolujte vizuální znázornění nastavení aplikace a monitorování. Informace o použití mapy aplikace najdete v tématu [Mapa aplikace: třídění distribuovaných aplikací](https://docs.microsoft.com/azure/azure-monitor/app/app-map).

## <a name="use-search"></a>Použít hledání

Použijte funkci Search k dotazování na jiné konkrétní položky telemetrie. Na stránce **distribuované trasování** vyberte **Hledat**. Další informace o tom, jak používat funkci hledání, najdete v tématu [Použití vyhledávání v Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/diagnostic-search).

## <a name="use-application-insights"></a>Použít Application Insights

Application Insights poskytuje kromě funkcí mapa aplikace a funkce hledání i možnosti monitorování. Vyhledejte v Azure Portal název vaší aplikace a poté otevřete Application Insights stránku, kde najdete informace o monitorování. Další informace o tom, jak tyto nástroje používat, najdete v [Azure Monitorch dotazech protokolu](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language).

## <a name="disable-application-insights"></a>Zakázat Application Insights

1. V Azure Portal přejdete na stránku služby jarní cloud Azure.
1. V **monitorování**vyberte **distribuované trasování**.
1. Pokud chcete zakázat Application Insights, vyberte **Zakázat** .

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak povolit a pochopit distribuované trasování v Azure jarním cloudu. Další informace o tom, jak vytvořit vazby aplikace k databázi Azure Cosmos DB, najdete v dalším kurzu.

> [!div class="nextstepaction"]
> [Naučte se navazovat databáze Azure Cosmos DB.](spring-cloud-tutorial-bind-cosmos.md)
