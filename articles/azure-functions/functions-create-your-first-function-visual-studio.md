---
title: 'Rychlý Start: Vytvoření první funkce v Azure pomocí sady Visual Studio'
description: V tomto rychlém startu se dozvíte, jak pomocí sady Visual Studio vytvořit a publikovat funkci Azure Functions triggeru HTTP.
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.topic: quickstart
ms.date: 03/06/2020
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, 23113853-34f2-4f
ms.openlocfilehash: d7cfcd02c5e6c9ff57241288bd79efb45d4ac7ab
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "88213975"
---
# <a name="quickstart-create-your-first-function-in-azure-using-visual-studio"></a>Rychlý Start: Vytvoření první funkce v Azure pomocí sady Visual Studio

V tomto článku vytvoříte pomocí sady Visual Studio funkci založenou na knihovně jazyka C#, která reaguje na požadavky HTTP. Po místním testování kódu ho nasadíte do prostředí Azure Functions bez serveru.  

Po dokončení tohoto rychlého startu dojde v účtu Azure k malým nákladům na několik centů nebo méně.

## <a name="prerequisites"></a>Předpoklady

Chcete-li dokončit tento kurz, nejprve nainstalujte [Visual Studio 2019](https://azure.microsoft.com/downloads/). Během instalace se ujistěte, že jste vybrali **vývojovou úlohu Azure** . Pokud chcete vytvořit Azure Functions projekt pomocí sady Visual Studio 2017, je třeba nejprve nainstalovat [nejnovější nástroje Azure Functions](functions-develop-vs.md#check-your-tools-version).

![Instalace sady Visual Studio pomocí úlohy vývoj pro Azure](media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

Pokud ještě nemáte [předplatné Azure](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), vytvořte si [bezplatný účet](https://azure.microsoft.com/free/dotnet/) před tím, než začnete.

## <a name="create-a-function-app-project"></a>Vytvoření projektu aplikace funkcí

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

Visual Studio vytvoří projekt a třídu, která obsahuje často používaný kód pro typ funkce triggeru protokolu HTTP. Často používaný kód odešle odpověď HTTP obsahující hodnotu z textu žádosti nebo řetězce dotazu. `HttpTrigger`Atribut určuje, že funkce je aktivována požadavkem http. 

## <a name="rename-the-function"></a>Přejmenování funkce

`FunctionName`Atribut Method nastaví název funkce, která je ve výchozím nastavení generována jako `Function1` . Vzhledem k tomu, že nástroje neumožňují při vytváření projektu přepsat výchozí název funkce, vytvoření lepšího názvu pro třídu funkce, soubor a metadata vydejte za minutu.

1. V **Průzkumníku souborů**klikněte pravým tlačítkem na soubor function1.cs a přejmenujte ho na `HttpExample.cs` .

1. V kódu přejmenujte třídu Function1 na ' HttpExample '.

1. V `HttpTrigger` metodě s názvem `Run` , přejmenujte `FunctionName` atribut Method na `HttpExample` .

Teď, když jste přejmenovali funkci, ji můžete otestovat na místním počítači.

## <a name="run-the-function-locally"></a>Místní spuštění funkce

Visual Studio se integruje s Azure Functions Core Tools, takže můžete své funkce testovat místně pomocí plného Azure Functions runtime.  

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

Po ověření správného fungování funkce na místním počítači je na čase publikovat projekt do Azure.

## <a name="publish-the-project-to-azure"></a>Publikování projektu do Azure

Než budete moct projekt publikovat, musíte mít ve svém předplatném Azure aplikaci Function App. Publikování sady Visual Studio vytvoří aplikaci funkcí pro vás při prvním publikování projektu.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Testování funkce v Azure

1. V Průzkumníku cloudu by se měla vybrat nová aplikace Function App. Pokud ne, rozbalte své předplatné > **App Services**a vyberte svou novou aplikaci Function App.

1. Klikněte pravým tlačítkem na aplikaci Function App a vyberte **otevřít v prohlížeči**. Tím se otevře kořen vaší aplikace Function App ve výchozím webovém prohlížeči a zobrazí se stránka, která indikuje, že je spuštěná vaše aplikace Function App. 

    :::image type="content" source="media/functions-create-your-first-function-visual-studio/function-app-running-azure.png" alt-text="Spuštěná aplikace Function App":::

1. Do panelu Adresa v prohlížeči přidejte řetězec `/api/HttpExample?name=Functions` k základní adrese URL a spusťte požadavek.

    Adresa URL, která volá funkci triggeru HTTP, je v následujícím formátu:

    `http://<APP_NAME>.azurewebsites.net/api/HttpExample?name=Functions`

2. Přejděte na tuto adresu URL a v prohlížeči se zobrazí odpověď na požadavek na vzdálenou kopii vrácenou funkcí, který vypadá podobně jako v následujícím příkladu:

    :::image type="content" source="media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png" alt-text="Spuštěná aplikace Function App":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Další rychlé starty v této kolekci jsou postavené na tomto rychlém startu. Pokud plánujete pracovat s dalšími rychlými starty, kurzy nebo se všemi službami, které jste v tomto rychlém startu vytvořili, neprovádějte čištění prostředků.

*Prostředky* v Azure odkazují na aplikace funkcí, funkce, účty úložiště atd. Jsou seskupené do *skupin prostředků*a odstraněním skupiny můžete všechno odstranit ze skupiny. 

Vytvořili jste prostředky k dokončení těchto rychlých startů. Tyto prostředky se vám mohou fakturovat, a to v závislosti na [stavu účtu](https://azure.microsoft.com/account/) a [cenách služeb](https://azure.microsoft.com/pricing/). Pokud prostředky už nepotřebujete, můžete k jejich odstranění použít tento postup:

1. V Průzkumníku cloudu rozbalte své předplatné > **App Services**, klikněte pravým tlačítkem myši na svou aplikaci Function App a vyberte **otevřít na portálu**. 

1. Na stránce Function App vyberte kartu **Přehled** a potom vyberte odkaz v části **Skupina prostředků**.

   :::image type="content" source="media/functions-create-your-first-function-visual-studio/functions-app-delete-resource-group.png" alt-text="Spuštěná aplikace Function App":::

2. Na stránce **Skupina prostředků** zkontrolujte seznam zahrnutých prostředků a ověřte, že se jedná o ty, které chcete odstranit.
 
3. Vyberte **Odstranit skupinu prostředků** a postupujte podle pokynů.

   Odstranění může trvat několik minut. Po jeho dokončení se na několik sekund zobrazí oznámení. K zobrazení tohoto oznámení můžete také vybrat ikonu zvonku v horní části stránky.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste použili aplikaci Visual Studio k vytvoření a publikování aplikace funkcí jazyka C# v Azure pomocí jednoduché funkce triggeru HTTP. 

V dalším článku se dozvíte, jak do funkce Přidat vazbu Azure Storage fronty:
> [!div class="nextstepaction"]
> [Přidání vazby fronty Azure Storage k funkci](functions-add-output-binding-storage-queue-vs.md)

