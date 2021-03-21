---
title: Vazby Azure Cosmos DB pro Functions 2. xD a vyšší
description: Naučte se používat Azure Cosmos DB triggery a vazby v Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2017
ms.author: cshoe
ms.openlocfilehash: dde3b95c7997a7f742a9d48a964f4275169f6e9c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92106922"
---
# <a name="azure-cosmos-db-trigger-and-bindings-for-azure-functions-2x-and-higher-overview"></a>Azure Cosmos DB Trigger a vazby pro Azure Functions 2. x a vyšší

> [!div class="op_single_selector" title1="Vyberte verzi Azure Functions runtime, kterou používáte: "]
> * [Verze 1](functions-bindings-cosmosdb.md)
> * [Verze 2 a vyšší](functions-bindings-cosmosdb-v2.md)

Tato sada článků vysvětluje, jak pracovat s [Azure Cosmos DBmi](../cosmos-db/serverless-computing-database.md) vazbami v Azure Functions 2. x a vyšší. Azure Functions podporuje vazby triggeru, vstupu a výstupu pro Azure Cosmos DB.

| Akce | Typ |
|---------|---------|
| Spustit funkci při vytvoření nebo úpravě dokumentu Azure Cosmos DB | [Trigger](./functions-bindings-cosmosdb-v2-trigger.md) |
| Čtení dokumentu Azure Cosmos DB | [Vstupní vazba](./functions-bindings-cosmosdb-v2-input.md) |
| Uložení změn do dokumentu Azure Cosmos DB  |[Výstupní vazba](./functions-bindings-cosmosdb-v2-output.md) |

> [!NOTE]
> Tento odkaz je určen pro [Azure Functions verze 2. x a vyšší](functions-versions.md).  Informace o tom, jak tyto vazby použít ve funkcích 1. x, najdete v tématu [Azure Cosmos DB vazby pro Azure Functions 1. x](functions-bindings-cosmosdb.md).
>
> Tato vazba se původně jmenovala jako DocumentDB. Ve funkcích Functions verze 2. x a vyšší jsou triggery, vazby a balíčky všechny pojmenovány Cosmos DB.

## <a name="supported-apis"></a>Podporovaná rozhraní API

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="add-to-your-functions-app"></a>Přidat do aplikace Functions

### <a name="functions-2x-and-higher"></a>Functions 2.x a novější

Práce s triggerem a vazbami vyžaduje, abyste odkazovali na příslušný balíček. Balíček NuGet se používá pro knihovny tříd .NET, pokud se sada rozšíření používá pro všechny ostatní typy aplikací.

| Jazyk                                        | Přidat do...                                   | Poznámky 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalace [balíčku NuGet]verze 3. x | |
| Skript C#, Java, JavaScript, Python, PowerShell | Registrace [balíčku rozšíření]          | [Rozšíření Azure Tools] se doporučuje používat s Visual Studio Code. |
| Skript jazyka C# (pouze online v Azure Portal)         | Přidání vazby                            | Pokud chcete aktualizovat existující rozšíření vazby bez nutnosti opětovného publikování aplikace Function App, přečtěte si téma [aktualizace rozšíření]. |

[Balíček NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB
[core tools]: ./functions-run-local.md
[Sada rozšíření]: ./functions-bindings-register.md#extension-bundles
[Aktualizace rozšíření]: ./functions-bindings-register.md
[Rozšíření nástrojů Azure]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Aplikace Functions 1. x mají automaticky odkaz na balíček NuGet [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) , verze 2. x.

## <a name="next-steps"></a>Další kroky

- [Spustit funkci při vytvoření nebo úpravě dokumentu Azure Cosmos DB (aktivační událost)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Čtení dokumentu Azure Cosmos DB (vstupní vazba)](./functions-bindings-cosmosdb-v2-input.md)
- [Uložení změn do dokumentu Azure Cosmos DB (výstupní vazba)](./functions-bindings-cosmosdb-v2-output.md)