---
title: Postup vytvoření vazby mezipaměti Azure pro Redis do vaší aplikace pro jarní Cloud v Azure | Microsoft Docs
description: Naučte se navazovat Azure cache pro Redis do vaší aplikace pro jarní cloudy v Azure.
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/06/2019
ms.author: v-vasuke
ms.openlocfilehash: d8fa31207baecc80674fb11b492927800676b8a2
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038941"
---
# <a name="tutorial-bind-azure-services-to-your-azure-spring-cloud-application-azure-cache-for-redis"></a>Kurz: vázání služeb Azure na svoji cloudovou aplikaci Azure na jaře: Azure cache pro Redis

Pomocí Azure jaře cloudu můžete automaticky navazovat vybrané služby Azure na vaše aplikace místo ruční konfigurace aplikace pro spouštění pružiny. Tento článek ukazuje, jak vytvořit propojení aplikace s mezipamětí Azure cache pro Redis.

## <a name="prerequisites"></a>Požadavky

* Nasazená instance cloudu Azure pro jaře
* Služba Azure cache pro instanci služby Redis
* Rozšíření Azure jaře Cloud pro rozhraní příkazového řádku Azure

V případě potřeby nainstalujte rozšíření Azure jaře Cloud pro rozhraní příkazového řádku Azure pomocí následujícího příkazu:

```azurecli
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

>[!TIP]
> Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít ke spuštění kroků v tomto článku.  Má předinstalované běžné nástroje Azure, včetně nejnovějších verzí Git, JDK, Maven a Azure CLI. Pokud jste přihlášeni ke svému předplatnému Azure, spusťte [Azure Cloud Shell](https://shell.azure.com) z Shell.Azure.com.  Další informace o Azure Cloud Shell najdete v [naší dokumentaci](../cloud-shell/overview.md) .

## <a name="bind-azure-cache-for-redis"></a>Vytvoření vazby mezipaměti Azure pro Redis

1. Přidejte následující závislost do @no__t projektu-0

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