---
author: baanders
description: zahrnutý soubor popisující, jak ověřit modely digitálních vláken Azure
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: 32c02750f99f1aa6733d9c6dd673ffe4964978f4
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107303749"
---
> [!TIP]
> Po vytvoření modelu doporučujeme vaše modely před odesláním do instance digitálního vlákna Azure ověřit offline.

Pro ověřování dokumentů modelu je k dispozici ukázkový nezávisláý jazyk, abyste se ujistili, že DTDL je správný, než ho nahrajete do vaší instance. Je umístěn zde: [**Ukázka validátoru DTDL**](/samples/azure-samples/dtdl-validator/dtdl-validator).

Ukázka validátoru DTDL je postavená na knihovně DTDL analyzátoru .NET, která je k dispozici v NuGet jako knihovna na straně klienta: [**Microsoft. Azure. DigitalTwins. Parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/). Knihovnu můžete také použít přímo k návrhu vlastního řešení ověřování. Při použití knihovny analyzátoru se ujistěte, že používáte verzi, která je kompatibilní s verzí, kterou Azure Digital revláken používá. V současné době se jedná o verzi *3.12.4*.

Další informace o ukázce a knihovně analyzátoru validátoru, včetně příkladů použití, najdete v tématu [*Postupy: analýza a ověření modelů*](../articles/digital-twins/how-to-parse-models.md).