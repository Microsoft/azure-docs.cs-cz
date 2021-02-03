---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2021
ms.author: glenga
ms.openlocfilehash: 4b15fec0f22db740bbd7c24fcc0acf2ad1a2d1cd
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493450"
---
## <a name="run-the-function-in-azure"></a>Spuštění funkce v Azure

1. Zpátky v oblasti **Azure: Functions (funkce** ) na bočním panelu rozbalte **místní**  >  **funkce** projektu. Klikněte pravým tlačítkem myši (Windows) nebo <kbd>stiskněte klávesu CTRL</kbd> (MacOS) `HttpExample` a vyberte možnost **Spustit funkci nyní...**.

    :::image type="content" source="media/functions-vs-code-run-remote/execute-function-now.png" alt-text="Spustit nyní funkci v Azure z Visual Studio Code":::

1. V části **Zadejte text žádosti** se zobrazí hodnota tělo zprávy žádosti `{ "name": "Azure" }` . Stisknutím klávesy ENTER odešlete tuto zprávu požadavku vaší funkci.  

1. Když se funkce spustí v Azure a vrátí odpověď, vygeneruje se oznámení v Visual Studio Code.
