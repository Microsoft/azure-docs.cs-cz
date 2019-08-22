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
ms.author: glenga
ms.reviewer: azfuncdf, cotresne
ms.openlocfilehash: c54a5631222a6de261e9805f284a4dfa2801750f
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612911"
---
# <a name="create-your-first-durable-function-in-javascript"></a>Vytvoření první funkce trvalý v jazyce JavaScript

*Odolná služba Functions* je rozšířením [Azure Functions](../functions-overview.md) , který umožňuje zapisovat stavové funkce v prostředí bez serveru. Toto rozšíření za vás spravuje stav, kontrolní body a restartování.

V tomto článku se dozvíte, jak použít rozšíření Visual Studio Code Azure Functions místně vytvořit a otestovat funkci "hello world" trvalý.  Tato funkce bude orchestrovat a zřetězit volání dalších funkcí. Kód funkce potom publikujete do Azure.

![Odolné funkce spuštěné v Azure](./media/quickstart-js-vscode/functions-vs-code-complete.png)

## <a name="prerequisites"></a>Požadavky

K provedení kroků v tomto kurzu je potřeba:

* Nainstalujte [Visual Studio Code](https://code.visualstudio.com/download).

* Ujistěte se, že máte nejnovější verzi [nástrojů Azure Functions Core](../functions-run-local.md).

* Na počítači s Windows zkontrolujte, máte [emulátoru úložiště Azure](../../storage/common/storage-use-emulator.md) nainstalovaná a spuštěná. Na počítači Mac nebo Linux musíte použít účet skutečné úložiště Azure.

* Ujistěte se, že máte verze 8.0 nebo novější verzi [Node.js](https://nodejs.org/) nainstalované.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../../includes/functions-install-vs-code-extension.md)]

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-create-function-app-vs-code.md)]

## <a name="install-the-durable-functions-npm-package"></a>Nainstalujte balíček npm Durable Functions

1. Nainstalujte `durable-functions` balíčku npm spuštěním `npm install durable-functions` v kořenovém adresáři aplikace function app.

## <a name="creating-your-functions"></a>Vytvoření funkce

Teď vytvoříme tři funkce potřebné pro zahájení práce s Durable Functions: Spouštěč protokolu HTTP, orchestrator a funkci aktivity. Spouštěč protokolu HTTP opraví, zahájí se celé řešení a orchestrátor bude rozesílání pro různé funkce aktivity.

### <a name="http-starter"></a>Spouštěč protokolu HTTP

Nejprve vytvořte funkci aktivovanou protokolem HTTP, který se spustí Orchestrace odolné funkce.

1. Z *Azure: Funkce*, zvolte **Create Function** ikonu.

    ![Vytvoření funkce](./media/quickstart-js-vscode/create-function.png)

2. Vyberte složku s váš projekt aplikace funkcí a vyberte **trvalý Spouštěč protokolu HTTP pro funkce** šablony funkce.

    ![Výběr šablony starter HTTP](./media/quickstart-js-vscode/create-function-choose-template.png)

3. Ponechat výchozí název jako `DurableFunctionsHttpStart` a stiskněte klávesu ** **Enter**, pak vyberte **anonymní** ověřování.

    ![Volba anonymního ověřování](./media/quickstart-js-vscode/create-function-anonymous-auth.png)

Nyní jsme vytvořili vstupní bod do naší trvalé funkce. Přidejme orchestrátor.

### <a name="orchestrator"></a>Orchestrator

Teď vytvoříme orchestrator ke koordinaci aktivit funkce.

1. Z *Azure: Funkce*, zvolte **Create Function** ikonu.

    ![Vytvoření funkce](./media/quickstart-js-vscode/create-function.png)

2. Vyberte složku s váš projekt aplikace funkcí a vyberte **orchestrátor Durable Functions** šablony funkce. Nechte název tak, jako výchozí "DurableFunctionsOrchestrator"

    ![Výběr šablony funkce nástroje orchestrator](./media/quickstart-js-vscode/create-function-choose-template.png)

Přidali jsme orchestrator ke koordinaci aktivit funkce. Přidejme nyní odkazovaná aktivita funkce.

### <a name="activity"></a>Aktivita

Teď vytvoříme funkce aktivitu provádět ve skutečnosti práce řešení.

1. Z *Azure: Funkce*, zvolte **Create Function** ikonu.

    ![Vytvoření funkce](./media/quickstart-js-vscode/create-function.png)

2. Vyberte složku s váš projekt aplikace funkcí a vyberte **aktivita Durable Functions** šablony funkce. Nechte název tak jako výchozí "Hello".

    ![Výběr šablony funkce aktivity](./media/quickstart-js-vscode/create-function-choose-template.png)

Teď jsme přidali všechny komponenty potřebné ke spuštění vypnout Orchestrace a řetězce dohromady aktivita funkce.

## <a name="test-the-function-locally"></a>Místní testování funkce

Nástroje Azure Functions Core umožňují spouštět projekt Azure Functions na místním počítači pro vývoj. K instalaci těchto nástrojů budete vyzváni při prvním spuštění funkce z Visual Studio Code.

1. Na počítači s Windows spusťte emulátor úložiště Azure a ujistěte se, že **AzureWebJobsStorage** vlastnost *local.settings.json* je nastavena na `UseDevelopmentStorage=true`.

    Pro úložiště emulátor 5.8 Ujistěte se, že **AzureWebJobsSecretStorageType** vlastnost local.settings.json je nastavena na `files`. Na počítači Mac nebo Linux, je nutné nastavit **AzureWebJobsStorage** vlastnost připojovacího řetězce pro existující účet úložiště Azure. Dále v tomto článku vytvoříte účet úložiště.

2. Pokud chcete funkci otestovat, nastavte zarážku v kódu funkce a stiskněte klávesu F5, abyste spustili projekt aplikace funkcí. Výstup z nástrojů Tools se zobrazí na panelu **Terminál**. Pokud je toto vaše první přihlášení pomocí Durable Functions, instalaci rozšíření Durable Functions a sestavení může trvat několik sekund.

    > [!NOTE]
    > JavaScript Durable Functions vyžaduje verzi **1.7.0** nebo větší **Microsoft.Azure.WebJobs.Extensions.DurableTask** rozšíření. Spuštěním následujícího příkazu z kořenové složky vaší aplikace Azure Functions k instalaci rozšíření Durable Functions `func extensions install -p Microsoft.Azure.WebJobs.Extensions.DurableTask -v 1.7.0`

3. Na panelu **Terminál** zkopírujte adresu URL koncového bodu vaší funkce aktivované protokolem HTTP.

    ![Místní výstup Azure](../media/functions-create-first-function-vs-code/functions-vscode-f5.png)

4. Nahraďte `{functionName}` za `DurableFunctionsOrchestrator` (Jak velká může být moje znalostní báze?).

5. Pomocí některého nástroje, například [Postman](https://www.getpostman.com/) nebo [cURL](https://curl.haxx.se/), odeslat požadavek HTTP POST do koncového bodu adresy URL.

   Odpověď je, že počáteční výsledek z funkce protokolu HTTP nám dáte vědět, trvalý orchestration se úspěšně spustila. Ještě není konečný výsledek orchestraci. Odpověď obsahuje několik užitečných adresy URL. Teď Pojďme zjistit stav orchestraci.

6. Zkopírujte hodnotu adresy URL pro `statusQueryGetUri` a vložte ji do adresního řádku prohlížeče a proveďte požadavek. Případně můžete také dále používat Postman k vydávání požadavek GET.

   Požadavek bude dotaz instance Orchestrace stavu. Měli byste obdržet konečné odpověď, která zobrazuje instance byla dokončena a zahrnuje výstupů nebo důsledků odolné funkce. Vypadá jako: 

    ```json
    {
        "instanceId": "d495cb0ac10d4e13b22729c37e335190",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-11-08T07:07:40Z",
        "lastUpdatedTime": "2018-11-08T07:07:52Z"
    }
    ```

7. Chcete-li zastavit ladění, stiskněte **Shift + F5** ve VS Code.

Po ověření správného fungování funkce na místním počítači je na čase publikovat projekt do Azure.

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../../includes/functions-publish-project-vscode.md)]

## <a name="test-your-function-in-azure"></a>Testování funkce v Azure

1. Zkopírujte adresu URL triggeru HTTP z panelu **Výstup**. Adresa URL, která volá funkci aktivovanou protokolem HTTP, by měla být v následujícím formátu:

        http://<functionappname>.azurewebsites.net/orchestrators/<functionname>

2. Vložte tuto novou adresu URL pro požadavek HTTP do panelu Adresa prohlížeče. Měli byste obdržet stejnou odpověď stav jako před při použití publikované aplikace.

## <a name="next-steps"></a>Další kroky

Používáte Visual Studio Code k vytvoření a publikování aplikace odolné funkce jazyka JavaScript.

> [!div class="nextstepaction"]
> [Další informace o běžných vzorech trvalý – funkce](durable-functions-concepts.md)
