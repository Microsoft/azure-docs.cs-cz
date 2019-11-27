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

V tomto článku se naučíte, jak pomocí sady Visual Studio 2019 místně vytvořit a otestovat funkci "Hello World" a potom ji publikovat do Azure. Tento rychlý Start je určený pro Visual Studio 2019. Při vytváření projektu Functions pomocí sady Visual Studio 2017 je nutné nejprve nainstalovat [nejnovější nástroje Azure Functions](functions-develop-vs.md#check-your-tools-version).

![Odezva místního hostitele funkce v prohlížeči](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-local-final.png)

## <a name="prerequisites"></a>Požadavky

Abyste mohli tento kurz dokončit, musíte nejdřív nainstalovat [Visual Studio 2019](https://azure.microsoft.com/downloads/). Ujistěte se, že je nainstalovaná také úloha **vývoj pro Azure** .

![Instalace sady Visual Studio pomocí úlohy vývoj pro Azure](media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function-app-project"></a>Vytvoření projektu aplikace funkcí

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

Visual Studio vytvoří projekt a třídu, která obsahuje často používaný kód pro typ funkce triggeru protokolu HTTP. Atribut `FunctionName` v metodě nastaví název funkce, která je ve výchozím nastavení `HttpTrigger`. Atribut `HttpTrigger` určuje, že funkce je aktivována požadavkem HTTP. Často používaný kód odešle odpověď HTTP obsahující hodnotu z textu žádosti nebo řetězce dotazu.

Můžete rozšířit možnosti vaší funkce pomocí vstupní a výstupní vazby, a to použitím příslušných atributů pro metodu. Další informace najdete v části [Triggery a vazby](functions-dotnet-class-library.md#triggers-and-bindings) v [referenčních informacích pro vývojáře v jazyce C# v Azure Functions](functions-dotnet-class-library.md).

Teď máte vytvořený projekt funkcí a funkci aktivovanou protokolem HTTP, takže ji můžete otestovat na místním počítači.

## <a name="run-the-function-locally"></a>Místní spuštění funkce

Visual Studio se integruje s Azure Functions Core Tools, takže můžete své funkce testovat místně pomocí modulu runtime Full Functions.  

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

Po ověření správného fungování funkce na místním počítači je na čase publikovat projekt do Azure.

## <a name="publish-the-project-to-azure"></a>Publikování projektu do Azure

Před publikováním projektu musíte mít v předplatném Azure aplikaci funkcí. Publikování sady Visual Studio vytvoří aplikaci funkcí pro vás při prvním publikování projektu.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Testování funkce v Azure

1. Zkopírujte základní adresu URL aplikace funkcí ze stránky Publikovat profil. Nahraďte část adresy URL použité při místním testování funkce, která obsahuje `localhost:port`, novou základní adresou URL. Stejně jako dříve nezapomeňte k této adrese URL připojit řetězec dotazu `?name=<YOUR_NAME>` a provést požadavek.

    Adresa URL, která volá funkci aktivovanou protokolem HTTP, by měla být v následujícím formátu:

        http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?name=<YOUR_NAME> 

2. Vložte tuto novou adresu URL pro požadavek HTTP do panelu Adresa prohlížeče. Následuje ukázka odezvy na vzdálený požadavek GET vrácené funkcí v prohlížeči:

    ![Odezva funkce v prohlížeči](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png)

## <a name="next-steps"></a>Další kroky

Aplikaci Visual Studio jste použili k vytvoření a publikování aplikace C# Function App v Azure pomocí jednoduché funkce aktivované protokolem HTTP. Další informace o vývoji funkcí jako knihoven tříd .NET naleznete v tématu [Azure Functions C# reference pro vývojáře](functions-dotnet-class-library.md).

> [!div class="nextstepaction"]
> [Přidání vazby fronty Azure Storage k funkci](functions-add-output-binding-storage-queue-vs.md)
