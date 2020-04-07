---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 3705f58a37c109ebe0b774603c60e246fc174f25
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673265"
---
Nyní můžete použít nový `msg` parametr k zápisu do výstupní vazby z kódu funkce. Přidejte následující řádek kódu před odpověď úspěch `name` přidat `msg` hodnotu výstupní vazby.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="34":::

Při použití výstupní vazby, není potřeba použít kód Azure Storage SDK pro ověřování, získání odkazu na frontu nebo zápis dat. Funkce runtime a fronty výstupní vazba provést tyto úkoly za vás.

Vaše `run` metoda by nyní měla vypadat jako následující příklad:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="17-38":::