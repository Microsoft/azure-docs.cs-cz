---
title: FAN odesílacího/fan v scénáře v trvanlivý funkce – Azure
description: Zjistěte, jak implementovat scénáři fan-odesílacího ventilátor v v rozšíření trvanlivý funkce pro Azure Functions.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/19/2018
ms.author: azfuncdf
ms.openlocfilehash: 35877831c7f63c20fee2f2bc3838e73bb98328c0
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="fan-outfan-in-scenario-in-durable-functions---cloud-backup-example"></a>FAN odesílacího/fan v scénář v trvanlivý funkce – příklad zálohování cloudu

*FAN odesílacího/fan v* odkazuje na vzor souběžně provádění víc funkcí a následnému provedením některé agregace na výsledky. Tento článek vysvětluje vzorku, který používá [trvanlivý funkce](durable-functions-overview.md) k implementaci fan v nebo fan odesílacího scénáře. Ukázka je trvanlivý funkce, která Zálohuje všechny nebo některé z obsahu webu aplikace do Azure Storage.

## <a name="prerequisites"></a>Požadavky

* [Nainstalujte trvanlivý funkce](durable-functions-install.md).
* Dokončení [Hello pořadí](durable-functions-sequence.md) návod.

## <a name="scenario-overview"></a>Přehled scénáře

V této ukázce funkce nahrát všechny soubory v zadané directory rekurzivně do úložiště objektů blob. Také se počet celkový počet bajtů, které byly odeslány.

Je možné vytvořit jednu funkci, která má na starosti vše. Hlavní problém, spustili byste do **škálovatelnost**. Spuštění jedné funkce lze spustit jen u jeden virtuální počítač, aby propustnost bude omezeno propustnost tohoto jednoho virtuálního počítače. Dalším problémem je **spolehlivost**. Pokud je selhání polovině prostřednictvím nebo celý proces trvá déle než 5 minut, zálohování se možná nepovede ve stavu částečně dokončilo. Potom by ji muset restartovat.

Robustnější přístup by bylo zápisu dvě běžné funkce: jeden by výčet souborů a názvy souborů přidejte do fronty, a jinou by číst zprávy z fronty a odesílat soubory do úložiště objektů blob. Toto je lepší z hlediska propustnost a spolehlivost, ale vyžaduje, abyste zřizovat a spravovat frontu. Je důležité, je důležité složitost zavedená z hlediska **stavu správy** a **koordinaci** Pokud chcete udělat něco víc, jako je sestava celkový počet bajtů nahrát.

Trvanlivý funkce přístup získáte všechny výhody uvedených s velmi nízké režijní náklady.

## <a name="the-functions"></a>Funkce

Tento článek vysvětluje v ukázkové aplikace následující funkce:

* `E2_BackupSiteContent`
* `E2_GetFileList`
* `E2_CopyFileToBlob`

Následující části popisují konfiguraci a kódu, které se používají pro C# skriptování. Kód pro vývoj v sadě Visual Studio se zobrazí na konci tohoto článku.

## <a name="the-cloud-backup-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>Zálohování orchestration cloudu (portálu ukázkový kód pro Visual Studio Code a Azure)

`E2_BackupSiteContent` Funkce používá standardní *function.json* pro orchestrator funkce.

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_BackupSiteContent/function.json)]

Tady je kód, který implementuje funkce orchestrator:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_BackupSiteContent/run.csx)]

Tato funkce orchestrator v podstatě provede následující akce:

1. Přijímá `rootDirectory` hodnotu jako vstupní parametr.
2. Volání funkce, která se získat seznam souborů v rámci rekurzivní `rootDirectory`.
3. Volá více paralelní funkce nahrání každého souboru, do Azure Blob Storage.
4. Čeká se na všechny nahrávání k dokončení.
5. Vrátí součet celkový počet bajtů, které byly odeslány do úložiště objektů Blob Azure.

Upozornění `await Task.WhenAll(tasks);` řádku. Všechna volání do `E2_CopyFileToBlob` funkce byly *není* očekáváno. To je úmyslné umožnit, aby se spouštěly paralelně. Když jsme předat tuto řadu úloh, které se `Task.WhenAll`, se nám získat zpět úlohu, která se nedokončí *dokud byly dokončeny všechny operace kopírování*. Pokud jste obeznámeni s Task Parallel Library (TPL) v rozhraní .NET, není to pro vás nový. Rozdíl je, že tyto úlohy mohou běžet na víc virtuálních počítačů současně, a rozšíření trvanlivý funkce zajišťuje, že provádění začátku do konce odolné vůči recyklace procesu.

Po čeká na z `Task.WhenAll`, víme, že všechna volání funkce dokončili a aby vrátil hodnoty zpět do us. Každé volání `E2_CopyFileToBlob` vrátí počet bajtů nahráli, takže výpočet počet bajtů celkový součet je řádu přidání všechny ty společně návratové hodnoty.

## <a name="helper-activity-functions"></a>Podpůrné funkce aktivity

Podpůrné funkce aktivitu, stejně jako u jiných ukázky, jsou právě běžné funkce, které používají `activityTrigger` aktivovat vazby. Například *function.json* souboru `E2_GetFileList` vypadá podobně jako následující:

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_GetFileList/function.json)]

A tady je implementace:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_GetFileList/run.csx)]

> [!NOTE]
> Možná se ptáte, proč nelze stačí vložit tento kód přímo do funkce produktu orchestrator. Vám může, ale to by rozdělit jednu ze základních pravidel orchestrator funkcí, které je, že by měly nikdy dělat vstupně-výstupní operace, včetně místního systému souborů.

*Function.json* souboru `E2_CopyFileToBlob` podobně jednoduché:

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_CopyFileToBlob/function.json)]

Implementace je také poměrně jednoduché. Se stane, používat některé pokročilé funkce vazeb Azure Functions (to znamená, použití `Binder` parametr), ale nemusíte si dělat starosti o tyto podrobnosti pro účely tohoto návodu.

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_CopyFileToBlob/run.csx)]

Implementace načte soubor z disku a asynchronně datové proudy obsah do objektu blob se stejným názvem v kontejneru "zálohování". Vrácená hodnota je počet bajtů, které jsou zkopírovány do úložiště, pak používány orchestrator funkce pro výpočet agregační součet.

> [!NOTE]
> To je ideální příklad přesunu vstupně-výstupních operací do `activityTrigger` funkce. Pouze mohou práce být distribuovány na mnoha různých virtuálních počítačů, ale můžete také získat výhody vytváření kontrolních bodů průběhu. Pokud hostitelský proces získá ukončeno z jakéhokoli důvodu, víte, které nahrávání už byl dokončený.

## <a name="run-the-sample"></a>Spuštění ukázky

Orchestration můžete spustit odesláním následující požadavku HTTP POST.

```
POST http://{host}/orchestrators/E2_BackupSiteContent
Content-Type: application/json
Content-Length: 20

"D:\\home\\LogFiles"
```

> [!NOTE]
> `HttpStart` Funkce, která je vyvolán lze použít pouze se obsah ve formátu JSON. Z tohoto důvodu `Content-Type: application/json` záhlaví je povinná a cesta k adresáři je kódovaná jako řetězec formátu JSON.

Požadavek na tento HTTP aktivační události `E2_BackupSiteContent` orchestrator a předá řetězec `D:\home\LogFiles` jako parametr. Odpověď obsahuje odkaz na načíst stav operace zálohování:

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

V závislosti na tom, kolik souborů protokolu máte ve vaší aplikaci funkce tato operace může trvat několik minut na dokončení. Poslední stav můžete získat pomocí dotazu na adresu URL v `Location` hlavičky předchozí HTTP 202 odpovědi.

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

V takovém případě je stále spuštěna funkce. Budete moci zobrazit vstup, která byla uložena do stavu orchestrator a poslední čas poslední aktualizace. Můžete dál používat `Location` hodnoty hlavičky k dotazování na dokončení. Pokud stav je "dokončeno", uvidíte hodnota odpovědi HTTP podobný následujícímu:

```
HTTP/1.1 200 OK
Content-Length: 152
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"D:\\home\\LogFiles","output":452071,"createdTime":"2017-06-29T18:50:55Z","lastUpdatedTime":"2017-06-29T18:51:26Z"}
```

Nyní uvidíte, že orchestration je dokončena a přibližně kolik času ho trvalo dokončení. Zobrazí hodnotu `output` pole, které označuje, zda byly nahrán přibližně 450 KB protokolů.

## <a name="visual-studio-sample-code"></a>Visual Studio ukázkový kód

Tady je orchestration jako jeden soubor jazyka C# v projektu sady Visual Studio:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs)]

## <a name="next-steps"></a>Další postup

Tato ukázka ukazuje, jak implementovat fan odesílacího/fan v vzor. Další příklad ukazuje, jak implementovat pomocí vzoru monitorování [trvanlivý časovače](durable-functions-timers.md).

> [!div class="nextstepaction"]
> [Spustit ukázku monitorování](durable-functions-monitor.md)
