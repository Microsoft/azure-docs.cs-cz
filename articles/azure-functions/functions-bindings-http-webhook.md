---
title: Aktivační události a vazby HTTP pro Azure Functions
description: Naučte se používat triggery a vazby HTTP v Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.openlocfilehash: 5236a3be9f8908d886274764dfc0c0da5b3565bc
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92104474"
---
# <a name="azure-functions-http-triggers-and-bindings-overview"></a>Přehled aktivačních událostí a vazeb Azure Functions HTTP

Azure Functions mohou být vyvolány prostřednictvím požadavků HTTP k sestavení rozhraní API bez serveru a reagují na [Webhooky](https://en.wikipedia.org/wiki/Webhook).

| Akce | Typ |
|---------|---------|
| Spuštění funkce z požadavku HTTP | [Trigger](./functions-bindings-http-webhook-trigger.md) |
| Vrácení odpovědi HTTP z funkce |[Výstupní vazba](./functions-bindings-http-webhook-output.md) |

Kód v tomto článku je ve výchozím nastavení syntaxe .NET Core, která se používá ve funkcích verze 2. x a vyšší. Informace o syntaxi 1. x naleznete v [šablonách funkcí 1. x](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates).

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
[Balíček NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http
[Aktualizace rozšíření]: ./functions-bindings-register.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Aplikace Functions 1. x mají automaticky odkaz na balíček NuGet [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) , verze 2. x.

## <a name="next-steps"></a>Další kroky

- [Spuštění funkce z požadavku HTTP](./functions-bindings-http-webhook-trigger.md)
- [Vrácení odpovědi HTTP z funkce](./functions-bindings-http-webhook-output.md)