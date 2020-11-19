---
title: Připojení Azure Functions k Azure Storage pomocí nástrojů příkazového řádku
description: Naučte se připojit Azure Functions k frontě Azure Storage přidáním výstupní vazby do projektu příkazového řádku.
ms.date: 02/07/2020
ms.topic: quickstart
ms.custom: devx-track-python
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 6d0d559579e841553e04c26bf4dcc78934de3e95
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2020
ms.locfileid: "94920198"
---
# <a name="connect-azure-functions-to-azure-storage-using-command-line-tools"></a>Připojení Azure Functions k Azure Storage pomocí nástrojů příkazového řádku

V tomto článku integrujete frontu Azure Storage pomocí funkce a účtu úložiště, který jste vytvořili v předchozím článku rychlý Start. Tuto integraci dosáhnete pomocí *výstupní vazby* , která zapisuje data z požadavku HTTP do zprávy ve frontě. Po dokončení tohoto článku se neúčtují žádné další náklady nad několik centů za USD předchozího rychlého startu. Další informace o vazbách naleznete v tématu [Azure Functions triggery a koncepty vazeb](functions-triggers-bindings.md).

## <a name="configure-your-local-environment"></a>Konfigurace místního prostředí

::: zone pivot="programming-language-csharp"  
Než začnete, musíte dokončit článek, [rychlý Start: vytvoření Azure Functions projektu z příkazového řádku](create-first-function-cli-csharp.md). Pokud jste již vyčistili prostředky na konci tohoto článku, Projděte kroky znovu a znovu vytvořte aplikaci funkcí a související prostředky v Azure.  
::: zone-end  
::: zone pivot="programming-language-javascript"  
Než začnete, musíte dokončit článek, [rychlý Start: vytvoření Azure Functions projektu z příkazového řádku](create-first-function-cli-node.md). Pokud jste již vyčistili prostředky na konci tohoto článku, Projděte kroky znovu a znovu vytvořte aplikaci funkcí a související prostředky v Azure.  
::: zone-end   
::: zone pivot="programming-language-java"  
Než začnete, musíte dokončit článek, [rychlý Start: vytvoření Azure Functions projektu z příkazového řádku](create-first-function-cli-java.md). Pokud jste již vyčistili prostředky na konci tohoto článku, Projděte kroky znovu a znovu vytvořte aplikaci funkcí a související prostředky v Azure.  
::: zone-end   
::: zone pivot="programming-language-typescript"  
Než začnete, musíte dokončit článek, [rychlý Start: vytvoření Azure Functions projektu z příkazového řádku](create-first-function-cli-typescript.md). Pokud jste již vyčistili prostředky na konci tohoto článku, Projděte kroky znovu a znovu vytvořte aplikaci funkcí a související prostředky v Azure.  
::: zone-end   
::: zone pivot="programming-language-python"  
Než začnete, musíte dokončit článek, [rychlý Start: vytvoření Azure Functions projektu z příkazového řádku](create-first-function-cli-python.md). Pokud jste již vyčistili prostředky na konci tohoto článku, Projděte kroky znovu a znovu vytvořte aplikaci funkcí a související prostředky v Azure.  
::: zone-end   
::: zone pivot="programming-language-powershell"  
Než začnete, musíte dokončit článek, [rychlý Start: vytvoření Azure Functions projektu z příkazového řádku](create-first-function-cli-powershell.md). Pokud jste již vyčistili prostředky na konci tohoto článku, Projděte kroky znovu a znovu vytvořte aplikaci funkcí a související prostředky v Azure.  
::: zone-end   

[!INCLUDE [functions-cli-get-storage-connection](../../includes/functions-cli-get-storage-connection.md)]

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

[!INCLUDE [functions-add-output-binding-cli](../../includes/functions-add-output-binding-cli.md)]

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  
::: zone pivot="programming-language-java" 
[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]
::: zone-end   

Další informace o podrobnostech vazeb najdete v tématu [Azure Functions triggery a koncepty vazeb](functions-triggers-bindings.md) a [Konfigurace výstupu fronty](functions-bindings-storage-queue-output.md#configuration).

## <a name="add-code-to-use-the-output-binding"></a>Přidat kód pro použití výstupní vazby

Je-li definována vazba fronty, můžete nyní aktualizovat funkci tak, aby přijímala `msg` výstupní parametr, a zapisovat zprávy do fronty.

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

Pozor *, abyste nemuseli* psát žádný kód pro ověřování, získání odkazu na frontu nebo zápis dat. Všechny tyto úkoly integrace jsou pohodlně zpracovávány v Azure Functions modul runtime a výstupní vazba fronty.

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-extension-bundles-info](../../includes/functions-extension-bundles-info.md)]

## <a name="view-the-message-in-the-azure-storage-queue"></a>Zobrazení zprávy ve frontě Azure Storage

[!INCLUDE [functions-add-output-binding-view-queue-cli](../../includes/functions-add-output-binding-view-queue-cli.md)]

## <a name="redeploy-the-project-to-azure"></a>Opětovné nasazení projektu do Azure

Teď, když jste místně ověřili, že funkce zapsala zprávu do fronty Azure Storage, můžete projekt znovu nasadit, aby se aktualizoval koncový bod běžící v Azure.

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp" 
Ve složce *LocalFunctionsProj* použijte [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) příkaz pro opětovné nasazení projektu a nahraďte `<APP_NAME>` názvem vaší aplikace.

```
func azure functionapp publish <APP_NAME>
```
::: zone-end  

::: zone pivot="programming-language-java" 

V místní složce projektu použijte následující příkaz Maven pro opětovné publikování projektu:
```
mvn azure-functions:deploy
```
::: zone-end

## <a name="verify-in-azure"></a>Ověření v Azure

1. Stejně jako v předchozím rychlém startu použijte k otestování znovu nasazené funkce prohlížeč nebo KUDRLINKOU.

    # <a name="browser"></a>[Prohlížeč](#tab/browser)
    
    Zkopírujte úplnou **adresu URL pro vyvolání** zobrazenou ve výstupu příkazu publikovat do adresního řádku prohlížeče a přidejte parametr dotazu `&name=Functions` . V prohlížeči by se měl zobrazit podobný výstup jako při spuštění funkce místně.

    ![Výstup funkce se spouští v Azure v prohlížeči.](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-browser.png)

    # <a name="curl"></a>[Curl](#tab/curl)
    
    Spusťte [`curl`](https://curl.haxx.se/) s **adresou URL vyvolání** a přidejte parametr `&name=Functions` . Výstupem příkazu by měl být text "Hello".
    
    ![Výstup funkce se spouští v Azure pomocí KUDRLINKOU.](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-curl.png)

    --- 

1. Znovu zkontrolujte frontu úložiště, jak je popsáno v předchozí části, a ověřte tak, že obsahuje novou zprávu zapsanou do fronty.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení použijte následující příkaz k odstranění skupiny prostředků a všech jejích obsažených prostředků, abyste se vyhnuli vzniku dalších nákladů.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Další kroky

Aktualizovali jste funkci aktivovanou protokolem HTTP, která zapisuje data do fronty úložiště. Nyní se můžete dozvědět víc o vývoji funkcí z příkazového řádku pomocí základních nástrojů a Azure CLI:

+ [Práce s Azure Functions Core Tools](functions-run-local.md)  

+ [Aktivační události a vazby Azure Functions](functions-triggers-bindings.md)

::: zone pivot="programming-language-csharp"  
+ [Příklady kompletních projektů funkcí v jazyce C#](/samples/browse/?products=azure-functions&languages=csharp).

+ [Referenční informace pro vývojáře v jazyce C# Azure Functions](functions-dotnet-class-library.md)  

[previous-quickstart]: create-first-function-cli-csharp.md

::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [Příklady kompletních projektů funkcí v JavaScriptu](/samples/browse/?products=azure-functions&languages=javascript).

+ [Azure Functions příručka pro vývojáře JavaScriptu](functions-reference-node.md)  

[previous-quickstart]: create-first-function-cli-javascript.md
::: zone-end  
::: zone pivot="programming-language-typescript"  
+ [Příklady kompletních projektů funkcí v TypeScript](/samples/browse/?products=azure-functions&languages=typescript).

+ [Azure Functions příručka pro vývojáře TypeScript](functions-reference-node.md#typescript)  

[previous-quickstart]: create-first-function-cli-typescript.md
::: zone-end  
::: zone pivot="programming-language-python"  
+ [Příklady kompletních projektů funkcí v Pythonu](/samples/browse/?products=azure-functions&languages=python)

+ [Příručka pro vývojáře Azure Functions Pythonu](functions-reference-python.md)  

[previous-quickstart]: create-first-function-cli-python.md
::: zone-end  
::: zone pivot="programming-language-powershell"  
+ [Příklady kompletních projektů funkcí v prostředí PowerShell](/samples/browse/?products=azure-functions&languages=azurepowershell).

+ [Azure Functions příručka pro vývojáře PowerShellu](functions-reference-powershell.md) 

[previous-quickstart]: create-first-function-cli-powershell.md
::: zone-end
