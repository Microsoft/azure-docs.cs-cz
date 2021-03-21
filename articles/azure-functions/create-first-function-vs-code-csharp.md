---
title: Vytvoření funkce jazyka C# pomocí Visual Studio Code-Azure Functions
description: Naučte se, jak vytvořit funkci jazyka C# a pak publikovat místní projekt na hostování bez serveru v Azure Functions pomocí rozšíření Azure Functions v Visual Studio Code.
ms.topic: quickstart
ms.date: 11/03/2020
ms.custom: devx-track-csharp
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./create-first-function-vs-code-csharp-ieux
ms.openlocfilehash: b0c659c1b3bf373b7754dd9b058c74da35cc2e0a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101704782"
---
# <a name="quickstart-create-a-c-function-in-azure-using-visual-studio-code"></a>Rychlý Start: vytvoření funkce jazyka C# v Azure pomocí Visual Studio Code

[!INCLUDE [functions-language-selector-quickstart-vs-code](../../includes/functions-language-selector-quickstart-vs-code.md)]

V tomto článku použijete Visual Studio Code k vytvoření funkce založené na knihovně jazyka C#, která reaguje na požadavky HTTP. Po místním testování kódu ho nasadíte do prostředí Azure Functions bez serveru.

Po dokončení tohoto rychlého startu dojde v účtu Azure k malým nákladům na několik centů nebo méně.

K dispozici je také verze tohoto článku [na bázi CLI](create-first-function-cli-csharp.md) .

## <a name="configure-your-environment"></a>Konfigurace prostředí

Než začnete, ujistěte se, že máte zavedené následující požadavky:

+ Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) verze 3. x.

+ [Visual Studio Code](https://code.visualstudio.com/) na jedné z [podporovaných platforem](https://code.visualstudio.com/docs/supporting/requirements#_platforms)

+ [Rozšíření pro jazyk C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) pro Visual Studio Code  

+ [Rozšíření Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) pro Visual Studio Code

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Vytvořit místní projekt

V této části použijete Visual Studio Code k vytvoření místního projektu Azure Functions v jazyce C#. Později v tomto článku publikujete kód funkce do Azure.

1. Zvolte ikonu Azure na panelu Aktivita a pak v oblasti **Azure: Funkce** vyberte ikonu **Vytvořit nový projekt**.

    ![Volba možnosti Vytvořit nový projekt](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Zvolte umístění adresáře pro váš pracovní prostor projektu a zvolte **Vybrat**.

    > [!NOTE]
    > Tyto kroky jsou navržené tak, aby se dokončily mimo pracovní prostor. V tomto případě nevybírejte složku projektu, která je součástí pracovního prostoru.

1. Podle pokynů na obrazovce zadejte tyto informace:

    + **Vyberte jazyk pro váš projekt funkce:** Zvolte `C#`.

    + **Vyberte šablonu pro první funkci projektu:** Zvolte `HTTP trigger`.

    + **Zadejte název funkce:** Zadejte příkaz `HttpExample`.

    + **Zadejte obor názvů:** Zadejte příkaz `My.Functions`.

    + **Úroveň autorizace**: vyberte `Anonymous` , který umožňuje komukoli zavolat koncový bod funkce. Další informace o úrovni autorizace najdete v tématu [autorizační klíče](functions-bindings-http-webhook-trigger.md#authorization-keys).

    + **Vyberte způsob, jakým chcete projekt otevírat:** Zvolte `Add to workspace`.

1. Pomocí těchto informací Visual Studio Code generuje Azure Functions projekt pomocí triggeru protokolu HTTP. Můžete zobrazit soubory místních projektů v Průzkumníkovi. Další informace o souborech, které jsou vytvořeny, najdete v tématu [vygenerované soubory projektu](functions-develop-vs-code.md#generated-project-files).

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

Po ověření, že se funkce na místním počítači spustí správně, je čas použít Visual Studio Code k publikování projektu přímo do Azure.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

## <a name="next-steps"></a>Další kroky

Použili jste [Visual Studio Code](functions-develop-vs-code.md?tabs=csharp) k vytvoření aplikace funkcí pomocí jednoduché funkce aktivované protokolem HTTP. V dalším článku tuto funkci rozbalíte tak, že se připojíte k Azure Storage. Další informace o připojení k ostatním službám Azure najdete v tématu [Přidání vazeb do existující funkce v Azure Functions](add-bindings-existing-function.md?tabs=csharp). 

> [!div class="nextstepaction"]
> [Připojení k frontě Azure Storage](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-csharp)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
