---
title: Azure Event Grid vazby pro Azure Functions
description: Pochopte, jak zpracovávat události Event Grid v Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: 1dc1198ab9c546ef1defa1891777c4c8081171e9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85957174"
---
# <a name="azure-event-grid-bindings-for-azure-functions"></a>Azure Event Grid vazby pro Azure Functions

Tento odkaz vysvětluje, jak zpracovávat události [Event Grid](../event-grid/overview.md) v Azure Functions. Podrobnosti o zpracování Event Gridch zpráv v koncovém bodě HTTP najdete v tématu [příjem událostí do koncového bodu http](../event-grid/receive-events.md).

Event Grid je služba Azure, která odesílá požadavky HTTP, aby upozornila na události, ke kterým dochází ve *vydavatelích*. Vydavatel je služba nebo prostředek, který tuto událost vytvořil. Například účet Azure Blob Storage je Vydavatel a [odeslání nebo odstranění objektu BLOB je událost](../storage/blobs/storage-blob-event-overview.md). Některé [služby Azure mají integrovanou podporu pro publikování událostí do Event Grid](../event-grid/overview.md#event-sources).

*Obslužné rutiny* událostí přijímají a zpracovávají události. Azure Functions je jedna z několika [služeb Azure, které mají integrovanou podporu pro zpracování Event Gridch událostí](../event-grid/overview.md#event-handlers). V tomto odkazu se naučíte, jak pomocí triggeru Event Grid vyvolat funkci, když se z Event Grid přijme událost a použije se výstupní vazba k odeslání událostí do [vlastního tématu Event Grid](../event-grid/post-to-custom-topic.md).

Pokud chcete, můžete použít Trigger HTTP pro zpracování událostí Event Grid; viz [příjem událostí do koncového bodu http](../event-grid/receive-events.md). V současné době nemůžete použít Trigger Event Grid pro aplikaci Azure Functions, když se událost doručí ve [schématu CloudEvents](../event-grid/cloudevents-schema.md#azure-functions). Místo toho použijte Trigger HTTP.

| Akce | Typ |
|---------|---------|
| Spustit funkci při odeslání události Event Grid | [Trigger](./functions-bindings-event-grid-trigger.md) |
| Odešle událost Event Grid. |[Výstupní vazba](./functions-bindings-event-grid-output.md) |

Kód v tomto odkazu je ve výchozím nastavení syntaxe .NET Core, která se používá ve funkcích verze 2. x a vyšší. Informace o syntaxi 1. x naleznete v [šablonách funkcí 1. x](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates).

## <a name="add-to-your-functions-app"></a>Přidat do aplikace Functions

### <a name="functions-2x-and-higher"></a>Functions 2.x a novější

Práce s triggerem a vazbami vyžaduje, abyste odkazovali na příslušný balíček. Balíček NuGet se používá pro knihovny tříd .NET, pokud se sada rozšíření používá pro všechny ostatní typy aplikací.

| Jazyk                                        | Přidat do...                                   | Poznámky 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalace [balíčku NuGet], verze 2. x | |
| Skript C#, Java, JavaScript, Python, PowerShell | Registrace [balíčku rozšíření]          | [Rozšíření Azure Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) se doporučuje používat s Visual Studio Code. |
| Skript jazyka C# (pouze online v Azure Portal)         | Přidání vazby                            | Pokud chcete aktualizovat existující rozšíření vazby bez nutnosti opětovného publikování aplikace Function App, přečtěte si téma [aktualizace rozšíření]. |

[core tools]: ./functions-run-local.md
[Sada rozšíření]: ./functions-bindings-register.md#extension-bundles
[Balíček NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid
[Aktualizace rozšíření]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Aplikace Functions 1. x mají automaticky odkaz na balíček NuGet [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) , verze 2. x.

## <a name="next-steps"></a>Další kroky
* [Spustit funkci při odeslání události Event Grid](./functions-bindings-event-grid-trigger.md)
* [Odeslání události Event Grid](./functions-bindings-event-grid-trigger.md)
