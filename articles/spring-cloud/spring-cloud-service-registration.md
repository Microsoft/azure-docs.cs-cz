---
title: Automatizace registru a zjišťování služeb
description: Naučte se automatizovat zjišťování a registraci služeb pomocí registru služeb Spring Cloud Service
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: brendm
ms.openlocfilehash: 6c217096f0ba4200f49bd1fd8056768a6f6f6dbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278863"
---
# <a name="discover-and-register-your-spring-cloud-services"></a>Objevte a zaregistrujte své služby Spring Cloud

Zjišťování služeb je klíčovým požadavkem pro architekturu založenou na mikroslužbách.  Konfigurace každého klienta ručně vyžaduje čas a zavádí možnost lidské chyby.  Azure Spring Cloud Service Registry tento problém řeší.  Po konfiguraci bude server registru služeb řídit registraci a zjišťování služby pro mikroslužby vaší aplikace. Server registru služeb udržuje registr nasazených mikroslužeb, umožňuje vyrovnávání zatížení na straně klienta a odděluje poskytovatele služeb od klientů bez spoléhání se na služby DNS.

## <a name="register-your-application-using-spring-cloud-service-registry"></a>Registrace aplikace pomocí registru služby Spring Cloud Service

Před tím, než vaše aplikace může spravovat registraci a zjišťování služby pomocí spring cloudového registru služby, musí být do souboru *pom.xml* aplikace zahrnuto několik závislostí.
Zahrnout závislosti pro *spring-cloud-starter-netflix-eureka-client* a *spring-cloud-starter-azure-spring-cloud-client-client* do vašeho *pom.xml*

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

## <a name="update-the-top-level-class"></a>Aktualizace nejvyšší třídy

Nakonec přidáme poznámku do nejvyšší třídy vaší aplikace.

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

Koncový bod serveru registru služby Spring Cloud Service bude vložen jako proměnná prostředí ve vaší aplikaci.  Mikroslužby se teď budou moci zaregistrovat na serveru registru služeb a zjistit další závislé mikroslužby.

Všimněte si, že může trvat několik minut pro změny šířit ze serveru do všech mikroslužeb.
