---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: e10de8093bf152b75cc6f262a142ff07c3d5b0d3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "72329591"
---
Už jste v Azure vytvořili aplikaci funkcí spolu s požadovaným účtem úložiště. Připojovací řetězec pro tento účet se bezpečně uchovává v nastavení aplikace v Azure. V tomto článku zapisujete zprávy do fronty úložiště ve stejném účtu. Chcete-li se připojit k účtu úložiště při místním spuštění funkce, je nutné stáhnout nastavení aplikace do souboru local.settings.json. 

V kořenovém adresáři projektu spusťte následující příkaz Nástroje jádra funkce Azure a `<APP_NAME>` stáhněte si nastavení na local.settings.json a nahraďte název aplikace funkce z předchozího článku:

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

Možná se budete muset přihlásit ke svému účtu Azure.

> [!IMPORTANT]  
> Tento příkaz přepíše všechna existující nastavení s hodnotami z vaší aplikace funkce v Azure.  
>
> Vzhledem k tomu, že obsahuje tajné klíče, soubor local.settings.json nikdy publikuje a měl by být vyloučen ze správy zdrojového kódu.  
> 

Potřebujete hodnotu `AzureWebJobsStorage`, což je připojovací řetězec účtu úložiště. Toto připojení slouží k ověření, že výstupní vazba funguje podle očekávání.
