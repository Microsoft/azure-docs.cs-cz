---
title: Vytvoření první trvalé funkce v Azure pomocí JavaScriptu
description: Vytvořte a publikujte funkci trvalosti Azure v JavaScriptu pomocí Visual Studio Code.
author: anthonychu
ms.topic: quickstart
ms.date: 05/07/2020
ms.reviewer: azfuncdf, antchu
ms.custom: devx-track-js
ms.openlocfilehash: f8ffa90ba0f1ac32d4691165fabf3d8eb9fb7605
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91335445"
---
# <a name="create-your-first-durable-function-in-javascript"></a>Vytvoření první odolné funkce v JavaScriptu

*Durable Functions* je rozšíření [Azure Functions](../functions-overview.md) , které umožňuje psát stavové funkce v prostředí bez serveru. Toto rozšíření za vás spravuje stav, kontrolní body a restartování.

V tomto článku se naučíte, jak používat rozšíření Visual Studio Code Azure Functions k místnímu vytvoření a otestování trvalé funkce "Hello World".  Tato funkce provede orchestraci a zřetězení volání dalších funkcí. Kód funkce potom publikujete do Azure.

![Spuštění trvalé funkce v Azure](./media/quickstart-js-vscode/functions-vs-code-complete.png)

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* Nainstalujte [Visual Studio Code](https://code.visualstudio.com/download).

* Instalace rozšíření [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) vs Code

* Ujistěte se, že máte nejnovější verzi [Azure Functions Core Tools](../functions-run-local.md).

* Durable Functions vyžaduje účet služby Azure Storage. Potřebujete předplatné Azure.

* Ujistěte se, že máte nainstalovanou verzi 10. x nebo 12. x [Node.js](https://nodejs.org/) .

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Vytvořit místní projekt 

V této části použijete Visual Studio Code k vytvoření místního projektu Azure Functions. 

1. V Visual Studio Code stisknutím klávesy F1 (nebo Ctrl/Cmd + Shift + P) otevřete paletu příkazů. V paletě příkazů vyhledejte a vyberte `Azure Functions: Create New Project...` .

    ![Funkce Create](media/quickstart-js-vscode/functions-create-project.png)

1. Zvolte prázdné umístění složky pro váš projekt a zvolte **možnost vybrat**.

1. Podle pokynů zadejte následující informace:

    | Výzva | Hodnota | Popis |
    | ------ | ----- | ----------- |
    | Vyberte jazyk pro projekt Function App. | JavaScript | Vytvořte projekt místní Node.js Functions. |
    | Vybrat verzi | Azure Functions v3 | Tato možnost se zobrazí jenom v případě, že už nejsou nainstalované základní nástroje. V takovém případě se při prvním spuštění aplikace nainstalují základní nástroje. |
    | Vyberte šablonu pro funkci prvního projektu. | Prozatím přeskočte | |
    | Vyberte, jak se má projekt otevřít. | Otevřít v aktuálním okně | Znovu otevře VS Code ve vybrané složce. |

V případě potřeby Visual Studio Code nainstaluje Azure Functions Core Tools. Zároveň vytvoří projekt Function App ve složce. Tento projekt obsahuje [host.js](../functions-host-json.md) a [local.settings.jsv](../functions-run-local.md#local-settings-file) konfiguračních souborech.

V kořenové složce je také vytvořena package.jsv souboru.

## <a name="install-the-durable-functions-npm-package"></a>Instalace balíčku Durable Functions npm

Chcete-li pracovat s Durable Functions v aplikaci Node.js Function App, použijte knihovnu s názvem `durable-functions` .

1. K otevření nového terminálu v VS Code použijte nabídku *zobrazení* nebo kombinaci kláves CTRL + SHIFT +.

1. Nainstalujte `durable-functions` balíček npm spuštěním `npm install durable-functions` v kořenovém adresáři aplikace Function App.

## <a name="creating-your-functions"></a>Vytváření funkcí

Základní aplikace Durable Functions obsahuje tři funkce:

* *Funkce Orchestrator* – popisuje pracovní postup, který orchestruje jiné funkce.
* *Funkce aktivity* – volána funkcí Orchestrator, provede práci a případně vrátí hodnotu.
* *Klientská funkce* – běžná funkce Azure, která spouští funkci Orchestrator. V tomto příkladu se používá funkce aktivované protokolem HTTP.

### <a name="orchestrator-function"></a>Funkce Orchestrator

Použijete šablonu k vytvoření trvalého kódu funkce v projektu.

1. V paletě příkazů vyhledejte a vyberte `Azure Functions: Create Function...` .

1. Podle pokynů zadejte následující informace:

    | Výzva | Hodnota | Popis |
    | ------ | ----- | ----------- |
    | Vyberte šablonu pro funkci | Durable Functions Orchestrator | Vytvoření orchestrace Durable Functions |
    | Zadejte název funkce. | HelloOrchestrator | Název trvalé funkce |

Přidali jste nástroj Orchestrator pro koordinaci funkcí aktivity. Otevřete *HelloOrchestrator/index.js* pro zobrazení funkce Orchestrator. Každé volání metody `context.df.callActivity` vyvolá funkci aktivity s názvem `Hello` .

Dále přidáte `Hello` funkci odkazované aktivity.

### <a name="activity-function"></a>Activity – funkce

1. V paletě příkazů vyhledejte a vyberte `Azure Functions: Create Function...` .

1. Podle pokynů zadejte následující informace:

    | Výzva | Hodnota | Popis |
    | ------ | ----- | ----------- |
    | Vyberte šablonu pro funkci | Aktivita Durable Functions | Vytvoření funkce aktivity |
    | Zadejte název funkce. | Hello (Nazdar) | Název funkce aktivity |

Přidali jste `Hello` funkci aktivity, která je vyvolána nástrojem Orchestrator. Otevřete *Hello/index.js* a podívejte se, že se pojmenuje jako vstup a vrátí pozdrav. Funkce Activity je místo, kde provedete akce, jako je například vytvoření databázového volání nebo provedení výpočtu.

Nakonec přidáte funkci aktivovanou protokolem HTTP, která spouští orchestraci.

### <a name="client-function-http-starter"></a>Klientská funkce (HTTP Starter)

1. V paletě příkazů vyhledejte a vyberte `Azure Functions: Create Function...` .

1. Podle pokynů zadejte následující informace:

    | Výzva | Hodnota | Popis |
    | ------ | ----- | ----------- |
    | Vyberte šablonu pro funkci | Durable Functions HTTP Starter | Vytvoření funkce HTTP Starter |
    | Zadejte název funkce. | DurableFunctionsHttpStart | Název funkce aktivity |
    | Úroveň autorizace | Anonymní | Pro účely ukázky povolte funkci, která se má volat bez ověřování. |

Přidali jste funkci aktivovanou protokolem HTTP, která spouští orchestraci. Otevřete *DurableFunctionsHttpStart/index.js* , abyste viděli, že používá `client.startNew` ke spuštění nové orchestrace. Potom používá `client.createCheckStatusResponse` k vrácení odpovědi HTTP obsahující adresy URL, které lze použít k monitorování a správě nové orchestrace.

Nyní máte aplikaci Durable Functions, která se dá spustit místně a nasadit do Azure.

## <a name="test-the-function-locally"></a>Místní testování funkce

Nástroje Azure Functions Core umožňují spouštět projekt Azure Functions na místním počítači pro vývoj. K instalaci těchto nástrojů budete vyzváni při prvním spuštění funkce z Visual Studio Code.

1. Chcete-li otestovat funkci, nastavte zarážku v `Hello` kódu funkce Activity (*Hello/index.js*). Stisknutím klávesy F5 nebo výběrem `Debug: Start Debugging` z palety příkazů spusťte projekt Function App. Výstup z nástrojů Tools se zobrazí na panelu **Terminál**.

    > [!NOTE]
    > Další informace o ladění naleznete v tématu [diagnostika Durable Functions](durable-functions-diagnostics.md#debugging) .

1. Durable Functions vyžaduje, aby se spustil účet Azure Storage. Když VS Code zobrazí výzvu k výběru účtu úložiště, zvolte **Vybrat účet úložiště**.

    ![Vytvoření účtu úložiště](media/quickstart-js-vscode/functions-select-storage.png)

1. Podle pokynů zadejte následující informace, které vám pomůžou vytvořit nový účet úložiště v Azure.

    | Výzva | Hodnota | Popis |
    | ------ | ----- | ----------- |
    | Výběr předplatného | *název vašeho předplatného* | Vyberte své předplatné Azure. |
    | Vyberte účet úložiště. | Vytvoření nového účtu úložiště |  |
    | Zadejte název nového účtu úložiště. | *jedinečný název* | Název účtu úložiště, který se má vytvořit |
    | Výběr skupiny prostředků | *jedinečný název* | Název skupiny prostředků, která se má vytvořit |
    | Výběr umístění | *věřitel* | Vyberte oblast, kterou chcete zavřít. |

1. Na panelu **Terminál** zkopírujte adresu URL koncového bodu vaší funkce aktivované protokolem HTTP.

    ![Místní výstup Azure](media/quickstart-js-vscode/functions-f5.png)

1. Pomocí prohlížeče nebo nástroje, jako je například [post](https://www.getpostman.com/) nebo [kudrlinkou](https://curl.haxx.se/), odešlete požadavek HTTP POST na koncový bod adresy URL. Nahradí poslední segment názvem funkce Orchestrator ( `HelloOrchestrator` ). Adresa URL by měla být podobná `http://localhost:7071/api/orchestrators/HelloOrchestrator` .

   Odpověď je počáteční výsledek z funkce HTTP, která vám umožní zjistit, že trvalá orchestrace byla úspěšně spuštěna. Není ještě konečným výsledkem orchestrace. Odpověď obsahuje několik užitečných adres URL. Prozatím si vydejte dotaz na stav orchestrace.

1. Zkopírujte hodnotu URL pro `statusQueryGetUri` a vložte ji do adresního řádku prohlížeče a spusťte požadavek. Případně můžete i nadále používat metodu post pro vydání žádosti o získání.

   Požadavek se zadotazuje instance orchestrace na stav. Měli byste získat případnou reakci, která ukazuje, že se instance dokončila, a obsahuje výstupy nebo výsledky trvalé funkce. Vypadá takto: 

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

1. Pokud chcete zastavit ladění, stiskněte **SHIFT + F5** v vs Code.

Po ověření správného fungování funkce na místním počítači je na čase publikovat projekt do Azure.

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../../includes/functions-publish-project-vscode.md)]

## <a name="test-your-function-in-azure"></a>Testování funkce v Azure

1. Zkopírujte adresu URL triggeru HTTP z panelu **Výstup**. Adresa URL, která volá funkci spuštěnou protokolem HTTP, by měla být v tomto formátu: `http://<functionappname>.azurewebsites.net/api/orchestrators/HelloOrchestrator`

2. Vložte tuto novou adresu URL pro požadavek HTTP do panelu Adresa prohlížeče. Po použití publikované aplikace byste měli získat stejnou reakci na stav jako předtím.

## <a name="next-steps"></a>Další kroky

Použili jste Visual Studio Code k vytvoření a publikování aplikace s trvalou funkcí JavaScriptu.

> [!div class="nextstepaction"]
> [Další informace o běžných vzorech trvalých funkcí](durable-functions-overview.md#application-patterns)
