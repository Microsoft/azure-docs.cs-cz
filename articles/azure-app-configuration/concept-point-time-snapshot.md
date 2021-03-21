---
title: Načtení párů klíč-hodnota z určitého bodu v čase
titleSuffix: Azure App Configuration
description: Načte staré páry klíč-hodnota pomocí snímků v čase v konfiguraci aplikace Azure, které udržují záznam změn klíčových hodnot.
services: azure-app-configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 08/05/2020
ms.openlocfilehash: fa2dbb11b3b8b9afd90c7f6fe3ffe77e2e57c4e6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96929893"
---
# <a name="point-in-time-snapshot"></a>Snímek k určitému časovému okamžiku

Konfigurace aplikace Azure udržuje záznam změn provedených u klíčových hodnot. Tento záznam poskytuje časovou osu pro změny klíč-hodnota. Historii jakékoli hodnoty klíč-hodnota můžete kdykoli znovu sestavit v rámci období historie klíčů (7 dní pro úložiště úrovně Free nebo 30 dní pro úložiště úrovně Standard). Pomocí této funkce můžete "čas-cestování" zpět a načíst starou hodnotu klíč-hodnota. Můžete například obnovit nastavení konfigurace používané před nejnovějším nasazením, aby bylo možné aplikaci vrátit do předchozí konfigurace.

## <a name="key-value-retrieval"></a>Načtení hodnoty klíče

Pomocí Azure Portal nebo CLI můžete načíst minulé hodnoty klíč-hodnota. V Azure CLI použijte `az appconfig revision list` a přidejte vhodné parametry pro načtení požadovaných hodnot.  Zadáním názvu úložiště ( `--name <app-config-store-name>` ) nebo pomocí připojovacího řetězce () zadejte instanci Azure App Configuration `--connection-string <your-connection-string>` . Omezte výstup zadáním určitého bodu v čase ( `--datetime` ) a zadáním maximálního počtu položek, které se mají vrátit ( `--top` ).

Pokud nemáte rozhraní příkazového řádku Azure v místním počítači nainstalováno, můžete volitelně použít [Azure Cloud Shell](../cloud-shell/overview.md).

Načte všechny zaznamenané změny hodnot vašich klíčů.

```azurecli
az appconfig revision list --name <your-app-config-store-name>.
```

Načte všechny zaznamenané změny pro klíč `environment` a popisky `test` a `prod` .

```azurecli
az appconfig revision list --name <your-app-config-store-name> --key environment --label test,prod
```

Načte všechny zaznamenané změny v hierarchickém klíčovém prostoru `environment:prod` .

```azurecli
az appconfig revision list --name <your-app-config-store-name> --key environment:prod:* 
```

Načte všechny zaznamenané změny pro klíč v `color` určitém časovém okamžiku.

```azurecli
az appconfig revision list --connection-string <your-app-config-connection-string> --key color --datetime "2019-05-01T11:24:12Z" 
```

Načte posledních 10 zaznamenaných změn hodnot klíč-hodnota a vrátí pouze hodnoty pro `key` , `label` a `last_modified` časové razítko.

```azurecli-interactive
az appconfig revision list --name <your-app-config-store-name> --top 10 --fields key label last_modified
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření webové aplikace ASP.NET Core](./quickstart-aspnet-core-app.md)