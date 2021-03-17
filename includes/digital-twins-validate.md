---
author: baanders
description: zahrnutý soubor popisující, jak ověřit modely digitálních vláken Azure
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: 58ae9c187f50bb7eb3f1ccc2a618275885121267
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026330"
---
> [!TIP]
> Po vytvoření modelu doporučujeme vaše modely před odesláním do instance digitálního vlákna Azure ověřit offline.

Pro ověřování dokumentů modelu je k dispozici ukázkový nezávisláý jazyk, abyste se ujistili, že DTDL je správný, než ho nahrajete do vaší instance. Je umístěn zde: [**Ukázka validátoru DTDL**](/samples/azure-samples/dtdl-validator/dtdl-validator).

* Ukázka validátoru DTDL je postavená na knihovně DTDL analyzátoru .NET, která je k dispozici v NuGet jako knihovna na straně klienta: [**Microsoft. Azure. DigitalTwins. Parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/). Knihovnu můžete také použít přímo k návrhu vlastního řešení ověřování. Při použití knihovny analyzátoru se ujistěte, že používáte verzi, která je kompatibilní s verzí, kterou Azure Digital revláken používá. V současné době se jedná o verzi *3.12.4*.

Další informace o ukázce a knihovně analyzátoru validátoru, včetně příkladů použití, najdete v tématu [*Postupy: analýza a ověření modelů*](../articles/digital-twins/how-to-parse-models.md).