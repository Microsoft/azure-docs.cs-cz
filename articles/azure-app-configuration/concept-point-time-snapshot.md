---
title: Načtení párů klíč-hodnota z bodu v čase
titleSuffix: Azure App Configuration
description: Načtení starých párů klíč-hodnota pomocí snímků v čase v azure app configuration
services: azure-app-configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 1e2a4f7a7bc5db1b6a49f085821f7fa2bde54229
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523649"
---
# <a name="point-in-time-snapshot"></a>Snímek k určitému časovému okamžiku

Azure App Configuration udržuje záznam o změnách provedených v párech klíč hodnota. Tento záznam poskytuje časovou osu změn klíč-hodnota. Můžete rekonstruovat historii libovolné hodnoty klíče a poskytnout jeho minulé hodnoty v každém okamžiku v předchozích sedmi dnech. Pomocí této funkce můžete "cestování časem" zpět a načíst starou hodnotu klíče. Můžete například obnovit nastavení konfigurace použitá před posledním nasazením, abyste vrátili aplikaci zpět na předchozí konfiguraci.

## <a name="key-value-retrieval"></a>Načítání hodnoty klíče

Azure PowerShell můžete použít k načtení minulých hodnot klíče.  Použití `az appconfig revision list`, přidání mj.  Zadejte instanci Konfigurace aplikace Azure`--name {app-config-store-name}`zadáním názvu obchodu (`--connection-string {your-connection-string}`) nebo pomocí připojovacího řetězce ( ). Omezte výstup zadáním konkrétního`--datetime`bodu v čase ( ) a`--top`zadáním maximálního počtu položek, které mají být vráceny ( ).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Načíst všechny zaznamenané změny hodnoty klíče.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name}.
```

Načíst všechny zaznamenané `environment` změny pro `test` `prod`klíč a popisky a .

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment --label test,prod
```

Načíst všechny zaznamenané změny v `environment:prod`prostoru hierarchického klíče .

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment:prod:* 
```

Načíst všechny zaznamenané `color` změny pro klíč v určitém bodě v čase.

```azurepowershell
az appconfig revision list --connection-string {your-app-config-connection-string} --key color --datetime "2019-05-01T11:24:12Z" 
```

Načtěte posledních 10 zaznamenaných změn hodnot klíče `key`a `label`vraťte pouze hodnoty pro , a `last-modified` časové razítko.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --top 10 --fields key,label,last-modified
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření webové aplikace ASP.NET Core](./quickstart-aspnet-core-app.md)  
