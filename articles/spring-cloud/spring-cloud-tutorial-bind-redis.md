---
title: Kurz – svázání azure mezipaměti pro Redis s vaší aplikací Azure Spring Cloud
description: Tento kurz ukazuje, jak svázat Azure Cache pro Redis s vaší aplikací Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/31/2019
ms.author: brendm
ms.openlocfilehash: 94f7b5a2363b7c53e0f70500e5a0a8cb6f64e611
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "76277504"
---
# <a name="bind-azure-cache-for-redis-to-your-azure-spring-cloud-application"></a>Svázání azure mezipaměti pro Redis s aplikací Azure Spring Cloud 

Místo ruční konfigurace aplikací spring boot, můžete automaticky vázat vybrané služby Azure na vaše aplikace pomocí Azure Spring Cloud. Tento článek ukazuje, jak svázat aplikaci s Azure Cache pro Redis.

## <a name="prerequisites"></a>Požadavky

* Nasazená instance Azure Spring Cloud
* Instance služby Azure Cache for Redis
* Rozšíření Azure Spring Cloud pro azure cli

Pokud nemáte nasazenou instanci Azure Spring Cloud, postupujte podle pokynů v [úvodním panelu při nasazování aplikace Azure Spring Cloud](spring-cloud-quickstart-launch-app-portal.md).

## <a name="bind-azure-cache-for-redis"></a>Vazba mezipaměti Azure pro Redis

1. Do souboru pom.xml projektu přidejte následující závislost:

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    </dependency>
    ```
1. Odebrání `spring.redis.*` všech vlastností ze souboru `application.properties`

1. Aktualizujte aktuální `az spring-cloud app update` nasazení pomocí nebo `az spring-cloud app deployment create`vytvořte nové nasazení pomocí aplikace .

1. Přejděte na stránku služby Azure Spring Cloud na webu Azure Portal. Přejděte na **řídicí panel aplikace** a vyberte aplikaci, která se má vázat na Azure Cache for Redis. Tato aplikace je stejná jako ta, kterou jste aktualizovali nebo nasadili v předchozím kroku.

1. Vyberte **Vazby služby** a vyberte **Vytvořit vazbu služby**. Vyplňte formulář a ujistěte se, že vyberete hodnotu **typu vazby** **Azure Cache pro Redis**, váš server Azure Cache for Redis a možnost **Primární** klíč.

1. Restartujte aplikaci. Vazba by nyní měla fungovat.

1. Chcete-li zajistit, aby byla vazba služby správná, vyberte název vazby a ověřte její podrobnosti. Pole `property` by mělo vypadat takto:
    ```
    spring.redis.host=some-redis.redis.cache.windows.net
    spring.redis.port=6380
    spring.redis.password=abc******
    spring.redis.ssl=true
    ```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak svázat vaši aplikaci Azure Spring Cloud s Azure Cache for Redis. Další informace o službách vazby do vaší aplikace, pokračujte v kurzu o vazbě aplikace na instanci Azure Database for MySQL.

> [!div class="nextstepaction"]
> [Zjistěte, jak vytvořit vazbu na instanci Azure Database for MySQL](spring-cloud-tutorial-bind-mysql.md)
