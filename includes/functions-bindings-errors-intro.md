---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 629de079f7cc7d95d10f8ff951a47b8b8fc62dad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77474256"
---
Chyby vyvolané ve funkcích Azure může pocházet z některého z následujících původů:

- Použití integrovaných [aktivačních událostí](..\articles\azure-functions\functions-triggers-bindings.md) Azure
- Volání k api základních služeb Azure
- Volání koncových bodů REST
- Volání klientských knihoven, balíčků nebo api třetích stran

Následující solidní postupy zpracování chyb je důležité, aby se zabránilo ztrátě dat nebo zmeškaných zpráv. Doporučené postupy zpracování chyb zahrnují následující akce:

- [Povolení Application Insights](../articles/azure-functions/functions-monitoring.md)
- [Použití strukturovaného zpracování chyb](#use-structured-error-handling)
- [Návrh pro idempotenci](../articles/azure-functions/functions-idempotent.md)
- [Implementace zásad opakování](../articles/azure-functions/functions-reliable-event-processing.md) (je-li to vhodné)

### <a name="use-structured-error-handling"></a>Použití strukturovaného zpracování chyb

Zachytávání a publikování chyb je důležité pro sledování stavu vaší aplikace. Nejvyšší úroveň kódu funkce by měla obsahovat blok try/catch. V bloku catch můžete zachytit a publikovat chyby.

### <a name="retry-support"></a>Podpora opakování

Následující aktivační události mají integrovanou podporu opakování:

* [Úložiště objektů blob Azure](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Úložiště fronty Azure](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Azure Service Bus (fronta/téma)](../articles/azure-functions/functions-bindings-service-bus.md)

Ve výchozím nastavení tyto aktivační události opakují požadavky až pětkrát. Po páté mši se spustí úložiště fronty Azure a Azure Service Bus, které zapisují zprávu do [fronty poison](..\articles\azure-functions\functions-bindings-storage-queue-trigger.md#poison-messages).

Je třeba ručně implementovat zásady opakování pro všechny ostatní aktivační události nebo vazby typy. Ruční implementace mohou zahrnovat zápis informací o chybě do [fronty poškozená zpráva](..\articles\azure-functions\functions-bindings-storage-blob-trigger.md#poison-blobs). Zápisem do fronty jed, máte možnost opakovat operace později. Tento přístup je stejný, který používá aktivační událost úložiště objektů blob.
