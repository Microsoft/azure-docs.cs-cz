---
title: zahrnout soubor
description: zahrnout soubor
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 01/31/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 7c5eee801c4aaf4f4cc9abd8c0d423979341c823
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67175682"
---
Vytvořte [webovou aplikaci](../articles/app-service/containers/app-service-linux-intro.md) v plánu služby App Service `myAppServicePlan`. 

Ve službě Cloud Shell můžete použít příkaz [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest). V následujícím příkladu nahraďte `<app-name>` globálně jedinečným názvem aplikace (platné znaky jsou `a-z`, `0-9` a `-`). Modul runtime je nastavený na `DOTNETCORE|2.1`. Pokud chcete zobrazit všechny podporované moduly runtime, spusťte příkaz [`az webapp list-runtimes --linux`](/cli/azure/webapp?view=azure-cli-latest). 

```azurecli-interactive
# Bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "DOTNETCORE|2.1" --deployment-local-git
# PowerShell
az --% webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "DOTNETCORE|2.1" --deployment-local-git
```

Po vytvoření webové aplikace Azure CLI zobrazí výstup podobný následujícímu příkladu:

```json
Local git is configured with url of 'https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app-name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

Vytvořili jste prázdnou webovou aplikaci v kontejneru Linux s povoleným nasazením Gitu.

> [!NOTE]
> Adresa URL vzdáleného úložiště Git se zobrazuje ve vlastnosti `deploymentLocalGitUrl` ve formátu `https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git`. Tuto adresu URL si uložte, protože ji budete potřebovat později.
>
