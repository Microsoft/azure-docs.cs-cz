---
title: Jak používat Redis-CLI s Azure cache pro Redis
description: Naučte se používat *Redis-CLI. exe* jako nástroj příkazového řádku pro interakci s mezipamětí Azure pro Redis jako klient.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 03/22/2018
ms.openlocfilehash: bd2da798cae92a7e47bd879b69dd108618463402
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81010762"
---
# <a name="how-to-use-the-redis-command-line-tool-with-azure-cache-for-redis"></a>Jak používat nástroj příkazového řádku Redis s mezipamětí Azure pro Redis

*Redis-CLI. exe* je oblíbený nástroj příkazového řádku pro interakci s mezipamětí Azure pro Redis jako klient. Tento nástroj je také k dispozici pro použití s mezipamětí Azure pro Redis.

Nástroj je k dispozici pro platformy Windows stažením [nástrojů příkazového řádku Redis pro Windows](https://github.com/MSOpenTech/redis/releases/). 

Pokud chcete spustit nástroj příkazového řádku na jiné platformě, Stáhněte si Azure cache pro Redis z [https://redis.io/download](https://redis.io/download).

## <a name="gather-cache-access-information"></a>Shromáždění informací o přístupu k mezipaměti

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Informace potřebné pro přístup do mezipaměti můžete shromáždit pomocí tří metod:

1. Azure CLI pomocí [AZ Redis list-Keys](https://docs.microsoft.com/cli/azure/redis?view=azure-cli-latest#az-redis-list-keys)
2. Azure PowerShell pomocí [Get-AzRedisCacheKey](https://docs.microsoft.com/powershell/module/az.rediscache/Get-AzRedisCacheKey)
3. Pomocí webu Azure Portal.

V této části nasadíte klíče z Azure Portal.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-access-for-redis-cliexe"></a>Povolit přístup pro Redis-CLI. exe

V případě služby Azure cache pro Redis je ve výchozím nastavení povolen pouze port TLS (6380). Nástroj `redis-cli.exe` příkazového řádku nepodporuje protokol TLS. Máte dvě možnosti konfigurace, které můžete použít:

1. [Povolit port bez TLS (6379)](cache-configure.md#access-ports) - **Tato konfigurace se nedoporučuje** , protože v této konfiguraci se přístupové klíče odesílají pomocí protokolu TCP ve formátu prostého textu. Tato změna může ohrozit přístup k vaší mezipaměti. Jediným scénářem, kdy můžete uvažovat o této konfiguraci, je situace, kdy přistupujete pouze k testovací mezipaměti.

2. Stáhněte a nainstalujte [stunnelu](https://www.stunnel.org/downloads.html).

    Spuštění serveru spustíte spuštěním **STUNNELU GUI** .

    Klikněte pravým tlačítkem na ikonu hlavního panelu serveru stunnelu a pak klikněte na **Zobrazit okno protokolu**.

    V nabídce okna protokolu stunnelu klikněte na **Konfigurace** > **Upravit konfiguraci** a otevřete aktuální konfigurační soubor.

    V části **definice služby** přidejte následující položku pro *Redis-CLI. exe* . Místo zadejte svůj skutečný název mezipaměti `yourcachename`. 

    ```
    [redis-cli]
    client = yes
    accept = 127.0.0.1:6380
    connect = yourcachename.redis.cache.windows.net:6380
    ```

    Uložte a zavřete konfigurační soubor. 
  
    V nabídce okna protokolu stunnelu klikněte na **Konfigurace** > **znovu načíst konfiguraci**.


## <a name="connect-using-the-redis-command-line-tool"></a>Připojte se pomocí nástroje příkazového řádku Redis.

Pokud používáte stunnelu, spusťte *Redis-CLI. exe*a předejte jenom svůj *port*a *přístupová klávesa* (primární nebo sekundární) se připojte k mezipaměti.

```
redis-cli.exe -p 6380 -a YourAccessKey
```

![stunnelu s Redis-CLI](media/cache-how-to-redis-cli-tool/cache-redis-cli-stunnel.png)

Pokud používáte testovací mezipaměť s **nezabezpečeným** portem bez TLS `redis-cli.exe` , spusťte a předejte vašemu *názvu hostitele*, *portu*a *přístupovému klíči* (primárnímu nebo sekundárnímu), aby se připojil k testovací mezipaměti.

```
redis-cli.exe -h yourcachename.redis.cache.windows.net -p 6379 -a YourAccessKey
```

![stunnelu s Redis-CLI](media/cache-how-to-redis-cli-tool/cache-redis-cli-non-ssl.png)




## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o použití [konzoly Redis](cache-configure.md#redis-console) k vydávání příkazů.

