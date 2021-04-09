---
author: baanders
description: zahrnutí souboru pro digitální vlákna Azure – způsoby správy instance
ms.service: digital-twins
ms.topic: include
ms.date: 11/11/2020
ms.author: baanders
ms.openlocfilehash: 02f6c59a76a3fdb7bd4360570b29d7b40a1aff8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102473701"
---
V tomto článku se naučíte, jak provádět různé operace správy pomocí [ **sady** Azure Digital revlákens .NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/management). Můžete také vytvořit stejné volání správy pomocí dalších jazykových sad SDK popsaných v tématu [*Postupy: použití rozhraní API a sad SDK digitálních vláken Azure*](../articles/digital-twins/how-to-use-apis-sdks.md).

> [!TIP] 
> Pamatujte, že všechny metody sady SDK přicházejí v synchronních a asynchronních verzích. Pro volání stránkování vrátí asynchronní metody `AsyncPageable<T>` při návratu synchronních verzí `Pageable<T>` .

Další možností správy je volat [**rozhraní REST API**](/rest/api/azure-digitaltwins/) služby Azure Digital revlákens pro tuto oblast témat přímo prostřednictvím klienta REST, jako je například post. Pokyny k tomu, jak to provést, najdete v tématu [*How to: Make a post*](../articles/digital-twins/how-to-use-postman.md).

Nakonec můžete dokončit stejné operace správy pomocí rozhraní příkazového **řádku** Azure Digital revlákens. Další informace o použití rozhraní příkazového řádku naleznete v tématu [*How to: use the Azure Digital Revlákens CLI*](../articles/digital-twins/how-to-use-cli.md).