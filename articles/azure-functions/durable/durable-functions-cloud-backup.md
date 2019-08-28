---
title: Ventilátory/ventilátor – ve scénářích Durable Functions – Azure
description: Naučte se implementovat scénář s ventilátorem na více instancí v rozšíření Durable Functions pro Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 3db98039ae057e48867c91d1081c38066067c621
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087438"
---
# <a name="fan-outfan-in-scenario-in-durable-functions---cloud-backup-example"></a>Scénář ventilátoru/ventilátoru – ve scénáři v Durable Functions-cloudové zálohování – příklad

*Ventilátory/ventilátor – v* systému se odkazuje na vzor spouštění více funkcí současně a pak je prováděna nějaká agregace výsledků. Tento článek vysvětluje ukázku, která používá [Durable Functions](durable-functions-overview.md) k implementaci scénáře ventilátoru nebo ventilátoru na více instancích. Ukázka je trvalá funkce, která zálohuje veškerý obsah webu aplikace nebo jeho část do Azure Storage.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Přehled scénáře

V této ukázce funkce nahrávají všechny soubory v rámci zadaného adresáře rekurzivně do úložiště objektů BLOB. Také počítají celkový počet odeslaných bajtů.

Je možné napsat jednu funkci, která se bude starat o vše. Hlavní problém, ke kterému byste měli běžet, je **škálovatelnost**. Jedno spuštění funkce se dá spustit jenom na jednom virtuálním počítači, takže propustnost se omezí propustností tohoto jediného virtuálního počítače. Dalším problémem je **spolehlivost**. Pokud dojde k selhání prostřednictvím nástroje nebo pokud celý proces trvá déle než 5 minut, zálohování může selhat v částečně dokončeném stavu. Pak by se muselo restartovat.

Robustnější přístup by byl zápis dvou regulárních funkcí: jeden by mohl vytvořit výčet souborů a přidat názvy souborů do fronty a další by se načetl z fronty a nahrajte soubory do úložiště objektů BLOB. To je lepší z hlediska propustnosti a spolehlivosti, ale vyžaduje, abyste zřídili a spravovali frontu. Důležitější je, že značná složitá složitost je zavedená v souvislosti se **správou stavu** a **koordinaci** , pokud chcete něco dalšího dělat, jako je sestava celkového počtu odeslaných bajtů.

Durable Functions přístup vám poskytne všechny uvedené výhody s velmi nízkou režií.

## <a name="the-functions"></a>Funkce

Tento článek vysvětluje následující funkce v ukázkové aplikaci:

* `E2_BackupSiteContent`
* `E2_GetFileList`
* `E2_CopyFileToBlob`

Následující části popisují konfiguraci a kód, který se používá pro C# skriptování. Kód pro vývoj v aplikaci Visual Studio se zobrazí na konci článku.

## <a name="the-cloud-backup-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>Orchestrace cloudových záloh (Visual Studio Code a Azure Portal ukázkový kód)

Funkce používá standardní *Function. JSON* pro funkce nástroje Orchestrator. `E2_BackupSiteContent`

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_BackupSiteContent/function.json)]

Zde je kód, který implementuje funkci Orchestrator:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_BackupSiteContent/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (jenom funkce 2. x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/index.js)]

Tato funkce nástroje Orchestrator má v podstatě následující:

1. `rootDirectory` Přijímá hodnotu jako vstupní parametr.
2. Volá funkci pro získání rekurzivního seznamu souborů v rámci `rootDirectory`.
3. Umožňuje vícenásobné volání paralelních funkcí k nahrání každého souboru do Azure Blob Storage.
4. Čeká na dokončení všech nahrávání.
5. Vrátí celkový počet bajtů odeslaných do Azure Blob Storage.

Všimněte si řádkůC# `yield context.df.Task.all(tasks);` () a (JavaScript). `await Task.WhenAll(tasks);` Nebyla očekávána všechna jednotlivá volání `E2_CopyFileToBlob` funkce. To je úmyslné, aby je bylo možné spouštět paralelně. Když `Task.WhenAll` projdeme toto pole úkolů do (C#) nebo `context.df.Task.all` (JavaScript), vrátíme úlohu, která nebude dokončena, dokud nebudou dokončeny *všechny operace kopírování*. Pokud jste obeznámeni s úlohou Parallel Library (TPL) v rozhraní .NET [`Promise.all`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all) nebo v jazyce JavaScript, pak to není pro vás novinkou. Rozdílem je, že tyto úlohy můžou běžet na několika virtuálních počítačích současně a rozšíření Durable Functions zajišťuje, aby bylo kompletní provádění procesu recyklace odolné před dokončením.

> [!NOTE]
> Přestože jsou úkoly koncepčně podobné příslibů JavaScriptu, měly by funkce Orchestrator `context.df.Task.all` používat a `context.df.Task.any` místo `Promise.all` a `Promise.race` spravovat paralelní zpracování úkolů.

Po čekání na `Task.WhenAll` (nebo při obvracení z `context.df.Task.all`) víme, že všechna volání funkcí jsou dokončená a vrátila hodnoty zpátky do nás. Každé volání `E2_CopyFileToBlob` funkce vrátí počet odeslaných bajtů, takže výpočet celkového počtu bajtů je v důsledku přidávání všech vrácených hodnot dohromady.

## <a name="helper-activity-functions"></a>Funkce aktivity pomocníka

Funkce pomocné aktivity, jako u jiných ukázek, jsou pouze běžné funkce, které používají `activityTrigger` vazbu triggeru. Například soubor `E2_GetFileList` *Function. JSON* vypadá takto:

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_GetFileList/function.json)]

A zde je implementace:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_GetFileList/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (jenom funkce 2. x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/index.js)]

Implementace `E2_GetFileList` JavaScriptu`readdirp` používá modul pro rekurzivní čtení adresářové struktury.

> [!NOTE]
> Možná vás zajímá, proč nemůžete vložit tento kód přímo do funkce Orchestrator. To byste ale mohli poškodit jedno ze základních pravidel funkcí Orchestrator, což znamená, že by nikdy neměl dělat vstupně-výstupní operace, včetně přístupu k místnímu systému souborů.

Soubor *Function. JSON* pro `E2_CopyFileToBlob` je podobně jednoduchý:

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_CopyFileToBlob/function.json)]

C# Implementace je také poměrně jednoduchá. K tomu dojde v některých pokročilých funkcích Azure Functions vazeb (to znamená použití `Binder` parametru), ale nemusíte si dělat starosti s těmito podrobnostmi pro účely tohoto Názorného postupu.

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_CopyFileToBlob/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (jenom funkce 2. x)

Implementace JavaScriptu nemá přístup k `Binder` funkci Azure Functions, takže [sada Azure Storage SDK pro uzel](https://github.com/Azure/azure-storage-node) se zavede na místo.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/index.js)]

Implementace načte soubor z disku a asynchronně streamuje obsah do objektu BLOB se stejným názvem v kontejneru "zálohy". Vrácená hodnota je počet bajtů zkopírovaných do úložiště, který je pak použit funkcí Orchestrator k výpočtu agregačního součtu.

> [!NOTE]
> Toto je dokonalý příklad přesunu vstupně-výstupních operací do `activityTrigger` funkce. Nestačí, když rozšíříte práci napříč mnoha různými virtuálními počítači, ale získáte i výhody vytváření kontrolních bodů. Pokud se hostitelský proces z nějakého důvodu ukončí, víte, která nahrávání již byla dokončena.

## <a name="run-the-sample"></a>Spuštění ukázky

Orchestraci můžete zahájit odesláním následující žádosti HTTP POST.

```
POST http://{host}/orchestrators/E2_BackupSiteContent
Content-Type: application/json
Content-Length: 20

"D:\\home\\LogFiles"
```

> [!NOTE]
> `HttpStart` Funkce, kterou vyvoláte, funguje jenom s obsahem ve formátu JSON. Z tohoto důvodu `Content-Type: application/json` je vyžadováno záhlaví a cesta k adresáři je zakódována jako řetězec JSON. Fragment kódu http navíc předpokládá, že v `host.json` souboru je položka, která odebere výchozí `api/` předponu ze všech adres URL funkcí triggeru protokolu HTTP. Značky pro tuto konfiguraci najdete v `host.json` souboru v ukázkách.

Tento požadavek HTTP aktivuje `E2_BackupSiteContent` nástroj Orchestrator a předá `D:\home\LogFiles` řetězec jako parametr. Odpověď poskytuje odkaz na získání stavu operace zálohování:

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

V závislosti na tom, kolik souborů protokolů ve vaší aplikaci Function App máte, může dokončení této operace trvat několik minut. Nejnovější stav můžete získat dotazem na adresu URL v `Location` hlavičce předchozí odpovědi HTTP 202.

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 202 Accepted
Content-Length: 148
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"runtimeStatus":"Running","input":"D:\\home\\LogFiles","output":null,"createdTime":"2017-06-29T18:50:55Z","lastUpdatedTime":"2017-06-29T18:51:16Z"}
```

V tomto případě je funkce stále spuštěná. Můžete zobrazit vstup uložený do stavu nástroje Orchestrator a čas poslední aktualizace. Hodnoty `Location` hlaviček můžete dál používat k dotazování na dokončení. Pokud je stav "dokončeno", zobrazí se hodnota odpovědi HTTP podobná následující:

```
HTTP/1.1 200 OK
Content-Length: 152
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"D:\\home\\LogFiles","output":452071,"createdTime":"2017-06-29T18:50:55Z","lastUpdatedTime":"2017-06-29T18:51:26Z"}
```

Teď vidíte, že orchestrace je dokončená a přibližně kolik času trvalo dokončení. Zobrazí se také hodnota `output` pole, což znamená, že bylo nahráno přibližně 450 kB protokolů.

## <a name="visual-studio-sample-code"></a>Vzorový kód sady Visual Studio

Toto je orchestrace jako jeden C# soubor v projektu sady Visual Studio:

> [!NOTE]
> Pro spuštění ukázkového kódu níže `Microsoft.Azure.WebJobs.Extensions.Storage` budete muset nainstalovat balíček NuGet.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs)]

## <a name="next-steps"></a>Další postup

Tato ukázka ukázala, jak implementovat vzorek ventilátoru nebo ventilátoru. Další příklad ukazuje, jak implementovat model monitorování pomocí trvalých [časovačů](durable-functions-timers.md).

> [!div class="nextstepaction"]
> [Spuštění ukázky monitorování](durable-functions-monitor.md)
