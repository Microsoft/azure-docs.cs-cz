---
title: Azure Queue storage trigger and bindings for Azure Functions overview Azure Queue storage trigger and bindings for Azure Functions overview Azure Queue storage trigger and bindings for Azure Functions overview Azure Queue
description: Zjistěte, jak používat aktivační událost úložiště fronty Azure a výstupní vazbu ve funkcích Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 71321fa631bafde5b1dbaac2d165cb68fd8116d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277307"
---
# <a name="azure-queue-storage-trigger-and-bindings-for-azure-functions-overview"></a>Azure Queue storage trigger and bindings for Azure Functions overview Azure Queue storage trigger and bindings for Azure Functions overview Azure Queue storage trigger and bindings for Azure Functions overview Azure Queue

Azure Functions můžete spustit jako nové zprávy úložiště fronty Azure jsou vytvořeny a můžete psát zprávy fronty v rámci funkce.

| Akce | Typ |
|---------|---------|
| Spuštění funkce jako změny dat úložiště fronty | [Trigger](./functions-bindings-storage-queue-trigger.md) |
| Zápis zpráv o úložišti fronty |[Výstupní vazba](./functions-bindings-storage-queue-output.md) |

## <a name="add-to-your-functions-app"></a>Přidání do aplikace Funkce

### <a name="functions-2x-and-higher"></a>Funkce 2.x a vyšší

Práce s aktivační událost a vazby vyžaduje, abyste odkazovat na příslušný balíček. Balíček NuGet se používá pro knihovny tříd .NET, zatímco rozšíření svazku se používá pro všechny ostatní typy aplikací.

| Jazyk                                        | Přidat podle...                                   | Poznámky 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalace [balíčku NuGet], verze 3.x | |
| C# Script, Java, JavaScript, Python, PowerShell | Registrace [rozšíření balíčku]          | Rozšíření [Nástroje Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) se doporučuje používat s Visual Studio Code. |
| Skript C# (jenom online na webu Azure Portal)         | Přidání vazby                            | Pokud chcete aktualizovat existující rozšíření o vazby, aniž byste museli znovu publikovat aplikaci funkcí, přečtěte si informace [o aktualizaci rozšíření]. |

[core tools]: ./functions-run-local.md
[rozšiřující balíček]: ./functions-bindings-register.md#extension-bundles
[Balíček NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[Aktualizace rozšíření]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Aplikace functions 1.x mají automaticky odkaz na balíček [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet verze 2.x.

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="next-steps"></a>Další kroky

- [Spuštění funkce jako změny dat úložiště fronty (Trigger)](./functions-bindings-storage-queue-trigger.md)
- [Zápis zpráv úložiště fronty (výstupní vazba)](./functions-bindings-storage-queue-output.md)
