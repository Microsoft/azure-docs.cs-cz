---
title: Použití rozhraní příkazového řádku redis s Azure Redis Cache | Microsoft Docs
description: Naučte se používat rozhraní příkazového řádku redis s Azure Redis Cache.
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
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="how-to-use-the-redis-command-line-tool-with-azure-redis-cache"></a>Jak používat nástroj příkazového řádku Redis s Azure Redis Cache

*redis cli.exe* je Oblíbené nástroj příkazového řádku pro interakci s Redis Cache jako klient. Tento nástroj je také k dispozici pro použití s Azure Redis Cache.

Nástroj je k dispozici na platformách systému Windows tak, že stáhnete [Redis nástroje příkazového řádku pro Windows](https://github.com/MSOpenTech/redis/releases/). 

Pokud chcete spustit nástroj příkazového řádku na jiné platformě, stáhněte si Redis Cache z [ http://redis.io/download ](https://redis.io/download).

## <a name="gather-cache-access-information"></a>Shromažďování informací o přístupu k mezipaměti

Můžete shromáždit informace potřebné pro přístup do mezipaměti ze tří metod:

1. Použití Azure CLI [az redis seznamu klíčů](https://docs.microsoft.com/cli/azure/redis?view=azure-cli-latest#az-redis-list-keys)
2. Pomocí Azure PowerShell [Get-AzureRmRedisCacheKey](https://docs.microsoft.com/powershell/module/azurerm.rediscache/Get-AzureRmRedisCacheKey?view=azurermps-4.4.1)
3. Pomocí portálu Azure.

V této části se budou načítat klíče z portálu Azure.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-access-for-redis-cliexe"></a>Povolit přístup pro redis cli.exe

S Azure Redis Cache pouze SSL port (6380) je ve výchozím nastavení povolené. `redis-cli.exe` Nástroj příkazového řádku nepodporuje SSL. Máte dvě možnosti konfigurace pro použití:

1. [Povolení portu bez SSL (6379)](cache-configure.md#access-ports) - **tato konfigurace se nedoporučuje** vzhledem k tomu, že v této konfiguraci přístupové klíče se odesílají přes TCP ve formátu prostého textu. Tato změna může ohrozit přístup ke své mezipaměti. Jediný scénář, kde můžete zvážit tato konfigurace je právě prohlížejí testovací mezipaměti.

2. Stáhněte a nainstalujte [stunnel](https://www.stunnel.org/downloads.html).

    Spustit **stunnel grafickým uživatelským rozhraním počáteční** Start pro server.

    Klikněte pravým tlačítkem na ikonu panelu serveru stunnel a klikněte na tlačítko **zobrazit okno Protokol**.

    V nabídce okno Protokol stunnel, klikněte na tlačítko **konfigurace** > **upravit konfiguraci** otevřete aktuálního konfiguračního souboru.

    Přidejte následující položku pro *redis cli.exe* pod **služby definice** části. Vložit název skutečné mezipaměti místě `yourcachename`. 

    ```
    [redis-cli]
    client = yes
    accept = 127.0.0.1:6380
    connect = yourcachename.redis.cache.windows.net:6380
    ```

    Uložte a zavřete konfiguračního souboru. 
  
    V nabídce okno Protokol stunnel, klikněte na tlačítko **konfigurace** > **opětovného načtení konfigurace**.


## <a name="connect-using-the-redis-command-line-tool"></a>Připojte pomocí nástroje příkazového řádku Redis.

Při použití stunnel, spusťte *redis cli.exe*a předejte pouze vaše *port*, a *přístupový klíč* (primární nebo sekundární) pro připojení k mezipaměti.

```
redis-cli.exe -p 6380 -a YourAccessKey
```

![stunnel pomocí rozhraní příkazového řádku redis](media/cache-how-to-redis-cli-tool/cache-redis-cli-stunnel.png)

Pokud používáte testovací mezipaměť **nezabezpečená** port bez SSL, spusťte `redis-cli.exe` a předejte vaše *název hostitele*, *port*, a *přístupový klíč*(primární nebo sekundární) pro připojení k mezipaměti testu.

```
redis-cli.exe -h yourcachename.redis.cache.windows.net -p 6379 -a YourAccessKey
```

![stunnel pomocí rozhraní příkazového řádku redis](media/cache-how-to-redis-cli-tool/cache-redis-cli-non-ssl.png)




## <a name="next-steps"></a>Další postup

Další informace o používání [Redis konzoly](cache-configure.md#redis-console) vydat příkazy.

