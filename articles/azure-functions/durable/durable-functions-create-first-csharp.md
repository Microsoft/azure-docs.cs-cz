---
title: Vytvoření první trvalé funkce v Azure pomocíC#
description: Vytvořte a publikujte funkci trvalosti Azure pomocí sady Visual Studio.
author: jeffhollan
ms.topic: quickstart
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 6b3ead9eefd6f0d4c504cc7711ea4e03facf8edc
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231511"
---
# <a name="create-your-first-durable-function-in-c"></a>Vytvoření první trvalé funkce v jazyce C\#

*Durable Functions* je rozšíření [Azure Functions](../functions-overview.md) , které umožňuje psát stavové funkce v prostředí bez serveru. Toto rozšíření za vás spravuje stav, kontrolní body a restartování.

[!INCLUDE [v1-note](../../../includes/functions-durable-v1-tutorial-note.md)]

V tomto článku se naučíte, jak pomocí sady Visual Studio 2019 místně vytvořit a otestovat trvalou funkci "Hello World".  Tato funkce orchestruje a zřetězí volání dalších funkcí. Kód funkce potom publikujete do Azure. Tyto nástroje jsou k dispozici v rámci úlohy vývoje Azure v aplikaci Visual Studio 2019.

![Spuštění trvalé funkce v Azure](./media/durable-functions-create-first-csharp/functions-vs-complete.png)

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* Nainstalujte [Visual Studio 2019](https://visualstudio.microsoft.com/vs/). Ujistěte se, že je nainstalovaná také úloha **vývoj pro Azure** . Visual Studio 2017 také podporuje vývoj Durable Functions, ale uživatelské rozhraní a postup se liší.

* Ověřte, že je nainstalovaný a spuštěný [emulátor Azure Storage](../../storage/common/storage-use-emulator.md) .

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function-app-project"></a>Vytvoření projektu aplikace funkcí

Šablona Azure Functions vytvoří projekt, který se dá publikovat do aplikace Function App v Azure. Aplikace funkcí umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování a sdílení prostředků.

1. V sadě Visual Studio zvolte v nabídce **Soubor** možnost  > Nový**Projekt**.

1. V dialogovém okně **Přidat nový projekt** vyhledejte `functions`, zvolte šablonu **Azure Functions** a vyberte **Další**. 

    ![Dialogové okno Nový projekt pro vytvoření funkce v sadě Visual Studio](./media/durable-functions-create-first-csharp/functions-vs-new-project.png)

1. Zadejte **název projektu** a vyberte **OK**. Název projektu musí být platný jako C# obor názvů, takže nepoužívejte podtržítka, spojovníky nebo žádné jiné nealfanumerické znaky.

1. V části **vytvořit novou aplikaci Azure Functions**použijte nastavení zadaná v tabulce, která následuje po obrázku.

    ![Vytvoření nového dialogového okna aplikace Azure Functions v aplikaci Visual Studio](./media/durable-functions-create-first-csharp/functions-vs-new-function.png)

    | Nastavení      | Navrhovaná hodnota  | Popis                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Verze** | Azure Functions 2,0 <br />(.NET Core) | Vytvoří projekt funkce, který používá běhový modul verze 2,0 Azure Functions, který podporuje .NET Core. Azure Functions 1,0 podporuje .NET Framework. Další informace najdete v tématu s [přehledem verzí modulu runtime Azure Functions](../functions-versions.md).   |
    | **Šablona** | Obsahovat | Vytvoří prázdnou aplikaci Function App. |
    | **Účet úložiště**  | Emulátor úložiště | Pro trvalou správu stavu funkce se vyžaduje účet úložiště. |

4. Vyberte **vytvořit** k vytvoření prázdného projektu funkce. Tento projekt má základní konfigurační soubory potřebné ke spuštění vašich funkcí.

## <a name="add-functions-to-the-app"></a>Přidání funkcí do aplikace

Následující kroky používají šablonu k vytvoření trvalého kódu funkce v projektu.

1. Klikněte pravým tlačítkem na projekt v aplikaci Visual Studio a vyberte **přidat** > **Nová funkce Azure Functions**.

    ![Přidat novou funkci](./media/durable-functions-create-first-csharp/functions-vs-add-new-function.png)

1. Ověřte, že je v nabídce Přidat vybraná možnost **Azure Function** , zadejte název C# souboru a pak vyberte **Přidat**.

1. Vyberte šablonu **orchestrace Durable Functions** a pak vyberte **OK** .

    ![Vybrat trvanlivé šablonu](./media/durable-functions-create-first-csharp/functions-vs-select-template.png)  

> [!NOTE]
> Tato šablona teď vytvoří trvalou funkci pomocí starší verze 1. x rozšíření. Informace o tom, jak upgradovat na novější verze 2. x Durable Functions, najdete v článku o [Durable Functions verzích](durable-functions-versions.md) .

Do aplikace se přidá nová trvalá funkce.  Otevřete nový soubor. cs pro zobrazení obsahu. Tato trvalá funkce je příklad řetězení jednoduchých funkcí s následujícími metodami:  

| Metoda | functionName | Popis |
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

    ![Odezva místního hostitele funkce v prohlížeči](./media/durable-functions-create-first-csharp/functions-vs-status.png)

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

        http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>_HttpStart

2. Vložte tuto novou adresu URL pro požadavek HTTP do panelu Adresa prohlížeče. Po použití publikované aplikace byste měli získat stejnou reakci na stav jako předtím.

## <a name="next-steps"></a>Další kroky

Pomocí sady Visual Studio jste vytvořili a publikovali C# trvalou aplikaci Function App.

> [!div class="nextstepaction"]
> [Další informace o běžných vzorech trvalých funkcí](durable-functions-overview.md#application-patterns)