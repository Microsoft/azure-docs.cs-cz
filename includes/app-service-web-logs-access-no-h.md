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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84905863"
---
Chcete-li získat přístup k protokolům konzoly generovaným zevnitř kódu aplikace v App Service, zapněte protokolování diagnostiky spuštěním následujícího příkazu v [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp log config --resource-group <resource-group-name> --name <app-name> --application-logging true --level Verbose
```

Možné hodnoty pro `--level` jsou: `Error` , `Warning` , a `Info` `Verbose` . Každá další úroveň zahrnuje předchozí úroveň. Například: `Error` obsahuje pouze chybové zprávy a `Verbose` obsahuje všechny zprávy.

Jakmile bude zapnuté diagnostické protokolování, spusťte následující příkaz, který zobrazí datový proud protokolu:

```azurecli-interactive
az webapp log tail --resource-group <resource-group-name> --name <app-name>
```

Pokud nevidíte protokoly konzoly okamžitě, podívejte se znovu za 30 sekund.

> [!NOTE]
> Soubory protokolu můžete také zkontrolovat v prohlížeči na adrese `https://<app-name>.scm.azurewebsites.net/api/logs/docker` .

Pokud chcete streamování protokolů kdykoli zastavit, zadejte `Ctrl` + `C` .
