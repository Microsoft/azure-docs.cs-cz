---
title: Příprava jarní aplikace na nasazení v Azure jaře cloudu | Microsoft Docs
description: V tomto rychlém startu připravíte pro nasazení aplikaci pružiny Java.
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/06/2019
ms.author: v-vasuke
ms.openlocfilehash: 98d9f3f656cff84cec8d223ed535255157155bd2
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72039004"
---
# <a name="tutorial-prepare-a-java-spring-application-for-deployment-in-azure-spring-cloud"></a>Kurz: Příprava aplikace na jaře v jazyce Java pro nasazení v jarním cloudu Azure

V tomto rychlém startu se dozvíte, jak připravit stávající cloudovou aplikaci Java pro nasazení do jarního cloudu Azure.  Správně nakonfigurováno, poskytuje Azure jaře Cloud robustní služby pro monitorování, škálování a aktualizaci vaší jarní cloudové aplikace. 

## <a name="java-runtime-version"></a>Běhová verze Java

V Azure jaře cloudu se dají spouštět jenom pružinové a Java aplikace.

Podporují se jazyky Java 8 i Java 11. Hostující prostředí obsahuje nejnovější Azul Zulu OpenJDK pro Azure. Další informace o Azul Zulu OpenJDK pro Azure najdete v [tomto článku](https://docs.microsoft.com/azure/java/jdk/java-jdk-install) . 

## <a name="spring-boot-and-spring-cloud-versions"></a>Jarní spouštěcí a jarní verze cloudu

V Azure jaře cloudu se podporují jenom aplikace s pružinou pro spouštění. Podporují se jarní spouštění 2,0 a 2,1. V níže uvedené tabulce jsou podporované kombinace jarního spuštění a jarního cloudu.

Jarní spouštěcí verze | Jarní cloudová verze
---|---
2.0. x | Finchley. RELEASE
2.1. x | Střední verze

Ověřte, že váš soubor `pom.xml` má závislosti na jarním startu a jarním cloudu na základě vaší verze.

### <a name="version-20"></a>Verze 2,0:

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.9.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Finchley.SR4</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

### <a name="version-21"></a>Verze 2,1:

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.8.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Greenwich.SR3</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

## <a name="azure-spring-cloud-client-dependency"></a>Závislost klienta na jaře cloudu Azure

Hostitelská služba Azure jaře hostuje a spravuje komponenty jarního cloudu, jako je například registr jarní cloudová služba a jarní cloudový konfigurační server. Zahrňte do svých závislostí klientskou knihovnu pro jarní cloudy Azure, aby bylo možné komunikovat s instancí cloudové služby Azure na jaře.

V následující tabulce jsou uvedené správné verze cloudové aplikace pro jarní spuštění nebo pružiny.

Jarní spouštěcí verze | Jarní cloudová verze | Verze cloudu pro Azure jaře
---|---|---
2.0. x | Finchley. RELEASE | 2.0.0 – SNÍMEK
2.1. x | Střední verze | 2.1.0 – SNÍMEK

Zahrňte tento fragment kódu do `pom.xml` se správnou verzí cloudu pro Azure jaře v závislosti:

```xml
    <repositories>
        <repository>
            <id>nexus-snapshots</id>
            <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
        </repository>
    </repositories>
    
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.0-SNAPSHOT</version>
    </dependency>
```

## <a name="other-required-dependencies"></a>Další požadované závislosti

Aby bylo možné povolit integrované funkce Azure jarního cloudu, musí vaše aplikace zahrnovat následující závislosti. Tím se zajistí, že se vaše aplikace správně nakonfiguruje s každou komponentou.  

### <a name="service-registry"></a>Registr služby

Pokud chcete použít spravovanou službu Azure Service Registry, zahrňte `spring-cloud-starter-netflix-eureka-client` v `POM.xml`, jak je znázorněno níže.

Koncový bod serveru registru služby se do vaší aplikace automaticky vloží jako proměnné prostředí. Aplikace se budou moct zaregistrovat na serveru registru služby a zjišťovat další závislé mikroslužby.

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

### <a name="distributed-configuration"></a>Distribuovaná konfigurace

Pokud chcete povolit distribuovanou konfiguraci, zahrňte `spring-cloud-config-client` v části závislosti v `pom.xml`.

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> Nezadávejte `spring.cloud.config.enabled=false` v konfiguraci Bootstrap, protože přestane aplikace pracovat s konfiguračním serverem.

### <a name="metrics"></a>Metriky

Do souboru pom. xml přidejte `spring-boot-starter-actuator` v části závislosti. Metriky budou pravidelně načítány z koncových bodů JMX a lze je vizuálně vymezit pomocí Azure Portal.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

### <a name="distributed-tracing"></a>Distribuované trasování

Do části závislosti v souboru pom. xml přidejte `spring-cloud-starter-sleuth` a `spring-cloud-starter-zipkin`, jak je uvedeno níže. Také je potřeba povolit instanci Azure App Insights, aby fungovala s instancí cloudové služby Azure na jaře. Přečtěte si další informace o tom, jak povolit službu App Insights pomocí Azure [jaře cloudu](spring-cloud-tutorial-distributed-tracing.md) .

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

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak nakonfigurovat aplikaci pro pružinu v jazyce Java pro nasazení do jarního cloudu Azure.  Chcete-li se dozvědět, jak povolit konfigurační server, pokračujte k dalšímu kurzu.

> [!div class="nextstepaction"]
> [Přečtěte si, jak nastavit konfigurační server](spring-cloud-tutorial-config-server.md).

