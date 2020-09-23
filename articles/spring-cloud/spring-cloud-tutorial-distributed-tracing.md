---
title: Použití distribuovaného trasování u jarního cloudu Azure
description: Naučte se používat distribuované trasování jarního cloudu prostřednictvím Azure Application Insights
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/06/2019
ms.author: brendm
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 97926d5bdf3123ae50714d36ad0234872f67aa96
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90908299"
---
# <a name="use-distributed-tracing-with-azure-spring-cloud"></a>Použití distribuovaného trasování u jarního cloudu Azure

Pomocí nástrojů pro distribuované trasování v Azure jarním cloudu můžete snadno ladit a monitorovat složité problémy. Jarní cloud Azure integruje [jarní Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) s využitím Azure [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). Tato integrace poskytuje výkonnou schopnost distribuované vektorizace z Azure Portal.

::: zone pivot="programming-language-csharp"
V tomto článku se dozvíte, jak povolit aplikaci .NET Core Steeltoe k použití distribuovaného trasování.

## <a name="prerequisites"></a>Požadavky

Abyste mohli postupovat podle těchto postupů, potřebujete aplikaci Steeltoe, která je už [připravená pro nasazení do služby Azure jaře Cloud](spring-cloud-tutorial-prepare-app-deployment.md).

## <a name="dependencies"></a>Závislosti

Nainstalujte následující balíčky NuGet.

* [Steeltoe. Management. TracingCore](https://www.nuget.org/packages/Steeltoe.Management.TracingCore/)
* [Steeltoe. Management. ExporterCore](https://www.nuget.org/packages/Microsoft.Azure.SpringCloud.Client/)

## <a name="update-startupcs"></a>Aktualizovat Startup.cs

1. V `ConfigureServices` metodě zavolejte `AddDistributedTracing` `AddZipkinExporter` metody a.

   ```csharp
   public void ConfigureServices(IServiceCollection services)
   {
       services.AddDistributedTracing(Configuration);
       services.AddZipkinExporter(Configuration);
   }
   ```

1. V `Configure` metodě zavolejte `UseTracingExporter` metodu.

   ```csharp
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
        app.UseTracingExporter();
   }
   ```

## <a name="update-configuration"></a>Aktualizovat konfiguraci

Přidejte do zdroje konfigurace následující nastavení, která se použijí při spuštění aplikace ve jarním cloudu Azure:

1. Nastavte `management.tracing.alwaysSample` na true.

2. Pokud chcete zobrazit trasování mezi serverem Eureka, konfiguračním serverem a uživatelskými aplikacemi: nastavte `management.tracing.egressIgnorePattern` na/API/v2/spans |/v2/Apps/.* /Permissions |/Eureka/.*| /oauth/.*".

Například *appsettings.js* by měl obsahovat následující vlastnosti:
 
```json
"management": {
    "tracing": {
      "alwaysSample": true,
      "egressIgnorePattern": "/api/v2/spans|/v2/apps/.*/permissions|/eureka/.*|/oauth/.*"
    }
  }
```

Další informace o distribuovaném trasování v aplikacích .NET Core Steeltoe najdete v tématu [distribuované trasování](https://steeltoe.io/docs/3/tracing/distributed-tracing) v dokumentaci Steeltoe.
::: zone-end
::: zone pivot="programming-language-java"
V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Povolit distribuované trasování v Azure Portal.
> * Přidejte do své aplikace jarní cloudový Sleuth.
> * Zobrazte mapy závislostí pro vaše aplikace mikroslužeb.
> * Vyhledávejte data trasování pomocí různých filtrů.

## <a name="prerequisites"></a>Požadavky

Pokud chcete postupovat podle těchto pokynů, potřebujete službu pružinové cloudové služby Azure, která je už zřízená a spuštěná. Dokončete průvodce [nasazením prvního jarního cloudu aplikace na platformě Azure](spring-cloud-quickstart.md) , abyste zřídili a spustili službu jarní cloudu Azure.

## <a name="add-dependencies"></a>Přidat závislosti

1. Do souboru Application. Properties přidejte následující řádek:

   ```xml
   spring.zipkin.sender.type = web
   ```

   Po této změně může odesílatel Zipkin odeslat na web.

1. Tento krok přeskočte, pokud jste postupovali [podle našeho průvodce pro přípravu aplikace Azure jaře Cloud](spring-cloud-tutorial-prepare-app-deployment.md). V opačném případě přejdete do místního vývojového prostředí a upravíte soubor pom.xml tak, aby zahrnoval následující Sleuth závislost na jaře cloudu:

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
::: zone-end

## <a name="enable-application-insights"></a>Povolení Application Insights

1. V Azure Portal přejdete na stránku služby jarní cloud Azure.
1. Na stránce **monitorování** vyberte **distribuované trasování**.
1. Vyberte **Upravit nastavení** a upravte nebo přidejte nové nastavení.
1. Vytvořte nový dotaz Application Insights nebo vyberte existující.
1. Zvolte, kterou kategorii protokolování chcete monitorovat, a zadejte dobu uchování ve dnech.
1. Pokud chcete použít nové trasování, vyberte **použít** .

## <a name="view-the-application-map"></a>Zobrazit mapu aplikace

Vraťte se na stránku **distribuované trasování** a vyberte **Zobrazit mapu aplikací**. Zkontrolujte vizuální znázornění nastavení aplikace a monitorování. Informace o použití mapy aplikace najdete v tématu [Mapa aplikace: třídění distribuovaných aplikací](https://docs.microsoft.com/azure/azure-monitor/app/app-map).

## <a name="use-search"></a>Použití vyhledávání

Použijte funkci Search k dotazování na jiné konkrétní položky telemetrie. Na stránce **distribuované trasování** vyberte **Hledat**. Další informace o tom, jak používat funkci hledání, najdete v tématu [Použití vyhledávání v Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/diagnostic-search).

## <a name="use-application-insights"></a>Použít Application Insights

Application Insights poskytuje kromě funkcí mapa aplikace a funkce hledání i možnosti monitorování. Vyhledejte v Azure Portal název vaší aplikace a poté otevřete Application Insights stránku, kde najdete informace o monitorování. Další informace o tom, jak tyto nástroje používat, najdete v [Azure Monitorch dotazech protokolu](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language).

## <a name="disable-application-insights"></a>Zakázat Application Insights

1. V Azure Portal přejdete na stránku služby jarní cloud Azure.
1. V **monitorování**vyberte **distribuované trasování**.
1. Pokud chcete zakázat Application Insights, vyberte **Zakázat** .

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak povolit a pochopit distribuované trasování v Azure jarním cloudu. Další informace o vázání služby na aplikaci najdete v tématu [vázání databáze Azure Cosmos DB k aplikaci Azure jaře Cloud](spring-cloud-tutorial-bind-cosmos.md).
