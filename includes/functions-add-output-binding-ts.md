---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/11/2020
ms.author: glenga
ms.openlocfilehash: dab5f0f24fa1f36b611eb79329336832d8a4b3cb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "78191011"
---
Přidejte kód, který používá `msg` výstupní vazbu objektu pro `context.bindings` k vytvoření zprávy fronty. Přidejte tento kód před `context.res` příkaz.

:::code language="typescript" range="10" source="~/functions-docs-typescript/functions-add-output-binding-storage-queue-cli/HttpExample/index.ts":::

V tomto okamžiku by měla funkce vypadat takto:

:::code language="typescript" source="~/functions-docs-typescript/functions-add-output-binding-storage-queue-cli/HttpExample/index.ts":::