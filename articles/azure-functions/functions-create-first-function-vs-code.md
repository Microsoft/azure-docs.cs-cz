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
ms.date: 06/25/2019
ms.author: glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: fcf9f1d6420dbbde359d386bc3b67a0866aca30d
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444594"
---
# <a name="create-your-first-function-using-visual-studio-code"></a>Vytvoření první funkce pomocí Visual Studio Code

Služba Azure Functions umožňuje spuštění kódu v prostředí [bez serveru](https://azure.microsoft.com/solutions/serverless/), aniž byste nejdřív museli vytvořit virtuální počítač nebo publikovat webovou aplikaci.

V tomto článku se dozvíte, jak použít [rozšíření Azure Functions pro Visual Studio Code] k vytvoření a otestování funkce „hello world“ v místním počítači pomocí Microsoft Visual Studio Code. Kód funkce potom publikujete do Azure z Visual Studio Code.

![Kód služby Azure Functions v projektu sady Visual Studio](./media/functions-create-first-function-vs-code/functions-vscode-intro.png)

Rozšíření v současné době podporuje C#, JavaScript a Javu funguje s podpora v jazyce Python aktuálně ve verzi Preview. Kroky v tomto článku a v článku, který následuje podporují pouze jazyka JavaScript a C# funkce. Zjistěte, jak používat Visual Studio Code k vytvoření a publikování funkce Pythonu, najdete v článku [nasazení Pythonu do služby Azure Functions](https://code.visualstudio.com/docs/python/tutorial-azure-functions). Zjistěte, jak používat Visual Studio Code k vytvoření a publikování funkce Powershellu, najdete v článku [vytvoření první funkce prostředí PowerShell v Azure](functions-create-first-function-powershell.md). 

Rozšíření je v současné době ve verzi Preview. Další informace najdete na stránce rozšíření [Rozšíření Azure Functions pro Visual Studio Code].

## <a name="prerequisites"></a>Požadavky

K provedení kroků v tomto kurzu Rychlý start je potřeba:

* Nainstalujte [Visual Studio Code](https://code.visualstudio.com/) na jedné z [podporovaných platforem](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

* Nainstalovat verzi 2.x [nástrojů Azure Functions Core](functions-run-local.md#v2).

* Nainstalujte konkrétní požadavky pro vámi zvolený jazyk:

    | Jazyk | Požadavek |
    | -------- | --------- |
    | **C#** | [Rozšíření jazyka C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)  |
    | **JavaScript** | [Node.js](https://nodejs.org/)<sup>*</sup> | 
 
    <sup>*</sup>Aktivní LTS a údržby LTS verze (8.11.1 a 10.14.1 doporučeno).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

[!INCLUDE [functions-create-function-app-vs-code](../../includes/functions-create-function-app-vs-code.md)]

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

Po ověření správného fungování funkce na místním počítači je na čase publikovat projekt do Azure.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="run-the-function-in-azure"></a>Spuštění funkce v Azure

1. Zkopírujte adresu URL triggeru HTTP z panelu **Výstup**. Stejně jako dříve nezapomeňte k této adrese URL připojit řetězec dotazu `?name=<yourname>` a provést požadavek.

    Adresa URL, která volá funkci aktivovanou protokolem HTTP, by měla být v následujícím formátu:

        http://<functionappname>.azurewebsites.net/api/<functionname>?name=<yourname> 

1. Vložte tuto novou adresu URL pro požadavek HTTP do panelu Adresa prohlížeče. Následuje ukázka odezvy na vzdálený požadavek GET vrácené funkcí v prohlížeči: 

    ![Odezva funkce v prohlížeči](./media/functions-create-first-function-vs-code/functions-test-remote-browser.png)

## <a name="next-steps"></a>Další postup

Pomocí Visual Studio Code jste vytvořili aplikaci funkcí s jednoduchou funkcí aktivovanou protokolem HTTP. V následujícím článku rozšířit tuto funkci přidáním výstupní vazby. Tuto vazbu zapíše řetězec z požadavku HTTP pro zprávu do fronty úložiště Azure Queue Storage. Další článek také ukazuje, jak vyčistit těchto nových prostředků Azure tak, že odeberete, kterou jste vytvořili skupinu prostředků.

> [!div class="nextstepaction"]
> [Přidání vazby fronty Azure Storage do funkce](functions-add-output-binding-storage-queue-vs-code.md)

[Azure Functions Core Tools]: functions-run-local.md
[Rozšíření Azure Functions pro Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
