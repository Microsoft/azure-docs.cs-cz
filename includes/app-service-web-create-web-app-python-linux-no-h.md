---
title: zahrnout soubor
description: zahrnout soubor
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 08/07/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 08dca53b58a824367ebaf0c890ea1053e8938b2e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67175680"
---
Vytvořte [webovou aplikaci](../articles/app-service/containers/app-service-linux-intro.md) v plánu služby `myAppServicePlan` App Service. 

V prostředí Cloud Shell můžete [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest) použít příkaz. V následujícím příkladu nahraďte `<app-name>` globálně jedinečným názvem aplikace (platné znaky jsou `a-z`, `0-9` a `-`). Modul runtime je nastavený na `PYTHON|3.7`. Chcete-li zobrazit všechny [`az webapp list-runtimes --linux`](/cli/azure/webapp?view=azure-cli-latest)podporované moduly runtimes, spusťte . 

```azurecli-interactive
# Bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PYTHON|3.7" --deployment-local-git
# PowerShell
az --% webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PYTHON|3.7" --deployment-local-git
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

Vytvořili jste novou prázdnou webovou aplikaci s povoleným nasazením Gitu.

> [!NOTE]
> Adresa URL vzdáleného úložiště Git se zobrazuje ve vlastnosti `deploymentLocalGitUrl` ve formátu `https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git`. Tuto adresu URL si uložte, protože ji budete potřebovat později.
>
