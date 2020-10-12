---
title: Azure Service Bus vazby pro Azure Functions
description: Naučte se odesílat Azure Service Bus triggery a vazby v Azure Functions.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: 5e15dfec049197fa056cbd55fd839b3eb93be77c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90530365"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Azure Service Bus vazby pro Azure Functions

Azure Functions se integruje s [Azure Service Bus](https://azure.microsoft.com/services/service-bus) prostřednictvím [triggerů a vazeb](./functions-triggers-bindings.md). Integrace s Service Bus umožňuje vytvářet funkce, které reagují na zprávy front nebo témat a odesílají je.

| Akce | Typ |
|---------|---------|
| Spustit funkci při vytvoření Service Bus fronty nebo tématu | [Trigger](./functions-bindings-service-bus-trigger.md) |
| Odesílání zpráv Azure Service Bus |[Výstupní vazba](./functions-bindings-service-bus-output.md) |

## <a name="add-to-your-functions-app"></a>Přidat do aplikace Functions

> [!NOTE]
> Vazba Service Bus v současné době nepodporuje ověřování pomocí spravované identity. Místo toho prosím použijte [sdílený přístupový podpis Service Bus](../service-bus-messaging/service-bus-authentication-and-authorization.md#shared-access-signature).

### <a name="functions-2x-and-higher"></a>Functions 2.x a novější

Práce s triggerem a vazbami vyžaduje, abyste odkazovali na příslušný balíček. Balíček NuGet se používá pro knihovny tříd .NET, pokud se sada rozšíření používá pro všechny ostatní typy aplikací.

| Jazyk                                        | Přidat do...                                   | Poznámky 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalace [balíčku NuGet]verze 4. x | |
| Skript C#, Java, JavaScript, Python, PowerShell | Registrace [balíčku rozšíření]          | [Rozšíření Azure Tools] se doporučuje používat s Visual Studio Code. |
| Skript jazyka C# (pouze online v Azure Portal)         | Přidání vazby                            | Pokud chcete aktualizovat existující rozšíření vazby bez nutnosti opětovného publikování aplikace Function App, přečtěte si téma [aktualizace rozšíření]. |

[Balíček NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus/
[core tools]: ./functions-run-local.md
[Sada rozšíření]: ./functions-bindings-register.md#extension-bundles
[Aktualizace rozšíření]: ./install-update-binding-extensions-manual.md
[Rozšíření nástrojů Azure]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Aplikace Functions 1. x mají automaticky odkaz na balíček NuGet [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) , verze 2. x.

## <a name="next-steps"></a>Další kroky

- [Spustit funkci při vytvoření Service Bus fronty nebo tématu (trigger)](./functions-bindings-service-bus-trigger.md)
- [Posílání zpráv Azure Service Bus z Azure Functions (výstupní vazba)](./functions-bindings-service-bus-output.md)
