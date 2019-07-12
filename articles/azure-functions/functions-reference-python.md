---
title: Referenční informace pro vývojáře Python pro službu Azure Functions
description: Naučte se vyvíjet funkce s využitím Pythonu
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
keywords: Azure functions, funkce, zpracování událostí, dynamické výpočty, architektura bez serveru, python
ms.service: azure-functions
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/16/2018
ms.author: glenga
ms.openlocfilehash: 14594e95efe94fe38502dc6269627158c42a04be
ms.sourcegitcommit: dda9fc615db84e6849963b20e1dce74c9fe51821
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2019
ms.locfileid: "67622355"
---
# <a name="azure-functions-python-developer-guide"></a>Příručka pro vývojáře Azure Functions Pythonu

Tento článek je Úvod do vývoje služby Azure Functions pomocí Pythonu. Následující obsah se předpokládá, že jste už čtete [Příručka pro vývojáře Azure Functions](functions-reference.md).

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

## <a name="programming-model"></a>Programovací model

Funkce Azure by měl být bezstavové metodu ve svém skriptu Pythonu, která zpracovává vstup a výstup. Ve výchozím nastavení, modul runtime očekává, že metody k implementaci jako globální metoda volána `main()` v `__init__.py` souboru.

Výchozí konfigurace můžete změnit zadáním `scriptFile` a `entryPoint` vlastnosti *function.json* souboru. Například _function.json_ níže říká modul runtime pro použití `customentry()` metodu _main.py_ soubor jako vstupní bod pro vaši funkci Azure functions.

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  ...
}
```

Z triggerů a vazeb vázaná na funkci prostřednictvím metody atributů s použitím `name` vlastnosti definované v *function.json* souboru. Například _function.json_ níže popisuje jednoduchý funkce aktivované službou požadavek HTTP s názvem `req`:

```json
{
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

`__init__.py` Soubor obsahuje kód následující funkce:

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Volitelně můžete využít intellisense a automatické dokončování funkcí poskytovaných službou váš editor kódu, můžete také deklarovat typy atributů a návratový typ funkce pomocí poznámek typu Python. 

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Použití anotací Python součástí [azure.functions.*](/python/api/azure-functions/azure.functions?view=azure-python) balíček, který chcete svázat vaše metody vstup a výstup.

## <a name="folder-structure"></a>struktura složek

Struktura složek projektu Pythonu funkce vypadá takto:

```
 FunctionApp
 | - MyFirstFunction
 | | - __init__.py
 | | - function.json
 | - MySecondFunction
 | | - __init__.py
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.py
 | | - mySecondHelperFunction.py
 | - host.json
 | - requirements.txt
```

Existuje sdílený [host.json](functions-host-json.md) soubor, který můžete použít ke konfiguraci aplikace function app. Každá funkce má svůj vlastní soubor s kódem a vazby konfigurační soubor (function.json). 

Sdílený kód by udržováno do samostatné složky. Moduly ve složce SharedCode odkazovat, můžete tuto syntaxi:

```
from __app__.SharedCode import myFirstHelperFunction
```

Při nasazování projektu funkce do vaší aplikace funkcí v Azure, celý obsah *FunctionApp* složky by měl být součástí balíčku, ale nikoli složku.

## <a name="triggers-and-inputs"></a>Aktivační události a vstupy

Vstupy jsou rozděleny do dvou kategorií ve službě Azure Functions: vstup triggeru a další vstupy. I když se liší `function.json` souboru využití je stejný jako v kódu Pythonu.  Připojovací řetězce nebo tajné kódy pro aktivační událost a vstupní zdroje namapovat na hodnoty v `local.settings.json` soubor při místním spuštění a nastavení aplikace při spuštění v Azure. 

Například následující kód ukazuje rozdíl mezi těmito dvěma:

```json
// function.json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous",
      "route": "items/{id}"
    },
    {
      "name": "obj",
      "direction": "in",
      "type": "blob",
      "path": "samples/{id}",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

```json
// local.settings.json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "AzureWebJobsStorage": "<azure-storage-connection-string>"
  }
}
```

```python
# __init__.py
import azure.functions as func
import logging


def main(req: func.HttpRequest,
         obj: func.InputStream):

    logging.info(f'Python HTTP triggered function processed: {obj.read()}')
```

Při vyvolání funkce požadavek HTTP je předána funkci jako `req`. Načte položku z Azure Blob Storage na základě _ID_ v adrese URL trasy a k dispozici jako `obj` v těle funkce.  Tady se zadat připojovací řetězec se nachází v účtu úložiště `AzureWebJobsStorage` tedy stejný účet úložiště používá aplikace function app.


## <a name="outputs"></a>Výstupy

Výstup může být vyjádřena v návratovou hodnotu a výstupní parametry. Pokud existuje jenom jeden výstup, doporučujeme používat návratovou hodnotu. Pro několik výstupů budete muset použít výstupní parametry.

Používat návratovou hodnotu funkce jako hodnoty výstupní vazbu, `name` vlastnost vazby musí být nastavena na `$return` v `function.json`.

Chcete-li vytvořit několik výstupů, použijte `set()` metody poskytované `azure.functions.Out` rozhraní pro přiřazení hodnoty do vazby. Například následující funkce můžete vložit zprávu do fronty a také vrátit odpověď HTTP.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "msg",
      "direction": "out",
      "type": "queue",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

```python
import azure.functions as func


def main(req: func.HttpRequest,
         msg: func.Out[func.QueueMessage]) -> str:

    message = req.params.get('body')
    msg.set(message)
    return message
```

## <a name="logging"></a>Protokolování

Přístup k protokolovacího modulu runtime Azure Functions je k dispozici prostřednictvím kořenová [ `logging` ](https://docs.python.org/3/library/logging.html#module-logging) obslužné rutiny ve vaší aplikaci funkcí. Tento protokolovač se váže na Application Insights a umožňuje příznak upozornění a chyb zjištěných při provádění funkce.

Následující příklad zaprotokoluje informační zpráva při vyvolání funkce prostřednictvím triggeru HTTP.

```python
import logging


def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

Jsou k dispozici další metody protokolování, které umožňují zapisovat do konzoly v různými úrovněmi trasování:

| Metoda                 | Popis                                |
| ---------------------- | ------------------------------------------ |
| protokolování. **kritické (_zpráva_)**   | Zapíše zprávu s úrovní KRITICKÝ kořenové protokolovacího nástroje.  |
| protokolování. **chyby (_zpráva_)**   | Zapíše zprávu s chyba na úrovni root protokolovacího nástroje.    |
| protokolování. **upozornění (_zpráva_)**    | Zapíše zprávu s VAROVÁNÍM na úrovni root protokolovacího nástroje.  |
| protokolování. **info (_zpráva_)**    | Zapíše zprávu s informací o úrovni root protokolovacího nástroje.  |
| protokolování. **ladění (_zpráva_)** | Zapíše zprávu s úroveň ladění na kořenové protokolovacího nástroje.  |

## <a name="async"></a>Async

Doporučujeme vám, že napíšete vaši funkci Azure Functions jako asynchronní korutině s použitím `async def` příkazu.

```python
# Will be run with asyncio directly


async def main():
    await some_nonblocking_socket_io_op()
```

Pokud je funkce main() synchronní (žádné `async` kvalifikátor) jsme automaticky spustit funkci `asyncio` fondu vláken.

```python
# Would be run in an asyncio thread-pool


def main():
    some_blocking_socket_io()
```

## <a name="context"></a>Kontext

Získat kontext volání funkce během provádění, zahrnout `context` argument v podpisu. 

Příklad:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

**Kontextu** třída má následující metody:

`function_directory`  
Adresář, ve kterém je funkce spuštěná.

`function_name`  
Název funkce.

`invocation_id`  
ID aktuálního volání funkce.

## <a name="global-variables"></a>Globální proměnné

Není zaručeno, že stav vaší aplikace budou zachovány pro budoucí spuštění. Nicméně modul runtime služby Azure Functions často opětovně používá stejný proces pro více provedení stejné aplikace. Aby bylo možné ukládat do mezipaměti výsledky náročné výpočty, deklarujte ho jako globální proměnná. 

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="python-version-and-package-management"></a>Python version a package management

V současné době Azure Functions podporuje pouze Python 3.6.x (oficiální CPython distribuce).

Při vývoji, místním prostředí s využitím Azure Functions Core Tools nebo Visual Studio Code, přidejte názvy a verzemi požadované balíčky ke `requirements.txt` souboru a nainstalujte si ji pomocí `pip`.

Například následující příkaz Souborová služba a pip požadavky je možné nainstalovat `requests` balíček z PyPI.

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>Publikování do Azure

Jakmile budete připraveni k publikování, ujistěte se, že všechny závislosti jsou uvedeny v *souboru requirements.txt* souboru, který je umístěn v kořenovém adresáři projektu. Pokud používáte balíček, který vyžaduje kompilátor a nepodporuje instalaci souborů Wheel manylinux kompatibilní z PyPI, publikování na platformě Azure se nezdaří s následující chybou: 

```
There was an error restoring dependencies.ERROR: cannot install <package name - version> dependency: binary dependencies without wheels are not supported.  
The terminal process terminated with exit code: 1
```

Automatické vytváření a konfigurace požadované binární soubory [nainstalovat Docker](https://docs.docker.com/install/) na místním počítači a spusťte následující příkaz pro publikování pomocí [nástrojů Azure Functions Core](functions-run-local.md#v2) (funkce). Nezapomeňte nahradit `<app name>` s názvem aplikace function App v Azure. 

```bash
func azure functionapp publish <app name> --build-native-deps
```

Pod pokličkou, základní nástroje použít docker ke spuštění [mcr.microsoft.com/azure-functions/python](https://hub.docker.com/r/microsoft/azure-functions/) image jako kontejner na místním počítači. Pomocí tohoto prostředí ho potom sestavíte a nainstalujte požadované moduly ze zdrojového distribučního před balení pro poslední nasazení do Azure.

Vytváření závislostí a publikovat pomocí systému průběžné doručování (CD), [pomocí kanálů DevOps Azure](https://docs.microsoft.com/azure/azure-functions/functions-how-to-azure-devops). 

## <a name="unit-testing"></a>Testování částí

Funkce, které jsou napsané v Pythonu můžete testovat jako jiný kód Python pomocí standardní testovací architektury. Pro většinu vazby, je možné vytvořit mock vstupní objekt po vytvoření instance třídy odpovídající z `azure.functions` balíčku. Protože [ `azure.functions` ](https://pypi.org/project/azure-functions/) balíčku není ihned k dispozici, je nutné nainstalovat prostřednictvím vaší `requirements.txt` sdílené, jak je popsáno v [Python version a package management](#python-version-and-package-management) výše uvedené části.

Například tady je mock test funkci aktivovanou protokolem HTTP:

```json
{
  "scriptFile": "httpfunc.py",
  "entryPoint": "my_function",
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

```python
# myapp/httpfunc.py
import azure.functions as func
import logging

def my_function(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

```python
# myapp/test_httpfunc.py
import unittest

import azure.functions as func
from httpfunc import my_function


class TestFunction(unittest.TestCase):
    def test_my_function(self):
        # Construct a mock HTTP request.
        req = func.HttpRequest(
            method='GET',
            body=None,
            url='/api/HttpTrigger',
            params={'name': 'Test'})

        # Call the function.
        resp = my_function(req)

        # Check the output.
        self.assertEqual(
            resp.get_body(),
            b'Hello Test',
        )
```

Tady je další příklad, pomocí funkce aktivované frontou:

```python
# myapp/__init__.py
import azure.functions as func


def my_function(msg: func.QueueMessage) -> str:
    return f'msg body: {msg.get_body().decode()}'
```

```python
# myapp/test_func.py
import unittest

import azure.functions as func
from . import my_function


class TestFunction(unittest.TestCase):
    def test_my_function(self):
        # Construct a mock Queue message.
        req = func.QueueMessage(
            body=b'test')

        # Call the function.
        resp = my_function(req)

        # Check the output.
        self.assertEqual(
            resp,
            'msg body: test',
        )
```

## <a name="known-issues-and-faq"></a>Známé problémy a nejčastější dotazy

Všechny známé problémy a požadavky na funkce jsou sledovány v rámci [problémy Githubu](https://github.com/Azure/azure-functions-python-worker/issues) seznamu. Pokud narazíte na problém a nejde najít problému v Githubu otevře nový problém a zahrnují podrobný popis problému.

## <a name="next-steps"></a>Další kroky

Další informace naleznete v následujících materiálech:

* [Osvědčené postupy pro službu Azure Functions](functions-best-practices.md)
* [Azure Functions aktivačními událostmi a vazbami](functions-triggers-bindings.md)
* [Vazby služby BLOB storage](functions-bindings-storage-blob.md)
* [HTTP a Webhookové vazby](functions-bindings-http-webhook.md)
* [Vazby front úložiště](functions-bindings-storage-queue.md)
* [Trigger časovače](functions-bindings-timer.md)
