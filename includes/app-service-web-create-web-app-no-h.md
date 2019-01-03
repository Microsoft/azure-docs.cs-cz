---
title: zahrnout soubor
description: zahrnout soubor
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 6725766ea761a93511d719f883b31821b105d61f
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53604398"
---
Ve službě Cloud Shell vytvořte [webovou aplikaci](../articles/app-service/overview.md) v plánu služby `myAppServicePlan` App Service. Můžete to udělat pomocí příkazu [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create). V následujícím příkladu nahraďte *\<app_name>* globálně jedinečným názvem aplikace (platné znaky jsou `a-z`, `0-9` a `-`).

```azurecli-interactive
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan --deployment-local-git
```

Po vytvoření webové aplikace se v rozhraní příkazového řádku Azure CLI zobrazí podobné informace jako v následujícím příkladu:

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

Vytvořili jste prázdnou webovou aplikaci s povoleným nasazením Gitu.

> [!NOTE]
> Adresa URL vzdáleného úložiště Git se zobrazuje ve vlastnosti `deploymentLocalGitUrl` ve formátu `https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git`. Tuto adresu URL si uložte, protože ji budete potřebovat později.
>

Přejděte do nově vytvořené webové aplikace.

```
http://<app_name>.azurewebsites.net
```

Vaše nová webová aplikace by měla vypadat takto:
