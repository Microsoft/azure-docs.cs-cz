---
title: 'Úvodní příručka: Použití Azure Cache pro Redis s node.js'
description: V tomto rychlém startu se dozvíte, jak používat Azure Cache pro Redis s Node.js a node_redis.
author: yegu-ms
ms.service: cache
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 05/21/2018
ms.author: yegu
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: 07e2d6f174e5af4af9bdcac73dc74f5cf061ed41
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78300481"
---
# <a name="quickstart-use-azure-cache-for-redis-with-nodejs"></a>Úvodní příručka: Použití Azure Cache pro Redis s node.js

V tomto rychlém startu začlenit Azure Cache pro Redis do aplikace Node.js mít přístup k zabezpečené, vyhrazené mezipaměti, která je přístupná z libovolné aplikace v rámci Azure.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/)
- [node_redis](https://github.com/mranney/node_redis), který můžete nainstalovat `npm install redis`pomocí příkazu . 

Příklady použití dalších klientů Node.js najdete v individuální dokumentaci pro klienty Node.js uvedené v [klientech Node.js Redis](https://redis.io/clients#nodejs).

## <a name="create-a-cache"></a>Vytvoření mezipaměti
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]


Přidejte proměnné prostředí pro **NÁZEV HOSTITELE** a **primární** přístupový klíč. Tyto proměnné z kódu použijete místo citlivých informací, které byste zahrnuli přímo do kódu.

```
set REDISCACHEHOSTNAME=contosoCache.redis.cache.windows.net
set REDISCACHEKEY=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
```

## <a name="connect-to-the-cache"></a>Připojení k mezipaměti

Nejnovější sestavení [node_redis](https://github.com/mranney/node_redis) poskytují podporu pro připojení k Azure Cache pro Redis pomocí SSL. Následující příklad ukazuje, jak se připojit k Azure Cache pro Redis pomocí koncového bodu SSL 6380. 

```js
var redis = require("redis");

// Add your cache name and access key.
var client = redis.createClient(6380, process.env.REDISCACHEHOSTNAME,
    {auth_pass: process.env.REDISCACHEKEY, tls: {servername: process.env.REDISCACHEHOSTNAME}});
```

Nevytvářejte nová připojení pro každou operaci v kódu. Raději co nejvíce připojení použijte opakovaně. 

## <a name="create-a-new-nodejs-app"></a>Vytvoření nové aplikace Node.js

Vytvořte nový soubor skriptu s názvem *redistest.js*. Pomocí příkazu `npm install redis bluebird` nainstalujte požadované balíčky.

Přidejte následující příklad JavaScriptu do souboru. Tento kód ukazuje, jak se připojit k instanci Azure Cache for Redis pomocí názvu hostitele mezipaměti a proměnných prostředí klíčů. Kód také ukládá a načítá hodnotu řetězce v mezipaměti. Spouští se také příkazy `PING` a `CLIENT LIST`. Pro další příklady použití Redis pomocí klienta [node_redis](https://github.com/mranney/node_redis) se podívejte na [https://redis.js.org/](https://redis.js.org/).

```js
var redis = require("redis");
var bluebird = require("bluebird");

// Convert Redis client API to use promises, to make it usable with async/await syntax
bluebird.promisifyAll(redis.RedisClient.prototype);
bluebird.promisifyAll(redis.Multi.prototype);

async function testCache() {

    // Connect to the Azure Cache for Redis over the SSL port using the key.
    var cacheConnection = redis.createClient(6380, process.env.REDISCACHEHOSTNAME, 
        {auth_pass: process.env.REDISCACHEKEY, tls: {servername: process.env.REDISCACHEHOSTNAME}});
        
    // Perform cache operations using the cache connection object...

    // Simple PING command
    console.log("\nCache command: PING");
    console.log("Cache response : " + await cacheConnection.pingAsync());

    // Simple get and put of integral data types into the cache
    console.log("\nCache command: GET Message");
    console.log("Cache response : " + await cacheConnection.getAsync("Message"));    

    console.log("\nCache command: SET Message");
    console.log("Cache response : " + await cacheConnection.setAsync("Message",
        "Hello! The cache is working from Node.js!"));    

    // Demonstrate "SET Message" executed as expected...
    console.log("\nCache command: GET Message");
    console.log("Cache response : " + await cacheConnection.getAsync("Message"));    

    // Get the client list, useful to see if connection list is growing...
    console.log("\nCache command: CLIENT LIST");
    console.log("Cache response : " + await cacheConnection.clientAsync("LIST"));    
}

testCache();
```

Spusťte skript s Node.js.

```
node redistest.js
```

V níže uvedeném příkladu můžete vidět, že klíč `Message` měl předtím hodnotu z mezipaměti, která byla nastavena pomocí konzoly Redis na webu Azure Portal. Aplikace tuto hodnotu z mezipaměti aktualizovala. Aplikace rovněž spustila příkazy `PING` a `CLIENT LIST`.

![Aplikace Redis Cache byla dokončena.](./media/cache-nodejs-get-started/redis-cache-app-complete.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud budete pokračovat k dalšímu kurzu, můžete prostředky vytvořené v tomto rychlém startu zachovat a znovu je použít.

V opačném případě, pokud jste už s ukázkovou aplikací v tomto rychlém startu skončili, můžete prostředky Azure vytvořené v tomto rychlém startu odstranit, abyste se vyhnuli poplatkům. 

> [!IMPORTANT]
> Odstranění skupiny prostředků je nevratné a skupina prostředků včetně všech v ní obsažených prostředků bude trvale odstraněna. Ujistěte se, že nechtěně neodstraníte nesprávnou skupinu prostředků nebo prostředky. Pokud jste vytvořili prostředky pro hostování této ukázky ve stávající skupině prostředků obsahující prostředky, které chcete zachovat, můžete místo odstranění skupiny prostředků odstranit jednotlivé prostředky z jejich odpovídajících oken.
>

Přihlaste se k webu [Azure Portal](https://portal.azure.com) a potom vyberte **Skupiny prostředků**.

Do textového pole **Filtr podle názvu** zadejte název skupiny prostředků. V pokynech v tomto článku se používala skupina prostředků *TestResources*. Ve skupině prostředků v seznamu výsledků vyberte **...** a **pak Odstranit skupinu prostředků**.

![Odstranit skupinu prostředků Azure](./media/cache-nodejs-get-started/redis-cache-delete-resource-group.png)

Zobrazí se výzva k potvrzení odstranění skupiny prostředků. Zadejte název skupiny prostředků, kterou chcete potvrdit, a vyberte **Odstranit**.

Po chvíli bude skupina prostředků včetně všech obsažených prostředků odstraněná.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili používat Azure Cache pro Redis z aplikace Node.js. Pokračujte k dalšímu rychlému startu a pomocí ASP.NET webové aplikace můžete používat Azure Cache for Redis.

> [!div class="nextstepaction"]
> [Vytvořte ASP.NET webovou aplikaci, která používá Azure Cache pro Redis.](./cache-web-app-howto.md)
