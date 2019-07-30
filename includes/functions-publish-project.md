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
ms.openlocfilehash: fbb537c9584c948af37694b3bfc77a7c345e084d
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639103"
---
## <a name="deploy-the-function-app-project-to-azure"></a>Nasazení projektu aplikace funkcí do Azure

Po vytvoření aplikace Function App v Azure můžete k nasazení kódu projektu do [`func azure functionapp publish`](../articles/azure-functions/functions-run-local.md#project-file-deployment) Azure použít příkaz Core Tools. V následujícím příkazu nahraďte `<APP_NAME>` názvem vaší aplikace z předchozího kroku.

```bash
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