---
title: Scénáře fan-out/fan-in v odolných funkcích – Azure
description: Zjistěte, jak implementovat scénář fan-out-fan-in v rozšíření Durable Functions pro funkce Azure.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: d61600801286126ea6ffb9a97bc5655b6f233816
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562186"
---
# <a name="fan-outfan-in-scenario-in-durable-functions---cloud-backup-example"></a>Scénář fan-out/fan-in v programu Durable Functions – příklad zálohování v cloudu

*Fan-out/fan-in* odkazuje na vzor provádění více funkcí současně a následné provedení některých agregací výsledků. Tento článek vysvětluje ukázku, která používá [trvalé funkce](durable-functions-overview.md) k implementaci scénáře fan-in/fan-out. Ukázka je trvalá funkce, která zálohuje veškerý nebo některé obsahu webu aplikace do Azure Storage.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Přehled scénáře

V této ukázce funkce nahrát všechny soubory pod zadaným adresářem rekurzivně do úložiště objektů blob. Počítají také celkový počet nahraných bajtů.

Je možné napsat jednu funkci, která se postará o všechno. Hlavním problémem, na který byste narazili, je **škálovatelnost**. Spuštění jedné funkce lze spustit pouze na jednom virtuálním počítači, takže propustnost bude omezena propustností tohoto jednoho virtuálního počítače. Dalším problémem je **spolehlivost**. Pokud dojde k selhání v polovině, nebo pokud celý proces trvá déle než 5 minut, zálohování může selhat v částečně dokončeném stavu. To by pak muselo být restartováno.

Robustnější přístup by bylo napsat dvě běžné funkce: jeden by výčet souborů a přidat názvy souborů do fronty a jiný by číst z fronty a nahrát soubory do úložiště objektů blob. Tento přístup je lepší z hlediska propustnosti a spolehlivosti, ale vyžaduje zřízení a správu fronty. Ještě důležitější je, že je zavedena významná složitost z hlediska **správy stavu** a **koordinace,** pokud chcete udělat něco víc, jako například nahlásit celkový počet nahraných bajtů.

Přístup durable funkce vám všechny uvedené výhody s velmi nízkou režií.

## <a name="the-functions"></a>Funkce

Tento článek vysvětluje následující funkce v ukázkové aplikaci:

* `E2_BackupSiteContent`: [Funkce orchestrator,](durable-functions-bindings.md#orchestration-trigger) která volá `E2_GetFileList` k získání seznamu souborů, které chcete zálohovat, a pak volá `E2_CopyFileToBlob` k zálohování každého souboru.
* `E2_GetFileList`: [Funkce aktivity,](durable-functions-bindings.md#activity-trigger) která vrací seznam souborů v adresáři.
* `E2_CopyFileToBlob`: Funkce aktivity, která zálohuje jeden soubor do úložiště objektů blob Azure.

### <a name="e2_backupsitecontent-orchestrator-function"></a>E2_BackupSiteContent funkce orchestrátoru

Tato funkce orchestrator v podstatě provádí následující:

1. Bere `rootDirectory` hodnotu jako vstupní parametr.
2. Zavolá funkci, která získá rekurzivní seznam `rootDirectory`souborů v části .
3. Provede více volání paralelních funkcí pro nahrání každého souboru do úložiště objektů blob Azure.
4. Čeká na dokončení všech nahrávek.
5. Vrátí celkový součet bajtů, které byly odeslány do úložiště objektů blob Azure.

# <a name="c"></a>[C #](#tab/csharp)

Zde je kód, který implementuje funkci orchestrator:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=16-42)]

Všimněte `await Task.WhenAll(tasks);` si linky. Všechny jednotlivé volání `E2_CopyFileToBlob` funkce *nebyly* očekávány, což jim umožňuje běžet paralelně. Když předáme toto `Task.WhenAll`pole úkolů , získáme zpět úkol, který nebude *dokončen, dokud nebudou dokončeny všechny operace kopírování*. Pokud jste obeznámeni s task parallel library (TPL) v rozhraní .NET, pak to není pro vás nové. Rozdíl je, že tyto úlohy může být spuštěna na více virtuálních počítačů současně a trvalé funkce rozšíření zajišťuje, že spuštění od konce do konce je odolný vůči recyklaci procesů.

Po čekání `Task.WhenAll`od , víme, že všechny volání funkce byly dokončeny a vrátili hodnoty zpět k nám. Každé volání `E2_CopyFileToBlob` vrátí počet nahraných bajtů, takže výpočet celkového počtu bajtů je otázkou sečtení všech těchto vrácených hodnot dohromady.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Funkce používá standardní *function.json* pro funkce orchestrator.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/function.json)]

Zde je kód, který implementuje funkci orchestrator:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/index.js)]

Všimněte `yield context.df.Task.all(tasks);` si linky. Všechny jednotlivé volání `E2_CopyFileToBlob` funkce *nebyly* výnosy, což jim umožňuje spustit paralelně. Když předáme toto `context.df.Task.all`pole úkolů , získáme zpět úkol, který nebude *dokončen, dokud nebudou dokončeny všechny operace kopírování*. Pokud jste obeznámeni [`Promise.all`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all) s v JavaScriptu, pak to není pro vás něco nového. Rozdíl je, že tyto úlohy může být spuštěna na více virtuálních počítačů současně a trvalé funkce rozšíření zajišťuje, že spuštění od konce do konce je odolný vůči recyklaci procesů.

> [!NOTE]
> Přestože jsou úkoly koncepčně podobné slibům `context.df.Task.all` `context.df.Task.any` JavaScriptu, funkce orchestratoru by měly používat a místo paralelizace `Promise.all` úloh a `Promise.race` spravovat je.

Po výnosu `context.df.Task.all`z , víme, že všechny volání funkce byly dokončeny a vrátili hodnoty zpět k nám. Každé volání `E2_CopyFileToBlob` vrátí počet nahraných bajtů, takže výpočet celkového počtu bajtů je otázkou sečtení všech těchto vrácených hodnot dohromady.

---

### <a name="helper-activity-functions"></a>Pomocné funkce aktivity

Pomocné funkce aktivity, stejně jako u jiných `activityTrigger` vzorků, jsou pouze pravidelné funkce, které používají aktivační událost vazby.

#### <a name="e2_getfilelist-activity-function"></a>E2_GetFileList funkce aktivity

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=44-54)]

# <a name="javascript"></a>[Javascript](#tab/javascript)

Soubor *function.json* `E2_GetFileList` vypadá takto:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/function.json)]

A tady je implementace:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/index.js)]

Funkce používá `readdirp` modul (verze 2.x) k rekurzivníčtení adresářové struktury.

---

> [!NOTE]
> Možná se divíte, proč jste nemohli jen dát tento kód přímo do funkce orchestrator. Dalo by se, ale to by porušit jeden ze základních pravidel funkce orchestrator, což je, že by nikdy dělat I / O, včetně přístupu k místnímu systému souborů. Další informace naleznete v [tématu Orchestrator omezení kódu funkce](durable-functions-code-constraints.md).

#### <a name="e2_copyfiletoblob-activity-function"></a>E2_CopyFileToBlob funkce aktivity

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=56-81)]

> [!NOTE]
> Budete muset nainstalovat `Microsoft.Azure.WebJobs.Extensions.Storage` balíček NuGet ke spuštění ukázkového kódu.

Funkce používá některé pokročilé funkce vazby Azure Functions (to znamená použití [ `Binder` parametru),](../functions-dotnet-class-library.md#binding-at-runtime)ale nemusíte se starat o tyto podrobnosti pro účely tohoto návodu.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Soubor *function.json* `E2_CopyFileToBlob` je podobně jednoduchý:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/function.json)]

Implementace JavaScriptu používá [Azure Storage SDK pro uzel](https://github.com/Azure/azure-storage-node) k nahrání souborů do úložiště objektů blob Azure.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/index.js)]

---

Implementace načte soubor z disku a asynchronně streamuje obsah do objektu blob se stejným názvem v kontejneru "zálohy". Vrácená hodnota je počet bajtů zkopírovaných do úložiště, který se pak používá funkce orchestrator k výpočtu agregační součet.

> [!NOTE]
> Toto je dokonalý příklad přesunutí vstupně-va operací do `activityTrigger` funkce. Nejen, že práce může být distribuovány v mnoha různých počítačích, ale také získat výhody kontrolníbody průběhu. Pokud se hostitelský proces z nějakého důvodu ukončí, víte, které nahrávání již bylo dokončeno.

## <a name="run-the-sample"></a>Spuštění ukázky

Orchestraci můžete spustit odesláním následujícího požadavku HTTP POST.

```
POST http://{host}/orchestrators/E2_BackupSiteContent
Content-Type: application/json
Content-Length: 20

"D:\\home\\LogFiles"
```

> [!NOTE]
> Funkce, `HttpStart` kterou se odvoláváte, funguje pouze s obsahem ve formátu JSON. Z tohoto důvodu `Content-Type: application/json` je záhlaví povinné a cesta k adresáři je kódována jako řetězec JSON. Fragment HTTP navíc předpokládá, že v `host.json` souboru je položka, která `api/` odstraní výchozí předponu ze všech adres URL aktivačních událostí HTTP. Značky pro tuto konfiguraci najdete v souboru `host.json` ve vzorcích.

Tento požadavek HTTP `E2_BackupSiteContent` aktivuje orchestrator `D:\home\LogFiles` a předá řetězec jako parametr. Odpověď poskytuje odkaz pro získání stavu operace zálohování:

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

V závislosti na tom, kolik souborů protokolu máte v aplikaci funkce, tato operace může trvat několik minut. Nejnovější stav můžete získat dotazem na `Location` adresu URL v záhlaví předchozí odpovědi HTTP 202.

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

V tomto případě je funkce stále spuštěna. Můžete zobrazit vstup, který byl uložen do stavu orchestrator a poslední aktualizovaný čas. Můžete pokračovat v `Location` použití hodnoty záhlaví k dotazování pro dokončení. Pokud je stav Dokončeno, zobrazí se hodnota odpovědi HTTP podobná následující:

```
HTTP/1.1 200 OK
Content-Length: 152
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"D:\\home\\LogFiles","output":452071,"createdTime":"2019-06-29T18:50:55Z","lastUpdatedTime":"2019-06-29T18:51:26Z"}
```

Nyní můžete vidět, že orchestrace je dokončena a přibližně, kolik času trvalo dokončení. Zobrazí se také hodnota `output` pole, která označuje, že bylo odesláno přibližně 450 kB protokolů.

## <a name="next-steps"></a>Další kroky

Tato ukázka ukázala, jak implementovat fan-out/fan-in vzor. Další ukázka ukazuje, jak implementovat vzor monitoru pomocí [trvalých časovačů](durable-functions-timers.md).

> [!div class="nextstepaction"]
> [Spuštění ukázky monitoru](durable-functions-monitor.md)