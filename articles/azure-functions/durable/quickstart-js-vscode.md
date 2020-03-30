---
title: Vytvořte si první trvalou funkci v Azure pomocí JavaScriptu
description: Vytvořte a publikujte funkci Azure Durable pomocí kódu Sady Visual Studio.
author: anthonychu
ms.topic: quickstart
ms.date: 03/24/2020
ms.reviewer: azfuncdf, antchu
ms.openlocfilehash: 55098daa69d3e878140b20095b0a3e08811269e1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80257644"
---
# <a name="create-your-first-durable-function-in-javascript"></a>Vytvoření první odolné funkce v JavaScriptu

*Trvalé funkce* je rozšíření [funkcí Azure,](../functions-overview.md) které vám umožní psát stavové funkce v prostředí bez serveru. Toto rozšíření za vás spravuje stav, kontrolní body a restartování.

[!INCLUDE [v1-note](../../../includes/functions-durable-v1-tutorial-note.md)]

V tomto článku se dozvíte, jak používat rozšíření Visual Studio Code Azure Functions místně vytvořit a otestovat trvalé funkce "hello world".  Tato funkce bude organizovat a řetězspolečně volání dalších funkcí. Kód funkce potom publikujete do Azure.

![Spuštění trvanlivé funkce v Azure](./media/quickstart-js-vscode/functions-vs-code-complete.png)

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* Nainstalujte [kód sady Visual Studio](https://code.visualstudio.com/download).

* Instalace rozšíření kódu [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) VS

* Ujistěte se, že máte nejnovější verzi [nástrojů Azure Functions Core Tools](../functions-run-local.md).

* Trvalé funkce vyžadují účet úložiště Azure. Potřebujete předplatné Azure.

* Ujistěte se, že máte nainstalovanou verzi 10.x nebo 12.x [souboru Node.js.](https://nodejs.org/)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Vytvoření místního projektu 

V této části použijete visual studio kód k vytvoření projektu místní funkce Azure. 

1. V kódu visual ateliéru otevřete paletu příkazů stisknutím klávesF1 (nebo Ctrl/Cmd+Shift+P). V paletě příkazů vyhledejte `Azure Functions: Create New Project...`a vyberte .

    ![Vytvoření funkce](media/quickstart-js-vscode/functions-create-project.png)

1. Zvolte umístění prázdné složky pro projekt a zvolte **Vybrat**.

1. V následujících výzvách uveďte následující informace:

    | Výzva | Hodnota | Popis |
    | ------ | ----- | ----------- |
    | Výběr jazyka pro projekt aplikace pro funkce | JavaScript | Vytvořte místní projekt Node.js Functions. |
    | Výběr verze | Funkce Azure v3 | Tuto možnost se zobrazí pouze v případě, že základní nástroje ještě nejsou nainstalovány. V takovém případě jsou základní nástroje nainstalovány při prvním spuštění aplikace. |
    | Výběr šablony pro první funkci projektu | Přeskočit pro tuto chvíli | |
    | Vyberte, jak chcete projekt otevřít. | Otevřít v aktuálním okně | Znovu otevře kód VS ve vybrané složce. |

Visual Studio Code nainstaluje nástroje Azure Functions Core Tools, v případě potřeby. Také vytvoří projekt aplikace funkce ve složce. Tento projekt obsahuje konfigurační soubory [host.json](../functions-host-json.md) a [local.settings.json.](../functions-run-local.md#local-settings-file)

Soubor package.json je také vytvořen v kořenové složce.

### <a name="enable-azure-functions-v2-compatibility-mode"></a>Povolení režimu kompatibility funkcí Azure V2

V současné době javascriptové trvalé funkce vyžadují, aby byl povolen režim kompatibility Azure Functions V2.

1. Otevřete *local.settings.json* a upravte nastavení použitá při místním spuštění aplikace.

1. Přidejte nastavení `FUNCTIONS_V2_COMPATIBILITY_MODE` s názvem `true`s hodnotou .

    ```json
    {
        "IsEncrypted": false,
        "Values": {
            "AzureWebJobsStorage": "",
            "FUNCTIONS_WORKER_RUNTIME": "node",
            "FUNCTIONS_V2_COMPATIBILITY_MODE": "true"
        }
    }
    ```

## <a name="install-the-durable-functions-npm-package"></a>Instalace balíčku Durable Functions npm

Chcete-li pracovat s durable funkce v aplikaci funkce Node.js, použijte knihovnu s názvem `durable-functions`.

1. K otevření nového terminálu v kódu VS použijte nabídku *Zobrazení* nebo Ctrl+Shift+' .

1. Nainstalujte `durable-functions` balíček npm spuštěním `npm install durable-functions` v kořenovém adresáři aplikace funkce.

## <a name="creating-your-functions"></a>Vytváření funkcí

Nejzákladnější aplikace Durable Functions obsahuje tři funkce:

* *Funkce Orchestrator* - popisuje pracovní postup, který orchestruje další funkce.
* *Funkce aktivity* - volána funkcí orchestrator, provádí práci a volitelně vrací hodnotu.
* *Funkce klienta* - pravidelná funkce Azure, která spustí funkci orchestrator. Tento příklad používá funkci spouštěnou protokolem HTTP.

### <a name="orchestrator-function"></a>Funkce Orchestrator

Šablonu použijete k vytvoření kódu trvalé funkce v projektu.

1. V paletě příkazů vyhledejte `Azure Functions: Create Function...`a vyberte .

1. V následujících výzvách uveďte následující informace:

    | Výzva | Hodnota | Popis |
    | ------ | ----- | ----------- |
    | Výběr šablony pro vaši funkci | Orchestrátor odolných funkcí | Vytvoření orchestrace trvalých funkcí |
    | Zadání názvu funkce | HelloOrchestrator | Název vaší trvanlivé funkce |

Přidali jste orchestrátor pro koordinaci funkcí aktivity. Otevřete *HelloOrchestrator/index.js,* chcete-li zobrazit funkci orchestrator. Každé volání `context.df.callActivity` vyvolá funkci aktivity s názvem `Hello`.

Dále přidáte funkci odkazované `Hello` aktivity.

### <a name="activity-function"></a>Funkce aktivity

1. V paletě příkazů vyhledejte `Azure Functions: Create Function...`a vyberte .

1. V následujících výzvách uveďte následující informace:

    | Výzva | Hodnota | Popis |
    | ------ | ----- | ----------- |
    | Výběr šablony pro vaši funkci | Aktivita trvalých funkcí | Vytvoření funkce aktivity |
    | Zadání názvu funkce | Dobrý den | Název funkce aktivity |

Přidali jste `Hello` funkci aktivity, která je vyvolána orchestrátorem. Otevřete *Hello/index.js,* abyste viděli, že bere název jako vstup a vrací pozdrav. Funkce aktivity je místo, kde budete provádět akce, jako je například volání databáze nebo provedení výpočtu.

Nakonec přidáte funkci aktivovanou protokolem HTTP, která spustí orchestraci.

### <a name="client-function-http-starter"></a>Klientská funkce (HTTP starter)

1. V paletě příkazů vyhledejte `Azure Functions: Create Function...`a vyberte .

1. V následujících výzvách uveďte následující informace:

    | Výzva | Hodnota | Popis |
    | ------ | ----- | ----------- |
    | Výběr šablony pro vaši funkci | Odolné funkce HTTP startér | Vytvoření počáteční funkce HTTP |
    | Zadání názvu funkce | TrvaléFunkcehttpStart | Název funkce aktivity |
    | Úroveň autorizace | Anonymní | Pro ukázkové účely povolte volání funkce bez ověření |

Přidali jste funkci aktivovanou protokolem HTTP, která spustí orchestraci. Otevřete *durableFunctionsHttpStart/index.js,* abyste `client.startNew` viděli, že se používá ke spuštění nové orchestrace. Pak se `client.createCheckStatusResponse` používá k vrácení odpovědi HTTP obsahující adresy URL, které lze použít ke sledování a správě nové orchestrace.

Teď máte aplikaci Durable Functions, kterou lze spustit místně a nasadit do Azure.

## <a name="test-the-function-locally"></a>Místní testování funkce

Nástroje Azure Functions Core umožňují spouštět projekt Azure Functions na místním počítači pro vývoj. K instalaci těchto nástrojů budete vyzváni při prvním spuštění funkce z Visual Studio Code.

1. Chcete-li otestovat funkci, nastavte `Hello` zarážku v kódu funkce aktivity (*Hello/index.js).* Stisknutím klávesy `Debug: Start Debugging` F5 nebo výběrem z palety příkazů spusťte projekt aplikace funkcí. Výstup z nástrojů Tools se zobrazí na panelu **Terminál**.

    > [!NOTE]
    > Další informace o ladění naleznete v [části Diagnostika trvanlivých funkcí.](durable-functions-diagnostics.md#debugging)

1. Trvalé funkce vyžaduje účet úložiště Azure ke spuštění. Když vás Kód VS vyzve k výběru účtu úložiště, zvolte **Vybrat účet úložiště**.

    ![Vytvoření účtu úložiště](media/quickstart-js-vscode/functions-select-storage.png)

1. V návaznosti na výzvy zadejte následující informace k vytvoření nového účtu úložiště v Azure.

    | Výzva | Hodnota | Popis |
    | ------ | ----- | ----------- |
    | Výběr předplatného | *název předplatného* | Vyberte své předplatné Azure. |
    | Výběr účtu úložiště | Vytvoření nového účtu úložiště |  |
    | Zadejte název nového účtu úložiště. | *jedinečný název* | Název účtu úložiště, který chcete vytvořit |
    | Výběr skupiny prostředků | *jedinečný název* | Název skupiny prostředků, kterou chcete vytvořit |
    | Výběr umístění | *oblast* | Výběr oblasti blízko vás |

1. Na panelu **Terminál** zkopírujte adresu URL koncového bodu vaší funkce aktivované protokolem HTTP.

    ![Místní výstup Azure](media/quickstart-js-vscode/functions-f5.png)

1. Pomocí nástroje, jako je [Postman](https://www.getpostman.com/) nebo [cURL](https://curl.haxx.se/), odešlete požadavek HTTP POST do koncového bodu URL. Nahraďte poslední segment názvem funkce orchestrator (`HelloOrchestrator`). Adresa URL by `http://localhost:7071/api/orchestrators/HelloOrchestrator`měla být podobná adrese .

   Odpověď je počáteční výsledek funkce HTTP umožňuje vědět, že trvalé orchestrace byla úspěšně spuštěna. Ještě není konečným výsledkem orchestrace. Odpověď obsahuje několik užitečných adres URL. Prozatím se podíváme na stav orchestrace.

1. Zkopírujte hodnotu `statusQueryGetUri` adresy URL pro a vložte ji do adresního řádku prohlížeče a proveďte požadavek. Případně můžete také pokračovat v používání Postman k vydání požadavku GET.

   Požadavek bude dotaz orchestrace instance pro stav. Měli byste získat konečnou odpověď, která nám ukazuje, že instance byla dokončena a obsahuje výstupy nebo výsledky trvalé funkce. Vypadá to takto: 

    ```json
    {
        "name": "HelloOrchestrator",
        "instanceId": "9a528a9e926f4b46b7d3deaa134b7e8a",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2020-03-18T21:54:49Z",
        "lastUpdatedTime": "2020-03-18T21:54:54Z"
    }
    ```

1. Ladění zastavíte stisknutím **kláves Shift + F5** v kódu VS.

Po ověření správného fungování funkce na místním počítači je na čase publikovat projekt do Azure.

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../../includes/functions-publish-project-vscode.md)]

### <a name="enable-azure-functions-v2-compatibility-mode"></a>Povolení režimu kompatibility funkcí Azure V2

Stejná kompatibilita Azure Functions V2, kterou jste povolili místně, musí být povolena v aplikaci v Azure.

1. Pomocí palety příkazů vyhledejte `Azure Functions: Edit Setting...`a vyberte .

1. Podle pokynů vyhledejte aplikaci funkcí ve svém předplatném Azure.

1. Vyberte `Create new App Setting...`.

1. Zadejte nový klíč `FUNCTIONS_V2_COMPATIBILITY_MODE`nastavení souboru .

1. Zadejte hodnotu `true`nastavení .

## <a name="test-your-function-in-azure"></a>Testování funkce v Azure

1. Zkopírujte adresu URL triggeru HTTP z panelu **Výstup**. Adresa URL, která volá funkci spuštěnou protokolem HTTP, by měla být v tomto formátu:`http://<functionappname>.azurewebsites.net/orchestrators/HelloOrchestrator`

2. Vložte tuto novou adresu URL pro požadavek HTTP do panelu Adresa prohlížeče. Při používání publikované aplikace byste měli dostat stejnou odpověď na stav jako předtím.

## <a name="next-steps"></a>Další kroky

Ke vytvoření a publikování aplikace s trvalými funkcemi jazyka JavaScript jste použili kód Visual Studio.

> [!div class="nextstepaction"]
> [Informace o běžných vzorcích trvalých funkcí](durable-functions-overview.md#application-patterns)