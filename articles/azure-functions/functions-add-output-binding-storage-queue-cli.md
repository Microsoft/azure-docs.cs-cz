---
title: Připojení funkcí Azure k Úložišti Azure pomocí nástrojů příkazového řádku
description: Zjistěte, jak připojit funkce Azure k frontě Azure Storage přidáním výstupní vazby do projektu příkazového řádku.
ms.date: 02/07/2020
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: f9d9573523083b6355f423b7b3db94b795d8657f
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673333"
---
# <a name="connect-azure-functions-to-azure-storage-using-command-line-tools"></a>Připojení funkcí Azure k Úložišti Azure pomocí nástrojů příkazového řádku

V tomto článku integrujete frontu služby Azure Storage s účtem funkce a úložiště, které jste [vytvořili](functions-create-first-azure-function-azure-cli.md)v předchozím rychlém startu . Dosažení této integrace pomocí *výstupní vazby,* která zapisuje data z požadavku HTTP na zprávu ve frontě. Dokončení tohoto článku nevznikají žádné další náklady nad rámec několika USD centů z předchozího rychlého startu. Další informace o vazbách najdete v [tématu Azure Functions triggers and bindings concepts](functions-triggers-bindings.md).

## <a name="configure-your-local-environment"></a>Konfigurace místního prostředí

Než začnete, musíte dokončit [článek, Úvodní příručka: Vytvořte projekt Azure Functions z příkazového řádku](functions-create-first-azure-function-azure-cli.md). Pokud jste už vyčistili prostředky na konci tohoto článku, projděte si další kroky k opětovnému vytvoření aplikace funkce a souvisejících prostředků v Azure.

[!INCLUDE [functions-cli-get-storage-connection](../../includes/functions-cli-get-storage-connection.md)]

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

[!INCLUDE [functions-add-output-binding-cli](../../includes/functions-add-output-binding-cli.md)]

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  
::: zone pivot="programming-language-java" 
[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]
::: zone-end   

Další informace o podrobnostech vazby, najdete v [tématu Azure Functions aktivační události a vazby koncepty](functions-triggers-bindings.md) a [konfigurace výstupu fronty](functions-bindings-storage-queue-output.md#configuration).

## <a name="add-code-to-use-the-output-binding"></a>Přidání kódu pro použití výstupní vazby

S definovanou vazbou fronty můžete nyní aktualizovat `msg` funkci tak, aby přijímali výstupní parametr a zapisovat zprávy do fronty.

::: zone pivot="programming-language-python"     
[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]
::: zone-end  

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"  
[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]  
::: zone-end

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]
::: zone-end 

::: zone pivot="programming-language-java"
[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]
::: zone-end

Všimněte si, že *není* nutné psát žádný kód pro ověřování, získání odkazu na frontu nebo zápis dat. Všechny tyto úlohy integrace jsou pohodlně zpracovány v Azure Functions runtime a fronty výstupní vazby.

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-extension-bundles-info](../../includes/functions-extension-bundles-info.md)]

## <a name="view-the-message-in-the-azure-storage-queue"></a>Zobrazení zprávy ve frontě Azure Storage

[!INCLUDE [functions-add-output-binding-view-queue-cli](../../includes/functions-add-output-binding-view-queue-cli.md)]

## <a name="redeploy-the-project-to-azure"></a>Opětovné nasazení projektu do Azure

Teď, když jste místně ověřili, že funkce napsala zprávu do fronty Azure Storage, můžete znovu nasadit projekt a aktualizovat koncový bod spuštěný v Azure.

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp" 
Ve složce *LocalFunctionsProj* [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) použijte příkaz k opětovnému`<APP_NAME>` nasazení projektu a nahrazením názvem aplikace.

```
func azure functionapp publish <APP_NAME>
```
::: zone-end  

::: zone pivot="programming-language-java" 

V místní složce projektu použijte k opětovnému publikování projektu následující příkaz Maven:
```
mvn azure-functions:deploy
```
::: zone-end

## <a name="verify-in-azure"></a>Ověření v Azure

1. Stejně jako v předchozím rychlém startu použijte prohlížeč nebo CURL k testování znovu nasazené funkce.

    # <a name="browser"></a>[Prohlížeč](#tab/browser)
    
    Zkopírujte úplnou **adresu URL invoke** zobrazenou ve výstupu příkazu publikovat `&name=Functions`do adresního řádku prohlížeče a přidejte parametr dotazu . Prohlížeč by měl zobrazit podobný výstup jako při spuštění funkce místně.

    ![Výstup funkce běží v Azure v prohlížeči](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-browser.png)

    # <a name="curl"></a>[Curl](#tab/curl)
    
    Spustit [`curl`](https://curl.haxx.se/) s **adresou URL**invoke `&name=Functions`, připojit parametr . Výstupem příkazu by měl být text "Funkce Hello".
    
    ![Výstup funkce běží v Azure pomocí CURL](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-curl.png)

    --- 

1. Zkontrolujte frontu úložiště znovu, jak je popsáno v předchozí části, chcete-li ověřit, zda obsahuje novou zprávu zapsanou do fronty.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení odstraňte skupinu prostředků a všechny její obsažené prostředky pomocí následujícího příkazu, abyste zabránili dalším nákladům.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Další kroky

Aktualizovali jste funkci aktivovanou protokolem HTTP tak, aby zapisovali data do fronty úložiště. Teď se můžete dozvědět více o vývoji funkcí z příkazového řádku pomocí základních nástrojů a Azure CLI:

+ [Práce s nástroji Azure Functions Core Tools](functions-run-local.md)  

::: zone pivot="programming-language-csharp"  
+ [Příklady úplných projektů funkce v c#](/samples/browse/?products=azure-functions&languages=csharp).

+ [Odkaz na vývojáře Azure Functions C#](functions-dotnet-class-library.md)  
::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [Příklady kompletních projektů funkcí v JavaScriptu](/samples/browse/?products=azure-functions&languages=javascript).

+ [Průvodce vývojářem JavaScriptu azure funkce](functions-reference-node.md)  
::: zone-end  
::: zone pivot="programming-language-typescript"  
+ [Příklady úplných projektů funkce v typescriptu](/samples/browse/?products=azure-functions&languages=typescript).

+ [Průvodce vývojářem Azure Functions TypeScript](functions-reference-node.md#typescript)  
::: zone-end  
::: zone pivot="programming-language-python"  
+ [Příklady kompletních projektů funkcí v Pythonu](/samples/browse/?products=azure-functions&languages=python).

+ [Průvodce vývojářem Azure Functions Pythonu](functions-reference-python.md)  
::: zone-end  
::: zone pivot="programming-language-powershell"  
+ [Příklady kompletních projektů funkcí v prostředí PowerShell](/samples/browse/?products=azure-functions&languages=azurepowershell).

+ [Průvodce vývojářem prostředí Azure Functions PowerShell](functions-reference-powershell.md) 
::: zone-end
+ [Azure Funkce aktivační události a vazby](functions-triggers-bindings.md)

+ [Stránka s cenami funkcí](https://azure.microsoft.com/pricing/details/functions/)

+ [Odhad nákladů plánu spotřeby](functions-consumption-costs.md) 
