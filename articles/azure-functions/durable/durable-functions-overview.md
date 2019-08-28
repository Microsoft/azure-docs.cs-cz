---
title: Přehled Durable Functions – Azure
description: Úvod do rozšíření Durable Functions pro Azure Functions
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: overview
ms.date: 12/22/2018
ms.author: glenga
ms.reviewer: azfuncdf
ms.openlocfilehash: 323ab530d8199dd154e5d3568c09f86f6f52d702
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087194"
---
# <a name="what-are-durable-functions"></a>Co jsou Durable Functions?

*Durable Functions* jsou rozšíření [Azure Functions](../functions-overview.md) umožňující psaní stavových funkcí v prostředí bez serveru. Toto rozšíření za vás spravuje stav, kontrolní body a restartování.

## <a name="benefits"></a>Výhody

Rozšíření umožňuje definovat stavové pracovní postupy pomocí [*funkce Orchestrator*](durable-functions-types-features-overview.md#orchestrator-functions), která může poskytovat následující výhody:

* Pracovní postupy můžete definovat v kódu. Nejsou potřeba žádné schéma JSON ani návrháře.
* Další funkce mohou být volány synchronně i asynchronně. Výstup z volaných funkcí lze uložit do místních proměnných.
* Průběh je automaticky nastaven na kontrolní bod, pokud funkce čeká. Při recyklování nebo restartování virtuálního počítače se místní stav nikdy neztratí.

## <a name="application-patterns"></a>Vzory aplikací

Primární případ použití pro Durable Functions zjednodušuje komplexní požadavky na stavovou spolupráci v aplikacích bez serveru. Níže jsou uvedeny některé typické vzory aplikací, které mohou Durable Functions těžit z následujících možností:

* [Řetězení](durable-functions-concepts.md#chaining)
* [Ventilátor/ventilátor – v](durable-functions-concepts.md#fan-in-out)
* [Asynchronní rozhraní HTTP API](durable-functions-concepts.md#async-http)
* [Monitorování](durable-functions-concepts.md#monitoring)
* [Lidská interakce](durable-functions-concepts.md#human)

## <a name="language-support"></a>Podporované jazyky

Durable Functions aktuálně podporuje následující jazyky:

* **C#** : [předkompilované knihovny tříd](../functions-dotnet-class-library.md) a [ C# skript](../functions-reference-csharp.md).
* **F#** : předkompilované knihovny tříd a F# skript. F#skript je podporován pouze pro verzi 1. x modulu Azure Functions runtime.
* **JavaScript**: podporuje se jenom pro verzi 2. x Azure Functions runtime. Vyžaduje verzi 1.7.0 rozšíření Durable Functions nebo novější verzi. 

Durable Functions má za cíl podporu všech [Azure Functionsch jazyků](../supported-languages.md). Nejnovější stav práce pro podporu dalších jazyků najdete v [seznamu problémů s Durable Functions](https://github.com/Azure/azure-functions-durable-extension/issues) .

Stejně jako Azure Functions, jsou šablony, které vám pomohou vyvíjet Durable Functions pomocí sady [Visual Studio 2019](durable-functions-create-first-csharp.md), [Visual Studio Code](quickstart-js-vscode.md)a [Azure Portal](durable-functions-create-portal.md).

## <a name="billing"></a>Fakturace

Durable Functions se účtují stejně jako Azure Functions. Další informace najdete v tématu [ceny Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

## <a name="jump-right-in"></a>Skok přímo v

Můžete začít s Durable Functions za 10 minut, a to provedením jednoho z těchto kurzů pro rychlý Start pro konkrétní jazyk:

* [C#pomocí sady Visual Studio 2019](durable-functions-create-first-csharp.md)
* [JavaScript pomocí Visual Studio Code](quickstart-js-vscode.md)

V obou rychlých startech můžete místně vytvořit a otestovat funkci "Hello World". Kód funkce potom publikujete do Azure. Funkce, kterou vytváříte, orchestruje a řetězí volání dalších funkcí.

## <a name="learn-more"></a>Víc se uč

Následující video zvýrazní výhody Durable Functions:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Durable-Functions-in-Azure-Functions/player] 

Vzhledem k tomu, že Durable Functions je pokročilá rozšíření pro [Azure Functions](../functions-overview.md), není vhodné pro všechny aplikace. Další informace o Durable Functions najdete v tématu [Durable Functions Patterns and Technical koncepty](durable-functions-concepts.md). Porovnání s dalšími technologiemi orchestrace Azure najdete v tématu [porovnání Azure functions a Azure Logic Apps](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-azure-functions-and-azure-logic-apps).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Způsoby Durable Functions a technické koncepce](durable-functions-concepts.md)
