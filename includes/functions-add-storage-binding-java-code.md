---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/18/2020
ms.author: glenga
ms.openlocfilehash: f4f109a6da0cb75555f9aadfa2fff31b8aa3107d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "80673477"
---
Nyní můžete použít nový `msg` parametr pro zápis do výstupní vazby z kódu funkce. Přidejte následující řádek kódu před reakci na úspěch pro přidání hodnoty `name` do `msg` výstupní vazby.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="33":::

Když použijete výstupní vazbu, nemusíte používat Azure Storage kód SDK pro ověřování, získání odkazu na frontu nebo zápis dat. Úlohy za běhu functions a Queue výstupní vazby jsou za vás.

Vaše `run` Metoda by teď měla vypadat jako v následujícím příkladu:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="16-37":::