---
title: Aktivační událost a vazby služby Azure Blob Storage pro Azure Functions
description: Naučte se používat v Azure Functions Trigger a vazby služby Azure Blob Storage.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: 4ec21086ee94610be1d9cf5da7b64c837b5311a9
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100381524"
---
# <a name="azure-blob-storage-bindings-for-azure-functions-overview"></a>Přehled vazeb Azure Blob Storage pro Azure Functions

Azure Functions se integruje s [Azure Storage](../storage/index.yml) prostřednictvím [triggerů a vazeb](./functions-triggers-bindings.md). Integrace s úložištěm objektů BLOB umožňuje vytvářet funkce, které reagují na změny v datech objektů blob, i na hodnoty pro čtení a zápis.

| Akce | Typ |
|---------|---------|
| Spustit funkci jako změny dat v úložišti objektů BLOB | [Trigger](./functions-bindings-storage-blob-trigger.md) |
| Čtení dat služby Blob Storage ve funkci | [Vstupní vazba](./functions-bindings-storage-blob-input.md) |
| Povolení funkce pro zápis dat služby Blob Storage |[Výstupní vazba](./functions-bindings-storage-blob-output.md) |

## <a name="add-to-your-functions-app"></a>Přidat do aplikace Functions

### <a name="functions-2x-and-higher"></a>Functions 2.x a novější

Práce s triggerem a vazbami vyžaduje, abyste odkazovali na příslušný balíček. Balíček NuGet se používá pro knihovny tříd .NET, pokud se sada rozšíření používá pro všechny ostatní typy aplikací.

| Jazyk                                        | Přidat do...                                   | Poznámky 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalace [balíčku NuGet]verze 3. x | |
| Skript C#, Java, JavaScript, Python, PowerShell | Registrace [balíčku rozšíření]          | [Rozšíření Azure Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) se doporučuje používat s Visual Studio Code. |
| Skript jazyka C# (pouze online v Azure Portal)         | Přidání vazby                            | Pokud chcete aktualizovat existující rozšíření vazby bez nutnosti opětovného publikování aplikace Function App, přečtěte si téma [aktualizace rozšíření]. |

#### <a name="storage-extension-5x-and-higher"></a>Rozšíření úložiště 5. x a vyšší

Nová verze rozšíření vazby úložiště je k dispozici jako [balíček NuGet Preview](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/5.0.0-beta.2). Tato verze Preview zavádí možnost [připojit se pomocí identity místo tajného](./functions-reference.md#configure-an-identity-based-connection)kódu. V případě aplikací .NET také změní typy, se kterými se můžete vytvořit, a nahradíte typy z `WindowsAzure.Storage` a `Microsoft.Azure.Storage` novějších typů z [Azure. Storage. BLOBs](/dotnet/api/azure.storage.blobs).

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

## <a name="hostjson-settings"></a>host.jsnastavení

> [!NOTE]
> Tato část se nevztahuje na použití verzí rozšíření starších než 5.0.0. Pro tyto verze neexistují žádná globální nastavení konfigurace pro objekty blob.

Tato část popisuje globální nastavení konfigurace, která jsou k dispozici pro tuto vazbu při použití [rozšíření 5.0.0 a vyšší verze](#storage-extension-5x-and-higher). Příklad *host.jsv* souboru níže obsahuje pouze nastavení verze 2. x + pro tuto vazbu. Další informace o globálních nastaveních konfigurace ve funkcích verze 2. x a novějších naleznete v tématu [host.jsv referenčních informacích pro Azure Functions](functions-host-json.md).

```json
{
    "version": "2.0",
    "extensions": {
        "blobs": {
            "maxDegreeOfParallelism": "4"
        }
    }
}
```

|Vlastnost  |Výchozí | Description |
|---------|---------|---------|
|Z MaxDegreeOfParallelism|8 * (počet dostupných jader)|Celočíselný počet souběžných volání povolených pro jednotlivé funkce aktivované objektem BLOB. Minimální povolená hodnota je 1.|

## <a name="next-steps"></a>Další kroky

- [Spustit funkci při změně dat služby Blob Storage](./functions-bindings-storage-blob-trigger.md)
- [Při spuštění funkce číst data služby Blob Storage](./functions-bindings-storage-blob-input.md)
- [Zápis dat služby Blob Storage z funkce](./functions-bindings-storage-blob-output.md)
