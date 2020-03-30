---
title: Vazby Azure Event Grid pro funkce Azure
description: Zjistěte, jak zpracovat události gridu událostí v Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: 21654a3b325e8b8f0a3e49ee64b7624c8540d0d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461075"
---
# <a name="azure-event-grid-bindings-for-azure-functions"></a>Vazby Azure Event Grid pro funkce Azure

Tento odkaz vysvětluje, jak zpracovat události [Grid událostí](../event-grid/overview.md) v Azure Functions. Podrobnosti o tom, jak zpracovat zprávy mřížky událostí v koncovém bodě HTTP, naleznete v [tématu Příjem událostí do koncového bodu HTTP](../event-grid/receive-events.md).

Event Grid je služba Azure, která odesílá požadavky HTTP, aby vás upozornila na události, ke kterým dochází u *vydavatelů*. Vydavatel je služba nebo prostředek, který událost pochází. Například účet úložiště objektů blob Azure je vydavatel a [nahrávání nebo odstraňování objektů blob je událost](../storage/blobs/storage-blob-event-overview.md). Některé [služby Azure mají integrovanou podporu pro publikování událostí do Event Grid](../event-grid/overview.md#event-sources).

*Obslužné rutiny* událostí přijímají a zpracovávají události. Azure Functions je jedna z několika [služeb Azure, které mají integrovanou podporu pro zpracování událostí Grid událostí](../event-grid/overview.md#event-handlers). V tomto odkazu se naučíte použít aktivační událost Mřížka událostí k vyvolání funkce při přijetí události z event gridu a k odeslání událostí do [vlastního tématu event gridu](../event-grid/post-to-custom-topic.md).

Pokud chcete, můžete použít aktivační událost HTTP pro zpracování událostí mřížky událostí; viz [Příjem událostí do koncového bodu HTTP](../event-grid/receive-events.md). V současné době nelze použít aktivační událost Grid pro aplikaci Azure Functions při události se zobrazí ve [schématu CloudEvents](../event-grid/cloudevents-schema.md#azure-functions). Místo toho použijte aktivační událost HTTP.

| Akce | Typ |
|---------|---------|
| Spuštění funkce při odeslání události Event Grid | [Trigger](./functions-bindings-event-grid-trigger.md) |
| Odešle událost Event Grid |[Výstupní vazba](./functions-bindings-event-grid-output.md) |

Kód v tomto odkazu výchozí syntaxe .NET Core, který se používá v funkce verze 2.x a vyšší. Informace o syntaxi 1.x naleznete v [šablonách funkcí 1.x](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates).

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
[Balíček NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid
[Aktualizace rozšíření]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Aplikace functions 1.x mají automaticky odkaz na balíček [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet verze 2.x.

## <a name="next-steps"></a>Další kroky
* [Spuštění funkce při odeslání události Event Grid](./functions-bindings-event-grid-trigger.md)
* [Odeslání události Mřížky událostí](./functions-bindings-event-grid-trigger.md)
