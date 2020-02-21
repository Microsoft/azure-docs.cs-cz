---
title: Vazby služby Azure Functions Signal
description: Naučte se používat vazby služby signalizace s Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: 863620ce6f0af33b05ef290ae95ccdc99a53a54d
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2020
ms.locfileid: "77523032"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>Vazby služby SignalR pro Azure Functions

Tato sada článků vysvětluje, jak ověřit a odeslat zprávy v reálném čase klientům připojeným ke [službě Azure Signal](https://azure.microsoft.com/services/signalr-service/) pomocí vazeb služby signalizace v Azure Functions. Služba Azure Functions podporuje vazby vstupu a výstupu pro SignalR Service.

| Akce | Typ |
|---------|---------|
| Vrátí adresu URL koncového bodu služby a přístupový token. | [Vstupní vazba](./functions-bindings-signalr-service-input.md) |
| Odeslat zprávy služby Signaler |[Výstupní vazba](./functions-bindings-signalr-service-output.md) |

## <a name="add-to-your-functions-app"></a>Přidat do aplikace Functions

### <a name="functions-2x-and-higher"></a>Functions 2. x a vyšší

Práce s triggerem a vazbami vyžaduje, abyste odkazovali na příslušný balíček. Balíček NuGet se používá pro knihovny tříd .NET, pokud se sada rozšíření používá pro všechny ostatní typy aplikací.

| Jazyk                                        | Přidat do...                                   | Poznámky 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalace [Balíček NuGet]verze 3. x | |
| C#Skript, Java, JavaScript, Python, PowerShell | Registrace [Sada rozšíření]          | [Rozšíření nástrojů Azure] se doporučuje používat s Visual Studio Code. |
| C#Skript (online – pouze v Azure Portal)         | Přidání vazby                            | Pokud chcete aktualizovat existující rozšíření vazby bez nutnosti opětovného publikování aplikace Function App, přečtěte si téma [aktualizace rozšíření]. |

[Balíček NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService
[core tools]: ./functions-run-local.md
[Sada rozšíření]: ./functions-bindings-register.md#extension-bundles
[Aktualizace rozšíření]: ./install-update-binding-extensions-manual.md
[Rozšíření nástrojů Azure]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

Podrobnosti o tom, jak nakonfigurovat a používat službu signalizace a Azure Functions společně, najdete v tématu [Azure Functions vývoj a konfigurace pomocí služby Azure Signal Service](../azure-signalr/signalr-concept-serverless-development-config.md).

### <a name="annotations-library-java-only"></a>Knihovna poznámek (jenom Java)

Pokud chcete používat poznámky ke službě signalizace ve funkcích Java, musíte do souboru *pom. XML* přidat závislost do artefaktu *Azure-Functions-Java-Library-signal* (verze 1,0 nebo vyšší).

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="next-steps"></a>Další kroky

- [Vrácení adresy URL koncového bodu služby a přístupového tokenu (vstupní vazba)](./functions-bindings-signalr-service-input.md)
- [Odeslat zprávy služby Signaler (výstupní vazba)](./functions-bindings-signalr-service-output.md) 
