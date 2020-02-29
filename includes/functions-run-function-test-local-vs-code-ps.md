---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/19/2020
ms.author: glenga
ms.openlocfilehash: edf5fc33ec14d41630462cca1a4ace0663473196
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/29/2020
ms.locfileid: "78191007"
---
## <a name="run-the-function-locally"></a>Místní spuštění funkce

Azure Functions Core Tools se integruje s Visual Studio Code, aby se mohl spustit a ladit Azure Functions projektu místně. Podrobnosti o tom, jak ladit v Visual Studio Code, najdete v tématu [ladění powershellu Azure Functions místně](../articles/azure-functions/functions-debug-powershell-local.md). 

1. Stisknutím klávesy F5 spusťte projekt Function App. Výstup z nástrojů Tools se zobrazí na panelu **Terminál**.

1. Na panelu **Terminál** zkopírujte adresu URL koncového bodu vaší funkce aktivované protokolem HTTP.

    ![Místní výstup Azure](./media/functions-run-function-test-local-vs-code-ps/functions-vscode-f5.png)

1. Připojením řetězce dotazu `?name=<yourname>` k této adrese URL a potom pomocí `Invoke-RestMethod` v druhém příkazovém řádku prostředí PowerShell spusťte požadavek následujícím způsobem:

    ```powershell
    PS > Invoke-RestMethod -Method Get -Uri http://localhost:7071/api/HttpTrigger?name=PowerShell
    Hello PowerShell
    ```

    Požadavek GET můžete také spustit z prohlížeče z následující adresy URL:

    <http://localhost:7071/api/HttpExample?name=PowerShell>

    Když zavoláte koncový bod HttpTrigger bez předání parametru `name` buď jako parametr dotazu, nebo v těle, funkce vrátí chybu `BadRequest`. Když zkontrolujete kód v běhu. ps1, uvidíte, že k této chybě dochází podle návrhu.

1. Informace o žádosti se zobrazí na panelu **terminálu** .

    ![Spuštění funkce na panelu terminálu](./media/functions-run-function-test-local-vs-code-ps/function-execution-terminal.png)

1. Po dokončení stiskněte klávesy **CTRL + C** a zastavte základní nástroje.

Po ověření správného fungování funkce na místním počítači je na čase publikovat projekt do Azure.