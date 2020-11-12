---
author: baanders
description: zahrnutí souboru pro digitální vlákna Azure – způsoby správy instance
ms.service: digital-twins
ms.topic: include
ms.date: 11/11/2020
ms.author: baanders
ms.openlocfilehash: 47de00780fb259e7476c16dea9b7c63bc512a211
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94533896"
---
V tomto článku se naučíte, jak provádět různé operace správy pomocí [ **sady** Azure Digital revlákens .NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/management?view=azure-dotnet&preserve-view=true). Můžete také vytvořit stejné volání správy pomocí dalších jazykových sad SDK popsaných v tématu [*Postupy: použití rozhraní API a sad SDK digitálních vláken Azure*](../articles/digital-twins/how-to-use-apis-sdks.md).

> [!TIP] 
> Pamatujte, že všechny metody sady SDK přicházejí v synchronních a asynchronních verzích. Pro volání stránkování vrátí asynchronní metody `AsyncPageable<T>` při návratu synchronních verzí `Pageable<T>` .

Další možností správy je volat [**rozhraní REST API**](/rest/api/azure-digitaltwins/) služby Azure Digital revlákens pro tuto oblast témata přímo.

Nakonec můžete dokončit stejné operace správy pomocí rozhraní příkazového **řádku** Azure Digital revlákens. Další informace o použití rozhraní příkazového řádku naleznete v tématu [*How to: use the Azure Digital Revlákens CLI*](../articles/digital-twins/how-to-use-cli.md).