---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 801613f4e3fb1e754856d716c6815895ea5da3aa
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2019
ms.locfileid: "71839175"
---
Aplikaci Function App jste už vytvořili v Azure společně s požadovaným účtem úložiště. Připojovací řetězec pro tento účet je bezpečně uložený v nastavení aplikace v Azure. V tomto článku napíšete zprávy do fronty úložiště ve stejném účtu. Pokud se chcete připojit k účtu úložiště, když se funkce spouští místně, musíte si stáhnout nastavení aplikace do souboru Local. Settings. JSON. Spuštěním následujícího příkazu Azure Functions Core Tools Stáhněte nastavení do Local. Settings. JSON a nahraďte `<APP_NAME>` názvem vaší aplikace Function App z předchozího článku:

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

Možná se budete muset přihlásit ke svému účtu Azure.

> [!IMPORTANT]  
> Protože obsahuje tajné kódy, soubor Local. Settings. JSON se nikdy nepublikuje a měl by být vyloučený ze správy zdrojového kódu.

Potřebujete hodnotu `AzureWebJobsStorage`, což je připojovací řetězec účtu úložiště. Pomocí tohoto připojení ověříte, zda výstupní vazba funguje podle očekávání.
