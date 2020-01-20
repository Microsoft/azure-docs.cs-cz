---
title: zahrnout soubor
description: zahrnout soubor
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/16/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 30a6d8556a251ba76dff77e004fb864f3eaf04cf
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279528"
---
## <a name="publish-the-project-to-azure"></a>Publikování projektu do Azure

Visual Studio Code umožňuje publikovat projekt funkcí přímo do Azure. Během postupu vytvoříte aplikaci funkcí a související prostředky ve vašem předplatném Azure. Aplikace funkcí poskytuje kontext spuštění pro vaše funkce. Projekt se zabalí a nasadí do nové aplikace funkcí ve vašem předplatném Azure.

Ve výchozím nastavení Visual Studio Code vytvoří všechny prostředky Azure potřebné k vytvoření aplikace Function App. Názvy těchto prostředků jsou založené na zvoleném názvu aplikace Function App. Pokud potřebujete mít úplnou kontrolu nad vytvořenými prostředky, můžete místo toho [publikovat pomocí pokročilých možností](../articles/azure-functions/functions-develop-vs-code.md#enable-publishing-with-advanced-create-options).

V této části se předpokládá, že vytváříte novou aplikaci Function App v Azure.

> [!IMPORTANT]
> Publikování do existující aplikace funkcí přepíše obsah této aplikace v Azure.

1. V Visual Studio Code stisknutím klávesy F1 otevřete paletu příkazů. V paletě příkazů vyhledejte a vyberte `Azure Functions: Deploy to function app...`.

1. Pokud není přihlášený, zobrazí se výzva, abyste se **přihlásili k Azure**. Můžete si také **vytvořit bezplatný účet Azure**. Po úspěšném přihlášení z prohlížeče se vraťte na Visual Studio Code. 

1. Pokud máte více předplatných, **Vyberte předplatné** pro aplikaci Function App a pak zvolte **+ vytvořit novou Function App v Azure**.

1. Zadejte globálně jedinečný název, který identifikuje vaši aplikaci funkcí, a stiskněte klávesu Enter. Platné znaky pro název aplikace funkcí jsou `a-z`, `0-9` a `-`.

    Po stisknutí klávesy ENTER se ve vašem předplatném vytvoří následující prostředky Azure:

    * **[Skupina prostředků](../articles/azure-resource-manager/management/overview.md)** : obsahuje všechny vytvořené prostředky Azure. Název je založen na názvu vaší aplikace Function App.
    * **[Účet úložiště](../articles/storage/common/storage-account-create.md)** : účet úložiště úrovně Standard se vytvoří s jedinečným názvem, který je založený na názvu vaší aplikace Function App.
    * **[Plán hostování](../articles/azure-functions/functions-scale.md)** : v oblasti západní USA se vytvoří plán spotřeby pro hostování aplikace Function bez serveru.
    * **Aplikace Function App**: projekt se nasadí do této nové aplikace Function App a spustí se.

    Po vytvoření aplikace funkcí a použití balíčku nasazení se zobrazí oznámení. Pokud chcete zobrazit výsledky vytvoření a nasazení, včetně prostředků Azure, které jste vytvořili, vyberte **Zobrazit výstup** v tomto oznámení.

1. Zpátky v oblasti **Azure: Functions** rozbalte novou aplikaci Function App v rámci vašeho předplatného. Rozbalte **funkce**, klikněte pravým tlačítkem na **HttpTrigger**a pak zvolte **Kopírovat adresu URL funkce**.

    ![Zkopírujte adresu URL funkce pro nový Trigger HTTP.](./media/functions-publish-project-vscode/function-copy-endpoint-url.png)
