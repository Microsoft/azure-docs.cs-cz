---
title: Vytvoření vazby mezipaměti Azure pro Redis do vaší aplikace pro jarní Cloud v Azure
description: Naučte se navazovat Azure cache pro Redis do vaší aplikace pro jarní cloudy v Azure.
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/31/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 414f7dc64027b286d8a963452d2b86126b3c5818
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104877926"
---
# <a name="bind-azure-cache-for-redis-to-your-azure-spring-cloud-application"></a>Vytvoření vazby mezipaměti Azure pro Redis do vaší aplikace pro jarní Cloud v Azure 

**Tento článek se týká:** ✔️ Java

Místo ruční konfigurace aplikací pro spouštění pružiny můžete automaticky navazovat výběr služeb Azure na své aplikace s využitím jarního cloudu Azure. Tento článek ukazuje, jak vytvořit propojení aplikace s mezipamětí Azure cache pro Redis.

## <a name="prerequisites"></a>Předpoklady

* Nasazená instance cloudu Azure pro jaře
* Služba Azure cache pro instanci služby Redis
* Rozšíření Azure jaře Cloud pro rozhraní příkazového řádku Azure

Pokud nemáte nasazenou instanci Azure jaře cloudu, postupujte podle kroků v [rychlém startu o nasazení aplikace pro jarní Cloud v Azure](spring-cloud-quickstart.md).

## <a name="bind-azure-cache-for-redis"></a>Vytvoření vazby mezipaměti Azure pro Redis

1. Do souboru pom.xml projektu přidejte následující závislost:

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    </dependency>
    ```
1. Odebrat všechny `spring.redis.*` vlastnosti ze `application.properties` souboru

1. Aktualizujte aktuální nasazení pomocí `az spring-cloud app update` nebo vytvořte nové nasazení pomocí `az spring-cloud app deployment create` .

1. V Azure Portal přejdete na stránku služby jarní cloud Azure. Přejít na **řídicí panel aplikace** a vybrat aplikaci, která se váže k Azure cache pro Redis. Tato aplikace je stejná jako ta, kterou jste aktualizovali nebo nasadili v předchozím kroku.

1. Vyberte možnost **vazba služby** a vyberte **vytvořit vazbu služby**. Vyplňte formulář, ujistěte se, že jste vybrali hodnotu **typu vazby** **Azure cache pro Redis**, mezipaměť Azure pro server Redis a možnost **primárního** klíče.

1. Restartujte aplikaci. Vazba by teď měla fungovat.

1. Chcete-li zajistit, aby vazba služby byla správná, vyberte název vazby a ověřte jeho podrobnosti. `property`Pole by mělo vypadat takto:
    ```
    spring.redis.host=some-redis.redis.cache.windows.net
    spring.redis.port=6380
    spring.redis.password=abc******
    spring.redis.ssl=true
    ```

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak vytvořit datovou aplikaci v Azure jaře pro službu Azure cache pro Redis. Další informace o vázání služeb pro vaši aplikaci najdete v tématu [vazba na instanci Azure Database for MySQL](spring-cloud-howto-bind-mysql.md).
