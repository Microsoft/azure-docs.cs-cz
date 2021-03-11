---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 05d136093bd509e8c23ce8622423216326b0f1f2
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102623443"
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
[Aktualizace rozšíření]: ../articles/azure-functions/functions-bindings-register.md
[Rozšíření nástrojů Azure]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="event-hubs-extension-5x-and-higher"></a>Event Hubs rozšíření 5. x a vyšší

Nová verze rozšíření vazby Event Hubs je k dispozici jako [balíček NuGet Preview](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs/5.0.0-beta.1). Tato verze Preview zavádí možnost [připojit se pomocí identity místo tajného](../articles/azure-functions/functions-reference.md#configure-an-identity-based-connection)kódu. U aplikací .NET se také změní typy, se kterými můžete vytvořit vazby, a nahradíte typy z `Microsoft.Azure.EventHubs` novějších typů z [Azure. Messaging. EventHubs](/dotnet/api/azure.messaging.eventhubs).

> [!NOTE]
> Balíček verze Preview není zahrnutý v rozšiřující sadě a musí se nainstalovat ručně. V případě aplikací .NET přidejte do balíčku odkaz. Všechny ostatní typy aplikací najdete v tématu [aktualizace rozšíření].

[core tools]: ./functions-run-local.md
[Sada rozšíření]: ./functions-bindings-register.md#extension-bundles
[Balíček NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs/
[Aktualizace rozšíření]: ./functions-bindings-register.md
[Rozšíření nástrojů Azure]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Aplikace Functions 1. x mají automaticky odkaz na balíček NuGet [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) , verze 2. x.

## <a name="hostjson-settings"></a>host.jsnastavení
<a name="host-json"></a>

[host.jsv](../articles/azure-functions/functions-host-json.md#eventhub) souboru obsahuje nastavení, která řídí chování triggeru Event Hubs. Konfigurace se liší v závislosti na verzi Azure Functions.

[!INCLUDE [functions-host-json-event-hubs](../articles/azure-functions/../../includes/functions-host-json-event-hubs.md)]