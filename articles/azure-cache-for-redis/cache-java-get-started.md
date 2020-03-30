---
title: 'Úvodní příručka: Použití Azure Cache pro Redis s Javou'
description: V tomto rychlém startu vytvoříte novou aplikaci Java, která používá Azure Cache pro Redis
author: yegu-ms
ms.service: cache
ms.devlang: java
ms.topic: quickstart
ms.date: 05/23/2018
ms.author: yegu
ms.custom: mvc, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 6163392c80d40b6ea65e681aa175c21c244099b9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "74122709"
---
# <a name="quickstart-use-azure-cache-for-redis-with-java"></a>Úvodní příručka: Použití Azure Cache pro Redis s Javou

V tomto rychlém startu můžete začlenit Azure Cache pro Redis do aplikace Java pomocí [klienta Jedis](https://github.com/xetorthio/jedis) Redis, abyste měli přístup k zabezpečené vyhrazené mezipaměti, která je přístupná z libovolné aplikace v rámci Azure.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/)
- [Apache Maven](https://maven.apache.org/download.cgi)

## <a name="create-an-azure-cache-for-redis"></a>Vytvoření instance Azure Cache for Redis

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]

Přidejte proměnné prostředí pro **NÁZEV HOSTITELE** a **primární** přístupový klíč. Tyto proměnné z kódu použijete místo citlivých informací, které byste zahrnuli přímo do kódu.

```CMD 
set REDISCACHEHOSTNAME=contosoCache.redis.cache.windows.net
set REDISCACHEKEY=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
```

## <a name="create-a-new-java-app"></a>Vytvoření nové aplikace Java

Pomocí Mavenu vygenerujte novou aplikaci rychlého startu:

```CMD
mvn archetype:generate -DarchetypeGroupId=org.apache.maven.archetypes -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.3 -DgroupId=example.demo -DartifactId=redistest -Dversion=1.0
```

Přejděte do nového adresáře projektu *redistest*.

Otevřete soubor *pom.xml* a přidejte závislost pro [Jedis](https://github.com/xetorthio/jedis):

```xml
    <dependency>
        <groupId>redis.clients</groupId>
        <artifactId>jedis</artifactId>
        <version>2.9.0</version>
        <type>jar</type>
        <scope>compile</scope>
    </dependency>
```

Uložte soubor *pom.xml*.

Otevřete *App.java* a kód nahraďte následujícím kódem:

```java
package example.demo;

import redis.clients.jedis.Jedis;
import redis.clients.jedis.JedisShardInfo;

/**
 * Redis test
 *
 */
public class App 
{
    public static void main( String[] args )
    {

        boolean useSsl = true;
        String cacheHostname = System.getenv("REDISCACHEHOSTNAME");
        String cachekey = System.getenv("REDISCACHEKEY");

        // Connect to the Azure Cache for Redis over the SSL port using the key.
        JedisShardInfo shardInfo = new JedisShardInfo(cacheHostname, 6380, useSsl);
        shardInfo.setPassword(cachekey); /* Use your access key. */
        Jedis jedis = new Jedis(shardInfo);      

        // Perform cache operations using the cache connection object...

        // Simple PING command        
        System.out.println( "\nCache Command  : Ping" );
        System.out.println( "Cache Response : " + jedis.ping());

        // Simple get and put of integral data types into the cache
        System.out.println( "\nCache Command  : GET Message" );
        System.out.println( "Cache Response : " + jedis.get("Message"));

        System.out.println( "\nCache Command  : SET Message" );
        System.out.println( "Cache Response : " + jedis.set("Message", "Hello! The cache is working from Java!"));

        // Demonstrate "SET Message" executed as expected...
        System.out.println( "\nCache Command  : GET Message" );
        System.out.println( "Cache Response : " + jedis.get("Message"));

        // Get the client list, useful to see if connection list is growing...
        System.out.println( "\nCache Command  : CLIENT LIST" );
        System.out.println( "Cache Response : " + jedis.clientList());

        jedis.close();
    }
}
```

Tento kód ukazuje, jak se připojit k instanci Azure Cache for Redis pomocí názvu hostitele mezipaměti a proměnných prostředí klíčů. Kód také ukládá a načítá hodnotu řetězce v mezipaměti. Spouští se také příkazy `PING` a `CLIENT LIST`. 

Uložte *App.java*.

## <a name="build-and-run-the-app"></a>Sestavení a spuštění aplikace

Spusťte následující příkaz Maven k sestavení a spuštění aplikace:

```CMD
mvn compile
mvn exec:java -D exec.mainClass=example.demo.App
```

V níže uvedeném příkladu můžete vidět, že klíč `Message` měl předtím hodnotu z mezipaměti, která byla nastavena pomocí konzoly Redis na webu Azure Portal. Aplikace tuto hodnotu z mezipaměti aktualizovala. Aplikace rovněž spustila příkazy `PING` a `CLIENT LIST`.

![Dokončena aplikace Azure Cache for Redis](./media/cache-java-get-started/azure-cache-redis-complete.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud budete pokračovat k dalšímu kurzu, můžete prostředky vytvořené v tomto rychlém startu zachovat a znovu je použít.

V opačném případě, pokud jste už s ukázkovou aplikací v tomto rychlém startu skončili, můžete prostředky Azure vytvořené v tomto rychlém startu odstranit, abyste se vyhnuli poplatkům. 

> [!IMPORTANT]
> Odstranění skupiny prostředků je nevratné a skupina prostředků včetně všech v ní obsažených prostředků bude trvale odstraněna. Ujistěte se, že nechtěně neodstraníte nesprávnou skupinu prostředků nebo prostředky. Pokud jste vytvořili prostředky pro hostování této ukázky ve stávající skupině prostředků obsahující prostředky, které chcete zachovat, můžete místo odstranění skupiny prostředků odstranit jednotlivé prostředky z jejich odpovídajících oken.
>

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) a potom vyberte **Skupiny prostředků**.

1. Do textového pole **Filtr podle názvu** zadejte název skupiny prostředků. V pokynech v tomto článku se používala skupina prostředků *TestResources*. Ve skupině prostředků v seznamu výsledků vyberte **...** a **pak Odstranit skupinu prostředků**.

   ![Odstraněná skupina prostředků Azure](./media/cache-java-get-started/azure-cache-redis-delete-resource-group.png)

1. Zobrazí se výzva k potvrzení odstranění skupiny prostředků. Zadejte název skupiny prostředků, kterou chcete potvrdit, a vyberte **Odstranit**.

Po chvíli bude skupina prostředků včetně všech obsažených prostředků odstraněná.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili používat Azure Cache pro Redis z aplikace Java. Pokračujte k dalšímu rychlému startu a pomocí ASP.NET webové aplikace můžete používat Azure Cache for Redis.

> [!div class="nextstepaction"]
> [Vytvořte ASP.NET webovou aplikaci, která používá Azure Cache pro Redis.](./cache-web-app-howto.md)
