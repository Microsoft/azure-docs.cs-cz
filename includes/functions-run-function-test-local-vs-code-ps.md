---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/16/2020
ms.author: glenga
ms.openlocfilehash: c54145cf48912d3911a39e681d85cb6907be8e52
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842130"
---
## <a name="run-the-function-locally"></a>Místní spuštění funkce

Azure Functions Core Tools se integruje s Visual Studio Code, aby se mohl spustit a ladit Azure Functions projektu místně.  

1. Chcete-li ladit funkci, vložte volání rutiny [`Wait-Debugger`](/powershell/module/microsoft.powershell.utility/wait-debugger?view=powershell-6) v kódu funkce předtím, než budete chtít připojit ladicí program, a stisknutím klávesy F5 spusťte projekt Function App a připojte ladicí program. Výstup z nástrojů Tools se zobrazí na panelu **Terminál**.

1. Na panelu **Terminál** zkopírujte adresu URL koncového bodu vaší funkce aktivované protokolem HTTP.

    ![Místní výstup Azure](./media/functions-run-function-test-local-vs-code-ps/functions-vscode-f5.png)

1. Připojením řetězce dotazu `?name=<yourname>` k této adrese URL a potom pomocí `Invoke-RestMethod` v druhém příkazovém řádku prostředí PowerShell spusťte požadavek následujícím způsobem:

    ```powershell
    PS > Invoke-RestMethod -Method Get -Uri http://localhost:7071/api/HttpTrigger?name=PowerShell
    Hello PowerShell
    ```

    Požadavek GET můžete také spustit z prohlížeče.

    Když zavoláte koncový bod HttpTrigger bez předání parametru `name` buď jako parametr dotazu, nebo v těle, funkce vrátí chybu [HttpStatusCode]:: důvodu chybného požadavku. Když zkontrolujete kód v běhu. ps1, uvidíte, že k této chybě dochází podle návrhu.

1. Pokud chcete zastavit ladění, stiskněte Shift + F5.

Po ověření správného fungování funkce na místním počítači je na čase publikovat projekt do Azure.

> [!NOTE]
> Před publikováním funkcí do Azure nezapomeňte odebrat všechna volání `Wait-Debugger`. 