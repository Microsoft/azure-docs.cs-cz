---
title: Přidání vazby fronty Azure Storage k funkci Pythonu
description: Naučte se, jak do funkce Pythonu Přidat výstupní vazbu Azure Storage fronty pomocí základních nástrojů Azure CLI a Functions.
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
ms.openlocfilehash: 34ec7c678410b2e0814f8dbb7a69257886cb891d
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639153"
---
# <a name="add-an-azure-storage-queue-binding-to-your-python-function"></a>Přidání vazby fronty Azure Storage k funkci Pythonu

Azure Functions umožňuje připojit služby Azure a další prostředky k funkcím bez nutnosti psát vlastní kód pro integraci. Tyto *vazby*, které představují vstupní i výstupní, jsou deklarovány v rámci definice funkce. Data z vazeb jsou k dispozici funkci jako parametry. *Trigger* je speciální typ vstupní vazby. I když má funkce pouze jednu Trigger, může mít více vstupních a výstupních vazeb. Další informace najdete v tématu [Azure Functions triggery a koncepty vazeb](functions-triggers-bindings.md).

V tomto článku se dozvíte, jak integrovat funkci, kterou jste vytvořili v [předchozím článku rychlý Start](functions-create-first-function-python.md) s frontou Azure Storage. Výstupní vazba, kterou přidáte do této funkce, zapisuje data z požadavku HTTP do zprávy ve frontě.

Většina vazeb vyžaduje uložený připojovací řetězec, který funkce používá pro přístup k vázané službě. Aby bylo toto připojení snazší, použijte účet úložiště, který jste vytvořili v aplikaci Function App. Připojení k tomuto účtu je již Uloženo v nastavení aplikace s názvem `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto článkem, proveďte kroky v [části 1 rychlého](functions-create-first-function-python.md)startu v Pythonu.

## <a name="download-the-function-app-settings"></a>Stažení nastavení Function App

V předchozím článku rychlý Start jste vytvořili aplikaci funkcí v Azure společně s požadovaným účtem úložiště. Připojovací řetězec pro tento účet je bezpečně uložený v nastavení aplikace v Azure. V tomto článku napíšete zprávy do fronty úložiště ve stejném účtu. Pokud se chcete připojit k účtu úložiště, když se funkce spouští místně, musíte si stáhnout nastavení aplikace do souboru Local. Settings. JSON. Spuštěním následujícího příkazu Azure Functions Core Tools Stáhněte nastavení do Local. Settings. JSON a nahraďte `<APP_NAME>` ho názvem vaší aplikace Function App z předchozího článku:

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

Možná se budete muset přihlásit ke svému účtu Azure.

> [!IMPORTANT]  
> Protože obsahuje tajné kódy, soubor Local. Settings. JSON se nikdy nepublikuje a měl by být vyloučený ze správy zdrojového kódu.

Potřebujete hodnotu `AzureWebJobsStorage`, což je připojovací řetězec účtu úložiště. Pomocí tohoto připojení ověříte, zda výstupní vazba funguje podle očekávání.

## <a name="enable-extension-bundles"></a>Povolit sady rozšíření

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

Nyní můžete přidat výstupní vazbu úložiště do projektu.

## <a name="add-an-output-binding"></a>Přidání výstupní vazby

V Functions každý typ vazby vyžaduje, aby `direction` `type`byl v souboru Function. JSON definován `name` , a a jedinečný. V závislosti na typu vazby můžou být potřeba další vlastnosti. [Konfigurace výstupu fronty](functions-bindings-storage-queue.md#output---configuration) popisuje pole požadovaná pro vazbu fronty Azure Storage.

Chcete-li vytvořit vazbu, přidejte objekt konfigurace vazby do souboru Function. JSON. Úpravou souboru Function. JSON ve složce HttpTrigger přidejte objekt do `bindings` pole, které má tyto vlastnosti:

| Vlastnost | Value | Popis |
| -------- | ----- | ----------- |
| **`name`** | `msg` | Název, který identifikuje parametr vazby, na který je odkazováno v kódu. |
| **`type`** | `queue` | Vazba je vazba fronty Azure Storage. |
| **`direction`** | `out` | Vazba je výstupní vazba. |
| **`queueName`** | `outqueue` | Název fronty, do které vazba zapisuje. `queueName` Pokud neexistuje, vazba ji vytvoří při prvním použití. |
| **`connection`** | `AzureWebJobsStorage` | Název nastavení aplikace, které obsahuje připojovací řetězec pro účet úložiště. Toto `AzureWebJobsStorage` nastavení obsahuje připojovací řetězec pro účet úložiště, který jste vytvořili pomocí aplikace Function App. |

Váš soubor Function. JSON by teď měl vypadat jako v tomto příkladu:

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

Po dokončení konfigurace je můžete začít používat pro přístup k vazbě jako atributu metody v signatuře funkce. `name` V následujícím příkladu `msg` je instance [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest)třídy.

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

Když použijete výstupní vazbu, nemusíte používat Azure Storage kód SDK pro ověřování, získání odkazu na frontu nebo zápis dat. Úlohy za běhu functions a Queue výstupní vazby jsou za vás.

## <a name="run-the-function-locally"></a>Místní spuštění funkce

Stejně jako dřív pomocí následujícího příkazu spusťte modul runtime Functions místně:

```bash
func host start
```

> [!NOTE]  
> Vzhledem k tomu, že v předchozím rychlém startu jste povolili sady rozšíření v Host. JSON, bylo [rozšíření pro vytváření úložiště](functions-bindings-storage-blob.md#packages---functions-2x) během spouštění staženo a nainstalováno společně s dalšími rozšířeními vazby společnosti Microsoft.

Zkopírujte adresu URL vaší funkce `HttpTrigger` z výstupu modulu runtime a vložte do panelu Adresa vašeho prohlížeče. Připojí řetězec `?name=<yourname>` dotazu k této adrese URL a spustí žádost. V prohlížeči by se měla zobrazit stejná odpověď jako v předchozím článku.

Tentokrát výstupní vazba také vytvoří ve svém účtu úložiště frontu `outqueue` s názvem a přidá zprávu se stejným řetězcem.

Dále pomocí Azure CLI zobrazíte novou frontu a ověříte, že se přidala zpráva. Frontu můžete také zobrazit pomocí [Průzkumník služby Microsoft Azure Storage][Azure Storage Explorer] nebo v [Azure Portal](https://portal.azure.com).

### <a name="set-the-storage-account-connection"></a>Nastavení připojení účtu úložiště

Otevřete soubor Local. Settings. JSON a zkopírujte hodnotu `AzureWebJobsStorage`, což je připojovací řetězec účtu úložiště. Nastavte proměnnou `AZURE_STORAGE_CONNECTION_STRING` prostředí na připojovací řetězec pomocí tohoto příkazu bash:

```azurecli-interactive
export AZURE_STORAGE_CONNECTION_STRING=<STORAGE_CONNECTION_STRING>
```

Při nastavování připojovacího řetězce v `AZURE_STORAGE_CONNECTION_STRING` proměnné prostředí můžete získat přístup k účtu úložiště bez nutnosti zadávat ověřování pokaždé, když.

### <a name="query-the-storage-queue"></a>Dotazování fronty úložiště

Pomocí [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) příkazu můžete zobrazit fronty úložiště ve vašem účtu, jako v následujícím příkladu:

```azurecli-interactive
az storage queue list --output tsv
```

Výstup z tohoto příkazu zahrnuje frontu s názvem `outqueue`, což je fronta, která se vytvořila při spuštění funkce.

Dále pomocí [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) příkazu Zobrazte zprávy v této frontě, jako v tomto příkladu:

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

Vrácený řetězec by měl být stejný jako zpráva, kterou jste odeslali k otestování funkce.

> [!NOTE]  
> Předchozí příklad dekóduje vrácený řetězec z formátu base64. Důvodem je to, že vazby úložiště fronty zapisují a čtou z Azure Storage jako [řetězce Base64](functions-bindings-storage-queue.md#encoding).

Teď je čas na opětovné publikování aktualizované aplikace Function App do Azure.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

Znovu můžete k otestování nasazené funkce použít kudrlinkou nebo prohlížeč. Stejně jako dřív přidejte řetězec `&name=<yourname>` dotazu k adrese URL, jako v tomto příkladu:

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

Můžete [zkontrolovat zprávu fronty úložiště](#query-the-storage-queue) a ověřit, zda výstupní vazba znovu generuje novou zprávu ve frontě.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Další postup

Aktualizovali jste funkci aktivovanou protokolem HTTP, která zapisuje data do fronty úložiště. Další informace o vývoji Azure Functions pomocí Pythonu najdete v [příručce pro vývojáře Azure Functions Pythonu](functions-reference-python.md) a v [Azure Functions triggerech a vazbách](functions-triggers-bindings.md).

Dále byste měli povolit Application Insights monitorování aplikace Function App:

> [!div class="nextstepaction"]
> [Povolit integraci Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/