---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 04/16/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: ec0425ff2188ecf1816d5f5841394c8e32f301d2
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64744036"
---
## <a name="publish-the-project-to-azure"></a>Publikování projektu do Azure

Visual Studio Code umožňuje publikovat projekt funkcí přímo do Azure. Během postupu vytvoříte aplikaci funkcí a související prostředky ve vašem předplatném Azure. Aplikace funkcí poskytuje kontext spuštění pro vaše funkce. Projekt se zabalí a nasadí do nové aplikace funkcí ve vašem předplatném Azure.

Tento článek předpokládá, že vytváříte novou aplikaci funkcí. 

> [!IMPORTANT]
> Publikování do existující aplikace funkcí přepíše obsah této aplikace v Azure.

1. V **Azure: Funkce** oblasti, vyberte nasazení na ikonu aplikace Function App.

    ![Nastavení aplikace funkcí](./media/functions-publish-project-vscode/function-app-publish-project.png)

1. Pokud nejsou přihlášeni, zobrazí se výzva k **přihlášení k Azure**. Můžete také **vytvořit si bezplatný účet Azure**. Po úspěšném přihlášení z prohlížeče přejděte zpět do Visual Studio Code. 

1. Pokud máte více předplatných, **vybrat odběr, který** pro aplikaci function app, klikněte na tlačítko **+ vytvořit novou aplikaci Function App v Azure**.

1. Zadejte globálně jedinečný název, který identifikuje vaši aplikaci funkcí, a stiskněte klávesu Enter. Platné znaky pro název aplikace funkcí jsou `a-z`, `0-9` a `-`.

1. Zvolte **+ Vytvořit novou skupinu prostředků**, zadejte název skupiny prostředků, například `myResourceGroup`, a stiskněte klávesu Enter. Můžete také použít existující skupinu prostředků.

1. Zvolte **+ vytvořit nový účet úložiště**, typ globálně jedinečný název pro nový účet úložiště používá vaše aplikace function app a potom stiskněte klávesu Enter. Názvy účtů úložiště musí mít délku 3 až 24 znaků a můžou obsahovat jenom číslice a malá písmena. Můžete taky použít existující účet.

1. Zvolte umístění v [oblasti](https://azure.microsoft.com/regions/) ve své blízkosti nebo v blízkosti jiných služeb, které vaše funkce využívají.

    Při stisknutí klávesy Enter, vytvoří následující prostředky Azure v rámci vašeho předplatného:

    * **[Skupina prostředků](../articles/azure-resource-manager/resource-group-overview.md)**: Obsahuje všechny vytvořené prostředky Azure. Název je založené na název vaší aplikace funkcí.
    * **[Účet úložiště](../articles/storage/common/storage-quickstart-create-account.md)**: Standardní účet úložiště se vytvoří s jedinečným názvem, který je založen na název vaší aplikace funkcí.
    * **[Plán hostování](../articles/azure-functions/functions-scale.md)**: Plán consumption se vytvoří v oblasti USA – západ hostovat vaše function app bez serveru.
    * **Aplikace Function app**: Váš projekt nasazené a běží v této nové aplikace function app.

    Po vytvoření aplikace funkcí a použití balíčku nasazení se zobrazí oznámení. Vyberte **zobrazení výstupu** toto oznámení zobrazíte vytváření a výsledky nasazení, včetně Azure prostředky, které jste vytvořili.

1. Zpátky **Azure: Funkce** oblasti, rozbalte novou aplikaci function app v rámci vašeho předplatného. Rozbalte **funkce**, klikněte pravým tlačítkem na **HttpTrigger**a klikněte na tlačítko **zkopírujte adresu URL funkce**.

    ![Zkopírujte adresu URL funkce pro novou aktivační událost HTTP](./media/functions-publish-project-vscode/function-copy-endpoint-url.png)
