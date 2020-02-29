---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 8bdd8b9d900cc50fdeb34ff7d233ac4d7e17a45c
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/29/2020
ms.locfileid: "78191009"
---
Aktualizujte *HttpExample\\\_\_init\_\_. py* , aby odpovídaly následujícímu kódu, přidání parametru `msg` do definice funkce a `msg.set(name)` v rámci příkazu `if name:`.

:::code language="python" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

Parametr `msg` je instancí [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest). Jeho `set` Metoda zapisuje do fronty zprávu s řetězcem, v tomto případě název předaný do funkce v řetězci dotazu URL.
