---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: e10de8093bf152b75cc6f262a142ff07c3d5b0d3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "72329591"
---
Aplikaci Function App jste už vytvořili v Azure společně s požadovaným účtem úložiště. Připojovací řetězec pro tento účet je bezpečně uložený v nastavení aplikace v Azure. V tomto článku napíšete zprávy do fronty úložiště ve stejném účtu. Pokud se chcete připojit k účtu úložiště, když se funkce spouští místně, musíte si stáhnout nastavení aplikace do souboru Local. Settings. JSON. 

Z kořenového adresáře projektu spusťte následující Azure Functions Core Tools příkaz pro stažení nastavení do Local. Settings. JSON a nahrazení `<APP_NAME>` názvem vaší aplikace Function App z předchozího článku:

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

Možná se budete muset přihlásit ke svému účtu Azure.

> [!IMPORTANT]  
> Tento příkaz přepíše všechna existující nastavení hodnotami z vaší aplikace Function App v Azure.  
>
> Protože obsahuje tajné kódy, soubor Local. Settings. JSON se nikdy nepublikuje a měl by být vyloučený ze správy zdrojového kódu.  
> 

Potřebujete hodnotu `AzureWebJobsStorage`, což je připojovací řetězec účtu úložiště. Pomocí tohoto připojení ověříte, zda výstupní vazba funguje podle očekávání.
