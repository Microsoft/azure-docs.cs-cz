---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: d1931614356a313334d712713965346e843a403d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93425026"
---
## <a name="invoke-the-function-on-azure"></a>Vyvolání funkce v Azure

Vzhledem k tomu, že funkce používá Trigger HTTP, vyvoláte ji pomocí požadavku HTTP na adresu URL v prohlížeči nebo pomocí nástroje, jako je například kudrlinkou. 

# <a name="browser"></a>[Prohlížeč](#tab/browser)

Zkopírujte úplnou **adresu URL pro vyvolání** zobrazenou ve výstupu příkazu publikovat do adresního řádku prohlížeče a přidejte parametr dotazu `&name=Functions` . V prohlížeči by se měl zobrazit podobný výstup jako při spuštění funkce místně.

![Výstup funkce spuštěné v Azure v prohlížeči](./media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

# <a name="curl"></a>[curl](#tab/curl)

Spusťte [`curl`](https://curl.haxx.se/) s **adresou URL vyvolání** a přidejte parametr `&name=Functions` . Výstupem příkazu by měl být text "Hello".

![Výstup funkce spuštěný v Azure pomocí kudrlinkou](./media/functions-run-remote-azure-cli/function-test-cloud-curl.png)

---
