---
title: zahrnout soubor
description: zahrnout soubor
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 0dd6618bdee8e6810d414d4b04b16a1e0a9c90ed
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66137345"
---
Protokoly konzoly z uvnitř kontejneru přístupná. Nejdříve zapněte protokolování kontejneru spuštěním následujícího příkazu ve službě Cloud Shell:

```azurecli-interactive
az webapp log config --name <app-name> --resource-group myResourceGroup --docker-container-logging filesystem
```

Po zapnutí protokolování kontejneru spuštěním následujícího příkazu zobrazíte datový proud protokolu:

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
```

Pokud nevidíte protokoly konzoly okamžitě, podívejte se znovu za 30 sekund.

> [!NOTE]
> Můžete také zkontrolovat soubory protokolů z prohlížeče na `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Streamování protokolů kdykoli zastavit zadáním `Ctrl` + `C`.
