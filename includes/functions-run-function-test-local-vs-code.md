---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: f4af3c202d4f00c4ac3041921175c92226f0db7c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "76964077"
---
## <a name="run-the-function-locally"></a>Místní spuštění funkce

Visual Studio Code se integruje s [nástroji Azure Functions Core Tools,](../articles/azure-functions/functions-run-local.md) které vám umožní spustit tento projekt na místním vývojovém počítači před publikováním do Azure.

1. Chcete-li volat svou funkci, spusťte spuštění projektu aplikace funkce stisknutím klávesy F5. Výstup z nástrojů Tools se zobrazí na panelu **Terminál**.

1. Pokud jste ještě nenainstalovali nástroje Azure Functions Core Tools, vyberte **nainstalovat** na výzvu. Když jsou základní nástroje nainstalovány, aplikace se spustí na panelu **Terminál.** Můžete vidět koncový bod adresy URL funkce aktivované http spuštěna místně. 

    ![Místní výstup Azure](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Při spuštění nástroje Core Tools přejděte na následující adresu `?name=Functions` URL a spusťte požadavek GET, který obsahuje řetězec dotazu.

    <http://localhost:7071/api/HttpExample?name=Functions>

1. Je vrácena odpověď, která vypadá takto v prohlížeči:

    ![Odezva místního hostitele funkce v prohlížeči](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. Informace o požadavku jsou zobrazeny v panelu **Terminálu.**

    ![Spuštění funkce v terminálovém panelu](./media/functions-run-function-test-local-vs-code/function-execution-terminal.png)

1. Stisknutím kláves Ctrl + C zastavíte základní nástroje a odpojte ladicí program.
