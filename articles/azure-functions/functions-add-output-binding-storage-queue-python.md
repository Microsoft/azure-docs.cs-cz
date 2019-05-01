---
title: Přidání vazby fronty Azure Storage do funkce Pythonu
description: Zjistěte, jak přidat vazby funkce Pythonu pomocí rozhraní příkazového řádku Azure a Functions Core Tools výstupní fronty Azure Storage.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 04/24/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: python
manager: jeconnoc
ms.openlocfilehash: aaeee4238110faa7a842073af8431b30b885db3c
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870037"
---
# <a name="add-an-azure-storage-queue-binding-to-your-function"></a>Přidání vazby fronty Azure Storage do funkce

Služba Azure Functions vám umožní připojit se službami Azure a dalších prostředků pro funkce bez nutnosti psát vlastní kód integrace. Tyto *vazby*, které představují vstupní a výstupní, jsou deklarované uvnitř definice funkce. Data z vazby je k dispozici funkce jako parametry. Aktivační události je zvláštní druh vstupní vazby. Funkce obsahuje pouze jednu aktivační událost, může mít více vstupní a výstupní vazby. Další informace najdete v tématu [aktivace Azure Functions a vazby koncepty](functions-triggers-bindings.md).

V tomto článku se dozvíte, jak integrovat funkce, kterou jste vytvořili v [předchozím článku rychlý Start](functions-create-first-function-python.md) pomocí fronty služby Azure Storage. Výstupní vazby, který přidáte do této funkce zapíše data z požadavku HTTP pro zprávu ve frontě. 

Většina vazby vyžadují uložené připojovací řetězec, který funkce používá pro přístup k vazbou služby. Aby bylo snazší, použijete účet úložiště, který jste vytvořili pomocí aplikace function app. Připojení k tomuto účtu je již uložen v aplikaci nastavení s názvem `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Požadavky

Předtím, než se pustíte do tohoto článku, proveďte kroky v [části 1 tohoto rychlého startu Python](functions-create-first-function-python.md).

## <a name="download-the-function-app-settings"></a>Stáhněte si nastavení aplikace function app

V předchozím článku rychlý start jste vytvořili aplikaci function app v Azure společně s účtem služby Storage. Připojovací řetězec pro tento účet je bezpečně uložen v nastavení aplikace v Azure. V tomto článku se zápis zpráv do fronty úložiště ve stejném účtu. Pro připojení k účtu úložiště při místním spuštění funkce, je třeba do souboru local.settings.json stáhnout nastavení aplikace. Spuštěním následujícího příkazu Azure Functions Core Tools ke stažení nastavení do souboru local.settings.json, nahrazení `<APP_NAME>` s názvem aplikace function App v předchozím článku:

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

Možná budete muset přihlásit ke svému účtu Azure.

> [!IMPORTANT]  
> Protože obsahuje tajné kódy, souboru local.settings.json nikdy publikuje a má být vyloučena ze správy zdrojového kódu.

Danou hodnotu potřebujete `AzureWebJobsStorage`, což je připojovací řetězec účtu úložiště. Toto připojení použijete k ověření, že výstupní vazbu funguje podle očekávání.

## <a name="add-an-output-binding"></a>Přidání výstupní vazby

Ve funkcích, každý typ vazby vyžaduje `direction`, `type`a jedinečný `name` byly definovány v souboru function.json. V závislosti na typu vazby může vyžadovat další vlastnosti. [Konfigurace výstupní fronty](functions-bindings-storage-queue.md#output---configuration) popisuje pole, vyžaduje se pro vazbu fronty Azure Storage.

Pokud chcete vytvořit vazbu, přidáte objekt konfigurace vazby na `function.json` souboru. Upravte soubor function.json ve složce HttpTrigger přidání objektu do `bindings` pole, které má následující vlastnosti:

| Vlastnost | Hodnota | Popis |
| -------- | ----- | ----------- |
| **`name`** | `msg` | Název identifikující vazby parametru odkazovaného v kódu. |
| **`type`** | `queue` | Vazba je vazbu fronty Azure Storage. |
| **`direction`** | `out` | Vazba je výstupní vazbu. |
| **`queueName`** | `outqueue` | Název, který vazba zapisuje do fronty. Když *queueName* neexistuje, vazba se vytvoří při prvním použití. |
| **`connection`** | `AzureWebJobsStorage` | Název nastavení aplikace, které obsahuje připojovací řetězec pro účet úložiště. `AzureWebJobsStorage` Nastavení obsahuje připojovací řetězec pro účet úložiště, který jste vytvořili pomocí aplikace function app. |

Váš soubor function.json by teď měl vypadat jako v následujícím příkladu:

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

## <a name="add-code-that-uses-the-output-binding"></a>Přidání kódu, který používá výstupní vazbu

Jakmile je nakonfigurovaná, můžete začít používat `name` vazby pro přístup k ní jako atribut method v signatuře funkce. V následujícím příkladu `msg` je instance [ `azure.functions.InputStream class` ](/python/api/azure-functions/azure.functions.httprequest).

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

Když použijete výstupní vazbu, není nutné použít sadu SDK služby Azure Storage kód pro ověření, získat odkaz na frontu nebo zápis dat. Funkce modulu runtime a fronty výstupní vazbu udělal za vás tyto úlohy.

## <a name="run-the-function-locally"></a>Místní spuštění funkce

Modul runtime služby Functions místně spustit stejně jako dříve použijte následující příkaz:

```bash
func host start
```

> [!NOTE]  
> Protože předchozím článku měli povolit rozšíření sady v host.json, [rozšíření vazby úložiště](functions-bindings-storage-blob.md#packages---functions-2x) byla stažena a nainstalována během spouštění.

Zkopírujte adresu URL vaší funkce `HttpTrigger` z výstupu modulu runtime a vložte do panelu Adresa vašeho prohlížeče. K této adrese URL připojte řetězec dotazu `?name=<yourname>` a proveďte požadavek. Stejně jako v předchozím článku byste měli vidět stejnou odpověď v prohlížeči.

Tentokrát výstupní vazbu také vytvoří frontu s názvem `outqueue` ve službě Storage account a přidá zprávu pomocí tohoto stejného řetězce.

V dalším kroku použijete rozhraní příkazového řádku Azure k zobrazení novou frontu a ověřte, že byl přidán zprávu. Můžete také zobrazit, pomocí vaší fronty [Microsoft Azure Storage Explorer] [ Azure Storage Explorer] nebo v [webu Azure portal](https://portal.azure.com).

### <a name="set-the-storage-account-connection"></a>Nastavit připojení k účtu úložiště

Otevřete soubor local.settings.json a zkopírujte hodnotu `AzureWebJobsStorage`, což je připojovací řetězec účtu úložiště. Nastavte `AZURE_STORAGE_CONNECTION_STRING` proměnnou prostředí pro připojovací řetězec pomocí následujícího příkazu prostředí Bash:

```azurecli-interactive
export AZURE_STORAGE_CONNECTION_STRING=<STORAGE_CONNECTION_STRING>
```

S připojovacím řetězcem, nastavte `AZURE_STORAGE_CONNECTION_STRING` proměnnou prostředí vašeho účtu úložiště můžou přistupovat bez nutnosti se poskytovat ověřování pokaždé, když.

### <a name="query-the-storage-queue"></a>Dotaz na frontu úložiště

Můžete použít [ `az storage queue list` ](/cli/azure/storage/queue#az-storage-queue-list) příkazu zobrazíte fronty úložiště ve vašem účtu, jako v následujícím příkladu:

```azurecli-interactive
az storage queue list --output tsv
```

Výstup tohoto příkazu obsahuje frontu s názvem `outqueue`, což je fronty, který byl vytvořen při spuštění funkce.

Pak pomocí [ `az storage message peek` ](/cli/azure/storage/message#az-storage-message-peek) příkazu zobrazte zprávy z této fronty, jako v následujícím příkladu.

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

Řetězec vrácený by měl být stejný jako zprávy odeslané k otestování funkce.

> [!NOTE]  
> V předchozím příkladu dekóduje vráceného řetězce z formátu base64. Důvodem je, že vazby úložiště front zapisovat a číst ze služby Azure Storage jako [řetězce ve formátu base64](functions-bindings-storage-queue.md#encoding).

Teď je čas se publikovat aplikace aktualizace funkcí do Azure.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

Znovu můžete otestujete nasazenou funkci pomocí cURL nebo v prohlížeči. Stejně jako předtím připojte řetězec dotazu `&name=<yourname>` na adresu URL, jako v následujícím příkladu:

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

Je možné [Zkontrolujte zprávu fronty úložiště](#query-the-storage-queue) k ověření, že výstupní vazbu znovu vygeneruje nové zprávy ve frontě.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Další postup

Aktualizovali jsme vaši funkci aktivovanou protokolem HTTP k zápisu dat do fronty úložiště. Další informace o vývoji funkcí Azure pomocí Pythonu najdete v tématu [– Příručka pro vývojáře Azure Functions Python](functions-reference-python.md) a [aktivační události Azure Functions a vazby](functions-triggers-bindings.md).

Dále byste měli povolit monitorování pomocí Application Insights pro aplikaci funkcí:

> [!div class="nextstepaction"]
> [Povolit integraci modulu Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/