---
title: Vazby Azure Cosmos DB pro funkce 2.x
description: Zjistěte, jak používat aktivační události a vazby Azure Cosmos DB ve funkcích Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2017
ms.author: cshoe
ms.openlocfilehash: f258a7aff52796a53540706bc8413575d63c9e7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77605759"
---
# <a name="azure-cosmos-db-trigger-and-bindings-for-azure-functions-2x-overview"></a>Azure Cosmos DB aktivační události a vazby pro Azure Functions 2.x přehled

> [!div class="op_single_selector" title1="Vyberte verzi runtime Azure Functions, kterou používáte: "]
> * [Verze 1](functions-bindings-cosmosdb.md)
> * [Verze 2](functions-bindings-cosmosdb-v2.md)

Tato sada článků vysvětluje, jak pracovat s vazby [Azure Cosmos DB](../cosmos-db/serverless-computing-database.md) v Azure Functions 2.x. Funkce Azure podporuje aktivační, vstupní a výstupní vazby pro Azure Cosmos DB.

| Akce | Typ |
|---------|---------|
| Spuštění funkce při vytvoření nebo úpravě dokumentu Azure Cosmos DB | [Trigger](./functions-bindings-cosmosdb-v2-trigger.md) |
| Čtení dokumentu Azure Cosmos DB | [Vstupní vazba](./functions-bindings-cosmosdb-v2-input.md) |
| Uložení změn do dokumentu Azure Cosmos DB  |[Výstupní vazba](./functions-bindings-cosmosdb-v2-output.md) |

> [!NOTE]
> Tento odkaz je pro [Funkce Azure verze 2.x](functions-versions.md).  Informace o tom, jak používat tyto vazby ve funkcích 1.x, najdete v [tématu Vazby Azure Cosmos DB pro Funkce Azure 1.x](functions-bindings-cosmosdb.md).
>
> Tato vazba byla původně pojmenována DocumentDB. Ve functions verze 2.x se aktivační událost, vazby a balíček nazývají Cosmos DB.

## <a name="supported-apis"></a>Podporovaná přístupová řešení API

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="add-to-your-functions-app"></a>Přidání do aplikace Funkce

### <a name="functions-2x-and-higher"></a>Funkce 2.x a vyšší

Práce s aktivační událost a vazby vyžaduje, abyste odkazovat na příslušný balíček. Balíček NuGet se používá pro knihovny tříd .NET, zatímco rozšíření svazku se používá pro všechny ostatní typy aplikací.

| Jazyk                                        | Přidat podle...                                   | Poznámky 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalace [balíčku NuGet], verze 3.x | |
| C# Script, Java, JavaScript, Python, PowerShell | Registrace [rozšíření balíčku]          | Rozšíření [Nástroje Azure] se doporučuje používat s Visual Studio Code. |
| Skript C# (jenom online na webu Azure Portal)         | Přidání vazby                            | Pokud chcete aktualizovat existující rozšíření o vazby, aniž byste museli znovu publikovat aplikaci funkcí, přečtěte si informace [o aktualizaci rozšíření]. |

[Balíček NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB
[core tools]: ./functions-run-local.md
[rozšiřující balíček]: ./functions-bindings-register.md#extension-bundles
[Aktualizace rozšíření]: ./install-update-binding-extensions-manual.md
[Rozšíření nástrojů Azure]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Aplikace functions 1.x mají automaticky odkaz na balíček [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet verze 2.x.

## <a name="next-steps"></a>Další kroky

- [Spuštění funkce při vytvoření nebo úpravě dokumentu Azure Cosmos DB (aktivační událost)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Čtení dokumentu Azure Cosmos DB (vstupní vazba)](./functions-bindings-cosmosdb-v2-input.md)
- [Uložení změn do dokumentu Azure Cosmos DB (výstupní vazba)](./functions-bindings-cosmosdb-v2-output.md)
