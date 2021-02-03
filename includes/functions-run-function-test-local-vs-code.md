---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2021
ms.author: glenga
ms.openlocfilehash: 1a0521f76a2cf986f7036d1f701a40a156d16ee7
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493447"
---
## <a name="run-the-function-locally"></a>Místní spuštění funkce

Visual Studio Code se integruje s [nástroji Azure Functions Core](../articles/azure-functions/functions-run-local.md) , aby bylo možné spustit tento projekt na místním vývojovém počítači před publikováním do Azure.

1. Pro volání funkce stiskněte klávesu <kbd>F5</kbd> ke spuštění projektu Function App. Výstup z nástrojů Tools se zobrazí na panelu **Terminál**. Vaše aplikace se spustí na panelu **terminálu** . Koncový bod adresy URL vámi spuštěné funkce aktivované protokolem HTTP se zobrazí v místním prostředí.

    ![Místní funkce VS Code výstupem](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

    Pokud máte v systému Windows potíže, ujistěte se, že výchozí terminál pro Visual Studio Code není nastaven na **WSL bash**.

1. Se základními nástroji, které běží, přejdete do oblasti **Azure: Functions** . V části **funkce** rozbalte **místní**  >  **funkce** projektu. Klikněte pravým tlačítkem myši (Windows) nebo <kbd>stiskněte klávesu CTRL</kbd> (MacOS) `HttpExample` a vyberte možnost **Spustit funkci nyní...**.

    :::image type="content" source="media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="Spustit funkci hned z Visual Studio Code":::

1. V části **Zadejte text žádosti** se zobrazí hodnota tělo zprávy žádosti `{ "name": "Azure" }` . Stisknutím klávesy ENTER odešlete tuto zprávu požadavku vaší funkci.  

1. Když se funkce spustí místně a vrátí odpověď, vygeneruje se oznámení v Visual Studio Code. Informace o spuštění funkce se zobrazí na panelu **terminálu** .

1. Stisknutím <kbd>kombinace kláves CTRL + C</kbd> zastavte základní nástroje a odpojte ladicí program.
