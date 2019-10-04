---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 89e2e64910e33d43c107ee88137de718d020d7d2
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2019
ms.locfileid: "71839013"
---
Otevřete soubor Local. Settings. JSON a zkopírujte hodnotu `AzureWebJobsStorage`, což je připojovací řetězec účtu úložiště. Nastavte proměnnou prostředí `AZURE_STORAGE_CONNECTION_STRING` na připojovací řetězec pomocí tohoto příkazu bash:

```azurecli-interactive
AZURE_STORAGE_CONNECTION_STRING="<STORAGE_CONNECTION_STRING>"
```

Při nastavování připojovacího řetězce v proměnné prostředí `AZURE_STORAGE_CONNECTION_STRING` můžete získat přístup k účtu úložiště bez nutnosti zadávat ověřování pokaždé, když.