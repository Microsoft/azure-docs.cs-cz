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
ms.openlocfilehash: df71f0804b62eb4b17ff8d2f652b076b5c64c959
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
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

Pokud chcete streamování protokolů kdykoli zastavit, zadejte **CTRL** + **C**.

Můžete také zkontrolovat soubory protokolu v prohlížeči na adrese `https://<app-name>.scm.azurewebsites.net/api/logs/docker` .
