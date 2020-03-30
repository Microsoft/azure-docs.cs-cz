---
title: Vazby Azure Service Bus pro funkce Azure
description: Naučte se odesílat aktivační události a vazby služby Azure Service Bus ve funkcích Azure.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: 44e4114b328701d5de363e91f5562f1daad351c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277411"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Vazby Azure Service Bus pro funkce Azure

Azure Functions se integruje s [Azure Service Bus](https://azure.microsoft.com/services/service-bus) prostřednictvím [aktivačních událostí a vazeb](./functions-triggers-bindings.md). Integrace s Service Bus umožňuje vytvářet funkce, které reagují na a odesílat fronty nebo tematické zprávy.

| Akce | Typ |
|---------|---------|
| Spuštění funkce při vytvoření fronty služby Service Bus nebo tematické zprávy | [Trigger](./functions-bindings-service-bus-trigger.md) |
| Odeslání zpráv azure service bus |[Výstupní vazba](./functions-bindings-service-bus-output.md) |

## <a name="add-to-your-functions-app"></a>Přidání do aplikace Funkce

### <a name="functions-2x-and-higher"></a>Funkce 2.x a vyšší

Práce s aktivační událost a vazby vyžaduje, abyste odkazovat na příslušný balíček. Balíček NuGet se používá pro knihovny tříd .NET, zatímco rozšíření svazku se používá pro všechny ostatní typy aplikací.

| Jazyk                                        | Přidat podle...                                   | Poznámky 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalace [balíčku NuGet], verze 4.x | |
| C# Script, Java, JavaScript, Python, PowerShell | Registrace [rozšíření balíčku]          | Rozšíření [Nástroje Azure] se doporučuje používat s Visual Studio Code. |
| Skript C# (jenom online na webu Azure Portal)         | Přidání vazby                            | Pokud chcete aktualizovat existující rozšíření o vazby, aniž byste museli znovu publikovat aplikaci funkcí, přečtěte si informace [o aktualizaci rozšíření]. |

[Balíček NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus/
[core tools]: ./functions-run-local.md
[rozšiřující balíček]: ./functions-bindings-register.md#extension-bundles
[Aktualizace rozšíření]: ./install-update-binding-extensions-manual.md
[Rozšíření nástrojů Azure]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Aplikace functions 1.x mají automaticky odkaz na balíček [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet verze 2.x.

## <a name="next-steps"></a>Další kroky

- [Spuštění funkce při vytvoření fronty služby Service Bus nebo zprávy tematické zprávy (Aktivační událost)](./functions-bindings-service-bus-trigger.md)
- [Odesílání zpráv Azure Service Bus z Azure Functions (výstupní vazba)](./functions-bindings-service-bus-output.md)
