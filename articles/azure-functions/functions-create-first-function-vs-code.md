---
title: Vytvoření první funkce v Azure pomocí Visual Studio Code
description: Vytvořte a publikujte do Azure jednoduchou funkci aktivovanou protokolem HTTP pomocí rozšíření Azure Functions ve Visual Studio Code.
ms.topic: quickstart
ms.date: 01/10/2020
ms.custom: mvc, devcenter
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 0540c7b01d693975f34515c7d13f0477ac74d4a1
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842124"
---
# <a name="quickstart-create-an-azure-functions-project-using-visual-studio-code"></a>Rychlý Start: vytvoření projektu Azure Functions pomocí Visual Studio Code

V tomto článku použijete Visual Studio Code k vytvoření funkce, která reaguje na požadavky HTTP. Po místním testování kódu ho nasadíte do prostředí Azure Functions bez serveru. Po dokončení tohoto rychlého startu dojde v účtu Azure k malým nákladům na několik centů nebo méně. 

K dispozici je také verze tohoto článku [na bázi CLI](functions-create-first-azure-function-azure-cli.md) .

## <a name="prerequisites"></a>Požadavky

+ [Visual Studio Code](https://code.visualstudio.com/) na některé z [podporovaných platforem](https://code.visualstudio.com/docs/supporting/requirements#_platforms). 
::: zone pivot="programming-language-csharp"
+ Rozšíření pro Visual Studio Code. [ C# ](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
::: zone-end
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ Verze [Node. js](https://nodejs.org/), Active LTS a Maintenance LTS (Doporučené 8.11.1 a 10.14.1).
::: zone-end
::: zone pivot="programming-language-python"
+ [Python 3,7](https://www.python.org/downloads/release/python-375/) nebo [Python 3,6](https://www.python.org/downloads/release/python-368/), který podporuje Azure Functions. Python 3,8 není zatím podporován. 

+ [Přípona Pythonu](https://marketplace.visualstudio.com/items?itemName=ms-python.python) pro Visual Studio Code.
::: zone-end
::: zone pivot="programming-language-powershell"
+ [Jádro PowerShellu](/powershell/scripting/install/installing-powershell-core-on-windows)

+ [.NET Core SDK 2.2 +](https://www.microsoft.com/net/download)

+ [Rozšíření PowerShellu pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell). 
::: zone-end

+ [Azure Functions rozšíření](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) pro Visual Studio Code. 

+ [Účet Azure](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing) s aktivním předplatným. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="create-an-azure-functions-project"></a>Vytvořit místní projekt 

V této části použijete Visual Studio Code k vytvoření místního projektu Azure Functions ve zvoleném jazyce. Později v tomto článku publikujete kód funkce do Azure. 

1. Zvolte ikonu Azure na řádku aktivity a potom v oblasti **Azure: funkce** vyberte ikonu **vytvořit nový projekt...** .

    ![Zvolit vytvořit nový projekt](media/functions-create-first-function-vs-code/create-new-project.png)

1. Zvolte umístění adresáře pro váš pracovní prostor projektu a zvolte **možnost vybrat**.

    > [!NOTE]
    > Tyto kroky jsou navržené tak, aby se dokončily mimo pracovní prostor. V tomto případě nevybírejte složku projektu, která je součástí pracovního prostoru.

1. Zadejte následující informace na následujících dotazech:

    ::: zone pivot="programming-language-csharp"

    | Výzva | Hodnota | 
    | ------ | ----- | 
    | Vyberte jazyk pro váš projekt funkce. | C# |
    | vybrat verzi | Azure Functions v2 | 
    | Vyberte šablonu pro funkci prvního projektu. | Trigger HTTP | 
    | Zadejte název funkce. | HttpExample | 
    | Zadejte obor názvů. | My. Functions | 
    | Úroveň autorizace | Anonymní | 
    | Vyberte, jak se má projekt otevřít. | Přidat do pracovního prostoru |

    ::: zone-end

    ::: zone pivot="programming-language-javascript"

    | Výzva | Hodnota | 
    | ------ | ----- | 
    | Vyberte jazyk pro váš projekt funkce. | JavaScript | 
    | vybrat verzi | Azure Functions v2 | 
    | Vyberte šablonu pro funkci prvního projektu. | Trigger HTTP | 
    | Zadejte název funkce. | HttpExample | 
    | Úroveň autorizace | Anonymní | 
    | Vyberte, jak se má projekt otevřít. | Přidat do pracovního prostoru |

    ::: zone-end

    ::: zone pivot="programming-language-typescript"

    | Výzva | Hodnota | 
    | ------ | ----- | 
    | Vyberte jazyk pro váš projekt funkce. | TypeScript | 
    | vybrat verzi | Azure Functions v2 | 
    | Vyberte šablonu pro funkci prvního projektu. | Trigger HTTP | 
    | Zadejte název funkce. | HttpExample | 
    | Úroveň autorizace | Anonymní | 
    | Vyberte, jak se má projekt otevřít. | Přidat do pracovního prostoru |

    ::: zone-end

    ::: zone pivot="programming-language-powershell"

    | Výzva | Hodnota | 
    | ------ | ----- | 
    | Vyberte jazyk pro váš projekt funkce. | PowerShell | 
    | vybrat verzi | Azure Functions v2 | 
    | Vyberte šablonu pro funkci prvního projektu. | Trigger HTTP | 
    | Zadejte název funkce. | HttpExample | 
    | Úroveň autorizace | Anonymní | 
    | Vyberte, jak se má projekt otevřít. | Přidat do pracovního prostoru |

    ::: zone-end

    ::: zone pivot="programming-language-python"

    | Výzva | Hodnota | 
    | ------ | ----- | 
    | Vyberte jazyk pro váš projekt funkce. | Python | 
    | vybrat verzi | Azure Functions v2 | 
    | Vyberte alias Pythonu pro vytvoření virtuálního prostředí. | Alias Pythonu | 
    | Vyberte šablonu pro funkci prvního projektu. | Trigger HTTP | 
    | Zadejte název funkce. | HttpExample | 
    | Úroveň autorizace | Anonymní | 
    | Vyberte, jak se má projekt otevřít. | Přidat do pracovního prostoru | 

    ::: zone-end

1. Visual Studio Code provádí následující akce:

    + Vytvoří projekt Azure Functions v novém pracovním prostoru, který obsahuje konfigurační soubory [Host. JSON](functions-host-json.md) a [Local. Settings. JSON](functions-run-local.md#local-settings-file) . 

    ::: zone pivot="programming-language-csharp"

    + Vytvoří [soubor knihovny tříd HttpExample.cs](functions-dotnet-class-library.md#functions-class-library-project) , který implementuje funkci.

    ::: zone-end
        
    ::: zone pivot="programming-language-javascript"
    
    + Vytvoří v kořenové složce soubor Package. JSON.

    + Vytvoří složku HttpExample, která obsahuje [soubor s definicí Function. JSON](functions-reference-node.md#folder-structure) a [soubor index. js](functions-reference-node.md#exporting-a-function), soubor Node. js, který obsahuje kód funkce.
    
    ::: zone-end
    
    ::: zone pivot="programming-language-typescript"
    
    + Vytvoří soubor Package. JSON a soubor tsconfig. JSON v kořenové složce.

    + Vytvoří složku HttpExample, která obsahuje [soubor s definicí Function. JSON](functions-reference-node.md#folder-structure) a [soubor index. TS](functions-reference-node.md#typescript), soubor TypeScript, který obsahuje kód funkce.
    
    ::: zone-end
    
    ::: zone pivot="programming-language-powershell"
    
    + Vytvoří složku HttpExample, která obsahuje [soubor definice Function. JSON](functions-reference-python.md#programming-model) a soubor run. ps1, který obsahuje kód funkce.
    
    ::: zone-end
    
    ::: zone pivot="programming-language-python"
    
    + Vytvoří soubor. txt s požadavky na úrovni projektu, ve kterém jsou uvedeny balíčky požadované funkcemi.
    
    + Vytvoří složku HttpExample, která obsahuje [soubor definice Function. JSON](functions-reference-python.md#programming-model) a soubor \_\_init\_\_. py, který obsahuje kód funkce.
    
    ::: zone-end

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

1. Zkopírujte adresu URL triggeru HTTP z panelu **Výstup**. Znovu přidejte řetězec dotazu `name` jako `?name=<yourname>` na konec této adresy URL a spusťte požadavek.

    Adresa URL, která volá funkci aktivovanou protokolem HTTP, by měla být v následujícím formátu:

        http://<functionappname>.azurewebsites.net/api/httpexample?name=<yourname> 

1. Vložte tuto novou adresu URL pro požadavek HTTP do panelu Adresa prohlížeče. Následující příklad ukazuje odpověď v prohlížeči na požadavek Remote GET vracený funkcí: 

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
