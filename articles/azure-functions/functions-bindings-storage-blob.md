---
title: Aktivační událost úložiště objektů blob Azure a vazby pro funkce Azure
description: Naučte se používat aktivační událost úložiště objektů blob Azure a vazby v Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: 6950f1dd81ef2c70f2c45fb4c547ed7676067790
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277229"
---
# <a name="azure-blob-storage-bindings-for-azure-functions-overview"></a>Azure Blob storage vazby pro Azure Functions přehled

Azure Functions se integruje s [Azure Storage](https://docs.microsoft.com/azure/storage/) prostřednictvím [aktivačních událostí a vazeb](./functions-triggers-bindings.md). Integrace s úložištěm objektů blob umožňuje vytvářet funkce, které reagují na změny v datech objektů blob, stejně jako čtení a zápis hodnoty.

| Akce | Typ |
|---------|---------|
| Spuštění funkce při změnách dat úložiště objektů blob | [Trigger](./functions-bindings-storage-blob-trigger.md) |
| Čtení dat úložiště objektů blob ve funkci | [Vstupní vazba](./functions-bindings-storage-blob-input.md) |
| Povolení zápisu dat úložiště objektů blob |[Výstupní vazba](./functions-bindings-storage-blob-output.md) |

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

- [Spuštění funkce při změně dat úložiště objektů blob](./functions-bindings-storage-blob-trigger.md)
- [Čtení dat úložiště objektů blob při spuštění funkce](./functions-bindings-storage-blob-input.md)
- [Zápis dat úložiště objektů blob z funkce](./functions-bindings-storage-blob-output.md)
