---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/05/2019
ms.author: glenga
ms.openlocfilehash: 5e1a2622df0038141dd5cb05237f93d5e33e0bfb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "78190907"
---
V projektu knihovny tříd jazyka C# jsou vazby definovány jako atributy vazby v metodě Function. *function.jsv* souboru vyžadovaném funkcemi se pak automaticky generuje na základě těchto atributů.

Otevřete soubor projektu *HttpExample.cs* a do definice metody přidejte následující parametr `Run` :

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="17":::

`msg`Parametr je `ICollector<T>` typ, který představuje kolekci zpráv zapsaných do výstupní vazby po dokončení funkce. V tomto případě je výstupem fronta úložiště s názvem `outqueue` . Připojovací řetězec pro účet úložiště je nastaven pomocí `StorageAccountAttribute` . Tento atribut označuje nastavení, které obsahuje připojovací řetězec účtu úložiště a dá se použít na úrovni třídy, metody nebo parametru. V tomto případě můžete vynechat, `StorageAccountAttribute` protože už používáte výchozí účet úložiště.

Definice metody spuštění by teď měla vypadat takto:  

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="14-18":::
