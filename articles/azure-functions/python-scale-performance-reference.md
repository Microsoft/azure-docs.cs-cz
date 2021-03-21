---
title: Zvyšte výkon propustnosti aplikací v Pythonu v Azure Functions
description: Naučte se vyvíjet Azure Functions aplikace pomocí Pythonu, které mají vysoce výkonné a škálovatelné škálování v rámci zátěže.
ms.topic: article
ms.date: 10/13/2020
ms.custom: devx-track-python
ms.openlocfilehash: e3bbdb8819062d45d071633e0208fb58a003da54
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98786102"
---
# <a name="improve-throughput-performance-of-python-apps-in-azure-functions"></a>Zvyšte výkon propustnosti aplikací v Pythonu v Azure Functions

Při vývoji pro Azure Functions s využitím Pythonu potřebujete pochopit, jak vaše funkce fungují a jak výkon ovlivňuje způsob, jakým se vaše aplikace Functions mění. Potřeba je důležitější při navrhování vysoce výkonných aplikací. Hlavní faktory, které je potřeba vzít v úvahu při navrhování, psaní a konfiguraci aplikací Functions, jsou horizontální škálování a výkon propustnosti.

## <a name="horizontal-scaling"></a>Horizontální škálování
Ve výchozím nastavení Azure Functions automaticky monitoruje zatížení aplikace a v případě potřeby vytvoří další instance hostitele pro Python. Azure Functions používá předdefinované prahové hodnoty pro různé typy triggerů k rozhodnutí, kdy přidat instance, například stáří zpráv a velikost fronty pro QueueTrigger. Tyto prahové hodnoty se nedají uživatelsky konfigurovat. Další informace najdete v tématu [škálování na základě událostí v Azure Functions](event-driven-scaling.md).

## <a name="improving-throughput-performance"></a>Zlepšení výkonu propustnosti

Výchozí konfigurace jsou vhodné pro většinu Azure Functionsch aplikací. Můžete ale zvýšit výkon propustnosti vašich aplikací tím, že použijete konfigurace na základě vašeho profilu zatížení. Prvním krokem je pochopení typu úlohy, kterou používáte.

| Typ úlohy | Vlastnosti aplikace Function App       | Příklady                                          |
| ------------- | ---------------------------------- | ------------------------------------------------- |
| **Vstup/výstup – vázaný**     | • Aplikace potřebuje zpracovat mnoho souběžných volání.<br>• Aplikace zpracovává velký počet vstupně-výstupních událostí, jako jsou síťová volání a čtení a zápisy na disk. | • Webová rozhraní API                                          |
| **Vázaný na procesor**     | • Aplikace používá dlouhotrvající výpočty, jako je například změna velikosti obrázku.<br>• Aplikace dělá transformaci dat.                                                | • Zpracování dat<br>• Odvození strojového učení<br> |

 
Jelikož jsou úlohy reálného světa tvořeny většinou kombinace vstupně-výstupních operací a procesoru, měli byste aplikaci profilovat v rámci realistického produkčního zatížení.


### <a name="performance-specific-configurations"></a>Konfigurace specifické pro výkon

Po porozumění profilu úlohy aplikace Function App jsou zde uvedené konfigurace, které můžete použít ke zlepšení výkonu vašich funkcí v propustnosti.

* [Async](#async)
* [Pracovník s více jazyky](#use-multiple-language-worker-processes)
* [Maximální počet pracovních procesů v rámci jazyka pracovního procesu](#set-up-max-workers-within-a-language-worker-process)
* [Smyčka události](#managing-event-loop)
* [Vertikální škálování](#vertical-scaling)



#### <a name="async"></a>Async

Protože [Python je modul runtime s jedním vláknem](https://wiki.python.org/moin/GlobalInterpreterLock), instance hostitele pro Python může ve výchozím nastavení zpracovávat pouze jedno vyvolání funkce. Pro aplikace, které zpracovávají velký počet vstupně-výstupních událostí a/nebo jsou vázané na vstupně-výstupní operace, můžete výrazně zvýšit výkon spuštěním asynchronních funkcí.

Chcete-li spustit funkci asynchronně, použijte `async def` příkaz, který spustí funkci s [asyncio](https://docs.python.org/3/library/asyncio.html) přímo:

```python
async def main():
    await some_nonblocking_socket_io_op()
```
Tady je příklad funkce s triggerem HTTP, který používá klienta http [aiohttp](https://pypi.org/project/aiohttp/) :

```python
import aiohttp

import azure.functions as func

async def main(req: func.HttpRequest) -> func.HttpResponse:
    async with aiohttp.ClientSession() as client:
        async with client.get("PUT_YOUR_URL_HERE") as response:
            return func.HttpResponse(await response.text())

    return func.HttpResponse(body='NotFound', status_code=404)
```


Funkce bez `async` klíčového slova se spustí automaticky ve fondu vláken ThreadPoolExecutor:

```python
# Runs in an ThreadPoolExecutor threadpool. Number of threads is defined by PYTHON_THREADPOOL_THREAD_COUNT. 
# The example is intended to show how default synchronous function are handled.

def main():
    some_blocking_socket_io()
```

Aby bylo možné plně využít výhod spouštění funkcí asynchronně, musí být vstupně-výstupní operace/knihovna, která se používá ve vašem kódu, také implementována i při asynchronním provádění. Používání synchronních vstupně-výstupních operací ve funkcích, které jsou definovány jako asynchronní, **může snížit** celkový výkon. Pokud knihovny, které používáte, nemají implementaci asynchronních verzí, můžete i nadále využívat asynchronní spouštění kódu tím, že ve své aplikaci [spravujete smyčku událostí](#managing-event-loop) . 

Tady je několik příkladů klientských knihoven, které mají implementovaný asynchronní vzor:
- [aiohttp](https://pypi.org/project/aiohttp/) -klient/server HTTP pro asyncio 
- [Rozhraní API datových proudů](https://docs.python.org/3/library/asyncio-stream.html) – primitivní a asynchronní a nedokončené primitivum připravené pro práci se síťovým připojením
- Fronta [Janus](https://pypi.org/project/janus/) -asyncio s podporou pro přístup z více vláken pro Python
- [pyzmq](https://pypi.org/project/pyzmq/) – vazby Pythonu pro ZeroMQ
 
##### <a name="understanding-async-in-python-worker"></a>Princip asynchronního procesu Pythonu

Pokud definujete `async` před signaturou funkce, Python tuto funkci označí jako korutinu. Při volání korutiny může být naplánována jako úloha do smyčky události. Při volání `await` v asynchronní funkci zaregistruje pokračování do smyčky událostí a zavolá smyčku události, aby zpracovával další úlohu během čekací doby.

V našem pracovním procesu Python Worker sdílí smyčku událostí se zákaznickou `async` funkcí a podporuje souběžné zpracování více požadavků. Zákazníkům doporučujeme, aby používali asyncio knihovny kompatibilní s (např. [aiohttp](https://pypi.org/project/aiohttp/), [pyzmq](https://pypi.org/project/pyzmq/)). Používání těchto doporučení značně zvýší propustnost vaší funkce v porovnání s těmito knihovnami implementovanými synchronně způsobem.

> [!NOTE]
>  Pokud je vaše funkce deklarována tak `async` `await` , aby nebyla v rámci své implementace, výkon vaší funkce bude vážně ovlivněn, protože smyčka události bude zablokována, což zabrání pracovnímu procesu Pythonu zpracovávat souběžné požadavky.

#### <a name="use-multiple-language-worker-processes"></a>Použít více pracovních procesů jazyka

Ve výchozím nastavení má každá instance hostitele Functions pracovní proces s jedním jazykem. Počet pracovních procesů na hostitele můžete zvýšit (až 10) pomocí nastavení aplikace [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) . Azure Functions se pak pokusí rovnoměrně distribuovat souběžná volání funkcí mezi tyto pracovní procesy.

U aplikací vázaných na procesor byste měli nastavit, aby počet jazykových pracovních procesů byl stejný nebo vyšší než počet jader, které jsou dostupné na základě aplikace Function App. Další informace najdete v tématu [dostupné skladové položky instance](functions-premium-plan.md#available-instance-skus). 

U aplikací vázaných na vstupně-výstupní operace může být také výhodné zvýšit počet pracovních procesů nad rámec počtu dostupných jader. Pamatujte, že nastavení maximálního počtu pracovních procesů může mít vliv na celkový výkon kvůli zvýšenému počtu požadovaných přepínačů kontextu. 

FUNCTIONS_WORKER_PROCESS_COUNT se vztahuje na každého hostitele, který funkce vytvoří při horizontálním navýšení kapacity aplikace, aby splňovala požadavky.

#### <a name="set-up-max-workers-within-a-language-worker-process"></a>Nastavení maximálního počtu zaměstnanců v rámci pracovního procesu jazyka

Jak je uvedeno v [části](#understanding-async-in-python-worker)Async, pracovní proces jazyka Pythonu zpracovává funkce a [korutiny](https://docs.python.org/3/library/asyncio-task.html#coroutines) jinak. Korutina se spouští v rámci stejné smyčky událostí, na které je spuštěný jazyk Worker. Na druhé straně vyvolání funkce je spuštěno v rámci [ThreadPoolExecutor](https://docs.python.org/3/library/concurrent.futures.html#threadpoolexecutor), které je udržováno pomocí jazyka Worker jako vlákno.

Můžete nastavit hodnotu maximálního počtu pracovních procesů povolených pro spouštění funkcí synchronizace pomocí nastavení aplikace [PYTHON_THREADPOOL_THREAD_COUNT](functions-app-settings.md#python_threadpool_thread_count) . Tato hodnota nastaví `max_worker` argument objektu ThreadPoolExecutor, který umožňuje Pythonu použít fond ve většině `max_worker` vláken k asynchronnímu provedení volání. `PYTHON_THREADPOOL_THREAD_COUNT`Platí pro každého pracovního procesu, který funkce Host vytvoří, a v Pythonu se rozhodne, kdy vytvořit nové vlákno nebo znovu použít stávající nečinné vlákno. Pro starší verze Pythonu (to znamená,, `3.8` `3.7` a `3.6` ) `max_worker` je hodnota nastavená na 1. V případě verze `3.9` Python `max_worker` je nastavena na `None` .

U aplikací vázaných na procesor byste měli zachovat nastavení nízké číslo, od 1 do experimentování s úlohou. Účelem tohoto návrhu je zkrátit čas strávený na přepnutích kontextu a povolit dokončení úkolů vázaných na procesor.

V případě aplikací vázaných na vstupně-výstupní operace byste měli vidět výrazné nárůsty tím, že zvýšíte počet vláken, která při každém vyvolání pracují. doporučení je začínat výchozím nastavením Pythonu – počet jader + 4 a na základě hodnot propustnosti, které vidíte, se pak bude selepšit.

Pro aplikace se smíšenou úlohami byste měli vyrovnávat i `FUNCTIONS_WORKER_PROCESS_COUNT` `PYTHON_THREADPOOL_THREAD_COUNT` konfiguraci a maximalizovat tak propustnost. Abychom porozuměli tomu, co vaše aplikace Function App tráví, doporučujeme, abyste je nastavili jako profilování a nastavili hodnoty podle chování, které jsou k dispozici. Další informace o FUNCTIONS_WORKER_PROCESS_COUNT nastavení aplikace najdete také v této [části](#use-multiple-language-worker-processes) .

> [!NOTE]
>  I když se tato doporučení vztahují na funkce aktivované protokolem HTTP i bez protokolu HTTP, může být nutné upravit jiné konfigurace triggerů pro funkce aktivované jiným způsobem než pomocí protokolu HTTP a získat tak očekávaný výkon z aplikací Function App. Další informace najdete v tomto [článku](functions-best-practices.md).


#### <a name="managing-event-loop"></a>Správa smyčky událostí

Měli byste používat asyncio kompatibilní knihovny třetích stran. Pokud žádná z knihoven třetích stran nevyhovuje vašim potřebám, můžete také spravovat smyčky událostí v Azure Functions. Správa smyček událostí poskytuje větší flexibilitu v rámci správy výpočetních prostředků a umožňuje také zabalit synchronní vstupně-výstupní knihovny do korutin.

K dispozici je mnoho užitečných oficiálních dokumentů Pythonu, které se projednávají s [korutinami a úlohami](https://docs.python.org/3/library/asyncio-task.html) a [smyčkou událostí](https://docs.python.org/3.8/library/asyncio-eventloop.html) pomocí předdefinované knihovny **asyncio** .

Vezměte následující knihovny [požadavků](https://github.com/psf/requests) jako příklad, tento fragment kódu používá knihovnu **asyncio** k zabalení `requests.get()` metody do korutiny, spouštění více webových požadavků na SAMPLE_URL souběžně.


```python
import asyncio
import json
import logging

import azure.functions as func
from time import time
from requests import get, Response


async def invoke_get_request(eventloop: asyncio.AbstractEventLoop) -> Response:
    # Wrap requests.get function into a coroutine
    single_result = await eventloop.run_in_executor(
        None,  # using the default executor
        get,  # each task call invoke_get_request
        'SAMPLE_URL'  # the url to be passed into the requests.get function
    )
    return single_result

async def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    eventloop = asyncio.get_event_loop()

    # Create 10 tasks for requests.get synchronous call
    tasks = [
        asyncio.create_task(
            invoke_get_request(eventloop)
        ) for _ in range(10)
    ]

    done_tasks, _ = await asyncio.wait(tasks)
    status_codes = [d.result().status_code for d in done_tasks]

    return func.HttpResponse(body=json.dumps(status_codes),
                             mimetype='application/json')
```
#### <a name="vertical-scaling"></a>Vertikální škálování
V případě více jednotek zpracování, zejména v případě operací vázaných na procesor, je to možné, že se na plán Premium upgraduje pomocí vyšších specifikací. U vyšších jednotek zpracování můžete upravit počet pracovních procesů podle počtu dostupných jader a dosáhnout vyšší úrovně paralelismu. 

## <a name="next-steps"></a>Další kroky

Další informace o Azure Functions vývoje v Pythonu najdete v následujících zdrojích informací:

* [Příručka pro vývojáře Azure Functions Pythonu](functions-reference-python.md)
* [Osvědčené postupy pro službu Azure Functions](functions-best-practices.md)
* [Referenční informace pro vývojáře Azure Functions](functions-reference.md)

