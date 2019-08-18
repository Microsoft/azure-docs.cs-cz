---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: e2d63ab38bad341400538c5079fee22737cf0b8e
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2019
ms.locfileid: "69562950"
---
## <a name="deploy-the-function-app-project-to-azure"></a>Nasazení projektu aplikace funkcí do Azure

Po vytvoření aplikace Function App v Azure můžete k nasazení kódu projektu do [`func azure functionapp publish`](../articles/azure-functions/functions-run-local.md#project-file-deployment) Azure použít příkaz Core Tools. V těchto příkladech nahraďte `<APP_NAME>` názvem vaší aplikace z předchozího kroku.

### <a name="c--javascript"></a>C\# /JavaScript

```command
func azure functionapp publish <APP_NAME>
```

### <a name="python"></a>Python

```command
func azure functionapp publish <APP_NAME> --build remote
```

### <a name="typescript"></a>TypeScript

```command
npm run build:production 
func azure functionapp publish <APP_NAME>
```

Zobrazí se výstup podobný následujícímu, který byl zkrácen z důvodu čitelnosti:

```output
Getting site publishing info...
...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
Functions in myfunctionapp:
    HttpTrigger - [httpTrigger]
        Invoke url: https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....
```

`Invoke url` Zkopírujte hodnotu`HttpTrigger`pro, kterou teď můžete použít k otestování funkce v Azure. Adresa URL obsahuje `code` hodnotu řetězce dotazu, který je klíčovou funkcí. Tento klíč ztěžuje ostatním uživatelům volání koncového bodu HTTP triggeru v Azure.