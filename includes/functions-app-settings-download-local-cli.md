---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: e10de8093bf152b75cc6f262a142ff07c3d5b0d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "72329591"
---
Aplikaci Function App jste už vytvořili v Azure společně s požadovaným účtem úložiště. Připojovací řetězec pro tento účet je bezpečně uložený v nastavení aplikace v Azure. V tomto článku napíšete zprávy do fronty úložiště ve stejném účtu. Pokud se chcete připojit k účtu úložiště, když se funkce spouští místně, musíte si stáhnout nastavení aplikace do local.settings.jsv souboru. 

V kořenovém adresáři projektu spusťte následující příkaz Azure Functions Core Tools pro stažení nastavení local.settings.jsv systému nahraďte `<APP_NAME>` názvem vaší aplikace Function App z předchozího článku:

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

Možná se budete muset přihlásit ke svému účtu Azure.

> [!IMPORTANT]  
> Tento příkaz přepíše všechna existující nastavení hodnotami z vaší aplikace Function App v Azure.  
>
> Vzhledem k tomu, že obsahuje tajné kódy, local.settings.jsv souboru nikdy nezveřejňuje a měla by být vyloučena ze správy zdrojového kódu.  
> 

Potřebujete hodnotu `AzureWebJobsStorage` , což je připojovací řetězec účtu úložiště. Pomocí tohoto připojení ověříte, zda výstupní vazba funguje podle očekávání.
