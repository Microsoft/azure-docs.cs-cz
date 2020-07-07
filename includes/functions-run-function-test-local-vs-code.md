---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: f4af3c202d4f00c4ac3041921175c92226f0db7c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "76964077"
---
## <a name="run-the-function-locally"></a>Místní spuštění funkce

Visual Studio Code se integruje s [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md) a umožní vám spustit tento projekt na místním vývojovém počítači před publikováním do Azure.

1. Pro volání funkce stiskněte klávesu F5 ke spuštění projektu Function App. Výstup z nástrojů Tools se zobrazí na panelu **Terminál**.

1. Pokud jste ještě nenainstalovali Azure Functions Core Tools, vyberte na příkazovém řádku **nainstalovat** . Po instalaci základních nástrojů se vaše aplikace spustí na panelu **terminálu** . Koncový bod adresy URL vámi spuštěné funkce aktivované protokolem HTTP se zobrazí v místním prostředí. 

    ![Místní výstup Azure](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Pokud běží základní nástroje, přejděte na následující adresu URL a spusťte požadavek GET, který zahrnuje `?name=Functions` řetězec dotazu.

    <http://localhost:7071/api/HttpExample?name=Functions>

1. Vrátí se odpověď, která v prohlížeči vypadá jako v následujících případech:

    ![Odezva místního hostitele funkce v prohlížeči](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. Informace o žádosti se zobrazí na panelu **terminálu** .

    ![Spuštění funkce na panelu terminálu](./media/functions-run-function-test-local-vs-code/function-execution-terminal.png)

1. Stisknutím kombinace kláves CTRL + C zastavte základní nástroje a odpojte ladicí program.
