---
title: Vytvoření první funkce v Azure pomocí Visual Studio Code
description: Vytvořte a publikujte do Azure jednoduchou funkci aktivovanou protokolem HTTP pomocí rozšíření Azure Functions ve Visual Studio Code.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: azure functions, functions, event processing, compute, serverless architecture
ms.service: azure-functions
ms.topic: quickstart
ms.date: 06/25/2019
ms.author: glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: 52f682f7c7f06056be122b33d27592a55a01be94
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70744078"
---
# <a name="create-your-first-function-using-visual-studio-code"></a>Vytvoření první funkce pomocí Visual Studio Code

Služba Azure Functions umožňuje spuštění kódu v prostředí [bez serveru](https://azure.microsoft.com/solutions/serverless/), aniž byste nejdřív museli vytvořit virtuální počítač nebo publikovat webovou aplikaci.

V tomto článku se dozvíte, jak použít [rozšíření Azure Functions pro Visual Studio Code] k vytvoření a otestování funkce „hello world“ v místním počítači pomocí Microsoft Visual Studio Code. Kód funkce potom publikujete do Azure z Visual Studio Code.

![Kód služby Azure Functions v projektu sady Visual Studio](./media/functions-create-first-function-vs-code/functions-vscode-intro.png)

Rozšíření aktuálně podporuje C#funkce, JavaScript, Java a Python. Kroky v tomto článku a článku, které následují, podporují pouze JavaScript C# a funkce. Informace o tom, jak používat Visual Studio Code k vytváření a publikování funkcí Pythonu, najdete v tématu [nasazení Pythonu pro Azure Functions](https://code.visualstudio.com/docs/python/tutorial-azure-functions). Další informace o použití Visual Studio Code k vytváření a publikování funkcí PowerShellu najdete v tématu [Vytvoření první funkce PowerShellu v Azure](functions-create-first-function-powershell.md). 

Rozšíření je v současné době ve verzi Preview. Další informace najdete na stránce rozšíření [Rozšíření Azure Functions pro Visual Studio Code].

## <a name="prerequisites"></a>Požadavky

K provedení kroků v tomto kurzu Rychlý start je potřeba:

* Nainstalujte [Visual Studio Code](https://code.visualstudio.com/) na jedné z [podporovaných platforem](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

* Nainstalujte verzi 2. x z [Azure Functions Core Tools](functions-run-local.md#v2).

* Nainstalujte konkrétní požadavky pro vámi zvolený jazyk:

    | Jazyk | Požadavek |
    | -------- | --------- |
    | **C#** | [C#klapk](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)  |
    | **JavaScript** | [Node.js](https://nodejs.org/)<sup>*</sup> | 
 
    <sup>*</sup>Aktivní LTS a verze LTS údržby (Doporučené 8.11.1 a 10.14.1).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

[!INCLUDE [functions-create-function-app-vs-code](../../includes/functions-create-function-app-vs-code.md)]

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

Po ověření správného fungování funkce na místním počítači je na čase publikovat projekt do Azure.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="run-the-function-in-azure"></a>Spuštění funkce v Azure

1. Zkopírujte adresu URL triggeru HTTP z panelu **Výstup**. Tato adresa URL zahrnuje klíč funkce, který je předán `code` parametru dotazu. Stejně jako dříve nezapomeňte k této adrese URL připojit řetězec dotazu `?name=<yourname>` a provést požadavek.

    Adresa URL, která volá funkci aktivovanou protokolem HTTP, by měla být v následujícím formátu:

        http://<functionappname>.azurewebsites.net/api/<functionname>?code=<function_key>&name=<yourname> 

1. Vložte tuto novou adresu URL pro požadavek HTTP do panelu Adresa prohlížeče. Následuje ukázka odezvy na vzdálený požadavek GET vrácené funkcí v prohlížeči: 

    ![Odezva funkce v prohlížeči](./media/functions-create-first-function-vs-code/functions-test-remote-browser.png)

## <a name="next-steps"></a>Další kroky

Pomocí Visual Studio Code jste vytvořili aplikaci funkcí s jednoduchou funkcí aktivovanou protokolem HTTP. V dalším článku rozbalíte tuto funkci přidáním výstupní vazby. Tato vazba zapíše řetězec z požadavku HTTP do zprávy ve frontě Azure Queue Storage. Další článek také ukazuje, jak vyčistit tyto nové prostředky Azure odebráním vytvořené skupiny prostředků.

> [!div class="nextstepaction"]
> [Přidání vazby fronty Azure Storage k funkci](functions-add-output-binding-storage-queue-vs-code.md)

[Azure Functions Core Tools]: functions-run-local.md
[Rozšíření Azure Functions pro Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
