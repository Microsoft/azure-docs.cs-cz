---
author: baanders
description: zahrnutý soubor popisující, jak ověřit modely digitálních vláken Azure
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: fc7c31d1ab82e2e7edc74fb57233e8a0891fd113
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/12/2020
ms.locfileid: "91974956"
---
> [!TIP]
> Po vytvoření modelu doporučujeme vaše modely před odesláním do instance digitálního vlákna Azure ověřit offline.

Pro ověřování dokumentů modelu je k dispozici ukázkový nezávisláý jazyk, abyste se ujistili, že DTDL je správný. Je umístěn zde: [**Ukázka validátoru DTDL**](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator).

Ukázka validátoru DTDL je postavená na knihovně DTDL analyzátoru .NET, která je k dispozici v NuGet jako knihovna na straně klienta: [**Microsoft. Azure. DigitalTwins. Parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/). Knihovnu můžete také použít přímo k návrhu vlastního řešení ověřování. Při použití knihovny analyzátoru se ujistěte, že používáte verzi, která je kompatibilní s verzí, kterou Azure Digital revláken používá. Během období Preview se jedná o verzi *3.12.4*.

Další informace o ukázce a knihovně analyzátoru validátoru, včetně příkladů použití, najdete v tématu [*Postupy: analýza a ověření modelů*](../articles/digital-twins/how-to-parse-models.md).