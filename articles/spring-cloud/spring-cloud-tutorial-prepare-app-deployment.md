---
title: Jak na to – příprava aplikace Java Spring pro nasazení v Azure Spring Cloudu
description: V tomto tématu připravíte aplikaci Java Spring pro nasazení do Azure Spring Cloud.
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 02/03/2020
ms.author: brendm
ms.openlocfilehash: 025dbc56fb46ef5b6225d35564b8e4ac3c82e6e3
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414468"
---
# <a name="prepare-a-java-spring-application-for-deployment-in-azure-spring-cloud"></a>Příprava aplikace Java Spring pro nasazení v Azure Spring Cloud

Toto téma ukazuje, jak připravit existující java jarní aplikaci pro nasazení do Azure Spring Cloud. Pokud je Azure Spring Cloud správně nakonfigurovaný, poskytuje robustní služby pro monitorování, škálování a aktualizaci aplikace Java Spring Cloud.

Další příklady vysvětlují, jak nasadit aplikaci do Azure Spring Cloud, když je nakonfigurovaný soubor POM. 
* [Spuštění aplikace pomocí portálu Azure](spring-cloud-quickstart-launch-app-portal.md)
* [Spuštění aplikace pomocí azure cli](spring-cloud-quickstart-launch-app-cli.md)

Tento článek vysvětluje požadované závislosti a jak je přidat do souboru POM.

## <a name="java-runtime-version"></a>Java Runtime verze

V Azure Spring Cloudu můžou běžet jenom jarní/java aplikace.

Azure Spring Cloud podporuje Java 8 i Java 11. Hostitelské prostředí obsahuje nejnovější verzi Azul Zulu OpenJDK pro Azure. Další informace o Azul Zulu OpenJDK pro Azure najdete [v tématu Instalace JDK](https://docs.microsoft.com/azure/java/jdk/java-jdk-install).

## <a name="spring-boot-and-spring-cloud-versions"></a>Verze Spring Boot a Spring Cloud

Chcete-li připravit existující jarní spuštění aplikace pro nasazení do Azure Spring Cloud patří jarní spuštění a jarní cloud závislosti v souboru pom aplikace, jak je znázorněno v následujících částech.

Azure Spring Cloud podporuje jenom aplikace spring boot u verze 2.1 nebo verze 2.2. V následující tabulce jsou uvedeny podporované kombinace spring boot a spring cloud:

Verze spring bootu | Jarní cloudová verze
---|---
2.1 | Greenwich.RELEASE
2,2 | Hoxton.RELEASE

### <a name="dependencies-for-spring-boot-version-21"></a>Závislosti pro jarní spuštění verze 2.1

Pro jarní spuštění verze 2.1 přidejte následující závislosti do souboru POM aplikace.

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
                <version>Greenwich.SR4</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

### <a name="dependencies-for-spring-boot-version-22"></a>Závislosti pro jarní spuštění verze 2.2

Pro jarní spuštění verze 2.2 přidejte následující závislosti do souboru POM aplikace.

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
                <version>Hoxton.SR1</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

## <a name="azure-spring-cloud-client-dependency"></a>Závislost klienta Azure Spring Cloud

Azure Spring Cloud hostuje a spravuje komponenty Spring Cloud. Mezi součásti patří Spring Cloud Service Registry a Spring Cloud Config Server. Zahrňte klientskou knihovnu Azure Spring Cloud do svých závislostí, abyste mohli komunikovat s instancí služby Azure Spring Cloud.

V následující tabulce jsou uvedeny správné verze Azure Spring Cloud pro vaši aplikaci, která používá spring boot a spring cloud.

Verze spring bootu | Jarní cloudová verze | Verze Azure Spring Cloud
---|---|---
2.1 | Greenwich.RELEASE | 2.1
2,2 | Hoxton.RELEASE | 2,2

Zahrňte do souboru pom.xml jednu z následujících závislostí. Vyberte závislost, jejíž verze Azure Spring Cloud odpovídá vaší vlastní.

### <a name="dependency-for-azure-spring-cloud-version-21"></a>Závislost pro Azure Spring Cloud verze 2.1

Pro jarní spuštění verze 2.1 přidejte následující závislost do souboru POM aplikace.

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.1</version>
</dependency>
```

### <a name="dependency-for-azure-spring-cloud-version-22"></a>Závislost pro Azure Spring Cloud verze 2.2

Pro jarní spuštění verze 2.2 přidejte následující závislost do souboru POM aplikace.

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.2.0</version>
</dependency>
```

## <a name="other-required-dependencies"></a>Další požadované závislosti

Chcete-li povolit integrované funkce Azure Spring Cloud, vaše aplikace musí obsahovat následující závislosti. Toto zahrnutí zajišťuje, že vaše aplikace nakonfiguruje sám správně s každou komponentu.

### <a name="enablediscoveryclient-annotation"></a>Povolit poznámky klienta Discovery

Přidejte následující poznámku do zdrojového kódu aplikace.
```java
@EnableDiscoveryClient
```
Podívejte se například na aplikaci prasačné metriky z předchozích příkladů:
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

### <a name="service-registry-dependency"></a>Závislost registru služeb

Chcete-li použít spravovanou službu Azure Service Registry, zahrňte `spring-cloud-starter-netflix-eureka-client` závislost do souboru pom.xml, jak je znázorněno zde:

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

Koncový bod serveru registru služby se automaticky vloží jako proměnné prostředí s vaší aplikací. Aplikace se mohou zaregistrovat na serveru registru služeb a zjistit další závislé mikroslužby.

### <a name="distributed-configuration-dependency"></a>Závislost distribuované konfigurace

Chcete-li povolit distribuovanou konfiguraci, zahrňte do oddílu závislostí souboru pom.xml následující `spring-cloud-config-client` závislost:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> Nespecifikujte `spring.cloud.config.enabled=false` v konfiguraci bootstrap. V opačném případě aplikace přestane pracovat s config serverem.

### <a name="metrics-dependency"></a>Závislost metrik

Zahrňte `spring-boot-starter-actuator` závislost do části závislostí souboru pom.xml, jak je znázorněno zde:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

 Metriky jsou pravidelně vykrádány z koncových bodů JMX. Metriky můžete vizualizovat pomocí portálu Azure.

### <a name="distributed-tracing-dependency"></a>Závislost distribuovaného trasování

Do oddílu závislostí v souboru pom.xml zahrňte následující `spring-cloud-starter-sleuth` a `spring-cloud-starter-zipkin` závislosti:

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

 Taky musíte povolit instanci Azure Application Insights pro práci s instancí služby Azure Spring Cloud. Přečtěte si [kurz o distribuovaném trasování](spring-cloud-tutorial-distributed-tracing.md) a zjistěte, jak používat Application Insights s Azure Spring Cloud.

## <a name="see-also"></a>Viz také
* [Analýza protokolů a metrik aplikací](https://docs.microsoft.com/azure/spring-cloud/diagnostic-services)
* [Nastavení konfiguračního serveru](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-config-server)
* [Použití distribuovaného trasování pomocí Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-distributed-tracing)
* [Průvodce rychlým startem pružiny](https://spring.io/quickstart)
* [Dokumentace k jarnímu startu](https://spring.io/projects/spring-boot)

## <a name="next-steps"></a>Další kroky

V tomto tématu jste se dozvěděli, jak nakonfigurovat aplikaci Java Spring pro nasazení do Azure Spring Cloud. Informace o tom, jak nastavit instanci konfiguračního serveru, naleznete v následujícím článku.

> [!div class="nextstepaction"]
> [Přečtěte si, jak nastavit instanci konfiguračního serveru](spring-cloud-tutorial-config-server.md)

Další ukázky jsou k dispozici na [GitHubu: Ukázky Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples).
