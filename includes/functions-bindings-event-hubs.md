---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 47e9e37676a4afa9ec29393bf970c368f3f9e5be
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "77586120"
---
## <a name="add-to-your-functions-app"></a>Přidat do aplikace Functions

### <a name="functions-2x-and-higher"></a>Functions 2.x a novější

Práce s triggerem a vazbami vyžaduje, abyste odkazovali na příslušný balíček. Balíček NuGet se používá pro knihovny tříd .NET, pokud se sada rozšíření používá pro všechny ostatní typy aplikací.

| Jazyk                                        | Přidat do...                                   | Poznámky 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalace [balíčku NuGet]verze 3. x | |
| Skript C#, Java, JavaScript, Python, PowerShell | Registrace [balíčku rozšíření]          | [Rozšíření Azure Tools] se doporučuje používat s Visual Studio Code. |
| Skript jazyka C# (pouze online v Azure Portal)         | Přidání vazby                            | Pokud chcete aktualizovat existující rozšíření vazby bez nutnosti opětovného publikování aplikace Function App, přečtěte si téma [aktualizace rozšíření]. |

[Balíček NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs
[core tools]: ../articles/azure-functions/functions-run-local.md
[Sada rozšíření]: ../articles/azure-functions/functions-bindings-register.md#extension-bundles
[Aktualizace rozšíření]: ../articles/azure-functions/install-update-binding-extensions-manual.md
[Rozšíření nástrojů Azure]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Aplikace Functions 1. x mají automaticky odkaz na balíček NuGet [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) , verze 2. x.