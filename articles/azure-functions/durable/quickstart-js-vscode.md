---
title: Vytvoření první funkce trvalý v Azure pomocí jazyka JavaScript
description: Vytvoření a publikování odolné funkce Azure pomocí nástroje Visual Studio Code.
services: functions
documentationcenter: na
author: ColbyTresness
manager: jeconnoc
keywords: azure functions, functions, event processing, compute, serverless architecture
ms.service: azure-functions
ms.devlang: multiple
ms.topic: quickstart
ms.date: 11/07/2018
ms.author: azfuncdf, cotresne, glenga
ms.openlocfilehash: 4ee1c9edf8cb10cae1a8a6e1c15f9bcf6e9a8ff8
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359455"
---
# <a name="create-your-first-durable-function-in-javascript"></a>Vytvoření první funkce trvalý v jazyce JavaScript

*Odolná služba Functions* je rozšířením [Azure Functions](../functions-overview.md) , který umožňuje zapisovat stavové funkce v prostředí bez serveru. Rozšíření spravuje stav, kontrolní body a restartuje za vás.

V tomto článku se dozvíte, jak použít rozšíření Visual Studio Code Azure Functions místně vytvořit a otestovat funkci "hello world" trvalý.  Tato funkce bude orchestrovat a zřetězit volání dalších funkcí. Kód funkce potom publikujete do Azure.

![Odolné funkce spuštěné v Azure](./media/quickstart-js-vscode/functions-vs-code-complete.png)

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* Nainstalujte [Visual Studio Code](https://code.visualstudio.com/download).

* Přesvědčte se, že máte [nejnovější nástroje Azure Functions](../functions-develop-vs.md#check-your-tools-version).

* Na počítači s Windows zkontrolujte, máte [emulátoru úložiště Azure](../../storage/common/storage-use-emulator.md) nainstalovaná a spuštěná. Na počítači Mac nebo Linux musíte použít účet skutečné úložiště Azure.

* Ujistěte se, že máte verze 8.0 nebo novější verzi [Node.js](https://nodejs.org/) nainstalované.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../../includes/functions-install-vs-code-extension.md)]

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-create-function-app-vs-code.md)]

## <a name="install-the-durable-functions-npm-package"></a>Nainstalujte balíček npm Durable Functions

1. Nainstalujte `durable-functions` balíčku npm spuštěním `npm install durable-functions` v kořenovém adresáři aplikace function app.

## <a name="create-a-starter-function"></a>Vytvoření funkce Starter

Nejprve vytvořte funkci aktivovanou protokolem HTTP, který se spustí Orchestrace odolné funkce.

1. Z **Azure: Funkce**, zvolte ikonu Create Function.

    ![Vytvoření funkce](./media/quickstart-js-vscode/create-function.png)

2. Vyberte složku s vaším projektem aplikace funkcí a vyberte šablonu funkce **Trigger HTTP**.

    ![Volba šablony triggeru HTTP](./media/quickstart-js-vscode/create-function-choose-template.png)

3. Jako název funkce zadejte `HttpStart`, stiskněte klávesu Enter a pak vyberte **Anonymní** ověřování.

    ![Volba anonymního ověřování](./media/quickstart-js-vscode/create-function-anonymous-auth.png)

    Funkce ve vybraném jazyce se vytvoří pomocí šablony funkce aktivované protokolem HTTP.

4. Nahraďte index.js s pod JavaScript:

    [!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

5. Nahraďte function.json se následující JSON:

    [!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json)]

Nyní jsme vytvořili vstupní bod do naší trvalé funkce. Přidejme orchestrátor.

## <a name="create-an-orchestrator-function"></a>Vytvoření funkce Orchestrátoru

Dále vytvoříte další funkce orchestrátoru. Šablonu funkce triggeru HTTP používáme ke zvýšení pohodlí. Samotný kód funkce se nahradí kódem produktu orchestrator.

1. Opakujte kroky v předchozí části, chcete-li vytvořit druhou funkci, která pomocí šablony triggeru HTTP. Pojmenujte funkci tentokrát `OrchestratorFunction`.

2. Otevřete soubor index.js nové funkce a nahraďte obsah následujícím kódem:

    [!code-json[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

3. Otevřete soubor function.json a nahraďte ho následujícím kódem JSON:

    [!code-json[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/function.json)]

Přidali jsme orchestrator ke koordinaci aktivit funkce. Přidejme nyní odkazovaná aktivita funkce.

## <a name="create-an-activity-function"></a>Vytvoření funkce aktivity

1. Opakujte kroky v předchozích částech vytvoříte třetí funkci pomocí šablony triggeru HTTP. Ale tentokrát název funkce `E1_SayHello`.

2. Otevřete soubor index.js nové funkce a nahraďte obsah následujícím kódem:

    [!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

3. Nahraďte function.json se následující JSON:

    [!code-json[Main](~/samples-durable-functions/samples/csx/E1_SayHello/function.json)]

Teď jsme přidali všechny komponenty potřebné ke spuštění vypnout Orchestrace a řetězce dohromady aktivita funkce.

## <a name="test-the-function-locally"></a>Místní testování funkce

Nástroje Azure Functions Core umožňují spouštět projekt Azure Functions na místním počítači pro vývoj. K instalaci těchto nástrojů budete vyzváni při prvním spuštění funkce z Visual Studio Code.  

1. Na počítači s Windows spusťte emulátor úložiště Azure a ujistěte se, že **AzureWebJobsStorage** vlastnost local.settings.json je nastavena na `UseDevelopmentStorage=true`. Na počítači Mac nebo Linux, je nutné nastavit **AzureWebJobsStorage** vlastnost připojovacího řetězce pro existující účet úložiště Azure. Dále v tomto článku vytvoříte účet úložiště.

2. Pokud chcete funkci otestovat, nastavte zarážku v kódu funkce a stiskněte klávesu F5, abyste spustili projekt aplikace funkcí. Výstup z nástrojů Tools se zobrazí na panelu **Terminál**. Pokud je toto vaše první přihlášení pomocí Durable Functions, instalaci rozšíření Durable Functions a sestavení může trvat několik sekund.

    > [!NOTE]
    > JavaScript Durable Functions vyžaduje verzi **1.7.0** nebo větší **Microsoft.Azure.WebJobs.Extensions.DurableTask** rozšíření. Ověřit verzi rozšíření Durable Functions ve vaší `extensions.csproj` soubor splňuje tento požadavek. Pokud tomu tak není, zastavte aplikaci function app, změnit verzi a stiskněte klávesu F5 a restartujte aplikaci function app.

3. Na panelu **Terminál** zkopírujte adresu URL koncového bodu vaší funkce aktivované protokolem HTTP.

    ![Místní výstup Azure](../media/functions-create-first-function-vs-code/functions-vscode-f5.png)

4. Nahraďte `{functionName}` za `OrchestratorFunction` (Jak velká může být moje znalostní báze?).

5. Pomocí některého nástroje, například [Postman](https://www.getpostman.com/) nebo [cURL](https://curl.haxx.se/), odeslání požadavku HTTP POST do koncového bodu adresy URL.

6. Pokud chcete zastavit ladění, stiskněte Shift + F1 v nástroji VS Code.

Po ověření správného fungování funkce na místním počítači je na čase publikovat projekt do Azure.

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../../includes/functions-publish-project-vscode.md)]

## <a name="test-your-function-in-azure"></a>Testování funkce v Azure

1. Zkopírujte adresu URL triggeru HTTP z panelu **Výstup**. Adresa URL, která volá funkci aktivovanou protokolem HTTP, by měla být v následujícím formátu:

        http://<functionappname>.azurewebsites.net/orchestrators/<functionname>

2. Vložte tuto novou adresu URL pro požadavek HTTP do panelu Adresa prohlížeče. Měli byste obdržet stejnou odpověď stav jako před při použití publikované aplikace.

## <a name="next-steps"></a>Další postup

Používáte Visual Studio Code k vytvoření a publikování aplikace odolné funkce jazyka JavaScript.

> [!div class="nextstepaction"]
> [Další informace o běžných vzorech trvalý – funkce](durable-functions-concepts.md)