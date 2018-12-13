---
title: Referenční informace pro vývojáře Python pro službu Azure Functions
description: Naučte se vyvíjet funkce s Pythong
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
keywords: Azure functions, funkce, zpracování událostí, dynamické výpočty, architektura bez serveru, python
ms.service: functions
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/16/2018
ms.author: glenga
ms.openlocfilehash: 619db07204b88609314d0d3d06709eaa93cb7a43
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53188024"
---
# <a name="azure-functions-python-developer-guide"></a>Příručka pro vývojáře Azure Functions Pythonu

Tento článek je Úvod do vývoje služby Azure Functions pomocí Pythonu. Následující obsah se předpokládá, že jste už čtete [Příručka pro vývojáře Azure Functions](functions-reference.md).

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

## <a name="programming-model"></a>Programovací model

Funkce Azure by měl být bezstavové metodu ve svém skriptu Pythonu, která zpracovává vstup a výstup. Ve výchozím nastavení, modul runtime očekává, že to k implementaci jako globální metoda volána `main()` v `__init__.py` souboru.

Výchozí konfigurace můžete změnit zadáním `scriptFile` a `entryPoint` vlastnosti `function.json` souboru. Například _function.json_ níže říká modul runtime pro použití _customentry()_ metodu _main.py_ soubor jako vstupní bod pro vaši funkci Azure functions.

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  ...
}
```

Z triggerů a vazeb vázaná na funkci prostřednictvím metody atributů s použitím `name` vlastnosti definované v `function.json` konfigurační soubor. Například _function.json_ níže popisuje jednoduchý funkce aktivované službou požadavek HTTP s názvem `req`:

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

Volitelně můžete také deklarovat typy parametrů a návratový typ funkce pomocí poznámek typu Python. Například na stejnou funkci lze zapsat pomocí poznámek, následujícím způsobem:

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
 | - extensions.csproj
 | - bin
```

Existuje sdílený [host.json](functions-host-json.md) soubor, který můžete použít ke konfiguraci aplikace function app. Každá funkce má svůj vlastní soubor s kódem a vazby konfigurační soubor (function.json). 

Sdílený kód by udržováno do samostatné složky. Moduly ve složce SharedCode odkazovat, můžete tuto syntaxi:

```
from ..SharedCode import myFirstHelperFunction
```

Rozšíření vazby používá modul runtime služby Functions jsou definovány v `extensions.csproj` souboru se soubory knihovny v `bin` složky. Při vývoji místně, musíte [registraci rozšíření vazby](functions-triggers-bindings.md#local-development-azure-functions-core-tools) pomocí nástrojů Azure Functions Core. 

Při nasazování projektu funkce do vaší aplikace funkcí v Azure, měly by být celý obsah složky FunctionApp součástí balíčku, ale nikoli složku.

## <a name="inputs"></a>Vstupy

Vstupy jsou rozděleny do dvou kategorií ve službě Azure Functions: vstup triggeru a další vstupy. I když jsou v různých `function.json`, využití je stejný jako v kódu Pythonu. Pojďme se například následující fragment kódu:

```json
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

```python
import azure.functions as func
import logging

def main(req: func.HttpRequest,
         obj: func.InputStream):

    logging.info(f'Python HTTP triggered function processed: {obj.read()}')
```

Při vyvolání funkce požadavek HTTP je předána funkci jako `req`. Načte položku z Azure Blob Storage na základě _id_ v adrese URL trasy a k dispozici jako `obj` v těle funkce.

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

## <a name="importing-shared-code-into-a-function-module"></a>Import sdílený kód do modulu – funkce

Moduly Pythonu publikován společně s moduly funkce musí být importovány pomocí syntaxe relativní import:

```python
from . import helpers  # Use more dots to navigate up the folder structure.
def main(req: func.HttpRequest):
    helpers.process_http_request(req)
```

Můžete také vložit sdílený kód do samostatného balíčku, její publikování do veřejné nebo soukromé instanci PyPI a je zadat jako regulární závislost.

## <a name="async"></a>asynchronní

Protože aplikace function app může existovat pouze jeden proces Pythonu, doporučujeme implementovat vaše funkce Azure Functions jako asynchronní korutině s použitím `async def` příkazu.

```python
# Will be run with asyncio directly
async def main():
    await some_nonblocking_socket_io_op()
```

Pokud je funkce main() synchronní (žádné `async` kvalifikátor) jsme automaticky spustit v `asyncio` fondu vláken.

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

## <a name="python-version-and-package-management"></a>Python version a package management

V současné době Azure Functions podporuje pouze Python 3.6.x (oficiální CPython distribuce).

Při vývoji, místním prostředí s využitím Azure Functions Core Tools nebo Visual Studio Code, přidejte názvy a verzemi požadované balíčky ke `requirements.txt` souboru a nainstalujte si ji pomocí `pip`.

Například následující příkaz Souborová služba a pip požadavky je možné nainstalovat `requests` balíček z PyPI.

```bash
pip install requests
```

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

Jakmile budete připraveni k publikování, ujistěte se, že všechny závislosti jsou uvedeny v `requirements.txt` umístěný v kořenovém adresáři projektu. Úspěšně provést Azure Functions, požadavky na soubor musí obsahovat minimálně následující balíčky:

```txt
azure-functions
azure-functions-worker
grpcio==1.14.1
grpcio-tools==1.14.1
protobuf==3.6.1
six==1.11.0
```

## <a name="publishing-to-azure"></a>Publikování do Azure

Pokud používáte balíček, který vyžaduje kompilátor a nepodporuje instalaci souborů Wheel manylinux kompatibilní z PyPI, publikování na platformě Azure se nezdaří s následující chybou: 

```
There was an error restoring dependencies.ERROR: cannot install <package name - version> dependency: binary dependencies without wheels are not supported.  
The terminal process terminated with exit code: 1
```

Automatické vytváření a konfigurace požadované binární soubory [nainstalovat Docker](https://docs.docker.com/install/) na místním počítači a spusťte následující příkaz pro publikování pomocí [nástrojů Azure Functions Core](functions-run-local.md#v2) (funkce). Nezapomeňte nahradit `<app name>` s názvem aplikace function App v Azure. 

```bash
func azure functionapp <app name> --build-native-deps
```

Pod pokličkou, základní nástroje použít docker ke spuštění [mcr.microsoft.com/azure-functions/python](https://hub.docker.com/r/microsoft/azure-functions/) image jako kontejner na místním počítači. Pomocí tohoto prostředí ho potom sestavíte a nainstalujte požadované moduly ze zdrojového distribučního před balení pro poslední nasazení do Azure.

> [!NOTE]
> Základní nástroje (func) používá PyInstaller program k zablokování uživatelského kódu a závislostí do jednoho samostatný spustitelný soubor pro spuštění v Azure. Tato funkce je aktuálně ve verzi preview a nemusí rozšířit do všech typů balíčků Python. Pokud jste se nepovedlo importovat moduly, zkuste publikování znovu pomocí `--no-bundler` možnost. 
> ```
> func azure functionapp publish <app_name> --build-native-deps --no-bundler
> ```
> Pokud budete nadále dochází k problémům, dejte nám prosím vědět o [otevřete problém](https://github.com/Azure/azure-functions-core-tools/issues/new) a včetně popisu problému. 


Sestavit závislosti a publikovat pomocí průběžné integrace (CI) a průběžné doručování (CD) systému, můžete použít [kanálu Azure](https://docs.microsoft.com/azure/devops/pipelines/get-started-yaml?view=vsts) nebo [Travis CI pro vlastní skripty](https://docs.travis-ci.com/user/deployment/script/). 

Tady je příklad `azure-pipelines.yml` skript pro proces sestavení a publikování.
```yml
pool:
  vmImage: 'Ubuntu 16.04'

steps:
- task: NodeTool@0
  inputs:
    versionSpec: '8.x'

- script: |
    set -e
    echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ wheezy main" | sudo tee /etc/apt/sources.list.d/azure-cli.list
    curl -L https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
    sudo apt-get install -y apt-transport-https
    echo "install Azure CLI..."
    sudo apt-get update && sudo apt-get install -y azure-cli
    npm i -g azure-functions-core-tools --unsafe-perm true
    echo "installing dotnet core"
    curl -sSL https://dot.net/v1/dotnet-install.sh | bash /dev/stdin --channel 2.0
- script: |
    set -e
    az login --service-principal --username "$(APP_ID)" --password "$(PASSWORD)" --tenant "$(TENANT_ID)" 
    func settings add FUNCTIONS_WORKER_RUNTIME python
    func extensions install
    func azure functionapp publish $(APP_NAME) --build-native-deps
```

Tady je příklad `.travis.yaml` skript pro proces sestavení a publikování.

```yml
sudo: required

language: node_js

node_js:
  - "8"

services:
  - docker

before_install:
  - echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ wheezy main" | sudo tee /etc/apt/sources.list.d/azure-cli.list
  - curl -L https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
  - sudo apt-get install -y apt-transport-https
  - sudo apt-get update && sudo apt-get install -y azure-cli
  - npm i -g azure-functions-core-tools --unsafe-perm true


script:
  - az login --service-principal --username "$APP_ID" --password "$PASSWORD" --tenant "$TENANT_ID"
  - az account get-access-token --query "accessToken" | func azure functionapp publish $APP_NAME --build-native-deps

```

## <a name="known-issues-and-faq"></a>Známé problémy a nejčastější dotazy

Všechny známé problémy a požadavky na funkce jsou sledovány v rámci [problémy Githubu](https://github.com/Azure/azure-functions-python-worker/issues) seznamu. Pokud narazíte na problém a nejde najít problému v Githubu otevře nový problém a zahrnují podrobný popis problému.

## <a name="next-steps"></a>Další postup

Další informace najdete v následujících materiálech:

* [Osvědčené postupy pro službu Azure Functions](functions-best-practices.md)
* [Azure Functions aktivačními událostmi a vazbami](functions-triggers-bindings.md)
* [Vazby služby BLOB storage](functions-bindings-storage-blob.md)
* [HTTP a Webhookové vazby](functions-bindings-http-webhook.md)
* [Vazby front úložiště](functions-bindings-storage-queue.md)
* [Trigger časovače](functions-bindings-timer.md)
