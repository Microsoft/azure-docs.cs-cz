---
title: Vazby Azure RabbitMQ pro Azure Functions
description: Naučte se odesílat triggery Azure RabbitMQ a vazby v Azure Functions.
author: cachai2
ms.assetid: ''
ms.topic: reference
ms.date: 12/11/2020
ms.author: cachai
ms.custom: ''
ms.openlocfilehash: cd6c8de0d9290aab273e25403bb1cb2d8fe07e27
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2020
ms.locfileid: "97505708"
---
# <a name="rabbitmq-bindings-for-azure-functions-overview"></a>RabbitMQ – Přehled vazeb pro Azure Functions

> [!NOTE]
> Vazby RabbitMQ se plně podporují jenom v plánech **Windows Premium** . Spotřeba a Linux se aktuálně nepodporují.

Azure Functions se integruje s [RabbitMQ](https://www.rabbitmq.com/) prostřednictvím [triggerů a vazeb](./functions-triggers-bindings.md). Rozšíření Azure Functions RabbitMQ Binding umožňují odesílat a přijímat zprávy pomocí rozhraní RabbitMQ API s funkcemi.

| Akce | Typ |
|---------|---------|
| Spustit funkci, když se RabbitMQ zpráva dostane přes frontu | [Trigger](./functions-bindings-rabbitmq-trigger.md) |
| Odesílání zpráv RabbitMQ |[Výstupní vazba](./functions-bindings-rabbitmq-output.md) |

## <a name="add-to-your-functions-app"></a>Přidat do aplikace Functions

Pokud chcete začít s vývojem s tímto rozšířením, nezapomeňte nejdřív [nastavit koncový bod RabbitMQ](https://github.com/Azure/azure-functions-rabbitmq-extension/wiki/Setting-up-a-RabbitMQ-Endpoint). Pokud se chcete dozvědět víc o RabbitMQ, podívejte se na jeho [stránku Začínáme](https://www.rabbitmq.com/getstarted.html).

### <a name="functions-2x-and-higher"></a>Functions 2.x a novější

Práce s triggerem a vazbami vyžaduje, abyste odkazovali na příslušný balíček. Balíček NuGet se používá pro knihovny tříd .NET, pokud se sada rozšíření používá pro všechny ostatní typy aplikací.

| Jazyk                                        | Přidat do...                                   | Poznámky 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalace [balíčku NuGet]verze 4. x | |
| Skript C#, Java, JavaScript, Python, PowerShell | Registrace [balíčku rozšíření]          | [Rozšíření Azure Tools] se doporučuje používat s Visual Studio Code. |
| Skript jazyka C# (pouze online v Azure Portal)         | Přidání vazby                            | Pokud chcete aktualizovat existující rozšíření vazby bez nutnosti opětovného publikování aplikace Function App, přečtěte si téma [aktualizace rozšíření]. |

[Balíček NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.RabbitMQ
[core tools]: ./functions-run-local.md
[Sada rozšíření]: ./functions-bindings-register.md#extension-bundles
[Aktualizace rozšíření]: ./functions-bindings-register.md
[Rozšíření nástrojů Azure]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Rozšíření vazby RabbitMQ nejsou podporována pro funkce 1. x. Použijte prosím Functions 2. x a vyšší.

## <a name="next-steps"></a>Další kroky

- [Spustit funkci při vytvoření zprávy RabbitMQ (trigger)](./functions-bindings-rabbitmq-trigger.md)
- [Posílání zpráv RabbitMQ z Azure Functions (výstupní vazba)](./functions-bindings-rabbitmq-output.md)