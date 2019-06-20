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
ms.openlocfilehash: 48bb91b3b2e9a31de63e515edb857bc2a170ea79
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67174792"
---
## <a name="deploy-the-function-app-project-to-azure"></a>Nasazení projektu aplikace funkcí do Azure

Po vytvoření aplikace function app v Azure můžete použít [ `func azure functionapp publish` ](../articles/azure-functions/functions-run-local.md#project-file-deployment) příkaz základní nástroje pro nasazení projektu kódu do Azure. V následujícím příkazu nahraďte `<APP_NAME>` s názvem vaší aplikace z předchozího kroku.

```bash
func azure functionapp publish <APP_NAME>
```

Zobrazí se výstup podobný následující text, který byl zkrácen pro lepší čitelnost.

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

Zkopírujte hodnotu adresy URL vyvolat pro vaše HttpTrigger, které nyní můžete funkci otestovat v Azure. Adresa URL obsahuje `code` dotaz řetězcová hodnota, která je klíč funkce. Tento klíč je těžké pro ostatní volání váš koncový bod triggeru HTTP v Azure.