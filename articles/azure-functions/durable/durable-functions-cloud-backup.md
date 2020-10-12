---
title: Ventilátory/ventilátor – ve scénářích Durable Functions – Azure
description: Naučte se implementovat scénář s ventilátorem na více instancí v rozšíření Durable Functions pro Azure Functions.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: d61600801286126ea6ffb9a97bc5655b6f233816
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "77562186"
---
# <a name="fan-outfan-in-scenario-in-durable-functions---cloud-backup-example"></a>Scénář ventilátoru/ventilátoru – ve scénáři v Durable Functions-cloudové zálohování – příklad

*Ventilátory/ventilátor – v* systému se odkazuje na vzor spouštění více funkcí současně a pak je prováděna nějaká agregace výsledků. Tento článek vysvětluje ukázku, která používá [Durable Functions](durable-functions-overview.md) k implementaci scénáře ventilátoru nebo ventilátoru na více instancích. Ukázka je trvalá funkce, která zálohuje veškerý obsah webu aplikace nebo jeho část do Azure Storage.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Přehled scénáře

V této ukázce funkce nahrávají všechny soubory v rámci zadaného adresáře rekurzivně do úložiště objektů BLOB. Také počítají celkový počet odeslaných bajtů.

Je možné napsat jednu funkci, která se bude starat o vše. Hlavní problém, ke kterému byste měli běžet, je **škálovatelnost**. Jedno spuštění funkce se dá spustit jenom na jednom virtuálním počítači, takže propustnost se omezí propustností tohoto jednoho virtuálního počítače. Dalším problémem je **spolehlivost**. Pokud dojde k selhání prostřednictvím nástroje nebo pokud celý proces trvá déle než 5 minut, zálohování může selhat v částečně dokončeném stavu. Pak by se muselo restartovat.

Robustnější přístup by byl zápis dvou regulárních funkcí: jeden by mohl vytvořit výčet souborů a přidat názvy souborů do fronty a další by se načetl z fronty a nahrajte soubory do úložiště objektů BLOB. Tento přístup je lepší z hlediska propustnosti a spolehlivosti, ale vyžaduje, abyste zřídili a spravovali frontu. Důležitější je, že značná složitá složitost je zavedená v souvislosti se **správou stavu** a **koordinaci** , pokud chcete něco dalšího dělat, jako je sestava celkového počtu odeslaných bajtů.

Durable Functions přístup vám poskytne všechny uvedené výhody s velmi nízkou režií.

## <a name="the-functions"></a>Funkce

Tento článek vysvětluje následující funkce v ukázkové aplikaci:

* `E2_BackupSiteContent`: [Funkce Orchestrator](durable-functions-bindings.md#orchestration-trigger) , která volá, `E2_GetFileList` aby získala seznam souborů, které se mají zálohovat, a pak volá `E2_CopyFileToBlob` k zálohování každého souboru.
* `E2_GetFileList`: [Funkce Activity](durable-functions-bindings.md#activity-trigger) , která vrátí seznam souborů v adresáři.
* `E2_CopyFileToBlob`: Funkce Activity, která zálohuje jeden soubor do Azure Blob Storage.

### <a name="e2_backupsitecontent-orchestrator-function"></a>E2_BackupSiteContent funkce Orchestrator

Tato funkce nástroje Orchestrator má v podstatě následující:

1. Přijímá `rootDirectory` hodnotu jako vstupní parametr.
2. Volá funkci pro získání rekurzivního seznamu souborů v rámci `rootDirectory` .
3. Umožňuje vícenásobné volání paralelních funkcí k nahrání každého souboru do Azure Blob Storage.
4. Čeká na dokončení všech nahrávání.
5. Vrátí celkový počet bajtů odeslaných do Azure Blob Storage.

# <a name="c"></a>[C#](#tab/csharp)

Zde je kód, který implementuje funkci Orchestrator:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=16-42)]

Všimněte si `await Task.WhenAll(tasks);` řádku. Žádná jednotlivá volání `E2_CopyFileToBlob` funkce nebyla očekávána, *not* což umožňuje paralelní spuštění. Až do tohoto pole úkolů předáte, vrátíme `Task.WhenAll` úlohu, která nebude dokončena, *dokud nebudou dokončeny všechny operace kopírování*. Pokud jste obeznámeni s úlohou Parallel Library (TPL) v rozhraní .NET, pak to není pro vás novinkou. Rozdílem je, že tyto úlohy mohou být souběžně spuštěny na několika virtuálních počítačích a rozšíření Durable Functions zajišťuje, aby bylo kompletní provádění procesu recyklace odolné proti chybám.

Po očekávání od `Task.WhenAll` nás víme, že všechna volání funkcí jsou dokončená a vrátila hodnoty zpátky do nás. Každé volání `E2_CopyFileToBlob` funkce vrátí počet odeslaných bajtů, takže výpočet celkového počtu bajtů je v důsledku přidávání všech vrácených hodnot dohromady.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Funkce používá standardní *function.js* pro funkce nástroje Orchestrator.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/function.json)]

Zde je kód, který implementuje funkci Orchestrator:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/index.js)]

Všimněte si `yield context.df.Task.all(tasks);` řádku. Všechna jednotlivá volání `E2_CopyFileToBlob` funkce nebyla získána, *not* což umožňuje paralelní spuštění. Až do tohoto pole úkolů předáte, vrátíme `context.df.Task.all` úlohu, která nebude dokončena, *dokud nebudou dokončeny všechny operace kopírování*. Pokud jste obeznámeni s [`Promise.all`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all) jazykem JavaScript, pak to není novinkou. Rozdílem je, že tyto úlohy mohou být souběžně spuštěny na několika virtuálních počítačích a rozšíření Durable Functions zajišťuje, aby bylo kompletní provádění procesu recyklace odolné proti chybám.

> [!NOTE]
> Přestože jsou úkoly koncepčně podobné příslibů JavaScriptu, měly by funkce Orchestrator používat `context.df.Task.all` a `context.df.Task.any` místo `Promise.all` a `Promise.race` Spravovat paralelní zpracování úkolů.

Po získání od společnosti víme `context.df.Task.all` , že všechna volání funkcí jsou dokončená a vrátila hodnoty zpátky do nás. Každé volání `E2_CopyFileToBlob` funkce vrátí počet odeslaných bajtů, takže výpočet celkového počtu bajtů je v důsledku přidávání všech vrácených hodnot dohromady.

---

### <a name="helper-activity-functions"></a>Funkce aktivity pomocníka

Funkce pomocné aktivity, jako u jiných ukázek, jsou pouze běžné funkce, které používají `activityTrigger` vazbu triggeru.

#### <a name="e2_getfilelist-activity-function"></a>Funkce aktivity E2_GetFileList

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=44-54)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

*function.jsv* souboru, aby `E2_GetFileList` vypadala takto:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/function.json)]

A zde je implementace:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/index.js)]

Funkce používá `readdirp` modul (verze 2. x) k rekurzivnímu čtení adresářové struktury.

---

> [!NOTE]
> Možná vás zajímá, proč nemůžete vložit tento kód přímo do funkce Orchestrator. To byste ale mohli poškodit jedno ze základních pravidel funkcí Orchestrator, což znamená, že by nikdy neměl dělat vstupně-výstupní operace, včetně přístupu k místnímu systému souborů. Další informace najdete v tématu [omezení kódu funkce nástroje Orchestrator](durable-functions-code-constraints.md).

#### <a name="e2_copyfiletoblob-activity-function"></a>Funkce aktivity E2_CopyFileToBlob

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=56-81)]

> [!NOTE]
> `Microsoft.Azure.WebJobs.Extensions.Storage`Pro spuštění ukázkového kódu budete muset nainstalovat balíček NuGet.

Funkce používá některé pokročilé funkce Azure Functions vazeb (to znamená použití [ `Binder` parametru](../functions-dotnet-class-library.md#binding-at-runtime)), ale nemusíte si dělat starosti s těmito podrobnostmi pro účely tohoto Názorného postupu.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

*function.jsv* souboru pro `E2_CopyFileToBlob` je podobně jednoduchá:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/function.json)]

Implementace JavaScriptu používá [Azure Storage SDK pro uzel](https://github.com/Azure/azure-storage-node) k nahrání souborů do Azure Blob Storage.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/index.js)]

---

Implementace načte soubor z disku a asynchronně streamuje obsah do objektu BLOB se stejným názvem v kontejneru "zálohy". Vrácená hodnota je počet bajtů zkopírovaných do úložiště, který je pak použit funkcí Orchestrator k výpočtu agregačního součtu.

> [!NOTE]
> Toto je dokonalý příklad přesunu vstupně-výstupních operací do `activityTrigger` funkce. Nestačí, když rozšíříte práci napříč mnoha různými počítači, ale získáte i výhody vytváření kontrolních bodů. Pokud se hostitelský proces z nějakého důvodu ukončí, víte, která nahrávání již byla dokončena.

## <a name="run-the-sample"></a>Spuštění ukázky

Orchestraci můžete zahájit odesláním následující žádosti HTTP POST.

```
POST http://{host}/orchestrators/E2_BackupSiteContent
Content-Type: application/json
Content-Length: 20

"D:\\home\\LogFiles"
```

> [!NOTE]
> `HttpStart`Funkce, kterou vyvoláte, funguje jenom s obsahem ve formátu JSON. Z tohoto důvodu `Content-Type: application/json` je vyžadováno záhlaví a cesta k adresáři je zakódována jako řetězec JSON. Fragment kódu HTTP navíc předpokládá, že v souboru je položka, `host.json` která odebere výchozí `api/` předponu ze všech adres URL funkcí TRIGGERU protokolu HTTP. Značky pro tuto konfiguraci najdete v `host.json` souboru v ukázkách.

Tento požadavek HTTP aktivuje nástroj `E2_BackupSiteContent` Orchestrator a předá řetězec `D:\home\LogFiles` jako parametr. Odpověď poskytuje odkaz na získání stavu operace zálohování:

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

V závislosti na tom, kolik souborů protokolů ve vaší aplikaci Function App máte, může dokončení této operace trvat několik minut. Nejnovější stav můžete získat dotazem na adresu URL v `Location` hlavičce předchozí odpovědi HTTP 202.

```
GET http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 202 Accepted
Content-Length: 148
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"runtimeStatus":"Running","input":"D:\\home\\LogFiles","output":null,"createdTime":"2019-06-29T18:50:55Z","lastUpdatedTime":"2019-06-29T18:51:16Z"}
```

V tomto případě je funkce stále spuštěná. Můžete zobrazit vstup uložený do stavu nástroje Orchestrator a čas poslední aktualizace. Hodnoty hlaviček můžete dál používat `Location` k dotazování na dokončení. Pokud je stav "dokončeno", zobrazí se hodnota odpovědi HTTP podobná následující:

```
HTTP/1.1 200 OK
Content-Length: 152
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"D:\\home\\LogFiles","output":452071,"createdTime":"2019-06-29T18:50:55Z","lastUpdatedTime":"2019-06-29T18:51:26Z"}
```

Teď vidíte, že orchestrace je dokončená a přibližně kolik času trvalo dokončení. Zobrazí se také hodnota `output` pole, což znamená, že bylo nahráno přibližně 450 kB protokolů.

## <a name="next-steps"></a>Další kroky

Tato ukázka ukázala, jak implementovat vzorek ventilátoru nebo ventilátoru. Další příklad ukazuje, jak implementovat model monitorování pomocí [trvalých časovačů](durable-functions-timers.md).

> [!div class="nextstepaction"]
> [Spuštění ukázky monitorování](durable-functions-monitor.md)