---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 4ace70abe0112e0fe27d177c02bcb697746c92cc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "78262019"
---
### <a name="set-the-storage-account-connection"></a>Nastavení připojení účtu úložiště

Otevřete soubor Local. Settings. JSON a zkopírujte hodnotu `AzureWebJobsStorage`, což je připojovací řetězec účtu úložiště. Nastavte proměnnou `AZURE_STORAGE_CONNECTION_STRING` prostředí na připojovací řetězec pomocí tohoto příkazu bash:

```bash
AZURE_STORAGE_CONNECTION_STRING="<STORAGE_CONNECTION_STRING>"
```

Při nastavování připojovacího řetězce v `AZURE_STORAGE_CONNECTION_STRING` proměnné prostředí můžete získat přístup k účtu úložiště bez nutnosti zadávat ověřování pokaždé, když.