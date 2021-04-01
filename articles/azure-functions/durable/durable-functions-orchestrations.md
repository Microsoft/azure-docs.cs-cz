---
title: Trvalé orchestrace – Azure Functions
description: Seznámení s funkcí orchestrace pro Azure Durable Functions.
author: cgillum
ms.topic: overview
ms.date: 09/08/2019
ms.author: azfuncdf
ms.openlocfilehash: ba314963058389e171601407ff00411049eecd45
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97845429"
---
# <a name="durable-orchestrations"></a>Trvalé orchestrace

Durable Functions je rozšíření [Azure Functions](../functions-overview.md). *Funkci Orchestrator* můžete použít k orchestraci provádění dalších trvalých funkcí v rámci aplikace Function App. Funkce nástroje Orchestrator mají následující vlastnosti:

* Funkce Orchestrator definují pracovní postupy funkcí pomocí procedurálního kódu. Nejsou potřeba žádná deklarativní schémata ani návrháře.
* Funkce Orchestrator mohou volat jiné trvalé funkce synchronně a asynchronně. Výstup z volaných funkcí lze spolehlivě Uložit do místních proměnných.
* Funkce nástroje Orchestrator jsou odolné a spolehlivé. Průběh provádění je automaticky nastaven na kontrolní bod, pokud funkce čeká na vyřízení nebo výnosy. Při recyklování nebo restartování virtuálního počítače se místní stav nikdy neztratí.
* Funkce Orchestrator mohou být dlouhotrvající. Celkový životnost *instance orchestrace* může být sekundy, dny, měsíce nebo nikdy nekončí.

Tento článek vám poskytne přehled o funkcích nástroje Orchestrator a o tom, jak vám může pomáhat při řešení různých výzev pro vývoj aplikací. Pokud ještě neznáte typy funkcí, které jsou k dispozici v aplikaci Durable Functions, přečtěte si článek nejdřív o [trvalých typech funkcí](durable-functions-types-features-overview.md) .

## <a name="orchestration-identity"></a>Identita orchestrace

Každá *instance* orchestrace má identifikátor instance (označovaný také jako *ID instance*). Ve výchozím nastavení je každé ID instance automaticky generovaný identifikátor GUID. ID instancí ale může být také jakákoli uživatelem generovaná hodnota řetězce. Každé ID instance orchestrace musí být jedinečné v rámci [centra úloh](durable-functions-task-hubs.md).

Níže jsou některá pravidla týkající se ID instancí:

* ID instance musí mít 1 až 256 znaků.
* ID instance nesmí začínat na `@` .
* Identifikátory instance nesmí obsahovat `/` znaky, `\` , `#` ani `?` .
* ID instance nesmí obsahovat řídicí znaky.

> [!NOTE]
> Obecně se doporučuje používat automaticky generovaná ID instancí, kdykoli je to možné. Uživatelem generovaná ID instancí jsou určena pro scénáře, kdy existuje mapování 1:1 mezi instancí Orchestrace a některou externí entitou specifickou pro aplikaci, jako je například nákupní objednávka nebo dokument.

ID instance orchestrace je povinný parametr pro většinu [operací správy instancí](durable-functions-instance-management.md). Jsou také důležité pro diagnostiku, jako je například [hledání prostřednictvím dat sledování orchestrace](durable-functions-diagnostics.md#application-insights) v Application Insights pro účely řešení potíží nebo analýzy. Z tohoto důvodu se doporučuje uložit vygenerovaná ID instance do nějakého externího umístění (například databáze nebo v protokolech aplikací), kde je lze snadno odkazovat později.

## <a name="reliability"></a>Spolehlivost

Služba Orchestrator Functions spolehlivě udržuje jejich stav spouštění pomocí vzoru návrhu pro vytváření [událostí](/azure/architecture/patterns/event-sourcing) . Místo přímého ukládání aktuálního stavu orchestrace používá nepřímý úkol úložiště pouze připojit k zaznamenání celé řady akcí, které orchestrace funkce provádí. Úložiště jen pro připojení má mnoho výhod v porovnání s "výpisem" celého běhového stavu. Mezi výhody patří vyšší výkon, škálovatelnost a rychlost odezvy. Získáte také konečnou konzistenci pro transakční data a úplné záznamy a historii auditu. Záznamy auditu podporují spolehlivé kompenzační akce.

Durable Functions používá zdrojové události transparentně. Na pozadí `await` operátor (C#) nebo `yield` (JavaScript/Python) ve funkci Orchestrator poskytuje kontrolu nad vláknem nástroje Orchestrator zpátky do trvalého dispečeru rozhraní úloh. Dispečer pak potvrdí všechny nové akce, které naplánovala funkce Orchestrator (například volání jedné nebo více podřízených funkcí nebo naplánování trvalého časovače) do úložiště. Transparentní akce potvrzení připojí k historii spuštění instance Orchestration. Historie je uložená v tabulce úložiště. Akce potvrzení pak přidá zprávy do fronty, aby naplánovala skutečnou práci. V tomto okamžiku může být funkce Orchestrator uvolněna z paměti.

Když je funkce orchestrace předána více práce (například přijetí zprávy odpovědi nebo vypršení platnosti časovače), nástroj Orchestrator probudí a znovu spustí celou funkci od začátku až po opětovné sestavení místního stavu. Při opakovaném přehrání, pokud se kód pokusí zavolat funkci (nebo provést jakoukoli jinou asynchronní práci), bude architektura trvalého úkolu vyzvat k historii spuštění aktuální orchestrace. Pokud zjistí, že [funkce Activity](durable-functions-types-features-overview.md#activity-functions) již byla provedena a způsobila výsledek, přehraje výsledek této funkce a kód nástroje Orchestrator pokračuje v běhu. Opětovné přehrání pokračuje, dokud nebude kód funkce dokončen nebo dokud nebude naplánována nová asynchronní práce.

> [!NOTE]
> Aby model opětovného přehrávání fungoval správně a spolehlivě, musí být kód funkce Orchestrator *deterministický*. Další informace o omezeních kódu pro funkce nástroje Orchestrator naleznete v tématu [omezení kódu funkce nástroje Orchestrator](durable-functions-code-constraints.md) .

> [!NOTE]
> Pokud funkce Orchestrator vygeneruje zprávy protokolu, chování při opakovaném přehrávání může způsobit vygenerování duplicitních zpráv protokolu. V tématu [protokolování](durable-functions-diagnostics.md#app-logging) najdete další informace o tom, proč k tomuto chování dochází a jak ho obejít.

## <a name="orchestration-history"></a>Historie orchestrace

Chování při vytváření událostí pro trvalý rámec úloh je úzce spojeno s kódem funkce Orchestrator, který zapisujete. Předpokládejme, že máte funkci Orchestrator pro řetězení aktivit, jako je například následující funkce Orchestrator:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("E1_HelloSequence")]
public static async Task<List<string>> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var outputs = new List<string>();

    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Tokyo"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Seattle"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return outputs;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const output = [];
    output.push(yield context.df.callActivity("E1_SayHello", "Tokyo"));
    output.push(yield context.df.callActivity("E1_SayHello", "Seattle"));
    output.push(yield context.df.callActivity("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return output;
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    result1 = yield context.call_activity('SayHello', "Tokyo")
    result2 = yield context.call_activity('SayHello', "Seattle")
    result3 = yield context.call_activity('SayHello', "London")
    return [result1, result2, result3]

main = df.Orchestrator.create(orchestrator_function)
```
---

V každém z nich `await` (C#) nebo `yield` (JavaScript/Python) příkaz, dohlíží k trvalému stavu spuštění funkce do nějakého back-endu odolného úložiště (obvykle Azure Table Storage). Tento stav je označován jako *Historie orchestrace*.

### <a name="history-table"></a>Tabulka historie

Obecně řečeno, trvalá architektura pro úlohy v každém kontrolním bodu provede následující:

1. Uloží historii spouštění do Azure Storage tabulek.
2. Zařazování zpráv do fronty pro funkce, které chce Orchestrator vyvolat.
3. Ve frontě zařazování zpráv pro nástroj Orchestrator &mdash; , například trvalé zprávy časovače.

Po dokončení kontrolního bodu je možné odebrat funkci Orchestrator z paměti, dokud nebude k dispozici více práce.

> [!NOTE]
> Azure Storage neposkytuje žádné transakční záruky mezi uložením dat do úložiště tabulek a front. Pro zpracování selhání používá poskytovatel úložiště Durable Functions konečné vzorce *konzistence* . Tyto vzory zajistí, že nedojde ke ztrátě dat v případě selhání nebo ztráty připojení uprostřed kontrolního bodu.

Po dokončení bude historie dříve zobrazených funkcí vypadat podobně jako v následující tabulce v Azure Table Storage (pro ilustraci se zkráceně):

| PartitionKey (InstanceId)                     | Typ události             | Timestamp               | Vstup | Name             | Výsledek                                                    | Status |
|----------------------------------|-----------------------|----------|--------------------------|-------|------------------|-----------------------------------------------------------|
| eaee885b | ExecutionStarted      | 2017-05-05T18:45:28.852 Z | null  | E1_HelloSequence |                                                           |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:32.362 Z |       |                  |                                                           |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:32.670 Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:32.670 Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.201 Z |       |                  | "" "Hello Tokio!" "                                        |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.232 Z |       |                  |                                                           |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.435 Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.435 Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.763 Z |       |                  | "" "Hello Praha!" "                                      |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.857 Z |       |                  |                                                           |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.857 Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.857 Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.919 Z |       |                  | "" "Hello Londýn!" "                                       |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:35.032 Z |       |                  |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:35.044 Z |       |                  |                                                           |                     |
| eaee885b | ExecutionCompleted    | 2017-05-05T18:45:35.044 Z |       |                  | "[" "Hello Tokio!" "," "Hello Seattle!" "," "Hello Londýn!" "]" | Dokončeno           |

Několik poznámek na hodnotách sloupců:

* **PartitionKey**: obsahuje ID instance orchestrace.
* **EventType**: představuje typ události. Může být jeden z následujících typů:
  * **OrchestrationStarted**: funkce Orchestrator obnovila z await nebo je spuštěná poprvé. `Timestamp`Sloupec se používá k naplnění deterministické hodnoty pro `CurrentUtcDateTime` rozhraní API (.NET), `currentUtcDateTime` (JavaScript) a `current_utc_datetime` (Python).
  * **ExecutionStarted**: funkce Orchestrator zahájila první spuštění. Tato událost také obsahuje vstup funkce ve `Input` sloupci.
  * **TaskScheduled**: naplánovala se funkce Activity. Název funkce aktivity je zachycen ve `Name` sloupci.
  * **TaskCompleted**: byla dokončena funkce aktivity. Výsledek funkce je ve `Result` sloupci.
  * **TimerCreated**: byl vytvořen trvalý časovač. `FireAt`Sloupec obsahuje plánovaný čas UTC, kdy vyprší platnost časovače.
  * **TimerFired**: byl aktivován trvalý časovač.
  * **EventRaised**: do instance Orchestration byla odeslána externí událost. `Name`Sloupec zachytí název události a `Input` sloupec zachycuje datovou část události.
  * **OrchestratorCompleted**: očekává se funkce Orchestrator.
  * **ContinueAsNew**: funkce Orchestrator byla dokončena a restartována s novým stavem. `Result`Sloupec obsahuje hodnotu, která se používá jako vstup v restartované instanci.
  * **ExecutionCompleted**: funkce Orchestrator se úspěšně dokončila (nebo selhala). Výstupy funkce nebo podrobnosti o chybě jsou uloženy ve `Result` sloupci.
* **Timestamp**: časové razítko UTC události historie.
* **Název**: název funkce, která byla vyvolána.
* **Input**: vstup ve formátu JSON funkce.
* **Výsledek**: výstup funkce; To znamená, že návratová hodnota.

> [!WARNING]
> I když je to užitečné jako ladicí nástroj, neprovádějte žádnou závislost v této tabulce. Může se změnit, jak se vyvíjí rozšíření Durable Functions.

Pokaždé, když se funkce obnoví z `await` (C#) nebo `yield` (JavaScript/Python), reaktivuje se znovu funkce Orchestrator od nuly. Při každém opakovaném spuštění prochází historii spouštění a určí, zda aktuální asynchronní operace proběhla.  Pokud tato operace proběhla, rozhraní přehraje výstup této operace okamžitě a přesune se k dalšímu `await` (C#) nebo `yield` (JavaScript/Python). Tento proces pokračuje, dokud nebude celá historie znovu přehrána. Po opětovném přehrání aktuální historie budou místní proměnné obnoveny na jejich předchozí hodnoty.

## <a name="features-and-patterns"></a>Funkce a vzory

V dalších částech najdete popis funkcí a vzorů funkcí nástroje Orchestrator.

### <a name="sub-orchestrations"></a>Dílčí orchestrace

Funkce Orchestrator mohou volat funkce aktivity, ale také jiné funkce nástroje Orchestrator. Můžete například vytvořit větší orchestraci z knihovny funkcí nástroje Orchestrator. Nebo můžete souběžně spustit více instancí funkce nástroje Orchestrator.

Další informace a příklady najdete v článku o [dílčích orchestrcích](durable-functions-sub-orchestrations.md) .

### <a name="durable-timers"></a>Trvalé časovače

Orchestrace můžou naplánovat *trvalé časovače* pro implementaci zpoždění nebo nastavení zpracování časového limitu u asynchronních akcí. Používejte odolné časovače ve funkcích nástroje Orchestrator místo `Thread.Sleep` a `Task.Delay` (C#) nebo `setTimeout()` a (JavaScript) `setInterval()` , nebo `time.sleep()` (Python).

Další informace a příklady najdete v článku o [trvalých časovačích](durable-functions-timers.md) .

### <a name="external-events"></a>Externí události

Funkce Orchestrator mohou počkat na externí události a aktualizovat instanci Orchestration. Tato funkce Durable Functions často je užitečná pro zpracování lidské interakce nebo jiných externích zpětných volání.

Další informace a příklady najdete v článku věnovaném [externím událostem](durable-functions-external-events.md) .

### <a name="error-handling"></a>Zpracování chyb

Funkce nástroje Orchestrator mohou používat funkce pro zpracování chyb v programovacím jazyce. Existující vzory, jako `try` / `catch` jsou podporované, jsou podporovány v kódu orchestrace.

Funkce nástroje Orchestrator mohou také přidat zásady opakování do aktivity nebo dílčích funkcí Orchestrator, které volají. Pokud dojde k chybě aktivity nebo dílčí funkce Orchestrator s výjimkou, zadané zásady opakování můžou automaticky zpozdit a opakovat spuštění v zadaném počtu.

> [!NOTE]
> Pokud ve funkci Orchestrator dojde k neošetřené výjimce, instance Orchestration se dokončí ve `Failed` stavu. Instanci orchestrace nelze opakovat, jakmile se nezdařila.

Další informace a příklady najdete v článku o [zpracování chyb](durable-functions-error-handling.md) .

### <a name="critical-sections-durable-functions-2x-currently-net-only"></a>Kritické oddíly (Durable Functions 2. x, aktuálně pouze .NET)

Instance orchestrace jsou jednoduché vlákny, takže není nutné se starat o konflikty časování *v rámci* orchestrace. V případě, že orchestrace komunikují s externími systémy, je ale možné konflikty časování. Chcete-li zmírnit konflikty časování při komunikaci s externími systémy, mohou funkce nástroje Orchestrator definovat *kritické oddíly* pomocí `LockAsync` metody v rozhraní .NET.

Následující vzorový kód ukazuje funkci nástroje Orchestrator, která definuje kritickou část. Do kritické části se zadává pomocí `LockAsync` metody. Tato metoda vyžaduje předání jednoho nebo více odkazů na [netrvalou entitu](durable-functions-entities.md), která trvale spravuje stav zámku. Pouze jediná instance této orchestrace může spustit kód v sekci kritické v daném okamžiku.

```csharp
[FunctionName("Synchronize")]
public static async Task Synchronize(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var lockId = new EntityId("LockEntity", "MyLockIdentifier");
    using (await context.LockAsync(lockId))
    {
        // critical section - only one orchestration can enter at a time
    }
}
```

Načte `LockAsync` trvalé zámky a vrátí hodnotu `IDisposable` , která ukončí kritickou část, pokud byla uvolněna. Tento `IDisposable` výsledek lze použít společně s `using` blokem pro získání syntaktické reprezentace oddílu kritického. Když funkce Orchestrator vstoupí do kritické části, může tento blok kódu spustit pouze jedna instance. Všechny ostatní instance, které se pokoušejí zadat kritickou část, budou zablokovány, dokud předchozí instance neukončí kritickou část.

Funkce kritická část je také užitečná pro koordinaci změn trvalých entit. Další informace o důležitých částech najdete v tématu [odolné entity "koordinace entit"](durable-functions-entities.md#entity-coordination) .

> [!NOTE]
> Důležité oddíly jsou k dispozici v Durable Functions 2,0 a novějších. V současné době tuto funkci implementují jenom orchestrace .NET.

### <a name="calling-http-endpoints-durable-functions-2x"></a>Volání koncových bodů HTTP (Durable Functions 2. x)

Funkce nástroje Orchestrator nejsou povoleny v/v, jak je popsáno v tématu [omezení kódu funkce nástroje Orchestrator](durable-functions-code-constraints.md). Typickým řešením pro toto omezení je zabalení jakéhokoli kódu, který potřebuje v/v funkce Activity. Orchestrace, která komunikuje s externími systémy, často používají funkce aktivity k provádění volání HTTP a vracejí výsledek do orchestrace.

# <a name="c"></a>[C#](#tab/csharp)

Pro zjednodušení tohoto obecného vzoru mohou funkce nástroje Orchestrator použít `CallHttpAsync` metodu k přímému vyvolání rozhraní HTTP API.

```csharp
[FunctionName("CheckSiteAvailable")]
public static async Task CheckSiteAvailable(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    Uri url = context.GetInput<Uri>();

    // Makes an HTTP GET request to the specified endpoint
    DurableHttpResponse response = 
        await context.CallHttpAsync(HttpMethod.Get, url);

    if (response.StatusCode >= 400)
    {
        // handling of error codes goes here
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const url = context.df.getInput();
    var res = yield context.df.callHttp("GET", url);
    if (res.statusCode >= 400) {
        // handling of error codes goes here
    }
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    url = context.get_input()
    res = yield context.call_http('GET', url)
    if res.status_code >= 400:
        # handing of error code goes here
```
---

Kromě podpory základních vzorů požadavků a odpovědí podporuje metoda automatické zpracování běžných asynchronních vzorů cyklického dotazování HTTP 202 a zároveň podporuje ověřování pomocí externích služeb pomocí [spravovaných identit](../../active-directory/managed-identities-azure-resources/overview.md).

Další informace a podrobné příklady najdete v článku věnovaném [funkcím protokolu HTTP](durable-functions-http-features.md) .

> [!NOTE]
> Volání koncových bodů HTTP přímo z funkcí nástroje Orchestrator je k dispozici v Durable Functions 2,0 a vyšších.

### <a name="passing-multiple-parameters"></a>Předávání více parametrů

Není možné předat více parametrů funkci Activity přímo. Doporučení je nutné předat v poli objektů nebo složených objektů.

# <a name="c"></a>[C#](#tab/csharp)

V rozhraní .NET můžete také použít objekty [ValueTuple](/dotnet/csharp/tuples) . Následující ukázka používá nové funkce [ValueTuple](/dotnet/csharp/tuples) přidané v [jazyce C# 7](/dotnet/csharp/whats-new/csharp-7#tuples):

```csharp
[FunctionName("GetCourseRecommendations")]
public static async Task<object> RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string major = "ComputerScience";
    int universityYear = context.GetInput<int>();

    object courseRecommendations = await context.CallActivityAsync<object>(
        "CourseRecommendations",
        (major, universityYear));
    return courseRecommendations;
}

[FunctionName("CourseRecommendations")]
public static async Task<object> Mapper([ActivityTrigger] IDurableActivityContext inputs)
{
    // parse input for student's major and year in university
    (string Major, int UniversityYear) studentInfo = inputs.GetInput<(string, int)>();

    // retrieve and return course recommendations by major and university year
    return new
    {
        major = studentInfo.Major,
        universityYear = studentInfo.UniversityYear,
        recommendedCourses = new []
        {
            "Introduction to .NET Programming",
            "Introduction to Linux",
            "Becoming an Entrepreneur"
        }
    };
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="orchestrator"></a>Orchestrator

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const location = {
        city: "Seattle",
        state: "WA"
    };
    const weather = yield context.df.callActivity("GetWeather", location);

    // ...
};
```

#### <a name="getweather-activity"></a>`GetWeather` Akce

```javascript
module.exports = async function (context, location) {
    const {city, state} = location; // destructure properties into variables

    // ...
};
```

# <a name="python"></a>[Python](#tab/python)

#### <a name="orchestrator"></a>Orchestrator

```python
from collections import namedtuple
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    Location = namedtuple('Location', ['city', 'state'])
    location = Location(city='Seattle', state= 'WA')

    weather = yield context.call_activity("GetWeather", location)

    # ...

```
#### <a name="getweather-activity"></a>`GetWeather` Akce

```python
from collections import namedtuple

Location = namedtuple('Location', ['city', 'state'])

def main(location: Location) -> str:
    city, state = location
    return f"Hello {city}, {state}!"
```

---

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Omezení kódu orchestrátoru](durable-functions-code-constraints.md)
