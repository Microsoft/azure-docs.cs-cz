---
title: Shromažďovat metriky pro vypínacího okruhu Resilience4Jů v cloudu
description: Jak shromažďovat metriky pro vypínacího okruhu Resilience4Jů na jaře cloudu
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/15/2020
ms.custom: devx-track-java
ms.openlocfilehash: 9fc8ccc5ba21f02885b2002d5040051baac92068
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104877862"
---
# <a name="collect-spring-cloud-resilience4j-circuit-breaker-metrics-preview"></a>Shromažďovat metriky pro vypínacího okruhu (Preview) jarního cloudu Resilience4J

Tento dokument vysvětluje, jak shromažďovat Resilience4j metriky pro přerušení cloudového okruhu pomocí Application Insightsho agenta Java v procesu.  Pomocí této funkce můžete monitorovat metriky Application Insightsového okruhu z resilience4j.

K zobrazení toho, jak funguje, používáme [ukázku jaře-Cloud-okruh-break](https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo) .

## <a name="prerequisites"></a>Předpoklady

* Povolit agenta Java In-Process od [agenta java In-Process pro průvodce Application Insights](./spring-cloud-howto-application-insights.md#enable-java-in-process-agent-for-application-insights). 

* Povolte kolekci dimenzí pro resilience4j metriky z [průvodce Application Insights](../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation).

* Nainstalujte Git, Maven a Java, pokud ho vývojový počítač ještě nepoužívá.

## <a name="build-and-deploy-apps"></a>Sestavování a nasazování aplikací

Následující postup sestaví a nasadí aplikace.

1. Naklonujte a sestavte ukázkové úložiště.

```bash
git clone https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo.git
cd spring-cloud-circuitbreaker-demo && mvn clean package -DskipTests
```

2. Vytváření aplikací s koncovými body

```azurecli
az spring-cloud app create --name resilience4j --assign-endpoint \
    -s ${asc-service-name} -g ${asc-resource-group}
az spring-cloud app create --name reactive-resilience4j --assign-endpoint \
    -s ${asc-service-name} -g ${asc-resource-group}
```

3. Nasaďte aplikace.

```azurecli
az spring-cloud app deploy -n resilience4j \
    --jar-path ./spring-cloud-circuitbreaker-demo-resilience4j/target/spring-cloud-circuitbreaker-demo-resilience4j-0.0.1.BUILD-SNAPSHOT.jar \
    -s ${service_name} -g ${resource_group}
az spring-cloud app deploy -n reactive-resilience4j \
    --jar-path ./spring-cloud-circuitbreaker-demo-reactive-resilience4j/target/spring-cloud-circuitbreaker-demo-reactive-resilience4j-0.0.1.BUILD-SNAPSHOT.jar \
    -s ${service_name} -g ${resource_group}
```

> [!Note]
>
> * Zahrňte požadovanou závislost pro Resilience4j:
>
>   ```xml
>   <dependency>
>       <groupId>io.github.resilience4j</groupId>
>       <artifactId>resilience4j-micrometer</artifactId>
>   </dependency>
>   <dependency>
>       <groupId>org.springframework.cloud</groupId>
>       <artifactId>spring-cloud-starter-circuitbreaker-resilience4j</artifactId>
>   </dependency>
>   ```
> * Kód zákazníka musí používat rozhraní API `CircuitBreakerFactory` , které je implementováno jako `bean` automaticky vytvořené při zahrnutí spouštěcího programu pro přerušení cloudového okruhu. Podrobnosti najdete v tématu [přerušení cloudového okruhu](https://spring.io/projects/spring-cloud-circuitbreaker#overview).
>
> * Následující dvě závislosti mají konflikty s balíčky resilient4j výše.  Ujistěte se, že ji zákazník neobsahuje.
>
>   ```xml
>   <dependency>
>       <groupId>org.springframework.cloud</groupId>
>       <artifactId>spring-cloud-starter-sleuth</artifactId>
>   </dependency>
>   <dependency>
>       <groupId>org.springframework.cloud</groupId>
>       <artifactId>spring-cloud-starter-zipkin</artifactId>
>   </dependency>
>   ```
>
>
> Přejděte na adresu URL poskytovanou aplikacemi brány a získejte přístup ke koncovému bodu z [jarního cloudového okruhu – ukázka](https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo) následujícím způsobem:
>
>   ```console
>   /get
>   /get/delay/{seconds}
>   /get/fluxdelay/{seconds}
>   ```

## <a name="locate-resilence4j-metrics-from-portal"></a>Vyhledat metriky Resilence4j z portálu

1. Vyberte okno **Application Insights** na portálu Azure jaře Cloud Portal a klikněte na **Application Insights**.

   [![resilience4J 0](media/spring-cloud-resilience4j/resilience4J-0.png)](media/spring-cloud-resilience4j/resilience4J-0.PNG)

2. Vyberte **metriky** ze stránky **Application Insights** .  Z **oboru názvů metriky** vyberte **Azure. ApplicationInsights** .  Také vyberte **resilience4j_circuitbreaker_buffered_calls** metriky s **průměrem**.

   [![resilience4J 1](media/spring-cloud-resilience4j/resilience4J-1.png)](media/spring-cloud-resilience4j/resilience4J-1.PNG)

3. Vyberte **resilience4j_circuitbreaker_calls** metriky a **průměr**.

   [![resilience4J 2](media/spring-cloud-resilience4j/resilience4J-2.png)](media/spring-cloud-resilience4j/resilience4J-2.PNG)

4. Vyberte **resilience4j_circuitbreaker_calls**  metriky a **průměr**.  Klikněte na **Přidat filtr** a pak vyberte název jako **createNewAccount**.

   [![resilience4J 3](media/spring-cloud-resilience4j/resilience4J-3.png)](media/spring-cloud-resilience4j/resilience4J-3.PNG)

5. Vyberte **resilience4j_circuitbreaker_calls**  metriky a **průměr**.  Pak klikněte na **použít rozdělení** a vyberte **druh**.

   [![resilience4J 4](media/spring-cloud-resilience4j/resilience4J-4.png)](media/spring-cloud-resilience4j/resilience4J-4.PNG)

6. Vyberte **resilience4j_circuitbreaker_calls**,**resilience4j_circuitbreaker_buffered_calls** a **resilience4j_circuitbreaker_slow_calls** metriky s **průměrem**.

   [![resilience4J 5](media/spring-cloud-resilience4j/resilience4j-5.png)](media/spring-cloud-resilience4j/resilience4j-5.PNG)

## <a name="see-also"></a>Viz také

* [Application Insights](spring-cloud-howto-application-insights.md)
* [Distribuované trasování](spring-cloud-howto-distributed-tracing.md)
* [Řídicí panel pro přerušení okruhu](spring-cloud-tutorial-circuit-breaker.md)