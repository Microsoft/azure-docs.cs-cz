---
title: Profilace paměti v aplikacích v Pythonu v Azure Functions
description: Naučte se profilovat využití paměti aplikací v Pythonu a identifikovat problémovou kritickou paměť.
ms.topic: how-to
author: hazhzeng
ms.author: hazeng
ms.date: 3/22/2021
ms.custom: devx-track-python
ms.openlocfilehash: 26f9040016f9eae7e82a85c589d673c90e542f47
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060302"
---
# <a name="profile-python-apps-memory-usage-in-azure-functions"></a>Profilace využití paměti Python Apps v Azure Functions

Během vývoje nebo po nasazení místního projektu aplikace funkcí Pythonu do Azure je dobrým zvykem při analýze potenciálních problémů s pamětí ve vašich funkcích. Taková kritická místa můžou snížit výkon vašich funkcí a vést k chybám. Následující pokyny vám ukážou, jak používat balíček Pythonu [pro profilaci paměti](https://pypi.org/project/memory-profiler) , který poskytuje analýzu spotřeby paměti pro vaše funkce při jejich spuštění.

> [!NOTE]
> Profilace paměti je určena pouze pro analýzu paměťových nároků ve vývojovém prostředí. Nepoužívejte prosím Profiler paměti pro aplikace produkčních funkcí.

## <a name="prerequisites"></a>Požadavky

Než začnete s vývojem aplikace funkcí Pythonu, musíte splnit tyto požadavky:

* [Python 3.6. x nebo vyšší](https://www.python.org/downloads/release/python-374/). Pokud chcete zjistit úplný seznam podporovaných verzí Pythonu v Azure Functions, navštivte prosím [příručku pro vývojáře v Pythonu](functions-reference-python.md#python-version).

* [Azure Functions Core Tools](functions-run-local.md#v2) verze 3. x.

* [Visual Studio Code](https://code.visualstudio.com/) nainstalovat na jednu z [podporovaných platforem](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

* Musíte mít aktivní předplatné Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="memory-profiling-process"></a>Proces profilace paměti

1. V requirements.txt přidejte, `memory-profiler` abyste zajistili, že bude balíček ve vašem nasazení zahrnutý. Pokud vyvíjíte na místním počítači, možná budete chtít [aktivovat virtuální prostředí Pythonu](create-first-function-cli-python.md#create-venv) a provést rozlišení balíčku pomocí `pip install -r requirements.txt` .

2. Ve skriptu funkce (obvykle \_ \_ init \_ \_ . py) přidejte do funkce následující řádky `main()` . Tím se zajistí, že kořenový protokolovací nástroj hlásí podřízené názvy protokolovacích nástrojů, aby bylo možné protokoly profilace paměti rozlišovat předponou `memory_profiler_logs` .

    ```python
    import logging
    import memory_profiler
    root_logger = logging.getLogger()
    root_logger.handlers[0].setFormatter(logging.Formatter("%(name)s: %(message)s"))
    profiler_logstream = memory_profiler.LogFile('memory_profiler_logs', True)

3. Apply the following decorator above any functions that need memory profiling. This does not work directly on the trigger entrypoint `main()` method. You need to create subfunctions and decorate them. Also, due to a memory-profiler known issue, when applying to an async coroutine, the coroutine return value will always be None.

    ```python
    @memory_profiler.profile(stream=memory_logger)

4. Test the memory profiler on your local machine by using azure Functions Core Tools command `func host start`. This should generate a memory usage report with file name, line of code, memory usage, memory increment, and the line content in it.

5. To check the memory profiling logs on an existing function app instance in Azure, you can query the memory profiling logs in recent invocations by pasting the following Kusto queries in Application Insights, Logs.

:::image type="content" source="media/python-memory-profiler-reference/application-insights-query.png" alt-text="Query memory usage of a Python app in Application Insights":::

```text
traces
| where timestamp > ago(1d)
| where message startswith_cs "memory_profiler_logs:"
| parse message with "memory_profiler_logs: " LineNumber "  " TotalMem_MiB "  " IncreMem_MiB "  " Occurences "  " Contents
| union (
    traces
    | where timestamp > ago(1d)
    | where message startswith_cs "memory_profiler_logs: Filename: "
    | parse message with "memory_profiler_logs: Filename: " FileName
    | project timestamp, FileName, itemId
)
| project timestamp, LineNumber=iff(FileName != "", FileName, LineNumber), TotalMem_MiB, IncreMem_MiB, Occurences, Contents, RequestId=itemId
| order by timestamp asc
```

## <a name="example"></a>Příklad

Tady je příklad provádění profilace paměti u asynchronního a synchronního triggeru HTTP s názvem "HttpTriggerAsync" a "HttpTriggerSync" v uvedeném pořadí. Vytvoříme aplikaci funkcí Pythonu, která jednoduše pošle žádosti o získání na domovskou stránku Microsoftu.

### <a name="create-a-python-function-app"></a>Vytvoření aplikace funkcí v Pythonu

Aplikace funkcí v Pythonu by měla následovat Azure Functions zadané [struktuře složek](functions-reference-python.md#folder-structure). K vytvoření uživatelského rozhraní projektu doporučujeme použít Azure Functions Core Tools spuštěním následujících příkazů:

```bash
func init PythonMemoryProfilingDemo --python
cd PythonMemoryProfilingDemo
func new -l python -t HttpTrigger -n HttpTriggerAsync -a anonymous
func new -l python -t HttpTrigger -n HttpTriggerSync -a anonymous
```

### <a name="update-file-contents"></a>Aktualizovat obsah souboru

requirements.txt definuje balíčky, které budou použity v našem projektu. Kromě Azure Functions SDK a paměti – Profiler zavádíme `aiohttp` pro asynchronní požadavky HTTP a `requests` synchronní volání http.

```text
# requirements.txt

azure-functions
memory-profiler
aiohttp
requests
```

Musíme také přepsat asynchronní aktivační proceduru HTTP `HttpTriggerAsync/__init__.py` a nakonfigurovat profil paměti, formát kořenového protokolovacího nástroje a vazbu streamování protokolovacího nástroje.

```python
# HttpTriggerAsync/__init__.py

import azure.functions as func
import aiohttp
import logging
import memory_profiler

# Update root logger's format to include the logger name. Ensure logs generated
# from memory profiler can be filtered by "memory_profiler_logs" prefix.
root_logger = logging.getLogger()
root_logger.handlers[0].setFormatter(logging.Formatter("%(name)s: %(message)s"))
profiler_logstream = memory_profiler.LogFile('memory_profiler_logs', True)

async def main(req: func.HttpRequest) -> func.HttpResponse:
    await get_microsoft_page_async('https://microsoft.com')
    return func.HttpResponse(
        f"Microsoft Page Is Loaded",
        status_code=200
    )

@memory_profiler.profile(stream=profiler_logstream)
async def get_microsoft_page_async(url: str):
    async with aiohttp.ClientSession() as client:
        async with client.get(url) as response:
            await response.text()
    # @memory_profiler.profile does not support return for coroutines.
    # All returns become None in the parent functions.
    # GitHub Issue: https://github.com/pythonprofilers/memory_profiler/issues/289
```

Synchronní Trigger HTTP najdete v následující `HttpTriggerSync/__init__.py` části kódu:

```python
# HttpTriggerSync/__init__.py

import azure.functions as func
import requests
import logging
import memory_profiler

# Update root logger's format to include the logger name. Ensure logs generated
# from memory profiler can be filtered by "memory_profiler_logs" prefix.
root_logger = logging.getLogger()
root_logger.handlers[0].setFormatter(logging.Formatter("%(name)s: %(message)s"))
profiler_logstream = memory_profiler.LogFile('memory_profiler_logs', True)

def main(req: func.HttpRequest) -> func.HttpResponse:
    content = profile_get_request('https://microsoft.com')
    return func.HttpResponse(
        f"Microsoft Page Response Size: {len(content)}",
        status_code=200
    )

@memory_profiler.profile(stream=profiler_logstream)
def profile_get_request(url: str):
    response = requests.get(url)
    return response.content
```

### <a name="profile-python-function-app-in-local-development-environment"></a>Profilovat aplikaci funkcí Pythonu v místním vývojovém prostředí

Po provedení všech výše uvedených změn je k dispozici několik dalších kroků k inicializaci Virtual Environment Pythonu pro Azure Functions runtime.

1. Otevřete Windows PowerShell nebo libovolné prostředí Linux, jak budete chtít.
2. Vytvořte virtuální prostředí Pythonu ve `py -m venv .venv` Windows nebo v systému `python3 -m venv .venv` Linux.
3. Aktivujte prostředí Python virtuální `.venv\Scripts\Activate.ps1` ve Windows PowerShellu nebo `source .venv/bin/activate` v prostředí Linux.
4. Obnovte závislosti Pythonu s `pip install requirements.txt`
5. Spustit modul runtime Azure Functions lokálně s Azure Functions Core Tools `func host start`
6. Odešlete požadavek GET na `https://localhost:7071/api/HttpTriggerAsync` nebo `https://localhost:7071/api/HttpTriggerSync` .
7. V Azure Functions Core Tools by se měla zobrazit Sestava profilace paměti podobná této:

    ```text
    Filename: <ProjectRoot>\HttpTriggerAsync\__init__.py
    Line #    Mem usage    Increment  Occurences   Line Contents
    ============================================================
        19     45.1 MiB     45.1 MiB           1   @memory_profiler.profile
        20                                         async def get_microsoft_page_async(url: str):
        21     45.1 MiB      0.0 MiB           1       async with aiohttp.ClientSession() as client:
        22     46.6 MiB      1.5 MiB          10           async with client.get(url) as response:
        23     47.6 MiB      1.0 MiB           4               await response.text()
    ```

## <a name="next-steps"></a>Další kroky

Další informace o Azure Functions vývoje v Pythonu najdete v následujících zdrojích informací:

* [Příručka pro vývojáře Azure Functions Pythonu](functions-reference-python.md)
* [Osvědčené postupy pro službu Azure Functions](functions-best-practices.md)
* [Referenční informace pro vývojáře Azure Functions](functions-reference.md)
