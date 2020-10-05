---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/28/2020
ms.author: glenga
ms.openlocfilehash: 43da0ea4ddfc5410425465d436522523739218fe
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91408534"
---
## <a name="run-the-function-locally"></a>Místní spuštění funkce

Visual Studio Code se integruje s [nástroji Azure Functions Core](../articles/azure-functions/functions-run-local.md) , aby bylo možné spustit tento projekt na místním vývojovém počítači před publikováním do Azure.

1. Pro volání funkce stiskněte klávesu <kbd>F5</kbd> ke spuštění projektu Function App. Výstup z nástrojů Tools se zobrazí na panelu **Terminál**.

1. Pokud jste ještě nenainstalovali Azure Functions Core Tools, vyberte na příkazovém řádku **nainstalovat** . Po instalaci základních nástrojů se vaše aplikace spustí na panelu **terminálu** . Koncový bod adresy URL vámi spuštěné funkce aktivované protokolem HTTP se zobrazí v místním prostředí.

    ![Místní funkce VS Code výstupem](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Pokud běží základní nástroje, přejděte na následující adresu URL a spusťte požadavek GET, který zahrnuje `?name=Functions` řetězec dotazu.

    `http://localhost:7071/api/HttpExample?name=Functions`

1. Vrátí se odpověď, která v prohlížeči vypadá jako v následujících případech:

    ![Browser – ukázkový výstup pro localhost](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. Informace o žádosti se zobrazí na panelu **terminálu** .

    ![Spuštění hostitele úkolu – výstup VS Code terminálu](./media/functions-run-function-test-local-vs-code/function-execution-terminal.png)

1. Stisknutím <kbd>kombinace kláves CTRL + C</kbd> zastavte základní nástroje a odpojte ladicí program.
