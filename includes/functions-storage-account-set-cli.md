---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 4ace70abe0112e0fe27d177c02bcb697746c92cc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78262019"
---
### <a name="set-the-storage-account-connection"></a>Nastavení připojení účtu úložiště

Otevřete soubor local.settings.json a `AzureWebJobsStorage`zkopírujte hodnotu , což je připojovací řetězec účtu úložiště. Nastavte `AZURE_STORAGE_CONNECTION_STRING` proměnnou prostředí na připojovací řetězec pomocí tohoto příkazu Bash:

```bash
AZURE_STORAGE_CONNECTION_STRING="<STORAGE_CONNECTION_STRING>"
```

Když nastavíte připojovací řetězec v proměnné `AZURE_STORAGE_CONNECTION_STRING` prostředí, můžete přistupovat k účtu úložiště, aniž byste museli pokaždé zajišťovat ověřování.