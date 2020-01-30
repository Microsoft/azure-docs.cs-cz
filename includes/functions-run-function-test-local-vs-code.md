---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: f4075b8d05c179e8115ff46c9f82751817372491
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842120"
---
## <a name="run-the-function-locally"></a>Místní spuštění funkce

Visual Studio Code se integruje s [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md) a umožní vám spustit tento projekt na místním vývojovém počítači před publikováním do Azure.

1. Pro volání funkce stiskněte klávesu F5 ke spuštění projektu Function App. Výstup z nástrojů Tools se zobrazí na panelu **Terminál**.

1. Pokud jste ještě nenainstalovali Azure Functions Core Tools, vyberte na příkazovém řádku **nainstalovat** . Po instalaci základních nástrojů se vaše aplikace spustí na panelu **terminálu** .

1. Na panelu **Terminál** zkopírujte adresu URL koncového bodu vaší funkce aktivované protokolem HTTP. 

    ![Místní výstup Azure](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Vložte adresu URL pro požadavek HTTP do panelu adresy prohlížeče. Připojí řetězec dotazu `?name=<yourname>` k této adrese URL a provede požadavek GET. 

1. Vrátí se odpověď, která v prohlížeči vypadá jako v následujících případech:

    ![Odezva místního hostitele funkce v prohlížeči](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. Stisknutím Shift + F5 zastavíte základní nástroje a odpojíte ladicí program.
