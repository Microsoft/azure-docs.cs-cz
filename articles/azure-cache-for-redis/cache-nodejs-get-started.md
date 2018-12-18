---
title: Rychlý start pro další informace o použití mezipaměti Azure pro Redis pomocí Node.js | Dokumentace Microsoftu
description: V tomto rychlém startu se dozvíte, jak používat Azure Cache pro Redis s využitím Node.js a node_redis.
services: azure-cache-for-redis
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: v-lincan
ms.assetid: 06fddc95-8029-4a8d-83f5-ebd5016891d9
ms.service: cache
ms.devlang: nodejs
ms.topic: quickstart
ms.tgt_pltfrm: azure-cache-for-redis
ms.workload: tbd
ms.date: 05/21/2018
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: cff742688c307becf4bf87cced15036b25fa10c8
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53554618"
---
# <a name="quickstart-how-to-use-azure-cache-for-redis-with-nodejs"></a>Rychlý start: Použití mezipaměti Azure pro Redis s využitím Node.js



Mezipaměti Redis Azure nabízí přístup k zabezpečené vyhrazené mezipaměti Azure Redis, spravovanou microsoftem. Vaše mezipaměť je přístupná ze všech aplikací v rámci Microsoft Azure.

Toto téma ukazuje, jak začít pracovat s mezipamětí Azure pro Redis pomocí Node.js. 

K dokončení kroků v tomto rychlém startu můžete použít jakýkoli editor kódu. Skvělou volbou je však editor [Visual Studio Code](https://code.visualstudio.com/), který je dostupný pro platformy Windows, macOS a Linux.

![Dokončená aplikace v mezipaměti](./media/cache-nodejs-get-started/cache-app-complete.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Požadavky
Nainstalujte [node_redis](https://github.com/mranney/node_redis):

    npm install redis

Tento kurz používá [node_redis](https://github.com/mranney/node_redis). Příklady použití dalších klientů Node.js najdete v individuální dokumentaci pro klienty Node.js uvedené v [klientech Node.js Redis](http://redis.io/clients#nodejs).


## <a name="create-a-cache"></a>Vytvoření mezipaměti
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]


Přidejte proměnné prostředí pro **NÁZEV HOSTITELE** a **primární** přístupový klíč. Tyto proměnné z kódu použijete místo citlivých informací, které byste zahrnuli přímo do kódu.

```
set REDISCACHEHOSTNAME=contosoCache.redis.cache.windows.net
set REDISCACHEKEY=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
```


## <a name="connect-to-the-cache"></a>Připojení k mezipaměti

Nejnovější sestavení [node_redis](https://github.com/mranney/node_redis) poskytuje podporu pro připojení k mezipaměti Azure pro Redis pomocí protokolu SSL. Následující příklad ukazuje, jak připojit k mezipaměti Azure Redis pomocí protokolu SSL koncového bodu 6380. 

```js
var redis = require("redis");

// Add your cache name and access key.
var client = redis.createClient(6380, process.env.REDISCACHEHOSTNAME,
    {auth_pass: process.env.REDISCACHEKEY, tls: {servername: process.env.REDISCACHEHOSTNAME}});
```

Nevytvářejte nová připojení pro každou operaci v kódu. Raději co nejvíce připojení použijte opakovaně. 

## <a name="create-a-new-nodejs-app"></a>Vytvoření nové aplikace Node.js

Vytvořte nový soubor skriptu s názvem *redistest.js*.

Přidejte následující příklad JavaScriptu do souboru. Tento kód ukazuje, jak se připojit k Azure Cache pro instanci Redis pomocí mezipaměti název hostitele a proměnných prostředí klíče. Kód také ukládá a načítá hodnotu řetězce v mezipaměti. Spouští se také příkazy `PING` a `CLIENT LIST`. Pro další příklady použití Redis pomocí klienta [node_redis](https://github.com/mranney/node_redis) se podívejte na [http://redis.js.org/](http://redis.js.org/).

```js
var redis = require("redis");
var bluebird = require("bluebird");

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

![Dokončená aplikace v mezipaměti](./media/cache-nodejs-get-started/cache-app-complete.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud budete pokračovat k dalšímu kurzu, můžete prostředky vytvořené v tomto rychlém startu zachovat a znovu je použít.

V opačném případě, pokud jste už s ukázkovou aplikací v tomto rychlém startu skončili, můžete prostředky Azure vytvořené v tomto rychlém startu odstranit, abyste se vyhnuli poplatkům. 

> [!IMPORTANT]
> Odstranění skupiny prostředků je nevratné a skupina prostředků včetně všech v ní obsažených prostředků bude trvale odstraněna. Ujistěte se, že nechtěně neodstraníte nesprávnou skupinu prostředků nebo prostředky. Pokud jste vytvořili prostředky pro hostování této ukázky ve stávající skupině prostředků obsahující prostředky, které chcete zachovat, můžete místo odstranění skupiny prostředků odstranit jednotlivé prostředky z jejich odpovídajících oken.
>

Přihlaste se na web [Azure Portal ](https://portal.azure.com) a klikněte na **Skupiny prostředků**.

Do textového pole **Filtrovat podle názvu** zadejte název vaší skupiny prostředků. V pokynech v tomto článku se používala skupina prostředků *TestResources*. Ve výsledcích hledání klikněte na **...** u vaší skupiny prostředků a pak na **Odstranit skupinu prostředků**.

![Odstranění](./media/cache-nodejs-get-started/cache-delete-resource-group.png)

Zobrazí se výzva k potvrzení odstranění skupiny prostředků. Potvrďte odstranění zadáním názvu vaší skupiny prostředků a klikněte na **Odstranit**.

Po chvíli bude skupina prostředků včetně všech obsažených prostředků odstraněná.



## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste zjistili, jak používat Azure Cache z aplikace Node.js pro Redis. Pokračujte k dalšímu rychlému startu pro účely Azure mezipaměti Redis s webovou aplikaci ASP.NET.

> [!div class="nextstepaction"]
> [Vytvoření webové aplikace ASP.NET využívající Azure Cache pro Redis.](./cache-web-app-howto.md)



