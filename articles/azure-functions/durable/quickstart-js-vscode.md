---
title: Vytvoření první trvalé funkce v Azure pomocí JavaScriptu
description: Vytvořte a publikujte funkci trvalosti Azure pomocí Visual Studio Code.
author: ColbyTresness
ms.topic: quickstart
ms.date: 11/07/2018
ms.reviewer: azfuncdf, cotresne
ms.openlocfilehash: b0a1d1a9305f6de2a072ee1ded310d8de174436b
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845728"
---
# <a name="create-your-first-durable-function-in-javascript"></a>Vytvoření první trvalé funkce v JavaScriptu

*Durable Functions* je rozšíření [Azure Functions](../functions-overview.md) , které umožňuje psát stavové funkce v prostředí bez serveru. Toto rozšíření za vás spravuje stav, kontrolní body a restartování.

[!INCLUDE [v1-note](../../../includes/functions-durable-v1-tutorial-note.md)]

V tomto článku se naučíte, jak používat rozšíření Visual Studio Code Azure Functions k místnímu vytvoření a otestování trvalé funkce "Hello World".  Tato funkce provede orchestraci a zřetězení volání dalších funkcí. Kód funkce potom publikujete do Azure.

![Spuštění trvalé funkce v Azure](./media/quickstart-js-vscode/functions-vs-code-complete.png)

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* Nainstalujte [Visual Studio Code](https://code.visualstudio.com/download).

* Ujistěte se, že máte nejnovější verzi [Azure Functions Core Tools](../functions-run-local.md).

* Ověřte, že je v počítači se systémem Windows nainstalovaný a spuštěný [emulátor Azure Storage](../../storage/common/storage-use-emulator.md) . Na počítači se systémem Mac nebo Linux musíte použít skutečný účet úložiště Azure.

* Ujistěte se, že je nainstalována verze 8,0 nebo novější verze [Node. js](https://nodejs.org/) .

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../../includes/functions-install-vs-code-extension.md)]

## <a name="create-an-azure-functions-project"></a>Vytvořit místní projekt 

V této části použijete Visual Studio Code k vytvoření místního projektu Azure Functions. 

1. V Visual Studio Code stisknutím klávesy F1 otevřete paletu příkazů. V paletě příkazů vyhledejte a vyberte `Azure Functions: Create new project...`.

1. Zvolte umístění adresáře pro váš pracovní prostor projektu a zvolte **možnost vybrat**.

    > [!NOTE]
    > Tyto kroky jsou navržené tak, aby se dokončily mimo pracovní prostor. V tomto případě nevybírejte složku projektu, která je součástí pracovního prostoru.

1. Podle pokynů zadejte následující informace pro požadovaný jazyk:

    | Výzva | Hodnota | Popis |
    | ------ | ----- | ----------- |
    | Vyberte jazyk pro projekt Function App. | JavaScript | Vytvoří místní projekt funkcí Node. js. |
    | vybrat verzi | Azure Functions v2 | Tato možnost se zobrazí jenom v případě, že už nejsou nainstalované základní nástroje. V takovém případě se při prvním spuštění aplikace nainstalují základní nástroje. |
    | Vyberte šablonu pro funkci prvního projektu. | Trigger HTTP | Vytvoření funkce aktivované protokolem HTTP v nové aplikaci Function App. |
    | Zadejte název funkce. | HttpTrigger | Pokud chcete použít výchozí název, stiskněte klávesu ENTER. |
    | Úroveň autorizace | Funkce | `function` úroveň autorizace vyžaduje, abyste při volání koncového bodu HTTP vaší funkce zadali přístupový klíč. To ztěžuje přístup k nezabezpečenému koncovému bodu. Další informace najdete v tématu [autorizační klíče](../functions-bindings-http-webhook.md#authorization-keys).  |
    | Vyberte, jak se má projekt otevřít. | Přidat do pracovního prostoru | Vytvoří aplikaci Function App v aktuálním pracovním prostoru. |

V případě potřeby Visual Studio Code nainstaluje Azure Functions Core Tools. Také vytvoří projekt Function App v novém pracovním prostoru. Tento projekt obsahuje konfigurační soubory [Host. JSON](../functions-host-json.md) a [Local. Settings. JSON](../functions-run-local.md#local-settings-file) . Vytvoří také složku HttpExample, která obsahuje soubor s [definicí Function. JSON](../functions-reference-node.md#folder-structure) a [soubor index. js](../functions-reference-node.md#exporting-a-function), soubor Node. js, který obsahuje kód funkce.

V kořenové složce se vytvoří také soubor Package. JSON.

## <a name="install-the-durable-functions-npm-package"></a>Instalace balíčku Durable Functions npm

1. Nainstalujte balíček `durable-functions` npm spuštěním `npm install durable-functions` v kořenovém adresáři aplikace Function App.

## <a name="creating-your-functions"></a>Vytváření funkcí

Nyní vytvoříme tři funkce, které potřebujete, abyste mohli začít s Durable Functions: HTTP Starter, Orchestrator a Activity Function. Počáteční protokol HTTP Start spustí celé řešení a nástroj Orchestrator bude odesílat práci do různých funkcí aktivity.

### <a name="http-starter"></a>HTTP Starter

Nejdřív vytvořte funkci aktivovanou protokolem HTTP, která spustí orchestraci trvalé funkce.

1. V *Azure: funkce*klikněte na ikonu **vytvořit funkci** .

    ![Vytvoření funkce](./media/quickstart-js-vscode/create-function.png)

2. Vyberte složku s vaším projektem Function App a vyberte šablonu funkce **Durable FUNCTIONS http Starter** .

    ![Zvolit šablonu HTTP Starter](./media/quickstart-js-vscode/create-function-choose-template.png)

3. Ponechat výchozí název jako `DurableFunctionsHttpStart` a stiskněte klávesu ** **Enter**, pak vyberte **anonymní** ověřování.

    ![Volba anonymního ověřování](./media/quickstart-js-vscode/create-function-anonymous-auth.png)

Nyní jsme vytvořili vstupní bod do naší trvalé funkce. Pojďme přidat Orchestrator.

### <a name="orchestrator"></a>Orchestrator

Nyní vytvoříme nástroj Orchestrator pro koordinaci funkcí aktivity.

1. V *Azure: funkce*klikněte na ikonu **vytvořit funkci** .

    ![Vytvoření funkce](./media/quickstart-js-vscode/create-function.png)

2. Vyberte složku s vaším projektem Function App a vyberte šablonu funkce **Durable Functions Orchestrator** . Ponechte název jako výchozí "DurableFunctionsOrchestrator".

    ![Zvolit šablonu Orchestrator](./media/quickstart-js-vscode/create-function-choose-template.png)

Přidali jsme nástroj Orchestrator pro koordinaci funkcí aktivity. Pojďme teď přidat funkci odkazované aktivity.

### <a name="activity"></a>Aktivita

Nyní vytvoříme funkci aktivity, která bude ve skutečnosti provádět práci řešení.

1. V *Azure: funkce*klikněte na ikonu **vytvořit funkci** .

    ![Vytvoření funkce](./media/quickstart-js-vscode/create-function.png)

2. Vyberte složku s vaším projektem Function App a vyberte šablonu funkce **aktivity Durable Functions** . Ponechte název jako výchozí "Hello".

    ![Výběr šablony aktivity](./media/quickstart-js-vscode/create-function-choose-template.png)

Nyní jsme přidali všechny komponenty potřebné ke spuštění Orchestrace a zřetězení funkcí aktivit.

## <a name="test-the-function-locally"></a>Místní testování funkce

Nástroje Azure Functions Core umožňují spouštět projekt Azure Functions na místním počítači pro vývoj. K instalaci těchto nástrojů budete vyzváni při prvním spuštění funkce z Visual Studio Code.

1. Na počítači se systémem Windows spusťte emulátor Azure Storage a ujistěte se, že vlastnost **AzureWebJobsStorage** souboru *Local. Settings. JSON* je nastavena na hodnotu `UseDevelopmentStorage=true`.

    U emulátoru úložiště 5,8 se ujistěte, že vlastnost **AzureWebJobsSecretStorageType** souboru Local. Settings. JSON je nastavená na `files`. V počítači se systémem Mac nebo Linux musíte nastavit vlastnost **AzureWebJobsStorage** na připojovací řetězec existujícího účtu úložiště Azure. Účet úložiště můžete vytvořit později v tomto článku.

2. Pokud chcete funkci otestovat, nastavte zarážku v kódu funkce a stiskněte klávesu F5, abyste spustili projekt aplikace funkcí. Výstup z nástrojů Tools se zobrazí na panelu **Terminál**. Pokud Durable Functions používáte poprvé, nainstalují se rozšíření Durable Functions a sestavení může trvat několik sekund.

    > [!NOTE]
    > Durable Functions JavaScriptu vyžadují verzi **1.7.0** nebo novější rozšíření **Microsoft. Azure. WebJobs. Extensions. DurableTask** . Spusťte následující příkaz z kořenové složky vaší aplikace Azure Functions pro instalaci rozšíření Durable Functions `func extensions install -p Microsoft.Azure.WebJobs.Extensions.DurableTask -v 1.7.0`

3. Na panelu **Terminál** zkopírujte adresu URL koncového bodu vaší funkce aktivované protokolem HTTP.

    ![Místní výstup Azure](../media/functions-create-first-function-vs-code/functions-vscode-f5.png)

4. Nahraďte `{functionName}` za `DurableFunctionsOrchestrator` (Jak velká může být moje znalostní báze?).

5. Pomocí nástroje, jako je například [post](https://www.getpostman.com/) nebo [kudrlinkou](https://curl.haxx.se/), odešlete požadavek HTTP POST na koncový bod adresy URL.

   Odpověď je počáteční výsledek z funkce HTTP, což nám umožňuje zjistit, že trvalá orchestrace byla úspěšně spuštěna. Není ještě konečným výsledkem orchestrace. Odpověď obsahuje několik užitečných adres URL. Prozatím si vydejte dotaz na stav orchestrace.

6. Zkopírujte hodnotu URL pro `statusQueryGetUri` a vložte ji do adresního řádku prohlížeče a spusťte požadavek. Případně můžete i nadále používat metodu post pro vydání žádosti o získání.

   Požadavek se zadotazuje instance orchestrace na stav. Měli byste získat případnou reakci, která ukazuje, že se instance dokončila, a obsahuje výstupy nebo výsledky trvalé funkce. Vypadá takto: 

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

7. Pokud chcete zastavit ladění, stiskněte **SHIFT + F5** v vs Code.

Po ověření správného fungování funkce na místním počítači je na čase publikovat projekt do Azure.

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../../includes/functions-publish-project-vscode.md)]

## <a name="test-your-function-in-azure"></a>Testování funkce v Azure

1. Zkopírujte adresu URL triggeru HTTP z panelu **Výstup**. Adresa URL, která volá funkci aktivovanou protokolem HTTP, by měla být v následujícím formátu:

        http://<functionappname>.azurewebsites.net/orchestrators/<functionname>

2. Vložte tuto novou adresu URL pro požadavek HTTP do panelu Adresa prohlížeče. Po použití publikované aplikace byste měli získat stejnou reakci na stav jako předtím.

## <a name="next-steps"></a>Další kroky

Použili jste Visual Studio Code k vytvoření a publikování aplikace s trvalou funkcí JavaScriptu.

> [!div class="nextstepaction"]
> [Další informace o běžných vzorech trvalých funkcí](durable-functions-overview.md#application-patterns)