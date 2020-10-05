---
title: Vytvoření první trvalé funkce v Azure pomocí Pythonu
description: Vytvořte a publikujte funkci trvalosti Azure v Pythonu pomocí Visual Studio Code.
author: anthonychu
ms.topic: quickstart
ms.date: 04/04/2020
ms.reviewer: azfuncdf, antchu
ms.openlocfilehash: 5d624027259212d804ced26a6daaffb853984a98
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91294840"
---
# <a name="create-your-first-durable-function-in-python"></a>Vytvoření první trvalé funkce v Pythonu

*Durable Functions* je rozšíření [Azure Functions](../functions-overview.md) , které umožňuje psát stavové funkce v prostředí bez serveru. Toto rozšíření za vás spravuje stav, kontrolní body a restartování.

V tomto článku se naučíte, jak používat rozšíření Visual Studio Code Azure Functions k místnímu vytvoření a otestování trvalé funkce "Hello World".  Tato funkce provede orchestraci a zřetězení volání dalších funkcí. Kód funkce potom publikujete do Azure.

![Spuštění trvalé funkce v Azure](./media/quickstart-python-vscode/functions-vs-code-complete.png)

## <a name="prerequisites"></a>Předpoklady

Pro absolvování tohoto kurzu potřebujete:

* Nainstalujte [Visual Studio Code](https://code.visualstudio.com/download).

* Nainstalujte rozšíření [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) vs Code.

* Ujistěte se, že máte nejnovější verzi [Azure Functions Core Tools](../functions-run-local.md).

* Durable Functions vyžaduje účet služby Azure Storage. Potřebujete předplatné Azure.

* Ujistěte se, že je v jazyce [Python](https://www.python.org/) nainstalovaná verze 3,6, 3,7 nebo 3,8.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Vytvořit místní projekt 

V této části použijete Visual Studio Code k vytvoření místního projektu Azure Functions. 

1. V Visual Studio Code stisknutím klávesy F1 (nebo Ctrl/Cmd + Shift + P) otevřete paletu příkazů. V paletě příkazů vyhledejte a vyberte `Azure Functions: Create New Project...` .

    ![Vytvoření funkce](media/quickstart-python-vscode/functions-create-project.png)

1. Zvolte prázdné umístění složky pro váš projekt a zvolte **možnost vybrat**.

1. Podle pokynů zadejte následující informace:

    | Výzva | Hodnota | Popis |
    | ------ | ----- | ----------- |
    | Vyberte jazyk pro projekt Function App. | Python | Vytvořte projekt místních funkcí Pythonu. |
    | Vybrat verzi | Azure Functions v3 | Tato možnost se zobrazí jenom v případě, že už nejsou nainstalované základní nástroje. V takovém případě se při prvním spuštění aplikace nainstalují základní nástroje. |
    | Verze Pythonu | Python 3,6, 3,7 nebo 3,8 | VS Code vytvoří virtuální prostředí s vybranou verzí. |
    | Vyberte šablonu pro funkci prvního projektu. | Prozatím přeskočte | |
    | Vyberte, jak se má projekt otevřít. | Otevřít v aktuálním okně | Znovu otevře VS Code ve vybrané složce. |

V případě potřeby Visual Studio Code nainstaluje Azure Functions Core Tools. Zároveň vytvoří projekt Function App ve složce. Tento projekt obsahuje [host.js](../functions-host-json.md) a [local.settings.jsv](../functions-run-local.md#local-settings-file) konfiguračních souborech.

V kořenové složce se vytvoří také soubor requirements.txt. Určuje balíčky Pythonu potřebné ke spuštění vaší aplikace Function App.

## <a name="install-azure-functions-durable-from-pypi"></a>Instalace Azure – funkce – trvalé z PyPI

Při vytváření projektu rozšíření Azure Functions VS Code automaticky vytvořilo virtuální prostředí s vybranou verzí Pythonu. Virtuální prostředí aktivujete v terminálu a nainstalujete některé závislosti vyžadované Azure Functions a Durable Functions.

1. Otevřete `requirements.txt` v editoru a změňte jeho obsah na následující:

    ```
    azure-functions
    azure-functions-durable>=1.0.0b6
    ```

1. V aktuální složce () Otevřete integrovaný terminál editoru `` Ctrl-Shift-` `` .

1. V integrovaném terminálu aktivujte virtuální prostředí v aktuální složce:

    **Linux nebo macOS**

    ```bash
    source .venv/bin/activate
    ```

    **Windows**

    ```powershell
    .venv\scripts\activate
    ```

    ![Aktivovat virtuální prostředí](media/quickstart-python-vscode/activate-venv.png)

1. V integrovaném terminálu, kde je virtuální prostředí aktivováno, použijte PIP k instalaci balíčků, které jste právě definovali:

    ```bash
    python -m pip install -r requirements.txt
    ```

## <a name="create-your-functions"></a>Vytvoření funkcí

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

Přidali jste nástroj Orchestrator pro koordinaci funkcí aktivity. Otevřete *HelloOrchestrator/ \_ \_ init__. py* pro zobrazení funkce Orchestrator. Každé volání metody `context.call_activity` vyvolá funkci aktivity s názvem `Hello` .

Dále přidáte `Hello` funkci odkazované aktivity.

### <a name="activity-function"></a>Activity – funkce

1. V paletě příkazů vyhledejte a vyberte `Azure Functions: Create Function...` .

1. Podle pokynů zadejte následující informace:

    | Výzva | Hodnota | Popis |
    | ------ | ----- | ----------- |
    | Vyberte šablonu pro funkci | Aktivita Durable Functions | Vytvoření funkce aktivity |
    | Zadejte název funkce. | Hello (Nazdar) | Název funkce aktivity |

Přidali jste `Hello` funkci aktivity, která je vyvolána nástrojem Orchestrator. Otevřete *Hello/ \_ \_ init__. py* , abyste viděli, že jako vstup pořizuje název a vrátí pozdrav. Funkce Activity je místo, kde provedete akce, jako je například vytvoření databázového volání nebo provedení výpočtu.

Nakonec přidáte funkci aktivovanou protokolem HTTP, která spouští orchestraci.

### <a name="client-function-http-starter"></a>Klientská funkce (HTTP Starter)

1. V paletě příkazů vyhledejte a vyberte `Azure Functions: Create Function...` .

1. Podle pokynů zadejte následující informace:

    | Výzva | Hodnota | Popis |
    | ------ | ----- | ----------- |
    | Vyberte šablonu pro funkci | Durable Functions HTTP Starter | Vytvoření funkce HTTP Starter |
    | Zadejte název funkce. | DurableFunctionsHttpStart | Název funkce aktivity |
    | Úroveň autorizace | Anonymní | Pro účely ukázky povolte funkci, která se má volat bez ověřování. |

Přidali jste funkci aktivovanou protokolem HTTP, která spouští orchestraci. Otevřete *DurableFunctionsHttpStart/ \_ \_ init__. py* , abyste viděli, že používá `client.start_new` ke spuštění nové orchestrace. Potom používá `client.create_check_status_response` k vrácení odpovědi HTTP obsahující adresy URL, které lze použít k monitorování a správě nové orchestrace.

Nyní máte aplikaci Durable Functions, která se dá spustit místně a nasadit do Azure.

## <a name="test-the-function-locally"></a>Místní testování funkce

Nástroje Azure Functions Core umožňují spouštět projekt Azure Functions na místním počítači pro vývoj. Pokud ho nemáte nainstalovanou, budete vyzváni k instalaci těchto nástrojů při prvním spuštění funkce z Visual Studio Code.

1. Chcete-li otestovat funkci, nastavte zarážku v `Hello` kódu funkce Activity (*Hello/ \_ \_ init__. py*). Stisknutím klávesy F5 nebo výběrem `Debug: Start Debugging` z palety příkazů spusťte projekt Function App. Výstup z nástrojů Tools se zobrazí na panelu **Terminál**.

    > [!NOTE]
    > Další informace o ladění naleznete v tématu [diagnostika Durable Functions](durable-functions-diagnostics.md#debugging) .

1. Durable Functions vyžaduje, aby se spustil účet Azure Storage. Když VS Code zobrazí výzvu k výběru účtu úložiště, zvolte **Vybrat účet úložiště**.

    ![Vytvoření účtu úložiště](media/quickstart-python-vscode/functions-select-storage.png)

1. Podle pokynů zadejte následující informace, které vám pomůžou vytvořit nový účet úložiště v Azure.

    | Výzva | Hodnota | Popis |
    | ------ | ----- | ----------- |
    | Výběr předplatného | *název vašeho předplatného* | Vyberte své předplatné Azure. |
    | Vyberte účet úložiště. | Vytvoření nového účtu úložiště |  |
    | Zadejte název nového účtu úložiště. | *jedinečný název* | Název účtu úložiště, který se má vytvořit |
    | Výběr skupiny prostředků | *jedinečný název* | Název skupiny prostředků, která se má vytvořit |
    | Výběr umístění | *oblast* | Vyberte oblast, kterou chcete zavřít. |

1. Na panelu **Terminál** zkopírujte adresu URL koncového bodu vaší funkce aktivované protokolem HTTP.

    ![Místní výstup Azure](media/quickstart-python-vscode/functions-f5.png)

1. Pomocí prohlížeče nebo nástroje, jako je například [post](https://www.getpostman.com/) nebo [kudrlinkou](https://curl.haxx.se/), odešle požadavek HTTP na koncový bod adresy URL. Nahradí poslední segment názvem funkce Orchestrator ( `HelloOrchestrator` ). Adresa URL by měla být podobná `http://localhost:7071/api/orchestrators/HelloOrchestrator` .

   Odpověď je počáteční výsledek z funkce HTTP, která vám umožní zjistit, že trvalá orchestrace byla úspěšně spuštěna. Není ještě konečným výsledkem orchestrace. Odpověď obsahuje několik užitečných adres URL. Prozatím si vydejte dotaz na stav orchestrace.

1. Zkopírujte hodnotu URL pro `statusQueryGetUri` a vložte ji do adresního řádku prohlížeče a spusťte požadavek. Případně můžete i nadále používat metodu post pro vydání žádosti o získání.

   Požadavek se zadotazuje instance orchestrace na stav. Měli byste získat případnou reakci, která ukazuje dokončení instance a obsahuje výstupy nebo výsledky trvalé funkce. Vypadá takto: 

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

1. Zkopírujte adresu URL triggeru HTTP z panelu **Výstup**. Adresa URL, která volá funkci spuštěnou protokolem HTTP, by měla být v tomto formátu: `http://<functionappname>.azurewebsites.net/orchestrators/HelloOrchestrator`

2. Vložte tuto novou adresu URL pro požadavek HTTP do panelu Adresa prohlížeče. Po použití publikované aplikace byste měli získat stejnou reakci na stav jako předtím.

## <a name="next-steps"></a>Další kroky

Použili jste Visual Studio Code k vytvoření a publikování odolné aplikace Function App v Pythonu.

> [!div class="nextstepaction"]
> [Další informace o běžných vzorech trvalých funkcí](durable-functions-overview.md#application-patterns)
