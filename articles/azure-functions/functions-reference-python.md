---
title: Odkaz na vývojáře Pythonu pro funkce Azure
description: Pochopit, jak vyvíjet funkce s Pythonem
ms.topic: article
ms.date: 12/13/2019
ms.openlocfilehash: 30f40db33b6aa8b40202c023f301265565257180
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276683"
---
# <a name="azure-functions-python-developer-guide"></a>Průvodce vývojářem Azure Functions Pythonu

Tento článek je úvod do vývoje funkcí Azure pomocí Pythonu. Níže uvedený obsah předpokládá, že jste si už [přečetli průvodce vývojáři Azure Functions](functions-reference.md). 

Ukázkové projekty samostatné funkce v Pythonu najdete v [ukázkách funkcí Pythonu](/samples/browse/?products=azure-functions&languages=python). 

## <a name="programming-model"></a>Programovací model

Azure Functions očekává, že funkce bude bezstavová metoda ve skriptu Pythonu, který zpracovává vstup a vytváří výstup. Ve výchozím nastavení runtime očekává, že metoda bude `main()` implementována jako globální metoda volaná v souboru. `__init__.py` Můžete také [určit alternativní vstupní bod](#alternate-entry-point).

Data z aktivačních událostí a vazeb jsou vázána `name` na funkci pomocí atributů metody pomocí vlastnosti definované v souboru *function.json.* Například níže uvedený _soubor function.json_ popisuje jednoduchou funkci `req`spuštěnou požadavkem HTTP s názvem :

:::code language="son" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

Na základě této `__init__.py` definice může soubor obsahující kód funkce vypadat jako následující příklad:

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Můžete také explicitně deklarovat typy atributů a návratový typ ve funkci pomocí anotací typu Pythonu. To vám pomůže používat funkce intellisense a automatického dokončování poskytované mnoha editory kódu Pythonu.

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Pomocí anotací pythonu zahrnutých v balíčku [azure.functions.*](/python/api/azure-functions/azure.functions?view=azure-python) můžete svázat vstupy a výstupy s vašimi metodami.

## <a name="alternate-entry-point"></a>Alternativní vstupní bod

Výchozí chování funkce můžete změnit volitelně zadáním `scriptFile` `entryPoint` vlastností a v souboru *function.json.* Například _function.json_ níže říká, že runtime použít metodu `customentry()` v _souboru main.py,_ jako vstupní bod pro funkci Azure.

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

Doporučená struktura složek pro projekt Funkce Pythonu vypadá jako následující příklad:

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
 tests
```
Hlavní složka projektu\_\_\_\_( aplikace ) může obsahovat následující soubory:

* *local.settings.json*: Používá se k ukládání nastavení aplikací a připojovacích řetězců při místním spuštění. Tento soubor se do Azure nepublikuje. Další informace naleznete v [tématu local.settings.file](functions-run-local.md#local-settings-file).
* *requirements.txt*: Obsahuje seznam balíčků, které systém nainstaluje při publikování do Azure.
* *host.json*: Obsahuje možnosti globální konfigurace, které ovlivňují všechny funkce v aplikaci funkce. Tento soubor se publikuje do Azure. Ne všechny možnosti jsou podporovány při spuštění místně. Další informace naleznete v [tématu host.json](functions-host-json.md).
* *.funcignore*: (Volitelné) deklaruje soubory, které by neměly být publikovány do Azure.
* *.gitignore*: (Volitelné) deklaruje soubory, které jsou vyloučeny z úložiště git, například local.settings.json.

Každá funkce má svůj vlastní soubor kódu a konfigurační soubor vazby (function.json). 

Při nasazování projektu do aplikace funkce v Azure by měl být do balíčku zahrnut celý obsah složky hlavního projektu (*\_\_aplikace),\_* ale ne samotná složka. V tomto příkladu `tests`doporučujeme udržovat testy ve složce oddělené od složky projektu . To vám zabrání nasazení testovacího kódu s vaší aplikací. Další informace naleznete [v tématu Testování částí](#unit-testing).

## <a name="import-behavior"></a>Chování importu

Moduly v kódu funkce můžete importovat pomocí explicitních relativních i absolutních odkazů. Na základě výše uvedené struktury složek fungují následující importy z * \_ \_aplikace\_\_\_souboru\_\_funkcí \my\_first\_function\\_ init .py*:

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

Následující importy *nefungují* ze stejného souboru:

```python
import example
```

```python
from example import some_helper_code
```

```python
import shared_code
```

Sdílený kód by měl být uložen v samostatné složce v * \_ \_aplikaci\_*. Chcete-li odkazovat na moduly ve sdílené složce *\_kódu,* můžete použít následující syntaxi:

```python
from __app__.shared_code import my_first_helper_function
```

## <a name="triggers-and-inputs"></a>Aktivační události a vstupy

Vstupy jsou rozděleny do dvou kategorií v Azure Functions: aktivační vstup a další vstup. I když se `function.json` v souboru liší, použití je v kódu Pythonu stejné.  Připojovací řetězce nebo tajné klíče pro `local.settings.json` spouštěcí a vstupní zdroje mapovat na hodnoty v souboru při spuštění místně a nastavení aplikace při spuštění v Azure. 

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

Pokud je funkce vyvolána, je požadavek HTTP `req`předán funkci jako . Položka se načte z úložiště objektů blob Azure na základě _ID_ `obj` v adrese URL trasy a bude k dispozici jako v těle funkce.  Zde je zadaný účet úložiště připojovací řetězec nalezený v nastavení aplikace AzureWebJobsStorage, což je stejný účet úložiště, který používá aplikace funkce.


## <a name="outputs"></a>Výstupy

Výstup může být vyjádřen jak v návratové hodnotě, tak v výstupních parametrech. Pokud existuje pouze jeden výstup, doporučujeme použít vrácenou hodnotu. Pro více výstupů budete muset použít výstupní parametry.

Chcete-li použít vrácenou hodnotu funkce jako hodnotu výstupní vazby, `name` `$return` vlastnost `function.json`vazby by měla být nastavena na v .

Chcete-li vytvořit více `set()` výstupů, použijte [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out?view=azure-python) metodu poskytnutou rozhraním k přiřazení hodnoty vazbě. Například následující funkce může push zprávu do fronty a také vrátit odpověď HTTP.

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

Přístup k runtime loggeru Azure Functions [`logging`](https://docs.python.org/3/library/logging.html#module-logging) je k dispozici prostřednictvím root handler ve vaší aplikaci funkce. Tento protokolování je vázána na Application Insights a umožňuje příznak upozornění a chyby došlo během provádění funkce.

Následující příklad zaznamená informační zprávu, když je funkce vyvolána pomocí aktivační události HTTP.

```python
import logging


def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

K dispozici jsou další metody protokolování, které umožňují zapisovat do konzoly na různých úrovních trasování:

| Metoda                 | Popis                                |
| ---------------------- | ------------------------------------------ |
| **`critical(_message_)`**   | Zapíše zprávu s úrovní CRITICAL na kořenovém záznamníku.  |
| **`error(_message_)`**   | Zapíše zprávu s chybou úrovně na kořenovém záznamníku.    |
| **`warning(_message_)`**    | Zapíše zprávu s úrovní UPOZORNĚNÍ na kořenovém záznamníku.  |
| **`info(_message_)`**    | Zapíše zprávu s úrovní INFO na kořenovém záznamníku.  |
| **`debug(_message_)`** | Zapíše zprávu s úrovní LADĚNÍ na kořenovém záznamníku.  |

Další informace o protokolování najdete [v tématu Sledování funkcí Azure](functions-monitoring.md).

## <a name="http-trigger-and-bindings"></a>Aktivační událost protokolu HTTP a vazby

Aktivační událost HTTP je definována v souboru function.jon. Vazba `name` musí odpovídat pojmenované parametr ve funkci. V předchozích příkladech se `req` používá název vazby. Tento parametr je [objekt HttpRequest] a je vrácen objekt [HttpResponse.]

Z objektu [HttpRequest] můžete získat záhlaví požadavku, parametry dotazu, parametry trasy a text zprávy. 

Následující příklad je ze [šablony aktivační události HTTP pro Python](https://github.com/Azure/azure-functions-templates/tree/dev/Functions.Templates/Templates/HttpTrigger-Python). 

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

V této funkci je `name` hodnota parametru dotazu získána z parametru `params` objektu [HttpRequest.] Tělo zprávy kódované JSON je čteno pomocí `get_json` metody. 

Podobně můžete nastavit `status_code` a `headers` pro odpověď zprávy ve vrácenéhttpResponse objektu. [HttpResponse]

## <a name="scaling-and-concurrency"></a>Změna velikosti a souběžnost

Ve výchozím nastavení Funkce Azure automaticky monitoruje zatížení vaší aplikace a podle potřeby vytvoří další instance hostitele pro Python. Funkce používá vestavěné (nikoli uživatelsky konfigurovatelné) prahové hodnoty pro různé typy aktivačních událostí k rozhodnutí, kdy přidat instance, jako je stáří zpráv a velikost fronty pro QueueTrigger. Další informace najdete [v tématu Jak fungují plány Spotřeba a Premium](functions-scale.md#how-the-consumption-and-premium-plans-work).

Toto chování škálování je dostatečná pro mnoho aplikací. Aplikace s některou z následujících vlastností však nesmí být tak účinné:

- Aplikace musí zpracovat mnoho souběžných vyvolání.
- Aplikace zpracovává velký počet vstupně-va událostí.
- Aplikace je vázána na vstupně-výstupní.

V takových případech můžete dále zlepšit výkon použitím asynchronních vzorů a pomocí více jazykových pracovních procesů.

### <a name="async"></a>Async

Vzhledem k tomu, že Python je runtime s jedním podprocesem, instance hostitele pro Python může zpracovat pouze jednu vyvolání funkce najednou. U aplikací, které zpracovávají velký počet vstupně-výstupních událostí a/nebo jsou vstupně-výstupní vstupně-výstupní vázány, můžete zvýšit výkon spuštěním funkcí asynchronně.

Chcete-li spustit funkci asynchronně, `async def` použijte příkaz, který spustí funkci s [asyncio](https://docs.python.org/3/library/asyncio.html) přímo:

```python
async def main():
    await some_nonblocking_socket_io_op()
```

Funkce bez `async` klíčového slova je spuštěna automaticky v fondu vláken asyncio:

```python
# Runs in an asyncio thread-pool

def main():
    some_blocking_socket_io()
```

### <a name="use-multiple-language-worker-processes"></a>Použití více jazykových pracovních procesů

Ve výchozím nastavení má každá instance hostitele Functions pracovní proces v jednom jazyce. Pomocí nastavení [aplikace FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) můžete zvýšit počet pracovních procesů na hostitele (až 10). Azure Functions se pak pokusí rovnoměrně distribuovat souběžné vyvolání funkcí napříč těmito pracovníky. 

FUNCTIONS_WORKER_PROCESS_COUNT platí pro každého hostitele, který funkce vytvoří při škálování z vaší aplikace tak, aby splňovaly požadavky. 

## <a name="context"></a>Kontext

Chcete-li získat kontext vyvolání funkce během [`context`](/python/api/azure-functions/azure.functions.context?view=azure-python) provádění, zahrňte argument do jeho podpisu. 

Například:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

Třída [**Context**](/python/api/azure-functions/azure.functions.context?view=azure-python) má následující atributy řetězce:

`function_directory`  
Adresář, ve kterém je spuštěna funkce.

`function_name`  
Název funkce.

`invocation_id`  
ID vyvolání aktuální funkce.

## <a name="global-variables"></a>Globální proměnné

Není zaručeno, že stav vaší aplikace bude zachován pro budoucí spuštění. Však Azure Functions runtime často používá stejný proces pro více spuštění stejné aplikace. Chcete-li uložit výsledky nákladného výpočtu do mezipaměti, deklarujte jej jako globální proměnnou. 

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="environment-variables"></a>Proměnné prostředí

V funkcích jsou [nastavení aplikace](functions-app-settings.md), například připojovací řetězce služby, během provádění vystavena jako proměnné prostředí. K těmto nastavením můžete `import os` přistupovat `setting = os.environ["setting-name"]`deklarováním a následným použitím aplikace .

Následující příklad získá [nastavení aplikace](functions-how-to-use-azure-function-app-settings.md#settings)s `myAppSetting`názvem klíče :

```python
import logging
import os
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    # Get the setting named 'myAppSetting'
    my_app_setting_value = os.environ["myAppSetting"]
    logging.info(f'My app setting value:{my_app_setting_value}')
```

Pro místní vývoj jsou nastavení aplikace [udržována v souboru local.settings.json](functions-run-local.md#local-settings-file).  

## <a name="python-version"></a>Verze Pythonu 

Funkce Azure podporuje následující verze Pythonu:

| Verze funkcí | Verze<sup>*</sup> Pythonu |
| ----- | ----- |
| 3.x | 3.8<br/>3.7<br/>3.6 |
| 2.x | 3.7<br/>3.6 |

<sup>*</sup>Oficiální distribuce CPython

Chcete-li požádat o konkrétní verzi Pythonu při `--runtime-version` vytváření [`az functionapp create`](/cli/azure/functionapp#az-functionapp-create) aplikace funkcí v Azure, použijte možnost příkazu. Funkce runtime verze je `--functions-version` nastavena možností. Verze Pythonu je nastavena, když je aplikace funkce vytvořena a nelze ji změnit.  

Při místním spuštění modul runtime používá dostupnou verzi Pythonu. 

## <a name="package-management"></a>Správa balíčků

Při místním vývoji pomocí nástrojů Azure Functions Core Tools nebo visual studio code `requirements.txt` přidejte do `pip`souboru názvy a verze požadovaných balíčků a nainstalujte je pomocí . 

Například následující požadavky soubor a pip příkaz lze `requests` použít k instalaci balíčku z PyPI.

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>Publikování do Azure

Až budete připraveni k publikování, ujistěte se, že všechny veřejně dostupné závislosti jsou uvedeny v souboru requirements.txt, který je umístěn v kořenovém adresáři projektu. 

Soubory projektu a složky, které jsou vyloučeny z publikování, včetně složky virtuálního prostředí, jsou uvedeny v souboru .funcignore.

Pro publikování projektu Pythonu do Azure jsou podporované tři akce sestavení:

+ Vzdálené sestavení: Závislosti jsou získávány vzdáleně na základě obsahu souboru requirements.txt. [Vzdálené sestavení](functions-deployment-technologies.md#remote-build) je doporučená metoda sestavení. Vzdálené je také výchozí možnost sestavení nástrojů Azure. 
+ Místní sestavení: Závislosti jsou získávány místně na základě obsahu souboru requirements.txt. 
+ Vlastní závislosti: Váš projekt používá balíčky, které nejsou veřejně dostupné pro naše nástroje. (Vyžaduje Docker.)

Chcete-li vytvořit své závislosti a publikovat pomocí systému průběžného doručování (CD), [použijte Azure Pipelines](functions-how-to-azure-devops.md).

### <a name="remote-build"></a>Vzdálené sestavení

Ve výchozím nastavení nástroje Azure Functions Core Tools požadují vzdálené sestavení, když použijete následující příkaz [pro publikování funkce func azure k](functions-run-local.md#publish) publikování projektu Pythonu do Azure. 

```bash
func azure functionapp publish <APP_NAME>
```

Nezapomeňte nahradit `<APP_NAME>` názvem aplikace funkce v Azure.

[Rozšíření funkcí Azure pro visual studio kód](functions-create-first-function-vs-code.md#publish-the-project-to-azure) také požaduje vzdálené sestavení ve výchozím nastavení. 

### <a name="local-build"></a>Místní sestavení

Můžete zabránit dělá vzdálené sestavení pomocí [následujícífunc azure functionapp publikovat](functions-run-local.md#publish) příkaz publikovat s místní sestavení. 

```command
func azure functionapp publish <APP_NAME> --build local
```

Nezapomeňte nahradit `<APP_NAME>` názvem aplikace funkce v Azure. 

Pomocí `--build local` možnosti jsou závislosti projektu čteny ze souboru requirements.txt a tyto závislé balíčky jsou staženy a nainstalovány místně. Soubory a závislosti projektu se nasazují z místního počítače do Azure. To má za následek větší balíček nasazení se nahrává do Azure. Pokud z nějakého důvodu nelze získat závislosti v souboru requirements.txt pomocí nástroje Core Tools, musíte použít možnost vlastních závislostí pro publikování. 

### <a name="custom-dependencies"></a>Vlastní závislosti

Pokud váš projekt používá balíčky, které nejsou veřejně dostupné pro naše nástroje, \_ \_\_\_můžete je zpřístupnit vaší aplikaci tak, že je vložíte do adresáře /.python_packages. Před publikováním spusťte následující příkaz pro místní instalaci závislostí:

```command
pip install  --target="<PROJECT_DIR>/.python_packages/lib/site-packages"  -r requirements.txt
```

Při použití vlastních závislostí `--no-build` byste měli použít možnost publikování, protože jste již nainstalovali závislosti.  

```command
func azure functionapp publish <APP_NAME> --no-build
```

Nezapomeňte nahradit `<APP_NAME>` názvem aplikace funkce v Azure.

## <a name="unit-testing"></a>Testování částí

Funkce napsané v Pythonu mohou být testovány jako jiný kód Pythonu pomocí standardních testovacích rámců. Pro většinu vazeb je možné vytvořit mock vstupní objekt vytvořením instance příslušné `azure.functions` třídy z balíčku. Vzhledem [`azure.functions`](https://pypi.org/project/azure-functions/) k tomu, že balíček není `requirements.txt` okamžitě k dispozici, nezapomeňte jej nainstalovat prostřednictvím souboru, jak je popsáno v části [správy balíčků](#package-management) výše. 

Například následující je falešný test funkce aktivované protokolem HTTP:

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

Zde je další příklad s funkcí spuštěnou frontou:

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

Metoda `tempfile.gettempdir()` vrátí dočasnou složku, která `/tmp`je v Linuxu . Aplikace může tento adresář použít k ukládání dočasných souborů generovaných a používaných vašimi funkcemi během provádění. 

> [!IMPORTANT]
> Není zaručeno, že soubory zapsané do dočasného adresáře zůstanou zachovány napříč vyvoláními. Během horizontálního navýšení kapacity nejsou dočasné soubory sdíleny mezi instancemi. 

Následující příklad vytvoří pojmenovaný dočasný soubor v`/tmp`dočasném adresáři ( ):

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

Doporučujeme udržovat testy ve složce oddělené od složky projektu. To vám zabrání nasazení testovacího kódu s vaší aplikací. 

## <a name="known-issues-and-faq"></a>Známé problémy a nejčastější dotazy

Všechny známé problémy a požadavky na funkce jsou sledovány pomocí seznamu [problémů GitHubu.](https://github.com/Azure/azure-functions-python-worker/issues) Pokud narazíte na problém a nemůžete najít problém v GitHubu, otevřete nový problém a zahrňte podrobný popis problému.

### <a name="cross-origin-resource-sharing"></a>Sdílení prostředků různého původu

Funkce Azure podporuje sdílení prostředků mezi zdroji (CORS). CORS je nakonfigurovaný [na portálu](functions-how-to-use-azure-function-app-settings.md#cors) a prostřednictvím [azure cli](/cli/azure/functionapp/cors). Seznam povolených počátků CORS platí na úrovni aplikace funkce. S cors povoleno odpovědi `Access-Control-Allow-Origin` zahrnují záhlaví. Další informace naleznete v tématu [Sdílení prostředků různého původu](functions-how-to-use-azure-function-app-settings.md#cors).

Seznam povolených původů [není aktuálně podporován](https://github.com/Azure/azure-functions-python-worker/issues/444) pro aplikace funkcí Pythonu. Z důvodu tohoto omezení je nutné `Access-Control-Allow-Origin` výslovně nastavit záhlaví ve funkcích HTTP, jak je znázorněno v následujícím příkladu:

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

Ujistěte se, že také aktualizujete soubor function.json tak, aby podporoval metodu OPTIONS HTTP:

```json
    ...
      "methods": [
        "get",
        "post",
        "options"
      ]
    ...
```

Tato metoda HTTP používá webové prohlížeče k vyjednání seznamu povolených počátků. 

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících materiálech:

* [Dokumentace rozhraní API balíčku Azure Functions](/python/api/azure-functions/azure.functions?view=azure-python)
* [Osvědčené postupy pro službu Azure Functions](functions-best-practices.md)
* [Azure Funkce aktivační události a vazby](functions-triggers-bindings.md)
* [Vazby úložiště objektů blob](functions-bindings-storage-blob.md)
* [Vazby HTTP a Webhooku](functions-bindings-http-webhook.md)
* [Vazby úložiště fronty](functions-bindings-storage-queue.md)
* [Trigger časovače](functions-bindings-timer.md)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest?view=azure-python
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse?view=azure-python
