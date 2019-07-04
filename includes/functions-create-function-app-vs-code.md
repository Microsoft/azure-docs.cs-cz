---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/27/2018
ms.author: glenga
ms.openlocfilehash: f9b853f0e86fc298ab35421928492ba813d85827
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444633"
---
## <a name="create-an-azure-functions-project"></a>Vytvoření projektu funkce pomocí funkcí. 

Šablona projektu Azure Functions ve Visual Studio Code vytvoří projekt, který jde publikovat do aplikace funkcí v Azure. Aplikace funkcí umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování a sdílení prostředků.

1. Ve Visual Studio Code stisknutím klávesy F1 otevřete paletu příkazů. V paletu příkazů, vyhledejte a vyberte `Azure Functions: Create new project...`.

1. Zvolte umístění adresáře pro váš pracovní prostor projektu a zvolte **vyberte**.

    > [!NOTE]
    > Tyto kroky byly navrženy k dokončení mimo pracovní prostor. V tomto případě nevybírejte složku projektu, která je součástí pracovního prostoru.

1. Po vyzvání zadejte následující informace:

    | Výzva | Hodnota | Popis |
    | ------ | ----- | ----------- |
    | Vyberte jazyk pro váš projekt aplikace funkcí | C# nebo JavaScript | Tento článek podporuje C# a JavaScript. Python, naleznete v tématu [v tomto článku Python](https://code.visualstudio.com/docs/python/tutorial-azure-functions)a prostředí PowerShell, naleznete v tématu [Tento článek k prostředí PowerShell](../articles/azure-functions/functions-create-first-function-powershell.md).  |
    | Vyberte šablonu pro váš projekt první funkce | Trigger HTTP | Vytvoření funkce aktivované protokolem HTTP do nové aplikace function App. |
    | Zadejte název funkce | HttpTrigger | Použít výchozí název a stisknutím klávesy Enter. |
    | Zadejte obor názvů | My.Functions | (C# jenom) C# knihovny tříd musí obsahovat obor názvů.  |
    | Úroveň autorizace | Funkce | Vyžaduje [klíč funkce](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys) k volání koncového bodu protokolu HTTP funkce. |
    | Vyberte, jak byste chtěli otevření projektu | Přidat do pracovního prostoru | Vytvoří aplikaci funkcí v aktuálním pracovním prostoru. |

Visual Studio Code vytvoří projekt aplikace funkcí v novém pracovním prostoru. Tento projekt obsahuje konfigurační soubory [host.json](../articles/azure-functions/functions-host-json.md) a [local.settings.json](../articles/azure-functions/functions-run-local.md#local-settings-file) a navíc všechny soubory projektu pro konkrétní jazyk. 

Ve složce HttpTrigger projektu aplikace funkcí se vytvoří také novou funkci aktivovanou protokolem HTTP.