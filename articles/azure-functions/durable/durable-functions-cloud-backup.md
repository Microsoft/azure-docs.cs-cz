---
title: Ventilátory/ventilátor – ve scénářích Durable Functions – Azure
description: Naučte se implementovat scénář s ventilátorem na více instancí v rozšíření Durable Functions pro Azure Functions.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: d61600801286126ea6ffb9a97bc5655b6f233816
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/22/2020
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

* `E2_BackupSiteContent`: [funkce Orchestrator](durable-functions-bindings.md#orchestration-trigger) , která volá `E2_GetFileList`, aby získala seznam souborů, které se mají zálohovat, a pak zavolá `E2_CopyFileToBlob` k zálohování jednotlivých souborů.
* `E2_GetFileList`: [funkce Activity](durable-functions-bindings.md#activity-trigger) , která vrátí seznam souborů v adresáři.
* `E2_CopyFileToBlob`: funkce Activity, která zálohuje jeden soubor do Azure Blob Storage.

### <a name="e2_backupsitecontent-orchestrator-function"></a>E2_BackupSiteContent funkce Orchestrator

Tato funkce nástroje Orchestrator má v podstatě následující:

1. Převezme `rootDirectory` hodnotu jako vstupní parametr.
2. Volá funkci pro získání rekurzivního seznamu souborů v rámci `rootDirectory`.
3. Umožňuje vícenásobné volání paralelních funkcí k nahrání každého souboru do Azure Blob Storage.
4. Čeká na dokončení všech nahrávání.
5. Vrátí celkový počet bajtů odeslaných do Azure Blob Storage.

# <a name="c"></a>[C#](#tab/csharp)

Zde je kód, který implementuje funkci Orchestrator:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=16-42)]

Všimněte si `await Task.WhenAll(tasks);`ho řádku. Všechna jednotlivá volání funkce `E2_CopyFileToBlob` *nebyla* očekávána, což umožňuje paralelní spuštění. Když předáte tomuto poli úkoly `Task.WhenAll`, vrátíme vám úkol, který se nedokončí, *dokud se nedokončí všechny operace kopírování*. Pokud jste obeznámeni s úlohou Parallel Library (TPL) v rozhraní .NET, pak to není pro vás novinkou. Rozdílem je, že tyto úlohy mohou být souběžně spuštěny na několika virtuálních počítačích a rozšíření Durable Functions zajišťuje, aby bylo kompletní provádění procesu recyklace odolné proti chybám.

Po očekávání od `Task.WhenAll`ví, že všechna volání funkcí jsou dokončená a vrátila hodnoty zpátky do nás. Každé volání funkce `E2_CopyFileToBlob` vrátí počet odeslaných bajtů, takže výpočet celkového počtu bajtů je takový, že se přidají všechny návratové hodnoty dohromady.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Funkce používá standardní *Function. JSON* pro funkce nástroje Orchestrator.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/function.json)]

Zde je kód, který implementuje funkci Orchestrator:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/index.js)]

Všimněte si `yield context.df.Task.all(tasks);`ho řádku. Všechna jednotlivá volání funkce `E2_CopyFileToBlob` *nebyla* získána, což umožňuje paralelní spuštění. Když předáte tomuto poli úkoly `context.df.Task.all`, vrátíme vám úkol, který se nedokončí, *dokud se nedokončí všechny operace kopírování*. Pokud jste obeznámeni s [`Promise.all`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all) v JavaScriptu, pak to není pro vás novinkou. Rozdílem je, že tyto úlohy mohou být souběžně spuštěny na několika virtuálních počítačích a rozšíření Durable Functions zajišťuje, aby bylo kompletní provádění procesu recyklace odolné proti chybám.

> [!NOTE]
> Přestože jsou úkoly koncepčně podobné příslibů JavaScriptu, funkce Orchestrator by měly používat `context.df.Task.all` a `context.df.Task.any` namísto `Promise.all` a `Promise.race` ke správě paralelního zpracování úkolů.

Po získání z `context.df.Task.all`ví, že všechna volání funkcí jsou dokončená a vrátila hodnoty zpátky do nás. Každé volání funkce `E2_CopyFileToBlob` vrátí počet odeslaných bajtů, takže výpočet celkového počtu bajtů je takový, že se přidají všechny návratové hodnoty dohromady.

---

### <a name="helper-activity-functions"></a>Funkce aktivity pomocníka

Funkce pomocné aktivity, jako u jiných vzorků, jsou pouze běžné funkce, které používají vazbu triggeru `activityTrigger`.

#### <a name="e2_getfilelist-activity-function"></a>Funkce aktivity E2_GetFileList

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=44-54)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Soubor *Function. JSON* pro `E2_GetFileList` vypadá takto:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/function.json)]

A zde je implementace:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/index.js)]

Funkce používá modul `readdirp` (verze 2. x) k rekurzivnímu čtení adresářové struktury.

---

> [!NOTE]
> Možná vás zajímá, proč nemůžete vložit tento kód přímo do funkce Orchestrator. To byste ale mohli poškodit jedno ze základních pravidel funkcí Orchestrator, což znamená, že by nikdy neměl dělat vstupně-výstupní operace, včetně přístupu k místnímu systému souborů. Další informace najdete v tématu [omezení kódu funkce nástroje Orchestrator](durable-functions-code-constraints.md).

#### <a name="e2_copyfiletoblob-activity-function"></a>Funkce aktivity E2_CopyFileToBlob

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=56-81)]

> [!NOTE]
> Pro spuštění ukázkového kódu budete muset nainstalovat balíček NuGet `Microsoft.Azure.WebJobs.Extensions.Storage`.

Funkce používá některé pokročilé funkce Azure Functions vazeb (to znamená použití [parametru`Binder`](../functions-dotnet-class-library.md#binding-at-runtime)), ale nemusíte si dělat starosti s těmito podrobnostmi pro účely tohoto Názorného postupu.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Soubor *Function. JSON* pro `E2_CopyFileToBlob` je podobně jednoduchý:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/function.json)]

Implementace JavaScriptu používá [Azure Storage SDK pro uzel](https://github.com/Azure/azure-storage-node) k nahrání souborů do Azure Blob Storage.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/index.js)]

---

Implementace načte soubor z disku a asynchronně streamuje obsah do objektu BLOB se stejným názvem v kontejneru "zálohy". Vrácená hodnota je počet bajtů zkopírovaných do úložiště, který je pak použit funkcí Orchestrator k výpočtu agregačního součtu.

> [!NOTE]
> Toto je dokonalý příklad přesunu vstupně-výstupních operací do funkce `activityTrigger`. Nestačí, když rozšíříte práci napříč mnoha různými počítači, ale získáte i výhody vytváření kontrolních bodů. Pokud se hostitelský proces z nějakého důvodu ukončí, víte, která nahrávání již byla dokončena.

## <a name="run-the-sample"></a>Spuštění ukázky

Orchestraci můžete zahájit odesláním následující žádosti HTTP POST.

```
POST http://{host}/orchestrators/E2_BackupSiteContent
Content-Type: application/json
Content-Length: 20

"D:\\home\\LogFiles"
```

> [!NOTE]
> Funkce `HttpStart`, kterou vyvoláte, funguje jenom s obsahem ve formátu JSON. Z tohoto důvodu je vyžadováno záhlaví `Content-Type: application/json` a cesta k adresáři je zakódována jako řetězec JSON. Kromě toho předpokládá fragment kódu HTTP v souboru `host.json` záznam, který odstraní výchozí předponu `api/` ze všech adres URL funkcí triggeru protokolu HTTP. Značky této konfigurace najdete v souboru `host.json` v ukázkách.

Tento požadavek HTTP aktivuje `E2_BackupSiteContent` Orchestrator a předá řetězec `D:\home\LogFiles` jako parametr. Odpověď poskytuje odkaz na získání stavu operace zálohování:

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

V závislosti na tom, kolik souborů protokolů ve vaší aplikaci Function App máte, může dokončení této operace trvat několik minut. Nejnovější stav můžete získat dotazem na adresu URL v hlavičce `Location` předchozí odpovědi HTTP 202.

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

V tomto případě je funkce stále spuštěná. Můžete zobrazit vstup uložený do stavu nástroje Orchestrator a čas poslední aktualizace. Můžete nadále používat hodnoty hlaviček `Location` k dotazování na dokončení. Pokud je stav "dokončeno", zobrazí se hodnota odpovědi HTTP podobná následující:

```
HTTP/1.1 200 OK
Content-Length: 152
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"D:\\home\\LogFiles","output":452071,"createdTime":"2019-06-29T18:50:55Z","lastUpdatedTime":"2019-06-29T18:51:26Z"}
```

Teď vidíte, že orchestrace je dokončená a přibližně kolik času trvalo dokončení. Zobrazí se také hodnota pole `output`, což znamená, že bylo nahráno přibližně 450 KB protokolů.

## <a name="next-steps"></a>Další kroky

Tato ukázka ukázala, jak implementovat vzorek ventilátoru nebo ventilátoru. Další příklad ukazuje, jak implementovat model monitorování pomocí [trvalých časovačů](durable-functions-timers.md).

> [!div class="nextstepaction"]
> [Spuštění ukázky monitorování](durable-functions-monitor.md)