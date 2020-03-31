---
title: Aktivační události a vazby HTTP pro Azure Functions
description: Naučte se používat aktivační události a vazby HTTP v Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.openlocfilehash: 29b5e9c7673b4a730a41bf7cf2b1c4a2a86209ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462101"
---
# <a name="azure-functions-http-triggers-and-bindings-overview"></a>Azure Functions HTTP aktivační události a vazby přehled

Funkce Azure mohou být vyvolány prostřednictvím požadavků HTTP k vytvoření bezserverových api a reakci na [webhooky](https://en.wikipedia.org/wiki/Webhook).

| Akce | Typ |
|---------|---------|
| Spuštění funkce z požadavku HTTP | [Trigger](./functions-bindings-http-webhook-trigger.md) |
| Vrácení odpovědi HTTP z funkce |[Výstupní vazba](./functions-bindings-http-webhook-output.md) |

Kód v tomto článku je výchozí pro syntaxi jádra .NET, která se používá ve funkcích verze 2.x a vyšších. Informace o syntaxi 1.x naleznete v [šablonách funkcí 1.x](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates).

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
[Balíček NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http
[Aktualizace rozšíření]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Aplikace functions 1.x mají automaticky odkaz na balíček [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet verze 2.x.

## <a name="next-steps"></a>Další kroky

- [Spuštění funkce z požadavku HTTP](./functions-bindings-http-webhook-trigger.md)
- [Vrácení odpovědi HTTP z funkce](./functions-bindings-http-webhook-output.md)
