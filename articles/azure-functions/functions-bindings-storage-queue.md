---
title: Aktivační událost a vazby Azure Queue Storage pro Azure Functions přehled
description: Naučte se používat aktivační událost a výstupní vazbu Azure Queue Storage v Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: a1b9d03da29b7c89055303fa97fc38c2ef734b23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100381473"
---
# <a name="azure-queue-storage-trigger-and-bindings-for-azure-functions-overview"></a>Aktivační událost a vazby Azure Queue Storage pro Azure Functions přehled

Azure Functions může běžet, protože se vytvoří nové zprávy ve frontě Azure Queue Storage a můžou ve funkci zapisovat zprávy ve frontě.

| Akce | Typ |
|---------|---------|
| Spustit funkci jako změny dat v úložišti front | [Trigger](./functions-bindings-storage-queue-trigger.md) |
| Zapsat zprávy ve frontě úložiště |[Výstupní vazba](./functions-bindings-storage-queue-output.md) |

## <a name="add-to-your-functions-app"></a>Přidat do aplikace Functions

### <a name="functions-2x-and-higher"></a>Functions 2.x a novější

Práce s triggerem a vazbami vyžaduje, abyste odkazovali na příslušný balíček. Balíček NuGet se používá pro knihovny tříd .NET, pokud se sada rozšíření používá pro všechny ostatní typy aplikací.

| Jazyk                                        | Přidat do...                                   | Poznámky 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalace [balíčku NuGet]verze 3. x | |
| Skript C#, Java, JavaScript, Python, PowerShell | Registrace [balíčku rozšíření]          | [Rozšíření Azure Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) se doporučuje používat s Visual Studio Code. |
| Skript jazyka C# (pouze online v Azure Portal)         | Přidání vazby                            | Pokud chcete aktualizovat existující rozšíření vazby bez nutnosti opětovného publikování aplikace Function App, přečtěte si téma [aktualizace rozšíření]. |

#### <a name="storage-extension-5x-and-higher"></a>Rozšíření úložiště 5. x a vyšší

Nová verze rozšíření vazby úložiště je k dispozici jako [balíček NuGet Preview](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/5.0.0-beta.2). Tato verze Preview zavádí možnost [připojit se pomocí identity místo tajného](./functions-reference.md#configure-an-identity-based-connection)kódu. V případě aplikací .NET také změní typy, se kterými se můžete vytvořit, a nahradíte typy z `WindowsAzure.Storage` a `Microsoft.Azure.Storage` novějších typů z [Azure. Storage. Queues](/dotnet/api/azure.storage.queues).

> [!NOTE]
> Balíček verze Preview není zahrnutý v rozšiřující sadě a musí se nainstalovat ručně. V případě aplikací .NET přidejte do balíčku odkaz. Všechny ostatní typy aplikací najdete v tématu [aktualizace rozšíření].

[core tools]: ./functions-run-local.md
[Sada rozšíření]: ./functions-bindings-register.md#extension-bundles
[Balíček NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[Aktualizace rozšíření]: ./functions-bindings-register.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Aplikace Functions 1. x mají automaticky odkaz na balíček NuGet [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) , verze 2. x.

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.jsnastavení

Tato část popisuje globální nastavení konfigurace, která jsou k dispozici pro tuto vazbu ve verzích 2. x a vyšší. Příklad *host.jsv* souboru níže obsahuje pouze nastavení verze 2. x + pro tuto vazbu. Další informace o globálních nastaveních konfigurace ve verzích 2. x a novějších naleznete v tématu [host.jsv referenčních informacích pro Azure Functions](functions-host-json.md).

> [!NOTE]
> Odkaz na host.jspro ve funkcích 1. x naleznete v tématu [host.json reference for Azure Functions 1. x](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "queues": {
            "maxPollingInterval": "00:00:02",
            "visibilityTimeout" : "00:00:30",
            "batchSize": 16,
            "maxDequeueCount": 5,
            "newBatchThreshold": 8,
            "messageEncoding": "base64"
        }
    }
}
```

|Vlastnost  |Výchozí | Description |
|---------|---------|---------|
|maxPollingInterval|00:00:01|Maximální interval mezi cykly dotazování fronty. Minimum je 00:00:00.100 (100 ms) a zvýší až 00:01:00 (1 min).  Ve funkcích 2. x a vyšší je datový typ `TimeSpan` , zatímco ve verzi 1. x je v milisekundách.|
|visibilityTimeout|00:00:00|Časový interval mezi opakovanými pokusy při zpracování zprávy se nezdařil. |
|batchSize|16|Počet zpráv ve frontě, které funkce runtime Functions načítá současně a procesy paralelně. Když se zpracovávané číslo vrátí do `newBatchThreshold` , modul runtime získá další dávku a začne zpracovávat tyto zprávy. Proto je navíc maximální počet souběžných zpráv zpracovávaných na funkci `batchSize` plus `newBatchThreshold` . Toto omezení se vztahuje odděleně na jednotlivé funkce aktivované frontou. <br><br>Pokud se chcete vyhnout paralelnímu provádění zpráv přijatých v jedné frontě, můžete nastavit `batchSize` na hodnotu 1. Toto nastavení však eliminuje souběžnost, pokud vaše aplikace Function App běží pouze na jednom virtuálním počítači (VM). Pokud se aplikace funkcí škáluje na více virtuálních počítačů, každý virtuální počítač může spustit jednu instanci každé funkce aktivované frontou.<br><br>Maximální `batchSize` hodnota je 32. |
|maxDequeueCount|5|Počet pokusů o zpracování zprávy před jejich přesunutím do nepoškozené fronty.|
|newBatchThreshold|batchSize/2|Pokaždé, když se počet zpracovávaných zpráv souběžně vrátí k tomuto číslu, modul runtime načte další dávku.|
|messageEncoding|base64| Toto nastavení je dostupné jenom ve [verzi rozšíření 5.0.0 a vyšší](#storage-extension-5x-and-higher). Představuje formát kódování pro zprávy. Platné hodnoty jsou `base64` a `none` .|

## <a name="next-steps"></a>Další kroky

- [Spustit funkci jako změny dat úložiště ve frontě (aktivační událost)](./functions-bindings-storage-queue-trigger.md)
- [Zápis zpráv do fronty úložiště (výstupní vazba)](./functions-bindings-storage-queue-output.md)
