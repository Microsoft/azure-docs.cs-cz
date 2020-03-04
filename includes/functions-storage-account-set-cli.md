---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 4ace70abe0112e0fe27d177c02bcb697746c92cc
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2020
ms.locfileid: "78262019"
---
### <a name="set-the-storage-account-connection"></a>Nastavení připojení účtu úložiště

Otevřete soubor Local. Settings. JSON a zkopírujte hodnotu `AzureWebJobsStorage`, což je připojovací řetězec účtu úložiště. Nastavte proměnnou prostředí `AZURE_STORAGE_CONNECTION_STRING` na připojovací řetězec pomocí tohoto příkazu bash:

```bash
AZURE_STORAGE_CONNECTION_STRING="<STORAGE_CONNECTION_STRING>"
```

Při nastavování připojovacího řetězce v proměnné prostředí `AZURE_STORAGE_CONNECTION_STRING` máte přístup k účtu úložiště bez nutnosti zadávat ověřování pokaždé, když.