---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 178fa7d5f129a12736ec068fca605ba24cd37839
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71955857"
---
Chyby vyvolané v Azure Functions můžou pocházet z některého z následujících zdrojů:

- Použití integrovaných [triggerů Azure functions a vazeb](..\articles\azure-functions\functions-triggers-bindings.md)
- Volání rozhraní API základních služeb Azure
- Volání koncových bodů REST
- Volání klientských knihoven, balíčků nebo rozhraní API třetích stran

Následující postupy při zpracování chyb jsou důležité k tomu, aby se zabránilo ztrátě dat nebo nezmeškaných zpráv. Doporučené postupy pro zpracování chyb zahrnují následující akce:

- [Povolit Application Insights](../articles/azure-functions/functions-monitoring.md)
- [Použití strukturovaného zpracování chyb](#use-structured-error-handling)
- [Návrh pro idempotence](../articles/azure-functions/functions-idempotent.md)
- [Implementujte zásady opakování](../articles/azure-functions/functions-reliable-event-processing.md) (tam, kde je to vhodné).

### <a name="use-structured-error-handling"></a>Použití strukturovaného zpracování chyb

Chyby zachytávání a publikování jsou klíčové pro monitorování stavu vaší aplikace. Nejvyšší úroveň každého kódu funkce by měla obsahovat blok try/catch. V bloku catch můžete zachytit a publikovat chyby.

### <a name="retry-support"></a>Opakovat podporu

Následující triggery mají integrovanou podporu opakování:

* [Úložiště objektů BLOB v Azure](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Úložiště front Azure](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Azure Service Bus (fronta/téma)](../articles/azure-functions/functions-bindings-service-bus.md)

Ve výchozím nastavení se tyto triggery spustí znovu a požádá se o pět časů. Po pátém opakování obě aktivační události zapisují zprávu do [fronty otrav](..\articles\azure-functions\functions-bindings-storage-queue.md#trigger---poison-messages).

Je potřeba ručně implementovat zásady opakování pro všechny ostatní triggery nebo typy vazeb. Ruční implementace mohou zahrnovat zápis informací o chybách do [fronty nezpracovatelných zpráv](..\articles\azure-functions\functions-bindings-storage-blob.md#trigger---poison-blobs). Když zapíšete do fronty nečinnosti, budete mít příležitost opakovat operace později. Tento přístup je stejný jako ten, který používá Trigger služby Blob Storage.
