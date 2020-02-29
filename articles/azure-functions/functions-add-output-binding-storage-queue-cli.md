---
title: Připojení Azure Functions k Azure Storage pomocí nástrojů příkazového řádku
description: Naučte se připojit Azure Functions k frontě Azure Storage přidáním výstupní vazby do projektu příkazového řádku.
ms.date: 02/07/2020
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: e3c37b368b723cc95302949baa8e85e2a8b621be
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/29/2020
ms.locfileid: "78201924"
---
# <a name="connect-azure-functions-to-azure-storage-using-command-line-tools"></a>Připojení Azure Functions k Azure Storage pomocí nástrojů příkazového řádku

V tomto článku integrujete frontu Azure Storage pomocí funkce a účtu úložiště, který jste vytvořili v [předchozím rychlém](functions-create-first-azure-function-azure-cli.md)startu. Tuto integraci dosáhnete pomocí *výstupní vazby* , která zapisuje data z požadavku HTTP do zprávy ve frontě. Po dokončení tohoto článku se neúčtují žádné další náklady nad několik centů za USD předchozího rychlého startu. Další informace o vazbách naleznete v tématu [Azure Functions triggery a koncepty vazeb](functions-triggers-bindings.md).

## <a name="configure-your-local-environment"></a>Konfigurace místního prostředí

Než začnete, musíte dokončit článek, [rychlý Start: vytvoření Azure Functions projektu z příkazového řádku](functions-create-first-azure-function-azure-cli.md). Pokud jste již vyčistili prostředky na konci tohoto článku, Projděte kroky znovu a znovu vytvořte aplikaci funkcí a související prostředky v Azure.

## <a name="retrieve-the-azure-storage-connection-string"></a>Načtení připojovacího řetězce Azure Storage

Při vytváření aplikace Function App v Azure v předchozím rychlém startu jste taky vytvořili účet úložiště. Připojovací řetězec pro tento účet je bezpečně uložený v nastavení aplikace v Azure. Stažením nastavení do souboru *Local. Settings. JSON* můžete toto připojení použít pro zápis do fronty úložiště ve stejném účtu, pokud je funkce spuštěná místně. 

1. Z kořenového adresáře projektu spusťte následující příkaz, ve kterém nahradíte `<APP_NAME>` názvem vaší aplikace Function App z předchozího rychlého startu. Tento příkaz přepíše všechny existující hodnoty v souboru.

    ```
    func azure functionapp fetch-app-settings <APP_NAME>
    ```
    
1. Otevřete *Local. Settings. JSON* a vyhledejte hodnotu s názvem `AzureWebJobsStorage`, což je připojovací řetězec účtu úložiště. Použijete název `AzureWebJobsStorage` a připojovací řetězec v dalších částech tohoto článku.

> [!IMPORTANT]
> Protože *Local. Settings. JSON* obsahuje tajné kódy stažené z Azure, tento soubor vždycky vylučte ze správy zdrojového kódu. Soubor *. gitignore* vytvořený pomocí projektu místní funkce vyloučí soubor ve výchozím nastavení.

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

## <a name="add-an-output-binding-definition-to-the-function"></a>Přidání definice výstupní vazby do funkce

I když funkce může mít jenom jednu Trigger, může mít víc vstupních a výstupních vazeb, které vám umožní připojit se k dalším službám a prostředkům Azure bez nutnosti psát vlastní kód pro integraci. 

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
Tyto vazby deklarujete v souboru *Function. JSON* ve složce Functions. Z předchozího rychlého startu soubor *Function. JSON* ve složce *HttpExample* obsahuje dvě vazby v kolekci `bindings`:  
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
Každá vazba má alespoň typ, směr a název. V předchozím příkladu je první vazba typu `httpTrigger` se směrovým `in`. Pro `in` směr určuje `name` název vstupního parametru, který je odeslán funkci při vyvolání triggerem.  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
Druhá vazba v kolekci je pojmenována `res`. Tato `http`ová vazba je výstupní vazba (`out`), která se používá k zápisu odpovědi HTTP. 

Chcete-li z této funkce zapisovat do fronty Azure Storage, přidejte `out` vazby typu `queue` s názvem `msg`, jak je znázorněno v následujícím kódu:

:::code language="json" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python"  
Druhá vazba v kolekci je typu `http` se směrovým `out`. v takovém případě speciální `name` `$return` označuje, že tato vazba používá návratovou hodnotu funkce namísto zadání vstupního parametru.

Chcete-li z této funkce zapisovat do fronty Azure Storage, přidejte `out` vazby typu `queue` s názvem `msg`, jak je znázorněno v následujícím kódu:

:::code language="json" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-powershell"  
Druhá vazba v kolekci je pojmenována `res`. Tato `http`ová vazba je výstupní vazba (`out`), která se používá k zápisu odpovědi HTTP. 

Chcete-li z této funkce zapisovat do fronty Azure Storage, přidejte `out` vazby typu `queue` s názvem `msg`, jak je znázorněno v následujícím kódu:

:::code language="json" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
V tomto případě je `msg` předána funkci jako výstupní argument. U `queue`ho typu musíte zadat také název fronty v `queueName` a zadat *název* Azure Storage připojení (z *Local. Settings. json*) v `connection`. 
::: zone-end  

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  

Další informace o podrobnostech vazeb najdete v tématu [Azure Functions triggery a koncepty vazeb](functions-triggers-bindings.md) a [Konfigurace výstupu fronty](functions-bindings-storage-queue-output.md#configuration).

## <a name="add-code-to-use-the-output-binding"></a>Přidat kód pro použití výstupní vazby

S vazbou fronty zadanou v *Function. JSON*můžete nyní aktualizovat funkci tak, aby přijímala výstupní parametr `msg` a zapisovat zprávy do fronty.

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

Pozor *, abyste nemuseli* psát žádný kód pro ověřování, získání odkazu na frontu nebo zápis dat. Všechny tyto úkoly integrace jsou pohodlně zpracovávány v Azure Functions modul runtime a výstupní vazba fronty.

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-extension-bundles-info](../../includes/functions-extension-bundles-info.md)]

## <a name="view-the-message-in-the-azure-storage-queue"></a>Zobrazení zprávy ve frontě Azure Storage

Tuto frontu můžete zobrazit v [Azure Portal](../storage/queues/storage-quickstart-queues-portal.md) nebo v [Průzkumník služby Microsoft Azure Storage](https://storageexplorer.com/). Tuto frontu můžete také zobrazit v rozhraní příkazového řádku Azure CLI, jak je popsáno v následujících krocích:

1. Otevřete soubor *Local. Setting. JSON* projektu funkce a zkopírujte hodnotu připojovacího řetězce. V terminálu nebo příkazovém okně spusťte následující příkaz, který vytvoří proměnnou prostředí s názvem `AZURE_STORAGE_CONNECTION_STRING`a místo `<MY_CONNECTION_STRING>`bude vkládat konkrétní připojovací řetězec. (Tato proměnná prostředí znamená, že připojovací řetězec nemusíte zadávat do každého následného příkazu pomocí argumentu `--connection-string`.)

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<MY_CONNECTION_STRING>"
    ```
    
    # <a name="cmd"></a>[Přepsat](#tab/cmd)
    
    ```cmd
    set AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    ---
    
1. Volitelné Pomocí příkazu [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) můžete zobrazit fronty úložiště ve vašem účtu. Výstup z tohoto příkazu by měl zahrnovat frontu s názvem `outqueue`, která byla vytvořena při zapsání první zprávy do této fronty.
    
    ```azure-cli
    az storage queue list --output tsv
    ```

1. K načtení zprávy z této fronty použijte příkaz [`az storage message get`](/cli/azure/storage/message#az-storage-message-get) , který by měl být první název, který jste použili při předchozím testování funkce. Příkaz přečte a Odebere první zprávu z fronty. 

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="cmd"></a>[Přepsat](#tab/cmd)
    
    ```cmd
    az storage message get --queue-name outqueue -o tsv --query [].{Message:content} > %TEMP%out.b64 && certutil -decode -f %TEMP%out.b64 %TEMP%out.txt > NUL && type %TEMP%out.txt && del %TEMP%out.b64 %TEMP%out.txt /q
    ```

    Tento skript používá příkaz certutil k dekódování kolekce zpráv s kódováním base64 z místního dočasného souboru. Pokud neexistuje žádný výstup, zkuste z skriptu odebrat `> NUL`, aby se zastavil výstup příkazu certutil, pokud dojde k chybě. 
    
    ---
    
    Vzhledem k tomu, že tělo zprávy je uložené v kódování [Base64](functions-bindings-storage-queue-trigger.md#encoding), je nutné zprávu dekódovat předtím, než se zobrazí. Po provedení `az storage message get`se zpráva z fronty odebere. Pokud byla v `outqueue`jenom jedna zpráva, při spuštění tohoto příkazu se nezobrazí zpráva a místo toho se zobrazí chyba.

## <a name="redeploy-the-project-to-azure"></a>Opětovné nasazení projektu do Azure

Teď, když jste místně ověřili, že funkce zapsala zprávu do fronty Azure Storage, můžete projekt znovu nasadit, aby se aktualizoval koncový bod běžící v Azure.

1. Ve složce *LocalFunctionsProj* použijte příkaz [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) pro opětovné nasazení projektu a nahraďte`<APP_NAME>` názvem vaší aplikace.

    ```
    func azure functionapp publish <APP_NAME>
    ```
    
1. Stejně jako v předchozím rychlém startu použijte k otestování znovu nasazené funkce prohlížeč nebo KUDRLINKOU.

    # <a name="browser"></a>[Prohlížeee](#tab/browser)
    
    Zkopírujte úplnou **adresu URL pro vyvolání** zobrazenou ve výstupu příkazu publikovat do adresního řádku prohlížeče a přidejte parametr dotazu `&name=Functions`. V prohlížeči by se měl zobrazit podobný výstup jako při spuštění funkce místně.

    ![Výstup funkce se spouští v Azure v prohlížeči.](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-browser.png)

    # <a name="curl"></a>[Curl](#tab/curl)
    
    Spusťte [`curl`](https://curl.haxx.se/) s **adresou URL vyvolání**a připojením parametru `&name=Functions`. Výstupem příkazu by měl být text "Hello".
    
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

::: zone pivot="programming-language-csharp"  
+ [Příklady kompletních projektů funkcí v C# ](/samples/browse/?products=azure-functions&languages=csharp).

+ [Referenční C# informace pro vývojáře Azure Functions](functions-dotnet-class-library.md)  
::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [Příklady kompletních projektů funkcí v JavaScriptu](/samples/browse/?products=azure-functions&languages=javascript).

+ [Azure Functions příručka pro vývojáře JavaScriptu](functions-reference-node.md)  
::: zone-end  
::: zone pivot="programming-language-typescript"  
+ [Příklady kompletních projektů funkcí v TypeScript](/samples/browse/?products=azure-functions&languages=typescript).

+ [Azure Functions příručka pro vývojáře TypeScript](functions-reference-node.md#typescript)  
::: zone-end  
::: zone pivot="programming-language-python"  
+ [Příklady kompletních projektů funkcí v Pythonu](/samples/browse/?products=azure-functions&languages=python)

+ [Příručka pro vývojáře Azure Functions Pythonu](functions-reference-python.md)  
::: zone-end  
::: zone pivot="programming-language-powershell"  
+ [Příklady kompletních projektů funkcí v prostředí PowerShell](/samples/browse/?products=azure-functions&languages=azurepowershell).

+ [Azure Functions příručka pro vývojáře PowerShellu](functions-reference-powershell.md) 
::: zone-end
+ [Aktivační události a vazby Azure Functions](functions-triggers-bindings.md)

+ [Stránka s cenami funkcí](https://azure.microsoft.com/pricing/details/functions/)

+ [Odhad nákladů na plán spotřeby](functions-consumption-costs.md) 
