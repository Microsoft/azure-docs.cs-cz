---
title: Postup přípravy aplikace pružiny v jazyce Java pro nasazení v jarním cloudu Azure
description: Přečtěte si, jak připravit pružinovou aplikaci Java pro nasazení do jarního cloudu Azure.
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 02/03/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 59318cca33ba1607498546161764aa3aaaaea13e
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2020
ms.locfileid: "90014935"
---
# <a name="prepare-a-java-spring-application-for-deployment-in-azure-spring-cloud"></a>Příprava pružinové aplikace Java pro nasazení v jarním cloudu Azure

V tomto tématu se dozvíte, jak připravit existující aplikaci pružiny v jazyce Java pro nasazení do jarního cloudu Azure. Pokud jsou správně nakonfigurovány, poskytuje Azure jarní Cloud robustní služby pro monitorování, škálování a aktualizaci vaší aplikace v Java pro jarní Cloud.

Před spuštěním tohoto příkladu můžete vyzkoušet [základní rychlý Start](spring-cloud-quickstart.md).

Další příklady vysvětlují, jak nasadit aplikaci do jarního cloudu Azure při konfiguraci souboru POM. 
* [Spuštění první aplikace](spring-cloud-quickstart.md)
* [Sestavování a spouštění mikroslužeb](spring-cloud-quickstart-sample-app-introduction.md)

Tento článek vysvětluje požadované závislosti a jejich přidání do souboru POM.

## <a name="java-runtime-version"></a>Běhová verze Java

V Azure jaře cloudu se dají spouštět jenom pružinové a Java aplikace.

Azure jarní Cloud podporuje Java 8 i Java 11. Hostující prostředí obsahuje nejnovější verzi Azul Zulu OpenJDK pro Azure. Další informace o Azul Zulu OpenJDK pro Azure najdete v tématu [instalace JDK](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install).

## <a name="spring-boot-and-spring-cloud-versions"></a>Jarní spouštěcí a jarní verze cloudu

K přípravě existující aplikace pro nasazování do Azure jarního cloudu je třeba, aby se v souboru POM aplikace napravily síťové závislosti pružinového a jarního cloudu, jak je znázorněno v následujících oddílech.

Jarní cloud Azure podporuje pouze aplikace pružiny pro spuštění, které jsou ve verzi 2,1 nebo 2,2. V následující tabulce jsou uvedeny podporované kombinace pružinové spouštěcí a jarní cloudové sady:

Jarní spouštěcí verze | Jarní cloudová verze
---|---
2.1 | Střední verze
2,2 | Hoxton.SR8
2.3 | Hoxton.SR8

### <a name="dependencies-for-spring-boot-version-21"></a>Závislosti pro jaře Booting verze 2,1

Pro jaře Booting verze 2,1 přidejte do souboru POM aplikace následující závislosti.

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.12.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Greenwich.RELEASE</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

### <a name="dependencies-for-spring-boot-version-22"></a>Závislosti pro jaře Booting verze 2,2

Pro jaře Booting verze 2,2 přidejte do souboru POM aplikace následující závislosti.

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.4.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR8</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```
### <a name="dependencies-for-spring-boot-version-23"></a>Závislosti pro jaře Booting verze 2,3

Pro jaře Booting verze 2,3 přidejte do souboru POM aplikace následující závislosti.

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.0.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR8</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```
## <a name="azure-spring-cloud-client-dependency"></a>Závislost klienta na jaře cloudu Azure

Azure jaře Cloud hostuje a spravuje jarní cloudové komponenty. Mezi tyto komponenty patří jarní cloudová služba a server pro konfiguraci jarního cloudu. Doporučuje se použít jaře Boot 2,2 nebo 2,3. V případě jarního spouštění 2,1 budete muset do svých závislostí zahrnout klientskou knihovnu pro jarní cloudy Azure, aby bylo možné komunikovat s instancí cloudové služby Azure jaře.

V následující tabulce jsou uvedené správné verze cloudových cloudů Azure pro vaši aplikaci, které využívají jarní spouštěcí a jarní Cloud.

Jarní spouštěcí verze | Jarní cloudová verze | Verze počátečního cloudu pro Azure jaře pro klienta
---|---|---
2.1. x | Střední verze | 2.1.2
2.2. x | Hoxton.SR8 | Není potřeba
2.3. x | Hoxton.SR8 | Není potřeba

Pokud používáte jarní spouštění 2,1, zahrňte do souboru pom.xml následující dependenciy.

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.2</version>
</dependency>
```

## <a name="other-recommended-dependencies-to-enable-azure-spring-cloud-features"></a>Další doporučené závislosti pro povolení funkcí Azure jarního cloudu

Pokud chcete pro distribuované trasování povolit integrované funkce Azure jarního cloudu z registru služby, musíte do své aplikace zahrnout taky následující závislosti. Některé z těchto závislostí můžete odstranit, pokud nepotřebujete odpovídající funkce pro konkrétní aplikace.

### <a name="service-registry"></a>Registr služby

Pokud chcete použít spravovanou službu Azure Service Registry, zahrňte `spring-cloud-starter-netflix-eureka-client` závislost do souboru pom.xml, jak je znázorněno zde:

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

Koncový bod serveru registru služby se do vaší aplikace automaticky vloží jako proměnné prostředí. Aplikace se mohou registrovat pomocí serveru registru služby a zjišťovat další závislé mikroslužby.

#### <a name="enablediscoveryclient-annotation"></a>EnableDiscoveryClient anotace

Přidejte následující anotaci do zdrojového kódu aplikace.
```java
@EnableDiscoveryClient
```
Podívejte se například na aplikaci piggymetrics z předchozích příkladů:
```java
package com.piggymetrics.gateway;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.netflix.zuul.EnableZuulProxy;

@SpringBootApplication
@EnableDiscoveryClient
@EnableZuulProxy

public class GatewayApplication {
    public static void main(String[] args) {
        SpringApplication.run(GatewayApplication.class, args);
    }
}
```

### <a name="distributed-configuration"></a>Distribuovaná konfigurace

Pokud chcete povolit distribuovanou konfiguraci, zahrňte `spring-cloud-config-client` do části závislosti v souboru pom.xml následující závislost:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> Nezadávejte `spring.cloud.config.enabled=false` v konfiguraci Bootstrap. V opačném případě přestane vaše aplikace pracovat s konfiguračním serverem.

### <a name="metrics"></a>Metriky

Zahrňte `spring-boot-starter-actuator` závislost do oddílu závislosti v souboru pom.xml, jak je znázorněno zde:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

 Metriky jsou pravidelně načítány z koncových bodů JMX. Metriky můžete vizualizovat pomocí Azure Portal.

 > [!WARNING]
 > Zadejte prosím `spring.jmx.enabled=true` vlastnost Configuration. V opačném případě se metriky nedají vizuálně vyAzure Portal.

### <a name="distributed-tracing"></a>Distribuované trasování

`spring-cloud-starter-sleuth` `spring-cloud-starter-zipkin` Do oddílu závislosti v souboru pom.xml zahrňte následující a závislosti:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-sleuth</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zipkin</artifactId>
</dependency>
```

 Musíte taky povolit instanci Azure Application Insights, abyste mohli pracovat s instancí cloudové služby Azure na jaře. Informace o tom, jak používat Application Insights s využitím služby Azure Pramenitého cloudu, najdete v [dokumentaci k distribuovanému trasování](spring-cloud-tutorial-distributed-tracing.md).

## <a name="see-also"></a>Viz také
* [Analýza protokolů a metrik aplikace](https://docs.microsoft.com/azure/spring-cloud/diagnostic-services)
* [Nastavení konfiguračního serveru](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-config-server)
* [Použití distribuovaného trasování u jarního cloudu Azure](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-distributed-tracing)
* [Průvodce rychlým startem pružiny](https://spring.io/quickstart)
* [Dokumentace ke jarnímu spuštění](https://spring.io/projects/spring-boot)

## <a name="next-steps"></a>Další kroky

V tomto tématu jste zjistili, jak nakonfigurovat svoji aplikaci v jazyce Java pro nasazení do jarního cloudu Azure. Informace o tom, jak nastavit instanci konfiguračního serveru, najdete v tématu [Nastavení instance konfiguračního serveru](spring-cloud-tutorial-config-server.md).

Další ukázky jsou k dispozici na GitHubu: [ukázky Azure pro jarní Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples).
