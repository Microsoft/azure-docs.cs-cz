---
title: Vazby úložiště tabulek Azure pro Azure Functions
description: Naučte se používat vazby Azure Table Storage v Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 4f2b890dc60cd50b5fcaefabe8d418268b738c20
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92096721"
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Vazby úložiště tabulek Azure pro Azure Functions

Azure Functions se integruje s [Azure Storage](../storage/index.yml) prostřednictvím [triggerů a vazeb](./functions-triggers-bindings.md). Integrace s úložištěm Table umožňuje vytvářet funkce, které čtou a zapisují data úložiště tabulek.

| Akce | Typ |
|---------|---------|
| Čtení dat z tabulkového úložiště ve funkci | [Vstupní vazba](./functions-bindings-storage-table-input.md) |
| Povolení funkce pro zápis dat do tabulkového úložiště |[Výstupní vazba](./functions-bindings-storage-table-output.md) |

## <a name="packages---functions-2x-and-higher"></a>Balíčky – funkce 2. x a vyšší

Vazby tabulkového úložiště jsou k dispozici v balíčku NuGet [Microsoft. Azure. WebJobs. Extensions. Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) , verze 3. x. Zdrojový kód balíčku je v úložišti GitHub [Azure-WebJobs-SDK](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables) .

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="packages---functions-1x"></a>Balíčky – funkce 1. x

Vazby tabulkového úložiště jsou k dispozici v balíčku NuGet [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) , verze 2. x. Zdrojový kód balíčku je v úložišti GitHub [Azure-WebJobs-SDK](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Table) .

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="next-steps"></a>Další kroky

- [Čtení dat z tabulkového úložiště při spuštění funkce](./functions-bindings-storage-table-input.md)
- [Zápis dat úložiště tabulek z funkce](./functions-bindings-storage-table-output.md)
