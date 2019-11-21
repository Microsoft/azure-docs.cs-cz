---
title: Vytvoření první funkce v Azure pomocí Visual Studio Code
description: Vytvořte a publikujte do Azure jednoduchou funkci aktivovanou protokolem HTTP pomocí rozšíření Azure Functions ve Visual Studio Code.
ms.topic: quickstart
ms.date: 06/25/2019
ms.custom: mvc, devcenter
ms.openlocfilehash: b89e6d75cd82a65dfbce29b949c4b7d463582bb9
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230797"
---
# <a name="create-your-first-function-using-visual-studio-code"></a>Vytvoření první funkce pomocí Visual Studio Code

Služba Azure Functions umožňuje spuštění kódu v prostředí [bez serveru](https://azure.microsoft.com/solutions/serverless/), aniž byste nejdřív museli vytvořit virtuální počítač nebo publikovat webovou aplikaci.

V tomto článku se dozvíte, jak použít [rozšíření Azure Functions pro Visual Studio Code] k vytvoření a otestování funkce „hello world“ v místním počítači pomocí Microsoft Visual Studio Code. Kód funkce potom publikujete do Azure z Visual Studio Code.

![Kód služby Azure Functions v projektu sady Visual Studio](./media/functions-create-first-function-vs-code/functions-vscode-intro.png)

The extension currently supports C#, JavaScript, Java, and Python functions. The steps in this article and the article that follows support only JavaScript and C# functions. To learn how to use Visual Studio Code to create and publish Python functions, see [Create and deploy serverless Azure Functions in Python with Visual Studio Code](/azure/python/tutorial-vs-code-serverless-python-01). To learn how to use Visual Studio Code to create and publish PowerShell functions, see [Create your first PowerShell function in Azure](functions-create-first-function-powershell.md). 

Rozšíření je v současné době ve verzi Preview. Další informace najdete na stránce rozšíření [Rozšíření Azure Functions pro Visual Studio Code].

## <a name="prerequisites"></a>Předpoklady

K provedení kroků v tomto kurzu Rychlý start je potřeba:

* Nainstalujte [Visual Studio Code](https://code.visualstudio.com/) na jedné z [podporovaných platforem](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

* Install version 2.x of the [Azure Functions Core Tools](functions-run-local.md#v2).

* Nainstalujte konkrétní požadavky pro vámi zvolený jazyk:

    | Jazyk | Požadavek |
    | -------- | --------- |
    | **C#** | [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)  |
    | **JavaScript** | [Node.js](https://nodejs.org/)<sup>*</sup> | 
 
    <sup>*</sup>Active LTS and Maintenance LTS versions (8.11.1 and 10.14.1 recommended).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

[!INCLUDE [functions-create-function-app-vs-code](../../includes/functions-create-function-app-vs-code.md)]

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

Po ověření správného fungování funkce na místním počítači je na čase publikovat projekt do Azure.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="run-the-function-in-azure"></a>Run the function in Azure

1. Zkopírujte adresu URL triggeru HTTP z panelu **Výstup**. This URL includes the function key, which is passed to the `code` query parameter. Stejně jako dříve nezapomeňte k této adrese URL připojit řetězec dotazu `?name=<yourname>` a provést požadavek.

    Adresa URL, která volá funkci aktivovanou protokolem HTTP, by měla být v následujícím formátu:

        http://<functionappname>.azurewebsites.net/api/<functionname>?code=<function_key>&name=<yourname> 

1. Vložte tuto novou adresu URL pro požadavek HTTP do panelu Adresa prohlížeče. Následuje ukázka odezvy na vzdálený požadavek GET vrácené funkcí v prohlížeči: 

    ![Odezva funkce v prohlížeči](./media/functions-create-first-function-vs-code/functions-test-remote-browser.png)

## <a name="next-steps"></a>Další kroky

Pomocí Visual Studio Code jste vytvořili aplikaci funkcí s jednoduchou funkcí aktivovanou protokolem HTTP. In the next article, you expand that function by adding an output binding. This binding writes the string from the HTTP request to a message in an Azure Queue Storage queue. The next article also shows you how to clean up these new Azure resources by removing the resource group you created.

> [!div class="nextstepaction"]
> [Add an Azure Storage queue binding to your function](functions-add-output-binding-storage-queue-vs-code.md)

[Azure Functions Core Tools]: functions-run-local.md
[Rozšíření Azure Functions pro Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
