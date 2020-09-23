---
title: Automatizace registru služby a zjišťování
description: Naučte se automatizovat zjišťování a registraci služeb pomocí registru jarní cloudové služby.
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 1e60799878cc30b729344c03df36a4c5e4f4a199
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90904208"
---
# <a name="discover-and-register-your-spring-cloud-services"></a>Objevte a zaregistrujte své jarní cloudové služby

Zjišťování služeb je klíčovým požadavkem na architekturu založenou na mikroslužbách.  Ruční konfigurace každého klienta trvá určitou dobu a zavádí možnost lidské chyby.  Registr služby Azure jaře Cloud Service tento problém řeší.  Po nakonfigurování bude server registru služby řídit registraci služby a zjišťování pro mikroslužby vaší aplikace. Server registru služby udržuje registr nasazených mikroslužeb, umožňuje vyrovnávání zatížení na straně klienta a odděluje poskytovatele služeb od klientů, aniž by se museli spoléhat na DNS.

::: zone pivot="programming-language-csharp"
Informace o tom, jak nastavit registraci služby pro aplikaci Steeltoe, najdete v tématu [Příprava pružinové aplikace Java pro nasazení v jarním cloudu Azure](spring-cloud-tutorial-prepare-app-deployment.md).
::: zone-end
::: zone pivot="programming-language-java"

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
::: zone-end
