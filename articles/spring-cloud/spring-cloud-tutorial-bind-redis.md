---
title: Kurz – vytvoření vazby mezipaměti Azure pro Redis do vaší aplikace založené na jarním cloudu Azure
description: V tomto kurzu se dozvíte, jak vytvořit vazby Azure cache pro Redis do vaší aplikace pro jarní Cloud v Azure.
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/31/2019
ms.author: jeconnoc
ms.openlocfilehash: 1653db3619fd569238872ca1fcfd6d0c439e84c9
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74708787"
---
# <a name="tutorial-bind-azure-services-to-your-azure-spring-cloud-application-azure-cache-for-redis"></a>Kurz: vázání služeb Azure na svoji cloudovou aplikaci Azure na jaře: Azure cache pro Redis

Pomocí Azure jaře cloudu můžete automaticky navazovat vybrané služby Azure na vaše aplikace místo ruční konfigurace aplikace pro spouštění pružiny. Tento článek ukazuje, jak vytvořit propojení aplikace s mezipamětí Azure cache pro Redis.

## <a name="prerequisites"></a>Předpoklady

* Nasazená instance cloudu Azure pro jaře
* Služba Azure cache pro instanci služby Redis
* Rozšíření Azure jaře Cloud pro rozhraní příkazového řádku Azure

Pokud nemáte nasazenou instanci Azure jaře cloudu, postupujte podle kroků v tomto [rychlém](spring-cloud-quickstart-launch-app-portal.md) startu a nasaďte svou první jarní cloudovou aplikaci.

## <a name="bind-azure-cache-for-redis"></a>Vytvoření vazby mezipaměti Azure pro Redis

1. Do `pom.xml` projektu přidejte následující závislost.

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    </dependency>
    ```
1. Odebrání vlastností `spring.redis.*`, pokud existují, v souboru `application.properties`

1. Aktualizujte aktuální nasazení pomocí `az spring-cloud app update` nebo vytvořte nové nasazení pomocí `az spring-cloud app deployment create`.

1. V Azure Portal přejdete na stránku služby jarní cloud Azure. Najděte **řídicí panel aplikace** a vyberte aplikaci, která se váže k mezipaměti Azure pro Redis.  Jedná se o stejnou aplikaci, kterou jste aktualizovali nebo nasadili v předchozím kroku. Potom zvolte `Service binding` a vyberte tlačítko `Create service binding`. Vyplňte formulář, nezapomeňte vybrat **typ vazby** `Azure Cache for Redis`, server Redis a možnost primárního klíče. 

1. Restartujte aplikaci a tato vazba by teď měla fungovat.

1. Chcete-li zajistit, aby vazba služby byla správná, vyberte název vazby a ověřte jeho podrobnosti. Pole `property` by mělo vypadat takto:
    ```
    spring.redis.host=some-redis.redis.cache.windows.net
    spring.redis.port=6380
    spring.redis.password=abc******
    spring.redis.ssl=true
    ```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak vytvořit datovou aplikaci v Azure pro Azure Redis Cache s využitím cloudu.  Další informace o vázání služeb pro vaši aplikaci získáte v kurzu pro vazbu aplikace na databázi MySQL.

> [!div class="nextstepaction"]
> [Naučte se navazovat službu Azure MySQL na jarní cloudovou službu Azure](spring-cloud-tutorial-bind-mysql.md).