---
title: Automatizace registru služby a zjišťování
description: Naučte se automatizovat zjišťování a registraci služeb pomocí registru jarní cloudové služby.
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: jeconnoc
ms.openlocfilehash: 72327e116e498ce0f6881a5c585a08e56c8bf8c2
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038740"
---
# <a name="discover-and-register-your-spring-cloud-services"></a>Objevte a zaregistrujte své jarní cloudové služby

Zjišťování služeb je klíčovým požadavkem na architekturu založenou na mikroslužbách.  Ruční konfigurace každého klienta trvá určitou dobu a zavádí možnost lidské chyby.  Registr služby Azure jaře Cloud Service tento problém řeší.  Po nakonfigurování bude server registru služby řídit registraci služby a zjišťování pro mikroslužby vaší aplikace. Server registru služby udržuje registr nasazených mikroslužeb, umožňuje vyrovnávání zatížení na straně klienta a odděluje poskytovatele služeb od klientů, aniž by se museli spoléhat na DNS.

## <a name="register-your-application-using-spring-cloud-service-registry"></a>Registrace aplikace pomocí registru jarní cloudové služby

Předtím, než může vaše aplikace spravovat registraci a zjišťování služby pomocí registru jarní cloudové služby, musí být do souboru *pom. XML* aplikace zahrnuty několik závislostí.

Začněte přidáním úložiště snímků do části *úložiště* souboru *pom. XML.*

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
```

## <a name="include-dependencies"></a>Zahrnout závislosti

V dalším kroku uvádíme závislosti pro *jaře-Cloud-Started-Netflix-Eureka-Client* a *jaře-Cloud-Start-Azure-jaře-Cloud-Client* to a *pom. XML.*

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.0-SNAPSHOT</version>
    </dependency>
```

## <a name="update-the-top-level-class"></a>Aktualizovat třídu nejvyšší úrovně

Nakonec přidáme anotaci do třídy nejvyšší úrovně vaší aplikace.

 ```java
    package foo.bar;

    import org.springframework.boot.SpringApplication;
    import org.springframework.cloud.client.SpringCloudApplication;

    @SpringCloudApplication
    public class DemoApplication {
        public static void main(String... args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
 ```

Koncový bod serveru registru služby jarní Cloud Service bude ve vaší aplikaci vložen jako proměnná prostředí.  Mikroslužby teď budou moct zaregistrovat se serverem registru služby a zjišťovat další závislé mikroslužby.

Všimněte si, že může trvat několik minut, než se změny rozšíří ze serveru do všech mikroslužeb.
