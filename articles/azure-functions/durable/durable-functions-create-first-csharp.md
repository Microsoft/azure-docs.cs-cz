---
title: Create your first durable function in Azure using C#
description: Create and publish an Azure Durable Function using Visual Studio.
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
# <a name="create-your-first-durable-function-in-c"></a>Create your first durable function in C\#

*Durable Functions* is an extension of [Azure Functions](../functions-overview.md) that lets you write stateful functions in a serverless environment. Toto rozšíření za vás spravuje stav, kontrolní body a restartování.

[!INCLUDE [v1-note](../../../includes/functions-durable-v1-tutorial-note.md)]

In this article, you learn how to use the Visual Studio 2019 to locally create and test a "hello world" durable function.  This function orchestrates and chains-together calls to other functions. Kód funkce potom publikujete do Azure. These tools are available as part of the Azure development workload in Visual Studio 2019.

![Running durable function in Azure](./media/durable-functions-create-first-csharp/functions-vs-complete.png)

## <a name="prerequisites"></a>Předpoklady

K provedení kroků v tomto kurzu je potřeba:

* Install [Visual Studio 2019](https://visualstudio.microsoft.com/vs/). Make sure that the **Azure development** workload is also installed. Visual Studio 2017 also supports Durable Functions development, but the UI and steps differ.

* Verify you have the [Azure Storage Emulator](../../storage/common/storage-use-emulator.md) installed and running.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function-app-project"></a>Vytvoření projektu aplikace funkcí

The Azure Functions template creates a project that can be published to a function app in Azure. Aplikace funkcí umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování a sdílení prostředků.

1. V sadě Visual Studio zvolte v nabídce **Soubor** možnost **Nový** > **Projekt**.

1. In the **Add a new project** dialog, search for `functions`, choose the **Azure Functions** template, and select **Next**. 

    ![Dialogové okno Nový projekt pro vytvoření funkce v sadě Visual Studio](./media/durable-functions-create-first-csharp/functions-vs-new-project.png)

1. Type a **Project name** for your project, and select **OK**. The project name must be valid as a C# namespace, so don't use underscores, hyphens, or any other nonalphanumeric characters.

1. In **Create a new Azure Functions Application**, use the settings specified in the table that follows the image.

    ![Create a new Azure Functions Application dialog in Visual Studio](./media/durable-functions-create-first-csharp/functions-vs-new-function.png)

    | Nastavení      | Navrhovaná hodnota  | Popis                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Verze** | Azure Functions 2.0 <br />(.NET Core) | Creates a function project that uses the version 2.0 runtime of Azure Functions, which supports .NET Core. Azure Functions 1.0 supports the .NET Framework. Další informace najdete v tématu s [přehledem verzí modulu runtime Azure Functions](../functions-versions.md).   |
    | **Šablona** | Empty | Creates an empty function app. |
    | **Účet úložiště**  | Emulátor úložiště | A storage account is required for durable function state management. |

4. Select **Create** to create an empty function project. This project has the basic configuration files needed to run your functions.

## <a name="add-functions-to-the-app"></a>Add functions to the app

The following steps use a template to create the durable function code in your project.

1. Right-click the project in Visual Studio and select **Add** > **New Azure Function**.

    ![Add new function](./media/durable-functions-create-first-csharp/functions-vs-add-new-function.png)

1. Verify **Azure Function** is selected from the add menu, type a name for your C# file, and then select **Add**.

1. Select the **Durable Functions Orchestration** template and then select **Ok**

    ![Select durable template](./media/durable-functions-create-first-csharp/functions-vs-select-template.png)  

> [!NOTE]
> This template currently creates a durable function using an older 1.x version of the extension. See the [Durable Functions Versions](durable-functions-versions.md) article for information about how to upgrade to the newer 2.x versions of Durable Functions.

A new durable function is added to the app.  Open the new .cs file to view the contents. This durable function is a simple function chaining example with the following methods:  

| Metoda | FunctionName | Popis |
| -----  | ------------ | ----------- |
| **`RunOrchestrator`** | `<file-name>` | Manages the durable orchestration. In this case, the orchestration starts, creates a list, and adds the result of three functions calls to the list.  When the three function calls are complete, it returns the list. |
| **`SayHello`** | `<file-name>_Hello` | The function returns a hello. It is the function that contains the business logic that is being orchestrated. |
| **`HttpStart`** | `<file-name>_HttpStart` | An [HTTP-triggered function](../functions-bindings-http-webhook.md) that starts an instance of the orchestration and returns a check status response. |

Now that you've created your function project and a durable function, you can test it on your local computer.

## <a name="test-the-function-locally"></a>Místní testování funkce

Nástroje Azure Functions Core umožňují spouštět projekt Azure Functions na místním počítači pro vývoj. K instalaci těchto nástrojů budete vyzváni při prvním spuštění funkce ze sady Visual Studio.

1. Pokud chcete funkci otestovat, stiskněte F5. Po výzvě přijměte požadavek ze sady Visual Studio na stažení a instalaci nástrojů Azure Functions Core (CLI). Může být také potřeba povolit výjimku brány firewall, aby nástroje mohly zpracovávat požadavky HTTP.

2. Zkopírujte adresu URL vaší funkce z výstupu modulu runtime služby Azure Functions.

    ![Místní modul runtime Azure](./media/durable-functions-create-first-csharp/functions-vs-debugging.png)

3. Paste the URL for the HTTP request into your browser's address bar and execute the request. Následuje ukázka odezvy na místní požadavek GET vrácené funkcí v prohlížeči:

    ![Odezva místního hostitele funkce v prohlížeči](./media/durable-functions-create-first-csharp/functions-vs-status.png)

    The response is the initial result from the HTTP function letting us know the durable orchestration has started successfully.  It is not yet the end result of the orchestration.  The response includes a few useful URLs.  For now, let's query the status of the orchestration.

4. Copy the URL value for `statusQueryGetUri` and pasting it in the browser's address bar and execute the request.

    The request will query the orchestration instance for the status. You should get an eventual response that looks like the following.  This output shows us the instance has completed, and includes the outputs or results of the durable function.

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

    The URL that calls your durable function HTTP trigger should be in the following format:

        http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>_HttpStart

2. Vložte tuto novou adresu URL pro požadavek HTTP do panelu Adresa prohlížeče. You should get the same status response as before when using the published app.

## <a name="next-steps"></a>Další kroky

You have used Visual Studio to create and publish a C# durable function app.

> [!div class="nextstepaction"]
> [Learn about common durable function patterns](durable-functions-overview.md#application-patterns)