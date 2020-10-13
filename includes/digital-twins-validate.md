---
author: baanders
description: zahrnutý soubor popisující, jak ověřit modely digitálních vláken Azure
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: 0146660fa50f9a136cd82483200ca2e6c6cc0b89
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87985868"
---
> [!TIP]
> Po vytvoření modelu doporučujeme vaše modely před odesláním do instance digitálního vlákna Azure ověřit offline.

Pro ověřování dokumentů modelu je k dispozici ukázkový nezávisláý jazyk, abyste se ujistili, že DTDL je správný. Je umístěn zde: [**Ukázka validátoru DTDL**](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator).

Ukázka validátoru DTDL je postavená na knihovně DTDL analyzátoru .NET, která je k dispozici v NuGet jako knihovna na straně klienta: [**Microsoft. Azure. DigitalTwins. Parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/). Knihovnu můžete také použít přímo k návrhu vlastního řešení ověřování. Při použití knihovny analyzátoru se ujistěte, že používáte verzi, která je kompatibilní s verzí, kterou Azure Digital revláken používá. Během období Preview se jedná o verzi *3.7.0*.

Další informace o ukázce a knihovně analyzátoru validátoru, včetně příkladů použití, najdete v tématu [*Postupy: analýza a ověření modelů*](../articles/digital-twins/how-to-parse-models.md).