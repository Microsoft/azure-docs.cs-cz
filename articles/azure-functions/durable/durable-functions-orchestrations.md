---
title: Trvalé orchestrace – funkce Azure
description: Úvod k funkci orchestrace pro azure durable functions.
author: cgillum
ms.topic: overview
ms.date: 09/08/2019
ms.author: azfuncdf
ms.openlocfilehash: caa62483373a240991cfec96437cea7849d9b19c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241357"
---
# <a name="durable-orchestrations"></a>Trvalé orchestrace

Trvalé funkce je rozšíření [funkcí Azure](../functions-overview.md). *Funkci orchestrator* můžete použít k orchestraci provádění dalších trvalých funkcí v rámci aplikace funkce. Funkce orchestratoru mají následující charakteristiky:

* Funkce Orchestrator definují pracovní postupy funkcí pomocí procedurálního kódu. Nejsou potřeba žádné deklarativní schémata nebo návrháři.
* Funkce Orchestrator můžete volat další trvanlivé funkce synchronně a asynchronně. Výstup z volaných funkcí lze spolehlivě uložit do lokálních proměnných.
* Funkce Orchestrator jsou odolné a spolehlivé. Průběh provádění je automaticky kontrolní body, když funkce "čeká" nebo "výnosy". Místní stav se nikdy neztratí, když se proces recykluje nebo se virtuální počítač restartuje.
* Funkce orchestratoru mohou být dlouhotrvající. Celková životnost instance *orchestrace* může být sekundy, dny, měsíce nebo nikdy nekončící.

Tento článek poskytuje přehled funkcí orchestratoru a jak vám mohou pomoci vyřešit různé problémy s vývojem aplikací. Pokud ještě nejste obeznámeni s typy funkcí, které jsou k dispozici v aplikaci trvalé funkce, přečtěte si nejprve článek [Typy trvalých funkcí.](durable-functions-types-features-overview.md)

## <a name="orchestration-identity"></a>Identita orchestrace

Každá *instance* orchestrace má identifikátor instance (označovaný také jako *ID instance).* Ve výchozím nastavení je každé ID instance automaticky vygenerovaným identifikátorem GUID. ID instancí však může být také libovolná hodnota řetězce generovaná uživatelem. Každé ID instance orchestrace musí být jedinečné v centru [úloh](durable-functions-task-hubs.md).

Níže jsou uvedena některá pravidla týkající se ID instancí:

* ID instancí musí být mezi 1 a 256 znaky.
* ID instancí nesmí `@`začínat na .
* ID instancí `/`nesmí `\` `#`obsahovat `?` znaky , , nebo znaky.
* ID instancí nesmí obsahovat řídicí znaky.

> [!NOTE]
> Obecně se doporučuje používat automaticky vygenerovaná ID instancí, kdykoli je to možné. ID instancí generované uživatelem jsou určeny pro scénáře, kde existuje mapování 1:1 mezi instancí orchestrace a některou entitou specifickou pro externí aplikaci, jako je nákupní objednávka nebo doklad.

ID instance orchestrace je povinný parametr pro většinu [operací správy instancí](durable-functions-instance-management.md). Jsou také důležité pro diagnostiku, jako je [například prohledávání dat sledování orchestrace](durable-functions-diagnostics.md#application-insights) v Application Insights pro účely řešení potíží nebo analýzy. Z tohoto důvodu se doporučuje uložit id generované instance do některého externího umístění (například databáze nebo v protokolech aplikací), kde mohou být snadno odkazovat později.

## <a name="reliability"></a>Spolehlivost

Funkce Orchestrator spolehlivě udržovat jejich spuštění stavu pomocí [události sourcing](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) návrhu vzoru. Místo přímého ukládání aktuálního stavu orchestrace, durable task framework používá úložiště pouze pro připojení k zaznamenání celé řady akcí orchestrace funkce trvá. Úložiště pouze pro připojení má mnoho výhod ve srovnání s "dumping" celý stav runtime. Mezi výhody patří vyšší výkon, škálovatelnost a odezva. Získáte také konečnou konzistenci pro transakční data a úplné auditní stopy a historii. Auditní stopy podporují spolehlivé kompenzační akce.

Trvalé funkce používá zdroje událostí transparentně. Na pozadí operátor `await` (C#) `yield` nebo (JavaScript) ve funkci orchestrator poskytuje kontrolu podprocesu orchestrator zpět dispečerovi Durable Task Framework. Dispečer pak potvrdí všechny nové akce, které orchestrator funkce naplánováno (například volání jedné nebo více podřízených funkcí nebo plánování trvalý časovač) do úložiště. Transparentní akce potvrzení připojí k historii provádění instance orchestrace. Historie je uložena v tabulce úložiště. Akce potvrzení pak přidá zprávy do fronty k naplánování skutečné práce. V tomto okamžiku může být funkce orchestrator uvolněna z paměti.

Když orchestrace funkce je dána více práce (například zpráva odpovědi je přijata nebo trvalý časovač vyprší), orchestrator probudí a znovu provede celou funkci od začátku znovu sestavit místní stav. Během přehrání, pokud se kód pokusí volat funkci (nebo provést jinou asynchronní práci), rozhraní Durable Task Framework konzultuje historii provádění aktuální orchestrace. Pokud zjistí, že [funkce aktivity](durable-functions-types-features-overview.md#activity-functions) již byla spuštěna a přinesla výsledek, přehraje výsledek této funkce a kód orchestratoru bude nadále spuštěn. Přehrání pokračuje, dokud není kód funkce dokončen nebo dokud nenaplánuje novou asynchronní práci.

> [!NOTE]
> Aby vzorek přehrání fungoval správně a spolehlivě, musí být kód funkce orchestratoru *deterministický*. Další informace o omezení kódu pro funkce orchestrator, naleznete v tématu [omezení kódu funkce orchestrator.](durable-functions-code-constraints.md)

> [!NOTE]
> Pokud funkce orchestrator vydává zprávy protokolu, chování přehrání může způsobit, že duplicitní zprávy protokolu, které mají být emitovány. Další informace o tom, proč k tomuto chování dochází a jak ho obejít, najdete v tématu [Protokolování.](durable-functions-diagnostics.md#logging)

## <a name="orchestration-history"></a>Historie orchestrace

Chování získávání událostí rozhraní Durable Task Framework je úzce spojeno s kódem funkce orchestratoru, který napíšete. Předpokládejme, že máte funkci orchestrátoru zřetězování aktivit, jako je následující funkce orchestrator:

# <a name="c"></a>[C #](#tab/csharp)

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

---

Na `await` každém příkazu `yield` (C#) nebo (JavaScript) bodů durable task framework spuštění stavu funkce do některé trvalé úložiště back-end (obvykle Azure Table storage). Tento stav je to, co se označuje jako *historie orchestrace*.

### <a name="history-table"></a>Tabulka historie

Obecně řečeno, rámec trvalých úloh provádí na každém kontrolním bodu následující:

1. Uloží historii spuštění do tabulek úložiště Azure.
2. Zařadí zprávy pro funkce, které chce orchestrátor vyvolat.
3. Zařadí zprávy pro samotné orchestrator &mdash; například trvalé zprávy časovače.

Po dokončení kontrolního bodu, funkce orchestrator je zdarma odebrat z paměti, dokud je více práce pro něj provést.

> [!NOTE]
> Azure Storage neposkytuje žádné transakční záruky mezi ukládáním dat do úložiště tabulek a front. Pro zpracování selhání používá poskytovatel úložiště durable functions *vzorky případné konzistence.* Tyto vzory zajistit, že dojde ke ztrátě dat, pokud dojde k chybě nebo ztrátě připojení uprostřed kontrolního bodu.

Po dokončení historie funkce zobrazené dříve vypadá podobně jako v následující tabulce v Azure Table Storage (zkráceně pro ilustrační účely):

| PartitionKey (InstanceId)                     | Typ události             | Časové razítko               | Vstup | Name (Název)             | Výsledek                                                    | Status |
|----------------------------------|-----------------------|----------|--------------------------|-------|------------------|-----------------------------------------------------------|
| eaee885b | Spuštění bylo zahájeno.      | 2017-05-05T18:45:28.852Z | null  | E1_HelloSequence |                                                           |                     |
| eaee885b | OrchestratorZačal   | 2017-05-05T18:45:32.362Z |       |                  |                                                           |                     |
| eaee885b | Úkolnaplánované         | 2017-05-05T18:45:32.670Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | Orchestrator dokončen | 2017-05-05T18:45:32.670Z |       |                  |                                                           |                     |
| eaee885b | Úkol byl dokončen.         | 2017-05-05T18:45:34.201Z |       |                  | ""Dobrý den, Tokio!""                                        |                     |
| eaee885b | OrchestratorZačal   | 2017-05-05T18:45:34.232Z |       |                  |                                                           |                     |
| eaee885b | Úkolnaplánované         | 2017-05-05T18:45:34.435Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | Orchestrator dokončen | 2017-05-05T18:45:34.435Z |       |                  |                                                           |                     |
| eaee885b | Úkol byl dokončen.         | 2017-05-05T18:45:34.763Z |       |                  | ""Dobrý den, Seattle!""                                      |                     |
| eaee885b | OrchestratorZačal   | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     |
| eaee885b | Úkolnaplánované         | 2017-05-05T18:45:34.857Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | Orchestrator dokončen | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     |
| eaee885b | Úkol byl dokončen.         | 2017-05-05T18:45:34.919Z |       |                  | ""Dobrý den, Londýn!""                                       |                     |
| eaee885b | OrchestratorZačal   | 2017-05-05T18:45:35.032Z |       |                  |                                                           |                     |
| eaee885b | Orchestrator dokončen | 2017-05-05T18:45:35.044Z |       |                  |                                                           |                     |
| eaee885b | Spuštění bylo dokončeno.    | 2017-05-05T18:45:35.044Z |       |                  | "[""Dobrý den, Tokio!"",""Dobrý den, Seattle!"",""Dobrý den, Londýn!""]" | Dokončeno           |

Několik poznámek k hodnotám sloupců:

* **PartitionKey**: Obsahuje ID instance orchestraci.
* **EventType**: Představuje typ události. Může se na pojetí může je vyvádět jeden z následujících typů:
  * **OrchestraationStarted**: Funkce orchestrator obnovena z await nebo je spuštěna poprvé. Sloupec `Timestamp` se používá k naplnění deterministické hodnoty rozhraní API `CurrentUtcDateTime` (.NET) a `currentUtcDateTime` (JavaScript).
  * **ExecutionStarted**: Funkce orchestrator začala spouštět poprvé. Tato událost také obsahuje vstup `Input` funkce ve sloupci.
  * **TaskScheduled**: Byla naplánována funkce aktivity. Název funkce aktivity je zachycen `Name` ve sloupci.
  * **Úkol byl dokončen**: Byla dokončena funkce aktivity. Výsledek funkce je ve `Result` sloupci.
  * **TimerCreated**: Byl vytvořen trvalý časovač. Sloupec `FireAt` obsahuje naplánovaný čas času UTC, kdy vyprší platnost časovače.
  * **TimerFired:** Odolný časovač vystřelil.
  * **EventRaised**: Externí událost byla odeslána do instance orchestrace. Sloupec `Name` zachycuje název události a `Input` sloupec zachycuje datovou část události.
  * **OrchestratorCompleted**: Funkce orchestrator byla očekávána.
  * **ContinueAsNew**: Funkce orchestrator byla dokončena a restartována s novým stavem. Sloupec `Result` obsahuje hodnotu, která se používá jako vstup v restartování instance.
  * **ExecutionCompleted**: Funkce orchestrator byla dokončena (nebo se nezdařila). Výstupy funkce nebo podrobnosti o chybě `Result` jsou uloženy ve sloupci.
* **Časové razítko**: Časové razítko UTC události historie.
* **Název**: Název funkce, která byla vyvolána.
* **Vstup**: Vstup funkce ve formátu JSON.
* **Výsledek**: Výstup funkce; to znamená jeho vrácená hodnota.

> [!WARNING]
> I když je to užitečné jako ladicí nástroj, neber žádnou závislost na této tabulce. Může se změnit s tím, jak se vyvíjí rozšíření Durable Functions.

Pokaždé, když funkce obnoví `await` z (C#) nebo `yield` (JavaScript), durable task framework znovu spustí funkci orchestrator od začátku. Při každém opakování konzultuje historii provádění k určení, zda došlo k aktuální asynchronní operaci.  Pokud operace proběhla, rozhraní přehraje výstup této operace okamžitě a `await` přesune se `yield` na další (C#) nebo (JavaScript). Tento proces pokračuje, dokud nebude celá historie přehrána. Po přehrávaných aktuálních hodnotách budou místní proměnné obnoveny na své předchozí hodnoty.

## <a name="features-and-patterns"></a>Vlastnosti a vzory

V následujících částech jsou popsány funkce a vzory funkcí orchestratoru.

### <a name="sub-orchestrations"></a>Dílčí orchestrace

Funkce orchestrator mohou volat funkce aktivity, ale i další funkce orchestratoru. Můžete například vytvořit větší orchestraci z knihovny funkcí orchestratoru. Nebo můžete spustit více instancí funkce orchestrator paralelně.

Další informace a příklady naleznete v článku [Sub-orchestrations.](durable-functions-sub-orchestrations.md)

### <a name="durable-timers"></a>Odolné časovače

Orchestrations můžete naplánovat *trvalé časovače* implementovat zpoždění nebo nastavit zpracování časového plánu na asynchronní akce. Používejte trvalé časovače ve funkcích `Thread.Sleep` `Task.Delay` orchestrator namísto a (C#) nebo `setTimeout()` (JavaScript). `setInterval()`

Další informace a příklady naleznete v článku [Trvalé časovače.](durable-functions-timers.md)

### <a name="external-events"></a>Externí události

Funkce Orchestrator můžete čekat na externí události aktualizovat instanci orchestrace. Tato funkce trvalé funkce je často užitečné pro zpracování lidské interakce nebo jiné externí zpětná volání.

Další informace a příklady naleznete v článku [Externí události.](durable-functions-external-events.md)

### <a name="error-handling"></a>Zpracování chyb

Funkce Orchestrator mohou používat funkce zpracování chyb programovacího jazyka. Existující vzory `try` / `catch` jako jsou podporovány v kódu orchestrace.

Funkce orchestrator můžete také přidat zásady opakování aktivity nebo podorchestrátor funkce, které volají. Pokud aktivita nebo funkce dílčího orchestrátoru selže s výjimkou, zadaná zásada opakování může automaticky zpozdit a opakovat spuštění až na zadaný počet opakování.

> [!NOTE]
> Pokud je neošetřené výjimky ve funkci orchestrator, instance `Failed` orchestrace bude dokončena ve stavu. Instanci orchestrace nelze opakovat, jakmile se nezdařila.

Další informace a příklady naleznete v článku [Zpracování chyb.](durable-functions-error-handling.md)

### <a name="critical-sections-durable-functions-2x-currently-net-only"></a>Kritické oddíly (Trvalé funkce 2.x, aktuálně pouze rozhraní .NET)

Instance orchestrace jsou jednovláknové, takže není nutné se starat o podmínky časování *v rámci* orchestrace. Však podmínky časování jsou možné při orchestraci interakci s externími systémy. Chcete-li zmírnit podmínky časování při interakci s externími `LockAsync` systémy, funkce orchestrator můžete definovat *kritické oddíly* pomocí metody v rozhraní .NET.

Následující ukázkový kód ukazuje funkci orchestrator, která definuje kritický oddíl. Zadá kritický oddíl metodou. `LockAsync` Tato metoda vyžaduje předávání jednoho nebo více odkazů na [trvalé entity](durable-functions-entities.md), který trvale spravuje stav zámku. Kód v kritické části může současně spustit pouze jedna instance této orchestrace.

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

Získá `LockAsync` odolné zámky a `IDisposable` vrátí, který končí kritický oddíl při vyřazení. Tento `IDisposable` výsledek lze použít `using` společně s blokem získat syntaktické reprezentace kritické části. Když funkce orchestrator zadá kritický oddíl, může tento blok kódu spustit pouze jedna instance. Všechny ostatní instance, které se pokusí vstoupit do kritického oddílu, budou blokovány, dokud předchozí instance kritickou část neukončí.

Funkce kritické části je také užitečná pro koordinaci změn trvalých entit. Další informace o kritických částech naleznete v tématu [Trvalé entity "Koordinace entit".](durable-functions-entities.md#entity-coordination)

> [!NOTE]
> Kritické části jsou k dispozici v odolných funkcích 2.0 a vyšších. V současné době tuto funkci implementují pouze orchestrations .NET.

### <a name="calling-http-endpoints-durable-functions-2x"></a>Volání koncových bodů PROTOKOLU HTTP (trvalé funkce 2.x)

Funkce orchestratoru nejsou povoleny pro vstupně-va, jak je popsáno v [omezeních kódu funkce orchestratoru](durable-functions-code-constraints.md). Typické řešení pro toto omezení je zalomit libovolný kód, který je třeba provést vstupně-videa ve funkci aktivity. Orchestrace, které interagují s externími systémy, často používají funkce aktivity k volání HTTP a vracejí výsledek orchestraci.

# <a name="c"></a>[C #](#tab/csharp)

Chcete-li zjednodušit tento společný vzor, `CallHttpAsync` funkce orchestrator můžete použít metodu k vyvolání HTTP API přímo.

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

---

Kromě podpory základních vzorů požadavků a odpovědí podporuje metoda automatické zpracování běžných asynchronních vzorů dotazování PROTOKOLU HTTP 202 a také podporuje ověřování pomocí externích služeb pomocí [spravovaných identit](../../active-directory/managed-identities-azure-resources/overview.md).

Další informace a podrobné příklady naleznete v článku [funkce protokolu HTTP.](durable-functions-http-features.md)

> [!NOTE]
> Volání koncových bodů HTTP přímo z funkcí orchestratoru je k dispozici v trvanlivé funkce 2.0 a vyšší.

### <a name="passing-multiple-parameters"></a>Předání více parametrů

Není možné předat více parametrů funkci aktivity přímo. Doporučujeme předat v poli objektů nebo složených objektů.

# <a name="c"></a>[C #](#tab/csharp)

V rozhraní .NET můžete také použít objekty [ValueTuples.](https://docs.microsoft.com/dotnet/csharp/tuples) Následující ukázka používá nové funkce [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) přidané s [C# 7](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-7#tuples):

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

#### <a name="activity"></a>Aktivita

```javascript
module.exports = async function (context, location) {
    const {city, state} = location; // destructure properties into variables

    // ...
};
```

---

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Omezení kódu orchestrátoru](durable-functions-code-constraints.md)
