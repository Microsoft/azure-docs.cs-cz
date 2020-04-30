---
title: zahrnout soubor
description: zahrnout soubor
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 04/22/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: f397a3df7280b9277b2b7205368ef5788ed321aa
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "82206663"
---
Vytvořte [webovou aplikaci](../articles/app-service/containers/app-service-linux-intro.md) v plánu `myAppServicePlan` App Service. 

V Cloud Shell můžete použít [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest) příkaz. V následujícím příkladu nahraďte `<app-name>` globálně jedinečným názvem aplikace (platné znaky jsou `a-z`, `0-9` a `-`). Modul runtime je nastavený na `DOTNETCORE|LTS`, což je .net Core 3,1. Pokud chcete zobrazit všechny podporované moduly runtime, [`az webapp list-runtimes --linux`](/cli/azure/webapp?view=azure-cli-latest)spusťte příkaz. 

```azurecli-interactive
# Bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "DOTNETCORE|LTS" --deployment-local-git
# PowerShell
az --% webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "DOTNETCORE|LTS" --deployment-local-git
```

Po vytvoření webové aplikace Azure CLI zobrazí výstup podobný následujícímu příkladu:

<pre>
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
</pre>

Vytvořili jste prázdnou webovou aplikaci v kontejneru Linux s povoleným nasazením Gitu.

> [!NOTE]
> Adresa URL vzdáleného úložiště Git se zobrazuje ve vlastnosti `deploymentLocalGitUrl` ve formátu `https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git`. Tuto adresu URL si uložte, protože ji budete potřebovat později.
>

V současné době musíte spustit následující příkaz navíc ke správné konfiguraci rozhraní .NET Core (nahraďte `<app-name>` ho z předchozího kroku):

```azurecli-interactive
# Bash
az webapp config set --resource-group myResourceGroup --name <app-name> --linux-fx-version "DOTNETCORE|3.1"
# PowerShell
az --% webapp config set --resource-group myResourceGroup --name <app-name> --linux-fx-version "DOTNETCORE|3.1"
```
