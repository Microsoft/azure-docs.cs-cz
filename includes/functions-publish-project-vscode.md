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
ms.openlocfilehash: 3cfa36331f8f4ad45f3bf8ff32eee7d89c7d8852
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67608135"
---
## <a name="publish-the-project-to-azure"></a>Publikování projektu do Azure

Visual Studio Code umožňuje publikovat projekt funkcí přímo do Azure. Během postupu vytvoříte aplikaci funkcí a související prostředky ve vašem předplatném Azure. Aplikace funkcí poskytuje kontext spuštění pro vaše funkce. Projekt se zabalí a nasadí do nové aplikace funkcí ve vašem předplatném Azure.

Ve výchozím nastavení Visual Studio vytvoří všechny prostředky Azure potřebné k vytvoření aplikace function app. Názvy těchto prostředků jsou založeny na název aplikace funkcí, které zvolíte. Pokud potřebujete mít úplnou kontrolu nad vytvořené prostředky, můžete místo toho [publikovat pomocí rozšířené možnosti](../articles/azure-functions/functions-develop-vs-code.md#enabled-publishing-with-advanced-create-options).

V této části se předpokládá, že vytváříte novou aplikaci function app v Azure.

> [!IMPORTANT]
> Publikování do existující aplikace funkcí přepíše obsah této aplikace v Azure.

1. Ve Visual Studio Code stisknutím klávesy F1 otevřete paletu příkazů. V paletu příkazů, vyhledejte a vyberte `Azure Functions: Deploy to function app...`.

1. Pokud nejsou přihlášeni, zobrazí se výzva k **přihlášení k Azure**. Můžete také **vytvořit si bezplatný účet Azure**. Po úspěšném přihlášení z prohlížeče přejděte zpět do Visual Studio Code. 

1. Pokud máte více předplatných, **vybrat odběr, který** pro aplikaci function app, klikněte na tlačítko **+ vytvořit novou aplikaci Function App v Azure**.

1. Zadejte globálně jedinečný název, který identifikuje vaši aplikaci funkcí, a stiskněte klávesu Enter. Platné znaky pro název aplikace funkcí jsou `a-z`, `0-9` a `-`.

    Při stisknutí klávesy Enter, vytvoří následující prostředky Azure v rámci vašeho předplatného:

    * **[Skupina prostředků](../articles/azure-resource-manager/resource-group-overview.md)** : Obsahuje všechny vytvořené prostředky Azure. Název je založené na název vaší aplikace funkcí.
    * **[Účet úložiště](../articles/storage/common/storage-quickstart-create-account.md)** : Standardní účet úložiště se vytvoří s jedinečným názvem, který je založen na název vaší aplikace funkcí.
    * **[Plán hostování](../articles/azure-functions/functions-scale.md)** : Plán consumption se vytvoří v oblasti USA – západ hostovat vaše function app bez serveru.
    * **Aplikace Function app**: Váš projekt nasazené a běží v této nové aplikace function app.

    Po vytvoření aplikace funkcí a použití balíčku nasazení se zobrazí oznámení. Vyberte **zobrazení výstupu** toto oznámení zobrazíte vytváření a výsledky nasazení, včetně Azure prostředky, které jste vytvořili.

1. Zpátky **Azure: Funkce** oblasti, rozbalte novou aplikaci function app v rámci vašeho předplatného. Rozbalte **funkce**, klikněte pravým tlačítkem na **HttpTrigger**a klikněte na tlačítko **zkopírujte adresu URL funkce**.

    ![Zkopírujte adresu URL funkce pro novou aktivační událost HTTP](./media/functions-publish-project-vscode/function-copy-endpoint-url.png)
