---
title: Automatizace registru služby a zjišťování
description: Naučte se automatizovat zjišťování a registraci služeb pomocí registru jarní cloudové služby.
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: brendm
ms.openlocfilehash: 6c217096f0ba4200f49bd1fd8056768a6f6f6dbd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "76278863"
---
# <a name="discover-and-register-your-spring-cloud-services"></a>Objevte a zaregistrujte své jarní cloudové služby

Zjišťování služeb je klíčovým požadavkem na architekturu založenou na mikroslužbách.  Ruční konfigurace každého klienta trvá určitou dobu a zavádí možnost lidské chyby.  Registr služby Azure jaře Cloud Service tento problém řeší.  Po nakonfigurování bude server registru služby řídit registraci služby a zjišťování pro mikroslužby vaší aplikace. Server registru služby udržuje registr nasazených mikroslužeb, umožňuje vyrovnávání zatížení na straně klienta a odděluje poskytovatele služeb od klientů, aniž by se museli spoléhat na DNS.

## <a name="register-your-application-using-spring-cloud-service-registry"></a>Registrace aplikace pomocí registru jarní cloudové služby

Předtím, než může vaše aplikace spravovat registraci a zjišťování služby pomocí registru jarní cloudové služby, musí být do souboru *pom.xml* aplikace zahrnuto několik závislostí.
Zahrňte závislosti pro *jaře-Cloud-Started-Netflix-Eureka-Client* a *jaře-Cloud-Start-Azure-jaře-Cloud-Client* to *pom.xml*

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.0</version>
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
