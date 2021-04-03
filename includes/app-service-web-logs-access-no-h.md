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
ms.openlocfilehash: e6c4b07d01a4992e22107cb7d524646f439c37c6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "95997896"
---
Pokud chcete získat přístup k protokolům konzoly vygenerovaným v rámci kódu aplikace ve službě App Service, zapněte protokolování diagnostiky spuštěním následujícího příkazu v [Cloud Shellu](https://shell.azure.com):

```azurecli-interactive
az webapp log config --resource-group <resource-group-name> --name <app-name> --application-logging true --level Verbose
```

Možné hodnoty pro `--level` jsou: `Error`, `Warning`, `Info` a `Verbose`. Každá další úroveň zahrnuje předchozí úroveň. Například `Error` zahrnuje jenom chybové zprávy a `Verbose` zahrnuje všechny zprávy.

Jakmile je aktivované protokolování diagnostiky, spusťte následující příkaz pro zobrazení streamu protokolů:

```azurecli-interactive
az webapp log tail --resource-group <resource-group-name> --name <app-name>
```

Pokud nevidíte protokoly konzoly okamžitě, podívejte se znovu za 30 sekund.

> [!NOTE]
> Soubory protokolu můžete také zkontrolovat v prohlížeči na `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Streamování protokolů můžete kdykoli zastavit zadáním `Ctrl`+`C`.
