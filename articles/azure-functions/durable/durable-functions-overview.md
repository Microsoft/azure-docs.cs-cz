---
title: Odolná služba Functions přehled – Azure
description: Úvod do rozšíření Durable Functions pro službu Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: overview
ms.date: 12/22/2018
ms.author: azfuncdf, glenga
ms.openlocfilehash: 4c7b4733d05f18d3c30e45fd08c3cf9c50354ebc
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55816085"
---
# <a name="what-are-durable-functions"></a>Co jsou odolná služba Functions?

*Odolná služba Functions* jsou rozšíření [Azure Functions](../functions-overview.md) , který umožňuje zapisovat stavové funkce v prostředí bez serveru. Toto rozšíření za vás spravuje stav, kontrolní body a restartování.

## <a name="benefits"></a>Výhody

Rozšíření umožňuje definovat stavové pracovní postupy pomocí [ *funkce orchestrátoru*](durable-functions-types-features-overview.md#orchestrator-functions), což poskytuje následující výhody:

* Můžete definovat pracovní postupy v kódu. Nejsou potřeba žádné schémat JSON nebo návrháře.
* Další funkce lze volat i synchronního a asynchronního. Výstup z volané funkce lze uložit do místní proměnné.
* Průběh je automaticky byl vytvořen kontrolní bod, když čeká na funkci. Místní se nikdy ztraceny, pokud se proces recykluje nebo restartování virtuálního počítače.

## <a name="application-patterns"></a>Vzory aplikací

Případem primárního použití pro Durable Functions je zjednodušuje složité a stavové koordinace požadavky aplikace bez serveru. Tady jsou některé vzory Typická aplikace, které může přinést Durable Functions:

* [Řetězení](durable-functions-concepts.md#chaining)
* [FAN odesílací/fan v](durable-functions-concepts.md#fan-in-out)
* [Async HTTP APIs](durable-functions-concepts.md#async-http)
* [Monitorování](durable-functions-concepts.md#monitoring)
* [Lidská interakce](durable-functions-concepts.md#human)

## <a name="language-support"></a>Podporované jazyky

Odolná služba Functions nyní podporuje následující jazyky:

* **C#**: oba [předkompilované knihovny tříd](../functions-dotnet-class-library.md) a [ C# skript](../functions-reference-csharp.md).
* **F#**: předkompilované knihovny tříd a F# skriptu. F#skript se podporuje jenom pro verzi 1.x modulu runtime Azure Functions.
* **JavaScript**: podporuje jenom pro verzi 2.x modulu runtime Azure Functions. Vyžaduje verzi 1.7.0 rozšíření Durable Functions nebo novější. 

Odolná služba Functions je cílem podporovat všechny [Azure Functions jazyky](../supported-languages.md). Najdete v článku [seznam problémů Durable Functions](https://github.com/Azure/azure-functions-durable-extension/issues) nejnovější stav práce pro podporu dalších jazyků.

Podobně jako Azure Functions jsou šablony, které vám pomůžou s vývojem pomocí Durable Functions [Visual Studio 2017](durable-functions-create-first-csharp.md), [Visual Studio Code](quickstart-js-vscode.md)a [webu Azure portal](durable-functions-create-portal.md).

## <a name="billing"></a>Fakturace

Odolná služba Functions se účtuje stejná jako funkce Azure Functions. Další informace najdete v tématu [ceny Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

## <a name="jump-right-in"></a>Pustit do práce

Můžete začít s Durable Functions za méně než 10 minut provedením následujících kurzech rychlý start specifické pro jazyk:

* [C#pomocí sady Visual Studio 2017](durable-functions-create-first-csharp.md)
* [JavaScript s využitím Visual Studio Code](quickstart-js-vscode.md)

V obou šablon rychlý start místně vytvořit a otestovat trvalý funkci "hello world". Kód funkce potom publikujete do Azure. Funkce, které vytvoříte orchestruje a zřetězí společně volání dalších funkcí.

## <a name="learn-more"></a>Další informace

V následujícím videu najdete výhody Durable Functions:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Durable-Functions-in-Azure-Functions/player] 

Protože je pokročilá rozšíření pro Durable Functions [Azure Functions](../functions-overview.md), není vhodná pro všechny aplikace. Další informace o Durable Functions najdete v tématu [Durable Functions vzory a technických konceptech](durable-functions-concepts.md). Porovnání s jinými technologiemi Azure orchestration najdete v tématu [porovnání Azure Functions a Azure Logic Apps](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-azure-functions-and-azure-logic-apps).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Odolné funkce vzory a technické koncepty](durable-functions-concepts.md)
