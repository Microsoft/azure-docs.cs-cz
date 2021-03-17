---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/19/2020
ms.author: glenga
ms.openlocfilehash: a5c113849296275432acf1f5603377a1909a2c04
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2020
ms.locfileid: "96842256"
---
## <a name="run-the-function-locally"></a>Místní spuštění funkce

Azure Functions Core Tools se integruje s Visual Studio Code, aby se mohl spustit a ladit Azure Functions projektu místně. Podrobnosti o tom, jak ladit v Visual Studio Code, najdete v tématu [ladění powershellu Azure Functions místně](../articles/azure-functions/functions-debug-powershell-local.md). 
1. Pro volání funkce stiskněte klávesu <kbd>F5</kbd> ke spuštění projektu Function App. Výstup z nástrojů Tools se zobrazí na panelu **Terminál**. Pokud máte v systému Windows potíže, ujistěte se, že výchozí terminál pro Visual Studio Code není nastaven na **WSL bash**.

1. Na panelu **Terminál** zkopírujte adresu URL koncového bodu vaší funkce aktivované protokolem HTTP.

    ![Místní výstup Azure](./media/functions-run-function-test-local-vs-code-ps/functions-vscode-f5.png)

1. Přidejte řetězec dotazu `?name=<yourname>` k této adrese URL a pak pomocí `Invoke-RestMethod` příkazu ve druhém příkazovém řádku prostředí PowerShell spusťte požadavek následujícím způsobem:

    ```powershell
    PS > Invoke-RestMethod -Method Get -Uri http://localhost:7071/api/HttpTrigger?name=PowerShell
    Hello PowerShell
    ```

    Požadavek GET můžete také spustit z prohlížeče z následující adresy URL:

    `http://localhost:7071/api/HttpExample?name=PowerShell`

    Když zavoláte koncový bod HttpTrigger bez předání `name` parametru buď jako parametr dotazu, nebo v těle, funkce vrátí `BadRequest` chybu. Při revizi kódu v run.ps1 vidíte, že k této chybě dochází v rámci návrhu.

1. Informace o žádosti se zobrazí na panelu **terminálu** .

    ![Spuštění funkce na panelu terminálu](./media/functions-run-function-test-local-vs-code-ps/function-execution-terminal.png)

1. Po dokončení stiskněte klávesy **CTRL + C** a zastavte základní nástroje.

Po ověření správného fungování funkce na místním počítači je na čase publikovat projekt do Azure.
