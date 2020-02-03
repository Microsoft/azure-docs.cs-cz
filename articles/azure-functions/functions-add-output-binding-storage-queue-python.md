---
title: Přidání vazby fronty Azure Storage k funkci Pythonu
description: Integrujte frontu Azure Storage s funkcí Pythonu pomocí výstupní vazby.
ms.date: 01/15/2020
ms.topic: quickstart
ms.openlocfilehash: 14a381d13da052fd67679ed17bbb6b6711f7a0e6
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715364"
---
# <a name="add-an-azure-storage-queue-binding-to-your-python-function"></a>Přidání vazby fronty Azure Storage k funkci Pythonu

V tomto článku integrujete frontu Azure Storage pomocí funkce a účtu úložiště, který jste vytvořili v tématu [Vytvoření funkce Pythonu aktivované protokolem HTTP](functions-create-first-function-python.md). Tuto integraci dosáhnete pomocí *výstupní vazby* , která zapisuje data z požadavku HTTP do zprávy ve frontě. Po dokončení tohoto článku se neúčtují žádné další náklady nad několik centů za USD předchozího rychlého startu.

## <a name="prerequisites"></a>Předpoklady

- Dokončete rychlé zprovoznění a [vytvořte funkci Pythonu, která byla aktivována protokolem HTTP](functions-create-first-function-python.md). Pokud jste již vyčistili prostředky na konci tohoto článku, Projděte kroky znovu pro opětovné vytvoření aplikace Functions v Azure, ale ponechte prostředky na místě.

## <a name="retrieve-the-azure-storage-connection-string"></a>Načtení připojovacího řetězce Azure Storage

Při vytváření aplikace Function App v Azure v předchozím rychlém startu jste taky vytvořili účet úložiště. Připojovací řetězec pro tento účet je bezpečně uložený v nastavení aplikace v Azure. Stažením nastavení do souboru *Local. Settings. JSON* můžete toto připojení použít pro zápis do fronty úložiště ve stejném účtu, pokud je funkce spuštěná místně. 

1. Z kořenového adresáře projektu spusťte následující příkaz, ve kterém nahradíte `<app_name>` názvem vaší aplikace Function App z předchozího rychlého startu. Tento příkaz přepíše všechny existující hodnoty v souboru.

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. Otevřete *Local. Settings. JSON* a vyhledejte hodnotu s názvem `AzureWebJobsStorage`, což je připojovací řetězec účtu úložiště. Použijete název `AzureWebJobsStorage` a připojovací řetězec v dalších částech tohoto článku.

> [!IMPORTANT]
> Protože *Local. Settings. JSON* obsahuje tajné kódy stažené z Azure, tento soubor vždycky vylučte ze správy zdrojového kódu. Soubor *. gitignore* vytvořený pomocí projektu místní funkce vyloučí soubor ve výchozím nastavení.

## <a name="add-an-output-binding-to-functionjson"></a>Přidání výstupní vazby do Function. JSON

I když funkce může mít jenom jednu Trigger, může mít víc vstupních a výstupních vazeb, které vám umožní připojit se k dalším službám a prostředkům Azure bez nutnosti psát vlastní kód pro integraci. Tyto vazby deklarujete v souboru *Function. JSON* ve složce Functions, který je vhodný pro jazyk, který používáte pro funkci.

Z předchozího rychlého startu soubor *Function. JSON* ve složce *HttpExample* obsahuje dvě vazby v kolekci `bindings`:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

Každá vazba má alespoň typ, směr a název. V předchozím příkladu je první vazba typu `httpTrigger` se směrovým `in`. Pro `in` směr určuje `name` název vstupního parametru, který je odeslán funkci při vyvolání triggerem.

Druhá vazba je typu `http` se směrovým `out`. v takovém případě speciální `name` `$return` označuje, že tato vazba používá návratovou hodnotu funkce namísto zadání vstupního parametru.

Chcete-li z této funkce zapisovat do fronty Azure Storage, přidejte `out` vazby typu `queue` s názvem `msg`, jak je znázorněno v následujícím kódu:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

V tomto případě je `msg` předána funkci jako výstupní argument. U `queue`ho typu musíte zadat také název fronty v `queueName` a zadat *název* Azure Storage připojení (z *Local. Settings. json*) v `connection`.

Další informace o podrobnostech vazeb najdete v tématu [Azure Functions triggery a koncepty vazeb](functions-triggers-bindings.md) a [Konfigurace výstupu fronty](functions-bindings-storage-queue.md#output---configuration).

## <a name="add-code-to-use-the-output-binding"></a>Přidat kód pro použití výstupní vazby

S vazbou fronty zadanou v *Function. JSON*můžete nyní aktualizovat funkci tak, aby přijímala výstupní parametr `msg` a zapisovat zprávy do fronty.

Aktualizujte *HttpExample\\\_\_init\_\_. py* , aby odpovídaly následujícímu kódu, přidání parametru `msg` do definice funkce a `msg.set(name)` v rámci příkazu `if name:`.

```python
import logging

import azure.functions as func


def main(req: func.HttpRequest, msg: func.Out[func.QueueMessage]) -> str:

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        msg.set(name)
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
            "Please pass a name on the query string or in the request body",
            status_code=400
        )
```

Parametr `msg` je instancí [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest). Jeho `set` Metoda zapisuje do fronty zprávu s řetězcem, v tomto případě název předaný do funkce v řetězci dotazu URL.

Pozor *, abyste nemuseli* psát žádný kód pro ověřování, získání odkazu na frontu nebo zápis dat. Všechny tyto úkoly integrace jsou pohodlně zpracovávány v Azure Functions modul runtime a výstupní vazba fronty.

## <a name="run-and-test-the-function-locally"></a>Místní spuštění a testování funkce

1. V terminálu nebo příkazovém řádku přejděte do složky projektu funkcí *LocalFunctionProj*.

1. Spusťte hostitele modulu runtime Azure Functions pomocí následujícího příkazu.

    ```
    func host start
    ```

1. Po dokončení spuštění a zobrazení adresy URL koncového bodu `HttpExample` zkopírujte jeho adresu URL do prohlížeče a přidejte řetězec dotazu `?name=<your-name>`a provedete tak úplnou adresu URL, jako je `http://localhost:7071/api/HttpExample?name=Guido`. V prohlížeči by se měla zobrazit zpráva, například `Hello Guido` jako v předchozím článku.

    Pokud se nezobrazí koncový bod `HttpExample`, zastavte hostitele pomocí **kombinace kláves Ctrl**+**C** a zkontrolujte, zda výstup neobsahuje chyby. Například hostitel neaktivuje koncový bod, pokud dojde k chybě v *Function. JSON*. Také ověřte, že používáte `func host start` ze složky projektu functions a nikoli do složky *HttpExample* .

1. Až skončíte, zastavte hostitele pomocí **kombinace kláves Ctrl**+**C**.

> [!TIP]
> Během spouštění hostitel stáhne a nainstaluje [rozšíření vazby úložiště](functions-bindings-storage-blob.md#packages---functions-2x-and-higher) a další rozšíření Microsoft Binding Extensions. K této instalaci dochází, protože rozšíření vazby jsou ve výchozím nastavení povolena v souboru *Host. JSON* s následujícími vlastnostmi:
>
> ```json
> {
>     "version": "2.0",
>     "extensionBundle": {
>         "id": "Microsoft.Azure.Functions.ExtensionBundle",
>         "version": "[1.*, 2.0.0)"
>     }
> }
> ```
>
> Pokud narazíte na chyby související s rozšířeními vazby, ověřte, zda jsou v *Host. JSON*uvedeny výše uvedené vlastnosti.

## <a name="view-the-message-in-the-azure-storage-queue"></a>Zobrazení zprávy ve frontě Azure Storage

Když funkce vygeneruje odpověď HTTP pro webový prohlížeč, zavolá také `msg.set(name)`, která zapíše zprávu do fronty Azure Storage s názvem `outqueue`, jak je uvedeno ve vazbě fronty. Tuto frontu můžete zobrazit v [Azure Portal](../storage/queues/storage-quickstart-queues-portal.md) nebo v [Průzkumník služby Microsoft Azure Storage](https://storageexplorer.com/). Tuto frontu můžete také zobrazit v rozhraní příkazového řádku Azure CLI, jak je popsáno v následujících krocích:

1. Otevřete soubor *Local. Setting. JSON* projektu funkce a zkopírujte hodnotu připojovacího řetězce. V terminálu nebo příkazovém okně spusťte následující příkaz, který vytvoří proměnnou prostředí s názvem `AZURE_STORAGE_CONNECTION_STRING`a místo `<connection_string>`bude vkládat konkrétní připojovací řetězec. (Tato proměnná prostředí znamená, že připojovací řetězec nemusíte zadávat do každého následného příkazu pomocí argumentu `--connection-string`.)

    # <a name="bashtabbash"></a>[bash](#tab/bash)
    
    ```bash
    AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<connection_string>"
    ```
    
    # <a name="cmdtabcmd"></a>[Přepsat](#tab/cmd)
    
    ```cmd
    set AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    ---
    
1. Volitelné Pomocí příkazu [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) můžete zobrazit fronty úložiště ve vašem účtu. Výstup z tohoto příkazu by měl zahrnovat frontu s názvem `outqueue`, která byla vytvořena při zapsání první zprávy do této fronty.
    
    # <a name="bashtabbash"></a>[bash](#tab/bash)
    
    ```bash
    az storage queue list --output tsv
    ```
    
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    az storage queue list --output tsv
    ```
    
    # <a name="cmdtabcmd"></a>[Přepsat](#tab/cmd)
    
    ```cmd
    az storage queue list --output tsv
    ```
    
    ---


1. Pomocí příkazu [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) můžete zobrazit zprávy v této frontě, které by měly být křestní jméno, které jste použili při předchozím testování funkce. Příkaz načte první zprávu ve frontě v [kódování Base64](functions-bindings-storage-queue.md#encoding), takže musíte také dekódovat zprávu, aby se zobrazila jako text.

    # <a name="bashtabbash"></a>[bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="cmdtabcmd"></a>[Přepsat](#tab/cmd)
    
    Vzhledem k tomu, že je potřeba odkázat na shromažďování zpráv a dekódovat z formátu base64, spusťte PowerShell a použijte příkaz prostředí PowerShell.

    ---
    
## <a name="redeploy-the-project-to-azure"></a>Opětovné nasazení projektu do Azure

Teď, když jste místně otestovali funkci a ověřili, že zapsala zprávu do fronty Azure Storage, můžete znovu nasadit projekt a aktualizovat koncový bod běžící v Azure.

1. Ve složce *LocalFunctionsProj* použijte příkaz [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) pro opětovné nasazení projektu a nahraďte`<app_name>` názvem vaší aplikace.

    ```
    func azure functionapp publish <app_name>
    ```
    
1. Stejně jako v předchozím rychlém startu použijte k otestování znovu nasazené funkce prohlížeč nebo KUDRLINKOU.

    # <a name="browsertabbrowser"></a>[Prohlížeee](#tab/browser)
    
    Zkopírujte úplnou **adresu URL pro vyvolání** zobrazenou ve výstupu příkazu publikovat do adresního řádku prohlížeče a přidejte parametr dotazu `&name=Azure`. V prohlížeči by se měl zobrazit podobný výstup jako při spuštění funkce místně.

    ![Výstup funkce spuštěné v Azure v prohlížeči](./media/functions-create-first-function-python/function-test-cloud-browser.png)

    # <a name="curltabcurl"></a>[Curl](#tab/curl)
    
    Spusťte [kudrlinkou](https://curl.haxx.se/) s **adresou URL vyvolání**a připojením parametru `&name=Azure`. Výstupem příkazu by měl být text "Hello Azure".
    
    ![Výstup funkce spuštěný v Azure pomocí kudrlinkou](./media/functions-create-first-function-python/function-test-cloud-curl.png)

    --- 

1. Znovu zkontrolujte frontu úložiště, jak je popsáno v předchozí části, a ověřte tak, že obsahuje novou zprávu zapsanou do fronty.

    Pokud ke kontrole fronty používáte rozhraní příkazového řádku Azure, příkaz `az storage message peek` zobrazuje pouze první zprávu ve frontě. Chcete-li simulovat zpracování zpráv, použijte místo toho `az storage message get` se všemi stejnými argumenty. Příkaz `get` vrátí zprávu a odebere ji z fronty. Stejný příkaz pak můžete opakovat, dokud nebude fronta prázdná (a příkaz zobrazí chybu).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud budete pokračovat k dalšímu kroku, [Povolte integraci Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource), zachovejte všechny prostředky na místě, jak budete vytvářet informace o tom, co jste už provedli.

Jinak pomocí následujícího příkazu odstraňte skupinu prostředků a všechny její obsažené prostředky, abyste se vyhnuli vzniku dalších nákladů.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Povolení Application Insights integrace s aplikací Azure Functions](functions-monitoring.md#manually-connect-an-app-insights-resource)

Další zdroje informací:

- [Příklady kompletních projektů funkcí v Pythonu](/samples/browse/?products=azure-functions&languages=python)
- [Příručka pro vývojáře Azure Functions Pythonu](functions-reference-python.md)
- [Azure Functions triggery a vazby](functions-triggers-bindings.md).
- [Stránka s cenami funkcí](https://azure.microsoft.com/pricing/details/functions/)
- [Odhad nákladů na plán spotřeby](functions-consumption-costs.md)
