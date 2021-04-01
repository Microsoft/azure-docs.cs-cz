---
title: 'Vytvoření první trvalé funkce v Azure pomocí jazyka C #'
description: Vytvořte a publikujte funkci trvalosti Azure pomocí sady Visual Studio nebo Visual Studio Code.
author: jeffhollan
ms.topic: quickstart
ms.date: 03/18/2020
ms.author: azfuncdf
zone_pivot_groups: code-editors-set-one
ms.openlocfilehash: d5b6ecc12cee983cee0772da8b6f8f26a3b5a8ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96008160"
---
# <a name="create-your-first-durable-function-in-c"></a>Vytvoření první trvalé funkce v jazyce C\#

*Durable Functions* je rozšíření [Azure Functions](../functions-overview.md) , které umožňuje psát stavové funkce v prostředí bez serveru. Toto rozšíření za vás spravuje stav, kontrolní body a restartování.

::: zone pivot="code-editor-vscode"

V tomto článku se naučíte, jak používat Visual Studio Code k místnímu vytvoření a otestování trvalé funkce "Hello World".  Tato funkce orchestruje a zřetězí volání dalších funkcí. Kód funkce potom publikujete do Azure. Tyto nástroje jsou dostupné jako součást [rozšíření VS Code Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions).

![Snímek obrazovky ukazuje Visual Studio Code okno s trvalou funkcí.](./media/durable-functions-create-first-csharp/functions-vscode-complete.png)

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* Nainstalujte [Visual Studio Code](https://code.visualstudio.com/download).

* Nainstalujte následující rozšíření VS Code:
    - [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
    - [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)

* Ujistěte se, že máte nejnovější verzi [Azure Functions Core Tools](../functions-run-local.md).

* Durable Functions vyžaduje účet služby Azure Storage. Potřebujete předplatné Azure.

* Ujistěte se, že máte nainstalovanou verzi 3,1 nebo novější verzi [.NET Core SDK](https://dotnet.microsoft.com/download) .

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Vytvořit místní projekt 

V této části použijete Visual Studio Code k vytvoření místního projektu Azure Functions. 

1. V Visual Studio Code stisknutím klávesy F1 (nebo Ctrl/Cmd + Shift + P) otevřete paletu příkazů. V paletě příkazů vyhledejte a vyberte `Azure Functions: Create New Project...` .

    ![Vytvoření projektu funkce](media/durable-functions-create-first-csharp/functions-vscode-create-project.png)

1. Zvolte prázdné umístění složky pro váš projekt a zvolte **možnost vybrat**.

1. Podle pokynů zadejte následující informace:

    | Výzva | Hodnota | Popis |
    | ------ | ----- | ----------- |
    | Vyberte jazyk pro projekt Function App. | C# | Vytvořte místní projekt C# Functions. |
    | Vybrat verzi | Azure Functions v3 | Tato možnost se zobrazí jenom v případě, že už nejsou nainstalované základní nástroje. V takovém případě se při prvním spuštění aplikace nainstalují základní nástroje. |
    | Vyberte šablonu pro funkci prvního projektu. | Prozatím přeskočte | |
    | Vyberte, jak se má projekt otevřít. | Otevřít v aktuálním okně | Znovu otevře VS Code ve vybrané složce. |

V případě potřeby Visual Studio Code nainstaluje Azure Functions Core Tools. Zároveň vytvoří projekt Function App ve složce. Tento projekt obsahuje [host.js](../functions-host-json.md) a [local.settings.jsv](../functions-run-local.md#local-settings-file) konfiguračních souborech.

## <a name="add-functions-to-the-app"></a>Přidání funkcí do aplikace

Následující kroky používají šablonu k vytvoření trvalého kódu funkce v projektu.

1. V paletě příkazů vyhledejte a vyberte `Azure Functions: Create Function...` .

1. Podle pokynů zadejte následující informace:

    | Výzva | Hodnota | Popis |
    | ------ | ----- | ----------- |
    | Vyberte šablonu pro funkci | DurableFunctionsOrchestration | Vytvoření orchestrace Durable Functions |
    | Zadejte název funkce. | HelloOrchestration | Název třídy, ve které jsou vytvářeny funkce |
    | Zadejte obor názvů. | Company. Function | Obor názvů pro generovanou třídu |

1. Když VS Code zobrazí výzvu k výběru účtu úložiště, zvolte **Vybrat účet úložiště**. Podle pokynů zadejte následující informace, které vám pomůžou vytvořit nový účet úložiště v Azure.

    | Výzva | Hodnota | Popis |
    | ------ | ----- | ----------- |
    | Výběr předplatného | *název vašeho předplatného* | Vyberte své předplatné Azure. |
    | Vyberte účet úložiště. | Vytvoření nového účtu úložiště |  |
    | Zadejte název nového účtu úložiště. | *jedinečný název* | Název účtu úložiště, který se má vytvořit |
    | Výběr skupiny prostředků | *jedinečný název* | Název skupiny prostředků, která se má vytvořit |
    | Výběr umístění | *věřitel* | Vyberte oblast, kterou chcete zavřít. |

Do projektu se přidá třída obsahující nové funkce. VS Code taky přidá připojovací řetězec účtu úložiště, který se má *local.settings.jsna* , a odkaz na [`Microsoft.Azure.WebJobs.Extensions.DurableTask`](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) balíček NuGet na soubor projektu *. csproj* .

Otevřete nový soubor *HelloOrchestration. cs* pro zobrazení obsahu. Tato trvalá funkce je příklad řetězení jednoduchých funkcí s následujícími metodami:  

| Metoda | FunctionName | Description |
| -----  | ------------ | ----------- |
| **`RunOrchestrator`** | `HelloOrchestration` | Spravuje trvalou orchestraci. V tomto případě se orchestrace spustí, vytvoří seznam a přidá výsledek tří volání funkcí do seznamu.  Po dokončení tří volání funkce vrátí seznam. |
| **`SayHello`** | `HelloOrchestration_Hello` | Funkce vrátí Hello. Jedná se o funkci, která obsahuje orchestraci obchodní logiky. |
| **`HttpStart`** | `HelloOrchestration_HttpStart` | [Funkce aktivovaná protokolem HTTP](../functions-bindings-http-webhook.md) , která spustí instanci Orchestrace a vrátí odpověď stavu kontroly. |

Teď, když jste vytvořili projekt funkcí a trvalou funkci, můžete ho otestovat na místním počítači.

## <a name="test-the-function-locally"></a>Místní testování funkce

Nástroje Azure Functions Core umožňují spouštět projekt Azure Functions na místním počítači pro vývoj. K instalaci těchto nástrojů budete vyzváni při prvním spuštění funkce z Visual Studio Code.

1. Chcete-li otestovat funkci, nastavte zarážku v `SayHello` kódu funkce Activity a stisknutím klávesy F5 spusťte projekt Function App. Výstup z nástrojů Tools se zobrazí na panelu **Terminál**.

    > [!NOTE]
    > Další informace o ladění naleznete v tématu [diagnostika Durable Functions](durable-functions-diagnostics.md#debugging) .

1. Na panelu **Terminál** zkopírujte adresu URL koncového bodu vaší funkce aktivované protokolem HTTP.

    ![Místní výstup Azure](media/durable-functions-create-first-csharp/functions-vscode-f5.png)

1. Pomocí nástroje, jako je například [post](https://www.getpostman.com/) nebo [kudrlinkou](https://curl.haxx.se/), odešlete požadavek HTTP POST na koncový bod adresy URL.

   Odpověď je počáteční výsledek z funkce HTTP, což nám umožňuje zjistit, že trvalá orchestrace byla úspěšně spuštěna. Není ještě konečným výsledkem orchestrace. Odpověď obsahuje několik užitečných adres URL. Prozatím si vydejte dotaz na stav orchestrace.

1. Zkopírujte hodnotu URL pro `statusQueryGetUri` a vložte ji do adresního řádku prohlížeče a spusťte požadavek. Případně můžete i nadále používat metodu post pro vydání žádosti o získání.

   Požadavek se zadotazuje instance orchestrace na stav. Měli byste získat případnou reakci, která ukazuje, že se instance dokončila, a obsahuje výstupy nebo výsledky trvalé funkce. Vypadá takto: 

    ```json
    {
        "name": "HelloOrchestration",
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

1. Zkopírujte adresu URL triggeru HTTP z panelu **Výstup**. Adresa URL, která volá funkci aktivovanou protokolem HTTP, by měla být v následujícím formátu:

    `https://<functionappname>.azurewebsites.net/api/HelloOrchestration_HttpStart`

1. Vložte tuto novou adresu URL pro požadavek HTTP do panelu Adresa prohlížeče. Po použití publikované aplikace byste měli získat stejnou reakci na stav jako předtím.

## <a name="next-steps"></a>Další kroky

Použili jste Visual Studio Code k vytvoření a publikování aplikace funkce, která je v jazyce C#.

> [!div class="nextstepaction"]
> [Další informace o běžných vzorech trvalých funkcí](durable-functions-overview.md#application-patterns)

::: zone-end

::: zone pivot="code-editor-visualstudio"

V tomto článku se naučíte, jak se v rámci sady Visual Studio 2019 místně vytvoří a otestuje trvalá funkce Hello World.  Tato funkce orchestruje a zřetězí volání dalších funkcí. Kód funkce potom publikujete do Azure. Tyto nástroje jsou k dispozici v rámci úlohy vývoje Azure v aplikaci Visual Studio 2019.

![Snímek obrazovky ukazuje okno sady Visual Studio 2019 s trvalou funkcí.](./media/durable-functions-create-first-csharp/functions-vs-complete.png)

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* Nainstalujte sadu [Visual Studio 2019](https://visualstudio.microsoft.com/vs/). Ujistěte se, že je nainstalovaná také úloha **vývoj pro Azure** . Visual Studio 2017 také podporuje vývoj Durable Functions, ale uživatelské rozhraní a postup se liší.

* Ověřte, že je nainstalovaný a spuštěný [emulátor Azure Storage](../../storage/common/storage-use-emulator.md) .

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function-app-project"></a>Vytvoření projektu aplikace funkcí

Šablona Azure Functions vytvoří projekt, který se dá publikovat do aplikace Function App v Azure. Aplikace Function App umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování, škálování a sdílení prostředků.

1. V sadě Visual Studio zvolte v nabídce **Soubor** možnost **Nový** > **Projekt**.

1. V dialogovém okně **vytvořit nový projekt** vyhledejte `functions` , vyberte šablonu **Azure Functions** a vyberte **Další**. 

    ![Dialogové okno Nový projekt pro vytvoření funkce v sadě Visual Studio](./media/durable-functions-create-first-csharp/functions-vs-new-project.png)

1. Zadejte **název projektu** a vyberte **OK**. Název projektu musí být platný jako obor názvů C#, proto nepoužívejte podtržítka, spojovníky nebo žádné jiné nealfanumerické znaky.

1. V části **vytvořit novou aplikaci Azure Functions** použijte nastavení zadaná v tabulce, která následuje po obrázku.

    ![Vytvoření nového dialogového okna aplikace Azure Functions v aplikaci Visual Studio](./media/durable-functions-create-first-csharp/functions-vs-new-function.png)

    | Nastavení      | Navrhovaná hodnota  | Popis                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Verze** | Azure Functions 3,0 <br />(.NET Core) | Vytvoří projekt funkce, který používá běhový modul Azure Functions verze 3,0, který podporuje .NET Core 3,1. Další informace najdete v tématu s [přehledem verzí modulu runtime Azure Functions](../functions-versions.md).   |
    | **Šablona** | Obsahovat | Vytvoří prázdnou aplikaci Function App. |
    | **Účet úložiště**  | Emulátor úložiště | Pro trvalou správu stavu funkce se vyžaduje účet úložiště. |

4. Vyberte **vytvořit** k vytvoření prázdného projektu funkce. Tento projekt má základní konfigurační soubory potřebné ke spuštění vašich funkcí.

## <a name="add-functions-to-the-app"></a>Přidání funkcí do aplikace

Následující kroky používají šablonu k vytvoření trvalého kódu funkce v projektu.

1. Klikněte pravým tlačítkem na projekt v aplikaci Visual Studio a vyberte **Přidat**  >  **novou funkci Azure Functions**.

    ![Přidat novou funkci](./media/durable-functions-create-first-csharp/functions-vs-add-function.png)

1. Ověřte, že je v nabídce Přidat vybraná možnost **Azure Function** , zadejte název souboru C# a pak vyberte **Přidat**.

1. Vyberte šablonu **orchestrace Durable Functions** a pak vyberte **OK** .

    ![Vybrat trvanlivé šablonu](./media/durable-functions-create-first-csharp/functions-vs-select-template.png)

Do aplikace se přidá nová trvalá funkce.  Otevřete nový soubor. cs pro zobrazení obsahu. Tato trvalá funkce je příklad řetězení jednoduchých funkcí s následujícími metodami:  

| Metoda | FunctionName | Description |
| -----  | ------------ | ----------- |
| **`RunOrchestrator`** | `<file-name>` | Spravuje trvalou orchestraci. V tomto případě se orchestrace spustí, vytvoří seznam a přidá výsledek tří volání funkcí do seznamu.  Po dokončení tří volání funkce vrátí seznam. |
| **`SayHello`** | `<file-name>_Hello` | Funkce vrátí Hello. Jedná se o funkci, která obsahuje orchestraci obchodní logiky. |
| **`HttpStart`** | `<file-name>_HttpStart` | [Funkce aktivovaná protokolem HTTP](../functions-bindings-http-webhook.md) , která spustí instanci Orchestrace a vrátí odpověď stavu kontroly. |

Teď, když jste vytvořili projekt funkcí a trvalou funkci, můžete ho otestovat na místním počítači.

## <a name="test-the-function-locally"></a>Místní testování funkce

Nástroje Azure Functions Core umožňují spouštět projekt Azure Functions na místním počítači pro vývoj. K instalaci těchto nástrojů budete vyzváni při prvním spuštění funkce ze sady Visual Studio.

1. Pokud chcete funkci otestovat, stiskněte F5. Po výzvě přijměte požadavek ze sady Visual Studio na stažení a instalaci nástrojů Azure Functions Core (CLI). Může být také potřeba povolit výjimku brány firewall, aby nástroje mohly zpracovávat požadavky HTTP.

2. Zkopírujte adresu URL vaší funkce z výstupu modulu runtime služby Azure Functions.

    ![Místní modul runtime Azure](./media/durable-functions-create-first-csharp/functions-vs-debugging.png)

3. Vložte adresu URL požadavku HTTP do adresního řádku prohlížeče a spusťte požadavek. Následuje ukázka odezvy na místní požadavek GET vrácené funkcí v prohlížeči:

    ![Snímek obrazovky se zobrazením okna prohlížeče, které statusQueryGetUri vyvolalo.](./media/durable-functions-create-first-csharp/functions-vs-status.png)

    Odpověď je počáteční výsledek z funkce HTTP, což nám umožňuje zjistit, že trvalá orchestrace byla úspěšně spuštěna.  Není ještě konečným výsledkem orchestrace.  Odpověď obsahuje několik užitečných adres URL.  Prozatím si vydejte dotaz na stav orchestrace.

4. Zkopírujte hodnotu URL pro `statusQueryGetUri` a vložte ji do adresního řádku prohlížeče a spusťte požadavek.

    Požadavek se zadotazuje instance orchestrace na stav. Měli byste získat případnou reakci, která vypadá nějak takto.  Tento výstup ukazuje, že jsme instanci dokončili, a obsahuje výstupy nebo výsledky trvalé funkce.

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
        "createdTime": "2019-11-02T07:07:40Z",
        "lastUpdatedTime": "2019-11-02T07:07:52Z"
    }
    ```

5. Pokud chcete zastavit ladění, stiskněte **Shift + F5**.

Po ověření správného fungování funkce na místním počítači je na čase publikovat projekt do Azure.

## <a name="publish-the-project-to-azure"></a>Publikování projektu do Azure

Před publikováním projektu musíte mít v předplatném Azure aplikaci funkcí. Aplikaci funkcí můžete vytvořit přímo v sadě Visual Studio.

[!INCLUDE [Publish the project to Azure](../../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Testování funkce v Azure

1. Zkopírujte základní adresu URL aplikace funkcí ze stránky Publikovat profil. Nahraďte část adresy URL použité při místním testování funkce, která obsahuje `localhost:port`, novou základní adresou URL.

    Adresa URL, která volá aktivační událost trvalé funkce HTTP, by měla být v následujícím formátu:

    `https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>_HttpStart`

2. Vložte tuto novou adresu URL pro požadavek HTTP do panelu Adresa prohlížeče. Po použití publikované aplikace byste měli získat stejnou reakci na stav jako předtím.

## <a name="next-steps"></a>Další kroky

Pomocí sady Visual Studio jste vytvořili a publikovali aplikaci funkcí s trvalým prostředím C#.

> [!div class="nextstepaction"]
> [Další informace o běžných vzorech trvalých funkcí](durable-functions-overview.md#application-patterns)

::: zone-end
