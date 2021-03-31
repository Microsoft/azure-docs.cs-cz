---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/18/2020
ms.author: glenga
ms.openlocfilehash: c27f3fea6d2dd8b891220ba06b375b33e6cd950b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "80673505"
---
V projektu Java jsou vazby definovány jako anotace vazby v metodě Function. *function.jsv* souboru se pak automaticky generuje na základě těchto poznámek.

Přejděte do umístění kódu funkce v části _Src/Main/Java_, otevřete soubor projektu *Function. Java* a do definice metody přidejte následující parametr `run` :

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="20-21":::

`msg`Parametr je [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) typ, který představuje kolekci řetězců, které jsou zapsány jako zprávy do výstupní vazby po dokončení funkce. V tomto případě je výstupem fronta úložiště s názvem `outqueue` . Připojovací řetězec pro účet úložiště je nastaven `connection` metodou. Místo samotného připojovacího řetězce předáte nastavení aplikace, které obsahuje připojovací řetězec účtu úložiště.

`run`Definice metody by teď měla vypadat jako v následujícím příkladu:  

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="16-22":::