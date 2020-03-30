---
title: 'Vytvořte si první odolnou funkci v Azure pomocí Jazyka C #'
description: Vytvořte a publikujte funkci Azure Durable pomocí visual studia nebo kódu Sady Visual Studio.
author: jeffhollan
ms.topic: quickstart
ms.date: 03/18/2020
ms.author: azfuncdf
zone_pivot_groups: code-editors-set-one
ms.openlocfilehash: eda3afdf8deb3336cd0c5293c2422e694caa69c8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80132795"
---
# <a name="create-your-first-durable-function-in-c"></a>Vytvořte si první odolnou funkci v C\#

*Trvalé funkce* je rozšíření [funkcí Azure,](../functions-overview.md) které vám umožní psát stavové funkce v prostředí bez serveru. Toto rozšíření za vás spravuje stav, kontrolní body a restartování.

::: zone pivot="code-editor-vscode"

V tomto článku se dozvíte, jak pomocí kódu sady Visual Studio místně vytvořit a otestovat "hello world" trvalé funkce.  Tato funkce orchestruje a řetězy společně volání dalších funkcí. Kód funkce potom publikujete do Azure. Tyto nástroje jsou k dispozici jako součást rozšíření VS Code [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions).

![Spuštění trvanlivé funkce v Azure](./media/durable-functions-create-first-csharp/functions-vscode-complete.png)

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* Nainstalujte [kód sady Visual Studio](https://code.visualstudio.com/download).

* Nainstalujte následující rozšíření v kódu VS:
    - [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
    - [C #](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)

* Ujistěte se, že máte nejnovější verzi [nástrojů Azure Functions Core Tools](../functions-run-local.md).

* Trvalé funkce vyžadují účet úložiště Azure. Potřebujete předplatné Azure.

* Ujistěte se, že máte nainstalovanou verzi 3.1 nebo novější verzi sady [.NET Core SDK.](https://dotnet.microsoft.com/download)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Vytvoření místního projektu 

V této části použijete visual studio kód k vytvoření projektu místní funkce Azure. 

1. V kódu visual ateliéru otevřete paletu příkazů stisknutím klávesF1 (nebo Ctrl/Cmd+Shift+P). V paletě příkazů vyhledejte `Azure Functions: Create New Project...`a vyberte .

    ![Vytvoření projektu funkce](media/durable-functions-create-first-csharp/functions-vscode-create-project.png)

1. Zvolte umístění prázdné složky pro projekt a zvolte **Vybrat**.

1. V následujících výzvách uveďte následující informace:

    | Výzva | Hodnota | Popis |
    | ------ | ----- | ----------- |
    | Výběr jazyka pro projekt aplikace pro funkce | C# | Vytvořte místní c# funkce projektu. |
    | Výběr verze | Funkce Azure v3 | Tuto možnost se zobrazí pouze v případě, že základní nástroje ještě nejsou nainstalovány. V takovém případě jsou základní nástroje nainstalovány při prvním spuštění aplikace. |
    | Výběr šablony pro první funkci projektu | Přeskočit pro tuto chvíli | |
    | Vyberte, jak chcete projekt otevřít. | Otevřít v aktuálním okně | Znovu otevře kód VS ve vybrané složce. |

Visual Studio Code nainstaluje nástroje Azure Functions Core Tools, v případě potřeby. Také vytvoří projekt aplikace funkce ve složce. Tento projekt obsahuje konfigurační soubory [host.json](../functions-host-json.md) a [local.settings.json.](../functions-run-local.md#local-settings-file)

## <a name="add-functions-to-the-app"></a>Přidání funkcí do aplikace

Následující kroky používají šablonu k vytvoření kódu trvalé funkce v projektu.

1. V paletě příkazů vyhledejte `Azure Functions: Create Function...`a vyberte .

1. V následujících výzvách uveďte následující informace:

    | Výzva | Hodnota | Popis |
    | ------ | ----- | ----------- |
    | Výběr šablony pro vaši funkci | DurableFunctionsOrchestraation | Vytvoření orchestrace trvalých funkcí |
    | Zadání názvu funkce | HelloOrchestration | Název třídy, ve které jsou vytvářeny funkce |
    | Poskytnutí oboru názvů | Společnost.Function | Obor názvů pro vygenerovanou třídu |

1. Když vás Kód VS vyzve k výběru účtu úložiště, zvolte **Vybrat účet úložiště**. V návaznosti na výzvy zadejte následující informace k vytvoření nového účtu úložiště v Azure.

    | Výzva | Hodnota | Popis |
    | ------ | ----- | ----------- |
    | Výběr předplatného | *název předplatného* | Vyberte své předplatné Azure. |
    | Výběr účtu úložiště | Vytvoření nového účtu úložiště |  |
    | Zadejte název nového účtu úložiště. | *jedinečný název* | Název účtu úložiště, který chcete vytvořit |
    | Výběr skupiny prostředků | *jedinečný název* | Název skupiny prostředků, kterou chcete vytvořit |
    | Výběr umístění | *oblast* | Výběr oblasti blízko vás |

Třída obsahující nové funkce je přidán do projektu. VS Code také přidá připojovací řetězec účtu úložiště do *local.settings.json* a odkaz na balíček [`Microsoft.Azure.WebJobs.Extensions.DurableTask`](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) NuGet do souboru projektu *.csproj.*

Otevřete nový *soubor HelloOrchestration.cs* a zobrazte obsah. Tato trvalá funkce je jednoduchýpříklad zřetězení funkcí s následujícími metodami:  

| Metoda | Název funkce | Popis |
| -----  | ------------ | ----------- |
| **`RunOrchestrator`** | `HelloOrchestration` | Spravuje trvalé orchestraci. V tomto případě orchestrace spustí, vytvoří seznam a přidá výsledek tři funkce volání do seznamu.  Po dokončení tři volání funkce vrátí seznam. |
| **`SayHello`** | `HelloOrchestration_Hello` | Funkce vrátí hello. Je to funkce, která obsahuje obchodní logiku, která je orchestrována. |
| **`HttpStart`** | `HelloOrchestration_HttpStart` | [Funkce spouštěná protokolem HTTP,](../functions-bindings-http-webhook.md) která spustí instanci orchestraci a vrátí odpověď na stav kontroly. |

Nyní, když jste vytvořili projekt funkce a trvalou funkci, můžete jej otestovat v místním počítači.

## <a name="test-the-function-locally"></a>Místní testování funkce

Nástroje Azure Functions Core umožňují spouštět projekt Azure Functions na místním počítači pro vývoj. K instalaci těchto nástrojů budete vyzváni při prvním spuštění funkce z Visual Studio Code.

1. Chcete-li otestovat funkci, nastavte `SayHello` zarážku v kódu funkce aktivity a stisknutím klávesy F5 spusťte projekt aplikace funkce. Výstup z nástrojů Tools se zobrazí na panelu **Terminál**.

    > [!NOTE]
    > Další informace o ladění naleznete v [části Diagnostika trvanlivých funkcí.](durable-functions-diagnostics.md#debugging)

1. Na panelu **Terminál** zkopírujte adresu URL koncového bodu vaší funkce aktivované protokolem HTTP.

    ![Místní výstup Azure](media/durable-functions-create-first-csharp/functions-vscode-f5.png)

1. Pomocí nástroje, jako je [Postman](https://www.getpostman.com/) nebo [cURL](https://curl.haxx.se/), odešlete požadavek HTTP POST do koncového bodu URL.

   Odpověď je počáteční výsledek z funkce HTTP nám vědět, trvalé orchestrace úspěšně spuštěna. Ještě není konečným výsledkem orchestrace. Odpověď obsahuje několik užitečných adres URL. Prozatím se podíváme na stav orchestrace.

1. Zkopírujte hodnotu `statusQueryGetUri` adresy URL pro a vložte ji do adresního řádku prohlížeče a proveďte požadavek. Případně můžete také pokračovat v používání Postman k vydání požadavku GET.

   Požadavek bude dotaz orchestrace instance pro stav. Měli byste získat konečnou odpověď, která nám ukazuje, že instance byla dokončena a obsahuje výstupy nebo výsledky trvalé funkce. Vypadá to takto: 

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

1. Ladění zastavíte stisknutím **kláves Shift + F5** v kódu VS.

Po ověření správného fungování funkce na místním počítači je na čase publikovat projekt do Azure.

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../../includes/functions-publish-project-vscode.md)]

## <a name="test-your-function-in-azure"></a>Testování funkce v Azure

1. Zkopírujte adresu URL triggeru HTTP z panelu **Výstup**. Adresa URL, která volá funkci aktivovanou protokolem HTTP, by měla být v následujícím formátu:

        https://<functionappname>.azurewebsites.net/api/HelloOrchestration_HttpStart

1. Vložte tuto novou adresu URL pro požadavek HTTP do panelu Adresa prohlížeče. Při používání publikované aplikace byste měli dostat stejnou odpověď na stav jako předtím.

## <a name="next-steps"></a>Další kroky

Použili jste Visual Studio Kód k vytvoření a publikování aplikace c# trvalé funkce.

> [!div class="nextstepaction"]
> [Informace o běžných vzorcích trvalých funkcí](durable-functions-overview.md#application-patterns)

::: zone-end

::: zone pivot="code-editor-visualstudio"

V tomto článku se dozvíte, jak Visual Studio 2019 místně vytvořit a otestovat "hello world" trvalé funkce.  Tato funkce orchestruje a řetězy společně volání dalších funkcí. Kód funkce potom publikujete do Azure. Tyto nástroje jsou dostupné jako součást úlohy vývoje Azure ve Visual Studiu 2019.

![Spuštění trvanlivé funkce v Azure](./media/durable-functions-create-first-csharp/functions-vs-complete.png)

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* Nainstalujte sadu [Visual Studio 2019](https://visualstudio.microsoft.com/vs/). Ujistěte se, že je také nainstalována úloha **vývoje Azure.** Visual Studio 2017 také podporuje vývoj trvanlivé funkce, ale ui a kroky se liší.

* Ověřte, že máte nainstalovaný a spuštěný [emulátor úložiště Azure.](../../storage/common/storage-use-emulator.md)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function-app-project"></a>Vytvoření projektu aplikace funkcí

Šablona Azure Functions vytvoří projekt, který se dá publikovat do aplikace funkce v Azure. Aplikace funkce umožňuje seskupit funkce jako logická jednotka pro snadnější správu, nasazení, škálování a sdílení prostředků.

1. V Sadě Visual Studio **vyberte** > nový**projekt** z nabídky **Soubor.**

1. V **dialogovém** okně Vytvořit nový `functions`projekt vyhledejte , zvolte šablonu **Azure Functions** a vyberte **Další**. 

    ![Dialogové okno Nový projekt pro vytvoření funkce v sadě Visual Studio](./media/durable-functions-create-first-csharp/functions-vs-new-project.png)

1. Zadejte **název projektu** projektu a vyberte **OK**. Název projektu musí být platný jako obor názvů jazyka C#, proto nepoužívejte podtržítka, pomlčky ani jiné nealfanumerické znaky.

1. V **části Vytvoření nové aplikace Azure Functions**použijte nastavení zadaná v tabulce, která následuje za bitovou kopií.

    ![Vytvoření nového dialogového okna aplikace Azure Functions v Sadě Visual Studio](./media/durable-functions-create-first-csharp/functions-vs-new-function.png)

    | Nastavení      | Navrhovaná hodnota  | Popis                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Verze** | Funkce Azure 3.0 <br />(.NET Core) | Vytvoří projekt funkce, který používá runtime verze 3.0 Azure Functions, který podporuje rozhraní .NET Core 3.1. Další informace najdete v tématu s [přehledem verzí modulu runtime Azure Functions](../functions-versions.md).   |
    | **Šablony** | Prázdné | Vytvoří prázdnou aplikaci funkce. |
    | **Účet úložiště**  | Emulátor úložiště | Účet úložiště je vyžadován pro správu stavu trvalé funkce. |

4. Vyberte **Vytvořit,** chcete-li vytvořit prázdný projekt funkce. Tento projekt má základní konfigurační soubory potřebné ke spuštění funkcí.

## <a name="add-functions-to-the-app"></a>Přidání funkcí do aplikace

Následující kroky používají šablonu k vytvoření kódu trvalé funkce v projektu.

1. Klikněte pravým tlačítkem myši na projekt v sadě Visual Studio a vyberte **Přidat** > **novou funkci Azure**.

    ![Přidat novou funkci](./media/durable-functions-create-first-csharp/functions-vs-add-function.png)

1. Ověřte, zda je **funkce Azure** vybraná v nabídce Přidat, zadejte název souboru C# a pak vyberte **Přidat**.

1. Vyberte šablonu **Orchestrace trvanlivé funkce** a pak vyberte **Ok**

    ![Vybrat odolnou šablonu](./media/durable-functions-create-first-csharp/functions-vs-select-template.png)

Do aplikace se přidá nová trvalá funkce.  Otevřete nový soubor CS a zobrazte obsah. Tato trvalá funkce je jednoduchýpříklad zřetězení funkcí s následujícími metodami:  

| Metoda | Název funkce | Popis |
| -----  | ------------ | ----------- |
| **`RunOrchestrator`** | `<file-name>` | Spravuje trvalé orchestraci. V tomto případě orchestrace spustí, vytvoří seznam a přidá výsledek tři funkce volání do seznamu.  Po dokončení tři volání funkce vrátí seznam. |
| **`SayHello`** | `<file-name>_Hello` | Funkce vrátí hello. Je to funkce, která obsahuje obchodní logiku, která je orchestrována. |
| **`HttpStart`** | `<file-name>_HttpStart` | [Funkce spouštěná protokolem HTTP,](../functions-bindings-http-webhook.md) která spustí instanci orchestraci a vrátí odpověď na stav kontroly. |

Nyní, když jste vytvořili projekt funkce a trvalou funkci, můžete jej otestovat v místním počítači.

## <a name="test-the-function-locally"></a>Místní testování funkce

Nástroje Azure Functions Core umožňují spouštět projekt Azure Functions na místním počítači pro vývoj. K instalaci těchto nástrojů budete vyzváni při prvním spuštění funkce ze sady Visual Studio.

1. Pokud chcete funkci otestovat, stiskněte F5. Po výzvě přijměte požadavek ze sady Visual Studio na stažení a instalaci nástrojů Azure Functions Core (CLI). Může být také potřeba povolit výjimku brány firewall, aby nástroje mohly zpracovávat požadavky HTTP.

2. Zkopírujte adresu URL vaší funkce z výstupu modulu runtime služby Azure Functions.

    ![Místní modul runtime Azure](./media/durable-functions-create-first-csharp/functions-vs-debugging.png)

3. Vložte adresu URL pro požadavek HTTP do adresního řádku prohlížeče a požadavek spusťte. Následuje ukázka odezvy na místní požadavek GET vrácené funkcí v prohlížeči:

    ![Odezva místního hostitele funkce v prohlížeči](./media/durable-functions-create-first-csharp/functions-vs-status.png)

    Odpověď je počáteční výsledek z funkce HTTP nám vědět, trvalé orchestrace úspěšně spuštěna.  Ještě není konečným výsledkem orchestrace.  Odpověď obsahuje několik užitečných adres URL.  Prozatím se podíváme na stav orchestrace.

4. Zkopírujte hodnotu `statusQueryGetUri` adresy URL pro a vneste ji do adresního řádku prohlížeče a proveďte požadavek.

    Požadavek bude dotaz orchestrace instance pro stav. Měli byste dostat konečnou odpověď, která vypadá takto.  Tento výstup nám ukazuje, že instance byla dokončena a obsahuje výstupy nebo výsledky trvalé funkce.

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

5. Ladění zastavíte stisknutím **kláves Shift + F5**.

Po ověření správného fungování funkce na místním počítači je na čase publikovat projekt do Azure.

## <a name="publish-the-project-to-azure"></a>Publikování projektu do Azure

Před publikováním projektu musíte mít v předplatném Azure aplikaci funkcí. Aplikaci funkcí můžete vytvořit přímo v sadě Visual Studio.

[!INCLUDE [Publish the project to Azure](../../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Testování funkce v Azure

1. Zkopírujte základní adresu URL aplikace funkcí ze stránky Publikovat profil. Nahraďte část adresy URL použité při místním testování funkce, která obsahuje `localhost:port`, novou základní adresou URL.

    Adresa URL, která volá aktivační událost HTTP s trvalou funkcí, by měla být v následujícím formátu:

        https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>_HttpStart

2. Vložte tuto novou adresu URL pro požadavek HTTP do panelu Adresa prohlížeče. Při používání publikované aplikace byste měli dostat stejnou odpověď na stav jako předtím.

## <a name="next-steps"></a>Další kroky

Visual Studio jste použili k vytvoření a publikování aplikace trvalé funkce jazyka C#.

> [!div class="nextstepaction"]
> [Informace o běžných vzorcích trvalých funkcí](durable-functions-overview.md#application-patterns)

::: zone-end
