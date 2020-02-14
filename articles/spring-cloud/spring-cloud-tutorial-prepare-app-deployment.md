---
title: Kurz – Příprava pružinové aplikace Java pro nasazení v jarním cloudu Azure
description: V tomto kurzu připravíte pružinovou aplikaci Java pro nasazení do jarního cloudu Azure.
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 02/03/2020
ms.author: brendm
ms.openlocfilehash: af3611e4c4d1f5d8ca52b3ceb80d79dcfd7d2061
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190744"
---
# <a name="prepare-a-java-spring-application-for-deployment-in-azure-spring-cloud"></a>Příprava pružinové aplikace Java pro nasazení v jarním cloudu Azure

V tomto rychlém startu se dozvíte, jak připravit existující aplikaci pružiny v jazyce Java pro nasazení do jarního cloudu Azure. Pokud jsou správně nakonfigurovány, poskytuje Azure jarní Cloud robustní služby pro monitorování, škálování a aktualizaci vaší aplikace v Java pro jarní Cloud.

## <a name="java-runtime-version"></a>Běhová verze Java

V Azure jaře cloudu se dají spouštět jenom pružinové a Java aplikace.

Azure jarní Cloud podporuje Java 8 i Java 11. Hostující prostředí obsahuje nejnovější verzi Azul Zulu OpenJDK pro Azure. Další informace o Azul Zulu OpenJDK pro Azure najdete v tématu [instalace JDK](https://docs.microsoft.com/azure/java/jdk/java-jdk-install).

## <a name="spring-boot-and-spring-cloud-versions"></a>Jarní spouštěcí a jarní verze cloudu

Jarní cloud Azure podporuje jenom aplikace pro pružinové spouštění. Podporuje jak pružinové spouštění verze 2,1, tak verze 2,2. V následující tabulce jsou uvedeny podporované kombinace pružinové spouštěcí a jarní cloudové sady:

Jarní spouštěcí verze | Jarní cloudová verze
---|---
2.1 | Střední verze
2.2 | Hoxton. RELEASE

Ověřte, že soubor pom. XML má správné závislosti na jaře a pružinovém cloudu na základě vaší jarní spouštěcí verze.

### <a name="dependencies-for-spring-boot-version-21"></a>Závislosti pro jaře Booting verze 2,1

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

### <a name="dependencies-for-spring-boot-version-22"></a>Závislosti pro jaře Booting verze 2,2

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

## <a name="azure-spring-cloud-client-dependency"></a>Závislost klienta na jaře cloudu Azure

Azure jaře Cloud hostuje a spravuje komponenty pro jarní Cloud. Mezi tyto komponenty patří jarní cloudová služba registru a server pro konfiguraci jarního cloudu. Zahrňte do svých závislostí klientskou knihovnu jarních cloudů Azure, aby bylo možné komunikovat s instancí cloudové služby Azure na jaře.

V následující tabulce jsou uvedené správné verze cloudových cloudů Azure pro vaši aplikaci, které využívají jarní spouštěcí a jarní Cloud.

Jarní spouštěcí verze | Jarní cloudová verze | Verze cloudu pro Azure jaře
---|---|---
2.1 | Střední verze | 2.1
2.2 | Hoxton. RELEASE | 2.2

Do souboru pom. xml přidejte jednu z následujících závislostí. Vyberte závislost, jejíž verze cloudu pro Azure jaře se shoduje s vaší vlastní.

### <a name="dependency-for-azure-spring-cloud-version-21"></a>Závislost pro Azure jaře Cloud verze 2,1

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.1</version>
</dependency>
```

### <a name="dependency-for-azure-spring-cloud-version-22"></a>Závislost pro Azure jaře Cloud verze 2,2

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.2.0</version>
</dependency>
```

## <a name="other-required-dependencies"></a>Další požadované závislosti

Aby bylo možné povolit integrované funkce Azure jarního cloudu, musí vaše aplikace zahrnovat následující závislosti. Díky tomu je zajištěno, že aplikace správně nakonfiguruje jednotlivé komponenty.  

### <a name="service-registry-dependency"></a>Závislost registru služby

Pokud chcete použít spravovanou službu Azure Service Registry, zahrňte `spring-cloud-starter-netflix-eureka-client` závislost v souboru pom. XML, jak je znázorněno zde:

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

Koncový bod serveru registru služby se do vaší aplikace automaticky vloží jako proměnné prostředí. Aplikace se mohou registrovat pomocí serveru registru služby a zjišťovat další závislé mikroslužby.

### <a name="distributed-configuration-dependency"></a>Závislost distribuované konfigurace

Chcete-li povolit distribuovanou konfiguraci, zahrňte následující závislost `spring-cloud-config-client` do části závislosti v souboru pom. XML:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> Nezadávejte `spring.cloud.config.enabled=false` v konfiguraci Bootstrap. V opačném případě přestane vaše aplikace pracovat s konfiguračním serverem.

### <a name="metrics-dependency"></a>Závislost metrik

Zahrňte `spring-boot-starter-actuator` závislost v části závislosti v souboru pom. XML, jak je znázorněno zde:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

 Metriky jsou pravidelně načítány z koncových bodů JMX. Metriky můžete vizualizovat pomocí Azure Portal.

### <a name="distributed-tracing-dependency"></a>Závislost distribuované vektorizace

V části závislosti v souboru pom. XML zahrňte následující `spring-cloud-starter-sleuth` a `spring-cloud-starter-zipkin` závislosti:

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

 Musíte taky povolit instanci Azure Application Insights, abyste mohli pracovat s instancí cloudové služby Azure na jaře. Přečtěte si [kurz o distribuovaném trasování](spring-cloud-tutorial-distributed-tracing.md) a Naučte se používat Application Insights s využitím Azure jaře cloudu.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak nakonfigurovat aplikaci pro pružinu v jazyce Java pro nasazení do jarního cloudu Azure. Pokud se chcete dozvědět, jak nastavit instanci konfiguračního serveru, přejděte k dalšímu kurzu.

> [!div class="nextstepaction"]
> [Přečtěte si, jak nastavit instanci konfiguračního serveru.](spring-cloud-tutorial-config-server.md)

Další ukázky jsou k dispozici na GitHubu: [ukázky Azure pro jarní Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples).
