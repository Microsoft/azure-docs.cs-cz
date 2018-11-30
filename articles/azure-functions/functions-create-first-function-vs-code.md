---
title: Vytvoření první funkce v Azure pomocí Visual Studio Code
description: Vytvořte a publikujte do Azure jednoduchou funkci aktivovanou protokolem HTTP pomocí rozšíření Azure Functions ve Visual Studio Code.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: azure functions, functions, event processing, compute, serverless architecture
ms.service: azure-functions
ms.devlang: multiple
ms.topic: quickstart
ms.date: 09/07/2018
ms.author: glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: c5889d89ec8c913d9300fa85318a16b4eb452d3b
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52633754"
---
# <a name="create-your-first-function-using-visual-studio-code"></a>Vytvoření první funkce pomocí Visual Studio Code

Služba Azure Functions umožňuje spuštění kódu v prostředí [bez serveru](https://azure.microsoft.com/solutions/serverless/), aniž byste nejdřív museli vytvořit virtuální počítač nebo publikovat webovou aplikaci.

V tomto článku se dozvíte, jak použít [rozšíření Azure Functions pro Visual Studio Code] k vytvoření a otestování funkce „hello world“ v místním počítači pomocí Microsoft Visual Studio Code. Kód funkce potom publikujete do Azure z Visual Studio Code.

![Kód služby Azure Functions v projektu sady Visual Studio](./media/functions-create-first-function-vs-code/functions-vscode-intro.png)

Rozšíření nyní podporuje funkce C#, JavaScript a Java. Kroky v tomto článku se mohou lišit podle vašeho výběru jazyka pro váš projekt Azure Functions. Rozšíření je v současné době ve verzi Preview. Další informace najdete na stránce rozšíření [Rozšíření Azure Functions pro Visual Studio Code].

## <a name="prerequisites"></a>Požadavky

K provedení kroků v tomto kurzu Rychlý start je potřeba:

* Nainstalujte [Visual Studio Code](https://code.visualstudio.com/) na jedné z [podporovaných platforem](https://code.visualstudio.com/docs/supporting/requirements#_platforms). Tento článek byl vytvořený a testovaný na zařízení se systémem macOS (High Sierra).

* Nainstalujte verzi 2.x [nástrojů Azure Functions Core](functions-run-local.md#v2), která je stále ve verzi Preview.

* Nainstalujte konkrétní požadavky pro vámi zvolený jazyk:

    | Jazyk | Linka |
    | -------- | --------- |
    | **C#** | [C# pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)<br/>[Nástroje .NET Core CLI](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x)*   |
    | **Java** | [Ladicí program pro Javu](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug)<br/>[Java 8](https://aka.ms/azure-jdks)<br/>[Maven 3+](https://maven.apache.org/) |
    | **JavaScript** | [Node 8.0+](https://nodejs.org/)  |

    \* Také vyžadují nástroje Core.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

[!INCLUDE [functions-create-function-app-vs-code](../../includes/functions-create-function-app-vs-code.md)]

## <a name="create-an-http-triggered-function"></a>Vytvoření funkce aktivované protokolem HTTP

1. Z oblasti **Azure: Functions** zvolte ikonu Vytvořit funkci.

    ![Vytvoření funkce](./media/functions-create-first-function-vs-code/create-function.png)

1. Vyberte složku s vaším projektem aplikace funkcí a vyberte šablonu funkce **Trigger HTTP**.

    ![Volba šablony triggeru HTTP](./media/functions-create-first-function-vs-code/create-function-choose-template.png)

1. Jako název funkce zadejte `HTTPTrigger`, stiskněte klávesu Enter a pak vyberte **Anonymní** ověřování.

    ![Volba anonymního ověřování](./media/functions-create-first-function-vs-code/create-function-anonymous-auth.png)

    Funkce ve vybraném jazyce se vytvoří pomocí šablony funkce aktivované protokolem HTTP.

    ![Šablona funkce aktivované protokolem HTTP ve Visual Studio Code](./media/functions-create-first-function-vs-code/new-function-full.png)

Můžete přidat vstupní a výstupní vazbu k funkci tak, že upravíte soubor function.json. Další informace najdete v tématu o [koncepcích triggerů a vazeb Azure Functions](functions-triggers-bindings.md).

Teď máte vytvořený projekt funkcí a funkci aktivovanou protokolem HTTP, takže ji můžete otestovat na místním počítači.

## <a name="test-the-function-locally"></a>Místní testování funkce

Nástroje Azure Functions Core umožňují spouštět projekt Azure Functions na místním počítači pro vývoj. K instalaci těchto nástrojů budete vyzváni při prvním spuštění funkce z Visual Studio Code.  

1. Pokud chcete funkci otestovat, nastavte zarážku v kódu funkce a stiskněte klávesu F5, abyste spustili projekt aplikace funkcí. Výstup z nástrojů Tools se zobrazí na panelu **Terminál**.

1. Na panelu **Terminál** zkopírujte adresu URL koncového bodu vaší funkce aktivované protokolem HTTP.

    ![Místní výstup Azure](./media/functions-create-first-function-vs-code/functions-vscode-f5.png)

1. Vložte adresu URL pro požadavek HTTP do panelu adresy prohlížeče. K této adrese URL připojte řetězec dotazu `?name=<yourname>` a proveďte požadavek. Spuštění se při dosažení zarážky pozastaví.

    ![Dosažení zarážky funkce ve Visual Studio Code](./media/functions-create-first-function-vs-code/function-debug-vscode-js.png)

1. Pokud pokračujete v provádění, dále je uvedená ukázka odezvy na požadavek GET v prohlížeči:

    ![Odezva místního hostitele funkce v prohlížeči](./media/functions-create-first-function-vs-code/functions-test-local-browser.png)

1. Pokud chcete ladění zastavit, stiskněte Shift + F1.

Po ověření správného fungování funkce na místním počítači je na čase publikovat projekt do Azure.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="test-your-function-in-azure"></a>Testování funkce v Azure

1. Zkopírujte adresu URL triggeru HTTP z panelu **Výstup**. Stejně jako dříve nezapomeňte k této adrese URL připojit řetězec dotazu `?name=<yourname>` a provést požadavek.

    Adresa URL, která volá funkci aktivovanou protokolem HTTP, by měla být v následujícím formátu:

        http://<functionappname>.azurewebsites.net/api/<functionname>?name=<yourname> 

1. Vložte tuto novou adresu URL pro požadavek HTTP do panelu Adresa prohlížeče. Následuje ukázka odezvy na vzdálený požadavek GET vrácené funkcí v prohlížeči: 

    ![Odezva funkce v prohlížeči](./media/functions-create-first-function-vs-code/functions-test-remote-browser.png)

## <a name="next-steps"></a>Další postup

Pomocí Visual Studio Code jste vytvořili aplikaci funkcí s jednoduchou funkcí aktivovanou protokolem HTTP. Další informace o vývoji funkcí v určitém jazyce naleznete v referenčních příručkách jazyka pro [JavaScript](functions-reference-node.md), [.NET](functions-dotnet-class-library.md) nebo [Javu](functions-reference-java.md).

Dál se můžete chtít dozvědět další informace o místním testování a ladění z okna Terminál nebo příkazového řádku pomocí nástrojů Azure Functions Core.

> [!div class="nextstepaction"]
> [Kódování a testování místně](functions-run-local.md)

[Azure Functions Core Tools]: functions-run-local.md
[Rozšíření Azure Functions pro Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
