---
title: Vytvoření první funkce trvalý v Azure s využitímC#
description: Vytvoření a publikování odolné funkce Azure pomocí sady Visual Studio.
services: functions
documentationcenter: na
author: jeffhollan
manager: jeconnoc
keywords: azure functions, functions, event processing, compute, serverless architecture
ms.service: azure-functions
ms.devlang: multiple
ms.topic: quickstart
ms.date: 11/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 2a0cee1ad750144f30b9ab6732e0bbdf8138db28
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54038145"
---
# <a name="create-your-first-durable-function-in-c"></a>Vytvoření první funkce trvalý v jazyce C\#

*Odolná služba Functions* je rozšířením [Azure Functions](../functions-overview.md) , který umožňuje zapisovat stavové funkce v prostředí bez serveru. Rozšíření spravuje stav, kontrolní body a restartuje za vás.

V tomto článku se dozvíte, jak používat nástroje Visual Studio 2017 pro službu Azure Functions místně vytvořit a otestovat funkci "hello world" trvalý.  Tato funkce orchestruje a zřetězen dohromady volání dalších funkcí. Kód funkce potom publikujete do Azure. Tyto nástroje jsou dostupné jako součást sady funkcí Azure – vývoj v sadě Visual Studio 2017.

![Odolné funkce spuštěné v Azure](./media/durable-functions-create-first-csharp/functions-vs-complete.png)

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* Nainstalujte [Visual Studio 2017](https://azure.microsoft.com/downloads/). Ujistěte se, že **vývoj pro Azure** je nainstalovaná úloha.

* Přesvědčte se, že máte [nejnovější nástroje Azure Functions](../functions-develop-vs.md#check-your-tools-version).

* Ověřte, že máte [emulátoru úložiště Azure](../../storage/common/storage-use-emulator.md) nainstalovaná a spuštěná.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function-app-project"></a>Vytvoření projektu aplikace funkcí

Šablony Azure Functions vytvoří projekt, který jde publikovat do aplikace function app v Azure. Aplikace funkcí umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování a sdílení prostředků.

1. V sadě Visual Studio zvolte v nabídce **Soubor** možnost **Nový** > **Projekt**.

2. V dialogovém okně **Nový projekt** vyberte **Nainstalované**, rozbalte uzel **Visual C#** > **Cloud**, vyberte **Azure Functions**, zadejte **název** vašeho projektu a klikněte na **OK**. Název aplikace funkcí musí být platný jako obor názvů C#, takže nepoužívejte podtržítka, pomlčky nebo jiné nealfanumerické znaky.

    ![Dialogové okno Nový projekt pro vytvoření funkce v sadě Visual Studio](./media/durable-functions-create-first-csharp/functions-vs-new-project.png)

3. Použijte nastavení uvedená v tabulce pod obrázkem.

    ![Dialogové okno Nová funkce v sadě Visual Studio](./media/durable-functions-create-first-csharp/functions-vs-new-function.png)

    | Nastavení      | Navrhovaná hodnota  | Popis                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Verze** | Azure Functions 2.x <br />(.NET Core) | Vytvoří projekt funkce, která používá modul runtime verze 2.x Azure Functions, která podporuje .NET Core. Azure Functions 1.x podporuje rozhraní .NET Framework. Další informace najdete v tématu s [přehledem verzí modulu runtime Azure Functions](../functions-versions.md).   |
    | **Šablona** | Prázdné | Vytvoří aplikaci funkcí prázdný. |
    | **Účet úložiště**  | Emulátor úložiště | Účet úložiště je nutná pro správu odolné funkce stavu. |

4. Klikněte na tlačítko **OK** pro vytvoření projektu funkce empty. Tento projekt obsahuje základní konfigurační soubory potřebné ke spuštění vaší funkce.

## <a name="add-functions-to-the-app"></a>Přidání funkce do aplikace

V následujících krocích používá šablony k vytvoření kódových odolné funkce ve vašem projektu.

1. Klikněte pravým tlačítkem na projekt v sadě Visual Studio a vyberte **přidat** > **novou funkci Azure Functions**.

    ![Přidat nové funkce](./media/durable-functions-create-first-csharp/functions-vs-add-new-function.png)

2. Ověřte **funkce Azure Functions** se vybere z nabídky přidat a poskytnout vaší C# název souboru.  Stiskněte **Přidat**.

3. Vyberte **odolné funkce Orchestrace** šablonu a klikněte na tlačítko **Ok**

    ![Šablona odolné výběru](./media/durable-functions-create-first-csharp/functions-vs-select-template.png)  

Nová funkce trvalý se přidá do aplikace.  Otevřete nový soubor .cs, chcete-li zobrazit obsah. Tato funkce trvalý je jednoduchou funkci příklad řetězení pomocí následujících metod:  

| Metoda | functionName | Popis |
| -----  | ------------ | ----------- |
| **`RunOrchestrator`** | `<file-name>` | Spravuje trvalé Orchestrace. V tomto případě orchestraci spustí, vytvoří seznam a přidá výsledek tři funkce volání do seznamu.  Po dokončení tři volání se vrátí seznam. |
| **`SayHello`** | `<file-name>_Hello` | Funkce vrátí hello. Toto je funkce, která obsahuje obchodní logiku, která je právě orchestrované. |
| **`HttpStart`** | `<file-name>_HttpStart` | [Funkci aktivovanou protokolem HTTP](../functions-bindings-http-webhook.md) , který spustí instanci orchestraci a vrátí odpověď na kontrolu stavu. |

Teď máte vytvořený projekt funkcí a odolné funkce, takže ji otestovat na místním počítači.

## <a name="test-the-function-locally"></a>Místní testování funkce

Nástroje Azure Functions Core umožňují spouštět projekt Azure Functions na místním počítači pro vývoj. K instalaci těchto nástrojů budete vyzváni při prvním spuštění funkce ze sady Visual Studio.

1. Pokud chcete funkci otestovat, stiskněte F5. Po výzvě přijměte požadavek ze sady Visual Studio na stažení a instalaci nástrojů Azure Functions Core (CLI). Může být také potřeba povolit výjimku brány firewall, aby nástroje mohly zpracovávat požadavky HTTP.

2. Zkopírujte adresu URL vaší funkce z výstupu modulu runtime služby Azure Functions.

    ![Místní modul runtime Azure](./media/durable-functions-create-first-csharp/functions-vs-debugging.png)

3. Vložte adresu URL pro požadavek HTTP do panelu Adresa v prohlížeči a proveďte požadavek. Následuje ukázka odezvy na místní požadavek GET vrácené funkcí v prohlížeči:

    ![Odezva místního hostitele funkce v prohlížeči](./media/durable-functions-create-first-csharp/functions-vs-status.png)

    Odpověď je, že počáteční výsledek z funkce protokolu HTTP nám dáte vědět, trvalý orchestration se úspěšně spustila.  Ještě není konečný výsledek orchestraci.  Odpověď obsahuje několik užitečných adresy URL.  Teď Pojďme zjistit stav orchestraci.

4. Zkopírujte hodnotu adresy URL pro `statusQueryGetUri` a vložením do prohlížeče adresa pruhové a proveďte požadavek.

    Požadavek bude dotaz instance Orchestrace stavu. Měli byste obdržet odpověď konečný výsledek, který vypadá takto.  Ukazuje to nám instance byla dokončena a zahrnuje výstupů nebo důsledků odolné funkce.

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

5. Pokud chcete zastavit ladění, stiskněte **Shift + F5**.

Po ověření správného fungování funkce na místním počítači je na čase publikovat projekt do Azure.

## <a name="publish-the-project-to-azure"></a>Publikování projektu do Azure

Před publikováním projektu musíte mít v předplatném Azure aplikaci funkcí. Aplikaci funkcí můžete vytvořit přímo v sadě Visual Studio.

[!INCLUDE [Publish the project to Azure](../../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Testování funkce v Azure

1. Zkopírujte základní adresu URL aplikace funkcí ze stránky Publikovat profil. Nahraďte část adresy URL použité při místním testování funkce, která obsahuje `localhost:port`, novou základní adresou URL.

    Adresa URL, která volá váš trigger HTTP odolné funkce by měla být v následujícím formátu:

        http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>_HttpStart

2. Vložte tuto novou adresu URL pro požadavek HTTP do panelu Adresa prohlížeče. Měli byste obdržet stejnou odpověď stav jako před při použití publikované aplikace.

## <a name="next-steps"></a>Další postup

Visual Studio jste použili k vytvoření a publikování C# odolné funkce aplikace.

> [!div class="nextstepaction"]
> [Další informace o běžných vzorech odolné funkce.](durable-functions-concepts.md)
