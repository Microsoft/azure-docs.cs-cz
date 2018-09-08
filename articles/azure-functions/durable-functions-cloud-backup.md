---
title: FAN odesílací/fan v scénáře v Durable Functions – Azure
description: Zjistěte, jak implementovat scénář fan-odesílací ventilátor – se změnami v rozšíření Durable Functions pro službu Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: azfuncdf
ms.openlocfilehash: eec75ad9cf0f568e674b2a4f12d962982f84294f
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44092661"
---
# <a name="fan-outfan-in-scenario-in-durable-functions---cloud-backup-example"></a>FAN odesílací/fan v scénář v Durable Functions – cloudové zálohování příklad

*FAN odesílací/fan v* odkazuje na vzorec, podle kterého současně provádí více funkcí a pak provádí některé agregace na výsledky. Tento článek vysvětluje, ukázky, která používá [Durable Functions](durable-functions-overview.md) implementovat fan v/fan odesílací scénář. Vzorek je odolné funkce, která Zálohuje všechny nebo některé z vaší aplikace obsah webu do služby Azure Storage.

## <a name="prerequisites"></a>Požadavky

* [Nainstalujte Durable Functions](durable-functions-install.md).
* Dokončení [Hello pořadí](durable-functions-sequence.md) návodu.

## <a name="scenario-overview"></a>Přehled scénáře

Funkce v této ukázce nahrát všechny soubory v zadaném adresáři rekurzivně do úložiště objektů blob. Se berou také celkový počet bajtů, které byly odeslány.

Je možné napsat jednu funkci, která se postará o všechno. Hlavní problém by měli je **škálovatelnost**. Spuštění jedné funkce dají spustit jenom na jeden virtuální počítač, aby propustnost zpracovat, omezeno propustnost tohoto jednoho virtuálního počítače. Dalším problémem je **spolehlivost**. Dojde-li k selhání polovině nebo celý proces trvá déle než 5 minut, by mohlo selhat zálohování ve stavu částečně dokončilo. Pak bude nutné restartovat.

Robustnější přístup může být zápis dvě běžné funkce: jeden by výčet souborů a názvy souborů přidejte do fronty a jiné by číst zprávy z fronty a nahrání souborů do úložiště objektů blob. Toto je lepší propustnost a spolehlivost, ale vyžaduje, abyste zřizovat a spravovat frontu. Důležitější je, je zavedená významné složitost z hlediska **správu stavu** a **koordinace** Pokud budete chtít provádět žádnou další, stejně jako sestavy celkový počet bajtů nahrát.

Odolná služba Functions přístup poskytuje všechny výhody jsme už zmínili, s velmi nízkou režií.

## <a name="the-functions"></a>Funkce

Tento článek vysvětluje následující funkce v ukázkové aplikaci:

* `E2_BackupSiteContent`
* `E2_GetFileList`
* `E2_CopyFileToBlob`

Následující části popisují konfiguraci a kód, který se používají pro C# skriptování. Kód pro vývoj sady Visual Studio se zobrazí na konci tohoto článku.

## <a name="the-cloud-backup-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>Zálohování Orchestrace cloudu (Visual Studio Code a Azure portal ukázkový kód)

`E2_BackupSiteContent` Funkce používá standardní *function.json* pro funkce nástroje orchestrator.

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_BackupSiteContent/function.json)]

Tady je kód, který implementuje funkce orchestrátoru:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_BackupSiteContent/run.csx)]

### <a name="javascript-functions-v2-only"></a>JavaScript (jenom funkce v2)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/index.js)]

Tato funkce nástroje orchestrator v podstatě provede následující akce:

1. Přijímá `rootDirectory` hodnotu jako vstupní parametr.
2. Volá funkci k získání seznamu souborů v rámci rekurzivní `rootDirectory`.
3. Volá více paralelních funkce kvůli nahrání každého souboru do úložiště objektů Blob v Azure.
4. Čeká na dokončení všech nahrávání.
5. Vrátí součet celkový počet bajtů, které byly odeslány do Azure Blob Storage.

Všimněte si, že `await Task.WhenAll(tasks);` (C#) a `yield context.df.Task.all(tasks);` řádku (JS). Všechna volání `E2_CopyFileToBlob` funkce byly *není* očekáváno. Je to záměr, aby se mohly běžet paralelně. Když jsme předat toto pole úloh určených k `Task.WhenAll`, jsme vrátit úlohu, která se nikdy nedokončí *až do dokončení všech operací kopírování*. Pokud jste obeznámeni s Task Parallel Library (TPL) v rozhraní .NET, není to pro vás nová. Rozdíl je, že tyto úlohy mohou běžet na několika virtuálních počítačů současně, a rozšíření Durable Functions se zajistí, že začátku do konce provádění odolné vůči recyklace procesů.

Úlohy jsou velmi podobný koncept JavaScript příslibů. Ale `Promise.all` má několik rozdílů z `Task.WhenAll`. Koncept `Task.WhenAll` se přenáší přes jako součást `durable-functions` modul JavaScript a je určena výhradně pro ho.

Po čekání na z `Task.WhenAll` (nebo získávání z `context.df.Task.all`), víme, že všechna volání funkce dokončili a vracet hodnoty zpět na nás. Každé volání `E2_CopyFileToBlob` vrátí počet bajtů nahráli, výpočet součtu počet celkový počet bajtů je otázkou přidání všech projektů společně návratové hodnoty.

## <a name="helper-activity-functions"></a>Pomocná funkce aktivity

Aktivita funkce pomocné rutiny, stejně jako u jiných ukázky jsou pouze běžné funkce, které používají `activityTrigger` aktivovat vazby. Například *function.json* souboru `E2_GetFileList` vypadá následovně:

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_GetFileList/function.json)]

A tady je implementace:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_GetFileList/run.csx)]

### <a name="javascript-functions-v2-only"></a>JavaScript (jenom funkce v2)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/index.js)]

Javascriptovou implementaci `E2_GetFileList` používá `readdirp` modulu rekurzivně čtení struktury adresářů.

> [!NOTE]
> Možná se ptáte, proč nelze stačí vložit tento kód přímo do funkce orchestrátoru. Vám může, ale to by narušil jednu ze základních pravidel funkcí nástroje orchestrator, což je, že byste nikdy dělají vstupně-výstupních operací, včetně přístupu k místním souborům systému.

*Function.json* souboru `E2_CopyFileToBlob` je podobně jednoduchý:

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_CopyFileToBlob/function.json)]

Implementace jazyka C# je také poměrně jednoduchý. To se stane, že některé pokročilé funkce Azure Functions vazby (to znamená použití `Binder` parametr), ale není nutné se starat o podrobnosti pro účely tohoto návodu.

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_CopyFileToBlob/run.csx)]

### <a name="javascript-functions-v2-only"></a>JavaScript (jenom funkce v2)

Javascriptovou implementaci nemá přístup k `Binder` funkce Azure Functions, takže [sadu SDK služby Azure Storage pro uzel](https://github.com/Azure/azure-storage-node) jeho probíhá. Všimněte si, že sada SDK vyžaduje `AZURE_STORAGE_CONNECTION_STRING` nastavení aplikace.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/index.js)]

Implementace načte soubor z disku a asynchronně streamování obsahu do objektu blob se stejným názvem v kontejneru "zálohování". Vrácená hodnota je počet bajtů zkopírovat do úložiště, který se potom využijí funkce orchestrátoru vypočítat agregace sum.

> [!NOTE]
> To je ideální příkladem přesun vstupně-výstupních operací do `activityTrigger` funkce. Nejenže je práce možné distribuovat napříč mnoha různých virtuálních počítačů, ale získáte také výhody vytváření kontrolních bodů průběhu. Pokud hostitelský proces získá z jakéhokoli důvodu, víte, které nahrávání se dokončila.

## <a name="run-the-sample"></a>Spuštění ukázky

Orchestraci můžete spustit odesláním následujících požadavku HTTP POST.

```
POST http://{host}/orchestrators/E2_BackupSiteContent
Content-Type: application/json
Content-Length: 20

"D:\\home\\LogFiles"
```

> [!NOTE]
> `HttpStart` Funkce, která jsou volání funguje jenom s obsahem ve formátu JSON. Z tohoto důvodu `Content-Type: application/json` vyžaduje se hlavička a cesta k adresáři je zakódovaný jako řetězec formátu JSON.

Požadavek na tomto HTTP aktivační události `E2_BackupSiteContent` orchestrator a předává řetězec `D:\home\LogFiles` jako parametr. Odpověď obsahuje odkaz na načíst stav operace zálohování:

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

V závislosti na tom, kolik soubory protokolu budete mít ve své aplikaci function app tato operace může trvat několik minut. Můžete získat nejnovější stav pomocí adresy URL v dotazu `Location` hlavičky pro předchozí odpověď HTTP 202.

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

V takovém případě funkce stále běží. Budete moct zobrazit vstup, který byl uložen do produktu orchestrator stavu a čas poslední aktualizace. Můžete dál používat `Location` hodnoty hlavičky k dotazování na dokončení. Stav "dokončení", zobrazí hodnota odpovědi HTTP podobný následujícímu:

```
HTTP/1.1 200 OK
Content-Length: 152
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"D:\\home\\LogFiles","output":452071,"createdTime":"2017-06-29T18:50:55Z","lastUpdatedTime":"2017-06-29T18:51:26Z"}
```

Nyní uvidíte, že dokončení orchestraci a přibližně kolik času je potřeba k dokončení. Zobrazí také hodnotu `output` pole, což znamená, že přibližně 450 KB protokoly nahraný.

## <a name="visual-studio-sample-code"></a>Visual Studio ukázkový kód

Tady je Orchestrace jako jeden soubor jazyka C# v sadě Visual Studio projekt:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs)]

## <a name="next-steps"></a>Další postup

Tato ukázka ukazuje, jak implementovat fan odesílací/fan v modelu. Další příklad ukazuje, jak implementovat vzor na monitorování pomocí [trvalý časovače](durable-functions-timers.md).

> [!div class="nextstepaction"]
> [Spusťte ukázku monitorování](durable-functions-monitor.md)
