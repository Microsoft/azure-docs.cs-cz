---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 07780c94960c581ce69e4fdd45b346c75ab8b098
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329588"
---
### <a name="set-the-storage-account-connection"></a>Nastavení připojení účtu úložiště

Otevřete soubor Local. Settings. JSON a zkopírujte hodnotu `AzureWebJobsStorage`, což je připojovací řetězec účtu úložiště. Nastavte proměnnou prostředí `AZURE_STORAGE_CONNECTION_STRING` na připojovací řetězec pomocí tohoto příkazu bash:

```azurecli-interactive
AZURE_STORAGE_CONNECTION_STRING="<STORAGE_CONNECTION_STRING>"
```

Při nastavování připojovacího řetězce v proměnné prostředí `AZURE_STORAGE_CONNECTION_STRING` můžete získat přístup k účtu úložiště bez nutnosti zadávat ověřování pokaždé, když.