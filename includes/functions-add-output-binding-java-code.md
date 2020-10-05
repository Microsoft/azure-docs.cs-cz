---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 3705f58a37c109ebe0b774603c60e246fc174f25
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "80673265"
---
Nyní můžete použít nový `msg` parametr pro zápis do výstupní vazby z kódu funkce. Přidejte následující řádek kódu před reakci na úspěch pro přidání hodnoty `name` do `msg` výstupní vazby.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="34":::

Když použijete výstupní vazbu, nemusíte používat Azure Storage kód SDK pro ověřování, získání odkazu na frontu nebo zápis dat. Úlohy za běhu functions a Queue výstupní vazby jsou za vás.

Vaše `run` Metoda by teď měla vypadat jako v následujícím příkladu:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="17-38":::