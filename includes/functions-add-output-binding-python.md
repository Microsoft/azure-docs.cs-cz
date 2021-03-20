---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 8bdd8b9d900cc50fdeb34ff7d233ac4d7e17a45c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "78191009"
---
Aktualizujte *HttpExample \\ \_ \_ init \_ \_ . py* tak, aby odpovídala následujícímu kódu, přidání `msg` parametru do definice funkce a `msg.set(name)` v rámci `if name:` příkazu.

:::code language="python" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

`msg`Parametr je instancí [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest) . Jeho `set` Metoda zapíše do fronty zprávu s řetězcem, v tomto případě název předaný do funkce v řetězci dotazu URL.
