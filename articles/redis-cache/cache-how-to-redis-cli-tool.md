---
title: Jak používat rozhraní příkazového řádku redis s Azure Redis Cache | Dokumentace Microsoftu
description: Další informace o použití rozhraní příkazového řádku redis s Azure Redis Cache.
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: wesmc
ms.openlocfilehash: facc3e2079c8f5e19266f5379762d71754a6ed84
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38299087"
---
# <a name="how-to-use-the-redis-command-line-tool-with-azure-redis-cache"></a>Jak používat nástroj příkazového řádku Redis s Azure Redis Cache

*redis-cli.exe* je oblíbené nástroje příkazového řádku pro práci s mezipamětí Redis jako klient. Tento nástroj je také k dispozici pro použití s Azure Redis Cache.

Nástroj je k dispozici pro platformy Windows, stáhněte si [Redis nástroje příkazového řádku pro Windows](https://github.com/MSOpenTech/redis/releases/). 

Pokud chcete spustit nástroj příkazového řádku na jiné platformě, stáhněte si Redis Cache z [ http://redis.io/download ](https://redis.io/download).

## <a name="gather-cache-access-information"></a>Shromážděte informace o přístupu k mezipaměti

Můžete shromáždit informace potřebné pro přístup do mezipaměti ze tří metod:

1. Pomocí Azure CLI [az redis list-keys](https://docs.microsoft.com/cli/azure/redis?view=azure-cli-latest#az-redis-list-keys)
2. Pomocí Azure Powershellu [Get AzureRmRedisCacheKey](https://docs.microsoft.com/powershell/module/azurerm.rediscache/Get-AzureRmRedisCacheKey?view=azurermps-4.4.1)
3. Pomocí webu Azure portal.

V této části načtete klíče z portálu Azure portal.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-access-for-redis-cliexe"></a>Povolení přístupu k redis-cli.exe

S Azure Redis Cache pouze SSL port (6380) je ve výchozím nastavení povolené. `redis-cli.exe` Nástroj příkazového řádku nepodporuje SSL. Máte dvě možnosti konfigurace pro použití:

1. [Povolit port bez SSL (6379)](cache-configure.md#access-ports) - **tato konfigurace se nedoporučuje** vzhledem k tomu, že v této konfiguraci jsou přístupové klíče odesílají přes TCP ve formátu prostého textu. Tato změna může ohrozit přístup k vaší mezipaměti. Jediným případem, kde můžete zvážit tato konfigurace je jen prohlížejí mezipaměti testu.

2. Stáhněte a nainstalujte [stunnelu](https://www.stunnel.org/downloads.html).

    Spustit **stunnelu spuštění grafického uživatelského rozhraní** spuštění serveru.

    Klikněte pravým tlačítkem na hlavním panelu ikonu pro server stunnelu a klikněte na tlačítko **zobrazit okno Protokol**.

    V nabídce okno Protokol stunnelu, klikněte na **konfigurace** > **upravit konfiguraci** otevřete aktuálního konfiguračního souboru.

    Přidejte následující položku pro *redis-cli.exe* pod **služby definice** oddílu. Vložit název vaší skutečné mezipaměti místo `yourcachename`. 

    ```
    [redis-cli]
    client = yes
    accept = 127.0.0.1:6380
    connect = yourcachename.redis.cache.windows.net:6380
    ```

    Uložte a zavřete soubor konfigurace. 
  
    V nabídce okno Protokol stunnelu, klikněte na **konfigurace** > **opětovného načtení konfigurace**.


## <a name="connect-using-the-redis-command-line-tool"></a>Připojte se pomocí nástroje příkazového řádku Redis.

Při použití stunnelu, spusťte *redis-cli.exe*a předejte pouze váš *port*, a *přístupový klíč* (primární nebo sekundární) pro připojení k mezipaměti.

```
redis-cli.exe -p 6380 -a YourAccessKey
```

![stunnelu pomocí rozhraní příkazového řádku redis](media/cache-how-to-redis-cli-tool/cache-redis-cli-stunnel.png)

Pokud používáte testovací mezipaměť se **nezabezpečené** port bez SSL, spusťte `redis-cli.exe` a předejte mu vaše *název hostitele*, *port*, a *přístupový klíč*(primární nebo sekundární) pro připojení k mezipaměti testu.

```
redis-cli.exe -h yourcachename.redis.cache.windows.net -p 6379 -a YourAccessKey
```

![stunnelu pomocí rozhraní příkazového řádku redis](media/cache-how-to-redis-cli-tool/cache-redis-cli-non-ssl.png)




## <a name="next-steps"></a>Další postup

Další informace o používání [konzola Redis](cache-configure.md#redis-console) vydat příkazy.

