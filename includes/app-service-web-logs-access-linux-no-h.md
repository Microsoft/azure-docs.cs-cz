---
title: zahrnout soubor
description: zahrnout soubor
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: e2c5794e5ce6e23b60bff513562f69c9333d6e34
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92743799"
---
Můžete přistupovat k protokolům konzoly generovaným zevnitř kontejneru.

Nejprve zapněte protokolování kontejneru spuštěním následujícího příkazu:

```azurecli-interactive
az webapp log config --name <app-name> --resource-group <resource-group-name> --docker-container-logging filesystem
```

Nahraďte `<app-name>` a `<resource-group-name>` názvem vhodným pro vaši webovou aplikaci.

Jakmile je protokolování kontejneru zapnuté, spusťte následující příkaz, který zobrazí datový proud protokolu:

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group <resource-group-name>
```

Pokud nevidíte protokoly konzoly okamžitě, podívejte se znovu za 30 sekund.

Pokud chcete streamování protokolů kdykoli zastavit, zadejte **CTRL** + **C** .

Můžete také zkontrolovat soubory protokolu v prohlížeči na adrese `https://<app-name>.scm.azurewebsites.net/api/logs/docker` .
