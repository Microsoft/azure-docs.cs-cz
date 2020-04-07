---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/18/2020
ms.author: glenga
ms.openlocfilehash: c27f3fea6d2dd8b891220ba06b375b33e6cd950b
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673505"
---
V projektu Java jsou vazby definovány jako vazby poznámky na metodu funkce. Soubor *function.json* je pak automaticky generován na základě těchto anotací.

Přejděte do umístění kódu funkce pod _src/main/java_, otevřete soubor projektu *Function.java* a přidejte do definice `run` metody následující parametr:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="20-21":::

Parametr `msg` je [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) typ, který představuje kolekci řetězců, které jsou zapsány jako zprávy do výstupní vazby po dokončení funkce. V tomto případě je výstupem `outqueue`fronta úložiště s názvem . Připojovací řetězec pro účet `connection` úložiště je nastaven metodou. Spíše než samotný připojovací řetězec, předáte nastavení aplikace, která obsahuje připojovací řetězec účtu úložiště.

Definice `run` metody by nyní měla vypadat jako následující příklad:  

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="16-22":::