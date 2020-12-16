---
title: 'Rychlý Start: Vytvoření první statické lokality pomocí statického Web Apps Azure'
description: Naučte se nasadit statickou lokalitu do statického Web Apps Azure.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: eb2356451c349f894c9ca74b1359f6a02d0e002a
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97562510"
---
# <a name="quickstart-building-your-first-static-site-with-azure-static-web-apps"></a>Rychlý Start: Vytvoření první statické lokality pomocí statického Web Apps Azure

Služba Azure static Web Apps publikuje web do produkčního prostředí tím, že vytváří aplikace z úložiště GitHub. V tomto rychlém startu nasadíte webovou aplikaci do statických webových aplikací Azure pomocí rozšíření Visual Studio Code.

Pokud nemáte předplatné Azure, [Vytvořte si bezplatný zkušební účet](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Předpoklady

- Účet [GitHub](https://github.com)
- Účet [Azure](https://portal.azure.com)
- [Visual Studio Code](https://code.visualstudio.com)
- [Rozšíření statického Web Apps Azure pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

Pak otevřete Visual Studio Code a přejít na **soubor > otevřít složku** a otevřete úložiště, které jste právě naklonoval na váš počítač v editoru.

## <a name="create-a-static-web-app"></a>Vytvoření statické webové aplikace

1. Ve Visual Studio Code výběrem loga Azure na panelu aktivit otevřete okno rozšíření Azure.

    :::image type="content" source="media/getting-started/extension-azure-logo.png" alt-text="Logo Azure":::

    > [!NOTE]
    > Vyžaduje se přihlášení k Azure a GitHubu. Pokud ještě ve Visual Studio Code nejste přihlášení k Azure a GitHubu, rozšíření vás během procesu vytváření vyzve k přihlášení k oběma účtům.

1. Umístěte myš nad popisek _Static Web Apps_ a vyberte **symbol plus**.

    :::image type="content" source="media/getting-started/extension-create-button.png" alt-text="Název aplikace":::

1. V horní části editoru se otevře příkaz akcemi a zobrazí se výzva k pojmenování aplikace.

    Zadejte název **my-first-static-web-app** a stiskněte **Enter**.

    :::image type="content" source="media/getting-started/extension-create-app.png" alt-text="Vytvoření statické webové aplikace":::

1. Vyberte větev **main** (Hlavní) a stiskněte **Enter**.

    :::image type="content" source="media/getting-started/extension-branch.png" alt-text="Název větve":::

1. **/** Jako umístění pro kód aplikace vyberte a stiskněte klávesu **ENTER**.

    :::image type="content" source="media/getting-started/extension-app-location.png" alt-text="Umístění kódu aplikace":::

1. Rozšíření hledá umístění rozhraní API ve vaší aplikaci. Tento článek se nevěnuje implementaci rozhraní API.

    Vyberte **Skip for now** (Prozatím přeskočit) a stiskněte **Enter**.

    :::image type="content" source="media/getting-started/extension-api-location.png" alt-text="Umístění rozhraní API":::

1. Vyberte umístění, ve kterém se mají v aplikaci vytvářet soubory pro produkční prostředí.

    # <a name="no-framework"></a>[Žádná architektura](#tab/vanilla-javascript)

    Zrušte zaškrtnutí políčka a stiskněte klávesu **ENTER**.

    :::image type="content" source="media/getting-started/extension-artifact-no-framework.png" alt-text="Cesta k souborům aplikace":::

    # <a name="angular"></a>[Angular](#tab/angular)

    Zadejte **DIST/úhlové základní** a stiskněte klávesu **ENTER**.

    :::image type="content" source="media/getting-started/extension-artifact-angular.png" alt-text="Cesta k souborům aplikace Angular":::

    # <a name="react"></a>[React](#tab/react)

    Zadejte **build** a stiskněte **Enter**.

    :::image type="content" source="media/getting-started/extension-artifact-react.png" alt-text="Cesta k souborům aplikace React":::

    # <a name="vue"></a>[Vue](#tab/vue)

    Zadejte **dist** a stiskněte **Enter**.

    :::image type="content" source="media/getting-started/extension-artifact-vue.png" alt-text="Cesta k souborům aplikace Vue":::

    ---

1. Vyberte nejbližší umístění a stiskněte **Enter**.

    :::image type="content" source="media/getting-started/extension-location.png" alt-text="Umístění prostředku":::

1. Po vytvoření aplikace se ve Visual Studio Code zobrazí oznámení s potvrzením.

    :::image type="content" source="media/getting-started/extension-confirmation.png" alt-text="Potvrzení o vytvoření":::

1. V okně Průzkumníka Visual Studio Code přejděte na uzel, který má název vašeho předplatného a rozbalte ho. Všimněte si, že může trvat několik minut, než se nasazení dokončí. Pak se vraťte do oddílu static Web Apps a vyberte název aplikace a potom klikněte pravým tlačítkem na My-First-static-Web-App a vyberte otevřít na portálu. zobrazí se aplikace v Azure Portal.

    :::image type="content" source="media/getting-started/extension-open-in-portal.png" alt-text="Otevření na portálu":::

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, můžete pomocí rozšíření odstranit instanci statického Web Apps Azure.

V okně Průzkumníka Visual Studio Code se vraťte do oddílu _Static Web Apps_ a klikněte pravým tlačítkem na **My-First-static-Web-App** a vyberte **Odstranit**.

:::image type="content" source="media/getting-started/extension-delete.png" alt-text="Odstranit aplikaci":::

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přidání rozhraní API](add-api.md)
