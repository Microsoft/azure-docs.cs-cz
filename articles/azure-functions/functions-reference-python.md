---
title: Referenční dokumentace pro vývojáře v Pythonu pro Azure Functions
description: Vysvětlení, jak vyvíjet funkce pomocí Pythonu
ms.topic: article
ms.date: 12/13/2019
ms.custom: devx-track-python
ms.openlocfilehash: f9b81a7263dc9a1bdae9fd881519ac734da2c6bc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88642193"
---
# <a name="azure-functions-python-developer-guide"></a>Příručka pro vývojáře Azure Functions Pythonu

Tento článek představuje úvod k vývoji Azure Functions s využitím Pythonu. Níže uvedený obsah předpokládá, že už jste si přečetli [příručku pro vývojáře Azure Functions](functions-reference.md).

Ukázkové projekty samostatné funkce v Pythonu najdete v [ukázkách funkcí Pythonu](/samples/browse/?products=azure-functions&languages=python).

## <a name="programming-model"></a>Programovací model

Azure Functions očekává ve skriptu Pythonu funkci, která bude mít nestavovou metodu, která zpracuje vstup a vytvoří výstup. Ve výchozím nastavení očekává modul runtime metodu, která má být implementována jako globální metoda volána `main()` v `__init__.py` souboru. Můžete také [zadat alternativní vstupní bod](#alternate-entry-point).

Data z aktivačních událostí a vazeb jsou svázána s funkcí prostřednictvím atributů metody pomocí `name` vlastnosti definované v *function.jsv* souboru. Například  _function.js_ níže popisuje jednoduchou funkci aktivovanou požadavkem http s názvem `req` :

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

Na základě této definice `__init__.py` může soubor, který obsahuje kód funkce, vypadat jako v následujícím příkladu:

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Typy atributů a návratový typ lze také explicitně deklarovat pomocí anotací typu Python. To vám pomůže používat funkce IntelliSense a automatického dokončování poskytované mnoha editory kódu Python.

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Použijte poznámky Pythonu, které jsou součástí balíčku [Azure. Functions. *](/python/api/azure-functions/azure.functions?view=azure-python) pro svázání vstupu a výstupů s vašimi metodami.

## <a name="alternate-entry-point"></a>Alternativní vstupní bod

Výchozí chování funkce můžete změnit volitelně určením `scriptFile` `entryPoint` vlastností a v *function.jsv* souboru. Například _function.jsv_ níže říká modulu runtime, aby používal `customentry()` metodu v souboru _Main.py_ jako vstupní bod pro funkci Azure Functions.

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

Doporučená struktura složek pro projekt funkcí Pythonu vypadá jako v následujícím příkladu:

```
 __app__
 | - my_first_function
 | | - __init__.py
 | | - function.json
 | | - example.py
 | - my_second_function
 | | - __init__.py
 | | - function.json
 | - shared_code
 | | - my_first_helper_function.py
 | | - my_second_helper_function.py
 | - host.json
 | - requirements.txt
 | - Dockerfile
 tests
```
Hlavní složka projektu ( \_ \_ aplikace \_ \_ ) může obsahovat následující soubory:

* *local.settings.jsv*: používá se k ukládání nastavení aplikace a připojovacích řetězců při místním spuštění. Tento soubor se nepublikuje do Azure. Další informace najdete v tématu [Local. Settings. File](functions-run-local.md#local-settings-file).
* *requirements.txt*: obsahuje seznam balíčků, které systém nainstaluje při publikování do Azure.
* *host.js*: obsahuje možnosti globální konfigurace, které ovlivňují všechny funkce aplikace Function App. Tento soubor se publikuje do Azure. Ne všechny možnosti jsou podporovány při místním spuštění. Další informace najdete v tématu [host.jsv](functions-host-json.md).
* *. funcignore*: (volitelné) deklaruje soubory, které by neměly být publikovány do Azure.
* *Souboru Dockerfile*: (volitelné) používá se při publikování projektu ve [vlastním kontejneru](functions-create-function-linux-custom-image.md).

Každá funkce má svůj vlastní soubor kódu a konfigurační soubor vazby (function.json).

Když nasadíte projekt do aplikace Function App v Azure, celý obsah hlavní složky projektu (* \_ \_ App \_ \_ *) by měl být součástí balíčku, ale ne samotné složky. V tomto příkladu doporučujeme udržovat testy ve složce oddělené od složky projektu `tests` . Tím zajistíte, že budete nasazovat testovací kód s vaší aplikací. Další informace najdete v tématu [testování částí](#unit-testing).

## <a name="import-behavior"></a>Chování při importu

Moduly v kódu funkce můžete importovat pomocí explicitních relativních i absolutních odkazů. V závislosti na struktuře složky uvedené výše následující importy fungují v rámci aplikace Function App. * \_ \_ \_ \_ \_ první \_ funkce \\ _ \_ init \_ \_ . py*:

```python
from . import example #(explicit relative)
```

```python
from ..shared_code import my_first_helper_function #(explicit relative)
```

```python
from __app__ import shared_code #(absolute)
```

```python
import __app__.shared_code #(absolute)
```

Následující importy *nefungují* v rámci stejného souboru:

```python
import example
```

```python
from example import some_helper_code
```

```python
import shared_code
```

Sdílený kód by měl být uložený v samostatné složce * \_ \_ aplikace \_ \_ *. Chcete-li odkazovat na moduly ve složce *sdíleného \_ kódu* , můžete použít následující syntaxi:

```python
from __app__.shared_code import my_first_helper_function
```

## <a name="triggers-and-inputs"></a>Aktivační události a vstupy

Vstupy jsou rozdělené do dvou kategorií v Azure Functions: aktivační událost vstup a další vstup. I když se v souboru liší `function.json` , je použití v kódu Pythonu stejné.  Připojovací řetězce nebo tajné klíče pro zdroje triggeru a vstupu jsou mapovány na hodnoty v `local.settings.json` souboru při spuštění místně a nastavení aplikace při spuštění v Azure.

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

Při vyvolání funkce je požadavek HTTP předán funkci jako `req` . Položka bude načtena z Blob Storage Azure na základě _ID_ v adrese URL trasy a zpřístupněna jako `obj` v těle funkce.  Tady je zadaný účet úložiště připojovací řetězec, který se nachází v nastavení aplikace AzureWebJobsStorage, což je stejný účet úložiště, který používá aplikace Function App.


## <a name="outputs"></a>Výstupy

Výstup může být vyjádřen v návratové hodnotě i v parametrech Output. Pokud je k dispozici pouze jeden výstup, doporučujeme použít vrácenou hodnotu. Pro více výstupů budete muset použít výstupní parametry.

Chcete-li použít vrácenou hodnotu funkce jako hodnotu výstupní vazby, `name` vlastnost vazby by měla být nastavena na hodnotu `$return` v `function.json` .

Chcete-li vytvořit více výstupů, použijte `set()` metodu poskytnutou [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out?view=azure-python) rozhraním pro přiřazení hodnoty k vazbě. Například následující funkce může odeslat zprávu do fronty a také vrátit odpověď HTTP.

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

## <a name="logging"></a>protokolování

Přístup k protokolovacímu nástroji Azure Functions runtime je k dispozici prostřednictvím kořenové [`logging`](https://docs.python.org/3/library/logging.html#module-logging) obslužné rutiny ve vaší aplikaci Function App. Tento protokolovací nástroj je svázán s Application Insights a umožňuje označit upozornění a chyby, které byly zjištěny během provádění funkce.

Následující příklad zaznamená informační zprávu, když je funkce vyvolána prostřednictvím triggeru protokolu HTTP.

```python
import logging


def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

K dispozici jsou další metody protokolování, které umožňují zapisovat do konzoly na různých úrovních trasování:

| Metoda                 | Popis                                |
| ---------------------- | ------------------------------------------ |
| **`critical(_message_)`**   | Zapíše zprávu s KRITICKou úrovní na kořenovém protokolovacím nástroji.  |
| **`error(_message_)`**   | Zapíše zprávu s CHYBou úrovně v kořenovém protokolovacím nástroji.    |
| **`warning(_message_)`**    | Zapíše zprávu s UPOZORNĚNÍm na úrovni kořenového protokolovacího nástroje.  |
| **`info(_message_)`**    | Zapíše zprávu s informacemi o úrovni kořenového protokolovacího nástroje.  |
| **`debug(_message_)`** | Zapíše zprávu s LADĚNÍm úrovně na kořenovém protokolovacím nástroji.  |

Další informace o protokolování najdete v tématu [monitorování Azure Functions](functions-monitoring.md).

## <a name="http-trigger-and-bindings"></a>Aktivační procedura HTTP a vazby

Aktivační událost HTTP je definována v function.jsv souboru. `name`Vazba musí odpovídat pojmenovanému parametru ve funkci.
V předchozích příkladech se používá název vazby `req` . Tento parametr je objekt [HttpRequest] a je vrácen objekt [HttpResponse] .

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

V této funkci se hodnota `name` parametru dotazu získá z `params` parametru objektu [HttpRequest] . Tělo zprávy s kódováním JSON je čteno pomocí `get_json` metody.

Podobně můžete nastavit `status_code` a `headers` pro zprávu odpovědi v vráceném objektu [HttpResponse] .

## <a name="scaling-and-concurrency"></a>Škálování a souběžnost

Ve výchozím nastavení Azure Functions automaticky monitoruje zatížení aplikace a v případě potřeby vytvoří další instance hostitele pro Python. Funkce používá předdefinované (neuživatelsky konfigurovatelné) prahové hodnoty pro různé typy triggerů k rozhodnutí, kdy přidat instance, například stáří zpráv a velikost fronty pro QueueTrigger. Další informace najdete v tématu [Jak fungují plány spotřeby a Premium](functions-scale.md#how-the-consumption-and-premium-plans-work).

Toto chování škálování je dostatečné pro mnoho aplikací. Aplikace s kteroukoli z následujících vlastností se ale nemusí škálovat efektivně:

- Aplikace potřebuje zpracovat mnoho souběžných volání.
- Aplikace zpracovává velký počet vstupně-výstupních událostí.
- Aplikace je vázaná na vstupně-výstupní operace.

V takových případech můžete zvýšit výkon tím, že budete využívat asynchronní vzorce a pomocí více pracovních procesů jazyka.

### <a name="async"></a>Async

Vzhledem k tomu, že Python je modul runtime s jedním vláknem, může instance hostitele pro Python zpracovat pouze jedno vyvolání funkce najednou. Pro aplikace, které zpracovávají velký počet vstupně-výstupních událostí a/nebo jsou vázané na vstup/výstup, můžete zvýšit výkon spuštěním asynchronních funkcí.

Chcete-li spustit funkci asynchronně, použijte `async def` příkaz, který spustí funkci s [asyncio](https://docs.python.org/3/library/asyncio.html) přímo:

```python
async def main():
    await some_nonblocking_socket_io_op()
```

Funkce bez `async` klíčového slova se spustí automaticky ve fondu vláken asyncio:

```python
# Runs in an asyncio thread-pool

def main():
    some_blocking_socket_io()
```

### <a name="use-multiple-language-worker-processes"></a>Použít více pracovních procesů jazyka

Ve výchozím nastavení má každá instance hostitele Functions pracovní proces s jedním jazykem. Počet pracovních procesů na hostitele můžete zvýšit (až 10) pomocí nastavení aplikace [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) . Azure Functions se pak pokusí rovnoměrně distribuovat souběžná volání funkcí mezi tyto pracovní procesy.

FUNCTIONS_WORKER_PROCESS_COUNT se vztahuje na každého hostitele, který funkce vytvoří při horizontálním navýšení kapacity aplikace, aby splňovala požadavky.

## <a name="context"></a>Kontext

Chcete-li získat kontext vyvolání funkce během provádění, zahrňte [`context`](/python/api/azure-functions/azure.functions.context?view=azure-python) do jejího podpisu argument.

Například:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

Třída [**Context**](/python/api/azure-functions/azure.functions.context?view=azure-python) má následující atributy řetězce:

`function_directory` Adresář, ve kterém je funkce spuštěná.

`function_name` Název funkce

`invocation_id` ID aktuálního vyvolání funkce

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

V funkcích jsou [nastavení aplikace](functions-app-settings.md), jako jsou například připojovací řetězce služby, vystavena jako proměnné prostředí během provádění. K těmto nastavením můžete přistupovat deklarováním `import os` a následným použitím, `setting = os.environ["setting-name"]` .

Následující příklad získá [nastavení aplikace](functions-how-to-use-azure-function-app-settings.md#settings)s klíčem s názvem `myAppSetting` :

```python
import logging
import os
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    # Get the setting named 'myAppSetting'
    my_app_setting_value = os.environ["myAppSetting"]
    logging.info(f'My app setting value:{my_app_setting_value}')
```

Pro místní vývoj se nastavení aplikace [uchovávají v local.settings.jssouboru](functions-run-local.md#local-settings-file).

## <a name="python-version"></a>Verze Pythonu

Azure Functions podporuje následující verze Pythonu:

| Verze funkcí | Verze Pythonu <sup>*</sup> |
| ----- | ----- |
| 3.x | 3,8<br/>3.7<br/>3,6 |
| 2.x | 3.7<br/>3,6 |

<sup>*</sup>Oficiální CPython distribuce

Pokud chcete požádat o konkrétní verzi Pythonu při vytváření aplikace Function App v Azure, použijte `--runtime-version` možnost [`az functionapp create`](/cli/azure/functionapp#az-functionapp-create) příkazu. Verze modulu runtime Functions je nastavená `--functions-version` možností. Verze Pythonu se nastaví, když se aplikace Function App vytvoří a nedá se změnit.

Při místním spuštění používá modul runtime dostupnou verzi Pythonu.

## <a name="package-management"></a>Správa balíčků

Při místním vývoji pomocí Azure Functions Core Tools nebo Visual Studio Code přidejte do souboru názvy a verze požadovaných balíčků `requirements.txt` a nainstalujte je pomocí `pip` .

K instalaci balíčku z PyPI můžete použít například následující soubor požadavků a příkaz PIP `requests` .

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>Publikování do Azure

Až budete připraveni k publikování, ujistěte se, že všechny veřejně dostupné závislosti jsou uvedeny v souboru requirements.txt, který je umístěn v kořenovém adresáři adresáře projektu.

Soubory projektu a složky, které jsou vyloučeny z publikování, včetně složky virtuálního prostředí, jsou uvedeny v souboru. funcignore.

Existují tři akce sestavení, které jsou podporovány pro publikování projektu v jazyce Python do Azure: vzdálené sestavení, místní sestavení a sestavení pomocí vlastních závislostí.

Azure Pipelines můžete použít také k sestavení závislostí a publikování pomocí průběžného doručování (CD). Další informace najdete v tématu [průběžné doručování pomocí Azure DevOps](functions-how-to-azure-devops.md).

### <a name="remote-build"></a>Vzdálené sestavení

Při použití vzdáleného sestavení se závislosti obnovené na serveru a nativní závislosti shodují s produkčním prostředím. Výsledkem je menší balíček pro nasazení, který se má nahrát. Používejte vzdálené sestavení při vývoji aplikací v jazyce Python ve Windows. Pokud má váš projekt vlastní závislosti, můžete [použít vzdálené sestavení s dodatečnou adresou URL indexu](#remote-build-with-extra-index-url).

Závislosti se získávají vzdáleně na základě obsahu souboru requirements.txt. Doporučenou metodou sestavení je [vzdálené sestavení](functions-deployment-technologies.md#remote-build) . Ve výchozím nastavení Azure Functions Core Tools požádá o vzdálené sestavení, když použijete následující příkaz [Func Azure functionapp Publish](functions-run-local.md#publish) pro publikování projektu v Pythonu do Azure.

```bash
func azure functionapp publish <APP_NAME>
```

Nezapomeňte nahradit `<APP_NAME>` názvem vaší aplikace Function App v Azure.

[Rozšíření Azure Functions pro Visual Studio Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure) také požádá o vzdálené sestavení ve výchozím nastavení.

### <a name="local-build"></a>Místní sestavení

Závislosti se získávají místně na základě obsahu souboru requirements.txt. Můžete zabránit provedení vzdáleného sestavení pomocí následujícího příkazu [Func Azure functionapp Publish](functions-run-local.md#publish) pro publikování s místním sestavením.

```command
func azure functionapp publish <APP_NAME> --build local
```

Nezapomeňte nahradit `<APP_NAME>` názvem vaší aplikace Function App v Azure.

Pomocí `--build local` Možnosti se závislosti projektu čtou ze souboru requirements.txt a tyto závislé balíčky se stahují a instalují místně. Soubory projektu a závislosti se nasazují z místního počítače do Azure. Výsledkem je větší balíček pro nasazení, který se nahrává do Azure. Pokud z nějakého důvodu nepůjde závislosti v souboru requirements.txt získat základními nástroji, musíte pro publikování použít možnost vlastní závislosti.

Při vývoji místně ve Windows nedoporučujeme používat místní buildy.

### <a name="custom-dependencies"></a>Vlastní závislosti

Pokud váš projekt obsahuje závislosti nenalezené v [indexu balíčku Pythonu](https://pypi.org/), existují dva způsoby sestavení projektu. Metoda sestavení závisí na tom, jak sestavíte projekt.

#### <a name="remote-build-with-extra-index-url"></a>Vzdálené sestavení s dodatečnou adresou URL indexu

Pokud jsou balíčky dostupné z dostupného vlastního indexu balíčku, použijte vzdálené sestavení. Před publikováním se ujistěte, že jste [vytvořili nastavení aplikace](functions-how-to-use-azure-function-app-settings.md#settings) s názvem `PIP_EXTRA_INDEX_URL` . Hodnota tohoto nastavení je adresa URL vašeho vlastního indexu balíčku. Pomocí tohoto nastavení se vzdálené sestavení spustí `pip install` pomocí `--extra-index-url` Možnosti. Další informace najdete v [dokumentaci k instalaci Python PIP](https://pip.pypa.io/en/stable/reference/pip_install/#requirements-file-format).

Můžete také použít přihlašovací údaje základního ověřování s dalšími adresami URL indexu balíčku. Další informace najdete v tématu [základní přihlašovací údaje pro ověřování](https://pip.pypa.io/en/stable/user_guide/#basic-authentication-credentials) v dokumentaci Pythonu.

#### <a name="install-local-packages"></a>Nainstalovat místní balíčky

Pokud váš projekt používá balíčky, které nejsou veřejně dostupné pro naše nástroje, můžete je zpřístupnit pro vaši aplikaci jejich vložením do \_ \_ adresáře app \_ \_ /.python_packages. Před publikováním spusťte následující příkaz pro místní instalaci závislostí:

```command
pip install  --target="<PROJECT_DIR>/.python_packages/lib/site-packages"  -r requirements.txt
```

Pokud používáte vlastní závislosti, měli byste použít `--no-build` možnost publikování, protože už jste nainstalovali závislosti do složky projektu.

```command
func azure functionapp publish <APP_NAME> --no-build
```

Nezapomeňte nahradit `<APP_NAME>` názvem vaší aplikace Function App v Azure.

## <a name="unit-testing"></a>Testování částí

Funkce napsané v Pythonu se dají testovat jako jiný kód Pythonu pomocí standardních testovacích architektur. U většiny vazeb je možné vytvořit objektový vstupní objekt vytvořením instance příslušné třídy z `azure.functions` balíčku. Vzhledem k [`azure.functions`](https://pypi.org/project/azure-functions/) tomu, že balíček není hned dostupný, nezapomeňte ho nainstalovat pomocí `requirements.txt` souboru, jak je popsáno výše v části [Správa balíčků](#package-management) .

Následující příklad je vzorovým testem funkce aktivované protokolem HTTP:

```json
{
  "scriptFile": "__init__.py",
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
# __app__/HttpTrigger/__init__.py
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
# tests/test_httptrigger.py
import unittest

import azure.functions as func
from __app__.HttpTrigger import my_function

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

```json
{
  "scriptFile": "__init__.py",
  "entryPoint": "my_function",
  "bindings": [
    {
      "name": "msg",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "python-queue-items",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

```python
# __app__/QueueTrigger/__init__.py
import azure.functions as func

def my_function(msg: func.QueueMessage) -> str:
    return f'msg body: {msg.get_body().decode()}'
```

```python
# tests/test_queuetrigger.py
import unittest

import azure.functions as func
from __app__.QueueTrigger import my_function

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
## <a name="temporary-files"></a>Dočasné soubory

`tempfile.gettempdir()`Metoda vrátí dočasnou složku, která je na systému Linux `/tmp` . Aplikace může pomocí tohoto adresáře ukládat dočasné soubory vygenerované a používané funkcemi během provádění.

> [!IMPORTANT]
> U souborů zapsaných do dočasného adresáře není zaručeno zachování trvalých volání. Během horizontálního navýšení kapacity se nesdílí dočasné soubory mezi instancemi.

Následující příklad vytvoří pojmenovaný dočasný soubor v dočasném adresáři ( `/tmp` ):

```python
import logging
import azure.functions as func
import tempfile
from os import listdir

#---
   tempFilePath = tempfile.gettempdir()
   fp = tempfile.NamedTemporaryFile()
   fp.write(b'Hello world!')
   filesDirListInTemp = listdir(tempFilePath)
```

Doporučujeme udržovat testy ve složce oddělené od složky projektu. Tím zajistíte, že budete nasazovat testovací kód s vaší aplikací.

## <a name="preinstalled-libraries"></a>Předinstalované knihovny

Modul runtime funkcí Pythonu přináší několik knihoven.

### <a name="python-standard-library"></a>Standardní knihovna Pythonu

Standardní knihovna Python obsahuje seznam integrovaných modulů Pythonu, které jsou dodávány s každou distribucí v Pythonu. Většina těchto knihoven vám pomůže při přístupu k systémovým funkcím, jako jsou například vstupně-výstupní operace se soubory. V systémech Windows se tyto knihovny instalují pomocí Pythonu. V systémech UNIX jsou k dispozici kolekce balíčků.

Chcete-li zobrazit úplné podrobnosti o seznamu těchto knihoven, navštivte odkazy níže:

* [Standardní knihovna Python 3,6](https://docs.python.org/3.6/library/)
* [Standardní knihovna Python 3,7](https://docs.python.org/3.7/library/)
* [Standardní knihovna Python 3,8](https://docs.python.org/3.8/library/)

### <a name="azure-functions-python-worker-dependencies"></a>Azure Functions závislosti pracovního procesu Pythonu

Funkce Python Worker vyžaduje konkrétní sadu knihoven. Tyto knihovny můžete použít také ve svých funkcích, ale nejsou součástí standardu Pythonu. Pokud vaše funkce spoléhají na některé z těchto knihoven, nemusí být k dispozici pro váš kód při spuštění mimo Azure Functions. Podrobný seznam závislostí najdete v souboru [Setup.py](https://github.com/Azure/azure-functions-python-worker/blob/dev/setup.py#L282) v části **instalace \_ vyžaduje** .

> [!NOTE]
> Pokud vaše aplikace Function App requirements.txt obsahuje `azure-functions-worker` položku, odeberte ji. Pracovní proces Functions je automaticky spravovaný Azure Functions platformou a pravidelně ho aktualizuje novými funkcemi a opravami chyb. Ruční instalace staré verze pracovního procesu v requirements.txt může způsobit neočekávané problémy.

### <a name="azure-functions-python-library"></a>Azure Functions knihovna Pythonu

Každá aktualizace pro Python Worker zahrnuje novou verzi [Azure Functions knihovny Python (Azure. Functions)](https://github.com/Azure/azure-functions-python-library). Tento přístup usnadňuje průběžnou aktualizaci aplikací funkcí Pythonu, protože každá aktualizace je zpětně kompatibilní. Seznam verzí této knihovny najdete v části [Azure-Functions PyPI](https://pypi.org/project/azure-functions/#history).

Verze běhové knihovny je opravena v Azure a nemůže být přepsána requirements.txt. `azure-functions`Položka v requirements.txt je určena pouze pro linting a povědomí o zákaznících.

Použijte následující kód ke sledování skutečné verze knihovny funkcí Pythonu v modulu runtime:

```python
getattr(azure.functions, '__version__', '< 1.2.1')
```

### <a name="runtime-system-libraries"></a>Běhové systémové knihovny

Pokud chcete zobrazit seznam předinstalovaných systémových knihoven v Python Worker image Docker, postupujte prosím podle následujících odkazů:

|  Modul runtime Functions  | Verze Debian | Verze Pythonu |
|------------|------------|------------|
| Verze 2. x | Roztažení  | [Python 3,6](https://github.com/Azure/azure-functions-docker/blob/master/host/2.0/stretch/amd64/python/python36/python36.Dockerfile)<br/>[Python 3.7](https://github.com/Azure/azure-functions-docker/blob/master/host/2.0/stretch/amd64/python/python37/python37.Dockerfile) |
| Verze 3. x | Buster | [Python 3,6](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python36/python36.Dockerfile)<br/>[Python 3.7](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python37/python37.Dockerfile)<br />[Python 3,8](https://github.com/Azure/azure-functions-docker/blob/master/host/3.0/buster/amd64/python/python38/python38.Dockerfile) |

## <a name="cross-origin-resource-sharing"></a>Sdílení prostředků různého původu

[!INCLUDE [functions-cors](../../includes/functions-cors.md)]

CORS je plně podporovaná pro aplikace funkcí Pythonu.

## <a name="known-issues-and-faq"></a>Známé problémy a nejčastější dotazy

Následuje seznam průvodců odstraňováním potíží pro běžné problémy:

* [ModuleNotFoundError a chyba při importu](recover-python-functions.md#troubleshoot-modulenotfounderror)
* [Nejde importovat ' cygrpc '.](recover-python-functions.md#troubleshoot-cannot-import-cygrpc)

Všechny známé problémy a žádosti o funkce jsou sledovány pomocí seznamu [problémů na GitHubu](https://github.com/Azure/azure-functions-python-worker/issues) . Pokud narazíte na problém a nemůžete najít problém v GitHubu, otevřete nový problém a zahrňte podrobný popis problému.

## <a name="next-steps"></a>Další kroky

Další informace naleznete v následujících zdrojích:

* [Dokumentace k rozhraní API balíčku Azure Functions](/python/api/azure-functions/azure.functions?view=azure-python)
* [Osvědčené postupy pro službu Azure Functions](functions-best-practices.md)
* [Aktivační události a vazby Azure Functions](functions-triggers-bindings.md)
* [Vazby úložiště objektů BLOB](functions-bindings-storage-blob.md)
* [Vazby HTTP a Webhooku](functions-bindings-http-webhook.md)
* [Vazby úložiště front](functions-bindings-storage-queue.md)
* [Trigger časovače](functions-bindings-timer.md)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest?view=azure-python
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse?view=azure-python
