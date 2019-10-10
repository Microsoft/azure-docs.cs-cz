---
title: Referenční dokumentace pro vývojáře v Pythonu pro Azure Functions
description: Vysvětlení, jak vyvíjet funkce pomocí Pythonu
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
keywords: funkce Azure Functions, Functions, zpracování událostí, dynamický výpočet, architektura bez serveru, Python
ms.service: azure-functions
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/16/2018
ms.author: glenga
ms.openlocfilehash: 4fd73f528ac823a8e794a880f87dd5f8872e1251
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243279"
---
# <a name="azure-functions-python-developer-guide"></a>Příručka pro vývojáře Azure Functions Pythonu

Tento článek představuje úvod k vývoji Azure Functions s využitím Pythonu. Níže uvedený obsah předpokládá, že už jste si přečetli [příručku pro vývojáře Azure Functions](functions-reference.md). 

Ukázkové projekty samostatné funkce v Pythonu najdete v [ukázkách funkcí Pythonu](/samples/browse/?products=azure-functions&languages=python). 

## <a name="programming-model"></a>Programovací model

Azure Functions očekává ve skriptu Pythonu funkci, která bude mít nestavovou metodu, která zpracuje vstup a vytvoří výstup. Ve výchozím nastavení očekává modul runtime metodu, která má být implementována jako globální metoda s názvem `main()` v souboru `__init__.py`. Můžete také [zadat alternativní vstupní bod](#alternate-entry-point).

Data z aktivačních událostí a vazeb jsou svázána s funkcí prostřednictvím atributů metody pomocí vlastnosti `name` definované v souboru *Function. JSON* . Například _funkce. JSON_ níže popisuje jednoduchou funkci aktivovanou požadavkem http s názvem `req`:

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

Soubor `__init__.py` obsahuje následující kód funkce:

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

typy atributů a návratový typ lze také explicitně deklarovat pomocí anotací typu Python. To vám pomůže používat funkce IntelliSense a automatického dokončování poskytované mnoha editory kódu Python.

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Použijte poznámky Pythonu, které jsou součástí balíčku [Azure. Functions. *](/python/api/azure-functions/azure.functions?view=azure-python) pro svázání vstupu a výstupů s vašimi metodami.

## <a name="alternate-entry-point"></a>Alternativní vstupní bod

Výchozí chování funkce můžete změnit volitelně zadáním vlastností `scriptFile` a `entryPoint` v souboru *Function. JSON* . Například _funkce Function. JSON_ ukazuje, že modul runtime používá metodu `customentry()` v souboru _Main.py_ jako vstupní bod pro funkci Azure Functions.

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  "bindings": [
      ...
  ]
}
```

## <a name="folder-structure"></a>Struktura složek

Struktura složek pro projekt Python Functions vypadá jako v následujícím příkladu:

```
 FunctionApp
 | - MyFirstFunction
 | | - __init__.py
 | | - function.json
 | | - example.py
 | - MySecondFunction
 | | - __init__.py
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.py
 | | - mySecondHelperFunction.py
 | - host.json
 | - requirements.txt
```

Existuje soubor Shared [Host. JSON](functions-host-json.md) , který se dá použít ke konfiguraci aplikace Function App. Každá funkce má svůj vlastní soubor kódu a konfigurační soubor vazby (Function. JSON). 

Sdílený kód by měl být uložený v samostatné složce. Chcete-li odkazovat na moduly ve složce SharedCode, můžete použít následující syntaxi:

```
from __app__.SharedCode import myFirstHelperFunction
```

Chcete-li odkazovat na moduly místně na funkci, můžete použít syntaxi relativního importu následujícím způsobem:

```
from . import example
```

Když nasadíte projekt funkce do aplikace Function App v Azure, celý obsah složky *FunctionApp* by měl být součástí balíčku, ale ne samotná složka.

## <a name="triggers-and-inputs"></a>Aktivační události a vstupy

Vstupy jsou rozdělené do dvou kategorií v Azure Functions: aktivační událost vstup a další vstup. I když se liší v souboru `function.json`, je použití v kódu Pythonu stejné.  Připojovací řetězce nebo tajné klíče pro zdroje triggeru a vstupu jsou mapovány na hodnoty v souboru `local.settings.json` při spuštění místně a nastavení aplikace při spuštění v Azure. 

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

Když je funkce vyvolána, požadavek HTTP je předán do funkce jako `req`. Položka bude načtena z Blob Storage Azure na základě _ID_ v adrese URL trasy a zpřístupněna jako `obj` v těle funkce.  Tady je uvedený účet úložiště, který je připojovacím řetězcem, který se nachází v, což je stejný účet úložiště, který používá aplikace Function App.


## <a name="outputs"></a>Výstupy

Výstup může být vyjádřen v návratové hodnotě i v parametrech Output. Pokud je k dispozici pouze jeden výstup, doporučujeme použít vrácenou hodnotu. Pro více výstupů budete muset použít výstupní parametry.

Chcete-li použít vrácenou hodnotu funkce jako hodnotu výstupní vazby, vlastnost `name` vazby by měla být nastavena na `$return` v `function.json`.

Chcete-li vytvořit více výstupů, použijte metodu `set()` poskytnutou rozhraním [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out?view=azure-python) k přiřazení hodnoty k vazbě. Například následující funkce může odeslat zprávu do fronty a také vrátit odpověď HTTP.

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

Přístup k protokolovacímu nástroji Azure Functions runtime je k dispozici prostřednictvím kořenové obslužné rutiny [`logging`](https://docs.python.org/3/library/logging.html#module-logging) ve vaší aplikaci Function App. Tento protokolovací nástroj je svázán s Application Insights a umožňuje označit upozornění a chyby, které byly zjištěny během provádění funkce.

Následující příklad zaznamená informační zprávu, když je funkce vyvolána prostřednictvím triggeru protokolu HTTP.

```python
import logging


def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

K dispozici jsou další metody protokolování, které umožňují zapisovat do konzoly na různých úrovních trasování:

| Metoda                 | Description                                |
| ---------------------- | ------------------------------------------ |
| **`critical(_message_)`**   | Zapíše zprávu s KRITICKou úrovní na kořenovém protokolovacím nástroji.  |
| **`error(_message_)`**   | Zapíše zprávu s CHYBou úrovně v kořenovém protokolovacím nástroji.    |
| **`warning(_message_)`**    | Zapíše zprávu s UPOZORNĚNÍm na úrovni kořenového protokolovacího nástroje.  |
| **`info(_message_)`**    | Zapíše zprávu s informacemi o úrovni kořenového protokolovacího nástroje.  |
| **`debug(_message_)`** | Zapíše zprávu s LADĚNÍm úrovně na kořenovém protokolovacím nástroji.  |

Další informace o protokolování najdete v tématu [monitorování Azure Functions](functions-monitoring.md).

## <a name="http-trigger-and-bindings"></a>Aktivační procedura HTTP a vazby

Aktivační událost HTTP je definována v souboru Function. Jan. @No__t-0 vazby musí odpovídat pojmenovanému parametru ve funkci. V předchozích příkladech se používá název vazby `req`. Tento parametr je objekt [HttpRequest] a je vrácen objekt [HttpResponse] .

Z objektu [HttpRequest] můžete získat hlavičky žádosti, parametry dotazu, parametry směrování a tělo zprávy. 

Následující příklad pochází ze [šablony triggeru http pro Python](https://github.com/Azure/azure-functions-templates/tree/dev/Functions.Templates/Templates/HttpTrigger-Python). 

```python
def main(req: func.HttpRequest) -> func.HttpResponse:
    headers = {"my-http-header": "some-value"}

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')
            
    if name:
        return func.HttpResponse(f"Hello {name}!", headers=headers)
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             headers=headers, status_code=400
        )
```

V této funkci se hodnota parametru dotazu `name` získá z parametru `params` objektu [HttpRequest] . Tělo zprávy s kódováním JSON je čteno pomocí metody `get_json`. 

Podobně můžete nastavit `status_code` a `headers` pro zprávu odpovědi v vráceném objektu [HttpResponse] .

## <a name="concurrency"></a>Concurrency

Ve výchozím nastavení funkce modulu runtime jazyka Python mohou současně zpracovat pouze jedno vyvolání funkce. Tato úroveň souběžnosti nemusí být dostatečná pro jednu nebo více následujících podmínek:

+ Snažíte se zpracovat několik vyvolání současně...
+ Zpracováváte velký počet vstupně-výstupních událostí.
+ Vaše aplikace je vázaná na vstupně-výstupní operace.

V těchto situacích můžete zlepšit výkon spuštěním asynchronního a pomocí více pracovních procesů v jazyce.  

### <a name="async"></a>Async

Pro spuštění funkce jako asynchronní korutina doporučujeme použít příkaz `async def`.

```python
# Runs with asyncio directly

async def main():
    await some_nonblocking_socket_io_op()
```

Pokud je funkce `main()` synchronní (bez kvalifikátoru `async`), funkce se automaticky spustí ve fondu vláken `asyncio`.

```python
# Runs in an asyncio thread-pool

def main():
    some_blocking_socket_io()
```

### <a name="use-multiple-language-worker-processes"></a>Použít více pracovních procesů jazyka

Ve výchozím nastavení má každá instance hostitele Functions pracovní proces s jedním jazykem. Nicméně podpora pro více jazykových pracovních procesů na instanci hostitele. Volání funkcí je pak možné rovnoměrně rozdělit mezi tyto jazykové pracovní procesy. Tuto hodnotu můžete změnit pomocí nastavení aplikace [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) . 

## <a name="context"></a>Souvislost

Chcete-li získat kontext vyvolání funkce během provádění, zahrňte do podpisu argument [`context`](/python/api/azure-functions/azure.functions.context?view=azure-python) . 

Například:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

Třída [**Context**](/python/api/azure-functions/azure.functions.context?view=azure-python) má následující atributy řetězce:

`function_directory`  
Adresář, ve kterém je funkce spuštěná.

`function_name`  
Název funkce

`invocation_id`  
ID aktuálního vyvolání funkce

## <a name="global-variables"></a>Globální proměnné

Není zaručeno, že se stav vaší aplikace bude uchovávat pro budoucí provádění. Modul runtime Azure Functions však často opakovaně používá stejný proces pro více spuštění stejné aplikace. Aby bylo možné ukládat výsledky nákladných výpočtů do mezipaměti, deklarujte ji jako globální proměnnou. 

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="environment-variables"></a>Proměnné prostředí

V funkcích jsou [nastavení aplikace](functions-app-settings.md), jako jsou například připojovací řetězce služby, vystavena jako proměnné prostředí během provádění. K těmto nastavením můžete přistupovat pomocí deklarace `import os` a pak pomocí nástroje, `setting = os.environ["setting-name"]`.

Následující příklad získá [nastavení aplikace](functions-how-to-use-azure-function-app-settings.md#settings)s klíčem s názvem `myAppSetting`:

```python
import logging
import os
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    # Get the setting named 'myAppSetting'
    my_app_setting_value = os.environ["myAppSetting"]
    logging.info(f'My app setting value:{my_app_setting_value}')
```

Pro místní vývoj se nastavení aplikace [uchovávají v souboru Local. Settings. JSON](functions-run-local.md#local-settings-file).  

## <a name="python-version-and-package-management"></a>Verze Pythonu a Správa balíčků

V současné době Azure Functions podporuje jenom Python 3.6. x (oficiální distribuce CPython).

Při místním vývoji pomocí Azure Functions Core Tools nebo Visual Studio Code přidejte do souboru `requirements.txt` názvy a verze požadovaných balíčků a nainstalujte je pomocí `pip`.

K instalaci balíčku `requests` z PyPI můžete použít například následující soubor požadavků a příkaz PIP.

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>Publikování do Azure

Až budete připraveni k publikování, ujistěte se, že všechny vaše závislosti jsou uvedeny v souboru *. txt požadavků* , který je umístěn v kořenovém adresáři adresáře projektu. Azure Functions můžou tyto závislosti [vzdáleně sestavit](functions-deployment-technologies.md#remote-build) .

Soubory projektu a složky, které jsou vyloučeny z publikování, včetně složky virtuálního prostředí, jsou uvedeny v souboru. funcignore.  

K nasazení do Azure a provedení vzdáleného sestavení použijte následující příkaz:

```bash
func azure functionapp publish <app name> --build remote
```

Pokud nepoužíváte vzdálené sestavení a použijete balíček, který vyžaduje kompilátor a nepodporuje instalaci řady nekompatibilních kol se systémem Linux z PyPI, publikování do Azure bez automatického sestavení selže s následující chybou:

```
There was an error restoring dependencies.ERROR: cannot install <package name - version> dependency: binary dependencies without wheels are not supported.  
The terminal process terminated with exit code: 1
```

Chcete-li vytvořit místně a nakonfigurovat požadované binární soubory, [nainstalujte Docker](https://docs.docker.com/install/) do místního počítače a spusťte následující příkaz pro publikování pomocí [Azure Functions Core Tools](functions-run-local.md#v2) (Func). Nezapomeňte nahradit `<app name>` názvem vaší aplikace Function App v Azure. 

```bash
func azure functionapp publish <app name> --build-native-deps
```

Pod pokrytí budou základní nástroje používat Docker ke spuštění image [MCR.Microsoft.com/Azure-Functions/Python](https://hub.docker.com/r/microsoft/azure-functions/) jako kontejneru na místním počítači. Pomocí tohoto prostředí pak bude sestavovat a instalovat požadované moduly ze distribuce zdrojového kódu, než je zabalí do Azure pro konečné nasazení.

K sestavování závislostí a publikování pomocí systému pro průběžné doručování (CD) [použijte Azure Pipelines](functions-how-to-azure-devops.md). 

## <a name="unit-testing"></a>Testování částí

Funkce napsané v Pythonu se dají testovat jako jiný kód Pythonu pomocí standardních testovacích architektur. U většiny vazeb je možné vytvořit objektový vstupní objekt vytvořením instance příslušné třídy z balíčku `azure.functions`. Vzhledem k tomu, že balíček [`azure.functions`](https://pypi.org/project/azure-functions/) není hned k dispozici, nezapomeňte jej nainstalovat prostřednictvím souboru `requirements.txt`, jak je popsáno v části [verze Pythonu a Správa balíčků](#python-version-and-package-management) výše.

Následující příklad je vzorovým testem funkce aktivované protokolem HTTP:

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

Tady je další příklad s funkcí aktivovanými ve frontě:

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

Všechny známé problémy a žádosti o funkce jsou sledovány pomocí seznamu [problémů na GitHubu](https://github.com/Azure/azure-functions-python-worker/issues) . Pokud narazíte na problém a nemůžete najít problém v GitHubu, otevřete nový problém a zahrňte podrobný popis problému.

### <a name="cross-origin-resource-sharing"></a>Sdílení prostředků mezi zdroji

Azure Functions podporuje sdílení prostředků mezi zdroji (CORS). CORS se konfiguruje na [portálu](functions-how-to-use-azure-function-app-settings.md#cors) a prostřednictvím rozhraní příkazového [řádku Azure CLI](/cli/azure/functionapp/cors). Seznam povolených zdrojů CORS se vztahuje na úrovni aplikace Function App. Pokud je povolená CORS, odpovědi zahrnují hlavičku `Access-Control-Allow-Origin`. Další informace najdete v tématu [sdílení prostředků mezi zdroji](functions-how-to-use-azure-function-app-settings.md#cors).

Seznam povolených zdrojů [není aktuálně podporován](https://github.com/Azure/azure-functions-python-worker/issues/444) pro aplikace funkcí Pythonu. Z důvodu tohoto omezení musíte výslovně nastavit hlavičku `Access-Control-Allow-Origin` ve funkcích HTTP, jak je znázorněno v následujícím příkladu:

```python
def main(req: func.HttpRequest) -> func.HttpResponse:

    # Define the allow origin headers.
    headers = {"Access-Control-Allow-Origin": "https://contoso.com"}

    # Set the headers in the response.
    return func.HttpResponse(
            f"Allowed origin '{headers}'.",
            headers=headers, status_code=200
    )
``` 

Ujistěte se, že jste taky aktualizovali Function. JSON, aby podporoval metodu HTTP OPTIONS:

```json
    ...
      "methods": [
        "get",
        "post",
        "options"
      ]
    ...
```

Tuto metodu používá prohlížeč Chrome k vyjednání seznamu povolených zdrojů. 

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících materiálech:

* [Dokumentace k rozhraní API balíčku Azure Functions](/python/api/azure-functions/azure.functions?view=azure-python)
* [Osvědčené postupy pro Azure Functions](functions-best-practices.md)
* [Aktivační události a vazby Azure Functions](functions-triggers-bindings.md)
* [Vazby úložiště objektů BLOB](functions-bindings-storage-blob.md)
* [Vazby HTTP a Webhooku](functions-bindings-http-webhook.md)
* [Vazby úložiště front](functions-bindings-storage-queue.md)
* [Aktivační událost časovače](functions-bindings-timer.md)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest?view=azure-python
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse?view=azure-python
