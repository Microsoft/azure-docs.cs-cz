---
title: Vytvoření první funkce v Azure pomocí Visual Studio Code
description: Vytvořte a publikujte do Azure jednoduchou funkci aktivovanou protokolem HTTP pomocí rozšíření Azure Functions ve Visual Studio Code.
ms.topic: quickstart
ms.date: 01/10/2020
ms.custom: mvc, devcenter
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 08575164334db965a9e5592b761a06205208de9e
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732772"
---
# <a name="quickstart-create-an-azure-functions-project-using-visual-studio-code"></a>Úvodní příručka: Vytvoření projektu Azure Functions pomocí kódu Visual Studia

V tomto článku pomocí kódu sady Visual Studio vytvořit funkci, která reaguje na požadavky HTTP. Po testování kódu místně jej nasadíte do prostředí Azure Functions bez serveru. Dokončení tohoto rychlého startu bude mít na účtu Azure malé náklady na několik usd centů nebo méně. 

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python" 
K dispozici je také verze tohoto článku [založená na cli.](functions-create-first-azure-function-azure-cli.md)
::: zone-end  

::: zone pivot="programming-language-java"  
> [!NOTE]
> Pokud VS Code není váš preferovaný vývojový nástroj, podívejte se na naše podobné návody pro vývojáře Java pomocí [Maven](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-java), [Gradle](/azure/azure-functions/functions-create-first-java-gradle) a [IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions).
::: zone-end  

## <a name="configure-your-environment"></a>Konfigurace prostředí

Než začnete, ujistěte se, že máte následující požadavky:

+ Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

::: zone pivot="programming-language-csharp,programming-language-powershell,programming-language-python"  
+ [Node.js](https://nodejs.org/), vyžadované systémem Windows pro npm. Pouze [verze LTS active LTS a Údržba LTS](https://nodejs.org/about/releases/). Pomocí `node --version` příkazu zkontrolujte svou verzi.
    Není vyžadováno pro místní vývoj na macOS a Linux.   
::: zone-end  
::: zone pivot="programming-language-javascript,programming-language-typescript"  
+ [Node.js](https://nodejs.org/), Verze LTS active LTS a Údržba (doporučeno 10.14.1). Pomocí `node --version` příkazu zkontrolujte svou verzi.
::: zone-end 
::: zone pivot="programming-language-python"
+ [Python 3.8](https://www.python.org/downloads/release/python-381/), [Python 3.7](https://www.python.org/downloads/release/python-375/), [Python 3.6](https://www.python.org/downloads/release/python-368/) jsou podporované funkcemi Azure (x64).
::: zone-end   
::: zone pivot="programming-language-powershell"
+ [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

+ Sada [SDK 2.2 core .NET](https://www.microsoft.com/net/download)  
::: zone-end  
::: zone pivot="programming-language-java"  
+ [Java Developer Kit](https://aka.ms/azure-jdks), verze 8.

+ [Apache Maven](https://maven.apache.org), verze 3.0 nebo vyšší.
::: zone-end  
+ [Visual Studio Code](https://code.visualstudio.com/) na jedné z [podporovaných platforem](https://code.visualstudio.com/docs/supporting/requirements#_platforms).  
::: zone pivot="programming-language-csharp"  
+ [Rozšíření C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) pro kód sady Visual Studio.  
::: zone-end  
::: zone pivot="programming-language-python"
+ [Rozšíření Pythonu](https://marketplace.visualstudio.com/items?itemName=ms-python.python) pro kód Visual Studia.  
::: zone-end  
::: zone pivot="programming-language-powershell"
+ [Rozšíření prostředí PowerShell pro kód sady Visual Studio](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell). 
::: zone-end  
::: zone pivot="programming-language-java"  
+ [Rozšiřující balíček Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)
::: zone-end  

+ [Rozšíření Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) pro kód Visual Studia. 

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Vytvoření místního projektu 

V této části použijete visual studio kód k vytvoření projektu místní funkce Azure ve zvoleném jazyce. Dále v tomto článku publikujete kód funkce do Azure. 

1. Vyberte ikonu Azure na panelu aktivit a pak v oblasti **Azure: Funkce** vyberte ikonu **Vytvořit nový projekt...**

    ![Zvolte Vytvořit nový projekt.](media/functions-create-first-function-vs-code/create-new-project.png)

1. Zvolte umístění adresáře pro pracovní prostor projektu a zvolte **Vybrat**.

    > [!NOTE]
    > Tyto kroky byly navrženy tak, aby byly dokončeny mimo pracovní prostor. V tomto případě nevybírejte složku projektu, která je součástí pracovního prostoru.

1. Na výzvách uveďte následující informace:

    ::: zone pivot="programming-language-csharp"
    + **Vyberte jazyk pro projekt** `C#`funkcí : Zvolte .
    ::: zone-end
    ::: zone pivot="programming-language-javascript"
    + **Vyberte jazyk pro projekt** `JavaScript`funkcí : Zvolte .
    ::: zone-end
    ::: zone pivot="programming-language-typescript"
    + **Vyberte jazyk pro projekt** `TypeScript`funkcí : Zvolte .
    ::: zone-end
    ::: zone pivot="programming-language-powershell"
    + **Vyberte jazyk pro projekt** `PowerShell`funkcí : Zvolte .
    ::: zone-end
    ::: zone pivot="programming-language-python"
    + **Vyberte jazyk pro projekt** `Python`funkcí : Zvolte .

    + **Vyberte alias Pythonu pro vytvoření virtuálního prostředí**: Zvolte umístění interpreta Pythonu. Pokud se umístění nezobrazuje, zadejte úplnou cestu k binárnímu souboru Pythonu.  
    ::: zone-end

    ::: zone pivot="programming-language-java"  
    + **Vyberte jazyk pro projekt** `Java`funkcí : Zvolte .

    + **Zadejte ID skupiny**: Zvolte `com.function`.

    + **Zadejte ID artefaktu**: Zvolte `myFunction`.

    + **Zadejte verzi** `1.0-SNAPSHOT`: Zvolte .

    + **Zadejte název**balíčku `com.function`: Zvolte .

    + **Zadejte název**aplikace `myFunction-12345`: Zvolte .
    ::: zone-end  
    ::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"
    + **Vyberte šablonu pro první funkci** `HTTP trigger`projektu : Zvolte .
    
    + **Zadejte název**funkce `HttpExample`: Zadejte .
    ::: zone-end  
    ::: zone pivot="programming-language-csharp"
    + **Zadejte obor**názvů `My.Functions`: Zadejte . 
    ::: zone-end  
    ::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"
    + **Úroveň autorizace**: Zvolte `Anonymous`, který umožňuje komukoli volat koncový bod vaší funkce. Další informace o úrovni autorizace naleznete v [tématu Autorizace klíčů](functions-bindings-http-webhook-trigger.md#authorization-keys).
    ::: zone-end  
    + **Vyberte, jak chcete projekt**otevřít `Add to workspace`: Zvolte .

1. Pomocí těchto informací Visual Studio Code generuje projekt Azure Functions s aktivační událostí HTTP. Můžete zobrazit místní soubory projektu v Průzkumníkovi. Další informace o vytvořených souborech naleznete v [tématu Generované soubory projektu](functions-develop-vs-code.md#generated-project-files). 

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-python,programming-language-java"

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-run-function-test-local-vs-code-ps](../../includes/functions-run-function-test-local-vs-code-ps.md)]

::: zone-end

Po ověření, že funkce běží správně v místním počítači, je čas použít Visual Studio Kód publikovat projekt přímo do Azure. 

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="run-the-function-in-azure"></a>Spuštění funkce v Azure

1. Zpět v **Oblasti Azure: Funkce** v postranním panelu rozbalte novou aplikaci funkcí v rámci předplatného. Rozbalte **funkce**, klepněte pravým tlačítkem myši (Windows) nebo Ctrl + klepněte na tlačítko (macOS) na **httpexample**a pak zvolte **Kopírovat adresu URL funkce**.

    ![Kopírování adresy URL funkce pro novou aktivační událost HTTP](./media/functions-create-first-function-vs-code/function-copy-endpoint-url.png)

1. Vložte tuto adresu URL pro požadavek HTTP do `name` adresního řádku prohlížeče, přidejte řetězec dotazu na `?name=Functions` konec této adresy URL a potom požadavek proveďte. Adresa URL, která volá funkci aktivovanou protokolem HTTP, by měla být v následujícím formátu:

        http://<functionappname>.azurewebsites.net/api/httpexample?name=Functions 
        
    Následující příklad ukazuje odpověď v prohlížeči na vzdálený požadavek GET vrácený funkcí: 

    ![Odezva funkce v prohlížeči](./media/functions-create-first-function-vs-code/functions-test-remote-browser.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když budete pokračovat k dalšímu kroku, [přidejte do své funkce vazbu fronty Azure Storage](functions-add-output-binding-storage-queue-vs-code.md), budete muset mít všechny prostředky na místě, abyste mohli stavět na tom, co jste už udělali.

V opačném případě můžete pomocí následujících kroků odstranit aplikaci funkce a související prostředky, abyste zabránili vzniku dalších nákladů.

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

Další informace o nákladech na funkce naleznete v [tématu Odhad nákladů plánu spotřeby](functions-consumption-costs.md).

## <a name="next-steps"></a>Další kroky

Pomocí Visual Studio Code jste vytvořili aplikaci funkcí s jednoduchou funkcí aktivovanou protokolem HTTP. V dalším článku rozbalíte tuto funkci přidáním výstupní vazby. Tato vazba zapíše řetězec z požadavku HTTP na zprávu ve frontě služby Azure Queue Storage. 

> [!div class="nextstepaction"]
> [Přidání vazby fronty azure storage do vaší funkce](functions-add-output-binding-storage-queue-vs-code.md)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
