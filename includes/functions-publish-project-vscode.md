---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 04/16/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: abb8b6bed6766ff0ea85eab1434014a057af4ca3
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68843171"
---
## <a name="publish-the-project-to-azure"></a>Publikování projektu do Azure

Visual Studio Code umožňuje publikovat projekt funkcí přímo do Azure. Během postupu vytvoříte aplikaci funkcí a související prostředky ve vašem předplatném Azure. Aplikace funkcí poskytuje kontext spuštění pro vaše funkce. Projekt se zabalí a nasadí do nové aplikace funkcí ve vašem předplatném Azure.

Ve výchozím nastavení Visual Studio Code vytvoří všechny prostředky Azure potřebné k vytvoření aplikace Function App. Názvy těchto prostředků jsou založené na zvoleném názvu aplikace Function App. Pokud potřebujete mít úplnou kontrolu nad vytvořenými prostředky, můžete místo toho [publikovat pomocí pokročilých možností](../articles/azure-functions/functions-develop-vs-code.md#enable-publishing-with-advanced-create-options).

V této části se předpokládá, že vytváříte novou aplikaci Function App v Azure.

> [!IMPORTANT]
> Publikování do existující aplikace funkcí přepíše obsah této aplikace v Azure.

1. V Visual Studio Code stisknutím klávesy F1 otevřete paletu příkazů. V paletě příkazů vyhledejte a vyberte `Azure Functions: Deploy to function app...`.

1. Pokud není přihlášený, zobrazí se výzva, abyste se přihlásili **k Azure**. Můžete si také **vytvořit bezplatný účet Azure**. Po úspěšném přihlášení z prohlížeče se vraťte na Visual Studio Code. 

1. Pokud máte více předplatných, **Vyberte předplatné** pro aplikaci Function App a pak zvolte **+ vytvořit novou Function App v Azure**.

1. Zadejte globálně jedinečný název, který identifikuje vaši aplikaci funkcí, a stiskněte klávesu Enter. Platné znaky pro název aplikace funkcí jsou `a-z`, `0-9` a `-`.

    Po stisknutí klávesy ENTER se ve vašem předplatném vytvoří následující prostředky Azure:

    * **[Skupina prostředků](../articles/azure-resource-manager/resource-group-overview.md)** : Obsahuje všechny vytvořené prostředky Azure. Název je založen na názvu vaší aplikace Function App.
    * **[Účet úložiště](../articles/storage/common/storage-quickstart-create-account.md)** : Účet úložiště úrovně Standard se vytvoří s jedinečným názvem, který je založený na názvu vaší aplikace Function App.
    * **[Plán hostování](../articles/azure-functions/functions-scale.md)** : Plán spotřeby se vytvoří v oblasti Západní USA pro hostování aplikace Function bez serveru.
    * **Aplikace Function App**: Projekt se nasadí do a spustí se v této nové aplikaci Function App.

    Po vytvoření aplikace funkcí a použití balíčku nasazení se zobrazí oznámení. Pokud chcete zobrazit výsledky vytvoření a nasazení, včetně prostředků Azure, které jste vytvořili, vyberte **Zobrazit výstup** v tomto oznámení.

1. Zpět v **Azure: Oblast** funkce, rozbalte novou aplikaci Function App v rámci vašeho předplatného. Rozbalte **funkce**, klikněte pravým tlačítkem na **HttpTrigger**a pak zvolte **Kopírovat adresu URL funkce**.

    ![Zkopírujte adresu URL funkce pro nový Trigger HTTP.](./media/functions-publish-project-vscode/function-copy-endpoint-url.png)
