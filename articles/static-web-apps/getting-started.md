---
title: 'Rychlý Start: Vytvoření první statické webové aplikace pomocí statického Web Apps Azure'
description: Naučte se vytvářet web Azure static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: db3836e6171d187539b8615efcb5ab782c368020
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752399"
---
# <a name="quickstart-building-your-first-static-web-app"></a>Rychlý Start: Vytvoření první statické webové aplikace

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

1. Uvnitř Visual Studio Code v řádku aktivity vyberte logo Azure a otevřete okno rozšíření Azure.

    :::image type="content" source="media/getting-started/extension-azure-logo.png" alt-text="Logo Azure":::

    > [!NOTE]
    > Vyžaduje se přihlášení Azure a GitHubu. Pokud ještě nejste přihlášeni k Azure a GitHub z Visual Studio Code, rozšíření vás během procesu vytváření vyzve k přihlášení k obou.

1. Umístěte ukazatel myši na popisek _statické Web Apps_ a vyberte **znaménko plus**.

    :::image type="content" source="media/getting-started/extension-create-button.png" alt-text="Název aplikace":::

1. V horní části editoru se otevře příkaz akcemi a zobrazí se výzva k pojmenování aplikace.

    Zadejte **My-First-static-Web-App** a stiskněte klávesu **ENTER**.

    :::image type="content" source="media/getting-started/extension-create-app.png" alt-text="Vytvoření statické webové aplikace":::

1. Vyberte **Hlavní** větev a stiskněte klávesu **ENTER**.

    :::image type="content" source="media/getting-started/extension-branch.png" alt-text="Název větve":::

1. **/** Jako umístění pro kód aplikace vyberte a stiskněte klávesu **ENTER**.

    :::image type="content" source="media/getting-started/extension-app-location.png" alt-text="Umístění kódu aplikace":::

1. Rozšíření hledá umístění rozhraní API ve vaší aplikaci. Tento článek neimplementuje rozhraní API.

    Vyberte **Přeskočit pro nyní** a stiskněte klávesu **ENTER**.

    :::image type="content" source="media/getting-started/extension-api-location.png" alt-text="Umístění rozhraní API":::

1. Vyberte umístění, ve kterém se mají v aplikaci vytvářet soubory pro produkci.

    # <a name="no-framework"></a>[Žádná architektura](#tab/vanilla-javascript)

    Zrušte zaškrtnutí políčka a stiskněte klávesu **ENTER**.

    :::image type="content" source="media/getting-started/extension-artifact-no-framework.png" alt-text="Cesta k souborům aplikace":::

    # <a name="angular"></a>[Angular](#tab/angular)

    Zadejte **DIST/úhlové základní** a stiskněte klávesu **ENTER**.

    :::image type="content" source="media/getting-started/extension-artifact-angular.png" alt-text="Cesta k souborům aplikace v úhlovém umístění":::

    # <a name="react"></a>[React](#tab/react)

    Zadejte **Build** a stiskněte klávesu **ENTER**.

    :::image type="content" source="media/getting-started/extension-artifact-react.png" alt-text="Cesta k souborům aplikace reagují":::

    # <a name="vue"></a>[Vue](#tab/vue)

    Zadejte **DIST** a stiskněte klávesu **ENTER**.

    :::image type="content" source="media/getting-started/extension-artifact-vue.png" alt-text="Cesta k souborům aplikace Vue":::

    ---

1. Vyberte nejbližší umístění a stiskněte klávesu **ENTER**.

    :::image type="content" source="media/getting-started/extension-location.png" alt-text="Umístění prostředku":::

1. Po vytvoření aplikace se v Visual Studio Code zobrazí oznámení o potvrzení.

    :::image type="content" source="media/getting-started/extension-confirmation.png" alt-text="Vytvořeno potvrzení":::

1. V okně Průzkumníka Visual Studio Code se vraťte do oddílu _Static Web Apps_ a klikněte pravým tlačítkem na **produkční** prostředí a výběrem **otevřít na portálu** zobrazte aplikaci v Azure Portal.

    :::image type="content" source="media/getting-started/extension-open-in-portal.png" alt-text="Otevřít portál":::

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, můžete pomocí rozšíření odstranit instanci statického Web Apps Azure.

V okně Průzkumníka Visual Studio Code se vraťte do oddílu _Static Web Apps_ a klikněte pravým tlačítkem na **My-First-static-Web-App** a vyberte **Odstranit**.

:::image type="content" source="media/getting-started/extension-delete.png" alt-text="Odstranit aplikaci":::

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přidání rozhraní API](add-api.md)
