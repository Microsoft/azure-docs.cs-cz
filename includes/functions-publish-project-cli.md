---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: c99cac6626cb40b8fd368e4fc1d8454bb2195521
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "93425016"
---
## <a name="deploy-the-function-project-to-azure"></a>Nasazení projektu funkce do Azure

Po úspěšném vytvoření aplikace Function App v Azure jste teď připraveni nasadit svůj místní projekt Functions pomocí příkazu [Func Azure functionapp Publish](../articles/azure-functions/functions-run-local.md#project-file-deployment) .  

V následujícím příkladu nahraďte `<APP_NAME>` názvem vaší aplikace.

```console
func azure functionapp publish <APP_NAME>
```

Příkaz publikovat zobrazuje výsledky podobné následujícímu výstupu (zkrácený pro zjednodušení):

<pre>
...

Getting site publishing info...
Creating archive for current directory...
Performing remote build for functions project.

...

Deployment successful.
Remote build succeeded!
Syncing triggers...
Functions in msdocs-azurefunctions-qs:
    HttpExample - [httpTrigger]
        Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample
</pre>
