---
title: Jak používat redis-cli s Azure Cache pro Redis
description: Naučte se používat *redis-cli.exe* jako nástroj příkazového řádku pro interakci s Azure Cache pro Redis jako klient.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 03/22/2018
ms.openlocfilehash: bd2da798cae92a7e47bd879b69dd108618463402
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010762"
---
# <a name="how-to-use-the-redis-command-line-tool-with-azure-cache-for-redis"></a>Jak používat nástroj příkazového řádku Redis s Azure Cache for Redis

*redis-cli.exe* je populární nástroj příkazového řádku pro interakci s Azure Cache pro Redis jako klienta. Tento nástroj je také k dispozici pro použití s Azure Cache pro Redis.

Nástroj je k dispozici pro platformy Windows stažením [nástrojů příkazového řádku Redis pro Windows](https://github.com/MSOpenTech/redis/releases/). 

Pokud chcete spustit nástroj příkazového řádku na jiné platformě, [https://redis.io/download](https://redis.io/download)stáhněte si Azure Cache for Redis z .

## <a name="gather-cache-access-information"></a>Shromáždění informací o přístupu ke mezipaměti

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Informace potřebné pro přístup ke mezipaměti můžete shromáždit třemi způsoby:

1. Azure CLI pomocí [klíčů seznamu az redis](https://docs.microsoft.com/cli/azure/redis?view=azure-cli-latest#az-redis-list-keys)
2. Azure PowerShell pomocí [klíče Get-AzRedisCacheKey](https://docs.microsoft.com/powershell/module/az.rediscache/Get-AzRedisCacheKey)
3. Pomocí webu Azure Portal.

V této části načtete klíče z webu Azure Portal.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-access-for-redis-cliexe"></a>Povolení přístupu pro redis-cli.exe

S Azure Cache pro Redis je ve výchozím nastavení povolen pouze port TLS (6380). Nástroj `redis-cli.exe` příkazového řádku nepodporuje TLS. Máte dvě možnosti konfigurace, které můžete použít:

1. [Povolte port ne-TLS (6379)](cache-configure.md#access-ports) - **Tato konfigurace se nedoporučuje,** protože v této konfiguraci jsou přístupové klíče odesílány prostřednictvím protokolu TCP ve prostém textu. Tato změna může ohrozit přístup k mezipaměti. Jediný scénář, kde můžete zvážit tuto konfiguraci je, když jste právě přístup k mezipaměti test.

2. Stáhněte si a nainstalujte [stunnel](https://www.stunnel.org/downloads.html).

    Spusťte **stunnel GUI Start** pro spuštění serveru.

    Klepněte pravým tlačítkem myši na ikonu hlavního panelu serveru stunnel a klepněte na příkaz **Zobrazit okno protokolu**.

    V nabídce stunnel Log Window kliknutím na **Konfigurace** > **konfigurace otevřete** aktuální konfigurační soubor.

    Do části **Definice služby** přidejte následující položku pro *redis-cli.exe.* Místo aplikace vložte skutečný `yourcachename`název mezipaměti. 

    ```
    [redis-cli]
    client = yes
    accept = 127.0.0.1:6380
    connect = yourcachename.redis.cache.windows.net:6380
    ```

    Uložte a zavřete konfigurační soubor. 
  
    V nabídce stunnel Log Window klepněte na **položku Konfigurace** > **znovu načíst konfiguraci**.


## <a name="connect-using-the-redis-command-line-tool"></a>Připojte se pomocí nástroje příkazového řádku Redis.

Při použití stunnel, spusťte *redis-cli.exe*a předat pouze *port*a *přístupový klíč* (primární nebo sekundární) pro připojení ke mezipaměti.

```
redis-cli.exe -p 6380 -a YourAccessKey
```

![stunnel s redis-cli](media/cache-how-to-redis-cli-tool/cache-redis-cli-stunnel.png)

Pokud používáte testovací mezipaměť s **nezabezpečeným** portem `redis-cli.exe` tls, spusťte a předejte *název hostitele*, *port*a *přístupový klíč* (primární nebo sekundární) pro připojení k testovací mezipaměti.

```
redis-cli.exe -h yourcachename.redis.cache.windows.net -p 6379 -a YourAccessKey
```

![stunnel s redis-cli](media/cache-how-to-redis-cli-tool/cache-redis-cli-non-ssl.png)




## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o používání [konzoly Redis](cache-configure.md#redis-console) k vydávání příkazů.

