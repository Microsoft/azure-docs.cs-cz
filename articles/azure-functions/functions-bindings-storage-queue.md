---
title: Aktivační událost a vazby Azure Queue Storage pro Azure Functions přehled
description: Naučte se používat aktivační událost a výstupní vazbu Azure Queue Storage v Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 2d8f3985bc9a726735ebc0af0a3d3422f4fca54a
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92108860"
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

[core tools]: ./functions-run-local.md
[Sada rozšíření]: ./functions-bindings-register.md#extension-bundles
[Balíček NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[Aktualizace rozšíření]: ./functions-bindings-register.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Aplikace Functions 1. x mají automaticky odkaz na balíček NuGet [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) , verze 2. x.

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="next-steps"></a>Další kroky

- [Spustit funkci jako změny dat úložiště ve frontě (aktivační událost)](./functions-bindings-storage-queue-trigger.md)
- [Zápis zpráv do fronty úložiště (výstupní vazba)](./functions-bindings-storage-queue-output.md)