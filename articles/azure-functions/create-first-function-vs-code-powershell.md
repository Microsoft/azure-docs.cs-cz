---
title: Vytvoření funkce PowerShellu pomocí Visual Studio Code-Azure Functions
description: Naučte se vytvořit funkci PowerShellu a pak publikovat místní projekt na hostování bez serveru v Azure Functions pomocí rozšíření Azure Functions v Visual Studio Code.
ms.topic: quickstart
ms.date: 11/04/2020
ms.openlocfilehash: ad129d8faec5725a6e76f14577e6a0e9deae8d86
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2021
ms.locfileid: "97968003"
---
# <a name="quickstart-create-a-powershell-function-in-azure-using-visual-studio-code"></a>Rychlý Start: vytvoření funkce PowerShellu v Azure pomocí Visual Studio Code

[!INCLUDE [functions-language-selector-quickstart-vs-code](../../includes/functions-language-selector-quickstart-vs-code.md)]

V tomto článku použijete Visual Studio Code k vytvoření funkce PowerShellu, která reaguje na požadavky HTTP. Po místním testování kódu ho nasadíte do prostředí Azure Functions bez serveru.

Po dokončení tohoto rychlého startu dojde v účtu Azure k malým nákladům na několik centů nebo méně.

K dispozici je také verze tohoto článku [na bázi CLI](create-first-function-cli-powershell.md) .

## <a name="configure-your-environment"></a>Konfigurace prostředí

Než začnete, ujistěte se, že máte zavedené následující požadavky:

+ Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) verze 3. x.

+ [PowerShell 7](/powershell/scripting/install/installing-powershell-core-on-windows)

+ Runtime [.NET core 3,1](https://www.microsoft.com/net/download) a [.NET Core 2,1 runtime](https://dotnet.microsoft.com/download/dotnet-core/2.1)  

+ [Visual Studio Code](https://code.visualstudio.com/) na některé z [podporovaných platforem](https://code.visualstudio.com/docs/supporting/requirements#_platforms).    

+ [Rozšíření PowerShellu pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell).  

+ [Azure Functions rozšíření](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) pro Visual Studio Code. 

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Vytvořit místní projekt

V této části použijete Visual Studio Code k vytvoření místního projektu Azure Functions v prostředí PowerShell. Později v tomto článku publikujete kód funkce do Azure.

1. Zvolte ikonu Azure na řádku aktivity a potom v oblasti **Azure: funkce** vyberte ikonu **vytvořit nový projekt...** .

    ![Zvolit vytvořit nový projekt](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Zvolte umístění adresáře pro váš pracovní prostor projektu a zvolte **možnost vybrat**.

    > [!NOTE]
    > Tyto kroky jsou navržené tak, aby se dokončily mimo pracovní prostor. V tomto případě nevybírejte složku projektu, která je součástí pracovního prostoru.

1. Zadejte následující informace na následujících dotazech:

    + **Vyberte jazyk pro projekt funkce**: zvolte `PowerShell` .

    + **Vyberte šablonu pro funkci prvního projektu**: zvolte `HTTP trigger` .

    + Zadejte **název funkce**: typ `HttpExample` .

    + **Úroveň autorizace**: vyberte `Anonymous` , který umožňuje komukoli zavolat koncový bod funkce. Další informace o úrovni autorizace najdete v tématu [autorizační klíče](functions-bindings-http-webhook-trigger.md#authorization-keys).

    + **Vyberte, jak se má projekt otevřít**: zvolte `Add to workspace` .

1. Pomocí těchto informací Visual Studio Code generuje Azure Functions projekt pomocí triggeru protokolu HTTP. Můžete zobrazit soubory místních projektů v Průzkumníkovi. Další informace o souborech, které jsou vytvořeny, najdete v tématu [vygenerované soubory projektu](functions-develop-vs-code.md#generated-project-files). 

[!INCLUDE [functions-run-function-test-local-vs-code-ps](../../includes/functions-run-function-test-local-vs-code-ps.md)]

Po ověření, že se funkce na místním počítači spustí správně, je čas použít Visual Studio Code k publikování projektu přímo do Azure. 

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

## <a name="next-steps"></a>Další kroky

Pomocí Visual Studio Code jste vytvořili aplikaci funkcí s jednoduchou funkcí aktivovanou protokolem HTTP. V dalším článku rozbalíte tuto funkci přidáním výstupní vazby. Tato vazba zapíše řetězec z požadavku HTTP do zprávy ve frontě Azure Queue Storage. 

> [!div class="nextstepaction"]
> [Připojení k frontě Azure Storage](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-powershell)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
