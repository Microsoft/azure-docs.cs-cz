---
title: Vytvoření první funkce v Azure pomocí sady Visual Studio
description: Vytvořte v sadě Visual Studio funkci Azure Function aktivovanou protokolem HTTP a publikujte ji.
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.topic: quickstart
ms.date: 07/19/2019
ms.custom: mvc, devcenter, vs-azure, 23113853-34f2-4f
ms.openlocfilehash: 6d09453db0fdf8c09ad6d90c63b27f48508302be
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230715"
---
# <a name="create-your-first-function-using-visual-studio"></a>Vytvoření první funkce pomocí sady Visual Studio

Služba Azure Functions umožňuje spuštění kódu v prostředí [bez serveru](https://azure.microsoft.com/solutions/serverless/), aniž byste nejdřív museli vytvořit virtuální počítač nebo publikovat webovou aplikaci.

In this article, you learn how to use Visual Studio 2019 to locally create and test a "hello world" function and then publish it to Azure. This quickstart is designed for Visual Studio 2019. When creating a Functions project using Visual Studio 2017, you must first install the [latest Azure Functions tools](functions-develop-vs.md#check-your-tools-version).

![Odezva místního hostitele funkce v prohlížeči](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-local-final.png)

## <a name="prerequisites"></a>Předpoklady

To complete this tutorial, you must first install [Visual Studio 2019](https://azure.microsoft.com/downloads/). Make sure that the **Azure development** workload is also installed.

![Install Visual Studio with the Azure development workload](media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function-app-project"></a>Vytvoření projektu aplikace funkcí

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

Visual Studio creates a project and class that contains boilerplate code for the HTTP trigger function type. The `FunctionName` attribute on the method sets the name of the function, which by default is `HttpTrigger`. The `HttpTrigger` attribute specifies that the function is triggered by an HTTP request. Často používaný kód odešle odpověď HTTP obsahující hodnotu z textu žádosti nebo řetězce dotazu.

You can expand the capabilities of your function using input and output bindings by applying the appropriate attributes to the method. Další informace najdete v části [Triggery a vazby](functions-dotnet-class-library.md#triggers-and-bindings) v [referenčních informacích pro vývojáře v jazyce C# v Azure Functions](functions-dotnet-class-library.md).

Teď máte vytvořený projekt funkcí a funkci aktivovanou protokolem HTTP, takže ji můžete otestovat na místním počítači.

## <a name="run-the-function-locally"></a>Místní spuštění funkce

Visual Studio integrates with Azure Functions Core Tools so that you can test your functions locally using the full Functions runtime.  

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

Po ověření správného fungování funkce na místním počítači je na čase publikovat projekt do Azure.

## <a name="publish-the-project-to-azure"></a>Publikování projektu do Azure

Před publikováním projektu musíte mít v předplatném Azure aplikaci funkcí. Visual Studio publishing creates a function app for you the first time you publish your project.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Testování funkce v Azure

1. Zkopírujte základní adresu URL aplikace funkcí ze stránky Publikovat profil. Nahraďte část adresy URL použité při místním testování funkce, která obsahuje `localhost:port`, novou základní adresou URL. Stejně jako dříve nezapomeňte k této adrese URL připojit řetězec dotazu `?name=<YOUR_NAME>` a provést požadavek.

    Adresa URL, která volá funkci aktivovanou protokolem HTTP, by měla být v následujícím formátu:

        http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?name=<YOUR_NAME> 

2. Vložte tuto novou adresu URL pro požadavek HTTP do panelu Adresa prohlížeče. Následuje ukázka odezvy na vzdálený požadavek GET vrácené funkcí v prohlížeči:

    ![Odezva funkce v prohlížeči](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png)

## <a name="next-steps"></a>Další kroky

You have used Visual Studio to create and publish a C# function app in Azure with a simple HTTP triggered function. To learn more about developing functions as .NET class libraries, see [Azure Functions C# developer reference](functions-dotnet-class-library.md).

> [!div class="nextstepaction"]
> [Add an Azure Storage queue binding to your function](functions-add-output-binding-storage-queue-vs.md)
