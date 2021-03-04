---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/06/2020
ms.author: glenga
ms.openlocfilehash: bff2f05a95faf9c475189cb5a8003cb7fd9f69be
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101701384"
---
1. Chcete-li spustit funkci, stiskněte klávesu <kbd>F5</kbd> v aplikaci Visual Studio. Možná budete muset povolit výjimku brány firewall, aby nástroje mohly zpracovávat požadavky HTTP. Při místním spuštění funkce se nikdy neuplatní úrovně autorizace.

2. Zkopírujte adresu URL vaší funkce z výstupu modulu runtime služby Azure Functions.

    ![Místní modul runtime Azure](./media/functions-run-function-test-local-vs/functions-debug-local-vs.png)

3. Vložte adresu URL pro požadavek HTTP do panelu adresy prohlížeče. Připojí řetězec dotazu `?name=<YOUR_NAME>` k této adrese URL a spustí žádost. Následující obrázek ukazuje odpověď v prohlížeči na místní požadavek GET vrácený funkcí: 

    ![Odezva místního hostitele funkce v prohlížeči](./media/functions-run-function-test-local-vs/functions-run-browser-local-vs.png)

4. Pokud chcete zastavit ladění, stiskněte <kbd>SHIFT</kbd> + <kbd>F5</kbd> v aplikaci Visual Studio.
