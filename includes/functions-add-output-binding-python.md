---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 8bdd8b9d900cc50fdeb34ff7d233ac4d7e17a45c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78191009"
---
Aktualizace *HttpExample\\\_\_\_\_init .py* tak, aby `msg` odpovídaly následující kód, přidání parametru definice funkce a `msg.set(name)` pod `if name:` příkaz.

:::code language="python" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

Parametr `msg` je instancí [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest)třídy . Jeho `set` metoda zapíše zprávu řetězce do fronty, v tomto případě název předán funkci v řetězci dotazu URL.
