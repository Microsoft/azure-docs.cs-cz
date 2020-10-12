---
title: Aktivační událost a vazby služby Azure Blob Storage pro Azure Functions
description: Naučte se používat v Azure Functions Trigger a vazby služby Azure Blob Storage.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: eead2d6ae2860bf06f1b878c1bb1692d74c06df6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87055977"
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
