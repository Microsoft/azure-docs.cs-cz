---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/05/2019
ms.author: glenga
ms.openlocfilehash: 5e1a2622df0038141dd5cb05237f93d5e33e0bfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190907"
---
V projektu knihovny tříd jazyka C# jsou vazby definovány jako atributy vazby na metodě funkce. Soubor *function.json* vyžadovaný funkcemi je pak automaticky generován na základě těchto atributů.

Otevřete *soubor HttpExample.cs* projektu a do `Run` definice metody přidejte následující parametr:

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="17":::

Parametr `msg` je `ICollector<T>` typ, který představuje kolekci zpráv, které jsou zapsány do výstupní vazby po dokončení funkce. V tomto případě je výstupem `outqueue`fronta úložiště s názvem . Připojovací řetězec pro účet `StorageAccountAttribute`úložiště je nastaven . Tento atribut označuje nastavení, které obsahuje připojovací řetězec účtu úložiště a lze jej použít na úrovni třídy, metody nebo parametru. V takovém případě můžete vynechat, `StorageAccountAttribute` protože již používáte výchozí účet úložiště.

Definice metody Run by nyní měla vypadat takto:  

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="14-18":::
