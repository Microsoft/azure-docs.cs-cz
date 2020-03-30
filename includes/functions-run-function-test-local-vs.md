---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/06/2020
ms.author: glenga
ms.openlocfilehash: b4b2409928b6a4196738c7cc6c7040e781d34686
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80056666"
---
1. Chcete-li spustit funkci, stiskněte klávesu F5 v sadě Visual Studio. Možná budete muset povolit výjimku brány firewall, aby nástroje mohly zpracovávat požadavky HTTP. Úrovně autorizace jsou nikdy vynuceny při spuštění funkce místně.

2. Zkopírujte adresu URL vaší funkce z výstupu modulu runtime služby Azure Functions.

    ![Místní modul runtime Azure](./media/functions-run-function-test-local-vs/functions-debug-local-vs.png)

3. Vložte adresu URL pro požadavek HTTP do panelu adresy prohlížeče. Přidejte řetězec `?name=<YOUR_NAME>` dotazu k této adrese URL a spusťte požadavek. Následující obrázek znázorňuje odpověď v prohlížeči na místní požadavek GET vrácený funkcí: 

    ![Odezva místního hostitele funkce v prohlížeči](./media/functions-run-function-test-local-vs/functions-run-browser-local-vs.png)

4. Ladění můžete ukončit stisknutím shift+F5 v sadě Visual Studio.