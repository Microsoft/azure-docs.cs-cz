---
title: 'Rychlý Start: Vytvoření první funkce v Azure pomocí sady Visual Studio'
description: V tomto rychlém startu se dozvíte, jak vytvořit a publikovat funkci Azure aktivovanou protokolem HTTP pomocí sady Visual Studio.
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.topic: quickstart
ms.date: 03/06/2020
ms.custom: mvc, devcenter, vs-azure, 23113853-34f2-4f
ms.openlocfilehash: e58414fa94a4ef54eb2f288b2cd636c10611460d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "81308937"
---
# <a name="quickstart-create-your-first-function-in-azure-using-visual-studio"></a>Rychlý Start: Vytvoření první funkce v Azure pomocí sady Visual Studio

Azure Functions umožňuje spuštění kódu v prostředí bez serveru, aniž byste nejdřív museli vytvořit virtuální počítač nebo publikovat webovou aplikaci.

V tomto rychlém startu se dozvíte, jak pomocí sady Visual Studio 2019 místně vytvořit a otestovat aplikaci funkcí jazyka C#, která je typu "Hello World", kterou pak publikujete do Azure. 

![Odezva místního hostitele funkce v prohlížeči](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-local-final.png)

Tento rychlý Start je určený pro Visual Studio 2019. 

## <a name="prerequisites"></a>Požadavky

Chcete-li dokončit tento kurz, nejprve nainstalujte [Visual Studio 2019](https://azure.microsoft.com/downloads/). Během instalace se ujistěte, že jste vybrali **vývojovou úlohu Azure** . Pokud chcete vytvořit Azure Functions projekt pomocí sady Visual Studio 2017, je třeba nejprve nainstalovat [nejnovější nástroje Azure Functions](functions-develop-vs.md#check-your-tools-version).

![Instalace sady Visual Studio pomocí úlohy vývoj pro Azure](media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

Pokud ještě nemáte [předplatné Azure](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), vytvořte si [bezplatný účet](https://azure.microsoft.com/free/dotnet/) před tím, než začnete.

## <a name="create-a-function-app-project"></a>Vytvoření projektu aplikace funkcí

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

Visual Studio vytvoří projekt a třídu, která obsahuje často používaný kód pro typ funkce triggeru protokolu HTTP. Atribut `FunctionName` Method nastaví název funkce, která je ve výchozím nastavení nastavena `Function1`na. `HttpTrigger` Atribut určuje, že funkce je aktivována požadavkem http. Často používaný kód odešle odpověď HTTP obsahující hodnotu z textu žádosti nebo řetězce dotazu.

Rozšiřte možnosti vaší funkce se vstupními a výstupními vazbami, a to použitím příslušných atributů pro metodu. Další informace najdete v části [Triggery a vazby](functions-dotnet-class-library.md#triggers-and-bindings) v [referenčních informacích pro vývojáře v jazyce C# v Azure Functions](functions-dotnet-class-library.md).

Teď máte vytvořený projekt funkcí a funkci aktivovanou protokolem HTTP, takže ji můžete otestovat na místním počítači.

## <a name="run-the-function-locally"></a>Místní spuštění funkce

Visual Studio se integruje s Azure Functions Core Tools, takže můžete své funkce testovat místně pomocí plného Azure Functions runtime.  

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

Po ověření správného fungování funkce na místním počítači je na čase publikovat projekt do Azure.

## <a name="publish-the-project-to-azure"></a>Publikování projektu do Azure

Než budete moct projekt publikovat, musíte mít ve svém předplatném Azure aplikaci Function App. Publikování sady Visual Studio vytvoří aplikaci funkcí pro vás při prvním publikování projektu.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Testování funkce v Azure

1. Zkopírujte základní adresu URL aplikace Function App ze stránky profil **publikování** . Nahraďte `localhost:port` část adresy URL, kterou jste použili k místnímu otestování funkce, novou základní adresou URL. Připojí řetězec `?name=<YOUR_NAME>` dotazu k této adrese URL a spustí žádost.

    Adresa URL, která volá funkci aktivovanou protokolem HTTP, je v následujícím formátu:

    `http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?name=<YOUR_NAME>`

2. Vložte tuto novou adresu URL pro požadavek HTTP do panelu Adresa prohlížeče. Na následujícím obrázku je znázorněna odpověď v prohlížeči na žádost o vzdálený přístup získanou funkcí:

    ![Odezva funkce v prohlížeči](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste použili aplikaci Visual Studio k vytvoření a publikování aplikace funkcí v jazyce C# v Azure pomocí jednoduché funkce aktivované protokolem HTTP. 

V dalším článku se dozvíte, jak do funkce Přidat vazbu Azure Storage fronty:
> [!div class="nextstepaction"]
> [Přidání vazby fronty Azure Storage k funkci](functions-add-output-binding-storage-queue-vs.md)

