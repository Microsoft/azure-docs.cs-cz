---
title: Připojení funkcí Azure k Úložišti Azure pomocí nástrojů příkazového řádku
description: Zjistěte, jak připojit funkce Azure k frontě Azure Storage přidáním výstupní vazby do projektu příkazového řádku.
ms.date: 02/07/2020
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 9181caf516d5c2003cfe99b125d2921732cbbb9d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79473383"
---
# <a name="connect-azure-functions-to-azure-storage-using-command-line-tools"></a>Připojení funkcí Azure k Úložišti Azure pomocí nástrojů příkazového řádku

V tomto článku integrujete frontu služby Azure Storage s účtem funkce a úložiště, které jste [vytvořili](functions-create-first-azure-function-azure-cli.md)v předchozím rychlém startu . Dosažení této integrace pomocí *výstupní vazby,* která zapisuje data z požadavku HTTP na zprávu ve frontě. Dokončení tohoto článku nevznikají žádné další náklady nad rámec několika USD centů z předchozího rychlého startu. Další informace o vazbách najdete v [tématu Azure Functions triggers and bindings concepts](functions-triggers-bindings.md).

## <a name="configure-your-local-environment"></a>Konfigurace místního prostředí

Než začnete, musíte dokončit [článek, Úvodní příručka: Vytvořte projekt Azure Functions z příkazového řádku](functions-create-first-azure-function-azure-cli.md). Pokud jste už vyčistili prostředky na konci tohoto článku, projděte si další kroky k opětovnému vytvoření aplikace funkce a souvisejících prostředků v Azure.

## <a name="retrieve-the-azure-storage-connection-string"></a>Načtení připojovacího řetězce úložiště Azure

Když jste vytvořili aplikaci funkce v Azure v předchozím rychlém startu, jste také vytvořili účet úložiště. Připojovací řetězec pro tento účet se bezpečně uchovává v nastavení aplikace v Azure. Stažením nastavení do souboru *local.settings.json* můžete toto připojení použít do fronty úložiště ve stejném účtu při místním spuštění funkce. 

1. V kořenovém adresáři projektu spusťte `<APP_NAME>` následující příkaz, který nahradí název aplikace funkce z předchozího rychlého startu. Tento příkaz přepíše všechny existující hodnoty v souboru.

    ```
    func azure functionapp fetch-app-settings <APP_NAME>
    ```
    
1. Otevřete *soubor local.settings.json* `AzureWebJobsStorage`a vyhledejte hodnotu s názvem , což je připojovací řetězec účtu úložiště. Název `AzureWebJobsStorage` a připojovací řetězec v jiných částech tohoto článku.

> [!IMPORTANT]
> Vzhledem k tomu, *že local.settings.json* obsahuje tajné klíče stažené z Azure, vždy vyloučit tento soubor ze správy zdrojového kódu. Soubor *.gitignore* vytvořený s projektem místních funkcí ve výchozím nastavení vyloučí soubor.

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

## <a name="add-an-output-binding-definition-to-the-function"></a>Přidání definice výstupní vazby do funkce

I když funkce může mít pouze jednu aktivační událost, může mít více vstupních a výstupních vazeb, které umožňují připojení k jiným službám a prostředkům Azure bez psaní vlastního integračního kódu. 

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
Deklarujete tyto vazby v souboru *function.json* ve složce funkce. Z předchozího rychlého startu soubor *function.json* ve složce *HttpExample* obsahuje dvě vazby v kolekci: `bindings`  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-python"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-powershell"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json" range="2-18":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript, programming-language-powershell, programming-language-typescript"  
Každá vazba má alespoň typ, směr a název. Ve výše uvedeném příkladu je `httpTrigger` první `in`vazba typu se směrem . Pro `in` směr `name` určuje název vstupního parametru, který je odeslán do funkce při vyvolání aktivační událostí.  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
Druhá vazba v kolekci je pojmenována `res`. Tato `http` vazba je`out`výstupní vazba ( ), která se používá k zápisu odpovědi HTTP. 

Chcete-li z této funkce zapsat `out` do fronty `queue` služby `msg`Azure Storage, přidejte vazbu typu s názvem , jak je znázorněno v níže uvedeném kódu:

:::code language="json" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python"  
Druhá vazba v kolekci `http` je `out`typu se směrem `name` , `$return` v takovém případě special of označuje, že tato vazba používá vrácenou hodnotu funkce spíše než poskytnutí vstupního parametru.

Chcete-li z této funkce zapsat `out` do fronty `queue` služby `msg`Azure Storage, přidejte vazbu typu s názvem , jak je znázorněno v níže uvedeném kódu:

:::code language="json" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-powershell"  
Druhá vazba v kolekci je pojmenována `res`. Tato `http` vazba je`out`výstupní vazba ( ), která se používá k zápisu odpovědi HTTP. 

Chcete-li z této funkce zapsat `out` do fronty `queue` služby `msg`Azure Storage, přidejte vazbu typu s názvem , jak je znázorněno v níže uvedeném kódu:

:::code language="json" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
V tomto `msg` případě je dána funkce jako výstupní argument. Pro `queue` typ musíte také zadat název fronty `queueName` a zadat *název* připojení Azure Storage (z *local.settings.json)* v . `connection` 
::: zone-end  

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  

Další informace o podrobnostech vazby, najdete v [tématu Azure Functions aktivační události a vazby koncepty](functions-triggers-bindings.md) a [konfigurace výstupu fronty](functions-bindings-storage-queue-output.md#configuration).

## <a name="add-code-to-use-the-output-binding"></a>Přidání kódu pro použití výstupní vazby

Pomocí vazby fronty zadané v *souboru function.json*můžete `msg` nyní aktualizovat funkci tak, aby přijímali výstupní parametr a zapisovat zprávy do fronty.

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

Všimněte si, že *není* nutné psát žádný kód pro ověřování, získání odkazu na frontu nebo zápis dat. Všechny tyto úlohy integrace jsou pohodlně zpracovány v Azure Functions runtime a fronty výstupní vazby.

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-extension-bundles-info](../../includes/functions-extension-bundles-info.md)]

## <a name="view-the-message-in-the-azure-storage-queue"></a>Zobrazení zprávy ve frontě Azure Storage

Frontu můžete zobrazit na [webu Azure Portal](../storage/queues/storage-quickstart-queues-portal.md) nebo v [Průzkumníkovi úložiště Microsoft Azure](https://storageexplorer.com/). Frontu můžete také zobrazit v příkazovém příkazu k dispozici azure, jak je popsáno v následujících krocích:

1. Otevřete soubor *local.setting.json* projektu funkce a zkopírujte hodnotu připojovacího řetězce. V terminálu nebo příkazovém okně spusťte `AZURE_STORAGE_CONNECTION_STRING`následující příkaz a vytvořte proměnnou prostředí s názvem a místo ní vložte konkrétní připojovací řetězec `<MY_CONNECTION_STRING>`. (Tato proměnná prostředí znamená, že není nutné zadávat připojovací řetězec ke každému následnému příkazu pomocí argumentu.) `--connection-string`

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<MY_CONNECTION_STRING>"
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/cmd)
    
    ```azurecli
    set AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    ---
    
1. (Nepovinné) Pomocí [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) příkazu můžete zobrazit fronty úložiště ve vašem účtu. Výstup z tohoto příkazu by `outqueue`měl obsahovat frontu s názvem , která byla vytvořena, když funkce napsala svou první zprávu do této fronty.
    
    ```azurecli
    az storage queue list --output tsv
    ```

1. Pomocí [`az storage message get`](/cli/azure/storage/message#az-storage-message-get) příkazu přečtěte zprávu z této fronty, která by měla být křestním jménem, které jste použili při testování funkce dříve. Příkaz přečte a odebere první zprávu z fronty. 

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/cmd)
    
    ```azurecli
    az storage message get --queue-name outqueue -o tsv --query [].{Message:content} > %TEMP%out.b64 && certutil -decode -f %TEMP%out.b64 %TEMP%out.txt > NUL && type %TEMP%out.txt && del %TEMP%out.b64 %TEMP%out.txt /q
    ```

    Tento skript používá certutil k dekódování kolekce zpráv kódované base64 z místního dočasného souboru. Pokud neexistuje žádný výstup, `> NUL` zkuste odebrat ze skriptu zastavit potlačení certutil výstup, v případě, že dojde k chybě. 
    
    ---
    
    Vzhledem k tomu, že text zprávy je uložen [base64 kódované](functions-bindings-storage-queue-trigger.md#encoding), musí být zpráva dekódována před zobrazením. Po spuštění `az storage message get`bude zpráva odebrána z fronty. Pokud v aplikaci `outqueue`byla pouze jedna zpráva , nenačtete zprávu při druhém spuštění tohoto příkazu a místo toho se zobrazí chyba.

## <a name="redeploy-the-project-to-azure"></a>Opětovné nasazení projektu do Azure

Teď, když jste místně ověřili, že funkce napsala zprávu do fronty Azure Storage, můžete znovu nasadit projekt a aktualizovat koncový bod spuštěný v Azure.

1. Ve složce *LocalFunctionsProj* [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) použijte příkaz k opětovnému`<APP_NAME>` nasazení projektu a nahrazením názvem aplikace.

    ```
    func azure functionapp publish <APP_NAME>
    ```
    
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
