---
title: 'Rychlý Start: Vytvoření první statické lokality pomocí statického Web Apps Azure'
description: Naučte se nasadit statickou lokalitu do statického Web Apps Azure.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: fb874c25ab688cc5e6723d1023157b8acd9478b9
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483835"
---
# <a name="quickstart-building-your-first-static-site-with-azure-static-web-apps"></a>Rychlý Start: Vytvoření první statické lokality pomocí statického Web Apps Azure

Služba Azure static Web Apps zveřejňuje web tím, že vytváří aplikace z úložiště kódu. V tomto rychlém startu nasadíte aplikaci do statických webových aplikací Azure pomocí rozšíření Visual Studio Code.

Pokud nemáte předplatné Azure, [Vytvořte si bezplatný zkušební účet](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Požadavky

- Účet [GitHub](https://github.com)
- Účet [Azure](https://portal.azure.com)
- [Visual Studio Code](https://code.visualstudio.com)
- [Rozšíření Azure Static Web Apps pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps)
- [Nainstalovat Git](https://www.git-scm.com/downloads).

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

Pak otevřete Visual Studio Code a přejít na **soubor > otevřít složku** a otevřete úložiště, které jste naklonoval na svém počítači v editoru.

## <a name="create-a-static-web-app"></a>Vytvoření statické webové aplikace

1. Ve Visual Studio Code výběrem loga Azure na panelu aktivit otevřete okno rozšíření Azure.

    :::image type="content" source="media/getting-started/extension-azure-logo.png" alt-text="Logo Azure":::

    > [!NOTE]
    > Vyžaduje se přihlášení k Azure a GitHubu. Pokud ještě ve Visual Studio Code nejste přihlášení k Azure a GitHubu, rozšíření vás během procesu vytváření vyzve k přihlášení k oběma účtům.

1. V popisku _statického Web Apps_ vyberte znaménko **plus**.

    :::image type="content" source="media/getting-started/extension-create-button.png" alt-text="Název aplikace":::

1. V horní části editoru se otevře příkaz akcemi a zobrazí se výzva k pojmenování aplikace.

    Zadejte název **my-first-static-web-app** a stiskněte **Enter**.

    :::image type="content" source="media/getting-started/extension-create-app.png" alt-text="Vytvoření statické webové aplikace":::

1. Vyberte Předvolby, které odpovídají typu aplikace.

    # <a name="no-framework"></a>[Žádná architektura](#tab/vanilla-javascript)
    :::image type="content" source="media/getting-started/extension-presets-no-framework.png" alt-text="Přednastavení aplikace: žádné rozhraní":::

    Jako umístění souborů aplikace zadejte **./** .

    :::image type="content" source="media/getting-started/extension-app-location.png" alt-text="Umístění souborů aplikace":::

    Jako umístění pro rozhraní Azure Functions API vyberte **Přeskočit nyní** .

    :::image type="content" source="media/getting-started/extension-api-location.png" alt-text="Umístění rozhraní API":::

    Jako výstupní umístění sestavení zadejte **./.**

    :::image type="content" source="media/getting-started/extension-build-location.png" alt-text="Umístění výstupu sestavení aplikace":::

    # <a name="angular"></a>[Angular](#tab/angular)

    I když je k dispozici úhlová předvolba, vyberte možnost **vlastní** , abyste pro tuto aplikaci mohli zadat vhodné umístění výstupu.

    :::image type="content" source="media/getting-started/extension-presets-no-framework.png" alt-text="Přednastavení aplikací: úhlová":::

    Jako umístění souborů aplikace zadejte **./** .

    :::image type="content" source="media/getting-started/extension-app-location.png" alt-text="Umístění souborů aplikace: úhlové":::

    Jako umístění pro rozhraní Azure Functions API vyberte **Přeskočit nyní** .

    :::image type="content" source="media/getting-started/extension-api-location.png" alt-text="Umístění rozhraní API: úhlová":::

    Jako výstupní umístění sestavení zadejte **DIST/úhlové základní** umístění.

    :::image type="content" source="media/getting-started/extension-angular.png" alt-text="Umístění výstupu sestavení aplikace: úhlová":::

    # <a name="react"></a>[React](#tab/react)

    :::image type="content" source="media/getting-started/extension-presets-react.png" alt-text="Přednastavení aplikace: reakce":::

    # <a name="vue"></a>[Vue](#tab/vue)

    :::image type="content" source="media/getting-started/extension-presets-vue.png" alt-text="Přednastavení aplikace: Vue":::

    ---

1. Vyberte nejbližší umístění a stiskněte **Enter**.

    :::image type="content" source="media/getting-started/extension-location.png" alt-text="Umístění prostředku":::

1. Po vytvoření aplikace se ve Visual Studio Code zobrazí oznámení s potvrzením.

    :::image type="content" source="media/getting-started/extension-confirmation.png" alt-text="Potvrzení o vytvoření":::

    Potom klikněte na tlačítko **otevřít akce v GitHubu**. Na této stránce se zobrazuje stav sestavení aplikace.

    Po dokončení akce GitHubu můžete přejít na publikovaný web.

1. Chcete-li zobrazit web v prohlížeči, klikněte pravým tlačítkem na projekt v rozšíření statické Web Apps a vyberte **Procházet lokalita**.

    :::image type="content" source="media/getting-started/extension-browse-site.png" alt-text="Procházet web":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, můžete pomocí rozšíření odstranit instanci statického Web Apps Azure.

V okně Průzkumníka Visual Studio Code se vraťte do oddílu _Static Web Apps_ a klikněte pravým tlačítkem na **My-First-static-Web-App** a vyberte **Odstranit**.

:::image type="content" source="media/getting-started/extension-delete.png" alt-text="Odstranit aplikaci":::

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přidání rozhraní API](add-api.md)
