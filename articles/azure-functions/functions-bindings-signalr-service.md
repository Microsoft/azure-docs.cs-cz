---
title: Vazby služby Azure Functions SignalR
description: Zjistěte, jak používat vazby služby SignalR s funkcemi Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: 863620ce6f0af33b05ef290ae95ccdc99a53a54d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523032"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>Vazby služby SignalR pro Azure Functions

Tato sada článků vysvětluje, jak ověřit a odesílat zprávy v reálném čase klientům připojeným ke [službě Azure SignalR](https://azure.microsoft.com/services/signalr-service/) pomocí vazby služby SignalR ve funkcích Azure. Služba Azure Functions podporuje vazby vstupu a výstupu pro SignalR Service.

| Akce | Typ |
|---------|---------|
| Vrácení adresy URL koncového bodu služby a přístupového tokenu | [Vstupní vazba](./functions-bindings-signalr-service-input.md) |
| Odeslat zprávy služby SignalR |[Výstupní vazba](./functions-bindings-signalr-service-output.md) |

## <a name="add-to-your-functions-app"></a>Přidání do aplikace Funkce

### <a name="functions-2x-and-higher"></a>Funkce 2.x a vyšší

Práce s aktivační událost a vazby vyžaduje, abyste odkazovat na příslušný balíček. Balíček NuGet se používá pro knihovny tříd .NET, zatímco rozšíření svazku se používá pro všechny ostatní typy aplikací.

| Jazyk                                        | Přidat podle...                                   | Poznámky 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalace [balíčku NuGet], verze 3.x | |
| C# Script, Java, JavaScript, Python, PowerShell | Registrace [rozšíření balíčku]          | Rozšíření [Nástroje Azure] se doporučuje používat s Visual Studio Code. |
| Skript C# (jenom online na webu Azure Portal)         | Přidání vazby                            | Pokud chcete aktualizovat existující rozšíření o vazby, aniž byste museli znovu publikovat aplikaci funkcí, přečtěte si informace [o aktualizaci rozšíření]. |

[Balíček NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService
[core tools]: ./functions-run-local.md
[rozšiřující balíček]: ./functions-bindings-register.md#extension-bundles
[Aktualizace rozšíření]: ./install-update-binding-extensions-manual.md
[Rozšíření nástrojů Azure]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

Podrobnosti o tom, jak společně nakonfigurovat a používat služby SignalR a funkce Azure, najdete v části [Vývoj a konfigurace funkcí Azure pomocí služby Azure SignalR](../azure-signalr/signalr-concept-serverless-development-config.md).

### <a name="annotations-library-java-only"></a>Knihovna anotací (pouze java)

Chcete-li používat poznámky služby SignalR ve funkcích jazyka Java, musíte do souboru *pom.xml* přidat závislost na *artefaktu azure-functions-library-signalr* (verze 1.0 nebo vyšší).

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="next-steps"></a>Další kroky

- [Vrácení adresy URL koncového bodu služby a přístupového tokenu (vstupní vazba)](./functions-bindings-signalr-service-input.md)
- [Odeslat zprávy služby SignalR (výstupní vazba)](./functions-bindings-signalr-service-output.md) 
