---
title: Vytvoření první funkce v Azure pomocí Visual Studio Code
description: Vytvořte a publikujte do Azure jednoduchou funkci aktivovanou protokolem HTTP pomocí rozšíření Azure Functions ve Visual Studio Code.
ms.topic: quickstart
ms.date: 01/10/2020
ms.custom: mvc, devcenter
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 42888060f206c89a597a1a18783070d0a805dfb9
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79241301"
---
# <a name="quickstart-create-an-azure-functions-project-using-visual-studio-code"></a>Rychlý Start: vytvoření projektu Azure Functions pomocí Visual Studio Code

V tomto článku použijete Visual Studio Code k vytvoření funkce, která reaguje na požadavky HTTP. Po místním testování kódu ho nasadíte do prostředí Azure Functions bez serveru. Po dokončení tohoto rychlého startu dojde v účtu Azure k malým nákladům na několik centů nebo méně. 

K dispozici je také verze tohoto článku [na bázi CLI](functions-create-first-azure-function-azure-cli.md) .

## <a name="configure-your-environment"></a>Konfigurace prostředí

Než začnete, ujistěte se, že máte zavedené následující požadavky:

+ Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

::: zone pivot="programming-language-csharp,programming-language-powershell,programming-language-python"  
+ [Node. js](https://nodejs.org/), který vyžaduje systém Windows pro npm. Pouze [aktivní LTS a verze LTS údržby ](https://nodejs.org/about/releases/). K ověření verze použijte příkaz `npm --version`.
    Nevyžaduje se pro místní vývoj na MacOS a Linux.   
::: zone-end  
::: zone pivot="programming-language-javascript,programming-language-typescript"  
+ LTS verze [Node. js](https://nodejs.org/), Active a Maintenance LTS (Doporučené 10.14.1). K ověření verze použijte příkaz `npm --version`.
::: zone-end 
::: zone pivot="programming-language-python"
+ Azure Functions podporuje [python 3,8](https://www.python.org/downloads/release/python-381/), [Python 3,7](https://www.python.org/downloads/release/python-375/)a [Python 3,6](https://www.python.org/downloads/release/python-368/) .
::: zone-end   
::: zone pivot="programming-language-powershell"
+ [Jádro PowerShellu](/powershell/scripting/install/installing-powershell-core-on-windows)

+ [.NET Core SDK 2.2 +](https://www.microsoft.com/net/download)  
::: zone-end  
+ [Visual Studio Code](https://code.visualstudio.com/) na některé z [podporovaných platforem](https://code.visualstudio.com/docs/supporting/requirements#_platforms).  
::: zone pivot="programming-language-csharp"  
+ Rozšíření pro Visual Studio Code. [ C# ](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)  
::: zone-end  
::: zone pivot="programming-language-python"
+ [Přípona Pythonu](https://marketplace.visualstudio.com/items?itemName=ms-python.python) pro Visual Studio Code.  
::: zone-end  
::: zone pivot="programming-language-powershell"
+ [Rozšíření PowerShellu pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell).  
::: zone-end  

+ [Azure Functions rozšíření](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) pro Visual Studio Code. 

## <a name="create-an-azure-functions-project"></a>Vytvořit místní projekt 

V této části použijete Visual Studio Code k vytvoření místního projektu Azure Functions ve zvoleném jazyce. Později v tomto článku publikujete kód funkce do Azure. 

1. Zvolte ikonu Azure na řádku aktivity a potom v oblasti **Azure: funkce** vyberte ikonu **vytvořit nový projekt...** .

    ![Zvolit vytvořit nový projekt](media/functions-create-first-function-vs-code/create-new-project.png)

1. Zvolte umístění adresáře pro váš pracovní prostor projektu a zvolte **možnost vybrat**.

    > [!NOTE]
    > Tyto kroky jsou navržené tak, aby se dokončily mimo pracovní prostor. V tomto případě nevybírejte složku projektu, která je součástí pracovního prostoru.

1. Zadejte následující informace na následujících dotazech:

    ::: zone pivot="programming-language-csharp"
    + **Vyberte jazyk pro projekt funkce**: zvolte `C#`.
    ::: zone-end
    ::: zone pivot="programming-language-javascript"
    + **Vyberte jazyk pro projekt funkce**: zvolte `JavaScript`.
    ::: zone-end
    ::: zone pivot="programming-language-typescript"
    + **Vyberte jazyk pro projekt funkce**: zvolte `TypeScript`.
    ::: zone-end
    ::: zone pivot="programming-language-powershell"
    + **Vyberte jazyk pro projekt funkce**: zvolte `PowerShell`.
    ::: zone-end
    ::: zone pivot="programming-language-python"
    + **Vyberte jazyk pro projekt funkce**: zvolte `Python`.

    + **Vyberte alias Pythonu pro vytvoření virtuálního prostředí**: Zvolte umístění překladače Pythonu. Pokud se umístění nezobrazuje, zadejte úplnou cestu k binárnímu souboru Pythonu.  
    ::: zone-end

    + **Vyberte šablonu pro funkci prvního projektu**: zvolte `HTTP trigger`.
    
    + Zadejte **název funkce**: typ `HttpExample`.
    
    ::: zone pivot="programming-language-csharp"
    + Zadejte **obor názvů**: typ `My.Functions`. 
    ::: zone-end

    + **Úroveň autorizace**: vyberte `Anonymous`, která umožňuje komukoli zavolat koncový bod funkce. Další informace o úrovni autorizace najdete v tématu [autorizační klíče](functions-bindings-http-webhook-trigger.md#authorization-keys).

    + **Vyberte způsob, jakým chcete projekt otevřít**: zvolte `Add to workspace`.

1. Pomocí těchto informací Visual Studio Code generuje Azure Functions projekt pomocí triggeru protokolu HTTP. Můžete zobrazit soubory místních projektů v Průzkumníkovi. Další informace o souborech, které jsou vytvořeny, najdete v tématu [vygenerované soubory projektu](functions-develop-vs-code.md#generated-project-files). 

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-python"

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-run-function-test-local-vs-code-ps](../../includes/functions-run-function-test-local-vs-code-ps.md)]

::: zone-end

Po ověření, že se funkce na místním počítači spustí správně, je čas použít Visual Studio Code k publikování projektu přímo do Azure. 

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="run-the-function-in-azure"></a>Spuštění funkce v Azure

1. Zpátky v oblasti **Azure: Functions (funkce** ) na bočním panelu rozbalte novou aplikaci Function App v rámci vašeho předplatného. Rozbalte **funkce**, klikněte pravým tlačítkem myši (Windows) nebo Ctrl + Click (MacOS) na **HttpExample**a pak zvolte **Kopírovat adresu URL funkce**.

    ![Zkopírujte adresu URL funkce pro nový Trigger HTTP.](./media/functions-create-first-function-vs-code/function-copy-endpoint-url.png)

1. Vložte tuto adresu URL pro požadavek HTTP do adresního řádku prohlížeče, přidejte `name` řetězec dotazu jako `?name=Functions` na konec této adresy URL a pak spusťte požadavek. Adresa URL, která volá funkci aktivovanou protokolem HTTP, by měla být v následujícím formátu:

        http://<functionappname>.azurewebsites.net/api/httpexample?name=Functions 
        
    Následující příklad ukazuje odpověď v prohlížeči na požadavek Remote GET vracený funkcí: 

    ![Odezva funkce v prohlížeči](./media/functions-create-first-function-vs-code/functions-test-remote-browser.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete pokračovat k dalšímu kroku, [přidejte do své funkce vazbu fronty Azure Storage](functions-add-output-binding-storage-queue-vs-code.md), budete muset všechny vaše prostředky zachovávat, abyste mohli vytvářet informace o tom, co jste už provedli.

V opačném případě můžete pomocí následujících kroků odstranit aplikaci Function App a její související prostředky, abyste se vyhnuli dalším nákladům.

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

Další informace o nákladech na funkce najdete v tématu [odhad nákladů na plán spotřeby](functions-consumption-costs.md).

## <a name="next-steps"></a>Další kroky

Pomocí Visual Studio Code jste vytvořili aplikaci funkcí s jednoduchou funkcí aktivovanou protokolem HTTP. V dalším článku rozbalíte tuto funkci přidáním výstupní vazby. Tato vazba zapíše řetězec z požadavku HTTP do zprávy ve frontě Azure Queue Storage. 

> [!div class="nextstepaction"]
> [Přidání vazby fronty Azure Storage k funkci](functions-add-output-binding-storage-queue-vs-code.md)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
