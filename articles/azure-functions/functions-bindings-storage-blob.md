---
title: Aktivační událost a vazby služby Azure Blob Storage pro Azure Functions
description: Naučte se používat v Azure Functions Trigger a vazby služby Azure Blob Storage.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: 794e18590f554d1537449d2aee9451c0053ac4a7
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198411"
---
# <a name="azure-blob-storage-bindings-for-azure-functions-overview"></a>Přehled vazeb Azure Blob Storage pro Azure Functions

Azure Functions se integruje s [Azure Storage](https://docs.microsoft.com/azure/storage/) prostřednictvím [triggerů a vazeb](./functions-triggers-bindings.md). Integrace s úložištěm objektů BLOB umožňuje vytvářet funkce, které reagují na změny v datech objektů blob, i na hodnoty pro čtení a zápis.

| Akce | Typ |
|---------|---------|
| Spustit funkci jako změny dat v úložišti objektů BLOB | [Trigger](./functions-bindings-storage-blob-trigger.md) |
| Čtení dat služby Blob Storage ve funkci | [Vstupní vazba](./functions-bindings-storage-blob-input.md) |
| Povolení funkce pro zápis dat služby Blob Storage |[Výstupní vazba](./functions-bindings-storage-blob-output.md) |

## <a name="add-to-your-functions-app"></a>Přidat do aplikace Functions

### <a name="functions-2x-and-higher"></a>Functions 2. x a vyšší

Práce s triggerem a vazbami vyžaduje, abyste odkazovali na příslušný balíček. Balíček NuGet se používá pro knihovny tříd .NET, zatímco rozšíření všechny ostatní typy aplikací.

| Jazyk                                        | Přidat do...                                   | Poznámky 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalace [Balíček NuGet]verze 3. x | |
| C#Skript, Java, JavaScript, Python, PowerShell | Registrace [Sada rozšíření]          | [Rozšíření Azure Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) se doporučuje používat s Visual Studio Code. |
| C#Skript (online – pouze v Azure Portal)         | Přidání vazby                            | Pokud chcete aktualizovat existující rozšíření vazby bez nutnosti opětovného publikování aplikace Function App, přečtěte si téma [aktualizace rozšíření]. |

[core tools]: ./functions-run-local.md
[Sada rozšíření]: ./functions-bindings-register.md#extension-bundles
[Balíček NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[Aktualizace rozšíření]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Aplikace Functions 1. x mají automaticky odkaz na balíček NuGet [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) , verze 2. x.

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="next-steps"></a>Další kroky

- [Spustit funkci při změně dat služby Blob Storage](./functions-bindings-storage-blob-trigger.md)
- [Při spuštění funkce číst data služby Blob Storage](./functions-bindings-storage-blob-input.md)
- [Zápis dat služby Blob Storage z funkce](./functions-bindings-storage-blob-output.md)
