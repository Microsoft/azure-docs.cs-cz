---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/19/2020
ms.author: glenga
ms.openlocfilehash: edf5fc33ec14d41630462cca1a4ace0663473196
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78191007"
---
## <a name="run-the-function-locally"></a>Místní spuštění funkce

Nástroje Azure Functions Core se integrují s visual studioovým kódem, který vám umožní spouštět a ladit projekt Azure Functions místně. Podrobnosti o ladění v kódu Visual Studia najdete v [tématu ladění powershellových funkcí Azure místně](../articles/azure-functions/functions-debug-powershell-local.md). 

1. Stisknutím klávesy F5 spusťte projekt aplikace funkce. Výstup z nástrojů Tools se zobrazí na panelu **Terminál**.

1. Na panelu **Terminál** zkopírujte adresu URL koncového bodu vaší funkce aktivované protokolem HTTP.

    ![Místní výstup Azure](./media/functions-run-function-test-local-vs-code-ps/functions-vscode-f5.png)

1. Přidejte řetězec `?name=<yourname>` dotazu k této `Invoke-RestMethod` adrese URL a potom použijte druhý příkazový řádek prostředí PowerShell k provedení požadavku následujícím způsobem:

    ```powershell
    PS > Invoke-RestMethod -Method Get -Uri http://localhost:7071/api/HttpTrigger?name=PowerShell
    Hello PowerShell
    ```

    Požadavek GET můžete také provést z prohlížeče z následující adresy URL:

    <http://localhost:7071/api/HttpExample?name=PowerShell>

    Při volání koncového bodu HttpTrigger `name` bez předání parametru buď jako parametr dotazu nebo v těle, funkce vrátí `BadRequest` chybu. Při kontrole kódu v run.ps1, uvidíte, že k této chybě dochází podle návrhu.

1. Informace o požadavku jsou zobrazeny v panelu **Terminálu.**

    ![Spuštění funkce v terminálovém panelu](./media/functions-run-function-test-local-vs-code-ps/function-execution-terminal.png)

1. Až po dokončení, stiskněte **Ctrl + C** zastavit základní nástroje.

Po ověření správného fungování funkce na místním počítači je na čase publikovat projekt do Azure.