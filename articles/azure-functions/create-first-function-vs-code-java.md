---
title: Vytvoření funkce Java pomocí Visual Studio Code-Azure Functions
description: Naučte se vytvořit funkci Java a pak publikovat místní projekt na hostování bez serveru v Azure Functions pomocí rozšíření Azure Functions v Visual Studio Code.
ms.topic: quickstart
ms.date: 11/03/2020
ms.openlocfilehash: 36516abd8b50c0b0b7d72e6bae0b67701509ca65
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493630"
---
# <a name="quickstart-create-a-java-function-in-azure-using-visual-studio-code"></a>Rychlý Start: vytvoření funkce Java v Azure pomocí Visual Studio Code

[!INCLUDE [functions-language-selector-quickstart-vs-code](../../includes/functions-language-selector-quickstart-vs-code.md)]

V tomto článku použijete Visual Studio Code k vytvoření funkce jazyka Java, která reaguje na požadavky HTTP. Po místním testování kódu ho nasadíte do prostředí Azure Functions bez serveru.

Po dokončení tohoto rychlého startu dojde v účtu Azure k malým nákladům na několik centů nebo méně.

> [!NOTE]
> Pokud Visual Studio Code není preferovaným vývojářským nástrojem, podívejte se na naše podobné kurzy pro vývojáře v jazyce Java, které používají [Maven](create-first-function-cli-java.md), [GRADLE](./functions-create-first-java-gradle.md) a [IntelliJ nápad](/azure/developer/java/toolkit-for-intellij/quickstart-functions).

## <a name="configure-your-environment"></a>Konfigurace prostředí

Než začnete, ujistěte se, že máte zavedené následující požadavky:

+ Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Sada Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), verze 8 nebo 11.

+ [Apache Maven](https://maven.apache.org)verze 3,0 nebo vyšší.

+ [Visual Studio Code](https://code.visualstudio.com/) na jedné z [podporovaných platforem](https://code.visualstudio.com/docs/supporting/requirements#_platforms)

+ [Balíček rozšíření Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)  

+ [Rozšíření Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) pro Visual Studio Code 

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Vytvořit místní projekt

V této části použijete Visual Studio Code k vytvoření místního projektu Azure Functions v jazyce Java. Později v tomto článku publikujete kód funkce do Azure. 

1. Zvolte ikonu Azure na panelu Aktivita a pak v oblasti **Azure: Funkce** vyberte ikonu **Vytvořit nový projekt**.

    ![Volba možnosti Vytvořit nový projekt](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Zvolte umístění adresáře pro váš pracovní prostor projektu a zvolte **Vybrat**.

    > [!NOTE]
    > Tyto kroky jsou navržené tak, aby se dokončily mimo pracovní prostor. V tomto případě nevybírejte složku projektu, která je součástí pracovního prostoru.

1. Podle pokynů na obrazovce zadejte tyto informace:

    + **Vyberte jazyk pro váš projekt funkce:** Zvolte `Java`.

    + **Vyberte verzi Java**: zvolte `Java 8` nebo `Java 11` , verze jazyka Java, na které budou vaše funkce běžet v Azure. Vyberte verzi jazyka Java, kterou jste ověřili místně.

    + **Zadejte ID skupiny**: vyberte `com.function` .

    + **Zadejte ID artefaktu**: vyberte `myFunction` .

    + **Zadejte verzi**: vyberte `1.0-SNAPSHOT` .

    + **Zadejte název balíčku**: vyberte `com.function` .

    + **Zadejte název aplikace**: vyberte `myFunction-12345` .

    + **Úroveň autorizace**: vyberte `Anonymous` , který umožňuje komukoli zavolat koncový bod funkce. Další informace o úrovni autorizace najdete v tématu [autorizační klíče](functions-bindings-http-webhook-trigger.md#authorization-keys).

    + **Vyberte způsob, jakým chcete projekt otevírat:** Zvolte `Add to workspace`.

1. Pomocí těchto informací Visual Studio Code generuje Azure Functions projekt pomocí triggeru protokolu HTTP. Můžete zobrazit soubory místních projektů v Průzkumníkovi. Další informace o souborech, které jsou vytvořeny, najdete v tématu [vygenerované soubory projektu](functions-develop-vs-code.md#generated-project-files). 

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

Po ověření, že se funkce na místním počítači spustí správně, je čas použít Visual Studio Code k publikování projektu přímo do Azure.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

## <a name="next-steps"></a>Další kroky

Použili jste [Visual Studio Code](functions-develop-vs-code.md?tabs=java) k vytvoření aplikace funkcí pomocí jednoduché funkce aktivované protokolem HTTP. V dalším článku tuto funkci rozbalíte tak, že se připojíte k Azure Storage. Další informace o připojení k ostatním službám Azure najdete v tématu [Přidání vazeb do existující funkce v Azure Functions](add-bindings-existing-function.md?tabs=java). 

> [!div class="nextstepaction"]
> [Připojení k frontě Azure Storage](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-java)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
