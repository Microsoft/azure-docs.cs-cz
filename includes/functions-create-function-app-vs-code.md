---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/27/2018
ms.author: glenga
ms.openlocfilehash: 79dbee33928fbc7560d0ea27be3af25cc510e996
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52638659"
---
## <a name="create-an-azure-functions-project"></a>Vytvoření projektu Azure Functions

Šablona projektu Azure Functions ve Visual Studio Code vytvoří projekt, který jde publikovat do aplikace funkcí v Azure. Aplikace funkcí umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování a sdílení prostředků.

1. Ve Visual Studio Code vyberte logo Azure, aby se zobrazila oblast **Azure: Functions**, pak vyberte ikonu Vytvořit nový projekt.

    ![Vytvoření projektu aplikace funkcí](./media/functions-create-function-app-vs-code/create-function-app-project.png)

1. Vyberte umístění pro pracovní prostor vašeho projektu a zvolte **Vybrat**.

    > [!NOTE]
    > Tento článek je navržený k dokončení mimo pracovní prostor. V tomto případě nevybírejte složku projektu, která je součástí pracovního prostoru.

1. Vyberte jazyk pro váš projekt aplikace funkcí. V tomto článku se používá jazyk JavaScript.
    ![Zvolit jazyk projektu](./media/functions-create-function-app-vs-code/create-function-app-project-language.png)

1. Po zobrazení výzvy zvolte **Přidat do pracovního prostoru**.

Visual Studio Code vytvoří projekt aplikace funkcí v novém pracovním prostoru. Tento projekt obsahuje konfigurační soubory [host.json](../articles/azure-functions/functions-host-json.md) a [local.settings.json](../articles/azure-functions/functions-run-local.md#local-settings-file) a navíc všechny soubory projektu pro konkrétní jazyk. Získáte také nové úložiště Git ve složce projektu.