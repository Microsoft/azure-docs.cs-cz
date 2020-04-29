---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 8bdd8b9d900cc50fdeb34ff7d233ac4d7e17a45c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "78191009"
---
Aktualizujte *\\\_\_HttpExample\_init\_. py* tak, aby odpovídala následujícímu `msg` kódu, přidání parametru do definice `msg.set(name)` funkce a `if name:` v rámci příkazu.

:::code language="python" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

`msg` Parametr je instancí [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest). Jeho `set` Metoda zapíše do fronty zprávu s řetězcem, v tomto případě název předaný do funkce v řetězci dotazu URL.
