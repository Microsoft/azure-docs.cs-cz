---
title: 'Rychlý Start: použití mezipaměti Azure pro Redis v jazyce Java'
description: V tomto rychlém startu vytvoříte novou aplikaci Java, která bude používat Azure cache pro Redis.
author: yegu-ms
ms.service: cache
ms.devlang: java
ms.topic: quickstart
ms.date: 05/22/2020
ms.author: yegu
ms.custom: mvc, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: edb80ab3107cbd0a5ddd802d56a60ce77affb2f2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012914"
---
# <a name="quickstart-use-azure-cache-for-redis-in-java"></a>Rychlý Start: použití mezipaměti Azure pro Redis v jazyce Java

V tomto rychlém startu zabezpečíte mezipaměť Azure pro Redis do aplikace Java pomocí klienta [Jedis](https://github.com/xetorthio/jedis) Redis, abyste měli přístup k zabezpečené vyhrazené mezipaměti, která je přístupná z libovolné aplikace v Azure.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
- [Apache Maven](https://maven.apache.org/download.cgi)

## <a name="create-an-azure-cache-for-redis"></a>Vytvoření instance Azure Cache for Redis

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]

## <a name="setting-up-the-working-environment"></a>Nastavení pracovního prostředí 

V závislosti na vašem operačním systému přidejte proměnné prostředí pro **název hostitele** a **Primární přístupový klíč**. Otevřete příkazový řádek nebo okno terminálu a nastavte následující hodnoty:

```CMD 
set REDISCACHEHOSTNAME=<YOUR_HOST_NAME>.redis.cache.windows.net
set REDISCACHEKEY=<YOUR_PRIMARY_ACCESS_KEY>
```

```bash
export REDISCACHEHOSTNAME=<YOUR_HOST_NAME>.redis.cache.windows.net
export REDISCACHEKEY=<YOUR_PRIMARY_ACCESS_KEY>
```

Zástupné symboly nahraďte následujícími hodnotami:

- `<YOUR_HOST_NAME>`: Název hostitele DNS získaný z oddílu Properties ( *vlastnosti* ) prostředku Azure cache for Redis v Azure Portal.
- `<YOUR_PRIMARY_ACCESS_KEY>`: Primární přístupový klíč získaný z oddílu *přístupové klíče* v mezipaměti Azure pro prostředek Redis v Azure Portal.

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
        <version>3.2.0</version>
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

        // Connect to the Azure Cache for Redis over the TLS/SSL port using the key.
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

Tento kód ukazuje, jak se připojit k instanci služby Azure cache for Redis pomocí názvu hostitele mezipaměti a proměnných prostředí klíče. Kód také ukládá a načítá hodnotu řetězce v mezipaměti. Spouští se také příkazy `PING` a `CLIENT LIST`. 

Uložte *App.java*.

## <a name="build-and-run-the-app"></a>Sestavte a spusťte aplikaci.

Spusťte následující příkaz Maven k sestavení a spuštění aplikace:

```CMD
mvn compile
mvn exec:java -D exec.mainClass=example.demo.App
```

V níže uvedeném příkladu můžete vidět, že klíč `Message` měl předtím hodnotu z mezipaměti, která byla nastavena pomocí konzoly Redis na webu Azure Portal. Aplikace tuto hodnotu z mezipaměti aktualizovala. Aplikace rovněž spustila příkazy `PING` a `CLIENT LIST`.

![Aplikace Azure cache pro Redis se dokončila.](./media/cache-java-get-started/azure-cache-redis-complete.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud budete pokračovat k dalšímu kurzu, můžete prostředky vytvořené v tomto rychlém startu zachovat a znovu je použít.

V opačném případě, pokud jste už s ukázkovou aplikací v tomto rychlém startu skončili, můžete prostředky Azure vytvořené v tomto rychlém startu odstranit, abyste se vyhnuli poplatkům. 

> [!IMPORTANT]
> Odstranění skupiny prostředků je nevratné a skupina prostředků včetně všech v ní obsažených prostředků bude trvale odstraněna. Ujistěte se, že nechtěně neodstraníte nesprávnou skupinu prostředků nebo prostředky. Pokud jste vytvořili prostředky pro hostování této ukázky ve stávající skupině prostředků obsahující prostředky, které chcete zachovat, můžete místo odstranění skupiny prostředků odstranit jednotlivé prostředky z jejich odpovídajících oken.
>

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) a potom vyberte **Skupiny prostředků**.

1. Do textového pole **filtrovat podle názvu** zadejte název vaší skupiny prostředků. V pokynech v tomto článku se používala skupina prostředků *TestResources*. Ve vaší skupině prostředků v seznamu výsledků vyberte **...** a pak **odstraňte skupinu prostředků**.

   ![Skupina prostředků Azure se odstranila.](./media/cache-java-get-started/azure-cache-redis-delete-resource-group.png)

1. Zobrazí se výzva k potvrzení odstranění skupiny prostředků. Zadejte název vaší skupiny prostředků a potvrďte je a vyberte **Odstranit**.

Po chvíli bude skupina prostředků včetně všech obsažených prostředků odstraněná.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak používat Azure cache pro Redis z aplikace Java. Přejděte k dalšímu rychlému startu a použijte Azure cache pro Redis s webovou aplikací ASP.NET.

> [!div class="nextstepaction"]
> [Vytvořte webovou aplikaci ASP.NET, která používá Azure cache pro Redis.](./cache-web-app-howto.md)
